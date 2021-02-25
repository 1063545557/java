## spring5

![](../%E5%B7%A5%E5%85%B7/spring_1.png)

### IOC（概念和原理）

#### 什么是IOC

1. 控制反转，把对象创建和对象之间的调用过程交给spring进行管理
2. 使用ioc的目的：降低耦合度

#### IOC底层原理

xml解析、工厂模式、反射

#### IOC过程

1. xml配置文件，配置创建的对象

<bean id="dao" class="com.atguigu.UserDao"></bean>

2. 有service类和dao类，创建工厂类

```java
class UserFctory{
    public static UserDao getDao(){
        String classValue=class属性值;//1.xml解析
        Class clazz=Class.forName(classValue);//2.通过反射创建对象
        return (UserDao)clazz.newInstance();
    }
}
```

#### IOC(接口)

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂
2. spring提供IOC容器两种实现方式：（两个接口）

+ **BeanFactory**：IOC容器基本实现，是spring内部的使用接口，不提供开发人员进行使用

  加载配置文件时候不会创建对象，在获取对象（使用）才去创建对象

+ **ApplicationContext**：BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员进行使用

  加载配置文件时候就会把配置文件对象进行创建

3. ApplicationContext接口有实现类

主要实现类**FileSystemXmlApplicationContext、ClassPathXmlApplicationContext**

### IOC操作 Bean管理

1. 什么是bean管理

+ spring创建对象
+ spring注入属性

2. bean管理操作有两种方式

+ 基于xml配置文件方式实现
+ 基于注解方式实现

#### 基于xml方式创建对象

<bean id="dao" class="com.atguigu.UserDao"></bean>

1. 在spring配置文件中，使用bean标签，标签里面添加对应的属性，就可以实现对象创建

2. bean标签常用属性

   id属性：唯一标识

   class属性：类全路径（包类路径）

3. 创建对象时候，默认也是执行无参构造方法完成对象创建

#### 基于xml方式注入属性

1. DI：依赖注入，就是注入属性，是IOC的具体实现

+ 第一种注入方式：使用set方法进行注入
  + 创建类，定义set方法
  + 在spring配置文件配置对象创建，配置属性注入

+ 第二种注入方式：使用有参构造进行注入

2. p名称空间注入（了解）

​    使用p名称空间注入，可以简化基于xml配置方式

​	第一步：添加p名称空间在配置文件中

​	xmlns:p="http://www.springframework.org/schema/p"

​	第二步：在bean标签里边进行操作

3. xml注入其他类型属性

   1. 字面量
      1. 设置null值
      2. 属性值包含特殊符号
   2. 注入属性-外部bean
      1. 创建两个类service类和dao类
      2. 在service调用dao里面的方法

   3. 注入属性-内部bean和级联赋值

4. xml注入集合属性

   1. 注入数组类型属性
   2. 注入List集合类型属性
   3. 注入map集合类型属性
      1. 创建类，定义数组、list、map、set类型属性，生成对应set方法
      2. 在spring的配置文件中进行配置
   4. 在集合里面设置对象类型值
   5. 把集合注入部分提取出来
      1. 在spring配置文件中引入名称空间util
      2. 使用util标签完成list集合注入提取

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    <!--p名称空间注入 -->
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:util="http://www.springframework.org/schema/util"
	xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/util
        https://www.springframework.org/schema/util/spring-util.xsd
http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd" >

    <bean id="book" class="com.spring5.book">
        <!--使用property完成属性注入-->
    	<property name="bname" value="张三"></property>
        <!--null值-->
        <property name="address">
        	<null/>
        </property>
        <property name="address1" value=""></property>
        <!--属性值包含特殊符号
				1.把《》进行转义 &lt; &gt;
				2.把带特殊符号内容写到CDATA-->
        <property name="address2">
        	<value><![CDATA[《南京》]]></value>
        </property>
    </bean>
	
	<!--p名称空间注入-->
	<bean id="book1" class="com.spring5.Book1" p:name="bname1" p:author="zhangsan"></bean>

    <bean id="orders" class="com.spring5.Orders">
        <!--有参构造注入属性-->
    	<constructor-arg name="oname" value="abc"></constructor-arg>
        <constructor-arg index="0" value="123"></constructor-arg>
    </bean>
	
	<!--外部bean注入-->
	<bean id="userService" class="com.spring5.UserService">
		<property name="userDao" ref="userDaoImpl"></property>
	</bean>
	<bean id="userDaoImpl" class="com.spring5.UserDaoImpl"></bean>
		
	<bean id="emp" class="com.spring5.Emp">
		<property name="ename" value="lucy"></property>
        <property name="gender" value="女"></property>
        <!--内部bean-->
        <property name="dept">
        	<bean id="dept" class="com.spring5.Dept">
            	<property name="dname" value="安保部"></property>
            </bean>
        </property>
        
        <!--级联赋值 第一种写法-->
        <property name="dept" ref="dept"></property>
        <!--级联赋值 第二种写法-->
        <property name="dept.dname" value="技术部"></property>
	</bean>
	<bean id="dept" class="com.spring5.Dept">
		<property name="dname" value="财务部"></property>
	</bean>

	<!--集合类型属性的注入-->
	<bean id="stu" class="com.spring5.Stu">
        <!--数组类型属性注入-->
		<property name="courses">
        	<array>
            	<value>java</value>
                <value>数据库</value>
            </array>
        </property>
        
        <!--list类型属性注入-->
		<property name="list">
        	<list>
            	<value>张三</value>
                <value>李四</value>
            </list>
        </property>
        
        <!--map类型属性注入-->
		<property name="maps">
        	<map>
            	<entry key="JAVA" value="java"></entry>
                <entry key="Python" value="python"></entry>
            </map>
        </property>
        
         <!--set类型属性注入-->
		<property name="sets">
        	<set>
            	<value>MySQL</value>
                <value>Redis</value>
            </set>
        </property>
        
        <!--注入list集合类型，值是对象-->
        <property name="courseList">
        	<list>
            	<ref bean="course1"></ref>
                <ref bean="course2"></ref>
            </list>
        </property>
	</bean>

	<bean id="course1" class="com.spring5.Course">
		<property name="cname" value="Spring5"></property>
	</bean>
	<bean id="course2" class="com.spring5.Course">
		<property name="cname" value="MyBatis"></property>
	</bean>

	<!--提取list集合类型属性注入-->
	<util:list id="bookList">
		<value>1</value>
        <value>2</value>
        <value>3</value>
	</util:list>
	<!--提取list集合类型属性注入使用-->
	<bean id="book" class="com.spring5.Book">
		<property name="list" ref="bookList"></property>
	</bean>

	<!--自动装配-->
	<bean id="emp" class="com.spring5.Emp" autowire="byName/byType">
        <property name="dept" ref="dept"></property>
	</bean>
	<bean id="dept" class="com.spring5.Dept"></bean>

	<!--直接配置连接池-->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <preperty name="url" value="jdbc:mysql://localhost:3306/userDb"></preperty>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>
	</bean>

	<!--引入外部属性文件-->
	<context:property-placeholder location="classpath:jdbc.properties"/>
	<!--配置连接池-->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClass}"></property>
        <preperty name="url" value="${prop.url}"></preperty>
        <property name="username" value="${prop.username}"></property>
        <property name="password" value="${prop.password}"></property>
	</bean>

	<!--开启组件扫描-->
	<context:component-scan base-package="com.spring5.dao,com.spring5.service">			</context:component-scan>

	<!--开启组件扫描细节配置-->
		<!--示例1
			use-default-filters="false" 表示现在不使用默认filter，自己配置filter
			context:include-filter,设置扫描哪些内容-->
	<context:component-scan base-package="com.spring5" use-default-filters="false">
		<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
	</context:component-scan>
		<!--示例2
			context:exclude-filter,设置不扫描哪些内容-->
	<context:component-scan base-package="com.spring5">
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"
	</context:component-scan>
</beans>
```

#### IOC操作bean管理（FactoryBean）

1. spring有两种类型bean,一种普通bean,另外一种是工厂bean(FactoryBean)
2. 普通bean：在配置文件中定义bean类型就是返回类型
3. 工厂bean：在配置文件定义bean类型可以和返回类型不一致
   1. 创建类，让这个类作为工厂bean，实现接口FactoryBean
   2. 实现接口里面的方法，在实现的方法中定义返回的bean类型

#### IOC操作bean管理（bean作用域）

1. 在spring里面，设置创建bean实列是单实例还是多实例
2. 在spring里面，默认情况下，bean是单实例对象

3. 如何设置单实例还是多实例
   1. 在spring配置文件bean标签里面有属性（scope）用于设置单实例还是多实例
   2. scope属性值
      1. singleton，默认值，单实例
      2. prototype，表示多实例对象
      3. 设置scope值是singleton时候，加载spring配置文件时候就会创建单实例，设置scope值是prototype时候，不是在加载spring配置文件时候创建对象，在调用getBean方法时候创建多实例对象
      4. request, Web项目中，给每一个http request新建一个Bean实例 
      5. session, Web项目中，给每一个http session新建一个Bean实例。 
      6.  GlobalSession:这个只在portal应用中有用，给每一个global http session新建一个Bean实例。 

#### IOC操作bean管理（bean生命周期）

1. bean生命周期
   1. 通过构造器创建bean实例（无参数构造）
   2. 为bean的属性设置值和对其他bean引用（调用set方法)
   3. 把bean实例传递给bean后置处理器的方法，执行postProcessBeforeInitialization
   4. 调用bean的初始化的方法（需要进行配置）init-method
   5. 把bean实例传递给bean后置处理器的方法，执行postProcessAfterInitialization
   6. bean可以使用了（对象获取到了）
   7. 当容器关闭时，调用bean的销毁方法（需要进行配置销毁的方法）destroy-method
2. 创建后置处理器
   1. 创建类实现接口BeanPostProcessor,创建后置处理器
   2. 后置处理器会为当前配置文件中的所有bean添加后置处理器

#### IOC操作bean管理（自动装配）

1. 什么是自动装配

   根据指定装配规则（属性名称和属性类型），spring自动将匹配的属性值进行注入

2. 自动装配过程(见spring配置文件)

#### IOC操作bean管理（外部属性文件）

1. 直接配置数据库信息

   1. 配置druid连接池
   2. 引入druid连接池依赖jar包

2. 引入外部属性文件配置数据库连接池

   1. 创建外部配置文件

   2. 把外部配置文件引入到spring配置文件中

      引入context名称空间，在spring配置文件使用标签引入外部属性文件

#### IOC操作bean管理（基于注解方式）

1. spring针对bean管理中创建对象提供注解
   1. @Component：value属性可以省略，默认值是类名称首字母小写
   2. @Service
   3. Controller
   4. Repository

上面四个注解功能是一样的，都可以用来创建bean实例

2. 基于注解方式实现对象创建
   1. 引入依赖
   2. 开启组件扫描
   3. 开启组件扫描细节配置
3. 基于注解方式实现属性注入
   1. @Autowired：根据属性类型进行自动装配
      1. 把service和dao对象创建，在service和dao类添加创建对象注解
      2. 在service里面注入dao对象，在service类添加dao类型属性，在属性上面使用注解
   2. @Qualifier：根据属性名称进行注入
      1. 这个@Qualifier注解的使用，和上面@Autowired一起使用
   3. @Resource：可以根据类型注入，可以根据名称注入（填写name参数）
   4. @Value：注入普通类型属性
4. 完全注解开发
   1. 创建配置类，替代xml配置文件
   2. @ComponentScan(basePackages={"com.spring5"})
   
   

SpelExpressionParser:spel表达式解析器

DefaultParameterNameDiscover:参数名发现器

### AOP(概念和原理)

1. 什么是AOP

   1. 面向切面编程，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率
   2. 通俗描述：不通过修改源代码的方式，添加新的功能

2. AOP底层原理

   1. AOP底层使用动态代理

      1. 两种情况的动态代理

         第一种，有接口情况，使用JDK动态代理

         创建接口实现类的代理对象，增强类的方法

         第二种，没有接口情况，使用CGLIB动态代理

         创建子类的代理对象，增强类的方法

      2. 

   2. JDK动态代理

      使用JDK动态代理，使用Proxy类里面的newProxyInstance方法创建代理对象

      ```java
      public static Object newProxyInstance(ClassLoader loader,
                                            类<?>[] interfaces,
                                            InvocationHandler h)
                                     throws IllegalArgumentException
      ```

      方法有三个参数：

      第一参数：类加载器

      第二参数：增强方法所在类，这个类实现的接口，支持多个接口

      第三参数：实现这个接口InvocationHandler,创建代理对象，写增强的方法

   3. CGLIB动态代理

   

- **Aspect：切面**，由一系列切点、增强和引入组成的模块对象，可定义优先级，从而影响增强和引入的执行顺序。事务管理（Transaction management）在java企业应用中就是一个很好的切面样例。
- **Join point：接入点**，程序执行期的一个点，例如方法执行、类初始化、异常处理。 在Spring AOP中，接入点始终表示方法执行。
- **Advice：增强**，切面在特定接入点的执行动作，包括 “around,” “before” and “after”等多种类型。包含Spring在内的许多AOP框架，通常会使用拦截器来实现增强，围绕着接入点维护着一个拦截器链。
- **Pointcut：切点**，用来匹配特定接入点的谓词（表达式），增强将会与切点表达式产生关联，并运行在任何切点匹配到的接入点上。通过切点表达式匹配接入点是AOP的核心，Spring默认使用AspectJ的切点表达式。
- **Introduction：引入**，为某个type声明额外的方法和字段。Spring AOP允许你引入任何接口以及它的默认实现到被增强对象上。
- **Target object：目标对象**，被一个或多个切面增强的对象。也叫作被增强对象。既然Spring AOP使用运行时代理（runtime proxies），那么目标对象就总是代理对象。
- **AOP proxy：AOP代理**，为了实现切面功能一个对象会被AOP框架创建出来。在Spring框架中AOP代理的默认方式是：有接口，就使用基于接口的JDK动态代理，否则使用基于类的CGLIB动态代理。但是我们可以通过设置`proxy-target-class="true"`，完全使用CGLIB动态代理。
- **Weaving：织入**，将一个或多个切面与类或对象链接在一起创建一个被增强对象。织入能发生在编译时 （compile time ）(使用AspectJ编译器)，加载时（load time），或运行时（runtime） 。Spring AOP默认就是运行时织入，可以通过`枚举AdviceMode`来设置。

![advice执行顺序](img/advice执行顺序.png)

## springmvc

![springmvc流程](../%E5%B7%A5%E5%85%B7/springmvc%E6%B5%81%E7%A8%8B.png)