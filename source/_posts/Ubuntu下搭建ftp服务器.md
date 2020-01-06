---
title: Ubuntu下搭建ftp服务器
date: 2019-12-25 22:28:36
tags:
    - Linux
    - ftp
categories: Linux
---

#### 1 下载vsftpd  
```
sudo apt-get install vsftpd
```

#### 2 修改配置文件

```
sudo vi /etc/vsftpd.conf
```
在vsftpd.conf中进行设置

```
anonymous_enable=YES
local_enable=YES
write_enable=YES
anon_upload_enable=YES
anon_mkdir_write_enable=YES
```
主要是打开wirte权限，其他是我嫌麻烦允许匿名上传，反正没人知道我的vps的ip...

#### 3 创建ftp目录
在home文件夹下创建一个ftp的工作目录

```
sudo mkdir /home/ftp
sudo mkdir /home/ftp/download
sudo mkdir /home/ftp/upload
```

#### 4 添加ftp用户
```
sudo useradd -d /home/ftp -s bin/bash username
sudo passwd username
```

#### 5 启动vsftpd服务器

```
sudo service vsftpd start
```

#### 6 登录测试

```
ftp 127.0.0.1
```
