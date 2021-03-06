![postgreSQL Logo](/uploads/logos/postgres-logo.jpg "postrgeSQL Logo"){.pagelogo}
<!-- TITLE: Postgressql -->
<!-- SUBTITLE: A quick summary of Postgressql -->

# General
## Installation (fedora 29)
https://www.if-not-true-then-false.com/2012/install-postgresql-on-fedora-centos-red-hat-rhel/

## hda conf
`/var/lib/pgsql/11/data/pg_hba.conf`:
```
#host    all             all             127.0.0.1/32            ident
#host    all             all             ::1/128                 ident
host    all             all              192.168.1.0/24         md5
host    all             all              10.0.0.0/24            md5
host    all             all              127.0.0.1/32           md5
host    all             all               ::1/128               md5
```

To effect changes:  `systemctl restart postgresql-11.service`
# Create Database
`createdb [name of DB]`

# Create User
```
psql [name of DB]
CREATE ROLE [username] WITH SUPERUSER LOGIN PASSWORD 'password';
\q
```

# Login as user
`psql -h [ip] -u [username] -p`

# Backup/Export
 `pg_dump -U [user] -h [hostname/IP] -W  --schema=[schema name] [database name] > backup-filename.sql`
 
 # Better Display of Records
 Using `psql` and selecting data can be hard to read. For a better view of records, use issue `\x` at the `psql` prompt.