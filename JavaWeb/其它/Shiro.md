Shiro的三个核心组件：



* Subject：代表当前操作用户；
* SecurityManager：Shiro框架的核心，Shiro通过SecurityManager来管理内部组件实例，并通过它来提供安全管理的各种服务；
* Realm：充当Shiro与应用安全数据间的“桥梁”或者“连接器”，即当对用户执行认证（登录）和授权（访问控制）验证时，Shiro会从应用配置的Realm中查找用户及其权限信息。