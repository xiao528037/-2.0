# Shiro

身份验证：在应用中证明他是本人，一般提供如他们的身份ID一些标识信息来表明他就是他本人，如提供身份证，用户名/密码来证明，在shiro中，用户需要提供principals（身份）和credentials（证明）给shiro，从而应用能验证用户身份：

- principals：身份，及主题的表示属性，可以是任何都关系，如用户名，邮箱等，唯一即可。一个注意可以有多个principals，但只有Primary principals，一般是用户名/密码/手机号

- credentials：证明/凭证，即只有主题知道的安全之，如密码/数字证书等。

  最常见的principals和credentials组合就是用户密码。

  另外两个相关的概念是之前提到的Subject及Realm，分别是主题及验证主题的数据源

## Shiro入门

使用Maven进行项目构建。

~~~xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.9</version>
    </dependency>
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.1.3</version>
    </dependency>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.2.2</version>
    </dependency>
</dependencies>
~~~

配置shiro.ini，准备用户身份/凭证

~~~ini
[users]
zhang=123
wang=123
~~~

通过[user]指定了两个主题：zhangsan/123，wang/123

测试demo

~~~java
@Test
public void testHelloworld() {
    //1、获取SecurityManager工厂，此处使用Ini配置文件初始化SecurityManager  Factory<org.apache.shiro.mgt.SecurityManager> factory =
            new IniSecurityManagerFactory("classpath:shiro.ini");
    //2、得到SecurityManager实例 并绑定给SecurityUtils   org.apache.shiro.mgt.SecurityManager securityManager = factory.getInstance();
    SecurityUtils.setSecurityManager(securityManager);
    //3、得到Subject及创建用户名/密码身份验证Token（即用户身份/凭证）
    Subject subject = SecurityUtils.getSubject();
    UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");
    try {
        //4、登录，即身份验证
        subject.login(token);
    } catch (AuthenticationException e) {
        //5、身份验证失败
    }
    Assert.assertEquals(true, subject.isAuthenticated()); //断言用户已经登录
    //6、退出
    subject.logout();
}
~~~

- 首先通过 new IniSecurityManagerFactory 并指定一个 ini 配置文件来创建一个 SecurityManager 工厂；
- 接着获取 SecurityManager 并绑定到 SecurityUtils，这是一个全局设置，设置一次即可；
- 通过 SecurityUtils 得到 Subject，其会自动绑定到当前线程；如果在 web 环境在请求结束时需要解除绑定；然后获取身份验证的 Token，如用户名 / 密码；
- 调用 subject.login 方法进行登录，其会自动委托给 SecurityManager.login 方法进行登录；
- 如果身份验证失败请捕获 AuthenticationException 或其子类，常见的如： DisabledAccountException（禁用的帐号）、LockedAccountException（锁定的帐号）、UnknownAccountException（错误的帐号）、ExcessiveAttemptsException（登录失败次数过多）、IncorrectCredentialsException （错误的凭证）、ExpiredCredentialsException（过期的凭证）等，具体请查看其继承关系；对于页面的错误消息展示，最好使用如 “用户名 / 密码错误” 而不是 “用户名错误”/“密码错误”，防止一些恶意用户非法扫描帐号库；
- 最后可以调用 subject.logout 退出，其会自动委托给 SecurityManager.logout 方法退出。

在新版本中，IniSecurityManagerFactory类不推荐使用，所以通过DefaultSecurityManager来进行管理

~~~java
   @Test
    public void testHellowrld() {
        Logger logger = Logger.getLogger(TestShiro.class);
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();
        IniRealm realm = new IniRealm("classpath:shiro.ini");
        defaultSecurityManager.setRealm(realm);
        SecurityUtils.setSecurityManager(defaultSecurityManager);
        Subject subject = SecurityUtils.getSubject();
        //创建令牌
        UsernamePasswordToken token = new UsernamePasswordToken("zhang", "sd");
        try {
            subject.login(token);
            logger.info("----------> 登录成功.");
        } catch (UnknownAccountException uae) {
            logger.info("----------> 未知账号");
        } catch (IncorrectCredentialsException ice) {
            logger.info("密码错误.");
        } catch (Exception e) {
            System.out.println("--------> 未知错误!");
            e.printStackTrace();
        }
    }
~~~

- 首先通过 new IniSecurityManagerFactory 并指定一个 ini 配置文件来创建一个 SecurityManager 工厂；
- 接着获取 SecurityManager 并绑定到 SecurityUtils，这是一个全局设置，设置一次即可；
- 通过 SecurityUtils 得到 Subject，其会自动绑定到当前线程；如果在 web 环境在请求结束时需要解除绑定；然后获取身份验证的 Token，如用户名 / 密码；
- 调用 subject.login 方法进行登录，其会自动委托给 SecurityManager.login 方法进行登录；
- 如果身份验证失败请捕获 AuthenticationException 或其子类，常见的如： DisabledAccountException（禁用的帐号）、LockedAccountException（锁定的帐号）、UnknownAccountException（错误的帐号）、ExcessiveAttemptsException（登录失败次数过多）、IncorrectCredentialsException （错误的凭证）、ExpiredCredentialsException（过期的凭证）等，具体请查看其继承关系；对于页面的错误消息展示，最好使用如 “用户名 / 密码错误” 而不是 “用户名错误”/“密码错误”，防止一些恶意用户非法扫描帐号库；
- 最后可以调用 subject.logout 退出，其会自动委托给 SecurityManager.logout 方法退出。

**从如上代码可总结出身份验证的步骤**：

1. 收集用户身份 / 凭证，即如用户名 / 密码；
2. 调用 Subject.login 进行登录，如果失败将得到相应的 AuthenticationException 异常，根据异常提示用户错误信息；否则登录成功；
3. 最后调用 Subject.logout 进行退出操作。

如上测试的几个问题：

1. 用户名 / 密码硬编码在 ini 配置文件，以后需要改成如数据库存储，且密码需要加密存储；
2. 用户身份 Token 可能不仅仅是用户名 / 密码，也可能还有其他的，如登录时允许用户名 / 邮箱 / 手机号同时登录。

**身份认证流程**



![img](https://atts.w3cschool.cn/attachments/image/wk/shiro/4.png)

流程如下：

1. 首先调用 Subject.login(token) 进行登录，其会自动委托给 Security Manager，调用之前必须通过 SecurityUtils.setSecurityManager() 设置；
2. SecurityManager 负责真正的身份验证逻辑；它会委托给 Authenticator 进行身份验证；
3. Authenticator 才是真正的身份验证者，Shiro API 中核心的身份认证入口点，此处可以自定义插入自己的实现；
4. Authenticator 可能会委托给相应的 AuthenticationStrategy 进行多 Realm 身份验证，默认 ModularRealmAuthenticator 会调用 AuthenticationStrategy 进行多 Realm 身份验证；
5. Authenticator 会把相应的 token 传入 Realm，从 Realm 获取身份验证信息，如果没有返回 / 抛出异常表示身份验证失败了。此处可以配置多个 Realm，将按照相应的顺序及策略进行访问。

**Realm**

Realm：域，Shiro 从从 Realm 获取安全数据（如用户、角色、权限），就是说 SecurityManager 要验证用户身份，那么它需要从 Realm 获取相应的用户进行比较以确定用户身份是否合法；也需要从 Realm 得到用户相应的角色 / 权限进行验证用户是否能进行操作；可以把 Realm 看成 DataSource，即安全数据源。如我们之前的 ini 配置方式将使用 org.apache.shiro.realm.text.IniRealm。