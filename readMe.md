```
use svoc-db

const lower = ObjectId("654cdc1694c0a266403f5fe8")
const upper = ObjectId("654cdfa994c0a266405b2d4b")
const coll  = db.getCollection("customer_enquiry")
Query 1 – what migration-verifier sends:
const v = coll.find({ $and: [
  { $expr: { $gte: ["$_id", { $literal: lower }] } },
  { $expr: { $lte: ["$_id", { $literal: upper }] } }
]}).sort({ _id: 1 }).hint({ _id: 1 }).limit(100).explain("executionStats")

printjson(v.queryPlanner.winningPlan)
printjson({ plannerVersion: v.queryPlanner.plannerVersion, executionTimeMillis: v.executionStats.executionTimeMillis, nReturned: v.executionStats.nReturned })
Query 2 – what dsync sends:
const d = coll.find({ _id: { $gt: lower, $lte: upper } })
  .limit(100).explain("executionStats")

printjson(d.queryPlanner.winningPlan)
printjson({ plannerVersion: d.queryPlanner.plannerVersion, executionTimeMillis: d.executionStats.executionTimeMillis, nReturned: d.executionStats.nReturned })
 ```
