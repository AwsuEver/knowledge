### typescript基础知识

#### 环境搭建

1. 全局搭建typescript

   ~~~bash
   npm install -g typescript
   ~~~

2. 创建一个ts文件

3. 编译

   * 执行命令

     ~~~bash
     tsc xxx.ts
     ~~~

   * 编译生成生成js文件

#### 类型声明

##### 类型申明

* 类型声明是ts非常重要的特点
* 通过类型声明可以指定ts中的变量（参数、形参）的类型
* 指定类型后，当为变量赋值时，TS编译器就会自动检查值是否符合类型声明
* 简而言之，类型声明就是给变量设置类型，使得变量只能存储某种类型的值

语法如下：

~~~typescript
let 变量： 类型
let 变量: 类型1 | 类型2   // 或
let 变量: 类型1 & 类型2
let 变量： 类型 = 值

function fn(参数: 类型, 参数: 类型): 类型{
    // 该函数定义了参数类型、函数返回值类型
}
~~~

##### 自动类型判断

* TS拥有自动的类型判断机制
* 当对变量的声明和赋值同时进行时，TS编译器会自动判断变量的类型
* 因此如果变量声明和赋值同时进行，可以省略掉变量声明

##### 类型

**基本类型**

| 类型    | 声明方式                   | 描述                               | 示例                                                   |
| ------- | -------------------------- | ---------------------------------- | ------------------------------------------------------ |
| number  | `let num: number = 100`    | 数值型，任意数字                   | 1、-3、，25                                            |
| string  | `let str: string = 'hi'`   | 任意字符串                         | 'hi',、"hi"、hi                                        |
| boolean | `let bool: boolean = true` | 布尔值，true或false                | true、false                                            |
| any     | *                          | 任意类型，不希望某个值导致类型错误 | *                                                      |
| unknow  | *                          | 类型安全的any                      | *                                                      |
| void    | `function fn(): void{}`    | 以函数为例，表示返回值为空         | *                                                      |
| never   | `function fn(): never{}`   | 表示不会有返回结果                 | function fn(): never{<br />thrrow new Error('报错了')} |

**对象**

~~~typescript
// 属性个数确定的对象声明
let obj = {
    name: string,
	age: number
}

// 可选属性的对象声明
let obj = {
    name: string,   // name属性为必填属性
    age?: number    // age属性可用可不用，不会报错
}

// 属性个数不确定的对象声明
let obj = {
    name: string,
    [paramsName: string]: any     // 表示非必填属性的属性名类型为字符串，属性值为任意值
}
~~~

**函数**

~~~typescript
// 参数个数确定的函数声明及使用
let func = (a: nunmber, b: number) => number     // 声明
func = function(age1, age2): number{             // 使用
    return age1 + age2
}

~~~

**数组**

~~~typescript
// 方式一
let arr: string[]

// 方式二
let arr: Array<number>
~~~

**元组**

~~~typescript
// 元组：就是固定长度的数组
// 方式一
let arr: [string, number]    // 定义长度为2,类型依次为string、number的数组
arr = ['Tom', 18]
~~~

**枚举**

~~~typescript
// 对于常用的类型，可以通过枚举的方式先定义好
enum VersionTypeEnum{    // 定义
    versionDate = 0,
    urgent = 1,
    quality = 2,  
}

// 使用
let obj = {system: string, versionType: VersionTypeEnum}
obj = {
    system: 'N-OPMS',
    versionType: VersionTypeEnum.versionDate
}

~~~

**类型别名**

~~~typescript
let myType = 1 | 2 | 3 | 4 | 5

let a = myType
~~~

