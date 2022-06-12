### HTML知识点

#### 常用标签

> 标题
>
> > `h1` -> `h6` 、`title`
> >
> > `hr` 水平线可用于分割
>
> 段落
>
> > `p`
> >
> > `br` 换行
>
> 文本格式化
>
> > `b`、`em`，`i`，`small`，`strong`，`sub`，`sup`，`ins`、`del`、`q`
>
> 区块
>
> > `p`、`div`、`span`
>
> 列表
>
> > `ul`、`ol`、`li`、`dl`、`dt`、`dd`
>
> 表格
>
> > `table`、`tr`、`th`、`td`、`thead`、`tbody`、`tfoot`、`caption`
>
> 链接、图像
>
> > `a`、`img`
>
> 框架
>
> > `iframe`

#### html5

* 语义化

  优点：1. 样式丢失时页面会呈现清晰的结构

  2. 有利于SEO
  3. 方便屏幕解析器解析（如盲人阅读器）
  4. 便于代码维护

  > `header`、`nav`、`section`、`article`、`aside`、`figcaption`、`figure`、`footer`

  ![HTML5 语义元素](https://www.runoob.com/wp-content/uploads/2013/07/html5-layout.jpg)

* 表单

  * 新增元素

    `detalist`  用户输入时显示选项、`keygen`  用户加密生成密钥、`output`  用户不同类型的输出

  * 新增属性

    - form属性

      `autocomplete`,`novalidate`布尔值，不验证输入域

    - input属性

      `autocomplete`、`autofocus`、`form`、`formaction`描述表单提交地址、`formenctype`描述数据编码、`formmethod`描述表单提交方式、`formnovalidate`布尔值表单提交无需验证、`formtarget`、`height`、`width`、`list`、`min`、`max`、`mulltiple`、`pattern`

      `placeholder`、`required`、`step`
      
    - input类型
    
      `color`、`date`、`datetime`、`datetime-local`、`email`、`month`、`number`、`range`、`search`、`tel`、`time`、`url`、`week`

* 多媒体

  - 视频（video）

    ```html
    <video width=‘’ height='' controls autoplay loop muted ></video>
    	<source src="" type="video/mp4">
    	<source src="" type="video/ogg">
    	<source src="" type="video/webm">
    </video>
    
    <!--
    video标签：
    	autoplay: 视频就绪后马上播放
    	controls: 展示控件
    	height: 播放器高度
    	width: 播放器宽度
    	loop: 完成播放后再次开始播放
    	muted： 视频输出为静音
    	poster: 视频下载时显示的图像
    	preload: 视频在页面加载时进行加载，并预备播放，使用autoplay时或略该属性
    source标签：
    	src: 视频地址
    	type：媒体资源MIME类型
    -->
    ```

    

  - 音频（audio）

    ~~~html
    <audio controls autoplay loop muted ></video>
    	<source src="" type="video/mp3">
    	<source src="" type="video/ogg">
    	<source src="" type="video/mpeg">
    </audio>
    
    <!--
    video标签：
    	autoplay: 音频就绪后马上播放
    	controls: 展示控件
    	loop: 完成播放后再次开始播放
    	muted： 静音
    	preload: 页面加载时音频是否默认加载，以何种形式加载
    source标签：
    	src: 音频地址
    	type：媒体资源MIME类型
    -->
    ~~~

    

* 图形

  - Canvas

    > canvas是图形容器，用于定义图形、图像，可以使用脚本来绘制图形

    1. 创建画布

       ~~~html
       <canvas id="myCanvas" width="200" height="200" style="border: 1px solid black">
           
       </canvas>
       ~~~

    2. 尝试用js来绘制图形

       ~~~html
       <script>
       	let canvas = document.getElementById(',yCanvas') //获取元素
           let ctx = canvas.getContext('2d') // 创建context对象
           ctx.fillStyle="#FF0000" // 属性值可以是：颜色、渐变、图案
           ctx.fillRect(0,0,150,75) // 定义当前填充方式fillRect(x,y,width,height)
       </script>
       <!--
       canvas坐标：
       	canvas容器的左上角坐标为(0,0)
       	X轴为上方的线，正方向向右
       	Y轴为左侧的线，正方向向下
       -->
       ~~~

    3. 绘制路径

       ~~~html
       <script>
       	let canvas = document.getElementById(',yCanvas')
           let ctx = canvas.getContext('2d')
           // 绘制直线
           ctx.moveTo(0,0)      // 定义开始坐标
           ctx.lineTo(100,100)  // 定义结束坐标
           ctx.stroke()         // 开始绘制直线
           
           // 绘制圆
           ctx.beginPath()     // 可以不写
           ctx.arc(90, 45, 10, 0, 2*Math.PI)   // 绘制圆的方法 arc(x,y,r,start,stop)
           ctx.stroke()
       </script>
       ~~~

    4. 绘制文本

       ~~~html
       <script>
       	let canvas = document.getElementById(',yCanvas')
           let ctx = canvas.getContext('2d')
           
           ctx.font="30ox Arial"   // 定义字体
           // 绘制实心字体
           ctx.fillText("Hello World!", 10,30)  // fillText(text, x, y)
           // 绘制空心字体
           ctx.strokeText("Hello World", 10, 30)  // strokeText(text, x, y)
       </script>
       ~~~

    5. 渐变

       > 渐变可以填充矩形、圆形、线条、文字，可以自己定义不同的颜色

       绘制线条渐变

       ~~~html
       <script>
       	let canvas = document.getElementById(',yCanvas')
           let ctx = canvas.getContext('2d')
           
           // 创建渐变
           var grd = ctx.createLinearGradient(0,0,200,0); //createLinearGradient(x, y, x1, y1) 
           grd.addColorStop(0,"red");   // addColorStop()指定颜色停止，参数使用坐标来描述，可以是0至1.
           grd.addColorStop(1,"white");
       
           // 填充渐变
           ctx.fillStyle=grd;
           ctx.fillRect(10,10,150,80);
       </script>
       ~~~

       绘制径向/圆渐变

       ~~~html
       <script>
       	let canvas = document.getElementById(',yCanvas')
           let ctx = canvas.getContext('2d')
           
           // 创建渐变
           var grd=ctx.createRadialGradient(75,50,5,90,60,100);  // createRadialGradient(x, y, r, x1, y1, r1)
           grd.addColorStop(0,"red");
           grd.addColorStop(1,"white");
       
           // 填充渐变
           ctx.fillStyle=grd;
           ctx.fillRect(10,10,150,80);
       </script>
       ~~~

       

    6. 将图片放到画布上

       ~~~html
       <script>
           // 创建canvas对象
       	let canvas = document.getElementById(',yCanvas')
           let ctx = canvas.getContext('2d')
           
           // 获取图片
           let img = document.getElementById('img')
           
           ctx.drawImage(img, 10, 30)    // drawImage(image, x, y)
       </script>
       ~~~

       

  - SVG

    

* 存储

  cookie、html5存储（localStorage、sessionStorage）

  > localStorage:仅在客户端存储不参与服务器通信，存储大小一般为5M，如果不是人为清除，那么即使是关闭浏览器也会一直存在。
  >
  > sessionStorage:仅在客户端存储不参与服务器通信，存储大小一般为5M，会话级存储，也就是说如果关闭当前页面或者浏览器那么就会清除
  >
  > cookie:客户端存储，参与服务器通信，存储大小为4k,可设置生命周期，在设置的生命周期内有效
  >
  > > 存储方面localstorage和sessionStorage具有巨大优势，大小为5M，比cookie大多了。换个说法：localStorage是一个小型的数据库

  - 需要注意的问题：

    - `localStorage`和`sessionStorage`的返回值都为string，使用时需要进行类型转换
    - 浏览器隐私模式下不可读取
    - 不同浏览器无法共享`localStorage`、`sessionStorage`
    - `localStorage`、`sessionStorage`无法被爬虫获取
    - 存储过多时会导致页面变卡，本质上是对字符串的读取

  - API

    ~~~js
    /**
    以localStorage为例，sessionStorage和localStorage方法类似
    */
    // 保存数据
    localStorage.setItem(key, value)
    // 读取数据
    localStorage.getItem(key, value)
    // 删除单个数据
    localStorage.removeItem(key, value)
    // 删除所有数据
    localStorage.clear()
    // 获取某个索引的key
    localStorage.key(index)
    ~~~

    

* WebWorker

  > HTML在执行脚本时，页面状态不可相应，直到脚本执行结束
  >
  > webworker是运行在后台的js，独立于其它脚本，不影响页面性能

  

  脚本定义

  ~~~js
  // workers.js
  let num = 0
  if(num <10){
      return data
      num ++
  }
  
  
  ~~~

  

  ~~~html
  <!DOCTYPE html>
  <html>
  <head> 
  <meta charset="utf-8"> 
  </head>
  <body>
  
  <p>计数： <output id="result"></output></p>
  <button onclick="startWorker()">开始工作</button> 
  <button onclick="stopWorker()">停止工作</button>
  
  <script>
  var w;
  
  function startWorker() {
      if(typeof(Worker) !== "undefined") {
          if(typeof(w) == "undefined") {
              w = new Worker("workers.js");
          }
          w.onmessage = function(event) {
              console.log(event.data)
          };
      } else {
          alert("抱歉，你的浏览器不支持 Web Workers...")
      }
  }
  
  function stopWorker() 
  { 
      w.terminate();
      w = undefined;
  }
  </script>
  
  </body>
  </html>
  ~~~

  

* WebSocket

> 1. WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
>
> 2. WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。
> 3. 在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。
>
> > 现在，很多网站为了实现推送技术，所用的技术都是 Ajax 轮询。轮询是在特定的的时间间隔（如每1秒），由浏览器对服务器发出HTTP请求，然后由服务器返回最新的数据给客户端的浏览器。这种传统的模式带来很明显的缺点，即浏览器需要不断的向服务器发出请求，然而HTTP请求可能包含较长的头部，其中真正有效的数据可能只是很小的一部分，显然这样会浪费很多的带宽等资源。

![img](https://www.runoob.com/wp-content/uploads/2016/03/ws.png)

- Web Socket属性

  | 属性                  | 方法                                                         |
  | --------------------- | ------------------------------------------------------------ |
  | Socket.readyState     | 只读属性 **readyState** 表示连接状态，可以是以下值：<br />0 - 表示连接尚未建立。<br />1 - 表示连接已建立，可以进行通信。<br />2 - 表示连接正在进行关闭<br />3 - 表示连接已经关闭或者连接不能打开。 |
  | Socket.bufferedAmount | 只读属性 **bufferedAmount** 已被 send() 放入正在队列中等待传输，但是还没有发出的 UTF-8 文本字节数。 |

- web Socket事件

  | 事件    | API              | 描述                       |
  | ------- | ---------------- | -------------------------- |
  | open    | Socket.onopen    | 连接建立时触发             |
  | message | Socket.onmessage | 客户端接收服务端数据时触发 |
  | error   | Socket.onerror   | 通信发生错误时触发         |
  | close   | Socket.onclose   | 连接关闭时触发             |

- Web Socket方法

  | 方法           | 描述             |
  | -------------- | ---------------- |
  | Socket.send()  | 使用连接发送数据 |
  | Socket.close() | 关闭连接         |

- 实例

  ~~~html
  <!DOCTYPE HTML>
  <html>
     <head>
     <meta charset="utf-8">
     <title>菜鸟教程(runoob.com)</title>
      
        <script type="text/javascript">
           function WebSocketTest()
           {
              if ("WebSocket" in window)
              {
                 alert("您的浏览器支持 WebSocket!");
                 
                 // 打开一个 web socket
                 var ws = new WebSocket("ws://localhost:9998/echo");
                  
                 ws.onopen = function()
                 {
                    // Web Socket 已连接上，使用 send() 方法发送数据
                    ws.send("发送数据");
                    alert("数据发送中...");
                 };
                  
                 ws.onmessage = function (evt) 
                 { 
                    var received_msg = evt.data;
                    alert("数据已接收...");
                 };
                  
                 ws.onclose = function()
                 { 
                    // 关闭 websocket
                    alert("连接已关闭..."); 
                 };
              }
              
              else
              {
                 // 浏览器不支持 WebSocket
                 alert("您的浏览器不支持 WebSocket!");
              }
           }
        </script>
         
     </head>
     <body>
        <div id="sse">
           <a href="javascript:WebSocketTest()">运行 WebSocket</a>
        </div>
     </body>
  </html>
  ~~~

  

#### mate标签

#### Iframe
