### 1. 安装配置IDEA

​		-> 勾选必要选项

​                             ![](https://github.com/CyS2020/Images/raw/master/%E5%AE%89%E8%A3%85IDEA.jpg)

​		-> 在**Font**中设置字体 英文用：**JetBrains Mono**， 中文用：**SimHei**

​		-> 在**update**中关闭自动更新

​		-> 在**File Encodings** 设置编码格式**UTF-8**

​								<img src="https://github.com/CyS2020/Images/raw/master/IDEA%E9%85%8D%E7%BD%AE%E7%BC%96%E7%A0%81.jpg" style="zoom: 50%;" />

​		-> 在**view**菜单栏下显示**ToolBar**

### 2. 配置Git与GitHub

​        -> 在IDEA中配置Git安装路径 ...\Git\bin\git.exe

​        -> 登录GitHub账号(一个项目要登录一次)

​		-> 安装ignore插件，外网速度较慢可以现在下载到本地

​		-> 如图操作配置Git忽略文件，.gitignore本身无法忽略

​						<img src="https://github.com/CyS2020/Images/raw/master/%E6%B7%BB%E5%8A%A0gitignore.jpg" style="zoom: 50%;" />

-> 选择Java, 然后Generate

```
//还需手动添加三行
.idea/
target/
*.iml
```

### 3. 引入版本控制

-   -> 创建项目的本地仓库(左侧列表显示红色)

![](https://github.com/CyS2020/Images/raw/master/IDEA%E5%BC%95%E5%85%A5%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6.png)

​        -> 项目右击 -> Git -> Add (绿色)

​        -> 项目右击 -> Git -> Commit Directory (黑色) 

​        -> 在GitHub上创建一个仓库并**复制地址**

-   推送操作push

    -> VCS-> Git  -> Push -> **粘贴地址**

- 克隆操作clone

  -> VCS -> Get from Version Control

- 拉取操作pull

  -> VCS ->  Git -> Pull

- 分支操作branch

  -> VCS -> Git -> branch 然后Checkout或者Merge

