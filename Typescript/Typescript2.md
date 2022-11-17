# Typescript 2

## 类型兼容性

### 类兼容性

两种类型系统：

- Structural Type System：结构化类型系统
- Nominal Type System：标明类型系统 （C#、Java等），这类语言检查的是名称，如果名称不同，则会被认为是不同的类型

TS采用的是第一种，类型检查的是值所具有的形状（属性名、数量等），直观理解为如果两个对象的形状相同，则被认为属于同一类型，例如：

```ts
class Point{
    x:number
    y:number
}
class Point2D{
    x:number
    y:number
}
// 此时并未出错 Point 和 Point2D 形状相同
// TS 只会检查两者结构是否相同（都有x、y两个属性，属性的类型也相同）
// 注意属性名也必须相同
let p:Point = new Point2D()
```

除了结构相同以外，如果类A包含类B的全部属性，则初始化B的实例对象可以使用A，例如：

```ts
class Point {
    x: number
}
class Point2D {
    x: number
    y: number
}
class Point3D {
    x: number
    y: number
    z: number
}
let a = new Point()
let b: Point = new Point3D()
let c: Point2D = new Point3D()
// 出错
// let d: Point2D = new Point()
```

简单说：多的可以兼容（初始化）少的，少的不能兼容多的

### 接口兼容性

TS中的接口、函数也存在兼容性：

```ts
interface Point {
    x: number
}
interface PointT {
    x: number
}
interface Point2D {
    x: number
    y: number
}
interface Point3D {
    x: number
    y: number
    z: number
}

let a: Point = { x: 1 }
let b: PointT = a
let c: Point3D = { x: 1, y: 1, z: 1 }
let d: Point = c
```

**注意：类和接口之间也存在兼容（毕竟两者最终都归于实例对象）**：

```ts
interface Point {
    x: number
    y: number
}

class Point2D {
    x: number
    y: number
}
// 兼容
let p: Point = new Point2D()
```

### 函数兼容性

函数兼容性需要考虑：

- 参数个数
- 参数类型
- 函数返回值类型

#### 参数

从参数的角度，参数多的见如同参数少的 -- **参数少的可以赋值给参数多的**（注意这一规则与类兼容相反），例如：

```ts
let f1 = (a:number)=>{}
let f2 = (a:number, b:number)=>{}
f2 = f1
```

参数少的可以赋值给参数多的，这一点是为了符合JS中书写的习惯，例如常用的 foreach 函数，其参数列表为 （value：string，index：number，array：string），但是在实际使用的时候往往只传入部分参数

**参数类型限制**：

相同位置的参数类型要相同（原始类型），或兼容（对象类型）

如果参数为对象，需要将对象拆开，将每个属性看作一个参数，参数少的可以赋值给参数多的，例如：

```ts
interface Point2D {
    x: number
    y: number
}
interface Point3D {
    x: number
    y: number
    z: number
}
type F2 = (p: Point2D) => void
type F3 = (p: Point3D) => void

let f2:F2
let f3:F3
// 报错
// f2 = f3
f3= f2
```

#### 返回值

函数返回值类型必须相同（原始类型），如果返回值为对象类型，则需要符合对象兼容的要求（成员多的赋值给成员少的）

## 交叉类型

交叉类型功能类似接口继承，用于将多个类型组合为一个类型（常用于对象），例如：

```ts
interface Person {
    name: string
}
interface Contact {
    phone: string
}
// 将两个接口组合为新的类型
type PersonDetail = Person & Contact
let obj: PersonDetail = {
    name: 'zs',
    phone: '123',
}
```

**交叉类型和接口继承之间的区别 （?）：**

两种方法对于同名属性的冲突处理不同（实际使用中尽量避免这种方式）

接口继承中，如果两个接口之间出现属性冲突，继承会出错：

```ts
interface A{
    name:string;
    fn(a:number):void;
}
// 这里会报错 name 和 方法 fn 中的参数 a 类型不符合
interface B extends A{
    name:number;
    fn(a:string):void;
}
```

但是在交叉类型中：

```ts
interface A {
    name: string
    fn: (a: number) => string
}
interface B {
    name: number
    fn: (a: string) => void
}
// 此时对于重复的属性 其类型采用的 或
// name: (string | number)
// 对于函数参数同样如此
type C = A & B
```



## 泛型

泛型在保证类型安全的前提下，让函数多种类型一起工作，实现复用，常用于函数、接口、类中。例如让一个函数接收任意类型的数据（不推荐使用 any 类型）

### 创建泛型函数

例如：

```ts
function id<Type>(value:Type):Type{
    return value
}
```

格式：

- 在函数名后面添加`<>`，括号中添加类型变量
- 类型变量处理的是类型，类型变量相当于一个容器，具体类型由用户在使用的时候确定

### 调用泛型函数

例如：

```ts
const num = id<number>(10)
const str = id<string>('aaaa')
```

调用格式：

- 在尖括号中指定具体类型，此时 Type 会捕获该类型，将所有的 Type 类型转化为指定类型

**简化调用泛型函数**：

调用泛型函数的时候，可以省略尖括号的类型：

```ts
const num = id(10)
```

此时 TS 内部会进行类型参数推断，根据传入的实参自动推断出类型变量 Type 的类型

### 泛型约束

使用泛型时，由于类型变量 Type 可以代表多个类型，这导致无法访问任何属性，这时候需要为泛型添加约束来收缩类型（缩小类型取值范围）：

- 指定更具体的类型：例如使用数组的长度属性的时候，直接将类型修改为 Type[] 类型，这样，数组一定会存在 length 属性，从而访问长度属性：

  ```ts
  function getLength<Type>(value: Type[]): void {
      console.log(value.length)
  }
  
  getLength([1, 2, 3])
  ```

- 添加约束：需要创建描述约束条件的接口，并通过 extends 关键字使用该接口，为泛型添加约束：

  ```ts
  // 使用接口描述约束条件
  // 该约束条件表示传入的参数必须具有 length 属性
  interface ILength {
      length: number
  }
  function getLength<Type extends ILength>(value: Type): void {
      console.log(value.length)
  }
  getLength('afdfd')
  getLength([1, 2, 3, 4])
  // 注意具有 length 属性的对象也符合约束条件：
  getLength({ length: 23 })
  ```

### 使用多个泛型变量

泛型变量可以有多个，并且这些变量之间可以互相约束，例如获取对象属性值的方法：

```ts
function getProp<Type, Key extends keyof Type>(obj: Type, key: Key) {
    console.log(obj[key])
}

let person = { name: 'zs', age: 12 }
// 访问对象属性
getProp(person, 'name')
// 访问数组属性
getProp([1, 2, 3], 'length')
// 访问数组索引 - 数字索引也可以作为属性使用 (number)
getProp([1, 2, 3], 1)
// 访问字符串属性
getProp('abc', 1)
// 访问字符串属性方法
getProp('abc', 'length')
// 访问数字属性方法
getProp(123, 'toFixed')
```

- keyof 关键字接收一个对象类型，生成其键名称的联合类型（例如上述代码中，表示 (name | age)）-- 对象所有属性的联合类型
- 上述代码中 Key 变量收到 Type 的约束，限制为 Key 只能是 Type 键中的任意一个

### 泛型接口

接口可以配合泛型来使用，例如：

```ts
interface Test<Type> {
    // 接口的成员也都可以使用 Type
    getId: (value: Type) => Type
    ids: () => Type[]
}
// 与泛型函数不同点：泛型接口必须指定类型
let test: Test<number> = {
    getId(value) {
        return value
    },
    ids() {
        return [1, 2, 3]
    },
}
```

数组其实就是通过泛型接口实现（可以对各种数据类型的数组进行处理）

### 泛型类

泛型类的创建方式与泛型接口基本一致：

```ts
class Generic<Type>{
    defaultValue:Type
    add:(x:Type, y:Type)=>Type
}

const test = new Generic<number>()
```

泛型类可以通过构造函数的方式不指定类型：

```ts
class Generic<Type> {
    defaultValue: Type
    constructor(val: Type) {
        this.defaultValue = val
    }
}
// 向构造函数传递数字10 从而自动推断出 Type 为10 
const test = new Generic(10)
```

### 泛型工具类型 

#### partial

泛型工具类 partial 用来构建一个类型，并将所有的属性设置为可选：

```ts
interface Props {
    id: string
    children: number[]
}
type PartialProps = Partial<Props>
```

#### readonly

构建一个所有属性都变为只读的类型：

```ts
type ReadonlyProps = Readonly<Props>
```

#### Pick

Pick工具类型用于从 Type 属性中选择一组属性来构造新类型

```ts
type PickProps = Pick<Props, 'id'|'name'>
```

注意第二个类型变量传入的属性必须是第一个类型变量中存在的属性

#### Record

`Record<Keys, Type>`构建一个对象类型，属性键为 Keys， 属性类型为 Type

```ts
type RecordProps = Record<'a' | 'b' | 'c', string[]>
let obj: RecordProps = {
    a: ['a'],
    b: ['b'],
    c: ['c'],
}
```

限制条件：所有属性都是 Type 类型时使用（属性类型一致）

## 索引签名

特殊情况下，如果在使用对象时无法确定对象的结构或者对象结构中可以出现任意多个属性的时候，需要使用索引签名类型，其结构为：

```ts
interface AnyObject {
    // key 为占位符 string 表示属性名称类型（其实js对象的属性都是string类型）
    // number 表示属性值的类型为 number
    [key: string]: number
}
```

使用：

```ts
let obj: AnyObject = {
    a: 12,
    b: 1,
}
```

数组在实现上借助了索引签名，方法类似：

```ts
interface MyArray<T> {
    // 使用数字作为索引/属性
    // 表示数组中可以有任意多个数字索引的元素
    [n: number]: T
}

let arr: MyArray<number> = [1, 3, 5]
let arr1: MyArray<string> = ['a', 'b', 'c']
```

## 映射类型

目的：简化写法，例如，当类型 PropKeys 中有 x/y/z，另一个类型 Type 中也有这三个属性：

```ts
type PropKeys = 'x'|'y'|'z'
type Type1 = {x: number, y:number, z:number}
```

类似这样的结构，可以使用映射类型来简化：

```ts
type Type2 = { [Key in PropKeys]: number }
```

语法上类似 forin 循环，映射类型是基于索引签名类型实现的，因此两者都使用了`[]`，通过映射类型创建的类型与原类型结构完全相同，**注意映射类型只能使用在类型别名中**

使用 keyof 可以拓展为获取对象类型的所有属性：

```ts
type Type3 = { [Key in keyof Props]: string }
```

注意这里属性的来源也必须通过类型别名指定

## 索引查询类型

索引查询类型用于查询属性的类型：

```ts
type Props = {a:number; b:string, c:boolean}
type ta = Props['a']
type tb = Props['b']
```

注意要使用属性的字符串方式，也可以同时查询多个属性：

```ts
type tab = Props['a'|'b']
type tabc = Props[keyof Props]
```

## 类型声明文件

JS 第三方库无论是否使用 TS 编写，最终都会编译成 JS 代码，而在使用这些 JS 的第三方库的时候，为了保证代码提示和类型保护机制，需要使用类型声明文件

### TS 的两种文件类型

- `.ts`文件：implementation
  - 包含类型信息和可执行代码
  - 可以被编译为 js 文件，然后执行
  - 编写程序逻辑
- `.d.ts`文件：declaration
  - 只包含类型信息的类型声明文件
  - 不会生成 js 文件，仅提供类型信息

例如，`Ctrl+left mouse`可以打开 `lib.es5.d.ts`文件，这就是 ts 的类型声明文件，对于 dom ，window 等对象，其类型声明文件则保存在`lib.dom.d.ts`文件中

### 第三方库的类型文件

第三方库的类型声明文件有两种存在形式：

- 自带的类型声明文件：例如 axios - 此时 TS 会自动导入该库的类型声明文件（该文件在 `package.json`中的`typings`属性指定）
- 由 DefinitelyTyped 提供：专门用于提供高质量 TS 类型声明的 github 仓库，可以通过 npm/yarn 来下载该仓库的类型声明包，例如 `npm i --sav-dev @types/lodash`，如果使用包时 vscode 并未发现该库的声明文件，vscode 也会给出该命令的安装提示信息 （除此之外，TS 官网也给出了一个页面用于查询`@types/`库）

### 创建类型声明文件

目的：

- 将 JS 项目迁移到  TS 中，需要为项目添加类型声明
- 多个 TS 文件公用相同的类型，为了简化代码抽离公共的部分类型
- 开发库

创建自己项目的类型声明文件：

1. 创建 `index.d.ts`文件
2. 在 index 文件中创建需要共享的类型，并使用 export 导出（ts 文件支持 import-export 的模块化功能，官网文档中介绍了 TS 支持各种模块化形式的类型声明，例如 AMD、CommonJS 等等）
3. 在需要使用这部分类型的 ts 文件中，通过 import 导入，注意要省略文件的`.d.ts`后缀

为已有的 js 文件创建类型声明文件例子：

js文件：

```js
let count = 10
let name = 'zs'

function add(x, y) {
    return x + y
}

function changeDirection(direction) {
    console.log(direction)
}

const formatPoint = point => {
    console.log('position: ', point)
}

export { count, name, add, changeDirection, formatPoint }
```

为上述 js 文件创建类型声明文件，注意此类型声明文件应与 ts 文件同名，TS 会自动加载与 js 文件同名的 d.ts 文件：

```ts
// declare 关键字用于类型声明，为其他文件已存在的变量声明类型
// 常用于 let const function 等同时存在与 js 和 ts 文件的类型
// 对于 type interface 等明确只出现在 ts 中的类型，可以省略
declare let count: number
declare let username: string
declare function add(x: number, y: number): number
declare function changeDirection(direction: 'UP' | 'DOWN')

interface Point {
  x: number
  y: number
}
type FormatPoint = (point: Point) => void
declare const formatPoint: FormatPoint

export { count, username, add, changeDirection, formatPoint }
```

