#### Less

##### 变量

~~~less
@width: 100px;
@height: @width+100px;
@primaryColor: rgb(33,65,172);

.className: {
    width: @width;
    height: @height;
}
~~~

编译结果:

~~~css
.className: {
    width: 200px;
    height: 200px;
}
~~~

##### 混合

~~~less
.button{
    width: @width;
    height: 30px;
    border-radius: 5px;
}
.button1{
    .button();
    background: @primaryColor;
    cursor: pointer;
}
~~~

编译结果

~~~css
.button1{
    width: 100px;
    height: 30px;
    border-radius: 5px;
    background: rgb(33,65,172);
    cursor: pointer;
}
~~~

##### 嵌套

~~~less
.header{
    height: @height;
    background: green;
    .logo{
        width: 50px;
        height: 50px;
    }
    .navigation{
        width: 100%;
        height: @height - 300px;
    }
}
~~~

编译后

~~~css
.header{
    height: @height;
    background: green;
}
.header .logo{
    width: 50px;
    height: 50px;
}
.header .navigation{
    width: 100%;
    height: @height - 300px;
}
~~~

##### 运算

> 算术运算符`+`、`-`、`*`、`/`可以对任何数字、颜色或变量进行运算。如果需要，算数运算符在`+`、`-`运算之前会对单位进行换算。计算结果以最左侧的单位为准，如果单位换算失去意义，则忽略单位。

~~~:
@base_1: 5cm + 10m;          // 6cm
@base_2: 10 - 3cm - 10mm;    // 6cm
@base_3: 2 + 5cm - 3px;      // 4cm 单位换算失去意义

@base: 5%;
@filter: @base * 2           // 10%
@other: @base + @filter      // 15%
~~~

##### 转义

> 任何 ~”anthing“ 形式的内容都会原样输出

~~~less
@min768: ~"(min-width: 768px)";
.element {
  @media @min768 {
    font-size: 1.2rem;
  }
}
// 或者less3.5+可以这样写
@min768: (min-width: 768px);
.element {
  @media @min768 {
    font-size: 1.2rem;
  }
}
~~~

编译后

~~~less
@media (min-width: 768px) {
  .element {
    font-size: 1.2rem;
  }
}
~~~

##### 命名空间和访问符

> 主要实现对样式的分组操作和按需使用的需求

~~~less
.color{
    background-color: green;
    .color1{
        background-color: orange;
    }
}
.button1{
    .color.color1();
    cursor: pointer;
}
~~~

编译后

~~~css
.button1{
    background-color: orange;
    cursor: pointer;
}
~~~

##### 映射

~~~less
#colors() {
  primary: blue;
  secondary: green;
}

.button {
  color: #colors[primary];
  border: 1px solid #colors[secondary];
}
~~~

编译后

~~~css
.button {
  color: blue;
  border: 1px solid green;
}
~~~

##### 作用域

~~~less
@var: red;

#page {
  @var: white;
  #header {
    color: @var; // white
  }
}
~~~

##### 导入

> 可以导入一个 .less 文件，此文件中的所有变量就可以全部使用了。如果导入的文件是 .less 扩展名，则可以将扩展名省略掉

~~~less
@import "library"; // library.less
@import "typo.css";
~~~

