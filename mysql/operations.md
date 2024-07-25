**[Mysql常用命令]**  
1.创建db
```
CREATE DATABASE IF NOT EXISTS <db_name> DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```
2.修改数据库字符集
```
ALTER DATABASE <db_name> DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
**[忘记数据库账号密码]** 
**Mysql 5.7**
```
1）stop mysqls erver
2) ./mysqld_safe --skip-grant-tables &
3) 用root账号登录： mysql -u root -p (无密码）
4) 
  对于MySQL 5.7.6及以上版本，使用:
  FLUSH PRIVILEGES;
  ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';

  对于MySQL 5.7.5及以下版本，使用：
  FLUSH PRIVILEGES;
  SET PASSWORD FOR 'root'@'localhost' = PASSWORD('新密码');

  或者
  USE mysql;
  UPDATE user SET password=password("123") WHERE user="root";
  FLUSH PRIVILEGES;

5）退出并重启mysqld
```
**Mysql 8**
1）stop mysqls erver
2）./mysqld_safe --skip-grant-tables &
3）
  FLUSH PRIVILEGES; 
  ALTER USER 'root'@'localhost' IDENTIFIED BY '你的新密码';
4） 退出并重启mysqld


**【账号加权限】**
**Mysql 5.7**
```
CREATE USER root@'%' IDENTIFIED WITH mysql_native_password BY 'roottoor';
GRANT ALL ON *.* to 'root'@'%';
FLUSH PRIVILEGES;

或者：
GRANT ALL PRIVILEGES ON *.* TO '用户名'@'%' IDENTIFIED BY '密码' WITH GRANT OPTION
FLUSH PRIVILEGES;


修改密码：
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'new_password'
FLUSH PRIVILEGES;
```

**Mysql 8**
```
CREATE USER root@'%' IDENTIFIED WITH mysql_native_password BY 'roottoor';
GRANT ALL PRIVILEGES ON *.* TO root@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;

或者三步
CREATE USER root@'%' ;  -- 创建用户
ALTER  USER root@'%' IDENTIFIED BY '密';  -- 指定密码
GRANT ALL PRIVILEGES ON *.* TO u@'%' WITH GRANT OPTION;  -- 授权
FLUSH PRIVILEGES;
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
