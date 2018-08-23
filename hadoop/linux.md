## 一些Linux相关知识
#### 改变文件拥有者和群组，利用chown可以将文件的所有者加以改变，这个指令是由root使用。
- chown [-R] [用户名称:组名称] [文件或目录]
- chown -R hadoop:hadoop /usr/hadoop
#### Linux忘记用户密码
- 以单用户模式登录跳过密码，并且修改root用户密码，重启后就可以进入系统了。
- 在启动的时候，进入grub选项菜单(当前主流是grub2)，选中内核，按e进入编辑模式。(按c进入命令行模式)
- 修改Linux后面的or quiet 为rw sigle init =/bin/bash 
- ctrl+x重启
```javascript
#mount -a 
#passwd root 
#reboot
```
#### tar命令一次解压多个压缩文件
- for查询：
- for tar in *.tar.gz; do tar -xzvf $tar;done
- 列出文件列表，然后xargs逐一解压：
- ls *.tar.gz|xargs -n1 tar -xzvf
- 用find条件查找解压：
- find -maxdepth 1 -name "*.tar.bz2"|xargs -i tar -xvjf{}
#### $which jps //查找命令位置
#### xargs妙用
- xargs命令是给其他命令传递参数的一个过滤器，也是组合多个命令的一个工具。
- 它将标准输入数据转换成命令行参数，xargs能够处理管道或者stdin并将其转换成待定命令的命令参数。
- xargs也可以将单行或多行文本输入转换为其他格式，例如多行变单行，单行变多行。
- xargs的默认命令是echo，空格是默认定界符。
- 这意味着通过管道传递给xargs的输入将会包含换行和空白，不过通过xargs的处理，换行和空白将被空格取代。xargs是构建单行命令的重要组件之一。
- xargs用作替换工具，读取输入数据重新格式化后输出。
- 定义一个测试文件，内有多行文本数据：
```javascript
cat test.txt 
a b c d e f g
h i g k l m n 
o p q 
r s t 
u v w x y z 
```
- 多行输入但行输出：
```javascript
cat test.txt | xargs 
a b c d e f g h i j k l m n o p q r s t u v w x y z
```
- -n选项多行输出：(-nx其中x表示每一行元素个数)
```javascript
cat test.txt | xargs -n3 
a b c 
d e f 
g h i 
j k l 
m n o 
p q r 
s t u 
v w x 
y z
```
- -d选项自定义一个定界符：
```javascript
echo "nameXnameXnameXname" | xargs -dX 
name name name name
```
- 结合-d和-n使用：
```javascript
echo "nameXnameXnameXname" | xargs -dX -n2 
name name 
name name
```
- 读取stdin，将其格式化后的参数传递给命令。
- 假设一个命令为 sk.sh 和一个保存参数的文件arg.txt： 
- sk.sh内容：
```javascript
#!/bin/bash 
#sk.sh命令内容，打印出所有参数。 
echo $* 
```
- arg.txt文件内容：
```javascript 
cat arg.txt 
aaa 
bbb 
ccc 
```
- xargs的一个选项-I，使用-I指定一个替换字符串{}，这个字符串在xargs扩展时会被替换掉，当-I与xargs结合使用，每一个参数命令都会被执行一次：
```javascript
cat arg.txt | xargs -I {} ./sk.sh -p {} -l 
-p aaa -l 
-p bbb -l 
-p ccc -l 
```
- 复制所有图片文件到 /data/images 目录下： 
- ls *.jpg | xargs -n1 -I {} cp {} /data/images   //这个命令在{}和cp 分开来容易理解，前半段把输出处理成{},作为后一个命令的输入，就相当于先声明变量，后引用变量。
- xargs结合find使用 用rm 删除太多的文件时候，可能得到一个错误信息：/bin/rm Argument list too long. 用xargs去避免这个问题： 
- find . -type f -name "*.log" -print0 | xargs -0 rm -f //-print0：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式为全部的名称皆在同一行。
- xargs -0将\0作为定界符。 
- 统计一个源代码目录中所有php文件的行数： 
- find . -type f -name "*.php" -print0 | xargs -0 wc -l 
- 查找所有的jpg 文件，并且压缩它们： 
- find . -type f -name "*.jpg" -print | xargs tar -czvf images.tar.gz 
- xargs其他应用 假如你有一个文件包含了很多你希望下载的URL，你能够使用xargs下载所有链接： 
- cat url-list.txt | xargs wget -c