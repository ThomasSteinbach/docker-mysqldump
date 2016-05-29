# Docker mysqldump
This is a docker image for making fast and easy backups of dockerized mysql databases.

## Backup/Restore Methods
**Remote:** All operations will be directly executed on the remote mysql database running in the foreign container.

**Local:** The mysql datadir _/var/lib/mysql_ will be copied from the remote container and used within an own mysql instance. The foreign container (database) must be stopped.

All following examples show _local_ operations. To swith to _remote_ operations just replace `--volumes-from mysql_server` with `--link mysql_server:dbserver`. Mention that `mysql_server` is the arbitrary name of the foreign MySQL container and `dbserver` is the internal used name for the remote backup and must be exactly called that way.

## Usage
**Backup:**

```
sudo docker run --rm \
 --volumes-from mysql_server \
 -v /host/backup/dir:/backup \
 -e DBUSER=root \
 -e DBPASS=mysecret \
 -e BACKUP_NAME=2015-05-13 \
 thomass/mysqldump backup
```

- `mysql_server` - the container the mysql server is running in
- `/host/backup/path` - the path on your host where the backup would be stored

**Restore:**

```
sudo docker run --rm \
 --volumes-from mysql_server \
 -v /host/backup/dir:/backup \
 -e DBUSER=root \
 -e DBPASS=mysecret \
 -e BACKUP_NAME=2015-05-13.sql.7z \
 thomass/mysqldump restore
```

- `2015-05-13.sql.7z` - the file created during the backup to restore
- `BACKUP_NAME` would also work with shell expansion like `*.sql.7z`

### Select Databases to Backup

Either with

```
-env DATABASES="db1 db2"
```

or with

```
-env SKIP_DATABASES="dbx dby"
```

You could either use `DATABASES` or `SKIP_DATABASES` but not both together. On `SKIP_DATABASES` following databases will be skipped by default:

- information_schema
- performance_schema
- sys

## Predefined variables
The default _DBUSER_ is _root_ and by default all databases becomes backuped, such that both commands could be shortened as follows:

```
sudo docker run --rm \
 --volumes-from mysql_server \
 -v /host/backup/dir:/backup \
 -e DBPASS=mysecret \
 -e BACKUP_NAME=2015-05-13 \
 thomass/mysqldump backup

sudo docker run --rm \
--volumes-from mysql_server \
 -v /host/backup/dir:/backup \
 -e DBPASS=mysecret \
 -e SOURCE=2015-05-13.sql.7z \
 thomass/mysqldump restore
```

## Licence
The whole repository is licenced under BSD. Please mention following:

github.com/ThomasSteinbach (thomass at aikq.de)
