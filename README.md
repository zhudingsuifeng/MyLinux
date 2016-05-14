<center>![ubuntu{100}](image/ubuntulogo.png)
>使用相对路径添加图片成功

记录在学习linux过程中所遇到的一些问题，同时学习markdown，主要是ubuntu上一些开发环境的搭建，软件的安装与配置，像mysql，apache2，php7，sublime-text-3，retext，flashplugin-nonfree，zsh,oh-my-zsh等等。



首先是选用一个发行版本的linux系统,这里我选择的是ubuntu，因为他对于初学者来说比较简单。
***
###ubuntu安装与配置
+ 安装的过程用，最好联网，选择中文支持，同时支持mp3等第三方协议，否则mp3音乐播放不了，手动安装中文支持比较麻烦，对初学者来说是一个挑战。
+ 安装好后的第一件事，就是安装搜狗拼音输入法，因为搜狗拼音提供linux版本，直接到官方网站下载deb包，之后在命令行，输入**dpkg -i sougoupinyin.deb**就可以安装了。
![dpkg安装软件](image/dpkg安装软件.png)
这样再遇到问题的时候就可以尽情搜索了。
+ ubuntu默认的浏览器是firefox，但是默认是不安装flash插件的，这就导致无法在线看视频，这对刚从windows转过来的孩子来说，简直就是灾难。而且在网页上点击安装总是不成功，这个时候就要通过**apt-get install flashplugin**来实现了，当然执行这条命令之前，要先转到系统权限**sudo su**之后输入密码。
![transfer-authoriy](image/transfer-authority.png)
+ 说到安装软件，linux最有特点的就是命令行，所有操作都能在命令行下完成，通过命令行来安装软件，但是很多时候我们并不清楚自己要安装的软件具体是哪一个，这时候**apt-cache search 软件名称的一部分**，就能显示出多个软件共我们选择，还是以flashplugin为例。
![apt-cache-search](image/apt-cache-search.png)
根据介绍，选择一个我们需要的软件，使用**apt-get install 软件名**来安装就可以了。
>到这里一些基本的操作已经可以进行了，要想搭建程序开发环境，还需要安装其他程序。
***
**安装sublime text 3**

sublime text 是一个跨平台的，好用的文本编辑器，可以用来编写C，html,php等文件。首先去sublime text官网下载最新版本。
![sublime-text-3](image/sublime.png)
下载的是.deb软件包，通过dpkg -i ***.deb安装。sublime text需要破解一下，。网上搜的注册码。
![register](image/sublime-text-3-register.png)



复制License中的内容，Sublime Text，执行 Help->Enter license 粘贴你的License，点击OK，重启Sublime Text即可看到你的注册信息。注册之后可以安装很多好用的插件，提高编程的效率。通过PackageControl组件安装插件。

1. 按Ctrl+`调出console（注：安装有QQ输入法的这个快捷键会有冲突的，输入法属性设置-输入法管理-取消热键切换至QQ拼音)。
2. 粘贴以下代码到底部命令行并回车：
`
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
`
3. 重启sublime text3，如果在Perferences->package settings中看到package control这一项，则安装成功。