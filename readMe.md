```
for wf in svoc-hot svoc-cold; do
  printf '\n=== %s: current execution ===\n' "$wf"

  docker exec compose-temporal-1 \
    temporal workflow describe --workflow-id "$wf" -o json |
    jq '{
      status: .workflowExecutionInfo.status,
      runId: .workflowExecutionInfo.execution.runId,
      startTime: .workflowExecutionInfo.startTime,
      pendingChildren: [.pendingChildren[]?.workflowId],
      activityTypes: [.pendingActivities[]?.activityType.name]
    }'

  printf '\n=== %s: workflow tree ===\n' "$wf"

  docker exec compose-temporal-1 \
    temporal workflow list --query "RootWorkflowId='$wf'"
done
 ```
