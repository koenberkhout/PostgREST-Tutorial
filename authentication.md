---
title: Authentication
has_children: false
nav_order: 4
---

# Authentication
At this point, we assume that you have everything installed and running. Let's head over to `localhost:8080` and have a look at the generated API. Make a GET request to the _admin_vpres_ endpoint that lives on `locahost:3000/admin_vpres` and inspect the result. Also try to make a POST, DELETE and PATCH request to the same endpoint. You'll see that the response is _401 Unauthorized_, because we have set the default role in PostgREST to _readonly_.

But we want the API to be a bit more interesting, so we are going to work with roles and JSON Web Token (JWT) authentication.

## Step 1: Create new role
In the previous chapter we created the default role _readonly_ that is used to execute anonymous web requests. This role only has read (SELECT) privileges, and thus any other request method results in a 401 error code. Now we are going to make it a bit more interesting by adding a _student_ role that can add entries to the _stud_input_ table.
```sql
CREATE ROLE student NOLOGIN;
GRANT USAGE ON SCHEMA api TO student;
GRANT SELECT ON ALL TABLES IN SCHEMA api TO student;
GRANT INSERT ON api.stud_input TO student;
GRANT student TO authenticator;
```
With `GRANT student TO authenticator` we give the _authenticator_ role the ability to switch to the _student_ role, given that a correct password is provided. Additionally, the `GRANT INSERT ON api.stud_input TO student` statement enables the _student_ role to insert new values into the _stud_input_ table. Consequently, a POST request on the _stud_input_ endpoint should result in a new value in the table.

The password for _student_ is defined in _docker-compose.yml_: `PGRST_JWT_SECRET: "mfn4iDz7ttt7pdLCChs1mLskfPZOZ4vX"` (or in _postgrest.conf_ if you followed the manual installation steps).

## Step 2: Create JTW
Generate a token on <a href="https://jwt.io/#debugger-io" target="_blank">jwt.io</a>. In box 1, replace the sample content with `role: student`. In box 2, replace _your-256-bit-secret_ with the password that we defined earlier: `mfn4iDz7ttt7pdLCChs1mLskfPZOZ4vXz`. Copy the resulting JWT from box 3: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoic3R1ZGVudCJ9.jQDNGAJLPK4XLRkqd9FcQb-eewo604Ve9960hxe_W00`.

![Image](images/jwt-io.png)

## Step 3: Make POST request
Open Postman, Insomnia or any other API tool. Set the request method to POST and enter _http://localhost:3000/stud_input_ in the request URL field:
![Image](images/postman-1.png)

---
Then, open the _Body_ tab and enter the payload. The _stud_input_ table has only one column and the request body must have a corresponding field:
```json
{
  "input_value": "your_input_value_here"
}
```
![Image](images/postman-2.png)

---
Lastly, open the _Auth_ tab, select TYPE: Bearer Token and fill in the JWT that we generated in step 2 `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoic3R1ZGVudCJ9.jQDNGAJLPK4XLRkqd9FcQb-eewo604Ve9960hxe_W00`:

![Image](images/postman-3.png)

Before using the authentication header, any non-GET request resulted in a _401 Unauthorized_ message. Now, with the added JWT authentication, this request results in _201 Created_. Exactly what we want!