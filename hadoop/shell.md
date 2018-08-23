## shell特殊字符的含义，条件判断的含义 
#### shell中条件判断if中的-a到-z的意思
- [-a file]如果file存在则为真
- [-b file]如果file存在且是一个块特殊文件则为真
- [-c file]如果file存在且是一个字特殊文件则为真
- [-d file]如果file文件存在且是一个目录则为真
- [-e file]如果file文件存在则为真
- [-f file]如果file存在且是一个普通文件则为真
- [-g file]如果file存在且已经设置了SGID则为真
- [-h file]如果file存在且是一个符号链接则为真
- [-k file]如果file存在且已经设置粘滞位则为真
- [-p file]如果file存在且是一个名字管道则为真
- [-r file]如果file存在且是可读的则为真
- [-s file]如果file存在且大小不为0则为真
- [-t FD]如果文件描述符FD打开且指向一个终端则为真
- [-u file]如果file存在且设置了SUID（set userID）则为真
- [-w file]如果file存在且是可写的则为真
- [-x file]如果file存在且是可执行的则为真
- [-O file]如果file存在且属于有效用户ID的则为真
- [-G file]如果file存在且属于有效用户组的则为真
- [-L file]如果file存在且是一个符号链接则为真
- [-S file]如果file存在且是一个套接字则为真
- [-o optionname]如果shell选项optionname开启则为真
- [-z string]string的长度为零则为真
- [-n string]string长度非零non-zero则为真
### 当存在不清楚选项时，man test就可以了
#### shell中一些特殊字符含义 
- $0这个程式的执行名称
- $n这个程式的第n个参数值，n=1...9
- $*这个程式的所有参数，此选项参数可以超过9个。
- $$这个程式的PID（脚本运行的当前进程ID号）
- $!执行上一个背景指令的PID（后台运行的最后一个进程的进程ID号）
- $?执行上一个指令的返回值（显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误）
- $-显示shell使用的当前选项，与set命令功能相同
- $@跟$*类似，但是可以当作数组用
#### /etc/profile引发的问题
```javascript
for i in /etc/profile.d/*.sh ; do
	if [ -r "$i" ]; then
		if ["${-#*i}"!="$-"] ; then
 			."$i"
 		else
 			."$i">/dev/null 2>&1
 		fi 
 	fi 
done
```
- $echo $-
- 输出结果是：himBH
- himBH没一个字符是一个shell的选项，详情man bash然后搜索-h -B 等。
- ${-#*i}意思是，从左往右看，删除掉$-变量的值中第一个i字符以及之前的内容。
- 相应的，${-%i*}，%与#的意义刚好相反，从右往左看，删除掉$-变量的值中的最后一个i字符以及之后的内容。
- $-记录着当前设置的shell选项，himBH是默认值，你可以通过set命令来设置或者取消一个选项配置。
- set -x   //这个命令可以打开shell的调试开关，这个时候再次输出$-变量，结果是himxBH
- set -x 可以通过set +x关掉，这是相对应的。
#### i-interactive
- 包含这个选项说明当前的shell是一个交互式的shell，交互式，就是你输入命令，shell解释执行后会给你返回结果，terminal下使用的shell就是交互式的。
#### H-history expand
- history expand设置后，shell会把我们执行过的命令记录下来，可以通过history命令查看，shell退出时会保存到~/.bash_history文件中，set +H关闭这项功能。
#### B-brace expansion
- 花括号扩展，可以用来连接字符或者变量
- echo sp{el,il,al}l       //结果是：spell spill spall 
- firstname=wang
- echo ${firstname}jiankun   //结果是：wangjiankun
- 相应的扩展还有：brace expansion(花括号扩展)，tilde expansion(代字符号扩展)，parameter expansion(参量扩展)，command substitution(命令替换)，arithmetic expansion(算数扩展)，process substitution(过程替换)，word splitting(词分离)，file name expansion(路径名扩展)。
#### m-monitor mode
- 打开监控模式，可以控制进程的停止、继续，后台或者前台执行。
#### h-hashall
- 使bash记住它使用PATH找到的命令的位置
#### set -o //查看当前shell的选项配置。
#### centos7彻底删除一个用户
- userdel -r username //并不能删除在线用户