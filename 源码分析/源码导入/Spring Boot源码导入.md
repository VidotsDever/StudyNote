先进行如下操作，然后再导入到IDEA中



#### 修改工程使用的Gradle指向

在工程的`gradle`目录下的`gradle-wrapper.properties`文件中，修改`distributionUrl`值，令其指向本地下载好的gradle压缩包。

```
distributionUrl=file:///d:/gradleDownloads/wrapper/dists/gradle-6.7-bin.zip
```



#### 修改buildSrc工程

在`buildSrc`工程下的`build.grdle`和`settings.gradle`文件中，添加阿里云的镜像地址：

```
maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }
maven { url 'https://maven.aliyun.com/nexus/content/repositories/jcenter' }
maven { url "https://repo.spring.io/plugins-release" }
```



#### 修改根目录

在根目录下的`settings.gradle`文件中，添加阿里云的镜像地址：

```
maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }
maven { url 'https://maven.aliyun.com/nexus/content/repositories/jcenter' }
maven { url "https://repo.spring.io/plugins-release" }
```



在根目录下的`build.gradle`文件中，按照如下方式添加：

```
在文件顶部添加：
buildscript {
	repositories {
		maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }
		maven { url 'https://maven.aliyun.com/nexus/content/repositories/jcenter' }
		maven { url "https://repo.spring.io/plugins-release" }
	}
}

在其它地方添加：
maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }
		maven { url 'https://maven.aliyun.com/nexus/content/repositories/jcenter' }
		maven { url "https://repo.spring.io/plugins-release" }
```

