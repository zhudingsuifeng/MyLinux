## docker
### Install docker for windows
- 安装Docker for Windows就可以在windows下使用docker了。
- 注意要开启微软虚拟化hyper-V,home+x->程序和功能->启用或关闭windows功能->hyper-V
- 但是这样存在一个问题，就是windows对要使用的linux镜像不友好，会报错，而windows镜像就没有问题。
- 就是switch windows containers时正常，switch linux containers时报错。
- Install docker toolbox通过使用virtualbox来实现虚拟化，这里要关闭hyper-V,而且一点要勾选git，尽管你可能已经安装过git，要不然不能正常运行docker quick start terminal.
### Install docker for ubuntu
```javascript
#apt install apt-transport-https ca-certificates curl software-properties-common
#curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
#add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
#apt update
#apt install docker-ce       #install docker
#service docker start
#docker run -it ubuntu bash  #centos is different from ubuntu
```
### Install docker for centos
```javascript
#yum install docker       #install docker
#service docker start     #start docker service
#docker version           #Show the Docker version information
#docker info              #Display system-wide information
#docker images            #List images
#docker [xxx] --help      #Get help information about xxx
#docker search [xxx]      #Search the Docker Hub for images
#docker pull [xxx]        #Pull an image or a repository from a registry
#docker run -it --name cnetos7 centos /bin/bash   #Run a command in a new container
#docker create IMAGE      #create a new container
#docker start [XXX]       #start a containet already exists
#docker run IMAGE         #run a command in a new container
#docker attach [XXX]      #attach local standard input,output,and error streams to a running container
#docker exec CONTAINER COMMAND #run a command in a running container
#exec is faster to attach but is a new terminal interface
#docker start -i [xxx]    #start a container already exists with standard io
#docker stats             #display a live stream of container resource usage statistics
#docker rm CONTAINER      #remove one or more containers
#docker ps -q             #return running containers ID
#docker ps -a -q          #return all containers ID
#docker rmi CONTAINER     #remove one or more containers
#docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG] #create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
#docker inspect NAME|ID   #return low-level information on Docker objects
#docker history IMAGE     #show the history of an image
#docker network ls        #list networks
#docker network inspect NETWORK    #display detailed information on one or more networks
#docker network create NETWORK     #create a network
#docker run -v /webapp ubuntu #mount a volume from localhost /var/lib/docker/volumes to container /webapp
#docker run -v /web:/webapp ubuntu #mount a volume from localhost /web to container /webapp
#docker run -v 宿主机目录:容器目录    #需要注意，目录所属的用户，必须是容器中正在执行你程序的用户，通常是root用户，不然文件会拒绝访问。
#docker run -p HostPort:ContainerPort           #publish a container port to the host
#docker run -p IP:HostPort:ContainerPort        #映射指定IP的端口到容器端口
#docker run -p IP::ContainerPort 
#docker port              #list port mappings or a specific mapping for the container
#yum -y install epel-release #install expansion source
#yum install python2-pip  #install pip
#pip install --upgrade pip #upgrade pip
#pip install numpy
#pip install pandas
#pip install matplotlib   #error:command 'gcc' failed with exit status 1.Missing modules
#yum install python-matplotlib #install successful
#pip install numpy==1.13.3 #install package specified version
#docker build -t <hub-user>/<repo-name>[:<tag>] #name local images when build it
#docker tag <existing-image> <hub-user>/<repo-name>[:<tag>] #re-tagging an existing local image
#docker commit <exiting-container> <hub-user>/<repo-name>[:<tag>] 
#generate an image from the container,and commit the changes
#docker push <hub-user>/<repo-name>:<tag>
#docker push [OPTIONS] NAME[:TAG]  #push an image or a repository to a registry
#docker build -t="zhudingsuifeng/centos" git@github.com:zhudingsuifeng/docker
#NOTE:docker is a directory not a file,and Dockerfile is in this directory.
#docker inspect [xxx]     #Return low-level information on container or image [xxx]
#docker build [OPTIONS] PATH | URL|-  #Build an image from a Dockerfile
#docker build -t="zhudingsuifeng/centos" #build an images from the current directory of the Dockerfile
#named centos inside the repository zhudingsuifeng
#docker port CONTAINER   #list port mappings or a specific mapping for the container
#tree     #list contents of directories in a tree-like format.
#tree /var/lib/docker    #list contents of docker
#yum install tree
```
### CMD ENTRYPOINT and RUN
- RUN指令是设置编译镜像时执行的脚本和程序，镜像编译完成，RUN指令的生命周期结束。
- CMD叫作容器默认启动命令，若在docker run命令结尾添加command，则可以替换镜像中CMD设置的启动程序。
- ENTRYPOINT叫做入口程序，不能被docker run命令末尾的command替换。
- docker run命令末尾的command会被当作字符，传递给ENTRYPOINT，作为参数。
- 在Dockerfile中，至少有一条CMD或ENTRYPOINT指令。
```javascript
FROM ubuntu
CMD ["echo","I am default CMD."]
#docker build -t test .
启动容器时，在docker run 末尾不添加command，运行镜像中CMD设置的启动程序。
#docker run --rm test
->I am default CMD.
#docker run --rm test echo "I am from docker run."
->I am from docker run.
FROM ubuntu
ENTRYPOINT ["ps"]
#docker build -t test .
#docker run -rm test
->PID TTY      TIME  CMD
  1    ?    00:00:00 ps
#docker run --rm test -ef
->UID   PID   PPID   C   STIME  TTY   TIME    CMD
  root  1     0    0     03:56  ?    00:00:00 ps -ef
FROM ubuntu
ENTRYPOINT ["ps"]
CMD ["-ef"]
#docker build -t test .
#docker run --rm test
->UID   PID   PPID   C   STIME  TTY   TIME    CMD
  root  1     0    0     03:56  ?    00:00:00 ps -ef
```
- lsof -i:port \#查看port被哪些进程占用。
- 由于缓存机制，一定要在一条RUN指令中执行apt-get update && apt-get install,不然可能安装失败。
- 详细的使用说明文档在[here](https://docs.docker.com)
- 通过\#docker commit 来创建image在push的时候非常缓慢，不知道是不是国内网络对于dockerhub墙的原因。
- docker pull centos #在学校的时候速度挺快，但是在家里速度就很慢了。
- 推荐使用docker build命令和Dockerfile文件来创建image,并且只把Dockerfile文件保存在github中。
- 需要生成image的时候，可以通过github和dockerhub授权，自动构建。
