# Typescript

## 概念

Typescript（TS）是 Javascript 的超集，Typescript = Type + Javascript，即在 JS 的基础上，增加了类型支持，例如：

```tsx
// ts 声明方式
let age1: number = 18
// js 声明方式
let age2 = 18
```

## 增加类型支持

JS 编写代码中出现的大部分错误都是类型错误 （UncaughtTypeError），这样的错误不利于解决 bug，影响开发效率

从编程语言的类型区分，TS 属于静态编程语言， JS属于动态编程语言，其中静态语言在编译期进行类型检查，动态语言在执行期进行类型检查

因此，JS在代码真正执行的时候才能发现错误（晚），而TS在代码编译的时候就可以发现错误（早），TS可以在编写代码的同时就可以发现代码中的错误，从而解决bug更有效率

## TS的优势

- 更早发现错误
- 程序任何位置都有代码提示
- 强大的类型系统提升系统的可维护性，使得重构代码更加容易
- 支持最新的 ECMAScript 语法
- TS类型推断机制，**不需要再代码中每个地方都显示标注类型**

此外，目前主流框架 Vue3 使用TS重写，React与TS完美配合，TS已经成为大中型前端项目的首选语言

## TS编译工具包

TS编译工具包将TS代码转化为JS代码，

![image-20221029101043592](Typescript.assets/image-20221029101043592.png)

安装：

```
npm i -g typescript
// 验证
tsc -V
```

## 编译运行TS代码

1. 创建`xxx.ts`文件
2. 将TS文件编译为JS文件：`tsc xxx.ts`，编译结束后会生成一个同名的JS文件
3. 运行JS文件：`node xxx.js`

## 简化运行ts

使用`ts-node`包，安装：

```
npm i -g ts-node
```

使用方式：

```
ts-node test.ts
```

`ts-node`命令会自动将 ts 文件先转化为 js 文件，然后再运行 js 代码，注意此命令不会实际生成 js 文件，而是内部转化后立即执行

## TS 常用类型

### 类型注解

例如 `let age:number = 18`，其中使用 `:number`即是类型注解，其作用在于为变量添加类型约束，

### JS与TS类型

JS已有类型：

- 原始类型：number/string/boolean/null/undefined/synbol
- 对象类型：object（数组、对象、函数）

TS新增类型：

- 联合类型
- 自定义类型（类型别名）
- 接口
- 元组
- 字面量类型
- 枚举
- void
- any

### 原始类型

例如：

```tsx
let age: number = 19
let myName: string = 'ssss'
let isLoading: boolean = true

let a:null = null
let b:undefined = undefined
let s:symbol = Symbol()
```

### 对象类型 object

对象类型再TS中更加细化，每种对象（数组、对象、函数）都有自己的类型语法

#### 数组类型

数组类型的两种写法：

```tsx
let numbers: number[] = [1, 2, 3, 4]  // 推荐写法 格式：类型[]
let strings: Array<string> = ['a', 'b']
```

如果数组中元素的类型多于一种，需要使用联合类型定义：

```tsx
let arr: (number | string | boolean)[] = [1, 'a', true]
```

使用`|`分隔多个类型，表示可以是这些类型中的任意一种

#### 类型别名（自定义类型）

如果一个定义较为复杂的类型被多次使用，为了简化写法，可以使用类型别名方式简化使用：

```tsx
type CustomArray = (number | string)[]
let arr1: CustomArray = [1, 2, 'a']
```

格式：

- 类型别名通过 type 关键字创建
- 类型别名遵循变量名命名规则
- 创建类型别名后，直接使用该类型别名作为变量的类型注解即可

#### 函数类型

函数类型需要为函数参数和返回值指定类型，有三种指定方式：

- 单独指定参数、返回值类型：

  ```tsx
  // 函数声明方式
  function add(number1: number, number2: number): number {
    return number1 + number2
  }
  // 函数表达式方式
  const add1 = (number1: number, number2: number): number => {
    return number1 + number2
  }
  ```

- 同时指定参数和返回值类型：

  ```tsx
  const add2: (num1: number, num2: number) => number = (num1, num2) => {
    return num1 + num2
  }
  ```

  这种方式只适用于函数表达式方式，表达式前半部分相当于一个模板，指定参数和返回值类型

如果函数不需要返回值，需要为函数指定`void`类型：

```tsx
function greet(name: string): void{
  console.log('hello '+ name)
}
```

**可选参数：**如果某个参数可传入也可以不传入，需要为此参数指定可选，使用格式为再可选参数后面添加`?`：

```tsx
function testChoose(start?: number, end?: number): void {
  console.log('param1: ', start, 'param2: ', end)
}
testChoose(1, 2)
testChoose(1)
testChoose()
```

输出的结果为：

```
param1:  1 param2:  2
param1:  1 param2:  undefined
param1:  undefined param2:  undefined
```

**注意可选参数必须再必选参数的后面**

### 对象类型

TS中对象的类型就是在描述对象的结构，例如：

```tsx
let person: { name: string; age: number; sayHi(): void } = {
  name: 'zhangsan',
  age: 12,
  sayHi() {
    console.log('hi')
  },
}
```

其格式为：

- 使用`{}`描述对象结构，属性应采用属性名：类型的形式，方法采用方法名()：返回值类型的形式

- 如果方法有参数，采用方法参数的定义方式，`func(param: string):void`

- 在一行代码中指定对象的多个属性的时候，使用分号进行分隔 

- 如果一行代码只指定一个属性类型，可以省略分号

  ```tsx
  let person2: {
    name: string
    age: number
  } = {
    name: 'lisi',
    age: 12,
  }
  ```

- 方法的定义可以使用箭头函数，例如：

  ```tsx
  let person3: { testfunc: (name: string) => void }
  let person4: { testfunc(name: string): void }
  ```

**对象可选属性：**在某个属性后面添加`:`表示可选，例如：

```tsx
function myAxios(config: { url: string; methods?: string }) {
  console.log(config)
}
```

### 接口

如果对象类型被多次使用，一般使用接口（interface）来简化写法，例如：

```tsx
interface IPerson {
  name: string
  age: number
  sayHi(): void
}

let person5: IPerson = {
  name: 'zhangsan',
  age: 12,
  sayHi() {},
}
```

格式：

- 使用 interface 关键字
- 接口名称可以是任意合法的变量名
- 声明接口后，直接使用接口作为变量的类型

### 接口与类型别名

相同点：都可以为对象指定类型，只是定义格式稍有不同，使用 interface 无需`=`:

```tsx
// 通过类型别名指定对象类型
type IPerson = {
  name: string
  age: number
  print(): void
}

// Interface方式指定对象类型
interface IPerson2 {
  name: string
  age: number
  print(): void
}
```

不同点：

- 接口只能为对象指定类型
- 类型别名则可以为任意类型指定别名

### 接口的继承

接口之间如果存在相同的属性或者方法，可以考虑抽离这些属性方法，通过继承进行复用，例如：

```tsx
interface Point2D {
  x: number
  y: number
}
interface Point3D extends Point2D {
  z: number
}

let p: Point3D = {
  x: 1,
  y: 2,
  z: 3,
}
```

### 元组

元组是特殊定义的数组，用于定义固定数量、固定类型的数组，例如，使用一个数组表示经纬度位置的时候，该数组应该指定长度为2，并且为数字类型，因此更适合使用元组进行表示，元组定义格式为：

```
let position: [number, number] = [12, 32]
```

## 类型推论

TS 的类型推论机制会帮助提供类型，发生类型推论的场景为：

- 声明变量并**初始化**

  ```tsx
  let age = 18
  age = 'str'  // 此时报错 age 已经分配为 number 类型
  ```

- 决定函数返回值

  ```tsx
  function func2(){
      let a = 1
      let b = 2
      return a+b
  }
  // 此时 func2 的返回类型自动定义为 number
  ```

## 类型断言

使用JS/TS控制HTML元素的时候，使用`getElementById`方法返回的结果是一个宽泛的`HTMLElement`元素，该类型只包含标签的公共属性，不包含特定标签的属性（例如 a 标签的 href 属性），为了获取更准确的数据类型，需要使用类型断言

类型断言使用 `as`关键字比较元素具体类型，这样可以访问标签的具体属性：

```tsx
const link = document.getElementById('link') as HTMLAnchorElement
```

此时可以使用 `link.href`属性

Tip：

如果想要查看 html 标签的元素类型，可以选中元素后使用`console.dir($0)`在`Prototype`中查看

## TS新增类型

### 字面量类型

例如：

```tsx
let str1 = 'Hello TS'
const str2 = 'Hello TS'
```

上述两个变量都没有指定数据类型，由TS自动进行类型推断，但是会发现 str2 的数据类型是 `Hello TS`，这是由于 const 常量值不可变化。此处的`Hello TS`为字面量类型，在TS中，特定字符串、对象、数字等都可以作为类型使用，例如：

```tsx
let num1:3 = 3
```

这里指定 num1 的类型为 3

字面量类型常常搭配联合类型一起使用，用于表示一组明确的可选值列表，例如在贪吃蛇游戏中，控制移动的方向为上下左右四个方向之一，此时就可以使用字面量类型来控制：

```tsx
function changeDirection(direction: 'up' | 'down' | 'left' | 'right') {
    console.log(direction)
}
```

此时指定了方向的可选值为四个方向之一，减少使用 string 类型的时候后续需要进行比较判断的麻烦

### 枚举类型

枚举类型使用 `enum`关键字来定义，其功能类似字面量类型与联合类型组合的功能，可以表示一组明确的可选值，例如同样选择四个方向之一的方法实现：

```tsx
enum Direction {
    Up,
    Down,
    Left,
    Right,
}
function changeDirection(direction: Direction) {
    console.log(direction)
}

changeDirection(Direction.Up)  // 0
changeDirection(Direction.Right)  // 3
```

注意枚举值的调用方式，并且其输出结果为值的索引，使用枚举类型注意**开发者约定枚举名称、枚举值以大写字母开头**，不同枚举值之间通过逗号分隔

#### 枚举成员的值

利用TS的提示功能，可以方便地看到枚举成员地取值，上述定义的方式中，枚举成员的值为从0开始增长的数值，这种枚举成员值为数字的枚举被称为**数字枚举**，当然也可以为枚举中的成员赋值初始化，例如：

```tsx
// 注意如果只给第一个成员赋值 则后续成员会基于此进行自增
enum Direction {
    Up = 2,
    Down = 4,
    Left = 6,
    Right = 8,
}
```

同样可以为枚举成员赋值为字符串等其他数据类型：

```tsx
enum Direction {
    Up = 'UP',
    Down = 'DOWN',
    Left = 'LEFT',
    Right = 'RIGHT',
}
```

字符串枚举不具备自增长的特性，如果某一个成员设置为字符串，除了开头的成员外（自动初始化为数字，其他的成员都要设置为字符串）

在使用 tsc 命令将 ts 代码转化为 js 代码的时候，枚举类型会转化为一个对象，此外，枚举类型和字面量+联合类型都可以实现相同的功能，但是约定上**更推荐使用字面量类型+联合类型的方式**（这种方式在编译为js代码的时候直接移除即可，使用枚举则会转化）

### any类型（不推荐使用）

例如：

```tsx
let obj: any = { x: 0 }
```

声明为 any 类型的时候，变量将失去TS的类型保护，并且不会有代码提示，因此并不推荐使用（唯一使用场景是，避免出现波浪线，先使用 any 类型占位，最后再进行修改）

### typeof - ts

TS 中也支持使用 typeof 获取元素在 JS 中的数据类型，除此之外，TS 中 typeof 也用于类型查询，即根据已有变量的值，获取其类型用于指定另一个变量，简化类型书写，例如：

```tsx
let obj: {
    name: string
    age: number
} = {
    name: 'testName',
    age: 12,
}
// 简化对象类型书写
let obj1: typeof obj = { name: 'vepor', age: 13 }
```

注意，使用 typeof 输出数据类型的时候：

```tsx
let arr: number[] = [1, 2, 3]
let obj: {
    name: string
    age: number
} = {
    name: 'testName',
    age: 12,
}
console.log(typeof arr)  // object
console.log(typeof obj)  // object
```

虽然两者都是 object，但是在 类型注解位置使用的时候并不相同（`xxx:typeof arr`和`xxx:typeof obj`）同样也无法查询函数调用的类型

## 高级类型

### class 类

例如：

```tsx
class Person {
    age: number
    gender: string
    // 构造函数参数必须指定类型注解，否则会被隐式推断为any类型
    // 注意构造函数不要指定返回值类型
    constructor(age: number, gender: string) {
        this.age = age
        this.gender = gender
    }
    // 成员方法定义与对象一样
    func(n:number):void{}
}
```

### class - 继承

类实现继承复用的方式由两个：

- 使用 extends 关键字（继承父类，同JS）
- 实现接口 implements （仅TS中有）

使用 extends：

```tsx
class Animal {
    move() {
        console.log('run')
    }
}
// extends 关键字继承
class Dog extends Animal {
    bark() {
        console.log('wawa')
    }
}
```

使用接口实现

```tsx
interface Singale {
    name: string
    sing(): void
}
// 实现接口，类必须实现接口所有的属性和方法
class Person implements Singale {
    name: string
    sing() {
        console.log('sing')
    }
}
```

### 类可见修饰符

可见性修饰符包括：

- public（默认）：成员在任何继承子类、实现都可访问

- protected：受保护的成员，**仅在其声明所在类以及其子类中可使用（非实例对象）**，换言之，只在类内部可以使用，例如：

  ```tsx
  class Animal{
      public run(){
          console.log('run')
      }
      protected move(){
          console.log('move')
      }
  }
  
  class Dog extends Animal{
      bark(){
          console.log('dog')
          // 在子类中调用父类的 protected 成员
          this.move()
      }
      
  const dog = new Dog()
  dog.move()  // 这里会出错 实例对象不可访问 protected 成员
  ```

- private：私有成员，**只在当前类内部可见，在实例对象以及子类中均不可使用**，例如：

  ```tsx
  class Animal{
      private __run__(){
          console.log('run')
      }
      move(){
          // 类内部方法可以调用
          this.__run__()
          console.log('move')
      }
  }
  
  class Dog extends Animal{
      run(){
          // 调用出错
          this.__run__()
      }
  }
  ```

- 只读修饰符 readonly，只读修饰符用于防止在构造函数之外对属性值进行修改（**被 readonly 修饰的属性只被允许初始化时赋默认值以及在构造函数中赋值**），**注意 readonly 修饰符只能用于修饰成员属性，不能修饰方法**，并且**如果修饰的属性没有添加类型注解，则会变为字面量类型（类似 const）**，readonly 也**可以用于修饰接口或者{}**：

  ```ts
  interface Test {
      readonly testP: number
      testP2: string
  }
  let t1: Test = {
      testP: 12,
      testP2: 'str',
  }
  // 提示只读属性，不可修改
  t1.testP = 15
  t1.testP2 = 'str1'
  
  // {}
  let obj: { readonly name: string; age: number } = {
      name: 'str',
      age: 13,
  }
  // 此时也会提示只读属性
  obj.name = 'str2'
  ```

  

