``` nohup $DSYNCT \
  --host-port=0.0.0.0:8081 \
  sync \
  --save-file resume-reverse.file \
  --skip-initial-sync \
  "$MDB_DEST" "$DOCDB_SRC" \
  > dsynct-reverse.log 2>&1 & ```
