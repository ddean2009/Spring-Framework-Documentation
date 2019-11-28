# 1.Spring WebFlux

Spring框架中包含的原始Web框架Spring Web MVC是专门为Servlet API和Servlet容器而构建的。 反应性堆栈Web框架Spring WebFlux在更高版本5.0中添加。 它是完全非阻塞的，支持Reactive Streams背压，并在Netty，Undertow和Servlet 3.1+容器等服务器上运行。

这两个Web框架都反映了其源模块的名称（spring-webmvc和spring-webflux），并在Spring Framework中并存。 每个模块都是可选的。 应用程序可以使用一个模块或另一个模块，或者在某些情况下同时使用这两个模块，例如，带有响应式WebClient的Spring MVC控制器。

