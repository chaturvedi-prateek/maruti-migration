```
cd /opt/docdb-migration/compose
docker compose exec -T temporal temporal activity unpause \
  --workflow-id svoc-cold/bulk-4 --activity-id 447 --reset-attempts </dev/null
 ```
