---
title: Introduction
has_children: false
nav_order: 1
---

# Introduction
## Next-level RESTing :)
### Brought to you by [@koenberkhout](https://github.com/koenberkhout) and [@shleppy](https://github.com/shleppy).

---

## What is PostgREST?
Let's start with a short introduction to PostgREST and cite the authors:
>PostgREST serves a fully RESTful API from any existing PostgreSQL database. It provides a cleaner, more standards-compliant, faster API than you are likely to write from scratch. [1]

> PostgREST is a standalone web server that turns your PostgreSQL database directly into a RESTful API. The structural constraints and permissions in the database determine the API endpoints and operations. [2]

![Image](images/web-postgrest-server.png)

PostGREST is a good alternative to manually building an API for an existing PostgreSQL database. Custom APIs often suffer from duplicates in the business logic such as role management. The PostGREST philophy establishes a single source of truth: the data itself.

We also need to talk about efficiency. PostgreSQL uses its query planner to figure out details of a query, and joins data much faster than you could do by looping through rows yourself.

Remember: **single source of truth => consistency** and **built-in query planner => speed**.

## The REST dialect/OpenAPI standard
PostgREST adheres to the OpenAPI standard, and every API hosted by PostgREST automatically serves a full OpenAPI description on the root path. This provides a list of all endpoints, along with supported HTTP requests (GET, PATCH, POST, DELETE) and example payloads. OpenAPI is defined as follows:

> The OpenAPI Specification (OAS) defines a standard, programming language-agnostic interface description for REST APIs, which allows both humans and computers to discover and understand the capabilities of a service without requiring access to source code, additional documentation, or inspection of network traffic. When properly defined via OpenAPI, a consumer can understand and interact with the remote service with a minimal amount of implementation logic. Similar to what interface descriptions have done for lower-level programming, the OpenAPI Specification removes guesswork in calling a service. [3]

The fact that PostgREST adheres to this specification has certain benefits. Clients can understand and consume services without knowledge of server implementation or access to the server code. Also, documentation and source code are kept in sync, which is good for consistency. Swagger UI is a tool that can consume and generate documentation from the API description that is present on the root path of the PostgREST installation.

## Prerequisites
These prerequisites depend on your chosen installation method, you don't have to install anything now.
- PostgreSQL database
- PostgREST server
- Docker
  - image: postgrest/postgrest
  - image: postgres
  - image: swaggerapi/swagger-ui
- API request tool (Postman/Insomnia/other)

Don't worry, the database is already up-and-running in the cloud, and we'll provide you with a docker-compose.yml script with all images pre-configured and linked together.

## Warning
The database credentials shown in this tutorial have limited privileges and are for demonstration purposes only. Make sure you never publish or commit any usernames, passwords, or connection strings to your repository.