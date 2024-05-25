# 一、修改配置文件
修改mysqld.conf文件，让其不绑定127.0.0.1端口
```bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.conf
```
注释掉里面的
```
bind-server=127.0.0.1
```
否则远程登录一直报`conection refuse`


**修改完之后记得重启mysql服务**
```bash
sudo systemctl 
```



# 二、修改账号的登录验证
## 切换到mysql表
```sql
use mysql;
```


## 设置用户密码
```sql
alter user 'root'@'localhost' identified with mysql_native_password by '334859';
```
如果设置弱密码就加`with mysql_native_password`

# 三、允许账号远程登录
除了修改mysql的绑定ip，还需要设置用户，让用户账号能够远程登录
```sql
   update user set host = '%' where user = 'root';

```

这里是通过修改user表项来允许用户远程登录，这样不会对用户的权限有影响

