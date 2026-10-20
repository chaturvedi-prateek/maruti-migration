```
(
set -uo pipefail
unset STREAM_QUEUE QUEUE WORKFLOW_ID NAMESPACE
cd /opt/docdb-migration/compose
tc() { docker compose exec -T temporal temporal "$@" </dev/null; }
echo "== 1. paused activities in svoc-cold"
/opt/docdb-migration/helpers/dsync-find-pauses.sh --compose-dir "$PWD" --workflow-id svoc-cold
echo "== 2. svoc-cold/bulk-4: status, and activity 447 if still pending"
tc workflow describe --workflow-id svoc-cold/bulk-4 -o json \
  | jq '{status: (.workflowExecutionInfo // .executionInfo // {}).status,
         act447: [.pendingActivities[]? | select(.activityId=="447")
                  | {type: .activityType.name, state, attempt, lastFailure: .lastFailure.message,
                     lastHeartbeatTime, lastStartedTime, worker: .lastWorkerIdentity}]}'
echo "== 3. other cold partitions with the same error"
ids="$( { tc workflow list -o json --query "RootWorkflowId='svoc-cold' AND ExecutionStatus='Running'" | jq -r '.[]?.execution.workflowId // empty'
          tc workflow describe --workflow-id svoc-cold -o json | jq -r '.pendingChildren[]?.workflowId // empty'; } | sort -u | grep -v '^svoc-cold$')"
for id in $ids; do
  tc workflow describe --workflow-id "$id" -o json \
    | jq -r --arg id "$id" '.pendingActivities[]? | select((.lastFailure.message // "") | test("invalid cursor"))
                            | "\($id) activity=\(.activityId) state=\(.state) attempt=\(.attempt)"'
done
echo "== 4. cold worker uptime (was it recreated around 08:50?) and matching log lines"
docker ps --format '{{.Names}}  {{.Status}}' | grep flow-cold
docker compose -p flow-cold --env-file .env.cold logs --since 4h worker 2>&1 | grep -i 'invalid cursor' | tail -n 10
)
 ```
