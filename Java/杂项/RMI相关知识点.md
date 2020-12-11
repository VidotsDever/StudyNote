`At the most basic level, RMI is Java's remote procedure call (RPC) mechanism.`

注意：`RPC调用一个方法`

RMI `registry`是服务器用来注册服务的地方，以及让客户端查询服务的地方；就像RMI服务器和客户端之间的代理。

![](D:\Github\StudyNote\assets\java-rmi.png)

#### 实现步骤：

* 定义远程接口(客户端和服务器之间的约定)

```
import java.rmi.Remote;
import java.rmi.RemoteException;
// 为了让该可接口可导出，需要继承自Remote方法
public interface Greeting extends Remote
{
  public String greet(String name) throws RemoteException;
}
```

* 实现服务器端对象：

```
public class GreetingObject implements Greeting
{
  private String fmtString = "Hello, %s";
  public String greet(String name)
  {
    return String.format(this.fmtString, name);
  }
}
```

* 服务器端的Main方法：

```
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;
import java.rmi.server.UnicastRemoteObject;
public class Main
{
  static public void main(String[] args) throws Exception
  {
    if ( args.length == 0 ) {
      System.err.println("usage: java Main port#");
      System.exit(1);
    }
    int index = 0;
    int port = Integer.parseInt(args[index++]);
    String name = "Greeting";
    
    // 创建服务端对象
    Greeting greeting = new GreetingObject();
    
    // 从RMI运行时获取服务端对象的桩，桩实现了和服务端对象一样的接口，客户端使用桩来调用服务端对象的方法
    Greeting stub = (Greeting)UnicastRemoteObject.exportObject(greeting, 0);
    
    Registry registry = LocateRegistry.getRegistry(port);
    
    // 获取到桩后，将其提交给注册中心；注册中心将桩绑定到命名服务，当客户端请求服务时，它回接收到桩
    registry.rebind(name, stub);
    
    System.out.println("Greeting bound to \"" + name + "\"");
  }
}
```

#### 实现客户端：

```
package client;
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;
import server.Greeting;
public class Client
{
  static public void main(String[] args) throws Exception
  {
    if ( args.length != 3 ) {
      System.err.println("usage: java Client host port myName");
      System.exit(1);
    }
    int index = 0;
    String host = args[index++];
    int port = Integer.parseInt(args[index++]);
    String myName = args[index++];
    String name = "Greeting";
    // 获取注册中心的引用
    Registry registry = LocateRegistry.getRegistry(host, port);
    // 查询注册中心上的服务，lookup()方法返回桩，用于调用服务
    Greeting greeting = (Greeting) registry.lookup(name);
    System.out.println(name + " reported: " + greeting.greet(myName));
  }
}
```

#### 启动RMI Registry

RMI Registry是Java虚拟机携带的一个程序，运行命令：`rmiregistry port`；

注意：需要让RMI Registry知道远程接口的位置。





















