---
title: Linux学习笔记
date: 2019-12-18 16:37:54
tags:
---

## Linux系统目录
+ bin：存放二进制可执行文件
+ boot：存放开机启动程序
+ dev：存放设备文件
+ home：存放用户
+ etc：存放信息和系统配置文件
+ lib：库文件
+ root：管理员宿主目录
+ usr：用户资源管理目录

## Linux系统文件类型
* 普通文件（-）
* 目录文件（d）
* 字符设备文件（c）
* 块设备文件（b）
* 软连接（l）
* 管道文件（p）
* 套接字（s）
* 未知文件

## 常用命令
+ ls：列出文件
+ cd：切换目录
+ which：查看命令路径
+ pwd：查看当前路径
+ mkdir：创建目录
+ rmdir：删除空目录
+ touch：创建文件
+ rm：删除文件（-r删除目录）

## 文件命令
+ chmod: 更改文件权限
    * 文字设定法：
        * 操作对象可以是： u表示用户， g表示同组用户，o表示其他用户，a表示所有用户  
        * 操作符号可以有： +添加权限，-取消权限，=赋予给定权限并取消其他所有权限
        * 操作权限可以是： r读，w写，x执行   
``` 
chmod a+x file # 所有用户可执行
```
    * 数字设定法：r是4，w是2，x是1   
```
chmod 764 file # 用户可读可写可执行，组用户可读可写，其他用户可读
```
+ chown：修改文件所属用户
```
sudo chmod user file
```

+ find：查找文件
    * -type：按文件类型查找
    * -name：按文件名查找
    * -maxdepth：指定查找深度，应作为第一个参数出现
    * -size：指定大小
```
find ./ -type "l"               # 查找软连接
find ./ -name '*.jpg'           # 查找当前目录及子目录下的jpg文件
find ./ -maxdepth 1 '*.jpg'     # 查找当前目录下的jpg文件
find ./ -size +200k -size -2M   # 查找200k~2M的jpg文件，注意是小写的k，大写的M
```

+ grep命令：找文件内容
```
grep -r 'copy' ./ -n
ps aux | grep cupsd
```
+ 