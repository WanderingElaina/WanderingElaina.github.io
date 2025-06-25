---
layout: post_layout
title: nginx-uninstall
time: 2024年01月24日 星期三
location: beijing
pulished: true
excerpt_separator: "##"
---
#### 卸载nginx安装方式
##### 先停止nginx命令
```
systemctl stop nginx
```
##### 通过apt-get命令卸载nginx
```
apt-get --purge autoremove nginx
```
##### 查看nginx的版本号
```
nginx -v
```

#### 删除Nginx：

要彻底删除 Ubuntu 上的 Nginx，你可以按照以下步骤进行操作：

1. 使用以下命令停止 Nginx 服务：
   ```
   sudo systemctl stop nginx
   ```

2. 然后，使用以下命令从系统启动中移除 Nginx 服务：
   ```
   sudo systemctl disable nginx
   ```

3. 接下来，使用以下命令来删除 Nginx 软件包及其配置文件：
   ```
   sudo apt purge nginx
   ```

4. 然后，使用以下命令来清除系统中的无用依赖项：
   ```
   sudo apt autoremove
   ```
5. 最后，使用以下命令找到nginx
```
find / -name nginx
```
```
rm ******
```
### 注释：还有可能一些后台nginx启动的，如：jar包，
这些步骤会将 Nginx 从 Ubuntu 系统中彻底删除，并清除相关的配置文件和依赖项。完成后，你的系统就不再包含 Nginx 服务器了。
