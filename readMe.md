nohup env DSYNCT_MODE=simple $DSYNCT \
  --host-port=0.0.0.0:8081 \
  sync \
  --save-file resume-reverse.file \
  --reverse \
  "$DOCDB_SRC" "$MDB_DEST" \
  > dsynct-<env>-reverse.log 2>&1 &
