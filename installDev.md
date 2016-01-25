##Install cartodb-postgresql
```bash
cd ~
git clone https://github.com/cartoDB/pg_schema_triggers.git
cd pg_schema_triggers
sudo make all install PGUSER=postgres
sudo make installcheck PGUSER=postgres # to run tests
cd ..
git clone git@github.com:CartoDB/cartodb-postgresql.git
cd cartodb-postgresql
git checkout cdb
sudo make all install
sudo PGUSER=postgres make installcheck # to run tests
```
