---
title: Linux下Intellij系列无法使用输入中文的解决方法
date: 2018-08-11 23:55:51
link: 180811-1
tags: [linux]
---

## intellij系列产品无法输入中文

我用的是ArchLinux 输入法用的是fcitx-googlepinyin

首先进入软件的bin目录下，找到启动该软件的XXXX.sh文件

在注释之后的首行添加：

```shell
export XMODIFIERS="@im=fcitx"
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"
```

就好了。

注意，一定是注释之后的“首行”，我之前添加到最下面就没起作用