# Java高级知识

## 扩展Lambda表达式

### 基础语法

- Lambda表达式的基础语法：Java8中引入一个新的操作符"->"该操作符称为箭头操作符或Lambda操作符，箭头操作符将Lambda表达式拆分成两部分:
	- 左侧：Lambda表达式的参数列表。
	- 右侧：Lambda表达式中所需执行的功能，即Lambda体
- 语法一：无参数，无返回值:

~~~java
() -> System.out.println("就你也来开发java！")
~~~

- 语法二：有一个参数并且无返回值：

~~~java
(x) -> System.out.println(x)
~~~

- 语法三：若只有一个参数，小括号可以省略不写：

~~~java
 x -> System.out.println(x)
~~~

- 语法四：有两个以上的参数，有返回值，并且Lambda体中有多条语句

~~~java
Comparator<Integer> com = (x, y) -> {
          System.out.println("函数式接口");
          return Integer.compare(x, y);
       };
~~~

- 语法五：若Lambda体重只有一条语句，return和大括号可以省略不写

~~~java
Comparator<Integer> com = (x, y) -> Integer.compare(x, y);
~~~

- 语法六：Lambda表达式的参数列表的参数累心够可以省略不写，因为JVM编译器通过上下文推断出数据类型，即"类型推断":

~~~java
(Integer x, Integer y) -> Integer.compare(x, y);
~~~

<font size='4px' color='red'>Lambda表达式需要“函数式接口”的支持：函数式接口：接口中只有一个抽象方法的接口，称为函数式接口，可以使用注解，@Functionallnterface修饰可以检查是否是函数式接口</font>

### <font color='red'>扩展自定义注解</font>

​	注解是一种能别添加到java源代码中的元数据，方法，类，参数和包都可以用来注解修饰，注解可以看做一种特殊的标记，可以用子啊方法、类、参数和包上，程序在编译或者运行时可以检测到这些特别热的标记进行一些特殊的处理。

#### 注解的基本元素

- 修饰符：访问修饰符必须为public，不写默认为public
- 关键字：关键字为@interface；
- 注解名称：注解名称为自定义注解的名称，使用时还会用到；
- 注解类型元素：注解类型元素是注解中内容，可以理解成自定义接口的实现部分；

#### 使用元注解修饰注解

- @Target表明该注解可以应用的java元素类型

| Target类型                  |                             描述                             |
| :-------------------------- | :----------------------------------------------------------: |
| ElementType.TYPE            |             应用于类、接口（包括注解类型）、枚举             |
| ElementType.FIELD           |                应用于属性（包括枚举中的常量）                |
| ElementType.METHOD          |                          应用于方法                          |
| ElementType.PARAMETER       |                       应用于方法的形参                       |
| ElementType.CONSTRUCTOR     |                        应用于构造函数                        |
| ElementType.LOCAL_VARIABLE  |                        应用于局部变量                        |
| ElementType.ANNOTATION_TYPE |                        应用于注解类型                        |
| ElementType.PACKAGE         |                           应用于包                           |
| ElementType.TYPE_PARAMETER  |                1.8版本新增，应用于类型变量）                 |
| ElementType.TYPE_USE        | 1.8版本新增，应用于任何使用类型的语句中（例如声明语句、泛型和强制转换语句中的类型） |

- @Retention表明该注解的生命周期

| 生命周期类型            |                       描述                       |
| :---------------------- | :----------------------------------------------: |
| RetentionPolicy.SOURCE  |          编译时被丢弃，不包含在类文件中          |
| RetentionPolicy.CLASS   |     JVM加载时被丢弃，包含在类文件中，默认值      |
| RetentionPolicy.RUNTIME | 由JVM 加载，包含在类文件中，在运行时可以被获取到 |

- @Document

表明该注解标记的元素可以被JavaDoc或类似的工具文档化。

- Inherited

表明使用了@inherited注解的注解，所标记的类的子类也会拥有这个注解。

## 反射

### 反射机制的相关类

| 类名          | 用途                                             |
| ------------- | ------------------------------------------------ |
| Class类       | 代表雷的实体，在运行的Java应用程序中表示类和接口 |
| Field类       | 代表类的成员变量（成员变量也称为类的属性）       |
| Method类      | 代表类的方法                                     |
| Constructor类 | 代表类的构造方法                                 |

### Class类

Class代表类的实体，在运行的Java应用程序中表示类和接口。在这个类中提供了很多有用的方法，这里对他们简单的分类介绍。

- 获得类相关的方法

| 方法                       | 用途                                                   |
| -------------------------- | ------------------------------------------------------ |
| asSubclass(Class<U> clazz) | 把传递的类的对象转换成代表其子类的对象                 |
| Cast                       | 把对象转换成代表类或是接口的对象                       |
| getClassLoader()           | 获得类的加载器                                         |
| getClasses()               | 返回一个数组，数组中包含该类中所有公共类和接口类的对象 |
| getDeclaredClasses()       | 返回一个数组，数组中包含该类中所有类和接口类的对象     |
| forName(String className)  | 根据类名返回类的对象                                   |
| getName()                  | 获得类的完整路径名字                                   |
| newInstance()              | 创建类的实例                                           |
| getPackage()               | 获得类的包                                             |
| getSimpleName()            | 获得类的名字                                           |
| getSuperclass()            | 获得当前类继承的父类的名字                             |
| getInterfaces()            | 获得当前类实现的类或是接口                             |

- 获得类中属性相关方法

| 方法                          | 用途                   |
| ----------------------------- | ---------------------- |
| getField（String name）       | 获得某个共有的属性对象 |
| getFields（）                 | 获得所有共有的属性对象 |
| getDeclaredField(String name) | 获得某个属性对象       |
| getDeclaredFields()           | 获得所有属性对象       |

- 获得类中注解相关的方法

| 方法                                            | 用途                                   |
| ----------------------------------------------- | -------------------------------------- |
| getAnnotation(Class<A> annotationClass)         | 返回该类中与参数类型匹配的公有注解对象 |
| getAnnotations()                                | 返回该类所有的公有注解对象             |
| getDeclaredAnnotation(Class<A> annotationClass) | 返回该类中与参数类型匹配的所有注解对象 |
| getDeclaredMethods()                            | 获得该类所有方法                       |

- 类中其他重要的方法

| 方法                                                         | 用途                             |
| ------------------------------------------------------------ | -------------------------------- |
| isAnnotation()                                               | 如果是注解类型则返回true         |
| isAnnotationPresent(Class<? extends Annotation> annotationClass) | 如果是指定类型注解类型则返回true |
| isAnonymousClass()                                           | 如果是匿名类则返回true           |
| isArray()                                                    | 如果是一个数组类则返回true       |
| isEnum()                                                     | 如果是枚举类则返回true           |
| isInstance(Object obj)                                       | 如果obj是该类的实例则返回true    |
| isInterface()                                                | 如果是接口类则返回true           |
| isLocalClass()                                               | 如果是局部类则返回true           |
| isMemberClass()                                              | 如果是内部类则返回true           |

- Field类

	 [Field](https://developer.android.google.cn/reference/java/lang/reflect/Field)代表类的成员变量（成员变量也称为类的属性）。 

	| 方法                          | 用途                    |
	| ----------------------------- | ----------------------- |
	| equals(Object obj)            | 属性与obj相等则返回true |
	| get(Object obj)               | 获得obj中对应的属性值   |
	| set(Object obj, Object value) | 设置obj中对应属性值     |

	

- Method类

| 方法                               | 用途                                     |
| ---------------------------------- | ---------------------------------------- |
| invoke(Object obj, Object... args) | 传递object对象及参数调用该对象对应的方法 |

- ConStructor类

 [Constructor](https://developer.android.google.cn/reference/java/lang/reflect/Constructor)代表类的构造方法。 

| 方法                            | 用途                       |
| ------------------------------- | -------------------------- |
| newInstance(Object... initargs) | 根据传递的参数创建类的对象 |

