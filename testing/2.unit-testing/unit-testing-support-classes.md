# 2.2单元测试支持类

Spring包含许多可以帮助进行单元测试的类。 它们分为两类：

* 一般测试实用程序
* Spring MVC测试实用程序

## 2.2.1 一般测试实用程序

org.springframework.test.util包中包含几个用于单元和集成测试的通用实用程序。

ReflectionTestUtils是基于反射的实用程序方法的集合。你可以在测试需要更改常量值，设置非公共字段，调用非公共setter方法或在测试应用程序代码时调用非公共配置或生命周期回调方法的场景中使用这些方法。用例如下：

* 容忍私有或受保护字段访问的ORM框架（例如JPA和Hibernate），而不是域实体中属性的公共setter方法。
* Spring支持注解（例如@Autowired，@Inject和@Resource），它们为私有或受保护字段，setter方法和配置方法提供依赖注入。
* 使用@PostConstruct和@PreDestroy等注解进行生命周期回调方法。

AopTestUtils是AOP相关实用程序方法的集合。你可以使用这些方法获取对隐藏在一个或多个Spring代理后面的基础目标对象的引用。例如，如果你已使用诸如EasyMock或Mockito之类的库将bean配置为动态模拟，并且模拟包装在Spring代理中，则可能需要直接访问底层模拟以配置对它的期望并执行验证。对于Spring的核心AOP实用程序，请参阅AopUtils和AopProxyUtils。

## 2.2.2 Spring MVC测试实用程序

org.springframework.test.web包中包含ModelAndViewAssert，你可以将其与JUnit，TestNG或任何其他用于处理Spring MVC ModelAndView对象的单元测试的测试框架结合使用。

> 单元测试Spring MVC控制器
>
> 要将Spring MVC Controller类单元测试为POJO，请使用ModelAndViewAssert与Spring的Servlet API模拟中的MockHttpServletRequest，MockHttpSession等结合使用。 有关Spring MVC和REST Controller类的完整集成测试以及Spring MVC的WebApplicationContext配置，请使用Spring MVC Test Framework。

