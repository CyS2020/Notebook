### ECMAScript 6

#### Web 前端
- html是名词, css是形容词/副词, js是动词
- DOM: Document Object Model; 文本对象模型
- ![Dom对象](https://github.com/CyS2020/Notebook/blob/master/images/dom%E5%AF%B9%E8%B1%A1.png?raw=true)


#### css选择器
- 选择器
  - 类型选择器: 针对特定的标签, 通过标签声明 `h1{}`
  - 类选择器: 通过class指定, 通过点声明 `.style{}`
  - id选择器: 通过id指定, 通过#号声明 `#style{}`
  - 属性选择器: 通过属性名与值指定, 通过[]声明 `style[attr="value"]`
  - 通用选择器: 使用所有元素, 通过*指定 `*{}`
- 选择器组合
  - 通过`.`、` `(空格)、`,`组合
- 选择器优先级
  - 相同规则按照加载顺序, 后面优先级更高
  - 继承下来的样式永远低于直接指定的样式
  - 内联样式优先级最高, !important是个意外
  - 类型选择器 < 类选择器 < id选择器
- 静: css; 动: jquery
- 选择器可以搭配event事件的

#### css盒子模型
- 外边距-margin: margin
- 边框-border: border
- 内边距-padding: padding
- 内容-content: width + height
- 块级盒子: `<div/>, <p/>, <h1/>`
- 行内盒子: `<span/>, <a/>, <input>`

#### JQuery语法
- `$(selector).action()`: 定义符(选择器).操作()
- jQuery函数入口, DOM结构加载完毕
```jquery
$(document).ready(function(){
    // TODO
});
//或者
$(function(){
    // TODO
});
```
- javaScript函数入口, 网页全部加载完毕
```javascript
window.onload = function () {
    // TODO
}
```
- 常用选择器: 类型选择器(`p`), 类选择器(`.class`), id选择器(`#id`), 属性选择器(`[href]`)
- JQuery事件、动画、DOM操作、回调函数
- DOM操作
  - text()
  - html()
  - val()
  - attr()

#### javaScript
- this 关键字绑定
  - 默认绑定: 不使用call, apply, new时, 默认绑定到全局
  - 隐式绑定: 考虑是否有上下文对象, 如果有则指向该对象; 
  - 显示绑定: 通过call, apply, bind 进行显示绑定
  - new绑定: 优先级最高, 绑定到创建的对象上

#### 前后端对比
|语言|框架|工具|项目构建|依赖管理|  运行环境  |
|:---:|:---:|:---:|:---:|:---:|:------:|
|javaScript es6,7,8|jquery, vue, react|vscode|webpack|npm| nodejs |
|java jdk8,9,10|spring, springMVC|idea|maven|maven|  jre   |

#### Vs code插件安装
- Auto Close Tag; Auto Rename Tag; Chinese (Simplified); ESLint; HTML CSS Support;  Vetur; 
- HTML Snippets; JavaScript (ES6) code snippets; Vue 2 Snippets; Live Server; open in browser;

### ECMAScript 6.0
#### 变量声明
- var 声明的变量往往会越狱, 可以声明多次, 存在变量提升
- let 声明的变量有严格局部作用域，只能声明一次
- const 声明常量即只读变量

#### 解构表达式
- 使用解构表达式进行快速赋值, 数组[], 对象{}
- 使用: 将解构出的属性变量重新起一个变量名
```
// 数组解构
let arr = [1, 2, 3];
let [a, b, c] = arr;
// 对象解构
const person = {
    name : "Jack",
    age : 18,
    language : ['html', 'css', 'js']
};
const {name:abc, age, language} = person;
```

#### 字符串操作
- 字符串常见api需要了解下
- 使用字符串模板声明多行字符串，使用反引号: `` 来标识
- 字符串模板中的 ${} 里可以放入变量与表达式

#### 函数
- 使用function声明一个函数, 后面声明函数名与参数列表
- 不需要声明参数类型，参数可以设置默认值
- 使用return进行值的返回, 也不需要写返回值得类型
- 不定参数列表 ...args, lambda表达式等都支持

#### 对象属性
- 可以说 JavaScript对象是变量的容器; 但是, 我们通常认为 JavaScript对象是键值对的容器
- 键值对通常写法为 name : value; 键值对在 JavaScript 对象通常称为对象属性
- 既然是键值对那么有Object.keys(), Object.values(), Object.entries()这几个方法就不奇怪了
- 另外还有个Object.assign()用来合并几个对象为一个对象
- 对象里也可以拥有方法, 如果使用lambda这种形式则不能使用this

#### 对象的新特性
- 对象的深拷贝
```
let person = {
    name : "Jack",
    age : 18,
    language : ['html', 'css', 'js']
};
let someone = {...person};
```
- 对象的合并
```
let name = {name : "Jack"};
let age = {age : 18};
let person = {...name, ...age}
```
- 数组的map与reduce操作, 类似于java中stream的操作

#### 前端的异步操作
- 使用resolve方法将成功的结果传递下去, reject传递失败的结果
- .then() .catch() 和 .finally() 三个方法, 这三个方法的参数都是一个函数
```
let p = new Promise((resolve, reject) => {
    resolve(data);
    reject(err);
});
```

#### 模块化--导包
- 使用import导入其他模块功能
- 使用export规定模块对外接口

### Vue
#### MVVM思想
- M: 即model, 模型, 包括数据和一些基本操作
- V: 即view, 视图, 页面渲染结果
- VM: 即View-Model, 模型与视图间的双向操作(无需开发人员干涉)
- View === DOM Listeners ===> Model
- Model === Directives ===> View

#### Vue使用
- 创建一个Vue实例, 关联页面的模板, 将自己的数据渲染到关联的模板上
- 使用指令来简化对dom(Document Object Model)的操作. 例如: v-model; v-on;
- 声明方法来做更复杂的操作
- el绑定元素; data封装数据; methods封装方法
- {{表达式}} 该形式为插值表达式的写法,表达式必须有返回结果, 可以直接使用Vue的数据和函数
```
<body>
    <div id="app">
        <input type="text" v-model="num">
        <button v-on:click = "num++">点赞</button>
        <button v-on:click = "cancel">取消</button>
        <h1> {{name}} , 非常帅 {{num}} 人为他点赞</h1>
    </div>
    <script src="./node_modules/vue/dist/vue.js"></script>
    <script>
        let vm = new Vue({
            el: "#app",
            data: {
                name: "张三",
                num:1
            },
            methods:{
                cancel(){
                    this.num--;
                }
            }
        });
    </script>
</body>
```

#### Vue指令
- v-on : 主要用来监听dom事件，以便执行一些代码块; 还有事件修饰符及案件修饰符, 简写 `@`
- v-model : 这个指令用于在表单上创建双向数据绑定
- v-html : 双大括号的方式会将数据解释为纯文本
- v-text : 主要用来更新textContent
- v-bind : v-bind用来动态的绑定一个或者多个特性. 常用于动态绑定class和style以及href等, 简写 `:`
- v-for : 据遍历数组来进行渲染, `<ul><li>`遍历操作`</li></ul>`写入可以构成一个无序列表
- v-if : 可以实现条件渲染，Vue会根据表达式的值的真假条件来渲染元素; 配合v-else, v-else-if
- v-show : 根据条件展示元素. 和v-if不同的是, 如果v-if的值是false, 则这个元素被销毁不在dom中.
但是v-show的元素会始终被渲染并保存在dom中,它只是简单的切换css的display属性

#### 侦听器与过滤器
- 使用Vue对象里面的watch功能, 某个属性值发生变化后实时监控并给出响应的函数处理
- 使用Vue对象里面的filters功能, 使用插值表达式的时候结合 | 管道操作符来处理数据

#### 组件化
- 全局组件, 局部组件; 全局声明直接使用, 局部需要在Vue的components里面声明下
- Vue可以自定义组件, 复用代码; 组件其实也是一个Vue, data必须是一个函数不再是一个对象

#### 生命周期钩子函数
- beforeCreate, create, beforeMount, mounted, beforeUpdate, updated, beforeDestroy, destroyed, activated 

#### Vue脚手架模块开发
- 全局安装两个依赖, 然后初始化vue项目; vue脚手架使用webpack初始化一个应用项目
- 如果一直是 downloading template 状态, 则说明需要科学上网了
- 生成的目录结构
    - build : 和webpack有关的打包项目的代码在这里
    - config : 配置信息. 端口等
    - node_modules : 当前项目的依赖
    - src : 我们编写代码的文件夹, main.js为主程序
    - static : 静态资源文件. 图片, 字体等
    - 一堆配置文件, index.html为主入口页面
```
npm install webpack -g
npm install -g @vue/cli-init
vue init webpack 应用名
```

#### 单文件组件
- 三要素: template, script, style
- 编写代码时候的 xxx.js 路由地址与xxx.vue文件的对应
```
<template>
  <div>This will be pre-compiled</div>
</template>
<script src="./my-component.js"></script>
<style src="./my-component.css"></style>
```

### Element UI
#### Vue整合Element
- 安装, 导入与使用
```
npm i element-ui
// main.js引入element
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
// main.js使用element
Vue.use(ElementUI);
```

### 拦路虎
#### 前端项目npm失败
- 缺少某种驱动或者根据提示百度下需要安装啥更新啥，一般都能解决

#### vue不是内部命令
- 安装Node.js 并使用node -v查看
- 安装全局vue-cli 并使用 vue -V查看(V大写的哦)
```
npm install -g vue-cli
```
