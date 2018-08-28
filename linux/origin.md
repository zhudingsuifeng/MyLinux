## building my ubuntu from scratch
### make a multi-system U disk boot disk.
- using multibootusb software could make a multi-system boot disk easily.
### hp BIOS
- you should press the Esc to enter BIOS when the computer is power.
- If the HP computer monitor is black after booting up,perhaps because it is an integrated graphics card,not a discrete graphics card.
### dpkg install teamviewer
```javascript
#dpkg -i *.deb      #installing *.deb app use dpkg
#apt -f -y install  #save the dependency problem with *.deb
#dpkg -l |grep *software  #list the software version
#dpkg -L package    #list the dependency file about the installed software
#dpkg -P *software name  #uninstall software that install by dpkg not save the configuration
#dpkg -r *software name  #uninstall softwave that install by dpkg save the configuration
#dpkg -s package    #search the package information
```
- set unattended
### 搜狗拼音shift中英文切换问题
- 有点bug，这里settings设置，语言，需要添加键盘-英语(美国)，并把搜狗拼音放在第二个，这样就不会再输入shift组合键的时候，自动切换输入法了。
- 还是存在一些问题，不知道是不是机械键盘太灵敏的缘故。时好时坏。也有可能是远程连接的问题，直接使用本地键盘，完全没有问题。
### vim 
- In vi insert mode,can not delete char using backspace.
```javascript
sudo vi /etc/vim/vimrc.tiny
add the following code in the file 
comments in the vimrc.tiny begin with '"'
set nocompatible #setting compatibility
set backspace=2  #backspace could remove any char
set nu    #set line number
set ts=4  #set tab = 4 space
```
### oh my zsh
```javascript
$sudo su
#apt install zsh
$sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"  
#install theme to current shell
$chsh -s /bin/zsh         #change the current shell
$sudo reboot
```
- git
```javascript
sudo su
apt install git
ssh-keygen -t rsa
add ~/.ssh/id_rsa.pub to SSH and GPG keys
ssh git@github.com
git clone git@github.com:zhudingsuifeng/*.git
git push -u origin master
*** Please tell me who you are.
Run
	git config user.email \"1002557401@qq.com\"
	git config user.name \"zhudingsuifeng\"
to set your account\'s default identity.
```
- Install nvidia390 driver,cuda toolit9.1 and cudnn7.1
```javascript
#apt update
#apt upgrade
$nvidia-smi       #view the nvidia information.If the driver information is displayed normally,you do not need to install it again.
#apt install nvidia-utils-390    #install nvidia driver manually
#ubuntu-drivers devices    #view drivers with ubuntu
#ubuntu-drivers autoinstall     #autoinstall drivers recommand
$gcc --version    #check your gcc version
$g++ --version    #check your g++ version 
#apt install gcc  #install gcc (default gcc7)
#apt install g++  #install g++ (default g++7)
#apt install gcc-5 g++-5  #install software with release
#update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 60 --slave /usr/bin/g++ g++ /usr/bin/g++-5   #modify software version priority. 
#apt install nvidia-cuda-toolkit   #install cuda9.1 toolkit in ubuntu18 default.
$nvcc -V     #information cuda version,if cuda9.1 is normal ,you do not have to install it manually.
Download and install cudnn (cudnn-9.1-linux-x64-v7.1.tgz),you need register and log in.
$cd Downloads
$tar -zxf cudnn-9.1-linux-x64-v7.1.tgz
$cd cuda
$mkdir -p lib/x86_64-linux-gnu
$mv lib64/* lib/x86_64-linux-gnu
$rm -rvf lib64
#rsync -avp ./ /usr
$nvcc -V     #information cuda version,if cuda9.1 is normal ,you do not have to install it manually.
#download the CUDA Toolkit with corresponding release.(deb[local])
$md5sum <file>    #md5 verify download file
comparison check code from https://developer.download.nvidia.com/compute/cuda/9.2/Prod2/docs/sidebar/md5sum-c.txt
#dpkg -i cuda-repo-ubuntu1604-9-2-local_9.2.148-1_amd64.deb
#apt-key add /var/cuda-repo-9-2-local/7fa2af80.pub
#apt update
#apt install cuda 
```
### Install tensorflow with pip3
```javascript
#apt install python3-pip
Download and install tensorflow from https://github.com/mind/wheels/releases/
#pip3 install tesorflow-1.8.0-cp36-cp36m-linux_x86_64.whl  #install tensorflow with pip3 and python3.6.5
#verify the installation is correct
$python3
import tensorflow as tf
hello=tf.constant('hello,tensorflow')
sess=tf.Session()     #maybe need many time
print(sess.run(hello))
>>>hello,tensorflow
```
### Install tensorflow with docker
```javascript
#groupadd docker              #add group
#usermod -aG docker $USER     #add user to group
$groups                       #display the group to which the user belongs.
#apt install docker.io     #install docker default version of ubuntu18.04
#apt remove docker docker-engine docker-ce docker.io  #remove docker install before
#apt update
#apt install -y apt-transport-https ca-certificates curl software-properties-common
#curl -fsSL https://download.docker.com/linux/ubuntu/gpg |apt-key add -
#add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
#apt update
#apt install docker-ce
$docker run hello-world
$docker --version         #information of the docker version
Docker version 18.06.1-ce
#apt install -y nvidia-docker2
#reboot
$nvidia-docker run --name tensorflow -it tensorflow/tensorflow:latest-gpu bash         #start tensorflow docker with nvidia support
#pip3 default python3.6 ,nvidia-docker default python2.7
```
