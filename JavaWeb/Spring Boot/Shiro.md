#### 安全框架

* 自定义请求映射，接收用户名、密码，响应token数据；
* 接收用户请求(携带前一步的token)，利用过滤器对该token进行验证，让用户处于登录状态；



Shiro的三个核心组件：



* Subject：代表当前操作用户；官方描述：`A Subject is just a security-specific “view” of an application User.`
* SecurityManager：Shiro框架的核心，Shiro通过SecurityManager来管理内部组件实例，并通过它来提供安全管理的各种服务；
* Realm：充当Shiro与应用安全数据间的“桥梁”或者“连接器”，即当对用户执行**认证**（登录）和**授权**（访问控制）验证时，**Shiro会从应用配置的Realm中查找用户及其权限信息**。

在几乎所有的环境中，都可以通过下面的调用来获取当前正在操作的用户：

```
Subject currentUser = SecurityUtils.getSubject();
```

Subject实例代表当前的用户，如果没有登录，则该用户就是匿名的，直到该用户登陆过一次。



#### 相关的类

* `AuthenticationToken`：在验证期间，一个用户提交的身份信息(`principals`和`credentials`)的结合体。































