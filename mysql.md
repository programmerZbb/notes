# 1. 问题解决

## 1.ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES/NO)

1. mysql.server stop  停止服务
2. mysqld_safe --user=mysql --skip-grant-tables --skip-networking &
3. mysql -u root mysql  使用root登录mysql数据库
4. update user set password=password('你的密码') where user='root'
5. mysql> FLUSH PRIVILEGES;   刷新权限
6. mysql> quit
7. mysql.server start 



## 8.0 以上

1. **mysql -uroot -p**
2. **use mysql;**
3. **update user set authentication_string='' where user='root';**
4. **alter user 'root'@'localhost' identified by 'newpassword';**

