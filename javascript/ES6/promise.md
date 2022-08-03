#### promise

1. **promise有几种状态**

   promise有三种状态，分别是：pending、resolved、rejected

   > * 默认初始化状态为 `pending`  状态，当执行完异步操作后，会变成 `resolved`  状态或者 `rejected`  状态。
   >
   > * promise的状态，根据实际的异步返回结果来控制
   > * promise的状态发生变化后，不能再继续修改
   > * promise.then((result) => {}, (reason) => {})：then()方法可以接收两个参数，第一个参数接收的是成功的回调，第二个参数接收是失败的回调。**第二个参数也可以不写**。
   > * promise.then((result) => {}, (reason) => {})，在处理 `rejected` 状态时，可以通过 promise.then(result => {}).catch(reason => {})来处理

   promise是一个对象，有resolve和reject两种回调方法

   ~~~js
   const promise1 = new Promise((resolve, reject) => {} )
   const promise2 = new Promise((resolve, reject) => resolve(1) )
   const promise3 = new Promise((resolve, reject) => reject(2) )
   
   console.log('promise1', promise1)     // 打印出promise对象，状态为padding
   
   console.log('promise2', promise2)     // 打印出promise对象，状态为resolved
   
   console.log('promise1', promise1)     // 打印出promise对象，状态为rejected
   
   promise2.then( v => {
       console.log('then')
       console.log('v: ', v)
   }).catch( v => {
       console.log('catch')
       console.log('v: ', v)
   })             // 打印出'then' 1 ，promise捕获到resolved，会执行then方法中的回调
   
   promise3.then( v => {
       console.log('then')
       console.log('v: ', v)
   }).catch( v => {
       console.log('catch')
       console.log('v: ', v)
   })             // 打印出'catch' 2 ，promise捕获到rejected，会执行catch方法中的回调
   ~~~

   

2. **promise状态是否可变**

   promise对象的状态不可变

   ~~~js
   const promise3 = new Promise((resolve, reject) => {
       reject(2) 
       resolve(1)
   })
   
   promise3.then( v => {
       console.log('then')
       console.log('v: ', v)
   }).catch( v => {
       console.log('catch')
       console.log('v: ', v)
   })             // 打印出'catch' 2 ，当执行到reject()回调后就不会再去执行resolve()回调，去更改promise的状态
   ~~~

3. **promise如何解决回调地狱**

   当第一次回调请求完成后—>第二次回调请求—>第三次回调请求.......

   像上面一样：一层又一层的回调嵌套就有可能导致回调地狱

   **then()回调方法可以return Promise**

   **使用promise解决回调地狱**：

   ~~~js
   function request1() {
       return new Promise((resolve, reject) => {
           resolve(1)
       })
   }
   
   function request2() {
       return new Promise((resolve, reject) => {
           resolve(2)
       })
   }
   
   function request3() {
       return new Promise((resolve, reject) => {
           resolve(3)
       })
   }
   
   // 解决回调地狱
   request1().then( v =>{
       console.log('第一层回调then')
       return request2()
   }).then( v => {
       console.log('第二层回调then')
       return request3()
   }).....
   ~~~

4. **promise有哪些方法？应用场景是什么？（all/race）**

   

5. 





#### async和await

> async函数真正意义上结局了异步回调的问题，用同步流程表达异步操作
>
> 本质：Generator语法糖
>
> 语法：
>
> ~~~js
> async function foo(){
>     await 异步操作;
>     await 异步操作;
> }
> ~~~
>
> 特点：
>
> 1. 不需要像Generator去调用next方法，遇到await等待，当前异步操作完成后再往下执行
> 2. 返回的值总是Promise对象，可以用then进行下一步操作
> 3. async 取代 Generator 函数的星号*，await取代了 Generator 的yield
> 4. 语义上更明确，使用简单

1. **async函数**

   * async 函数的返回值时 promise 对象
   * promise 对象的结果由async 函数执行的返回值来决定

   ~~~js
   // async函数
   async function fn() {
       /**
       *返回一个字符串或者其他返回值,或者直接返回
       *如果返回的值不是一个promise对象，那么async函数会返回一个成功的Promise对象
       */
       // return '返回的结果'
       // return
       
       /**
       * 抛出错误
       * 返回的结果是一个失败的promise
       */
       // throw new Error("error...");
       
       /**
       * 直接返回一个promise对象
       */
       // return new Promise((resolve, reject) => {
       //    resolve("成功了！")
       // })
   }
   ~~~

   

2. **await**

   * await必须写在async函数中，但是async函数可以不写await
   * await右侧表达式一般为promise对象
   * await返回值是promise成功的值
   * await的promise失败后，会抛出异常，通过try...catch捕获处理

   ~~~js
   let p = new Promise((resolve, reject) => {
       resolve("成功了!!!")
       
       reject("失败了。。。")
   })
   
   async funciton fn() {
       // promise状态为成功时的处理方式
       let result = await p
       console.log(resule)
       
       // promise状态为失败时的处理方式
       try{
           let result = await p
           console.log(result)
       } catch (e) {
           console.log(e)
       }
   }
   
   fn()
   ~~~

   * await函数会等待当前异步操作完成后才往后面继续进行

     ~~~js
     function fn() {
         return new Promise((resolve, reject) => {
             setTimeOut(() =>{
                 resolve("成功了")
             }, 2000)
         })
     }
     
     async function main() {
         console.log("开始执行")
         let result = await fn()
         console.log(result)
     }
     
     main()    	/**
     			* 执行结果：
     			* 	开始执行
     			*	成功了（等待两秒后）
     			*/
     ~~~

     

3. 



