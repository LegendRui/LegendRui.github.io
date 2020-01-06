---
title: Deepin中安装TeXLive+VSCode
date: 2020-01-05 21:45:49
tags:
    - deepin
    - texlive
    - vscode
categories: 
---

#### 1 安装TexLive
##### 1.1 到镜像网站下载TexLive安装包
点击[科大镜像](https://mirrors.ustc.edu.cn/CTAN/systems/texlive/Images/texlive2019.iso)或[清华镜像](https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/texlive2019.iso)下载

##### 1.2 挂载镜像文件
切换到安装包所在目录，在命令行输入
```
sudo mount -o loop texlive2019.iso /mnt
cd /mnt/
sudo ./install-tl
```
**注意：**到这一步会问你是否安装，输入`I`继续安装。

##### 1.3 卸载镜像文件
```
cd /
sudo umount /mnt
```

#### 2 安装VSCode
打开应用商店，搜索vscode，点击安装即可。

#### 3 安装插件
##### 3.1 安装LaTeX language support
打开VSCode，按下`ctrl+shift+x`调出插件市场，搜索LaTeX language support，安装。

##### 3.2 安装LaTeX Workshop
按同**3.1**的方法安装LaTeX Workshop。

#### 4 下载毕业论文模板
这里使用的是中国科学技术大学的论文模板。如果电脑有安装git，可以在命令行输入：
```
git clone git@github.com:ustctug/ustcthesis.git
```
没有安git的话，可以直接到GitHub[下载](https://github.com/ustctug/ustcthesis)。

#### 5 开始
选择用VSCode打开模板文件夹下的`main.tex`，输入`Ctrl+K`然后按`V`，打开PDF预览窗口。再输入快捷键`Ctrl+Alt+X`打开TeX命令窗口，点击`build`命令。大功告成！