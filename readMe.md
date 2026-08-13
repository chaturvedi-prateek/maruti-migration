DSYNCT_MODE=simple $DSYNCT \
  verify \
  --parallelism 8 \
  --skip-change-stream \
  --report-all \
  "$DOCDB_SRC" "$MDB_DEST" \
  2>&1 | tee verify-all-namespaces.log
