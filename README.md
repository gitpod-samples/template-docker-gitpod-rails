After launching into your IDE

```
docker-compose run --no-deps myapp-web rails new . --force --database=postgresql
```
Rails app and files should then be created

If necessary, do 
```
sudo chown -R $USER:$USER .
```

Next, run
```
docker-compose build
```

To rebuild the image.

Edit config/database.yml to
```
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  host: <%= ENV.fetch('DB_HOST', 'localhost') %>
  database: <%= ENV['DB_NAME'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>

development:
  <<: *default

test:
  <<: *default
```
so we can have Rails connect to Postgres

Run 
```
docker-compose up
```
to launch the app.


To launch the Rails terminal (so you can use rails generate, etc.), do
```
docker exec -it <container> /bin/bash
```

