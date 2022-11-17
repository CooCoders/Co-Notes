## Java note 1

整体学习路线：

<img src="https://mmbiz.qpic.cn/mmbiz_jpg/xq9PqibkVAzqgiaWSQAiazsBu1IXLpqL4YLmSBMcemArjB36ZELVVBicdic0xmDH2C9CiaAZGF2r9OZuI8oRrLtvlbBg/640?wx_fmt=jpeg&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="图片" style="zoom:50%;" />





### 常见CMD命令：

- 盘符 +  ： （E:）
- dir 查看当前路径下的内容
- cd 目录 进入单级目录
- cd  ..  回退到上一级目录
- cd 目录1\目录2\ ..... 进入多级目录
- cd \ 回退到盘符目录
- cls 清屏
- exit 退出窗口



### java 版本

- Java SE： 标准版，用于桌面应用的开发 （基础）（桌面应用最广泛使用的C）
- Java ME： 小型版，应用于嵌入式等等，使用android等替代
- java EE： 企业版，用于 web方向的网站开发 - （网站开发：浏览器 + 服务器）



### 高级语言的运行方式：

1. 编程
2. 编译
   1. 编译型（C）： 针对不同的设备使用不同的编译方式（整体翻译）
   2. 解释型（Python）：按行进行翻译
   3. 混合型（编译 + 解释）（java），先编译，随后运行在不同的虚拟机中JVM，因此可以跨平台，针对不同的平台，使用不同的 JVM 就可以了
3. 运行

### jdk jre

其中jdk java 开发工具包，其中包含的是  JVM， 核心类库 以及 开发工具（javac，java等命令）

jre是运行环境，其中包含 JVM， 核心类库 以及运行工具 （比较小）

其中 jdk 包含了 jre ，jre 包含了 jvm



### 注释

1. 单行注释 `// 注释信息`
2. 多行注释 `/* 注释信息 */`
3. 文档注释 `/** 注释信息 */` 主要用于生成文档（根据注释内容自动生成）



### 数据类型

java数据类型分为两种，基本数据类型和引用数据类型。

其中基本数据类型分为四类八种：

- 整数
  - byte （-128 - 127）
  - short
  - int
  - long
- 浮点数
  - float
  - double
- 字符
  - char
- 布尔
  - boolean

注意书写的时候，对于 float 类型数据要在末尾加 `F` 标记，long 类型数据则是加 `L`标记（推荐使用大写）

### 标识符

组成：数字，字母，下划线以及`$`，开头不能是数字

软性规则：小驼峰命名法，适用于 **方法** 和 **变量**

- 标识符是一个单词的时候，全部为小写字母
- 标识符为多个单词组成的时候，第一个单词首字母小写，其他单词的首字母大写，例如 `firstName`

大驼峰命名法，适用于类

- 标识符为一个单词的时候，首字母大写
- 标识符为多个单词的时候，所有单词的首字母大写，例如 `GoodStudent`

所有的标识符名称都应该： 见名知义， 所有的名称注意要有对应含义



### 键盘键入

使用 Scanner 类输入，例如：

```java
Scanner sc = new Scanner(System.in);
int i = sc.nextInt();

System.out.println(i);
```



### IDEA 使用

项目结构：

- project
  - module
    - package
      - class



在建立 package 的时候，一般使用单位域名的反顺序建立，例如域名为 `a.b.c`，则建立的时候使用`c.b.a`，使用 点号 隔开的时候，会自动创建多级的目录结构

注意几个快捷键的方式：

- psvm ：生成 `public static void main(String[] args)`
- sout :  生成 `System.out.println()`

设置图片作为编辑窗口的背景图片：`Settings` -> `Appearance & behavior` -> `Appearance` -> `background image`



#### 快捷键

- 格式化代码： `ctrl + alt + L`





### 运算符

#### 算数运算符

加减乘除以及取模

- 在计算过程中，如果有小数参与，结果可能会出现误差（存储方式）

- 如果只有整数参与运算，得到的结果是整数

- 类型转换：

  - 隐式转换（自动类型提升）：由取值范围较小的数据类型转化为取值范围较大的数据类型（byte，short，char数据在计算的时候会先转化为int，然后进行计算），例如：

    ```
    byte a = 10;
    byte b = 10;
    c = a + b  --> c 实际上是 int 类型
    ```

    

  - 强制转换：例如 double b = 10.3;  int  a = (int)b; 

    - 如果转化过程中出现了转化超过范围，那么结果会出现错误（但是计算过程是允许的）

  - `+`可用于字符串拼接，如果一个字符串与数字等类型使用`+`连接，那么此时是拼接符号 ：

    ```
    "123" + 123 -> "123123"
    "acb" + true -> abctrue
    
    1 + 10 + "123" -> 11123  // 从左往右 先计算后进行拼接 (注意连续 + )
    1 + 2 + "abc" + 1 + 2  -> "3abc12"
    ```

    

#### 自增自减运算符

`++`和`--`，注意先用后加还是先加后用



#### 赋值运算符和关系运算符

赋值运算符：=, +=, -= 等等

注意**对于 +=, -=, *= 等等运算都会隐藏一个强制转换**，例如：

```
short b = 10;
b += 1;        // 实际上执行的是  b = (short)(b + 1), 因为正常情况下 b+1 的计算结果是 int 类型
System.out.println(getType(b));
```



#### 关系运算符

==， >=， <= 等，实现各种判断，返回 true 或 false。



#### 逻辑运算符

四种逻辑计算 `&`,`|`,`^`,`！`

逻辑判断的优化过程：短路逻辑运算符：

- 短路与 `&&`
- 短路或 `||`

在进行逻辑运算的时候，如果自左到右运算到某一部分时候已经可以确定表达式的值，则接下来的部分就不需要继续计算，例如：

```java
int a = 10;
int b = 10;
boolean c = ++a < 10 && ++b < 10;
System.out.println(c);   // false
System.out.println(a);   //  11 ++a执行
System.out.println(b);   // 10 ++b没有执行
```

与之对应，如果使用的是原始 `&`:

```java
boolean c = ++a < 10 & ++b < 10;
```

则 b=11，全部都会计算



#### 三元运算符

vc = 表达式 ? va : vb ;

例如判断两个数字较大者：

```java
int res = a > b ? a: b;
```

并且也可以较为方便的将 boolean 结果转化为 字符串等其他类型



#### 运算符优先级

小括号优先级最高，合理使用小括号：

- 提高优先级
- 格式清楚















### 书写规范

- 对于长整数类型，一般在数据后面加`L`。
- java中数值类型无法转化为 boolean 类型，因此 `if(a)`这种写法不被允许
- java不允许使用未声明的变量
- 变量尽可能放在第一次使用的地方





### 小点

- 制表符的作用在于将前面字符串的长度补齐到8或者8的倍数，最少补1个，最多补8个。

- 对于数据类型判断：

  - 基本数据类型无法直接获得数据类型

  - 包装类型的数据可以通过 getClass().getName() 方法来得到，为了方便一般会将此过程写成函数：

    ```java
    public static String getType(Object obj){
    //        return obj.getClass().toString();
    return obj.getClass().getName();
    }
    ```

    





































