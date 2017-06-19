---
layout: post
title: IntelliJ Idea 2017破解
category: 工具
tags: Java
keywords: Java
---

1. 下载 ```JetbrainsCrack-2.6.2.jar``` 文件
2. 将下载的 jar 复制到安装目录下的 bin 文件夹
3. 编辑 ```idea.vmoptions``` 在文件最后一行加上下面这句话:

```
-javaagent:/你的IntelliJ Idea安装目录/bin/JetbrainsCrack-2.6.2.jar

windows下还需要编辑 idea64.vmoptions
```

##### 全部完成后启动Idea，选择第二项随意输入几个字符即可。