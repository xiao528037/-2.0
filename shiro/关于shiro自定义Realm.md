# 关于shiro自定义Realm

 1.首先创建类，继承AuthorizingRealm类实现doGetAuthenticationInfo和doGetAuthorizationInfo方法，前者用于认证后者用于授权。

~~~java
package Realm;

import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

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
        String password = "123456";
        return new SimpleAuthenticationInfo(username, password, "abc");
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

2.测试，在新版本中IniSecurityManagerFactory这个类已经过期，所以使用DefaultSecurityManager的setRealm方法进行测试。

~~~java
import Realm.MyRealm;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;
import org.junit.Test;

public class TestShiroMyRealm {
    @Test
    public void testShiroMyRealm() {
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();
//        IniRealm realm = new IniRealm("classpath:shiro-myrealm.ini");
        MyRealm myRealm = new MyRealm();
        defaultSecurityManager.setRealm(myRealm);
        SecurityUtils.setSecurityManager(defaultSecurityManager);
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken("root", "123456");
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

测试结果

~~~java
需要认证的账号密码:root
登录成功
~~~

