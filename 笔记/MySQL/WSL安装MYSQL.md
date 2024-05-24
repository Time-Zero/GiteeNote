# 一、启用systemd服务
## 编辑wsl.conf文件
```bash
sudo vim /etc/wsl.conf
```

## 添加内容
```
[boot]
systemd = true
```
