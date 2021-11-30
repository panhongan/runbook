**[Mysql Community Server 8(Master-Slave)搭建]**  
1.Prepare
```
apt-get install selinux-utils
setenforce 0
set SELINUX=disabled in /etc/selinux/config
```

2.Master & Slave Setup
```
cd /var/cache/apt/archives
wget https://dev.mysql.com/get/mysql-apt-config_0.8.17-1_all.deb
sudo dpkg -i ./mysql-apt-config_0.8.17-1_all.deb
sudo apt-get update
sudo apt-get install mysql-server
```

3.Master Server Config  
(/etc/mysql/mysql.conf.d/mysqld.cnf)
```
[mysqld]
port            = 3306
server-id       = 1
log-bin         = master-bin
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
datadir		= /var/lib/mysql1
log-error	= /var/log/mysql/error.log
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci
```

4.Slave Server Config  
(/etc/mysql/mysql.conf.d/mysqld.cnf)

```
[mysqld]
port            = 3306
server-id       = 2
relay-log       = relay-log-bin
read-only       = 1
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql1
log-error       = /var/log/mysql/error.log
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci
```

5.Mysql Client Config  
(/etc/mysql/conf.d/mysql.cnf)

```
[mysql]
port = 3306
default-character-set = utf8mb4
```

6.Other Server Config  
if datadir is not the defualt directory /var/lib/mysql , need to change file /etc/apparmor.d/usr.sbin.mysqld  
Add following lines:
```
#/var/lib/mysql
/var/lib/mysql1/ r,
/var/lib/mysql1/** rwk,
```

7.Start Mysql Server  
```
mkdir -p /var/lib/mysql1 (optional, if datadir is user specified)
chown mysql:mysql /var/lib/mysql1 
chmod 755 /var/lib/mysql 1
service apparmor restart
mysqld --defaults-file=/etc/mysql/mysql.cnf  --initialize --user=mysql
service mysql start
```
 
#reset password for root
Find out password from log file.  
Run `mysql`, type:  
```
root / <original password>
alter user user() IDENTIFIED BY '<new password>';
```
 
**[Replica Config]**  
1.Master Node  
ADD slave user for all slave nodes:

```
drop user slave;
CREATE USER 'slave'@'<salve_host1>' IDENTIFIED BY 'slave';
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'<slave_host1>';
FLUSH PRIVILEGES;
```

2.Slave Node  
Change master  
```
change master to master_host='<master host>',master_user='slave',master_password='slave',get_master_public_key=1;
start slave;
```

check by:
```
show slave status \G
```

check item: 
     Slave_SQL_Running_State
     Exec_Master_Log_Pos: 1286
     
**[Switch Master/Slave]**  
1.Check  
slave node : Exec_Master_Log_Pos: 1231  
master node: show master status;  
 
2.Slave -> Master  
```
mysql -u root -p
stop slave;
reset master;
reset slave all;
set global read_only=0;
```
 
add slave user for replica node  
the same for other slave nodes
```
drop user slave;
CREATE USER 'slave'@'<slave_host1>' IDENTIFIED BY 'slave';
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON . TO 'slave'@'<slave_host1';
FLUSH PRIVILEGES;
```
 
3.Master -> Slave  
```
mysql -u root -p
reset master;
reset slave all;
set global read_only=1;
 
# add slave replica task
change master to master_host='<new master>',master_user='slave',master_password='slave',get_master_public_key=1;
start slave;
```
