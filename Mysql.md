**[Mysq常用命令]**  
1.创建db
```
CREATE DATABASE IF NOT EXISTS <db_name>
DEFAULT CHARACTER SET utf8
DEFAULT COLLATE utf8_general_ci;
```
<br></br>

2.忘记数据库账号密码  
```
1）stop mysqls erver
2) mysqld --skip-grant-tables
3) mysql 连接server
4) use mysql; select user,host,authentication_string from user where user='root';
5) update user set authentication_string=<new_password> where user='root' and host='localhost';
6) restart mysql server 
```
<br></br>

3.给特定主机/账号加权限
```
grant all privileges on 库名.表名 to 用户名@主机 identified by "密码";
flush privileges;
```
