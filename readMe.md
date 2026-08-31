```
db.email_log.createIndex(
  { toAddress: "text" },
  {
    name: "toAddress_text",
    default_language: "english",
    weights: { toAddress: 1 }
  }
)
 ```
