# MySQL Cheatsheet

This document contains useful commands, configurations, and troubleshooting tips for MySQL.

## 1. Installation

Install MySQL via Homebrew:

<details>
<summary>Click to expand</summary>

```bash
brew install mysql@8.0
echo 'export PATH="/usr/local/opt/mysql@8.0/bin:$PATH"' >> ~/.bash_profile
export PATH="/usr/local/opt/mysql@8.0/bin:$PATH"
source ~/.bash_profile
```

</details>

Check version:

```bash
$ mysql --version
mysql  Ver 8.0.43 for macos14.7 on x86_64 (Homebrew)
```

---

## 2. Uninstallation

Uninstall MySQL Completely:

<details>
<summary>Click to expand</summary>

```bash
brew uninstall mysql
brew uninstall mysql-client
brew uninstall --ignore-dependencies mysql-client
brew cleanup

sudo rm -rf /usr/local/var/mysql
sudo rm -rf /usr/local/mysql*
sudo rm -rf /usr/local/mysql-client*
sudo rm -rf /Library/StartupItems/MySQLCOM
sudo rm -rf /Library/PreferencePanes/My*
sudo rm -rf /Library/Receipts/mysql*
sudo rm -rf /Library/Receipts/MySQL*
```

</details>

Reference: [Remove MySQL completely](https://gist.github.com/vitorbritto/0555879fe4414d18569d)

---

## 3. Common MySQL Commands

### Start / Stop Server

```bash
mysql.server start
mysql.server stop
```

### Login

```bash
mysql -u root -p
```

### Show MySQL Version

```mysql
SHOW VARIABLES LIKE "%version%";
```

---

## 4. Process Management

### Show Process List

```mysql
SHOW PROCESSLIST;
```

### Show Only Queries

```mysql
SELECT *
FROM INFORMATION_SCHEMA.PROCESSLIST
WHERE COMMAND = 'Query'
ORDER BY TIME DESC;
```

### Kill a Query (RDS Example)

```mysql
CALL mysql.rds_kill(query_id);
```

### Show InnoDB Transactions

```mysql
SELECT *
FROM INFORMATION_SCHEMA.INNODB_TRX;
```

### Show Active Processes (excluding sleep)

```mysql
SELECT *
FROM information_schema.processlist
WHERE command <> 'Sleep' AND (db IN ('example_db') OR db IS NULL);
```

---

## 5. Deadlock Info

```mysql
SHOW ENGINE INNODB STATUS;
```

---

## 6. Index Management

### Show Indexes

```mysql
SHOW INDEX FROM example_db.example_table;
```

### Index Usage Statistics

```mysql
SELECT INDEX_NAME, COUNT_STAR, COUNT_READ, COUNT_WRITE
FROM performance_schema.table_io_waits_summary_by_index_usage
WHERE OBJECT_SCHEMA = 'example_db' AND OBJECT_NAME = 'example_table'
ORDER BY COUNT_STAR;
```

### Cardinality & Size

```mysql
SELECT TABLE_NAME, INDEX_NAME, CARDINALITY, INDEX_TYPE
FROM information_schema.STATISTICS
WHERE TABLE_SCHEMA = 'example_db' AND TABLE_NAME = 'example_table';
```

### Find Unused Indexes

```mysql
SELECT *
FROM sys.schema_unused_indexes
WHERE object_schema = 'example_db' AND object_name = 'example_table';
```

### Find Redundant Indexes

```mysql
SELECT *
FROM sys.schema_redundant_indexes
WHERE table_schema = 'example_db' AND table_name = 'example_table';
```

### Check Index Statistics

```mysql
SELECT *
FROM sys.schema_index_statistics
WHERE table_schema = 'example_db' AND table_name = 'example_table';
```

---

## 7. Slow Query Log

### Enable & Configure Slow Query Log

`my.cnf` sample:

```ini
# Log queries not using indexes
log_queries_not_using_indexes = 0

# Enable slow query log
slow_query_log = 1

# Threshold for slow queries (seconds)
long_query_time = 3

# File to store slow queries
slow-query-log-file = /var/lib/mysql/logs/slow.log
```

### View Slow Queries

```mysql
-- Show slow log entries for a specific DB
SELECT *
FROM mysql.slow_log
WHERE db = 'example_db'
ORDER BY start_time DESC;
```

---

## 8. File Upload

```mysql
LOAD DATA LOCAL INFILE '/path/to/file.csv'
    INTO TABLE example_schema.example_table
    FIELDS TERMINATED BY ';'
    OPTIONALLY ENCLOSED BY '"'
    LINES TERMINATED BY '\n'
    IGNORE 1 LINES;

SHOW WARNINGS;
```

---

## 9. Time Zone

### Check Current Time Zone

```mysql
SELECT @@time_zone;
```

### Example Connection String with Time Zone

```
# Asia/Baghdad
jdbc:mysql://example_host:3306/example_db?useSSL=false&serverTimezone=Asia/Baghdad

# GMT+3
jdbc:mysql://example_host:3306/example_db?useSSL=false&serverTimezone=GMT%2B3
```

---

## 10. Password Management

### Change User Password

```
mysql -u root

ALTER USER 'example_user'@'localhost' IDENTIFIED BY 'new_password';
ALTER USER 'root'@'localhost' IDENTIFIED BY '';

FLUSH PRIVILEGES;

mysql -u root -h localhost -p
mysql.server restart
```

Reference: [Change MySQL Password](https://www.cyberciti.biz/faq/mysql-change-user-password/)

### Reset Root Password

```mysql
UPDATE mysql.user
SET authentication_string=NULL
WHERE User = 'root';
FLUSH PRIVILEGES;

ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'new_password';
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new_password';
```

Reference: [Password Reset Gist](https://gist.github.com/zubaer-ahammed/c81c9a0e37adc1cb9a6cdc61c4190f52)

---

## 11. Table Operations

### Atomic Rename

```mysql
RENAME TABLE old_table TO old_table_backup, new_table TO old_table;
```

### Partitioning Example

```mysql
ALTER TABLE example_table
REORGANIZE PARTITION p_max INTO (
    PARTITION p_20201023 VALUES LESS THAN ('2020-10-24'),
    PARTITION p_20201024 VALUES LESS THAN ('2020-10-25'),
    PARTITION p_max VALUES LESS THAN (MAXVALUE)
    );
```

---

## 12. Random Row

```mysql
SELECT *
FROM example_table
ORDER BY RAND()
LIMIT 1;
```

---

## 13. Reset Auto Increment

```mysql
ALTER TABLE example_table
AUTO_INCREMENT = 1;
```

