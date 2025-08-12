# PostgreSQL Cheatsheet

This guide is designed to provide a clear, concise overview of common PostgreSQL tasks and commands, suitable for
developers and DBAs transitioning from MySQL or other database systems.

## 1. Installation

```bash
brew install postgresql

# Restart PostgreSQL service after upgrade
brew services restart postgresql

# Start PostgreSQL manually without background service
/usr/local/opt/postgresql/bin/postgres -D /usr/local/var/postgres

# If PostgreSQL fails to start, remove stale PID file
rm /usr/local/var/postgres/postmaster.pid

# Stop PostgreSQL service
brew services stop postgresql
```

---

## 2. Basic PostgreSQL User & Database Management

Connect to the default `postgres` database:

```bash
psql postgres
```

### 2.1 User Management

```postgresql
-- Create a new user with encrypted password
CREATE USER username WITH ENCRYPTED PASSWORD 'password';

-- Alter user to have superuser privileges
ALTER USER username WITH SUPERUSER;

-- Allow user to create databases
ALTER USER username CREATEDB;
```

### 2.2 Database Management

```postgresql
-- Create a new database owned by a specific user
CREATE DATABASE dbname WITH OWNER username;

-- Change the owner of a database
ALTER DATABASE dbname OWNER TO username;

-- Grant all privileges on a database to a user
GRANT ALL PRIVILEGES ON DATABASE dbname TO username;
```

---

## 3. Running SQL Scripts

Execute a SQL file from command line:

```bash
psql -U username -d dbname -f path/to/script.sql
```

---

## 4. Managing Sequences (Auto-Increment IDs)

```postgresql
-- Set sequence value manually
SELECT setval('sequence_name', 100000);

-- Get next sequence value
SELECT nextval(pg_get_serial_sequence('table_name', 'column_name')) AS next_id;

-- Get current sequence value
SELECT currval(pg_get_serial_sequence('table_name', 'column_name')) AS current_id;

-- Check last value from a sequence
SELECT last_value
FROM sequence_name;
```

---

## 5. Monitoring & Managing Connections

```postgresql
-- View active connections to a specific database
SELECT *
FROM pg_stat_activity
WHERE datname = 'your_database';

-- Cancel a running query by PID
SELECT pg_cancel_backend(pid);

-- Terminate a connection forcefully by PID
SELECT pg_terminate_backend(pid);
```

Use system commands to find PostgreSQL processes:

```bash
ps -ef | grep postgres
sudo kill -9 <PID>
```

---

## 6. PostgreSQL System Catalog and Information Queries

### Viewing Roles and Users

```postgresql
SELECT *
FROM pg_catalog.pg_user;

SELECT *
FROM pg_catalog.pg_roles;

SELECT DISTINCT rolname
FROM pg_catalog.pg_roles;
```

### Viewing Databases, Tables, and Groups

```postgresql
SELECT *
FROM pg_catalog.pg_database;

SELECT *
FROM pg_catalog.pg_tables;

SELECT *
FROM pg_catalog.pg_group;

SELECT *
FROM pg_catalog.pg_namespace;

SELECT *
FROM pg_catalog.pg_default_acl;
```

### Using information_schema for Privileges

```postgresql
SELECT *
FROM information_schema.role_table_grants;

SELECT *
FROM information_schema.role_table_grants
WHERE grantee LIKE '%psqladmin%';
```

### Showing Server Settings and Extensions

```postgresql
SHOW wal_level; -- Should be 'logical' for logical replication
SHOW max_worker_processes; -- Typically 16
SHOW azure.extensions;
SHOW session_replication_role;
```

---

## 7. Role & Privilege Management

```postgresql
-- Grant default privileges on new tables in a schema to a role
ALTER DEFAULT PRIVILEGES IN SCHEMA public
    GRANT SELECT ON TABLES TO role_name;

-- Revoke default privileges on new tables
ALTER DEFAULT PRIVILEGES IN SCHEMA public
    REVOKE SELECT ON TABLES FROM role_name;

-- Example granting multiple privileges
ALTER DEFAULT PRIVILEGES IN SCHEMA public
    GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO role_name;
```

---

## 8. Performance & Maintenance

```postgresql
-- Analyze query plan and execution time
EXPLAIN ANALYZE
SELECT *
FROM your_table;

-- Perform vacuum full to reclaim storage
VACUUM FULL your_table;
```

---

## 9. Useful SQL Snippets

```postgresql
-- Generate a random number within a range
SELECT floor(random() * (max_value - min_value + 1) + min_value);

-- Select random rows from a table
SELECT *
FROM your_table
ORDER BY random()
LIMIT 100;

-- Use SET LOCAL within a transaction
BEGIN;
SET LOCAL some.custom_variable = 'value';
-- your SQL commands here
COMMIT;
```

---

## Helpful Resources

- [PostgreSQL Official Documentation](https://www.postgresql.org/docs/current/)
- [StackOverflow: Run PostgreSQL SQL file via CLI](https://stackoverflow.com/questions/9736085/run-a-postgresql-sql-file-using-command-line-arguments)
- [Managing Roles & Permissions](https://www.postgresql.org/docs/current/sql-grant.html)
- [PostgreSQL ALTER DEFAULT PRIVILEGES](https://www.postgresql.org/docs/current/sql-alterdefaultprivileges.html)