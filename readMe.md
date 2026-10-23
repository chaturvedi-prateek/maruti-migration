```
db.customer_srv.find({ $and: [
  { $expr: { $gte: ["$_id", { $literal: LOWER }] } },
  { $expr: { $lte: ["$_id", { $literal: UPPER }] } }
]}).sort({ _id: 1 }).hint({ _id: 1 }).limit(100).explain("executionStats")

db.customer_srv.find({ _id: { $gt: LOWER, $lte: UPPER } })
  .limit(100).explain("executionStats")
 ```
