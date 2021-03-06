#### Zookeeper

zookeeper是apache项目下的开源分布式应用程序协调服务。用来确保集群中只有一个master。zookeeper使用java语言开发，运行需要安装jdk环境。

#### zookeeper部署

zookeeper集群运行至少需要两台机器，以下将使用三台机器搭建zookeeper集群，分别为192.168.10.193,192.168.10.194,192.168.10.195

三台机器的zookeeper配置文件均一样，所以只要安装一台机器，然后将程序拷贝至其他机器即可\(除myid文件外\)。

##### zookeeper安装

```
   1.  yum install java -y

   2.  wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz

   3.  tar -zxf zookeeper-3.4.10.tar.gz && mv zookeeper-3.4.10 /usr/local/zookeeper

   4.  vim /usr/local/zookeeper/config/zoo.cfg

          tickTime=2000      #心跳间隔时间 2s
          initLimit=10       #客户端连接超时时间 10*tickTime 
          syncLimit=5        #Leader和Follower连接超时时间 5*tickTime
          dataDir=/usr/local/zookeeper/data
          clientPort=2181
          autopurge.snapRetainCount=5 #参数指定了需要保留的事务日志和快照文件的数目,默认是保留3个。和autopurge.purgeInterval搭配使用
          autopurge.purgeInterval=1 #在3.4.0及之后版本，Zookeeper提供了自动清理事务日志和快照文件的功能，这个参数指定了清理频率，单位是小时，需要配置一个1或更大的整数，默认是0，表示不开启自动清理功能
          server.1=192.168.10.193:2888:3888  #对应相应的id，如service.1 此处的1为id
          server.2=192.168.10.194:2888:3888
          server.3=192.168.10.195:2888:3888
   5. mkdir /usr/local/zookeeper/data && echo 1 > /usr/local/zookeeper/data/myid  #

   6.  scp -r /usr/local/zookeeper 192.168.10.194:/usr/local/  #登录194 修改/usr/local/zookeeper内容为2

   7. scp -r /usr/local/zookeeper 192.168.10.195:/usr/local/  #登录195 修改/usr/local/zookeeper内容为 3

   8. /etc/init.d/iptables stop  #关闭防火墙。

   9. /usr/local/zookeeper/bin/zkServer.sh start #启动zookeeper服务

   10. 登录其他两台机器，启动zookeeper服务
```

##### zookeeper集群状态

192.168.10.195

![](/assets/1.png)

192.168.10.194

![](/assets/2.png)

192.168.10.193

![](/assets/3.png)

##### zookeeper脚本说明

```
./bin/zkCleanup.sh   #清理zookeeper历史数据 -包括事务日志文件和快照数据文件

./bin/zkCli.sh  #zookeeper的简易客户端通过这个客户端可以对zookeeper服务端数据进行各种操作 接远程机器可以执行./zkCli.sh -server ip:port

./bin/zkEnv.sh  # 设置zookeeper启动时的环境变量   这个脚本不要单独执行,它需要嵌入到zkServer.sh或者其他脚本中使用

./bin/zkServer.sh # zookeeper服务器的启动停止重启和状态查询
```



