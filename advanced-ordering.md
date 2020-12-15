---
title: Filtering & Ordering
parent: Advanced
has_children: false
nav_order: 2
---

# Filtering & Ordering
In SQL you can filter out results by adding a `WHERE` or `SELECT` clause, and order the results by adding an `ORDER BY` clause. PostgREST has some nice built-in functionality to perform these actions for you. Just append the conditions as parameters to your request, and the database will do the heavy lifting for you.

## Filtering
### 1. Horizontal filtering
You can filter result rows by adding a condition on columns. This correstponds with using the `WHERE` clause in PostgreSQL. The condition is provided to PostgREST as a query string parameter. Recall the _president_ table in the database, and let's say that you would like to get an overview of presidents that were born after 1920 and served for less than five years. Then you could make te following request:

`https://dev-postgrest.herokuapp.com/president?birth_year=gt.1920&years_served=lt.5`

And the result is:
```json
[
    {
        "id": 38,
        "name": "CARTER J M",
        "birth_year": 1924,
        "years_served": 4,
        "death_age": null,
        "party": "DEMOCRATIC",
        "state_id_born": 43
    },
    {
        "id": 39,
        "name": "BUSH G H W",
        "birth_year": 1924,
        "years_served": 4,
        "death_age": null,
        "party": "REPUBLICAN",
        "state_id_born": 38
    }
]
```

There are many operators available, but the most common ones are _eq_, _gt_, _gte_, _lt_, _lte_, _neq_. You can negate any operator by adding _not._ in front of it.

### 2. Vertical filtering
Just like you can filter rows, you can also filter colums. This correstponds with using the `SELECT` clause in PostgreSQL. You can specify which colums you would like to see by using the _select_ parameter. Let's say you would only like to see the name, birth year and time served for presidents that were born after 1920 and served for less than five years. If you append `&select=name,birth_year,years_served`, the new request would be:

`https://dev-postgrest.herokuapp.com/president?birth_year=gt.1900&years_served=lt.5&select=name,birth_year,years_served`

And the result is:

```json
[
    {
        "name": "KENNEDY J F",
        "birth_year": 1917,
        "years_served": 2
    },
    {
        "name": "FORD G R",
        "birth_year": 1913,
        "years_served": 2
    },
    {
        "name": "CARTER J M",
        "birth_year": 1924,
        "years_served": 4
    },
    {
        "name": "BUSH G H W",
        "birth_year": 1924,
        "years_served": 4
    }
]
```

---

## Ordering
PostgREST can also order the result rows for you. This correstponds with using the `ORDER BY` clause in PostgreSQL. You can specify on which colums you would like to order the results by using the _order_ parameter. The direction can be either _asc_ or _desc_ and can optionally be chained with _.nullsfirst_ or _.nullslast_. Let's say you would like to order the results from the previous example on _birth_year_ in ascending order. Append `&order=birth_year.asc`, the new request would be:

`https://dev-postgrest.herokuapp.com/president?birth_year=gt.1900&years_served=lt.5&select=name,birth_year,years_served&order=birth_year.asc`

And the result is:

```json
[
    {
        "name": "FORD G R",
        "birth_year": 1913,
        "years_served": 2
    },
    {
        "name": "KENNEDY J F",
        "birth_year": 1917,
        "years_served": 2
    },
    {
        "name": "CARTER J M",
        "birth_year": 1924,
        "years_served": 4
    },
    {
        "name": "BUSH G H W",
        "birth_year": 1924,
        "years_served": 4
    }
]
```
Awesome!