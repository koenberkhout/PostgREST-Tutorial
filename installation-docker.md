---
title: B. Docker installation
parent: Installation
has_children: false
nav_order: 2
---

# B. Docker installation
This method is a bit less laborious than installing everything manually. You just need Docker to get going.

## B.1 Install Docker
Skip this step if you already have Docker installed.

**MacOS (.dmg)**\
Download [Docker Desktop for MacOS](https://desktop.docker.com/mac/stable/Docker.dmg).

**Linux (Ubuntu)**\
See [installation instructions](https://docs.docker.com/engine/install/ubuntu/) for Ubuntu.

**Windows**\
Download [Docker Toolbox for Windows](https://docs.docker.com/toolbox/overview/).

## B.2 Start the engine
We'll be using the three Docker images that were mentioned in the introduction:
- postgrest/postgrest
- postgres
- swaggerapi/swagger-ui

Thus, there will be three containers. One for the PostgREST API, one for the PostgreSQL database, and an additional one for automatically generated Swagger API documentation. We are not going to run them individually. Docker provides `docker-compose` to link the containers together and run them in perfect harmony. Create a _docker-compose.yml_ file and copy-paste the example below, or simply download our ready-made [docker-compose.yml](sources/docker-compose.yml) sample file.

```
# docker-compose.yml

version: '3'
services:
  server:
    image: postgrest/postgrest
    ports:
      - "3000:3000"
    links:
      - db:db
    environment:
      PGRST_DB_URI: postgres://authenticator:Fontys123@dev-postgres.cperpcjgj3fr.eu-west-1.rds.amazonaws.com:5432/presidents
      PGRST_DB_SCHEMA: api
      PGRST_DB_ANON_ROLE: readonly
      PGRST_SERVER_PROXY_URI: "http://127.0.0.1:3000"
      PGRST_JWT_SECRET: "mfn4iDz7ttt7pdLCChs1mLskfPZOZ4vX"
    depends_on:
      - db
  db:
    image: postgres
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: presidents
      POSTGRES_USER: authenticator
      POSTGRES_PASSWORD: Fontys123
  swagger:
    image: swaggerapi/swagger-ui
    ports:
      - "8080:8080"
    expose:
      - "8080"
    environment:
      API_URL: http://localhost:3000/
```
Open the terminal in the folder that holds _docker-compose.yml_ and run `docker-compose up`. You should see something like
```
Creating sources_swagger_1 ... done
Creating sources_db_1      ... done
Creating sources_server_1  ... done
(...)
Connection successful
```

That's it! PostgREST is now running at `localhost:3000`, PostgreSQL at `localhost:5432` and you can check the automatically generated Swagger API docs at `localhost:8080`.