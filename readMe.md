nohup "$DSYNC" \
  --logfile dsync-uat-1-full.log \
  --verbosity DEBUG \
  --load-level High \
  --web-host 0.0.0.0 \
  --web-port 8080 \
  --doc-partition 500000 \
  --namespace-fanout 100 \
  --documentdb-sampling-fanout 100 \
  -m "$META_STORE" \
  "$DOCDB_SRC" \
  "$MDB_DEST" > dsync-uat-1-full.out 2>&1 &

echo $! > dsync-uat-1-full.pid
echo "dsync started for uat — PID $(cat dsync-uat-1-full.pid)"
