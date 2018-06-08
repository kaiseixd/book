# 1
## Unary operators

- typeof

## Boolean values
### Comparisons 

- 字符串比较是在比较 Unicode 码（including nonalphabetic characters）
- 只有 NaN 不等于它自己

## undefined / null values *

待补充

## Automatic type conversion *

- 8 * null -> 0
- 只有 undefined 和 null == undefined 或者 null（所以也 != boolean values）
- undefined 或 null == true 或 false 的结果都是 false
- undefined === null -> false
- 0, NaN and "" count as false

## short-circuiting of logical operators

- || operator 常用于设置默认值
- 三元运算符同样也会短路后面的表达式

# 2 Program Structure
## Expressions and statements * + ?

- expression：
    - a fragment of code that produces a value
    - every value that is written literally（->常量）
    - an expression between parentheses（小括号）

- a binary operator applied to two expressions

- statement：以分号结尾（分隔）

> 表达式有值而语句不总有

## Variables

- 变量名中可以用的符号：$ and _

## Keywords and reserved words（保留字）

.

## The environment（执行环境）*

- 所有的变量及它们的值组成了当前程序的运行环境
- 包含语言标准提供的变量以及用于和 surrounding system （比如浏览器）交互的方法
    - 函数内的环境里会有这些方法和变量吗?

## Functions

- 形参实参?

# 3 Functions
## Parameters and scopes *

- 参数和正常的变量很像，但是它的初始值是由 caller（函数的调用者）提供的
- 定义在任意函数外的变量（使用 var）称为全局变量 / functions are the only things to create a new scope（let 也行）/ let create a variable that is local to the enclosing block
- 如果在函数内定义了一个和全局变量具有相同名字的变量，会将其屏蔽

## Nested scope（嵌套作用域）

- 局部作用域可以看到包含它的所有上层作用域
- lexical scoping（词法作用域）：approach to variable visibility ==?==

## Functions as values（函数作为值）

- Function variables 一般只是简单地作为一段代码的名字
- 但是也能像其他变量一样作为参数传入函数，还能重新赋值

## Declaration notation

- function declarations are moved to the top of their scope

## The call stack（调用栈）*

- Because a function has to jump back to the place of the call when it
returns, the computer must remember the context from which the function was called
    - 计算机存储这些上下文的地方就叫调用栈
    - 每当函数调用时，当前上下文会被推至栈顶，函数结束时移除
    - 存储这样的上下文需要内存空间，如果太大会 blow the stack

## Optional Arguments

- 传太多会无视，传太少会变成 undefined
- arguments

## Closure *

- 函数运行结束后里面的局部变量去哪了?
- This feature — being able to reference a specifc instance of local variables in an enclosing function — is called closure

## Recursion（递归）

- 调用自己的函数
- 这种方法更优雅但是比正常的循环要慢（更高的复杂度）
- 切勿过早优化
- 处理多分支问题时递归会很高效 ==?==

## Functions and side effects（函数与副作用）

- 函数可以被粗略地分为两类，一种是为了它的副作用，一种是为了它的返回值，当然也可以 both
- A pure function is a specifc kind of value-producing function that not
only has no side eﬀects but also doesn’t rely on side eﬀects from other
code
    - 纯函数是一种仅返回值的函数，没有副作用而且也不依赖于其他代码的副作用
    - 传入相同的参数，总是返回相同的值（易于测试）

# 4 Data Structures: Objects and Arrays （数据结构！）
## Data sets（数据集）

- -> array
- The elements in an array are stored in properties
- length 属性只计算其中的 elements

## Properties

- 几乎所有的 js 对象都有属性，除了 null 和 undefined
- value.x 中的 x 必须是合法的变量名，x 直接指明了属性名（但是属性名可以是任意字符串）
- value[x] 中的 x 会被当做表达式来计算，可以是变量也可以是任意字符串

## Methods

- Properties that contain functions are generally called methods 

## Objects

- Values of the type object are arbitrary collections of properties
- 二元运算符 in
- 一元运算符 delete

## Mutability（修改）

- numbers, strings and booleans 都是不可变数据，无法改变，只能重新赋另一个值
- 对象的值是可以改变的
- 比较对象时只有在两个对象指向同一个对象时才会返回 true

- 拟合度 ==?==

## Objects as maps（把对象作为一种映射关系）*

- 将对象用于存储（相比于数组，找出某值更方便）
- 由于对象的属性并不是有序排列的，不能用 for 循环将其遍历出来
- 可以使用 for in, for of
- 以及…… ==?==

## Strings and their properties *

- 可以从字符串上读取属性，但是不能添加属性（number 和 Boolean 也是）
- 原始类型并没有内置属性，是从包装对象上来的

## The arguments object

- 调用函数时 arguments 会被添加到函数内部（to the environment）
- 是一个包含所有接收到的参数的 "类数组"
- does not have any array methods

## The global object

- The global scope can also be approached as an object: window
- Each global variable is present as a property of this object（所有全局变量都是 window 对象的属性）

## A list（链表）*

- A list is a nested set of objects, with the first object holding a reference to the second, the second to the third, and so on

```
var list = {
    value: 1,
    rest: {
        value: 2,
        rest: {
            value: 3,
            rest: null
        }
    }
}
```

# 5 Higher-Order Functions（高阶函数）
## Abstraction（抽象）

- hide details and give us the ability to talk about problems at a higher level

## Abstracting array traversal（抽象数组遍历）

- do something with each element in an array

```
function forEach(array, action) {
    for (var i = 0; i < arr.length; i++) {
        action(array[i]);
    }
}
```

```
// 数组对象内置的 forEach 方法：array.forEach(function)
// function 的参数就是 array[i]
// 有点类似 map
// 注意！！没有 return ！！
```

## Higher-order functions *

- Functions that operate on other functions, either by taking them as arguments or by returning them, are called higher-order functions

## Passing along arguments

- 为什么不能使用 arguments 参数?
- apply

## JSON

- 所有属性名必须被双引号包裹
- 只允许 simple data expressions（直接量）
- JSON.stringify / JSON.parse

## Filtering an array

- build a new array with some elements filtered out

```
// 通过能返回 true 的 array[i]
function filter(array, test) {
    var passed = []
    for (var i = 0; i < array.length; i++) {
        if (test(array[i]) {
            passed.push(array[i])
        }
    }
    return passed
}
```

```
// 数组内置方法 filter：array.filter(function)
// function 的参数就是 array[i]
```

## Transforming with map

- to build a new array where each element has been put through a function

```
function map(array, transform) {
    var mapped = []
    for (var i = 0; i < array.length; i++) {
        mapped.push(transform(array[i]))
    }
    return mapped
}

// 同样也是数组内置方法
```

## Summarizing with reduce

- combine all ann array's elements into a **single** value

```
function reduce(array, combine, start) {
    var current = start
    for (var i = 0; i < array.length; i++) {
        current = combine(current, array[i)
    }
    return current
}

// 数组内置方法 reduce 的 start 参数默认值是 array[0]
// a = function(a, b), a 是要被 return 的 sum, b 是 array[i]
```

## The cost

- 上述这些处理数组的方法组合使用起来可以使代码很优雅，但是也有低效的缺点
- 建立过渡数组是很昂贵的
- 抽象会带来更多代码上的工作
- 多重嵌套循环会带来 M * N 的 复杂度

## family tree ==?==

.

## Binding

- apply: call functions with an array specifying their arguments
- bind: create a partially applied version of the function
- call: 类似 apply 但是需要一个个传入参数而不是数组

# 6 The Secret Life of Objects
## Methods

- 方法是指向了函数的对象属性
- 方法中的 this 指向方法被调用时所属的对象

## Prototypes *

- A prototype is a object that is used as a fallback source of properties
- 访问一个对象上不存在的属性时，就会去它的 prototype 上找（通过 __proto__）
- Object.getPrototypeOf()
- Object.create(): 创建一个被指定了特定原型的对象
    - 创建的对象的 prototype 为什么和原型的 prototype 一样 ==?==

## Constructors *

- A more convenient way to create objects that derive from some shared prototype is to use constructor
- 使用 new 关键字来调用
- 构造函数会自动获得 prototype 属性，衍生自 Object.prototype，作为其实例的原型对象（prototype）

## Overriding derived properties（对衍生属性的覆盖）

- 对象中的属性会屏蔽原型中的属性
- Object.prototype.toString

## Prototype interference（打断）*

- for in 循环会把对象所有可枚举属性迭代出来
- obj.hasOwnProperty tell us whether the object itself has the property（而不包括 prototype 中的属性）
    - 对不可枚举的属性有用吗 ==?==
    - 所以可以在 for in 循环中加一层 obj.hasOwnProperty 判断
- 直接赋值（simply assigning）到对象上的属性都是可枚举的
- standard properties in Object.prototype are all nonenumerable（因此不会被 for in 枚举出来）
- 通过 Object.defineProperty 来定义属性，可以控制属性的类型（是否可枚举等等……）
    - 只能给 prototype 添加属性吗 ==?==

## Prototype-less objects

- 可以通过 Object.create(null) 来创建没有原型（__proto__）的对象
- Object.setPrototype(obj, null) ==?==

## Polymorphism（多态）

- 输入不同的值有不同的结果?有不同的处理方法?

## Laying out a table

==?==

## Getters and setters *

- 接口可以仅用属性而不用方法
- 在对象直接量写法中，属性名前面加上 get 和 set 两个标志可以让你在读取和修改一个属性时运行一个函数
- 也可以给已经存在的对象添加 getter 和 setter（Object.defineProperty）
- get：将对象属性绑定到查询该属性时将被调用的函数

## Inheritance

...

# 7 Electronic Life



# 8 Bugs and Error Handling
## Testing

- 测试框架：mocha tape jasmine

## Exceptions

- throw
- try / catch

...

## cleaning

- try / finally

## assert

...

# 9 Regular Expressions












