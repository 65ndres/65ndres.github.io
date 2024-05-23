---
title: Move Your Existing Rails, React and Postgres to Docker
date: 2024-01-17 12:00:00 -500
categories: [programming]
tags: [tutorial,docker,rails,react,postgres]
---

Suppose you started building a full stack application using React and Rails and then you realized you wanted to containerize it using Docker so that all developers on your team can have the same consistent developer experience. The goal is to Dockerize an existing application so that deployment of your application is seamless and reduces uncertainty across environments

Pre-requisites

* Existing React, Rails application with Postgres as the database
* Docker installed

## Game Plan

1. Create new directory structure
2. Create Dockerfile for each new directory
3. Create docker-compose file
4. Build images

### Create New Directory Structure

To get started we will be moving our applications into a new structure. First, move all of the Rails files into a new `/api` directory and all of the frontend files into a new `/client` directory

Project Structure:

```
root directory/
│
├── api (rails backend)
│   ├── Dockerfile
│   ├── entrypoint.sh
│   ├── Gemfile
│   ├── Gemfile.lock
│   └── ... (other ruby / rails files)
│
├── docker-compose.yaml
├── .gitignore
│
├── client (react frontend)
│   ├── ... (other react frontend files)
│   ├── package.json
│   └── Dockerfile
│
└── README.md
```

### Create Dockerfile For Each New Directory

Now we need to create a `Dockerfile` for each of our new two folders. The `Dockerfile` will be the set of instructions that Docker will use to create the images withing the container.

#### API Directory

In the root of your Rails project create a file, name it `Dockerfile` and paste the following:

```bash
FROM ruby:2.7.4
RUN apt-get update -qq && apt-get install -y nodejs postgresql-client
WORKDIR /api
COPY . .
COPY Gemfile /api/Gemfile
COPY Gemfile.lock /api/Gemfile.lock
RUN bundle install

# Add a script to be executed every time the container starts.
COPY entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
EXPOSE 3000

# Configure the main process to run when running the image
CMD ["rails", "server", "-b", "0.0.0.0"]
```

Along with the `Dockerfile` we will be creating another file next to it called `entrypoint.sh` and we will be editing the `config/database.yml` file.
This addresses a specific issue in Rails relating to server processes identified (pids).

```bash
#!/bin/bash
set -e

# Remove a potentially pre-existing server.pid for Rails.
rm -f /api/tmp/pids/server.pid


# Then exec the container's main process (what's set as CMD in the Dockerfile).
exec "$@"
```


Update the default section of `config/database.yml` to include host, username and password

```yml
default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  username: postgres
  password: password
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
```

#### Client Directory

In the clients `Dockerfile`, add the below code.

```bash
FROM node

WORKDIR /client
COPY package.json . #!!!the "." is important!!

# install app dependencies
COPY package.json ./client/package.json
COPY package-lock.json ./client/package-lock.json
RUN npm install

# add app
COPY . ./
ENV NODE_ENV development
EXPOSE 4000

# start app
CMD ["npm", "run", "dev"] 
```
### Create docker-compose file

Next in the root directory, create a `docker-compose.yml` file with the below code. Here, we are instructing Docker to spin up three services, a database service `db`, an `api` service for Rails and a frontend `client` service for React. The image property is what we are choosing to name these containers. The volume property represents where Docker is mounting the code base and allows the images that Docker create to update when you make changes to your code, without the need to rebuild each time (saving you lots of time!)

The commands should be familiar — these are the terminal commands that you would run to start a rails server on the backend at `port 3000` and similarly install dependencies (using npm) and start the application within the client directory at `port 4000`.

```yml
services:
  db:
    image: postgres
    volumes:
      - ./tmp/db:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: password
    working_dir: /api
  api:
    build: api
    image: rails-api
    command: bash -c "bundle exec rails s -p 3000 -b '0.0.0.0'"
    stdin_open: true
    tty: true
    ports:
      - "3000:3000"
    working_dir: /api
    volumes:
      - ./api:/api
    depends_on:
      - db
  client:
    build: client
    image: react-client
    command: sh -c "npm install && npm run dev"
    ports:
      - "4000:4000"
    working_dir: /client
    volumes:
      - ./client:/client
    environment:
      POSTGRES_PASSWORD: password
```

Update `.gitignore` — after moving all of the rails files into a separate directory, the paths in your gitignore will need to be updated by appending `api/` and `client/` as required. Make sure you ignore the master key correctly so it does not get pushed to Github.

```yml
####### api #######
# Ignore bundler config.
api/.bundle

# Ignore all logfiles and tempfiles.
/log/*
/tmp/*
api/log/*
api/tmp/*
!/api/log/.keep
!/api/tmp/.keep

# Ignore pidfiles, but keep the directory.
api/tmp/pids/*
!/api/tmp/pids/
!/api/tmp/pids/.keep

# Ignore uploaded files in development.
api/storage/*
!/api/storage/.keep
api/tmp/storage/*
!/api/tmp/storage/
!/api/tmp/storage/.keep

# Ignore master key for decrypting credentials and more.
api/config/master.key

api/.DS_Store
api/.byebug_history


####### client #######
# dependencies
client/node_modules
client/.pnp
client/.pnp.js

# testing
client/coverage

# production
client/build

# misc
client/.DS_Store
client/.env.local
client/.env.development.local
client/.env.test.local
client/.env.production.local

client/npm-debug.log*
client/yarn-debug.log*
client/yarn-error.log*
```

### Build Images

Finally, build and start the Docker images by running the following command in your project’s root directory. You only need the “ --build” tag the first time you build. Aftewards, just run “docker compose up”.

```bash
docker compose up --build
```

Depending on your application, this can take some time. For me, with a simple React Rails Postgres application, it took about 2 minutes to build the three containers and another few minutes for the dependencies to be downloaded and installed.

You should now be able to test your frontend by going to `localhost:4000` and your backend on `localhost:3000`.

Since I used rails to configure my database, I have database migrations and seed files that I can run. To do this with docker compose, simply run the following command:

```bash
docker compose run api rails db:create db:migrate
```

In fact, to run any rails commands within docker, simply add “docker compose run `api` followed by the rails command where `api` is the name of the Docker service we created for our rails backend. For example, if I wanted to enter rails console, this would be the command:

```bash
docker compose run api rails c
```
When you are finished with your work, you can close down the docker containers and images by running:

```bash
docker compose down
```

And if you end up with quite a few images and containers that are taking up too much space, you can run the following command to clean up containers, networks and remove build caches. I like to run this to clean up old unused builds to keep my system clean.

```bash
docker system prune --all
```
## Conclusion


<!-- order. Create new folder structure -->
<!-- create docker file for each -->
<!-- create docker-compose file -->
<!-- build images -->