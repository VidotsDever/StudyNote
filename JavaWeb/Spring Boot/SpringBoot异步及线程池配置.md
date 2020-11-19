### 异步方法注解@Async

在SpringBoot中进行异步处理，可以使用异步注解`@Async`和`@EnableAsync`。`@Async`注解表示异步，如：`@Async("asyncServiceExecutor")`，后面的参数`asyncServiceExecutor`对应于自定义的线程池配置类(在以下例子中是`ExecutorConfig`类) 中的线程池方法名字。如果不写后面的参数，直接用`@Async`，则是使用默认的线程池。



### Future实现类获取异步处理结果

如果想要获取异步处理的结果，可以通过`Future`接口的实现类调用`get()`方法获得。`Future`接口的常见实现类有`FutureTask`。
在SpringBoot中，一般用 `AsyncResult`作为异步结果。



```
@Component
@Slf4j
public class AsyncService {
    /**
     * @Async注解表示异步，后面的参数对应于线程池配置类ExecutorConfig中的方法名asyncServiceExecutor()，
     * 如果不写后面的参数，直接使用@Async注解，则是使用默认的线程池
     * Future<String>为异步返回的结果。可以通过get()方法获取结果。
     *
     */
    @Async("asyncServiceExecutor")
    public Future<String> getDataResult( ){
        log.info("开始异步处理");
        String result="asyncResultTest";
        return new AsyncResult<String>(result);
    }
}
```



自定义线程池配置如下：

```
@Slf4j
@Configuration
public class ExecutorConfig {
    @Bean
    public Executor asyncServiceExecutor() {
        log.info("start asyncServiceExecutor");
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        //配置核心线程数
        executor.setCorePoolSize(5);
        //配置最大线程数
        executor.setMaxPoolSize(5);
        //配置队列大小
        executor.setQueueCapacity(99999);
        //配置线程池中的线程的名称前缀
        executor.setThreadNamePrefix("async-service-");

        // 设置拒绝策略：当pool已经达到max size的时候，如何处理新任务
        // CALLER_RUNS：不在新线程中执行任务，而是有调用者所在的线程来执行
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        //执行初始化
        executor.initialize();
        return executor;
    }
}
```



```
@Service
@Slf4j
public class UserService {
    @Autowired
    private AsyncService asyncService;

    /**
     * 调用异步服务，获取异步结果。
     * @return
     */
    public String getAsyncResult()  {
        Future<String>  future=asyncService.getDataResult();
        String result=null;
        try{
            result =future.get();
        }catch (InterruptedException | ExecutionException e) {
            log.error("error：{}",e.getMessage());
        }
        log.info("异步处理结果为：{}",result);
        return result;
    }

}
```



`@EnableAsync`表示开启异步，可以放在@Controller层上方，也可以放在Application类的上方。

```
@Controller
@EnableAsync
public class UserController {
    @Autowired
    private UserService userService;

    @GetMapping("/user/query")
    @ResponseBody
    public String getUserData(){
        return userService.getAsyncResult();
    }
}
```