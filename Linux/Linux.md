# Linux

## 网络连接的三种模式

网络连接的三种模式（虚拟机安装网络设置的三个选项）

桥接模式：虚拟系统可以与外界通讯，但是易造成 IP 冲突（虚拟系统与主机网络在同一个网段）

NAT模式：网络地址转换模式，将主机和虚拟系统构建一个网络系统（与主机网络不同），这两个之间可进行通信，虚拟系统可通过主机 IP 访问外网，因此不易造成 IP 冲突

主机模式：主机和虚拟系统构建一个独立系统



## 目录结构

Linux 下一切皆文件

根目录为 `/`，具体的目录结构：

- `/bin`（`/usr/bin`，`/usr/local/bin`）存放最常使用的命令
- `/sbin`（`/usr/sbin`, `/usr/local/sbin`）存放系统管理员管理程序
- `/home`存放普通用户的主目录，系统中每个用户都有一个自己的目录，该目录以用户名命名
- `/root`系统管理员主目录
- `/lib`系统开机所需要的冬天连接共享库
- `/lost+found`一般为空，系统非法关机后存放文件
- `/etc`系统管理所需要的配置文件和子目录
- `/usr`用户安装的程序和文件默认放在这个目录下
- `/boot`启动 linux 使用的核心文件
- `/proc`虚拟目录，获取系统信息
- `/srv`服务启动后提取的数据
- `/tmp`存放临时文件
- `/dev` 类似 windows 下的设备管理器，将所有的硬件按照文件形式存储
- `/media` linux 会自动识别一些设备，并且将他们挂载到这个目录下
- `/mnt` 让用户临时挂载别的文件系统，可以将外部的存储挂载到该目录下，然后进入即可看到内容
- `/opt` 给主机额外安装软件**存放**的目录
- `/usr/local`额外软件安装的目录
- `/var` 存放不断扩充的东西，例如各种日志文件

连接远程服务器

使用命令 `ifconfig` 查看服务器 ip 地址，连接之间首先使用 `ping` 命令测试一下

## vi 和 vim 编辑器

### 三种工作状态

- 正常模式：使用 vi 或 vim 命令打开文件就直接进入一般模式，在此模式下，可以使用方向键移动光标以及进行删除、复制粘贴操作
- 编辑模式：按下 i、I、o、a等命令进入编辑模式
- 命令行模式：完成读取、存盘、替换、离开编辑器以及显示行号等操作

### 三种模式的切换

使用 vi 命令进入的即为 一般模式，在一般模式下通过 i 等命令进入 编辑模式，通过 ：或  / 进入命令模式，编辑模式和命令模式通过 esc 返回一般模式，此外，编辑模式通过 `:wq、:q、:q!`退出编辑器

### vi 快捷键

- 拷贝当前行，使用`yy`，如果需要拷贝当前行向下的多行，例如光标所在位置向下5行，使用`5yy`，粘贴为`p`（一般模式下）
- 删除当前行使用`dd`，多行同上（一般模式）
- 在文件中查找某个单词，使用`/KEYWORD`然后回车查找，输入`n`表示查找下一个（命令行模式）
- 设置显示文件的行号或取消行号显示，使用`:set nu`和`:set nonu`（命令行模式）
- 定位到文件的首行：`gg`，定位到文件的末行:`G`，例如修改文件`etc/profile`文件
- 撤销修改命令：在一般模式下输入`u`
- 快速移动到文件的某个位置，例如定位到第20行，在一般模式下，输入20 ，然后输入`shift+g`

## 开关机以及登录注销

关机或重启之前注意先使用命令`sync`将内存数据同步到磁盘，相关命令有：

- `shutdown -h now`立刻关机
- `shutdown -h 1`1分钟后关机
- `shutdown -r now`重新启动
- `halt`关机
- `reboot`重启

注意登录的时候少用 root 账号，避免操作失误，在普通账号下，使用`su root`切换为系统管理员，在 root 账号下，使用`logout`命令注销用户（root 注销后为普通用户，再次注销后退出系统）

此外 `logout` 命令在图形运行级别无效，在运行级别3下有效

## 用户管理

### 添加用户

在 root 账号下，使用命令`useradd USERNAME`添加用户，创建用户成功后，会自动创建和用户名同名的家目录，也可以通过 `useradd -d TARGET_DIR`为新创建的用户指定家目录

(root用户->普通用户：`su - USERNAME`，反之使用 `su - root`)

### 修改密码

为用户设置密码命令：`passwd USERNAME`，输入即可

### 删除用户

使用命令`userdel USERNAME`删除用户，此时保留家目录，如同时删除家目录，使用命令：`userdel -r USERNAME`

### 查询用户

使用命令`id USERNAME`查询用户信息，使用命令`whoami`查询当前用户的信息

### 切换用户

使用指令`su - USERNAME`切换用户，从权限高的用户切换到权限低的用户无需密码，反之需要，退回到原来用户的时候使用 `exit`或者`logout`

### 用户组

用户组类似于角色，系统为有共性的多个用户统一进行管理

新增组：`groupadd GROUPNAME`

删除组：`groupdel GROUPNAME`

变更组：`usermod -g GROUPNAME USERNAME`

用户组相关的目录：

- `/etc/passwd`文件：用户的配置文件，包括用户名、口令、用户标识号等
- `/etc/shadow`文件：口令的配置文件，包括登录名、加密口令等
- `/etc/group`文件：组的配置文件，包括组名、口令等信息

## 实用指令

### 运行级别

运行级别：

- 0：关机
- 1：单用户（找回丢失密码）
- 2：多用户状态没有网络服务
- 3：多用户状态有网络服务
- 4：系统未使用保留给用户
- 5：图形界面
- 6：系统重启

常用的运行级别是3和5，也可以指定默认的运行级别，通过 `init N`指令切换运行级别，使用`systemctl get-default`查看运行级别

### 帮助指令

例如，查看`ls`指令的帮助信息：

使用`man ls`或者`help ls`

### 文件目录类

- pwd 命令：输出当前工作目录的绝对路径
- ls命令：列出当前目录下的所有文件，常用的参数有`-a/-l`表示输出
    - 使用`ls -l`打印详细信息的时候，文件大小显示不够直观，可以使用`ls -lh`将b单位转化为 M 等单位，方便直观查看大小
- cd命令：切换到指定路径，`cd ~`回到当前用户的家目录，`cd ..`返回上层目录
- `mkdir DIRNAME`：创建一级目录，可以使用`mkdir -p DIRNAME1/DIRNAME2`创建多级目录（多层目录同时创建）
- `rm DIRNAME`：删除空目录，如文件夹非空，则使用`rm -rf DIRNAME`删除 （强制递归删除，危险操作）
- `touch FILENAME`：创建空文件
- cp 命令：

  - `cp [option] source dest`，例如`cp hello.txt /home/dest`将文件 hello.txt 复制到 dest 目录下
  - 使用参数`-r`递归地复制整个文件夹，例如：`cp -r /home/test /dest`
- rm 命令：

  - `rm [option] file`，移除文件或文件夹，可选参数有：
    - `-r`：递归删除文件夹
    - `-f`：强制删除不提示
    - 例如，删除整个文件夹并且不提示：`rm -rf file`
- mv 命令：重命名文件或移动文件

  - `mv /home/a.txt /home/b.txt`：将文件a重命名为b
  - `mv /home/a.txt /home/dest`：将文件a移动到 dest 文件夹下
  - `mv /home/a.txt /home/dest/b.txt`：将文件a移动到 dest 文件夹下并重命名为b
  - 移动文件夹，例如：`mv /home/a /home/b/`，与移动文件方式相同
- cat 命令：查看文件内容：

  - `cat -n file`查看文件内容，参数`-n`表示显示行号
  - 与管道命令结合：`cat -n file | more`
- more 命令：基于 VI 编辑器的文本过滤器，按页显示文本文件内容，其快捷键有：

  - space：下一页
  - enter：下一行
  - q：离开 more
  - ctrl + f：向下滚动一屏
  - ctrl + b：返回上一屏
  - =：输出当前行的行号
- less 命令：与 more 类似，只是 more 显示文本的时候会将内容全部加载到内存，而 less 则是动态加载，因此更适合查看较大的文件，例如：`less /etc/profile`

  - pageup：上一页

  - pagedown：下一页

  - /str ：查找某个字符串

  - q：退出
 - echo 命令：输出内容到控制台
    - 例如`echo $PATH`，`echo $HOSTNAME`
    - 也可以配合`>`输出到文件：`echo hello > info.txt`
- head 命令：
  - `head -n FILENAME`查看文件的开头部分，如省略n，默认情况下显示前10行
- tail 命令：与 head 命令类似，查看文件末尾部分
  - 例如：`tail -n FILENAME`
  - 使用`tail -f FILENAME`实时监控文件的变化
- `>, >>`命令：输出内容到指定文件
  - `>`会覆盖原始文件，例如：`ls -l /home > info.txt`
  - `>>`表示追加方式写入，例如将日期信息追加进 mycal 文件：`cal >> mycal`
  - 应用场景，例如将环境变量写入另一个文件：`cat /etc/profile > myprofile`
- link 命令：用于创建软链接（符号链接）：
    - 例如：`ln -s /home /home/myroot`，此时使用 ll 命令查看 home 目录，可以看到`myroot -> /home/`
    - 使用 pwd 命令查看软链接路径的时候，看到的依然为软链接所在的目录
    - 删除软链接：`rm /home/myroot`，注意不要写成`rm /home/myroot/`，此时系统会认为是在删除文件夹，删除不成功，反之则是删除普通文件
- history 命令：查看历史指令
    - 查看最近的10条命令：`history 10`
    - 每条历史命令都有编号，如需执行某个编号的命令，例如：`!222`，意为执行编号为222的历史命令

### 时间日期

- date 命令：按照格式打印当前时间，例如：
  - `date "+%Y-%m-%d %H-%M-%S"`，打印结果为：`2022-11-29 19-42-27`
  - 参数 -s 用于设置时间：`date -s DATESTR`，例如：`date -s "2222-12-12 12:12:12"`
- cal 命令：打印日历
  - `cal 2022`表示打印 2022 年历

### 查找命令

- find 命令：从指定目录下遍历查找某个文件
  - 格式：`find [RANGE] [OPTION]`，选项参数有三个，`-name`表示按照文件名查找，`-user`按照所属用户查找，`-size`表示按照文件大小查找
  - 例如：`find /home -name hello.txt`，表示从 home 目录开始查找 hello.txt 文件
  - `find /home -user root | more`表示从 home 目录开始查找 root 用户的目录，并按页打印
  - `find /home -size +100M`表示查找大小大于100M的文件，可用的单位有`k, M, G`
- locate 命令
  - locate 命令可以快速定位文件位置，需要更新数据库索引
    - 更新数据库索引`updatedb`，搜索文件：`locate FILENAME`
- grep 命令：过滤查找，| 管道符号，表示将前一个命令的处理结果输出传递给后面的命令处理
  - 命令格式： `grep [option] CONTENT SOURCE_FILE`
  - 常用的选项有`-n`， `-i`
  - 例如：`cat /home/test.txt | grep "yes"`和`grep -n "yes" /home/test.txt`表示在 test.txt 文件中查找 yes 文本

### 压缩与解压缩

- gzip 命令用于压缩文件，gunzip 命令用于解压缩文件（注意不能压缩文件夹）
  - 例如：`gzip /home/hello.txt`与`gunzip /homt/hello.txt.gz`
- zip/unzip 命令用于压缩与解压缩文件或文件夹，格式为：`zip [option] ZIPNAME.zip ZIPPING_FILE`，解压缩命令格式：`gunzip [option]  ZIPFILE.zip`
  - 参数`-r`：用于递归压缩文件夹 
  - 例如：`zip -r test.zip /home/test `表示将 test 文件夹压缩为 test.zip
  - unzip参数 `-d TARGET_DIR` 表示将压缩文件解压到的地方
  - 例如`unzip -d /home/test2 /home/test.zip` 表示将 test.zip 解压缩至 test2 文件夹
- tar 命令：打包命令，生成`xxx.tar.gz`文件
  - 格式：`tar [option] xxx.tar.gz ZIPPING_FILE`
  - 主要参数：
    - -c ：产生 tar  打包文件
    - -v ： 显示详细信息
    - -f ： 指定压缩后的文件名
    - -z ： 打包同时压缩
    - -x ： 解包 tar 文件
  - 例如： 压缩多个文件：`tar -zcvf /home/ab.tar.gz /home/a.txt /home/b.txt`表示将 a.txt 和 b.txt 文件压缩为 ab.tar.gz 文件
  - 压缩整个文件夹：`tar -zcvf test.tar.gz /home/test `解压缩：`tar -zxvf test.tar.gz`
  - 将某个压缩文件解压缩至某个文件夹：`tar -zxvf test.tar.gz -C /home/test2`

## 权限管理

### 文件所有

查看：`ls -ahl`

修改文件所有者：`chown USER test.txt`

### 修改文件所在组

创建文件默认属于创建者所在的组，使用命令`chgrp GROUPNAME FILENAME`修改文件所在组，例如

### rwx 权限管理

使用命令`ls -ahl`打印文件信息：

```
-rw-r--r-- 1 root   gp    102 Nov 28 15:50 a.txt
```

该信息前九位给出了不同组的权限：

- 第0位确定了文件类型：
  - l 表示链接
  - d 是目录
  - c 表示字符设备文件，例如鼠标、键盘
  - b 是块设备，例如硬盘
- 第 1-3 位确定所有者的权限 （User）
- 第 4-6 位确定同用户组成员拥有该文件的权限 （Group）
- 第 7-9 位确定其他用户拥有该文件的权限

三个字符代表的权限：

- 对于文件：
  - r 表示可查看
  - w 表示可以修改（注意可修改不代表可以删除该文件，删除文件需要拥有文件所属的文件夹的写权限）
  - x 表示可执行，表示可执行该文件
- 对于目录：
  - r 表示可读，即可以使用 ls 命令查看文件夹内容
  - w 表示可修改，可以创建、修改、删除文件以及重命名目录
  - x 代表可执行，表示可以进入该目录

其他部分：

- 1 表示文件的硬链接数或子目录数
- 随后两个是所属用户和组
- 102 文件大小（字节），如果是文件夹，则是 4096
- 修改日期 以及 文件名

### 权限修改

通过 chmod 命令，可以修改文件或目录的权限

- 通过 chmod 命令修改目录或者文件的权限
- 第一种方式：u 所有者，g 所有组，a 所有人，o 其他人
  - 例如：`chmod u=rwx,g=rx FILENAME`表示为所有者赋全部权限，为所有组赋读和执行的权限（赋值执行权限文件会编程绿色）
  - `chmod o+w FILENAME`表示为其他用户赋写权限
  - `chmod a-x FILENAME`表示为所有用户关闭执行权限
- 第二种方式：使用数字，三种权限：r=4，w=2，x=1，不同权限数字可以叠加组合
  - 例如`chmod 751 a.txt`与`chmod u=rwx,g=rx,o=x a.txt`同义

## 磁盘管理

Linux 采用了一种叫做“载入”的处理方法，将图个分区和一个目录联系起来，linux 硬盘分 IDE 硬盘和SCSI硬盘，目前基本上是SCSI硬盘（开头为 sdx）

### 设备挂载

使用命令：`lsblk` 或者`lsblk -f`查看硬盘的挂载情况

### 磁盘情况查询

使用命令 `df -h`查看，如果磁盘使用率超过 80%，就要考虑增加硬盘空间或者清理空间

使用命令 `du -h` 查看指定目录的磁盘占用情况，主要参数有：

- `-s` 指定目录的占用大小汇总
- `-h` 带计量单位
- `-a` 含文件
- `--max-depth=1` 设置查询的子目录深度
- `-c` 列出明细，并增加汇总值

例如：`du -hac --max-depth=1 /home`

### 统计文件数量实例

使用 grep 命令+正则表达式过滤，并使用 wc 命令统计，例如：

```
ls -l | grep "^-" | wc -l
# 列出文件列表  使用正则表达式得到-开头的文件 wc统计数量
```

### 统计子文件夹内的文件数量

使用 `R`递归插叙文件夹内容：`ls -lR | more`，同理：

```
ls -lR | grep "^-" | wc -l
```

### 以树状结构展示目录结构

安装 tree 命令：`yum install tree`或者`apt install tree`

然后使用命令`tree /home`查看 home 目录下的文件结构

## 进程管理

### 进程

每个执行的程序都称为一个进程，每个进程都分配一个 ID 号（pid），进程存在的形式有两种：前台与后台，所谓的前台进程就是用户目前的屏幕上可进行操作的，后台进程则是在操作但无法直接可见的进程，一般系统的服务都是以后台进程的方式存在，直到关机才结束

### 查看进程

使用命令`ps`查看进程：

- `ps -a`：显示当前终端的所有进程
- `ps -u`：以用户格式显示进程信息
- `ps -x`：显示后台进程运行的参数

一般结合上述三个参数使用：`ps -aux`，或者结合 grep 命令过滤：`ps -aux | grep xxx`该命令显示的信息有：

- user：用户
- pid：进程号
- %CPU：CPU使用占比
- %MEM：物理内存占比
- VSZ：虚拟内存占用大小 / RSS：物理内存占用大小
- STAT：进程状态，例如 S 表示睡眠，R 表示正在运行， **Z 表示僵死进程**， T 表示被跟踪或者被停止 

### 父子进程

如需查看某个进程的父进程，使用命令 `ps -ef`，参数 e 表示以全格式显示当前所有的进程，参数 f 表示为全格式，配合 grep 命令实现对进程的过滤 `ps -ef | grep xxx`，该命令的打印结果为：

```
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 09:53 ?        00:00:00 /init
root         8     1  0 09:53 ?        00:00:00 /init
root         9     8  0 09:53 ?        00:00:00 /init
```

其中 PID 表示当前进程，PPID 表示该进程的父进程，C 表示 CPU 用于计算执行优先级的因子，数值越大，表明晋城市 CPU 密集型运算，其执行优先级会降低，越小则是 I/O 密集型运算，执行优先级会提高

### 杀死进程

需要停止进程成，使用 kill 命令，格式为 `kill [option] PID`，或者使用 killall 命令`killall PID`，killall 命令支持通配符，当系统很卡的时候非常有效

kill 命令常用的参数为 9 ，表示强制杀死进程：`kill -9 PID`

例如

- 终止远程登录服务 sshd，`kill sshd_PID`，重启该 sshd 服务：`/bin/systemctl start sshd.service`
- 同时终止多个 gedit 进程：`killall gedit`

### 进程树

使用命令 `pstree [option]`打印树形式的进程信息，常用参数有：

- p 同时显示进程的 PID
- u 显示进程的所属用户

## 服务

### 查看服务

服务（service）本质上就是进程，但是通常运行在后台，并监听某个端口，等待某个程序的请求，也被称为守护进程，例如 sshd 使用的 22 端口，mysqld 使用的 3306 端口。

服务管理命令：`service SERVICE_NAME [start|stop|restart|reload|status]`，注意在 centos 7 之后往往使用 `systemctl`代替`service`

查看服务：

- 使用 `setup` 命令
- 使用`ls -l /etc/init.d`命令

 开机流程：

```
开机 - BIOS - /boot - systemd进程1 - 运行级别 - 运行运行级别对应的服务
              引导                 确定运行级别
```

### 管理服务

systemctl 管理命令，格式为：`systemctl [start | stop | restart | status] SERVICE_NAME`，systemctl 命令所管理的服务可以在 `/usr/lib/systemd/system`中查看

systemctl 常用的命令有：

- `systemctl list-unit-files |grep ` 按照 grep 过滤的规则查看服务开机启动的状态
- `systemctl enable SERVICE_NAME` 设置服务开机启动
- `systemctl disable SERVICE_NAME` 关闭服务开机自启动
- `systemctl is-enabled SERVICE_NAME` 查询某个服务是否自启动

例如，对防火墙服务 (firewalld.service) 的设置：

- `systemctl status firewalld`：查看防火墙服务状态
- `systemctl stop firewalld.service`：临时关闭防火墙（重启系统后失效，如永久关闭，需要使用 disable 命令），此外，可以简写服务名称 （`firewalld.service` ->`firewalld`，即简写为点号前面的内容）

### 打开或关闭指定的端口

防火墙打开的同时打开某个端口：

- 打开端口：`firewall-cmd --permanent --add-port=PORT_ID/PROTO`（端口号/协议）
  - 协议可以通过命令`netstat -anp`查看
  - 例如：`firewall-cmd --permanent -add-prot=111/tcp`
- 关闭某个端口：`firewall-cmd --permanent --remove-port=PORT__ID/PROTO`
  - 例如：`firewall-cmd --permanent remove-port=111/tcp`
- 重启后生效：`firewall-cmd --reload`
- 查看端口是否开放：`firewall-cmd --query-port=PORT_ID`

注意：

- 打开或者关闭某个端口后，都要执行 reload 命令才会生效

### 动态监控进程

使用命令`top`，该命令与 ps 命令相似，但在执行一段时间后会更新正在运行的进程，主要参数有：

- d：指定默认几秒后刷新（默认三秒）
- i：不显示僵死进程（zombie）
- p：监听指定的进程

在 top 命令下可以使用交互命令：

- P：按照 CPU 使用率排序（默认）
- N：按照 PID 排序
- M：按照内存使用率排序
- q：退出
- u：然后输入用户名 -- 监控特定用户的进程

### 监控网络状态

使用 netstat 命令，一般使用参数为`netstat -anp`

```
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
协议                 本地地址（Linux）        外部地址（客户端连接的IP地址）
```

## 安装配置 JDK 案例

安装流程：

- 创建安装文件夹：`mkdir /opt/jdk`，用于保存 jdk 压缩包（一般都会将安装压缩包上传保存至 opt 目录下）
- 上传 jdk 压缩包
- 解压：`tar -zxvf jdk.xxxx.tar.gz`
- 在个人文件夹下创建安装 jdk 的文件夹：`mkdir /usr/local/java`
- 将解压后的文件移动到个人目录下：`mv jdkfile /usr/local/java`
- 配置环境变量：`vim /etc/profile`
  - 添加`export JAVA_HOME=/usr/local/java/jdkxxx`
  - `export PATH=$JAVA_HOME/bin:$PATH`，注意后面要拼接上原来的 PATH，否则会破坏原有的环境变量
- 刷新环境变量：`source /etc/profile`

## 日志相关

### 日志文件

日志文件用于记录重要的系统事件，包括用户的登录信息、系统的启动信息以及安全信息、服务相关信息等等，可以通过日志来检查错误发生的原因，或者收到攻击时攻击者留下的痕迹等，系统日志文件保存于`/var/log`文件夹下

### 常用的日志文件

- boot.log：系统启动日志
- cron：记录系统定时任务相关的日志
- btmp：记录错误登录的信息，该文件为二进制文件，需要使用 lastb 命令查看
- lastlog：记录系统中所有用户最后一次登录时间，二进制文件，需要使用 lastlog 命令查看
- maillog：记录邮件信息
- message：记录系统重要消息的日志，如果系统出现问题，首要检查文件
- secure：记录验证授权方面的信息，只要涉及账号密码就会被记录，例如系统登录，ssh登录，su切换用户
- utmp：记录当前已经登陆的用户的信息，该文件会随着用户的登录和注销不断变化，使用特定命令 w、who、users进行查看

### 日志服务

日志管理服务 rsyslogd，日志管理服务按照指定配置文件保存日志，系统记录日志必须保证该服务运行：

- 查询 rsyslogd 服务是否启动：`ps -aux | grep "rsyslog" | grep -v "grep"`
- 查询 rsyslogdf 服务的自启动状态：`systemctl list-unit-files | grep rsyslog`

日志服务配置文件`/etc/rsyslog.conf`

该文件的格式为：`a.b`，其中 a 表示日志类型，b 代表日志级别，如`*.*`表示任何类型的日志以及任何级别都记录

日志类型有：

- authpriv：ssh、ftp等登录信息的验证i想你想
- corn：时间任务相关
- kern： 内核相关
- mail：邮件相关
- user：用户程序产生的相关信息

日志级别有：

- debug：有调试信息的
- info：一般信息日志（最常用）
- notice：具有重要性的普通条件的信息
- warning：警告级别
- err：错误级别，某个功能和或者模块不能正常工作
- crit：严重级别，阻止系统或者软件不能正常工作
- alert：需要立刻修改
- emerg：内核崩溃信息
- none：不记录

上述日志级别由低到高，记录的信息越来越少，例如`mail.info`表示记录邮件相关的 info 级别的日志信息

由 rsyslogd 记录的日志信息内容包括四部分：

- 事件事件
- 产生事件的服务器主机名
- 产生事件的服务名
- 事件具体信息

## shell

shell 是一个命令行解释器，它为用户提供过了一个向 Linux 内核发送请求以便运行程序的界面系统级程序，用户可以用 shell 来启动、挂起、停止程序

### 变量

shell 变量分为系统变量和用户自定义变量：

- 系统变量，例如 `$PATH, $HOME, $USER`等
- 自定义变量
- 显示当前 shell 中所有的变量 `set`

在 shell 中定义变量：`a=xxx`（**注意 = 两侧不能有空格**），撤销变量为`unset var`，例如：

```shell
var1=100
echo $var1
# 不要加空格
# 使用变量 在变量名前面加 $ 符号
echo a=$var1
echo "b=$var1"
```

注意变量一般约定使用大写字母，将命令返回值返回给变量：

```sh
b=`date`
echo B=$b
# 输出
B=Mon Dec 5 11:56:00 CST 2022
```



## Q&A

### Linux 无法获得锁 /var/lib/dpkg/lock-frontend-open

出现这个问题是因为有其他的程序正在占用 apt-get 安装进程，可以手动杀死该进程：

```
sudo rm /var/lib/dpkg/lock-frontend
```

### ssh无法连接本地虚拟机

有可能是虚拟机 ssh 服务端，解决方案参考：

> https://www.dreams-true.com/lxqb/2250.html

### 主机名修改

使用 `hostname` 查看主机名，使用`vim /etc/hostname`打开的文件中修改

### 配置主机名与 hosts 映射

在 windows 下，找到`C:\Windows\System32\drivers\etc\hosts`文件，添加内容例如：`192.168.200.1 name`

在 linux 下，打开：`vim /etc/hosts`在文件中添加同上内容，即可使用 ping 命令按照主机名测试通讯

### 主机名解析过程

Hosts：一个用于记录 IP 和 Hostname 的映射关系

DNS：（Domain Name System），作为域名和 IP 地址相互映射的一个分布式数据库

主机名解析过程：

- 浏览器首先检查浏览器缓存中有没有该域名解析IP地址，如有，则调用该 IP 完成解析，如果没有，就检查 DNS 解析器缓存，如果有直接返回 IP 完成解析（本地解析器缓存）
  - 在 PC 首次成功访问某一个网站后，在一定时间内，浏览器或操作系统会缓存其 IP 地址（DNS解析记录），在 windows 下使用命令 `ipconfig /displaydns`查看
- 如果本地的解析器缓存中没有找到对应的映射，则检查系统中 hosts 文件中的域名 IP 映射，如有则返回
- 到域名服务器DNS进行解析域（多级）









