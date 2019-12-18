[toc]



# 笔记

## 依赖注入三种方式

- **构造器注入**.

  - 实体类

    ```java
    @Data
    @AllArgsConstructor
    public class Role {
        private Long id;
        private String roleName;
        private String note;
    }
    
    ```

  - 配置文件

    ```xml
    <bean id="role" class="pojo.Role">
            <constructor-arg index="0" value="1"/>
            <constructor-arg index="1" value="肖杰斌"/>
            <constructor-arg index="2" value="无备注"/>
    </bean>
    ```

- **setter注入**.

  - 配置文件

    ```xml
    <bean id="source" class="pojo.Source">
            <property name="fruit" value="橙子"/>
            <property name="size" value="大杯"/>
            <property name="sugar" value="少糖"/>
    </bean>
    ```

    

- 接口注入.

## 装配Bean

- 在XML中显示配置
- 在Java的接口和类中实现配置
- 隐式Bean的发现机制和自动装配原则

### 1.装配Bean的优先级

1. 基于约定由于配置的原则,最优先的应该是通过隐式Bean的发现机制和自动装配的原则.这样的好处减少程序开发者的决定权,简单又不失灵活.
2. 没有方法使用自动装配原则的情况下应该优先考虑Java接口和类中实现配置,好处是避免XML配置的泛滥,也更加容易.
3. 以上方法多无法使用的情况下,那么只能选择XML去配置Spring IoC容器.

**解释属性:**

```xml
<bean id="source" class="pojo.Source">
        <property name="fruit" value="橙子"/>
        <property name="size" value="大杯"/>
        <property name="sugar" value="少糖"/>
</bean>
```

- id属性是Spring找到的这个Bean的编号,不过id不是一个必须的属性,如果没有声明他,那么Spring将会采用"全限定名#{number}"的格式生成编号.
- class是个类全限定名
- property元素是定义类的属性.其中那么属性定义的是属性名称,二value是值.ref引入其他的Bean.还有Set、Map、Array和Properties等。

### 2.装配集合

- List属性对应**\<list\>**元素进行转配，然后通过多个**\<value\>**元素设值

```xml
<property name="list">
    <list>
        <value>value-list-1</value>
        <value>value-list-2</value>
        <value>value-list-3</value>
   	</list>
</property>

<property name="list">
   <list>
     <ref bean="role1"></ref>
     <ref bean="role2"></ref>
     <ref bean="role3"></ref>
   	</list>
</property>
```

- Map属性为对应的**\<map\>**元素进行装配，然后通过多个\<entry\>元素设置值，只是**\<entry\>**包含一个键**（key）**和一个值**\(value\)**的值。

```xml
<property name="map">
    <map>
       <entry key="key1" value="value-key-1"></entry>
       <entry key="key2" value="value-key-2"></entry>
       <entry key="key3" value="value-key-3"></entry>
    </map>
</property>
<property name="map">
    <map>
      <entry key-ref="role1" value-ref="source1"/>
      <entry key-ref="role2" value-ref="source2"/>
      <entry key-ref="role3" value-ref="source3"/>
    </map>
</property>
```

- Properties属性，为对应的\<properties\>元素进行装配，通过多个\<property\>元素设置，property元素必填属性可以，然后可以设置值

~~~xml
<property name="props">
  <props>
      <prop key="prop1">value-prop-1</prop>
      <prop key="prop2">value-prop-2</prop>
      <prop key="prop3">value-prop-3</prop>
  </props>
</property>
~~~

- Set属性为对应的**\<set\>**元素进行装配，然后通过多个**\<value\>**元素设置值。

~~~xml
<property name="set">
   <set>
      <value>value-set-1</value>
      <value>value-set-2</value>
      <value>value-set-3</value>
   </set>
</property>

<property name="set">
   <set>
    <ref bean="role1"/>
    <ref bean="role2"/>
    <ref bean="role3"/>
   </set>
</property>
~~~

- 数组，使用\<array\>设置值，然后通过多个\<value\>元素设置值

~~~xml
<property name="array">
    <array>
      <value>value-array-1</value>
      <value>value-array-2</value>
      <value>value-array-3</value>
    </array>
</property>
~~~

### 2.命名空间装配

~~~xml
xmlns:c="http://www.springframework.org/schema/c"
xmlns:util="http://www.springframework.org/schema/util" xmlns:p="http://www.springframework.org/schema/p"
~~~

- 以上为XML的命名空间，才能够在配置文件中使用前缀定义
- c:_0代表第一个参数，以此类推。
- p代表引用

~~~xml
<bean class="pojo.SourceRoleAssembly" id="sourceRoleAssembly2" p:id="1" p:list-ref="list" p:map-ref="map" p:set-ref="set"></bean>
~~~

### 4.通过注解的方式装配

> 使用场景更多的是注解（annotation）的方式去装配Bean，注解功能更加强大，基能给你实现XML的功能，也提供了自动装配的功能，”约定优于配置“。

两种方式俩让Spring IoC容器发现Bean。

1. 组件扫描：通过定义资源的方式，让Spring IoC容器扫描对应的包，从而把Bean装配进来。
2. 自动装配：通过注解定义，是的一些依赖关系可以通过注解完成。

#### 4.1使用@Component装配

~~~java
@Component(value = "person")
public class Person {
    @Value("1")
    private Long id;
    @Value("肖杰斌")
    private String name;
    @Value("17670513112")
    private String phone;
}
~~~

- 注解@Component代表Spring IoC会把这个类扫描生成Bean实例，value属性代表这个类在Spring中的id，可以简写为@Component("person"),不写，则是类名首字母小写作为id。
- 注解@Value代表的是值得注入，注入的时候spring会为其转换类型。

完成配置后需要通过@ComponentScan进行扫描，**默认扫描的是当前包的路径**，POJO的报名和他保持一致，否则无法扫描到。

~~~java
@ComponentScan
public class PojoConfig {
}
~~~

还可以通过配置文件的方式实现

~~~xml
<context:component-scan base-package="pojo"/>
~~~



### 5.自动装配@Autowired

- 按照类型装配
- 属性required默认值为true，为false时再找不到对应类型时，可以不注入，这样不会抛空指针
- 该注解可以加到方法上

#### 1.注解@Primary

如果存在多个同接口的实现类，Spring IoC容器无法判定注入那个，会优先注入使用了@Primary注解的实现类。

#### 2.注解@Qualifier

通过名称进行进行查找，可以消除歧义。

### 6.使用@Bean装配

在使用@Component只能注解在类上，在方法上使用@Bean，方法返回的对象作为Spring的Bean，存放至IoC容器中。

#### 6.1注解自定义Bean的初始化和销毁方法

> Bean的配置项中包含4个配置项

- **name**：是一个字符串数组，允许配置多个BeanName。
- **autowire**：标志是否是一个引用的Bean对象，默认值是autowire.NO。
- **initMethod**:自定义初始化方法。
- **destroyMethod**:自定义销毁方法。

### 7.使用Profile

> XML配置

~~~XML
<beans profile="test">
    <bean id="source" class="pojo.Source">
       <property name="size" value="大杯"/>
       <property name="fruit" value="甘蔗"/>
       <property name="sugar" value="少糖"/>
    </bean>
</beans>
<beans profile="dev">
    <bean id="source" class="pojo.Source">
       <property name="size" value="小杯"/>
       <property name="fruit" value="苹果"/>
       <property name="sugar" value="少糖"/>
    </bean>
</beans>
~~~

> 启动Profile

- 在Spring MVC的情况下可以配置Web上下文参数，或者DispatchServlet参数
- 作为JNDI条目
- 配置环境变量
- 配置JVM启动参数
- 在继承测试环境中使用@ActiveProfiles。

## 加载资源文件

### 1.加载属性（propertiest）文件

> <font color="#FF4500" size='5px'>配置项</font>

- **name**：字符串，配置这次属性配置的名称。
- **value**：字符串数组，可以配置多个属性文件。
- **ignoreResourceNotFound** ：boolean值，默认为false，其含义为如果找不到对应的属性文件是否进行忽略处理，由于默认值为false，所以在默认情况下找不到对应的配置文件会跑出异常。
- **encoding**:编码，默认为“”。

> <font color="#FF4500" size='5px'>测试代码</font>

~~~java
//配置类
@Configuration
@ComponentScan(basePackages = {"config"})
@PropertySource(value = {"classpath:db.properties"}, ignoreResourceNotFound = true)
public class ApplicationConfig {
    @Bean
    public PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
        return new PropertySourcesPlaceholderConfigurer();
    }
}
~~~

<font color="red">注：</font>在Spring中没有解析属性占位符的能力，但是通过propertySourcesPlaceholderConfigurer可以允许Spring解析对应的属性文件，并通过占位符去引用对应的配置。

> <font color="#FF4500" size='5px'>通过占位符引用</font>

~~~java
@Component
@Data
@ToString
public class DataSourceProperties {
    @Value("${jdbc.driver}")
    private String driver;

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;
}

~~~

### 2.通过XML方式加载属性文件

~~~XML
<context:property-placeholder ignore-resource-not-found="true" location="classpath*:db.properties"/>
~~~

ignore-resource-not-found属性代表着是否允许文件不存在，配置为true，则是允许不存在。

## Bean的作用域

- <font color="#FF4500" size='3px'>单例（singleton）：</font>它是默认的选择，在整个应用中，Spring只为其生成一个Bean的实例
- <font color="#FF4500" size='3px'>原型（prototype）：</font>当每次注入，或者通过Spring IoC容器获取Bean时，Spring都会为他创建一个新的实例。
- <font color="#FF4500" size='3px'>会话（session）：</font>在Web应用中使用，就是在会话过程中Spring只创建一个实例。
- <font color="#FF4500" size='3px'>请求（request）：</font>在Web应用中，再一次请求中Spring会创建一个实例。

## Spring EL

- 使用Bean的ID来用Bean
- 调用制定对象的方法和访问对象的属性
- 进行运算
- 提供正则表达式进行匹配
- 集合配置

<font size='5px' color='#9932CC '>基本使用:</font>

~~~java
 	@Value("#{role}")
    private Role role;

    @Value("#{role.id}")
    private Long id;

    @Value("#{'你好啊'}")
    private String note;

    @Value("#{T(Math).PI}")
    private double pi;

    @Value("#{T(Math).random()}")
    private double random;

    @Value("#{role.id==1}")
    private boolean eqaulNum;

    @Value("#{role.note eq '你好啊'}")
    private boolean eqaulString;

    @Value("#{role.id==1?10:15}")
    private Long number;


    @Value("#{role.note?:'为空'}")
    private String defaultString;
~~~

## 面向切面编程

### 面向切面术语

- <font size='5px' color='#FF4500'>1.切面（Aspect）</font>

  通过是切面开启后，切面的方法。他根据在代理对象真实方法调用前、后的顺序和逻辑区分，他和约定游戏的例子里的拦截器的方法十分接近。

- <font size='5px' color='#FF4500'>2.通知（Adice）</font>

  - <font size='3px' color='#008B00'>前置通知（before）</font>

    在动态代理反射原有对象方法或者执行环绕通知前执行的通知功能.

  - <font size='3px' color='#008B00'>后置通知（after）</font>

    在动态代理反射原有对象方法或者执行环绕通知后执行的通知功能。无论是否抛出异常，他都会执行。

  - <font size='3px' color='#008B00'>返回通知（afterReturning）</font>

    在动态代理反射原有对象方法或者执行环绕通知后执行的通知功能.

  - <font size='3px' color='#008B00'>异常通知（afterThrowing）</font>

    在动态代理发射原有对象方法或者执行环绕通知产生异常后执行的通知工能。

  - <font size='3px' color='#008B00'>环绕通知（aroundThrowing）</font>

  ​      在动态代理中，他可以取代当前被拦截对象的方法。

- <font size='5px' color='#FF4500'>3.引入（introduction）</font>

  引入允许我们在现有类添加自定义的类和方法。

- <font size='5px' color='#FF4500'>4.切点（Pointcut）</font>

  在动态代理中，被切面拦截的方法就是一个切面，切面将可以将其切点和被拦截的方法按照一定的逻辑织入到约定流程当中。

- <font size='5px' color='#FF4500'>5.连接点（join point）</font>

  连接点是一个判断条件，由它可以指定那些事切点。对于指定的切点，Spring会生成代理对象去使用对应的切面对其拦截，否则就不会拦截他。

- <font size='5px' color='#FF4500'>6.织入（Weaing）</font>

  织入一个生成代理对象的过程。

## Spring AOP

### 1.实现方式

- 实现对应的接口来实现AOP。
- 使用XML配置AOP
- 使用@AspectJ注解驱动切面
- 使用AspectJ注入切面

### 2.切面配置

|      注 解      |                     通 知                      |                           备 注                            |
| :-------------: | :--------------------------------------------: | :--------------------------------------------------------: |
|     @Before     |          在被代理对象对得方法前先调用          |                          前置通知                          |
|     @Around     | 将被代理对象的方法封装起来，并用环绕通知取代它 | 环绕通知，他将覆盖原有方法，但是允许你通过反射调用原有方法 |
|     @After      |            在被代理对象的方法后调用            |                          后置通知                          |
| @AfterReturning |        在被代理对象的方法正常返回后调用        |    返回通知，要求被代理对象的方法执行过程中没有发生异常    |
| @AfterThrowing  |        再被代理对象的方法抛出异常后调用        |      异常通知，要求被代理对象的方法执行过程中产生异常      |

### 3.连接点

- **execution：**代表执行方法的时候会触发
- ***：**代表任意返回类型的方法
- **com.xxx.xxx:**代表类的全限定名。
- **printRole：**被拦截的方法名称
- **(..)：**任意的参数

| AspectJ指示器 | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| arg()         | 限制连接点匹配参数为制定类型的方法                           |
| @args         | 限制连接点匹配参数为制定注解标注的执行方法                   |
| execution     | 用于匹配连接点的执行方法，这是最常用的匹配，可以通过类似上面的正则表达式进行匹配 |
| this()        | 限制连接点匹配AOP代理的bean，引用为制定类型的类              |
| target        | 限制连接点匹配被代理对象为执行的类型                         |
| @target()     | 限制连接点匹配特定的执行对象，这些对象要符合制定的注解类型   |
| within()      | 限制连接点匹配制定的包                                       |
| @within()     | 限制连接点匹配的类型                                         |
| @annotation   | 限定陪陪带有制定注解的连接点                                 |

~~~java
@Aspect
public class RoleAspect {

    Logger logger = Logger.getLogger(RoleAspect.class);

    @Pointcut("execution(* service.impl.RoleServiceImpl.printRole(..)) && args(pojo.Role)")
    public void print() {
    }

    @Before("execution(* service.impl.RoleServiceImpl.printRole(..))")
    public void before() {
        logger.info(">>>>>>>>>> before");
    }

    @After("execution(* service.impl.RoleServiceImpl.printRole(..)) && args(pojo.Role)")
    public void after() {
        logger.info(">>>>>>>>>> after");
    }

    @AfterReturning("print()")
    public void afterReturning(JoinPoint point) {
        List<Object> args = Arrays.asList(point.getArgs());
        logger.info(">>>>>>>>>>> afterReturning" + args);
    }

    @AfterThrowing("print()")
    public void afterThrowing() {
        logger.info(">>>>>>>>> afterThrowing");
    }

    @Around("print()")
    public void around(ProceedingJoinPoint joinPoint) {
        logger.info("around before.......");
        try {
            joinPoint.proceed();
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        logger.info("around after......");
    }

    @DeclareParents(value = "myinterface.impl.RoleVerifierImpl+", defaultImpl = RoleVerifierImpl.class)
    public RoleVerfier roleVerifier;
}
~~~

​	XML配置文件

~~~XML
<aop:config>
        <!--        引用xmlAspect作为切面-->
     <aop:aspect ref="xmlAspect">
            <aop:pointcut id="printRole"expression="execution(* service.impl.RoleServiceImpl_2.printRole(..))"/>
            <!--        定义通知-->
            <aop:after method="after" pointcut-ref="printRole"></aop:after>
            <aop:before method="before" pointcut-ref="printRole"></aop:before>
            <aop:after-returning method="afterReturning" pointcut-ref="printRole"/>
            <aop:after-throwing method="afterThrowing" pointcut-ref="printRole"/>
     </aop:aspect>
</aop:config>
~~~

- \<aop:asp\>:用于定义切面类，这里是xmlAspect
- \<aop:before\>:定义前置通知
- \<aop:after>:定义后置桶子
- \<aop:after-throwing>:定义异常通知
- \<aop:after-returning>:定义返回通知



# Spring数据库事务管理

## ACID

- **原子性（ Atomicity ）**：原子性是指事物是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生
- **一致性（Consistency）：**事务前后数据的完整性毕业保持一致。
- **隔离性（Isolation）：**事务的隔离性是多个用户并发访问数据库时，数据库为没一个用户开启的事务，不能被其他事物的操作数据所干扰，多个并发事务之间要相互隔离.
- **持久性（Durability）：**持久性是指一个事务一旦被提交，他对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响。

### 事务隔离级别

- Read uncommitted(读未提交):就是一个事务可以读取另一个未提交事务的数据(脏读)
- Read committed(读提交):一个事务要等另一个事务提交后才能读取数据(不可重复读)
- Repeatable read(重复读):就是在开始读取数据(事务开启)时不允许修改操作.(幻读)
- Serializable(序列化):是最高的事务隔离界别,该级下,事务串行化顺序执行,可以避免脏读,不可重复读,与幻读,但是效率低下.

### 传播行为

- **TransactionDefinition.PROPAGATION_REQUIRED：** 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
- **TransactionDefinition.PROPAGATION_SUPPORTS：** 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- **TransactionDefinition.PROPAGATION_MANDATORY：** 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
- 不支持当前事务的情况：
	- **TransactionDefinition.PROPAGATION_REQUIRES_NEW**： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
	- **TransactionDefinition.PROPAGATION_NOT_SUPPORTED**： 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
	- **TransactionDefinition.PROPAGATION_NEVER**： 以非事务方式运行，如果当前存在事务，则抛出异常。

- **其他情况：**
	- **TransactionDefinition.PROPAGATION_NESTED：** 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

## Spring数据库事务管理器

- 事务的创建,提交和回滚是通过PaltformTransactionManager接口来完成.
- 当事务产生异常时会回滚事务,在默认的实现中所有的异常都会回滚,我们可以通过配置去修改在某些异常发生时回滚或者不会滚事务.
- 当无遗产时,会提交事务.

~~~mermaid
graph BT 
	DataSourceTransactionManager-->AbstractPlatformTransactionManager 
	AbstractPlatformTransactionManager-->PlatformTransactionManager
	WebSphereUowTransactionManager-->CallbackPreferringPlatformTransactionManager
	CallbackPreferringPlatformTransactionManager-->PlatformTransactionManager
	HibernateTrans-->sourceTransactionManager
	ResourceTransactionManager-->PlatformTransactionManager
	DataSourceTransactionManager-->ResourceTransactionManager
	CciLocalTransactionManager-->AbstractPlatformTransactionManager
	CciLocalTransactionManager-->ResourceTransactionManager
~~~

### Transactional配置项


|         配置项         |           含义           |                             备注                             |
| :--------------------: | :----------------------: | :----------------------------------------------------------: |
|         value          |      定义事务管理器      | 它是Spring IOC容器里的一个Bean id,这个Bean需要实现接口PlatFormTransactionManager |
|   transactionManager   |           同上           |                             同上                             |
|       isolation        |         隔离级别         | 这是一个数据库在多个事务同事存在时的概念,默认值去数据库默认隔离级别. |
|      propagation       |         传播行为         |   传播行为是方法之间调用的问题.默认值Propagation.REQUIRED    |
|        timeout         |         超时时间         |       单位为秒,当超时时,会引发异常,默认会导致事务回滚        |
|        readOnly        |     是否开启只读职务     |                        默认值为false                         |
|      rollbackFor       |    回滚事务的异常定义    |  也就是只有当方法产生所定义异常时,才回滚事务,否则就提交事务  |
|  rollbackForClassName  |  回滚事务的异常类名定义  |               同rollbackFor,只是使用类名称定义               |
|     noRollbackFor      | 当产生哪些异常不会滚事务 |           当产生所定义异常时,Spring将继续提交事务            |
| noRollbackForClassName |     同noRollbackFor      |             同noRollbackFor,只是使用类的名称定义             |

<font size='4px' color = 'red'>Mysql拥有四种隔离级别,而默认的是可重复读.而Oracle只能支持读/写提交和序列化两种隔离界别,默认为读/写提交.</font>

# Spring MVC

## Web.xml配置详解

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
    <!--    配置spring IoC配置文件路径-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/spring-*.xml</param-value>
    </context-param>
    <!--    配置ContextLoadreListener用以初始化Spring IoC容器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--    配置DispatcherServlet-->
    <servlet>
        <!--        注意:SpringMVC框架会根据Servlet-name配置,
        找到/WEB-INF/dispatcher-servlet.xml作为配置文件载入到web工程中-->
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--        使用DisPatcher在服务器启动的时候就初始化-->
        <load-on-startup>2</load-on-startup>
    </servlet>
    <!--    servlet拦截配置-->
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
</web-app>
~~~

- 系统变量contextConfigLocation的配置,他会告诉SpringMVC其SpringIOC的配置文件在哪里,这样Spring就会找到这些配置文件去加载他们,如果是多个配置文件,可以使用逗号将他们分割开来,并且他还能支持正则式匹配,进行模糊匹配,这样就更加灵活了,其默认值为/WEB-INF/applicationContext.xml.

- ContextLoaderListener实现了接口ServletContextListener,通过Java Web容器的学习,我们知道ServletContextListener的作用是可以在整个Web工程前后加入自定义代码,所以可以在web工程处室话之前,它先完成对SpringIoC容器的初始化,也可以在Web工程关闭只是完成SpringIoC容器的资源进行释放
- 配置DispatcherServlet,首先是配置了servlet-name为dispatcher,这就意味着需要一个/WEB-INF/Dispatcher-servlet.xml文件(注意,servlet-name,这就意味着需要一个/web-inf/dispatcher-server.xml文件(注意,servlet-name和文件名的对应关系)与之对应,并且我们配置了在服务器启动期间就初始化它.
- 配置DiapatcherServlet拦截以后"do"结束的请求,这样所有以后缀"do"结尾的请求多会被他拦截.

## MVC配置文件详解

~~~xml
  <!--    使用注解驱动-->
    <mvc:annotation-driven/>
    <!--    定义扫描转载的包-->
    <context:component-scan base-package="mvc.*"/>
    <!--    定义视图解析器-->
    <!--    找到Web工程/WEB-INF/JSP文件夹,且文件结尾jsp的文件作为映射-->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/view/"/>
        <property name="suffix" value=".html"/>
    </bean>
~~~

- \<mvc:annotation-driven/>表示使用注解驱动SpringMVC.
- 定义一个扫描的包,用它来扫描对应的包,用来加载对应的控制器和其他的一些组件.
- 定义视图解析器,解析器定义了前缀和后缀.

## 请求流程

~~~mermaid
graph TB 
	A(用户)-->|1.发送请求| B(前端控制器)
	B-->|8.产生响应| A
	B-->|2.委托请求给处理器| C(页面控制器/处理器)
	C-->|5.返回ModelAndView| B
	C-->|3.调用业务对象| D(模型)
	D-->|4.返回模型数据| C
	B-->|6.渲染视图| E(视图)
	E-->|7.返回视图| B
~~~

## MVC初始化

### 初始化Spring IoC上下文

<font size='5' color='orange'>Java Web容器为其生命周期提供ServletContextListener接口,这个接口可以在Web容器初始化和结束期中执行一定的逻辑,通过实现它可以使得在DispatcherServlet初始化前就可以完成SpringIoC容器的初始化,也可以在结束期完成对SpringIoC容器的销毁,只要实现ServletContextListener接口的方法.</font>

### 初始化映射请求上下文

<font size='5' color='orange'>映射请求上下文是通过DispatcherServlet初始化的,他和普通的Servlet也是一样的,可以根据需要配置他在启动时初始化,或者等待用户第一次请求的时候进行初始化,或者等待用户第一次请求的时候进行初始化.								 		<font size='5' color='red'>注:在Web工程没有注册ContextLoaderListener时,这时DispatcherServlet就会在其初始化的时候对SpringIOC容器初始化.Spring IoC是一个耗时的操作,应该让DispatcherServle在服务器启动期间就完成Spring IoC容器的初始化,可在Web容器启动时初始化,也可在Web容器载入DispatcherServlet时初始化.建议在Web容器启动时初始化,</font></font>

~~~java
protected void initStrategies(ApplicationContext context) {
		initMultipartResolver(context);
		initLocaleResolver(context);
		initThemeResolver(context);
		initHandlerMappings(context);
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
		initViewResolvers(context);
		initFlashMapManager(context);
	}
~~~

- **MultipartResolve**r:文件解析器,用于支持服务器的文件上传
- **LocaleResolver**:国际化解析器,可以提供国际化的功能.
- **ThemeResolver**:主题解析器,类似于软件皮肤的转换功能.
- **HandlerMapping**:Spring MVC中十分重要的内容,他会包装用户提供一个控制器的方法和对他的一些拦截器,调用它就能够运行控制器
- **handlerAdapter**:处理器适配器,因为处理器会在不同的上下文中运行,所以SpringMVC会先找到合适的适配器,然后运行处理器服务方法,比如对于控制器的SimpleControllerHandlerAdapter,对于普通请求HttpRequestHandlerAdapter等.
- **HandlerExceptionResolver**:处理器异常解析器,处理器有可能产生异常,如果产生异常,则可以通过异常解析器来处理他.
- **RequestToViewNameTranslator**:视图逻辑名称转换器,有时候在控制器中返回一个视图的名称,通过他可以找到世纪的视图.当处理器没有返回逻辑视图名称等相关信息是,自动将请求URL映射为逻辑视图名.
- **ViewResolver**:视图解析器,当控制器返回后,通过视图解析器会把逻辑视图名称进行解析,然后定位世纪视图.

### 无配置文件启动Web

#### 定义初始化器

~~~java
/**
 * @author 肖
 * @version 1.0
 * @Package mvc.utils
 * @data 2019/12/12 16:55
 */
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    /**
     * spring ioc容器配置
     *
     * @return
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[]{};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"*.do"};
    }
}
~~~

- **getRootConfigClass**获取Spring IoC容器的java配置类,用以装在各类Spring Bean.
- **getServletConfigClasses**获取各类SpringMVC的URI和控制器的配置关系类,用以生成Web请求的上下文.
- **getServletMappings**定义DispatcherServlet拦截的请求

<font size='4' color=' DeepPink'>如果getRootConfigClass方法返回为空,不加载自定义的Bean到SpringIoC容器.getServletConfigClasses加载了WebConfig,则它就是URI和控制器的映射关系类.</font>

#### 配置MVC视图解析器

~~~java
@Configuration
@ComponentScan("mvc.*")
@EnableWebMvc
public class WebConfig {
    /**
     * 创建视图解析器
     *
     * @return 视图解析器
     */
    @Bean(name = "viewResolver")
    public ViewResolver initViewResolver() {
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix("/WEB-INF/view/");
        viewResolver.setSuffix(".html");
        return viewResolver;
    }
}
~~~

<font size='4px' color='DeepPink'>@EnableWebMvc，代表启动SpringMVC框架的配置。</font>

##  控制器的开发

@Controller和@RestController，@RestController注解相当于@ResponseBody+@Controller两个注解。

​	

- 获取参数
	- 通过@RequestParam注解，从Http请求中获取参数。
		- required是一个布尔值（boolean），默认为true，也就是不允许为空，如果要允许为空，则配置为false
		- defaultValue的默认值为‘\n\t\t\n\t\t\n\uE000\uE001\uE002\n\t\t\t\t\n,可以通过配置修改他它为你想要的内容
- 处理业务逻辑
- 绑定模型和视图

### 控制器接收各类请求参数

- 接收普通请求参数：通过参数名称和HTTP请求参数的名称保持一致来获取参数，如果不一致时没法获取到，这样的方式允许参数为空。
- 使用@RequestParam注解获取参数：如果参数@RequestParam注解，那么默认的情况下该参数不能为空，如果为空则系统抛出异常，如果希望允许它为空，那么要修改它的配置项required为false。
- 使用URL传递参数：它只支持GET请求。

~~~java
@RequestMapping(value = "getData/{id}", method = RequestMethod.GET)
public String pathVarible(@PathVariable("id") Long id) {
        logger.info(id);
        return "index";
}
~~~

### 保存并获取属性参数

- @RequestAttribut获取HTTP的请求（request）对象属性值，用来传递给控制器的参数
- @SessionAttribute在HTTP的会话（Session）对象属性值中，用来传递给控制器的参数
- @SessionAttributes，可以给它一个字符串数组，这个数组对应的是数据模型对应的键值对，然后讲这些键值对保存到Session中

### 拦截器