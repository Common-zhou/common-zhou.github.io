安装hadoop之前，首先要安装 jdk1.8.0_181（我使用的版本） 并配置jdk环境变量，接下来开始安装hadoop

1. hadoop2.6.0下载

下载地址：http://archive.apache.org/dist/hadoop/core/hadoop-2.6.0/

2 .解压安装包

tar -zxvf hadoop-2.6.0.tar.gz -C /opt/soft/
3 .配置hadoop环境变量

     vim  /etc/profile





    export HADOOP_HOME=/opt/soft/hadoop-2.6.0
    export PATH=HADOOP_HOME/bin:HADOOP_HOME/sbin:$PATH
    
    更新环境变量
    source /etc/profile

4.修改hadoop 配置文件

 （1）修改/opt/soft/hadoop-2.6.0/etc/hadoop/hadoop-env.sh  将JAVA_HOME加进去

export JAVA_HOME=/opt/soft/jdk1.8.0_181
（2）修改/opt/soft/hadoop-2.6.0/etc/hadoop/core-site.xml 文件，

```
<configuration>
    <!-- 指定HDFS（namenode）的通信地址 -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
    <!-- 指定hadoop运行时产生文件的存储路径 -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/data/hadoop/tmp</value>
    </property>
</configuration>
```

   fs.defaultFS直接用localhost就行，如果重命名了主机名，也可以用重命名的。

（3）修改/opt/soft/hadoop-2.6.0/etc/hadoop/hdfs-site.xml 



    <configuration>
        <property>
            <name>dfs.name.dir</name>
            <value>/opt/data/hadoop/hdfs/name</value>
            <description>namenode上存储hdfs名字空间元数据 </description> 
        </property>
        <property>
            <name>dfs.data.dir</name>
            <value>/opt/data/hadoop/hdfs/data</value>
            <description>datanode上数据块的物理存储位置</description>
        </property>
        <!-- 设置hdfs副本数量 -->
        <property>
            <name>dfs.replication</name>
            <value>1</value>
        </property>
    </configuration>

 



5.配置本机ssh免密码登录

ssh-keygen -t rsa  命令后一路回车，不输入任何东西
然后执行 cat ~/.ssh/id_rsa.pub  >>  ~/.ssh/authorized_keys  配置本机可免密登录
测试 ssh localhost 是否成功

6.hdfs启动与停止

  第一次启动hdfs需要格式化，之后启动就不需要了

```
cd /opt/soft/hadoop-2.6.0/bin
./hdfs namenode -format
启动命令：

cd /opt/soft/hadoop-2.6.0/sbin
./start-dfs.sh
停止命令：

cd /opt/soft/hadoop-2.6.0/sbin
./stop-dfs.sh
```

hdfs启动完成后，浏览器输入：http://192.168.2.14:50070 




7.接下来配置yarn文件. 配置/opt/soft/hadoop-2.6.0/etc/hadoop/mapred-site.xml   这里注意一下，hadoop里面默认是mapred-site.xml.template 文件，如果配置yarn，把mapred-site.xml.template   重命名为mapred-site.xml 。如果不启动yarn，把重命名还原。

 mv mapred-site.xml.template mapred-site.xml

 vim  mapred-site.xml

```
<configuration>
    <!-- 通知框架MR使用YARN -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```



8.配置/opt/soft/hadoop-2.6.0/etc/hadoop/yarn-site.xml文件，

 vim  yarn-site.xml

```
<configuration>
    <!-- reducer取数据的方式是mapreduce_shuffle -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

9.启动yarn
  启动命令：

cd /opt/soft/hadoop-2.6.0/sbin
./start-yarn.sh
  停止命令：

cd /opt/soft/hadoop-2.6.0/sbin
./stop-yarn.sh
  可以用jps命令查看都启动了什么进程：

[root@hadoop4 hadoop]# jps
4754 Jps
4163 ResourceManager
3848 DataNode
4024 SecondaryNameNode
4254 NodeManager
3759 NameNode
yarn启动完成后，浏览器输入：http://192.168.2.14:8088   (8088是默认端口，如果被占用，先把占用的端口杀掉 )




到这里，hdfs和yarn就启动成功了









搭建Spark

出现slf4j的jar包找不到，可以直接拷贝

<https://blog.csdn.net/zhaojw_420/article/details/53169915>

