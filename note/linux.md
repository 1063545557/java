**软连接，硬链接**

```bash
ln f1 f2 #f2是f1的硬链接
ln -s f1 f2 #f2是f1的软链接
```



![](../%E5%B7%A5%E5%85%B7/linux_1.png)

**硬链接**是有着相同 inode 号仅文件名不同的文件，因此硬链接存在以下几点特性：

- 文件有相同的 inode 及 data block；
- 只能对已存在的文件进行创建；
- 不能交叉文件系统进行硬链接的创建；
- 不能对目录进行创建，只可对文件创建；
- 删除一个硬链接文件并不影响其他有相同 inode 号的文件。

 **软链接**与硬链接不同，若文件用户数据块中存放的内容是另一文件的路径名的指向，则该文件就是软连接。 

- 软链接有自己的文件属性及权限等；
- 可对不存在的文件或目录创建软链接；
- 软链接可交叉文件系统；
- 软链接可对文件或目录创建；
- 创建软链接时，链接计数 i_nlink 不会增加；
- 删除软链接并不影响被指向的文件，但若被指向的原文件被删除，则相关软连接被称为死链接（即 dangling link，若被指向路径文件被重新创建，死链接可恢复为正常的软链接）。

### Linux命令

**关机 ：shutdown**

> 无论是关闭还是重启系统，首先要运行**sync**命令，把内存中的数据写到磁盘中。

```bash
sync #将数据同步到硬盘中

shutdown #关机指令

shutdown -h 10 #10分钟后关机

shutdown -h now #立马关机

shutdown -h 20:25 #今天20：25关机

shutdown -h +10 #10分钟后关机

shutdown -r now #立马重启

shutdown -r +10 #十分钟后重启

reboot #重启，相当于shutdown -r now

halt #关闭系统，相当于shutdown -h now 和poweroff
```

**系统目录结构：ls /**

![](../%E5%B7%A5%E5%85%B7/linux_2.png)



> **ls 列出目录**

```bash
-a #查看全部文件，包含隐藏文件
-l #列出所有文件，包括文件的属性和权限，没有隐藏文件

```

> **pwd 显示当前用户所在的目录**

> **makdir 创建一个目录**

```bash
-p #创建多级目录
```

> **rmdir 删除目录,仅能删除空的目录，如果下面存在文件，需要先删除文件**

```bash
-p #递归删除多个目录
```

> **cp 复制文件或目录**

```bash
cp 原来的地方 新的地方 #将原来的地方复制到新的地方
```

> **rm 移除文件或者目录**

```bash
-f #忽略不存在的文件，不会出现警告，强制删除
-r #递归删除目录
-i #删除询问是否删除
-rf 
```

> **mv 移动文件或者目录，重命名文件**

```bash
cp 原来的地方 新的地方 #将原来的地方移动到新的地方
-f #强制移动
-u #只替换已经更新过的文件
```

**文件属性**

第一个字符代表这个文件是目录，文件或连接文件等等：

- **d     代表目录**
- **-     代表文件**
- **l     代表链接文档**
- b     代表装置文件里面的可供储存的接口设备（可随机存取装置）
- c     代表装置文件里面的串行端口设备，例如键盘，鼠标（一次性读取装置）

**接下来的字符中，三个为一组，r代表可读（read），w代表可写（write），x代表可执行（execute）**

![](../%E5%B7%A5%E5%85%B7/linux_3.png)

> **修改文件属性**

```bash
chgrp [-R] 属组名 文件名 #更改文件属组，-R：递归更改文件属组
chown [-R] 属主名 文件名
chown [-R] 属主名:属组名 文件名 #更改文件属主，也可以同时更改文件属组

chmod #更改文件9个属性
chmod [-R] xyz 文件或目录 
r:4  w:2  x:1  # chmod  777 文件名 文件赋予所有用户可读可写可执行
```

**文件内容查看**

```bash
cat #由第一行开始显示文件内容

tac #从最后一行开始显示，可以看出tac是cat的倒着写

nl #显示的时候，顺道输出行号

more #一页一页的显示文件内容，空格代表翻页，回车代表向下看一行，:f查看当前行号

less #与more类似，但是比more更好的是，它可以往前翻页，空格代表翻页，上下键代表翻动页面，q退出命令,/要查询的字符串：从上查找字符串，？要查询的字符串，从下查询，n继续搜索下一个

head [-n]#只看头几行,-n显示几行

tail [-n]#只看尾巴几行,-n显示几行

ifconfig #查看网络配置

echo #输入字符串

touch #创建文件
```

**vim编辑器**

```bash
:w #保存文件但不退出vi
:w file #将修改另外保存到file中，不退出vi
:w! #强制保存，不推出vi
:wq #保存文件并退出vi
:wq! #强制保存文件，并退出vi
q: #不保存文件，退出vi
:q! #不保存文件，强制退出vi
:e! #放弃所有修改，从上次保存文件开始再编辑
```



```bash
i #切换到输入模式，以输入字符
x #删除当前光标所在处的字符
: #切换到底线命令模式
```

在输入模式中，可以使用以下按键：

- **字符按键以及Shift组合**，输入字符
- **ENTER**,回车键，换行
- **BACK SPACE**,退格键，删除光标前一个字符
- **DEL**，删除键，删除光标后一个字符
- **方向**键，在文本中移动光标
- **HOME/END**，移动光标到行首/行尾
- **Page up/Page down**，上下翻页
- **insert**，切换光标为输入/替换模式，将光标变为竖线/下划线
- **ESC**，退出输入模式，切换到命令模式



**账号管理**

> **useradd -选项 用户名  #添加用户**

```bash
-m #自动创建这个用户的主目录 /home/用户名
-G #给用户分配组
```

> **userdel 删除用户**

```bash
userdel -r 用户名 #删除用户的时候将他的目录页一并删除
```

> **usermod 修改用户**

```bash
usermod -d /home/修改后用户名 修改前用户名 #修改用户的目录为/home/修改后用户名
```

> **切换用户**

```bash
su 切换的用户名 
exit #退回原来的账户
hostname #查看主机名
hostname 主机名 #修改主机名
```

> 用户密码的设置问题

```bash
passwd 用户名 #修改用户密码
```

> 锁定账户（冻结账户）

```bash
passwd -l 用户名 #锁定之后这个用户就不能登录了
passwd -d 用户名 #没有密码也不能登录
```

**用户组管理**

> 创建一个用户组

```bash
groupadd 用户组名 #新增用户组
groupadd -g 520 用户组名 #指定用户组id,默认自增

groupdel 用户组名 #删除用户组

groupmod -g 修改的用户组id -n 修改后的用户组名 修改前的用户组名  #修改用户组

newgrp 切换的用户组名 #切换用户组
```

**/etc/passwd** 文件

```bash
用户名:口令（登录密码，我们不可见）:用户标识号:组标识号:注释性描述:主目录:登录shell
```

登录口令：把真正的加密后的用户口令字存放到/etc/shadow文件中，保证我们密码的安全性

用户组的信息放在 **/etc/group** 文件中



**磁盘管理**

```bash
df #(列出文件系统整体的磁盘使用量) 以字节数显示
df -h #以M,G等显示

du #检查磁盘空间的使用量
du -a #显示隐藏文件
du -sm /* #检查根目录下每个目录所占用的容量
```

> mac 或者想使用Linux挂载我们的一些本地磁盘或者文件

```bash
mount /dev/外部设备 /mnt/挂载目录 #将外部设备挂载到mnt目录下，来实现访问
umount -f [挂载位置] #卸载
```



**进程管理**

```bash
ps #查看当前系统中正在执行的各种进程的信息
ps -a #显示当前终端运行的所有的进程信息
ps -u #以用户的信息显示进程
ps -x #显示后台运行进程的参数
ps -aux #查看所有的进程
grep 条件字符串 #查找文件中符合条件的字符串
ps -aux|grep mysql #查询符合条件的进程
ps -ef #查看父进程的信息，一般可以通过目录树结构来查看

pstree -p #显示父id
pstree -u #显示用户组
pstree -pu #进程树

kill -9 进程id #强制结束进程

nohup #后台执行程序 nohup java -jar xxx.jar &

```



**环境 安装**

**三种方式：**

- **rpm(jdk)**
- **解压缩（tomcat）**
- **yum在线安装（docker）**

jdk安装

1. 下载jdk rpm

2. 安装Java环境

   ```bash
   java -version #检测当前系统是否存在Java环境,有的话需要卸载，
   rpm -qa|grep jdk #检测jdk版本信息
   rpm -e --nodeps jdk8 #卸载
   
   #卸载完后可安装jdk
   rpm -ivh rpm包
   
   #配置环境变量
   /etc/profile
   #在文件的最后面增加Java的配置
   JAVA_HOME=/user/java/jdk1.8.0_221-amd64
   CLASSSPATH=%JAVA_HOME%/lib;%JAVA_HOME%/jre/lib
   PATH=$JAVA_HOME$/bin;$JAVA_HOME$/jre/bin
   export PATH CLASSPATH JAVA_HOME
   
   #让配置文件生效
   source /etc/profile
   ```

   **防火墙**

   ```bash
   # 查看firewall服务状态
   systemctl status firewalld
   
   #开启、重启、关闭、firewalld.serice服务
   service firewalld start
   service firewalld restart
   service firewalld stop
   
   #查看防火墙规则
   firewall-cmd --list-all #查看全部信息
   firewall-cmd --list-ports #只看端口信息
   
   #开启端口
   firewall-cmd --zone=public --add-port=80/tcp --permanent
   
   #重启防火墙
   systemctl restart firewalld.service
   
   命令含义：
   --zone #作用域
   --add-port=80/tcp #添加端口，格式为：端口/通信协议
   --permanent #永久生效，没有此参数重启后失效
   ```

   

   

   