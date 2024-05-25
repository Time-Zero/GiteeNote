# 一、修改配置文件
修改mysqld.conf文件，让其不绑定127.0.0.1端口
```bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.conf
```
注释掉里面的
```
bind-server=127.0.0.1
```

# 二、修改账号的登录验证
## 切换到mysql表
```sql
use mysql;
```


## 查看user密码
 ```sql
 select host,user,authentication_string from user;
```
查看要设置的用户*authentication_string*字段是否为空

* 为空,这样设置
```sql
alter user 'root'@'localhost' identified with 
```
