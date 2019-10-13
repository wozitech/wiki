<!-- TITLE: Mongodb -->
<!-- SUBTITLE: MongoDB - Tips and Tricks -->

# Working Set Optimisation
https://www.vividcortex.com/blog/how-to-tell-if-your-mongodb-server-is-correctly-sized-for-your-working-set
# Circular Buffer style "Capped" Collections
https://docs.mongodb.com/manual/core/capped-collections/

# Backup and Restore (dump/restore)
This differs to export/import - which is to output/input pure JSON data.

Backup & restore are the traditional database backup/restore utilities; backing up a full database and being able to restore it. This is a precursor for being able to move databases.


## Backup
`mongodump` backs up a database. 

These utilities can be ran from locally install command lines tools, or can be run through docker (where `/local/backup/dir` is your local backup directory (`docker` must have write permission):
```
mongodump --out /local/backup/dir --uri mongodb+srv://aylingw@hostname/dbname
```

```
docker run --rm -v /local/backup/dir:/backup mongo bash -c 'mongodump --out /backup --uri <database uri>'
```

Or if wanting to backup a locally running MongoDB docker instance:
```
docker run --rm --link your-mongodb:mongo -v /root:/backup mongo bash -c ‘mongodump --out /backup --host mongo:27017’
```

_where `your-mongodb` is the name of your local docker instance._


The `mongodump` uri can include or exclude a specific database name. If excluding the database name, then all databases are exported, each database in a separate subdirectory. If a database name is given, then it only is exported, still within a named subdirectory. The data written to backup folder are _bson_ files.


## Restore
`mongorestore` restores a database backup created by `mongodump`. `mongorestore` format is similar to `mongodump`, as likewise, can be restored from local disk with locally installed MongoDB tools, or can use a docker container:

```
mongorestore --uri "mongodb+srv://aylingw@hostname/dbname" /local/backup/dir

```

```
docker run --rm -v /local/backup/dir:/backup mongo bash -c 'mongorestore --uri "mongodb+srv://aylingw@hostname/dbname" /local/backup/dir'

```

Or if wanting to restore into a local docker instance:

```
docker run --rm --link your-mongodb:mongo -v /root:/backup mongo bash -c ‘mongorestore --host mongo:27017 /backup’
```

_where `your-mongodb` is the name of your local docker instance._

When restoring, the path given for the backup must be the root of the backup directory as given on `mongodump`; this is true even if wanting to restore a specific database only.

If wanting to restore the full backup, don't provide the database name in the URI. If wanting to restore a specific database, include the database name in the URI.

# University
* [M220 - node.js app](/mongodb/uni/m220)
