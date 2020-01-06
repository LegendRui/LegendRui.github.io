---
title: linux下文件的读写操作
date: 2020-01-05 14:29:46
tags:
    - linux
categories: linux
---

#### 1 open函数
open函数有2个原型，一为接受两个参数，一为接受三个参数。

##### 1.1 原型一：接受两个参数
```
int open(char *pathname, int flags)
```
###### 参数
+ pathname：文件路径名
+ flags：文件打开方式，可以是O_RDONLY | O_WRONLY | O_RDWR、O_CREAT | O_APPEND | O_TRUNC | O_EXCL | O_NONBLOCK

###### 返回值
+ 成功：返回打开文件得到的文件描述符
+ 失败：-1，并设置error

##### 1.2 原型二：接受三个参数
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

#### 2 close函数
##### 原型
```
int close(int fd);
```

##### 参数
+ fd：关闭文件的文件描述符

##### 返回值
+0：成功
+-1：失败

#### 3 read函数

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

#### 4 write函数

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

**例：**
```
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <fcntl.h>
#include <errno.h>
#include <string.h>

int main()
{
    int fd = 0;
    char buf[1024];
    int n;
    fd = open("/dev/tty", O_RDONLY | O_NONBLOCK);
    if (fd < 0) {
        perror("open /dev/tty");
        exit(1);
    }

tryagain:
    n = read(fd, buf, 10);
    if (n < 0) {
        if (errno != EAGAIN) {
            perror("read /dev/tty");
            exit(1);
        } else {
        write(STDOUT_FILENO, "try again\n", strlen("try again\n"));
        sleep(2);
        goto tryagain;
        }
    } 
    write(STDOUT_FILENO, buf, n);
    close(fd);

    return 0;
}
```
**注：**读网络文件和设备文件时，读普通文件不会产生阻塞。阻塞和非阻塞时文件的属性，而不是read/write函数的属性。

#### 5 fcntl函数
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

#### 6 lseek函数
##### 原型
```
off_t lseek(int fd, off_t offset, int whence)
```

##### 功能
可以通过`offset`参数重定位文件描述符`fd`所对应的文件的偏移量

##### 参数
+ fd：待操作文件的文件描述符
+ offset：偏移量
+ whence：起始偏移位置
    - SEEK_SET：定位为文件起始
    - SEEK_CUR：定位为当前位置
    - SEEK_END：定位为文件末尾

##### 返回值
成功：返回从文件起始开始的偏移值
失败：-1
**注：**文件的读、写是用一个偏移量。

**例：**
```
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <fcntl.h>
#include <errno.h>
#include <string.h>

int main()
{
    int fd, n;
    char *msg = "It's a test for lseek.\n";
    char ch;

    fd = open("lseek.txt", O_RDWR | O_CREAT, 0664);
    if (n < 0) {
        perror("open lseek.txt error");
        exit(1);
    }

    write(fd, msg, strlen(msg));
    lseek(fd, 0, SEEK_SET);

    while ((n = read(fd, &ch, 1))) {
        if (n < 0) {
            perror("read error");
            exit(1);
        }
        write(STDOUT_FILENO, &ch, n);
    }
    return 0;
}
```

#### 7 stat函数
##### 原型
```
int stat(const char *path, struct stat* buf)
```

##### 参数
+ path：文件路径
+ buf：传出参数，存放文件属性的结构体

##### 返回值
+ 成功：0
+ 失败：-1，设置errno

**例：**查看文件大小。
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/stat.h>

int main(int argc, char *argv[])
{
	struct stat buf;

	int ret = stat(argv[1], &buf);
	if (ret == -1) {
		perror("stat error");
		exit(1);
	}

	printf("file size: %d\n", buf.st_size);

	return 0;
}

```
**注：**stat函数是可以穿透符号连接的（即，如果操查看的是软连接，得到的是软连接指向的文件是属性）。如果不想穿透符号连接，可以使用lstat函数。