# PostgreSQL JSONB Operations

This document covers common PostgreSQL JSONB operations with practical examples. It serves as a reference for querying,
modifying, and manipulating JSONB data.

---

## Introduction to JSONB

JSONB is a binary JSON data type introduced in PostgreSQL 9.4 that stores JSON data in a decomposed binary format,
enabling efficient access and manipulation.

Advantages of JSONB over JSON:

- Faster access and querying.
- Supports indexing.
- Allows modification of parts of JSON data.

---

## Basic JSONB Queries

Assuming a table `documents` with a JSONB column `data`.

```postgresql
-- Select all rows
SELECT *
FROM documents;

-- Select JSONB column
SELECT data
FROM documents;
```

---

## Accessing JSONB Elements

### Access with `->` and `->>` operators

- `->` returns JSON object/array
- `->>` returns text

```postgresql
-- Get JSON object field
SELECT data -> 'name'
FROM documents;

-- Get JSON object field as text
SELECT data ->> 'name'
FROM documents;
```

### Access nested JSON objects

```postgresql
SELECT data -> 'address' ->> 'city' AS city
FROM documents;
```

### Access JSON array elements by index

```postgresql
SELECT data -> 'items' ->> 0 AS first_item
FROM documents;
```

## JSONB Operators

| Operator | Description                         | Example                                        |
|----------|-------------------------------------|------------------------------------------------|
| `?`      | Does key exist?                     | `data ? 'name'`                                |
| `?|`     | Do any of these keys exist?         | `data ?| array['name','age']`                  |
| `?&`     | Do all of these keys exist?         | `data ?& array['name','age']`                  |
| `@>`     | Contains JSONB                      | `data @> '{"name":"John"}'`                    |
| `<@`     | Contained by JSONB                   | `'{"name":"John"}'::jsonb <@ data`              |

---

## JSONB Functions

### Expand JSON array elements: `jsonb_array_elements`

Expands a JSONB array to a set of JSONB values.

```postgresql
SELECT jsonb_array_elements(data -> 'items') AS item
FROM documents;
```

### Expand JSON object into key/value pairs: `jsonb_each`

Expands the outermost JSONB object into set of key/value pairs.

```postgresql
SELECT key, value
FROM jsonb_each(data);
```

### Get JSON object keys: `jsonb_object_keys`

Returns set of keys in JSONB object.

```postgresql
SELECT jsonb_object_keys(data)
FROM documents;
```

---

## Updating JSONB Data

### Set or update key/value with `jsonb_set`

```postgresql
UPDATE documents
SET data = jsonb_set(data, '{name}', '"Jane"')
WHERE id = 1;
```

### Remove key with - operator

```postgresql
UPDATE documents
SET data = data - 'age'
WHERE id = 1;
```

### Concatenate two JSONB objects with `||` (Merge JSONB objects)

```postgresql
UPDATE documents
SET data = data || '{"new_key": "new_value"}'
WHERE id = 1;
```

---

## Indexing JSONB

To speed up queries, create indexes on JSONB columns.

### GIN index for containment queries

```postgresql
CREATE INDEX idx_data_gin ON documents USING gin(data);
```

---

## Advanced JSONB Examples

**Example: Filter rows where JSONB contains a key with specific value:**

```postgresql
SELECT *
FROM documents
WHERE data @> '{"status": "active"}';
```

**Example: Extract all keys and values from JSONB object:**

```postgresql
SELECT key, value
FROM documents, jsonb_each(data);
```

**Example: Get all elements of a JSONB array:**

```postgresql
SELECT jsonb_array_elements(data -> 'tags') AS tag
FROM documents;
```

**Example: Update nested JSON key:**

```postgresql
UPDATE documents
SET data = jsonb_set(data, '{address,city}', '"New York"')
WHERE id = 2;
```

---

## Helpful Resources

This concludes the essential JSONB operations reference in PostgreSQL.
For further details, refer to the official PostgreSQL documentation on JSONB:

[https://www.postgresql.org/docs/current/functions-json.html](https://www.postgresql.org/docs/current/functions-json.html)
[https://www.postgresql.org/docs/current/datatype-json.html](https://www.postgresql.org/docs/current/datatype-json.html)
