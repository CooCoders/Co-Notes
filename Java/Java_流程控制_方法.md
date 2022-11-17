## Note 2

### 流程控制

#### 顺序结构

自上而下运行代码

#### 分支结构

- if {....}

- if else:

  ```JAVA
  if(money > 100){
      System.out.println("Go!");
  }else{
      System.out.println("No!");
  }
  ```

- ```java
  
  if (grade > 95) {
      System.out.println("bike");
  } else if (grade > 80) {
      System.out.println("transformer");
  } else {
      System.out.println("hunter time.");
  }
  ```

- switch - case: 表达式（将要匹配的值），取值可以是 byte，short等基础类型以及枚举或String，注意 case 后面只能是字面量，而不能是变量 ，case 给出的值不允许重复

  ```java
  switch(grade){
      case 1:
          System.out.println("1");
          break;
      case 2:
          System.out.println("2");
          break;
      case 3:
          System.out.println("3");
          break;
      default:
          System.out.println("no");
          break;
  ```

  也可以使用另一种格式（Java14 之后支持）：

  ```java
  String resStr = switch (grade) {
      case 1 -> "1";
    case 2 -> "2";
      case 3 -> "3";
      default -> "4";
  };
  ```
  



#### 循环结构

- for
- while ：与 for 等同
- do{....}while()  至少执行一次

搭配跳转使用：

- continue： 跳过
- break： 结束



随机数获取：使用 Random 类

```java
Random r = new Random();
//0 ~ 99
int num = r.nextInt(100);
```

上述生成随机数的范围是左闭区间，右开区间，因此，如果想要得到任意区间的随机数（左右均为闭区间）：

```
[a, b]
-> 
random r.next(0, b-a+1)+a 
```





### 数组

数组是一种容器，用来存储同种数据类型的多个值，两种方式：

- int[] array  
- int array[]

数组的静态化初始方式：int[] array = new int[]{1, 2, 3}  -> int[] array = {1, 2, 3}

直接打印 array 的时候，输出的结果结构为 `[D@776e4f`，其中`[`表示是数组，`D`表示其中所有的数据都是 double 类型的

#### 数组动态初始化方式

```java
int[] arrDynamic = new int[10];   //自主定义数组长度
```

不同数据类型默认初始化的值不同：

- 整数类型 0
- 小数类型 0.0
- 字符类型为 /u0000 空格
- 布尔类型 为 false
- 引用数据类型为 null

注意问题：

- 数组越界



### 方法

提高复用性和可维护性

```java
public static 返回值类型 方法名 （参数）{
	方法体;
	return 返回值;
}
```

注意事项：

- 方法不调用就不执行
- **方法与方法之间是平级关系，不能相互嵌套**
- 方法的编写顺序和执行顺序无关
- 方法的返回值类型为 void ， 可以省略 return 语句，如果有返回值 return 后面不能跟具体数据
- return 可以用来结束方法，后面的代码不会被执行



#### 重载

在同一个类中，定义多个同名的方法，这些同名的方法具有相同功能，但是每个方法具有**不同的参数**，则这些方法构成了重载关系。

其中不同参数可以为：

1. 参数个数不同
2. 类型不同
3. 顺序不同

实际运行的时候， JVM 会根据不同的参数判断使用不同的方法

判断不同方法是否构成重载关系的关键在于：

1. 在同一个类中
2. 方法名相同
3. 参数不同

对于返回类型并不关注，例如

```java
public static int func(int a){}

public static double func(int a){}
```

两者参数相同，所以不构成重载关系，而对于：

```java
public static int func(int a){}

public static double func(int a, int b){}
```

两者实际上是构成重载关系的

对于顺序不同构成的重载方法：

```java
public static void func(int a, double b){}

public static void func(double a, int b){}
```

虽然由于参数类型顺序不同，两个方法构成了重载关系，但是实际中并不推荐使用这种方式。



调用重载函数的时候，注意一些特殊情况，例如对于重载方法：

```java
public static void comp(short s1, short s2){
    System.out.println("short");
}

public static void comp(int i1, int i2){
    System.out.println("int");
}
```

调用：

```java
comp(10, 20);
```

这种情况下实际上调用的是 int 类型，如果要使用 int 类型，可以用两种方式：

```java
comp((short)10, (short)20);


short s1 = 10;
short s2 = 20;
comp(s1, s2);
```

第二种方式更为常见。



### Java 内存分配

- 栈： 方法运行时候使用的内存，方法进栈运行，运行完毕即出栈
- 堆： 使用 new 关键字的都会在堆内存中开辟一个小空间
- 方法区： 存储可以运行的 class 文件
- 本地方法栈：  JVM在使用操作系统功能时候使用（与开发无关）
- 寄存器： 给 CPU 使用（与开发无关）



#### 基本数据类型和引用数据类型

基本数据类型在使用的时候，会直接保存在栈中，而引用数据类型则是在使用 new 关键字创建时，在堆内存中进行实际存储，栈中变量存储的实际上是地址值（因此，“引用”的意思实际上是指使用了其他空间中的数据）

从内存使用的角度出发，对于基本数据类型的数据 a  和 b，如果使用语句 a = b，那么实际上是将 b 的数据内容复制了一份给 a，彼此进行改变的时候并不会有影响，但是对于引用数据类型， 使用语句 a = b 时候，实际上是将两个变量的内容指向了同一块内存区域，这样，**该内存区域中发生的数据改变会同时影响两部分**。 



#### 方法值传递

对于基本数据类型，传递给方法之后，进行值修改之后不会影响之前的的值（调用函数的时候会同样在栈中新建一个变量，而调用函数运行完毕后，所建立的变量随之撤销，因此对其进行的改变也不会保留）

对于引用数据类型，实际形参传递的是引用地址值，因此在调用函数中进行的数值修改同样会影响到原始的数据。



### 案例部分

#### 注意字符串比较的时候 == 与 equals方法

使用 == 比较字符串的时候，比较的不是内容，而是两个引用是否指向同一个对象，例如：

```java
String st1 = "头等舱";
String st2 = "头等舱";
System.out.println(st1 == st2);
```

此时结果为 true，因为此时结果都保存在字符串常量池中，保存结果为一个

```java
String st1 = new String("头等舱");
String st2 = new String("头等舱");
System.out.println(st1 == st2);
```

此时结果为 false， 由于使用了 new 关键字，相当于在常量池中创建了两个对象，指向不同

因此，比较内容的使用，更推荐使用 `String.equals()` 方法，比较字符串的内容

```java
String st1 = new String("头等舱");
String st2 = new String("头等舱");
System.out.println(st1.equals(st2));
```



#### 注意强制转换的使用

例如：

```
(char)('A' + 1)    ->  'B'
```

即使是随机产生字符，也可以通过先将字符保存在数组中，随后通过索引进行随机选择的方式实现



#### 通过循环实现等待功能

场景：在进行某个操作之后，需要进行判断是否合法，如果非法，则需要重复该操作直至产生合法操作。

这一功能可以通过改变循环变量的位置实现，例如：

```java
for (int i = 0; i < arr.length;) {  // 注意这里 循环变量 的自增要去掉
    int score = sc.nextInt();
    if(score >=0 && score <= 100){
        arr[i] = score;
        i++;  		// 将 i++ 从 for()中改到 判断表达式中，直到输入合法 循环变量才会变化，从而实现等待输入的目的
    }else{
        System.out.println("one more time.");
    }
}
```



#### 合理划分功能单元

为了保证思路清晰，所以要合理划分功能单元，尽量一个循环只做一件事，一个方法只完成一个功能



#### 初始化数组

```
int[] arr = new int[len];
Arrays.fill(res, -1);   // 使用 -1 初始化 数组
```



#### 生成随机序列方法

可以通过一次循环，当前索引的值与之后的随机索引不断进行交换的方法来实现，例如：

```java
public static int[] getRandNum(int num) {
    int[] res= new int[num];
    for(int i=0; i<num; i++){
        res[i] = i+1;
    }
    Random r = new Random();
    for(int i=num-1; i>0 ; i--){
        int tmpIndex = r.nextInt(i);
        int tmp = res[tmpIndex];
        res[tmpIndex] = res[i];
        res[i] = tmp;
    }
    return res;
}
```

同样，如果需要随机打乱某个数组中的内容，例如字符或者字符串，同样可以通过随机数字当作结果数组的索引来实现随机打乱。











### 书写规范

- 尽管 if 语句中只有一条语句的时候可以不使用括号，但是为了可读性一般还是会加上，并且，值得注意的是：

  ```java
  if(var_tmp > 10)
  	int c = 10;
  ```

  此时会报错，因为 声明变量并赋初始值并不是一条语句。

- 注意命名规范：

  - 项目名：全部小写
  - 包名：全部小写，并通过`.`进行分割
  - 类名：大驼峰法，所有单词的首字母都大写
  - 属性名、方法名： 小驼峰法，首个单词首字母小写，第二个单词开始首字母大写： userName
  - 常量：全部大写

- 对于 boolean 类型变量的判断，不要使用 `==`，直接进行判断（防止少写一个`=`，此时变量恒等于true）

- 快速生成循环语句：

  ```
  fori
  ```




- IDEA `Ctrl + p` 显示方法参数顺序以及类型
- IDEA `Ctrl + Alt + M` ，首先选中部分代码，然后使用此快捷键自动抽取代码，此时如果代码中存在类似的代码块，同样也会显示是否替换

