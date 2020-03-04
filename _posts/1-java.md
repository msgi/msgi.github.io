---
layout:     post
title:      "01、java笔记"
subtitle:   "java notes"
date:       2018-08-23
author:     "msg"
header-img: "img/posts/01.jpg"
header-mask: 0.3
catalog:    true

tags:
    - java
    - 学习
    - springboot
---

## 1、时间类型返回时间戳

> 这个问题是jackson将时间类型转换为json格式的时候，默认转为时间戳格式，要想自己控制时间输出的格式，可以进行如下的设置：

* 代码配置方式
```java
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        MappingJackson2HttpMessageConverter jackson2HttpMessageConverter = new 
        MappingJackson2HttpMessageConverter();
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
        jackson2HttpMessageConverter.setObjectMapper(objectMapper);
        converters.add(0,jackson2HttpMessageConverter);
    }
}
```

* 配置文件方式(application.yml)
```
spring:
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
```

## 2、返回时间缺少了8个小时

> 通过controller返回的数据，时间少了八个小时！！！

* 代码配置方式
```java
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        MappingJackson2HttpMessageConverter jackson2HttpMessageConverter = new 
        MappingJackson2HttpMessageConverter();
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setTimeZone(TimeZone.getTimeZone("GMT+8"));
        jackson2HttpMessageConverter.setObjectMapper(objectMapper);
        converters.add(0,jackson2HttpMessageConverter);
    }
}
```

* 配置文件
```
spring:
  jackson:
    time-zone: GMT+8
```

## 3、对项目中的error的返回数据做统一处理

```java
@RestController
@RequestMapping("${server.error.path:${error.path:/error}}")
public class MyErrorController implements ErrorController {
    @Override
    public String getErrorPath() {
        return "/error";
    }

    @RequestMapping
    public ResultBean doHandleError() {
        return new ResultBean(ResultCode.TIMEOUT);
    }
}
```

## 4、对项目中的exception做统一处理

1) 第一种方式：

```java

@ControllerAdvice
@ResponseBody
public class ExceptionHandlerAdvice {

    @ExceptionHandler(Exception.class)
    public ResultBean handleException(Exception e) {
        e.printStackTrace();
        return new ResultBean(ResultCode.OTHER_ERROR);
    }

    @ExceptionHandler(NoNodeAvailableException.class)
    public ResultBean handleNoNodeAvailableException(NoNodeAvailableException e) {
        return new ResultBean(ResultCode.ES_TIMEOUT);
    }
}

```

2) 第二种方式：

> @Aspect 注解；\\
> 植入点：\\
> 方法返回值为：ResultEntity\\
> 所有带有controller层级的包 下面的 所有类的所有方法

```java
@Aspect
@Component
public class ControllerAOP {

    @Pointcut("execution(public * com.msg.*.controller.*.*(..))")
    public void webLog() {
    }

    @Around("webLog()")
    public Object handlerControllerMethod(ProceedingJoinPoint pjp) {
        long startTime = System.currentTimeMillis();
        ResultBean<?> result;
        try {
            result = (ResultBean<?>) pjp.proceed();
            long stopTime = System.currentTimeMillis();
            log.info(pjp.getSignature() + "use time:" + ( stopTime- startTime));
        } catch (Throwable e) {
            result = handlerException(pjp, e);
        }

        return result;
    }

    private ResultBean<?> handlerException(ProceedingJoinPoint pjp, Throwable e) {
        ResultBean<?> result;
        e.printStackTrace();
        // 已知异常
        if (e instanceof BadSqlGrammarException) {
            result = new ResultBean<>(ResultCode.ILLEGAL_SQL);
        } else if (e instanceof HttpMessageNotReadableException) {
            result = new ResultBean<>(ResultCode.ILLEGAL_JSON);
        } else if (e instanceof NoNodeAvailableException) {
            result = new ResultBean<>(ResultCode.ES_TIMEOUT);
        }else if(e instanceof BatchInsertException){
            result = new ResultBean<>(ResultCode.EMPTY_LIST);
        }else if(e instanceof ClassCastException){
            result = new ResultBean<>(ResultCode.WRONG_PARAM);
        }


        else {
            result = new ResultBean<>(ResultCode.OTHER_ERROR);
        }

        return result;
    }
}
```

## 5、跨域配置

```java
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("POST", "PUT", "GET", "DELETE")
                .allowCredentials(false).maxAge(3600);
    }
}
```

## 6、springboot获取sessionId
```java
public String getSessionId(){
    HttpSession httpSession = getSession();
    String id = httpSession.getId();
    return id;
}


private HttpSession getSession() {
    HttpSession session = null;
    try {
        session = getRequest().getSession();
    } catch (Exception e) {
        log.error("获取session失败！");
    }
    return session;
}

private HttpServletRequest getRequest() {
    ServletRequestAttributes attrs = (ServletRequestAttributes) RequestContextHolder
            .getRequestAttributes();
    return attrs.getRequest();
}
```

## 7、更新maven项目版本号

```
mvn versions:set -DnewVersion=2.0.0-SNAPSHOT
```

## 8、@Data注解

原文中提到的大致有以下几点： 

1. 此注解会生成equals(Object other) 和 hashCode()方法。 
2. 它默认使用非静态，非瞬态的属性 
3. 可通过参数exclude排除一些属性 
4. 可通过参数of指定仅使用哪些属性 
5. 它默认仅使用该类中定义的属性且不调用父类的方法 
6. 可通过callSuper=true解决上一点问题。让其生成的方法中调用父类的方法。

另：@Data相当于@Getter @Setter @RequiredArgsConstructor @ToString @EqualsAndHashCode这5个注解的合集。

通过官方文档，可以得知，当使用@Data注解时，则有了@EqualsAndHashCode注解，那么就会在此类中存在equals(Object other) 和 hashCode()方法，且不会使用父类的属性，这就导致了可能的问题。 
比如，有多个类有相同的部分属性，把它们定义到父类中，恰好id（数据库主键）也在父类中，那么就会存在部分对象在比较时，它们并不相等，却因为lombok自动生成的equals(Object other) 和 hashCode()方法判定为相等，从而导致出错。

修复此问题的方法很简单： 

1. 使用@Getter @Setter @ToString代替@Data并且自定义equals(Object other) 和 hashCode()方法，比如有些类只需要判断主键id是否相等即足矣。 
2. 或者使用在使用@Data时同时加上@EqualsAndHashCode(callSuper=true)注解。

## 9、单点登录

　什么是单点登录？单点登录全称Single Sign On（以下简称SSO），是指在多系统应用群中登录一个系统，便可在其他所有系统中得到授权而无需再次登录，包括单点登录与单点注销两部分。

## 10、Idea突然不停indexing的问题

发现Idea中文件不停的indexing，界面不停闪烁，选择文件选不中等，只要清理一下Idea的缓存和索引就可以了，在File-Invalidate Caches / Restart中，选择Invalidate and Restart，之后会重启Idea，解决！

## 11、java内部类调用外部类

直接使用外部类名称.this.方法即可，例如Inner是内部类，Outer是外部类，在Inner中调用Outer.this.Print()即可使用Outer的print方法

## 12、elastic search排序

org.elasticsearch.search.sort.SortBuilder是一个抽象类，有4个子类

org.elasticsearch.search.sort.FieldSortBuilder 根据某属性值排序

org.elasticsearch.search.sort.GeoDistanceSortBuilder 根据地理位置排序

org.elasticsearch.search.sort.ScoreSortBuilder 根据score排序

org.elasticsearch.search.sort.ScriptSortBuilder 根据自定义脚本排序

可以通过SortBuilders的4个静态方法来分别生成SortBuilder的4个子类实例。可以通过SortBuiler的实例方法order(SortOrder order)来指定是升序还是倒序，默认是升序。

```java

// 创建ServiceLog类用来自定义注解

@Retention(RetentionPolicy.Runtime)
@Target(ElementType.METHOD)
public @interface ServiceLog {
 
}
 
// 定义ServiceLogAspect类用来定义日志打印信息
 
@Component
@Aspect
public class ServiceLogAspect {
 
   public ThreadLocal<Long> local = new ThreadLocal<Long>();
    
   @Pointcut("@annotation(com.test.XXX.ServiceLong)")
   public void pointCut() {
     
   }
 
   @Before("pointCut()")
   public void before(JoinPoint point) {
    String methodName = point.getTarget().getClass().getName()+"."+point.getSignature().getName();
    local.set(System.currentTimeMillis());
   }
 
  @After("pointCut()")
   public void after(JoinPoint point) {
    long start = local.get();
    String methodName = point.getTarget().getClass().getName()+"."+point.getSignature().getName();
    System.out.println(System.currentTimeMillis()-start));
    }
    
  @AfterThrowing(pointcut="pointCut()",throwing="error")
   public void throwing(JoinPoint point,Throwable error) {
    System.out.println("error");
    }
 
}
```

## 13、单例模式里双重加锁的机制

```java
public static PhraseSimilarity getInstance() {
        //拿掉判断，直接运行synchronization，会使每个getInstance()都会得到一个静态内部锁，降低了效率
    if (instance == null) {
        synchronized (PhraseSimilarity.class) {
            //同步块内不进行二次检验的话就会生成多个实例了。
            if (instance == null) {
                instance = new PhraseSimilarity();
            }
        }
    }
    return instance;
}
```

## 14、elasticsearch-data返回score

```java
return template.query(query, response -> {
        List<BotSentenceWithScore> botSentences = new ArrayList<>();
        SearchHit[] hits = response.getHits().getHits();
        for (SearchHit hit : hits) {
            try {
                BotSentenceWithScore botSentence  = entityMapper.mapToObject(hit.getSourceAsString(), BotSentenceWithScore.class);
                float documentScore = hit.getScore();
                botSentence.setScore(documentScore);
                botSentences.add(botSentence);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return botSentences;
    });
```

## 15、entitymapper

```java
@Configuration
public class EntityMapperConfig {
    @Bean
    public EntityMapper entityMapper(){
        return new DefaultEntityMapper();
    }
}
```

## 16、idea打包可执行jar包

1. 选中Java项目工程名称，在菜单中选择 File->project structure... (快捷键Ctrl+Alt+Shift+S)。

2. 在弹出的窗口中左侧选中"Artifacts"，点击"+"选择jar，然后选择"from modules with dependencies"。

3. 在配置窗口中配置"Main Class"。

4. 配置“Directory for META-INF/MAINFEST.MF”，此项配置的缺省值是：..\src\main\java，需要改成项目根目录。选择“extract to the target JAR”，这样所有依赖的jar包都会放在生成的jar包中。

5. 完成后，点击OK，Apply等按钮，回到IDEA的主菜单，选择“Build - Build Artifacts”下的“Build”或者“Rebuild”即可生成最终的可运行的jar。

## 17、aop处理身份验证token

```java
    private Boolean handleToken(ProceedingJoinPoint pjp){
        Object[] objects = pjp.getArgs();
        if(null != objects && objects.length > 0) {
            Object object = objects[0];

            Map<String,Object> map = (Map<String, Object>) object;

            String token = (String) map.get("token");

            if(null != token){
                return token.equals("123456");
            }
        }

        return false;
    }
```

## 18、es分页返回数据

```java
    List<BotSentence> botSentenceList = new ArrayList<>();
            ScrolledPage<BotSentence> botSentenceScrolledPage = (ScrolledPage<BotSentence>) template.startScroll(TimeValue.timeValueMinutes(1).millis(), searchQuery, BotSentence.class);
            while (botSentenceScrolledPage.hasContent()) {
                botSentenceList.addAll(botSentenceScrolledPage.getContent());
                botSentenceScrolledPage = (ScrolledPage<BotSentence>) template.continueScroll(botSentenceScrolledPage.getScrollId(), TimeValue.timeValueMinutes(1).millis(), BotSentence.class);
            }
    template.clearScroll(botSentenceScrolledPage.getScrollId());
```

## 19、springboot连接elasticsearch连接不上

```java
@Component
public class ElasticSearchConfiguration implements InitializingBean {

    static {
        System.setProperty("es.set.netty.runtime.available.processors", "false");
    }

    @Override
    public void afterPropertiesSet() {
        log.info("*****************es_config*************************");
        log.info("es.set.netty.runtime.available.processors:{}", System.getProperty("es.set.netty.runtime.available.processors"));
        log.info("***************************************************");
    }
}
```
