### go语言特性
- go = java + mvn + junit

#### go命令
- `go run`: 编译并运行源码文件，由于包含编译步骤
- `go build`: 编译指定的源码文件或代码包及依赖包
- `go install`: 将可执行文件自动安装到bin目录下
- `go fmt`: 用于检查并格式化成go语言的规范格式
- `go mod`: 项目初始化与模块管理，类似于mvn工具
  - `go mod init`: 初始化项目
  - `go mod tidy`: 管理模块下载与删除
- `go get`: 获取任意指定的URL的包
- `go test`: 运行单元测试，常用参数 -cover

#### 包(package)
- 同一个文件夹下的代码使用同一个包名(文件夹名)，同一个包的文件不能放在多个文件夹下
- main函数在main包里，编译时不包含main包的源代码是不会得到可执行文件的
- 如果遇见同名的包，就对包起别名进行区分，`_` 字符作为包名进行匿名导入，只运行init()方法
- 同一个包之间的访问不需要额外引入操作；文件夹名即包名而文件名确没什么鸟用
- 模块名 -> 执行文件(xxx.exe)；包名 -> 提供导入(fmt)；函数名 -> 提供调用(Println)

#### 变量与常量
- `var 变量名 变量类型`: 声明变量并且默认初始化，使用对应类型的零值初始化；引用类型初始化为nil
- `var 变量名 变量类型 = 初始值`: 声明变量并且显式初始化，函数内部可以使用`:=`代替
- `var 变量名 = 初始值`: 编译器根据变量的初始值推导出类型
- `_`: 使用多重赋值的时候，想要忽略某个值，多用于占位；不占命名空间不分配内存
- `const 常量名 = 初始值`: 常量定义的时候必须赋值；特殊关键字`iota`
- `const` 常量是指程序编译后运行时始终都不会变化的值
- 注释：函数外的每个语句必须以关键字开始(var, const, func, type等)

#### 参数传递
- go语言和java一样只有值传递, 值类型 复制 value 并赋值给新的 identifier
- 引用类型 复制 address 并复制给新的 identifier; slice 传递 24字节的数据

#### 值类型 -- 2层 identifier -> value
- `int、uint`: 根据操作系统来区分是32位还是64位
- `float32、float64`: 类似java中的float与double
- `byte、rune`: 字符类型对应uint8(ASCII)、int32
- `array`: 数组声明与初始化，长度必须是常量值，[const]type{}
  - `==` 比较两个数组，只有当两个数组的所有元素都是相等的时候数组才是相等的
- `struct`: 声明即初始化、使用new初始化、键值对初始化、值列表初始化
  - 自己实现构造函数；NewType()命名并返回结构体指针类型(推荐)
  - 方法是作用于特定类型变量的函数，类似于this；使用类型及首字母(小写)接收
  - 值接收者仍然是拷贝，修改不会反应到源对象上，推荐使用指针接收者
  - 使用匿名结构体组合完成类似继承的行为，方法可以重写但不能重载
  - 组合、方法返回值、方法接收者使用指针类型；Go语言里没有继承和虚拟方法
  - 定义字段的时候大写对外暴露；使用tag定义序列化、db、xml中字段样式
  - 结构体成员的输入顺序也有重要的意义，不同的定义顺序代表不同的结构体
  - 一个命名为S的结构体类型将不能再包含S类型的成员，可以包含 `*S` 指针类型的成员
  - 如果结构体的全部成员都是可以比较的，那么结构体也是可以比较的，比较结构体的每个成员
 - `iota`: go语言中使用该关键字来实现枚举类型

#### 引用类型 -- 3层 identifier -> address -> value
- `slice`: 声明初始化；基于数组切片；make函数初始化；
  - len() <= cap()，超过cap需要append()扩容，扩容后为新数组
  - 使用copy()函数进行切片拷贝，并不共享底层数组
  - append(s[:index], s[index+1]...) 实现切片删除功能
  - slice的len参数会进行零值初始化，map只有cap无len参数
  - slice之间不能比较，因此我们不能使用==操作符来判断两个slice是否含有全部相等元素
- `map`: 声明map[key]value{}初始化；make函数初始化；
  - 使用返回值的val, ok来接收查询返回值；获取不到val为零值，ok为false
  - 可以使用for range只遍历key、value，或者都遍历
  - 使用delete()来删除键值对；map[key]++零值自增且put
  - slice与map的cap影响扩容，提前预估优化性能
  - K对应的key必须是支持 `==` 比较运算符的数据类型
  - slice一样，map之间也不能进行相等比较；唯一的例外是和nil进行比较
  - 使用map来实现set结构，存在value置为true: `map[string]bool`
- `channel`: 通过通信共享内存，而不是通过共享内存而通信；make函数初始化
  - 有缓冲去通道：类似于ArrayBlockingQueue，缓冲区空、满会阻塞 
  - 无缓冲区通道：类似于JUC中的synchronousQueue，通道不存数据直接传递；又称同步通道
  - 发送与接收使用 `<-` 符号，类似于阻塞队列的take与put操作；关闭通道后取值则返回零值
  - 使用close函数关闭通道，虽然不是必须关闭的有垃圾回收，但推荐手动关闭
  - 通道取值方式：1) 无线循环，!ok => break；2）for range循环
  - 函数传参的时候限定单向通道：`chan<-` 往通道发数据；`<-chan` 从通道取数据
  - select多路复用：可以处理多个channel发送/接收，若多个case满足条件随机执行
- `interface`:
  - 是一种类型，一种抽象的数据类型；是一种鸭子类型的接口
  - 在使用上不需要显示使用关键字，只需要显示实现接口中的全部方法即可
  - 值类型接收者实现接口，可以使用值类型、指针类型赋值给接口引用
  - 指针类型接收者实现接口，只能使用指针类型赋值给接口引用
  - 接口可以进行嵌套，多个接口捆在一起，类似于java接口多继承
  - 空接口是指没有定义任何方法的接口，因此任何类型都实现了空接口，类似Object
  - 空接口的引用可以指向任意值，万能指针；空接口一般不需要起名字
  - 类型断言使用 xxx.() 来实现，类似于instanceof作用；或者switch xxx..(type)
- `pointer`: 指针操作`&`-取地址、`*`-根据地址取值
  - 对变量进行`&`操作可以获得这个变量的指针变量
  - 指针变量的值就是指针地址
  - 对指针变量进行`*`操作，可以获得指针变量指向的原变量的值
  - `*Type`称做Type的指针类型。*代表指针
  - new函数只接受一个参数，这个参数是一个类型，返回一个指向该类型内存地址的指针
- `function`: 函数像其他值一样，拥有类型，可以被赋值给其他变量，传递给函数，从函数返回
  - 如果两个函数的形参列表和返回值列表中的变量一一对应，那么两个函数别认为有相同的类型和标识符
  - 函数类型的零值是nil。调用值为nil的函数值会引起panic错误；函数值可以与nil比较，函数之间不可比较
  - 函数值中记录的是循环变量的内存地址，而不是循环变量某一时刻的值
```
# 为了解决这个问题，我们会引入一个与循环变量同名的局部变量，作为循环变量的副本
for _, dir := range tempDirs() {
    dir := dir // declares inner dir, initialized to outer dir
    // ...
}
```

#### 类型使用
- array, slice, map的访问与赋值均使用[]来进行
- 使用type关键字自定义类型，定义类型别名
- 值类型var声明即初始化，引用类型需要显式初始化

#### 函数
- 函数支持返回值声明变量，函数内部直接使用且可省略return后的变量
- 函数支持可变参数类型，可变参数类型在函数体中使切片类型
  - 调用者隐式的创建一个数组，并将原始参数复制到数组中，再把数组的一个切片作为参数传给被调函数。
  - 如果原始参数已经是切片类型,只需在最后一个参数后加上省略符 `...`
- 函数参数与返回值类型可以简写，若数据类型一致时可只写一个
- java与go均不支持函数默认参数，python支持
- defer访问语句类似finally，先defer的语句后执行(stack)
  - 延迟执行的匿名函数甚至可以修改函数返回给调用者的返回值
- 函数可以作为参数、变量、返回值传递，函数调用即 `变量名()` 调用，函数式编程
- 匿名函数定义完成赋给变量然后执行，也可以不赋值直接执行
- 闭包 = 函数 + 外层变量的应用
- 内置函数：close、len、new(值类型)、make(引用类型)、append、panic+recover

#### 异常处理
- 检查某个子函数是否失败后，我们通常将处理失败的逻辑代码放在处理成功的代码之前
- 如果某个错误会导致函数返回，那么成功时的逻辑代码不应放在else语句块中，而应直接放在函数体中
- 文件结尾错误（EOF）：`io.EOF`

#### 流程控制
- `fori、continue、break`: 只有for循环没有while循环
- `for range`: 遍历数组、切片、字符串、map以及通道(channel)
- `switch、case、default`: 支持增强switch块(~~break~~)，case可处理表达式
- `select、case、default`: 多路复用处理channel，没有case分支会一直阻塞

#### 反射
- reflect.TypeOf(): 函数可以获得任意值的类型对象(reflect.Type)，主要有Type和Kind
- reflect.ValueOf(): 返回的是reflect.Value类型，其中包含了原始值的值信息。专有的Elem()方法来获取指针对应的值。
- 结构体的反射：反射字段，反射方法，基本原理和java的区别不大

#### 并发编程
- goroutine线程类似于java中的Thread，`go func()` 启动并执行函数 = start()+ run()
- goroutine是协程不归操作系统管，goroutine和OS线程是m:n的关系，将m个goroutine分到n个OS thread执行
- goroutine是由go scheduler调度到OS thread上执行，再由OS scheduler调度到CPU core上执行
- java的Thread是由OS scheduler调度到CPU core上执行，java Thread与OS thread是一一对应的关系(线程模型)
- Java的用户态线程与内核态是一一对应的关系，java线程就是操作系统的线程，这么多线程由CPU core执行，就会产生上下文切换

#### 反射
- reflect.TypeOf(): 函数可以获得任意值的类型对象(reflect.Type)，主要有Type和Kind
- reflect.ValueOf(): 返回的是reflect.Value类型，其中包含了原始值的值信息。专有的Elem()方法来获取指针对应的值。
- 结构体的反射：反射字段，反射方法，基本原理和java的区别不大

#### 并发编程
- goroutine线程类似于java中的Thread，`go func()` 启动并执行函数 = start()+ run()
- goroutine是协程不归操作系统管，goroutine和OS线程是m:n的关系，将m个goroutine分到n个OS thread执行
- goroutine是由go scheduler调度到OS thread上执行，再由OS scheduler调度到CPU core上执行
- java的Thread是由OS scheduler调度到CPU core上执行，java Thread与OS thread是一一对应的关系(线程模型)
- JVM的用户态线程与内核态是一一对应的关系，java线程就是操作系统的线程，这么多线程由CPU core执行，就会产生上下文切换

#### 同步与锁
- `sync.Metux`: 互斥锁 = ReentrantLock
- `sync.RWMetux`: 读写锁 = ReentrantReadWriteLock
- `sync.Map`: 线程安全map = ConcurrentHashMap
- `sync.Once`: 资源初始化 = 双重检查 + volatile
- `sync.WaitGroup`: 线程协同 = CountDownLatch
- `atmoic`: 针对基本类型的原子操作

#### 网络编程
- net包实现tcp、udp的socket的连接与通信
- tcp粘包Nagle算法造成的发送端的粘包，凑一堆一起发送；接收端接收不及时造成的接收端粘包，tcp缓冲区中存放了几段数据
- 出现"粘包"的关键在于接收方不确定将要传输的数据包的大小，因此我们可以对数据包进行封包和拆包的操作

#### 单元测试
- `func TestName(t *testing.T)`: 逻辑测试
- `func BenchmarkName(b *testing.B)`: 基准测试
- `func TestMain(m *testing.M)`: 设置before、after

#### 疑问
- 1. 为什么可以 []byte(xxx)、string()、int() 这种构造方式；
  - 答：`type(a)` 为数据类型强制类型转换方式，指针为`(*type)(a)`，指针还可以使用unsafe.Pointer()方法，
  - 推荐使用 `s, ok := x.(T)`
- 2. 结构体中的 omitempty 字段作用
  - 答：序列化时字段若赋为零值，则不进行序列化；反序列化时未出现的值默认赋零值
  - `json:"-"` 可以直接不被JSON序列化，如果想被序列化 key-，可以设置tag为 `json:"-,"` 加个逗号
- 3. 序列化时需要string vs []byte
  - 答：通过string([]byte) vs []byte(string) 相互转换
- 4. strconv.Itoa() vs string()
  - 答：strconv.Itoa()将数字转为字符例如 97 => "97", string()将数字转为ASCII码值 97 => "a"
- 5. 如何初始化一个go项目并下载一些依赖
  - 答: 1)go mod init [module_name]; 2)go get "github.com/" 3)code自动提示并引用
- 6. 老项目有很多包找不到
  - 答: 直接运行go mod tidy即可
- 7. 单引号、双引号、反引号
  - 答：字符、字符串、不转义字符串(多行文本)

#### go code
- `...` 符号解压缩切片，则可以将切片直接传递给可变参数函数。在这种情况下，不会创建新的切片
- 引用类型传递不会拷贝对象，值传递会拷贝对象；引用类型传递与`&`取指针传递一致
- map数据结构在同一台机器上，每次迭代的元素顺序都是不一样的，这种设计是有意为之
- 对传入的map可以进行put操作，而slice不能进行append，源slice无法感知；如果使用则需返回append后的引用
- defer、panic、recover : https://go.dev/blog/defer-panic-and-recover
- 元组赋值，允许同时更新多个变量值，很有趣的特性: 交换两个值 - `a[i], a[j] = a[j], a[i]`
- 每个文件都可以包含多个init初始化函数，在程序开始执行时按照它们声明的顺序被自动调用
- 子字符串操作s[i:j]基于原始的s字符串的第i个字节开始到第j个字节（并不包含j本身）生成一个新字符串 - `s[1:5]`
