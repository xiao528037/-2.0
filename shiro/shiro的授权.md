# shiro的授权

## 代码触发

通过if/else授权代码块完成

~~~java
Subject subject=SecurityUilts.getSubject();
if(subject.hasRole("admin")){
    //有权限
}else{
    //无权限
}
~~~

## 注解触发

通过在执行的java方法上防止相应的注解完成

~~~java
@RequiresRoles("admin")
public void hello(){
    //有权限
}
~~~

## 标签触发

在JSP/GSP页面通过相应的标签完成

~~~html
<shiro:hasRole name="admin">
	<!-- 有权限 -->
</shiro:hasRole>
~~~

