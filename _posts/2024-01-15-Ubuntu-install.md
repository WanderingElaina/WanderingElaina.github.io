---
layout: post_layout
title: Ubuntu-install
time: 2024年01月15日 星期一
location: bj
pulished: true
excerpt_separator: "##"
---

#### 需要安装的软件
- Vmware
  - 关注微信公众号软件管家
- Diskgenius
  - [官网](https://www.diskgenius.cn/)
- Ubuntu
  - [官网](https://ubuntu.com/)
    - 选择 Ubuntu 20.04.6 LTS<img src="/assets/img/Ubuntu/1.png" width="400px">
    <img src="/assets/img/Ubuntu/2.png" width="400px">
##### Diskgenius操作
- 插上硬盘、打开diskgenius.exe
- 找到硬盘SanDisk
- 若为MBR <img src="/assets/img/Ubuntu/3.png" width="400px">
  - 选择磁盘->转化分区表类型为GUID(格式)p
- 若为GBT <img src="/assets/img/Ubuntu/4.png" width="400px">
  - 不用改变
- 删除所有分区->保存更改
- 选择分区 -> 建立ESP/MSR分区(ESP分区大小：2048 建立MSR分区：去掉) -> 保存更改<img src="/assets/img/Ubuntu/5.png" width="400px">
##### Vmware操作
- 打开虚拟机（Vmware）
- 创建新的虚拟机
- <img src="/assets/img/Ubuntu/6.png" width="400px">
- 典型
- <img src="/assets/img/Ubuntu/7.png" width="400px">
- 稍后安装操作系统
- <img src="/assets/img/Ubuntu/8.png" width="400px">
- Linux
- <img src="/assets/img/Ubuntu/9.png" width="400px">
- 选定位置
- <img src="/assets/img/Ubuntu/10.png" width="400px">
- 将虚拟磁盘拆分成多个文件
- <img src="/assets/img/Ubuntu/11.png" width="400px">
- 完成
- <img src="/assets/img/Ubuntu/12.png" width="400px">
- 编辑虚拟机设置
- <img src="/assets/img/Ubuntu/13.png" width="400px">
- 硬件->CD/DVD(SATA)->启动时连接->使用ISO映像文件（就是所下载的Ubuntu的ISO文件）
- <img src="/assets/img/Ubuntu/14.png" width="400px">
- 硬盘->添加
- <img src="/assets/img/Ubuntu/15.png" width="400px">
- 下一步
- <img src="/assets/img/Ubuntu/16.png" width="400px">
- SCSI
- <img src="/assets/img/Ubuntu/17.png" width="400px">
- 使用物理磁盘
- <img src="/assets/img/Ubuntu/18.png" width="400px">
- 设备选择最后一个->使用整个磁盘
- <img src="/assets/img/Ubuntu/19.png" width="400px">
- 完成
- <img src="/assets/img/Ubuntu/20.png" width="400px">
- 选项->高级->UEFI
- <img src="/assets/img/Ubuntu/21.png" width="400px">
- enter
- <img src="/assets/img/Ubuntu/22.png" width="400px">
- 选择语言
- <img src="/assets/img/Ubuntu/23.png" width="400px">
- enter
- <img src="/assets/img/Ubuntu/24.png" width="400px">
- 只选择最小安装
- <img src="/assets/img/Ubuntu/25.png" width="400px">
- 安装类型为其他选项
- <img src="/assets/img/Ubuntu/26.png" width="400px">
- 分区：
  - /home和/目录以6：4划分空间
  - swap：  例如32gb*1024=32768mb  逻辑分区  空间起始位置  swap（交换空间）
  - home：  例如：400gb*1024=409600mb  逻辑分区  空间起始位置  EXT4日志文件系统  /home
  - /：其余全部挂载 /（根）  逻辑分区 空间其实位置  EXT4日志文件系统  /
  - 安装启动引导器设备为类型efi所在的分区
- <img src="/assets/img/Ubuntu/27.png" width="400px">
- 点击有线的齿轮
- <img src="/assets/img/Ubuntu/28.png" width="400px">
- IPV4->ipv4方式(手动)
  - 地址：192.168.1*（例如：192.168.1.34）
  - 子网掩码：255.255.255.0
  - 网关：192.168.1.1
- DNS：114.114.114.114
- <img src="/assets/img/Ubuntu/29.png" width="400px">


