---
layout: post
title:  "typora设置gitee图床"
categories: typora gitee
tags: typora gitee 图床
excerpt: 设置gitee为图床，方便上传图片
auth: zhou
---

* content
{:toc}
# 1.安装软件

- 安装typora
- 安装picgo

[typora](https://typora.io/)

PicGo 是一款在 github 上开源的用于快速上传图片并获取图片 URL 连接的工具。可以通过以下官网链接选择对应的系统下载：

[PicGo](https://github.com/Molunerfinn/PicGo/releases)



# 2.图床创建步骤

### 2.1新建仓库

![image-20210720135346367](https://gitee.com/common-zhou/img/raw/master/img/20210720135356.png)

### 2.2安装PicGo插件

![image-20210720135453869](https://gitee.com/common-zhou/img/raw/master/img/20210720135506.png)



gitee 2.0.3和uploader均可。本人采用的是uploader



### 2.3申请personal keys

设置->私人令牌->生成新令牌

![image-20210720135730121](https://gitee.com/common-zhou/img/raw/master/img/20210720135732.png)

### 2.4打开PicGo设置

![image-20210720135837784](https://gitee.com/common-zhou/img/raw/master/img/20210720135839.png)

需要注意`PicGo设置`中的 `设置server`注意端口

### 2.5修改typora上传设置

偏好设置->图像->

将PicGo的安装路径复制过来即可。



如果出现 `Failed to Fetch`，则注意检查PicGo设置中的设置server端口



# 3.基本使用

之后对着图片点右键，上传即可。