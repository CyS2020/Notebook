### Code归约
- 命名规范(包名.类名.函数名.变量名)、函数40行类400行、严格使用访问限制符(private、protected、public)、代码格式化类引入优化
- 捕获异常需要打印描述信息还需要打印堆栈信息：logger.error(e.getMessage, e);
- 数组操作善于使用流操作Integer[] integers = Arrays.stream(value.split("\\.")).map(Integer::parseInt).toArray(Integer[]::new)
- 一般情况下从map中get某个值都需要进行判空处理，或者使用getOrDefault()方法
- Boolean值进行逻辑判断时候：Boolean.FALSE.equals()，基本数据类型boolean前面使用！即可
- 回调函数就是编写的时候不确定的函数行为，作为参数在调用的时候显示传参，调用行为执行相应的操作，回调就是个高大上的词罢了
- 代码中读取文件时候使用Class.getResourceAsStream()方法，避免在IDEA中能找到路径而在maven打包后找不到文件路径
- String属于不可变类型，字符串连接的时候使用StringBuilder.append().append...append();
- 某个类中，需要其他组合类：声明时即创建(独立功能)，构造函数中赋值(必要信息)，方法中初始化(加工信息)
- 需要容器时，只定义声明，然后在一个函数中创建并填充，然后将该容器返回给刚才的声明。例如：List<String> list = getList();
- javaBean:1.所有属性为private; 2.提供默认的构造方法; 3.提供getter和setter; 4.实现serializable接口

### 重构代码
- 使用IDEA查找get,set的调用地方，然后一一修改，并保证单元测试通过
- 在修改逻辑的时候，越往里挖，需要改动的地方则越少，所以尽量在深层次源头地方进行修改

### 莫名Bug
- mvn中使用jacoco统计代码覆盖率产生合成属性，所以在使用反射时会产生问题，单元测试可能在IDEA里能通过，在mvn test中就通不过

#### ArrayList源码
- Object.clone()方法为浅拷贝
- ArrayList中的modCount记录数组修改的次数，用于并发修改检查(ConcurrentModificationException)
- ArrayList中的SubList返回的是视图，增删会在源数组上进行修改
- ArrayList中的spliterator是在stream中进行并发遍历时用到的
- ArrayList中删除时除了使用iterator(并发时需加锁)还可以使用removeIf(Predicate<? super E> filter)
- ArrayList中普通for循环、增强for循环中执行add和remove操作会引发什么问题？
- ArrayList中的排序算法是调用的是Arrays.sort()而LinkedList中没有排序方法

#### Objects中常用的方法
- Objects.equals()
- Objects.requireNonNull()

#### 数组的复制方法
- System.arraycopy()
- xxx.clone()
- Arrays.copyOf()

#### forEach中的操作的外部变量必须是final的
- forEach在此处使用的是lambda 表达式，可以简单的把lambda表达式理解为匿名内部类(lambda 表达式不仅仅是内部类这么简单)。而匿名内部类的变量必须用final修饰。
- 类的生命周期比方法的生命周期长，同理匿名类的生命周期比方法的生命周期长。
- 方法运行完了，变量释放了，但是匿名内部类还在。这时就要求匿名内部类引用的变量必须还在，这样才能保持数据的一致性。

