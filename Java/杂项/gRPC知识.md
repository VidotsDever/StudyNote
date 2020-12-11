gRPC使用`protocol buffers`作为它的接口定义语言和底层消息交互格式；在gRPC中，客户端应用可以直接调用服务器应用上的方法。在**服务端**，实现接口并运行gPRC服务器来处理客户端调用；在**客户端**，客户端持有桩，该桩提供与服务器一样的方法。

默认，gPRC使用`Protocol Buffers`来序列化结构式的数据；`Protocol Buffers`数据会被组织成消息(message)，每个消息是一条逻辑记录，由一系列键值对组成，即字段。

```
message Person {
  string name = 1;
  int32 id = 2;
  bool has_ponycopter = 3;
}

该消息定义在文件中，文件名后缀.proto
```

在`.proto`文件中定义gPRC服务，方法参数和返回值类型都被定义为`Protocol Buffers`消息：

```
service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

gPRC使用`protoc`来从`.proto`文件中产生代码：客户端、服务器代码，以及普通的protocol buffer代码(用于填充、序列化和获取消息)。



#### 开发步骤

* 在`.proto`文件中定义服务；
* 使用`protocol buffer`编译器产生服务器和客户端代码；
* 创建服务器应用，实现产生的服务接口，并启动gPRC服务器；
* 创建客户端应用，使用产生的桩来进行RPC调用；

































