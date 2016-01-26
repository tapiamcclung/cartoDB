#Running CartoDB
In order to run your first development version of CartoDB, you will create a development env and our user/subdomain is going to be 'development'
```bash
export SUBDOMAIN=development

# Enter the `cartodb` directory.
cd ~/cartodb
# If it's a system wide installation
bundle install

# Start redis, if you haven't done so yet
# Redis must be running when starting either the
# node apps or rails or running the ``create_dev_user script``
# NOTE: the default server port is 6379, and the default
#       configuration expects redis to be listening there
redis-server &

# If you are using rbenv simply run:
rbenv local 1.9.3-p551
bundle install

# Configure the application constants
cp config/app_config.yml.sample config/app_config.yml
nano config/app_config.yml
```
Maybe configure things like `session_domain`, `http_port`, etc.
```bash
# Configure your postgres database connection details
cp config/database.yml.sample config/database.yml
nano config/database.yml

# Add entries to /etc/hosts needed in development
echo "127.0.0.1 ${SUBDOMAIN}.localhost.lan" | sudo tee -a /etc/hosts

# Create a development user
#
# The script will ask you for passwords and email
#
# Read the script for more informations about how to perform
# individual steps of user creation and settings management
#
sh script/create_dev_user ${SUBDOMAIN}
```
User password must be at least 6 characters
```bash
# Run this to sync user config
# from postgres metadata to redis metadata
# per: https://groups.google.com/d/msg/cartodb/-9QB9D4ZfSc/_tngHpl48U0J
# Configuring redis persistence should ensure
# that this won't need to be re-run
./script/restore_redis
```
##Start the resque daemon (needed for import jobs):
```bash
bundle exec script/resque
```
Finally, start the CartoDB development server on port 3000:
```bash
bundle exec rails s -p 3000
```
You should now be able to access `http://<mysubdomain>.localhost.lan:3000` in your browser and login with the password specified above.
```

#Next time you want to start up everything run:
```bash
# start CartoDB-SQL-API
cd ~/CartoDB-SQL-API
node app.js development

# start Tile server
cd ~/Windshaft-cartodb
node app.js development

# start CartoDB
cd ~/cartodb
redis-server
bundle exec script/resque
bundle exec rails s -p 3000
```

# Handy tasks
For a full list of CartoDB utility tasks:
```bash
bundle exec rake -T
```
#Using foreman
You can also use foreman to run the full stack (cartodb server, sql api, tiler, redis and resque), using a single command: IMPORTANT: You need to install foreman by yourself. It's not included in the Gemfile. Run this:
```bash
bundle exec gem install foreman
bundle exec foreman start -p $PORT
```
where `$PORT` is the port you want to attach the rails server to.

## check if things are running
`http://localhost:8181/version` for Windsaft and `http://localhost:8080/api/v1/version` for SQL API
