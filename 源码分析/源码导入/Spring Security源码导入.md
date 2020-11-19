构建Spring Security时，可能会遇到下面的错误：

```
Could not HEAD 'https://repo.spring.io/libs-snapshot/org/jetbrains/kotlin/kotlin-compiler-embeddable/1.4.10/kotlin-compiler-embeddable-1.4.10.pom'. Received status code 401 from server: Unauthorized
```

参考[[Notice of Permissions Changes to repo.spring.io, Fall and Winter, 2020](https://spring.io/blog/2020/10/29/notice-of-permissions-changes-to-repo-spring-io-fall-and-winter-2020)](https://spring.io/blog/2020/10/29/notice-of-permissions-changes-to-repo-spring-io-fall-and-winter-2020)，从`repo.spring.io`进行下载依赖包时，会需要验证权限。



注意：目前好像无法直接构建





