**[Mysql Community Server 8(Master-Slave)搭建]**
prepare
apt-get install selinux-utils
setenforce 0
set SELINUX=disabled in /etc/selinux/config
Master & Slave Setup
cd /var/cache/apt/archives
wget https://dev.mysql.com/get/mysql-apt-config_0.8.17-1_all.deb
sudo dpkg -i ./mysql-apt-config_0.8.17-1_all.deb
sudo apt-get update
sudo apt-get install mysql-server
Master Server Config
(/etc/mysql/mysql.conf.d/mysqld.cnf)

[mysqld]
port            = 3306
server-id       = 1
log-bin         = master-bin
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
datadir		= /conviva/var/lib/mysql
log-error	= /var/log/mysql/error.log
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci
Slave Server Config
(/etc/mysql/mysql.conf.d/mysqld.cnf)

[mysqld]
port            = 3306
server-id       = 2
relay-log       = relay-log-bin
read-only       = 1
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /conviva/var/lib/mysql
log-error       = /var/log/mysql/error.log
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci
Mysql Client Config
(/etc/mysql/conf.d/mysql.cnf)

[mysql]
port = 3306
default-character-set = utf8mb4
Other Server Config
if datadir is not the defualt directory /var/lib/mysql , need to change file /etc/apparmor.d/usr.sbin.mysqld
 
Add following lines:
#/var/lib/mysql
/conviva/var/lib/mysql/ r,
/conviva/var/lib/mysql/** rwk,
Start Mysql Server
mkdir -p /conviva/var/lib/mysql (optional, if datadir is user specified)
chown mysql:mysql /conviva/var/lib/mysql 
chmod 755 /conviva/var/lib/mysql 
service apparmor restart
mysqld --defaults-file=/etc/mysql/mysql.cnf  --initialize --user=mysql
service mysql start
 
#reset password for root
root / <see from err log file>
alter user user() IDENTIFIED BY '<new password>';
 
Replica Config
Master
ADD slave user for all slave nodes:
drop user slave;

CREATE USER 'slave'@'<salve_host1>' IDENTIFIED BY 'slave';
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'<slave_host1>';
# the same for other slave nods...

FLUSH PRIVILEGES;
Slave
Change master
change master to master_host='<master host>',master_user='slave',master_password='slave',get_master_public_key=1;
start slave;

check by:
show slave status \G
check item: 
     Slave_SQL_Running_State
     Exec_Master_Log_Pos: 1286
Switch Master/Slave
Check
slave node : Exec_Master_Log_Pos: 1231
master node: show master status;
 
2. Slave -> Master
mysql -u root -p
stop slave;
reset master;
reset slave all;
set global read_only=0;
 
# add slave user for replica
drop user slave;
 
CREATE USER 'slave'@'<slave_host1>' IDENTIFIED BY 'slave';
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON . TO 'slave'@'<slave_host1';
# the same for other slave nodes

FLUSH PRIVILEGES;
 
3. Master -> Slave
mysql -u root -p
reset master;
reset slave all;
set global read_only=1;
 
# add slave replica task
change master to master_host='<new master>',master_user='slave',master_password='slave',get_master_public_key=1;
start slave;

**[Mysql常用命令]**  
1.创建db
```
CREATE DATABASE IF NOT EXISTS <db_name> DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

2.忘记数据库账号密码  
```
1）stop mysqls erver
2) mysqld --skip-grant-tables
3) mysql 连接server
4) use mysql; select user,host,authentication_string from user where user='root';
5) update user set authentication_string=<new_password> where user='root' and host='localhost';
6) restart mysql server 
```

3.给特定主机/账号加权限
```
grant all privileges on 库名.表名 to 用户名@主机 identified by "密码";
flush privileges;
```
