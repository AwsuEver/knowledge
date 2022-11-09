### 基本概念

#### 操作符

##### 一元操作符

> 只能操作一个值的操作符叫做一元操作符

- 递增和递减操作符

  - 前置型

    ~~~js
    // 示例1
    var age = 20
    ++age   // 等同于 age = age + 1   age=21
    
    // 示例2
    var age = 20
    var anotherAge = ++age + 4     // 前置型操作符在执行递增或递减时，变量值在包含他们的语句被计算前操作的，这里先计算 ++age 再计算 （++age）+4
    console.log(age)   // 21       // 执行前置操作时，已经改变了age的值
    console.log(anotherAge)  // 25
    ~~~

  - 后置型

    ~~~js
    // 实例1
    var age = 20
    age--     // age = 19
    
    // 示例2
    var age1 = 2
    var age2 = 20
    var age3 = age1-- + age2    // 22，后置型操作符在递增或递减时，变量值在包含他们的语句被计算后操作的，这里先计算 age1 + age2，再计算age1-1
    var age4 = age1 + age2      // 21，age1已经被减去1， 此时为1+20 = 21
    ~~~

- 一元加和减操作符



