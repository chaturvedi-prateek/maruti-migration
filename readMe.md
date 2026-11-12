```
for wf in svoc-hot svoc-cold; do
  /opt/docdb-migration/helpers/dsync-progress.sh \
    --compose-dir /opt/docdb-migration/compose \
    --workflow-id "$wf" --interval 60

  /opt/docdb-migration/helpers/dsync-find-pauses.sh \
    --compose-dir /opt/docdb-migration/compose \
    --workflow-id "$wf"
done

curl --fail --silent --show-error --max-time 30 \
  http://127.0.0.1:27020/api/v1/progress | jq .

curl --fail --silent --show-error --max-time 30 \
  http://127.0.0.1:27020/api/v1/summary | jq .
 ```
