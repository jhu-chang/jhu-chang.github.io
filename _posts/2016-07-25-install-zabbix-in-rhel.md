---
layout: post
title: "Install Zabbix in RHEL"
category: "Monitor"
tags: [zabbix,monitor,redhat]
---

1. Install postgresql (version > 8.2)
  - Install
   
    ```
    yum install postgresql-server
    service postgresql initdb  // init the db
    chkconfig postgresql on  // auto start the postgresql
    service postgresql start
    ```

  - Config

    Edit the `/var/lib/pgsql/data/pg_hda.conf` to add remote password login

    ```
    LOCAL   ALL         POSTGRES                          MD5
    # "LOCAL" IS FOR UNIX DOMAIN SOCKET CONNECTIONS ONLY
    # LOCAL   ALL         ALL                               IDENT
    # IPV4 LOCAL CONNECTIONS:
    HOST    ALL         ALL         127.0.0.1/32         MD5
    # IPV6 LOCAL CONNECTIONS:
    HOST    ALL         ALL         ::1/128               MD5
    HOST    ALL         ALL          192.168.0.0/8         MD5
    HOST    ALL         ALL          10.0.0.0/8            MD5
    ```

  - Change the postsql username and pwd

    ```
    su – postgres
    psql
    ```

    Then execute following command

    ```
    ALTER USER postgres WITH PASSWORD 'postgres';
    select * from pg_shadow;
    create database david;
    ```

  - Yum repository for postgresql

    - RHEL 5
    
      ```
      rpm -ivh http://yum.postgresql.org/9.3/redhat/rhel-5-x86_64/pgdg-sl93-9.3-1.noarch.rpm
      ```

    - RHEL 6

      ```
      rpm -ivh http://yum.postgresql.org/9.3/redhat/rhel-6-x86_64/pgdg-redhat93-9.3-1.noarch.rpm
      ```

    - Default install path: `/usr/pgsql-9.3/`
    

2. Install/Configuration Zabbix
  
  - Install 

    ```
    rpm -ivh http://repo.zabbix.com/zabbix/2.2/rhel/6/x86_64/zabbix-release-2.2-1.el6.noarch.rpm
    rpm -ivh  http://repo.zabbix.com/zabbix/2.2/rhel/5/x86_64/zabbix-release-2.2-1.el5.noarch.rpm
    yum install zabbix-server-pgsql zabbix-web-pgsql zabbix-proxy-sqlite3 zabbix-java-gateway zabbix-agent
    ```

  - Configuration

    - Time zone
      
      Edit `/etc/httpd/conf.d/zabbix.conf`, change **date.timezone Asia/Shanghai**

    - Login in
      
      Edit `/etc/zabbix/zabbix_server.conf`, change DB user and pwd to **postgres**

    - Create table

      ```
      psql -U postgres
      ```

      Execute the following commands:

      ```
      create database zabbix;
      \q
      ```

      Then execute following shell script:

      ```
      cd /usr/share/doc/zabbix-server-pgsql-2.2.5/create
      psql -U postgres zabbix < schema.sql
      psql -U postgres zabbix < images.sql
      psql -U postgres zabbix < data.sql
      ```

  - Restart the zabbix and postgres
  
    ```
    service postgresql restart
    service zabbix-server restart
    service httpd restart 
    ``` 

2. Sample Zabbix conf: `/etc/zabbix/web/zabbix.conf.php`

  ```
  <?php
   // Zabbix GUI configuration file
   global $DB;

   $DB['TYPE']     = 'POSTGRESQL';
   $DB['SERVER']   = 'localhost';
   $DB['PORT']     = '0';
   $DB['DATABASE'] = 'zabbix';
   $DB['USER']     = 'postgres';
   $DB['PASSWORD'] = 'postgres';
   
   // SCHEMA is relevant only for IBM_DB2 database
   $DB['SCHEMA'] = '';
   
   $ZBX_SERVER      = 'localhost';
   $ZBX_SERVER_PORT = '10051';
   $ZBX_SERVER_NAME = 'MyLinux64';
   
   $IMAGE_FORMAT_DEFAULT = IMAGE_FORMAT_PNG;
  ?>
  ```

3. You may need to disable Selinux or Allow zabbix ports

4. The zabbix also can use SQLite

  ```
  mkdir /var/lib/sqlite
  sqlite3 /var/lib/sqlite/zabbix.db < /usr/share/doc/zabbix-proxy-sqlite3-2.0.3/create/schema.sql 
  chown zabbix:zabbix /var/lib/sqlite/zabbix.db
  echo DBName=/var/lib/sqlite/zabbix.db >> /etc/zabbix/zabbix_proxy.conf
  echo DBName=/var/lib/sqlite/zabbix.db >> /etc/zabbix/zabbix_server.conf
  ``` 
