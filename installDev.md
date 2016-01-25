##Install cartodb-postgresql
```bash
cd ~
git clone https://github.com/cartoDB/pg_schema_triggers.git
cd pg_schema_triggers
sudo make all install PGUSER=postgres
sudo make installcheck PGUSER=postgres # to run tests
cd ..
git clone https://github.com/CartoDB/cartodb-postgresql.git
cd cartodb-postgresql
```
check latest cartodb-postgresql tag
```bash
git tag -l
```
and checkout that  tag
```bash
git checkout <tag> # for instance git checkout 0.11.5
sudo make all install
sudo PGUSER=postgres make installcheck # to run tests
```

**NOTE**: if test_ddl_triggers fails it's likely due to an incomplete installation of schema_triggers. You need to add schema_triggers.so to the shared_preload_libraries setting in postgresql.conf :
```bash
sudo nano /etc/postgresql/9.3/main/postgresql.conf
...
shared_preload_libraries = 'schema_triggers.so'
```
and restart PostgreSQL
```bash
sudo service postgresql restart
```
After this change the 2nd installcheck of cartodb-postresql should be OK.

##Install Ruby
