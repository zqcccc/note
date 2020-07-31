# Spring

## Bean

### 配置

Bean 本质是 java 中的类，而 spring 中的 bean 其实就是对实体类的引用，来生产 java 类对象，从而实现生产和管理 bean

Bean 常用的配置：Properties 文件和 XML 文件，常用的是 XML 文件

beans元素常用属性：

- id：唯一标识符
- name：可以为 bean 指定多个名字，之间用逗号或者分号隔开
- class：具体实现类，用类的全限定名
- scope：用来设定 bean 的作用域，不指定就是singleton（单例）

### 实例化

构造器实例化、静态工厂方式实例化、实力工厂方式实例化

最常用的是构造器实例化

### 作用域

singleton, prototype, request, session, globalSession, application, websocket

Singleton 是 Spring 容器默认的作用域，作用域为 singleton 的时候，Spring容器就只存在一个共享的 Bean 实例

## AOP

是 OOP 的延伸和补充

### 术语

切面 aspect：封装的用于横向插入系统功能（如事务、日志等）的类

连接点 Joinpoint：在程序执行过程中的某个阶段点

切入点 Pointcut：切面与程序流程的交叉点，即那些需要处理的连接点

通知/增强处理 Advice：AOP 框架在特定的切入点执行的增强处理，即在定义好的切入点处所要执行的程序代码。可以将其理解为切面类中的方法

目标对象 target object：之所有被通知的对象，也被成为被增强对象。如果 AOP 框架采用的是动态的 AOP 实现，那么该对象就是一个被代理对象

代理 proxy：将通知应用到目标对象之后，被动态创建的对象

织入 weaving：将切面代码插入到目标对象上，从而生成代理对象的过程

### JDK动态代理

JDK 动态代理是通过 java.lang.reflect.Proxy 类来实现的，我们可以调用 Proxy 类的 newProxyInstance() 方法来创建代理对象。对于使用业务接口的类，Spring 默认会使用 JDK 动态代理来实现 AOP

### CGLIB代理

如果想代理没有实现皆接口的类，那么可以使用 CGLIB 代理

CGLIB (Code Generation Library) 是一个高性能开源的代码生成包， 它采用非常底层的字节码技术，对指定的目标类生成一个子类，并对子类进行增强

### 基于代理类的 AOP 实现

#### 通知类型

环绕通知，目标方法执行前后实施增强，用于日志、事务管理

前置通知，目标方法执行前，用于权限管理

后置通知，目标方法执行后，用于关闭流、上传文件、删除临时文件等

异常抛出通知，用于处理异常记录日志等

引介通知，在目标类中添加一些新的方法和属性，用于修改老版本程序

### ProxyFactoryBean

是 FactoryBean 接口的实现类，FactoryBean 负责实例化一个 Bean，而 ProxyFactoryBean 负责为其他 Bean 创建代理实例。在 Spring 中，使用 ProxyFactoryBean 是创建 AOP 代理的基本方式

target，代理的目标对象

proxyInterfaces，代理要实现的接口

proxyTargetClass，是否对类代理而不是接口

interceptorNames，需要织入目标的 Advice

singleton，返回的代理是否为单例，默认为 true

optimize，当设置为 true 时，强制使用 CGLIB

### AspectJ开发

AspectJ 是一个基于 java 语言的 AOP 框架，spring 2.0 后就引入了对 AspectJ 的支持，新的 Spring 框架都建议使用 AspectJ 来开发 AOP

使用 AspectJ 时间 AOP 有两种方式：一种是基于 XML 的声明式 AspectJ，另一种是基于注解的声明式 AspectJ

#### 基于 XML 的声明式 AspectJ

基于 XML 的声明式 AspectJ 是指通过 XML 文件来定义切面、切入点及通知，所有的切面、切入点和通知都必须定义在 `<aop:config>` 元素内