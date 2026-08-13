# Stream the SSE progress endpoint — look for lag=0 and a stable changeEvents counter
curl -s http://localhost:8080/progress | grep '^data:' | tail -5 | jq '.'

# Or poll once and pretty-print the sync state
curl -s http://localhost:8080/progress \
  | grep -m1 '^data: ' \
  | sed 's/^data: //' \
  | jq '{lag: .Lag, changeEvents: .ChangeStreamEvents, state: .SyncState}'
