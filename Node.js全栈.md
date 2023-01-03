
### Node.js
#### 介绍
- https://nodejs.dev/zh-cn/learn/
- Chrome = V8 + 内置API(DOM...); V8负责解析执行js代码，内置 api 由运行环境提供的特殊接口
- Node.js 是一个基于 Chrome V8 引擎的 javaScript 运行环境 = V8 + 内置API(http...)
- Node.js 应用程序运行在单个进程单个线程中，不需要为每个请求创建一个新线程
  - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop
  - Node.js 中的库是使用非阻塞范式编写的，这使得阻塞行为成为例外而不是常态。不过 Node.js 没有线程安全问题了，开不开心？
  - 当 Node.js 执行 I/O 操作时，比如从网络读取、访问数据库或文件系统，Node.js 将在响应返回时恢复操作，而不是阻塞线程和浪费 CPU 周期等待。
  - 这允许 Node.js 处理单个服务器上的数千个并发连接，而不引入管理线程并发性的负担，这可能是一个重要的 bug 来源。
- 任何回调函数中的第一个参数都是错误对象--错误优先回调，如果没有错误，则对象为null。如果存在错误，则包含错误的一些描述和其他信息
- setTimeout()、setInterval()、process.nextTick()、setImmediate()
  ```
  const interval = setInterval(() => {                      const myFunction = () => {
  if (App.somethingIWait === 'arrived') {                     // do something
    clearInterval(interval);                                  setTimeout(myFunction, 1000);
  }                                                         };
  // otherwise do things
  }, 100);                                                  setTimeout(myFunction, 1000);
  ```

#### npm常用命令
- `npm init -y`: 默认值初始化项目
- `npm install jquery@3.2.1`: 安装指定版本的依赖
- `npm uninstall jquery`: 卸载依赖
- `npm update jquery`: 更新依赖
- `npm run test`: 运行命令

#### 安装软件
- 首先添加 Source 到 yum repo 里面 `curl -sL https://rpm.nodesource.com/setup_16.x | sudo bash -`
- 然后执行 `yum install nodejs`, 安装node、npm，然后执行 `npm install --global yarn`，安装yarn
- `yarn build` 编译js; `yarn add typescript` 添加依赖; `tsc xxx.ts` 编译ts; `node xxx.js` 运行js
- package.json --> npm项目
- .eslintrc.json --> ESLint检查
- yarn.lock --> yarn项目
- tsconfig.json --> typeScirpt项目

### javaScript
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
  - Promise：类似于java的 CompleteFuture 异步编排，但没有get()方法，可使用await进行等待
    - pending:  初始状态，既没有被兑现，也没有被拒绝，此时请求还在进行中。
    - fulfilled(resolve): 意味着操作成功完成。当 Promise 完成时，它的 then() 处理函数被调用。
    - rejected(reject): 意味着操作失败。当一个 Promise 失败时，它的 catch() 处理函数被调用。
  - async/await：直接在定义处进行声明，所以调用该函数的地方均异步执行，使用await阻塞。
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function
    - 函数直接返回不等待回调函数的结果的运行方式叫同步执行，函数等待回调函数的运行方式叫异步执行
    - 最佳实战：async/await 配合 try-catch 来进行编码，错误的逻辑写在 catch 中
  - 所有 I/O 操作均是异步的，所有使用async标注的方法是异步的, 所有返回Promise方法是异步的，setTimeout是异步的
   ```
   await new Promise((resolve) => {
     setTimeout(resolve, 1000);
   });
   ```

### typeScript
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
- noImplicitAny，strictNullChecks 两个检查需要配置 tsconfig.json 文件中

### node code
- array.map(() => ({a: f1, b: f2})); 取出对象的部分字段组成数组，必须是这种k-v形式的
- typescript是强类型的语言了，只有 any 类型才能通过 Dot Notation 添加属性, 明确类型使用 Bracket Notation 添加属性
- `const people: myInterface = {name: "lihua", age: 18};` 接口初始化的时候一定要一对一对的赋值，不能省略 name, age
- restify 框架的 next() 并不会出栈，需要在后面添加 return; 才行，或者直接 return next(); 确保触发回调后执行停止
  - https://stackoverflow.com/questions/16810449/when-to-use-next-and-return-next-in-node-js
  - next() 调用后请求就返回了，如果此时函数没有 return，且在后面仍然有 next() 程序会强制退出
- 数组元素解构赋值的时候需要注意一下bug: `const { appName } = arr[0].appName`; 这样是不行的额
- Spread syntax (...) 展开语法可以用于数组，并不是深拷贝，也可以用于对象的
- JSON.parse()、JSON.stringify() 用于 JSON 与 string 格式的转换，用于深拷贝对象
- 数组没有越界异常，如果取不到值，就是 undefined, 不同与其他类型的语言
- this 取值 undefined、window、实例对象，使用bind绑定 `this.method = this.method.bind(this)` 原型方法 => 自身属性
- 连续解构赋值 e.g. `const obj = {a:{b:{c:1}}}` 解构出c并重命名keyword `const {a:{b:{c:keyword}}} = obj`
- req.params(url), req.body, req.query; req.query 不要使用bool作为参数，直接使用字符串 `Joi.string().valid('true', 'false').optional()`
- logical OR assignment (x ||= y) operator only assigns if x is falsy (虚值就是数据类型的零值).

### React快速入门
#### 开始项目
- `npx create-react-app my-app`: 初始化项目，src文件下的文件可以全部删除
- `yarn create react-app my-app --template typescript`: 使用 ts 初始化
- 浏览器安装开发者工具 `React Developer Tools` (chrome)
- 文件名可以是 jsx 或者 js(不推荐)，不影响文件中的代码；ReactDOM.render(VDOM, 容器)
  ```
  import ReactDOM from 'react-dom'
  import App from "./App";
  ReactDOM.render(
      <App/>,
      document.getElementById('root')
  )
  ```
- 类组件名必须大写 WebStorm 简写 rcc
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
- 函数时组件名也要大写，Webstorm 简写 rsf
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
  - react 中的状态更新 setState 是异步的；且有两种写法 对象式 与 函数式
    - 如果新状态不依赖于原状态 使用对象方式 e.g. `this.setState({count:99})`
    - 如果新对象依赖原状态 使用函数方式 e.g. `this.setState(state => ({count:state.count+1}))` 
    - 如果需要在 setState() 执行后获取最新的状态数据，要在第二个 callback 函数中读取
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

#### 高阶/柯里化
- 当react组件回调函数需要传递参数时，要么直接写成函数形式 `() => {this.f(xxx)}` 要么 this.f() 定义成高阶函数
- 函数可以作为另外一个函数的参数或者返回值使用，这样的做法叫高阶函数；e.g. Promise、setTimeout、Array.map
  - 若A函数接收的参数是一个函数，那么就 A 可以称之为高阶函数
  - 若A函数调用的返回值是一个函数，那么 A 就可以称之为高阶函数
  ```
  saveData = (dataType) => {
    return (event) => {
      this.setState({[dataType]:event.target.value})
    }
  }
  ```
- 函数的柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式
  ```
  function sum(a){
    return (b) => {
      return (c) => {
        return a + b + c
      }
    }
  }
  sum(1)(2)(3)
  ```

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

#### diff 算法
- 当状态中的数据发生变化时，react会根据新数据生成新的虚拟DOM
- 旧虚拟 DOM 中找到了与新虚拟 DOM 相同的 key
  - 若虚拟 DOM 中内容没有变，直接使用之前的真实 DOM
  - 若虚拟 DOM 中内容变了，则生成新的真实 DOM，随后替换页面之前的真实 DOM
- 旧虚拟 DOM 中未能找到与新虚拟 DOM 相同的 key
  - 根据数据创建新的真实 DOM，随后渲染到页面
- 如何选择 key ?
  - 最好使用每条数据的唯一标识作为 key，比如 uuid、手机号、身份证号、学号等唯一值
  - 如果确定只是简单的展示数据，用 index 也是可以的

#### 类组件优化
- Component 的两个问题：只要执行 setState() 就是不改变数据也会重新 render()；且子组件也会重新 render()
- 优化1：手动重写 shouldComponentUpdate() 方法，比较新旧 state 如果有变化才返回 true，没变化返回 false
- 优化2：类组件不继承 Component，而是继承 PureComponent；state 和 props 只进行浅比较(内存地址)
- 优化3：使用钩子函数 React.memo() 包裹组件，原理和 PureComponent 类似

#### 组件结构
- 在 B 组件代码中显示调用 `<C/>`, 即 B 与 C 是父子组件的关系
- 在 B 组件代码中调用`<C>xxx<C/>`, C 组件中通过 props.children 获取标签体内容
- 在更底层的组件中构建父子关系，B 与 C 是父子组件
  ```
  # A组件构建父子关系
  <A>
    <B render={(data)=><C data={data}/>}/>
  </A>
  # B组件预留组件位置(插槽)
  {props.render(data)}
  # C组件获取B组件传入数据
  {props.data}
  ```

#### 前端编码流程
- 拆分组件：拆分界面，抽取组件
- 实现静态组件：使用组件实现静态页面效果
- 实现动态组件：动态显示初始化数据
  - 数据类型
  - 数据名称
  - 保存在哪个组件
- 交互(从绑定事件监听开始)

#### 脚手架配置代理
- 前后端分离的时候如果前端能够在 public 文件夹下找到资源就不会请求后端了
  - 所以在前端调试的时候可以根据请求路径在 public 文件加下创建文件路径
  - 然后创建对应的文件(无后缀)，将模拟数据写在文件中，就可以 mock 数据了
- 方法1：package.json 中添加配置
  ```
  "proxy":"http://localhost:5000"
  ```
- 方法2：src/setupProxy.js 中代码配置
  ```
  const proxy = require('http-proxy-middleware')

  module.exports = function (app) {
    app.use(
        proxy.createProxyMiddleware('/api1', {
          target: 'http://localhost:5000', // 请求转发目的地址
          changeOrigin: true, // 控制服务器收到的请求头中 Host 的值
          pathRewrite: {'^/api1': ''} // 重写请求路径 取消api1路径
        }),
        proxy.createProxyMiddleware('/api2', {
          target: 'http://localhost:5001',
          changeOrigin: true,
          pathRewrite: {'^/api2': ''}
        })
    )
  }
  ```

#### 组件信息传递(非嵌套组件)
- 使用 订阅发布机制 在兄弟组件或任意组件间传递消息
- 订阅：`this.token = PubSub.subscribe('channel', (_,state) => {this.setState(state)})`
- 发布：`PubSub.publish('channel', data)`
- 取消订阅：`PubSub.unsubscribe(this.token)`

### React Router5
- 基本使用
  - `yarn add react-router-dom`
  - 明确导航区、内容展示区、默认展示内容
  - 导航区使用：`<Link to='/AAD'>Demo</Link>`
  - 路由注册：`<Route path='/AAD' component={Demo}/>`; exact参数开启严格路由匹配
  - 默认展示：`<Redirect to='/Demo'/>`
  - `<App>` 组件使用 `<BrowserRouter>`或`<HashRouter>` 包裹
- 路由组件与一般组件
  - 写法不同且存放的文件夹位置不一样
  - 接收到的 props 不同，路由组件接收三个固定的属性 history, location, match
  - withRouter 可以加工一般组件，使其具备路由组件所具有的 API
    ```
    history:
      go: f go(n)
      goBack: f goBack()
      goForward: f goForward()
      push: f push(path, state)
      replace: f replace(path, state)
    location:
      pathname: ""
      search: ""
      state: {}
    match:
      params: {}
      path: ""
      url: ""
    ```
- NavLink与封装NavLink
  - NavLink可以实现路由链接的高亮，通过 activeClassName 指定样式名
  - 标签体内容是一个特殊的标签属性，通过 this.props.children 获取标签体内容
- Switch 的使用
  - 通常情况下，path 和 component 是一一对应的关系
  - Switch 可以提高路由匹配效率，单一匹配，匹配上后不再往后匹配
- 解决多节路径刷新页面丢失问题
  - public/index.html 中 引入样式不写 ./ 写 / (常用)
  - public/index.html 中 引入样式不写 ./ 写 %PUBLIC_URL% (常用)
  - 使用 HashRouter 代替 BrowserRouter (不常用)
- 嵌套路由 (多级路由)
  - 注册子路由时要写上父路由的 path 值
  - 路由的匹配时按照注册路由的顺序进行的
- 向路由组件传递参数 -- params
  - 路由链接传入参数：``<Link to={`/home/detail/${name}/${age}`}>Detail Info</Link>``
  - 注册路由声明参数：`<Route path='/home/detail/:name/:age' component={Detail}/>`
  - 子组件中接收参数：`this.props.match.params`
- 向路由组件传递参数 -- search
  - 路由链接传入参数：``<Link to={`/home/detail/?name=${name}&age=${age}`}>Detail Info</Link>``
  - 注册路由声明参数：`<Route path='/home/detail' component={Detail}/>` search 参数无需声明
  - 子组件中接收参数：`this.props.location.search` 引入 querystringify 库进行 urlencoding 与j son 的转换
- 向路由组件传递参数 -- state
  - 路由链接传入参数：`<Link to={{pathname:'/home/detail',state:{name, age}}}>Detail Info</Link>`
  - 注册路由声明参数：`<Route path='/home/detail' component={Detail}/>` state 参数无需声明
  - 子组件中接收参数：`this.props.location.state` 刷新后参数不会丢失
- BrowserRouter vs HashRouter 区别
  - 底层原理：BrowserRouter 使用的是H5的history API 不兼容IE9及以下的版本；HashRouter使用的是 URL 的哈希值
  - 表现形式：BrowserRouter 的路径 `http://localhost:3000/demo/test`；HashRouter 的路径 `http://localhost:3000/#/demo/test`
  - 刷新影响：BrowserRouter 没有任何影响，state 保存在 history 对象中；HashRouter 刷新后会导致state参数丢失

### React Router6
- 与 React Router5 的版本比较
  - 内置组件变化：移除 `<Switch/>`, 新增 `<Routes/>` 等
  - 语法的变化：`component={About}` 变为 `element={<About/>}` 等
  - 新增多个 hooks：`useParams`、`useNavigate`、`useMatch` 等
- 基本使用
  - `yarn add react-router-dom`
  - 明确导航区、内容展示区、默认展示内容
  - 外侧`<App>` 组件使用 `<BrowserRouter>`或`<HashRouter>` 包裹
  - 路由链接：`<NavLink to='/home'>Home</NavLink>`
  - 路由注册：`<Routes><Route path="/home" element={<Home/>}/><Routes/>`; 
  - 默认路由：`<Route path="/" element={<Navigate to="/about"/>}/>`
- `<Navigate/>`：只要渲染就会引起视图的切换，要渲染到页面形成 `<a>` 标签才能点点击跳转
- 路由注册位置，如果页面命中就是页面展示的位置；或者使用 `<Outlet/>` 指定路由组件呈现的位置
- 优化路由定义 -- 使用 useRoutes() 钩子定义路由表
  ```
  const element = useRoutes([
  {
    path: '/about',
    element: <About/>
  },
  {
    path: '/home',
    element: <Home/>,
    children:[
      {
        path:'news',
        element: <News/>
      }
    ]
  },
  {
    path: '/',
    element: <Navigate to='/about'/>
  }])
  ```
- 向路由组件传递参数 -- params
  - 路由链接传入参数：``<Link to={`detail/${id}/${title}/${content}`}>{title}</Link>``
  - 路由表中声明参数：`{path:'detail/:id/:title/:content',element:<Detail/>}`
  - 子组件中接收参数：`const {id, title, content} = useParams()`
- 向路由组件传递参数 -- search
  - 路由链接传入参数：``<Link to={`detail?id=${id}&title=${title}&content=${content}`}>{title}</Link>``
  - 注册路由无需声明：`{path:'detail',element:<Detail/>}`
  - 子组件中接收参数：`const [search, setSearch] = useSearchParams(); const id = search.get('id')`
- 向路由组件传递参数 -- state
  - 路由链接传入参数：`<Link to='detail' state={{id, title, content}}>{title}</Link>`
  - 注册路由无需声明：`{path:'detail',element:<Detail/>}`
  - 子组件中接收参数：`const {state: {id, title, content}} = useLocation()`
- 编程式路由导航使用钩子函数：useNavigate()；还可以执行前进后退等功能
  ```
  # 跳转页面并携带参数
  const navigate = useNavigate()
  navigate('detail', {
    state: {id, title, content}
  })
  ```
- 其他常用的钩子函数
  - useInRouterContext(): 如果组件在 `<Router/>` 的上下文呈现，则该钩子函数返回 true，否则返回 false
  - useNavigationType(): 返回当前的导航类型(用户如何来到该页面)：`POP`、`PUSH`、`REPLACE`
  - useOutlet(): 用来呈现当前组件中渲染的嵌套路由
  - useResolvedPath(): 给定一个 URL 值，解析其中的：path, search, hash值
    
### Redux
![redux原理图](https://github.com/CyS2020/Notebook/blob/master/images/redux%E5%8E%9F%E7%90%86%E5%9B%BE.png?raw=true)
- redux 核心概念 -- action
  - 动作的对象
  - 两个属性：type: 标识属性，值为字符串，唯一必要属性；data: 数据属性，值任意类型，可选属性
  - e.g. `{type:'ADD', data:{name:'tom', age:19}}`
- redux 核心概念 -- reducer
  - 用于初始化状态，加工状态
  - 加工时，根据旧的 state 和 action, 产生新的 state 纯函数
- redux 核心概念 -- store
  - 将 state、action、reducer 联系在一起的对象
  - 创建 store: `legacy_createStore(cReducer)`
  - 创建 reducer: `function cReducer(state, action)`
  - 监听 state: `store.subscribe(() => {this.setState()})`
  - 分发 action: `store.dispatch({action})`
  - 获取 state: `store.getState()`
- 异步 action 就是指 action 的值为函数，异步 action 中一般都会调用同步 action

### Redux-react
![react-redux](https://github.com/CyS2020/Notebook/blob/master/images/react-redux%E6%A8%A1%E5%9E%8B%E5%9B%BE.png?raw=true)
- UI组件：不能使用任何redux的api, 只负责页面的呈现与交互
- 容器组件：负责redux通信，将结果交给UI组件
- 如何创建一个容器组件 -- react-redux 的 connect 
  - legacy_createStore(reducers, applyMiddleware(thunk));
  - connect(mapStateToProps, mapDispatchToProps)(UI组件)
  - mapStateToProps: 映射状态，返回值是一个对象
  - mapDispatchToProps: 映射操作状态的方法(也可以是对象)，返回值是一个对象
 - 容器组件中的 store 是靠 props 传入容器组件内的，而不是在容器组件直接引入的
 - 或者使用 `<Provider>` 标签管理应用所有 store
    ```
    <Provider store={store}>
      <App />
    </Provider>
    ```
- 写好 actions、reducers、store; 然后使用 connect 连接 UI 与 Container
    
#### 纯函数
- 一类特别的函数：只要是同样的输入(实参)，必定得到同样的输出(返回)
- 需要遵守以下约束条件
  - 不得改写参数数据
  - 不会产生任何副作用，例如网络请求，输入和输出设备
  - 不能调用 Date.now() 或者 Math.random() 等不纯的函数
  - redux 的 reducer 函数必须是一个纯函数

### Hooks
- React.useState()
  - State Hook 让函数组件也可以有state状态，并进行状态数据的读写操作
  - 语法格式：`const [xxx, setXxx] = React.useState(initValue)`
    - 参数：第一次初始化指定的值在内部做缓存
    - 返回值：包含2个元素的数组，第一个为内部状态值，第二个为更新状态值的函数
  - setXxx() 两种写法
    - setXxx(newValue): 参数为非函数值，直接指定新的状态值，内部用其覆盖原来的状态值
    - setXxx(value => newValue): 参数为函数，接收原本的状态值，返回新的状态值，内部用其覆盖原来的状态值
- React.useEffect()
  - Effect Hook 可以让你在函数组件中执行副作用操作(模拟类组件的生命周期钩子)
  - React 中的副作用操作：ajax 请求、设置订阅/定时器、手动更改真实 DOM
  - 语法格式：
    ```
    React.useEffect(() => {
      // 在此可以执行任何带副作用的操作
      return () => {
        // 在此执行收尾工作
      }
    }, [stateValue])
    ```
  - useEffect 传入的函数有返回值 == componentWillUnmount()
  - useEffect 第二参数传入的空数组 == componentDidMount()
  - useEffect 第二参数传入非空数组 == componentDidUpdate()
- React.useRef()
  - Ref Hook 可以在函数组件中存储/查找组件内的标签或其他任意数据
  - 语法：`const  myRef = React.useRef()`
  - 作用：保存标签对象，功能与类组件中 React.createRef() 相同
- React.useContext()
  - Context 可以在祖孙组件中进行数据传递
  - 语法格式：
    ```
    # 公共区定义 Context
    const MyContext = React.createContext(defaultData)
    # 祖组件中提供数据
    <MyContext.Provider value={data}>
      <B/>
    </MyContext.Provider>
    # 孙组件消费数据
    const data = React.useContext(MyContext)
    ```
- React.memo()
  - Memo Hook 只在子组件需要渲染的时候才渲染，原理和 PureComponent 类似
  - 语法格式：
    ```
    const Child = memo(function Child(props) {
      return (
        <div><div/>
      )
    },)
    ```
  - memo() 第二参数[prevProps, nextProps] 可以自定义是否更新同 shouldComponentUpdate()
- React.useMemo()
  - 用来缓存一些变量，某个依赖项改变时才重新计算，作为性能优化的手段
  - 语法格式：`const myMemo = useMemo(() => computeExpensiveValue(a, b), [a, b]);`
  - 如果没有提供依赖项数组，useMemo 在每次渲染时都会计算新的值。传给子组件就会重新渲染
- React.useCallback()
  - 用来缓存一些函数，该回调函数仅在某个依赖项改变时才会更新
  - 语法格式：
    ```
    const memoizedCallback = useCallback(
      () => {
        doSomething(a, b);
      },
      [a, b],
    );
    ```
  - useCallback(fn, deps) 相当于 useMemo(() => fn, deps)
  - 每次生成值相等的 obj 的对象或方法，引用不相同的场景，需要使用优化手段避免重复渲染
- React.useReducer()
  - useState() 的替代方案，useState() 底层就是 useReducer() 实现的；将state的更新规则解耦
  - 基本语法：
    ```
    # 使用 reducer
    const [state, dispatch] = useReducer(reducer, init);
    # 定义 action
    function reducer(state, action) {
      switch (action.type) {
        case 'A:
        default:
      }
    }
    # 更新状态
    const handle = () => dispatch({type: 'A'})
    ```
- useSelector() + useDispatch()
  - 使用 react-redux 中的钩子函数代替 connect
  - action是定义类型与数据的{type,data}，reducer定义switch即动作执行的函数
  - 基本语法：
    ```
    # 定义 action
    const increment = (data) => ({type: 'increment', data})
    # 定义 reducer
    function countReducer(preState=0, action) {
      const { type, data } = action
      switch (type){
        case 'increment':
        return preState + data
      default:
        return preState
      }
    }
    # 定义 store
    const reducers = combineReducers({count:reducer})
    legacy_createStore(reducers, applyMiddleware(thunk));
    # 获取 state
    const data = useSelector(state => {return state.count})
    # 更新 state
    const dispatch = useDispatch()
    dispatch(increment(count))
    # 包裹 App
    <Provider store={store}>
      <App />
    </Provider>
    ```
 
#### 集中式管理最佳实战
- 使用 react-redux + redux-toolkit
- 基本语法：
  ```
  # 定义 action、reducer
  const initialState = {value: 0}
  const counterSlice = createSlice({
    name: 'counter',
    initialState,
    reducers: {increment: (state) => {state.value += 1}}
  })
  export const {increment} = counterSlice.actions
  export default counterSlice.reducer
  # 定义 store
  import counterReducer from '../features/counter/counterSlice'
  const store = configureStore({reducer: {counter: counterReducer}})
  # 获取 state
  const count = useSelector((state) => state.counter.value)
  # 更新 state
  import {increment} from './counterSlice'
  const dispatch = useDispatch()
  dispatch(increment())
  # 包裹 App
  <Provider store={store}>
    <App/>
  </Provider>
  ```
          
#### 组件通信方式
- 父子组件: props
- 兄弟组件：订阅-发布、集中式
- 祖孙组件: 订阅-发布、集中式、context

### react code
- 状态 state 存放在哪个组件，操作状态的方法就在哪个组件中
- 多个组件使用的状态，放在共同的父组件 state 中 -- 状态提升
- 注意 defaultChecked 和 checked 的区别，类似的还有 defaultValue 和 value
- 使用 nanoid 库随机生成 uuid，前端组件常用的工具库
