---
layout: post_layout
title: ubuntu apt-get nginx
time: 2024年01月24日 星期三
location: beijing
pulished: true
excerpt_separator: "##"
---
#### 注：以下命令都是在root用户下使用
##### 检查是否存在apt
```
apt –version
```
若出现版本号，则存在，否则需要安装apt
```
sudo apt update
sudo apt install apt
```
##### 更新apt命令
```
apt update
```
##### 安装nginx
```
apt-get install nginx
```
##### 查看nginx版本
```
nginx -v
```
若出现nginx版本号，则为安装成功
##### 启动nginx
```
systemctl start nginx
```
##### 查看nginx的状态 
```
systemctl status nginx
```
若出现绿色的active（runing），则为成功
#####  安装nginx后的文件位置
/usr/sbin/nginx：主程序
/etc/nginx：存放配置文件
/usr/share/nginx：存放静态文件
/var/log/nginx：存放日志
