---
title: Swagger UI
parent: Advanced
has_children: false
nav_order: 1
---

# Swagger UI
In the _Installation_ section we saw that API documentation was automatically generated when running `docker-compose up` with our custom _docker-compose.yml_ file. Now that our PostgreSQL database runs on Amazon RDS, and the PostgREST server on Heroku, we can still generate OpenAPI/Swagger documentation for our API. The OpenAPI specification is running on the root domain of the app, in our case _https://dev-postgrest.herokuapp.com/_. Make sure Docker is running, fire up the terminal and run the following commands:

`docker pull swaggerapi/swagger-ui`\
`docker run -p 8080:8080 -e API_URL=https://dev-postgrest.herokuapp.com/ swaggerapi/swagger-ui`

Now you can view the documentation for the API that runs on Heroku by visiting _localhost:8080_ in your browser.