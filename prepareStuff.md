## Set locale to UTF-8

```bash
sudo locale-gen en_US.UTF-8
sudo update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8
```
or
```bash
echo -e 'LANG=en_US.UTF-8\nLC_ALL=en_US.UTF-8' | sudo tee /etc/default/locale
source /etc/default/locale
```

### Install build essentials and make
```bash
sudo apt-get install autoconf binutils-doc bison build-essential flex checkinstall
```

##Install python software properties to be able to run `add-apt-repository`
```bash
sudo apt-get install python-software-properties
```
##Add CartoDB Base PPA
```bash
sudo add-apt-repository ppa:cartodb/base
```
##Add CartoDB GIS PPA
```bash
sudo add-apt-repository ppa:cartodb/gis
```
##Add CartoDB Mapnik PPA
```bash
sudo add-apt-repository ppa:cartodb/mapnik
```
##Add CartoDB Node PPA
```bash
sudo add-apt-repository ppa:cartodb/nodejs
```
##Add CartoDB Redis PPA
```bash
sudo add-apt-repository ppa:cartodb/redis
```
##Add CartoDB PostgreSQL PPA
```bash
sudo add-apt-repository  ppa:cartodb/postgresql-9.3
```
##Resfresh repositories to use the PPAs
```bash
sudo apt-get update
```

##Install unp for archive file upload support
```bash
sudo apt-get install unp
```
## Install zip for table exports
```bash
sudo apt-get install zip
```

##Install GEOS for geometry support
```bash
sudo apt-get install libgeos-c1v5 libgeos-dev
```
##Install GDAL for raster support
```bash
sudo apt-get install gdal-bin libgdal1-dev
sudo apt-get install ogr2ogr2-static-bin
```
## Install JSON-C for GeoJSON support
```bash
sudo apt-get install libjson0 python-simplejson libjson0-dev
```
##Install PROJ4 for reprojection support
```bqsh
sudo apt-get install proj proj-bin proj-data libproj-dev
```
##Install PostgreSQL 9.3 client packages
```bash
sudo apt-get install libpq5 libpq-dev postgresql-client-9.3 postgresql-client-common
```
##Install PostgreSQL 9.3 server packages along with `plpython` por Python support
```bash
sudo apt-get install postgresql-9.3 postgresql-contrib-9.3 postgresql-server-dev-9.3 postgresql-plpython-9.3
```

## Modify PostgreSQL pg_hba.conf file
```bash
sudo nano /etc/postgresql/9.3/main/pg_hba.conf
```
so it looks like
```bash
local   all             postgres                                trust
local   all             all                                     trust
host    all             all             127.0.0.1/32            trust
```

## Restart PostgreSQL **twice**
```bash
sudo /etc/init.d/postgresql restart
```
or
```bash
sudo service postgresql restart
```

## Install PostGIS 2.1
```bash
sudo apt-get install libxml2-dev
cd /usr/local/src
sudo wget http://download.osgeo.org/postgis/source/postgis-2.1.7.tar.gz
sudo tar -xvzf postgis-2.1.7.tar.gz
cd postgis-2.1.7
sudo ./configure --with-raster --with-topology
sudo make
sudo make install
```
## Create `postgis_template`
```bash
sudo su - postgres
POSTGIS_SQL_PATH=`pg_config --sharedir`/contrib/postgis-2.1
createdb -E UTF8 template_postgis
createlang -d template_postgis plpgsql
psql -d postgres -c "UPDATE pg_database SET datistemplate='true' WHERE datname='template_postgis'"
psql -d template_postgis -c "CREATE EXTENSION postgis"
psql -d template_postgis -c "CREATE EXTENSION postgis_topology"
psql -d template_postgis -c "GRANT ALL ON geometry_columns TO PUBLIC;"
psql -d template_postgis -c "GRANT ALL ON spatial_ref_sys TO PUBLIC;"
exit
```
