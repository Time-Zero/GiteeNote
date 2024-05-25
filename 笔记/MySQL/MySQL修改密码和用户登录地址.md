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
如果user密码为空，则使用下面这种方法来修改密码
