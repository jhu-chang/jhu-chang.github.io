---
layout: post
title: "SQL user managerment"
category: "Database"
tags: [sql,users]
---

### Create User

- Oracle

  ```sql
  CREATE USER wildfly IDENTIFIED BY wildfly;
  GRANT dba TO wildfly WITH ADMIN OPTION;
  ```

- MySQL

  ```sql
  CREATE USER 'newuser'@'%' IDENTIFIED BY 'password';
  GRANT ALL PRIVILEGES ON *.* TO 'newuser'@'%';
  ```

- PostgreSQL

  ```sql
  CREATE USER newuser WITH PASSWORD 'password';
  GRANT ALL PRIVILEGES ON DATABASE my_newly_created_db_name TO my_user_name;
  ```

- SqlServer

  ```sql
  USE master
  GO
  CREATE LOGIN [username] WITH PASSWORD = 'username@1234',
               DEFAULT_DATABASE = [master],
               CHECK_EXPIRATION=OFF,
               CHECK_POLICY=OFF
  GO
  ```

### Drop User

- Oracle

  ```sql
  DROP USER wildfly CASCADE;
  ```

- MySQL

  ```sql
  DROP USER IF EXISTS wildfly
  ```

  Note: `IF EXISTS` can be used after MySQL 5.7.8

- PostgreSQL

  ```sql
  DROP OWNED BY wildfly CASCADE;
  DROP USER wildfly
  ```

- SqlServer

  ```sql
  USE master
  GO
  CREATE LOGIN [username] WITH PASSWORD = 'username@1234',
               DEFAULT_DATABASE = [master],
               CHECK_EXPIRATION=OFF,
               CHECK_POLICY=OFF
  GO
  ```