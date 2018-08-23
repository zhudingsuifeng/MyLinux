## ssh
### How to connect the container through ssh.
#### ip
- host ip : 192.168.1.6     \#ifconfig  ->eno1
- docker ip : 172.17.0.1    \#ifconfig  ->docker0 is a bridge of host and container
- container ip : 172.17.0.2 \#docker inspect sshd
#### ssh
```javascript
#using ssh in ubuntu environment
#首先有一个前提，想要远程登录一台主机，必须使用这台主机上已经存在的用户才能登录。
#第二，如果远程主机用户没有配置登录密码，你是登录不上的，会显示如下错误：
#Permission denied,please try again.
#这个时候可以通过把本地id_rsa.pub公钥添加到远程主机authorized_keys,就可以绕过密码登录。
#apt-get update && apt-get install openssh-server openssh-client
#openssh-client use to login openssh-server by ssh username@ip
#service ssh start                      #start openssh-server
#ps -e |grep ssh
36 ?   00:00:00 sshd
$ssh fly@192.168.1.6 -p 10022           #ssh username@ip -p port 默认是当前用户和22端口
#输入密码就可以登录，还有面密码登录的方法后面介绍
#ssh-keygen -t rsa
#在/root/.ssh/中产生id_rsa id_rsa.pub文件
#如果想要以root身份免密码登录，就需要把id_rsa.pub文件的内容复制到将要登录的机器的authorized_keys
#ssh-copy-id -i ~/.ssh/id_rsa.pub user@server #-i: 指定公钥文件
#把本地的ssh公钥文件安装到远程主机对应账户下。
$ssh-keygen -t rsa
#在/home/fly/.ssh中产生id_rsa id_rsa.pub文件，不同身份产生的秘钥文件是不能混用的。
#另外，ssh默认是不允许使用root用户登录的，需要对将要登录主机的/etc/ssh/sshd_config文件做如下修改：
PermitRootLogin prohibit-password  -> PermitRootLogin yes
UsePAM yes  -> UsePAM no
#做完这些就可以以root无密码登录远程主机了。
#ssh经常性的有一个错误WARNING:REMOTE HOST IDENTIFICATION HAS CHANGED!
#只需要删除对应用户目录下的.ssh/known_hosts文件就可以了。
```
#### commit
```javascript
host#docker run -it -p 10022:22 --name sshd ubuntu:16.04 /bin/bash
container#apt-get update
container#apt-get install -y openssh-server
container#apt-get install -y vim
container#ssh-keygen -t rsa
container#vi /etc/ssh/sshd_config
PermitRootLogin prohibit-password  -> PermitRootLogin yes
UsePAM yes  -> UsePAM no
host#docker cp SRC_PATH CONTAINER:DEST_PATH
#copy file/folders between a container and the local filesystem.
host#docker cp /root/.ssh/id_rsa.pub sshd:/root/.ssh/authorized_keys
#or host#ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.1.6 -p 10022
container#service ssh start
#本地ip就是192.168.1.6，docker把这宿主机的10022端口映射到了container的22端口。
host#ssh 192.168.1.6 -p 10022      #success
container#exit
host#docker ps -a
CONTAINER ID    IMAGE     COMMAND      CREATED   STATUS  PORTS  NAMES
14ce535fe24f   ubuntu:16.04 "/bin/bash"                         sshd
host#docker commit 14ce ubuntu:sshd
host#docker images
REPOSITORY   TAG   IMAGE ID   CREATED   SIZE
ubuntu       sshd  f91548bda780         240MB
host#docker run -d -p 10022:22 --rm ubuntu:sshd /usr/sbin/sshd -D
#这里就完成了所有工作，可以使用ssh远程登录了。
#docker tag ubuntu:sshd zhudingsuifeng/ubuntu:sshd 
#docker login
#docker push zhudingsuifeng/ubuntu:sshd
#后续操作就把制作好的镜像上传到dockerhub了，以备以后使用，上传的速度有点慢。
```
#### Dockerfile
- 构建ssh镜像，需要Dockerfile和authorized_keys两个文件，下面介绍文件内容。
```javascript
#authorized_keys
$cp /home/fly/.ssh/id_rsa.pub /home/fly/Mygit/docker/sshd_ubuntu/authorized_keys
#cat /root/.ssh/id_rsa.pub >>/home/fly/Mygit/docker/sshd_ubuntu/authorized_keys
#Dockerfile
FROM ubuntu:16.04
MAINTAINER fly 1002557401@qq.com
RUN apt-get update && apt-get install -y openssh-server
RUN mkdir -p /root/.ssh
RUN mkdir -p /var/run/sshd    #构建这个目录是为了不报错，具体现在还不太懂。
RUN sed -ri 's/PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
RUN sed -ri 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config
ADD authorized_keys /root/.ssh/authorized_keys
EXPOSE 22
CMD ["/usr/sbin/sshd","-D"]
#docker run -d -p 10022:22 --rm zhudingsuifeng/ubuntu:sshd2
sed -ri 's/stringa/stringb/g' file 
#-r 支持扩展表达式 -i直接修改文件内容 's///g' 以stringb替换stringa，以上操作是对file进行的。
```
