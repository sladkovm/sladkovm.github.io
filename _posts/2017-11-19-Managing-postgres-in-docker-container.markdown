---
layout: post
title:  "Managing Postgres in Docker container"
date:   2017-12-20 20:00:00 +0100
categories: webdev
comments: true
---


### Save the critical users data into a text file

Check that the *Docker Machine* is connected to the production server:

```
sladkovm:vmdash$ dm ls

NAME         ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
dev          -        virtualbox   Stopped                                       Unknown       
test         -        virtualbox   Stopped                                       Unknown       
velometria   *        generic      Running   tcp://146.185.150.29:2376           v17.06.1-ce
```

Check the status of the containers running at the production server:

```
511 sladkovm:vmdash$ dc -f docker-compose.prod.yml ps

     Name                   Command               State           Ports         
--------------------------------------------------------------------------------
api_server       gunicorn -w 1 -b :5000 man ...   Up      5000/tcp              
app_server       gunicorn -w 1 -b :8000 app ...   Up      8000/tcp              
db               docker-entrypoint.sh postgres    Up      0.0.0.0:5435->5432/tcp
vmdash_nginx_1   nginx -g daemon off;             Up      0.0.0.0:80->80/tcp  
```

Connect to the database container and launch the *psql* database inspector. Connect to the desired database and list the content of the desired table.

```
sladkovm:vmdash$ docker exec -it db psql -U postgres

psql (10.0)
Type "help" for help.

postgres=# \l
                                 List of databases
    Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
------------+----------+----------+------------+------------+-----------------------
 db_prod    | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 db_staging | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 postgres   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
            |          |          |            |            | postgres=CTc/postgres
 template1  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
            |          |          |            |            | postgres=CTc/postgres
(5 rows)

postgres=# \c db_prod

You are now connected to database "db_prod" as user "postgres".
db_prod=# \dt
          List of relations
 Schema |   Name   | Type  |  Owner   
--------+----------+-------+----------
 public | athletes | table | postgres
 public | users    | table | postgres
(2 rows)

db_prod=# SELECT * FROM users;


db_prod=# \q
```

### Connect and inspect the content of the *db* container using */bin/bash*

The [Database File Layout](https://www.postgresql.org/docs/9.2/static/storage-file-layout.html)


### Backup db

[Source](https://libertyseeds.ca/2017/08/07/PostgreSQL-Backup-and-Restore-Between-Docker-composed-Containers/)

Back up all databases into a text file.

```
$ docker exec -u postgres db pg_dumpall -c > ~/Dropbox/Projects/vmdata/dbbak/dump_`date +%d-%m-%Y"_"%H_%M_%S`.sql
```

Restore all databases from a text file:

```
docker exec -i db psql -U postgres db_prod < ~/Dropbox/Projects/vmdata/dbbak/dump_20-11-2017_21_43_59.sql
```


or

```
dc -f docker-compose.prod.yml exec -u postgres db pg_dump -Fc db_prod > ~/Dropbox/Projects/vmdata/dbbak/db_prod.dump
```
