Spring Boot `autoconfiguration` 基于出现在`classpath`路径上的依赖项来自动配置Spring应用。

#### 创建自定义的Auto-Configuration



[Create a Custom Auto-Configuration with Spring Boot](https://www.baeldung.com/spring-boot-custom-auto-configuration)

为了创建自定义的**Auto-Configuration**，需要创建一个使用**@Configuration**注解的类。比如：

```
@Configuration
public class MySQLAutoconfiguration {
    //...
}
```



下一步是将上述定义的类注册成**Auto-Configuration**候选者，在`resources/META-INF/spring.factories`文件中添加类名(只有类需要添加)：

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.vidots.autoconfiguration.MySQLAutoconfiguration
```



使用`@Conditional`注解的类或bean可以设计**Auto-Configuration**。

注意：只有自动装配的bean没有定义在应用中，**Auto-Configuration**才会生效。如果你定义了bean，那么默认的就会被覆盖。



* Class Conditions

```
使用注解@ConditionalOnClass：如果指定的类出现，自动装配才会生效；
使用注解@ConditionalOnMissingClass：如果指定的类不出现，自动装配才会生效

@Configuration
@ConditionalOnClass(DataSource.class)
public class MySQLAutoconfiguration {
    //...
}
```



* Bean Conditions

```
使用注解@ConditionalOnBean：如果指定的bean出现，自动装配才会生效；
使用注解@ConditionalOnMissingBean：如果指定的bean不出现，自动装配才会生效

@Bean
@ConditionalOnBean(name = "dataSource")
@ConditionalOnMissingBean
public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
    LocalContainerEntityManagerFactoryBean em
      = new LocalContainerEntityManagerFactoryBean();
    em.setDataSource(dataSource());
    em.setPackagesToScan("com.baeldung.autoconfiguration.example");
    em.setJpaVendorAdapter(new HibernateJpaVendorAdapter());
    if (additionalProperties() != null) {
        em.setJpaProperties(additionalProperties());
    }
    return em;
}
```



* Property Conditions

```如果S
@ConditionalOnProperty：用来声明如果Spring环境变量出现，且值匹配，自动装配才会生效

@PropertySource("classpath:mysql.properties")
public class MySQLAutoconfiguration {
    @Bean
    @ConditionalOnProperty(
      name = "usemysql", 
      havingValue = "local")
    @ConditionalOnMissingBean
    public DataSource dataSource() {
       DriverManagerDataSource dataSource = new DriverManagerDataSource();
 
       dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
       dataSource.setUrl("jdbc:mysql://localhost:3306/myDb?createDatabaseIfNotExist=true");
       dataSource.setUsername("mysqluser");
       dataSource.setPassword("mysqlpass");
 
       return dataSource;
}
}
```



* Resource Conditions

```
@ConditionalOnResource ：只有指定的资源出现时候，自动装配才会生效

@ConditionalOnResource(resources = "classpath:mysql.properties")
@Conditional(HibernateCondition.class)
Properties additionalProperties() {
    Properties hibernateProperties = new Properties();
 
    hibernateProperties.setProperty("hibernate.hbm2ddl.auto", 
      env.getProperty("mysql-hibernate.hbm2ddl.auto"));
    hibernateProperties.setProperty("hibernate.dialect", 
      env.getProperty("mysql-hibernate.dialect"));
    hibernateProperties.setProperty("hibernate.show_sql", 
      env.getProperty("mysql-hibernate.show_sql") != null 
      ? env.getProperty("mysql-hibernate.show_sql") : "false");
    return hibernateProperties;
}
```



* Custom Conditions

```
通过继承SpringBootCondition类，并且重写getMatchOutcome()方法来自定义条件

static class HibernateCondition extends SpringBootCondition {
 
    private static String[] CLASS_NAMES
      = { "org.hibernate.ejb.HibernateEntityManager", 
          "org.hibernate.jpa.HibernateEntityManager" };
 
    @Override
    public ConditionOutcome getMatchOutcome(ConditionContext context, 
      AnnotatedTypeMetadata metadata) {
 
        ConditionMessage.Builder message
          = ConditionMessage.forCondition("Hibernate");
        return Arrays.stream(CLASS_NAMES)
          .filter(className -> ClassUtils.isPresent(className, context.getClassLoader()))
          .map(className -> ConditionOutcome
            .match(message.found("class")
            .items(Style.NORMAL, className)))
          .findAny()
          .orElseGet(() -> ConditionOutcome
            .noMatch(message.didNotFind("class", "classes")
            .items(Style.NORMAL, Arrays.asList(CLASS_NAMES))));
    }
}

@Conditional(HibernateCondition.class)
Properties additionalProperties() {
  //...
}
```



* Application Conditions

```
通过添加@ConditionalOnWebApplication 或 @ConditionalOnNotWebApplication 注解来说明：只有在Web上下文之内/外，自动装配才有效。
```

