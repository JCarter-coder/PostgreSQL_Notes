# PostgreSQL Notes
Personal notes for creating and accessing PostgreSQL databases.
Official documentation found here: https://www.postgresql.org/docs/

## Traditional Commands
- Data Definition Language (DDL)
- Data Query Language (DQL)
- Data Manipulation Language (DML)
- Data Control Language (DCL)

```
SQL Commands
|
|--DDL
|  |--CREATE
|  |--ALTER
|  |--DROP
|  |--RENAME
|  |--TRUNCATE
|  '--COMMENT
|--DQL
|  '--SELECT
|--DML
|  |--INSERT
|  |--UPDATE
|  |--DELETE
|  |--MERGE
|  |--CALL
|  |--EXPLAIN PLAN
|  '--LOCK TABLE
'--DCL
   |--GRANT
   '--REVOKE
```

## Login to a Database

Login to your database with the following command:

```
psql -U <user> <database>
```

NOTE: If no database name is given, postgres will assume connection to a database with the same name as the user. For example, in the following command, entering the user "postgres" will open the "postgres" database.

```
psql -U postgres
```

## Databases and Templates

- POSTGRES (Default Database)
- TEMPLATE0
- TEMPLATE1

TEMPLATE0 is used to create TEMPLATE1. Do not change TEMPLATE0.

TEMPLATE1 is used to create new databases.

NOTE: New databases cannot be created if TEMPLATE1 is being accessed. Have all users disconnect from TEMPLATE1 to create a new database.

### To Create a Template Database
```
postgres=# CREATE DATABASE supertemplate;

postgres=# CREATE TABLE supertable ();

postgres=# CREATE DATABASE superdatabase WITH TEMPLATE supertemplate;
-- this will create a new database with the supertemplate which includes the supertable
```

### To Create a Database
```
postgres=# CREATE DATABASE name
  [ [ WITH ] [ OWNER [=] user_name ]
    [ TEMPLATE [=] template ]
    [ ENCODING [=] encoding ]
    [ LC_COLLATE [=] lc_collate ]
    [ LC_CTYPE [=] lc_ctype ]
    [ TABLESPACE [=] tablespace ]
    [ CONNECTION LIMIT [=] connlimit ] ]
```
Default Settings:
- template1
- owner is current user
- encoding is UTF8
- connection limit is 100
- schema is "public"

Show all schemas:
```
postgres=# CREATE SCHEMA tech;
CREATE SCHEMA
postgres=# \dn
  List of schemas
  Name  |  Owner
--------+----------
 public | postgres
 tech   | postgres
(2 rows)
```

## Roles in Postgres

Roles have attributes and privileges

### Common Attributes
- LOGIN PRIVILEGE
- SUPERUSER STATUS
- DATABASE CREATION
- ROLE CREATION
- PASSWORD

```
postgres=# CREATE ROLE readonly WITH LOGIN ENCRYPTED PASSWORD 'readonly';
```

### View Roles
```
postgres=# \du
```
NOTE: by default, only creator of the database and superuser has access to the database objects

### Create Role
```
postgres=# CREATE ROLE employee_read;
postgres=# GRANT SELECT ON ALL TABLES IN SCHEMA public TO employee_read;
```

### Create User
```
postgres=# CREATE USER test_user WITH LOGIN ENCRYPTED PASSWORD 'password';
```

### Add Role
```
postgres=# GRANT employee_read TO test_user;
```

Note: You can also create a role within the CLI without being logged into postgres.
```
createuser --interactive
```
However, if you want to add a password to this role, you'll need to log into postgres and run:
```
postgres=# ALTER ROLE interactive_user_name WITH ENCRYPTED PASSWORD 'password';
```

Login method of postgres is set to 'Trust' by default. Modify the following files to enable password encryption.
- pg_hba.conf
- postgresql.conf

Find the path to these files by running the following commands:
For authentication connection methods...
```
postgres=# show hba_file;
/../pg_hba.conf
```
General configuration of PostgreSQL...
```
postgres=# show config_file;
/../postgresql.conf
```

### Granting Privileges

```
GRANT ALL PRIVILEGES ON <table> TO <user>;
GRANT ALL ON ALL TABLES [IN SCHEMA <schema>] TO <user>;
GRANT [SELECT, UPDATE, INSERT, ...] ON <table> [IN SCHEMA <schema>] TO <user>;
```

## Data Types

- Numeric Types
- Arrays
- Character Types
- Date/Time Types
- Boolean Types
- UUID Type

### Character Types

- char(N): fixed length with space padding
- varchar(N): variable length with no padding
- text: unlimited length text

### Numeric Types

- integers: smallint, int, bigint
- floating point: float4, float8, decimal

### Arrays

- text[]
- float4[]
- \<Data Type\>[]

### Custom Data Types

Custom types can also be created. The following is a possible example.

```
CREATE DOMAIN Rating SMALLINT 
  CHECK (VALUE > 0 AND VALUE <= 5);

CREATE TYPE Feedback AS (
  student_id UUID,
  rating SMALLINT,
  feedback TEXT
);
```

## Tables

```
CREATE TABLE my_table (
  my_table_id <TYPE> [CONSTRAINT],
  table_constraint [CONSTRAINT]
) [INHERITS <existing_table>];
```

## Backups

### Have a Plan

1. Backup Plan
2. Disaster Recovery Plan
3. Test Your Plan

Decide what to back up.

| **Type** | **Purpose** | **Frequency** |
| :--- | :------ | :-------: |
| **Full Backup** | backup all data | less often |
| **Incremental** | backup data that changed since the last incremental backup (or since the last full backup if an incremental hasn't been done yet) | often |
| **Differential** | backup data that changed since the last full backup | often |
| **Transactional Log** | backup of database transactions | very often |

What are the appropriate ways to back up? (e.g. hardware, software, data)

Decide how frequently to back up 

Decide where to store backups

Have a retention policy for backups

### Backing Up in Postgres

`pg_dump dbname > outfile`

Or use Valentina Studio, right click the database, and select 'Create Dump...' then select your options.

Also available is pgBackRest (a PostgreSQL Backup & Restore utility).

### Restoring A Database

In Valentina Studio, click 'File' and select 'Load Dump', then follow instructions.

## Transactions

`BEGIN;` To start a transaction.
Run write commands...
`ROLLBACK;` This will cancel the transaction.
`COMMIT;` or `END;` This will complete the transaction.

### ACID
Atomicity - execute transaction entirely or not at all
Consistency - transaction should leave the database in a consistent state (commit or rollback)
Isolation - executed in isolation from other transactions
Durability - changes in database persist after transaction