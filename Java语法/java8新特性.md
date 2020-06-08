## java8新特性

### Lambda表达式

#### 1. 介绍

-   是一个匿名函数，理解为一段可以传递的代码，将代码像数据一样进行传递

-   Java8 中引入了一个新的操作符  " -> " 称为Lambda操作符

    左侧：Lambda 表达式的参数列表

    右侧：Lambda 表达式中所执行的功能，即Lambda体

-   Lambda表达式引用了同级别的变量，则变量是final

-   使用Lambda表达式实现函数式接口，有且仅有一个抽象方法的接口，java8之前使用匿名内部类实现

-   自定义函数式接口可以用@FunctionalInterface

#### 2. 语法格式

-   无参数，无返回值     ：(  )  ->  执行语句
-   一个参数，无返回值 ：( x )  ->  执行语句 
-   多个参数，有返回值 :  ( x,  y )  -> {执行语句1；执行语句2；...; return 语句}
-   如果只有一条语句     :  ( x,  y ) -> 执行语句  //**return**关键字，大括号可以省略
-   Lambda表达式的参数列表的数据类型可以省略不写，因为JVM可以根据上下文进行**类型推断**
-   左右遇一括号省， 左侧推断类型省，能省则省

#### 3. 内置四大核心函数式接口

-   Consumer<T>   :  消费型接口

    ​          void accept(T  t) ；

-   Supplier<T>  :  供给型接口

    ​           T get(  ) ；

-   Function<T, R>   :  函数型接口  

    ​          R  apply(T  t) 

-   Predicate<T>   : 断言型接口

    ​          boolean test(T  t)

-   其他接口，不同的**参数类型**和**返回类型**

#### 4. 方法引用

-   概念：若Lambda体中的内容已经有方法实现了,我们可以使用方法引用(Lambda表达式的另外一种表现形式）

-   语法形式：

    -   对象：：实例方法名
    -   类名：：静态方法名
    -   类名：：实例方法名 = ClassName：：method

-   注意：我需要实现的接口里方法的参数列表与返回值应该与已经实现的方法的参数列表与返回值保持一致

    ​            参数列表中的第一个参数是实例方法的调用者，第二个参数是实例方法的参数时，可以使用第三种

#### 5. 构造器引用

-   语法形式：
    -   ClassName：：new
-   注意：需要调用的构造器的参数列表要与函数式接口中的参数列表保持一致

#### 6. 数组引用

-   语法形式
    -   type[]：：new

---

### Stream的API

#### 1. 什么是Stream

-   数据渠道，用于操作数据源（集合数组）所生成的元素序列，集合讲的是数据，流讲的是计算
-   Stream不会自己存储元素，Stream不会改变源对象，Stream操作是延迟执行的

#### 2. Stream的三个操作步骤

-   创建Stream
    -   可以通过Collection系列集合提供stream(  )和parallelStream(  )
    -   通过Arrays中的静态方法stream(  )获取数组流
    -   通过Stream中的静态方法of(  )
    -   创建无限流，迭代Stream.iterate(  )；生成Stream.generate(  )
    
-   中间操作
    -   筛选与切片：filter，limit，skip，distinct
    
    -   映射：Map  接收Lambda，将元素转换为其他形式或者提取信息，函数作为参数被应用到每个元素上
    
        ​           flatMap 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有的流连接成一个流
    
    -   排序：sorted(  ) 自然排序--Comparable
    
        ​           sorted(Comparator com) 定制排序--Comparator
    
-   终止操作

    -   查找与匹配：allMatch, anyMatch, noneMatch, findFirst, findAny, count, max, min
    -   归约：reduce(T identity, BinaryOperator<T> accum) 将流中的元素反复结合起来，得到一个值
    -   收集：collect  将流转换为其他形式，涉及Collectors类，groupBy, partitionBy, joining等方法

---

### 并行流与串行流

-   parallel(  ) 与 sequential(  ) 在并行流与顺序流之间进行切换
-   底层是**fork - join** 实现，并行实现速度更快，cpu利用率更高

---

### Optional类

-   Optional.of(  ) 创建一个Optional实例
-   Optional.empty(  ) 创建一个空的Optional实例
-   Optional.ofNullable(T  t)  若T不为空则创建Optional实例，否则创建空实例
-   isPresent(  )  判断是否包含值
-   orElse(T  t)  如果对象包含值，则返回该值，否则返回T
-   orElseGet(Supplier  s)  如果对象包含值返回该值，否则返回s获取的值
-   map(Function  f)  如果有值对其处理并返回处理后的Optional，否则返回Optional.empty(  )
-   flatMap(Function mapper) 与map类似， 要求返回值必须是Optional 

---

### 接口中的默认方法与静态方法

-   类优先原则，接口定义了一个默认方法，父类或者接口中又定义了一个同名方法
-   接口冲突， 接口提供了一个默认方法，另一个接口也提供了具有相同名称和参数的方法（无论是否为默认），必须覆盖该方法来解决冲突
-   接口中允许有静态方法

---

### 新日期与时间的API

-   LocalDate：

-   LocalTime：

-   LocalDateTime：

    

