```
use verify_meta

// One of the 10 currently-stuck partition tasks, with real bounds
const t = db.verification_tasks.findOne(
  {
    type: "verifyDocuments",
    status: "processing",
    "query_filter.partition._id.lowerBound": { $not: { $type: "minKey" } },
    "query_filter.partition.upperBound": { $not: { $type: "maxKey" } }
  },
  { "query_filter.namespace": 1, "query_filter.partition": 1 }
)

print(t.query_filter.namespace)
print(EJSON.stringify({
  lower: t.query_filter.partition._id.lowerBound,
  upper: t.query_filter.partition.upperBound
}))

use svoc-db 

const b = EJSON.parse('')
const coll = db.getCollection("customer_srv") 

// What migration-verifier sends
printjson(coll.find({ $and: [
  { $expr: { $gte: ["$_id", { $literal: b.lower }] } },
  { $expr: { $lte: ["$_id", { $literal: b.upper }] } }
]}).sort({ _id: 1 }).hint({ _id: 1 }).limit(100).explain("executionStats"))

// What dsync sends
printjson(coll.find({ _id: { $gt: b.lower, $lte: b.upper } })
  .limit(100).explain("executionStats"))
 ```
