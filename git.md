## 说说使用github的一些基本命令和技巧
#### 本地安装git：
- #yum install git  #centos7安装git
#### 链接github：
- 登录github,新建仓库(远程的仓库是无法在本地直接建立的)。
- 并且，这个仓库要是空的，不然无法push,他会提示远程仓库有本地仓库没有的提交内容，就是不一致。
- 要连接到github必须配置ssh：
- ssh-keygen -t rsa    #这里注意会提示存储位置，默认就好了.
- Enter passphrase (empty for no passphrase): 还会出现这个提示，填写访问密码，空着就是没有密码，这里需要空着，直接回车.
- 登录github，设置ssh keys,将~/.ssh/id_rsa.pub中的内容复制到key中.
- 用户头像->Settings->SSH and GPG keys->New SSH key->给SSH取个名字，将~/.ssh/id_rsa.pub中的内容复制到key中。
- ssh git@github.com
- 显示Hi zhudingsufieng!You've successfully authenticated,but GitHub does not provide shell access.Connection to github.com closed.则ssh连接成功。
- git init   #在本地建立一个目录，之后git init初始化仓库。
- git add README.md
- git commit -m "networkx"    #向本地分支提交内容。
- 新建立的仓库，第一次执行commit操作，可能遇到如下错误：
```javascript
*** Please tell me who you are.
Run
	git config --global user.email "you@example.com"
	git config --global user.name "Your Name"
to set your account’s default identity. #并没有markdown的转义字符，而是使用中文引号代替的。
```
- 按照提示配置用户默认信息。
```javascript
git config --global user.email "1002557401@qq.com"
git config --global user.name "zhudingsuifeng"
git add README.md
git commit -m "networkx"   #在次执行的时候，就不会报错了。
```
- git remote add origin git@github.com:zhudingsuifeng/mynetworkx.git #添加远程仓库和本地仓库的关联
- git push -u origin master    #向远程master分支推送内容。
#### 克隆GitHub项目到本地：
- git clone git@github.com:/zhudingsuifeng/mygit.git
#### git简单用法
```javascript
git add filename               #添加文件
git commit -m "some words"     #向本地分支提交内容
git push -u origin master      #向远程主分支提交内容
git pull                       #同步远程仓库到本地
```
#### git分支相关操作
```javascript
git branch                     #列出本地分支
git branch -r                  #列出远端分支
git branch -a                  #列出所有分支
git branch -v                  #查看各个分支最后一个提交对象的信息
git branch --merge             #查看已经合并到当前分支的分支
git branch --no-merge          #查看为合并到当前分支的分支
git branch manual              #新建manual分支
git checkout manual            #切换到manual分支
git checkout -b manual         #新建+切换到manual分支
git checkout -b manual dev     #基于dev新建manual分支，并切换
git branch -d manual           #删除manual分支(只是删除本地分支，github上分支还存在)
git push origin -d manual      #这样删除之后，GitHub上的远程分支manual就被删除掉了，这个删除和本地是独立的，如果只删除远程分支，本地分支还在
git branch -D manual           #强制删除manual分支
git merge manual               #将manual分支合并到当前分支(只是合并本地分支)
git push origin branchname     #将分支发布在github上
git push -u origin master      #push之后，在github上才能看到合并之后的结果
git rebase master              #将master分之上超前的提交，变基到当前分支
```
#### git push -u origin master出现The authenticity of host 'github.com' can’t be established问题的原因：
- github上没有与本地仓库相关联。(重新remote)
- ssh不对。(多数情况)
#### github的https换为ssh
- git remote -v  #查看当前地址
```javascript
$git remote -v
origin https://github.com/zhudingsuifeng/mynetworkx.git(fetch)
origin https://github.com/zhudingsuifeng/mynetworkx.git(push)
```
- 设置为ssh地址：
```javascript
$git remote set-url origin git@github.com:/zhudingsuifeng/mynetworkx.git
$git remote -v
origin git@github.com:/zhudingsuifeng/mynetworkx.git(fetech)
origin git@github.com:/zhudingsuifeng/mynetworkx.git(push)
```
#### github遇到permanently added the RSA host key for IP address '192.30.255.112'to the list of known host.
- 其实这就是一个警告，不影响使用，就是看着不舒服。
- 在Linux环境下：
- vi /etc/hosts 
- 添加一行：192.30.255.112  github.com
- 再次执行命令，警告就没有了。
- 在windows环境下，我试了很多方法，都没有成功。
