
### Node.js
#### 安装软件
- 首先添加 Source 到 yum repo 里面 `curl -sL https://rpm.nodesource.com/setup_16.x | sudo bash -`
- 然后执行 `yum install nodejs`, 安装node、npm，然后执行 `npm install --global yarn`，安装yarn
- 使用 `yarn build` 编译程序使用 `node xxx.js` 运行程序
- package.json --> npm项目
- .eslintrc.json --> ESLint检查
- yarn.lock --> yarn项目
- tsconfig.json --> typeScirpt项目

#### 介绍
- Chrome = V8 + 内置API(DOM...); V8负责解析执行js代码，内置 api 由运行环境提供的特殊接口
- Node.js 是一个基于 Chrome V8 引擎的 javaScript 运行环境 = V8 + 内置API(http...)
- Node.js 应用程序运行在单个进程单个线程中，不需要为每个请求创建一个新线程
  - Node.js 中的库是使用非阻塞范式编写的，这使得阻塞行为成为例外而不是常态。
  - 当 Node.js 执行 I/O 操作时，比如从网络读取、访问数据库或文件系统，Node.js 将在响应返回时恢复操作，而不是阻塞线程和浪费 CPU 周期等待。
  - 这允许 Node.js 处理单个服务器上的数千个并发连接，而不引入管理线程并发性的负担，这可能是一个重要的 bug 来源。
