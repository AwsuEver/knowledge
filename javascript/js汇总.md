#### js获取节点方法

1. 通过id获取

   ~~~js
   document.getElementById('id')
   ~~~

2. 通过class获取

   ~~~js
   document.getElementsByClassName('class')
   ~~~

3. 通过name获取

   ~~~js
   document.getElementsByName('name')
   ~~~

4. 通过标签名获取

   ~~~js
   document.getElementsByTagName('tag')
   ~~~

5. 获取body

   ~~~js
   document.body
   ~~~

6. 获取html

   ~~~js
   document.documentElement
   ~~~

7. 通过选择器获取一个元素

   ~~~js
   document.querySelector('#id .class button')
   ~~~

8. 通过选择器获取一组元素

   ~~~js
   document.querySelectorAll('#id .class button')
   ~~~

   

#### 深拷贝浅拷贝

> 对象：深拷贝和浅拷贝都只针对引用数据类型
>
> 差异：1. 浅拷贝只复制内存地址，不复制对象本身，新旧对象共享同一个地址
>
> ​		 	2. 深拷贝会创建一个同样的新对象，新旧对象不会共享内存

1. **实现浅拷贝**

   * 直接用 = 赋值

     ~~~js
     let a = {a: 1, b: 2, c: 3}
     let b = a
     b.a = 100
     console.log(a.a)  // 100
     ~~~

   * Object.assign()方法

     ~~~js
     let a = {a: 1, b: 2, c: 3}
     let b = Object.assign(a)
     b.a = 100
     console.log(a.a)  // 100
     ~~~

   * for...in遍历第一层

     ~~~js
     let a = {a: 1, b: 2,c: {aa: 11, bb: 22}}
     let b = {}
     for (var key in a){
         b[key] = a[key]
     }
     b.a = 100
     b.b.aa = 1100
     console.log(a.a)       // 1
     console.log(a.b.aa)    // 1100
     ~~~

2. **实现深拷贝**

   * 对多层对象进行深度遍历

   * 通过JSON对象

     > 缺点：无法实现对象中方法的深拷贝，会显示undefined

     ~~~js
     let a = {a: 1, b: 2, c: 3}
     let b = JSON.parse(JSON.stringify(a))
     b.a = 100
     console.log(a.a)    // 1
     ~~~

   * 通过Object.assign()

     > 对象的value需要是基本类型

     ~~~js
     let a = {a: 1, b: 2, c: 3}
     let b = Object.assign({}, a)
     b.a = 100
     console.log(a.a)     // 1
     ~~~

   * 使用扩展运算符

     ~~~js
     let a = {a: 1, b: 2, c: 3}
     let b = {...a}
     b.a = 100
     console.log(a.a)    // 1
     ~~~

   * 使用 slice() 或 concat()

     > 针对数组

     ~~~js
     let a = [1, 2, 3, 4]
     let b = a.slice(0)
     let c = a.concat()
     b[0] = 10
     c[0] = 100
     console.log(a[0], b[0], c[0])   // 1 , 10 , 100
     ~~~

   
   

#### 防抖与节流

> 防抖：在一次触发事件后，不立即执行，而是给定一个时间值。
>
>    			1. 如果在这个时间范围内没有再次触发，那么就执行事件
>    			2. 如果在这个事件范围内再次触发，那么当前计时取消，重新记时
>
> 节流： 如果在短时间内大量触发同一个事件，那么在执行一次函数后，在限制的时间范围内不再执行，直到超过这个时间范围

1. **防抖**

   ~~~js
   // 原生js写法
   function debounce(fn, delay){
       var timer = null
       return function(){
           if(timer){
               clearTimeout(timer)
           }
           timer = setTimeout(fn, delay)
       }
   }
   function deal(){
       console.log(new Date().getTime())
   }
   window.onscroll = debounce(deal, 1000)
   ~~~

   ~~~react
   // react 写法
   ~~~

   

2. **节流**

   ~~~js
   // 原生js写法
   function throttle(fn, dely){
       var valid = true
       return function(){
           if(!valid){
              return false 
           }
           valid = true
           setTimeout(() => {
               fn()
               valid = false
           }, delay)
       }
   }
   
   function deal(){
       console.log(new Date().getTime())
   }
   window.onscroll = throttle(deal, 1000)
   ~~~

   ~~~react
   // react 写法
   ~~~

#### 闭包

#### 模块化编程

* **CommonJS**
* **AMD**
* **CMD**

#### 箭头函数

#### promise

> promise是异步编程的解决方案，可以取代传统方法：回调和事件。
>
> 特点：1.对象状态不受外界影响； 2.状态一旦改变就不会再变

**基本用法**

~~~js
test = (num) => {
    let promise = new Promise((resolve, reject) => {
        if(num === 1){
            resolve("promise执行成功了")
        }else{
            reject("promise执行失败了")
        }    
    })
    return promise
}

click = (num) => {
    this.test(num).then((res) => {
        console.log(res)
    }, (message) => {
        console.log(message)
    })
}

click(1)   // promise执行成功了
click(2)   // promise执行失败了
~~~

**all方法和race方法**

~~~js
p1 = (num) => {
    let promise = new Promise((resolve, reject) => {
        if(num === 1){
            resolve("promise执行成功了")
        }else{
            reject("promise执行失败了")
        }    
    })
    return promise
}
p2 = (num) => {
    let promise = new Promise((resolve, reject) => {
        if(num === 1){
            resolve("promise执行成功了")
        }else{
            reject("promise执行失败了")
        }    
    })
    return promise
}
p3 = (num) => {
    let promise = new Promise((resolve, reject) => {
        if(num === 1){
            resolve("promise执行成功了")
        }else{
            reject("promise执行失败了")
        }    
    })
    return promise
}

// all方法会等待所有异步操作执行完成后才执行函数
Promise.all([p1(), p2(), p3()]).then().catch()

// race方法等到第一个异步操作状态改变就开始执行回调函数
Promise.race([p1(), p2(), p3()]).then().catch()
~~~









