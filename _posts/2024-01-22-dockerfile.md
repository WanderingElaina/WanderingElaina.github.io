---
layout: post_layout
title: docker build image
time: 2024年01月22日 星期五
location: beijing
pulished: true
excerpt_separator: "##"
---
##### docker build image
SCP（Secure Copy）：使用SCP命令通过SSH连接将文件从本地计算机复制到远程服务器。在本地命令行中运行以下命令：
```
scp /path/to/file username@server_ip:/home/ps/Docker
```
其中，/path/to/file 是本地计算机上的文件路径，username 是远程服务器的用户名，server_ip 是远程服务器的IP地址。
找到docker_file文件
```
cd /home/ps/Docker
```
```
sudo docker build -t kasm_ros -f docker_file .
```
这条命令的含义是使用当前目录下名为 "docker_file" 的 Dockerfile 文件构建一个名为 "kasm_ros" 的 Docker 镜像。

- `sudo` 表示使用超级用户权限执行 Docker 命令（如果需要）。
- `docker build` 是 Docker 命令，用于构建 Docker 镜像。
- `-t kasm_ros` 表示给构建的镜像指定一个名称和标签。在这里，镜像名称是 "kasm_ros"。
- `-f docker_file` 表示使用指定的 Dockerfile 文件进行构建。 "docker_file" 是文件名。
- `.` 表示当前目录，它告诉 Docker 在当前目录中查找 Dockerfile 文件进行构建。

简而言之，该命令告诉 Docker 根据当前目录中名为 "docker_file" 的文件中的指令和配置构建一个名为 "kasm_ros" 的镜像。

要执行这个命令，请确保您在包含 Dockerfile 文件的正确目录下运行命令，并确保 Dockerfile 文件存在且正确命名。
查看所有镜像

```
 sudo docker images
```
REPOSITORY               TAG       IMAGE ID       CREATED        SIZE
kasm_ros                 latest    9e98668a90cb   10 hours ago   10.8GB
workspace_deluxe         1_13_0    5216a430d115   12 days ago    6.7GB
```
sudo docker run --shm-size 32g -itd --runtime=nvidia --gpus all --privileged -p <web_port>:6901 -p <ssh_port>:22 --security-opt seccomp=unconfined -
e VNC_PW=<password> -e NVIDIA_DRIVER_CAPABILITIES=all -v /mnt/sda/data/<name>:/data -v /mnt/sda/data/public:/public --name <name>
workspace_deluxe:1_13_0
```
这行代码是一个示例，您可以根据需要进行定制。以下是每个参数的含义：

- `sudo`: 以管理员权限运行 Docker 命令（如果必要）。
- `docker run`: 创建并运行一个新的 Docker 容器。
- `--shm-size 32g`: 设置容器内共享内存的大小为 32GB。
- `-itd`: 配置容器为交互模式 (interactive)、分离模式 (detached)，并将容器启动后保持运行。
- `--runtime=nvidia`: 使用 NVIDIA 容器运行时。
- `--gpus all`: 允许容器访问所有可用的 NVIDIA GPU。
- `--privileged`: 在容器内以特权模式运行，拥有主机的完全访问权限。
- `-p <web_port>:6901`: 将容器内的 VNC 端口 (6901) 映射到主机指定的端口 `<web_port>`。您可以自定义 `<web_port>` 的值。
- `-p <ssh_port>:22`: 将容器内 SSH 端口 (22) 映射到主机指定的端口 `<ssh_port>`。您可以自定义 `<ssh_port>` 的值。
- `--security-opt seccomp=unconfined`: 禁用容器内的 seccomp (安全计算模块)。
- `-e VNC_PW=<password>`: 设置一个名为 VNC_PW 的环境变量，并将其值设置为 `<password>`。您可以自定义 `<password>` 的值。
- `-e NVIDIA_DRIVER_CAPABILITIES=all`: 设置 NVIDIA 驱动程序的功能 (capabilities) 为全部，允许容器使用所有 NVIDIA 驱动功能。
- `-v /mnt/sda/data/<name>:/data`: 将主机中的 `/mnt/sda/data/<name>` 目录挂载到容器中的 `/data` 目录。您可以自定义 `<name>` 的值和挂载路径。
- `-v /mnt/sda/data/public:/public`: 将主机中的 `/mnt/sda/data/public` 目录挂载到容器中的 `/public` 目录。

您可以替换命令中用 `<web_port>`, `<ssh_port>`, `<password>`, `<name>` 标记表示的值，以满足您的需求。注意，这里的 `workspace_deluxe:1_13_0` 表示要使用的镜像及其标签，您可能需要根据自己的实际情况修改它。
##### 注释
示例
```
sudo docker run --shm-size 32g -itd --runtime=nvidia --gpus all --privileged -p 10336:6901 -p 7905:22 --security-opt seccomp=unconfined -e
VNC_PW=test -e NVIDIA_DRIVER_CAPABILITIES=all -v /mnt/sda/data/test:/data -v /mnt/sda/data/public:/public --name test  kasm_ros
```
这个命令是用于在 Docker 中创建并运行一个名为 “test” 的容器，使用了 kasm_ros 镜像。下面是命令中的各个参数的解释：

--shm-size 32g：设置容器的共享内存大小为 32GB。共享内存可用于进程间通信。
-itd：使用交互式终端和后台模式运行容器。
--runtime=nvidia：指定容器运行时为 NVIDIA runtime，以便在容器中访问 NVIDIA GPU。
--gpus all：在容器中启用对所有可用的 NVIDIA GPU 的访问。
--privileged：以特权模式运行容器，允许容器内的进程访问主机的所有设备。
-p 10336:6901：将容器内的 VNC 端口 6901 映射到主机的端口 10336。
-p 7905:22：将容器内的 SSH 端口 22 映射到主机的端口 7905。
--security-opt seccomp=unconfined：禁用容器内的 seccomp 策略，以允许更高级的系统调用。
-e VNC_PW=test：在容器中设置环境变量 VNC_PW 的值为 “test”，作为 VNC 访问密码。
-e NVIDIA_DRIVER_CAPABILITIES=all：将环境变量 NVIDIA_DRIVER_CAPABILITIES 设置为 “all”，以支持容器内的所有 NVIDIA 驱动能力。
-v /mnt/sda/data/test:/data：将主机的 /mnt/sda/data/test 目录挂载到容器的 /data 目录。
-v /mnt/sda/data/public:/public：将主机的 /mnt/sda/data/public 目录挂载到容器的 /public 目录。
通过运行此命令，您将创建一个名为 “test” 的容器，并在其中运行 kasm_ros 镜像。容器将具有上述参数所指定的各种配置，包括对 NVIDIA GPU 的访问、VNC 和 SSH 的端口映射、共享内存的大小等。
##### 注释
使用 sudo docker images 命令查看 Docker 主机上的镜像列表时，如果某个镜像的标签（Tag）为 latest，那意味着该标签代表了该镜像的最新版本。

在 Docker 中，镜像可以有多个标签，每个标签可以对应不同的版本或变体。其中，latest 是一个默认的标签，通常用于表示最新的版本。当您从一个仓库（Registry）中拉取镜像时，如果不指定具体的标签，默认会拉取该镜像的 latest 标签。

例如，假设有一个名为 ubuntu 的镜像，它有几个不同的标签，如 16.04、18.04 和 latest。这些标签对应着 Ubuntu 操作系统的不同版本。而 latest 标签则表示最新的版本。

通过使用 sudo docker images 命令，您可以查看本地 Docker 主机上的镜像列表，并了解每个镜像的标签信息。对于一个标记为 latest 的镜像，这意味着这是最新的版本供您使用。但请注意，最新版本可能会随着时间的推移而更新，如果您希望使用确切的版本，最好使用特定的标签来拉取或指定镜像。

##### 进入容器安装ssh
sudo docker exec -it <name> /bin/bash
apt-get update
apt-get install vim openssh-server nano
##### 修改默认用户密码，初始密码可以和<name>一致
passwd
vi /etc/ssh/sshd_config
***
PermitRootLogin yes
PasswordAuthentication yes
***
service ssh restart

https://ip:<web_port>


