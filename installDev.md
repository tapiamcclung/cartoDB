#Clone cartoDB to home directory
```bash
cd ~
git clone --recursive https://github.com/CartoDB/cartodb.git
```

#Install cartodb-postgresql
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

#Install Ruby and rbenv
From the official guide on https://github.com/sstephenson/rbenv#installation
```bash
git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
```
Restart bash or open a new terminal window or `source ~/.bashrc`
```bash
type rbenv
 ```
and you should get
`#=> "rbenv is a function"...`

Now install ruby build:
```bash
git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
```
so you can run 
```bash 
rbenv install -l
```
and see what is available. Install & set Ruby:
```bash
#rbenv install 1.9.3-p551 # this takes a while...
#rbenv global 1.9.3-p551
rbenv install 2.2.3 # this takes a while...
rbenv global 2.2.3
```
if you get an error about `ERROR: Ruby install aborted due to missing extensions`, run
```bash
sudo apt-get install libreadline-dev
```
and then install and set Ruby again.

Then, to install bundler simply run:
```bash
gem install bundler
```

##Add cartoDB Node PPA
```bash
sudo add-apt-repository ppa:cartodb/nodejs-010
sudo apt-get update
```
Install Node.js since the tiler API and SQL API are both Node.js apps
```bash
sudo apt-get install nodejs
```
Check for versions NodeJS 0.10.26 and npm 1.4.3
```bash
nodejs -v
npm -v
```

aqui voy
##Install Redis for cartoDB components like Windshaft or the SQL API
```bash
sudo apt-get install redis-server
```
Redis needs to be made persistent; see here for details. First, make the config file:
```bash
cd wherever/cartodb # cd /home/cartodb
nano redis.conf
```
Then, add this line to configure RDB persistence:
```bash
save 60 1000
```
This configuration seems to work in development, however if any issues arise with Redis it may need to be tweaked.
