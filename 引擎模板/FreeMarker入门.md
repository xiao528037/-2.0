# FreeMarker入门

## 1.XML配置文件

~~~XML
<!-- FreeMarker视图解析器  -->
    <bean id="freemarkerResolver" class="org.springframework.web.servlet.view.freemarker.FreeMarkerViewResolver">
        <property name="order" value="1" />
        <property name="suffix" value=".ftl" />
        <property name="contentType" value="text/html;charset=utf-8" />
        <property name="viewClass">
            <value>org.springframework.web.servlet.view.freemarker.FreeMarkerView</value>
        </property>
    </bean>
 
 
    <!-- freemarker的配置 -->
    <bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
        <property name="templateLoaderPath">
            <value>/WEB-INF/webpage/</value>
        </property>
        <property name="freemarkerSettings"><!-- 设置FreeMarker环境属性 -->
            <props>
                <prop key="template_update_delay">5</prop><!--刷新模板的周期，单位为秒 -->
                <prop key="default_encoding">UTF-8</prop><!--模板的编码格式 -->
                <prop key="locale">UTF-8</prop><!-- 本地化设置 -->
                <prop key="datetime_format">yyyy-MM-dd HH:mm:ss</prop>
                <prop key="time_format">HH:mm:ss</prop>
                <prop key="number_format">0.####</prop>
                <prop key="boolean_format">true,false</prop>
                <prop key="whitespace_stripping">true</prop>
                <prop key="tag_syntax">auto_detect</prop>
                <prop key="url_escaping_charset">UTF-8</prop>
            </props>
        </property>
    </bean>
~~~

## 2.java配置

~~~java
  /**
     * FreeMarker视图解析器
     */

    @Bean
    public FreeMarkerViewResolver initFreeMarkerViewResolver() {
        FreeMarkerViewResolver freeMarkerViewResolver = new FreeMarkerViewResolver();
        freeMarkerViewResolver.setOrder(1);
        freeMarkerViewResolver.setSuffix(".html");
        freeMarkerViewResolver.setContentType("text/html;charset=utf-8");
        freeMarkerViewResolver.setViewClass(new FreeMarkerView().getClass());
        return freeMarkerViewResolver;
    }

    /**
     * 创建Freemarker
     */
    @Bean
    public FreeMarkerConfigurer initFreeMarkerConfigurer() {
        FreeMarkerConfigurer freeMarkerConfigurer = new FreeMarkerConfigurer();
        freeMarkerConfigurer.setTemplateLoaderPath("/WEB-INF/view/");
        freeMarkerConfigurer.setDefaultEncoding("UTF-8");
        Properties properties = new Properties();
        properties.setProperty("classic_compatible", "true");
        properties.setProperty("template_update_delay","5");
        properties.setProperty("default_encoding","UTF-8");
        properties.setProperty("locale","UTF-8");
        properties.setProperty("datetime_format","yyyy-MM-dd HH:mm:ss");
        properties.setProperty("time_format","HH:mm:ss");
        properties.setProperty("number_format","0.####");
        properties.setProperty("boolean_format","true,false");
        properties.setProperty("whitespace_stripping","true");
        properties.setProperty("url_escaping_charset","UTF-8");
        freeMarkerConfigurer.setFreemarkerSettings(properties);
        return freeMarkerConfigurer;
    }
~~~

## 3.html使用

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>hello</title>
</head>
<body>
<h1>${username}</h1>
</body>
</html>
~~~

## 4.Controller

~~~java
@RequestMapping(value = "/test", method = RequestMethod.GET)
    public String select(Model model) {
        model.addAttribute("username", "hello world");
        return "index";
    }
~~~



## 语法

### 1、字符输出

```
${emp.name?if_exists}　　　　　　// 变量存在，输出该变量，否则不输出
${emp.name!}　　　　　　　　　　  // 变量存在，输出该变量，否则不输出

${emp.name?default("xxx")}     // 变量不存在，取默认值xxx 
${emp.name!"xxx"}    　　　　　　// 变量不存在，取默认值xxx
```

常用内部函数：

```
${"123<br>456"?html}    　　// 对字符串进行HTML编码，对html中特殊字符进行转义
${"str"?cap_first}    　　  // 使字符串第一个字母大写 
${"Str"?lower_case}        // 将字符串转换成小写 
${"Str"?upper_case}        // 将字符串转换成大写
${"str"?trim}              // 去掉字符串前后的空白字符
```

字符串的两种拼接方式拼接：

```
${"hello${emp.name!}"}     // 输出hello+变量名
${"hello"+emp.name!}       // 使用+号来连接，输出hello+变量名
```

可以通过如下语法来截取子串:

```
<#assign str = "abcdefghijklmn"/>

// 方法1
${str?substring(0,4)}  // 输出abcd

// 方法2
${str[0]}${str[4]}    // 结果是ae
${str[1..4]}    　　　 // 结果是bcde

// 返回指定字符的索引
${str?index_of("n")}
```

### 2、日期输出

```
${emp.date?string('yyyy-MM-dd')} //日期格式
```

### 3、数字输出(以数字20为例)

```
${emp.name?string.number}    　// 输出20
${emp.name?string.currency}    // ￥20.00 
${emp.name?string.percent}     // 20%
${1.222?int} 　　　　　　　　　　 // 将小数转为int，输出1

<#setting number_format="percent"/>    // 设置数字默认输出方式('percent',百分比)
<#assign answer=42/>    　　　　　　　　 // 声明变量 answer 42
#{answer}    　　　　　　　　 // 输出 4,200%
${answer?string}    　　　　 // 输出 4,200%
${answer?string.number} 　　// 输出 42
${answer?string.currency}  // 输出 ￥42.00
${answer?string.percent} 　// 输出 4,200%
#{answer}    　　　　　　　　// 输出 42
数字格式化插值可采用#{expr;format}形式来格式化数字,其中format可以是:
mX:小数部分最小X位
MX:小数部分最大X位
如下面的例子:
<#assign x=2.582/>
<#assign y=4/>
#{x; M2}    // 输出2.58
#{y; M2}    // 输出4
#{x; m2}    // 输出2.58
#{y; m2}    // 输出4.0
#{x; m1M2}  // 输出2.58
#{x; m1M2}  // 输出4.0
```

### 4、申明变量

```
<#assign foo=false/> // 声明变量,插入布尔值进行显示,注意不要用引号
${foo?string("yes","no")} // 当为true时输出"yes",否则输出"no"
```

申明变量的几种方式

```
<#assign name=value> 
<#assign name1=value1 name2=value2 ... nameN=valueN> 
<#assign same as above... in namespacehash>

<#assign name> 
capture this 
</#assign>

<#assign name in namespacehash> 
capture this 
</#assign>
```

### 5、if 逻辑判断（注意：elseif 不加空格）

```
<#if condition>
...
<#elseif condition2>
...
<#elseif condition3>
...
<#else>
...
</#if>
```

### 6、switch (条件可为数字，可为字符串)

```
<#switch value> 
<#case refValue1> 
....
<#break> 
<#case refValue2> 
....
<#break> 
<#case refValueN> 
....
<#break> 
<#default> 
```

### 7、集合 & 循环

```
// 遍历集合:
<#list empList! as emp> 
    ${emp.name!}
</#list>
empList?size 　　　// 取集合的长度
emp_index: 　　　　// int类型，当前对象的索引值 
emp_has_next:     // boolean类型，是否存在下一个对象
// 集合长度判断 
<#if empList?size != 0></#if> // 判断=的时候,注意只要一个=符号,而不是==
// 截取子集合：
empList[3..5] //返回empList集合的子集合,子集合中的元素是empList集合中的第4-6个元素
// seq_contains：判断序列中的元素是否存在
<#assign x = ["red", 16, "blue", "cyan"]> 
${x?seq_contains("blue")?string("yes", "no")}    // yes
${x?seq_contains("yellow")?string("yes", "no")}  // no
${x?seq_contains(16)?string("yes", "no")}        // yes
${x?seq_contains("16")?string("yes", "no")}      // no
// sort_by：排序（升序）
<#list movies?sort_by("showtime") as movie></#list>

// sort_by：排序（降序）
<#list movies?sort_by("showtime")?reverse as movie></#list>
```