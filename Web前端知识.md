### Web前端知识

#### 前后端对比
|语言|框架|工具|项目构建|依赖管理|运行环境|
|:---:|:---:|:---:|:---:|:---:|:---:|
|javaScript es6,7,8|jquery, vue, react|vscode|webpack|npm|jre|
|java jdk8,9,10|spring, springMVC|idea|maven|maven|nodejs|

#### Vs code插件安装
- Auto Close Tag; Auto Rename Tag; Chinese (Simplified); ESLint; HTML CSS Support; 
- HTML Snippets; JavaScript (ES6) code snippets; Live Server; open in browser; Vetur;

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

####对象属性
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