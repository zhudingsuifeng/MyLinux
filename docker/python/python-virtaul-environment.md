## Some considerations about virtual environment
### virtualenv
- python的虚拟环境可以使一个python程序拥有独立的库library和解释器interpreter，而不用与其他python程序共享一个library和interpreter。虚拟环境的好处是避免了不同python程序间的互相影响，例如两个程序依赖某个库的不同版本，这两个程序因为共享库，只能有一个版本，就不能同时执行两个程序了。
- 就我的理解，python虚拟环境是针对项目的，有点像容器的作用，隔离执行环境。平常在遇到使用python2还是python3的时候，可以使用脚本#!/usr/bin/env 指定解释器，但是同样使用python3版本，但是对第三方的包依赖不同版本就不能很好解决。所以，可以给每一个项目单独使用一个python虚拟环境，可以认为是轻量级的针对python的容器。
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

```   
