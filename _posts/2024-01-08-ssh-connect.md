---
layout: post_layout
title: ssh-connect
time: 2024年01月08日 星期一
location: bj
pulished: false
excerpt_separator: "##"
--- 
## 端口映射
命令：ssh 用户名@主机ip -p 映射的端口号

例子：比如要远程到主机22.22.22.22的12345端口，但是12345端口被映射到了54321端口，所以要访问12345端口的命令为<br>
```ssh root@22.22.22.22 -p 54321```

```sudo docker ps -a```

在 Docker 中以管理员权限列出所有容器的命令。sudo 表示以管理员身份运行命令，docker ps 用于显示正在运行的容器，而 -a 参数则显示所有的容器，包括正在运行和已经停止的容器。

## 下面是命令行容器的创建命令，自行修改<name>

```sudo docker run --shm-size 1g --runtime=nvidia --gpus all -itd --privileged --gpus all -p <ssh_port>:22 --security-opt seccomp=unconfined -e NVIDIA_DRIVER_CAPABILITIES=all -v /mnt/sda/data/<name>:/data -v /mnt/sda/data/public:/public --name <name> continuumio/miniconda3 /bin/bash```

这行代码是在 Docker 中以管理员权限运行一个镜像，并设置一些参数和选项。

具体解释如下：

- `sudo` 表示以管理员身份运行命令。
- `docker run` 是运行 Docker 容器的命令。
- `--shm-size 1g` 设置共享内存的大小为 1GB。
- `--runtime=nvidia` 指定 Docker 使用 Nvidia 运行时环境。
- `--gpus all` 指定容器中可以使用全部可用的 GPU。
- `-itd` 设置容器的交互式终端、后台运行和分离模式。
- `--privileged` 开启容器的特权模式。
- `-p <ssh_port>:22` 将容器的 SSH 端口映射到宿主机的指定端口。
- `--security-opt seccomp=unconfined` 关闭容器的 seccomp 安全机制。
- `-e NVIDIA_DRIVER_CAPABILITIES=all` 设置 NVIDIA 驱动的能力为全部。
- `-v /mnt/sda/data/<name>:/data` 将宿主机的 `/mnt/sda/data/<name>` 目录挂载到容器的 `/data` 目录。
- `-v /mnt/sda/data/public:/public` 将宿主机的 `/mnt/sda/data/public` 目录挂载到容器的 `/public` 目录。
- `--name <name>` 指定容器的名称为 `<name>`。
- `continuumio/miniconda3` 是要运行的镜像名称。
- `/bin/bash` 是容器启动后进入的默认命令行终端。

请注意，上述命令中的 `<ssh_port>` 和 `<name>` 都是占位符，需要替换为实际的值。

## 进入容器安装ssh

```sudo docker exec -it <name> /bin/bash```

这行代码是运行在 Docker 中管理容器的命令。具体解释如下：

- `sudo` 表示以管理员身份运行命令。

- `docker exec` 是运行 Docker 容器内部命令的命令。可以在已经运行的容器中运行命令。

- `-it` 表示交互式终端模式运行容器内的命令，使用户能够交互式地输入和输出。

- `<name>` 是要在其中运行命令的容器的名称。

- `/bin/bash` 是要在容器中运行的命令。在这里，它是一个 Bash shell。

使用这个命令可以让用户进入正在运行的 Docker 容器内部，以交互式方式来执行命令并执行其他操作。

`whoami`

- 显示用户名

```apt-get update```

```apt-get install vim openssh-server nano```

这行代码是在容器中使用apt-get包管理器更新软件包列表，然后安装vim、openssh-server和nano软件包。

具体解释如下：

- `apt-get update`：更新软件包列表。这会使apt-get获取最新可用的软件包和版本信息。

- `&&`：是Linux中的逻辑运算符，用于在前一个命令成功完成后才执行下一个命令。

- `install vim openssh-server nano`：安装vim、openssh-server和nano软件包。这些软件包可以提供编辑器和SSH服务器的功能。

因此，这行代码的作用是在容器内使用apt-get更新软件包列表，并安装vim、openssh-server和nano软件包。

## 修改默认用户密码，初始密码可以和```<name>```一致
passwd
vi /etc/ssh/sshd_config
***
PermitRootLogin yes
PasswordAuthentication yes
***
service ssh restart
## delete
````sudo docker stop zhouhuijie````

· 关闭容器

````sudo docker rm zhouhuijie````

· 移除容器

`logout`

· 退出linux系统





