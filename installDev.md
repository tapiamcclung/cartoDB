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

##Install Redis for cartoDB components like Windshaft or the SQL API
```bash
sudo apt-get install redis-server
```
Redis needs to be made persistent; see here for details. First, make the config file:
```bash
cd wherever/cartodb # cd /home/rtm/cartodb
nano redis.conf
```
Then, add this line to configure RDB persistence:
```bash
save 60 1000
```
This configuration seems to work in development, however if any issues arise with Redis it may need to be tweaked.

## Install Python dependencies
```bash
sudo apt-get install python2.7-dev
sudo apt-get install build-essential
sudo apt-get install python-setuptools
sudo apt-get install python-all-dev
```
#Install pip
```bash
sudo wget  -O /tmp/get-pip.py https://bootstrap.pypa.io/get-pip.py
sudo python /tmp/get-pip.py
```

#Install Python GDAL
```bash
sudo apt-get install python-gdal
```
#Modify `python_requirements.txt` to use GDAL 1.11.0
```bash
nano python_requirements.txt
...
gdal==1.11.0
```
##Install cartoDB Python requirements
```bash
export CPLUS_INCLUDE_PATH=/usr/include/gdal
export C_INCLUDE_PATH=/usr/include/gdal
export PATH=$PATH:/usr/include/gdal
pip install --no-use-wheel -r python_requirements.txt
```
#Install Mapnik
```bash
sudo apt-get install libmapnik-dev python-mapnik mapnik-utils
```
#Install CartoDB SQL API
```bash
cd ~
git clone https://github.com/CartoDB/CartoDB-SQL-API.git
cd CartoDB-SQL-API
git checkout master
npm install
cp config/environments/development.js.example config/environments/development.js
```
To run CartoDB SQL API in development mode, simply type:
```bash
node app.js development
```
#Install Windshaft-cartodb
First, install pango
```bash
sudo apt-get install libpango1.0-dev
```
Then, to install Windshaft:
```bash
cd ~
git clone http://github.com/CartoDB/Windshaft-cartodb.git
cd Windshaft-cartodb
git checkout master
npm install
cp config/environments/development.js.example config/environments/development.js
```
To run Windshaft-cartodb in development mode, simply type:
```bash
node app.js development
```
#Install ImageMagick
```bash
sudo apt-get install imagemagick
```
#~~Activate Sync Tables~~
~~Enable sync tables in the interface for all the users in your local install by updating the `sync_tables_enabled` row on the users table:~~
```bash
sudo su
su postgres
psql carto_db_development

# If this gives an error about
# postgresql boolean types,
# change 1 to 'true':
UPDATE users SET sync_tables_enabled = 1;

\q
exit
exit
```
~~However, sync tables also require a script to run every 15 minutes, which will enqueue pending synchronizations (run this in the cartodb/ directory):~~
```bash
RAILS_ENV=development bundle exec rake cartodb:sync_tables
```
~~This command will need to be scheduled to run at a regular interval, i.e. every 15 minutes or so. Also, the environment should be changed in the command as necessary. Add to crontab like so:~~
```bash
*/15 * * * *    username  cd /wherever/cartodb && RAILS_ENV=development bundle exec rake cartodb:sync_tables
```
#Optional components
##Varnish web app accelerator
```bash
sudo add-apt-repository  ppa:cartodb/varnish
sudo apt-get update
sudo apt-get install varnish=2.1.5.1-cdb1 #or any version <3.x
```
Varnish should allow telnet access in order to work with CartoDB, so you need to edit the varnish file 
```bash
sudo nano /etc/default/varnish
```
and in `the DAEMON_OPTS` variable remove the `-S /etc/varnish/secret \` line.
##Raster import support
Check raster2pgsql is in your path by doing:
```bash
which raster2pgsql
```
If it's not, you should link it:
```bash
sudo ln -s /usr/local/src/postgis-2.1.7/raster/loader/raster2pgsql /usr/bin/.
```
Access to temporary dir is also needed. Run
```bash
sudo chown 504:staff /usr/local/src/postgis-2.1.7/raster/loader/.libs 
```maybe replacing 504:staff with your installation `/usr/local/src/postgis-2.1.7/raster/loader/` group and owner).
