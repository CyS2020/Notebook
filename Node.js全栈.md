
### Node.js
#### 1. 介绍
- Node.js应用程序运行在单个进程中，不需要为每个请求创建一个新线程
  - Node.js中的库是使用非阻塞范式编写的，这使得阻塞行为成为例外而不是常态。
  - 当Node.js执行I/O操作时，比如从网络读取、访问数据库或文件系统，Node.js将在响应返回时恢复操作，而不是阻塞线程和浪费CPU周期等待。
  - 这允许Node.js处理单个服务器上的数千个并发连接，而不引入管理线程并发性的负担，这可能是一个重要的bug来源。

#### node.js code
- 关于文件换行符，window系统与linux系统不一致时，项目提示报错
  - 运行命令 `git config --global core.autocrlf true`
  - 或者添加 `.gitattributes` 内容为 `*.js text eol=lf`
