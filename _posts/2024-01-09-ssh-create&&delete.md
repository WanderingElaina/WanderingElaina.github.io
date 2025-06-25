---
layout: post_layout
title: ssh-create&&delete
time: 2024年01月09日 星期二
location: beijing
pulished: true
excerpt_separator: "##"
---
### 远程服务器端口

```ssh user@124.***.***.*** -p 29```
输入密码

```sudo docker ps -a```
查看所有容器


```sudo docker run --shm-size 1g --runtime=nvidia --gpus all -itd --privileged --gpus all -p 7905:22 --security-opt seccomp=unconfined -e NVIDIA_DRIVER_CAPABILITIES=all -v /mnt/sda/data/zz:/data -v /mnt/sda/data/public:/public --name zz continuumio/miniconda3 /bin/bash```


设置容器为zz，并将宿主机的/mnt/sda/data/zz挂载到容器的data上，容器的7905映射到宿主机的22端口

```sudo docker exec -it zz /bin/bash```
进入容器zz

```
adduser <username>
```
创建用户

`whoia`查看当前用户名

 ```apt-get update```
 
使用apt-get包管理器更新软件包列表

```apt-get install vim openssh-server nano```

安装vim openssh-server nano

点击y

`passwd`

修改用户密码

``` vi /etc/ssh/sshd_config```

进去之后，点击`i`在当前光标位置可以进行选择，然后修改，修改之后，需要双击或者单机esc键，进入命令行模式，输入```:wq```,
然后回车（enter）便可以退出保存。将里面的PermitRootLogin和PasswordAuthentication那行修改为```PermitRootLogin yes```
和```PasswordAuthentication yes```

```service ssh restart```

重启ssh服务

exit 退出，

logout 退出，

断开与服务器连接 

##### 若是创建用户user

```ssh username@124.***.***.*** -p 7905```
远程，然后输入设置的passwd

##### 若是没有创建用户user

```ssh root@124.***.***.*** -p 7905```
远程，然后输入设置的passwd

上一行代码可能会报错，需要去自己的.ssh文件夹，将host删除。
### 删除delete

```ssh user@124.***.***.*** -p 29```
输入密码

```sudo docker ps -a```
查看所有容器

选择要删除的容器名字

首先关闭容器zz ```sudo docker stop zz ```

然后移除容器zz ```sudo docker rm zz ```
