# Setting up Docker with Rails

The purpose is to have a docker network where you'll have the railsapp running from one container and the DB running on another. We can also choose to separate the frontend in a similar manner.

Make sure you inspect the docker-compose.yml file. If you have any questions, copy and paste that file into ChatGPT and it will explain the configurations.

I'm going to automate some of these steps in the .gitpod.yml, however, feel free to modify or delete the file, if you don't want those commands running. Here is the full process:

**1) Launch this workspace in Gitpod (or your local IDE)**

**2) Install Rails onto the myrails Docker container**
The starting Gemfile just has us loading rails. 

Create the new Rails app with the one-time command of:
```
docker-compose run --no-deps myrailsapp rails new . --force --database=postgresql
```

- This is like doing a ```docker run``` while also implementing some settings specified in the docker-compose.yml )

- You can also apply Rails installation settings by replacing ```rails new .``` with whatever configurations you prefer (like --api)

**3) Change file ownerships**

Run

```sudo chown -R $USER:$USER .```

because Docker just created some files in your workspace as the user, "root". Need to make sure the user permissions actually match your own.

**4) Rebuild the image and container for the Rails app, given the new installations**
(Notice how the Gemfile was updated when we installed Rails)

Run
```docker-compose build```

**5) Configure Rails to connect to our Postgres database**
Edit config/database.yml to incorporate the proper DB settings:

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

Note that we have specified the environment variables in the docker-compose.yml file.
You can change the usernames and passwords there.

**6) Allow the proper hosts (for Gitpod users, or anyone experiencing Blocked Host errors)**
In config/environments/development.rb, within the Rails.application.configure block, put in

```
config.hosts << /.*\.gitpod\.io/
```

**7) Launch your app, with all its services!**
```
docker-compose up
```

This will launch your Rails server as well as the Postgres DB.

If you want to shut everything down, just do
```
docker-compose down
```

And then to launch everything back up, it's as easy as ```docker-compose up```

**To execute rails terminal commands**
Run
```docker exec -it <container_id_or_name> bash```


To get container IDs or names, use ```docker-compose ps``` and/or ```docker ps```

From the new bash prompt, you can run your standard rails commands like ```rails generate```, ```rails console```, etc.





