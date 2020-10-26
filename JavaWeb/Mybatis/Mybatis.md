`@Mapper`注解需要在每一个mapper上添加；而`@MapperScan`直接将mapper所在的包扫描进去。



加入Mybatis依赖：

```
<dependency>
 <groupId>org.mybatis.spring.boot</groupId>
 <artifactId>mybatis-spring-boot-starter</artifactId>
 <version>1.1.1</version>
</dependency>
```



在application.properties文件中添加数据源配置：

```
spring.datasource.url= jdbc:mysql://localhost:3306/polling_app?useSSL=false&serverTimezone=UTC&useLegacyDatetimeCode=false
spring.datasource.username= root
spring.datasource.password= weichuang
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```



创建模型：

```
public class UserEntity implements Serializable {
    private Long id;
    private String name;
    private Integer age;
    private Byte status;
    @JsonFormat(pattern="yyyy-MM-dd HH:mm:ss", timezone="GMT+8")
    private Date createTime;
    @JsonFormat(pattern="yyyy-MM-dd HH:mm:ss", timezone="GMT+8")
    private Date updateTime;
    ...get和set方法
}
```





#### 基于注解



创建基于注解的mapper：

```
@Mapper // 这里的@Mapper注解的功能可以使用@MapperScan来代替
public interface UserMapper {

    @Select("SELECT id, user_name AS name, age, status, create_time AS createTime, update_time AS updateTime FROM user WHERE user_name = #{name}")
    UserEntity findByName(@Param("name") String name);
    
    @Insert("INSERT INTO user VALUES (NULL, #{name}, #{age}, 1, NOW(), NOW())")
    int insert(@Param("name") String name, @Param("age") Integer age);
    
}
```



#### 基于XML



在application.properties文件中指定xml所在的位置：

```
mybatis.mapper-locations=classpath:mapper/*.xml
```



在`resources`目录下创建`mapper/user.xml`，内容如下：

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.vidots.fast.mapper.UserMapper" >
    <resultMap id="BaseResultMap" type="com.vidots.fast.model.UserEntity" >
        <id column="id" property="id" jdbcType="BIGINT" />
        <result column="user_name" property="name" jdbcType="VARCHAR" />
        <result column="age" property="age" jdbcType="BIGINT" />
        <result column="status" property="status" jdbcType="INTEGER" />
        <result column="create_time" property="createTime" jdbcType="DATE" />
        <result column="update_time" property="updateTime" jdbcType="DATE" />
    </resultMap>


    <select id="findByName" resultMap="BaseResultMap"  >
        SELECT id, user_name, age, status, create_time, update_time
        FROM user WHERE user_name=#{name}
    </select>

    <insert id="insert" >
       INSERT INTO
            user
            (user_name,age)
        VALUES
            (#{name}, #{age})
    </insert>

</mapper>
```



创建mapper：

```
@Mapper
public interface UserMapper {
    UserEntity findByName(@Param("name") String name);
    int insert(@Param("name") String name,@Param("age") Integer age);
}
```

