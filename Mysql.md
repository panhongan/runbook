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