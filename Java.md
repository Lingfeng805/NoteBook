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