#### 基本原理
- 容器之间软件隔离，共用硬件；docker为环境管理引擎，容器看做简易版的Linux环境
- 容器的应用进程直接运行于宿主机的内核，容器内没有自己的内核，也没有进行硬件虚拟
- docker三大特征：镜像、容器、仓库；仓库速度慢，配置阿里云镜像
- 类比Java可以理解为镜像就是类，容器就是实例化的对象，一个镜像可以生成多个容器实例
- docker run 操作先本机寻找镜像文件找不到则去阿里云pull一个镜像，然后以该镜像为模板生产容器实例运行
- 宿主机 -> docker -> 容器实例；win10 -> VMware -> centos7 -> docker -> centos7
- 虚拟机安装完docker之后记得开启阿里云镜像加速

#### 常用命令
- `systemctl start docker` : 启动docker服务
- `systemclt stop docker` : 关闭docker服务
- `systemctl enable docker` : 设置开机自启动
- `systemctl status docker` : 查看docker运行状态
- `docker version` `docker info` `docker --help` : 帮助命令
<br/>

- `docker images` : 列出本地主机上的镜像 -a:列出本地所有(含中间印象层) -q:只显示镜像id
- `docker search` : 查找某个镜像 -f:列出收藏数不少于指定值的镜像
- `docker pull` : 下载镜像
- `docker rmi` : 删除镜像 -f:强制删除
<br/>

- `docker run [OPTIONS] IMAGE [COMMAND][ARG...]` : 运行容器 OPTIONS说明 -d:后台运行 -p端口映射 -it i交互t伪终端
- `docker ps` : 列出当前所有正在运行的容器 -a:正在运行的和历史上运行过的
- `exit` : 退出并关闭容器，在伪终端中执行 ctrl + p + q:退出不关闭容器
- `docker start ` : 启动所关闭的容器
- `docker restart` : 重启正在运行的容器
- `docker stop` : 温柔的停止，慢慢的停止
- `docker kill` :  强制停止，立即停止
- `docker rm` : 删除已停止的容器
- `docker logs` :  查看容器日志，-t:是加入的时间戳 -f:跟随最新的日志打印 --tail后面显示多少条
- `docker top` : 查看容器内的进程
- `docker inspect` : 查看容器内部细节
- `docker exec -it` : 重新进入正在运行的容器并以命令行交互不加i就隔山打牛了 或docker attch 加sh进入
- `docker cp 容器id 源路径 宿路径` : 容器内拷贝文件到主机上
- `docker update mysql --restart=always` : 随docker启动而运行
<br/>

- `docker commit ` : 提交容器副本使之成为一个新的镜像

#### 镜像原理
- `镜像` : 是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，包含运行某软件所需的全部内容包括代码、运行时库、环境变量、配置文件；docker的镜像实际上有一层层的文件系统组成，这种层级的文件系统UnionFS
<br/>

- `UnionFS` : 联合文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。特性是一次记载多个文件系统，但从外面看只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终文件系统会包含所有底层文件和目录
<br/>

- `bootfs(boot file system)` : 主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统；Docker镜像的最底层就是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就在内存中了，此时内存使用权已由bootfs转交给内核，此时系统也会卸载bootfs。
<br/>

- `rootfs(root file system)` : 在bootfs之上。包含最典型Linux系统中的/dev,/proc,/bin,/etc等标准目录和文件，rootfs就是各种不同操作系统发行版，比如Ubuntu，Centos等等。docker共用bootfs，只需rootfs所以docker上的centos很小
<br/>

- `分层结构` : 最大的好处就是共享资源，比如多个镜像都是从相同的base镜像构建而来，那么宿主机上只需要保存一份base镜像同时内存中也只需要加载一份base镜像，就可以为所有容器服务了而且镜像的每一层都可以被共享。
<br/>

- `写与读` : 容器启动时一个新的可写的被加载到镜像的顶部。这一层通常被称作容器层，容器层之下叫镜像层。镜像层都是只读。

#### 容器数据卷
- 将应用于运行环境打包成容器运行，运行可以伴随着容器，但是我们对数据的要求是希望持久化的，容器之间希望有可能共享数据
- docker容器产生的数据如果不通过docker commit生成新的镜像，使得数据作为一部分保存下来，那么当容器删除后数据自然也就没有了，为了能保存数据在docker中我们使用卷
- `数据卷` : 数据卷可以在容器中共享，卷中的更改可以直接生效，数据卷中的更改不会包含在镜像的更新中，数据卷生命周期一直持续到没有容器使用它为止
- 数据卷的理念是实现数据的持久化和数据的共享。实现容器到主机的数据共享，主机到容器的数据共享，数据时双向修改的一方修改另一方同步
- `dockerFile` : 对镜像源码级的文件，与镜像的关系类似于 .java 与 .class；编写 -> 构建 -> 运行
- 数据卷容器 : 命名的容器挂载数据卷，其他容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器，父子互相传递

- `docker run -it -v 宿主机绝对路径 : 容器内目录 镜像名` 实现容器和宿主机之间的数据共享
- `docker build -f 文件路径` : 根据编写的dockerFile文件生成镜像

#### 常见问题
- docker 内部命令行模式只有 #, 
```
cp /etc/skel/.bash* /root/
su
```
- docker 内部没有安装vim
```
apt-get update
apt-get install vim
```
- vim 无法使用右击粘贴功能
```
vim /usr/share/vim/vim81/defaults.vim
修改如下内容
if has(‘mouse’)
set mouse-=a
endif

注：vim81有可能是vim80之类的
```

### 常用环境配置命令
- 安装docker: 
```
yum install docker
```
- 配置镜像加速
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://x4x5z98e.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
- 安装mysql命令
```
docker pull mysql:5.7
```
- 启动mysql命令

```
docker run -p 3306:3306 --name mysql_scy -e MYSQL_ROOT_PASSWORD=SUchunyang -d mysql:5.7
```
- mysql基本配置
```
vim /etc/mysql/my.cnf
增加如下内容
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
skip-name-resolve
```
- 安装redis命令
```
docker pull redis
```
- 启动redis命令
```
docker run -p 6379:6379 --name redis_cys -d redis redis-server --appendonly yes
```