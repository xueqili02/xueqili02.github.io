---
title: spring
date: 2024-01-09 00:34:30
---

# Spring

@Copyright Li Xueqi. 2023/08/25.

## Spring 系统架构

- Core Container 核心容器
  - spring-core：核心工具类
  - spring-beans：提供bean的创建、管理、配置功能
  - spring-context：提供对国际化、事件传播、资源加载等的支持
  - spring-expression：提供对表达式语言Spring Expression Language的支持，只依赖于spring-core
- AOP 面向切面编程
  - spring-aspects：提供对AspectJ的集成
  - spring-aop：提供aop的实现
  - spring-instrument
- Aspects AOP思想实现
- Data Access/Integration 数据访问/整合
  - spring-jdbc：对数据库的抽象访问
  - spring-tx：对事务的支持
  - spring-orm：对orm框架的支持，包括hibernate、JPA、iBatis等
  - spring-oxm：提供一个抽象层支持OXM（Object-to-XML-Mapping）
  - spring-jms：消息服务
- Web web开发
  - spring-web
  - spring-webmvc
  - spring-websocket
  - spring-webflux
- test 单元测试与集成测试

## 核心概念

- 问题：若直接使用new创建类中引用类型变量，会导致代码耦合度高，引入新类改动多。
- 解决方案：使用对象时，不直接new对象，而是由外部提供对象
- IoC(Inversion of Control) 控制反转
  - 对象创建控制权由程序转到外部，此思想称为控制反转
  - Spring对IoC进行了实现，提供了一个容器，称为IoC容器，充当IoC思想中的“外部”
  - IoC容器负责对象的创建、初始化等一系列操作，被创建或被管理的对象在IoC容器中称为Bean
- DI (Dependency Injection)
  - 在容器中建立Bean与Bean之间的依赖关系的整个过程

IoC容器在Spring中其实是一个Map，存放了各种对象；

DI是IoC的一种实现方式

手动创建IoC(XML)
- 1 导入Spring坐标
- 2 定义Spring管理的类（接口）
- 3 创建Spring配置文件，配置对应类作为Spring管理的Bean  注意：bean定义时id属性在同一个上下文不能重复
- 4 初始化IoC容器，通过容器获得Bean

DI案例(XML)
- 1 删除使用new形式创建对象的代码
- 2 提供依赖对象的set方法
- 3 配置service与dao的关系

## bean

bean别名配置，在xml中的name中指定，可以定义多个，用逗号/空格/分号分隔

获取bean无论是通过id还是name，如果无法获取到，会抛出NoSuchBeanDefinitionException异常

### bean的作用范围
- spring默认的bean都是单例
- 指定scope为singleton是单例，prototype是非单例
- 为什么bean默认单例：防止对象太多，占据大量内存，难以管理；提高运行效率，不需要重复创建对象

### bean的实例化
- bean本质是对象，创建bean使用构造方法完成
- 方法一：默认调用无参构造器，不论是private还是public，因为是通过反射获取对象
- 方法二：静态工厂实例化Bean
- 方法三：实例工厂实例化Bean
- 方法四：FactoryBean实例化Bean（实用）

`@Bean`和`@Component`的区别
- `@Component`作用于类，`@Bean`作用于方法
- `@Component`通过类路径扫描，自动装配到Spring容器中；`@Bean`通常是在标有该注解的方法中产生这个bean
- `@Bean`的适用范围更广，自定义性更强。当引用第三方库的时候，只能通过`@Bean`来实现

### bean的生命周期

bean的生命周期是bean从创建到销毁的过程

bean的生命周期控制是在bean创建后到销毁前做一些事情

控制生命周期的方式：
1. 在类中定义初始化和销毁需要的操作，在xml中利用`init-method`和`destroy-method`定义
2. 在ServiceImpl中实现`InitializingBean`的`afterPropertiesSet`方法和`DisposableBean`的`destroy`方法

bean的生命周期
- 初始化容器
    1. 创建对象（内存分配）
    2. 执行构造方法
    3. 执行属性注入（set方法）
    4. 执行bean的初始化方法
- 使用bean
- 关闭/销毁容器
    1. 执行bean的销毁方法
    
    ```java
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    BookService bookService = (BookService) context.getBean("service");
    bookService.save();

    // 销毁bean方式一，推荐
    context.registerShutdownHook();
    // 销毁bean方式二，较为暴力，直接关闭容器
    context.close();
    ```

bean的生命周期 详细：
- Bean容器找到配置文件中Spring Bean的定义
- Bean容器利用Java Reflection API创建Bean的实例
- 若涉及到属性值，利用set方法设置属性值
- 如果Bean实现了BeanNameAware接口，调用setBeanName()方法，设置Bean的名字
- 如果Bean实现了BeanClassLoader接口，调用setBeanClassLoader()方法，传入ClassLoader的实例
- 如果Bean实现了BeanFactoryAware接口，调用setBeanFactory()方法，传入BeanFactory的实例
- 如果Bean实现了*Aware接口，调用方法
- 如果有和加载这个Bean有关的Spring容器的BeanPostProcess对象，执行postProcessBeforeInitialization()
- 如果Bean实现了InitializingBean接口，执行afterPropertiesSet()方法
- 如果Bean在配置文件中定义了init-method，执行方法
- 如果有和加载这个Bean有关的Spring容器的BeanPostProcess对象，执行postProcessAFterInitialization()
- 销毁Bean的时候，如果Bean实现了DisposableBean接口，执行方法
- 销毁Bean的时候，如果Bean在配置文件中定义了destroy-method，执行方法

## 依赖注入方式

注解方式：
- `@Autowired`：Spring内置注解，默认注入方式是byType，按照类型匹配注入；当一个接口有多个实现类，byType就会失效，会变为byName进行匹配注入；**建议通过`@Qualifier`显式指定名字，而不是依靠变量名**
- `@Resource`：JDK提供的注解，默认注入方式是byName，若无法通过名称匹配，则按照类型匹配；这个注解有两个重要属性，`name` `type`
  - 仅指定name，byName
  - 仅指定type，byType
  - 两个都指定，按照byType + byName注入
- `@Autowired` 支持在构造函数、方法、字段和参数上使用。`@Resource` 主要用于字段和方法上的注入，不支持在构造函数或参数上使用
- `@Inject`

- 普通方法（set方法）
- 构造器注入

依赖注入描述了在容器中建立bean和bean之间依赖关系的过程，如果bean需要的是数组或字符串
- 引用类型
- 简单类型（基本数据类型与String）

依赖注入方式：
- setter注入
  - 简单类型
    - 在类中定义属性的setter方法，在xml中定义property，设置name和value字段
  - 引用类型
    - 在类中定义属性的setter方法，在xml中定义property，设置name和ref字段
- 构造器注入
  - 简单类型
    - 在类中定义构造器，在xml中定义constructor-arg，设置name和value字段
  - 引用类型
    - 在类中定义构造器，在xml中定义constructor-arg，设置name和ref字段

依赖注入方式的选择
- 强制依赖通过构造器注入，使用setter注入可能会导致null出现
- 可选依赖通过setter注入，灵活
- Spring框架提倡构造器注入
- 如果受控对象没有提供setter方法，则必须用构造器注入
- 自己开发的模块推荐使用setter注入

## 依赖自动装配

自动装配：IoC根据bean所依赖的资源，在容器中自动寻找并自动注入bean的过程

方式：设置`bean`标签的`autowire`属性
- 按类型（常用）：`autowire="byType"`
- 按名称：`autowire="byName"`
- 按构造方法
- 不启用自动装配

注意：
- 自动装配用于引用类型依赖注入，不能对简单类型进行操作
- 使用按类型装配时，容器中相同类型的bean必须唯一
- 使用按名称装配时，容器中必须具有指定名称的bean，因变量名与配置耦合，不推荐使用
- 自动装配优先级低于setter和构造器注入，同时出现时自动装配失效

## 集合注入

```xml
<bean id="bookDao" class="org.example.dao.impl.BookDaoImpl" init-method="init" destroy-method="destroy">
        <property name="connectionNum" value="10"/>
        <property name="databaseName" value="MySQL"/>
        <constructor-arg name="port" value="3306"/>

        <property name="array">
            <array>
                <value>100</value>
                <value>200</value>
                <value>300</value>
            </array>
        </property>
        <property name="list">
            <list>
                <value>10</value>
                <value>20</value>
            </list>
        </property>
        <property name="set">
            <set>
                <value>1</value>
                <value>asdfawe</value>
            </set>
        </property>
        <property name="map">
            <map>
                <entry key="abc" value="def"/>
                <entry key="123c" value="defs"/>
            </map>
        </property>
        <property name="properties">
            <props>
                <prop key="key1">value1</prop>
            </props>
        </property>
    </bean>

```

## bean是线程安全的吗

以singleton和prototype为例：
- prototype在每次请求时都会创建一个新的bean，不存在线程安全问题
- singleton大多数情况下没有线程安全问题，因为大多数bean都是dao、service等，内部没有可变变量
- 若singleton的bean内部有可变变量，则可能会出现线程安全问题，此时可以通过在类内部定义ThreadLoacl成员变量，将可变变量保存至ThreadLocal中

## 加载properties文件
- 开启context命名空间
- 使用context命名空间，加载指定的properties文件
- 使用${}读取加载的属性值

- 不加载系统属性：
    ```xml
    <context:property-placeholder location="jdbc.properties" system-properties-mode="NEVER"/>
    ```
- 加载多个properties文件
    ```xml
    <context:property-placeholder location="jdbc.properties, msg.properties"/>
    ```
- 加载所有properties文件
    ```xml
    <context:property-placeholder location="*.properties"/>
    ```
- 加载properties文件标准格式
    ```xml
    <context:property-placeholder location="classpath:*.properties"/>
    ```
- 从类路径或jar包搜索并加载properties文件
    ```xml
    <context:property-placeholder location="classpath*:*.properties"/>
    ```

## 容器

### 创建容器

```java
// 类路径加载配置文件
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
// 文件路径加载配置文件
ClassPathXmlApplicationContext context = new FileSystemXmlApplicationContext("applicationContext.xml");
// 加载多个配置文件
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext1.xml", "applicationContext2.xml);
```

### 获取bean

```java
// 使用bean名称获取
BookDao bookDao = (BookDao) ctx.getBean("bookDao");
// 使用bean名称获取并指定类型
BookDao bookDao = ctx.getBean("bookDao", BookDao.class);
// 使用bean获取 这种方式要保证只有一个该类型的bean
BookDao bookDao = ctx.getBean(BookDao.class);
```

### 容器类层次结构

- BeanFactory 顶层接口
  - ApplicationContext 常用接口
    - ConfigurableApplicationContext 提供关闭容器的功能
      - ClassPathXmlApplicationContext 常用实现类

### BeanFactory

类路径加载
```java
Resource resources = new ClassPathResource("applicationContext.xml");
BeanFactory bf = new XmlBeanFactory(resources);
BookDao bookDao = bf.getBean("bookDao", BookDao.class);
bookDao.save();
```

BeanFactory创建完后，所有的bean均为延迟加载

## 注解开发

- @Configuration注解用于设定当前类为配置类
- @ComponentScan注解用于设定扫描路径，多个路径请使用数组形式

```java
@Configuration
@ComponentScan("org.example.xxx")  // {"path1", "path2"}
@Scope("singleton")  // "prototype" 单例or非单例
public class SpringConfig {
    @PostConstruct
    public void init();

    @PreDestroy
    public void destroy();
}

// main中加载配置类初始化容器
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
```

依赖注入

使用@Autowired注解开启自动装配模式（按类型）
- 自动装配基于反射设计创建对象并暴力反射对应属性为私有属性初始化数据，因此无需提供setter方法
- 自动装配建议使用无参构造方法创建对象(默认)，如果不提供对应构造方法，请提供唯一的构造方法
- 使用@Qualifier开启指定名称装配bean
  - @Qualifier无法单独使用，必须依赖于@Autowired

使用@Value实现简单类型注入
- 在SpringConfig类上加@PropertySource注解，内部指定property文件，不支持通配符  `@PropertySource({"jdbc.properties"})`
- 在@Value中通过placeholder设置值，`@Value(${jdbc.username})`

第三方bean管理
- 方法一：手写代码 不推荐
  1. 定义方法获取要管理的对象
  2. 添加@Bean，表示当前方法的返回值是一个bean
- 方法二：导入式
    ```java
    public class JdbcConfig {
        @Bean
        public DataSource dataSource() {
            DruidDataSource druid = new DruidDataSource();
            return druid;
        }
    }
    ```
    - 使用@import注解手动加入配置类到核心配置，多个数据使用数组
    ```java
    @Configuration
    @import({JdbcConfig.class})
    public class SpringConfig {

    }
    ```
- 方式三：扫描式
    ```java
    @Component
    public class JdbcConfig {
        @Bean
        public DataSource dataSource() {
            DruidDataSource druid = new DruidDataSource();
            return druid;
        }
    }
    ```
    - 使用@ComponentScan注解扫描配置类所在的包，加载对应配置类的信息
    ```java
    @Configuration
    @ComponentScan({"org.example.config"})
    public class SpringConfig {

    }
    ```

第三方bean中注入引用类型：只需要为bean定义方法形参即可，容器会根据类型自动装配
```java
@Bean
public DataSource dataSource(BookDao bookDao) {
    System.out.println(bookDao);
    DruidDataSource druid = new DruidDataSource();
    return druid;
}
```

## Spring整合MyBatis

```java
@Bean
public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) {
    SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
    ssfb.setTypeAliasesPackage("com.itheima.domain");
    ssfb.setDataSource(dataSource);
    return ssfb;
}

@Bean
public MapperScannerConfigurer mapperScannerConfigurer(){
    MapperScannerConfigurer msc = new MapperScannerConfigurer();
    msc.setBasePackage("com.itheima.dao");
    return msc;
}
```

## Spring整合JUnit

使用Spring整合Junit专用的类加载器
```java
@Runwith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class BookServiceTest {
    @Autowired
    private BookService bookService;
    
    @Test
    public void testSave(){
        bookService.save();
    }
}

```

## AOP

### 简介

AOP，Aspect Oriented Programming，面向切面编程，一种编程范式，指导开发者如何组织程序

作用：在不惊动原始设计的基础上，进行功能增强（无入侵式编程）

AOP核心概念：
- 连接点：程序执行过程中的任意位置，粒度为执行方法、抛出异常、设置变量等
  - 在Spring的AOP中，连接点指方法的执行
- 切入点：匹配连接点的式子
  - 在Spring AOP中，一个切入点可以只描述一个方法，也可以匹配多个方法
    - 一个具体方法
    - 多个方法：例如所有的save方法，以get开头的方法，所有带有一个参数的方法
- 通知：在切入点处执行的功能，也叫共性功能
- 通知类：实现通知的类
- 切面：描述通知与切入点的对应关系

### AOP工作流程
1. Spring容器启动
2. 读取所有切面配置中的切入点，没有使用的PointCut不读取
3. 初始化bean，判定bean对应的类中的方法是否匹配到任意切入点
    - 匹配失败，创建对象
    - 匹配成功，创建原始对象（目标对象）的代理对象
4. 获取bean执行方法
    - 获取bean，调用方法执行，完成操作
    - 获取的bean是代理对象时，根据代理对象的运行模式运行原始方法和增强功能，完成操作

目标对象( Target ): 原始功能去掉共性功能对应的类产生的对象，这种对象是无法直接完成最终工作的

代理( Proxy ): 目标对象无法直接完成工作，需要对其进行功能回填，通过原始对象的代理对象实现

```java
@Component
@Aspect
public class MyAdvice {
    @Pointcut("execution(void org.example.dao.BookDao.save())")
    private void pt() {

    }

    @Before("pt()")
    public void method() {
        System.out.println(System.currentTimeMillis());
    }
}
```


### AOP切入点表达式

切入点：要增强的方法

切入点表达式：要进行增强的方法的描述方式

切入点表达式标准格式：
- 动作关键字：一般都是execution
- 访问修饰符：public、private等，可省略
- 返回值
- 包名
- 类名/接口名
- 方法名
- 参数
- 异常：可省略

通配符：
- *：单个独立的任意符号，可以独立出现，也可以作为前缀或后缀
- ..：多个连续的任意符号，常用于简化包名和参数的书写
- +：匹配子类类型

书写技巧
- 所有代码按照标准规范开发，否则以下技巧全部失效
- 描述切入点通常描述接口，而不描述实现类
- 访问控制修饰符针对接口开发均采用public描述（可省略访问控制修饰符描述）
- 返回值类型对于增删改类使用精准类型加速匹配，对于查询类使用*通配快速描述
- 包名书写尽量不使用..匹配，效率过低，常用做单个包描述匹配，或精准匹配
- 接口名/类名书写名称与模块相关的采用*匹配，例如UserService书写成*Service，绑定业务层接口名
- 方法名书写以动词进行精准匹配，名词采用*匹配，例如getByld书写成getBy*,selectAll书写成selectAll
- 参数规则较为复杂，根据业务方法灵活调整
- 通常不使用异常作为匹配规则

### AOP通知类型

AOP通知分为五种类型：
- 前置通知：@Before
- 后置通知：@After
- 环绕通知（重点）：需要在内部指定原始方法的执行位置，并返回原始方法的返回值
  - 环绕通知必须依赖形参ProceedingJoinPoint才能实现对原始方法的调用，进而实现原始方法调用前后同时添加通知
  - 通知中如果未使用ProceedingJoinPoint对原始方法进行调用将跳过原始方法的执行
  - 对原始方法的调用可以不接收返回值，通知方法设置成void即可，如果接收返回值，必须设定为Object类型
  - 原始方法的返回值如果是void类型，通知方法的返回值类型可以设置成void，也可以设置成Object
  - 由于无法预知原始方法运行后是否会抛出异常，因此环绕通知方法必须抛出Throwable对象
    ```java
    @Around("pt()")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("around before advice ...");
        Object ret = pjp.proceed();
        System.out.println("around after advie ...");
        return ret;
    }
    ```
- 返回后通知：@AfterReturning
- 抛出异常后通知：@AfterThrowing

### AOP通知中获取数据

- 如果是@around，函数参数中使用ProceedingJoinPoint
- 如果是其他通知类型，函数参数中使用JoinPoint
- 上面两个参数必须是形参列表中的第一个参数
- 指定参数承接函数返回值：`@AfterReturning(value = "pt()", returning = "ret")`，同时在参数中定义ret变量

### AOP的使用场景
- 获取方法执行时间和执行效率
- AOP通过获取函数调用args，可以处理异常输入数据
- AOP用户鉴权


## Spring事务

作用：在业务层或数据层保障一系列数据库操作的同时成功或失败

### Spring事务角色

事务管理员：事务发起方，在Spring中通常指代业务层开启事务的方法

事务协调员：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法

### Spring事务传播行为

事务传播行为：事务协调员对事务管理员所携带的事务的处理态度

传播属性：
- REQUIRED (Default)
- REQUIRIES_NEW
- SUPPORTS
- NOT_SUPPORTED
- MANDATORY
- NEVER
- NESTED

# SpringMVC

Controller加载控制、业务bean加载控制
- SpringMVC相关bean（表现层bean）
- Spring控制的bean
  - 业务bean（service）
  - 功能bean（DataSource等）

解决方案：
- 加载Spring控制的bean时，去掉SpringMVC控制的bean
  - 使用`@ComponentScan(excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Controller.class))`

Spring MVC 核心组件
- DispatcherServlet：核心的中央处理器，负责接收请求、分发，并给予客户端响应。
- HandlerMapping：处理器映射器，根据 uri 去匹配查找能处理的 Handler ，并会将请求涉及到的拦截器和 Handler 一起封装。
- HandlerAdapter：处理器适配器，根据 HandlerMapping 找到的 Handler ，适配执行对应的 Handler。
- Handler：请求处理器，处理实际请求的处理器。
- ViewResolver：视图解析器，根据 Handler 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 DispatcherServlet 响应客户端

# Spring Boot
## 自动装配原理

Spring的核心注解`@SpringBootApplication`，底层分为三个注解：
- `@Configuration`：允许在上下文中注册额外的bean，或导入其他配置类
- `@EnableAutoConfiguration`：开启Spring Boot自动装配
- `@ComponentScan`：扫描被`@Component`注解的bean，默认扫描所有包下的所有类

# Maven

聚合与继承的区别
- 作用
  - 聚合用于快速构建项目
  - 继承用于快速配置
- 相同点:
  - 聚合与继承的pom.xm1文件打包方式均为pom，可以将两种关系制作到同一个pom文件中
  - 聚合与继承均属于设计型模块，并无实际的模块内容
- 不同点:
  - 聚合是在当前模块中配置关系，聚合可以感知到参与聚合的模块有哪些
  - 继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己

# MyBatisPlus

在Dao接口层继承BaseMapper<>，即可使用提供的增删改查