---
layout: post
title:  "centos安装mysql"
categories: centos
tags: centos mysql
excerpt: centos上安装mysql
auth: zhou
---
* content
{:toc}


# 本地安装：

## 1.查看CentOS自带的MySQL

输入以下代码即可查看系统中是否安装了MySQL：

```
rpm -qa | grep mysql
```

如果没有查询出来结果的话，就说明系统中还没有数据库。否则说明系统已经安装过数据库了。如图所示：

至此，如果数据库是我们想要的版本，下面的教程就可以不用看了。

如果不是我们所需要的版本，我们可以选择卸载重装。

**卸载指令：**

```
rpm -e --nodeps 软件名
```

## 2.将安装包通过远程工具上传到服务器。

我们上传了两个文件，
服务器端：rpm -ivh MySQL-server-5.6.22-1.el6.i686.rpm
客户端：rpm -ivh MySQL-client-5.6.22-1.el6.i686.rpm
具体就不做演示了，不明白的可以百度。

## 3.解压Mysql到/usr/local/下的mysql目录(mysql目录需要手动创建)内

首先，我们先进入/usr/local目录下，方便创建文件夹。

```
cd /usr/local
```

创建名为mysql的文件夹：

```
mkdir mysql
```

## 4.解压：

```
tar -xvf MySQL-5.6.22-1.el6.i686.rpm-bundle.tar -C /usr/local/mysql
```

## 5.在/usr/local/mysql下安装mysql

```
//安装服务器端：rpm -ivh MySQL-server-5.6.22-1.el6.i686.rpm//安装客户端：rpm -ivh MySQL-client-5.6.22-1.el6.i686.rpm
```

## 6.启动mysql

```
service mysql start
```

## 7.将mysql加到系统服务中并设置开机启动

```
//加入到系统服务：chkconfig --add mysql//自动启动：chkconfig mysql on
```

## 8.登录mysql

mysql安装好后会生成一个临时随机密码，存储位置在/root/.mysql_secret。

我们通过临时密码登陆mysql，进而修改密码：
登陆指令：

```
msyql –u root -p
```

## 9.修改mysql的密码

```
set password = password('密码');
```

## 10.开启mysql的远程登录

默认情况下mysql为安全起见，不支持远程登录mysql，所以需要设置开启 远程登录mysql的权限,登录mysql后输入如下命令：

```
grant all privileges on *.* to 'root'@'%' identified by 'root' WITH GRANT OPTION;

root处是密码
```

> 其中”*.*“代表所有资源所有权限， “’root’@%”其中root代表账户名，%代表所有的访问地址，也可以使用一个唯一的地址进行替换，只有一个地址能够访问。如果是某个网段的可以使用地址与%结合的方式，如10.0.42.%。IDENTIFIED BY ‘root’，这个root是指访问密码，WITH GRANT OPTION允许级联授权。

## 11.刷新访问权限表

```
mysql> FLUSH PRIVILEGES;
```

##### 如果你使用的类似阿里云的云服务器，你需要在控制台开放3306端口，下面的教程可不再进行，如果你是本地Centos系统，跳过此步骤，继续向下看。

## 12.开放Linux的对外访问的端口3306,并将修改保存到防火墙中。

```
/sbin/iptables -I INPUT -p tcp --dport 3306 -j ACCEPT
```

将修改永久保存到防火墙中：

```
/etc/rc.d/init.d/iptables save
```



# 在线安装：

## 一、下载rpm包

```
wget https://repo.mysql.com//mysql57-community-release-el7-11.noarch.rpm
```

## 二、安装rpm包

```
rpm -ivh mysql57-community-release-el7-11.noarch.rpm
```

## 三、更新yum源

```
yum clean all;
yum repolist;
yum makecache;
```

## 四、安装mysql

```
yum -y install mysql mysql-server mysql-devel
```

如果提示complete则代表安装完成。

## 五、启动mysql

```
启动mysql service mysqld start
重启mysql service mysqld restart;
停止mysql systemctl stop mysqld
查看mysql是否启动 service mysqld status
```

## 六、查询mysql初始密码

```
grep "temporary" /var/log/mysqld.log
```

## 七、用初始密码登陆mysql

```
mysql -uroot -p 初始密码
```

## 八、重置密码

```
set password=password('1234');
```

密码太过于简单会报错——Your password does not satisfy the current policy requirements
MySQL5.6.6版本之后增加了密码强度验证插件validate_password，相关参数设置的较为严格。使用了该插件会检查设置的密码是否符合当前设置的强度规则，若不满足则拒绝设置。

###### 解决方案一：

修改为复杂密码

###### 解决方案二：在mysql输入以下代码：

```
//set global validate_password_policy=0;set global validate_password_length=1;
```

再执行修改密码命令。

##### 接下来按照上面本地方法“开启mysql的远程登录”以后开始执行，流程一样。

