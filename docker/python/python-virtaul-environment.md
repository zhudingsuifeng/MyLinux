## Some considerations about virtual environment
### virtualenv
- python的虚拟环境可以使一个python程序拥有独立的库library和解释器interpreter，而不用与其他python程序共享一个library和interpreter。虚拟环境的好处是避免了不同python程序间的互相影响，例如两个程序依赖某个库的不同版本，这两个程序因为共享库，只能有一个版本，就不能同时执行两个程序了。
- 就我的理解，python虚拟环境是针对项目的，有点像容器的作用，隔离执行环境。平常在遇到使用python2还是python3的时候，可以使用脚本#!/usr/bin/env 指定解释器，但是同样使用python3版本，但是对第三方的包依赖不同版本就不能很好解决。所以，可以给每一个项目单独使用一个python虚拟环境，可以认为是轻量级的针对python的容器。
- virtualenv为应用程序提供了隔离的python运行环境，解决了不同应用间多版本的冲突问题。
- install and use virtualenv
```javascript
$pip install virtualenv   # install virtualenv
$virtualenv --version     # view app version
$virtualenv venv       # build a virtual envrionment which named venv
# venv是新创建的虚拟环境的名称。同时会在当前目录下创建一个与虚拟环境名称相同的文件夹。
$virtualenv -p /usr/bin/python3 --no-site-package venv
# -p 指定解释器路径，可以指定虚拟环境的python版本。
# --no-site-packates,已经安装到系统python环境的第三方包不会复制过来，得到不带任何第三方包的“干净”的python运行环境。
$source venv/bin/activate  #activate script (venv)
# 通过source命令进入虚拟环境，注意到命令提示符有所变化，前面多了一个(venv)，表示当前环境是一个名为venv的python环境。
(venv)->venv
$pip install django   #install app
# 在venv环境下，用pip安装的包都被安装到venv这个环境下，系统python环境不受任何影响。
$deactivate   #deactivate script
# 在项目打包移植时可以抛弃虚拟环境(一般很大)，利用命令生成项目所引用的第三方包，
# 在项目部署时新建虚拟环境，重新下载这些第三方包即可。
$pip list     #查看当前环境中安装的第三方库
$pip freeze > requirements.txt  #导出当前环境第三方库信息
$pip install -r requirements.txt    #移植机器重新安装第三方库
$rm -r venv   #delete virtual envrionment
# 直接删除虚拟环境所在的文件夹venv就删除了我们创建的venv虚拟环境。
```
- 当需要删除虚拟环境时，需要删除他的文件夹rm - rf venv。这里使用virtualenv有些不方便，因为virtualenv的启动，停止脚本都在特定的文件夹，可能一段时间后，可能有很多个虚拟环境散落在系统各处，可能早已忘记了虚拟环境的名字和位置。
### install and use virtualenvwrapper

### venv
- venv是python3.3之后的版本中自带的构建虚拟环境的包，存了替代virtualenv的心思，使用方法也和virtualenv类似，但是只能支持python3.3之后的版本，并且不够灵活，有一定的局限性。
```javascript
#apt install python3-venv
$python -m venv .  # build a virtual environment in current directory
$source bin/activate   # activate script
(tes)->test        # 使用方面和virtualenv类似，但是没发现指定python版本的命令
$deactivate        # deactivate script
$rm -rf test       # remove the virtual environment
$python -h venv    # show help message and exit
```
### pyenv
```javascript
# ubuntu requirements
#apt install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffidev liblzma-dev
#apt install git   # install git
$git clone https://github.com/yyuu/pyenv.git ~/.pyenv
#add PYENV_ROOT and pyenv init to ~/.bashrc or ~/.zshrc
echo 'export PATH=~/.pyenv/bin:$PATH' >> ~/.bashrc
echo 'export PYENV_ROOT=~/.pyenv' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' .. ~/.bashrc
#or use the vim to edit ~/.bashrc or ~/.zshrc
export PATH=~/.pyenv/bin:$PATH
export PYENV_ROOT=~/.pyenv
eval "$(pyenv init -)"
source ~/.bashrc or source ~/.zshrc #active the script
pyenv install --list             # list versions which could install 
pyenv install <version>          # install app with version
pyenv install -v <version>       # install app with version and view error
pyenv versions                   # view the python version
pyenv which python               # view the path of python 
pyenv global <version>           # set the default version
pyenv local <version>            # set the local python version
# 在当前路径创建一个本地python版本，当进入这个目录后自动切换为该版本
pyenv shell <version>        
ERROR: The Python zlib extension was not compiled. Missing the zlib?
Please consult to the Wiki page to fix the problem.
httERROR: The Python ssl extension was not compiled. Missing the OpenSSL lib?
Please consult to the Wiki page to fix the problem.
https://github.com/yyuu/pyenv/wiki/Common-build-problems
# 这是因为前面依赖的包没有安装好。
pyenv install -v 3.6.0 
pyenv versions
  system
* 3.6.0 (set by /home/fly/test/.python-version)
* 表示当前激活版本，后面的括号里面表示这个版本是由哪条途径激活的(global,local,shell)
pyenv local 3.6.0
pyenv rehash   #刷新以后才能显示设置的效果
pyenv uninstall 3.6.0  #卸载python版本
pyenv: remove /home/fly/.pyenv/versions/3.6.0? yes
#安装3.6.0版本成功，其他版本失败，另外安装时候挺慢的，感觉有点麻烦。
$rm -rf $(pyenv root)   # uninstall pyenv 
```
- pyenv是第三方的、开源的多版本python管理工具，用以管理在一台机器上多个python版本的共存问题。
- venv 模块是python3.3之后标准库自带的虚拟环境创建和管理工具，在一定程度上能够替代virtualenv。但是venv是python3.3之后才有的，python2.x无法使用，而virtualenv可以同时支持python2.x和python3.x。 
