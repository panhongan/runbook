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

4.修改数据库字符集
```
ALTER DATABASE <db_name> DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

**[ONLY_FULL_GROUP_BY]** 
MySQL 5.7.5及以上会检测该项。

修改并重启mysqld
```
[mysqld]
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

或者admin权限：
```
set @@global.sql_mode=’STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION’;
```
