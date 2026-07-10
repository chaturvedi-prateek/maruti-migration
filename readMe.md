Set Environment Variables
# Source — Amazon DocumentDB
export DOCDB_SRC="mongodb://dsync_migration:PASSWORD@your-cluster.cluster-xxxxxxxxxxxx.ap-south-1.docdb.amazonaws.com:27017/?tls=true&tlsCAFile=global-bundle.pem&replicaSet=rs0&readPreference=secondaryPreferred&retryWrites=false"

# Destination — MongoDB Atlas
export MDB_DEST="mongodb+srv://migration_user:PASSWORD@cluster.xxxxx.mongodb.net/?retryWrites=true"

# Metadata store — always Atlas (stores flow plan and CDC resume tokens)
export META_STORE="$MDB_DEST"

# dsync binary
export DSYNC=~/dsync-linux-amd64

# Verify all are set before running any command
[[ -z "$DOCDB_SRC" || -z "$MDB_DEST" || -z "$META_STORE" ]] && echo "ERROR: env vars not set" || echo "All set"
Verify Connectivity
# Test DocumentDB
mongosh "$DOCDB_SRC" --eval "db.adminCommand({ping:1})"

# Test Atlas
mongosh "$MDB_DEST" --eval "db.adminCommand({ping:1})"
