#MySql常用操作 

## MySql8.0修改密码


```
use mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new password';  
FLUSH PRIVILEGES; 
```

## 数据库

* 查看数据库

`SHOW DATABASES;`

* 创建数据库

 `CREATE DATABASE 数据库名字;`
 
* 删除数据库

`DROP DATABASE 数据库名字`

## 表
 
 * 建立表

 * 删除数据表

 `DROP TABLE 表名`
 
 

