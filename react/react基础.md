### react基础

#### 一、基础语法

##### 1.创建DOM

1. 使用React中的`createElement`方法

   ~~~js
   const DOM = React.createElement(
       'h1',{id: 'test'}, React.createElement(
       'span',{},'Hello React!'
   )
   // 创建出如下结构的DOM
   <h1 id="test">
       <span>
       	Hello React!
       </span>
   </h1>
   ~~~

2. 使用JSX语法

   ~~~js
   const DOM = (
   	<h1 id="test">
       	<span>
       		Hello React!
       	</span>
   	</h1>
   )
   ~~~

##### 2.渲染DOM

~~~jsx
// React.render(dom元素, 挂载点)
React.render(DOM,document.getElementById('root'))
~~~

##### 3.JSX语法规则

* 定义`JSX`语法时，不要要使用引号

  ~~~jsx
  const DOM = <h1 id="test">Hello React!</h1>
  ~~~

* 在`JSX`语法中混入**JS表达式**，要使用{}。但是在`JSX`中加入**JS语句**会报错

  > JS 表达式：**有返回值的数据**
  >
  > * 变量a
  > * 变量相加：a+b
  > * 函数返回值：fun(2)
  > * 数组方法：arr.map()
  > * 一个函数：funciton test () {}
  >
  > JS 语句（代码）：**代码逻辑，并无返回值**
  >
  > * 判断语句：if(){}
  > * 循环语句：for(){}
  > * 循环语句：switch(){}

  ~~~jsx
  const user = {
      name: "Bob",
      age: 19
  }
  const DOM  = (
  	<div>
          <h1>Jack is 18</h1>
          <h1>{user.name} is {uer.age}</h1>
      </div>
  )
  ~~~

* 样式的类名不要使用class，要使用className

  ~~~jsx
  const DOM  = (
  	<div className="dom">
          <h1>Jack is 18</h1>
      </div>
  )
  ~~~

* 内联样式，要使用style={{key:value}}的形式

  ~~~jsx
  const DOM  = (
  	<div className="dom">
          <h1 style={{color: "orange", fontSize: "19px"}}>Jack is 18</h1>
      </div>
  )
  ~~~

* 虚拟DOM必须只有一个根标签

  ~~~jsx
  <div>
      <h1>hello</h1>
      <h1>world</h1>
  </div>
  ~~~

* 标签必须闭合

* 遍历元素时，每个元素需要有唯一的`key`

  ~~~jsx
  const arr = ['1','3','5','7']
  const ODM = (
  	<ul>
      	{
              app.map((item,index) => {
                  <li key={index}>{item}</li>    // 不加key会报错
              })
          }
      </ul>
  )
  ~~~

  

##### 4.React组件

**组件的首字母大写**

> * 什么是模块
>   * 理解：向外提供特定功能的JS程序，一般是一个JS文件
>   * 为什么拆分：随着业务逻辑增加，代码越来越复杂
>   * 作用：复用JS、简化JS编写、提高JS运行效率
> * 什么是组件
>   * 理解：实现局部功能效果的代码和资源集合（html/js/css)
>   * 为什么拆分：一个界面的功能会越来越复杂
>   * 作用：复用代码、简化代码、提高运行效率
> * 什么是模块化
>   * 当应用的JS都是以模块来开发，这个应用就是模块化应用
> * 什么是组件化
>   * 当应用的模块都是以组件来开发，这个应用就是组件化应用

**复习JS类**

* **创建一个类**

  ~~~js
  // 创建一个Person类
  clss Person{
      // 构造器方法
      constructor(name.age){
          // 构造器中的this ——类的实例对象
          this.name = name
          this.age = age
      }
      // 一般方法
      speak(){
          // speak方法放在哪里？ ——类的原型对象
          // speak中的this就是Person实例
          console.log(`我叫${this.name}，我的年龄是${this.age}`)
      }
      
  }
  
  // 创建一个Person的实例对象
  const p1 = new Person('tom', 18)
  const P2 = new Person('jack', 19)
  ~~~

* **类的继承**

  ~~~js
  // 创建一个student类，继承Person类
  class Student extends Person{
      constructor(name, age, grade){
          super(name, age)   // super必须放在最前面
          this.grade  = grade
      }
      // 重写从父类继承过来的方法，如果不重写将根据原型链继承父类方法
      speak(){
          
          console.log(`我叫${this.name}，我的年龄是${this.age}，我读${this.grade}`)
      }
      
      study(){
          // study方法放在哪里？ ——类的原型对象上，供实例使用
          // 通过Student实例调用study时，study中的this就是Student的实例
          console.log('我在学习')
      }
  }
  ~~~

  

1. **函数式组件**

   ~~~jsx
   function Demo() {
       return (
           <div className="dom">
           	<h1>Jack is 18</h1>
       	</div>
       )
   }
   
   // 渲染到页面
   ReactDom.render(<Demo/>, document.getElementById('root'))
   ~~~

2. **类式组件**

   ~~~jsx
   // 通过类创建的组件，必须继承React.Component类
   class Demo extends React.Component {
       // 必须要有render()函数
       render(){
           // render函数中必须有return，并且return中返回的是DOM元素
           // render放在哪里？ ——Demo原型对象上，供实例使用
           // render中this指的是？ ——Demo的实例对象
           return(
               <div className="dom">
           		<h1>Jack is 18</h1>
       		</div>
           )
       }
   }
   
   // 渲染到页面
   ReactDom.render(<Demo/>, document.getElementById('root'))
   /*
   执行ReactDom.render(<Demo/>.....之后，发生了什么
    1.React解析组件标签，找到Demo组件
    2.发现组件是类定义时，自动new出该类的实例，并通过实例调用到原型上的render()方法
    3.将rende()函数返回的虚拟DOM转化为真实DOM，在页面中渲染
   */
   ~~~

#####  5.react中的事件处理



#### 二、组件的三大核心

##### 1.state

**对state的理解**

> 1. state是组件对象中最重要的属性，值是对象
>
>    ~~~js
>    state : {
>        name: ‘Bob’，
>        age: 15
>    }
>    ~~~
>
> 2. 组件被称为”状态机“，通过更新组件的state来更新应用对应的页面显示

**注意事项**

> 1. 组件中render方法中this为组件的实例对象
>
> 2. 组件自定义的方法中this 为undefined，解决方法
>
>    1) 通过强制绑定this，通过函数对象的bind()
>
>    2) 使用箭头函数
>
> 3. 数据状态不能直接更新，要使用setState()

下面代码解释了未使用脚手架创建类，初始化状态，并执行函数的过程

~~~jsx
// 创建一个组件
calss Wether extends React.Component{
    constructor(props){
      	super(props)
        // 初始化状态
        this.state = {
            isHot: true
        }
        /*
        this.changeWether.bind(this)通过原型链找到changeWether这个方法;
        bind返回一个函数并改变这个函数的this，在构造函数器中，this就是类的实例对象。因此这里的this为react自动生成的实例对象
        当this.changeWether.bind(this)这段代码执行完成后，会生成一个新的函数，并且这个函数的this是Wether类的实例对象
        
        this.changeWether = this.changeWether.bind(this) 这是等式左边：this.changeWether会被赋值为等式右边的函数
        */
        this.changeWether = this.changeWether.bind(this)
    }
    
    render() {
        const {isHot} = this,state
        return <h1 onClick={this.changeWether}>今天天气{isHot ? "炎热" : "寒冷"}</h1>
    }
    
    changeWether() {
        // changeWether放在哪里？ ——Wether的原型对象上，供实例使用
        //由于changeWether是作为onClick的回调，所以不是通过实例调用，二十直接调用
        // 类中方法默认开启了局部严格模式，所以changeWether中this为undefined
        console.log(this)
    }
}
~~~



##### 2.props

props是实例 对象上的一个方法，在组件中通过{key: value}的形式可以给props添加属性，且props里的属性是只读的，组件内部不要修改props属性

**构造器是否接收props，是否传递给super，取决于：是否希望在构造器中通过this访问props**

~~~js
class Person extends React.Component{
    const {name, sex, age} = this.props
    render(
    	return(
    		<ul>
         		<li>{name}</li>
                <li>{age}</li>
                <li>{sex}</li>
         	</ui>
   		)
    )
}

let person = {
    name: 'tom',
	age: 18,
    sex: '男'
}
ReactDom.render(<Person {...person}/>, document.getElementById('root'))   // 批量传递标签属性，可类似于父子组件传值
~~~

函数式组件可以通过以下方式获取props

~~~js
function Person (props){
    const {name, age} = props
    return(
    	<ul>
        	<li>{name}</li>
        	<li>{age}</li>
        </ul>
    )
}

ReactDom.render(<Person name="jsck" age="18"/>, document.getElementById('root'))
~~~



##### 3.ref

>  组件内的标签可以定义`ref`来标识自身

* 字符串形式的`ref`，（后面可能会被官方取消）

  ~~~js
  handelBlur = () => {
      const { input1} = this.refs
      console.log(input1.placeholder)   // 打印 this is a inoput
  }
  
  handelClick = () => {
      const { button1 } = this.refs
      console.log(button1.innerHTML)   // 打印 点击 
  }
  
  render(){
      return(
          <div id="root">
          	<input ref="input1" placeholder="this is a input"/>
          	<button ref="button1" onClick={handelClick}>点击</button>
          </div>
      )
  }
  ~~~

* 回调形式的ref（最方便的一种形式）

  通过回调函数，创建实例对象时，回调函数，并给this中的实例赋值。

  ~~~js
  handelBlur = () => {
      const { input2} = this
      console.log(input2.placeholder)   // 打印 this is a inoput
  }
  
  handelClick = () => {
      const { button2 } = this
      console.log(button2.innerHTML)   // 打印 点击 
  }
  
  render(){
      return(
          <div id="root">
          	<input ref={(currentNode) => { this.input2 = cunrrentNode }} placeholder="this is a input" onBlur={this.handelBlur} />
          	<button ref={(currentNOde) => { this.button2 = currentNode }} onClick={handelClick}>点击</button>
          </div>
      )
  }
  ~~~

  这种方式创建的ref，**在页面更新时会被执行两次**，第一次会传入参数null，然后在第二次会传入参数DOM元素。这是因为在每次一次渲染时会创建一个新的函数实例，所以react会 清空旧的ref并且设置新的。

  上述情况在大多数情况下，都是无关紧要的，当然我们可以绑定class的方法来处理调用两次的问题

  ~~~react
  saveInput = (currentNode) => {
      this.input3 = currentNode
  }
  
  saveButton = (currentNode) => {
  	this.button3 =  currentNode    
  }
  
  handelBlur = () => {
      const { input3} = this
      console.log(input3.placeholder)   // 打印 this is a inoput
  }
  
  handelClick = () => {
      const { button3 } = this
      console.log(button3.innerHTML)   // 打印 点击 
  }
  
  render(){
     return(
  		<div id="root">
          	<input ref={this.saveInput} placeholder="this is a input"/>
      		<button ref={this.saveButton}>点击</button>	
      	</div>
  	) 
  }
  
  ~~~

* 通过createRef API创建ref（react推荐的形式）

  ~~~react
  class Demo extends React.Component{
      /*
  		React.creactRef()调用后可以返回一个容器，该容器可以存储被ref所标识的节点
          该容器是“专人专用”，当需要时需要为该节创建一个专有的ref容器
      */
      inputRef = React.creactRef()
  	buttonRef = React.creactRef()
  
  	handelBlur = () => {
          const { input4} = this.inputRef.current
          console.log(input4.placeholder)   // 打印 this is a inoput
      }
  
      handelClick = () => {
          const { button4 } = this.buttonRef.current
          console.log(button4.innerHTML)   // 打印 点击 
      }
  
  	render(){
          return(
          	<div id="root">
              	<input ref={this.inputRef} placeholder="this is a input"/>
      			<button ref={this.buttonRef}>点击</button>
              </div>
          )
      }
  }
  ~~~

##### 4.对ref的升华

reac官方说明：不要过度的使用ref

在一些情况下我们可以不需要使用ref，就可以获取到当前元素的节点信息

**react事件处理**

> 1. 通过 `onXxx`属性来定义一个事件处理函数（需要注意大小写）
>    * React使用的是自定义（合成）事件，而不是使用的原生DOM事件
>    * React中的事件是通过事件委托的方式处理的（委托给事件最外层的函数）
> 2. 通过`event.target`得到发生的DOM元素

**如果发生事件的元素正好是需要操作的元素就可以省略掉ref**

~~~js
handelBlur = (event) => {
    console.log(event.target.placeholder)   // 打印 this is a inoput
}

handelClick = () => {
    const { button2 } = this
    console.log(button2.innerHTML)   // 打印 点击 
}

render(){
    return(
        <div id="root">
        	<input placeholder="this is a input" onBlur={this.handelBlur} />
        	<button ref={(currentNOde) => { this.button2 = currentNode }} onClick={handelClick}>点击</button>
        </div>
    )
}
~~~

##### 5. react 中的高阶函数喝函数柯里化

场景：对于一个注册页面，如果需要获取多个输入的值，并保存到`state`中，在点击注册时往后端传值

* **传统方法**

~~~jsx
state = {
    name: "",
    password: "",
    phone: ""
}

saveName = (event) => {
    this.setSate({
        name: event.target.value
    })
}

savePassword = (event) => {
    this.setSate({
        password: event.target.value
    })
}

savePhone = (event) => {
    this.setSate({
        phone: event.target.value
    })
}

handelSubmit =() => {
    let {name, password, phone } = this.state
    console.log(name, password, phone)
}

render(){
    <Form onSubmit={this.handelSubmit}>
        name：<input onChange={this.saveName}/>
        password: <input onChange={this.savePassword} />
        phone： <input onChange={this.savePhone} />
        <button></button>
    </Form>
}
~~~

上述方式：较为繁琐，当需要监听多个值得变化时，需要写多个函数来处理变化的值，我们需要一种函数能够统一处理传入的值

* **使用高阶函数+函数柯里化实现 **

**高阶函数：**

> 1. 如果A函数，接收的参数是一个函数，那么A就是一个高阶函数。如下：onchange
> 2. 如果A函数，返回值是一个函数，那么A就是一个高阶函数。如下：saveFormData

**函数柯里化：**

> 通过函数调用继续返回函数的形式，实现多次接收参数的最后统一处理的函数编码形式。如下：saveFormData

~~~jsx
state = {
    name: "",
    password: "",
    phone: ""
}

saveFormData = (dataType) => {
    console.log(dataType)   // 打印出传入的的参数
    return (event) => {        // onchange回调的是return的函数，因此onchange回调时会将event传入函数，在这里return的函数可以接收到event参数
    console.log(event.target.value)   // 打印出当前DOM对象的value值
    this.setState({
        [dataType]: event.target.value
        /*
        [dataType]：JS中为对象添加属性时，如果属性是一个变量，需要用
        */
    })    
    }
}

handelSubmit =() => {
    let {name, password, phone } = this.state
    console.log(name, password, phone)
}

render(){
    <Form onSubmit={this.handelSubmit}>
        /* onchange处理的是一个回调函数，而不是会点函数的值
        	1. onChange={this.saveFormData('name')}这段语句是将this.saveFormData('name')的返回值交给onchange处理，这时会报错，该返回值并不是一个函数
        	2.我们可以将this.saveFormData('name')的返回值定义为一个函数，来满足onChange的回调要求
        */
        name：<input onChange={this.saveFormData('name')} />
        password: <input onChange={this.saveFormData('password')} />
        phone： <input onChange={this.saveFormData('phone')} />
        <button></button>
    </Form>
}
~~~

* **不用函数柯里化实现**

~~~jsx
state = {
    name: "",
    password: "",
    phone: ""
}

saveFormData = (dataType, value) => {
    // 此方法需要将返回值交给change回到函数中的箭头函数 
    this.setState({
        [dataType]: value
        /*
        [dataType]：JS中为对象添加属性时，如果属性是一个变量，需要用[]
        */
    })    
}

handelSubmit =() => {
    let {name, password, phone } = this.state
    console.log(name, password, phone)
}

render(){
    <Form onSubmit={this.handelSubmit}>
        /* onchange处理的是一个回调函数，而不是会点函数的值
        	1. onChange={this.saveFormData('name')}这段语句是将this.saveFormData('name')的返回值交给onchange处理，这时会报错，该返回值并不是一个函数
        	2.我们可以将this.saveFormData('name')的返回值定义为一个函数，来满足onChange的回调要求
        */
        name：<input onChange={(event) => {this.saveFormData('name',event)} />
        password: <input onChange={(event) => {this.saveFormData('password',event)} />
        phone： <input onChange={(event) => {this.saveFormData('phone',event)} />
        <button></button>
    </Form>
}
~~~

#### 三、react脚手架

1. 脚手架文件说明

   > **node_modeles-------webpack配置文件、以及引入的库文件**
   >
   > **public--------------------静态文件（图片以及一些公共）**
   >
   > * .ico----------------------网站页签图标
   > * index.html------------主页面
   > * manifest.json--------应用加壳的配置文件
   > * rebots.txt--------------爬虫协议文件
   >
   > **src--------------------------源码文件夹**
   >
   > * index.js-----------------入口文件
   >
   > * reportWebVitals.js--页面性能分析文件（需要web-vitals库的支持）
   >
   >   
   >
   > * setupTests.js----------组件单元测试的文件（需要jest-dom支持）

   index.html文件中部分标签说明

   ~~~html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="utf-8" />
       <!-- %PUBLIC_URL%代表public文件夹的路径 -->
       <link rel="icon" href="%PUBLIC_URL%/pictures/bms.ico" />
       <!-- 开启理想视口，用于移动端适配 -->
       <meta name="viewport" content="width=device-width, initial-scale=1" />
       <!-- 用于配置浏览器标签颜色，仅适用于安卓手机端 -->
       <meta name="theme-color" content="#000000" />
       <!-- 描述网站信息 -->
       <meta
         name="description"
         content="Web site created using create-react-app"
       />
       <!-- 用于指定网页在添加到手机主屏幕后显示的图标
         <link rel="apple-touch-icon" href="XXX.png"/> 
       -->
       <!-- 应用加壳配置文件
         	 什么是手应用加壳：
   		 1.部分网页为了适配安卓或者ISO移动端， 且不想开发相应的应用，可以通过加壳配置，是网页适配相应的移动端
        -->
       <link rel="manifest" href="%OUBLIC_URL%/manifest.json"/>
       <title>React App</title>
     </head>
     <body>
       <noscript>You need to enable JavaScript to run this app.</noscript>
       <div id="root"></div>
     </body>
   </html>
   
   
   ~~~

   

2. 