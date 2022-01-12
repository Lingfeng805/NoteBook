- **JVM**（Java Virtual Machine）：Java虚拟机；通过JVM，Java实现了平台无关性，Java语言在不同平台运行时不需要重新编译，只需要在该平台上部署JVM就可以了。

- **JRE** (Java Runtime Environment):是Java程序的运行时环境，包含`JVM`和运行时所需要的核心类库；

- **JDK**(Java Development Kit):是Java程序开发工具包，包含JRE和开发人员使用的工具。其中的开发工具：编译工具（`javac.exe`）和运行工具（`java.exe`）

  

- ![image-20211214163716216](E:/software/Typora/pictures/image-20211214163716216.png)





# Java快速入门

## Java简介

### 第一个Java程序

```java
public class HelloWorld{
	public static void main(String[] args){
    	System.out.println("Hello,world!");
    }
}
```

- 定义`class`(类)，**类名**为`HelloWorld`, **`public`**表示该类是公开的。**{}**中间的部分为**类的定义**。

```java
public class HelloWorld{
	...
}
```

- **类的定义**：定义一个名为`main`的方法
  - **( )**括号括起来的参数，此处参数类型是`String[]`，参数名是`args`，而`public`、`static`用来修饰方法，这里表示它是一个**公开的静态方法**，`void`是方法的返回类型，而**{}**中间的就是**方法的代码**。

```java
	public static void main(String[] args){
    	...
    }
```

- 方法的代码每一行用`;`结束，此处只有一行代码

```java
		System.out.println("Hello,world!");
```

- 最后，把代码保存为文件时，文件名必须是`HelloWorld`（与定义的类名完全保持一致）

【如何运行Java程序？】

- Java源码本质上是一个文本文件，先用`javac`把`HelloWorld.java`编译成**字节码文件**`HelloWorld.class`格式，然后，用`java`命令执行这个字节码文件。

  ![image-20211214201526582](E:/software/Typora/pictures/image-20211214201526582.png)

- 因此，可执行文件`javac`时编译器，而可执行文件`java`就是虚拟机。
  - 第一步，在保存`HelloWorld.java`的目录下执行命令`javac HelloWorld.java`,(将源码编译产生一个字节码文件`HelloWorld.class`)
  - 第二步，执行生成的**字节码文件**，使用命令`java HelloWorld`。



## Java程序基础

### 变量

- Java是**强类型**语言，变量必须**先定义后使用**（类型+名称+初始值），如定义一个整数类型的变量：

  ```java
  int x = 1;
  ```

- 【变量使用的**注意事项**】：

  - 变量名不能重复；
  - 变量未赋值，不能使用；
  - 整数类型默认为`int`类型，所以在使用其他整数类型时，为了避免出错，在定义的时候要区分，如`long`类型的变量在定义时，为了防止整数过大（未超出`long`类型发范围，但超出`int`类型范围时），后面要加`L`,如：`long a = 100000000000L`;
  - 浮点数类型默认为`double`类型，所以在使用其他浮点数类型时，为了避免不兼容的类型，在定义的时候要区分，如`float`类型的变量在定义时，后面要加`F`,如：`float a = 3.14F`;

### 常量

- 定义变量的时候，如果加上`final`修饰符，这个变量就变成了常量：

  ```java
  final double PI = 3.14;  //PI是一个常量
  ```

  常量在定义时进行初始化后就**不能再次赋值**，再次赋值会导致编译错误。根据习惯，常量名通常全部**大写**。

### var关键字

- 有时，类型的名字太长，写起来比较麻烦，如：

  ```java
  StringBuilder sb = new StringBuilder();
  ```

  此时，若想**省略变量类型**，可使用`var`关键字：

  ```java
  var sb = new StringBuilder();
  ```

  编译器会根据赋值语句自动推断出变量`sb`的类型是`StringBuilder`。

  **使用`var`定义变量，仅仅是少写了变量类型而已**。

### 常见命名约定

- 【**小驼峰命名法**】：针对**方法、变量**的命名
  - 标识符只有一个单词的时候，首字母小写，如`name`;
  - 标识符由多个单词组成的时候，第一个单词首字母小写，其他单词首字母大写，如`fristName`;
- 【**大驼峰命名法**】：针对**类**的命名
  - 标识符只有一个单词的时候，首字母大写，如`Student`;
  - 标识符由多个单词组成的时候，每个单词首字母大写，如`GoodStudent`;

### 布尔运算

- **三元运算符**：`b ? x : y`
  - 首先判断b,若b为`True`，则返回x; 若b为`Flase`，则返回y;
  - x和y的类型必须相同；

### 数组类型

- 定义一个数组类型的变量，使用数组类型**类型[ ]**，例如`int[]`.
- 数组变量初始化必须使用`new int[5]`表示创建一个可容纳5个int元素的数组。
- Java数组的特点：
  - 数组所有元素初始化为默认值，整型都是0，浮点型是0.0，布尔型是`false`;
  - 数组一旦创建后，**大小就不可改变**；
- `数组变量.length`获取数组大小；



## 流程控制

### 数据输出

- `println`是print line的缩写，表示输出并换行。若输出后不想换行，可用`print()`。

- **格式化输出**

  `System.out.printf()`，通过使用**占位符**`%?`，`printf()`可以把后面的参数格式化成指定格式：

  ```java
  public class Main {
      public static void main(String[] args) {
          double d = 3.1415926;
          System.out.printf("%.2f\n", d); // 显示两位小数3.14
          System.out.printf("%.4f\n", d); // 显示4位小数3.1416
      }
  }
  ```

  - **占位符**
    - `%d` 格式化输出整数；
    - `%x` 格式化输出十六进制整数；
    - `%f` 格式化输出浮点数；
    - `%e` 格式化输出科学记数法表示的浮点数；
    - `%s` 格式化字符串；

### 数据输入

- **Scanner**使用步骤：

  - 【1】**导包**

    ```java
    import java.util.Scanner;
    ```

  - 【2】**创建对象**

    ```java
    Scanner sc = new Scanner(System.in);  // sc为变量名，System.in代表标准输入流。
    ```

  - 【3】**接收数据**

    ```java
    int i = sc.nextInt();  // i是变量名
    ```

    要读取用户输入的字符串，使用`sc.nextLine()`,要读取用户输入的整数，使用`sc.nextInt()`。

### if判断语句

- ```java
  if (条件语句){
  	条件满足时执行的语句；
  }
  ```

- ```java
  if (条件语句){
  	条件满足时执行的语句；
  }else{
  	执行语句；
  }
  ```

- ```java
  if (条件语句1){
  	条件满足时执行的语句；
  }else if(条件语句2){
  	执行语句；
  }else if(条件语句3){
  	执行语句；
  }else{
  	执行语句；
  }
  ```

- 判断**引用类型**相等，在Java中，判断值类型的变量是否相等，可以使用`==`运算符；但是判断**引用类型**的变量的**内容**是否相等，必须使用`equals()`方法.

  ```java
  s1.equals(s2);   //判断s1和s2两个变量的内容是否相等。
  ```

### switch多重选择语句

- 根据某个表达式的结果，分别去执行不同的分支。

- `(表达式)`的结果与`case几`的值相等，就执行相应的语句体。

  ```java
  switch(表达式){
      case 值1：
          语句体1；
          break;
      case 值2:
          语句体2;
          break;
      ...
      default:
          语句体n+1;
          break;
  }
  ```

- 使用`switch`时，不要**遗漏**`break`，若不慎遗漏`break`会出现case**穿透**现象。但从Java12开始，`switch`语句升级为更简洁的表达式语法，保证只有一种路径会被执行，并且不需要`break`语句；新语法使用`->`：

  ```java
  switch(表达式){
      case 值1 -> 语句体1；
      case 值2 -> 语句体2;
      ...
      default -> 语句体n+1;
  }
  ```

### while循环

- `while`循环先判断循环条件是否满足，再执行循环语句；
- `while`循环可能一次都不执行；

```java
初始化语句;
while (条件判断语句){
	循环体语句;
	条件控制语句;
}
```

### do while循环

- `do while`循环先执行循环，再判断条件；
- `while`循环会至少执行一次；

```java
初始化语句;
do{
	循环体语句;
    条件控制语句;
}while(条件判断语句);
```

### for循环

```java
for (初始化语句;条件判断语句;条件控制语句){
	循环体语句;	
}
```

### break和continue

- `break`跳出当前循环；即整个循环都不会执行了。
- `continue`提前结束**本次**循环，直接继续执行下次循环。



### Random

- 产生一个随机数

  - 【1】**导包**

    ```java
    import java.util.Random;
    ```

  - 【2】**创建对象**

    ```java
    Random r = new Random();  // r为变量名
    ```

  - 【3】**接收数据**

    ```java
    int i = r.nextInt(10);  // i是变量名,获取数据范围：[0,10)
    ```


## IDEA

- `IDEA`项目结构：

  ![image-20211216203600533](E:/software/Typora/pictures/image-20211216203600533.png)

- **项目Project**
  - 模块
    - 包
      - 类

### IDEA中内容辅助键和快捷键

- 快速生成语句
  - 快速生成`main()`方法：**psvm**,回车；
  - 快速生成输出语句：**sout**,回车；
- 内容辅助键
  - `Ctrl`+`Alt`+`space`（内容提示，代码补全等）
- 注释
  - 单行注释：选中代码，`Ctrl`+`/`
  - 多行注释：选中代码，`Ctrl`+`shift`+`/`
- 格式化：`Ctrl`+`Alt`+`L`



## 数组操作

### 数组定义格式

- **数组类型** **[ ]**   **变量名**

  如   `int[] arr`          定义了一个int类型的数组，数组名为arr

### 数组初始化

- **动态初始化**：初始化时只指定数组长度，由系统为数组分配初始值；

  格式： `数组类型[] 变量名` = `new 数据类型[数组长度]`;

  如：   `int[] arr = new int[3];`

- **静态初始化**：初始化时指定每个数组元素的初始值，由系统决定数组长度；

  格式： `数组类型[] 变量名` =  `new 数据类型[] {1,2,3,4,5,...}`;

  如：   `int[] arr = new int[] {1,2,3};`

  简化格式：`数组类型[] 变量名` = `{1,2,3,4,5,...};`

  如：   `int[] arr = {1,2,3};`

  

  

### 遍历数组内容

- 使用`for each`循环来打印(比较麻烦)

  ```java
  int[] ns = {1,1,2,3,5,8};
  for(int n : ns){
  	System.out.println(n);
  }
  ```

- 利用Java标准库中的**`Arrays.toString()`**,可快速打印数组内容：

  ```java
  import java.util.Arrays;
  public class Main{
  	public static void main(String[] args){
      	int[] ns = {1,1,2,3,5,8};
          System.out.println(Arrays.toString(ns));
      }
  }
  ```

### 多维数组

- 二维数组(即数组的数组)

  ```java 
  //数组ns包含3个数组，因此ns.length=3
  int[][] ns = {
      {1,2,3,4},
      {5,6,7,8},
      {9,10,11,12}
  };
  ```

  - 打印一个二维数组，可使用Java标准库的`Arrays.deepToString()`

- 三维数组（即二维数组的数组）

  ```java
  int[][][] ns = {
      {
          {1, 2, 3},
          {4, 5, 6},
          {7, 8, 9}
      },
      {
          {10, 11},
          {12, 13}
      },
      {
          {14, 15, 16},
          {17, 18}
      }
  };
  ```


### 命令行参数

- Java程序的入口是`main`方法，而`main`方法可以接受一个命令行参数，它是一个`String[]`数组。



## 面向对象编程

### 类和对象（实例）

- **类**：是对现实生活中一类具有**共同属性**和**行为**的事物的抽象，确定对象将会拥有的**属性**和**行为**；

  - 属性：在类中通过**成员变量**来体现（类中方法外的变量）

  - 行为：在类中通过**成员方法**来体现（和前述方法相比去掉**static**关键字即可）

  - ```java 
    //类的定义
    public class 类名{
    	//成员变量
        变量1的数据类型 变量1；
        变量2的数据类型 变量2；
        ...
        //成员方法
        方法1;
        方法2;
        ...
    }
    ```

  - 

- **对象**（实例）
  - **创建对象**
    - 格式： 类名 对象名 = new 类名( );
    - 如： Phone p = new Phone( );
  - **使用对象**
    - 使用成员变量： 格式： 对象名.变量名
    - 使用成员方法： 格式： 对象名.方法名( )

### 封装

- **private**关键字
  - 是一个**权限**修饰符；
  - 可以修饰成员（成员变量和成员方法）；
  - 作用：保护成员不被别的类使用，被`private`修饰的成员只在本类中才能访问；
  - 针对`private`修饰的成员变量，若需要被其他类使用，提供相应的操作：
    - 提供**get变量名（）**方法，用于获取成员变量的值，方法用**public（）**修饰；
    - 提供**set变量名（参数）**方法，用于设置成员变量的值，方法用**public（）**修饰；
- **this**关键字
  - `this`修饰的变量用于指代成员变量；
    - 方法的形参若与成员变量同名，不带this修饰的变量指的是形参，而不是成员变量；
    - 方法的形参没有与成员变量同名，不带this修饰的变量指的是成员变量；
  - `this`代表所在类的对象引用：方法被哪个对象调用，this就代表哪个对象。

## 方法

- 将具有**独立功能**的**代码块**组织成一个整体，使其具有特殊功能的代码集；

  - **方法定义**：先创建方法；

    ```java
    public static void 方法名(){
    	//方法体
    }
    ```

  - **方法调用**：手动使用后才会执行；

    ```java
    方法名();
    ```

### 带参数方法的定义和调用

- 方法定义时，参数中的**数据类型**和**变量名**缺一不可。多个参数之间用**逗号**隔开。

- ```java
  public static void 方法名(参数){
  	//方法体
  }
  ```

- ```java
  public static void 方法名(数据类型 变量名){
  	//方法体
  }
  ```
  
- **可变参数**：用`类型...`定义，可变参数相当于数组类型：

  ```java
  class Group{
      private String[] names;
      
      public void setNames(String... names){
          this.names = names;
      }
  }
  //setNames()定义了一个可变参数。调用时，可写为：
  Group g = new Group();
  g.setNames("小明","小红","小军");    //传入3个String
  ```

- 

### 带返回值方法的定义和调用

- `return`后面返回值与方法定义上的数据类型要匹配；

```java
public static 数据类型 方法名(参数){
	return 数据;
}
```

如：

```java
public static int getMax(int a, int b){
	return 100;
}
```

### 方法的通用格式

```java
public static 返回值类型 方法名(参数){
	方法体;
    return 数据;
}
```

- `public static`    **修饰符**，目前先记住这个格式；
- `返回值类型`    方法操作完毕之后返回的数据的**数据类型**；若方法操作完毕后，没有数据返回，此处写`void`，        而且此时方法体中一般不用写`return`。
- `方法名`    调用方法时使用的标识。
- `参数`    由**数据类型**和**变量名**组成，多个参数之间用**逗号**隔开。
- `方法体`    完成功能的代码块。

### 方法重载

- 指同一个类中定义的多个方法之间的关系，满足下列条件的多个方法相互构成重载：
  - 1.多个方法在**同一个类**中；
  - 2.多个方法具有**相同**的**方法名**；
  - 3.多个方法的**参数不相同**，指参数的**类型不同**或者**数量不同**。

- 重载**仅**针对同一个类中方法的**名称**与**参数**进行识别，与**返回值**无关。（重载方法返回值类型应该相同）

### 方法的参数传递

- **基本数据类型**：对于基本数据类型的参数，形式参数的改变，不影响实际参数的值；
- **引用类型**：对于引用类型的参数，形式参数的改变，影响实际参数的值；

## 构造方法

- 构造方法是一种特殊的方法，用于**创建对象**；

- 功能：完成对象数据的初始化；

  ```java
  public class 类名{
      修饰符 类名（参数）{
      
      }
  }
  //如：
  public class Student{
      public Student(){
          //构造方法内书写的内容
      }
  ```

### 构造方法的注意事项

- 构造方法的创建
  - 若没有定义构造方法，系统将给出一个默认的**无参数构造方法**；
  - 若定义了构造方法，系统将不再提供默认的构造方法；
- 构造方法的重载
  - 若自定义了带构造方法，还要使用无参数构造方法，就必须再写一个无参数构造方法；
- 推荐的使用方法
  - 无论是否使用，都手工书写无参数构造方法。

### 标准类制作

- 【1】成员变量
  - 使用`private`修饰
- 【2】构造方法
  - 提供一个无参数构造方法；
  - 提供一个带多个参数的构造方法；
- 【3】成员方法
  - 提供每一个成员变量对应的setXxx( ) / getXxx( )
  - 提供一个显示对象信息的show( )

## 字符串

- `API`
- `String`
- `StringBuilder`

### API

- Application Programming Interface:应用程序编程接口
- `Java API`：指的是JDK中提供的各种功能的Java类，这些类将底层的实现封装起来，我们不需要关心这些类是如何实现的，只需要学习这些类如何使用即可，可以通过帮助文档来学习这些API如何使用。
- 学会查询【**帮助文档**】；在`java.lang`包下的方法使用时不需要导包，除此之外都需要**导包**；

- 调用方法的时候，若方法有明确的返回值，我们用变量接收；可以手动完成，也可以使用快捷键的方式完成（`Ctrl+Alt+V`）;

### String

- 在Java中，`String`是一个**引用类型**，它本身也是一个`class`。可直接用`"..."`**双引号**来表示一个字符串；

  ```java
  //是否包含子串
  "Hello".contains("ll");  //true
  //去除首尾空白字符;空白字符包括空格、\t、\r、\n；trim()不会改变字符串的内容，而是返回一个新字符串
  "  \tHello\r\n ".trim()  //Hello
  //strip()也可以移除字符串首尾空白字符，与trim不同的是类似中文的空格字符\u3000也会被移除；
  ```

  ```java
  //替换子串
  String s = "Hello";
  s.replace('l','w');  //"Hewwo",所有字符'l'被替换为'w'
  s.replace("ll","~~");  //"He~~o",所有子串“ll”被替换为"~~"
  ```

  ```java
  //分割字符串
  String s = "A,B,C,D";
  String[] ss = s.split("\\,");  //{"A","B","C","D"}
  ```

  ```JAVA
  //拼接字符串
  String[] arr = {"A","B","C"};
  String s = String.join("***", arr);   //"A***B***C"
  ```

  ```JAVA
  //格式化字符串，字符串提供了formatted()方法和format()静态方法，可以传入其他参数，替换占位符，然后生成新的字符串
  public class Main{
  	public static void main(String[] args){
      	String s = "Hi %s, your score is %d!";
      	System.out.println(s.formatted("Alice", 80));
          System.out.println(String.format("Hi %s, your score is %.2f!", "Bob", 59.5));
      }	
  }
  ```

  **常用的占位符**：

  - `%s`：显示字符串；
  - `%d`：显示整数；
  - `%x`：显示十六进制整数；
  - `%f`：显示浮点数。

- **类型转换**：

  - 把任意基本类型或引用类型转换为字符串，可使用静态方法`valueOf()`。

  - 把字符串转换为其他类型（需根据情况而定），如把字符串转换为`int`类型：

    ```java
    int n1 = Integer.parseInt("123");  //123
    int n2 = Integer.parseInt("ff",16);  //按16进制转换，255
    ```

    把字符串转换为`boolean`类型：

    ```java
    boolean b1 = Boolean.parseBoolean("true");  //true
    boolean b2 = Boolean.parseBoolean("FALSE");  //false
    ```

  - 转换为char[]:

    ```java
    char[] cs = "Hello".toCharArray();  //String -> char[]
    String s = new String(cs); 
    ```

    

- String类在`java.lang`包下，所以使用的时候不需要导包；
- String类代表**字符串**，Java程序中所有的双引号字符串，都是String类的对象；
- 字符串**不可变**，它们的值在创建后不能被更改；
- 虽然String的值不可变，但可以被共享；
- 字符串效果上相当于字符数组（`char[]`）,但是底层原理是字节数组（`byte[]`）;
- 【字符串的比较】：使用`==`做比较
  - 基本类型：比较的是**数据值**是否相同；
  - 引用类型：比较的是**地址值**是否相同；

### StringBuilder

- `StringBuilder`是一个**可变的**字符串类，可以预分配缓冲区，我们可以把它看成是一个容器，此处的可变指的是`StringBuilder`对象中的内容是可变的。

  ```java
  StringBuilder sb = new StringBuilder(1024);
  for (int i = 0; i < 100; i++){
  	sb.append(',');
      sb.append('i');
  }
  String s = sb.toString();
  ```

- `StringBuilder`与`String`区别：
  - String：内容不可变；
  - `StringBuilder`：内容是可变的。

- **StringBuilder**主要操作是`append`和`insert`方法；

  - `append（任意类型）`；**始终在构建器的末尾添加字符**
    - 添加数据，并返回对象本身；

  - `insert`：在指定点添加字符；

- `StringBuilder`与`String`相互转换：
  - `toString()`：把`StringBuilder`转换为`String`;
  - 通过构造方法可以实现把`String`转换为`StringBuilder`, 即`StringBuilder(String s)`;

### StringJoiner

- 要**高效**拼接字符串，应该使用`StringBuilder`。

- `StringJoiner`:用指定**分隔符**拼接字符串；还可以额外附加一个“开头”和“结尾”；

  ```java
  import java.util.StringJoiner;
  public class Main {
      public static void main(String[] args) {
          String[] names = {"Bob", "Alice", "Grace"};
          var sj = new StringJoiner(", ", "Hello ", "!");    //", ":表示以“， ”作为连接；“Hello ”:指定开头；“!”：指定结尾；
          for (String name : names) {
              sj.add(name);
          }
          System.out.println(sj.toString());
      }
  }
  //Hello Bob, Alice, Grace!
  ```

  `String.join()`:`String`还提供了一个静态方法`join()`，这个方法在内部使用了`StringJoiner`来拼接字符串，在不需要指定“开头”和“结尾”的时候，用`String.join()`更方便：

  ```java
  String[] names = {"Bob", "Alice", "Grace"};
  var s = String.join(", ", names);
  ```



## 集合基础

- **集合**：提供一种存储空间**可变**的存储类型，存储的数据容量可以发生改变；集合类很多，目前先学一个：`ArrayList`；
- `ArrayList<E>`：
  - 可调整大小的数组实现；
  - <E>:是一种特殊的数据类型，**泛型**；E是此列表中元素的类型；
  - 如何使用？在出现E的地方使用**引用类型**替换即可：
    - 如：ArrayList<String>  ,  ArrayList<Student>

## 继承

- 面向对象三大特征之一，可以使子类具有父类的属性和方法，还可以在子类中重新定义，追加属性和方法；

  ```java
  public class 子类名 extends 父类名{}
  ```

- 继承的好处：

  - 提高了代码的**复用性**（多个类相同的成员可以放到同一个类中）；
  - 提高了代码的**维护性**（如果方法的代码需要修改，修改一处即可）；

- 继承的弊端：

  - 继承让类与类之间产生了关系，类的**耦合性**增强了，当父类发生变化时子类实现也不得不跟着变化，削弱了子类的独立性；

- `super`关键字和`this`关键字的用法：

  - `super`:代表父类存储空间的标识；
  - `this`:代表本类对象的引用；

- 方法重写：子类中出现了和父类中一模一样的方法声明；

  - 当子类需要父类的功能，而功能主体子类有自己特有内容时，可以重写父类中的方法，即沿袭了父类的功能，又定义了子类特有的内容；

- `@Override`

  - 是一个注解；
  - 检查重写方法的方法声明的正确性；

- 方法重写注意事项：

  - 私有方法不能被重写（父类私有成员子类是不能继承的）；
  - 子类方法访问权限不能更低（public>默认>私有）；

- Java中继承的注意事项：

  - Java中类只支持**单继承**，不支持多继承；
  - Java中类支持**多层继承**；

- `protected`：子类无法访问父类的`private`字段或方法，例如,`Student`类无法访问`Person`类的`name`和`age`字段；

  - ```java
    class Person{
    	private String name;
    	private int age;
    }
    class Student extends Person{
    	public String hello(){
        	return "Hello,"+name;    //编译错误：无法访问name字段；
        }
    }
    ```

  - 为了让子类可以访问父类的字段，需要用`protected`来修饰相应的字段：

  - ```java
    class Person{
    	protected String name;
    	protected int age;
    }
    class Student extends Person{
    	public String hello(){
        	return "Hello,"+name;    //OK!
        }
    }
    ```

  - `protected`关键字可以把字段和方法的访问权限控制在**继承树内部**，一个`protected`字段和方法可以被其子类，及子类的子类所访问。

- `super`关键字表示父类。子类引用父类的字段时，可以用`super.fieldName`。

  - 子类**不会继承**任何父类的构造方法；子类默认的构造方法是编译器自动生成的，不是继承的。即若父类没有默认的构造方法，子类就必须**显示**调用`super()`并给出参数以便让编译器定位到父类的一个合适的构造方法。


## 包

- 其实就是**文件夹**；
- 作用：对**类**进行分类管理；
- 包的定义格式：`package 包名;`  (多级包用.分开)
- 没有定义包名的`class`，它使用的是默认包，非常容易引起名字冲突，因此，**不推荐**不写包名的做法；
- 【包作用域】：位于同一个包的类，可以访问包作用域的字段和方法。不用`public`、`protected`、`private`修饰的字段和方法就是包作用域。

## 作用域

- 修饰符：`public`、`protected`、`private`；

  - `public`:定义为public的class、interface可以被其他任何类访问；

    ```java
    package abc;
    public class Hello{
    	public void hi(){
        }
    }
    ```

    上述Hello是public，因此可以被其他包的类访问：

    ```java
    package xyz;
    class Main{
    	void foo(){
        	//Main可以访问Hello
        	Hello h = new Hello();
        }
    }
    ```

    定义为public的`field`、`method`可以被其他类访问，前提是首先由访问`class`的权限；

  - `private`:定义为private的field、method无法被其他类访问：

    ```java
    package abc;
    public class Hello{
        public void hello(){
            this.hi();
        }
        //hi()不能被其他类调用
    	private void hi(){
        }
    }
    ```

    private的访问权限被限定在class内部，且与方法声明顺序无关。推荐把private方法放在后面，因为public方法定义了**类对外**提供的功能，阅读代码时应该先关注public方法；

    由于Java支持嵌套类，如果一个类**内部**还定义了嵌套类，嵌套类拥有访问private的权限：

    ```java
    public class Main {
        public static void main(String[] args) {
            Inner i = new Inner();
            i.hi();
        }
    
        // private方法:
        private static void hello() {
            System.out.println("private hello!");
        }
    
        // 静态内部类:
        static class Inner {
            public void hi() {
                Main.hello();
            }
        }
    }
    ```

  - `protected`:作用于**继承关系**。定义为protected的字段和方法可以被**子类**访问，以及子类的子类：

    ```java
    package abc;
    public class Hello{
        //protected方法
    	protected void hi(){
        }
    }
    ```

    ```java
    package xyz;
    class Main extends Hello{
    	void foo(){
        //可以访问protected方法
        hi();
        }
    }
    ```

- 【局部变量】在方法内部定义的变量称为局部变量，局部变量作用域从变量声明处开始到对应的块结束。方法参数也是局部变量。

- 【final】：

  - 用`final`修饰class可以阻止被继承；
  - 用`final`修饰方法可以阻止被子类覆写；
  - 用`final`修饰field可以阻止被重新赋值；
  - 用`final`修饰局部变量可以阻止被重新赋值；

- 【注】若不确定是否需要`public`,就不声明为`public`,即尽可能少地暴露对外的字段和方法；

- 【注】一个`.java`文件只能包含一个`public`类，但可以包含多个非`public`类。如果有`public`类，文件名必须和`public`类的名字相同。

## 修饰符

- **权限修饰符**

  ![image-20211222151639946](E:/software/Typora/pictures/image-20211222151639946.png)

- **状态修饰符**

  - `final`（最终态）：可以修饰成员方法，成员变量，类；

    - `final`修饰方法：表明此方法是最终方法，**不能被重写**；
    - `final`修饰变量：表明此变量是常量，**不能再次被赋值**；
    - `final`修饰类：表面此类是最终类，**不能被继承**；
    - `final`修饰局部变量：
      - 变量是**基本类型**：final修饰指的是基本类型的**数据值**不能改变；
      - 变量是**引用类型**：final修饰指的是引用类型的**地址值**不能发生改变，但地址里面的内容是可以发生改变的。
  - `static`(静态)：可以修饰成员方法、成员变量；
  
    - 被类的所有对象共享；
    - 可以通过类名调用；
    - 非静态的成员方法：
      - 能访问静态的成员变量；
      - 能访问非静态的成员变量；
      - 能访问静态的成员方法；
      - 能访问非静态的成员方法；
    - 静态的成员方法：
      - 能访问静态的成员变量；
      - 能访问静态的成员方法；
  



## 多态

- 多态：针对某个类型的方法调用，其真正执行的方法取决于运行时期实际类型的方法。

- 同一个对象，在不同时刻表现出来的不同形态；

- 多态的好处：提高了程序的**扩展性**：
  - 定义方法时，使用父类型作为参数，将来在使用的时候，使用具体的子类型参数操作；

- 多态的弊端：不能使用子类的特有功能。

- **覆写(Override)**:在继承关系中，子类如果定义了一个与父类方法签名完全相同的方法；

  - 若方法签名不同，就是**Overload**,overload方法是一个新方法；若**方法签名**相同、且**返回值**也相同，就是**Override**。

  - ```java
    class Person{
    	public void run(){
        	System.out.println("Person.run");
        }
    }
    ```

  - ```java
    //是Override;[1]方法签名相同：run;[2]返回值相同：void;
    class Student extends Person{
        @Override
    	public void run(){
        	System.out.println("Student.run");
        }
        //不是Override;因为参数不同；
        public void run(String s){...}
    	//不是Override;因为返回值不同；
        public int run(){...}
    }
    ```

- Java的实例方法调用是基于运行时的实际类型的动态调用，而非变量的声明类型。该特性在面向对象编程中称为多态。



## 抽象类

- 抽象类和抽象方法必须使用`abstract`关键字修饰

  - ```java
    public abstract class 类名{}
    ```

  - ```java
    public abstract void 方法名();
    ```

- 抽象类中不一定有抽象方法，但有抽象方法的类一定是抽象类；

- 抽象类不能直接实例化（创建对象）；可以参照多态的方式，通过子类对象实例化，叫抽象多态；

- 抽象类的子类：要么重写抽象类中的所有抽象方法、要么该子类也是抽象类；

- 抽象类本身被设计成只能用于**被继承**（无法执行抽象方法，无法实例化），因此，抽象类可以强迫子类实现其定义的抽象方法，否则编译会报错。因此，抽象方法实际上相当于定义了**“规范”**。

## 接口

- 接口的特点：

  - 接口用关键字`interface`修饰；

    - ```java
      public interface 接口名{}
      ```

  - 类实现接口用`implements`表示：

    - ```java
      public class 类名 implements 接口名{}
      ```

  - 接口不能实例化：

    - 参照多态的方式，通过实现类对象实例化，叫接口多态；
    - 多态的形式：具体类多态，抽象类多态，接口多态；
    - 多态的前提：有继承或者实现关系，有方法重写，有父(类/接口)引用指向(子/实现)类对象；

  - 接口的实现类：

    - 要么重写接口中的所有抽象方法；
    - 要么抽象类；
    
  - 接口的成员特点：
  
    - 成员变量
      - 只能是常量；
      - 默认修饰符：`public static final`；
    - 构造方法
      - 接口没有构造方法，因为接口主要是对行为进行抽象的，是没有具体存在；
      - 一个类如果没有父类，默认继承自Object类；
    - 成员方法
      - 只能是抽象方法；
      - 默认修饰符：`public abstract`；
  
  - 类和接口的关系
  
    - 类和类的关系：继承关系，只能单继承，但可以多层继承；
    - 类和接口的关系：实现关系，也可以多实现，还可以在继承一个类的同时实现多个接口；
    - 接口和接口的关系：继承关系，可以单继承，也可以多继承；
  
  - 抽象类和接口**设计理念**的区别：
  
    - 抽象类：对类抽象，包括属性、行为；
  
    - 接口：对行为抽象，主要是行为；
  
    - 门和警报的例子：
  
      - 门：都有open()和close()两个动作，可以分别使用抽象类和接口定义这个抽象概念；
  
      ```java
      //抽象类
      public abstract class Door{
      	public abstract void open();
          public abstract void close();
      }
      //接口
      public interface Door{
      	void open();
          void close();
      }
      ```
  
      - 此时，有个**新需求**，要实现**报警门**功能，在上述两种定义方法的情况下：
  
      ```java
      //第一种，通过定义抽象类——弊端：所有继承这个抽象类的子类都具有了报警功能！！！不论木门、铁门、玻璃门都具有报警功能了，显然不合理！！
      public abstract class Door{
      	public abstract void open();
          public abstract void close();
          public abstract void alarm();//在这里增加了报警的抽象方法；
      }
      
      //第二种，通过定义接口——弊端：需要用到报警功能的类就去实现这个接口里的方法，但实现这个接口意味着不仅有报警的功能，还有开和关的方法。例如单纯的一个报警器类去实现该接口，显示只需要报警方法，不需要开和关的方法，但因为报警类实现了这个接口，所以同步实现了开和关的方法，显然也不合理！！！
      public interface Door{
      	void open();
          void close();
          void alarm();
      }
      ```
  
      - open和close是门应该实现的动作，而报警属于附加的动作--行为；将报警设计为单独的接口，将门设计为一个抽象类；**报警门**继承抽象门并实现报警接口：
  
      ```java
      //抽象类--门
      public abstract class Door{
      	public abstract void open();
          public abstract void close();
      }
      //接口--报警行为
      public interface Alarm{
      	void alarm();
      }
      //报警门
      public class AlarmDoor extends Door implements Alarm{
          public void open(){}
          public void close(){}
          public void alarm(){}
      }
      ```
  
      **抽象类是对事物的抽象，而接口是对行为的抽象**
  

## 静态字段和静态方法

【静态字段】

- 在一个class中定义的字段，称之为**实例字段**；每个实例都有独立的字段，各个字段的同名字段互不影响（如ming的name和hong的name互不影响）；实例字段在每个实例中都有自己的一个独立“空间”，但是静态字段只有一个共享“空间”，所有实例都会共享该字段。
- ![image-20220104113802747](E:/software/Typora/pictures/image-20220104113802747.png)

- 对于静态字段，无论修改哪个实例的静态字段，效果都是一样的：所有实例的静态字段都被修改了，原因是静态字段**不属于实例**。虽然实例可以访问静态字段，但是它们指向的其实都是`Person class`的静态字段，所有实例**共享**一个静态字段；
- 因此不推荐用`实例变量.静态字段`去访问静态字段，因为在Java程序中，实例对象并没有静态字段。推荐用`类名.静态字段`来访问静态字段。

【静态方法】

- 调用实例方法必须通过一个实例变量，而调用静态方法不需要实例变量，通过**类名**就可以调用。

- ```java
  public class Main {
      public static void main(String[] args) {
          Person.setNumber(99);    //通过类名调用静态方法
          System.out.println(Person.number);
      }
  }
  
  class Person {
      public static int number;
      //静态方法
      public static void setNumber(int value) {
          number = value;
      }
  }
  ```



## 内部类

- 在一个类中定义一个类。

  ```java
  public class 类名{
  	修饰符 class 类名{
      }
  }
  ```

  内部类的访问特点：

  - 内部类可以直接访问外部类的成员，包括私有；
  - 外部类要访问内部类的成员，必须创建对象；

- 局部内部类：是在方法中定义的类，所以外界是无法直接使用，需要在方法内部创建对象并使用；该类可以直接访问外部类的成员，也可以访问方法内的局部变量。

- **匿名内部类**：前提：存在一个类或者接口，这里的类可以是具体类也可以是抽象类；

  - 格式：

    ```java
    new 类名或者接口名（）{
        重写方法；
    };
    ```

    **本质**：是一个继承了该类或者实现了该接口的子类匿名对象；

## 枚举类

- `enum`:为了让编译器能自动检查某个值在枚举的集合内，并且，不同用途的枚举需要不同的类型来标记，不能混用，可使用`enum`来定义枚举类：

  ```java
  public class Main{
  	public static void main(String[] args){
      	Weekday day = Weekday.SUN;
          if (day == Weekday.SAT || day == Weekday.SUN){
          	System.out.println("Work at home!");
          }else{
          	System.out.println("Work at office!");
          }
      }
  }
  
  enum Weekday{
  	SUN, MON, TUE, WED, THU, FRI, SAT;
  }
  ```

  定义枚举类通过关键字`enum`实现，只需**依次列出枚举的常量名**。

- `enum`定义枚举的优点：

  - `enum`本身带有**类型信息**，即`Weekday.SUN`类型是Weekday,编译器会自动检查出类型错误。
  - 不可能引用到非枚举的值，因为无法通过编译；
  - 不同类型的枚举**不能相互比较或者赋值**，因为类型不符。

- `enum`的比较：使用enum定义的枚举类是一种**引用类型**，引用类型的比较要使用`equals()`方法，若使用`==`比较，它比较的是两个引用类型的**变量**是否是同一个对象。但`enum`类型可以**例外**；因为，`enum`类型的每个常量在JVM中只有唯一实例，所以可以直接用`==`比较：

  ```java
  if(day == Weekday.FRI){...}    //可以比较
  if(day.equals(Weekday.SUN)){...}    //可以比较
  ```

- 通过`enum`定义的枚举类，和其他`class`有什么区别？

  - 定义的`enum`类型总是继承自`java.lang.Enum`，且**无法被继承**；
  - 只能定义出`enum`的实例，而无法通过`new`操作符创建`enum`的实例；
  - 定义的每个实例都是引用类型的**唯一实例**；
  - 可以将`enum`类型用于`switch`语句。

- `enum`实例的一些方法：

  - `name()`:返回常量名；    

    ```java
    String s = Weekday.SUN.name();	//"SUN"
    ```

  - `ordinal()`:返回定义的常量的顺序，从0开始计数；

    ```java
    int n = Weekday.MON.ordinal();	//	1
    ```

    

## 常用API

### Math

- Math包含执行基本数字运算的方法，**没有构造方法，如何使用类中的成员？**看类的成员是否都是静态的(static)，如果是，通过类名就可以直接调用。

  | 方法名                                      | 说明                                           |
  | ------------------------------------------- | ---------------------------------------------- |
  | public static int abs(int a)                | 返回参数的绝对值                               |
  | public static double ceil(double a)         | 返回大于或等于参数的最小double值，等于一个整数 |
  | public static double floor(double a)        | 返回小于或等于参数的最大double值，等于一个整数 |
  | public static int round(float a)            | 按照四舍五入返回最接近参数的int                |
  | public static int max(int a, int b)         | 返回两个int值中的较大值                        |
  | public static int min(int a, int b)         | 返回两个int值中的较小值                        |
  | public static double pow(double a,double b) | 返回a的b次幂的值                               |
  | public static double random（）             | 返回值为double的正值，[0.0, 1.0)               |

### System

- System包含几个有用的类字段和方法，它不能被实例化；

  | 方法名                                 | 说明                                       |
  | -------------------------------------- | ------------------------------------------ |
  | public static void exit(int status)    | 终止当前运行的Java虚拟机，非零表示异常终止 |
  | public static long currentTimeMillis() | 返回当前时间（以毫秒为单位）               |

### Object

- Object是类层次结构的根，每个类都可以将Object作为超类。所有类都直接或者间接的继承自该类。

  | 方法名                            | 说明                                                        |
  | --------------------------------- | ----------------------------------------------------------- |
  | public String toString()          | 返回对象的字符串表示形式。建议所有子类重写该方法,自动生成。 |
  | public boolean equals(Object obj) | 比较对象是否相等。默认比较地址，重写可以比较内容,自动生成。 |


### Arrays

- Arrays类包含用于操作数组的各种方法。

  | 方法名                           | 说明                               |
  | -------------------------------- | ---------------------------------- |
  | public static toString(int[] a)  | 返回指定数组的内容的字符串表示形式 |
  | public static void sort(int[] a) | 按照数字顺序排列指定的数组         |

- 工具类的设计思想：

  - 构造方法用`private`修饰；
  - 成员用`public static`修饰；

### 基本类型包装类

- 基本类型：`byte`，`short`，`int`，`long`，`boolean`，`float`，`double`，`char`

- 引用类型：所有`class`和`interface`类型
  - 引用类型可以赋值`null`，表示**空**，但基本类型**不能**赋值为`null`；

- 将基本数据类型封装成对象（引用类型）的好处在于可以在对象中定义更多的功能方法操作该数据；常见的操作之一：用于基本数据类型与字符串之间的转换。

  | 基本数据类型 | 包装类    |
  | ------------ | --------- |
  | byte         | Byte      |
  | short        | Short     |
  | int          | Integer   |
  | long         | Long      |
  | float        | Float     |
  | double       | Double    |
  | char         | Character |
  | boolean      | Boolean   |

- **Integer**类：

  - Integer:包装一个对象中的原始类型int的值；

- int和String的相互转换：

  - 基本类型包装类的最常见操作是：用于基本类型和字符串之间的相互转换；
  - 【int转换为String】:
    - public static String valueOf(int i)：返回int参数的字符串表示形式。该方法是String类中的方法。
  - 【String转换为int】：
    - public static int parselnt(String s):将字符串解析为int类型。该方法是Integer类中的方法。

- 自动装箱和拆箱：

  - 装箱：把基本数据类型转换为对应的包装类类型；

  - 拆箱：把包装类类型转换为对应的基本数据类型；

    ```java
    Integer i = 100;    //自动装箱
    i += 200;    //i=i+200; i+200自动拆箱； i=i+200;是自动装箱
    ```

    【注】：在使用包装类类型的时候，如果做操作，最好先判断是否为**null**，推荐只要是对象，在使用前就必须进行不为**null**的判断。



## 日期类

- 【Date】类：代表一个特定的时间，精确到毫秒

  Date类的构造方法：

  | 方法名                 | 说明                                                         |
  | ---------------------- | ------------------------------------------------------------ |
  | public Date()          | 分配一个Date对象，并初始化，以便它代表它被分配的时间，精确到毫秒； |
  | public Date(long date) | 分配一个Date对象，并将其初始化为表示从基准时间其指定的毫秒数； |

  Date类的常用方法：

  | 方法名                         | 说明                                               |
  | ------------------------------ | -------------------------------------------------- |
  | public long getTime()          | 获取的日期对象从1970年1月1日00:00:00到当前的毫秒值 |
  | public void setTime(long time) | 设置时间，给的是毫秒值                             |

- 【SimpleDateFormat】类：格式化和解析日期。

  - y    年；  M  月；  d    日；  H  时；  m   分；  s  秒；

    构造方法：

    | 方法名                                  | 说明                                                   |
    | --------------------------------------- | ------------------------------------------------------ |
    | public SimpleDateFormat()               | 构造一个SimpleDateFormat，使用默认模式和日期格式       |
    | public SimpleDateFormat(String pattern) | 构造一个SimpleDateFormat使用给定的模式和默认的日期格式 |

  - 格式化（从Date到String）:

    public final String format(Date date):将日期格式化成日期/时间字符串；

  - 解析（从String到Date）

    public Date parse(String source):从给定字符串的开始解析文本以生成日期。

  - 【Calendar】类：为某一时刻和一组日历字段之间的转换提供了一些方法，并为操作日历字段提供了一些方法；`Calendar`提供了一个类方法`getInstance`用于获取`Calendar`对象，其日历字段已使用当前日期和时间初始化：

    ```java
    Calendar rightNow = Calendar.getInstance();
    ```

    Calendar的常用方法：

    | 方法名                                               | 说明                                                   |
    | ---------------------------------------------------- | ------------------------------------------------------ |
    | public int get(int field)                            | 返回给定日历字段的值                                   |
    | public abstract void add(int field, int amount)      | 根据日历的规则，将指定的时间量添加或减去给定的日历字段 |
    | public final void set(int year, int month, int date) | 设置当前日历的年月日                                   |

## 异常

- **异常**：程序出现了不正常的情况；

  ```mermaid
  graph TD;
  	Throwable-->Error;
  	Throwable-->Exception;
  	Exception-->RuntimeException;
  	Exception-->非RuntimeException;
  ```

  Error:严重问题，不需要处理；

  Exception:称为异常类，它表示程序本身可以处理的问题；

  - RuntimeException:在编译期是不检查的，出现问题后，需要我们回来修改代码；
  - 非RuntimeException:编译期就必须处理的，否则程序不能通过编译，更不能正常运行；

- **异常处理**：

  ```java
  try{
  	可能出现异常的代码;
  }catch(异常类名 变量名){
  	异常的处理代码;
  }
  ```

   程序从try里面的代码开始执行，出现异常，会自动生成一个异常类对象，该异常对象将被提交给Java运行时系统；当Java运行时系统接收到异常对象时，会到catch中去找匹配的异常类，找到后进行异常的处理；执行完毕之后，程序还可以继续往下执行。

- `Throwable`的成员方法

  | 方法名                        | 说明                            |
  | ----------------------------- | ------------------------------- |
  | public String getMessage()    | 返回此throwable的详细消息字符串 |
  | public String toString()      | 返回此可抛出的简短描述          |
  | public void printStackTrace() | 把异常的错误信息输出在控制台    |

- 编译时异常和运行时异常的区别：

  - Java中的异常被分为两大类：*编译时异常*和*运行时异常*，也被称为*受检异常*和*非受检异常*；所有的RuntimeException类及其子类被称为运行时异常，其他的异常都是编译时异常。
    - 编译时异常：必须显示处理，否则程序就会发生错误，无法通过编译；
    - 运行时异常：无需显示处理，也可以和编译时异常一样处理。

- 异常处理之`throws`：

  ```java
  throws 异常类名;    //该格式是跟在方法的括号
  ```

  
