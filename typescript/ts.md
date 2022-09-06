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
let obj: {
    name: string,
	age: number
}

// 可选属性的对象声明
let obj: {
    name: string,   // name属性为必填属性
    age?: number    // age属性可用可不用，不会报错
}

// 属性个数不确定的对象声明
let obj: {
    name: string,
    [paramsName: string]: any     // 表示非必填属性的属性名类型为字符串，属性值为任意值
}
~~~

**函数**

~~~typescript
// 参数个数确定的函数声明及使用
let func: (a: nunmber, b: number) => number     // 声明
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

**接口**

~~~typescript
// 只能为对象指定类型
interface Animal{
    // 如果换行的话，不需要加分号；不换行需要分号
    name: string
    asyHi(): void
}

// 接口继承
interface Dog extends Animal{
    age: number
}

// 使用
let dog: Dog ={
    name: '旺财',
    age: 2,
    sayHi: () => {
        console.log('wang...')
    }
}
~~~

**类型别名**

~~~typescript
// 定义
type CustomArray = (string | number)[]

// 使用
let arr: CustomArray = [1,2,3, 'a', 'b']
~~~

**类型断言**

 ~~~typescript
 // 断言即可以手动一个类型，允许变量从一个类型变成另一个类型
 let obj: {name: stting, age: number}
 // 方式一
 let obj1 as obj
 // 方式二
 let <obj>obj2
 
 ~~~

**typeof**

~~~typescript
/**
	使用场景： 1. 可以像js中的typeof一样使用，用于判断元素类型
	2. 可以在类型上下文中引用变量或属性的类型
*/
// 场景一
typeof 'aaa'   // string

// 场景二
let p = {x: 1, y: 2}

function point(poing: {x: number, y: number}){}
// 可以改为下面这种方式
function point(poing: typeof p){}
~~~



#### 面向对象

**基本使用**

~~~typescript
// 创建一个对象并使用
class Person{
    name: string
    age: number
    gender= 'male'
    
    // 构造函数
    constructor(name: string, age: number){
        this.name = name
        this.age = age
    }
    
    // 对象函数
    sayHi(content: string): void{
        console.log('hello', contnt)
    }
}

// 实例化对象
let p = new Person('Bob', 18)
p.sayHi("大家好！")
~~~

**继承**

- 使用extends继承

  ~~~typescript
  class Animal {
      name: string
      age: number
  }
  
  class Dog extends Animal{
      
      constructor(name: string, age: number){
          this.name = name
          this.age = age
      }
      
      bark(){
          console.log("汪汪汪！")
      }
      
  }
  
  let d = new Dog('小白'， 3)
  ~~~

- 实现implements接口

  ~~~tsx
  interface Animal{    // 定义一个接口
      bark(): void
      name: string
      age: number
  }
  
  class Dog implements Animal{     // 通过implements实现接口
      name = '小白'
      age = 3
      bark(){
          console.log('汪汪汪！')
      }
  }
  ~~~

- 修饰符

  - public

    ~~~tsx
    // public: 表示公有的，公有成员可以被任何地方访问
    class Animal{
        public bark(){
            console.log('bark')
        }
    }
    let a = new Animal()
    a.bark  // bark
    
    class Dog extends Animal{
        name = '小白'
    }
    
    let d = new Dog()
    d.name   // 小白
    d.bark()  // bark
    ~~~

  - protected

    ~~~tsx
    // protected: 表示受保护的，仅对其声明所在的类和子类中使用，实例对象不可使用
    class Animal{
        bark(){
            console.log('bark')
            this.move()
        }
        
        protected move(){
            console.log("走一走")
        }
    }
    let a = new Animal()
    a.bark()  // bark
    a.move()  // 报错
    
    class Dog extends Animal{
        name = '小白'
        
        dogMove(){
            this.move()
        }
    }
    
    let d = new Dog()
    d.name       // 小白
    d.dogMove()  // 走一走
    d.move()     // 报错
    
    ~~~

  - private

    ~~~tsx
    // private：表示私有的，只在当前类中可见，对实例对象及其子类不可见
    class Animal{
        private move(){
            console.log('走一走')
        }
        
        bark(){
            console.log('bark')
            this.move()
        }
    }
    let a = new Animal()
    a.move()   // 报错
    a.bark()   // bark    走一走
    
    class Dog extends Animal{
        run(){
          	console.log('run')  
            
            this.move()  //报错
        }
    }
    let d = new Dog()
    d.run()   // run
    d.bark()  // bark    走一走
    d.move()  // 报错
    ~~~

  - readonly

    ~~~tsx
    // readonly: 表示只读，防止在构造函数之外对属性赋值，只能修饰属性不能修饰方法
    class Person {
        readonly age: number
        
       	// readonly使用时，必须手动指明类型
        readonly name = '小白'   // 未指明name的类型，默认name类型为：小白
        
        constructor(age: number){
            this.age = age
        }
        
        setAge(){
            this.age = 18     // 报错，不能在构造函数之外修饰只读属性
        }
        
        readonly setName(){   // 报错，不能修饰方法
            
        }
    }
    ~~~



#### 类型兼容性

**基本概念**

> TS采用的是结构化类型系统，也叫鸭子类型，类型检查关注的是值所具有的形状。
>
> 也就是说，在结构类型系统中，如果两个对象具有相同的形状，则认为是同一类型
>
> 例如：
>
> ~~~tsx
> class Point { x: number, y: number}
> class Point2D { x: number, y: number}
> 
> const p: Point = new Point2D()
> 
> /**
> 	说明：
> 	1. Point和Point2D是名称不同的类
> 	2. 变量P的类型被显式标注为Point类型，但它的值确实Point2D的实例，并且没有类型错误
> 	3. TS是结构化类型系统，只检查Point和Point2D的结构是否相同
> 	4. 在C、Java中，他们是不同的类，无法兼容
> */
> ~~~
>
> 
>
> 更准确的说：对于对象类型来说，对象X的成员至少与对象Y的成员相同，则Y兼容X（成员多的可以赋值给少的）
>
> 例如：
>
> ~~~tsx
> class Point2D { x: number, y: number}
> class Point3D { x: number, y: number, z: number}
> 
> const p: Point2D = new Point3D()
> 
> /**
> 	说明：
> 	1. Point3D的成员至少与Point相同，则Point兼容Point3D
> 	2. 所以，成员多的Point3D可以赋值给成员少的Point
> */
> ~~~
>
> 

**对象之间的类型兼容**

~~~tsx
class Point { x: number, y: number}
class Point2D { x: number, y: number}
class Point3D { x: number, y: number, z: number}

const p: Point = new Point2D()
const p1: Point2D = new Point3D()
~~~

**接口之间的类型兼容**

~~~tsx
interface Point { x: number, y: number}
interface Point2D { x: number, y: number}
let p1: Point
let P2: Point2D
p2 = p1

interface Point3D { x: number, y: number, z: number}
let p3: Point3D
p2 = p3 
~~~

**函数之间的类型兼容**

- 参数个数

  参数多的兼容参数少的（参数少的可以赋值给参数多的）

  ~~~tsx
  type F1 = (a: number) => void
  type F2 = (a: number, b: number) => void
  
  let f1: F1
  let f2: F2
  
  f2 = f1
  ~~~

- 参数类型

  相同位置的参数类型要相同（原始类型）或兼容（对象类型）

  - 原始类型

    ~~~tsx
    // 函数类型F2兼容F1，因为F1和F2的第一个参数类型相同
    type F1 = (a: number) => void
    type F2 = (a: number) => void
    
    let f1: F1
    let f2: F2
    
    f2 = f1
    ~~~

  - 对象类型

    ~~~tsx
    // 参数少的可以赋值给参数多的
    class Point2D { x: number, y: number}
    class Point3D { x: number, y: number, z: number}
    
    type F1 = (p: Point2D) => void
    type F2 = (p: Point3D) => void
    
    let f1: F1
    let f2: F2
    
    f2 = f1
    ~~~

- 返回值类型

  只需关注返回值类型本身即可

  - 原始类型

    ~~~tsx
    // 返回值为原始类型，此时返回值类型要相同
    type F1 = () => number
    type F2 = () => number
    
    let f1: F1
    let f2: F2
    
    f2 = f1
    ~~~

  - 对象类型

    ~~~tsx
    // 返回值为对象，此时成员多的可以赋值给成员少的
    type F1 = () => {name: string}
    type F2 = () => {name: string, age: number}
    
    let f1: F1
    let f2: F2
    
    f1 = f2
    ~~~




#### 交叉类型

> 交叉类型： 功能类似于接口继承，用于将多个类型组合为一个类型
>
> 说明：使用交叉类型后，新的类型就具备了原有多个类型的属性

~~~tsx
interface Person {
    name: string
}
interface Contact {
    phone: number
}
type PersonDetail = Person & Contact

let p: PersonDetail = {
    name: 'Bob',
    phone: 123456
}
~~~



#### 范型

> 泛型：可以保证在类型安全的前提下，让函数等与多种类型一起工作，从而实现复用，常用于：函数、接口、class中

- **创建泛型函数**

  ~~~tsx
  function id<Type>(value: Type): Type{
      return value
  }
  
  /**
  	说明：
  		1. 语法：在函数名称后面添加<>，尖括号中添加类型变量，比如此处的Type
  		2. 类型变量Type，是一种特殊的变量，它用于处理类型而不是值
  		3. 该类型变量相当于一个类型容器，能够捕获用户提供的类型（类型由用户调用时指定）
  		4. 因为Type是类型，可以将其作为函数参数及返回值的类型
  		5. 类型变量Type，可以是任意合法的变量名
  */
  ~~~

- **使用泛型函数**

  ~~~tsx
  function id<Type>(value: Type): Type{
      return value
  }
  
  const num = id<number>(10)    // 10
  
  /**
  	说明：
  	1. 语法：语法：在函数名称后面添加<>，尖括号中添加类型变量，比如此处的number
  	2. 传入类型number后，这个类型会被声明函数中的Type捕获到
  	3. 此时，Type的类型就是number，形参和函数返回值类型都是number
  */
  ~~~

- **简化使用**

  ~~~tsx
  function id<Type>(value: Type): Type{
      return value
  }
  
  const num = id<number>(10)
  const num1 = id(10)   // 可以通过省略<类型>的方式进行调用，编译器会自动推断出变量Type的类型
  ~~~

- **泛型约束**

  > 默认情况下，泛型函数的类型变量Type可以代表多个类型，这导致无法访问任何属性
  >
  > 说明：Type可以表示任意类型，但无法保证一定存在某些属性
  >
  > 此时，需要为泛型添加约束，来收缩类型

- 
