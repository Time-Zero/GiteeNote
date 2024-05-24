# 一、启用systemd服务
## 编辑wsl.conf文件
```bash
sudo vim /etc/wsl.conf
```

## 添加内容
*要注意，千万不能修改错了*
```
[boot]
systemd = true
```
修改完之后保存

## 重启wsl
直接关闭那个虚拟环境即可

# 二、安装wsl
## 安装
```bash
sudo apt install mysql-server -y
```

## 查看mysql启动状态
```bash
sudo systemctl status mysql.service 
```
如果显示activing就说明没有问题了


