export DSYNCT_MODE=simple

nohup "$DSYNCT" \
  --host-port=0.0.0.0:8080 \
  sync \
  --save-file dsync-uat-1.resume \
  --concurrent-activities 4 --sync-transform-workers 4 \
  --sync-writer-workers 8 --per-stream-workers 4 \
  "$DOCDB_SRC" \
  "$MDB_DEST" > dsync-uat-1-full.out 2>&1 &
