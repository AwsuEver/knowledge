#### 什么是ES6

1. ##### ECMAscript与javascript的关系

   ECMA（国际标准化组织），JavaScript是由Netscape公司创造，并提交给ECMA，希望成为国际标准。

   由于Java与JavaScript的商标问题，同时又由于ECMA想体现这门语言的制定者是ECMA。因此ECMAscript也就是JavaScript

2. ##### ES6和ECMAscript2015的关系

   2015年ECMA发布了JavaScript的6.0版本，因此ECMA2015和ES6都是说明JavaScript的第6.0+版本

3. ##### Babel转码器

   > Babel是ES6的转码器，可以将ES6代码转换为ES5代码，从而可以在老版本浏览器上执行

   * ###### 使用Babel

     首先需要在项目中安装Babel

     ~~~shell
     npm install --save-dev @babel/core
     ~~~

   * ###### 配置文件.babelrc

     Babel的配置文件是.babelrc，存放在项目的根目录下，一般和package.json放在同一目录，需要手动创建。

     这个文件用来设置转码规则和插件，基本格式（json）如下

     ~~~json
     {
         "presets": [],
         "plugins": []
     }
     ~~~

     例如：presets字段设定转码规则，我们可以根据需要下载官方的依赖集

     ~~~shell
     # 最新转码规则
     $ npm install --save-dev @babel/preset-env
     
     # react 转码规则
     $ npm install --save-dev @babel/preset-react
     ~~~

     下载依赖集以后，再把这些规则加入到.babelrc文件中

     ~~~json
     {
         "presets": [
           "@babel/env",
           "@babel/preset-react"
         ],
         "plugins": []
       }
     ~~~

   * **需要安装的插件**

     1. Babel提供了命令行工具`@babel/cli`,用于命令行转码，安装命令如下：

        ~~~shell
        npm install --save-dev @babel/cli
        ~~~

     2. `@babel/node`模块的`babel-node`命令，提供一个支持ES6的REPL环境的模块。它支持Node的REPL环境的所有功能，而且可以直接运行ES6代码。安装命令如下：

        ~~~shell
        npm install --save-dev @babel/node
        ~~~

     3. `@babel/register`模块改写`require`命令，每当使用`require`加载`.js`、`.jsx`、`.es`、`.es6`后缀的文件名，就会先用Babel进行转码。安装命令如下：

        ~~~shell
        npm install --save-dev @babel/register
        ~~~

     4. babel只会转换最新的JS语法，不会转换API，比如：Set、Map、Proxy等全局对象，以及定义在全局对象上的方法：Object.assign都不会进行转码。安装命令如下：

        ~~~shell
        npm install --save-dev core-js regenerator-runtime
        ~~~


#### 基本语法

##### 1.let和const

* **都是块级别元素**

  ~~~js
  function fi(){
      let a = 1
      const b = 10
      {
          let a = 2
          const b = 20
      }
      console.log(a)          // 1
      console.log(b)          // 10
  }
  ~~~

  块内元素和块外元素互不冲突，可重复定义

* **const定义的是常量**

  ~~~js
  const a = 1
  a = 2                     // 报错
  
  let b = 10
  b = 20                    // b=20
  ~~~

  const定义的变量是常量，在同一个作用域内如果重新赋值会报错，**常量**在定义的时候需要进行赋值，否则会报错

* **不存在变量提升**

  ~~~js
  console.log(a)           // undefined、存在变量提升
  var a = 10
  
  console.log(b)           // 报错，不存在变量提升
  let b = 10
  
  console.log(c)           // 报错，不存在变量提升
  const c = 10
  ~~~

  

* **存在暂时性死区**

  ~~~js
  var a = 123
  if(true){
      a = 456              // 报错
      let a
  }
  ~~~

  存在全局变量 `a` ，在块级作用域中又用 `let` `const` 声明一个局部变量 `a` ，局部变量会绑定这个局部作用域，如果在`let` 前为局部变量赋值会报错

##### 2.变量结构赋值

* **数组的结构赋值**

  本质上这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值

  ~~~js
  let [a,b,c] = [1,2,3]
  console.log(a,b,c)        // 1,2,3
  
  let [ , ,c] = [1,2,3]
  cosole.log(c)            // 3
  
  let [head, ...tail] = [1,2,3]
  console.log(head,tail)   // 1, [2,3]
  
  let [a,b,..c] = [1]
  cosole.log(a,b,c)       // 1, undefined, []
  
  let [a,[b],c] = [1,[2,3],4]
  console.log(a,b,c)      // 1 2 4
  ~~~

  如果解构不成功，变量的值就等于undefined

  **设置默认值**

  ~~~js
  let [foo = true] = []
  foo     // true
  
  let [x, y = 'b'] = ['a']    // x='a', y='b'
  let [x, y = 'b'] = ['a',undefined] //x='a',y='b'
  
  ~~~

* **对象的结构赋值**

  对象的解构赋值和数组的结构赋值不同的是：数组元素按顺序排列，对象不需要按顺序排列，只要对象的属性相同，就可以取到正确的值

  ~~~js
  let {foo, bar，baz} = {foo: "aaa", bar: "bbb"}
  foo   // 'aaa'
  bar   // 'bbb'
  baz   // undefined
  ~~~

  针对变量名和属性名不一致，必须要这样写

  ~~~js
  let {foo: f, bar} = {foo: "aaa", bar: "bbb"}
  f         // "aaa"
  bar       // "bbb"
  ~~~

  **默认值**

  ~~~js
  let {x = 3} = {}          // x:3
  
  let {x, y = 3} = {x: 1}  //x:1 y:3
  
  let {x: y = 1} = {}      // y: 1
  
  let {x: y = 3} = {x: 5}  // y: 5
  ~~~

* **字符串结构赋值**

  ~~~js
  let [a,b,c,d,e] = "hello"
  a // 'h'
  b // 'e'
  c // 'l'
  d // 'l'
  e // 'o'
  ~~~

* **结构赋值的用处**

  * 交换变量值

    ~~~js
    let x = 1
    let y = 2
    [x, y] = [y, x]
    ~~~

  * 从函数返回多个值

    ~~~js
    function fun(){
        return [1,2,3]
    }
    let [a,b,b] = fun()
    ~~~

  * 提取json数据

    ~~~js
    let jsonData = {
        id: 1,
        name: "bob",
        age: 18
    }
    let {id, name, age} = jsonData
    ~~~

  * 输入模块的指定方法

    加载模块时，往往需要输入指定的方法。使用解构赋值可以使得输入语句清晰

    ~~~js
    let {SourceMapConsumer, SourceNode} = require("source-map")
    ~~~

##### 3.字符串拓展和新增方法

* **遍历字符串**

  ES6新增了字符串遍历接口，是的字符串可以被`for ... of`循环

  ~~~js
  for (let codePoint of 'bar'){
      console.log(codePoint)
  }
  // 'b'
  // 'a'
  // 'r'
  ~~~

* **模板字符串**

  模板字符串是增强型字符串，用反引号(`)标识。作用： 

  1. 可以当作普通字符使用

  2. 可以定义多行字符串

  3. 在字符串中插入变量

     ~~~js
     const str = 'world'
     const srt1 = `hello ${str}`
     
     console.log("hello world")
     ~~~

  模板字符串可以调用函数

  ~~~js
  function fn(){
      return "hello world"
  }
  `foo ${fn()} bar`
  
  // foo hello world bar
  ~~~

  模板字符可以嵌套

  ~~~js
  const srt = `hello world
  `这是嵌套`
  123`
  ~~~

  模板字符串中使用变量和字符串

  ~~~js
  const str1 = `hello ${world}`
  // world 变量未被定义，报错
  
  const str2 = `hello ${'world'}`
  // hello 
  ~~~

* 实例方法

  **includes(),startsWith(),endsWith()**

  > includes()：返回布尔值，表示是否找到了参数字符串
  >
  > startsWith()：返回布尔值，表示参数字符串是否在原字符串头部
  >
  > endsWith()：返回布尔值，表示参数是否在原字符串尾部

  ~~~js
  let str = 'hello world'
  
  str.includes('hh')   // true
  str.startsWith('hello')  // true
  str.endsWith('rld')    //true
  ~~~

  上述三个方法，都支持第二个参数。startsWith()、includes()的第二个参数表示从第`n`个位置到字符串结束

  endsWith()的第二个参数`n`，针对的是前`n`个字符

* **repeat()**

  > `repeat`返回一个新的字符串，表示原字符串重复`n`次

  ~~~js
  'abc'.repeat(2)      // "abcabc"
  'abc'.repeat(0)      // ""
  'abc'.repeat(2.9)    // "abcabc"
  ~~~

* **padStart()、padWith()**

  ES2017提供了字符串自动补全功能，如果字符串不够长，会在头部和尾部进行补全

  ~~~js
  'a'.padstart(5,'bcd')    // bcdba
  
  'a'.padEnd(5,'bcd')      //abcdb
  ~~~

  * 如果省略第二个参数，默认用空格不全
  * 如果原字符长度等于或大于最大长度，则字符串补全不生效，返回原字符串
  * 如果补全字符串与原字符串长度之和，大于参数的最大长度，则截去超出的补全字符串

* **trimStart()、trimEnd()**

  与`trim()`方法一致，`trimStart()`消除字符串头部空格，`trimEnd()`消除字符串尾部空格

  ~~~js
  const s = "  abc  "
  
  s.trim()          // "abc"
  s.trimStart()     // "abc  "
  s.trimEnd()       // "  abc"
  ~~~

* **replaceAll()**

  在ES6之前，字符串的实力方法replace()只能替换第一个匹配

  ~~~js
  'aabbcc'.replace('b','_')      // 'aa_bcc'
  
  // 如果替换多个需要使用正则表达式
  'aabbbcc'.replace(/b/g, '_')   // 'aa___cc'
  ~~~

  使用replaceAll()可以解决上面多个替换的问题

  ~~~js
  'aabbcc'.replaceAll('b', '_')   // 'aa__cc'
  ~~~


##### 4.箭头函数

* 声明函数及调用

  ~~~js
  let func = (参数) => {
      /*函数体*/
  }
  
  func()
  ~~~

* 与普通函数的区别

  1. this是静态的，this始终指向函数申明时所在的作用域下的this值，无论使用call()、bind()无法改变this的指向

     ~~~js
     let name = 'a'
     let obj = {
         name: 'b'
     }
     
     func() {
         console.log(this.name)
     }
     
     let func1 = () => {
         console.log(this.name)
     }
     
     func.call(obj)   // 'b'
     func1.call(obj)  // 'a'   // this指向声明时作用域下的this，使用call90,bind()无法改变
     ~~~

  2. 不能作为构造函数实例化对象

     ~~~js
     let Person = (name, age) => {
         this.name = name
         this.age= age
     }
     
     let me = new Person('Bob', 18)    // 报错，Person is not a constructor
     ~~~

  3. 不能使用arguments保存变量

     ~~~js
     function fn(){
         console.log(arguments)    // 打印出arguments对象
     }
     fn(1,2,3)
     
     let fn1 = () => {
         console.log(arguments)   // 报错，arguments is not defined
     }
     fn1(1,2,3)
     ~~~

  4. 箭头函数简写

     ~~~js
     // 1.省略小括号，当形参有且只有一个的时候
     let fn = (a) => {
         return 2*a
     }
     // 上述可以写成
     let fn = a => {
         return 2*a
     }
     
     // 2.省略花括号，当代码体只有一句，并且需要省略return
     let fn = a => {
         return 2*a
     }
     //上述可以写成
     let fn = a => 2*a
     ~~~

#####  6.函数参数默认值设置

ES6中函数参数可以赋初始值

~~~js
function fn (a,b=2){
    return a+b
}

fn(1)     // 3
fn(1,4)   // 5
~~~

与结构赋值共用

~~~js
function fn({name, age, sex}){
    console.log(name)    // bob
    console.log(age)     // 15
    console.log(sex)     // mail
}

fn({
    name: "bob",
    age: 15,
    sex: "mail"
})
~~~

##### 6.rest参数

ES6引入rest参数，用于获取函数实参，用来代替ES5中的arguments

* arguments使用

  ~~~js
  function date() {
      console.log(arguments)   // arguments对象，包含参数值
  }
  date(1,2,3,4)
  ~~~

* rest参数

  ~~~js
  function date(...args){
      console.log(args)    // [1,2,3,4]
  }
  date(1,2,3,4)
  
  // rest参数必须放在最后，否则会报错
  function date1(a,b,...args){
      console.log(args)    // [3,4]
  }
  date(1,2,3,4)
  
  ~~~

##### 7.拓展运算符

拓展运算符的结构是：`...[array]`

~~~js
let arr = ['a','b','c','d']
function fu(){
    console.log(arguments)
}

fn(arr)       // 打印除arguments对象，只包含一个与arr值相同的数组
fn(...arr)    // 打印出arguments对象，arr数组元素被展开成4个元素
~~~

* 数组合并

  ~~~js
  const arr1 = ['a','b','c']
  const arr2 = ['d','e','f']
  
  const arr = arr1.concat(arr2)    // ['a','b','c','d','e',f]
  const arr = [...arr1, ...arr2]   // ['a','b','c','d','e',f]
  ~~~

* 数组克隆

  ~~~js
  const arr1 = ['a','b','c']
  
  const arr = [...arr1]   // ['a','b','c']
  ~~~

* 将伪数组转为真数组

  ~~~js
  <html>
  <div></div>
  <div></div>
  <div></div>
  </html>
  
  const divs = document.querySelectorAll('div')
  
  const divArr = [...divs]    // 生成数组
  ~~~

  


##### 8.Symbol

ES6引入了新的数据类型：symbol，用来表示独一无二的值，它是JS的第七种数据类型，十一中类似于字符串的数据类型

> 1. symbol的值是唯一的，用来解决命名冲突的问题
> 2. Symbol的值不能与其他数据进行运算
> 3. Symbol的对象属性不能使用`for...in`循环遍历，但是可以用`Reflect.ownKeys`来获取对象所有键名

~~~js
// 创建symbol
// 方式一
let s = Symbol()
// 方式二
let s1 = Symbol("aaa")    // 添加一个字符串，字符串是一个注释说明的作用
// 方式三
let s2 = Symbol.for('bbb')
~~~

* **Symbol的使用**

  

* 

#### 箭头函数

