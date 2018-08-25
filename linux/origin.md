## building my ubuntu from scratch
### make a multi-system U disk boot disk.
- using multibootusb software could make a multi-system boot disk easily.
### hp BIOS
- you should press the Esc to enter BIOS when the computer is power.
- If the HP computer monitor is black after booting up,perhaps because it is an integrated graphics card,not a discrete graphics card.
### dpkg install teamviewer
```javascript
dpkg -i *.deb      #installing *.deb app use dpkg
apt -f -y install  #save the dependency problem with *.deb
dpkg -l |grep *software  #list the software version
dpkg -L package    #list the dependency file about the installed software
dpkg -P *software name  #uninstall software that install by dpkg not save the configuration
dpkg -r *software name  #uninstall softwave that install by dpkg save the configuration
dpkg -s package    #search the package information
```
- set unattended
### 搜狗拼音shift中英文切换问题
- 有点bug，这里settings设置，语言，需要添加键盘-英语(美国)，并把搜狗拼音放在第二个，这样就不会再输入shift组合键的时候，自动切换输入法了。
- 还是存在一些问题，不知道是不是机械键盘太灵敏的缘故。时好时坏。也有可能是远程连接的问题，直接使用本地键盘，完全没有问题。
### vim 
- In vi insert mode,can not delete char using backspace.
- sudo vi /etc/vim/vimrc.tiny
- add 
```javascript
set nocompatible 
\"setting compatibility
set backspace=2  
\"backspace could remove any char
```
- it represents the comment after the \"
- set vim
```javascript
set nu    #set line number
set ts=4  #set tab = 4 space
```
### oh my zsh
```javascript
sudo su
apt install zsh
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
chsh -s /bin/zsh
sudo reboot
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
- nvidia cuda
```javascript
$lspci |grep -i nvidia    #verify the cuda functionality of the gpu
01:00.0 VGA compatible controller: NVIDIA corporation GM107[Geforce GTX 745]
$uname -m && cat /etc/*release     #x86_64  your os is 64bit
$nvidia-smi       #view the nvidia information
zsh:command not found:nvidia-smi
#apt install nvidia-utils-390
#ubuntu-drivers autoinstall
$gcc --version    #check your gcc version
$g++ --version    #check your g++ version 
#apt install gcc  #install gcc (default gcc7)
#apt install g++  #install g++ (default g++7)
#download the CUDA Toolkit with corresponding release.(deb[local])
$md5sum <file>    #md5 verify download file
comparison check code from https://developer.download.nvidia.com/compute/cuda/9.2/Prod2/docs/sidebar/md5sum-c.txt
#dpkg -i cuda-repo-ubuntu1604-9-2-local_9.2.148-1_amd64.deb
#apt-key add /var/cuda-repo-9-2-local/7fa2af80.pub
#apt update
#apt install cuda 
```
- docker
- tensorflow

