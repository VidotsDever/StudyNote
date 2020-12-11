**JVM**是通过软件来模拟Java字节码的指令集，是Java程序的运行环境。(物理机器上是CPU来执行指令集)

![](D:\Github\StudyNote\assets\jvm-overview.png)



#### JVM主要功能：

* 通过ClassLoader寻找和装载class文件；
* 解释字节码成为指令并执行，提供class文件的运行环境；
* 进行运行期间的内存分配和垃圾回收；
* 提供与硬件交互的平台；



#### JVM规范

JVM规范定义的主要内容：

* 字节码指令集(相当于中央处理器CPU)；
* class文件的格式；
* 数据类型和值；
* 运行时数据区；
* 栈帧；
* 特殊方法；
* 类库；
* 异常；
* 虚拟机的启动、加载、链接和初始化；

Java虚拟机的指令由一个字节长度的、代表着某种特定操作含义的操作码以及跟随其后的零至多个代表此操作所需参数的操作数所构成。

#### class文件格式

`class`文件是JVM的输入，Java虚拟机规范中定义了`class`文件的结构。`class`文件是一组以8字节为单位的字节流，各个数据项目按顺序紧凑排列。



`class`文件格式里面只有两种类型：无符号数和表

* 无符号数：基本数据类型，以u1、u2、u4、u8来代表几个字节的无符号数；
* 表：由多个无符号数和其它表构成的符合数据类型，通常以"_info"结构。

![](D:\Github\StudyNote\assets\jvm-class.png)

查看`class`文件的方式：

* 使用十六进制编辑器；
* 使用`javap`命令将`class`文件反编译为字节码指令集

```
javap -verbose com.vidots.Main // 在com包所在的目录下运行

javap工具生成非正式的虚拟机汇编语言，格式如下：
<index> <opcode> [<operand1>]...[<comment>] 
如：3: ldc(助记码)  #3(操作数)   // String welcome

<index>：指令操作码在数组中的下标；
<opcode>：指令的助记码
```

#### 阅读class字节码

* 常量池
* 类定义和属性
* 方法和方法调用



#### ASM

ASM是一个Java字节码操纵框架，它能被用来动态生成类或者增强既有类(类似AOP)的功能。

目前许多框架如cglib、Hibernate、Spring都直接或间接地使用AASM操作字节码。



ASM编程模型：

* **Core API**:提供了基于事件形式的编程模型。该模型不需要一次性将整个类的结构读取到内存中，因此这种方式更快，需要更少的内存，但这种编程方式难度较大

```
操纵节码的功能基于 ClassVisitor 接口，该接口中的每个方法对应了class文件中的每一项；

ASM提供了三个基于 ClassVisitor接口的类来实现 class 文件的生成和转换：
1. ClassReader：解析一个类的class字节码；
2. ClassAdapter：ClassVisitor的实现类，实现要变化的功能；
3. ClassWriter：ClassVisitor的实现类，可以用来输出变化后的字节码；
```



* **Tree API**:提供了基于树形的编程模型。该模型需要一次性将一个类的完整结构全部读取到内存当中，所以这种方法需要更多的内存，这种编程方式较简单













































