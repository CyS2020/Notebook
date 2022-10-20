### go语言特性
- go = java + mvn + junit

#### go命令
- `go run`: 编译并运行源码文件，由于包含编译步骤
- `go build`: 编译指定的源码文件或代码包及依赖包
- `go install`: 将可执行文件自动安装到bin目录下，任何目录位置均可运行代码
- `go fmt`: 用于检查并格式化成go语言的规范格式
- `go mod`: 项目初始化与模块管理，类似于mvn工具
  - `go mod init`: 初始化项目
  - `go mod tidy`: 管理模块下载与删除
- `go get`: 获取任意指定的URL的包
- `go test -v`: 运行单元测试
  - `go test -cover`: 统计代码覆盖率
  - `go test -bench=.`: 基准测试
  ```
  # 代码覆盖详情
  go test -coverprofile=c.out
  go tool cover -html=c.out
  ```
- `go env`: 查看环境变量

#### 包(package)
- 同一个文件夹下的代码使用同一个包名(文件夹名)，同一个包的文件不能放在多个文件夹下
- main函数在main包里(文件、文件名不叫main)，编译时不包含main包的源代码是不会得到可执行文件的
  - main函数所在文件的文件名，没有要求，可以是main.go，test.go或者任意其他的文件名，但func main()必须仅有一个
- 如果遇见同名的包，就对包起别名进行区分，`_` 字符作为包名进行匿名导入，只运行init()方法
- 同一个包之间的访问不需要额外引入操作；文件夹名即包名而文件名确没什么鸟用
- 模块名 -> 执行文件(xxx.exe)；包名 -> 提供导入(fmt)；函数名 -> 提供调用(Println)
- `module github.com/olivere/elastic/v7` 引入elastic是 import 模块名/包名 (v7版本省略)
- 一般情況下main函数会定义在项目的根目录下，和go.mod文件在一起
- 多个模块协同开发，并且有依赖关系
  ```
  go mod edit -replace example.com/greetings=../greetings
  go mod tidy
  # go.mod 的依赖
  replace example.com/greetings => ../greetings
  require example.com/greetings v0.0.0-00010101000000-000000000000
  ```

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
    - 继承与java的区别，参考：`https://learnku.com/articles/43446`
  - 定义字段的时候大写对外暴露；使用tag定义序列化、db、xml中字段样式
  - 结构体成员的输入顺序也有重要的意义，不同的定义顺序代表不同的结构体
  - 一个命名为S的结构体类型将不能再包含S类型的成员，可以包含 `*S` 指针类型的成员
  - 如果结构体的全部成员都是可以比较的，那么结构体也是可以比较的，比较结构体的每个成员
 - `iota`: go语言中使用该关键字来实现枚举类型

#### 引用类型 -- 3层 identifier -> address -> value
- `slice`: 声明初始化[]type{}；基于数组切片；make函数初始化；
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
    - 对于带缓存channel，发送操作与接收操作是解耦的
    - 关于无缓存或带缓存channels之间的选择，或者是带缓存channels的容量大小的选择，都可能影响程序的正确性
  - 无缓冲区通道：类似于JUC中的synchronousQueue，通道不存数据直接传递；又称同步通道
    - 消息事件并不携带额外的信息，它仅仅是用作两个goroutine之间的同步
    - `done := make(chan struct{}) done <- struct{}{}` (done <- 1 语句更短)
    - 无缓存channel更强地保证了每个发送操作与相应的同步接收操作
  - 发送与接收使用 `<-` 符号，类似于阻塞队列的take与put操作；关闭通道后取值则返回零值
  - 使用close函数关闭通道，虽然不是必须关闭的有垃圾回收，但推荐手动关闭
    - 所有的数据已经全部发送时由发送方关闭channel
    - 存在多个发送者时不要关闭发送者 channel，而是使用专门的 stop channel。
  - 通道取值方式：1) 无限循环，!ok => break；2）for range循环 -- 推荐
    - 使用for range的时候如果channel未关闭则循环不会终止
  - 函数传参的时候限定单向通道：`chan<-` 往通道发数据；`<-chan` 从通道取数据
    - 任何双向channel向单向channel变量的赋值操作都将导致该隐式转换
  - select多路复用：可以处理多个channel发送/接收，若多个case满足条件随机执行
    - select会等待case中有能够执行的case时去执行。一个没有任何case的select语句写作select{}，会永远地等待下去
    - channel的零值是nil。对一个nil的 channel发送和接收操作会永远阻塞，在select语句中操作nil的channel永远都不会被select到
  - 两个相同类型的channel可以使用==运算符比较。如果两个channel引用的是相同的对象
- `interface`:
  - 是一种类型，一种抽象的数据类型；是一种鸭子类型的接口
  - 在使用上不需要显式使用关键字，只需要显式实现接口中的全部方法即可
  - 每一个具体类型的组基于它们相同的行为可以表示成一个接口类型
  - 值类型接收者实现接口，可以使用值类型、指针类型赋值给接口引用
  - 指针类型接收者实现接口，只能使用指针类型赋值给接口引用
  - 空接口是指没有定义任何方法的接口，因此任何类型都实现了空接口，类似Object
  - 空接口的引用可以指向任意值，万能指针；空接口一般不需要起名字
  - 类型断言使用 `x.(T)` 来实现，类似于instanceof作用；或者 `switch x := x.(type) { /* ... */ }`
    ```
    最佳实践
    if w, ok := w.(*os.File); ok {
        // ...use w...
    }
    ```
  - 函数使用一个类型断言来知道一个普遍接口类型的值是否满足一个更加具体的接口类型；并且如果满足，它会使用这个更具体接口的行为
  - 使用匿名接口组合完成类似接口继承的行为，这种方式本称为接口内嵌
  - 两个接口值相等仅当它们都是nil值或者它们的动态类型相同并且动态值也根据这个动态类型的＝＝操作相等
  - **一个不包含任何值的nil接口值和一个刚好包含nil指针的接口值是不同的**
  - ![空接口](https://github.com/CyS2020/Notebook/blob/master/images/nil%E6%8E%A5%E5%8F%A3.png?raw=true) ![非空接口](https://github.com/CyS2020/Notebook/blob/master/images/not-nil%E6%8E%A5%E5%8F%A3.png?raw=true)
  - 接口类型sort.Interface来指定通用的排序算法和可能被排序到的序列类型之间的约定
  - 接口只有当有两个或两个以上的具体类型必须以相同的方式进行处理时才使用接口  ask only for what you need
  - java那种先定义接口，再创建实现类的方式不建议；先创建实现类后续根据实际情况创建接口
- `pointer`: 指针操作`&`-取地址、`*`-根据地址取值
  - 对变量进行`&`操作可以获得这个变量的指针变量
  - 指针变量的值就是内存地址
  - 对指针变量进行`*`操作，可以获得指针变量指向的原变量的值
  - `*Type`称做Type的指针类型。*代表指针
  - new函数只接受一个参数，这个参数是一个类型，返回一个指向该类型内存地址的指针
- `function`: 函数像其他值一样，拥有类型，可以被赋值给其他变量，传递给函数，从函数返回
  - 如果两个函数的形参列表和返回值列表中的变量一一对应(方法签名)，那么两个函数别认为有相同的类型和标识符
  - 函数类型的零值是nil。调用值为nil的函数值会引起panic错误；函数值可以与nil比较，函数值之间不可比较
  - 函数值中记录的是循环变量的内存地址，而不是循环变量某一时刻的值
    ```
    为了解决这个问题，我们会引入一个与循环变量同名的局部变量，作为循环变量的副本
    for _, dir := range tempDirs() {
        dir := dir // declares inner dir, initialized to outer dir
        // ...
    }
    ```
  - deferred函数中调用recover，并且定义该defer语句的函数发生了panic异常,则函数不会继续运行，但能正常返回
  - recover会使程序从panic中恢复，并返回panic value 在未发生panic时返回nil
    ```
    最佳实践
    defer func() {
        if p := recover(); p != nil {
            err = fmt.Errorf("internal error: %v", p)
        }
    }()
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

#### 方法
- 在声明方法时，如果一个类型名本身是一个指针的话，是不允许其出现在接收器中的 `type P *int`
- 一般会约定类有一个指针作为接收器的方法，那么所有的方法都必须有一个指针接收器
- 如果类型T的所有方法都是用T类型自己来做接收器(而不是 `*T`)，调用他的任何一个方法也就会产生一个值的拷贝
- 不管你的method的receiver是指针类型还是非指针类型，都是可以通过指针/非指针类型进行调用的，编译器会帮你做类型转换
- 在声明一个method的receiver该是指针还是非指针类型时，你需要考虑两方面
  - 是这个对象本身是不是特别大，如果声明为非指针变量时，调用会产生一次拷贝
  - 如果你用指针类型作为receiver，那么你一定要注意，这种指针类型指向的始终是一块内存地址

#### 异常处理
- 检查某个子函数是否失败后，我们通常将处理失败的逻辑代码放在处理成功的代码之前
- 如果某个错误会导致函数返回，那么成功时的逻辑代码不应放在else语句块中，而应直接放在函数体中
- 文件结尾错误（EOF）：`io.EOF`

#### 流程控制
- `fori、continue、break`: 只有for循环没有while循环
- `for range`: 遍历数组、切片、字符串、map以及通道(channel)
- `switch、case、default`: 支持增强switch块(~~break~~)，case可处理表达式
- `select、case、default`: 多路复用处理channel，没有case分支会一直阻塞

#### 并发编程
- 位于两个goroutine的事件x和y的执行顺序，x是在y之前还是之后还是同时发生是没法判断的。就说明x和y这两个事件是并发的。
- 一个函数在线性程序中可以正确地工作。并发的情况下，这个函数依然可以正确地工作，那么我们就说这个函数是并发安全的
- 数据竞争会在两个以上的goroutine并发访问相同的变量且至少其中一个为写操作时发生，三种方式可以避免数据竞争
  - 第一种方法是不要去写变量，只读变量
  - 第二种避免数据竞争的方法是，使用goroutine + channel -- 串行绑定
  - 第三种称为“互斥”
- goroutine线程类似于java中的Thread，`go func()` 启动并执行函数 = start() + run()
- 为了效率，对内存的写入一般会在每一个处理器中缓冲，并在必要时一起flush到主存，类似java
- Go routines和OS线程
  - OS线程都有一个固定大小的内存块(一般会是2MB)来做栈，一个goroutine会以2KB的栈开始其生命周期，最大伸缩到1G
  - goroutine是协程不归操作系统管，goroutine和OS线程是m:n的关系，将m个goroutine分到n个OS thread执行
  - goroutine是由go scheduler调度到OS thread上执行，再由OS scheduler调度到CPU core上执行
  - GOMAXPROCS的变量来决定会有多少个操作系统的线程同时执行Go的代码(即m:n中的n)，默认的值是CPU的核心数
  - goroutine没有可以被程序员获取到的身份(id)的概念，由于thread-local storage总是会被滥用
- Java Thread和OS线程
  - java的Thread是由OS scheduler调度到CPU core上执行，java Thread与OS thread是一一对应的关系(线程模型)
  - Java的用户态线程与内核态是一一对应的关系，java线程就是操作系统的线程，这么多线程由CPU core执行，就会产生上下文切换

#### 同步与锁
- `sync.Metux`: 互斥锁 = ReentrantLock
  - sync.Metux所保护的变量是在mutex变量声明之后立刻声明的
  - go里没有重入锁，一个通用的解决方案是将一个函数分离为多个函数
    ```
    最佳实践
    func Deposit(amount int) {
        mu.Lock()
        defer mu.Unlock()
        balance = balance + amount
    }
    ```
- `sync.RWMetux`: 读写锁 = ReentrantReadWriteLock
- `sync.Map`: 线程安全map = ConcurrentHashMap
- `sync.Once`: 资源初始化 = 双重检查 + volatile
  - Do这个唯一的方法需要接收初始化函数作为其参数
- `sync.WaitGroup`: 线程协同 = CountDownLatch
  - Add(1)、Done() -- 相当于Add(-1)、Wait()
- `atmoic`: 针对基本类型的原子操作

#### 反射
- 一个具体的值转为接口类型会有一个隐式的接口转换操作, 它会创建一个包含两个信息的接口值: 操作数的动态类型和它的动态的值
  ```
  操作数的动态类型这里是int和它的动态的值这里是3
  t := reflect.TypeOf(3)
  ```
- reflect.Value 和 interface{} 都能保存任意的值. 所不同的是, 一个空的接口隐藏了值对应的表示方式和所有的公开的方法
- reflect.TypeOf(): 函数可以获得任意值的类型对象(reflect.Type)
- reflect.ValueOf(): 返回的是reflect.Value类型，其中包含了原始值的值信息。
  - v.Kind()返回类型枚举值reflect.Kind，v.Type()将返回具体类型所对应的reflect.Type
  - slice和数组：v.Len()、v.Index()
  - 结构体：v.NumField()、v.Field()
  - Maps: v.MapKeys()、v.MapIndex(key)
  - 指针和接口：v.Elem()、v.IsNil()
- reflect.Value的CanAddr方法来判断其是否可以被取地址，CanSet是用于检查对应的reflect.Value是否是可取地址并可被修改
- 每当我们通过指针间接地获取的reflect.Value都是可取地址的，即使开始的是一个不可取地址的Value
  - first: 调用Addr()方法，它返回一个Value，里面保存了指向变量的指针
  - second: 在Value上调用Interface()方法，也就是返回一个interface{}，里面通用包含指向变量的指针
  - third: 知道变量的类型后使用类型的断言机制将得到的interface{}类型的接口强制环为普通的类型指针
    ```
    x := 2
    d := reflect.ValueOf(&x).Elem()   // d refers to the variable x
    px := d.Addr().Interface().(*int) // px := &x
    *px = 3                           // x = 3
    fmt.Println(x)                    // "3"
    ```
  - 或者通过调用可取地址的reflect.Value的reflect.Value.Set方法来更新对于的值
    ```
    d.Set(reflect.ValueOf(4))
    fmt.Println(x)                    // "4"
    # 用于基本数据类型的Set方法：SetInt、SetUint、SetString和SetFloat等
    d.SetInt(3)
    fmt.Println(x)                    // "3"
    ```
- 创建反射对象reflect.New(), reflect.Append(), reflect.MakeMap()
- reflect.Type和reflect.Value都提供了一个Method方法
  - t.Method(i)调用将一个reflect.Method的实例对应一个用于描述一个方法的名称和类型的结构体
  - v.Method(i)方法调用都返回一个reflect.Value以表示对应的值。使用reflect.Value.Call方法，可以调用一个Func类型的Value
- 结构体的反射：反射字段，反射方法，基本原理和java的区别不大

#### 底层编程
- Go 中普通指针类型、unsafe.Pointer、uintptr
  - `*类型`: 普通指针类型，用于传递对象地址，不能进行指针运算
  - `unsafe.Pointer`: 通用指针类型，用于转换不同类型的指针，不能进行指针运算，不能读取内存存储的值(必须转换到某一类型的普通指针)
    - unsafe.Pointer 是桥梁，可以让任意类型的指针实现相互转换，也可以将任意类型的指针转换为 uintptr 进行指针运算
    - unsafe.Pointer 不能参与指针运算，比如你要在某个指针地址上加上一个偏移量，Pointer 是不能做这个运算的
  - `uintptr`: 用于指针运算，GC 不把 uintptr 当指针，uintptr 无法持有对象。uintptr 类型的目标会被回收
    - 只要将 Pointer 类型转换成 uintptr 类型，做完加减法后，转换成 Pointer，通过*操作，取值，修改值，随意
    - unsafe.Pointer 可以让你的变量在不同的普通指针类型转来转去，也就是表示为任意可寻址的指针类型。而 uintptr 常用于与 unsafe.Pointer 打配合，用于做指针运算
- 来自reflect包的DeepEqual函数可以对两个值进行深度相等判断，可以支持任意的数据类型，但是它也有不足之处
  - 它将一个nil值的map和非nil值但是空的map视作不相等，同样nil值的slice 和非nil但是空的slice也视作不相等

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
  - 答：序列化时字段若赋为零值，则不进行序列化；但是不管有没有该字段反序列化时未出现的值均默认赋零值
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
- 执行go build的时候，需要在main函数所在的目录下编译，否则不会生成可执行文件，只编译
- `...` 符号解压缩切片，则可以将切片直接传递给可变参数函数。在这种情况下，不会创建新的切片
- 引用类型传递不会拷贝对象，值传递会拷贝对象；引用类型传递与`&`取指针传递一致
- map数据结构在同一台机器上，每次迭代的元素顺序都是不一样的，这种设计是有意为之
- 对传入的map可以进行put操作，而slice不能进行append，源slice无法感知；如果使用则需返回append后的引用
- defer、panic、recover : https://go.dev/blog/defer-panic-and-recover
- 元组赋值，允许同时更新多个变量值，很有趣的特性: 交换两个值 - `a[i], a[j] = a[j], a[i]`
  - 元组赋值是另一种形式的赋值语句，它允许同时更新多个变量的值。在赋值之前，赋值语句右边的所有表达式将会先进行求值，然后再统一更新左边对应变量的值
- 每个文件都可以包含多个init初始化函数，在程序开始执行时按照它们声明的顺序被自动调用
- 子字符串操作s[i:j]基于原始的s字符串的第i个字节开始到第j个字节（并不包含j本身）生成一个新字符串 - `s[1:5]`
- `*time.Time` 类型在反序列化过程中，如果变量值为字符串的空值，则反序列会报错，解决方案不要序列化该字段即可
