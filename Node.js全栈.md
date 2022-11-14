
### Node.js
#### 介绍
- Chrome = V8 + 内置API(DOM...); V8负责解析执行js代码，内置 api 由运行环境提供的特殊接口
- Node.js 是一个基于 Chrome V8 引擎的 javaScript 运行环境 = V8 + 内置API(http...)
- Node.js 应用程序运行在单个进程单个线程中，不需要为每个请求创建一个新线程
  - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop
  - Node.js 中的库是使用非阻塞范式编写的，这使得阻塞行为成为例外而不是常态。
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
- arguments 与 剩余参数
- 在模板字符串中嵌入变量的时候，需要将变量名写在${}中
- 对象字面量的赋值；函数声明会提升；
- 类表达式与类声明
  - 只能有唯一的构造函数，静态变量不能由实例访问
  - 当调用静态或原型方法时没有指定 this 的值，那么方法内的 this 值将被置为 undefined
  - 方法是给实例调用的，因此类中的 函数调用其他函数、访问属性 需要使用 this 进行调用
  - 当类的函数被直接调用(作为回调函数)的时候 `const f = c.method(); f()` this 为 undefined
- 闭包: 内部函数包含外部函数的作用域
- 异步机制 https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Asynchronous
  - EventHandler: 时间监听机制，while(true) 循环
  - Callback：函数式编程，java，go等均支持回调函数
  - Promise：类似于java的 CompleteFuture 异步编排
    - pending:  初始状态，既没有被兑现，也没有被拒绝，此时请求还在进行中。
    - fulfilled(resolve): 意味着操作成功完成。当 Promise 完成时，它的 then() 处理函数被调用。
    - rejected(reject): 意味着操作失败。当一个 Promise 失败时，它的 catch() 处理函数被调用。
  - async/await：直接在定义处进行声明，所以调用该函数的地方均异步执行，使用await阻塞。
    - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function
    - 函数直接返回不等待回调函数的结果的运行方式叫同步执行，函数等待回调函数的运行方式叫异步执行
  - 所有 I/O 操作均是异步的，所有使用async标注的方法是异步的, 所有返回Promise方法是异步的
- 函数可以作为另外一个函数的参数或者返回值使用，这样的做法叫高阶函数
- 类中的方法有构造方法、原型方法和静态方法，和其他语言区别不大，注意this指向调用的实例


#### typeScript
- 特殊变量any, unknown, never, void
- 变量参数声明居然使用 `:` 冒号，而且还是写在标识符后面
- 可缺省属性需要使用 `?` 问号跟在标志符后面；任意属性使用 `[]` 标识
- 可以使用 `|` 来限定多个类型或多个取值，可以用来实现枚举类型
- 使用 `export{}` 控制当前文件内变量的作用域
- interface的作用似乎是用来定义属性，定义函数，不是用来实现只是用来接收变量的，又是鸭子类型

#### node code
- req.params 指url中占位符，req.query 指url中`?`的键值对参数
- array.map(() => ({a: f1, b: f2})); 取出对象的部分字段组成数组
- typescript是强类型的语言了，只有 any 类型才能通过 Dot Notation 添加属性, 明确类型使用 Bracket Notation 添加属性
- `const people: myInterface = {name: "lihua", age: 18};` 接口初始化的时候一定要一对一对的赋值，不能省略 name, age
- restify 框架的 next() 并不会出栈，需要在后面添加 return; 才行，或者直接 return next(); 确保触发回调后执行停止
  - https://stackoverflow.com/questions/16810449/when-to-use-next-and-return-next-in-node-js
- 数组元素解构赋值的时候需要注意一下bug: `const { appName } = arr[0].appName`; 这样是不行的额
- Spread syntax (...) 展开语法可以用于数组，也可以用于对象的
- JSON.parse()、JSON.stringify() 用于 JSON 与 string 格式的转换
- 数组没有越界异常，如果取不到值，就是 undefined
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
- JS中出现 `()` 括号代表要写 html
- HTML中出现 `{}` 花括号代表要写 js表达式
 - 能使用 const x = {} 接住的就叫表达式，接不住的就是js代码
- 向外提供特定功能的 js 程序，一般就是一个 js 文件，称之为模块
- 用来实现局部功能效果的代码和资源的集合(html/css/js/image)，称之为组件
- export default 可以写在 class 前面
- React 中的列表循环有且只有map可以使用，map才有返回值，forEach没有
- 设置变量使用 setState 方法, 绑定事件时 () => {} 胖箭头这种写法没有this绑定问题
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

#### 组件
- 用来实现局部功能效果的代码和资源的集合(html/css/js/image)，称之为组件，组件状态驱动页面
- `ReactDOM.render(<App/>, document.getElementById('root'))` 执行该行代码后
  - React 解析组件标签，找到 App 组件，找不到即报错
  - 发现组件是函数定义的，调用该函数，将返回的虚拟DOM转为真实DOM，渲染页面
