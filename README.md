# 📘 SQL — Complete Reference Guide

> **A comprehensive, beginner-to-advanced SQL reference** covering every concept with detailed explanations, real-world examples, syntax breakdowns, and best practices. All examples use **PostgreSQL** syntax with MySQL/SQLite notes where they differ.

---

## 📚 Table of Contents

| # | Topic | Description |
|---|---|---|
| 1 | [Introduction to SQL](#1-introduction-to-sql) | What SQL is, how databases work, SQL dialects |
| 2 | [Data Types](#2-data-types) | All data types with examples |
| 3 | [Database & Schema Operations](#3-database--schema-operations) | CREATE/DROP databases, schemas |
| 4 | [CREATE TABLE](#4-create-table) | Table creation, all options |
| 5 | [Constraints](#5-constraints) | PRIMARY KEY, FOREIGN KEY, CHECK, UNIQUE, NOT NULL |
| 6 | [INSERT](#6-insert) | Adding rows, bulk inserts, upserts |
| 7 | [SELECT](#7-select) | Querying data, aliases, expressions |
| 8 | [WHERE](#8-where) | Filtering, operators, patterns |
| 9 | [ORDER BY](#9-order-by) | Sorting results |
| 10 | [LIMIT & OFFSET](#10-limit--offset) | Pagination |
| 11 | [UPDATE](#11-update) | Modifying rows |
| 12 | [DELETE & TRUNCATE](#12-delete--truncate) | Removing rows |
| 13 | [ALTER TABLE](#13-alter-table) | Modifying schema |
| 14 | [JOINS](#14-joins) | INNER, LEFT, RIGHT, FULL, CROSS, SELF |
| 15 | [Aggregate Functions](#15-aggregate-functions) | COUNT, SUM, AVG, MIN, MAX |
| 16 | [GROUP BY & HAVING](#16-group-by--having) | Grouping and filtering groups |
| 17 | [Subqueries](#17-subqueries) | Nested queries, correlated subqueries |
| 18 | [CTEs](#18-common-table-expressions-ctes) | WITH clauses, recursive CTEs |
| 19 | [Window Functions](#19-window-functions) | RANK, LAG, LEAD, running totals |
| 20 | [String Functions](#20-string-functions) | Text manipulation |
| 21 | [Numeric Functions](#21-numeric-functions) | Math, rounding, statistics |
| 22 | [Date & Time Functions](#22-date--time-functions) | Date arithmetic, formatting |
| 23 | [NULL Handling](#23-null-handling) | COALESCE, NULLIF, IS NULL |
| 24 | [CASE Expressions](#24-case-expressions) | Conditional logic in SQL |
| 25 | [Indexes](#25-indexes) | B-tree, partial, composite indexes |
| 26 | [Views](#26-views) | Regular and materialized views |
| 27 | [Stored Procedures & Functions](#27-stored-procedures--functions) | Reusable logic |
| 28 | [Triggers](#28-triggers) | Automatic actions on data changes |
| 29 | [Transactions & ACID](#29-transactions--acid) | BEGIN, COMMIT, ROLLBACK, isolation |
| 30 | [Normalization](#30-normalization) | 1NF through 3NF, denormalization |
| 31 | [Advanced Query Patterns](#31-advanced-query-patterns) | Pivots, gaps, hierarchies |
| 32 | [Performance & Optimization](#32-performance--optimization) | EXPLAIN, query tuning |
| 33 | [Security & Access Control](#33-security--access-control) | GRANT, REVOKE, roles |

---

## 1. Introduction to SQL

### What is SQL?

**SQL (Structured Query Language)** is the standard language for managing and querying data in **Relational Database Management Systems (RDBMS)**. It is declarative — you describe *what* you want, not *how* to get it.

```
How a SQL query flows:

┌────────────────────────────────────────────────────────────┐
│  Your Application                                          │
│  const users = await db.query("SELECT * FROM users")       │
└────────────────────────┬───────────────────────────────────┘
                         │ SQL string
                         ▼
┌────────────────────────────────────────────────────────────┐
│  Database Engine                                           │
│  1. Parse SQL text                                         │
│  2. Validate syntax and permissions                        │
│  3. Query planner: find the most efficient execution plan  │
│  4. Execute: read pages from disk / memory                 │
│  5. Return result set                                      │
└────────────────────────┬───────────────────────────────────┘
                         │ rows of data
                         ▼
┌────────────────────────────────────────────────────────────┐
│  Result: [{ id:1, name:"Alice" }, { id:2, name:"Bob" }]    │
└────────────────────────────────────────────────────────────┘
```

### Database Structure

```
SERVER
 └── DATABASE  (e.g. "ecommerce")
      └── SCHEMA  (e.g. "public", "sales", "inventory")
           └── TABLES  (e.g. "customers", "orders", "products")
                ├── COLUMNS  (name, data type, constraints)
                └── ROWS     (actual data records)
```

```sql
-- Visual example of a table:
TABLE: customers
┌─────────────┬────────────┬───────────┬─────────────────────┬──────────┐
│ customer_id │ first_name │ last_name │        email        │  city    │
├─────────────┼────────────┼───────────┼─────────────────────┼──────────┤
│      1      │   Alice    │   Smith   │ alice@example.com   │ New York │  ← ROW
│      2      │    Bob     │   Jones   │  bob@example.com    │ Chicago  │  ← ROW
│      3      │   Carol    │   White   │ carol@example.com   │  Dallas  │  ← ROW
└─────────────┴────────────┴───────────┴─────────────────────┴──────────┘
      ↑              ↑           ↑               ↑                ↑
   COLUMN          COLUMN      COLUMN          COLUMN           COLUMN
```

### SQL Command Categories

```sql
-- DDL — Data Definition Language: structure changes
CREATE TABLE products (...);
ALTER  TABLE products ADD COLUMN weight DECIMAL;
DROP   TABLE products;
TRUNCATE TABLE logs;

-- DML — Data Manipulation Language: row changes
INSERT INTO products VALUES (...);
UPDATE products SET price = 19.99 WHERE id = 1;
DELETE FROM products WHERE id = 1;

-- DQL — Data Query Language: reading data
SELECT * FROM products WHERE price < 50 ORDER BY name;

-- DCL — Data Control Language: permissions
GRANT  SELECT ON products TO readonly_user;
REVOKE INSERT ON products FROM readonly_user;

-- TCL — Transaction Control Language: ACID control
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;   -- or ROLLBACK;
```

### Sample Schema Used Throughout This Guide

```sql
-- Complete e-commerce schema used for all examples in this guide

CREATE TABLE customers (
    customer_id   SERIAL        PRIMARY KEY,
    first_name    VARCHAR(50)   NOT NULL,
    last_name     VARCHAR(50)   NOT NULL,
    email         VARCHAR(100)  NOT NULL UNIQUE,
    phone         VARCHAR(20),
    city          VARCHAR(50),
    country       VARCHAR(50)   DEFAULT 'USA',
    created_at    TIMESTAMPTZ   DEFAULT NOW(),
    is_active     BOOLEAN       DEFAULT TRUE
);

CREATE TABLE categories (
    category_id   SERIAL       PRIMARY KEY,
    name          VARCHAR(50)  NOT NULL UNIQUE,
    description   TEXT,
    parent_id     INT          REFERENCES categories(category_id)
);

CREATE TABLE products (
    product_id    SERIAL         PRIMARY KEY,
    name          VARCHAR(100)   NOT NULL,
    description   TEXT,
    price         DECIMAL(10,2)  NOT NULL CHECK (price >= 0),
    stock         INT            NOT NULL DEFAULT 0 CHECK (stock >= 0),
    category_id   INT            REFERENCES categories(category_id),
    created_at    TIMESTAMPTZ    DEFAULT NOW(),
    is_active     BOOLEAN        DEFAULT TRUE
);

CREATE TABLE orders (
    order_id      SERIAL        PRIMARY KEY,
    customer_id   INT           NOT NULL REFERENCES customers(customer_id),
    status        VARCHAR(20)   NOT NULL DEFAULT 'pending'
                  CHECK (status IN ('pending','processing','shipped','delivered','cancelled')),
    total_amount  DECIMAL(10,2),
    notes         TEXT,
    ordered_at    TIMESTAMPTZ   DEFAULT NOW(),
    shipped_at    TIMESTAMPTZ,
    delivered_at  TIMESTAMPTZ
);

CREATE TABLE order_items (
    item_id       SERIAL         PRIMARY KEY,
    order_id      INT            NOT NULL REFERENCES orders(order_id)    ON DELETE CASCADE,
    product_id    INT            NOT NULL REFERENCES products(product_id),
    quantity      INT            NOT NULL CHECK (quantity > 0),
    unit_price    DECIMAL(10,2)  NOT NULL,
    discount      DECIMAL(5,2)   DEFAULT 0
);

CREATE TABLE reviews (
    review_id     SERIAL        PRIMARY KEY,
    product_id    INT           NOT NULL REFERENCES products(product_id),
    customer_id   INT           NOT NULL REFERENCES customers(customer_id),
    rating        INT           NOT NULL CHECK (rating BETWEEN 1 AND 5),
    title         VARCHAR(200),
    body          TEXT,
    created_at    TIMESTAMPTZ   DEFAULT NOW(),
    UNIQUE (product_id, customer_id)
);
```

---

## 2. Data Types

### Numeric Types

```sql
-- ─── Integer Types ────────────────────────────────────────
SMALLINT          -- 2 bytes  │ -32,768 to 32,767
INTEGER / INT     -- 4 bytes  │ -2,147,483,648 to 2,147,483,647
BIGINT            -- 8 bytes  │ very large whole numbers

-- Auto-increment (PostgreSQL):
SERIAL            -- auto-increment INTEGER (1, 2, 3...)
BIGSERIAL         -- auto-increment BIGINT
SMALLSERIAL       -- auto-increment SMALLINT

-- Auto-increment (MySQL):
id INT AUTO_INCREMENT PRIMARY KEY

-- ─── Exact Decimal (use for money!) ──────────────────────
DECIMAL(precision, scale)   -- exact; DECIMAL(10,2) = up to 99999999.99
NUMERIC(precision, scale)   -- identical to DECIMAL

-- ─── Floating Point (imprecise — avoid for money) ─────────
REAL               -- 4-byte float (~6 decimal digits precision)
DOUBLE PRECISION   -- 8-byte float (~15 decimal digits precision)
FLOAT(n)           -- synonym for REAL or DOUBLE PRECISION

-- ─── Demonstration ────────────────────────────────────────
CREATE TABLE numeric_demo (
    item_id        SERIAL         PRIMARY KEY,
    quantity       INT,                      -- whole numbers
    views          BIGINT,                   -- very large whole numbers
    price          DECIMAL(10, 2),           -- exactly 2 decimal places ✅
    weight_kg      NUMERIC(8, 3),            -- 3 decimal places
    ratio          DOUBLE PRECISION,         -- scientific computations
    percentage     DECIMAL(5, 4)             -- e.g. 0.0825 = 8.25%
);

-- ⚠️ NEVER use FLOAT for money:
SELECT 0.1 + 0.2;                  -- → 0.30000000000000004 (WRONG!)
SELECT 0.1::DECIMAL + 0.2::DECIMAL; -- → 0.3 (CORRECT)
```

### String Types

```sql
-- ─── Character Types ──────────────────────────────────────
CHAR(n)           -- fixed-length, padded with spaces to n chars
VARCHAR(n)        -- variable-length, max n characters
TEXT              -- unlimited length (PostgreSQL/MySQL)

-- ─── Usage guidelines ────────────────────────────────────
CREATE TABLE string_demo (
    country_code  CHAR(2)       NOT NULL,         -- always 2: 'US', 'GB'
    status        VARCHAR(20)   DEFAULT 'active',  -- short fixed-set values
    full_name     VARCHAR(100)  NOT NULL,           -- names, titles
    email         VARCHAR(254)  NOT NULL UNIQUE,    -- email max length RFC
    slug          VARCHAR(255),                     -- URL-friendly identifiers
    description   TEXT,                             -- paragraphs, long text
    bio           TEXT,                             -- unlimited narrative
    content       TEXT                              -- articles, HTML, markdown
);

-- ─── String literals ─────────────────────────────────────
SELECT 'Hello, World!';          -- single quotes (SQL standard)
SELECT 'It''s a nice day';       -- escape ' by doubling it
SELECT E'Line1\nLine2';          -- escape sequences (PostgreSQL E-string)
SELECT $$Dollar quoted$$;        -- dollar quoting avoids escape issues (PG)
SELECT "Hello";                  -- MySQL uses double-quotes for strings
```

### Date & Time Types

```sql
-- ─── Temporal Types ──────────────────────────────────────
DATE                        -- date only:            '2024-01-15'
TIME                        -- time only:            '14:30:00'
TIME WITH TIME ZONE         -- time + timezone:      '14:30:00+05:30'
TIMESTAMP                   -- date + time:          '2024-01-15 14:30:00'
TIMESTAMPTZ                 -- date + time + tz:     '2024-01-15 14:30:00+00'
INTERVAL                    -- a duration:           '1 year 3 months 5 days'

-- ─── Best Practice ────────────────────────────────────────
-- Always use TIMESTAMPTZ (with time zone) for timestamps
-- Store everything in UTC, convert to local on display

CREATE TABLE temporal_demo (
    event_id      SERIAL        PRIMARY KEY,
    event_date    DATE,                           -- 2024-12-25
    event_time    TIME,                           -- 09:00:00
    created_at    TIMESTAMPTZ   DEFAULT NOW(),    -- ✅ recommended
    duration      INTERVAL,                       -- '2 hours 30 minutes'
    birth_date    DATE                            -- just the date part
);

-- ─── Literals ────────────────────────────────────────────
SELECT DATE '2024-01-15';
SELECT TIME '14:30:00';
SELECT TIMESTAMP '2024-01-15 14:30:00';
SELECT TIMESTAMPTZ '2024-01-15 14:30:00+00';
SELECT INTERVAL '1 year 2 months 15 days';
SELECT INTERVAL '3 hours 30 minutes';
```

### Other Important Types

```sql
-- ─── Boolean ──────────────────────────────────────────────
BOOLEAN     -- TRUE, FALSE, NULL
-- Accepted inputs: true/false, 't'/'f', 'yes'/'no', 'on'/'off', '1'/'0'

SELECT TRUE, FALSE;
SELECT NOT TRUE;         -- FALSE
SELECT TRUE AND FALSE;   -- FALSE
SELECT TRUE OR FALSE;    -- TRUE

-- ─── UUID ─────────────────────────────────────────────────
UUID        -- '550e8400-e29b-41d4-a716-446655440000'

CREATE TABLE api_keys (
    key_id     UUID   DEFAULT gen_random_uuid() PRIMARY KEY,  -- PG 13+
    user_id    INT    REFERENCES customers(customer_id),
    key_value  TEXT   NOT NULL UNIQUE
);

SELECT gen_random_uuid();    -- generate a UUID (PostgreSQL)
SELECT UUID();               -- MySQL

-- ─── JSONB (PostgreSQL — preferred over JSON) ─────────────
-- JSON  = stores text, re-parses on each access
-- JSONB = stores binary, indexed, faster queries ✅

CREATE TABLE product_attributes (
    product_id    INT     PRIMARY KEY REFERENCES products(product_id),
    attributes    JSONB   -- {"color":"red","sizes":["S","M","L"],"weight":1.5}
);

-- Insert JSONB:
INSERT INTO product_attributes VALUES (1, '{"color":"red","weight":1.5,"tags":["sale"]}');

-- Query JSONB:
SELECT attributes->>'color'            AS color         -- text value
FROM   product_attributes;

SELECT attributes->'tags'              AS tags_array    -- JSON value (keeps array)
FROM   product_attributes;

SELECT * FROM product_attributes
WHERE  attributes @> '{"color":"red"}';                -- contains operator

-- ─── Arrays (PostgreSQL) ──────────────────────────────────
CREATE TABLE tag_demo (
    post_id   SERIAL   PRIMARY KEY,
    tags      TEXT[],                -- array of text
    scores    INT[]                  -- array of integers
);

INSERT INTO tag_demo (tags, scores) VALUES (ARRAY['sql','postgres','tutorial'], ARRAY[10,20,30]);

SELECT tags[1] FROM tag_demo;         -- first element (1-indexed in PG)
SELECT unnest(tags) FROM tag_demo;    -- expand array to rows
SELECT * FROM tag_demo WHERE 'sql' = ANY(tags);  -- element exists in array

-- ─── Type Casting ─────────────────────────────────────────
SELECT '42'::INT;               -- string → integer (PostgreSQL)
SELECT 42::TEXT;                -- integer → string
SELECT '3.14'::DECIMAL;        -- string → decimal
SELECT '2024-01-15'::DATE;     -- string → date
SELECT CAST('42' AS INT);      -- SQL standard syntax
SELECT CAST(price AS INT) FROM products;  -- truncates decimal

-- ─── ENUM Type (PostgreSQL) ───────────────────────────────
CREATE TYPE order_status_type AS ENUM ('pending','processing','shipped','delivered','cancelled');

CREATE TABLE orders_typed (
    order_id  SERIAL            PRIMARY KEY,
    status    order_status_type DEFAULT 'pending'
);
-- Adding a new value to ENUM:
ALTER TYPE order_status_type ADD VALUE 'refunded' AFTER 'delivered';
```

---

## 3. Database & Schema Operations

```sql
-- ─── Create a database ───────────────────────────────────
CREATE DATABASE ecommerce;

CREATE DATABASE ecommerce
    WITH ENCODING   = 'UTF8'
         LC_COLLATE = 'en_US.UTF-8'
         LC_CTYPE   = 'en_US.UTF-8'
         OWNER      = myuser;

-- ─── Connect to a database ────────────────────────────────
\c ecommerce           -- psql client command
USE ecommerce;         -- MySQL syntax

-- ─── Drop a database ─────────────────────────────────────
DROP DATABASE IF EXISTS old_test_db;    -- ⚠️ IRREVERSIBLE

-- ─── List databases ───────────────────────────────────────
\l                                      -- psql list all databases
SELECT datname FROM pg_database;        -- programmatic list

-- ─── Schemas (namespaces within a database) ───────────────
CREATE SCHEMA sales;
CREATE SCHEMA inventory;
CREATE SCHEMA IF NOT EXISTS reporting;

-- Use schema-qualified names:
CREATE TABLE sales.orders       (...);
CREATE TABLE inventory.products (...);
SELECT * FROM sales.orders;

-- Set the default search path (which schemas to search first):
SET search_path TO sales, public;

-- Current schema:
SELECT current_schema();

-- ─── Drop schemas ─────────────────────────────────────────
DROP SCHEMA IF EXISTS reporting CASCADE;  -- CASCADE drops everything inside
DROP SCHEMA IF EXISTS empty_schema RESTRICT;  -- fail if not empty

-- ─── Useful catalog queries ──────────────────────────────
-- List all tables in current schema:
SELECT table_name, table_type
FROM   information_schema.tables
WHERE  table_schema = 'public'
ORDER  BY table_name;

-- Describe a table's columns:
SELECT
    column_name,
    data_type,
    character_maximum_length,
    is_nullable,
    column_default
FROM information_schema.columns
WHERE table_name = 'customers'
ORDER BY ordinal_position;

-- psql shortcuts:
\dt            -- list tables
\d customers   -- describe table (columns, indexes, constraints)
\di            -- list indexes
\dv            -- list views
\df            -- list functions
\du            -- list users/roles
```

---

## 4. CREATE TABLE

### Basic Table Creation

```sql
-- ─── Minimal table ───────────────────────────────────────
CREATE TABLE products (
    product_id   SERIAL          PRIMARY KEY,
    name         VARCHAR(100)    NOT NULL,
    price        DECIMAL(10, 2)  NOT NULL
);

-- ─── Full table with all features ─────────────────────────
CREATE TABLE employees (

    -- Primary key:
    employee_id   SERIAL           PRIMARY KEY,

    -- Required fields:
    first_name    VARCHAR(50)      NOT NULL,
    last_name     VARCHAR(50)      NOT NULL,
    email         VARCHAR(100)     NOT NULL UNIQUE,

    -- Optional fields:
    phone         VARCHAR(20),
    birth_date    DATE,
    gender        CHAR(1)          CHECK (gender IN ('M','F','O','N')),

    -- Numbers with defaults:
    department_id INT,
    salary        DECIMAL(12, 2)   CHECK (salary > 0),
    hire_date     DATE             DEFAULT CURRENT_DATE,
    is_active     BOOLEAN          DEFAULT TRUE,

    -- Audit timestamps:
    created_at    TIMESTAMPTZ      DEFAULT NOW(),
    updated_at    TIMESTAMPTZ      DEFAULT NOW(),

    -- Self-referential foreign key:
    manager_id    INT              REFERENCES employees(employee_id),

    -- Table-level named constraints:
    CONSTRAINT emp_salary_range   CHECK (salary BETWEEN 0 AND 10000000),
    CONSTRAINT emp_unique_email   UNIQUE (email)
);

-- ─── CREATE TABLE IF NOT EXISTS ──────────────────────────
CREATE TABLE IF NOT EXISTS audit_log (
    log_id      SERIAL        PRIMARY KEY,
    table_name  VARCHAR(50)   NOT NULL,
    action      VARCHAR(10)   NOT NULL CHECK (action IN ('INSERT','UPDATE','DELETE')),
    changed_at  TIMESTAMPTZ   DEFAULT NOW(),
    changed_by  VARCHAR(50)   DEFAULT CURRENT_USER
);

-- ─── CREATE TABLE AS SELECT (CTAS) ───────────────────────
-- Creates a new table populated from a query:
CREATE TABLE premium_customers AS
    SELECT customer_id, first_name, last_name, email, city
    FROM   customers
    WHERE  customer_id IN (
        SELECT customer_id FROM orders
        GROUP  BY customer_id
        HAVING SUM(total_amount) > 1000
    );

-- Create empty copy (structure only, no data):
CREATE TABLE customers_backup AS
    SELECT * FROM customers WHERE 1 = 0;   -- always-false condition

-- ─── CREATE TABLE LIKE ────────────────────────────────────
-- Copy structure from another table:
CREATE TABLE customers_archive
    (LIKE customers INCLUDING ALL);         -- columns + defaults + constraints + indexes

CREATE TABLE customers_staging
    (LIKE customers INCLUDING DEFAULTS);    -- columns + defaults only
```

### Composite Primary Keys

```sql
-- Use when no single column uniquely identifies a row

CREATE TABLE order_items (
    order_id    INT           NOT NULL,
    product_id  INT           NOT NULL,
    quantity    INT           NOT NULL DEFAULT 1,
    unit_price  DECIMAL(10,2) NOT NULL,

    PRIMARY KEY (order_id, product_id),   -- composite PK

    FOREIGN KEY (order_id)   REFERENCES orders(order_id)   ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- Student course enrollment — natural composite PK:
CREATE TABLE enrollments (
    student_id   INT  NOT NULL REFERENCES students(student_id),
    course_id    INT  NOT NULL REFERENCES courses(course_id),
    enrolled_at  DATE DEFAULT CURRENT_DATE,
    grade        CHAR(2),
    PRIMARY KEY (student_id, course_id)   -- one row per student per course
);

-- Friendship table — symmetric relationship:
CREATE TABLE friendships (
    user_id_1    INT  NOT NULL REFERENCES users(user_id),
    user_id_2    INT  NOT NULL REFERENCES users(user_id),
    created_at   TIMESTAMPTZ DEFAULT NOW(),
    PRIMARY KEY (user_id_1, user_id_2),
    CHECK (user_id_1 < user_id_2)         -- prevent (A,B) and (B,A) duplicates
);
```

---

## 5. Constraints

### All Constraint Types Explained

```sql
-- ─── PRIMARY KEY ──────────────────────────────────────────
-- Uniquely identifies each row; implies NOT NULL + UNIQUE
-- Every table should have one

CREATE TABLE demo (
    -- Inline (column-level):
    id       SERIAL   PRIMARY KEY,

    -- Named (table-level):
    -- CONSTRAINT pk_demo PRIMARY KEY (id)
    -- CONSTRAINT pk_order_item PRIMARY KEY (order_id, product_id)  -- composite
);

-- ─── NOT NULL ─────────────────────────────────────────────
-- The column must always have a value; NULL not allowed

CREATE TABLE not_null_demo (
    id        SERIAL        PRIMARY KEY,
    required  VARCHAR(100)  NOT NULL,     -- must provide
    optional  VARCHAR(100)               -- can be NULL (default)
);

-- ─── UNIQUE ───────────────────────────────────────────────
-- No two rows can have the same non-NULL value in this column
-- NULLs are allowed (even multiple)

CREATE TABLE unique_demo (
    id        SERIAL        PRIMARY KEY,
    email     VARCHAR(100)  UNIQUE,           -- single-column unique
    username  VARCHAR(50)   UNIQUE,
    code      CHAR(6),
    region    VARCHAR(20),
    -- Composite unique (combination must be unique):
    CONSTRAINT uq_code_region UNIQUE (code, region)
);

-- ─── CHECK ────────────────────────────────────────────────
-- Row can only be inserted/updated if the expression is TRUE

CREATE TABLE check_demo (
    id          SERIAL         PRIMARY KEY,
    age         INT            CHECK (age >= 0 AND age <= 150),
    price       DECIMAL(10,2)  CHECK (price >= 0),
    rating      INT            CHECK (rating BETWEEN 1 AND 5),
    email       VARCHAR(100)   CHECK (email LIKE '%@%'),
    start_date  DATE,
    end_date    DATE,
    status      VARCHAR(20)    CHECK (status IN ('active','inactive','pending')),

    -- Table-level CHECK (can reference multiple columns):
    CONSTRAINT chk_dates CHECK (end_date IS NULL OR end_date > start_date)
);

-- ─── DEFAULT ──────────────────────────────────────────────
-- Value used when column is omitted in INSERT

CREATE TABLE defaults_demo (
    id          SERIAL         PRIMARY KEY,
    status      VARCHAR(20)    DEFAULT 'pending',
    is_active   BOOLEAN        DEFAULT TRUE,
    created_at  TIMESTAMPTZ    DEFAULT NOW(),
    country     CHAR(2)        DEFAULT 'US',
    quantity    INT            DEFAULT 1,
    score       DECIMAL(5,2)   DEFAULT 0.00
);

-- ─── FOREIGN KEY ──────────────────────────────────────────
-- Enforces referential integrity — value must exist in referenced table

CREATE TABLE orders (
    order_id    SERIAL   PRIMARY KEY,

    -- Inline FK:
    customer_id INT      NOT NULL REFERENCES customers(customer_id),

    -- Inline FK with actions:
    product_id  INT      REFERENCES products(product_id) ON DELETE SET NULL,

    -- Named table-level FK with all options:
    CONSTRAINT fk_orders_customer
        FOREIGN KEY (customer_id)
        REFERENCES  customers(customer_id)
        ON DELETE   RESTRICT        -- prevent deleting customer with orders
        ON UPDATE   CASCADE         -- update customer_id in orders if it changes
);

-- ─── ON DELETE / ON UPDATE Options ───────────────────────
-- CASCADE     → propagate the change to child rows
-- RESTRICT    → block the change if child rows exist (default)
-- SET NULL    → set FK column to NULL in child rows
-- SET DEFAULT → set FK column to its default in child rows
-- NO ACTION   → like RESTRICT but deferred

CREATE TABLE order_items (
    item_id     SERIAL         PRIMARY KEY,
    order_id    INT            NOT NULL,
    product_id  INT,

    FOREIGN KEY (order_id)   REFERENCES orders(order_id)   ON DELETE CASCADE,
    -- If an order is deleted → delete all its items automatically ✅

    FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL
    -- If a product is deleted → set product_id to NULL (item remains) ✅
);
```

### Managing Constraints

```sql
-- ─── Add constraints to existing table ───────────────────
ALTER TABLE products ADD CONSTRAINT chk_positive_price CHECK (price >= 0);
ALTER TABLE customers ADD CONSTRAINT uq_email UNIQUE (email);
ALTER TABLE orders ADD CONSTRAINT fk_orders_customer
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
    ON DELETE RESTRICT ON UPDATE CASCADE;

-- ─── Drop constraints ────────────────────────────────────
ALTER TABLE products  DROP CONSTRAINT chk_positive_price;
ALTER TABLE customers DROP CONSTRAINT uq_email;
ALTER TABLE orders    DROP CONSTRAINT fk_orders_customer;

-- ─── Add/Drop NOT NULL ────────────────────────────────────
ALTER TABLE customers ALTER COLUMN email SET NOT NULL;
ALTER TABLE customers ALTER COLUMN phone DROP NOT NULL;

-- ─── View all constraints on a table ─────────────────────
SELECT
    tc.constraint_name,
    tc.constraint_type,
    kcu.column_name,
    ccu.table_name  AS foreign_table,
    ccu.column_name AS foreign_column
FROM information_schema.table_constraints    tc
LEFT JOIN information_schema.key_column_usage kcu
       ON tc.constraint_name = kcu.constraint_name
LEFT JOIN information_schema.constraint_column_usage ccu
       ON tc.constraint_name = ccu.constraint_name
WHERE tc.table_name = 'orders';
```

---

## 6. INSERT

### Basic Inserts

```sql
-- ─── Single row — specify columns ────────────────────────
INSERT INTO customers (first_name, last_name, email, phone, city, country)
VALUES ('Alice', 'Smith', 'alice@email.com', '555-0100', 'New York', 'USA');

-- ─── Let defaults fill in ─────────────────────────────────
INSERT INTO customers (first_name, last_name, email)
VALUES ('Bob', 'Jones', 'bob@email.com');
-- country → 'USA', created_at → NOW(), is_active → TRUE (all defaults)

-- ─── Multiple rows in one statement ───────────────────────
INSERT INTO customers (first_name, last_name, email, city)
VALUES
    ('Carol',  'White',  'carol@email.com',  'Chicago'),
    ('Dave',   'Black',  'dave@email.com',   'Houston'),
    ('Eve',    'Green',  'eve@email.com',    'Phoenix'),
    ('Frank',  'Brown',  'frank@email.com',  'Seattle'),
    ('Grace',  'Hall',   'grace@email.com',  'Boston');
-- Much faster than 5 separate INSERT statements

-- ─── INSERT from SELECT ───────────────────────────────────
-- Copy rows from one table to another:
INSERT INTO customers_archive (customer_id, first_name, last_name, email, created_at)
    SELECT customer_id, first_name, last_name, email, created_at
    FROM   customers
    WHERE  is_active = FALSE
      AND  created_at < NOW() - INTERVAL '2 years';

-- Transform during insert:
INSERT INTO products_summary (category_id, product_count, avg_price)
    SELECT category_id, COUNT(*), AVG(price)
    FROM   products
    WHERE  is_active = TRUE
    GROUP  BY category_id;
```

### RETURNING and Upserts

```sql
-- ─── RETURNING — get auto-generated values back ───────────
-- Essential for getting SERIAL IDs after insert

INSERT INTO customers (first_name, last_name, email)
VALUES ('Heidi', 'Klein', 'heidi@email.com')
RETURNING *;
-- Returns: customer_id=7, first_name='Heidi', last_name='Klein', ...

-- Get specific columns only:
INSERT INTO orders (customer_id, status)
VALUES (7, 'pending')
RETURNING order_id, ordered_at;

-- Use in a CTE to chain operations:
WITH new_customer AS (
    INSERT INTO customers (first_name, last_name, email)
    VALUES ('Ivan', 'Stone', 'ivan@email.com')
    RETURNING customer_id
)
INSERT INTO orders (customer_id, status)
SELECT customer_id, 'pending'
FROM   new_customer;

-- ─── ON CONFLICT — UPSERT (PostgreSQL) ───────────────────
-- "Insert if not exists, otherwise update"

-- Do nothing on conflict (ignore duplicates):
INSERT INTO customers (email, first_name, last_name)
VALUES ('alice@email.com', 'Alice', 'Smith')
ON CONFLICT (email) DO NOTHING;

-- Update on conflict (true upsert):
INSERT INTO customers (email, first_name, last_name, phone)
VALUES ('alice@email.com', 'Alice', 'Smith', '555-9999')
ON CONFLICT (email)
DO UPDATE SET
    phone      = EXCLUDED.phone,          -- EXCLUDED = the row we tried to insert
    first_name = EXCLUDED.first_name,
    updated_at = NOW();

-- Upsert only when specific condition met:
INSERT INTO product_prices (product_id, price, effective_date)
VALUES (1, 29.99, CURRENT_DATE)
ON CONFLICT (product_id)
DO UPDATE SET
    price          = EXCLUDED.price,
    effective_date = EXCLUDED.effective_date
WHERE product_prices.price <> EXCLUDED.price;  -- only update if price actually changed

-- ─── MySQL Equivalent (INSERT ON DUPLICATE KEY) ───────────
INSERT INTO customers (email, first_name, last_name, phone)
VALUES ('alice@email.com', 'Alice', 'Smith', '555-9999')
ON DUPLICATE KEY UPDATE
    phone = VALUES(phone),
    first_name = VALUES(first_name);

-- ─── Bulk insert with DEFAULT values ─────────────────────
INSERT INTO order_items (order_id, product_id, quantity, unit_price)
VALUES
    (1, 101, 2, 29.99),
    (1, 205, 1, 49.99),
    (1, 310, 3, 9.99),
    (2, 101, 1, 29.99),
    (2, 450, 2, 14.99);
-- discount defaults to 0 for all
```

---

## 7. SELECT

### SELECT Fundamentals

```sql
-- ─── All columns ──────────────────────────────────────────
SELECT * FROM customers;
-- ⚠️ Avoid SELECT * in production code — list columns explicitly

-- ─── Specific columns ────────────────────────────────────
SELECT first_name, last_name, email
FROM   customers;

-- ─── Column aliases ───────────────────────────────────────
SELECT
    first_name              AS "First Name",    -- quoted: allows spaces
    last_name               AS last,            -- simple alias
    email,                                      -- no alias
    created_at              AS joined_on,
    customer_id             AS id
FROM customers;

-- ─── Computed columns ─────────────────────────────────────
SELECT
    first_name || ' ' || last_name       AS full_name,
    UPPER(email)                          AS email_upper,
    price * 1.08                          AS price_with_tax,
    price * (1 - discount / 100)          AS discounted_price,
    ROUND(price * quantity, 2)            AS line_total,
    NOW() - created_at                    AS account_age
FROM products;

-- ─── SELECT without FROM ──────────────────────────────────
-- Compute values, test functions:
SELECT
    2 + 2                     AS math,
    NOW()                     AS current_time,
    CURRENT_DATE              AS today,
    'Hello' || ' World'       AS greeting,
    ROUND(PI()::NUMERIC, 5)   AS pi,
    TRUE AND FALSE            AS bool_result,
    LENGTH('hello')           AS str_len;

-- ─── DISTINCT ────────────────────────────────────────────
SELECT DISTINCT country            FROM customers;               -- unique countries
SELECT DISTINCT city, country      FROM customers;               -- unique city+country combos
SELECT COUNT(DISTINCT country)     FROM customers AS unique_count;

-- ─── SELECT INTO — create table from query (PostgreSQL) ──
SELECT first_name, last_name, email
INTO   premium_customers
FROM   customers
WHERE  customer_id IN (
    SELECT customer_id FROM orders
    GROUP  BY customer_id HAVING SUM(total_amount) > 500
);
```

### Practical SELECT Examples

```sql
-- ─── Product catalog with computed fields ─────────────────
SELECT
    p.product_id,
    p.name                                              AS product_name,
    c.name                                              AS category,
    p.price,
    p.stock,
    CASE
        WHEN p.stock = 0  THEN 'Out of Stock'
        WHEN p.stock < 10 THEN 'Low Stock'
        ELSE                   'In Stock'
    END                                                 AS stock_status,
    COALESCE(ROUND(AVG(r.rating), 1), 0)               AS avg_rating,
    COUNT(r.review_id)                                  AS review_count
FROM      products    p
LEFT JOIN categories  c ON p.category_id  = c.category_id
LEFT JOIN reviews     r ON p.product_id   = r.product_id
WHERE p.is_active = TRUE
GROUP BY p.product_id, p.name, c.name, p.price, p.stock
ORDER BY avg_rating DESC, p.name ASC;

-- ─── Customer order summary ───────────────────────────────
SELECT
    c.customer_id,
    c.first_name || ' ' || c.last_name       AS customer_name,
    c.email,
    c.city,
    COUNT(o.order_id)                         AS total_orders,
    COALESCE(SUM(o.total_amount), 0)          AS lifetime_value,
    MIN(o.ordered_at)::DATE                   AS first_order,
    MAX(o.ordered_at)::DATE                   AS last_order
FROM      customers c
LEFT JOIN orders    o ON c.customer_id = o.customer_id
                     AND o.status != 'cancelled'
GROUP BY c.customer_id, c.first_name, c.last_name, c.email, c.city
ORDER BY lifetime_value DESC;
```

---

## 8. WHERE

### Comparison Operators

```sql
-- ─── Basic comparisons ───────────────────────────────────
SELECT * FROM products WHERE price  = 29.99;    -- equal
SELECT * FROM products WHERE price != 29.99;    -- not equal (PostgreSQL/MySQL)
SELECT * FROM products WHERE price <> 29.99;    -- not equal (SQL standard)
SELECT * FROM products WHERE price  > 50;       -- greater than
SELECT * FROM products WHERE price >= 50;       -- greater or equal
SELECT * FROM products WHERE price  < 50;       -- less than
SELECT * FROM products WHERE price <= 50;       -- less or equal

-- ─── BETWEEN (inclusive on both ends) ────────────────────
SELECT * FROM products WHERE price BETWEEN 10 AND 100;
-- Equivalent: WHERE price >= 10 AND price <= 100

SELECT * FROM orders
WHERE ordered_at BETWEEN '2024-01-01' AND '2024-12-31 23:59:59';

SELECT * FROM customers WHERE customer_id BETWEEN 100 AND 200;

-- NOT BETWEEN:
SELECT * FROM products WHERE price NOT BETWEEN 50 AND 200;

-- ─── IN — match against a list ────────────────────────────
SELECT * FROM customers WHERE country IN ('USA', 'Canada', 'UK', 'Australia');
-- Equivalent: WHERE country='USA' OR country='Canada' OR ...

SELECT * FROM orders WHERE status NOT IN ('cancelled', 'pending');

-- IN with subquery:
SELECT * FROM customers
WHERE customer_id IN (
    SELECT DISTINCT customer_id
    FROM   orders
    WHERE  status = 'delivered'
);

-- ─── Pattern Matching with LIKE ───────────────────────────
-- % = zero or more of any character
-- _ = exactly one of any character

SELECT * FROM customers WHERE email       LIKE '%@gmail.com';    -- ends with
SELECT * FROM customers WHERE first_name  LIKE 'A%';             -- starts with A
SELECT * FROM products  WHERE name        LIKE '%Pro%';           -- contains 'Pro'
SELECT * FROM customers WHERE phone       LIKE '555-____';        -- 555- + 4 chars
SELECT * FROM products  WHERE name        LIKE 'Widget_';         -- Widget + 1 char

-- ILIKE — case-insensitive LIKE (PostgreSQL):
SELECT * FROM customers WHERE email ILIKE '%@GMAIL.COM';

-- NOT LIKE:
SELECT * FROM products WHERE name NOT LIKE '%Discontinued%';

-- ─── Logical Operators ────────────────────────────────────
-- AND — both must be true:
SELECT * FROM products
WHERE is_active = TRUE AND price < 100 AND stock > 0;

-- OR — at least one must be true:
SELECT * FROM customers
WHERE country = 'USA' OR country = 'Canada';

-- NOT — negate:
SELECT * FROM products WHERE NOT is_active;
SELECT * FROM customers WHERE NOT (country = 'USA' OR country = 'Canada');

-- Parentheses control precedence:
SELECT * FROM products
WHERE is_active = TRUE
  AND (
      (price < 20 AND stock > 50)     -- cheap AND well stocked
      OR
      (price > 500)                   -- OR premium products
  );

-- ─── EXISTS — check if subquery has rows ──────────────────
-- Efficient because it stops at the first match

-- Customers who have placed at least one order:
SELECT c.customer_id, c.email
FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o
    WHERE o.customer_id = c.customer_id
);

-- Customers who have NEVER ordered:
SELECT c.customer_id, c.email
FROM customers c
WHERE NOT EXISTS (
    SELECT 1 FROM orders o
    WHERE o.customer_id = c.customer_id
);

-- ─── ALL / ANY / SOME ────────────────────────────────────
-- Price higher than ALL products in category 3:
SELECT name, price FROM products
WHERE price > ALL (SELECT price FROM products WHERE category_id = 3);

-- Price higher than at least one product in category 3:
SELECT name, price FROM products
WHERE price > ANY (SELECT price FROM products WHERE category_id = 3);
-- Equivalent: WHERE price > (SELECT MIN(price) FROM products WHERE category_id = 3)
```

---

## 9. ORDER BY

```sql
-- ─── Basic sorting ────────────────────────────────────────
SELECT * FROM products ORDER BY price;             -- ascending (default)
SELECT * FROM products ORDER BY price ASC;         -- explicit ascending
SELECT * FROM products ORDER BY price DESC;        -- descending
SELECT * FROM customers ORDER BY last_name;        -- alphabetical

-- ─── Multiple sort columns ────────────────────────────────
-- Sort by category first, then price within each category:
SELECT name, category_id, price
FROM   products
ORDER BY category_id ASC, price DESC;

-- Sort by country, then city, then customer name:
SELECT * FROM customers
ORDER BY country ASC, city ASC, last_name ASC, first_name ASC;

-- ─── Sort by alias ────────────────────────────────────────
SELECT
    first_name || ' ' || last_name AS full_name,
    created_at                      AS joined
FROM customers
ORDER BY full_name ASC, joined DESC;

-- ─── Sort by expression ──────────────────────────────────
SELECT name, price, stock
FROM   products
ORDER BY price * stock DESC;   -- sort by inventory value (computed)

-- Sort by string length:
SELECT name FROM products ORDER BY LENGTH(name) ASC;

-- ─── NULLS FIRST / NULLS LAST ─────────────────────────────
-- Default: NULLs sort LAST with ASC, FIRST with DESC (PostgreSQL)
SELECT * FROM products ORDER BY description ASC  NULLS LAST;    -- NULLs at end
SELECT * FROM products ORDER BY description ASC  NULLS FIRST;   -- NULLs at start
SELECT * FROM products ORDER BY description DESC NULLS LAST;    -- NULLs at end

-- ─── Custom sort order with CASE ──────────────────────────
SELECT * FROM orders
ORDER BY
    CASE status
        WHEN 'processing' THEN 1    -- most urgent
        WHEN 'pending'    THEN 2
        WHEN 'shipped'    THEN 3
        WHEN 'delivered'  THEN 4
        WHEN 'cancelled'  THEN 5
        ELSE                   6
    END ASC,
    ordered_at ASC;

-- ─── Random order ────────────────────────────────────────
SELECT * FROM products ORDER BY RANDOM() LIMIT 10;   -- PostgreSQL
SELECT * FROM products ORDER BY RAND()   LIMIT 10;   -- MySQL
```

---

## 10. LIMIT & OFFSET

```sql
-- ─── LIMIT — cap the result set ──────────────────────────
SELECT * FROM products ORDER BY price     LIMIT 10;   -- cheapest 10
SELECT * FROM products ORDER BY price DESC LIMIT 5;   -- 5 most expensive
SELECT * FROM products                    LIMIT 0;    -- 0 rows (useful for schema inspection)

-- ─── OFFSET — skip rows ──────────────────────────────────
-- Skip first 20 rows, return rows 21–30:
SELECT * FROM products ORDER BY product_id LIMIT 10 OFFSET 20;

-- ─── Pagination formula ───────────────────────────────────
-- Page 1: LIMIT 10 OFFSET 0    → rows  1-10
-- Page 2: LIMIT 10 OFFSET 10   → rows 11-20
-- Page 3: LIMIT 10 OFFSET 20   → rows 21-30
-- Formula: OFFSET = (page - 1) * page_size

-- API pagination example (page=3, per_page=25):
SELECT * FROM products
ORDER  BY product_id
LIMIT  25
OFFSET 50;   -- (3 - 1) * 25 = 50

-- ─── Count total pages ───────────────────────────────────
SELECT COUNT(*)                         AS total_rows   FROM products WHERE is_active = TRUE;
-- total_pages = CEIL(total_rows / page_size)

-- Get data + count in one query using CTE:
WITH filtered AS (
    SELECT * FROM products WHERE is_active = TRUE AND price < 100
)
SELECT
    *,
    (SELECT COUNT(*) FROM filtered) AS total_count
FROM   filtered
ORDER  BY name
LIMIT  20 OFFSET 0;

-- ─── TOP (SQL Server) ────────────────────────────────────
SELECT TOP 10 * FROM products ORDER BY price;

-- ─── FETCH FIRST (SQL Standard — works in PostgreSQL) ─────
SELECT * FROM products ORDER BY price FETCH FIRST 10 ROWS ONLY;
SELECT * FROM products ORDER BY price OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY;

-- ─── ⚠️ Large OFFSET is slow — use keyset pagination instead ──────
-- ❌ Slow (must scan and skip 10,000 rows):
SELECT * FROM orders ORDER BY order_id LIMIT 10 OFFSET 10000;

-- ✅ Fast (uses index to start at the right place):
SELECT * FROM orders
WHERE  order_id > 10000    -- last ID from previous page
ORDER  BY order_id
LIMIT  10;
```

---

## 11. UPDATE

```sql
-- ─── Basic UPDATE ─────────────────────────────────────────
-- ⚠️ ALWAYS include WHERE — otherwise every row is updated!

UPDATE products
SET    price = 39.99
WHERE  product_id = 1;

-- ─── Update multiple columns ──────────────────────────────
UPDATE customers
SET
    phone      = '555-9999',
    city       = 'Boston',
    updated_at = NOW()
WHERE customer_id = 42;

-- ─── Update with arithmetic ───────────────────────────────
UPDATE products SET price = price * 1.10  WHERE category_id = 5;   -- 10% increase
UPDATE products SET stock = stock - 5     WHERE product_id  = 101;  -- decrement stock
UPDATE accounts SET balance = balance + 100 WHERE account_id = 7;   -- add funds

-- ─── UPDATE with subquery ────────────────────────────────
-- Recalculate order totals from line items:
UPDATE orders o
SET    total_amount = (
    SELECT COALESCE(SUM(quantity * unit_price * (1 - discount/100)), 0)
    FROM   order_items oi
    WHERE  oi.order_id = o.order_id
);

-- ─── UPDATE with FROM (PostgreSQL) ───────────────────────
-- Join to another table when updating:
UPDATE products p
SET    category_id = c.category_id
FROM   categories c
WHERE  c.name       = 'Electronics'
  AND  p.name LIKE '%Phone%';

-- Update based on join with a subquery:
UPDATE customers c
SET    tier = sub.customer_tier
FROM (
    SELECT
        customer_id,
        CASE
            WHEN SUM(total_amount) > 5000 THEN 'platinum'
            WHEN SUM(total_amount) > 1000 THEN 'gold'
            WHEN SUM(total_amount) > 200  THEN 'silver'
            ELSE 'bronze'
        END AS customer_tier
    FROM orders
    WHERE status = 'delivered'
    GROUP BY customer_id
) sub
WHERE c.customer_id = sub.customer_id;

-- ─── UPDATE with CASE ────────────────────────────────────
UPDATE products
SET    price =
    CASE
        WHEN price < 10    THEN price * 1.30   -- 30% increase for budget items
        WHEN price < 100   THEN price * 1.15   -- 15% for mid-range
        WHEN price < 1000  THEN price * 1.10   -- 10% for premium
        ELSE                    price * 1.05   -- 5% for luxury
    END,
    updated_at = NOW()
WHERE is_active = TRUE;

-- ─── RETURNING — see what was updated ─────────────────────
UPDATE products
SET    stock = stock - 1, updated_at = NOW()
WHERE  product_id = 5
RETURNING product_id, name, stock;

-- ─── Safe UPDATE workflow ────────────────────────────────
-- Step 1: Preview what will change:
SELECT product_id, name, price
FROM   products
WHERE  price < 5 AND is_active = TRUE;

-- Step 2: Run the update:
UPDATE products SET price = 5.00, updated_at = NOW()
WHERE  price < 5 AND is_active = TRUE;

-- Step 3: Verify:
SELECT COUNT(*) FROM products WHERE price = 5.00;
```

---

## 12. DELETE & TRUNCATE

```sql
-- ─── DELETE specific rows ────────────────────────────────
-- ⚠️ ALWAYS use WHERE — otherwise all rows are deleted!

DELETE FROM sessions    WHERE expires_at < NOW();
DELETE FROM order_items WHERE order_id = 999;
DELETE FROM customers   WHERE customer_id = 42 AND is_active = FALSE;

-- ─── DELETE with subquery ────────────────────────────────
-- Delete orders older than 5 years that are cancelled:
DELETE FROM orders
WHERE status = 'cancelled'
  AND ordered_at < NOW() - INTERVAL '5 years';

-- Delete customers who never ordered:
DELETE FROM customers
WHERE customer_id NOT IN (
    SELECT DISTINCT customer_id FROM orders
);
-- ⚠️ NOT IN with NULLs is risky — use NOT EXISTS instead:
DELETE FROM customers c
WHERE NOT EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);

-- ─── DELETE with USING (PostgreSQL) ──────────────────────
-- Join to another table when deleting:
DELETE FROM order_items oi
USING  products p
WHERE  oi.product_id = p.product_id
  AND  p.is_active   = FALSE;

-- ─── DELETE JOIN (MySQL) ──────────────────────────────────
DELETE oi
FROM   order_items oi
JOIN   products    p ON oi.product_id = p.product_id
WHERE  p.is_active = FALSE;

-- ─── RETURNING — get deleted rows ────────────────────────
DELETE FROM customers
WHERE  is_active = FALSE AND created_at < NOW() - INTERVAL '3 years'
RETURNING customer_id, email, created_at;

-- Archive before deleting (using CTE):
WITH deleted AS (
    DELETE FROM orders
    WHERE  status = 'cancelled' AND ordered_at < NOW() - INTERVAL '1 year'
    RETURNING *
)
INSERT INTO orders_archive SELECT * FROM deleted;

-- ─── DELETE vs TRUNCATE ───────────────────────────────────

-- DELETE — row by row, slower, can filter, fires triggers, can rollback:
DELETE FROM logs;            -- delete all rows (slow for large tables)

-- TRUNCATE — instant, no row-by-row, cannot filter, minimal logging:
TRUNCATE TABLE logs;                       -- delete all rows instantly
TRUNCATE TABLE logs RESTART IDENTITY;      -- also reset SERIAL counter to 1
TRUNCATE TABLE logs CASCADE;               -- also truncate child tables
TRUNCATE TABLE logs, audit_log;            -- truncate multiple tables at once

-- ─── Safe DELETE workflow ────────────────────────────────
BEGIN;

-- Preview first:
SELECT COUNT(*) FROM orders
WHERE  status = 'cancelled' AND ordered_at < '2022-01-01';

-- Delete:
DELETE FROM orders
WHERE  status = 'cancelled' AND ordered_at < '2022-01-01';

-- Check the count returned, then decide:
COMMIT;     -- save the change
-- or:
ROLLBACK;   -- undo the delete
```

---

## 13. ALTER TABLE

```sql
-- ─── Add columns ─────────────────────────────────────────
ALTER TABLE customers ADD COLUMN birth_date    DATE;
ALTER TABLE products  ADD COLUMN weight_kg     DECIMAL(8,3)   DEFAULT 0.0 NOT NULL;
ALTER TABLE orders    ADD COLUMN tracking_code VARCHAR(100);
ALTER TABLE products  ADD COLUMN tags          TEXT[]         DEFAULT '{}';

-- ─── Drop columns ────────────────────────────────────────
ALTER TABLE customers DROP COLUMN birth_date;
ALTER TABLE customers DROP COLUMN IF EXISTS old_unused_field;
ALTER TABLE customers DROP COLUMN phone CASCADE;  -- CASCADE removes dependent objects

-- ─── Rename column ────────────────────────────────────────
ALTER TABLE customers RENAME COLUMN phone TO phone_number;
ALTER TABLE orders    RENAME COLUMN notes TO internal_notes;

-- ─── Change data type ─────────────────────────────────────
ALTER TABLE products ALTER COLUMN price TYPE NUMERIC(12, 4);

-- With USING clause to convert existing data:
ALTER TABLE users ALTER COLUMN age TYPE BIGINT USING age::BIGINT;
ALTER TABLE logs  ALTER COLUMN created_at TYPE TIMESTAMPTZ USING created_at AT TIME ZONE 'UTC';

-- ─── Set / Drop DEFAULT ───────────────────────────────────
ALTER TABLE products ALTER COLUMN stock      SET DEFAULT 0;
ALTER TABLE products ALTER COLUMN stock      DROP DEFAULT;
ALTER TABLE orders   ALTER COLUMN created_at SET DEFAULT NOW();

-- ─── Set / Drop NOT NULL ──────────────────────────────────
ALTER TABLE products  ALTER COLUMN name  SET NOT NULL;
ALTER TABLE customers ALTER COLUMN phone DROP NOT NULL;

-- ─── Rename table ─────────────────────────────────────────
ALTER TABLE customers RENAME TO clients;
ALTER TABLE products  RENAME TO catalog_items;

-- ─── Add / Drop constraints ───────────────────────────────
ALTER TABLE products
    ADD CONSTRAINT chk_price_positive CHECK (price >= 0);

ALTER TABLE customers
    ADD CONSTRAINT uq_customers_email UNIQUE (email);

ALTER TABLE order_items
    ADD CONSTRAINT fk_items_order
        FOREIGN KEY (order_id)
        REFERENCES orders(order_id)
        ON DELETE CASCADE;

ALTER TABLE products DROP CONSTRAINT chk_price_positive;
ALTER TABLE customers DROP CONSTRAINT uq_customers_email;

-- ─── Safe migration pattern (adding NOT NULL to large table) ──
-- Step 1: add as nullable (fast, no rewrite):
ALTER TABLE orders ADD COLUMN payment_status VARCHAR(20);

-- Step 2: backfill existing rows:
UPDATE orders SET payment_status = 'paid'    WHERE status = 'delivered';
UPDATE orders SET payment_status = 'pending' WHERE payment_status IS NULL;

-- Step 3: add the NOT NULL constraint:
ALTER TABLE orders ALTER COLUMN payment_status SET NOT NULL;

-- Step 4: add CHECK constraint:
ALTER TABLE orders
    ADD CONSTRAINT chk_payment_status
        CHECK (payment_status IN ('pending','paid','failed','refunded'));
```

---

## 14. JOINS

### JOIN Types Explained

```
INNER JOIN  → only rows matching in BOTH tables
LEFT JOIN   → all LEFT table rows + matches from RIGHT (NULL if no match)
RIGHT JOIN  → all RIGHT table rows + matches from LEFT (NULL if no match)
FULL JOIN   → all rows from BOTH tables (NULL where no match on either side)
CROSS JOIN  → every combination: A rows × B rows (Cartesian product)
SELF JOIN   → a table joined to itself

Visual (Venn diagrams):

  INNER JOIN     LEFT JOIN      RIGHT JOIN     FULL JOIN
  ┌──┬──┐        ┌──┬──┐        ┌──┬──┐        ┌──┬──┐
  │  │██│        │██│██│        │  │██│        │██│██│
  │  │██│        │██│██│        │  │██│        │██│██│
  └──┴──┘        └──┴──┘        └──┴──┘        └──┴──┘
  only            left           right          everything
  overlap         + overlap      + overlap
```

### INNER JOIN

```sql
-- ─── Basic INNER JOIN ─────────────────────────────────────
-- Returns only rows where a match exists in BOTH tables
SELECT
    o.order_id,
    o.ordered_at,
    o.status,
    o.total_amount,
    c.first_name || ' ' || c.last_name  AS customer_name,
    c.email
FROM   orders    o
JOIN   customers c ON o.customer_id = c.customer_id;
-- "JOIN" = "INNER JOIN" (INNER is optional)

-- ─── Three-table join ─────────────────────────────────────
SELECT
    o.order_id,
    c.email                             AS customer_email,
    p.name                              AS product_name,
    oi.quantity,
    oi.unit_price,
    oi.quantity * oi.unit_price         AS line_total
FROM   orders      o
JOIN   customers   c  ON o.customer_id  = c.customer_id
JOIN   order_items oi ON o.order_id     = oi.order_id
JOIN   products    p  ON oi.product_id  = p.product_id
WHERE  o.status = 'delivered'
ORDER BY o.order_id, p.name;

-- ─── Four-table join with aggregation ────────────────────
SELECT
    cat.name                                     AS category,
    COUNT(DISTINCT o.order_id)                   AS total_orders,
    COUNT(DISTINCT o.customer_id)                AS unique_buyers,
    SUM(oi.quantity)                             AS units_sold,
    SUM(oi.quantity * oi.unit_price)             AS gross_revenue,
    ROUND(AVG(oi.unit_price), 2)                 AS avg_selling_price
FROM   order_items  oi
JOIN   orders       o   ON oi.order_id    = o.order_id
JOIN   products     p   ON oi.product_id  = p.product_id
JOIN   categories   cat ON p.category_id  = cat.category_id
WHERE  o.status IN ('processing', 'shipped', 'delivered')
GROUP  BY cat.name
ORDER  BY gross_revenue DESC;

-- ─── USING clause — shorthand when column names are identical ──
SELECT o.order_id, c.email
FROM   orders o
JOIN   customers c USING (customer_id);   -- both have customer_id column
-- Equivalent to: ON o.customer_id = c.customer_id

-- ─── NATURAL JOIN — auto-matches on same column names ─────
-- ⚠️ Fragile — avoid in production (breaks if columns are added)
SELECT * FROM orders NATURAL JOIN customers;
```

### LEFT JOIN

```sql
-- ─── LEFT JOIN — all left rows + matching right rows ──────
-- Customers with their order count (including those who never ordered):
SELECT
    c.customer_id,
    c.first_name || ' ' || c.last_name  AS customer_name,
    c.email,
    COUNT(o.order_id)                    AS total_orders,
    COALESCE(SUM(o.total_amount), 0)     AS lifetime_spent
FROM      customers c
LEFT JOIN orders    o ON c.customer_id = o.customer_id
                     AND o.status != 'cancelled'   -- filter on right table here, not WHERE!
GROUP BY  c.customer_id, c.first_name, c.last_name, c.email
ORDER BY  lifetime_spent DESC;

-- ─── Find rows with NO match (anti-join) ──────────────────
-- Customers who have NEVER placed an order:
SELECT c.customer_id, c.email, c.created_at
FROM      customers c
LEFT JOIN orders    o ON c.customer_id = o.customer_id
WHERE     o.order_id IS NULL;   -- no match in orders table
-- NULL in the right table = no matching row

-- Products that have never been ordered:
SELECT p.product_id, p.name, p.price
FROM      products    p
LEFT JOIN order_items oi ON p.product_id = oi.product_id
WHERE     oi.item_id IS NULL;

-- ─── LEFT JOIN with aggregation ───────────────────────────
-- All products with review stats (including products with no reviews):
SELECT
    p.product_id,
    p.name,
    p.price,
    COUNT(r.review_id)               AS review_count,
    ROUND(AVG(r.rating), 2)          AS avg_rating,
    MIN(r.rating)                    AS min_rating,
    MAX(r.rating)                    AS max_rating
FROM      products p
LEFT JOIN reviews  r ON p.product_id = r.product_id
GROUP BY  p.product_id, p.name, p.price
ORDER BY  avg_rating DESC NULLS LAST, p.name;
```

### RIGHT JOIN, FULL JOIN, CROSS JOIN, SELF JOIN

```sql
-- ─── RIGHT JOIN ───────────────────────────────────────────
-- All products, whether ordered or not (uncommon — just flip to LEFT JOIN)
SELECT
    p.product_id,
    p.name,
    COUNT(oi.item_id)  AS times_ordered
FROM      order_items oi
RIGHT JOIN products   p ON oi.product_id = p.product_id
GROUP BY  p.product_id, p.name
ORDER BY  times_ordered ASC;   -- products ordered least first

-- ─── FULL OUTER JOIN ──────────────────────────────────────
-- All customers + all orders, matched where possible:
SELECT
    c.customer_id,
    c.email          AS customer_email,
    o.order_id,
    o.total_amount
FROM      customers c
FULL JOIN orders    o ON c.customer_id = o.customer_id;
-- Rows with NULL c.customer_id  = orphaned orders
-- Rows with NULL o.order_id     = customers with no orders

-- Find unmatched rows from EITHER side:
SELECT c.email, o.order_id
FROM      customers c
FULL JOIN orders    o ON c.customer_id = o.customer_id
WHERE c.customer_id IS NULL OR o.order_id IS NULL;

-- ─── CROSS JOIN — Cartesian product ──────────────────────
-- Generate all size × color combinations:
SELECT
    s.size   AS size,
    c.color  AS color,
    s.size || '-' || c.color AS variant
FROM (VALUES ('XS'),('S'),('M'),('L'),('XL'))           AS s(size)
CROSS JOIN
     (VALUES ('Red'),('Blue'),('Green'),('Black'))       AS c(color);
-- 5 sizes × 4 colors = 20 rows

-- Generate a date range:
SELECT
    gs::DATE AS calendar_date
FROM generate_series(
    '2024-01-01'::DATE,
    '2024-12-31'::DATE,
    INTERVAL '1 day'
) AS gs;

-- ─── SELF JOIN — join a table to itself ───────────────────
-- Employee + their manager (both in same table):
SELECT
    e.employee_id,
    e.first_name || ' ' || e.last_name   AS employee,
    e.job_title,
    e.salary,
    m.first_name || ' ' || m.last_name   AS manager_name,
    m.job_title                           AS manager_title
FROM      employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;
-- LEFT JOIN: CEO has no manager (manager_id IS NULL) — still appears

-- Category hierarchy using self-join:
SELECT
    child.name    AS subcategory,
    parent.name   AS parent_category
FROM      categories child
LEFT JOIN categories parent ON child.parent_id = parent.category_id;

-- Find pairs of customers in the same city:
SELECT
    a.customer_id   AS cust_a,
    b.customer_id   AS cust_b,
    a.city
FROM   customers a
JOIN   customers b ON a.city = b.city
                  AND a.customer_id < b.customer_id  -- avoid (1,2) and (2,1)
ORDER  BY a.city, a.customer_id;
```

---

## 15. Aggregate Functions

```sql
-- ─── COUNT ───────────────────────────────────────────────
SELECT COUNT(*)             FROM orders;         -- total rows (incl. NULLs)
SELECT COUNT(phone)         FROM customers;      -- rows where phone is NOT NULL
SELECT COUNT(DISTINCT city) FROM customers;      -- distinct non-null cities

-- ─── SUM ─────────────────────────────────────────────────
SELECT SUM(total_amount)                          FROM orders;
SELECT SUM(total_amount)                          FROM orders WHERE status = 'delivered';
SELECT SUM(quantity * unit_price)                 FROM order_items;
SELECT SUM(quantity * unit_price * (1-discount))  FROM order_items;  -- with discount

-- ─── AVG ─────────────────────────────────────────────────
SELECT AVG(price)                                 FROM products;
SELECT ROUND(AVG(rating), 2)                      FROM reviews;
SELECT AVG(total_amount)                          FROM orders WHERE status = 'delivered';

-- ─── MIN & MAX ────────────────────────────────────────────
SELECT MIN(price), MAX(price)                     FROM products;
SELECT MIN(ordered_at), MAX(ordered_at)           FROM orders;
SELECT MIN(total_amount), MAX(total_amount)       FROM orders WHERE status != 'cancelled';

-- ─── Combined aggregate query ─────────────────────────────
SELECT
    COUNT(*)                                   AS total_products,
    COUNT(description)                         AS with_description,
    COUNT(DISTINCT category_id)                AS categories_used,
    MIN(price)                                 AS cheapest,
    MAX(price)                                 AS most_expensive,
    ROUND(AVG(price), 2)                       AS avg_price,
    SUM(price * stock)                         AS total_inventory_value,
    ROUND(STDDEV(price), 2)                    AS price_std_dev,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY price) AS median_price
FROM products
WHERE is_active = TRUE;

-- ─── FILTER clause (PostgreSQL) — conditional aggregation ──
SELECT
    COUNT(*)                                   AS total_orders,
    COUNT(*) FILTER (WHERE status = 'pending')   AS pending,
    COUNT(*) FILTER (WHERE status = 'delivered') AS delivered,
    COUNT(*) FILTER (WHERE status = 'cancelled') AS cancelled,
    SUM(total_amount) FILTER (WHERE status != 'cancelled') AS net_revenue,
    ROUND(
        COUNT(*) FILTER (WHERE status = 'delivered') * 100.0 / COUNT(*),
        2
    ) AS delivery_rate_pct
FROM orders;

-- ─── STRING_AGG — concatenate strings in a group ──────────
SELECT
    category_id,
    STRING_AGG(name, ', ' ORDER BY name)  AS product_names,
    COUNT(*)                               AS product_count
FROM   products
GROUP  BY category_id;

-- ─── ARRAY_AGG — collect into array (PostgreSQL) ──────────
SELECT
    category_id,
    ARRAY_AGG(product_id ORDER BY price DESC) AS product_ids_by_price,
    ARRAY_AGG(DISTINCT name)                   AS product_names
FROM   products
GROUP  BY category_id;

-- ─── JSON aggregation ────────────────────────────────────
SELECT
    o.order_id,
    JSON_AGG(
        JSON_BUILD_OBJECT(
            'product', p.name,
            'qty',     oi.quantity,
            'price',   oi.unit_price
        ) ORDER BY p.name
    ) AS items_json
FROM   orders      o
JOIN   order_items oi ON o.order_id    = oi.order_id
JOIN   products    p  ON oi.product_id = p.product_id
GROUP  BY o.order_id;
```

---

## 16. GROUP BY & HAVING

### GROUP BY

```sql
-- ─── Basic GROUP BY ───────────────────────────────────────
-- Count orders per status:
SELECT
    status,
    COUNT(*)            AS order_count,
    SUM(total_amount)   AS total_revenue
FROM   orders
GROUP  BY status
ORDER  BY order_count DESC;

-- Revenue by month:
SELECT
    DATE_TRUNC('month', ordered_at)  AS month,
    COUNT(*)                          AS orders,
    SUM(total_amount)                 AS revenue,
    ROUND(AVG(total_amount), 2)       AS avg_order
FROM   orders
WHERE  status != 'cancelled'
GROUP  BY DATE_TRUNC('month', ordered_at)
ORDER  BY month;

-- Products per category with stats:
SELECT
    cat.name                                      AS category,
    COUNT(p.product_id)                           AS product_count,
    ROUND(MIN(p.price),2)                         AS min_price,
    ROUND(MAX(p.price),2)                         AS max_price,
    ROUND(AVG(p.price),2)                         AS avg_price,
    SUM(p.stock)                                  AS total_stock,
    SUM(p.price * p.stock)                        AS inventory_value
FROM      products   p
LEFT JOIN categories cat ON p.category_id = cat.category_id
WHERE p.is_active = TRUE
GROUP BY cat.name
ORDER BY inventory_value DESC;

-- ─── GROUP BY multiple columns ────────────────────────────
SELECT
    EXTRACT(YEAR FROM ordered_at)   AS year,
    EXTRACT(MONTH FROM ordered_at)  AS month,
    status,
    COUNT(*)                        AS count,
    SUM(total_amount)               AS revenue
FROM orders
GROUP BY
    EXTRACT(YEAR FROM ordered_at),
    EXTRACT(MONTH FROM ordered_at),
    status
ORDER BY year, month, status;

-- ─── GROUPING SETS — multiple groupings in one query ──────
SELECT
    category_id,
    country,
    SUM(total_amount) AS revenue
FROM orders o
JOIN customers  c  ON o.customer_id  = c.customer_id
JOIN order_items oi ON o.order_id    = oi.order_id
JOIN products   p  ON oi.product_id  = p.product_id
GROUP BY GROUPING SETS (
    (category_id, country),   -- by category AND country
    (category_id),            -- subtotal by category
    (country),                -- subtotal by country
    ()                        -- grand total
);

-- ─── ROLLUP — hierarchical subtotals ──────────────────────
SELECT
    COALESCE(country, 'ALL')  AS country,
    COALESCE(city,    'ALL')  AS city,
    COUNT(*)                   AS customers
FROM customers
GROUP BY ROLLUP (country, city)
ORDER BY country NULLS LAST, city NULLS LAST;

-- ─── CUBE — all possible grouping combinations ────────────
SELECT country, city, status, COUNT(*) AS order_count
FROM orders o JOIN customers c USING (customer_id)
GROUP BY CUBE (country, city, status);
```

### HAVING

```sql
-- ─── HAVING — filter AFTER grouping ──────────────────────
-- WHERE filters before grouping; HAVING filters after

-- Categories with more than 5 products:
SELECT
    category_id,
    COUNT(*)     AS product_count,
    AVG(price)   AS avg_price
FROM products
WHERE is_active = TRUE                    -- filter rows before grouping
GROUP BY category_id
HAVING COUNT(*) > 5                       -- filter groups after aggregating
ORDER BY product_count DESC;

-- Customers who spent more than $500 total:
SELECT
    c.customer_id,
    c.first_name || ' ' || c.last_name  AS customer_name,
    COUNT(o.order_id)                    AS total_orders,
    SUM(o.total_amount)                  AS total_spent
FROM      customers c
JOIN      orders    o ON c.customer_id = o.customer_id
WHERE     o.status = 'delivered'
GROUP BY  c.customer_id, c.first_name, c.last_name
HAVING    SUM(o.total_amount) > 500
ORDER BY  total_spent DESC;

-- Products with 10+ reviews AND average rating below 3 (trouble products):
SELECT
    p.product_id,
    p.name,
    COUNT(r.review_id)  AS review_count,
    ROUND(AVG(r.rating), 2) AS avg_rating
FROM      products p
JOIN      reviews  r ON p.product_id = r.product_id
GROUP BY  p.product_id, p.name
HAVING    COUNT(r.review_id) >= 10 AND AVG(r.rating) < 3
ORDER BY  avg_rating ASC;

-- Categories whose average product price is above overall average:
SELECT
    category_id,
    ROUND(AVG(price), 2) AS avg_price
FROM products
GROUP BY category_id
HAVING AVG(price) > (SELECT AVG(price) FROM products);
```

---

## 17. Subqueries

### Scalar Subqueries

```sql
-- ─── Subquery in SELECT ───────────────────────────────────
-- A subquery that returns a single value:
SELECT
    product_id,
    name,
    price,
    (SELECT AVG(price) FROM products WHERE is_active = TRUE)  AS overall_avg,
    price - (SELECT AVG(price) FROM products WHERE is_active = TRUE) AS diff_from_avg
FROM products
WHERE is_active = TRUE;

-- ─── Subquery in WHERE ────────────────────────────────────
-- Products priced above the overall average:
SELECT name, price
FROM   products
WHERE  price > (SELECT AVG(price) FROM products);

-- Most expensive product:
SELECT name, price
FROM   products
WHERE  price = (SELECT MAX(price) FROM products);

-- ─── Correlated subquery ──────────────────────────────────
-- References the outer query — runs once per outer row
-- Most expensive product in EACH category:
SELECT p1.name, p1.price, p1.category_id
FROM   products p1
WHERE  p1.price = (
    SELECT MAX(p2.price)
    FROM   products p2
    WHERE  p2.category_id = p1.category_id  -- correlated!
);

-- Customers whose total > their country's average:
SELECT c.customer_id, c.email, c.country
FROM   customers c
WHERE  (
    SELECT COALESCE(SUM(total_amount), 0)
    FROM   orders o
    WHERE  o.customer_id = c.customer_id
      AND  o.status = 'delivered'
) > (
    SELECT AVG(customer_total)
    FROM (
        SELECT SUM(total_amount) AS customer_total
        FROM   orders o2
        JOIN   customers c2 ON o2.customer_id = c2.customer_id
        WHERE  c2.country = c.country
          AND  o2.status = 'delivered'
        GROUP  BY c2.customer_id
    ) country_stats
);
```

### Table Subqueries

```sql
-- ─── Subquery in FROM (derived table / inline view) ───────
SELECT category_name, avg_price, product_count
FROM (
    SELECT
        c.name          AS category_name,
        AVG(p.price)    AS avg_price,
        COUNT(p.product_id) AS product_count
    FROM      products   p
    JOIN      categories c ON p.category_id = c.category_id
    WHERE     p.is_active = TRUE
    GROUP BY  c.name
) AS cat_stats
WHERE avg_price > 50
ORDER BY avg_price DESC;

-- ─── IN with subquery ─────────────────────────────────────
-- Customers who ordered in the past 30 days:
SELECT customer_id, first_name, email
FROM   customers
WHERE  customer_id IN (
    SELECT DISTINCT customer_id
    FROM   orders
    WHERE  ordered_at >= NOW() - INTERVAL '30 days'
);

-- Products ordered by ALL VIP customers:
SELECT product_id, name
FROM   products
WHERE  product_id IN (
    SELECT oi.product_id
    FROM   order_items oi
    JOIN   orders o ON oi.order_id = o.order_id
    WHERE  o.customer_id IN (SELECT customer_id FROM vip_customers)
    GROUP  BY oi.product_id
    HAVING COUNT(DISTINCT o.customer_id) = (SELECT COUNT(*) FROM vip_customers)
);

-- ─── NOT IN — rows with no match ─────────────────────────
-- ⚠️ NOT IN is dangerous with NULLs — use NOT EXISTS instead!
-- If the subquery returns ANY NULL, NOT IN returns FALSE for all rows

-- ❌ Risky (if product_id can be NULL in order_items):
SELECT name FROM products
WHERE product_id NOT IN (SELECT product_id FROM order_items);

-- ✅ Safe with NOT EXISTS:
SELECT name FROM products p
WHERE NOT EXISTS (
    SELECT 1 FROM order_items oi WHERE oi.product_id = p.product_id
);

-- ─── EXISTS / NOT EXISTS ─────────────────────────────────
-- EXISTS stops at the first match — efficient for large tables

-- Customers with at least one delivered order:
SELECT c.customer_id, c.email
FROM   customers c
WHERE  EXISTS (
    SELECT 1 FROM orders o
    WHERE  o.customer_id = c.customer_id
      AND  o.status = 'delivered'
);

-- Products with no reviews yet:
SELECT p.product_id, p.name, p.price
FROM   products p
WHERE  NOT EXISTS (
    SELECT 1 FROM reviews r WHERE r.product_id = p.product_id
);

-- ─── Nested subqueries ────────────────────────────────────
-- (Shown for illustration — CTEs are cleaner for complex cases)
SELECT *
FROM   customers
WHERE  customer_id IN (
    SELECT DISTINCT customer_id
    FROM   orders
    WHERE  order_id IN (
        SELECT order_id
        FROM   order_items
        WHERE  product_id IN (
            SELECT product_id
            FROM   products
            WHERE  category_id = 3   -- Electronics category
        )
    )
);
```

---

## 18. Common Table Expressions (CTEs)

### Basic CTEs

```sql
-- ─── CTE syntax: WITH name AS (query) SELECT ... ─────────
-- Think of it as a named temporary view for the duration of one query

-- Simple CTE:
WITH active_products AS (
    SELECT product_id, name, price, category_id
    FROM   products
    WHERE  is_active = TRUE AND stock > 0
)
SELECT *
FROM   active_products
WHERE  price < 50
ORDER  BY name;

-- ─── Multiple CTEs ────────────────────────────────────────
WITH
    -- Monthly revenue calculation:
    monthly_revenue AS (
        SELECT
            DATE_TRUNC('month', ordered_at) AS month,
            SUM(total_amount)               AS revenue,
            COUNT(*)                        AS order_count
        FROM orders
        WHERE status != 'cancelled'
        GROUP BY DATE_TRUNC('month', ordered_at)
    ),
    -- Add month-over-month growth:
    revenue_with_growth AS (
        SELECT
            month,
            revenue,
            order_count,
            LAG(revenue) OVER (ORDER BY month)  AS prev_revenue
        FROM monthly_revenue
    )
-- Final result:
SELECT
    TO_CHAR(month, 'YYYY-MM') AS month,
    revenue,
    order_count,
    prev_revenue,
    ROUND(
        (revenue - prev_revenue) / NULLIF(prev_revenue, 0) * 100,
        2
    ) AS growth_pct
FROM revenue_with_growth
ORDER BY month;

-- ─── CTE for readable complex queries ────────────────────
WITH
    order_totals AS (
        SELECT
            order_id,
            SUM(quantity * unit_price * (1 - discount)) AS net_total,
            COUNT(*)                                     AS item_count
        FROM order_items
        GROUP BY order_id
    ),
    customer_segments AS (
        SELECT
            customer_id,
            SUM(total_amount) AS lifetime_value,
            CASE
                WHEN SUM(total_amount) > 5000 THEN 'Platinum'
                WHEN SUM(total_amount) > 1000 THEN 'Gold'
                WHEN SUM(total_amount) > 200  THEN 'Silver'
                ELSE 'Bronze'
            END AS segment
        FROM orders
        WHERE status = 'delivered'
        GROUP BY customer_id
    )
SELECT
    c.email,
    cs.segment,
    cs.lifetime_value,
    COUNT(o.order_id)           AS total_orders,
    AVG(ot.item_count)          AS avg_items_per_order
FROM customers        c
JOIN customer_segments cs ON c.customer_id = cs.customer_id
JOIN orders            o  ON c.customer_id = o.customer_id
JOIN order_totals      ot ON o.order_id    = ot.order_id
GROUP BY c.email, cs.segment, cs.lifetime_value
ORDER BY cs.lifetime_value DESC;

-- ─── Writable CTEs (INSERT/UPDATE/DELETE) ─────────────────
-- Archive then delete in one statement:
WITH archived AS (
    DELETE FROM orders
    WHERE  status = 'cancelled'
      AND  ordered_at < NOW() - INTERVAL '1 year'
    RETURNING *
)
INSERT INTO orders_archive
SELECT * FROM archived;

-- Update and return results:
WITH updated AS (
    UPDATE products
    SET    price = price * 1.10, updated_at = NOW()
    WHERE  category_id = 5
    RETURNING product_id, name, price
)
SELECT * FROM updated;
```

### Recursive CTEs

```sql
-- ─── Recursive CTE syntax ─────────────────────────────────
WITH RECURSIVE cte_name AS (
    -- Anchor/Base case (non-recursive query):
    SELECT ...

    UNION ALL

    -- Recursive case (references cte_name):
    SELECT ... FROM cte_name WHERE ...   -- must have a termination condition!
)
SELECT * FROM cte_name;

-- ─── Category tree (unlimited depth) ────────────────────
WITH RECURSIVE category_tree AS (
    -- Anchor: root categories (no parent):
    SELECT
        category_id,
        name,
        parent_id,
        0          AS depth,
        name       AS path,
        ARRAY[category_id] AS id_path   -- track IDs to detect cycles
    FROM categories
    WHERE parent_id IS NULL

    UNION ALL

    -- Recursive: children of previous level:
    SELECT
        c.category_id,
        c.name,
        c.parent_id,
        ct.depth + 1,
        ct.path || ' > ' || c.name,
        ct.id_path || c.category_id
    FROM   categories   c
    JOIN   category_tree ct ON c.parent_id = ct.category_id
    WHERE  NOT (c.category_id = ANY(ct.id_path))  -- prevent infinite loops
)
SELECT
    REPEAT('  ', depth) || name  AS indented_name,
    depth,
    path
FROM   category_tree
ORDER  BY path;

-- ─── Employee org chart ───────────────────────────────────
WITH RECURSIVE org_chart AS (
    -- CEO (no manager):
    SELECT
        employee_id,
        first_name || ' ' || last_name  AS full_name,
        job_title,
        manager_id,
        0                               AS level,
        first_name || ' ' || last_name  AS chain
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    SELECT
        e.employee_id,
        e.first_name || ' ' || e.last_name,
        e.job_title,
        e.manager_id,
        oc.level + 1,
        oc.chain || ' → ' || e.first_name || ' ' || e.last_name
    FROM   employees  e
    JOIN   org_chart  oc ON e.manager_id = oc.employee_id
)
SELECT
    REPEAT('  ', level) || full_name  AS org_structure,
    job_title,
    level
FROM   org_chart
ORDER  BY chain;

-- ─── Generate date series ─────────────────────────────────
WITH RECURSIVE date_series AS (
    SELECT CURRENT_DATE - INTERVAL '29 days' AS dt
    UNION ALL
    SELECT dt + INTERVAL '1 day'
    FROM   date_series
    WHERE  dt < CURRENT_DATE
)
-- Join with orders to fill in zero-revenue days:
SELECT
    ds.dt           AS date,
    COALESCE(daily.order_count, 0) AS orders,
    COALESCE(daily.revenue,     0) AS revenue
FROM date_series ds
LEFT JOIN (
    SELECT
        ordered_at::DATE        AS day,
        COUNT(*)                AS order_count,
        SUM(total_amount)       AS revenue
    FROM orders
    WHERE status != 'cancelled'
    GROUP BY ordered_at::DATE
) daily ON ds.dt = daily.day
ORDER BY ds.dt;

-- PostgreSQL built-in series (no recursion needed):
SELECT generate_series(1, 10) AS n;
SELECT generate_series('2024-01-01'::DATE, '2024-12-31'::DATE, '1 month') AS month;
```

---

## 19. Window Functions

### What Are Window Functions?

```sql
-- Window functions compute over a "window" of rows related to the current row
-- Key difference from GROUP BY: rows are NOT collapsed
--
-- Syntax:
--   function() OVER (
--       [PARTITION BY col]  -- like GROUP BY — defines the window
--       [ORDER BY col]      -- sort within window
--       [ROWS/RANGE BETWEEN start AND end]  -- window frame
--   )

-- Contrast: GROUP BY vs Window Function
-- GROUP BY (collapses rows):
SELECT department_id, AVG(salary) FROM employees GROUP BY department_id;
-- → one row per department

-- Window function (keeps all rows):
SELECT
    employee_id,
    first_name,
    salary,
    department_id,
    AVG(salary) OVER (PARTITION BY department_id) AS dept_avg
FROM employees;
-- → every employee row, PLUS the department average on each row!
```

### Ranking Functions

```sql
-- ─── ROW_NUMBER — unique number, no ties ─────────────────
SELECT
    product_id,
    name,
    price,
    ROW_NUMBER() OVER (ORDER BY price DESC)                      AS global_rank,
    ROW_NUMBER() OVER (PARTITION BY category_id ORDER BY price DESC) AS rank_in_cat
FROM products;

-- ─── RANK — ties share rank, then skips ──────────────────
-- e.g.: 1st, 2nd, 2nd, 4th (3rd is skipped)
SELECT
    name,
    price,
    RANK() OVER (ORDER BY price DESC) AS price_rank
FROM products;

-- ─── DENSE_RANK — ties share rank, no skip ───────────────
-- e.g.: 1st, 2nd, 2nd, 3rd (no skip)
SELECT
    name,
    price,
    DENSE_RANK() OVER (ORDER BY price DESC) AS dense_rank
FROM products;

-- ─── NTILE(n) — divide into n buckets ────────────────────
SELECT
    name,
    price,
    NTILE(4)  OVER (ORDER BY price) AS quartile,   -- 1=Q1, 2=Q2, 3=Q3, 4=Q4
    NTILE(10) OVER (ORDER BY price) AS decile,     -- 1-10
    NTILE(100) OVER (ORDER BY price) AS percentile -- 1-100
FROM products;

-- ─── Compare all ranking functions side-by-side ──────────
SELECT
    name,
    price,
    ROW_NUMBER()  OVER (ORDER BY price) AS row_num,    -- 1,2,3,4,5 (always unique)
    RANK()        OVER (ORDER BY price) AS rank,        -- 1,2,2,4,5 (gap after tie)
    DENSE_RANK()  OVER (ORDER BY price) AS dense,       -- 1,2,2,3,4 (no gap)
    NTILE(3)      OVER (ORDER BY price) AS ntile        -- 1,1,2,2,3 (buckets)
FROM products ORDER BY price;

-- ─── Top N per group — RANK() use case ───────────────────
SELECT *
FROM (
    SELECT
        p.product_id,
        p.name,
        p.category_id,
        p.price,
        RANK() OVER (PARTITION BY p.category_id ORDER BY p.price DESC) AS price_rank
    FROM products p
    WHERE p.is_active = TRUE
) ranked
WHERE price_rank <= 3;   -- top 3 most expensive per category
```

### Offset and Aggregate Window Functions

```sql
-- ─── LAG — value from a previous row ─────────────────────
SELECT
    TO_CHAR(ordered_at, 'YYYY-MM') AS month,
    SUM(total_amount)              AS revenue,
    LAG(SUM(total_amount)) OVER (ORDER BY DATE_TRUNC('month', ordered_at)) AS prev_revenue,
    SUM(total_amount) -
    LAG(SUM(total_amount)) OVER (ORDER BY DATE_TRUNC('month', ordered_at)) AS change
FROM orders
WHERE status != 'cancelled'
GROUP BY DATE_TRUNC('month', ordered_at), TO_CHAR(ordered_at, 'YYYY-MM')
ORDER BY DATE_TRUNC('month', ordered_at);

-- LAG with default (avoids NULL on first row):
SELECT
    ordered_at::DATE             AS order_date,
    customer_id,
    total_amount,
    LAG(total_amount, 1, 0)
        OVER (PARTITION BY customer_id ORDER BY ordered_at) AS prev_order_amount
FROM orders
ORDER BY customer_id, ordered_at;

-- ─── LEAD — value from a next row ────────────────────────
SELECT
    order_id,
    customer_id,
    ordered_at,
    LEAD(ordered_at) OVER (PARTITION BY customer_id ORDER BY ordered_at) AS next_order_date,
    LEAD(ordered_at) OVER (PARTITION BY customer_id ORDER BY ordered_at)
        - ordered_at AS days_to_next_order
FROM orders
ORDER BY customer_id, ordered_at;

-- ─── FIRST_VALUE / LAST_VALUE ────────────────────────────
SELECT
    employee_id,
    department_id,
    salary,
    FIRST_VALUE(salary) OVER (PARTITION BY department_id ORDER BY salary DESC)    AS highest_in_dept,
    LAST_VALUE(salary)  OVER (
        PARTITION BY department_id
        ORDER BY salary DESC
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS lowest_in_dept
FROM employees;

-- ─── Running total ────────────────────────────────────────
SELECT
    ordered_at::DATE   AS order_date,
    total_amount,
    SUM(total_amount) OVER (
        ORDER BY ordered_at
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total,
    COUNT(*) OVER (ORDER BY ordered_at) AS running_count
FROM orders
WHERE status != 'cancelled'
ORDER BY ordered_at;

-- ─── 7-day moving average ────────────────────────────────
SELECT
    order_date,
    daily_revenue,
    ROUND(
        AVG(daily_revenue) OVER (
            ORDER BY order_date
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ),
        2
    ) AS rolling_7day_avg,
    SUM(daily_revenue) OVER (
        ORDER BY order_date
        ROWS BETWEEN 29 PRECEDING AND CURRENT ROW
    ) AS rolling_30day_sum
FROM daily_revenue
ORDER BY order_date;

-- ─── Percent of total ─────────────────────────────────────
SELECT
    name,
    category_id,
    price,
    ROUND(price / SUM(price) OVER (PARTITION BY category_id) * 100, 2) AS pct_of_category,
    ROUND(price / SUM(price) OVER () * 100, 2)                         AS pct_of_total
FROM products
WHERE is_active = TRUE
ORDER BY category_id, price DESC;
```

---

## 20. String Functions

```sql
-- ─── Length and Case ──────────────────────────────────────
SELECT
    LENGTH('Hello World')         AS char_length,     -- 11
    UPPER('hello world')          AS uppercased,       -- 'HELLO WORLD'
    LOWER('HELLO WORLD')          AS lowercased,       -- 'hello world'
    INITCAP('hello world')        AS title_case,       -- 'Hello World'
    REVERSE('hello')              AS reversed;         -- 'olleh'

-- ─── Trimming Whitespace ──────────────────────────────────
SELECT
    TRIM('   hello   ')           AS trimmed,          -- 'hello'
    LTRIM('   hello   ')          AS left_trimmed,     -- 'hello   '
    RTRIM('   hello   ')          AS right_trimmed,    -- '   hello'
    TRIM('x' FROM 'xxxhelloxxx')  AS custom_trim,      -- 'hello'
    TRIM(LEADING  '0' FROM '0042') AS leading_zeros;   -- '42'

-- ─── Padding ──────────────────────────────────────────────
SELECT
    LPAD('42', 6, '0')            AS zero_padded,      -- '000042'
    RPAD('Hello', 10, '.')        AS right_padded,     -- 'Hello.....'
    LPAD(order_id::TEXT, 10, '0') AS formatted_id      -- '0000000001'
FROM orders LIMIT 5;

-- ─── Substrings ───────────────────────────────────────────
SELECT
    SUBSTRING('Hello World' FROM 1 FOR 5)  AS sub_start,    -- 'Hello'
    SUBSTRING('Hello World' FROM 7)        AS sub_from,     -- 'World'
    LEFT('Hello World', 5)                 AS left_chars,   -- 'Hello'
    RIGHT('Hello World', 5)                AS right_chars,  -- 'World'
    SUBSTR('Hello World', 7, 5)            AS substr;       -- 'World'

-- ─── Splitting and Parts ──────────────────────────────────
SELECT
    SPLIT_PART('2024-01-15', '-', 1)  AS year_part,    -- '2024'
    SPLIT_PART('2024-01-15', '-', 2)  AS month_part,   -- '01'
    SPLIT_PART('2024-01-15', '-', 3)  AS day_part,     -- '15'
    SPLIT_PART(email, '@', 1)          AS username,
    SPLIT_PART(email, '@', 2)          AS domain
FROM customers LIMIT 5;

-- ─── Search and Position ──────────────────────────────────
SELECT
    POSITION('World' IN 'Hello World')   AS position,     -- 7
    STRPOS('Hello World', 'World')       AS strpos;        -- 7

-- ─── Replace and Translate ────────────────────────────────
SELECT
    REPLACE('Hello World', 'World', 'SQL')        AS replaced,
    REPLACE(phone, '-', '')                        AS digits_only
FROM customers LIMIT 3;

SELECT
    TRANSLATE('Hello World', 'aeiou', '*****')    AS vowels_replaced;
-- 'H*ll* W*rld' — replaces each char from set 1 with corresponding char in set 2

-- ─── Concatenation ────────────────────────────────────────
SELECT
    'Hello' || ' ' || 'World'              AS pipe_concat,
    CONCAT('Hello', ' ', 'World')          AS concat_fn,
    CONCAT_WS(', ', first_name, last_name, city) AS csv_style
FROM customers LIMIT 3;

-- ─── Repeat ───────────────────────────────────────────────
SELECT REPEAT('ha', 3);    -- 'hahaha'
SELECT REPEAT('=', 50);    -- separator line

-- ─── Regex (PostgreSQL) ───────────────────────────────────
SELECT
    email ~ '@gmail\.com$'            AS is_gmail,      -- boolean match
    email ~* 'GMAIL'                  AS icase_match,   -- case-insensitive
    REGEXP_REPLACE(phone, '[^0-9]', '', 'g') AS digits  -- remove non-digits
FROM customers LIMIT 5;

-- ─── Practical examples ───────────────────────────────────
-- Extract username and domain from email:
SELECT
    email,
    SPLIT_PART(email, '@', 1)  AS username,
    SPLIT_PART(email, '@', 2)  AS domain
FROM customers;

-- Clean phone numbers (remove all non-digits):
UPDATE customers
SET phone = REGEXP_REPLACE(phone, '[^0-9+]', '', 'g')
WHERE phone IS NOT NULL;

-- Generate URL slug from title:
SELECT
    LOWER(
        REGEXP_REPLACE(
            REGEXP_REPLACE(title, '[^a-zA-Z0-9\s]', '', 'g'),
            '\s+', '-', 'g'
        )
    ) AS slug
FROM articles;

-- Format number as currency string:
SELECT TO_CHAR(price, 'FM$999,999,999.00') AS formatted_price
FROM products;
```

---

## 21. Numeric Functions

```sql
-- ─── Rounding ─────────────────────────────────────────────
SELECT
    ROUND(3.14159, 2)         AS round_2dp,         -- 3.14
    ROUND(3.14559, 2)         AS round_up,          -- 3.15
    ROUND(1234.5, -2)         AS round_hundreds,    -- 1200
    FLOOR(3.9)                AS floor_down,        -- 3   (toward -∞)
    CEIL(3.1)                 AS ceil_up,           -- 4   (toward +∞)
    TRUNC(3.999)              AS truncated,         -- 3   (toward 0)
    TRUNC(3.999, 2)           AS trunc_2dp;         -- 3.99

-- ─── Absolute Value and Sign ──────────────────────────────
SELECT
    ABS(-42)                  AS absolute,          -- 42
    ABS(-3.14)                AS abs_float,         -- 3.14
    SIGN(-5)                  AS negative_sign,     -- -1
    SIGN(0)                   AS zero_sign,         -- 0
    SIGN(5)                   AS positive_sign;     -- 1

-- ─── Power and Roots ──────────────────────────────────────
SELECT
    POWER(2, 10)              AS two_to_ten,        -- 1024
    SQRT(144)                 AS square_root,       -- 12
    CBRT(27)                  AS cube_root,         -- 3
    EXP(1)                    AS euler,             -- 2.71828...
    LN(EXP(1))                AS natural_log,       -- 1
    LOG(100)                  AS log_base_10,       -- 2
    LOG(2, 8)                 AS log_base_2;        -- 3

-- ─── Modulo and Integer Division ──────────────────────────
SELECT
    17 % 5                    AS modulo_op,         -- 2
    MOD(17, 5)                AS modulo_fn,         -- 2
    17 / 5                    AS int_div,           -- 3  ← integer division!
    17.0 / 5                  AS float_div,         -- 3.4
    17::NUMERIC / 5           AS cast_div;          -- 3.4

-- ─── Min/Max of multiple values ───────────────────────────
SELECT
    GREATEST(10, 20, 5, 30)   AS greatest,          -- 30
    LEAST(10, 20, 5, 30)      AS least_val;         -- 5

-- ─── Statistical Functions ────────────────────────────────
SELECT
    COUNT(price)                                              AS n,
    ROUND(AVG(price), 2)                                      AS mean,
    PERCENTILE_CONT(0.5)  WITHIN GROUP (ORDER BY price)       AS median,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY price)       AS q1,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY price)       AS q3,
    PERCENTILE_DISC(0.9)  WITHIN GROUP (ORDER BY price)       AS p90,
    ROUND(STDDEV(price), 2)                                   AS std_dev,
    ROUND(VARIANCE(price), 2)                                 AS variance
FROM products WHERE is_active = TRUE;

-- ─── Financial Calculations ───────────────────────────────
SELECT
    price,
    ROUND(price * 0.0825, 2)             AS tax_amount,      -- 8.25% tax
    ROUND(price * 1.0825, 2)             AS price_with_tax,
    ROUND(price * (1 - 0.15), 2)         AS after_15pct_discount,
    ROUND(price / 1.0825, 2)             AS pre_tax_price    -- back-calculate
FROM products;

-- ─── Random ──────────────────────────────────────────────
SELECT RANDOM();                              -- 0.0 to 1.0
SELECT FLOOR(RANDOM() * 100)::INT + 1;       -- random 1-100 integer
SELECT * FROM products ORDER BY RANDOM() LIMIT 5;  -- random sample
```

---

## 22. Date & Time Functions

```sql
-- ─── Current Date/Time ───────────────────────────────────
SELECT NOW();               -- 2024-01-15 14:30:00.123456+00 (with tz)
SELECT CURRENT_TIMESTAMP;   -- same as NOW()
SELECT CURRENT_DATE;        -- 2024-01-15
SELECT CURRENT_TIME;        -- 14:30:00.123456+00
SELECT LOCALTIMESTAMP;      -- 2024-01-15 14:30:00 (no tz)

-- ─── Extracting Date Parts ────────────────────────────────
SELECT
    EXTRACT(YEAR    FROM NOW())  AS year,       -- 2024
    EXTRACT(MONTH   FROM NOW())  AS month,      -- 1
    EXTRACT(DAY     FROM NOW())  AS day,        -- 15
    EXTRACT(HOUR    FROM NOW())  AS hour,       -- 14
    EXTRACT(MINUTE  FROM NOW())  AS minute,     -- 30
    EXTRACT(SECOND  FROM NOW())  AS second,     -- 0.123456
    EXTRACT(DOW     FROM NOW())  AS weekday,    -- 0=Sun, 6=Sat
    EXTRACT(DOY     FROM NOW())  AS day_of_year,-- 1-366
    EXTRACT(WEEK    FROM NOW())  AS iso_week,   -- ISO week number
    EXTRACT(QUARTER FROM NOW())  AS quarter,    -- 1-4
    EXTRACT(EPOCH   FROM NOW())  AS unix_epoch; -- seconds since 1970-01-01

-- ─── DATE_TRUNC — truncate to period boundary ─────────────
SELECT
    DATE_TRUNC('year',    NOW()) AS year_start,     -- 2024-01-01 00:00:00
    DATE_TRUNC('quarter', NOW()) AS quarter_start,  -- 2024-01-01 00:00:00
    DATE_TRUNC('month',   NOW()) AS month_start,    -- 2024-01-01 00:00:00
    DATE_TRUNC('week',    NOW()) AS week_start,     -- 2024-01-08 (Monday)
    DATE_TRUNC('day',     NOW()) AS day_start,      -- 2024-01-15 00:00:00
    DATE_TRUNC('hour',    NOW()) AS hour_start;     -- 2024-01-15 14:00:00

-- ─── Date Arithmetic ──────────────────────────────────────
SELECT
    CURRENT_DATE + 7                               AS next_week,
    CURRENT_DATE - 30                              AS thirty_ago,
    CURRENT_DATE + INTERVAL '1 month'              AS next_month,
    CURRENT_DATE + INTERVAL '1 year 3 months'      AS future_date,
    NOW() - INTERVAL '7 days'                      AS week_ago,
    NOW() + INTERVAL '2 hours 30 minutes'          AS later_today;

-- ─── Date Difference ─────────────────────────────────────
SELECT
    AGE('2024-01-15', '1990-05-20')               AS full_age,    -- 33 years 7 months...
    AGE(birth_date)                               AS current_age,
    EXTRACT(YEAR FROM AGE(birth_date))::INT        AS years_old,
    '2024-12-31'::DATE - '2024-01-01'::DATE        AS days_in_year, -- 365
    EXTRACT(EPOCH FROM (end_time - start_time)) / 3600 AS hours_elapsed
FROM customers WHERE birth_date IS NOT NULL;

-- ─── Formatting Dates ─────────────────────────────────────
SELECT
    TO_CHAR(NOW(), 'YYYY-MM-DD')              AS iso_date,      -- '2024-01-15'
    TO_CHAR(NOW(), 'DD/MM/YYYY')             AS uk_format,     -- '15/01/2024'
    TO_CHAR(NOW(), 'Month DD, YYYY')         AS long_format,   -- 'January 15, 2024'
    TO_CHAR(NOW(), 'Day')                    AS day_name,      -- 'Monday   '
    TO_CHAR(NOW(), 'HH12:MI AM')             AS time_12hr,     -- '02:30 PM'
    TO_CHAR(NOW(), 'HH24:MI:SS')             AS time_24hr;     -- '14:30:00'

-- ─── Parsing Strings to Dates ─────────────────────────────
SELECT
    TO_DATE('15/01/2024', 'DD/MM/YYYY')  AS parsed_date,
    TO_TIMESTAMP('2024-01-15 14:30', 'YYYY-MM-DD HH24:MI') AS parsed_ts;

-- ─── Time Zones ───────────────────────────────────────────
SELECT
    NOW()                                    AS utc_time,
    NOW() AT TIME ZONE 'America/New_York'    AS ny_time,
    NOW() AT TIME ZONE 'Asia/Tokyo'          AS tokyo_time,
    NOW() AT TIME ZONE 'Europe/London'       AS london_time;

-- ─── Practical Date Queries ───────────────────────────────
-- Orders from today:
SELECT * FROM orders WHERE ordered_at::DATE = CURRENT_DATE;

-- Orders from this week (Monday–Sunday):
SELECT * FROM orders
WHERE ordered_at >= DATE_TRUNC('week', NOW())
  AND ordered_at <  DATE_TRUNC('week', NOW()) + INTERVAL '1 week';

-- Orders from last 30 days:
SELECT * FROM orders WHERE ordered_at >= NOW() - INTERVAL '30 days';

-- Orders from this year:
SELECT * FROM orders
WHERE ordered_at >= DATE_TRUNC('year', NOW());

-- Customers with birthdays this month:
SELECT first_name, last_name, birth_date
FROM   customers
WHERE  EXTRACT(MONTH FROM birth_date) = EXTRACT(MONTH FROM CURRENT_DATE);

-- Age verification (must be 18+):
SELECT * FROM customers
WHERE EXTRACT(YEAR FROM AGE(birth_date)) >= 18;

-- Group sales by week:
SELECT
    DATE_TRUNC('week', ordered_at)::DATE  AS week_start,
    COUNT(*)                              AS orders,
    SUM(total_amount)                     AS revenue
FROM orders
WHERE status != 'cancelled'
GROUP BY DATE_TRUNC('week', ordered_at)
ORDER BY week_start;
```

---

## 23. NULL Handling

```sql
-- NULL = absence of a value. It is NOT zero, NOT empty string, NOT false.
-- Any arithmetic or comparison with NULL returns NULL.

-- Demonstration:
SELECT NULL = NULL;         -- NULL (not TRUE!)
SELECT NULL = 0;            -- NULL
SELECT NULL + 5;            -- NULL
SELECT 'hi' || NULL;        -- NULL
SELECT NULL IS NULL;        -- TRUE (correct way to check)
SELECT NULL IS NOT NULL;    -- FALSE

-- ─── IS NULL / IS NOT NULL ───────────────────────────────
SELECT * FROM customers WHERE phone IS NULL;
SELECT * FROM customers WHERE phone IS NOT NULL;
SELECT COUNT(*) FILTER (WHERE phone IS NULL)     AS missing_phones
FROM customers;

-- ─── COALESCE — first non-NULL value ─────────────────────
SELECT
    COALESCE(phone, 'No Phone')            AS phone_display,
    COALESCE(discount, 0)                  AS effective_discount,
    COALESCE(city, state, country, 'Unknown') AS location  -- try each in order
FROM customers;

-- Safe arithmetic:
SELECT quantity * COALESCE(unit_price, 0) AS safe_total
FROM order_items;

-- ─── NULLIF — return NULL if equal to a value ─────────────
-- Prevents division by zero:
SELECT revenue / NULLIF(cost, 0) AS margin;  -- returns NULL instead of error

-- Treat empty strings as NULL:
SELECT NULLIF(TRIM(phone), '') AS clean_phone FROM customers;

-- ─── NULL in Aggregates ───────────────────────────────────
-- All aggregate functions IGNORE NULL values (except COUNT(*)):
SELECT
    COUNT(*)       AS total_rows,        -- counts ALL rows including NULLs
    COUNT(phone)   AS rows_with_phone,   -- counts only non-NULL phone rows
    AVG(rating)    AS avg_rating,        -- NULLs excluded from average
    SUM(discount)  AS total_discount     -- NULLs treated as if not there
FROM customers c
LEFT JOIN reviews r USING (customer_id);

-- ─── NULL in WHERE ────────────────────────────────────────
-- ❌ This misses rows where phone IS NULL:
SELECT * FROM customers WHERE phone != '555-1234';

-- ✅ Correctly include NULL rows:
SELECT * FROM customers WHERE phone != '555-1234' OR phone IS NULL;

-- ─── NULL in ORDER BY ────────────────────────────────────
SELECT * FROM products ORDER BY description NULLS LAST;   -- NULLs at end
SELECT * FROM products ORDER BY description NULLS FIRST;  -- NULLs at start

-- ─── NULL in UNIQUE constraint ────────────────────────────
-- Multiple NULLs ARE allowed in a UNIQUE column (NULL ≠ NULL in SQL)
INSERT INTO customers (email, phone) VALUES ('a@x.com', NULL);  -- ✅
INSERT INTO customers (email, phone) VALUES ('b@x.com', NULL);  -- ✅ allowed

-- ─── CASE for NULL handling ───────────────────────────────
SELECT
    first_name,
    CASE
        WHEN phone IS NOT NULL THEN phone
        ELSE 'Contact by email'
    END AS contact_info
FROM customers;
```

---

## 24. CASE Expressions

```sql
-- CASE is SQL's conditional expression (like IF/ELSE)

-- ─── Searched CASE (general conditions) ──────────────────
SELECT
    product_id,
    name,
    price,
    CASE
        WHEN price <  10  THEN 'Budget'
        WHEN price <  50  THEN 'Standard'
        WHEN price < 200  THEN 'Premium'
        ELSE                   'Luxury'
    END AS price_tier
FROM products;

-- ─── Simple CASE (equality matching) ─────────────────────
SELECT
    order_id,
    CASE status
        WHEN 'pending'    THEN '⏳ Awaiting processing'
        WHEN 'processing' THEN '🔄 Being prepared'
        WHEN 'shipped'    THEN '🚚 On the way'
        WHEN 'delivered'  THEN '✅ Delivered'
        WHEN 'cancelled'  THEN '❌ Cancelled'
        ELSE                   '❓ Unknown status'
    END AS status_message
FROM orders;

-- ─── CASE in SELECT with aggregation ─────────────────────
SELECT
    COUNT(*)                                               AS total,
    COUNT(CASE WHEN status = 'pending'    THEN 1 END)      AS pending,
    COUNT(CASE WHEN status = 'delivered'  THEN 1 END)      AS delivered,
    COUNT(CASE WHEN status = 'cancelled'  THEN 1 END)      AS cancelled,
    SUM(CASE WHEN status != 'cancelled'   THEN total_amount ELSE 0 END) AS net_revenue,
    ROUND(
        COUNT(CASE WHEN status = 'delivered' THEN 1 END) * 100.0 / NULLIF(COUNT(*), 0),
        2
    ) AS delivery_rate_pct
FROM orders;

-- Cleaner with FILTER (PostgreSQL):
SELECT
    COUNT(*) FILTER (WHERE status = 'pending')    AS pending,
    COUNT(*) FILTER (WHERE status = 'delivered')  AS delivered,
    SUM(total_amount) FILTER (WHERE status != 'cancelled') AS net_revenue
FROM orders;

-- ─── CASE in ORDER BY ─────────────────────────────────────
SELECT * FROM orders
ORDER BY
    CASE status
        WHEN 'processing' THEN 1
        WHEN 'pending'    THEN 2
        WHEN 'shipped'    THEN 3
        WHEN 'delivered'  THEN 4
        ELSE                   5
    END ASC,
    ordered_at ASC;

-- ─── CASE in UPDATE ───────────────────────────────────────
UPDATE products
SET price_tier =
    CASE
        WHEN price <  10  THEN 'budget'
        WHEN price <  50  THEN 'standard'
        WHEN price < 200  THEN 'premium'
        ELSE                   'luxury'
    END;

-- ─── Nested CASE ──────────────────────────────────────────
SELECT
    name, price, stock,
    CASE
        WHEN stock = 0    THEN 'Out of Stock'
        WHEN stock < 5    THEN
            CASE WHEN price > 100 THEN 'Low Stock - Call to Reserve'
                 ELSE 'Low Stock'
            END
        WHEN stock < 20   THEN 'Limited Availability'
        ELSE                   'In Stock'
    END AS availability_message
FROM products;
```


---

## 25. Indexes

### What Are Indexes?

```sql
-- An index is a data structure that speeds up queries by allowing the DB
-- to find rows without scanning every row in the table.
--
-- Trade-off:
--   ✅ Faster SELECT (especially with WHERE, JOIN, ORDER BY)
--   ❌ Slower INSERT/UPDATE/DELETE (index must be updated too)
--   ❌ Uses disk space
--
-- Rule of thumb: Add indexes on columns you frequently filter, join, or sort on.

-- Without index: Table Scan — reads EVERY row to find matches
-- With index:    Index Scan — jumps directly to matching rows

-- ─── Default B-Tree Index ─────────────────────────────────
-- Best for: equality (=), ranges (<, >, BETWEEN), ORDER BY, LIKE 'prefix%'

CREATE INDEX idx_products_price ON products(price);
CREATE INDEX idx_orders_customer ON orders(customer_id);
CREATE INDEX idx_customers_email ON customers(email);
CREATE INDEX idx_orders_created  ON orders(ordered_at);

-- ─── Unique Index ─────────────────────────────────────────
-- Enforces uniqueness AND speeds up lookups
CREATE UNIQUE INDEX idx_customers_email_unique ON customers(email);

-- ─── Composite (Multi-column) Index ───────────────────────
-- Good when you often filter/sort on multiple columns together
-- Order matters: most selective column first, leftmost is most useful
CREATE INDEX idx_orders_customer_status ON orders(customer_id, status);
CREATE INDEX idx_products_cat_price     ON products(category_id, price DESC);

-- ─── Partial Index ────────────────────────────────────────
-- Index only a subset of rows — smaller, faster for filtered queries
CREATE INDEX idx_orders_pending    ON orders(customer_id)
    WHERE status = 'pending';            -- only index pending orders

CREATE INDEX idx_active_products   ON products(category_id, price)
    WHERE is_active = TRUE;             -- only active products

CREATE INDEX idx_large_orders      ON orders(total_amount)
    WHERE total_amount > 1000;          -- only large orders

-- ─── Expression (Functional) Index ───────────────────────
-- Index on a computed value
CREATE INDEX idx_customers_email_lower ON customers(LOWER(email));
-- Enables fast: WHERE LOWER(email) = 'alice@example.com'

CREATE INDEX idx_orders_month ON orders(DATE_TRUNC('month', ordered_at));
-- Enables fast grouping by month

CREATE INDEX idx_products_name_upper ON products(UPPER(name));

-- ─── CONCURRENTLY — build index without locking table ─────
-- Essential for production tables with ongoing traffic
CREATE INDEX CONCURRENTLY idx_orders_status ON orders(status);
-- ✅ Table remains readable and writable during index creation
-- ❌ Takes longer than regular CREATE INDEX

-- ─── IF NOT EXISTS ───────────────────────────────────────
CREATE INDEX IF NOT EXISTS idx_customers_city ON customers(city);

-- ─── DROP INDEX ──────────────────────────────────────────
DROP INDEX IF EXISTS idx_products_price;
DROP INDEX CONCURRENTLY IF EXISTS idx_orders_status;   -- non-blocking drop

-- ─── REINDEX ─────────────────────────────────────────────
REINDEX INDEX idx_products_price;   -- rebuild a specific index
REINDEX TABLE products;             -- rebuild all indexes on a table
```

### Index Types (PostgreSQL)

```sql
-- ─── B-Tree (default) ─────────────────────────────────────
-- Good for: =, <, <=, >, >=, BETWEEN, LIKE 'prefix%', IN, ORDER BY, IS NULL
CREATE INDEX idx_btree_price ON products USING BTREE (price);

-- ─── Hash Index ───────────────────────────────────────────
-- Good for: equality (=) only — slightly faster than B-tree for pure equality
CREATE INDEX idx_hash_email ON customers USING HASH (email);

-- ─── GIN (Generalized Inverted Index) ────────────────────
-- Good for: array contains (@>), JSONB, full-text search
CREATE INDEX idx_gin_tags        ON products USING GIN (tags);            -- array
CREATE INDEX idx_gin_attributes  ON products USING GIN (attributes);      -- JSONB
CREATE INDEX idx_gin_search      ON articles USING GIN (to_tsvector('english', body)); -- FTS

-- ─── GiST (Generalized Search Tree) ──────────────────────
-- Good for: geometric data, range types, full-text search
CREATE INDEX idx_gist_location ON stores USING GIST (coordinates);
CREATE INDEX idx_gist_range    ON events USING GIST (date_range);

-- ─── BRIN (Block Range Index) ─────────────────────────────
-- Good for: very large tables where values correlate with physical order (e.g., timestamps)
-- Very small index size, good for append-only time-series data
CREATE INDEX idx_brin_created ON events USING BRIN (created_at);

-- ─── Check which indexes exist ────────────────────────────
SELECT
    indexname,
    indexdef,
    pg_size_pretty(pg_relation_size(indexname::TEXT)) AS index_size
FROM pg_indexes
WHERE tablename = 'orders';

-- ─── Find unused indexes ──────────────────────────────────
SELECT
    schemaname,
    tablename,
    indexname,
    idx_scan        AS times_used,
    pg_size_pretty(pg_relation_size(indexrelid)) AS index_size
FROM pg_stat_user_indexes
WHERE idx_scan = 0              -- never used!
  AND indexrelname NOT LIKE 'pk_%'   -- not primary key
ORDER BY pg_relation_size(indexrelid) DESC;

-- ─── Full-Text Search Index ───────────────────────────────
-- Create a tsvector column and index it:
ALTER TABLE products ADD COLUMN search_vector TSVECTOR;

UPDATE products
SET search_vector = to_tsvector('english', COALESCE(name,'') || ' ' || COALESCE(description,''));

CREATE INDEX idx_fts_products ON products USING GIN (search_vector);

-- Search:
SELECT name, description
FROM   products
WHERE  search_vector @@ plainto_tsquery('english', 'wireless bluetooth headphones')
ORDER  BY ts_rank(search_vector, plainto_tsquery('english', 'wireless bluetooth')) DESC;
```

---

## 26. Views

### Regular Views

```sql
-- A VIEW is a saved SELECT query — a virtual table
-- Does NOT store data (computed on each query)
-- Provides abstraction, security, and query simplification

-- ─── Create a view ────────────────────────────────────────
CREATE VIEW active_products AS
    SELECT
        p.product_id,
        p.name,
        p.price,
        p.stock,
        c.name AS category_name
    FROM      products   p
    LEFT JOIN categories c ON p.category_id = c.category_id
    WHERE p.is_active = TRUE;

-- Use it like a table:
SELECT * FROM active_products WHERE price < 50;
SELECT * FROM active_products WHERE category_name = 'Electronics';
SELECT category_name, COUNT(*), AVG(price) FROM active_products GROUP BY category_name;

-- ─── Create or replace ────────────────────────────────────
CREATE OR REPLACE VIEW active_products AS
    SELECT
        p.product_id,
        p.name,
        p.description,           -- added column
        p.price,
        p.stock,
        c.name AS category_name
    FROM      products   p
    LEFT JOIN categories c ON p.category_id = c.category_id
    WHERE p.is_active = TRUE;

-- ─── More complex views ───────────────────────────────────

-- Customer order summary view:
CREATE VIEW customer_summary AS
    SELECT
        c.customer_id,
        c.first_name || ' ' || c.last_name     AS full_name,
        c.email,
        c.country,
        COUNT(o.order_id)                       AS total_orders,
        COALESCE(SUM(o.total_amount), 0)        AS lifetime_value,
        MAX(o.ordered_at)                       AS last_order_date,
        CASE
            WHEN SUM(o.total_amount) > 5000 THEN 'Platinum'
            WHEN SUM(o.total_amount) > 1000 THEN 'Gold'
            WHEN SUM(o.total_amount) > 200  THEN 'Silver'
            WHEN COUNT(o.order_id) > 0      THEN 'Bronze'
            ELSE 'New'
        END                                     AS segment
    FROM      customers c
    LEFT JOIN orders    o ON c.customer_id = o.customer_id
                         AND o.status = 'delivered'
    GROUP BY c.customer_id, c.first_name, c.last_name, c.email, c.country;

-- Monthly revenue view:
CREATE VIEW monthly_revenue AS
    SELECT
        DATE_TRUNC('month', ordered_at)::DATE  AS month,
        COUNT(*)                                AS order_count,
        COUNT(DISTINCT customer_id)             AS unique_customers,
        SUM(total_amount)                       AS revenue,
        ROUND(AVG(total_amount), 2)             AS avg_order_value
    FROM orders
    WHERE status != 'cancelled'
    GROUP BY DATE_TRUNC('month', ordered_at);

-- ─── List all views ───────────────────────────────────────
SELECT table_name, view_definition
FROM   information_schema.views
WHERE  table_schema = 'public';

-- ─── Drop a view ──────────────────────────────────────────
DROP VIEW IF EXISTS active_products;
DROP VIEW IF EXISTS customer_summary CASCADE;   -- also drops dependent views
```

### Updatable Views and Materialized Views

```sql
-- ─── Updatable Views ──────────────────────────────────────
-- Simple views (single table, no GROUP BY/DISTINCT/JOIN) can be updated

CREATE VIEW active_products AS
    SELECT product_id, name, price, stock, category_id
    FROM   products
    WHERE  is_active = TRUE
    WITH CHECK OPTION;   -- prevent INSERT/UPDATE that would make row invisible

-- These work on the view:
UPDATE active_products SET price = 39.99 WHERE product_id = 1;
INSERT INTO active_products (name, price, category_id) VALUES ('New Item', 9.99, 2);
DELETE FROM active_products WHERE product_id = 5;

-- ─── INSTEAD OF Triggers for complex views ────────────────
-- For non-updatable views (joins, etc.), create INSTEAD OF triggers
CREATE OR REPLACE FUNCTION update_order_view()
RETURNS TRIGGER AS $$
BEGIN
    UPDATE orders SET status = NEW.status WHERE order_id = NEW.order_id;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_update_order_view
INSTEAD OF UPDATE ON order_details_view
FOR EACH ROW EXECUTE FUNCTION update_order_view();

-- ─── Materialized Views ───────────────────────────────────
-- Stores the query RESULT physically — much faster to query
-- Must be manually refreshed to get fresh data

CREATE MATERIALIZED VIEW product_stats AS
    SELECT
        p.product_id,
        p.name,
        p.price,
        COUNT(r.review_id)           AS review_count,
        ROUND(AVG(r.rating), 2)      AS avg_rating,
        SUM(oi.quantity)             AS total_units_sold,
        SUM(oi.quantity * oi.unit_price) AS total_revenue
    FROM      products    p
    LEFT JOIN reviews     r  ON p.product_id = r.product_id
    LEFT JOIN order_items oi ON p.product_id = oi.product_id
    GROUP BY  p.product_id, p.name, p.price
WITH DATA;   -- WITH DATA = populate immediately; WITH NO DATA = empty until refreshed

-- Index the materialized view for fast access:
CREATE INDEX idx_mv_product_rating ON product_stats(avg_rating DESC);

-- Query it like a table (very fast):
SELECT * FROM product_stats WHERE avg_rating > 4 ORDER BY total_revenue DESC;

-- Refresh with new data:
REFRESH MATERIALIZED VIEW product_stats;                    -- locks during refresh
REFRESH MATERIALIZED VIEW CONCURRENTLY product_stats;       -- no lock (needs unique index)

-- Unique index required for CONCURRENTLY:
CREATE UNIQUE INDEX idx_mv_product_id ON product_stats(product_id);

-- Auto-refresh with a scheduled job:
-- (In PostgreSQL, use pg_cron or an external scheduler)
-- SELECT cron.schedule('*/10 * * * *', 'REFRESH MATERIALIZED VIEW CONCURRENTLY product_stats');

-- Drop materialized view:
DROP MATERIALIZED VIEW IF EXISTS product_stats;
```

---

## 27. Stored Procedures & Functions

### User-Defined Functions

```sql
-- ─── Simple function — returns a scalar value ─────────────
CREATE OR REPLACE FUNCTION calculate_tax(price DECIMAL, tax_rate DECIMAL DEFAULT 0.08)
RETURNS DECIMAL AS $$
BEGIN
    RETURN ROUND(price * tax_rate, 2);
END;
$$ LANGUAGE plpgsql;

-- Usage:
SELECT calculate_tax(100.00);         -- 8.00
SELECT calculate_tax(100.00, 0.0825); -- 8.25
SELECT name, price, calculate_tax(price) AS tax FROM products;

-- ─── Function returning a table ───────────────────────────
CREATE OR REPLACE FUNCTION get_customer_orders(p_customer_id INT)
RETURNS TABLE (
    order_id      INT,
    status        VARCHAR,
    total_amount  DECIMAL,
    ordered_at    TIMESTAMPTZ,
    item_count    BIGINT
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        o.order_id,
        o.status,
        o.total_amount,
        o.ordered_at,
        COUNT(oi.item_id) AS item_count
    FROM      orders      o
    LEFT JOIN order_items oi ON o.order_id = oi.order_id
    WHERE o.customer_id = p_customer_id
    GROUP BY o.order_id, o.status, o.total_amount, o.ordered_at
    ORDER BY o.ordered_at DESC;
END;
$$ LANGUAGE plpgsql;

-- Usage:
SELECT * FROM get_customer_orders(42);
SELECT * FROM get_customer_orders(42) WHERE status = 'delivered';

-- ─── Function with conditional logic ─────────────────────
CREATE OR REPLACE FUNCTION get_discount_price(
    p_price      DECIMAL,
    p_customer_id INT
) RETURNS DECIMAL AS $$
DECLARE
    v_segment   VARCHAR;
    v_discount  DECIMAL;
BEGIN
    -- Get customer segment:
    SELECT segment INTO v_segment
    FROM   customer_summary
    WHERE  customer_id = p_customer_id;

    -- Apply discount based on segment:
    v_discount := CASE v_segment
        WHEN 'Platinum' THEN 0.20
        WHEN 'Gold'     THEN 0.15
        WHEN 'Silver'   THEN 0.10
        WHEN 'Bronze'   THEN 0.05
        ELSE 0
    END;

    RETURN ROUND(p_price * (1 - v_discount), 2);
END;
$$ LANGUAGE plpgsql;

-- ─── PL/pgSQL language features ───────────────────────────
CREATE OR REPLACE FUNCTION process_order(p_order_id INT)
RETURNS TEXT AS $$
DECLARE
    v_order_status  VARCHAR;
    v_customer_name TEXT;
    v_total         DECIMAL;
    v_item          RECORD;
BEGIN
    -- Declare variables and fetch data:
    SELECT o.status, o.total_amount, c.first_name || ' ' || c.last_name
    INTO   v_order_status, v_total, v_customer_name
    FROM   orders    o
    JOIN   customers c ON o.customer_id = c.customer_id
    WHERE  o.order_id = p_order_id;

    -- Check if order exists:
    IF NOT FOUND THEN
        RAISE EXCEPTION 'Order % not found', p_order_id;
    END IF;

    -- Validate status:
    IF v_order_status != 'pending' THEN
        RAISE WARNING 'Order % is already %', p_order_id, v_order_status;
        RETURN 'SKIPPED: ' || v_order_status;
    END IF;

    -- Loop through items and update stock:
    FOR v_item IN
        SELECT oi.product_id, oi.quantity, p.name, p.stock
        FROM   order_items oi
        JOIN   products    p ON oi.product_id = p.product_id
        WHERE  oi.order_id = p_order_id
    LOOP
        -- Check stock:
        IF v_item.stock < v_item.quantity THEN
            RAISE EXCEPTION 'Insufficient stock for %: have %, need %',
                v_item.name, v_item.stock, v_item.quantity;
        END IF;

        -- Decrement stock:
        UPDATE products
        SET    stock = stock - v_item.quantity
        WHERE  product_id = v_item.product_id;
    END LOOP;

    -- Update order status:
    UPDATE orders SET status = 'processing' WHERE order_id = p_order_id;

    RETURN FORMAT('Order %s processed for %s. Total: $%s', p_order_id, v_customer_name, v_total);

EXCEPTION
    WHEN OTHERS THEN
        -- Rollback happens automatically in a transaction
        RAISE;
END;
$$ LANGUAGE plpgsql;
```

### Stored Procedures

```sql
-- ─── Stored Procedure (PostgreSQL 11+) ────────────────────
-- Unlike functions, procedures can manage transactions (COMMIT/ROLLBACK)
-- Called with CALL, not SELECT

CREATE OR REPLACE PROCEDURE transfer_funds(
    p_from_account  INT,
    p_to_account    INT,
    p_amount        DECIMAL
)
LANGUAGE plpgsql AS $$
DECLARE
    v_from_balance DECIMAL;
BEGIN
    -- Lock both accounts to prevent race conditions:
    SELECT balance INTO v_from_balance
    FROM   accounts
    WHERE  account_id = p_from_account
    FOR UPDATE;

    IF v_from_balance < p_amount THEN
        RAISE EXCEPTION 'Insufficient funds: balance is %, need %',
            v_from_balance, p_amount;
    END IF;

    UPDATE accounts SET balance = balance - p_amount WHERE account_id = p_from_account;
    UPDATE accounts SET balance = balance + p_amount WHERE account_id = p_to_account;

    INSERT INTO transfer_log (from_account, to_account, amount, transferred_at)
    VALUES (p_from_account, p_to_account, p_amount, NOW());

    COMMIT;  -- procedures can COMMIT/ROLLBACK unlike functions
END;
$$;

-- Call a procedure:
CALL transfer_funds(1, 2, 100.00);

-- ─── Drop functions and procedures ───────────────────────
DROP FUNCTION IF EXISTS calculate_tax(DECIMAL, DECIMAL);
DROP FUNCTION IF EXISTS get_customer_orders(INT);
DROP PROCEDURE IF EXISTS transfer_funds(INT, INT, DECIMAL);

-- ─── SQL functions (simpler syntax for SQL-only logic) ────
CREATE OR REPLACE FUNCTION product_count_by_category(p_category_id INT)
RETURNS BIGINT AS $$
    SELECT COUNT(*)
    FROM   products
    WHERE  category_id = p_category_id AND is_active = TRUE;
$$ LANGUAGE sql STABLE;
-- STABLE = same inputs always return same output within a transaction (allows caching)
-- IMMUTABLE = same inputs ALWAYS return same output (no DB access) — strictest
-- VOLATILE = may return different values (default) — functions with side effects

-- ─── List all user-defined functions ─────────────────────
SELECT
    routine_name,
    routine_type,
    data_type  AS return_type
FROM information_schema.routines
WHERE routine_schema = 'public'
ORDER BY routine_name;
```

---

## 28. Triggers

### What Are Triggers?

```sql
-- A trigger automatically executes a function when an event occurs on a table
-- Events: INSERT, UPDATE, DELETE, TRUNCATE
-- Timing: BEFORE, AFTER, INSTEAD OF (for views)
-- Level:  FOR EACH ROW (once per row), FOR EACH STATEMENT (once per statement)

-- Trigger creation pattern:
-- 1. Create the trigger function
-- 2. Create the trigger that calls it

-- ─── Auto-update timestamp trigger ───────────────────────
-- Step 1: Create the trigger function
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();   -- NEW = the row being inserted/updated
    RETURN NEW;               -- must RETURN NEW for BEFORE triggers
END;
$$ LANGUAGE plpgsql;

-- Step 2: Attach to tables
CREATE TRIGGER trg_customers_updated_at
    BEFORE UPDATE ON customers
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at();

CREATE TRIGGER trg_products_updated_at
    BEFORE UPDATE ON products
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at();

-- Now every UPDATE on customers automatically sets updated_at = NOW()!

-- ─── Audit log trigger ────────────────────────────────────
CREATE TABLE audit_log (
    log_id      SERIAL       PRIMARY KEY,
    table_name  VARCHAR(50),
    operation   VARCHAR(10),
    row_id      INT,
    old_data    JSONB,
    new_data    JSONB,
    changed_by  VARCHAR(50)  DEFAULT CURRENT_USER,
    changed_at  TIMESTAMPTZ  DEFAULT NOW()
);

CREATE OR REPLACE FUNCTION log_changes()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO audit_log (table_name, operation, row_id, old_data, new_data)
    VALUES (
        TG_TABLE_NAME,                      -- name of the table that fired trigger
        TG_OP,                              -- 'INSERT', 'UPDATE', or 'DELETE'
        COALESCE(NEW.id, OLD.id),
        CASE WHEN TG_OP = 'INSERT' THEN NULL  ELSE row_to_json(OLD)::JSONB END,
        CASE WHEN TG_OP = 'DELETE' THEN NULL  ELSE row_to_json(NEW)::JSONB END
    );
    RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_audit_products
    AFTER INSERT OR UPDATE OR DELETE ON products
    FOR EACH ROW EXECUTE FUNCTION log_changes();

-- ─── Validation trigger ───────────────────────────────────
CREATE OR REPLACE FUNCTION validate_order_insert()
RETURNS TRIGGER AS $$
DECLARE
    v_customer_active BOOLEAN;
BEGIN
    -- Check if customer is active:
    SELECT is_active INTO v_customer_active
    FROM   customers WHERE customer_id = NEW.customer_id;

    IF NOT v_customer_active THEN
        RAISE EXCEPTION 'Cannot create order: customer % is inactive', NEW.customer_id;
    END IF;

    -- Auto-set ordered_at if not provided:
    IF NEW.ordered_at IS NULL THEN
        NEW.ordered_at = NOW();
    END IF;

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_validate_order
    BEFORE INSERT ON orders
    FOR EACH ROW EXECUTE FUNCTION validate_order_insert();

-- ─── Stock management trigger ─────────────────────────────
CREATE OR REPLACE FUNCTION update_stock_on_order()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        -- Deduct stock when item is added to order:
        UPDATE products
        SET    stock = stock - NEW.quantity
        WHERE  product_id = NEW.product_id;

        -- Raise error if stock goes negative:
        IF (SELECT stock FROM products WHERE product_id = NEW.product_id) < 0 THEN
            RAISE EXCEPTION 'Insufficient stock for product %', NEW.product_id;
        END IF;

    ELSIF TG_OP = 'DELETE' THEN
        -- Restore stock when item is removed:
        UPDATE products
        SET    stock = stock + OLD.quantity
        WHERE  product_id = OLD.product_id;

    ELSIF TG_OP = 'UPDATE' THEN
        -- Adjust for quantity change:
        UPDATE products
        SET    stock = stock - (NEW.quantity - OLD.quantity)
        WHERE  product_id = NEW.product_id;
    END IF;

    RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_stock_management
    AFTER INSERT OR UPDATE OR DELETE ON order_items
    FOR EACH ROW EXECUTE FUNCTION update_stock_on_order();

-- ─── Managing triggers ────────────────────────────────────
-- Disable a trigger:
ALTER TABLE products DISABLE TRIGGER trg_audit_products;

-- Enable it again:
ALTER TABLE products ENABLE TRIGGER trg_audit_products;

-- Disable ALL triggers on a table (e.g., for bulk loading):
ALTER TABLE order_items DISABLE TRIGGER ALL;
-- ... bulk insert ...
ALTER TABLE order_items ENABLE TRIGGER ALL;

-- Drop a trigger:
DROP TRIGGER IF EXISTS trg_customers_updated_at ON customers;
DROP TRIGGER IF EXISTS trg_audit_products        ON products;

-- List all triggers:
SELECT trigger_name, event_manipulation, event_object_table, action_timing
FROM   information_schema.triggers
WHERE  trigger_schema = 'public'
ORDER  BY event_object_table, trigger_name;
```

---

## 29. Transactions & ACID

### Transaction Basics

```sql
-- A transaction groups SQL statements into one atomic unit.
-- Either ALL succeed or ALL fail — no partial changes.
--
-- ACID properties:
--   A — Atomicity:   all or nothing
--   C — Consistency: database remains valid
--   I — Isolation:   concurrent transactions don't interfere
--   D — Durability:  committed data survives crashes

-- ─── Basic transaction ───────────────────────────────────
BEGIN;                  -- start transaction (or: START TRANSACTION)

UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

COMMIT;                 -- save all changes permanently

-- ─── Rollback on error ────────────────────────────────────
BEGIN;

UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
-- Something goes wrong:
-- ERROR: ...
ROLLBACK;               -- undo ALL changes since BEGIN

-- ─── Savepoints — partial rollback ────────────────────────
BEGIN;

UPDATE products SET price = price * 1.10 WHERE category_id = 1;

SAVEPOINT price_update_1;   -- create a checkpoint

UPDATE products SET price = price * 1.10 WHERE category_id = 2;

-- Oops, category 2 update was wrong:
ROLLBACK TO SAVEPOINT price_update_1;  -- undo only the category 2 update
-- Category 1 update is still in effect!

-- Continue:
UPDATE products SET price = price * 1.05 WHERE category_id = 2;

RELEASE SAVEPOINT price_update_1;  -- optional: release savepoint

COMMIT;   -- save both updates

-- ─── Real-world transaction: place an order ───────────────
BEGIN;

-- 1. Create the order:
INSERT INTO orders (customer_id, status, total_amount)
VALUES (42, 'pending', 0)
RETURNING order_id INTO :new_order_id;

-- 2. Add items:
INSERT INTO order_items (order_id, product_id, quantity, unit_price)
VALUES (:new_order_id, 101, 2, 29.99),
       (:new_order_id, 205, 1, 49.99);

-- 3. Deduct stock:
UPDATE products SET stock = stock - 2 WHERE product_id = 101;
UPDATE products SET stock = stock - 1 WHERE product_id = 205;

-- 4. Calculate and update total:
UPDATE orders
SET    total_amount = (
    SELECT SUM(quantity * unit_price) FROM order_items WHERE order_id = :new_order_id
)
WHERE  order_id = :new_order_id;

-- If all good:
COMMIT;
-- If any step failed, the entire order is rolled back automatically
```

### Isolation Levels

```sql
-- Isolation levels control what one transaction can see from concurrent transactions
-- Trade-off: higher isolation = safer but more locking/contention

-- ─── Read Phenomena ───────────────────────────────────────
-- Dirty Read:       reading uncommitted changes from another transaction
-- Non-repeatable Read: same row gives different values in same transaction
-- Phantom Read:     new rows appear in same query within same transaction

-- ─── Isolation Levels (from lowest to highest) ────────────
-- READ UNCOMMITTED  — sees dirty reads (rarely used; not supported in PG)
-- READ COMMITTED    — default in PostgreSQL; no dirty reads
-- REPEATABLE READ   — no dirty reads, no non-repeatable reads
-- SERIALIZABLE      — full isolation; transactions appear sequential

SET TRANSACTION ISOLATION LEVEL READ COMMITTED;     -- default
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- Set for a transaction:
BEGIN ISOLATION LEVEL REPEATABLE READ;
SELECT * FROM accounts WHERE account_id = 1;  -- always sees same data within transaction
COMMIT;

-- ─── Locking ─────────────────────────────────────────────
-- SELECT FOR UPDATE — lock rows so others can't change them
BEGIN;

SELECT balance FROM accounts
WHERE account_id = 1
FOR UPDATE;             -- lock this row until COMMIT/ROLLBACK

-- Now safe to update — no other transaction can change this row:
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
COMMIT;

-- SELECT FOR SHARE — others can read but not update
SELECT * FROM products WHERE product_id = 1 FOR SHARE;

-- SKIP LOCKED — skip rows locked by other transactions
SELECT * FROM job_queue
WHERE  status = 'pending'
ORDER BY created_at
LIMIT  10
FOR UPDATE SKIP LOCKED;   -- great for concurrent job workers!

-- NOWAIT — fail immediately if row is locked (don't wait)
SELECT * FROM orders WHERE order_id = 1 FOR UPDATE NOWAIT;

-- ─── Deadlock prevention ──────────────────────────────────
-- Deadlock: Transaction A waits for B, Transaction B waits for A
-- Always lock resources in a CONSISTENT ORDER to prevent deadlocks:

-- ❌ Risky (could deadlock):
-- Txn A: locks account 1, then tries to lock account 2
-- Txn B: locks account 2, then tries to lock account 1

-- ✅ Safe (always lock smaller ID first):
BEGIN;
SELECT * FROM accounts
WHERE  account_id IN (1, 2)
ORDER  BY account_id          -- consistent order!
FOR UPDATE;
-- Now update both safely
COMMIT;
```

---

## 30. Normalization

### Normal Forms

```sql
-- Normalization = organizing tables to reduce redundancy and improve integrity
-- Each normal form builds on the previous

-- ─── 1NF — First Normal Form ─────────────────────────────
-- Rules:
--   ✅ Each column has atomic (indivisible) values
--   ✅ Each row is unique (has a primary key)
--   ✅ No repeating groups or arrays

-- ❌ VIOLATES 1NF (multiple values in one column):
CREATE TABLE orders_bad (
    order_id   INT PRIMARY KEY,
    customer   VARCHAR(100),
    products   TEXT,            -- 'Widget, Gadget, Doohickey' ← VIOLATION
    quantities TEXT             -- '2, 1, 3'                   ← VIOLATION
);

-- ✅ Satisfies 1NF (separate table for line items):
CREATE TABLE orders_1nf (
    order_id   INT PRIMARY KEY,
    customer   VARCHAR(100)
);
CREATE TABLE order_items_1nf (
    item_id    SERIAL PRIMARY KEY,
    order_id   INT    REFERENCES orders_1nf(order_id),
    product    VARCHAR(100),
    quantity   INT
);

-- ─── 2NF — Second Normal Form ────────────────────────────
-- Rules:
--   ✅ Satisfies 1NF
--   ✅ Every non-key column depends on the ENTIRE primary key
--   (No partial dependencies — relevant only when PK is composite)

-- ❌ VIOLATES 2NF (product_name depends only on product_id, not full PK):
CREATE TABLE order_items_bad (
    order_id      INT,
    product_id    INT,
    product_name  VARCHAR(100),   -- ← depends only on product_id (partial dependency!)
    quantity      INT,
    PRIMARY KEY (order_id, product_id)
);

-- ✅ Satisfies 2NF (move product info to its own table):
CREATE TABLE products_2nf (
    product_id   INT PRIMARY KEY,
    product_name VARCHAR(100)     -- depends on product_id only ✅
);
CREATE TABLE order_items_2nf (
    order_id    INT,
    product_id  INT REFERENCES products_2nf(product_id),
    quantity    INT,
    PRIMARY KEY (order_id, product_id)
    -- no partial dependencies now ✅
);

-- ─── 3NF — Third Normal Form ──────────────────────────────
-- Rules:
--   ✅ Satisfies 2NF
--   ✅ No transitive dependencies (non-key column depends on another non-key column)

-- ❌ VIOLATES 3NF (city depends on zip_code, which is non-key):
CREATE TABLE customers_bad (
    customer_id  INT PRIMARY KEY,
    first_name   VARCHAR(50),
    zip_code     CHAR(5),
    city         VARCHAR(50),    -- ← depends on zip_code (transitive dependency!)
    state        CHAR(2)         -- ← depends on zip_code
);

-- ✅ Satisfies 3NF (move zip→city mapping to its own table):
CREATE TABLE zip_codes (
    zip_code   CHAR(5)    PRIMARY KEY,
    city       VARCHAR(50),
    state      CHAR(2)
);
CREATE TABLE customers_3nf (
    customer_id  INT       PRIMARY KEY,
    first_name   VARCHAR(50),
    zip_code     CHAR(5)   REFERENCES zip_codes(zip_code)
);

-- ─── When to Denormalize ──────────────────────────────────
-- Sometimes breaking 3NF is acceptable for performance:
-- - Reporting/analytics tables (OLAP vs OLTP)
-- - Frequently read data that rarely changes
-- - When JOINs become too expensive

-- Example: store computed values to avoid expensive joins:
ALTER TABLE orders ADD COLUMN customer_email VARCHAR(100);
-- ← technically denormalized (redundant with customers table)
-- ← but avoids JOIN on every order query

-- Always document why you're denormalizing!
```

---

## 31. Advanced Query Patterns

### PIVOT — Rows to Columns

```sql
-- ─── Pivot using conditional aggregation ──────────────────
-- Convert: status rows → status columns

-- Before pivot:
-- month    | status    | count
-- 2024-01  | pending   | 10
-- 2024-01  | delivered | 45
-- 2024-02  | pending   | 8

-- After pivot:
-- month    | pending | delivered | cancelled
-- 2024-01  | 10      | 45        | 3

SELECT
    TO_CHAR(DATE_TRUNC('month', ordered_at), 'YYYY-MM')  AS month,
    COUNT(*) FILTER (WHERE status = 'pending')            AS pending,
    COUNT(*) FILTER (WHERE status = 'processing')         AS processing,
    COUNT(*) FILTER (WHERE status = 'shipped')            AS shipped,
    COUNT(*) FILTER (WHERE status = 'delivered')          AS delivered,
    COUNT(*) FILTER (WHERE status = 'cancelled')          AS cancelled
FROM orders
GROUP BY DATE_TRUNC('month', ordered_at)
ORDER BY month;

-- Dynamic pivot using crosstab (PostgreSQL tablefunc extension):
CREATE EXTENSION IF NOT EXISTS tablefunc;

SELECT * FROM crosstab(
    'SELECT customer_id, product_id, rating FROM reviews ORDER BY 1, 2',
    'SELECT DISTINCT product_id FROM reviews ORDER BY 1'
) AS ct(customer_id INT, prod_1 INT, prod_2 INT, prod_3 INT);

-- ─── UNPIVOT — Columns to Rows ────────────────────────────
-- Convert wide format → long format using UNION ALL:
SELECT 'Q1' AS quarter, q1_sales AS sales FROM quarterly_sales
UNION ALL
SELECT 'Q2',             q2_sales          FROM quarterly_sales
UNION ALL
SELECT 'Q3',             q3_sales          FROM quarterly_sales
UNION ALL
SELECT 'Q4',             q4_sales          FROM quarterly_sales
ORDER BY quarter;
```

### Gaps and Islands

```sql
-- ─── Find gaps in sequential IDs ─────────────────────────
SELECT
    id + 1                AS gap_start,
    next_id - 1           AS gap_end,
    next_id - id - 1      AS gap_size
FROM (
    SELECT
        order_id AS id,
        LEAD(order_id) OVER (ORDER BY order_id) AS next_id
    FROM orders
) gaps
WHERE next_id - id > 1;

-- ─── Find gaps in date sequences (missing days) ───────────
WITH date_range AS (
    SELECT generate_series(
        MIN(ordered_at)::DATE,
        MAX(ordered_at)::DATE,
        '1 day'::INTERVAL
    )::DATE AS dt
    FROM orders
)
SELECT dr.dt AS missing_date
FROM   date_range dr
LEFT JOIN (
    SELECT DISTINCT ordered_at::DATE AS order_date FROM orders
) od ON dr.dt = od.order_date
WHERE od.order_date IS NULL
ORDER BY dr.dt;

-- ─── Islands: find consecutive date groups ────────────────
-- Find contiguous ranges of active subscription days:
WITH numbered AS (
    SELECT
        active_date,
        active_date - ROW_NUMBER() OVER (ORDER BY active_date) * INTERVAL '1 day' AS grp
    FROM active_subscription_days
),
islands AS (
    SELECT
        MIN(active_date) AS island_start,
        MAX(active_date) AS island_end,
        COUNT(*)          AS days_count
    FROM   numbered
    GROUP  BY grp
)
SELECT * FROM islands ORDER BY island_start;
```

### Deduplication

```sql
-- ─── Find duplicate rows ──────────────────────────────────
SELECT email, COUNT(*) AS count
FROM   customers
GROUP  BY email
HAVING COUNT(*) > 1;

-- Find duplicates with all their details:
SELECT *
FROM customers
WHERE email IN (
    SELECT email FROM customers GROUP BY email HAVING COUNT(*) > 1
)
ORDER BY email, customer_id;

-- ─── Delete duplicates, keep the first occurrence ─────────
DELETE FROM customers
WHERE customer_id NOT IN (
    SELECT MIN(customer_id)   -- keep the lowest ID per email
    FROM   customers
    GROUP  BY email
);

-- Or using CTEs (cleaner):
WITH ranked AS (
    SELECT
        customer_id,
        ROW_NUMBER() OVER (PARTITION BY email ORDER BY customer_id ASC) AS rn
    FROM customers
)
DELETE FROM customers
WHERE  customer_id IN (
    SELECT customer_id FROM ranked WHERE rn > 1   -- all but the first
);

-- ─── SELECT DISTINCT ON (PostgreSQL) — keep first per group ─
SELECT DISTINCT ON (email)
    customer_id, email, first_name, created_at
FROM   customers
ORDER  BY email, customer_id ASC;   -- keeps lowest customer_id per email
```

### Running Totals and Cumulative Queries

```sql
-- ─── Cumulative sum ───────────────────────────────────────
SELECT
    ordered_at::DATE    AS date,
    total_amount        AS daily_revenue,
    SUM(total_amount) OVER (ORDER BY ordered_at)  AS cumulative_revenue,
    COUNT(*) OVER (ORDER BY ordered_at)            AS cumulative_orders
FROM orders
WHERE status != 'cancelled'
ORDER BY ordered_at;

-- ─── Year-to-date revenue ────────────────────────────────
SELECT
    TO_CHAR(ordered_at, 'YYYY-MM') AS month,
    SUM(total_amount)              AS monthly_revenue,
    SUM(SUM(total_amount)) OVER (
        PARTITION BY EXTRACT(YEAR FROM ordered_at)
        ORDER BY DATE_TRUNC('month', ordered_at)
    ) AS ytd_revenue
FROM orders
WHERE status != 'cancelled'
GROUP BY TO_CHAR(ordered_at, 'YYYY-MM'), DATE_TRUNC('month', ordered_at),
         EXTRACT(YEAR FROM ordered_at)
ORDER BY month;

-- ─── Cohort analysis ─────────────────────────────────────
-- How many users from each signup cohort are still ordering?
WITH cohorts AS (
    SELECT
        customer_id,
        DATE_TRUNC('month', created_at)::DATE AS cohort_month
    FROM customers
),
cohort_orders AS (
    SELECT
        c.cohort_month,
        DATE_TRUNC('month', o.ordered_at)::DATE AS order_month,
        COUNT(DISTINCT c.customer_id) AS active_users
    FROM      cohorts c
    JOIN      orders  o ON c.customer_id = o.customer_id
    GROUP BY  c.cohort_month, DATE_TRUNC('month', o.ordered_at)
),
cohort_sizes AS (
    SELECT cohort_month, COUNT(*) AS cohort_size FROM cohorts GROUP BY cohort_month
)
SELECT
    co.cohort_month,
    co.order_month,
    cs.cohort_size,
    co.active_users,
    ROUND(co.active_users * 100.0 / cs.cohort_size, 1) AS retention_pct
FROM   cohort_orders co
JOIN   cohort_sizes  cs ON co.cohort_month = cs.cohort_month
ORDER  BY co.cohort_month, co.order_month;
```

### Hierarchical Queries

```sql
-- ─── Adjacency List Model (parent_id) ─────────────────────
-- Simple, easy to update, unlimited depth
-- Query with recursive CTE (see CTEs section)

-- ─── Path Enumeration Model ───────────────────────────────
-- Store full path in each row for fast subtree queries
CREATE TABLE categories_path (
    category_id  INT         PRIMARY KEY,
    name         VARCHAR(50),
    path         TEXT,           -- e.g., '/1/3/7/' — ancestor IDs in path
    depth        INT
);

-- Find all descendants of category 3:
SELECT * FROM categories_path WHERE path LIKE '/1/3/%';

-- ─── Nested Set Model ─────────────────────────────────────
-- Fast reads, complex writes — good for read-heavy hierarchies
CREATE TABLE categories_nested (
    category_id   INT PRIMARY KEY,
    name          VARCHAR(50),
    lft           INT NOT NULL,   -- left value
    rgt           INT NOT NULL    -- right value
);
-- All descendants of node X: WHERE lft > X.lft AND rgt < X.rgt
-- Depth of node: (lft - 1) / 2 — ancestors have lft < this.lft AND rgt > this.rgt
```

---

## 32. Performance & Optimization

### EXPLAIN and EXPLAIN ANALYZE

```sql
-- ─── EXPLAIN — show the query execution plan ─────────────
EXPLAIN
SELECT * FROM orders WHERE customer_id = 42;

-- Output:
-- Index Scan using idx_orders_customer on orders
--   (cost=0.29..8.31 rows=5 width=72) ← estimated cost
--   Index Cond: (customer_id = 42)

-- ─── EXPLAIN ANALYZE — actually runs the query ────────────
EXPLAIN ANALYZE
SELECT o.*, c.email
FROM   orders    o
JOIN   customers c ON o.customer_id = c.customer_id
WHERE  o.status = 'delivered'
ORDER  BY o.ordered_at DESC
LIMIT  100;

-- Output includes:
-- cost=0.00..45.50 rows=100 width=120
-- actual time=0.123..4.567 rows=100 loops=1  ← real execution time
-- Buffers: shared hit=234 read=12            ← cache hits vs disk reads

-- ─── EXPLAIN output nodes ─────────────────────────────────
-- Seq Scan        — full table scan (⚠️ no index used)
-- Index Scan      — uses an index (✅ good for selective queries)
-- Index Only Scan — reads only from index (✅ fastest)
-- Bitmap Scan     — combines multiple indexes
-- Nested Loop     — join by iterating inner for each outer row
-- Hash Join       — join by building a hash table (good for large tables)
-- Merge Join      — join pre-sorted inputs
-- Sort            — explicit sort (may use lots of memory)
-- Aggregate       — GROUP BY / COUNT / SUM etc.

-- ─── EXPLAIN ANALYZE options ──────────────────────────────
EXPLAIN (
    ANALYZE   true,    -- actually execute
    BUFFERS   true,    -- show buffer hits/misses
    FORMAT    json,    -- json, text, xml, yaml
    VERBOSE   true     -- column names, schema info
)
SELECT * FROM products WHERE price > 100;

-- ─── Useful EXPLAIN metrics to watch ─────────────────────
-- cost=startup..total  ← planner estimate (in arbitrary units)
-- rows=N               ← estimated row count (if way off, statistics may be stale)
-- actual time=X..Y     ← real time in milliseconds
-- loops=N              ← how many times this node executed
-- Buffers hit=N read=N ← cache hits (fast) vs disk reads (slow)
```

### Query Optimization Techniques

```sql
-- ─── 1. Avoid SELECT * ────────────────────────────────────
-- ❌ Bad: transfers unnecessary data
SELECT * FROM orders JOIN customers ON ...;

-- ✅ Good: only fetch needed columns
SELECT o.order_id, o.total_amount, c.email FROM orders o JOIN customers c ON ...;

-- ─── 2. Use indexed columns in WHERE/JOIN ─────────────────
-- ❌ Breaks index — function call on indexed column:
SELECT * FROM orders WHERE EXTRACT(YEAR FROM ordered_at) = 2024;

-- ✅ Index can be used (range scan):
SELECT * FROM orders WHERE ordered_at >= '2024-01-01' AND ordered_at < '2025-01-01';

-- ─── 3. Avoid leading wildcards ───────────────────────────
-- ❌ Can't use index:
SELECT * FROM customers WHERE email LIKE '%gmail%';

-- ✅ Can use index:
SELECT * FROM customers WHERE email LIKE 'alice%';

-- ─── 4. Use EXISTS instead of COUNT for existence checks ──
-- ❌ Counts all matching rows unnecessarily:
SELECT * FROM customers WHERE (SELECT COUNT(*) FROM orders WHERE customer_id = customers.customer_id) > 0;

-- ✅ Stops at first match:
SELECT * FROM customers c WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id);

-- ─── 5. Update statistics ─────────────────────────────────
-- Outdated statistics = bad query plans
ANALYZE;                -- update statistics for all tables
ANALYZE products;       -- update for one table
VACUUM ANALYZE;         -- reclaim dead space + update statistics

-- ─── 6. Table statistics ──────────────────────────────────
-- Find slow queries:
SELECT
    query,
    calls,
    total_exec_time,
    mean_exec_time,
    rows
FROM   pg_stat_statements
ORDER  BY mean_exec_time DESC
LIMIT  20;
-- Requires: CREATE EXTENSION pg_stat_statements;

-- Table bloat:
SELECT
    tablename,
    pg_size_pretty(pg_total_relation_size(tablename::regclass)) AS total_size,
    pg_size_pretty(pg_relation_size(tablename::regclass))       AS table_size,
    pg_size_pretty(pg_indexes_size(tablename::regclass))        AS index_size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(tablename::regclass) DESC;

-- ─── 7. Partitioning large tables ────────────────────────
-- Partition orders by year for much faster queries on recent data:
CREATE TABLE orders_partitioned (
    order_id      SERIAL,
    customer_id   INT           NOT NULL,
    status        VARCHAR(20)   NOT NULL,
    total_amount  DECIMAL(10,2),
    ordered_at    TIMESTAMPTZ   NOT NULL DEFAULT NOW()
) PARTITION BY RANGE (ordered_at);   -- partition key

-- Create partitions:
CREATE TABLE orders_2023 PARTITION OF orders_partitioned
    FOR VALUES FROM ('2023-01-01') TO ('2024-01-01');

CREATE TABLE orders_2024 PARTITION OF orders_partitioned
    FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

CREATE TABLE orders_2025 PARTITION OF orders_partitioned
    FOR VALUES FROM ('2025-01-01') TO ('2026-01-01');

-- Query automatically uses only relevant partition(s):
SELECT * FROM orders_partitioned
WHERE  ordered_at >= '2024-06-01' AND ordered_at < '2024-07-01';
-- → Only scans orders_2024 partition!

-- ─── 8. Connection pooling ────────────────────────────────
-- PostgreSQL has overhead per connection
-- Use PgBouncer or similar pooler in production
-- Set work_mem appropriately for complex queries:
SET work_mem = '256MB';   -- per-sort-operation memory budget

-- ─── 9. VACUUM and maintenance ───────────────────────────
VACUUM ANALYZE orders;             -- reclaim space + update stats
VACUUM FULL orders;                -- full rewrite (locks table, use carefully)
REINDEX TABLE products;            -- rebuild all indexes
CLUSTER orders USING idx_orders_customer; -- physically reorder table by index
```

---

## 33. Security & Access Control

### Users, Roles, and Privileges

```sql
-- ─── Create roles ─────────────────────────────────────────
CREATE ROLE readonly_user;
CREATE ROLE app_user;
CREATE ROLE admin_user SUPERUSER;

-- Create login user with password:
CREATE ROLE alice WITH LOGIN PASSWORD 'SecurePass123!';
CREATE USER  bob   PASSWORD 'AnotherPass456!';   -- CREATE USER = CREATE ROLE + LOGIN

-- ─── Grant privileges ─────────────────────────────────────
-- On database:
GRANT CONNECT ON DATABASE ecommerce TO readonly_user;
GRANT CONNECT ON DATABASE ecommerce TO app_user;

-- On schema:
GRANT USAGE ON SCHEMA public TO readonly_user;
GRANT USAGE ON SCHEMA public TO app_user;

-- On tables:
GRANT SELECT                         ON ALL TABLES IN SCHEMA public TO readonly_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;

-- On specific table/columns:
GRANT SELECT (customer_id, first_name, last_name, email) ON customers TO readonly_user;
GRANT SELECT, INSERT, UPDATE ON products TO app_user;

-- On sequences (for SERIAL/auto-increment):
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- On functions:
GRANT EXECUTE ON FUNCTION calculate_tax(DECIMAL, DECIMAL) TO app_user;
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA public            TO app_user;

-- ─── Role inheritance ─────────────────────────────────────
GRANT readonly_user TO alice;   -- alice inherits all readonly_user privileges
GRANT app_user      TO bob;

-- Grant a role to another role:
GRANT readonly_user TO app_user;   -- app_user gets all readonly_user permissions too

-- ─── Revoke privileges ────────────────────────────────────
REVOKE INSERT ON products FROM app_user;
REVOKE ALL    ON customers FROM alice;
REVOKE CONNECT ON DATABASE ecommerce FROM old_user;

-- ─── Row-Level Security (RLS) ─────────────────────────────
-- Restrict which ROWS a user can see/modify

-- Enable RLS on a table:
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

-- Create policy: users can only see their own orders:
CREATE POLICY customer_own_orders ON orders
    USING (customer_id = current_setting('app.current_customer_id')::INT);

-- Admin bypass:
CREATE POLICY admin_all_orders ON orders
    TO admin_role
    USING (TRUE);   -- admin sees all rows

-- Policy for INSERT:
CREATE POLICY insert_own_orders ON orders
    FOR INSERT
    WITH CHECK (customer_id = current_setting('app.current_customer_id')::INT);

-- Set the current customer context:
SET app.current_customer_id = '42';
SELECT * FROM orders;   -- only returns orders for customer 42!

-- ─── View existing privileges ─────────────────────────────
-- Table privileges:
SELECT grantee, table_name, privilege_type
FROM   information_schema.role_table_grants
WHERE  table_schema = 'public'
ORDER  BY grantee, table_name;

-- List all roles:
SELECT rolname, rolsuper, rolcreatedb, rolcanlogin
FROM   pg_roles
ORDER  BY rolname;

-- Show current user:
SELECT CURRENT_USER, SESSION_USER;

-- ─── Alter and drop users ─────────────────────────────────
ALTER USER alice PASSWORD 'NewSecurePass!';
ALTER USER alice VALID UNTIL '2025-12-31';     -- expire password
ALTER USER alice NOLOGIN;                      -- disable login
ALTER ROLE  bob CREATEDB;                      -- allow creating databases

DROP USER IF EXISTS alice;
DROP ROLE IF EXISTS readonly_user;

-- ─── SQL Injection Prevention ─────────────────────────────
-- ❌ NEVER build queries with string concatenation:
-- query = "SELECT * FROM users WHERE email = '" + user_input + "'"
-- If user_input = "'; DROP TABLE users;--"  → DISASTER!

-- ✅ ALWAYS use parameterized queries / prepared statements:
-- In PostgreSQL:
PREPARE get_customer(TEXT) AS
    SELECT * FROM customers WHERE email = $1;
EXECUTE get_customer('alice@example.com');

-- In application code (Node.js example):
-- db.query('SELECT * FROM customers WHERE email = $1', [userInput])

-- ─── Audit and monitoring ─────────────────────────────────
-- Enable query logging (postgresql.conf):
-- log_min_duration_statement = 1000   # log queries > 1 second
-- log_statement = 'ddl'               # log DDL statements
-- log_connections = on                # log connections

-- Enable audit extension:
CREATE EXTENSION pgaudit;
-- SET pgaudit.log = 'read, write, ddl';
```

---

## Quick Reference Cheat Sheet

```sql
-- ══════════════════════════════════════════════════════════
--                    SQL CHEAT SHEET
-- ══════════════════════════════════════════════════════════

-- ─── DDL ──────────────────────────────────────────────────
CREATE TABLE t (id SERIAL PRIMARY KEY, name TEXT NOT NULL);
ALTER TABLE  t ADD COLUMN email TEXT;
ALTER TABLE  t RENAME COLUMN name TO full_name;
ALTER TABLE  t DROP COLUMN email;
DROP TABLE IF EXISTS t CASCADE;
TRUNCATE     t RESTART IDENTITY;

-- ─── DML ──────────────────────────────────────────────────
INSERT INTO t (name) VALUES ('Alice'), ('Bob');
UPDATE t SET name = 'Carol' WHERE id = 1;
DELETE FROM t WHERE id = 2;
INSERT INTO t VALUES (1,'Alice') ON CONFLICT (id) DO UPDATE SET name = EXCLUDED.name;

-- ─── SELECT ───────────────────────────────────────────────
SELECT DISTINCT col1, col2, expr AS alias
FROM   table1 t1
JOIN   table2 t2 ON t1.id = t2.t1_id
WHERE  condition AND col IN (v1, v2) AND col BETWEEN a AND b
GROUP  BY col1
HAVING COUNT(*) > 10
ORDER  BY col1 ASC, col2 DESC NULLS LAST
LIMIT  20 OFFSET 40;

-- ─── JOINS summary ────────────────────────────────────────
FROM a JOIN  b ON a.id=b.a_id      -- INNER: only matches
FROM a LEFT  JOIN b ON a.id=b.a_id -- LEFT:  all a + matches
FROM a RIGHT JOIN b ON a.id=b.a_id -- RIGHT: all b + matches
FROM a FULL  JOIN b ON a.id=b.a_id -- FULL:  all rows both
FROM a CROSS JOIN b                 -- all a × all b combinations

-- ─── Aggregate Functions ──────────────────────────────────
COUNT(*), COUNT(col), COUNT(DISTINCT col)
SUM(col), AVG(col), MIN(col), MAX(col)
STRING_AGG(col, sep ORDER BY col)
ARRAY_AGG(col), JSON_AGG(col)
STDDEV(col), VARIANCE(col)
PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY col)

-- ─── Window Functions ─────────────────────────────────────
fn() OVER (PARTITION BY col ORDER BY col ROWS BETWEEN ... AND ...)
ROW_NUMBER(), RANK(), DENSE_RANK(), NTILE(n)
LAG(col, n, default), LEAD(col, n, default)
FIRST_VALUE(col), LAST_VALUE(col), NTH_VALUE(col, n)
SUM/AVG/COUNT/MIN/MAX() OVER (...)

-- ─── String Functions ─────────────────────────────────────
LENGTH, UPPER, LOWER, INITCAP, TRIM, LTRIM, RTRIM
LPAD, RPAD, SUBSTRING, LEFT, RIGHT, SPLIT_PART
REPLACE, REGEXP_REPLACE, POSITION, STRPOS
CONCAT, CONCAT_WS, ||, REPEAT, REVERSE

-- ─── Date Functions ───────────────────────────────────────
NOW(), CURRENT_DATE, CURRENT_TIMESTAMP
EXTRACT(YEAR/MONTH/DAY/HOUR/... FROM ts)
DATE_TRUNC('month'/'week'/..., ts)
AGE(ts1, ts2), date + INTERVAL '1 month'
TO_CHAR(ts, 'YYYY-MM-DD'), TO_DATE(str, fmt)

-- ─── NULL Handling ────────────────────────────────────────
col IS NULL, col IS NOT NULL
COALESCE(col, default_value)
NULLIF(col, value)    -- returns NULL if equal

-- ─── Conditional ──────────────────────────────────────────
CASE WHEN cond THEN val WHEN ... ELSE val END
CASE col WHEN v1 THEN r1 WHEN v2 THEN r2 ELSE r3 END

-- ─── CTEs ─────────────────────────────────────────────────
WITH name AS (SELECT ...), name2 AS (SELECT ... FROM name)
SELECT ... FROM name2;

WITH RECURSIVE name AS (
    SELECT ... -- anchor
    UNION ALL
    SELECT ... FROM name -- recursive
) SELECT ... FROM name;

-- ─── Transactions ─────────────────────────────────────────
BEGIN;
SAVEPOINT sp1;
ROLLBACK TO SAVEPOINT sp1;
COMMIT;   -- or ROLLBACK;

-- ─── Indexes ──────────────────────────────────────────────
CREATE [UNIQUE] INDEX [CONCURRENTLY] idx_name ON t(col);
CREATE INDEX idx ON t(col) WHERE condition;     -- partial
CREATE INDEX idx ON t(LOWER(col));              -- expression
DROP INDEX CONCURRENTLY IF EXISTS idx_name;

-- ─── Views ────────────────────────────────────────────────
CREATE [OR REPLACE] VIEW v AS SELECT ...;
CREATE MATERIALIZED VIEW mv AS SELECT ... WITH DATA;
REFRESH MATERIALIZED VIEW [CONCURRENTLY] mv;
DROP VIEW IF EXISTS v;

-- ─── Permissions ──────────────────────────────────────────
CREATE ROLE r WITH LOGIN PASSWORD 'pass';
GRANT SELECT ON ALL TABLES IN SCHEMA public TO r;
REVOKE INSERT ON products FROM r;
ALTER TABLE t ENABLE ROW LEVEL SECURITY;
CREATE POLICY p ON t USING (user_id = current_user_id());

-- ─── Useful Operators ─────────────────────────────────────
LIKE, ILIKE, ~, ~*          -- pattern matching
@>, <@, &&, ?               -- JSONB / array operators
ANY(), ALL(), SOME()        -- subquery comparisons
EXISTS, NOT EXISTS          -- row existence check
BETWEEN, IN, NOT IN

-- ─── Common Clauses Order ────────────────────────────────
-- SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT → OFFSET
-- (Execution order: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT)

-- ─── Status Codes in SQL ─────────────────────────────────
-- P0001 raise_exception
-- 23000 integrity_constraint_violation
-- 23503 foreign_key_violation
-- 23505 unique_violation
-- 23514 check_violation
-- 40001 serialization_failure (retry!)
-- 42P01 undefined_table
-- 42703 undefined_column
```

---

## Complete Example: E-Commerce Queries

```sql
-- ─── 1. Product catalog with full details ─────────────────
SELECT
    p.product_id,
    p.name,
    p.price,
    ROUND(p.price * 1.08, 2)                           AS price_with_tax,
    cat.name                                            AS category,
    p.stock,
    CASE
        WHEN p.stock = 0  THEN 'Out of Stock'
        WHEN p.stock < 10 THEN 'Low Stock (' || p.stock || ')'
        ELSE 'In Stock'
    END                                                 AS availability,
    COALESCE(stats.review_count, 0)                    AS reviews,
    COALESCE(ROUND(stats.avg_rating, 1), 0)            AS rating,
    COALESCE(stats.total_sold, 0)                      AS units_sold
FROM products p
LEFT JOIN categories cat ON p.category_id = cat.category_id
LEFT JOIN (
    SELECT
        r.product_id,
        COUNT(r.review_id)       AS review_count,
        AVG(r.rating)            AS avg_rating,
        SUM(oi.quantity)         AS total_sold
    FROM reviews     r
    LEFT JOIN order_items oi ON r.product_id = oi.product_id
    GROUP BY r.product_id
) stats ON p.product_id = stats.product_id
WHERE p.is_active = TRUE
ORDER BY stats.total_sold DESC NULLS LAST, p.name;

-- ─── 2. Customer 360 view ─────────────────────────────────
WITH customer_metrics AS (
    SELECT
        o.customer_id,
        COUNT(o.order_id)                              AS order_count,
        SUM(o.total_amount)                            AS lifetime_value,
        AVG(o.total_amount)                            AS avg_order_value,
        MIN(o.ordered_at)                              AS first_order,
        MAX(o.ordered_at)                              AS last_order,
        NOW() - MAX(o.ordered_at)                      AS since_last_order
    FROM orders o
    WHERE o.status = 'delivered'
    GROUP BY o.customer_id
),
favorite_category AS (
    SELECT DISTINCT ON (o.customer_id)
        o.customer_id,
        cat.name AS top_category
    FROM orders o
    JOIN order_items oi ON o.order_id = oi.order_id
    JOIN products     p  ON oi.product_id = p.product_id
    JOIN categories   cat ON p.category_id = cat.category_id
    GROUP BY o.customer_id, cat.name
    ORDER BY o.customer_id, COUNT(*) DESC
)
SELECT
    c.customer_id,
    c.first_name || ' ' || c.last_name                AS name,
    c.email,
    c.city,
    c.country,
    COALESCE(m.order_count, 0)                         AS orders,
    COALESCE(ROUND(m.lifetime_value, 2), 0)            AS ltv,
    COALESCE(ROUND(m.avg_order_value, 2), 0)           AS avg_order,
    m.first_order::DATE,
    m.last_order::DATE,
    EXTRACT(DAY FROM m.since_last_order)::INT          AS days_since_last,
    CASE
        WHEN m.lifetime_value > 5000 THEN 'Platinum'
        WHEN m.lifetime_value > 1000 THEN 'Gold'
        WHEN m.lifetime_value > 200  THEN 'Silver'
        WHEN m.order_count > 0       THEN 'Bronze'
        ELSE 'Never Ordered'
    END                                                AS segment,
    fc.top_category
FROM customers       c
LEFT JOIN customer_metrics   m  ON c.customer_id = m.customer_id
LEFT JOIN favorite_category  fc ON c.customer_id = fc.customer_id
ORDER BY m.lifetime_value DESC NULLS LAST;

-- ─── 3. Sales dashboard ───────────────────────────────────
WITH daily_sales AS (
    SELECT
        ordered_at::DATE                               AS sale_date,
        COUNT(*)                                       AS orders,
        COUNT(DISTINCT customer_id)                    AS unique_customers,
        SUM(total_amount)                              AS revenue
    FROM orders
    WHERE status != 'cancelled'
    GROUP BY ordered_at::DATE
)
SELECT
    sale_date,
    orders,
    unique_customers,
    ROUND(revenue, 2)                                  AS revenue,
    ROUND(SUM(revenue) OVER (
        ORDER BY sale_date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ), 2)                                              AS rolling_7d_revenue,
    ROUND(revenue / NULLIF(LAG(revenue) OVER (ORDER BY sale_date), 0) * 100 - 100, 1)
                                                       AS day_over_day_pct,
    ROUND(SUM(revenue) OVER (
        PARTITION BY DATE_TRUNC('month', sale_date)
        ORDER BY sale_date
    ), 2)                                              AS mtd_revenue,
    ROUND(SUM(revenue) OVER (
        PARTITION BY DATE_TRUNC('year', sale_date)
        ORDER BY sale_date
    ), 2)                                              AS ytd_revenue
FROM daily_sales
ORDER BY sale_date;
```

---

*📘 SQL Complete Reference Guide — All concepts, all examples, production-ready patterns*
*Happy Querying! 🚀*
