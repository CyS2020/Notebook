
### Node.js
#### 介绍
- Chrome = V8 + 内置API(DOM...); V8负责解析执行js代码，内置 api 由运行环境提供的特殊接口
- Node.js 是一个基于 Chrome V8 引擎的 javaScript 运行环境 = V8 + 内置API(http...)
- Node.js 应用程序运行在单个进程单个线程中，不需要为每个请求创建一个新线程
  - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop
  - Node.js 中的库是使用非阻塞范式编写的，这使得阻塞行为成为例外而不是常态。不过 Node.js 没有线程安全问题了，开不开心？
  - 当 Node.js 执行 I/O 操作时，比如从网络读取、访问数据库或文件系统，Node.js 将在响应返回时恢复操作，而不是阻塞线程和浪费 CPU 周期等待。
  - 这允许 Node.js 处理单个服务器上的数千个并发连接，而不引入管理线程并发性的负担，这可能是一个重要的 bug 来源。

#### 安装软件
- 首先添加 Source 到 yum repo 里面 `curl -sL https://rpm.nodesource.com/setup_16.x | sudo bash -`
- 然后执行 `yum install nodejs`, 安装node、npm，然后执行 `npm install --global yarn`，安装yarn
- `yarn build` 编译js; `yarn add typescript` 添加依赖; `tsc xxx.ts` 编译ts; `node xxx.js` 运行js
- package.json --> npm项目
- .eslintrc.json --> ESLint检查
- yarn.lock --> yarn项目
- tsconfig.json --> typeScirpt项目

#### javaScript
- 基本数据类型：Null，Undefined，Boolean，Number，BigInt，String，Symbol
- arguments--装有所有参数的数组 与 剩余参数--装有不定数量参数的数组
- 对象字面量的赋值；函数声明会提升；
- 对象原型与继承
  - 每个函数都有一个特殊的属性叫作原型 prototype, 使用 function 关键字定义的函数才有
  - 原型相等 `f.__proto__ or [[Prototype]]` === `F.prototype` =!= `f.constructor.prototype`
  - 原型对象 prototype 有一个constructor属性，指向该原型对象对应的构造函数
  - 实例对象可以继承原型对象的属性，所以实例对象也拥有constructor属性，同样指向原型对象对应的构造函数
  - 上游对象的方法不会复制到下游的对象实例中；下游对象本身虽然没有定义这些方法，运行时通过上溯原型链、从上游对象中找到它们
  - 规约：在构造器（函数体）中定义属性、在 prototype 属性上定义方法。构造器只包含属性定义，而方法则封装在不同的代码块
  - 函数类型的构造器使用的时候前面的关键字 new 不可缺失，否则构造器函数会作为普通函数调用，不会返回实例
- 类表达式与类声明
  - 只能有唯一的构造函数，静态变量不能由实例访问，所有类的基类是 Object
  - 当调用静态或原型方法时没有指定 this 的值，那么方法内的 this 值将被置为 undefined
  - 方法是给实例调用的，因此类中的 **函数调用类中其他函数、访问类属性 需要使用 this.**
  - 当类的函数被直接调用(作为回调函数)的时候 `const f = c.method(); f()` this 为 undefined
- 闭包: 内部函数包含外部函数的作用域，值与函数一起作为参数传入
- 函数可以作为另外一个函数的参数或者返回值使用，这样的做法叫高阶函数；e.g. Promise、setTimeout、Array.map
- 函数的柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式
- 类中的方法有构造方法、原型方法和静态方法，和其他语言区别不大，注意this指向调用的实例
- 括号表示法一个有用的地方是它不仅可以动态的去设置对象成员的值，还可以动态的去设置成员的名字
  - 点表示法只能接受字面量的成员的名字，不接受变量作为名字；`dataType = "name"; obj = {[dataType]: "jack"};`
- 异步机制 https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Asynchronous
  - EventHandler: 时间监听机制，while(true) 循环
  - Callback：函数式编程，java，go等均支持回调函数
  - Promise：类似于java的 CompleteFuture 异步编排
    - pending:  初始状态，既没有被兑现，也没有被拒绝，此时请求还在进行中。
    - fulfilled(resolve): 意味着操作成功完成。当 Promise 完成时，它的 then() 处理函数被调用。
    - rejected(reject): 意味着操作失败。当一个 Promise 失败时，它的 catch() 处理函数被调用。
  - async/await：直接在定义处进行声明，所以调用该函数的地方均异步执行，使用await阻塞。
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function
    - 函数直接返回不等待回调函数的结果的运行方式叫同步执行，函数等待回调函数的运行方式叫异步执行
  - 所有 I/O 操作均是异步的，所有使用async标注的方法是异步的, 所有返回Promise方法是异步的

#### typeScript
- 在模板字符串中嵌入变量的时候，需要将变量名写在 `${}` 中
- 特殊变量 `any`, `unknown`, `never`, `void`
- 元组是一种特殊的 array，内部类型不一致且明确定义不同位置的类型 `pair: [string, number]`
  - 剩余元素可以用于元组定义；数组转为 readonly 元组 `[0, 1] as const`
- 使用 const var 声明变量，无需声明类型自动进行类型推断
- 变量参数声明居然使用 `:` 冒号，而且还是写在标识符后面，e.g. `fn: (a: string) => void`
- readonly 关键字定义不可变的类型，类似于 java 中的 final，可以用于数组哦
- 可缺省属性需要使用 `?` 问号跟在标志符后面；标志符后面的 `!` 代表 not null or undefined
  - `name: { first: string; last?: string }`
  - 为回调编写函数类型时，切勿编写可选参数，除非您打算在不传递该参数的情况下调用该函数
  - 参数列表除了可选之外还可以解耦且指定默认值 `function f({ x = 0, y = 0 }: Point)`
- `type` 和 `interface` 都可以用来定义 object type, 可以自由选择，扩展方式不一样
- 可以使用 `|` 来限定多个类型或多个取值，多个值可以用来实现枚举
- `typeof`、`instanceof`、`in`、`is`、`this is`等关键字进行类型 narrowing
- `keyof` 判断是否含某个属性，`[]` 来对属性的类型进行索引
- 使用 `<>` 或 `as` 关键字进行强制类型转换
  - `const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;`
  - `const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");`
- 引入泛型机制，描述类中包含的值的类型，并进行检查
  - 定义约束接口并使用 extends 关键字来表示我们的泛型约束
  - 泛型类型是可以推断出来的，在实例化对象的时候可以不写`<string>`
  - 如果一个类型参数只出现在一个位置，强烈重新考虑你是否真的需要泛型
  - 使用泛型创建工厂时(声明调用签名)，需要通过构造函数来引用类类型
  ```
  function create<Type>(c: { new (): Type }): Type {
    return new c();
  }
  ```
- 使用 `export{}` 控制当前文件内变量的作用域
- interface 用来定义属性和 type 关键字自由选择，定义函数给class implement，且是鸭子类型
- 函数签名为 形参列表 + 返回值列表，同go，java=函数名，参数列表
- 重载签名与实现签名是多对一的关系，声明多个重载的方法签名，然后用一个实现都覆盖起来
  - 函数签名可以带类型参数的(泛型)；` <Type>(arg: Type) => Type`
  - 函数除了可调用之外还可以具有属性，声明调用签名即可。调用签名的语法与声明函数的语法略有区别

#### node code
- array.map(() => ({a: f1, b: f2})); 取出对象的部分字段组成数组，必须是这种k-v形式的
- typescript是强类型的语言了，只有 any 类型才能通过 Dot Notation 添加属性, 明确类型使用 Bracket Notation 添加属性
- `const people: myInterface = {name: "lihua", age: 18};` 接口初始化的时候一定要一对一对的赋值，不能省略 name, age
- restify 框架的 next() 并不会出栈，需要在后面添加 return; 才行，或者直接 return next(); 确保触发回调后执行停止
  - https://stackoverflow.com/questions/16810449/when-to-use-next-and-return-next-in-node-js
- 数组元素解构赋值的时候需要注意一下bug: `const { appName } = arr[0].appName`; 这样是不行的额
- Spread syntax (...) 展开语法可以用于数组，并不是深拷贝，也可以用于对象的
- JSON.parse()、JSON.stringify() 用于 JSON 与 string 格式的转换，用于深拷贝对象
- 数组没有越界异常，如果取不到值，就是 undefined, 不同与其他类型的语言
- this 取值 undefined、window、实例对象，使用bind绑定 `this.method = this.method.bind(this)` 原型方法 => 自身属性

### React快速入门
#### 开始项目
- `npx create-react-app demo`: 初始化项目，src文件下的文件可以全部删除
- 浏览器安装开发者工具 `React Developer Tools` (chrome)
- 文件名可以是 jsx 或者 js，不影响文件中的代码；ReactDOM.render(VDOM, 容器)
  ```
  import ReactDOM from 'react-dom'
  import App from "./App";
  ReactDOM.render(
      <App/>,
      document.getElementById('root')
  )
  ```
- 类组件名必须大写
  ```
  import React from 'react'
  // 类组件
  class App extends React.Component {
      render() {
         return (
              <div>
                  <h2>Hello World</h2>
              </div>)
      }
  }
  export default App
  ```
- JavaScript中出现 `()` 括号代表要写 html
- HTML中出现 `{}` 花括号代表要写 js表达式，有返回值的叫表达式
 - 能使用 const x = {} 接住的就叫表达式，接不住的就是js代码
- 向外提供特定功能的 js 程序，一般就是一个 js 文件，称之为模块
- 用来实现局部功能效果的代码和资源的集合(html/css/js/image)，称之为组件
- export default 可以写在 class 前面
- React 中的列表循环有且只有map可以使用，map才有返回值，forEach没有
- 设置变量使用 setState 方法, 绑定事件时 () => {} 胖箭头这种写法 this 指向函数体外部的this
  - 或者函数在外面显示定义，绑定事件时使用 this.funcName.bind(this, parm1, parm2, ...) 即可
- 函数时组件名也要大写
  ```
  function App(){
    return <h2>Hello World</h2>
  }
  export default App
  ```
- 函数式组件特点
  - 函数式组件没有生命周期
  - 函数式组件没有 this
  - 函数式组件没有 state
 - Hooks 是 React 官方提供的 hook, 开发人员自定义的 hook
  - Hook 只能用在组件函数的最顶层
  - 使用 useState 钩子函数来触发视图更新的东西
    - ```const [var, setVar] = useState(v1)```
  - 使用 useEffect 钩子函数模拟生命周期
    - ```useEffect(callBack, [var1, var2, ...])```
- 父函数组件传给子函数组件使用 props 函数接收参数
  - 子传父其实实际上并没有子传父，只是父把回调函数传给了子，由子组件给回调函数传入参数
  - 多级父子组件使用 context 来传参：Context.Consumer(userContext), Context,Provider
  - 使用 memo 与 useCallback 钩子函数，避免父组件更新的时候子组件也强制更新
- 受控组件与不受控组件
  - 受控组件和不受控组件只存在于表单元素
  - 所谓受控组件就是表单元素的 value 需要通过 state(或useState) 来定义
  - 不受控组件意味着表单元素的 value 无法通过 state 获取，只能使用 ref(或useRef)
- 定义路由实现子页面跳转 router
  - useLocation 钩子函数获取当前页面的路径
  - useNavigate 钩子函数实现事件页面跳转，与参数传递 body
  - useParams 钩子函数实现页面跳转时的路径占位符的参数传递
  - useSearchParams 钩子函数实现路径 ? 后面的参数传递
- 简单理解常见的 hooks
  ```
  # 自变量                        因变量
  useState                      useMemo
  useReducer (更多)    useRef   useEffect (有副作用)
  useContext (跨层)            useCallback
  ```

### React 全家桶
#### React 是什么？
- React 用于构建用户界面的 javaScript 库
- React 是一个将数据渲染为 HTML 视图的开源 JavaScript 库
- React 使用虚拟 DOM + 优秀的 Diff 算法，尽量减少与真实DOM的交互

#### 虚拟DOM (Document Object Model)
- 本质是 Object 类型对象，即一般对象
- 虚拟 DOM 比较轻，真实 DOM 比较重，因为虚拟 DOM 是 React 内部在用无需这么多属性
- 虚拟 DOM 最终会被 React 转化为 真实 DOM，呈现在页面上

#### 类组件
- 用来实现局部功能效果的代码和资源的集合(html/css/js/image)，称之为组件，组件状态驱动页面
- `ReactDOM.render(<App/>, document.getElementById('root'))` 执行该行代码后
  - React 解析组件标签，找到 App 组件，找不到即报错
  - 发现组件是函数定义的，调用该函数，将返回的虚拟DOM转为真实DOM，渲染页面
- 特性之一 state
  - 定义实例属性，并在构造器中使用 props 初始化外部传入的状态值
  - 不能使用 this 直接修改state，react不会重新 re-render，使用 setState() 方法
  - 类中编写自定义函数的时候使用 f = () => {} 的形式，避免 this 绑定问题
- 特性之二 props
  - render()函数中使用 this.props 直接取出外部传入的 k-v 值，且this.props是只读的不允许修改
  - props 在jsx中可以进行批量传递 {...props}，这个语法是jsx的语法，与对象展开不是一个意思
  - 使用静态属性 propTypes、defaultProps 对传入的参数进行限制，例如 `static propTypes = {name:PropTypes.string.isRequired}`
- 特性之三 refs
  - 组件内的标签可以通过 ref 属性来标识自己，相当于原生组件中的 id；通过 this.refs 获取的是真正的节点(真实DOM)
  - 字符串形式 ref 已经过时，现在采用回调形式的 ref；`ref = nodeName` => `ref = {(c) => {this.nodeName = c}}`
  - 使用 React.createRef() 调用后可以返回一个容器，该容器可以存储被 ref 所标识的节点，只能存一个 (最佳实战)

#### 事件处理
- 通过 onXxx 属性指定时间处理函数(注意大小写)
  - React 使用的是自定义(合成)事件，而不是使用原生 DOM 事件
  - React 中的事件是通过事件委托的方式处理的(委托给组件最外层的元素)
- 通过 event.target 得到发生时间的 DOM 元素对象，例如 `event.target.value` 获取值
  - 不要过度使用 ref；发生事件的节点，正好是我想要操作的节点，就可以省略 ref 了

#### 非/受控组件
- 所有输入类型的 DOM，组件是现用现取的则为非受控组件
- 所有输入类型的 DOM，组件随着输入维护到 state 中则为受控组件

#### 旧生命周期
- render() 初始化渲染，状态更新之后调用 = 1 + n 次
- componentDidMount() 组件挂载完毕之后调用 = 1 次
- componentWillUnmount() 组件将要卸载前调用 = 1 次
- 初始化阶段：由 ReactDOM.render() 触发 -- 初次渲染
  - constructor()
  - componentWillMount()
  - render()
  - componentDidMount()
- 更新阶段：有组件内部 this.setState() 或父组件 render 触发
  - shouldComponentUpdate()
  - componentWillUpdate()
  - render()
  - componentDidUpdate()
- 卸载组件：由 ReactDOM.unmountComponentAtNode() 触发
  - componentWillUnmount()
![组件生命周期-旧](https://github.com/CyS2020/Notebook/blob/master/images/%E7%BB%84%E4%BB%B6%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F(%E6%97%A7).png?raw=true)

#### 新生命周期
![组件生命周期](https://github.com/CyS2020/Notebook/blob/master/images/%E7%BB%84%E4%BB%B6%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png?raw=true)
