---
title: open、read与write函数
date: 2020-01-05 14:29:46
tags:
    - linux
categories: linux
---

#### open函数
open函数有2个原型，一为接受两个参数，一为接受三个参数。

##### 原型一：接受两个参数
```
int open(char *pathname, int flags)
```
###### 参数
+ pathname：文件路径名
+ flags：文件打开方式，可以是O_RDONLY | O_WRONLY | O_RDWR、O_CREAT | O_APPEND | O_TRUNC | O_EXCL | O_NONBLOCK

###### 返回值
+ 成功：返回打开文件得到的文件描述符
+ 失败：-1，并设置error

##### 原型二：接受三个参数
```
int open(char *pathname, int flags)
```
###### 参数
+ pathname：文件路径名
+ flags：文件打开方式，可以是O_RDONLY | O_WRONLY | O_RDWR、O_CREAT | O_APPEND | O_TRUNC | O_EXCL | O_NONBLOCK
+ mode：当flags设置为O_CREAT时，才可设置mode。mode的取值为8进制数，用来描述文件的访问权限。例如：0664代表rw-rw-r--权限。

###### 返回值
+ 成功：返回打开文件得到的文件描述符
+ 失败：-1，并设置error

#### close函数
##### 原型
```
int close(int fd);
```

##### 参数
+ fd：关闭文件的文件描述符

##### 返回值
+0：成功
+-1：失败

#### read函数

##### 原型
```
ssize_t read(int fd, void *buf, size_t count)
```
##### 参数
+ fd：文件描述符
+ buf：存数据的缓冲区
+ count：缓冲区大小

##### 返回值
+ 成功：读到的字节数
+ 失败：-1，并设置error
+ -1：

#### write函数

##### 原型
```
ssize_t read(int fd, const void *buf, size_t count)
```
##### 参数
+ fd：文件描述符
+ buf：待写出的数据的缓冲区
+ count：写出的字节数

##### 返回值
+ 成功：写入的字节数
+ 失败：-1，并设置error

**注：**读网络文件和设备文件时，读普通文件不会产生阻塞。阻塞和非阻塞时文件的属性，而不是read/write函数的属性。

#### fcntl函数
可以在不重新打开文件的情况下，修改文件的属性。

##### 原型
```
int fcntl(int fd, int cmd, ... /* arg */)
```

##### 参数
+ fd：文件描述符
+ cmd：文件属性的控制命令
    - F_GETFL：获取文件属性
    - F_SETFL：设置文件属性
+ arg：控制文件的属性

例：
```
int flags = fcntl(fd, F_GETFL);
flags |= O_NONBLOCK;
fcntl(fd, F_SETFL, flags);
```