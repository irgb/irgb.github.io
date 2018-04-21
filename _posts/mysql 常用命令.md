### 编码
修改编码: `alter database mydb character set utf-8;`
查看数据库的默认编码： `show variables like 'collation_%';` 和 `show variables like 'character\_set\_%';` 或者 `status`
windows上修改默认字符集:
把文件`my-default.ini` 拷贝为 `my.ini` 并添加以下选项
```
default-character-set=utf8
default-storage-engine=INNODB
[client]
default-character-set=utf8
```

### 创建数据库
```sql
CREATE DATABASE dbname CHARACTER SET utf8;
```

### 操作表
查看表的模式: `describe tablename;` 或 `desc tablename;`
创建表:
```sql
CREATE TABLE usertable(USER_ID VARCHAR (255) PRIMARY KEY,
FIELD1 TEXT, FIELD2 TEXT);
```
修改表的模式：
```sql
ALTER TABLE table_name MODIFY column_name VARCHAR(255);
```
删除表:
```sql
drop table usertable;
DROP TABLE IF EXISTS usertable;
```
清空表: 
```sql
TRUNCATE TABLE usertable;
```
### 用户与权限
创建新用户:
```sql
insert into mysql.user(Host,User,Password) values('localhost','username',password('1234'));
flush privileges;
```
查看所有用户：
```sql
select user,host,password from user;
```
修改密码:
```sql
UPDATE user SET password=PASSWORD('123456') WHERE user='root';
```
用户授权:
```sql
GRANT ALL privileges ON databasename.* TO username@"%" IDENTIFIED BY "password";
GRANT ALL privileges ON *.* TO root@"%" IDENTIFIED BY "password" WITH GRANT OPTION;
GRANT ALL privileges ON *.* TO root@"localhost" IDENTIFIED BY "password" WITH GRANT OPTION;
GRANT ALL privileges ON *.* TO root@"127.0.0.1" IDENTIFIED BY "password" WITH GRANT OPTION;
```
重新加载权限：
```sql
flush privileges;
```
允许远程访问:
```
注释掉本机绑定, 修改/etc/mysql/my.cnf，把其中的 bind-address = 127.0.0.1 注释掉
```
  
### 数据存储
查看数据文件存放位置: `show variables like 'datadir'`
[数据备份与导出](http://stackoverflow.com/questions/6682916/how-to-take-backup-of-a-single-table-in-the-mysql-database)：
`mysqldump -u username -h host -p db_name table_name > table_name.sql`
数据恢复：
`mysql -u username -p db_name < table_name.sql`
数据导入：`mysqlimport -uroot -p123123 -h127.0.0.1 -P3306 db_name ~/usertable.txt`
