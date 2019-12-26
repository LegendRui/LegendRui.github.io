---
title: Git常用命令
date: 2019-12-26 13:11:58
tags:
    - git
categories: 
---

#### 初始化本地仓库
```
git init
```

#### 向本地仓库添加文件
```
git add a.txt       % 添加a.txt
git add *           % 添加所有文件
```

#### 从本地仓库删除文件
```
git rm a.txt        % 删除a.txt
git rm -r dir       % 删除目录dir
```

#### 向本地仓库提交修改
```
git commit -m "comment"
```


#### 查看本地仓库状态
```
git status
```

#### 查看日志
```
git log --pretty=oneline
```

#### 添加远程仓库
```
git remote add origin git@github.com:XXX/xxx.git
```

#### 克隆仓库
```
git clone git@github.com:XXX/xxx.git
```

#### 向远程仓库提交
```
git push -u origin master
```

#### 从远程仓库同步
``` 
git pull <远程主机名> <远程分支名>:<本地分支名>
```
例如，要取回origin主机的code分支，与本地的master分支合并：
```
git pull origin code:master
```
或者，远程分支code与当前分支合并，可以是：
```
git pull origin code
```
或者也可以先把本地仓库远程分支更新为远程仓库对应分支（只下载）：
```
git fetch
```
再进行合并：
```
git merge orgin master
```