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



#### class文件格式

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



* 2



