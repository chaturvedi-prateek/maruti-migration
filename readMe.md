```
mongosh "$DOCDB_SRC" --quiet --eval '
  const cs = db.getMongo().watch([], {});           // cluster-wide, like dsync
  const t0 = Date.now(); const byNs = {}, byOp = {}; let n = 0;
  while (Date.now() - t0 < 60000) {
    const e = cs.tryNext(); if (!e) continue; n++;
    const ns = e.ns.db + "." + e.ns.coll;
    byNs[ns] = (byNs[ns]||0) + 1; byOp[e.operationType] = (byOp[e.operationType]||0) + 1;
  }
  print("total events/sec: " + (n/60).toFixed(0));
  print("by op: " + JSON.stringify(byOp));
  Object.entries(byNs).sort((a,b)=>b[1]-a[1]).slice(0,10)
    .forEach(([k,v]) => print((v/60).toFixed(0).padStart(6) + "/s  " + k));'
 ```
