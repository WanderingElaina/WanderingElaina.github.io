---
layout: post_layout
title: ssh-gui-container
time: 2024年01月12日 星期五
location: beijing
pulished: true
excerpt_separator: "##"
--- 

### 远程服务器
```
ssh ps@124.***.***.*** -p 22
```
- 输入密码

```
sudo docker ps -a
```
若是数量多的话，删除，避免资源不够，访问不了要建立的网络地址进不去
### 创建gui容器
在这之前需要预编译，也就是提前写好dockerfile文件<br/>
然后用git指令将其传到服务器
##### 具体操作：
首先，在你的本地计算机上，你需要连接到远程服务器，可以使用类似于以下命令的 SSH 连接：

```
ssh username@your_server_ip
```

其中，"username" 是你在远程服务器上的用户名，"your_server_ip" 是远程服务器的 IP 地址。

接下来，你需要将你的 Dockerfile 和预编译文件添加到本地 Git 仓库中，并使用 Git 指令提交这些文件。

```
$ git init  # 初始化本地仓库
$ git add Dockerfile precompiled_files  # 将 Dockerfile 和预编译文件添加到 Git 仓库中
$ git commit -m "Add Dockerfile and precompiled files"  # 提交更改
```

然后，你需要将本地 Git 仓库推送到远程服务器上。要做到这一点，你可以使用 Git 的远程操作命令如 push、fetch 和 pull。

在此，我们使用 push 命令将本地 Git 仓库的内容推送到远程服务器上：

```
$ git remote add origin ssh://username@your_server_ip:/path/to/remote/repository
  # 添加远程仓库地址
$ git push -u origin master
  # 推送本地仓库的内容到远程服务器
```

这将把本地 Git 仓库的内容推送到远程 Git 仓库，然后你就可以使用 Docker 提供的构建命令将 Dockerfile 构建成一个镜像，如：

```
$ docker build -t my_image .
```

这将基于 Dockerfile 创建一个名为 "my_image" 的 Docker 镜像。

最后，你可以使用以下命令将 Docker 容器镜像从本地推送到 Docker 镜像仓库：

```
$ docker push my_image
```

这样做将使你的 Docker 容器镜像可供其他人使用和下载，但你需要授权其他人访问该镜像。

实际操作可能和具体操作不一致
由于本人的问题服务器具有dockerfile文件，因此直接开始create

```
sudo docker run --shm-size 32g -itd --runtime=nvidia --gpus all --privileged -p <web_port>:6901 -p <ssh_port>:22 --security-opt seccomp=unconfined -e VNC_PW=<password> -e NVIDIA_DRIVER_CAPABILITIES=all -v /mnt/sda/data/<name>:/data -v /mnt/sda/data/public:/public --name <name>  workspace_deluxe:1_13_0
```

这是一个Docker命令，用于创建并启动一个新的Docker容器。具体含义如下：

- `--shm-size 32g`：设置共享内存的大小为32GB。
- `--runtime=nvidia`：指定使用NVIDIA运行时环境。
- `--gpus all`：将所有的GPU设备分配给容器。
- `--privileged`：赋予容器特权级别权限。
- `-p <web_port>:6901 -p <ssh_port>:22`：将容器内部的端口6901映射到主机的<web_port>端口，端口22映射到主机的<ssh_port>端口，这样就可以通过Web方式或者SSH方式访问容器内部。
- `--security-opt seccomp=unconfined`：取消对容器中的系统调用的限制。
- `-e VNC_PW=<password> -e NVIDIA_DRIVER_CAPABILITIES=all`：设置环境变量VNC_PW和NVIDIA_DRIVER_CAPABILITIES，其中VNC_PW为VNC连接密码，NVIDIA_DRIVER_CAPABILITIES指定NVIDIA的驱动器能力，设置为all表示所有能力都启用。
- `-v /mnt/sda/data/<name>:/data -v /mnt/sda/data/public:/public`：将主机上的`/mnt/sda/data/<name>`目录挂载到容器内的`/data`目录，将主机上的`/mnt/sda/data/public`目录挂载到容器内的`/public`目录，这样就可以在容器内部访问主机上的数据。
- `--name <name>`：为容器指定名称为"<name>"。
- `workspace_deluxe:1_13_0`：使用名为"workspace_deluxe"且版本为"1_13_0"的镜像。

请注意，其中的 `<web_port>`、`<ssh_port>`、`<password>`、`<name>` 为需要替换为实际使用的值。此外，在使用此命令之前需要保证相应的镜像已经安装在本地Docker环境中，否则将会提示失败。
`<web_port>`:如10345
`<ssh_port>`:如7903
##### 进入容器安装ssh
```
sudo docker exec -it <name> /bin/bash
```
```
sudo apt-get update
```
```
sudo apt-get install vim openssh-server nano
```

```
sudo vi /etc/ssh/sshd_config
```
进去修改vim文本
点击`i`进入编辑
将以下两个

`# PermitRootLogin *******`
`# PasswordAuthentication yes`

修改为

***
PermitRootLogin yes

PasswordAuthentication yes
***

```
sudo service ssh restart
```

```
exit
```

```
logout
```
### text

将ip:<web_port>输入chrome浏览器中
如：124.***.***.***:10345
便可以访问
To connect GUI using web browser, type in
*username: kasm_user passwd: <password>*


<img src="/assets/img/ssh/ssh-GUI-1.png" width="200px">





