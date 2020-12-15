---
title: Exercises
has_children: false
nav_order: 7
---

# Exercises
## Introduction
Remember the _presidents_ database that was used in the DBS course? That's the one we will be using today, so you should already be quite familiar with it. It is deployed to an Amazon AWS RDS instance, so there's no need to run the Postgres database locally.

![Image](images/presidents-schema.png)

## Getting Started
First, follow the steps below:

1. Start Docker
2. Download our pre-defined [docker-compose.yml](https://sebivenlo.github.io/PostgREST-Workshop/sources/docker-compose.yml)
3. Open your terminal/command line and navigate to the folder where you saved _docker-compose.yml_
4. Run the command `docker-compose up`

The docker-compose.yml file contains the following configuration lines. 

```json
version: '3'
services:
  server:
    image: postgrest/postgrest
    ports:
      - "3000:3000"
    environment:
      PGRST_DB_URI: postgres://authenticator:Fontys123@dev-postgres.cperpcjgj3fr.eu-west-1.rds.amazonaws.com:5432/presidents
      PGRST_DB_SCHEMA: api
      PGRST_DB_ANON_ROLE: readonly
      PGRST_SERVER_PROXY_URI: "http://localhost:3000"
      PGRST_JWT_SECRET: "mfn4iDz7ttt7pdLCChs1mLskfPZOZ4vX"
  swagger:
    image: swaggerapi/swagger-ui
    ports:
      - "8080:8080"
    expose:
      - "8080"
    environment:
      API_URL: http://localhost:3000/
```

---

## Exercise 1
Now that you have PostgREST running locally, it is time to start exploring the API. Your local PostgREST server is connected to a presidents database that is centrally deployed to Amazon RDS.

1. Navigate to <a href="http://localhost:3000/" target="_blank">localhost:3000</a>
2. Look for the title and description of the service

## Exercise 2
1. Navigate to <a href="http://localhost:8080/" target="_blank">localhost:8080</a>
2. Convince yourself that this corresponds to the raw API description from exercise 1

--- 

#### Comment

In the next exercises, you are going to find out more about the presidents using filters. We'll give you a few hints first:

- Use Postman/Insomnia/other client to make requests
- The server lives on <a href="http://localhost:3000/" target="_blank">localhost:3000</a>
- Filter horizontally (similar to "WHERE") using `eq, gt, gte, lt, lte, neq`, For example `years_served=eq.4`.
- Filter vertically (similar to "SELECT") using the `select=` query parameter. The argument is a comma-separated list.
- Order (similar to "ORDER BY") using the `order={column}.[asc/desc]` query parameter. For example `order=name.asc`.
- A valid sample url is: `http://localhost:3000/president?birth_year=lt.1850&years_served=gt.7&select=name&order=name.desc`.

## Exercise 3
Retrieve a list of presidents that were born after 1920 and served for less than five years.

## Exercise 4
The same as exercise 2, but this time only get the name, birth year and time served of the presidents.

## Exercise 5
The same as exercise 3, but this time order the result on the presidents' year of birth in ascending order.

---

#### Comment

PostgREST needs to be configured with an anonymous role, the `PGRST_DB_ANON_ROLE`. In the _docker-compose.yml_ file you can see that this is set to _readonly_. This role was added to the _presidents_ database using the following SQL statements:

```sql
CREATE ROLE readonly NOLOGIN;
GRANT USAGE ON SCHEMA api TO readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA api TO readonly;
```

When PostgREST receives a request, it automatically switches to this _readonly_ role. Since this role has only been granted `USAGE` rights, any request method other than GET should result in a _401 Unauthorized_ response.

We want the API to be a bit more interesting, so we are going to work with custom roles and JSON Web Token (JWT) authentication. For this reason, the _stud_input_ table has been added to the _presidents_ database.

## Exercise 6
Make a GET request to `http://localhost:3000/stud_input` and take note of the response code.

## Exercise 7
Make a POST request to `http://localhost:3000/stud_input` with the following body (select raw, JSON):

```json
{
  "input_value": "ADD YOUR CUSTOM INPUT VALUE HERE"
}
```

Take note of the response code.

---

#### Comment

Your POST request to `http://localhost:3000/stud_input` should result in a _401 Unauthorized_ response. This is because your _readonly_ role is not allowed to insert new data into the database. For this reason, we added a _student_ role to the database which allows INSERTs on the _stud_input_ table. The role was added using the following SQL statement:

```sql
CREATE ROLE student NOLOGIN;
GRANT USAGE ON SCHEMA api TO student;
GRANT SELECT ON ALL TABLES IN SCHEMA api TO student;
GRANT INSERT ON api.stud_input TO student;
GRANT student TO authenticator;
```

Furthermore, in the _docker-compose.yml_ file a `PGRST_JWT_SECRET` is defined: _mfn4iDz7ttt7pdLCChs1mLskfPZOZ4vX_. This is the password that can be used for authentication. But we need to transform it into a JWT first. In the following exercises you'll need:

**Payload:**
```json
{
    "role": "student"
}
```

## Exercise 8
1. Go to <a href="https://jwt.io/#debugger-io" target="_blank">https://jwt.io/#debugger-io</a>
2. Replace the entire PAYLOAD with the example payload above
3. Replace the _your-256-bit-secret_ value under VERIFY SIGNATURE with `mfn4iDz7ttt7pdLCChs1mLskfPZOZ4vX`
4. Take note of the JWT that is automatically generated on the left

## Exercise 9
Add the JWT from the previous exercise as _Bearer token_ to Postman, and make a POST request to `http://localhost:3000/stud_input` with the following body (select raw, JSON):

```json
{
  "input_value": "ADD YOUR CUSTOM INPUT VALUE HERE"
}
```

You should see a _201 Created_ response code, and your value has been successfully added to the database.