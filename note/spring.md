# spring

SpelExpressionParser:spel表达式解析器

DefaultParameterNameDiscover:参数名发现器

## aop

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