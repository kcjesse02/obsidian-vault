# Docker

set-up the docker container (pull down from oracle)
```bash
docker run --name databased -d -p 1521:1521 -e ORACLE_PASSWORD=sillee gvenzl/oracle-xe
```

start docker container
```bash
docker start databased
```

create a user
```bash
docker exec -it databased sqlplus system/sillee@//localhost/databasedKath

#bypass password
/ as sysdba
```
create user sql command
```sql
create user kcjesse identified by beebo45;
```

