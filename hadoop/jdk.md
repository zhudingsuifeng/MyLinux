## Centos7纯命令行安装jdk8和软件版本迁移
### 注意，centos下载文件，使用不同用户复制解压之后，容易出现用户权限问题，所以尽量下载的用户和解压的用户是同一个人
#### Centos7命令行安装jdk8
```javascript
# wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u121-b13/e9e7ea248e2c4826b92b3f075a80e441/jdk-8u121-linux-x64.tar.gz"
# tar -xzvf jdk-8u121-linux-x64.tar.gz
```
- 首先下载安装包，然后解压。
#### alternatives版本迁移
- alternatives常用于同一个系统中安装同一个软件的多个版本。执行这个命令需要root权限。
- #alternatives --install <link> <name> <path> <priority>
- install表示安装
- link是符号链接
- name是标识符
- path是执行文件的路径
- priority表示优先级
- #alternatives --install /usr/bin/java java /usr/jdk/bin/java 1
- #alternatives --install /usr/bin/jar jar /usr/jdk/bin/jar 1
- #alternatives --install /usr/bin/javac javac /usr/jdk/bin/javac 1
- #alternatives --remove name path 
- 删除链接组中还有其他链接的话，系统自动将优先级高的链接作为默认链接。
- #alternatives --auto name 
- 转换auto和manual模式，默认为auto模式。
- #alternatives --config name 
- 在现有命令链接中选择一个作为系统默认的。
- #alternatives --display name
- 查看一个命令链接组的所有信息，包括连接模式、链接优先级、所有可用链接等。
#### 安装的时候总会出现错误，比如路径错误，他不会提示错误，但是在使用的时候会找不到，还不能通过命令修改。好在Linux的配置是以文件的形式存在的，直接删除配置文件就可以了。
- /var/lib/alternatives/xxx    //配置文件目录
- 删除配置文件，重新执行上面的配置命令就可以了。
