# MYSQL8 Docker Container (Support Replication)

Run all container DB
```shell
docker-compose up -d --build
```

Setup replication
```shell
# In master
CREATE USER 'repl'@'%' IDENTIFIED WITH 'mysql_native_password' BY '12345678';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
FLUSH PRIVILEGES;

SHOW MASTER STATUS;
```

```shell
# In slave
CHANGE MASTER TO
  MASTER_HOST='auth_db',
  MASTER_USER='repl',
  MASTER_PASSWORD='12345678',
  MASTER_LOG_FILE='mysql-bin.000003', # from SHOW MASTER STATUS
  MASTER_LOG_POS=827; # from SHOW MASTER STATUS

STOP SLAVE;
RESET SLAVE;
START SLAVE;

SHOW SLAVE STATUS;
```