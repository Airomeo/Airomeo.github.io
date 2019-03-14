---
title: Android开发中必须注意的关键细节
date: 2018-12-04 21:12:05
tags: Android
category: 
- Study
- Coding
id: 3
---
### 设置android:allowBackup = false权限
<!--more-->
#### 不设置的危害：

**Android开发自动生成的AndroidManifest.xml中的allowBackup属性的含义和危险性实例讲解**
https://blog.csdn.net/wolfking0608/article/details/78792650

**详解Android App AllowBackup配置带来的风险**
https://blog.csdn.net/forlong401/article/details/44241305

**android:allowBackup = false后编译报错**
https://blog.csdn.net/github_37847975/article/details/78741725

#### 解决：

单纯加上这一句会报错，需要在application节点下加入 tools:replace=”android:allowBackup”：

#### 例子：

> <application
.........................
tools:replace="android:allowBackup"
android:allowBackup="false"
........................../>

