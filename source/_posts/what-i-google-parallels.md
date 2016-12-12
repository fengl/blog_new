---
title: OSX 10.12 安装Paralles Desktop失败
date: 2016-12-12 16:45:48
tags: google
---

升级了Mac的操作系统后，Paralles就怎么也安装不上了。

<!-- more -->

尝试了各种版本，各种试都不行，现象就是安装后，出现Paralles菜单和图标，就是死在了那里，关不掉，退不出，打不开。


经过无数次的尝试，搜索，终于找到解决方案，如下：


```
sudo /Volumes/Parallels\ Desktop\ 12/Parallels\ Desktop.app/Contents/MacOS/inittool install -t /Applications/Parallels\ Desktop.app
```

完成后就出现在了/Applications下面了。