## 配置java语言开发环境

### 1. 下载java语言开发工具包JDK

​		-> <http://www.oracle.com/technetwork/java/javase/downloads/index.html>

​		-> 安装到 D:\Program Tools\Java\jdk-14.0.1\ 位置

### 2. 配置环境变量

​		-> 控制面板

​		-> 系统和安全

​		-> 系统

​		-> 高级系统设置

​		->  环境变量

​		-> 系统变量

​		-> 新建变量名：**JAVA_HOME** 变量值：**D:\Program Tools\Java\jdk-14.0.1**

​		-> 新建变量名：**CLASSPATH** 变量值：**.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;**

​		-> 变量名： **Path**  添加变量值：**%JAVA_HOME%\bin**

​										添加变量值：**%JAVA_HOME%\jre\bin**

### 3. 测试环境		

​		-> 使用 cmd测试 java -version; java; javac等命令

​		-> 如果配置失败会出现: **java不是内部或外部命令，也不是可以运行的程序

​						<img src="https://github.com/CyS2020/Images/raw/master/JDK%E9%85%8D%E7%BD%AE%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E5%A4%B1%E8%B4%A5.jpg"  />

### 4. 安装激活 sublime text 3

​		-> 官网下载sublime text 3

​		-> 修改host文件， host文件位置 <C:\WINDOWS\system32\drivers\etc>

```
127.0.0.1       www.sublimetext.com
127.0.0.1       sublimetext.com
127.0.0.1       sublimehq.com
127.0.0.1       license.sublimehq.com
127.0.0.1       45.55.255.55
127.0.0.1       45.55.41.223
0.0.0.0         license.sublimehq.com
```

​		-> 在help中Enter License输入激活码

​		-> 定期更新激活码网址：<http://blog.jdk5.com/zh/sublime-text-3-license-key/>

```
Member J2TeaM
Single User License
EA7E-1011316
D7DA350E 1B8B0760 972F8B60 F3E64036
B9B4E234 F356F38F 0AD1E3B7 0E9C5FAD
FA0A2ABE 25F65BD8 D51458E5 3923CE80
87428428 79079A01 AA69F319 A1AF29A4
A684C2DC 0B1583D4 19CBD290 217618CD
5653E0A0 BACE3948 BB2EE45E 422D2C87
DD9AF44B 99C49590 D2DBDEE1 75860FD2
8C8BB2AD B2ECE5A4 EFC08AF2 25A9B864
```

### 5. 安装配置IDEA

​		-> 勾选必要选项

​								![](https://github.com/CyS2020/Images/raw/master/%E5%AE%89%E8%A3%85IDEA.jpg)

​		-> 在**Font**中设置字体 英文用：**JetBrains Mono**， 中文用：**SimHei**

​		-> 在**update**中关闭自动更新

​		-> 在**File Encodings** 设置编码格式**UTF-8**

​								<img src="https://github.com/CyS2020/Images/raw/master/IDEA%E9%85%8D%E7%BD%AE%E7%BC%96%E7%A0%81.jpg" style="zoom: 50%;" />

​		-> 在**view**菜单栏下显示**ToolBar**

### 6. 配置Git与GitHub

​		-> 在IDEA中配置Git安装路径

​		-> 登录GitHub账号

- 推送操作push

    -> 创建项目的本地仓库(左侧列表显示红色)

![](https://github.com/CyS2020/Images/raw/master/IDEA%E5%BC%95%E5%85%A5%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6.png)

​		-> 配置忽略文件

​		-> 项目右击 -> Git -> Add (绿色)

​		-> 项目右击 -> Git -> Commit Directory (黑色) 

​		-> 在GitHub上创建一个仓库并复制地址

​		-> 项目右击 -> Git -> Repository -> Push -> 粘贴地址

- 克隆操作clone

  -> VCS -> Get from Version Control

- 拉取操作pull

  -> 项目右击 ->  Git -> Repository -> Pull

- 分支操作branch

  -> 项目右击 -> Git -> Repository -> branch 然后Checkout或者Merge

