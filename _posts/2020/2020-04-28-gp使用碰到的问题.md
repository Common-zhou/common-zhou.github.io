---
layout: post
title:  "gp使用碰到的问题"
categories: greenplum
tags: greenplum
excerpt: greenplum
auth: zhou
---
* content
{:toc}




# 1.gpfdist使用非超级管理员，报权限拒绝

```shell
ERROR:  permission denied: no privilege to create a readable gpfdist(s) external table

Assign the CREATEEXTTABLE permission to the user. Attempt to create the external table again:

alter user <user-name> CREATEEXTTABLE;

https://community.pivotal.io/s/article/Attempt-to-Create-an-External-Table-by-Non-Superuser-Leads-to-ERROR-permission-denied
```





# 2.permission denied for relation member_delta

```
permission denied for relation member_delta

相信大家都试过了
grant all privileges on database xxx to xxx
没叼用。
又不想一张一张表，去赋权限。

以superUser进入数据库
psql -U postgres -d postgres
\c mydb //切换到mydb数据库
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO xiaoming; //赋予所有表的所有权限给xiaoming
GRANT ALL PRIVILEGES ON tuser TO wechart; // 赋予wechart用户，tuser表的所有权限
```







# 3.设置了gpcc的密码之后 直接输入 gpcc start失败(报密码错误)

```
gpcc start -W

gpcc stop -W
```

