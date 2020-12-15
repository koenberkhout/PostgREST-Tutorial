---
title: JWT Generation
parent: Advanced
has_children: false
nav_order: 3
---

# JWT Generation
## Create tokens
You can create a valid JWT either from inside your database or via an external service. Each token is cryptographically signed with a secret key. In the case of symmetric cryptography the signer and verifier share the same secret passphrase. In asymmetric cryptography the signer uses the private key and the verifier the public key. PostgREST supports both symmetric and asymmetric cryptography. [2]

## JWT from PostgreSQL
Generating tokens in SQL can be done using the _pgjwt_ extension. Our database is hosted on Amazon RDS, which doesn't support installing extensions. After you installed _pgjwt_, you can create a functions that returns a JWT:

```sql
CREATE TYPE jwt_token AS (
  token text
);

CREATE FUNCTION jwt() RETURNS public.jwt_token AS $$
  SELECT public.sign(
    row_to_json(r), 'my_secret'
  ) AS token
  FROM (
    SELECT
      'my_role'::text as role,
      extract(epoch from now())::integer + 60 AS exp
  ) r;
$$ LANGUAGE sql;
```

PostgREST exposes functions via POST to _/rpc/function-name_. In our case, the function would be available on `https://dev-postgrest.herokuapp.com/rpc/jwt`. Making a POST request on this endpoint returns a JWT with an expiration time of 60 seconds. In practice, you should avoid hardcoding the secret in the function and save it as a property of the database instead. To do this, run `ALTER DATABASE mydb SET "jwt_secret" TO 'my_secret';`.