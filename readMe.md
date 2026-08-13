mongosh "$DOCDB_SRC" --quiet --eval '
db.adminCommand({listDatabases:1}).databases
  .filter(d => !["admin","local","config"].includes(d.name))
  .forEach(d => {
    let src = db.getSiblingDB(d.name);
    src.getCollectionNames().forEach(c => {
      print(d.name + "." + c + " " + src.getCollection(c).estimatedDocumentCount());
    });
  });
' > counts-docdb.txt

mongosh "$MDB_DEST" --quiet --eval '
db.adminCommand({listDatabases:1}).databases
  .filter(d => !["admin","local","config"].includes(d.name))
  .forEach(d => {
    let dst = db.getSiblingDB(d.name);
    dst.getCollectionNames().forEach(c => {
      print(d.name + "." + c + " " + dst.getCollection(c).estimatedDocumentCount());
    });
  });
' > counts-atlas.txt
