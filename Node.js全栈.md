
### Node.js
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
- 在模板字符串中嵌入变量的时候，需要将变量名写在${}中。
- 对象字面量的赋值；函数声明会提升；
- 类表达式与类声明
  - 只能有唯一的构造函数，静态变量不能由实例访问
  - 当调用静态或原型方法时没有指定 this 的值，那么方法内的 this 值将被置为 undefined
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

#### typeScript
- 特殊变量any, unknown, never, void
- 变量参数声明居然使用 `:` 冒号，而且还是写在标识符后面
- 可缺省属性需要使用 `?` 问号跟在标志符后面；任意属性使用 `[]` 标识
- 可以使用 `|` 来限定多个类型或多个取值，可以用来实现枚举类型
- 使用 `export{}` 控制当前文件内变量的作用域
- interface的作用似乎是用来定义属性，定义函数，不是用来实现只是用来接收变量的，又是鸭子类型

#### 介绍
- Chrome = V8 + 内置API(DOM...); V8负责解析执行js代码，内置 api 由运行环境提供的特殊接口
- Node.js 是一个基于 Chrome V8 引擎的 javaScript 运行环境 = V8 + 内置API(http...)
- Node.js 应用程序运行在单个进程单个线程中，不需要为每个请求创建一个新线程
  - https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop
  - Node.js 中的库是使用非阻塞范式编写的，这使得阻塞行为成为例外而不是常态。
  - 当 Node.js 执行 I/O 操作时，比如从网络读取、访问数据库或文件系统，Node.js 将在响应返回时恢复操作，而不是阻塞线程和浪费 CPU 周期等待。
  - 这允许 Node.js 处理单个服务器上的数千个并发连接，而不引入管理线程并发性的负担，这可能是一个重要的 bug 来源。

#### node code
- req.params 指url中占位符，req.query 指url中`?`的键值对参数
- array.map(() => ({a: f1, b: f2})); 取出对象的部分字段组成数组
- typescript是强类型的语言了，只有 any 类型才能通过 Dot Notation 添加属性, 明确类型使用 Bracket Notation 添加属性
- `const people: myInterface = {name: "lihua", age: 18};` 接口初始化的时候一定要一对一对的赋值，不能省略 name, age
- restify 框架的 next() 并不会出栈，需要在后面添加 return; 才行，或者直接 return next(); 确保触发回调后执行停止
  - https://stackoverflow.com/questions/16810449/when-to-use-next-and-return-next-in-node-js
- 数组元素解构赋值的时候需要注意一下bug: `const { appName } = arr[0].appName`; 这样是不行的额
- Spread syntax (...) 展开语法可以用于数组，也可以用于对象的

### React快速入门
#### 开始项目
- `npx create-react-app demo`: 初始化项目，src文件下的文件可以全部删除
- 文件名可以是 jsx 或者 js，不影响文件中的代码
- 组件名必须大写
- JS中出现 `()` 括号代表其中想要写 html
- HTML中出现 `{}` 花括号代表其中相要写 js
- export default 可以写在 class 前面
- React 中的列表循环有且只有map可以使用，map才有返回值，forEach没有
- 设置变量使用 setState 方法, 绑定事件时 () => {} 胖箭头这种写法没有this绑定问题
  - 或者函数在外面显示定义，绑定事件时使用 this.funcName.bind(this, parm1, parm2, ...) 即可
