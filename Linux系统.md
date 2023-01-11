### Linux操作系统

#### window + wsl2 = linux
- https://learn.microsoft.com/en-us/windows/wsl/install-manual
- https://www.how2shout.com/how-to/install-centos-on-windows-10-subsystem-for-linux.html
- https://github.com/gitextensions/gitextensions/issues/9954 ` vim ~/.gitconfig`

#### 部署虚拟机centos7环境
- VMware - 编辑 -虚拟网络编辑 -选择桥接模式 -桥接至主机网络适配器上
- dhclient命令生成ip，然后修改vim /etc/sysconfig/network-scripts/ifcfg-ens33
- ps -ef | grep dhclient 查看进程 然后 kill -9 进程id 关闭该进程
- 配置静态ip地址，注意的是先查看主机ip地址信息然后配置ip，子网掩码，网关，dns; 虚拟机网络适配器选桥接模式(不勾选复制...)
- 配置静态ip后, 虚拟机linux执行systemctl restart network.service就可以连接外网
- 虚拟机上网，主机与虚拟机互相ping，才算成功；一般情况下主机都能ping通虚拟机，只是虚拟机有时候ping不通主机
- 控制面板\网络和 Internet\网络和共享中心\高级共享设置--启用文件和打印机共享，虚拟机即可ping通主机
- 如果还出现ping不通的情况检查下面的参数肯定写错了，检查不出来就删了重写
- 发现数据库还是连接不上, 或者其他服务redis等通过 telnet ip port 来测试端口访问, 访问失败则关闭防火墙
- 防火墙状态变化可能会引起docker自定义链有问题, 最好重启下docker(docker启动image错误)
- 如果发现ip地址互相可以ping通, 但是端口不通先试试getenforce与setenforce 0命令, 还不行就重启linux系统
```
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.0.109
NETMASK=255.255.255.0
GATEWAY=192.168.0.1
DNS1=8.8.8.8
```

#### 1. Linux体系结构
- 体系结构分为用户态(用户上层活动)和内核态
- 内核：本质上是一段管理计算机硬件设备的程序
- 系统调用：内核的访问接口，是一种不能再简化的操作
- 公用函数库：系统调用的组合拳，对系统调用的封装，将简单的业务逻辑接口呈现给用户
- Shell命令：命令解释器，可编程，下通系统调用，上通应用程序，充当胶水角色

#### 2. 如何查找特定的文件
- find path [options] params
- 常用options: -name; -iname 例如`find /home -name "*.sh"`

#### 3. 检索文件内容
- grep [options] [pattern] [file] (-A后 -B前 -C前后)；file为`*`代表当前文件夹下所有文件
- 管道操作符"|":可以将指令连接起来，前一个指令的输出作为后一个指令的输入
- 只处理前一个命令正确输出，不处理错误输出
- 右边命令必须能够接收标准输入流，否则传递过程中数据会被抛弃
- 常用来作为接收数据管道的命令有sed, awk, grep, cut, head, top, less, more, wc(统计 -l), join, sort, split等
- xxx | grep '匹配内容'; grep -o '正则'; grep -v '排除' 

#### 4. 对文件内容做统计
- awk [options] 'cmd' file
- 一次读取一行文本，按输入分隔符进行切片，切成多个组成部分
- 将切片直接保存在内建的变量中，$1，$2...($0表示行的全部)
- 支持对单个切片的判断，支持循环判断，支持默认分隔符为空格
- xargs 构建命令的参数

#### 5. 批量替换掉文档里的内容
- sed [options] 'sed command' filename
- 全名stream editor，流编辑器，适合用于对文本行内容进行处理

#### 6. 查看日志常用命令 
- tail -f log 实时查看日志，可以搭配 grep 使用
- cat 查看日志全部内容，搭配grep使用，压缩文件使用zcat
- tee file.txt 输出到控制台并保存到文件

#### 7. 系统监控命令
- ps -ef | grep xxx 查看进程详细信息
- ps -e | grep xxx 查看进程
- top -p xxx 查看某个进程cpu
- kill -9 xxx 杀死某个进程

#### 8. 安装程序
- 1. 使用 wget 命令下载安装包
- 2. 使用 unzip 或 tar -xvf 命令解压软件
- 3. 使用 ./configure --prefix= 配置安装路径
- 4. 使用 make 命令编译程序
- 5. 使用 make install 命令安装程序
- 6. 使用 make clean 命令删除安装时产生的临时文件。
- 7. 使用 vim ~/.bash_profile 添加环境变量
参考教程：https://www.cnblogs.com/hanzhi/articles/10046003.html

#### 9. 新建文件
- `> filename`：将输出到控制台的内容输出到指定文件
  - `>> filename`: 将输出追加到文件末尾
- `touch`：创建空文件
- `vim xxx`：创建并编辑文件
- `mv old new`: 重命名文件

#### 10. 文件传输命令(注意文件权限)
- `ssh ubuntu@192.168.0.100`: 远程连接到服务器上
- `scp xxxfile ubuntu@192.168.0.100:/home/ubuntu/`: 本地传输到远程服务器
- `scp ubuntu@192.168.0.100:/home/ubuntu/xxxfile /`: 远程服务器传输到本地
- `docker cp /home/ubuntu/xxxfile 容器id:/`: 宿主机传输到容器内
- `docker cp 容器id:/xxxfile /home/ubuntu/`: 容器内传输到宿主机
- `~` : 当前用户目录
- `/` : 系统根目录

#### 11. 显示系统环境变量
- export 设置环境变量
- echo 查看环境变量
- env | grep VER 快速查看当前微服务部署的版本

#### 12. 恢复ens33接口
```
# 先试试
nmcli networking on
```
```
# 查看所有网卡信息 发现ens33没有静态IP信息
ifconfig -a 
chkconfig network off      
chkconfig network on  
service NetworkManager stop
service NetworkManager start
```

#### 13. alias简化命令
```
alias ck8s='kubectl -n cys' : 设置别名
alias : 查看别名
unalias ck8s : 删除别名
```

#### 14. git配置
- `git config --global user.name chunyang`
- `git config --global user.email chunyang@microsoft.com`
- `ssh-keygen -t ed25519 -C "chunyang@microsoft.com"`
- `cat ~/.ssh/id_ed25519.pub`
- github: `SSH and GPG keys` -> `New SSH key`
- `ssh -T git@github.com` 检验ssh配置
- `ssh -vT  git@adc.github.microsoft.com` : 检验ssh配置
- `nslookup adc.github.microsoft.com 192.168.204.2` : 检测dns域名解析
- 192.168.204.2 在网络的属性里面 -- Ipv4 DNS服务器
- `vim /etc/resolv.conf`: 虚拟机配置dns解析
```
(有时候配置一个可能会更好)
nameserver 8.8.8.8
nameserver 192.168.204.2
```

#### 15. git命令
- `git clean -f` : 清除未跟踪的文件，没有add的文件
- `git clean -fd` : 清除未跟踪的文件夹，没有add的文件夹
- `git checkout .` : 清除所有已修改未提交的文件
- `git checkout [filename]`: 清除某个已修改未提交的文件
- `git switch task/DDEC-5288_multiple_domains_search` : 切换到远程有本地没有的分支
- `git branch -d` : 删除分支
- `git branch -m` : 分支重命名
- `git fetch --all --prune` : 以删除过时的跟踪分支。
- `git stash -p` : 隐藏部分内容，根据y,n来进行筛选
- `git diff [filename]`: 查看文件修改内容
- `git push origin [branchname]`: 提交某个分支到远程仓库origin
  - `git push me [branchname]`: 提交分支到远程仓库me
- `git remote add localRepo git@xxx.git`: 关联xxx远程仓库本地名localRepo
- `git remote -v`: 查看本地库已经关联的 github 远程仓库

#### 16. 其他命令
- `cat etc/hosts`: 查看域名

#### 17. 文件权限
- `-rwxr-xr-x`: 在Linux 系统中权限是区分用户的，即用户u、组用户g、其他用户o，第一位表示文件的类型，-代表文件，d代表目录，其他每个用户占三个字符
- 这里-rwxr-xr-x对应如下关系：其中r表示读、w表示写、x表示可执行，-表示没有权限；例如`chmod ug+w,o-w file1.txt file2.txt`
- `su`: 需要目标账户的密码
- `sudo`: 需要当前账户的密码
- `su -`: (-)开关为您提供根环境(路径和shell变量)，而不是简单地为单个命令提供根用户权限，同时保留您自己的环境。
- `sudo -i`: 将您作为根用户带到一个交互会话
- `sudo su -`: 有时会遇见各种命令，只是给 `su -` 命令添加了 sudo 执行权限

|第一位|用户|组用户|其他用户|
|:---:|:---:|:---:|:---:|
|-|rwx|r-x|r-x|

### Shell Script
#### 1. First Script
- `#!/bin/sh`: 执行的此脚本使用/bin/sh来解释执行
- `echo`: 会在参数之间默认添加上空格，`echo *` 类似于 ls 命令
- `read VAR`: 将输入读入某个变量VAR中
- `cat /tmp/myfile | grep "mystring"` = `grep "mystring" /tmp/myfile`

#### 2. 变量
- 变量声明不能有空格。`VAR=value` 声明有效; `VAR = value` 声明无效
- 使用 `export` 导出为全局变量，shell关闭后变量消失
- 使用 `.` 在交互式的shell中运行脚本而不是使用一个新的shell。例如 `. ./myvar2.sh`
- 使用反引号在脚本中会启用 sub shell 运行程序(外部程序)，例如 echo -en "What is your name [ \` whoami \` ]"
- 通过`$`使用变量，例如`$MyVar`，使用`${}`替换变量，例如`${MyVar}_file`
- 变量被双引号包裹表示保留换行符，变量显示多行，否则变量结果为又长又大的一行文本
- 为您设计好的一组变量，大多数不能被赋值
  - `$0`: 程序被调用时的基名
  - `$1 .. $9`: 调用脚本的前9个附加参数
  - `$@ `: 调用脚本的所有附加参数，同 `$*`
  - `$#`: 是调用脚本时使用的参数数量。
  - `$?`: 这包含最后(上)一个运行命令的退出值
  - `$$`: 变量是当前运行的shell的PID(进程标识符)
  - `$!`: 变量是最后一次运行的后台进程的PID。这有助于在流程进行工作时跟踪它。
  - `shift`: 它在执行后将命令行参数移动到左边的一个位置。等于 `shift 1`
  - `:-`、`:=`: 用来设置默认值，例如`${myname:-`whoami`}`，飘号启用sub shell 运行 External Programs
- 习惯于其他语言的程序员可能会对shell函数的作用域规则感到惊讶。基本上除了参数($1、$2、$@等)之外，没有作用域
- 数组语法格式：`array_name=(value1 value2 ... valuen)`
- map语法格式: 需要使用关键字 declare 声明
  ```
  declare -A myMap=(["my01"]="01" ["my02"]="02")
  myMap["my03"]="03"
  myMap["my04"]="04"
  ```

#### 3. 流程控制
- 循环控制语句使用for与while
- `${arr[@]}`用于array，`${!arr[@]}`map遍历key，`${arr[@]}`map遍历value，`@`和`*`作用一致
  ```
  for i in {0..9}
  do
    ...
  done
  ```
- `while :` 永远为true，ctrl + c 退出循环
- `while read input_text`: 用于读取文件 `done < file.txt`
  ```
  while []
  do
    ...
  done
  ```
- 流程控制语句使用case
  ```
  case $input_text in
        hello)          echo English    ;;
        howdy)          echo American   ;;
        gday)           echo Australian ;;
        bonjour)        echo French     ;;
        "guten tag")    echo German     ;;
        *)              echo Unknown Language: $input_text ;;
   esac
  ```
- test语句`[]`, 前后被空格包围，test通常被if或while间接invoke
- `-lt <`, `-gt >`, `-le <=`, `-ge >=`, `=`, `!=`, `-n`, `-f`, `-nt`, `-x`
- `;` 代表一行结束，`\` 代表一行未结束
- 居然还支持三元运算，例如 `[ $X -ne 0 ] && echo "X isn't zero" || echo "X is zero"`
  ```
  if  [ something ]; then
    # do something
  elif [ something_else ]; then
    # do something
  else
    # do something
  fi
  ```
  
#### 4. 函数Function
- 函数返回返回值的方式
  - 改变一个或一些变量的状态
  - 使用 exit 命令退出shell脚本
  - 使用 return 命令退出函数，并返回值给调用者，调用者使用 `$?` 接收
  - echo output 到 stout，它将被调用者捕获，就像`c= ' expr $a + $b '`被捕获一样
- 这很像C语言，因为exit停止程序，return将控制返回给调用者。不同之处在于shell函数不能更改其参数，尽管它可以更改全局参数。
- 定义函数function关键字可省略，使用 2. 里面定义好的一组参数; 调用的时候 myfunc param1 param2 ...
  ```
  myfunc(){
    ...
  }
  ```
- `. ./common.lib` 引用其他库在代码开头加入运行命令，或者使用 `source` 命令

#### 5. shell code
- `set [+-abCdefhHklmnpPtuvx]`: 能设置所使用shell的执行方式，可依照不同的需求来做设置。
- `source FILENAME [arguments]`: Source是一个shell内置命令，用于读取和执行文件的内容(通常是一组命令)
- $()、${} 、$(()) 区别于联系
  - `$()`: 命令替换 `$(date + '%Y-%m-%d-%H-%M-%S')`
  - `${}`: 变量替换 `echo ${file#*/}`
  - `$(())`: 整数操作 `echo $((${a} + ${b} * $c))`
