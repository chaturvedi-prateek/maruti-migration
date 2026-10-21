```
(
set -uo pipefail
unset STREAM_QUEUE QUEUE WORKFLOW_ID NAMESPACE
cd /opt/docdb-migration/compose
SRC="$(tr -d '\r' < .env.cold | grep '^DOCDB_SRC=' | tail -n1 | cut -d= -f2- | sed 's|tlsCAFile=/certs/|tlsCAFile=/opt/docdb-migration/certs/|')"
mongosh "$SRC" --quiet --eval "
  const d=db.getSiblingDB('<db>');
  printjson({exists: d.getCollectionNames().includes('<coll>'),
             estCount: d.getCollection('<coll>').estimatedDocumentCount(),
             findOneOk: d.runCommand({find:'<coll>',limit:1,maxTimeMS:5000}).ok});
" </dev/null
)


docker compose exec -T temporal temporal activity unpause \
  --workflow-id svoc-cold/bulk-4 --activity-id 447 --reset-attempts --reset-heartbeats </dev/null



docker compose exec -T temporal temporal activity complete \
  --workflow-id svoc-cold/bulk-4 --activity-id 447 </dev/null
 ```
