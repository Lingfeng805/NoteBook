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



### do while循环



### for循环

```java
for (初始化语句;条件判断语句;条件控制语句){
	循环体语句;	
}
```



### break和continue



