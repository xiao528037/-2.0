# shiro加密

## 概念

​	数据加密的基本过程就是对原来位明文的文件活数据按某种算法进行处理，使其成为不可读的一段代码，通常称为“密文”，使其只能在输入相应的密钥之后才能显示出原本内容，通过这样的途径来达到保护数据不被非法人员窃取，阅读的目的。该过程位解密，即将该编码信息转化为其原来数据的过程。

## 机密分类

1.对称加密

​	双方使用的同一个密钥，既可以加密又可以解密，这种加密方式称为对称加密，也成为单密钥加密。

2.非对称加密

​	一对密钥由公钥和私钥组成（可以使用很多对密钥）。私钥解密公钥加密数据，公钥解密私钥加密数据（私钥公钥可以互相加密解密）。

## 加密算法分类

1.单向加密

​	单向加密是不可逆的，也就是只能加密，不能解密。通常用来传输类似用户名和密码，直接将加密后的数据提交到后台，因为后台不需要知道用户名和密码，可以直接接受到的加密后的数据存储到数据库。

2.双向加密

​	通常分为对称性机密算法和非对称性加密算法，对于对称性加密算法，信息接受双方都需要事先制动密钥和加解密算法且其密钥是相同的，之后便是对数据进行加解密，非堆成算法与之不同，放松双方A，B实现均生成一堆密钥，然后A将自己的共有密钥发送给B,B将自己的共有密钥发送给A，如果A要给B发送消息，则先需要用B的共有密钥进行消息加密，然后发送给B端，此时B端再用自己的私有密钥进行消息解密，B向A发送消息时位同样的道理。

## 通过MD5对密码进行加密

~~~java
//模拟数据加密
import org.apache.shiro.crypto.hash.Md5Hash;
import org.junit.Test;

public class Md5Test {
    @Test
    public void md5Test() {
        //简易加密，第一个参数，需要加密的数据，第二个参数，盐，第三个参数，迭代的次数
        Md5Hash abcd = new Md5Hash("12344", "aecgsgfdgdfga", 2);
        System.out.println(abcd.toString());

    }
}
~~~

~~~Java
//自定义ream对数据进行校验
package Realm;

import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.util.SimpleByteSource;

public class MyRealm extends AuthorizingRealm {
    /**
     * 认证的方法
     *
     * @param token
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        UsernamePasswordToken upToken = (UsernamePasswordToken) token;
        String username = upToken.getUsername();
        System.out.println("需要认证的账号密码:" + username);
        //根据username去数据库中查询
        if (!"root".equals(username)) {
            //假设数据库只有一条账号位root的记录
            return null;
        }
        String password = "68abfd35580f7029a6cd666386c5fc29";
        return new SimpleAuthenticationInfo(username, password, new SimpleByteSource("aecgsgfdgdfga"), "abc");
    }

    /**
     * 授权的方法
     *
     * @param principals
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {

        return null;
    }
}

~~~

~~~java
//对HashedCredentialsMatcher类设置参数，并将其放入自定义的realm中
import Realm.MyRealm;
import org.apache.log4j.Logger;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;
import org.junit.Test;

public class TestShiroMyRealm {

    Logger logger = Logger.getLogger(TestShiroMyRealm.class);

    @Test
    public void testShiroMyRealm() {
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();
        //        IniRealm realm = new IniRealm("classpath:shiro-myrealm.ini");
        MyRealm myRealm = new MyRealm();
        //新建HashedCredentialsMatcher对象，以及对该对象进行属性设置，加密方式，迭代次数等。
        HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();
        hashedCredentialsMatcher.setHashAlgorithmName("MD5");
        hashedCredentialsMatcher.setHashIterations(2);
        //将其设置到自定义的realm中
        myRealm.setCredentialsMatcher(hashedCredentialsMatcher);
        defaultSecurityManager.setRealm(myRealm);
        SecurityUtils.setSecurityManager(defaultSecurityManager);
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken("root", "12344");
        //登录验证
        try {
            subject.login(token);
            System.out.println("登录成功");
        } catch (UnknownAccountException e) {
            System.out.println("账号错误....");
        } catch (IncorrectCredentialsException e) {
            System.out.println("密码错误....");
        }
    }
}

~~~

