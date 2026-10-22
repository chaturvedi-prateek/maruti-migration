```
(
set -uo pipefail
unset STREAM_QUEUE QUEUE WORKFLOW_ID NAMESPACE
cd /opt/docdb-migration/compose
tc() { docker compose exec -T temporal temporal "$@" </dev/null; }
echo "== 1. paused activities"
/opt/docdb-migration/helpers/dsync-find-pauses.sh --compose-dir "$PWD" --workflow-id svoc-cold
echo "== 2. svoc-cold stream activities"
tc workflow describe --workflow-id svoc-cold -o json \
  | jq '[.pendingActivities[]? | {id: .activityId, type: .activityType.name, state, attempt, lastFailure: .lastFailure.message, lastHeartbeatTime}]'
echo "== 3. cold progress (60 s sample)"
/opt/docdb-migration/helpers/dsync-progress.sh --compose-dir "$PWD" --workflow-id svoc-cold --interval 60
echo "== 4. worker log lines for this error, and uptime"
docker compose -p flow-cold --env-file .env.cold logs --since 20h worker 2>&1 | grep -i 'Error in query execution' | tail -n 10
docker ps --format '{{.Names}}  {{.Status}}' | grep flow-cold
)
 ```
