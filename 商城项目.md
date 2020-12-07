#### 主键与索引
- 表关系、表结构、唯一索引、单索引及组合索引、时间戳
- 主键一定是唯一性索引，唯一性索引并不一定就是主键。
- 一个表中可以有多个唯一性索引，但只能有一个主键。
- 主键列不允许空值, 而唯一性索引列允许空值

#### 运行jar包
- java -jar mall-0.0.1-SNAPSHOT.jar 命令运行主程序

#### java对象
- po: persistent object 持久层对象
- pojo: plain ordinary java object 普通java对象只有属性+get/set方法

#### Modle层(模型层)
- dao层：dao层叫数据访问层, 全称为data access object, 属于一种比较底层, 比较基础的操作, 具体到对于某个表、某个实体的增删改查, 取出数据封装后返回给service层
- service层：service层叫服务层, 被称为服务, 肯定是相比之下比较高的层, 比如取得连接、关闭数据库连接、事务回滚, 一些复杂的逻辑业务, 相当于将几种操作封装起来

#### 框架理解
- 想要框架去执行某些文件xml中的方法需要让框架找到xml, 则需要application.yml配置文件
- java代码与框架交互，要么通过注解要么配置xml, 将调用转向注解或者xml的具体行为，java定义抽象

#### mybatis三剑客
- Mybatis-generator: 用来生成pojo、mapper、xml文件
- Mybatis-plugin: 用来查找方法对应的xml中的sql语句(Free MyBaits plugin)
- Mybaits-PageHelper: 数据库分页插件
