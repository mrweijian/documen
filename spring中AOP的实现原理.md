# 1、JDK动态代理

class字节码文件，见代码

# 2、Spring的AOP

**ProxyFactory** 

```java
ProxyFactory proxyFactory = new ProxyFactory();
proxyFactory.setTarget(new AServer());
proxyFactory.addAdvice((AfterReturningAdvice) (returnValue, method, args1, target) -> System.out.println(method.getName()+"_返回后"));
AServer proxy = (AServer)proxyFactory.getProxy();
proxy.testA();
```

**Advice的分类** 

1. Before Advice：方法之前执行 

2. After returning advice：方法return后执行，可以获取返回值 

3. After throwing advice：方法抛异常后执行 

4. After (finally) advice：方法执行完之后执行

5. Around advice：这是功能最强大的Advice，可以自定义执行顺序 

**Advisor**

一个Advisor是有一个Pointcut和一个Advice组成的

**AspectJ（有兴趣可以研究）**

AspectJ是在编译时对字节码进行了修改，所以如果想用AspectJ技术来生成代理对象 ，是需要用单独的 

AspectJ编译器的。

**Aspject中的注解，其中有五个是用来定义Advice的，表示代理逻辑，以及执行时机： **

1. @Before 

2. @AfterReturning 

3. @AfterThrowing 

4. @After 

5. @Around 

**Spring自己也提供了类似的执行实际的实现类：** 

1. 接口MethodBeforeAdvice，继承了接口BeforeAdvice 

2. 接口AfterReturningAdvice
3. 接口ThrowsAdvice 

4. 接口AfterAdvice 

5. 接口MethodInterceptor

**Spring会把五个注解解析为对应的Advice类： **

1. @Before：AspectJMethodBeforeAdvice，实际上就是一个MethodBeforeAdvice 

2. @AfterReturning：AspectJAfterReturningAdvice，实际上就是一个AfterReturningAdvice 

3. @AfterThrowing：AspectJAfterThrowingAdvice，实际上就是一个MethodInterceptor 

4. @After：AspectJAfterAdvice，实际上就是一个MethodInterceptor 

5. @Around：AspectJAroundAdvice，实际上就是一个MethodInterceptor 

**AbstractAdvisorAutoProxyCreator** 

DefaultAdvisorAutoProxyCreator的父类是AbstractAdvisorAutoProxyCreator。 

只要Spring容器中存在这个类型的 Bean，就相当于开启了AOP，AbstractAdvisorAutoProxyCreator实际上就是一个 BeanPostProcessor，所以在创建某个Bean时，就会进入到它对应的生命周期方法中。

**@EnableAspectJAutoProxy**

这个注解主要就是往Spring容器中添加了一个AnnotationAwareAspectJAutoProxyCreator类型的 

Bean。从而开启了AOP，并且还会解析@Before等注解 并生成Advisor。