#### 一：src目录结构说明

> api                        ajax相关
>
> assets                  公共资源
>
> components      非路由组件
>
> config                  配置文件
>
> pages                   路由组件
>
> utils                      工具模块

#### 二：antd按需引入

##### 1. 安装相关依赖

   ~~~bash
   npm install antd react-app-rewired customize-cra
   
   npm install babel-plugin-import --save-dev
   
   
   ~~~

##### 2. 修改package.json文件

   ~~~json
   "scripts": {
   -   "start": "react-scripts start",
   +   "start": "react-app-rewired start",
   -   "build": "react-scripts build",
   +   "build": "react-app-rewired build",
   -   "test": "react-scripts test",
   +   "test": "react-app-rewired test",
   }
   ~~~

##### 3.  项目根目录创建一个 config-overrides.js 用于修改默认配置 

   ~~~js
   const { override, fixBabelImports } = require('customize-cra');
   
   module.exports = override(
     fixBabelImports('import', {
       libraryName: 'antd',
       libraryDirectory: 'es',
       style: 'css',
     }),
   );
   ~~~

   完成这一步时已经可以实现按需引入

#### 三：antd修改默认样式

##### 1. 安装相关依赖

   ~~~shell
   npm install less
   npm install less-loader@5.0.0
   -- 使用方式一修改 config-overrides.js 这里需要指明less-loader的版本号小于5.0，否则启动后会报错，方式二不需要指明
   ~~~

##### 2. 修改 config-overrides.js 文件

   ~~~js
// 方式一：
const { override, fixBabelImports, addLessLoader } = require('customize-cra');
   
   module.exports = override(
     fixBabelImports('import', {
       libraryName: 'antd',
       libraryDirectory: 'es',
       style: true,
     }),
   
     // 导入addLessLoader，并增加以下的内容
     addLessLoader({
         javascriptEnabled: true,
         modifyVars: {'@primary-color': '#1DA57A'}
     })
   );
   ~~~



~~~js
// 方式一：
const { override, fixBabelImports, addLessLoader } = require('customize-cra');
   
   module.exports = override(
     fixBabelImports('import', {
       libraryName: 'antd',
       libraryDirectory: 'es',
       style: true,
     }),
   
     // 导入addLessLoader，并增加以下的内容
     addLessLoader({
         lessOptions: {
           	javascriptEnabled: true,
         	modifyVars: {'@primary-color': '#1DA57A'} 
         }
     })
   );
~~~



#### 四：封装axios请求

##### 1. 封装代码

   ~~~js
   import axios from 'axios'
   
   // 返回值为一个promise对象，有then()和catch()方法
   export function request(url, data={}, type='GET'){
       if(type === 'GET'){
           return axios.get(url, {
               params: data
           })
       }else{
           return axios.post(url, data)
       }
   }
   ~~~

##### 2. 使用async和await

   * 作用

     简化promise的使用：不再使用 `then()/catch()` 来指定成功/失败的回调函数

     以同步编码（没有回调函数）方式实现异步流程

   * 哪里写await

     **await等待成功时返回promise的then()回调结果**

     在promise表达式的左侧写await：不想要promise，想要promise异步执行成功的数据

   * 哪里写async

     await所在函数（最近的）定义的左侧写async

   * 

##### 3. 优化请求代码，当执行失败后统一处理失败

   > **优化方案**
   >
   > 1. 在外层包一个promise
   > 2. 对成功的请求，写入到外层promise的resolve中
   > 3. 对失败的请求，不写入reject中，采用其他方法进行处理

   ~~~js
   import axios from 'axios'
   
   // 返回值为一个promise对象，有then()和catch()方法
   export function request(url, data={}, type='GET'){
       return new Promise((resole, reject) => {
           let result
           // 执行异步请求
           if(type === 'GET'){
               result = axios.get(url, {
                   params: data
               })
           }else{
               result = axios.post(url, data)
           }
           // 请求成功，调用resolve()回调
           promise.then( responce => {
             	resolve(responce)
           }).catch( error => {
           	alert('失败信息：'+ error.message)      
           })
           // 请求失败，不调用reject,统一处理失败信息
           
       })
   }
   
   // 调用统一请求函数
   
   // 第一种方式：采用await方式，不使用promise的回到函数
   async reqLogin = (name, password) => {
       const result = await request('/login', { name, password }, 'POST')
       this.setState({
           data: result.data
       })  
   } 
   // 第二种方式：使用回调函数，处理返回结果
   reqLogin = (name, password) => {
       request('/login', { name, password }, 'POST').then( res => {
           this.setState({
               data: res.data
           })    
       }) 
   }
   ~~~

#### 五：配置代理，解决跨域

**跨域的产生**： 如果请求的 **协议、域名、端口号、子域名** 不同，所进行的访问都是跨域的，而浏览器一般为了安全都限制了跨域访问，也就是不允许跨域访问资源 

##### 1. 使用代理解决跨域的原理：

   本地服务（localhost:3000）<----------->（localhost:3000）代理服务器（localhost:3000）<------------->被请求服务器（localhost:5000）

   > **说明：**
   >
   > * 本地服务器向远程服务器发出请求，由于同源策略的限制，会产生跨域（注意：**本地服务器可以将请求发出，远程服务器可以接收到请求并相应，但是远程服务器无法将相应返回给本地服务器**）
   > * 当脚手架服务启动后同时会启动一个代理服务，端口号和本地服务一样，**代理无服务只用来转发请求，并未使用Ajax请求，因此不会产生跨域**。本地服务localhost:3000会将请求发送给通过代理服务localhost:3000
   > * 代理服务locaihost:3000将请求转发给locaihost:5000，
   > * locaihost:5000收到请求并将请求响应给代理服务localhost:3000
   > * 代理服务localhost:3000接收响应，并将响应转发给本地服务localhost:3000

   

   **注意：**上述方法只适用于开发环境跨域请求，生产环境需要使用别的方式，而且，这种方式只适用于react脚手架生成的项目

   

##### 2. 当请求的跨域服务只有一个时，可以通过修改package.json文件

   ~~~js
   // 当请求的服务端唯一时，只需要在package.json文件中添加一行
   "proxy": "localhost:5000"
   
   
   // 发送请求
   /*
   	1.如果在请求中写全路径，需要写与本地启动服务相同的基础路径，如：http://localhost:3000
   	2.如果不写全路径，代理服务会默认加上本地启动服务的基础路径
   */
   axios.get('http://localhost:3000/students', params).then().catch()
   
   axios.get('/student', params).then().catch()
   ~~~

   注意：**这种配置方式，当本地服务没有这个文件时，回响远程服务器请求，当本地服务有改文件时，就不会向远程服务请求**

   

##### 3. 当请求的跨域服务不唯一时

   ~~~js
   // 在src目录下创建setupProxy文件，并添加文件内容
   
   const proxy = require('http-proxy-middleware')
   
   module.exports = funciton(app) {
       app.use('/api1', {
           target: 'http://localhost:5000',    // 将以/api1开头的请求转发到http://localhost:5000
           changeOrigin: true,            // 默认值为false，尽量改成true，用于控制服务器收到的请求头中的HOST值。如果不改为true，服务器显示的是请求主机的HOST名；如果改为true，可以将HOST改为服务器的HOST名。可以避免一些服务端限制
           pathRewrite: {'/api1': ''}     // 将请求路径中的 /api1 替换成空串，否者路径出错
       }),
           
       app.use('/api2', {
           target: 'http://localhost:5001',    // 将以/api2开头的请求转发到http://localhost:5001
           changeOrigin: true,                // 默认值为false，尽量改成true，用于控制服务器收到的请求头中的HOST值。如果不改为true，服务器显示的是请求主机的HOST名；如果改为true，可以将HOST改为服务器的HOST名。可以避免一些服务端限制
           pathRewrite: {'/api2': ''}     // 将请求路径中的 /api2 替换成空串，否者路径出错
       })
   }
       
   // 新建请求
   axios.get('http://localhost:3000/api1/students').then().catch()    
   /*
   	代理服务器将含有 /api1 的请求转发给： http://localhost:5000/students， 
   	其中 /api1 是用来区分走哪个代理的，最后被替换成空串
   */
   
   axios.get('http://localhost:3000/api2/teachers').then().catch()
   /*
   	代理服务器将含有 /api2 的请求转发给： http://localhost:5001/teachers， 
   	其中 /api2 是用来区分走哪个代理的，最后被替换成空串
   */
   
   ~~~

#### 六：react路由

##### 1.单页面应用（SPA） 
    > * 整个应用只有一个完整的页面
   > * 点击页面中的链接不会进行刷新，只会做页面的局部更新
   > * 数据都需要通过Ajax请求获取，并在前端异步展示

##### 2. 前端路由理解

   > * 什么是路由
   >   * 一个路由就是一个映射关系
   >   * key为路径，value可能为function也可能为component
   >
   > * 前端路由
   >
   >   * 浏览器通过监听 `history`  的变化来获取路由信息
   >
   >     ~~~js
   >     // 创建一个hostory对象
   >     let history = History.createBrowserHistory()  // 方法一：通过H5的history对象
   >     
   >     let history = History.createHashHistory()  // 方法二：通过hash值创建，路由中待用 `#` 兼容性更好
   >     
   >     // 添加一条路由记录
   >     history.push(path)
   >     // 替代一条路由记录
   >     history.replace(path)
   >     // 前进（浏览器前进按钮相同作用）
   >     hostory.goForward()
   >     // 后退（浏览器后退按钮相同作用）
   >     history.goBack()
   >     
   >     ~~~
   >
   >     

##### 3. react-router的理解（react中路由管理）

   安装web开发使用的路由管理工具

   ~~~shell
   npm install react-router-dom
   ~~~

   创建一个简单的路由页面

   ~~~jsx
   import React from 'react'
   import { BrowserRouter, Link, Route } from 'react-router-dom'
   import Login from './pages/Login/Login'
   import Admin from './pages/Admin/Admin'
   
   class App extends React.Component{
   
     render() {
       return(
         <BrowserRouter>
           <div className="leftMenu">
             <Link to="/login">登录</Link>    // Link标签类似于<a>标签，会跳转到指定的组件去
             <Link to="/admin">首页</Link>
           </div>
           <div className="rightMenu">
             <Route path="/login" component={Login} />   // 路由器监听到<Link>的请求，修改页面中的组件内容
             <Route path="/admin" component={Admin} />
           </div>
         </BrowserRouter>
       )
     }
   
   }
   
   export default App;
   ~~~

   

##### 4. 路由组件与一般组件

   * 写法不同

     一般组件： <Login />

     路由组件： <Route path="/login" component={Login} />

   * 一般存放的文件夹不同

     一般组件： components 目录下

     路由组件： pages 目录下

   * 接收到的props不同

     一般组件：  <Login user={name: '', age: ''}  />   // 传递什么就接收到什么内容

     路由组件：  <Route path="/login" component={Login} />   // 会接收到默认传入的三个对象：

     ~~~json
     history: {
     	go: ƒ go(n),
     	goBack: ƒ goBack(),
     	goForward: ƒ goForward(),
         location: {
             pathname: "/login",
             search: "",
             hash: "",
             state: undefined
         },
     	push: ƒ push(path, state),
     	replace: ƒ replace(path, state),
     }
     location: {
         hash: "",
         pathname: "/login",
         search: ""
         state: ,
     }
     match: {
         params: {},
     	path: "/login",
     	url: "/login",
     }
     ~~~

     

##### 5. `<Link>`和`<NavLink>`

   * react路由器会监听<Link>和<NavLink>，
   * 当点击<NavLink>时，react会添加一个 `className="active"` 属性给<a>标签，同样可以在<NavLink> 中使用`activeClassName='XXX'` 添加指定名称的`className` 。
   * 通过this.props.children可以获取标签体内的内容
   * <Link>标签既不会自动添加也不能修改className名

   **封装一个<NavLink>**

   1. 封装NavLink

      ~~~jsx
      import React from 'react'
      import { NavLink } from 'react-router-dom'
      
      class MyNavLink extends React.Component{
      
        render() {
          return(
            <NavLink activeClassName="active1" className="list-group-native" {...this.props} />
          )
        }
      
      }
      
      export default MyNavLink;
      ~~~

   2. 调用MyNavLink

      ~~~jsx
      import React from 'react'
      import { BrowserRouter, Link, Route } from 'react-router-dom'
      import MyNavLink from './pages/MyNavLink'
      import Login from './pages/Login/Loing'
      import Admin from './pages/Admin/Admin'
      
      class App extends React.Component{
      
        render() {
          return(
            <BrowserRouter>
              <div className="leftMenu">
                <MyNavLink to="/login" a={1} b={2} >登录</MyNavLink>
                <MyNavLink to="/admin" >首页</MyNavLink>
              </div>
              <div className="rightMenu">
                <Route path="/login" component={Login} />   // 路由器监听到<Link>的请求，修改页面中的组件内容
                <Route path="/admin" component={Admin} />
              </div>
            </BrowserRouter>
          )
        }
      
      }
      
      export default App;
      ~~~

      

##### 6. 路由中的Switch

   ~~~jsx
   
     render() {
       return(
         <BrowserRouter>
           <div className="leftMenu">
             <Link to="/login">登录</Link> 
             <Link to="/admin">首页</Link>
           </div>
           <div className="rightMenu">
             <Route path="/login" component={Login} />
             <Route path="/admin" component={Admin} />
             <Route path="/admin" component={Home} />
           </div>
         </BrowserRouter>
       )
     }
   
   }
   
   ~~~

   上面这种情况，当路由匹配到 `/admin`  时，会把 `Home` 和 `Admin` 组件的内容同时展示

   ~~~jsx
   
     render() {
       return(
         <BrowserRouter>
           <div className="leftMenu">
             <Link to="/login">登录</Link> 
             <Link to="/admin">首页</Link>
           </div>
           <div className="rightMenu">
             <Switch>
               <Route path="/login" component={Login} />
             	<Route path="/admin" component={Admin} />
             	<Route path="/admin" component={Home} />
             </Switch>
           </div>
         </BrowserRouter>
       )
     }
   
   }
   
   ~~~

   上面这种情况，当路由匹配到第一个 `/admin`  时，就会停止，展示第一个匹配到的组件

   

##### 7. 解决多级路由的样式丢失问题

   ~~~jsx
    render() {
       return(
         <BrowserRouter>
           <div className="leftMenu">
             <Link to="/aaa/login">登录</Link> 
             <Link to="/aaa/admin">首页</Link>
           </div>
           <div className="rightMenu">
             <Switch>
               <Route path="/aaa/login" component={Login} />
             	<Route path="/aaa/admin" component={Admin} />
             </Switch>
           </div>
         </BrowserRouter>
       )
     }
   
   }
   ~~~

   ~~~html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="utf-8" />
       <link rel="icon" href="./Imgs/bms.ico" />
       <link rel="stylesheet" href="./css/bootstrap.css" />
       <title>React App</title>
     </head>
     <body>
       <div id="root"></div>
     </body>
   </html>
   ~~~

   像上面的情况，`html`  文件中引入了静态资源，在强制刷新时，如果静态资源是**相对路径**，会导致静态资源获取不到，因为请求的静态文件路径会被自动添加上`/aaa` 目录结构

   

   可以采取以下方法

   ~~~html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="utf-8" />
        <!-- <link rel="stylesheet" href="./css/bootstrap.css" />
       <meta name="viewport" content="width=device-width, initial-scale=1" /> -->
          // 方法一：去掉 .
         <link rel="icon" href="/Imgs/bms.ico" />
         <link rel="stylesheet" href="/css/bootstrap.css" />
          // 方法二： 将 . 改为%public%
         <link rel="icon" href="%PUBLIC_URL%/Imgs/bms.ico" />
         <link rel="stylesheet" href="%PUBLIC_URL%/css/bootstrap.css" />
       <title>React App</title>
     </head>
     <body>
       <div id="root"></div>
     </body>
   </html>
   ~~~

   方法三：将监听路由由 `BrowserRouter` 改成 `HashRouter`

   改成 `HashRouter` 后URL中，在IP+端口后会加上一个 `/#`  ，浏览器任务 `/#` 后面的内容为前端资源，不会向服务器请求，因此不会修改前端资源的请求路径

   ~~~jsx
    render() {
       return(
         <HashRouter>
           <div className="leftMenu">
             <Link to="/login">登录</Link> 
             <Link to="/admin">首页</Link>
           </div>
           <div className="rightMenu">
             <Route path="/login" component={Login} />
             <Route path="/admin" component={Admin} />
           </div>
         </HashRouter>
       )
     }
   
   }
   ~~~

   

##### 8. 路由的模糊匹配和严格匹配

   情况一：下面情况会模糊匹配到与之相似的路由

   ~~~jsx
   // 默认情况下，路由匹配为模糊匹配 
   render() {
       return(
         <BrowserRouter>
               
           <div className="leftMenu">
             // 这种情况点击是会匹配上相关路由
             <Link to="/login/a/b">登录</Link> 
             <Link to="/admin/c/d">首页</Link>
           </div>
               
           <div className="rightMenu">
             <Route path="/login" component={Login} />
             <Route path="/admin" component={Admin} />
           </div>
               
         </BrowserRouter>
       )
     }
   }
   ~~~

   情况二：下面情况不会匹配到与之相似的路由

   ~~~jsx
   render() {
       return(
         <BrowserRouter>
               
           <div className="leftMenu">
             // 这种情况点击不会匹配上相关路由
             <Link to="/login">登录</Link> 
             <Link to="/admin">首页</Link>
           </div>
               
           <div className="rightMenu">
             <Route path="/login/a/b" component={Login} />
             <Route path="/admin/c/d" component={Admin} />
           </div>
               
         </BrowserRouter>
       )
     }
   }
   ~~~

   使用精准匹配解决模糊匹配会出现的问题

   ~~~jsx
   render() {
       return(
         <BrowserRouter>
               
           <div className="leftMenu">
             // 这种情况点击是会匹配上相关路由
             <Link to="/login">登录</Link> 
             <Link to="/admin/c/d">首页</Link>
           </div>
               
           <div className="rightMenu">
             // 下面两种当时都开启了精准匹配
             <Route exact path="/login" component={Login} />  // 会匹配上
             <Route exact={true} path="/admin" component={Admin} />  // 不会匹配上
           </div>
               
         </BrowserRouter>
       )
     }
   }
   ~~~

   **注意：**并非所有情况都需要开启严格匹配，严格匹配可能会导致严重问题（会导致无法匹配到二级路由），如果不开启严格匹配不会出现异常，可以不开启严格匹配

   

##### 9. 路由中 `redirect` 的使用

   初始进入页面时，URL为：localhost:8000，不会匹配到路由

   当没有指明路由时，可以使用 `redirect` 来指明路由

   ~~~jsx
   import { BrowserRouter，Route，Redirect } from 'react-router-dom'
   
   render() {
       return(
         <BrowserRouter>
               
           <div className="leftMenu">
             <Link to="/login">登录</Link> 
             <Link to="/admin">首页</Link>
           </div>
               
           <div className="rightMenu">
             <Route path="/login" component={Login} />
             <Route path="/admin" component={Admin} />
             <Redirect to="/login" />   // 当未指明路由时，路由会指向 `/login`
           </div>
               
         </BrowserRouter>
       )
     }
   }
   ~~~

   

##### 10. 嵌套路由

​	当为多级路由时，**二级路由在配置时需要带上一级路由的路径**

~~~jsx
// 一级路由

import { BrowserRouter，Route，Redirect } from 'react-router-dom'
   
render() {
    return(
        <BrowserRouter>

            <div className="leftMenu">
                <Link to="/login">登录</Link> 
                <Link to="/admin">首页</Link>
            </div>

            <div className="rightMenu">
                <Route path="/login" component={Login} />
                <Route path="/admin" component={Admin} />
                <Redirect to="/login" />   // 当未指明路由时，路由会指向 `/login`
            </div>

        </BrowserRouter>
    )
}
~~~

~~~jsx
// 二级路由    admin.jsx文件

import { Route，Redirect, Switch } from 'react-router-dom'
import Home from ''
import Message from ''
   
render() {
    return(
        <BrowserRouter>

            <div className="leftMenu">
                <Link to="/admin/home">home</Link> 
                <Link to="/admin/message">message</Link>
            </div>

            <div className="rightMenu">
                <switch>
                    <Route path="/admin/home" component={Home} />
                    <Route path="/admin/message" component={Message} />
                    <Redirect to="/admin/home" />
                </switch>
            </div>

        </BrowserRouter>
    )
}
}
~~~

##### 11.向路由组件传递参数

当一级路由组件向二级路由组件传递参数时，有下面方式可以采用

###### （1）通过params

~~~jsx
// 一级路由

import { BrowserRouter，Route，Redirect } from 'react-router-dom'
import { Detail } from ''
 
const obj = [
    {id: 1, name: 'AAA'},
    {id: 2, name: 'BBB'},
    {id: 3, name: 'CCC'},
]

render() {
    return(
        <BrowserRouter>

            <ul>
                obj.map((item, index) => {
                    return (
                    	<li>
                            <Link to={`/detail/${obj.id}/${obj.name}`}></Link>
                        </li>
                    )
                })
            	<li></li>
            </ul>

            <div className="rightMenu">
                <Route path='/detail/:id/:name' component={Detail}></Route>
            </div>

        </BrowserRouter>
    )
}

~~~

~~~jsx
// 二级路由

import { BrowserRouter，Route，Redirect } from 'react-router-dom'
import { Detail } from ''
 
const data = [
    {id: 1, sex: 'man'},
    {id: 2, sex: 'woman'},
    {id: 3, sex: 'man'},
]

render() {
    const { obj } = this.props.match.params
    const findObj = data.find(item => {
        return obj === item.id
    })
    return(
        <ul>
        	<li>ID: {obj.id}</li>
            <li>Name: {obj.name}</li>
            <li>SEX: {findObj.sex}</li>
        </ul>
    )
}

~~~

###### （2）通过search

~~~jsx
// 一级路由

import { BrowserRouter，Route，Redirect } from 'react-router-dom'
import { Detail } from ''
 
const obj = [
    {id: 1, name: 'AAA'},
    {id: 2, name: 'BBB'},
    {id: 3, name: 'CCC'},
]

render() {
    return(
        <BrowserRouter>

            <ul>
                obj.map((item, index) => {
                    return (
                    	<li>
                            <Link to={`/detail/?id=${obj.id}&name=${obj.name}`}></Link>
                        </li>
                    )
                })
            	<li></li>
            </ul>

            <div className="rightMenu">
                <Route path='/detail' component={Detail}></Route>
            </div>

        </BrowserRouter>
    )
}
~~~

~~~jsx
// 二级路由
import { BrowserRouter，Route，Redirect } from 'react-router-dom'

render() {
    const { search } = this.props.location
    console.log(search) // 打印结果：?id=1&name='AAA'
    
}

~~~

###### （3）通过state

这里的state是路由中的state并非React中状态管理的state

~~~jsx
// 一级路由

import { BrowserRouter，Route，Redirect } from 'react-router-dom'
import { Detail } from ''
 
const obj = [
    {id: 1, name: 'AAA'},
    {id: 2, name: 'BBB'},
    {id: 3, name: 'CCC'},
]

render() {
    return(
        <BrowserRouter>

            <ul>
                obj.map((item, index) => {
                    return (
                    	<li>
                            <Link to={{pathname: '/detail', state: {id: obj.id, name: obj.name}}}></Link>
                        </li>
                    )
                })
            	<li></li>
            </ul>

            <div className="rightMenu">
                <Route path='/detail' component={Detail}></Route>
            </div>

        </BrowserRouter>
    )
}
~~~

~~~jsx
// 二级路由

render() {
    const { state } = this.props.location
    console.log(state) // 打印结果：{id=1, name='AAA'}
    
}
~~~



##### 12.withRouter的使用

###### （1）解决的问题

如果一般组件中想要使用路由组件的 `history.push()` 和 `history.replace()` 等方法，可以使用`withRouter()`

**一般组件和路由组组建的区别参考：第4节**

###### （2）使用方式

~~~jsx
import { withRouter } from 'react-router-dom'

class Demo extends Component {
    
}

export default withRouter(Demo)
~~~

上面的写法可以使 `Demo` 这个一般组件具有路由组件的属性



##### 13.BrowserRouter和hashRouter的区别

###### （1）底层原理不同

​			BrowserRouter使用的是H5的history API，不兼容IE9及以下版本

​			HashRouter使用的是URL的哈希值

###### （2）URL表现形式不一样

​			BrowserRouter的路径中没有#，例如：127.0.0.1:8000/Home

​			HashRouter的路径中包含#，例如：127.0.0.1:8000/#/Home

###### （3）刷新后对路由state的影响

​			BrowserRouter没有任何影响，因为state保存在history对象中

​			HashRouter刷新后会导致路由state参数丢失

###### （4）HashRouter可以用于解决一些路径错误的相关问题



#### 七：redux相关

 ##### 1.redex基本概念

###### （1）redux是什么

* redux 是一个专门用于**状态管理**的JS库（不是react插件库）

- 它可以在react、angular、vue等项目重使用，但基本与react配合使用
- 作用：集中式管理react应用中多个组件共享的状态

###### （2）什么情况下使用redux

* 某个组件的状态，需要让其组件可以随时拿到（共享）
* 一个组件需要改变另一个组件的状态（通信）
* 总体原则：能不用就不用，如果不用的话比较吃力才考虑使用

##### 2. redux工作流

##### 3.redux的三个核心概念

###### （1）action

* 动作的对象
* 包含两个属性
  * type：标识属性，值为字符串，唯一，必要属性
  * data：数据属性，值的类型任意，可选属性
* 例子：`{type: 'ADD_USER', data: {name: 'tom', age: 18}}`

 ###### （2）reducer

* 用于初始化状态、加工状态
* 加工时，根据旧的`state`和`action`，产生新的`state`的纯函数

###### （3）store

- 将`state`、`action`、`reducer`联系在一起的函数

- 如何得到此对象

  ~~~js
  // 引入redux
  import { createStore } from 'redux'
  
  import reducer from './reducers'
  
  const store = create(reducer)
  ~~~

- 此对象的功能

  * getState()： 得到state
  
  * dispatch(action)：分发action，触发reducer调用，产生新的state
  
  * subscribe(listener)：注册监听，当产生新的state时，自动调用
  
    

#### 八：mobx

##### 1.安装mobx

~~~bash
npm install mobx mobx-react
~~~

**使用 mobx **

* 安装配置插件

  ~~~bash
  npm install antd react-app-rewired customize-cra
  
  npm install babel-plugin-import --save-dev
  ~~~

* 修改配置文件

  ~~~js
  const { override, addDecoratorsLegacy } = require('customize-cra');
     
     module.exports = override(
       addDecoratorsLegacy()
     );
  ~~~

* 修改 `package.json` 文件

  ~~~json
  "scripts": {
     -   "start": "react-scripts start",
     +   "start": "react-app-rewired start",
     -   "build": "react-scripts build",
     +   "build": "react-app-rewired build",
     -   "test": "react-scripts test",
     +   "test": "react-app-rewired test",
  }
  ~~~

  

##### 2.实例

~~~jsx
import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import { observable, action } from 'mobx'
import { observer } from 'mobx-react'

class Store {
    @observable count = 0  // @observable装饰器函数，使得count可被mobx观测 

    @action.bound increment() {   // @action装饰器函数，用于修改count值 
        this.count++
    }
}

@observer    // @observer装饰器函数，用于将组件和mobx进行关联，动态获取监听的数据
class App extends Component{
    render() {
        const { store } = this.props
        return(
            <div>
                <p>{store.count}</p>
                <button onClick={store.increment()}>Increment</button>
            </div>
        )
    }
}

ReactDOM.render(<App store={new Store()}/>, Document.getElementById('root'))
~~~

##### 3.装饰器语法补充

**定义**：装饰器是一个对类进行处理的函数

* 普通装饰器函数

  ~~~js
  @fn
  class Myclass {
      
  }
  
  function fn(target){
      target.foo='bar'
  }
  
  console.log(Myclass.foo)    // bar
  ~~~

* 可以传参的装饰器函数

  ~~~js
  @fn(10)
  class Myclass {
      
  }
  
  function fn(num){
      return function(target){
          target.count=num
      }
  }
  
  console.log(Myclass.count)    // 10
  ~~~

* 添加实例属性

  ~~~js
  @fn
  class Myclass {
      
  }
  
  function fn(target){
      target.prototype.isTestble = true
  }
  
  console.log(new Myclass().isTestble)    // true
  ~~~

##### 4.@observable函数

只有被@observable修饰的函数，才会被mobx监听，并执行一系列的自动更新等操作

~~~js
import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import { observable, action, autorun } from 'mobx'
import { observer } from 'mobx-react'

class Store {
    @observable count = 0  // @observable装饰器函数，使得count可被mobx观测 

	foo='bar'

    @action.bound increment() {   // @action装饰器函数，用于修改count值 
        this.count++
    }
}

Store = new Store()

autorun(() => {
    console.log(count)
    cousole.log(foo)
})

Store.count = 1
Store.foo = 'baz'
/* 函数执行结果：
	0
	bar
	1
*/
~~~

上面的实例可以看出，只有在mobx中如果我们需要监听一个某个值的变化，需要对其添加 `@observable`  修饰函数，否则不会引起数据变化

##### 5.@computed计算属性

**含义**：computed计算属性，可以对mobx中需要进行相关业务操作数据进行计算，如果多次使用，会进行缓存，只进行一次计算

~~~js
import React, { Component } from 'react'
import { observable, action, autorun, computed } from 'mobx'
import { observer } from 'mobx-react'

class Store {
    @observable count = 0  // @observable装饰器函数，使得count可被mobx观测 
	@observable price = 10

	@action.bound increment() {   // @action装饰器函数，用于修改count值 
        this.count++
    }

	@couputed get total () {
        return this.count * this.price
    }
}

@observer    // @observer装饰器函数，用于将组件和mobx进行关联，动态获取监听的数据
class App extends Component{
    render() {
        const { store } = this.props
        return(
            <div>
                <p>{store.total}</p>
                <p>{store.total}</p> 
                <p>{store.total}</p>         // 多次调用时会进行缓存，只进行一次计算
                <button onClick={store.increment()}>Increment</button>   // 点击后会触发计算属性
            </div>
        )
    }
}

ReactDOM.render(<App store={new Store()}/>, Document.getElementById('root'))

~~~

##### 6.@action

###### (1) action使用

当需要对 `mobx` 中的多个值进行修改时，如果直接进行修改，会触发 `mobx`  进行多次更新操作

~~~js
import React, { Component } from 'react'
import { observable, action, autorun } from 'mobx'
import { observer } from 'mobx-react'

class Store {
    @observable count = 0  // @observable装饰器函数，使得count可被mobx观测 
	@observable price = 10

	@action.bound increment() {   // @action装饰器函数，用于修改count值 
        this.count++
    }

	@couputed get total () {
        return this.count * this.price
    }
}

store = new Store()

autorun(() => {
    console.log(store.count, store.foo)
})

store.count = 1
store.price = 20

/*执行顺序为：
	0，10
	1，10
	1，20
*/
~~~

如果使用 `action`  函数，只会进行一次修饰

~~~js
import React, { Component } from 'react'
import { observable, action, autorun } from 'mobx'
import { observer } from 'mobx-react'

class Store {
    @observable count = 0  // @observable装饰器函数，使得count可被mobx观测 
	@observable price = 10

	@action.bound increment() {   // @action装饰器函数，用于修改count值 
        this.count++
    }

	@action change() {
        this.count = 1
        this.price = 20
    }
}

store = new Store()

autorun(() => {
    console.log(store.count, store.foo)
})

store.change()

/* 执行顺序为：
	0, 10
	1, 20
*/
~~~

###### （2）action-bound

为 `@action` 修饰的方法绑定 `this`  ，在另一个组件中使用 `@action` 修饰时方法时，可能会导致 `this` 指向出现问题 

~~~jsx
import React, { Component } from 'react'
import { observable, action, autorun } from 'mobx'
import { observer } from 'mobx-react'

class Store {
    @observable count = 0  // @observable装饰器函数，使得count可被mobx观测 

	@action.bound increment() {   // @action装饰器函数，用于修改count值 
        console.log(this)
    }

	@action change() {
        console.log(this)
    }
}

store = new Store()
const increment = store.increment
const change = store.change

increment()  // 打印出this
change()     // undefined
~~~

###### （3）action-runInAction

为了在不定义 `action`  的情况下，对数据进行修改，可以使用 `runInAction` 的方法

~~~js
import React, { Component } from 'react'
import { observable, action, autorun, runInAction } from 'mobx'
import { observer } from 'mobx-react'

class Store {
    @observable count = 0  // @observable装饰器函数，使得count可被mobx观测 
}

store = new Store()
runInAction(() => {
    store.count = 1
})

~~~

###### （4）异步action

在严格模式下，异步执行action函数会导致报错，解决这种情况有以下三种方法

~~~js
import React, { Component } from 'react'
import { observable, action, autorun, runInAction } from 'mobx'
import { observer } from 'mobx-react'

class Store {
    @observable count = 0  // @observable装饰器函数，使得count可被mobx观测

	@action.bound asyncChnage () {
        
        // 直接执行会报错
        setTimeOut(() => {
            this.count = 10 
        },1000)
        
        // 方法一：再调用一个 @action 修饰的函数
        setTimeOut(() => {
             this.changeCount()
        },1000)
        
        // 方法二：直接调用 aciton 函数
        setTimeOut(() => {
             action('changeCount', () => {
                 this.count = 10
             })()
        },1000)
        
        // 使用 runInAction
        setTimeOut(() => {
             runInAction(() => {
            	this.count = 10
        	})
        },1000)
        
    }

	@action changeCount() {
        this.count = 10
    }
}

store = new Store()

store.asyncChnage()

~~~

#### 九：装饰器

> 装饰器是一个对类进行处理的函数，该函数接收的第一个参数就是类本身

##### 1. 基本语法

~~~javascript
@test
class Testable {
    // ...
}

function test(target) {
    target.isTestAble = true
}

let obj = new Testable()

console.log(obj.isTestAble)    //  true
~~~

##### 2. 支持参数的装饰器

> 通过返回函数的方式为装饰器传参

~~~javascript
@test(10)
class Testable {
    // ...
}

function test(bar) {
    return function(target){
        target.bar = bar
    }
}

let obj = new Testable()

console.log(obj.bar)   // 10
~~~

##### 3.添加实例属性

~~~javascript
@test
class Testable {
    // ...
}

function test(target) {
    target.prototype.isTestAble = true
}

console.log(Testable.isTestAble)    // true
~~~

##### 4.装饰器用来修饰类的成员变量

~~~javascript
class Tets{
    @fn foo = 'bar'
}

function fn(target, name, desc){
    /**
    * target：目标类的prototype
    * name：被修饰的类的成员变量
    * desc：被修饰类的成员的描述对象
    */
    
}
~~~

