![](D:\Github\StudyNote\assets\spring-cloud-微服务的各种术语.png)



#### Eureka

Eureka是SpringCloud Netflix的核心子模块，包括Eureka Server和Eureka Client。

* Eureka Server：即注册中心，提供服务注册服务，存储所有可用服务节点；
* Eureka Client：用于简化与Eureka Server通讯的复杂度；



<img src="D:\Github\StudyNote\assets\spring-cloud-微服务的各种术语.png" style="zoom:75%;" />



服务消费者根据`服务名称`从注册中心获取服务提供者的IP地址和端口号：

```
@Autowired
private LoadBalancerClient eurekaClient;

ServiceInstance choose = eurekaClient.choose("hello-service-provider");
String hostname = choose.getHost();
int port = choose.getPort();

String uri = "/provider/sayHello?message=" + message;
String url = "http://" + hostname + ":" + port + uri;
String result = restTemplate.getForObject(url, String.class);
return result;
```



服务注册：

* Eureka Client再第一次心跳时向Eureka Server注册；
* 注册时会提供诸多自身元数据：主机名、端口、健康指标URL等；

服务续约：

* Eureka Client通过发送心跳进行续约；
* 默认情况下每30秒发送一次心跳；
* 如果90秒内Eureka Server未收到续约，则进行服务剔除；

获取注册列表信息：

* Eureka Client会缓存从Eureka Server获取的注册表信息；
* Eureka Client会定期更新注册表信息(默认30秒)；
* Eureka Client会处理注册表的合并等内容；



Eureka注册慢的原因：

* 注册慢的根本原因在于Eureka的AP特性；
* Eureka Client延迟注册，默认30秒；
* Eureka Server的响应缓存，默认30秒；
* Eureka Server的缓存刷新，默认30秒；



Eureka的自我保护：

* Eureka Server会自动更新**续约更新**阈值；
* Eureka Server续约更新频率低于阈值则进入保护模式；
* 自我保护模式下Eureka Server不会剔除任何注册信息；



**Dubbo项目经典架构**：

<img src="D:\Github\StudyNote\assets\dubbo-1.png" style="zoom:87%;" />



**SpringCloud经典架构：**

<img src="D:\Github\StudyNote\assets\spring-cloud-项目架构.png" style="zoom:47%;" />



#### Ribbon

Ribbon适用于同一服务有多个provider场景；

* Ribbon是客户端(一般是服务消费者)负载均衡器；
* Ribbon核心功能：服务发现；
* Ribbon核心功能：服务选择规则；
* Ribbon核心功能：服务监听；



Ribbon功能实现：从注册中心获取serverlist，调用负载均衡算法；



Ribbon与Eureka整合(引入Eureka Client依赖后，Ribbon自动添加到依赖项中)：

* Ribbon与Eureka天然无缝整合；
* 通过@LoadBalanced提供负载均衡支持；
* 通过ribbon.eureka.enabled=false禁用Eureka

```
@Bean
@LoadBalanced
public RestTemplate restTemplate() {
   return new RestTemplate();
}

String uri = "/provider/sayHello?message=" + message;
String url = "http://hello-service-provider"  + uri; //调用时直接用服务名替代IP和端口号
String result = restTemplate.getForObject(url, String.class);
```



配置Ribbon的负载均衡规则：

```
@Bean
public IRule iRule() {
// return new RandomRule();
   return new RoundRobinRule();
}
```



IPing：探测服务存活状态；



#### Hystrix

* Hystrix是用于处理延迟和容错的开源库；
* Hystrix主要用于避免级联故障，提高系统弹性；
* Hystrix解决了由于扇出导致的"雪崩效应"；
* Hystrix的核心是隔离术和熔断机制；



Hystrix两种命令模式：

* `HystrixCommand`：会以隔离的形式完成`run`方法调用(在新的线程中执行)；

* `HystrixObservableCommand`：使用当前线程进行调用(在主线程中调用，则在main线程中执行)；



![](D:\Github\StudyNote\assets\spring-clou-hystrix-command.png)

Hystrix请求缓存：

* Hystrix支持将请求结果进行本地缓存
* 通过覆盖重新 getCacheKey方法来判断是否取出缓存(来自HystrixCommand类)
* 请求缓存要求请求必须在同一个上下文
* 可以用过 RequestcacheEnabled开启请求缓存

Hystrix请求合并：

* Hystrix支持将多个请求合并成一次请求
* Hystrix请求合并要求两次请求必须足够“近”
* 请求合并分为局部合并和全局合并两种
* Collapser可以设置相关参数

Hystrix隔离介绍

* Hystrix提供了信号量和线程两种隔离手段；
* 线程隔离会在单独的线程中执行业务逻辑
* 信号量隔离在调用线程上执行



Hystrix降级处理

* 降级是一种无奈的选择，就是俗称的备胎；
* Hystrix降级需要实现fallback方法；(fallback方法与run方法一一对应)
* ObservableCommand降级实现 resumeWithFallback方法

Hystrix降级触发原则

* HystrixBadRequestException以外的异常
* 运行超时或熔断器处于开启状态
* 线程池或信号量已满

熔断器介绍：

* 熔断器是一种开关，用来控制流量是否执行业务逻辑
* 熔断器核心指标:快照时间窗
* 熔断器核心指标:请求总数阈值
* 熔断器核心指标:错误百分比阈值



熔断器状态：

* 熔断器开启:所有请求都会进入 fallback方法
* 熔断器半开启:闯间歇性让请求触发run方法
* 熔断器关闭:正常处理业务请求
* 默认情况下熔断器开启5秒后进入半开启状态



Hystrix在Spring Cloud中使用：

```
在启动类上使用注解：
@EnableHystrix
@EnableHystrixDashboard

在控制器方法上使用注解，并添加对应的降级时处理的方法：该方法的返回值和参数类型与控制器方法要相同
public BaseResponseVO fallbackMethod(BasePageVO basePageVO) throws CommonServiceException{
        return BaseResponseVO.success();
}

@HystrixCommand(fallbackMethod = "fallbackMethod",
  commandProperties = {
     @HystrixProperty(name = "execution.isolation.strategy", value = "THREAD"),
     @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value= "1000"),
     @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),
     @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "50")
  },
  hreadPoolProperties = {
     @HystrixProperty(name = "coreSize", value = "1"),
     @HystrixProperty(name = "maxQueueSize", value = "10"),
     @HystrixProperty(name = "keepAliveTimeMinutes", value = "1000"),
     @HystrixProperty(name = "queueSizeRejectionThreshold", value = "8"),
     @HystrixProperty(name = "metrics.rollingStats.numBuckets", value = "12"),
     @HystrixProperty(name = "metrics.rollingStats.timeInMilliseconds", value = "1500")
  },
  ignoreExceptions = CommonServiceException.class)
@RequestMapping(value = "",method = RequestMethod.GET)
public BaseResponseVO describeCinemas(BasePageVO basePageVO) throws CommonServiceException {

        IPage<DescribeCinemasRespVO> describeCinemasRespVOIPage = cinemaServiceAPI.describeCinemas(basePageVO.getNowPage(), basePageVO.getPageSize());

        if(basePageVO.getNowPage()>10000){
            throw new CommonServiceException(400,"nowPage太大了，不支持此处理");
        }

        return BaseResponseVO.success();
}

```



查看Histrix Dashboard：

```
在application.yml中添加：
management:
  endpoints:
    web:
      exposure:
        include: "*"
        
访问：http://ip:port/hystrix
```



#### Feign



* Feign是一个非常好用的HTTP客户端；
* Feign很大程序上简化了HTTP调用方式
* Feign很好的弥补了Springcloud的HTTP调用缺陷



```
在启动类上添加注解：
@EnableFeignClients

Feign支持多种HTTP调用形式，比如SpringMVC的参数：@RequestMapping、@RequestParam、@PathVariable、@RequestHeader、@RequestBody

创建业务接口(Feign会自动生成接口实现)：

@FeignClient(name = "provider-invoke", url = "http://localhost:7101") // url是需要调用的服务的地址
public interface ProviderAPI {
    @RequestMapping(value = "/provider/sayHello", method = RequestMethod.GET) // SpringMVC形式的调用
    String invokeProviderController(@RequestParam("message") String message);
}

@Resource
private ProviderAPI providerAPI;

String result = providerAPI.invokeProviderController(message);
```

Feign进阶参数：

* primary:多实现时指定优先级
* configuration:自定义 Feign配置
* Fallback和 Fallbackfactory:降级统一处理

<img src="D:\Github\StudyNote\assets\spring-cloud-feign-config.png" style="zoom:55%;" />



默认可以SpringMVC形式调用的原因是feignContract的默认配置是SpringMvcContract；可以修改相应的配置，不过自定义配置要在Spring作用域之外：

```
@Configuration
public class FeignConf {

    @Bean
    public Contract contract(){
        return new feign.Contract.Default();
    }

}

配置好后，可以如下：

@RequestLine("GET /sayhello?message={message}")
String invokerProviderController(@Param("message") String message);
```



Feign多组件集成：

* Feign可以集成 Ribbon实现负载均衡

```
配置Ribbon、Feign后：

@FeignClient(name = "film-service") // url是需要调用的服务的地址
public interface ProviderAPI {
    @RequestMapping(value = "/provider/sayHello", method = RequestMethod.GET) // SpringMVC形式的调用
    String invokeProviderController(@RequestParam("message") String message);
}

如果@FeignClient不提供url，则name字段的值就是服务名称
```



* Feign可以集成 Hystrix实现命令封装

```
在application.yml中配置：
feign:
  hystrix:
    enabled: true
    
@Service
public class ProviderFallback implements ProviderAPI {
    @Override
    public String invokeProviderController(String message) {
        return "fake result";
    }
}

@FeignClient(name = "hello-service-provider", fallback = ProviderFallback.class)
public interface ProviderAPI {
    @RequestMapping(value = "/provider/sayHello", method = RequestMethod.GET)
    String invokeProviderController(@RequestParam("message") String message);
}
```



Feign可以集成 Hystrix实现业务降级

Feign之Http性能优化：

* Feign默认使用的JDK自带的HTTP方式
* Feign最大的优化点是更换HTTP底层实现
* 目前Apache HTTPClient是一个非常好的选择

如果替换成Apache HTTPClient：

```
添加依赖项：
<dependency>
      <groupId>io.github.openfeign</groupId>
      <artifactId>feign-httpclient</artifactId>
</dependency>

在application.yml中添加：
feign:
  httpclient:
    enabled: true
```



Feign继承

* 微服务的目标是大量复用， Feign会导致重复工作量
* Feign提供了继承特性帮助我们解决这个问题
* 接口复用最多只能有一层，切忌多继承

```
建议单独建立一个模块，存放Feign的公众接口：
public interface FilmFeignApis {
    @RequestMapping(value = "/{filmId}",method = RequestMethod.GET)
    BaseResponseVO<FilmAloneRespVO> describeFilmById(@PathVariable("filmId") String filmId);
}

在具体业务模块中，继承自Feign的公众接口：
@FeignClient(name = "film-service")
public interface FilmFeignApi extends FilmFeignApis {
}
```



#### Zuul

网关介绍

* 由于微服务“各自为政的特性”使微服务的使用非常麻烦
* 通常我们会雇佣一个“传达室大爷”作为统一入口，这就是网关
* 网关主要是实现请求转发和请求过滤

Zuul介绍

* Zuul是网关大军中的一员，目前市场使用律比较高
* Zuul除了实现请求转发和过滤，一般还作为鉴权和容错使用
* Zuul可以无缝衔接Ribbon和 Hystrix



Zuul需要对接到服务注册中心，因为它要从 服务中心获取到微服务；



```
添加依赖项；

在启动类上添加注解：
@EnableZuulProxy

在application.xml中配置路由：
zuul:
  routes:
    film-service:
      path: /film-api/**
```



请求路由：

* Zuul可以通过配置完成请求路由配置
* Zuul服务路由默认支持 serviceId作为上下文
* ignored-services可以禁用 serviceId



Zuul高层架构图：

  <img src="D:\Github\StudyNote\assets\spring-cloud-zuul-高层架构图.png" style="zoom:75%;" />



Zuul过滤器生命周期：

<img src="D:\Github\StudyNote\assets\spring-cloud-zuul-Filter生命周期.png" style="zoom:75%;" />



自定义Filter：

* 继承ZuulFilter并实现相应的方法
* 设置 Filter类型、级别和是否启用
* 开发具体的业务逻辑

```
@Slf4j
public class MyFilter extends ZuulFilter {
    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 0; // 数字越大，执行越靠后
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }
// RequestContext是线程安全的
    @Override
    public Object run() throws ZuulException {
        RequestContext requestContext = RequestContext.getCurrentContext();
        HttpServletRequest request = requestContext.getRequest();
        Enumeration<String> headerNames = request.getHeaderNames();
        while (headerNames.hasMoreElements()) {
            String headerName = headerNames.nextElement();
            log.error("header {} -- {}", headerName, request.getHeader(headerName));
        }
        return null;
    }
}


// 定义好过滤器后，需要将其交给Spring管理

@Configuration
public class ZuulConfig {

    @Bean
    public MyFilter myFilter() {
        return new MyFilter();
    }

}
```



Zuul和Hystrix整合：

```
在application.yml中添加配置：
hystrix:
  command:
    default:
      execution:
        timeout:
          enabled: true
        isolation:
          thread:
            timeoutInMilliseconds: 10000
编写降级类：

@Component
public class MyFallback implements FallbackProvider {

    /**
    * @Description: 针对哪一个路由进行降级， return可以写 *
    * @Param: []
    * @return: java.lang.String
    * @Author: jiangzh
    */
    @Override
    public String getRoute() {
        return "film-service";
    }

    /**
    * @Description: 降级时处理方式
    * @Param: [route, cause]
    * @return: org.springframework.http.client.ClientHttpResponse
    * @Author: jiangzh
    */
    @Override
    public ClientHttpResponse fallbackResponse(String route, Throwable cause) {
        return new ClientHttpResponse() {
            @Override
            public HttpStatus getStatusCode() throws IOException {
                return HttpStatus.OK;
            }

            @Override
            public int getRawStatusCode() throws IOException {
                return 200;
            }

            @Override
            public String getStatusText() throws IOException {
                return "OK";
            }

            @Override
            public void close() {

            }

            /**
            * @Description: 业务降级处理方式
            * @Param: []
            * @return: java.io.InputStream
            * @Author: jiangzh
            */
            @Override
            public InputStream getBody() throws IOException {
                BaseResponseVO responseVO
                        = BaseResponseVO.serviceException(
                        new CommonServiceException(404, "No Films!~"));
                String result = JSONObject.toJSONString(responseVO);
                return new ByteArrayInputStream(result.getBytes());
            }

            @Override
            public HttpHeaders getHeaders() {
                HttpHeaders headers = new HttpHeaders();
                headers.setContentType(MediaType.APPLICATION_JSON);
                return headers;
            }
        };
    }
}
```



Zuul默认会过滤一些头信息，需要进行过滤设置；

Cookie和头信息处理：

* Zuul帮助我们过滤了一些非安全的信息
* 诸如 cookie、 set-Cookie和 authorization等
* 可以通过设置 sensitiveHeaders来修改

```
zuul:
 sensitive-headers: "imooc"   ## zuul默认会过滤一些头信息，需要进行过滤设置;即headerName为immoc会被过滤掉；
```



**Zuul和JWT使用：**

```
@Component
public class JWTFilter extends ZuulFilter {
    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 0;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() throws ZuulException {
        // JWT工具类
        JwtTokenUtil jwtTokenUtil = new JwtTokenUtil();
        JwtProperties jwtProperties = JwtProperties.getJwtProperties();

        // ThreadLocal
        RequestContext ctx = RequestContext.getCurrentContext();
        // 获取当前请求和返回值
        HttpServletRequest request = ctx.getRequest();
        HttpServletResponse response = ctx.getResponse();

        // 提前设置请求继续，如果失败则修改此内容
        ctx.setSendZuulResponse(true);
        ctx.setResponseStatusCode(200);

        // 判断是否是登陆，如果是登陆则不验证JWT
        if (request.getServletPath().endsWith("/" + jwtProperties.getAuthPath())) {
            return null;
        }

        // 1、验证Token有效性 -> 用户是否登录过
        final String requestHeader = request.getHeader(jwtProperties.getHeader());
        String authToken = null;
        // Bearer header.payload.sign
        if (requestHeader != null && requestHeader.startsWith("Bearer ")) {
            authToken = requestHeader.substring(7);

            //验证token是否过期,包含了验证jwt是否正确
            try {
                boolean flag = jwtTokenUtil.isTokenExpired(authToken);
                if (flag) {
                    renderJson(ctx , response, BaseResponseVO.noLogin());
                }else{
                    // 2、解析出JWT中的payload -> userid - randomkey
                    String randomkey = jwtTokenUtil.getMd5KeyFromToken(authToken);
                    String userId = jwtTokenUtil.getUsernameFromToken(authToken);
                    // 3、是否需要验签,以及验签的算法

                    // 4、判断userid是否有效
                    // TODO
                }
            } catch (JwtException e) {
                //有异常就是token解析失败
                renderJson(ctx ,response, BaseResponseVO.noLogin());
            }
        } else {
            //header没有带Bearer字段
            renderJson(ctx ,response, BaseResponseVO.noLogin());
        }

        return null;
    }

    public static void renderJson(RequestContext ctx, HttpServletResponse response, Object jsonObject) {
        // 设置终止请求
        response.setHeader("Content-Type", "application/json;charset=UTF-8");
        ctx.setSendZuulResponse(false);
        ctx.setResponseBody(JSONObject.toJSONString(jsonObject));
    }
}

```

在 Zuul 中可以将认证之后的用户信息通过请求头的方式传递给下方服务，比如如下代码所示的方式。

```
ctx.addZuulRequestHeader("uid", userId);
```



Zuul解决跨域问题

```
@Component
public class CorsFilter extends ZuulFilter {

    public String filterType() {
        return "pre";
    }

    public int filterOrder() {
        return 0;
    }

    public boolean shouldFilter() {
        return true;
    }

    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        // 跨域
        HttpServletResponse response = ctx.getResponse();
        response.addHeader("Access-Control-Allow-Origin", "*");
        response.setHeader("Access-Control-Allow-Methods", "GET,POST,OPTIONS,DELETE,PUT");
        response.setHeader("Access-Control-Allow-Headers","DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range,Authorization");
        response.setContentType("application/json");
        response.setCharacterEncoding("UTF-8");
        return null;
    }

}
```



Eureka Server安全问题：

```
在Eureka Server模块：

1. 添加依赖
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-security</artifactId>
</dependency>

2. 修改application.yml:添加用户名、密码与地址
eureka:
  instance:
    hostname: localhost
    prefer-ip-address: true
  client:
    register-with-eureka: false
    fetch-registry: false
  service-url:
    defaultZone: http://root:123456@localhost:8761/eureka/ ## 添加用户名、密码

spring:
  security:
    user:
      name: root
      password: 123456
      roles: SUPERUSER
   
3. 添加配置：禁止CSRF
@EnableWebSecurity
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {

    // 对eureka注册的URL不进行CSRF防御
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().ignoringAntMatchers("/eureka/**");
        super.configure(http);
    }

}

Eureka Client模块

修改application.yml：
eureka:
  client:
    service-url:
      defaultZone: http://root:weichuang@localhost:8761/eureka/
```

