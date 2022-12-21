# MariaDB-Master-Slave-Replication-on-CentOS-8

```sh
master server - 192.168.100.168
slave  server - 192.168.100.169

Master server
Terminal:~$  sudo install mariadb
Terminal:~$  sudo firewalld-cmd --add-port=3306/tcp --zone=public --permanent
Terminal:~$  sudo firewalld-cmd --reload
Terminal:~$  sudo mysql_secure_installation
Terminal:~$  vi /etc/my.cnf
** add following lines
[mysqld]
bind-address=192.168.100.168
server-id=1
log_bin=mysql-bin
binlog-format=ROW
Terminal:~$  systemctl restart mariadb
Terminal:~$  mysql -u root -p
MariaDB [(none)]> GRANT REPLICATION SLAVE ON *.* TO 'slave_username' @'slave_ip_address' IDENTIFIED BY 'Password';
MariaDB [(none)]> FLUSH PRIVILEGES ;
MariaDB [(none)]> FLUSH TABLES WITH READ LOCK;
MariaDB [(none)]> EXIT;
MariaDB [(NONE)]> SHOW MASTER STATUS\G (get the MASTER_LOG_FILE and MASTER_LOG_POS)
Terminal:~$  sudo mysqldump --all-databases -u root -p > masterdatabase.sql
MariaDB [(NONE)]> UNLOCK TABLES;
MariaDB [(NONE)]> EXIT;
Terminal:~$  scp masterdatabase.sql slave_username@slave_ip_address:/root/


Slave server 
Terminal:~$  sudo install mariadb
Terminal:~$  sudo firewalld-cmd --add-port=3306/tcp --zone=public --permanent
Terminal:~$  sudo firewalld-cmd --reload
Terminal:~$  sudo mysql_secure_installation
Terminal:~$  vi /etc/my.cnf
** add following lines
[mysqld]
bind-address=192.168.100.169
server-id=1
log_bin=mysql-bin
binlog-format=ROW
 Terminal:~$  systemctl restart mariadb
 Terminal:~$  mysql -u root -p < masterdatabase.sql
MariaDB [(NONE)]> STOP SLAVE ;
MariaDB [(NONE)]> CHANGE MASTER TO MASTER_HOST="192.168.100.168", MASTER_USER="slave_username",
MariaDB [(NONE)]> MASTER_PASSWORD="password", MASTER_LOG_FILE="master1.000001" , MASTER_LOG_POS=2648;
MariaDB [(NONE)]>  START SLAVE
MariaDB [(NONE)]> SHOW SLAVE STATUS;


Master server
MariaDB [(NONE)]> CREATE DATABASE test;
MariaDB [(NONE)]> CREATE TABLE test.case (id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id));
MariaDB [(NONE)]> INSERT INTO case (id) VALUES (999);
 

Slave server 
select * from test.case;


Slave server ** If sync got problem **
MariaDB [(NONE)]> STOP SLAVE;
MariaDB [(NONE)]> SET GLOBAL SQL_SLAVE_SKIP_COUNTER = 1;
MariaDB [(NONE)]> START SLAVE;
MariaDB [(NONE)]> SELECT SLEEP(5);
MariaDB [(NONE)]> SHOW SLAVE STATUS\G

https://dba.stackexchange.com/questions/10184/mysql-replication-error
https://linuxhint.com/mariadb_replication_rhel8/
```
