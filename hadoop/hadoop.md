## Linux中安装配置Hadoop集群
#### 创建用户
- 创建用户，并为其添加root权限。
- groupadd hadoop//创建hadoop组，有关用户相关权限的修改都需要管理员身份
- adduser hadoop -g hadoop -m//创建hadoop用户并加入hadoop用户组，-m的意思是创建用户目录。
- passwd hadoop//设置hadoop用户密码
- vi /etc/sudoers//添加sudo权限，在centos7中默认是不具备sudo权限的
- 内容如下：root ALL=(ALL)ALL后面仿照格式添加hadoop ALL=(ALL)ALL
#### 安装ssh服务，确认Linux系统中已经具备ssh服务，centos7是自带的 
#### 配置ssh免密码登录，这部分可以查看ssh.md文件 
- vi /etc/hosts
- 注意不修改文件中原有的，直接另起一行添加
#### 在centos上安装Java环境，使用yum安装Java
- 首先，Hadoop是用Java开发的，Hadoop的编译及mapreduce的运行都需要使用JDK。
- Java -version可以查看JDK是否安装成功，以及版本。master和slave机器必须安装同一个版本的JDK，不然会出问题。为了避免出现不可预料的问题，我们统一一下各台机器上JDK的版本。
- 有个问题是centos7会自带OpenJDK，但是桌面版和最小安装所带的OpenJDK版本不同，桌面版带的是OpenJDK1.8，最小安装带的是OpenJDK1.7，所以要删除老版本，重新安装。
- openjdk只包含最精简的jdk，并不能很好的满足hadoop的需求，所以需要卸载centos7预装的openjdk，从新安装JDK，可以根据系统是多少位的，选择jdk是32位还是64位。(后面我又用openjdk成功了，但是JAVA_HOME变量和之前的不一样)
- rpm -qa  //查看所有rpm已安装软件名称。
- rpm -ql 软件名称 //显示软件安装路径
- yum list installed | grep java// 列出yum已经安装的软件
- yum -y remove *openjdk*     //删除带有openjdk的软件，-y的意思是，这个操作可能会在后续需要输入yes或者no ,-y选项默认所有问题都是yes，后面就不用再输入了。
#### 配置Java环境变量 
- vi /etc/profile   //会看到如下提示
- It's NOT a good idea to change this file unless you know what you are doing. It's much better to create a custom.sh shell script in /etc/profile.d/ to make custom changes to your environment, as this will prevent the need for merging in future updates.
- 在/etc/profile.d目录下新建java.sh 
- #!/bin/bash
- JAVA_HOME=/usr/jdk   
- PATH=$JAVA_HOME/bin:$PATH 
- CLASSPATH=.:$JAVA_HOME/lib
- export PATH JAVA_HOME CLASSPATH 
- $source /etc/profile   //使配置文件生效
#### 安装hadoop程序
- 下载的时候注意，hadoop*-src.tar.gz是源码压缩文件。可以用eclipse导入研究源码，或者Maven构建编译打包。
- hadoop*.tar.gz是已经官方发布版压缩包，可以直接使用。不过官网下载的hadoop发布版本只适合86环境，若要64则需要Maven重新构建。
- 所有机器上都要安装Hadoop，所以要进行Hadoop软件包在不同机器之间的传递，scp适用于小文件，rsync性能优良，可适用于大型文件传输。
- 最简单的rsync文件传输命令(与scp命令类似)：rsync <源地址>:<文件路径> <目的地址>:<文件路径>
- $rsync hadoop*.tar.gz hadoop@slaveone:/home/hadoop/
- $rsync hadoop*.tar.gz hadoop@slavetwo:/home/hadoop/
- $tar -xzvf hadoop*.tar.gz  //在/usr/文件下解压文件，虽然使用root身份，但是，在不同机器上解压后，文件所有者是不一样的。
- 修改配置文件，在/etc/profile.d目录下新建hadoop.sh 
- #!/bin/bash
- HADOOP_HOME=/usr/hadoop 
- export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin 
- $source /etc/profile //使配置文件生效
#### 改变文件拥有者和群组，因为hadoop文件要存储到/usr/hadoop/路径下，但是这个文件路径只有root权限才行，但是hadoop用户访问就不方便了。
- 利用chown可以将文件的所有者加以改变，这个指令是由root使用。
- #chown [-R] [用户名称:组名称] [文件或目录]
- #chown -R hadoop:hadoop /usr/hadoop
#### 配置hadoop文件
- 首先创建几个新文件夹在/usr/hadoop目录下新建如下目录
```javascript
$mkdir /usr/hadoop/dfs     //执行过前面的命令后，就不需要root权限就可以创建目录了。
$mkdir /usr/hadoop/dfs/name    //执行hdfs namenode -format命令后，这里会有变化
$mkdir /usr/hadoop/dfs/data
$mkdir /usr/hadoop/dfs/secondname
$mkdir /usr/hadoop/mapred
$mkdir /usr/hadoop/mapred/system
$mkdir /usr/hadoop/mapred/local
$mkdir /usr/hadoop/yarn
$mkdir /usr/hadoop/tmp
```
- 修改/usr/hadoop/etc/hadoop/hadoop-env.sh文件。
- 在文件末尾添加
- set java environment.
- export JAVA_HOME=/usr/jdk
- hadoop配置文件主要有workers,core-site.xml,hdfs-site.xml,mapred-site.xml,yarn-site.xml5个配置文件。
- 之前版本中是slaves文件，hadoop3中是文件workers,将作为DataNode的主机名写入该文件，每行一个，默认为localhost，所以在伪分布式配置时，节点即作为NameNode也作为DataNode。分布式配置可以保留localhost，也可以删掉，让Master节点节点仅作为NameNode使用。
- vi workers
```javascript
slaveone//我所配置的DataNode机器的主机名
slavetwo
```
- 修改core-site.xml
```javascript
<configuration>
<property>
	<name>fs.defaultFS</name>//注意不要打错，或者打多字母，不然后面出错 
	<value>hdfs://master:9000</value> //master即为控制节点的主机名，可用IP替换即192.168.186.131
</property>
<property>
	<name>fs.default.name</name> //fs.default.name指NameNode的URL
	<value>file://master:9000</value> //这两种写法是等价的
</property>
<property>
    <name>hadoop.tmp.dir</name>
    <value>/usr/hadoop/tmp</value>
</property>
</configuration>
```
- 修改hdfs-site.xml文件
```javascript
<configuration>  
<property>  
    <name>dfs.namenode.secondary.http-address</name>//secondnamenode的URL  
    <value>master:9001</value>  
</property>  
<property>  
    <name>dfs.namenode.name.dir</name>//namenode的元数据目录，以，隔开，hdfs会把元数据冗余复制到这些目录。  
    <value>file:/usr/hadoop/dfs/name</value>  
</property>  
<property>  
    <name>dfs.datanode.data.dir</name>//datanode的数据目录，hdfs会把数据存在这些目录下  
    <value>file:/usr/hadoop/dfs/data</value>  
</property>  
<property>  
    <name>dfs.replication</name>//设置副本数量，一般要小于或等于datanode节点数，不然会出错  
    <value>2</value>//我所配置的机器有2个datanode，所以这里选2  
</property>  
<property>  
    <name>dfs.webhdfs.enabled</name>//namenode的hdfs-site.xml是必须将dfs.webhdfs.enabled属性设置为true,否则无法使用liststatus,listfilestatus等命令。  
    <value>true</value>  
</property>  
</configuration> 
```
- 修改mapred-site.xml文件
```javascript
<configuration>
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
</configuration>
```
- hadoop自带历史服务器，可以查看运行完成的mapreduce作业，默认是不启动的，可以通过sbin/mr-jobhistory-daemon.sh start historyserver来启动。
- 修改yarn-site.xml文件
```javascript
<configuration>  
<property>  
    <name>yarn.nodemanager.aux-services</name>  
    <value>mapreduce_shuffle</value>//nodemanger上运行的附属服务。需要配置成magreduce_shuffle,才能运行mapreduce程序  
</property>    
<property>  
    <name>yarn.resourcemanager.hostname</name>  
    <value>master</value>  
</property>  
</configuration> 
```
#### 运行hadoop一定要记得关闭防火墙，如果不关闭防火墙，则：
- hdfs的web管理页面，打不开该节点的文件浏览页面。
- 后台运行脚本（hive),会出现莫名其妙的假死状态。
- 再删除和增加节点的时候，会让数据前一处理时间更长，甚至不能正常完成相关操作。
- 很多操作多会运行不正常。
#### centos7关闭启动服务命令与之前有所改变，不再使用service改用systemctl. 
- #systemctl start firewalld.service //启动防火墙
- #systemctl stop firewalld.service  //停止防火墙
- #firewall-cmd --state   //查看firewalld防火墙状态
- #systemctl disable firewalld.service //禁止防火墙开机启动
- #systemctl //列出正在运行的服务状态
- #systemctl start postfix.service //启动一个服务
- #systemctl stop postfix.service //关闭一个服务
- #systemctl restart postfix.service //重启一个服务
- #systemctl status postfix.service //显示一个服务的状态
- #systemctl disable postfix.service //在开机时禁用一个服务
- #systemctl is-enabled postfix.service;echo $?//查看服务是否开机启动
- #systemctl list-unit-files|grep enabled//查看已启动的服务列表
#### 格式化与启动hadoop
- $hdfs namenode -format//初始化(只能执行一次)，用来建立hdfs结构来存元数据。如果格式化错误，删除/usr/hadoop/dfs/name/current文件夹，重新执行这条命令。
- $start-all.sh //This script is deprecated.Use start-dfs.sh and start-yarn.sh instead.
- $hdfs --daemon start namenode //单独启动namenode守护进程
- $hdfs --daemon start datanode //单独启动datanode守护进程
- $start-dfs.sh   //如果已经在/usr/hadoop/etc/hadoop/workers进行配置，并且配置ssh免密码登录，执行这一条密码，效果相当于前面两条代码。
- $yarn --daemon start resourcemanager //单独启动resourcemanager守护进程
- $yarn --daemon start nodemanager  //单独启动nodemanager守护进程
- $start-yarn.sh  //与start-dfs.sh同理。
- $jps    //显示当前所有java进程pid。在master执行后显示的结果应该是：
```javascript
xxxxx SecondaryNameNode
xxxxx Namenode
xxxxx Jps
xxxxx ResourceManager
```
- 在slave执行后显示的结果应该是:
```javascript
xxxxx DataNode
xxxxx Jps
xxxxx NodeManager
```
- $mapred --daemon start historyserver //单独启动historyserver守护进程
#### 验证Hadoop是否安装成功
- master:9870(NameNode的web页面)
- master:8088(ResourceManager的web页面)
- master:19888(MapReduce JobHistory Server的web页面)
- 如果都能查看，说明Hadoop已经安装成功。
#### 关闭hadoop。
- $stop-yarn.sh  //关闭yarn
- $stop-dfs.sh   //关闭hdfs
- 注意关闭顺序，不然会出错。
- 虽然命令执行的顺序是对的，依然出了问题。下面就来说说出现的问题，以及解决办法。
```javascript
$stop-yarn.sh
Stopping resourcemanager
Stopping nodemanagers
slavetwo:WARNING:nodemanager did not stop gracefully after 5 seconds:Trying to kill with kill -9
slaveone:WARNING:nodemanager did not stop gracefully after 5 seconds:Trying to kill with kill -9
```
- This was because resource manager is stopped before node managers, when the shutdown hook manager tries to gracefully stop NM services, NM needs to unregister with RM, and it gets timeout as NM could not connect to RM (already stopped). See log (stop RM then run kill <nm_pid>)
- 简单来说，其实很明显，resourcemanager守护进程先于nodemanagers守护进程关闭，但是nodemanagers守护需要用到resourcemanager守护进程。
- 那么解决方法就简单了，把关闭的顺序修改一下。
- vi /usr/hadoop/sbin/stop-yarn.sh
```javascript
# stop nodemanager        //这段代码原来是在# stop resourceManager后面的，现在改到前面来，就改变了守护进程的顺序。
echo "Stopping nodemanagers"
"${HADOOP_YARN_HOME}/bin/yarn" \
    --config "${HADOOP_CONF_DIR}" \
    --workers \
    --daemon stop \
    nodemanager

# stop resourceManager
HARM=$("${HADOOP_HDFS_HOME}/bin/hdfs" getconf -confKey yarn.resourcemanager.ha.enabled 2>&-)
if [[ ${HARM} = "false" ]]; then
  echo "Stopping resourcemanager"
  "${HADOOP_YARN_HOME}/bin/yarn" \
      --config "${HADOOP_CONF_DIR}" \
      --daemon stop \
      resourcemanager
else
  logicals=$("${HADOOP_HDFS_HOME}/bin/hdfs" getconf -confKey yarn.resourcemanager.ha.rm-ids 2>&-)
  logicals=${logicals//,/ }
  for id in ${logicals}
  do
      rmhost=$("${HADOOP_HDFS_HOME}/bin/hdfs" getconf -confKey "yarn.resourcemanager.hostname.${id}" 2>&-)
      RMHOSTS="${RMHOSTS} ${rmhost}"
  done
  echo "Stopping resourcemanagers on [${RMHOSTS}]"
  "${HADOOP_YARN_HOME}/bin/yarn" \
      --config "${HADOOP_CONF_DIR}" \
      --daemon stop \
      --workers \
      --hostnames "${RMHOSTS}" \
      resourcemanager
fi
```
- 重新执行一遍命令，结果：
```javascript
Stopping nodemanagers
Stopping resourcemanager
```
- 结果就不再显示WARNING了。
#### 关于这次安装hadoop有一个体会，在参考资料方面，官网上的最准确，英文网站的次之，中文网站的最差。
- 关于hadoop 3.0.0-alpha1最详细文档(英文的),hadoop.apache.org/docs/r3.0.0-alpha1/。
- 还有关于寻求帮助，在hadoop.apache.org，左侧菜单栏Issue Tracking(问题追踪)，选择问题分类，之后再搜索框中搜索自己遇到的问题就可以了。
- 现在hadoop是安装好了，并且我也学到了一些解决问题的方法，下一步就是如何把hadoop用起来，以后有时间再弄。