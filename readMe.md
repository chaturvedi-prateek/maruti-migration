```
cd /opt/docdb-migration/compose
docker compose exec -T temporal temporal workflow describe --workflow-id svoc-cold/bulk-4 -o json </dev/null \
  | jq '{status: (.workflowExecutionInfo // .executionInfo // {}).status,
         act447: [.pendingActivities[]? | select(.activityId=="447") |
{state, attempt, lastFailure: .lastFailure.message, lastHeartbeatTime}]}'

(
set -uo pipefail
unset STREAM_QUEUE QUEUE WORKFLOW_ID NAMESPACE
cd /opt/docdb-migration/compose
SRC="$(tr -d '\r' < .env.cold | grep '^DOCDB_SRC=' | tail -n1 | cut -d= -f2- | sed 's|tlsCAFile=/certs/|tlsCAFile=/opt/docdb-migration/certs/|')"
mongosh "$SRC" --quiet --eval "
  const d=db.getSiblingDB('svoc-db'), c='sh_ordbook_temp_log';
  const t=(name,f)=>{ try { print(name+': '+JSON.stringify(f())); } catch(e) { print(name+': ERROR '+e.message); } };
  t('collStats(count,size,storageSize,nindexes)', ()=>{const s=d.runCommand({collStats:c}); return {count:s.count,size:s.size,storageSize:s.storageSize,nindexes:s.nindexes};});
  t('uuid now', ()=>String(d.getCollectionInfos({name:c})[0].info.uuid));
  t('estimatedCount', ()=>d.getCollection(c).estimatedDocumentCount());
  t('findOne(5s)', ()=>d.runCommand({find:c,limit:1,maxTimeMS:5000}).ok);
  sleep(120000);
  t('uuid after 2 min', ()=>String(d.getCollectionInfos({name:c})[0].info.uuid));
" </dev/null
)

docker compose exec -T temporal temporal activity complete \
  --workflow-id svoc-cold/bulk-4 --activity-id 447 --result 'null' </dev/null
 ```
