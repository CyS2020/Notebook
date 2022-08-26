### Linux操作系统

#### 1. Linux体系结构
- 体系结构分为用户态(用户上层活动)和内核态
- 内核：本质上是一段管理计算机硬件设备的程序
- 系统调用：内核的访问接口，是一种不能再简化的操作
- 公用函数库：系统调用的组合拳，对系统调用的封装，将简单的业务逻辑接口呈现给用户
- Shell命令：命令解释器，可编程，下通系统调用，上通应用程序，充当胶水角色

#### 2. 如何查找特定的文件
- find path [options] params
- 常用options: -name; -iname

#### 3. 检索文件内容
- grep [options] [pattern] [file] (-A后 -B前 -C前后)；file为`*`代表当前文件夹下所有文件
- 管道操作符"|":可以将指令连接起来，前一个指令的输出作为后一个指令的输入
- 只处理前一个命令正确输出，不处理错误输出
- 右边命令必须能够接收标准输入流，否则传递过程中数据会被抛弃
- 常用来作为接收数据管道的命令有sed, awk, grep, cut, head, top, less, more, wc(统计), join, sort, split等
- xxx | grep '匹配内容'; grep -o '正则'; grep -v '排除' 

#### 4. 对文件内容做统计
- awk [options] 'cmd' file
- 一次读取一行文本，按输入分隔符进行切片，切成多个组成部分
- 将切片直接保存在内建的变量中，$1，$2...($0表示行的全部)
- 支持对单个切片的判断，支持循环判断，支持默认分隔符为空格

#### 5. 批量替换掉文档里的内容
- sed [options] 'sed command' filename
- 全名stream editor，流编辑器，适合用于对文本行内容进行处理

#### 6. 查看日志常用命令 
- tail -f log 实时查看日志，可以搭配 grep 使用
- cat 查看日志全部内容，搭配grep使用，压缩文件使用zcat

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

#### 9. 控制台输出到文件
- 使用 `> filename` 将输出到控制台的内容输出到指定文件

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

#### 12. git配置
- ssh -vT  git@adc.github.microsoft.com : 检验ssh配置
- nslookup adc.github.microsoft.com 192.168.204.2 : 检测dns域名解析
- vim /etc/resolv.conf : 虚拟机配置dns解析
```
(有时候配置一个可能会更好)
nameserver 8.8.8.8
nameserver 192.168.204.2
```

#### 13. 恢复ens33接口
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

#### 14. alias简化命令
```
alias ck8s='kubectl -n cys' : 设置别名
alias : 查看别名
unalias ck8s : 删除别名
```

#### 15. git命令
- `git clean -f` : 清除未跟踪的文件
- `git clean -fd` : 清除未跟踪的文件夹
- `git checkout -- .` : 清除已修改未提交的文件
- `git switch task/DDEC-5288_multiple_domains_search` : 切换到远程有本地没有的分支
- `git branch -d` : 删除分支
- `git branch -m` : 分支重命名
- `git fetch --all --prune` : 以删除过时的跟踪分支。

#### 16. 其他命令
- `cat etc/hosts`: 查看域名
