### 1. 为什么要使用Git与GitHub

- 个人：回到某个历史版本
- 团队：团队协作，并行不悖

### 2. Git与Github的安装

- 对于Git安装需要注意以下一点

  ![](https://github.com/CyS2020/Images/raw/master/%E5%AE%89%E8%A3%85Git%E9%80%89%E9%A1%B9.jpg)

### 3. Git的结构

- 工作区、版本库

  版本库存在一个**stage**（暂存区）

  <img src="https://github.com/CyS2020/Images/raw/master/Git%E7%BB%93%E6%9E%84.png" style="zoom:30%;" />

### 4. Git与GitHub的交互方式

- 团队**内**协作

  <img src="https://github.com/CyS2020/Images/raw/master/Git%E5%9B%A2%E9%98%9F%E5%86%85%E5%8D%8F%E4%BD%9C.jpg" style="zoom: 80%;" />

- **跨**团队协作

  <img src="https://github.com/CyS2020/Images/raw/master/Git%E8%B7%A8%E5%9B%A2%E9%98%9F%E5%8D%8F%E4%BD%9C.jpg" style="zoom: 80%;" />

### 5. Git的命令操作

- 设置签名

  ```
  //仓库级别——仅在当前本地库范围内有效
  git config user.name [user name]
  git config user.email [user email]
  cat .git/config                              //保存在当前目录
  ```

  ```
  //系统级别——登录当前操作系统的用户范围
  git config --global user.name [user name]
  git config --global user.email [user email]
  cat ~/.gitconfig                            //保存在用户根目录
  ```

  **就近原则**：仓库级别优先于系统用户级别**推荐**使用系统级别，所有仓库都用一个就好了

- 初始化/添加/提交/查看状态

  ```
  git init                                     //初始化本地库
  git status                                   //工作区(红色)暂存区(绿色)
  git add [file name]                          //加入暂存区Git追踪文件
  git add --update                             //所有删除，删除文件加入暂存区
  git add --all                                //所有新建，删除，修改文件加入暂存区
  git rm --cached [file name]                  //从暂存区删除该文件
  git commit [file name]                       //从暂存区提交到本地库
  git checkout -- [file name]                  //取消当前已经提交的内容
  git commit -m "commit message" [file name]
  ```
- 版本前进后退

  ```
  //查看提交日志
  git log
  git log --pretty = oneline
  git log --oneline
  git reflog				
  ```

  ```
  //前进与后退
  git reset --soft [version hash]              //仅在本地库移动HEAD指针
  git reset --mixed [version hash]             //移动指针 + 重置暂存区
  git reset --hard [version hash]              //移动指针 + 重置暂存区 + 重置工作区
  ```

- 删除文件，前提该文件有被提交到本地库

  ```
  //删除工作区文件，再添加到暂存区，然后提交到本地库
  rm [file name] 
  git add [file name]
  git commit -m "commit message" [file name]
  //回到那个文件还存在的版本号
  git reset --hard [version hash]
  //删除工作区文件，添加到暂存区，还未提交到本地库
  git reset --hard HEAD
  ```

- 比较文件差异

  ```
  git diff [file name]                         //工作区的文件与暂存区比较
  git diff [version hash] [file name]          //工作区的文件与某版本比较
  git diff HEAD                                //比较工作区的所有文件
  ```
- 其他常用命令

  ```
  git help [command]                           //查看命令文档
  ```

### 6. Git分支管理

- 协同开发，共同推进

  ![](https://github.com/CyS2020/Images/raw/master/Git%E5%A4%9A%E5%88%86%E6%94%AF%E5%8D%8F%E5%90%8C%E5%BC%80%E5%8F%91.jpg)

- 分支操作

  ```
  git branch -v                             //查看所有分支
  git branch [branch name]                  //创建一个分支
  git checkout [branch name]                //切换分支
  git merge [branch name]                   //提前切换到master分支
  ```

- 分支冲突解决

  ```
  //1. 删除特殊符号并编辑文件到满意的程度
  //2. git add [file name]
  //3. git commit -m "commit message"
  ```

### 7. Git的分支工作原理

- **版本管理就是一个链条，在这个链条的基础之上**

- **创建分支就是创建新的指针，切换分支就是移动指针的指向的位置**

  <img src="https://github.com/CyS2020/Images/raw/master/Git%E5%88%86%E6%94%AF%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.jpg" style="zoom:80%;" />

- **Git工作流一览**

    <img src="https://github.com/CyS2020/Images/raw/master/Git%E5%B7%A5%E4%BD%9C%E6%B5%81.png.jpg" style="zoom:67%;" />

### 8. Git与GitHub交互命令操作

- 创建GitHub账号，并在GitHub创建一个仓库

- 复制仓库的HTTPS地址，并在Git中创建仓库地址别名

  ```
  git remote -v                               //查看现存的地址与别名
  git remote rm [https name]                  //删除地址别名
  git remote add [https name] [https address] //给地址创建一个别名
  ```

- 推送操作

  ```
  git push [https name] [branch name]         //推送本地库某分支推送到GitHub
  ```

- 克隆操作(首次)

  ```
  git clone [https name]                     //将GitHub库克隆到本地库
  ```

  命令效果：完整的代码下载到本地， 创建远程地址别名， 初始化本地库

- 拉取操作

  ```
  git fetch [https name] [branch name]       //将GitHub库下载到本地库
  git merge [https name/branch name]         //合并, 注意参数形式"/"
  git pull [https name] [branch name]        //pull = fetch + merge
  ```

- 邀请团队成员

  <img src="https://github.com/CyS2020/Images/raw/master/GitHub%E9%82%80%E8%AF%B7%E5%9B%A2%E9%98%9F%E6%88%90%E5%91%98.jpg" style="zoom:67%;" />

- 解决冲突

  如果不是基于GitHub的最新版本所做的修改，则不能推送，需要先拉取GitHub最新版

  拉取下来后进入冲突状态按照 **6.** 中的**分支冲突解决**办法解决即可

- 跨团队协作的GitHub操作

  ```
  //1. 在GitHub上fork一份路人乙的仓库
  //2. 将该仓库clone到本地
  //3. 本地修改，推送到自己的GitHub上
  //4. 推送给路人乙Pull requests -> New pull request -> Create pull request 
  //5. 路人乙进行审核Pull request -> Files changed -> Merge pull request -> Comfirm merge
  ```

### 9. SSH免密登录

- 配置SSH登录

  ```
  cd ~                                        //进入用户根目录
  rm -r .ssh/                                 //删除ssh目录
  ssh-keygen -t rsa -C [github account]       //生成ssh目录 一直回车
  cd .ssh/                                    //进入ssh目录
  cat id_rsa.pub                              //复制文件内容
  GitHub -> Settings -> SSH and GPG keys      //粘贴内容到Key
  ```

- 使用SSH推送

  ```
  //回到工作区的目录
  git remote add [ssh name] [ssh address]     //给地址创建别名
  git push [ssh name] [branch]                //推送GitHub上
  ```


