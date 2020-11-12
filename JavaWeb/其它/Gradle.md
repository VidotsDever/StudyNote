如何导入一个Gradle工程：

每个Gradle工程都有一个`gradle`目录，里面有两个文件，`gradle-wrapper.jar`和`gradle-wrapper.properties`。其中，`gradle-wrapper.properties`内容如下：

```
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-6.6.1-bin.zip
zipStoreBase=GRADLE_USER_HOME 上面的gradle下载存放的地方，可以配置环境变量
zipStorePath=wrapper/dists 
```



运行`gradlew.bat`，首先会下载上述文件中指定地址的gradle，可以自己下载，然后放在指定的目录。

更换镜像的方法：在gradle的解压目录下的`init.d`目录下，放置`init.gradle，内容如下

```
allprojects {
    repositories {
        maven {
            url "http://maven.aliyun.com/nexus/content/groups/public"
        }
    }
}
```

