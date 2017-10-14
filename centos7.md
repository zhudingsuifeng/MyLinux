## cnetos7
### Correct grub configuration
```javascript
/usr/lib/grub/i386-pc       #There are only i386-pc directory under the grub
#ls /boot
config-3.10.0-514.e17.x86_64
config-3.10.0-693.2.2.e17.x86_64
efi
grub
grub2
initramfs-0-rescue-......img
initramfs-3.10.0-514.e17.x86_64.img 
initramfs-3.10.0-514.e17.x86_64kdump.img 
initramfs-3.10.0-693.2.2.e17.x86_64.img
initramfs-3.10.0-693.2.2.e17.x86_64kdump.img
initrd-plymouth.img 
symvers-3.10.0-514.el7.x86_64.gz 
symvers-3.10.0-693.2.2.el7.x86_64.gz
System.map-3.10.0-514.el7.x86_64
System.map-3.10.0-693.2.2.el7.x86_64
vmlinuz-0.....
vmlinuz-3.10.0-514.......
vmlinuz-3.10.0-693......
ls /boot/efi/EFI/    #centos 
ls /boot/efi/EFI/centos    #is empty
ls /boot/grub/        #splash.xpm.gz 
ls /boot/grub2/       
device.mp     grub.cfg     grubenv     locale   fonts  grub.cfg....rpmsave   i386-pc
/myboot   #backup
``` 
### install teamviewer12 on centos7
- download the teamviewer12 rpm package.
- visit the https://www.teamviewer.com/en/download/linux/
- install teamviewer12 rpm
- rpm -ivh teamviewer12 package
- error:Failed dependencies:...
- yum install teamviewer12 rpm #error solve
- yum will install depended package automatically.
### support to chinese
- choose the chinese when install the centos7
- upper left corner->Applications->System tools->Settings->Region&language
- Language ->English(United States)
- Formats ->United States(English)
- Input Sources ->Chinese(Intelligent Pinyin)
- 这样就可以在显示英文的系统中输入中文。  
![Input Sources](image/input.png)
- Input Sources ->Options->Allow different sources for each window.  
![Input Sources](image/input1.png)
### hostname
- hostnamectl status    #get the status
- hostname   #return the hostnamec
- hostnamectl set-hostname localhost   #set hostname as localhost immediately.
### git
- yum install git
- ssh-keygen -t rsa  #produce ssh-key
- copy ~/.ssh/id_rsa.pub to SSH and GPG keys
- ssh git@github.com 
- Hi zhudingsuifeng!You've successfully authenticated,but GitHub does not provide shell access.
- Connection to github.com closed.
- git clone git@github.com:zhudingsuifeng/shell.git #clone remote repository
- git clone git@github.com:zhudingsuifeng/shell.git shell #clone remote repository to local address shell 
### python
```javascript
#yum install python-pip #error:no package python-pip available.
#yum -y install epel-release  #install epel-release
#yum -y install python-pip   #install successfully
#pip install somepackage  #install package from PyPi
#pip uninstall package   #uninstall package
#python2 -m pip install package #install package to python2
#### Install python and packages without root permission. ####
$wget https://www.python.org/ftp/python/2.7.3/Python-2.7.3.tgz  #download the python2.7.3.tgz 
$tar -xzvf Python-2.7.3.tgz  
$cd Python-2.7.3
$./configure --prefix='/data/home/xjsjxly/fly/python/'   #install directory is /data/home/xjsjxly/fly/python/,--prefix specify the installation path.
$make install
#### modify environment variables ####
export PATH=$PATH:/data/home/fly/python/bin:   #effective temporarily
vim ~/.bash_profile 
PATH=$PATH:$HOME/.local/bin:/data/home/fly/python/bin:   #only effective for current user,forever.
vi /etc/profile
PATH=$PATH:/data/home/fly/python/bin:
export PATH     #effective for all users forever.
#### The configure takes effect immediately. ####
..bash_profile
source .bash_profile
exec bash --login
#### Install python package ####
#pip install opencv-python(3.3.0.10)
#pip install opencv_contrib_python
#pip install numpy(1.13.1)
#pip install pandas(0.20.3)
#pip install PIL(4.2.1)
#yum -y install tkinter   #if not ,ImportError:No module named Tkinter.
#之后tkinter模块安装完毕，就不会出现之前的错误了。
#pip install matplotlib    #在centos7下执行安装会报错error:command 'gcc' failed with exit status 1
#缺少一些模块(libffi-devel python-devel openssl-devel)，执行安装之后就可以了
#yum install gcc libffi-devel python-devel openssl-devel
#pip install scikit-image(0.13.0)
#pip install matplotlib(2.0.2)
#### install setuptools ####
Before installing pip ,we should install setuptools ,because of depend.
wget --no-check-certificate https://pypi.python.org/packages/45/29/8814bf414e7cd1031e1a3c8a4169218376e284ea2553cc0822a6ea1c2d78/setuptools-36.6.0.zip#md5=74663b15117d9a2cc5295d76011e6fd1
unzip setuptools-36.6.0.zip
cd setuptools-36.6.0
/data/home/xjsjxly/fly/python/bin/python2.7 setup.py install 
#### install pip ####
wget https://pypi.python.org/packages/11/b6/abcb525026a4be042b486df43905d6893fb04f05aac21c32c638e939e447/pip-9.0.1.tar.gz#md5=35f01da33009719497f01a4ba69d63c9
ERROR:certificate common name "www.python.org" doesn't match requested host name "pypi.python.org".
To connect to pypi.python.org insecurely,use '--no-check-certificate'.
wget --no-check-certificate https://pypi.python.org/packages/11/b6/abcb525026a4be042b486df43905d6893fb04f05aac21c32c638e939e447/pip-9.0.1.tar.gz#md5=35f01da33009719497f01a4ba69d63c9
tar -xzvf pip-9.0.1.tar.gz
cd pip-9.0.1
/data/home/xjsjxly/fly/python/bin/python2.7 setup.py install
#### pip install package without root permission for python youself####
$python2.7 -m pip install networkx
$python2.7 -m pip install opencv-python(3.3.0.10)
$python2.7 -m pip install numpy(1.13.1)
$python2.7 -m pip install pandas(0.20.3)
$python2.7 -m pip install PIL(4.2.1)
$python2.7 -m pip install scikit-image(0.13.0)
$python2.7 -m pip install matplotlib(2.0.2)
#### scp file transfer ####
#copy file to remote directory
#scp filename username@ip:/directory
scp changefile.csv xjsjxly@sc.shu.edu.cn:/data/home/xjsjxly/fly
#copy directory to remote directory
#scp -r directory username@ip:/directory
scp -r data xjsjxly@sc.shu.edu.cn:/data/home/xjsjxly/fly
#### version problem ####
Pay attention to the version of the package, or return some inexplicable error.
``` 