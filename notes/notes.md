# Notes

- [PostgreSQL quick notes 🐘](#postgresql-quick-notes-)
- [Databases and Tables](#databases-and-tables)
  - [Databases](#databases)
  - [Default databases](#default-databases)
  - [Creating and connecting](#creating-and-connecting)
  - [Tables](#tables)
- [Data](#data)
  - [Inserts](#inserts)
  - [Updates and Deletes](#updates-and-deletes)
  - [Selects](#selects)
  - [Like and Functions](#like-and-functions)
  - [Node.js and PostgreSQL](#nodejs-and-postgresql)
  - [Project](#project)
- [Joins and Constraints](#joins-and-constraints)
  - [Relationships](#relationships)
  - [Other Types of Joins](#other-types-of-joins)
  - [Foreign Keys](#foreign-keys)
  - [Many-to-Many Relationships](#many-to-many-relationships)
  - [Constraints](#constraints)
  - [Project](#project-1)
- [JSONB](#jsonb)
  - [Unstructured Data](#unstructured-data)
  - [JSONB](#jsonb-1)
  - [When to Use](#when-to-use)
- [Aggregation](#aggregation)
  - [Aggregation](#aggregation-1)
  - [HAVING](#having)
- [Functions Triggers and Procedures](#functions-triggers-and-procedures)
  - [Functions](#functions)
  - [Procedures](#procedures)
  - [Triggers](#triggers)
- [The Movie Database](#the-movie-database)
  - [Exercises](#exercises)
  - [Answers](#answers)
  - [pgAdmin](#pgadmin)
- [Query Performance](#query-performance)
  - [Explain](#explain)
  - [Indexes](#indexes)
  - [Create an Index](#create-an-index)
  - [GIN](#gin)
  - [Partial Indexes](#partial-indexes)
  - [Derivative Value Indexes](#derivative-value-indexes)
- [Views](#views)
  - [Basic Views](#basic-views)
  - [Materialized Views](#materialized-views)
- [Subqueries](#subqueries)
  - [How to Subquery](#how-to-subquery)
  - [Arrays](#arrays)
- [Transactions](#transactions)
  - [Transactions](#transactions-1)
- [Window Functions](#window-functions)
  - [Window Functions](#window-functions-1)
- [Self Join](#self-join)
  - [Self Join](#self-join-1)
- [Conclusion](#conclusion)
  - [Congrats](#congrats)
  - [Project](#project-2)

## PostgreSQL quick notes 🐘

> Here I'll collect small pieces of info about PostgreSQL

- Good for sending quick data. It's not meanted to work with big files.
- Useful errors. *Key (title)=(broccoli) already exists*

## Databases and Tables

### Databases

> **A database is a collection of similar tables of data**.

We've already created the DB using docker. Now we'll enter its terminal.
Let's connect to the running database by running the following: `docker exec -u postgres -it pg psql`.

### Default databases

template1 is what Postgres uses by default when you create a database. If you want your databases to have a default shape, you can modify template1 to suit that.

template0 should never be modified. If your template1 gets out of whack, you can drop it and recreate from the fresh template0.

postgres exists for the purpose of a default database to connect to. We're actually connected to it by default since we didn't specify a database to connect to. You can technically could delete it but there's no reason to and a lot of tools and extensions do rely on it being there.

### Creating and connecting

-   Create the database: `CREATE DATABASE recipeguru`
-   Connect to it: `\c recipeguru`
-   Dropping the database: `DROP DATABASE recipeguru`

> **One or more databases?**
>
> There's a lot of schools of thought of when to shove everything into one database versus when to decompose it all into various different databases. I generally think in terms of clients: if my app is going to be querying for the data, then I'll try to keep it in an app-oriented database. If I have another client that is going to be storing client analytics for my team to digest later, I'd probably put that in a separate database. It's a bit vague of when you should, but think in terms of "what if I had to scale these independently" sorts of terms.

> **Killing the container**
>
> Note if you kill your container, it will kill all the data with it and you will need to recreate everything if you do that. I do provide this complete SQL file that has everything for the recipeguru database but it's for the whole lesson so if you do need to restart it may be easier to go back and do the creation by hand. You can also just use the container from Docker Hub.

### Tables

Let's create a table and insert data.

```sql
CREATE TABLE ingredients (
  id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  title VARCHAR ( 255 ) UNIQUE NOT NULL
);

INSERT INTO ingredients (title) VALUES ('bell pepper')
```

> `GENERATED ALWAYS AS IDENTITY`
>
> Basically says create this by your own and increment the id by 1 with every new row.

> **Types**
>
> INT, INT4 or INTEGER are the same thing. Feel free to use what suits you.
> Similarly DEC, FIXED, and DECIMAL are the same thing.

Now let's show our database and records with `\d` and `SELECT * FROM ingredients`.

```bash
postgres=# \d
                 List of relations
 Schema |        Name        |   Type   |  Owner
--------+--------------------+----------+----------
 public | ingredients        | table    | postgres
 public | ingredients_id_seq | sequence | postgres
(2 rows)

postgres=#
```

```sql
postgres=# SELECT * FROM ingredients;
 id |    title
----+-------------
  1 | bell pepper
(1 row)

postgres=#
```

Let's alter the table. What happens if we wanted to add a third field to our table?

```sql
ALTER TABLE ingredients ADD COLUMN image VARCHAR ( 255 );
```

We could also delete it with

```sql
ALTER TABLE ingredients DROP COLUMN image;
```

Another way to alter a table is:

```sql
ALTER TABLE ingredients
ADD COLUMN image VARCHAR ( 255 ),
ADD COLUMN type VARCHAR ( 50 ) NOT NULL DEFAULT 'vegetable';
```

Specifying a DEFAULT when using a NOT NULL constraint will prevent errors if the column has existing null values.

Let's check our table:

```bash
postgres=# \d ingredients
                               Table "public.ingredients"
 Column |          Type          | Collation | Nullable |            Default
--------+------------------------+-----------+----------+--------------------------------
 id     | integer                |           | not null | generated always as identity
 title  | character varying(255) |           | not null |
 image  | character varying(255) |           |          |
 type   | character varying(50)  |           | not null | 'vegetable'::character varying
Indexes:
    "ingredients_pkey" PRIMARY KEY, btree (id)
    "ingredients_title_key" UNIQUE CONSTRAINT, btree (title)

postgres=#
```

## Data

> Use **`--`** for comments.

### Inserts

```sql
INSERT INTO ingredients (
 title, image, type
) VALUES (
  'red pepper', 'red_pepper.jpg', 'vegetable'
);
```

> **Single quotes ≠ Double quotes**
>
> Single quotes in SQL means "this is a literal value". Double quotes in SQL mean "this is an identifier of some variety".

```sql
INSERT INTO "ingredients" (
 "title", "image", "type" -- Notice the " here
) VALUES (
  'broccoli', 'broccoli.jpg', 'vegetable' -- and the ' here
);
```

### Updates and Deletes

### Selects

### Like and Functions

### Node.js and PostgreSQL

### Project

## Joins and Constraints

### Relationships

### Other Types of Joins

### Foreign Keys

### Many-to-Many Relationships

### Constraints

### Project

## JSONB

### Unstructured Data

### JSONB

### When to Use

## Aggregation

### Aggregation

### HAVING

## Functions Triggers and Procedures

### Functions

### Procedures

### Triggers

## The Movie Database

### Exercises

### Answers

### pgAdmin

## Query Performance

### Explain

### Indexes

### Create an Index

### GIN

### Partial Indexes

### Derivative Value Indexes

## Views

### Basic Views

### Materialized Views

## Subqueries

### How to Subquery

### Arrays

## Transactions

### Transactions

## Window Functions

### Window Functions

## Self Join

### Self Join

## Conclusion

### Congrats

### Project
