## hadoop配置ssh免密码登录 ##
#### ssh免密登录到自己 ####
- 运行 ssh localhost来产生/home/用户名/.ssh目录，然后将生成的"id_rsa.pub"追加到授权的key里面去。这样就能免密码登录到自己了。
- cd ~/.ssh    #如果找不到这个文件夹，先执行一下"ssh localhost"
- ssh-keygen -t rsa    #这里注意会提示存储位置，默认就好了
- Enter passphrase (empty for no passphrase): 还会出现这个提示，填写访问密码，空着就是没有密码，这里需要空着，直接回车
- cat id_rsa.pub >>authorized_keys    #将id_rsa.pub追加到authorized_keys。第一次生成的时候，就相当于给id_rsa.pub改个名字。
#### 对于.ssh目录，公钥，私钥的权限要求。权限不对有可能造成不能连接。
- 用户目录755或700，不能使用77*
- .ssh目录755
- .pub或authorized_keys 664
- 私钥id_rsa 600
- /var/log/secure:该日志中有失败原因 
#### 实现无密码登录到其他的主机，只需要将生成的"id_rsa.pub"追加到其他主机的"~/.ssh/authorized_keys"中去。
- scp ~/.ssh/id_rsa.pub hadoop@192.168.186.128:/home/hadoop   
- scp跨主机复制文件命令，hadoop远程用户名，@后面跟远程IP地址：后面是远程目录
- cat /home/hadoop/id_rsa.pub >> .ssh/authorized_keys 
#### linux主机名查看与修改 
- hostname //查看当前主机名
- hostname 主机名 //临时修改主机名，重启后，主机名会自己变回来。
- 传统Linux修改主机名，是通过修改文件来实现的。
- 通过配置文件/etc/sysconfig/network文件修改。
- NETWORKING=yes
- HOSTNAME=主机名
- 通过修改此文件，能够实现永久修改Linux的主机名，但是不能立即生效，要重启后才能生效。配合hostname命令，可实现立即永久修改Linux的主机名。
- /etc/hosts文件的功能是，提供IP和主机名的对照作用，配置好之后，可以使用主机名代替IP。
- 在文件中添加行 IP     主机名，把其他主机的主机名添加到这，就可以使用ssh 用户名@主机名来登录了。
- 另外，centos7和其他Linux不同,主机名存储在/etc/hostname文件中，可以通过修改这个文件中的内容来修改主机名，但是要重启后才能生效，注销用户不管用，因为这个文件是在启动的时候读取的。
- 还可以通过命令：hostnamectl set-hostname 主机名，来修改主机名，立即生效，永久生效。
- hostnamectl status //查看主机名相关的设置。
- ssh密钥是一串字符，最后跟着主机名，所以在变更主机名后，原来配置的免密码登录就失效了。
- ssh登录时出现错误：Agent admitted failure to sign using the key.
- 在当前用户下执行命令：ssh-add就可以解决。