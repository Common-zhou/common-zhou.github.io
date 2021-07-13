---
layout: post
title:  "greenplum运维相关知识"
categories: greenplum
tags: greenplum
excerpt: greenplum
auth: zhou
---
* content
{:toc}
# greenplum运维



## 1.监控集群的状态

### 1.1 gpstate命令

gpstate是了解gp状态的基本命令，无论数据库是否正常运转，都可以使用该命令

```
如果gpstate无法正常工作，建议排查以下内容
	是否执行了 source <gp安装目录>/greenplum_path.sh
	是否正确设置了MASTER_DATA_DIRECTORY和PGPORT
```

```shell
$ gpstate --help

显示详细信息
$ gpstate -s

显示 primary instance 和 mirror instance 的对应关系
$ gpstate -c

显示 standby master details
$ gpstate -f

显示 mirrors实例的状态和配置信息
$ gpstate -m

显示状态综合信息
$ gpstate -Q

显示版本信息
$ gpstate -i
```

### 1.2 系统表gp_segment_configuration

```sql
select * FROM gp_segment_configuration;

--列出当前故障离线的节点
select * from gp_segment_configuration where status = 'd';

--列出集群里每个服务器正在运行的节点个数
select hostname,count(dbid) from gp_setment_configuration 
where status = 'u' and role = 'p' 
group by hostname;
```

### 1.3 Sement的故障恢复和再平衡

​		gp集群的数据节点（primary或者mirror）可能发生故障。配置了mirror的gp集群具备高可用特性，当节点因故障离线时仍然保持数据完整且可以读写。在排查修复可能的硬件故障之后，因尽快利用gprecoverseg命令将故障的节点恢复到正常的工作状态。

#### ①起始状态

![1587971189545](\assets\1587971189545.png)

#### ②主机二上一台primary挂了

![1587971299967](\assets\1587971299967.png)



#### ③解除硬件故障后

![1587971456908](\assets\1587971456908.png)

虽然此时可以正常使用 但是主机一有三台primary 主机二只有一台 这样负载不均衡

管理员可以在Master上运行以下命令再平衡节点以恢复集群性能

```shell
$ gprecocerseg -r
```





### 1.4 gp日志位置

| 路径                                      | 描述                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| /home/gpadmin/gpAdminLogs/*               | 多种命令行工具产生的日志，包括gpstart/gpstop/gpinitsystem/gpconfig |
| $MASTER_DATA_DIRECTORY/pg_log/startup.log | postgreSQL启动日志                                           |
| $MASTER_DATA_DIRECTORY/pg_log/gpdn-*.csv  | greenplum活动日志                                            |
| /var/log/messages                         | linux系统日志                                                |





## 2.管理gp集群

















## 3.gpcc