# Spring框架介绍

## Spring的介绍

**Spring是全栈轻量级开源框架，它的核心是IOC、AOP。**
![spring框架](https://shopping-mlk.oss-cn-beijing.aliyuncs.com/JavaNote/Spring/spring01.jpg)

## Spring IOC

原理：将JavaBean的控制权交给工厂，实现了**控制反转**。
![IOC原理图](https://shopping-mlk.oss-cn-beijing.aliyuncs.com/JavaNote/Spring/spring02.jpg)
**实际上SpringIOC所做的事情就是：将所有的JavaBean通过反射技术创建出来，并且存储到Map集合中。该Map的泛型是<String,Object>,使用时，只需要一个全限定类名（字符串）来得到对相应的对象**

**SpringIOC注解方式：**

**Component相关注解：**
| 注解 | 含义 |
| :---  | :---|
| @Component | 相当于配置文件中的bean标签 |
| @Contrller | 语义化注解：用于表现层 |
| @Service | 语义化注解：用于业务层 |
| @Repository | 语义化注解：用于持久层 |

**依赖注入的相关注解：**
| 注解 | 含义 |
| :---  | :---|
| @Autowired | **自动装配**：根据类型自动装配 |
| @Qualifier | **配合@Autowired使用**实现根据bean的id进行注入 |
| @Value | 注入基本数据类型 |
| @Resource | 功能是：@Autowired+@Qualifier |

**生命周期的相关注解：**
| 注解 | 含义 |
| :---  | :---|
| @PostConstruct | 指定初始化方法 |
| @PreDestroy | 指定销毁方法 |

**新注解：**
| 注解 | 含义 |
| :---  | :---|
| @Bean | 功能：将方法的返回的对象存储进IOC容器中 |
| @Configuration | 指定当前类为spring配置类 |
| @ComponentScan | 指定要扫描的包路径 |
| @PropertySource | 加载properties文件 **设置文件位置需要添加classpath** |
| @Import | 用于导入其它配置类 |

>注意：
>
>- **Component的语义化注解不能混用**，在相应的层次使用相应的注解
>- **@Autowired的自动注入原理**是根据类型进行注入，它是使用Setter注入，但是在Pojo中无需编写Setter代码。
  
**示例一**：Spring与Mybatis整合
1、编写DataSource配置类

```java
public class DataSourceConfig {
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Bean
    public DataSource dataSource(){
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        dataSource.setUrl(url);
        dataSource.setDriverClassName(driver);
        return dataSource;
    }
}
```

>因为MybatisConfig会导入该配置类，所以该类无需添加@Configuration。

2、编写MybatisConfig

```java
@Configuration
@PropertySource("classpath:jdbc.properties")
@Import(DataSourceConfig.class)
@ComponentScan("com")
public class MyBatisConfig {
    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource){
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        return sqlSessionFactoryBean;
    }
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        mapperScannerConfigurer.setBasePackage("com.dao");
        return mapperScannerConfigurer;
    }
}
```

## Spring AOP

AOP：面向切面编程
作用：在程序运行期间，不修改源代码对已有方法进行增强。**所实现的方法：动态代理技术**
优点:

- 减少重复代码
- 提高开发效率
- 方便维护

**专业术语：**

- 目标对象：待增强的类对象
- 增强类：类内部编写增强方法
- 通知：增强方法

**切点表达式：**
关键字：**execution(表达式)**
表达式的格式：
**访问修饰符  返回值  包名.包名.包名...类名.方法名(参数列表)**
**访问修饰符可以省略**
全通配写法：* *..*.*(..)
实际开发中切入点表达式的通常写法  com.itheima.service.impl.*.*(..)

**SpringAOP 注解方式开发：**

相关注解
| 注解 | 含义 |
| :--- | :---|
| @Aspect | 声明切面类 |
| @Before | 指定前置通知 |
| @AfterReturning | 指定后置通知 |
| @AfterThrowing | 指定异常通知 |
| @After | 指定最终通知 |
| @Around | 指定环绕通知 |
| @Pointcut | 指定切点表达式 |
| @EnableAspectJAutoProxy | 在配置类上添加此注解，表明启用AOP |

示例一：进行无参增强

```java
@Component
@Aspect
public class TranManner {
    @Pointcut("execution(* com.aop_test.domain.t1())")
    private void pointcut() {
    }

    @Before("pointcut()")
    public void begin() {
        System.out.println("前置增强");
    }

    @AfterReturning("pointcut()")
    public void after() {
        System.out.println("后置增强");
    }

    @AfterThrowing("pointcut()")
    public void error() {
        System.out.println("异常通知");
    }

    @After("pointcut()")
    public void after1() {
        System.out.println("最终增强");
    }

    //    环绕通知
    @Around("pointcut()")
    public Object around(ProceedingJoinPoint joinPoint) {
        Object o = null;
        try {
            System.out.println("前置增强");
            o = joinPoint.proceed();
            System.out.println("后置增强");
        } catch (Throwable throwable) {
            System.out.println("异常通知");
            throwable.printStackTrace();
        } finally {
            System.out.println("最终通知");
        }
        return o;
    }
}
```

示例二：进行有参增强

```java
@Component
@Aspect
public class TranManner1 {
    @Pointcut("execution(* com.aop_test.domain.t2(..))")
    private void pointcut() {
    }

    /**
     *前置通知、后置通知可以获取到参数
     * 最终通知可以获取到方法的返回值
     * 异常通知可以获取到异常信息
     */
    @Before("pointcut()")
    public void begin(JoinPoint joinPoint) {
        System.out.println("前置通知 参数："+joinPoint.getArgs()[0]);
    }

    @AfterReturning(value = "pointcut()",returning = "ret")
    public void after(String ret) {
        System.out.println("后置通知 原返回值："+ret);
    }

    @AfterThrowing(value = "pointcut()",throwing = "t")
    public void error(Throwable t) {
        System.out.println("异常通知"+t);
    }

    @After("pointcut()")
    public void after1(JoinPoint joinPoint) {
        System.out.println("最终通知"+joinPoint.getArgs()[0]);
    }

    //    环绕通知:可以修改参数
    @Around("pointcut()")
    public Object around(ProceedingJoinPoint joinPoint) {
        Object o = null;
        try {
            System.out.println("前置增强");
            Object[] args = joinPoint.getArgs();
            args[0]="aop";
            o = joinPoint.proceed(args);
            System.out.println("后置增强");
        } catch (Throwable throwable) {
            System.out.println("异常通知");
            throwable.printStackTrace();
        } finally {
            System.out.println("最终通知");
        }
        return o;
    }
}
```

>注意：
可以通过JoinPoint对象获取到方法的参数：joinPoint.getArgs()[0]，**主要使用的是环绕通知，它可以修改参数数据。**

## Spring整合junit

导入依赖：

```xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.3.18</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
```

**进行测试：**

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class t_1 {
    @Autowired
    private com.aop_test.domain domain;
    @Test
    public void t_2(){
        domain.t2("test");
    }
}
```

>注意：
>
>- @ContextConfiguration的参数是将要测试配置类的class
>- 在参数上直接自动注入即可进行测试

## Spring声明式事务

声明式事务与以往的编程事务相比，更加灵活，降低了重复代码，降低了维护的难度。

**相关的注解：**
| 注解 | 含义 |
| :--- | :---|
| @Transactional | 可以添加在类上、方法上，**注解的代码在执行出错的时候会进行事务回滚** |
| @EnableTransactionManagement | 开启事务 |
**Spring 声明式事务的注解开发：**

在方法或者类上添加@Transactional注解，标明该方法或者类启用事务。

```java
public interface IdepositSerivice {
    @Transactional
    void transferAccounts(String username1,String username2,int num);
}
public interface IlogService {
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    void pubsh(String username1,String username2,int num);
}
```

>注意：**propagation = Propagation.REQUIRES_NEW这个属性是指将设置成为一个新的事务**，与其它的事务互不影响。

在配置类中添加事务管理器的Bean，并且需要添加@EnableTransactionManagement注解
**下方是配置类的代码，包含了Mybatis的配置和事务管理器的配置。**

```java
@Configuration
@PropertySource("classpath:jdbc.properties")
@Import(DataSourceConfig.class)
@ComponentScan("com.tran")
@EnableTransactionManagement
public class TranConfig {
    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource){
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        return sqlSessionFactoryBean;
    }
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        mapperScannerConfigurer.setBasePackage("com.tran.dao");
        return mapperScannerConfigurer;
    }

    //配置事务管理器，mybatis使用的是jdbc事务
    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource){
        DataSourceTransactionManager tm = new DataSourceTransactionManager();
        tm.setDataSource(dataSource);
        return tm;
    }
}
```
