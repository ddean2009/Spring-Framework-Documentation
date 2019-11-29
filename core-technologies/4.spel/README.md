# 4.SpEL Spring表达式语言

Spring表达式语言（简称“SpEL”）是一种功能强大的表达式语言，支持在运行时查询和操作对象图。语言语法类似于统一EL，但提供了其他功能，最显著的是方法调用和基本的字符串模板化功能。

虽然还有其他几种Java表达式语言可用，即--OGNL、MVEL和JBOSS EL，但是，为了命名一致性，创建了Spring表达式语言，为Spring社区提供了一个支持良好的表达式语言，可以在Spring的所有产品中使用。它的语言特性是由Spring产品组合中项目的需求驱动的，包括基于Eclipse的Spring工具套件中代码完成支持的工具需求。这就是说，SPEL基于technology-agnostic的API，它允许在需要时集成其他表达式语言实现。

虽然SPEL是Spring组合中expression求值的基础，但它不直接与Spring挂钩，可以独立使用。为了自我包含，本章中的许多示例将spel用作独立的表达式语言。这需要创建一些引导基础结构类，如解析器。大多数Spring用户不需要处理这个基础结构，只需要编写表达式字符串进行求值。这种典型用法的一个例子是将spel集成到创建基于XML或注解的bean定义中，如定义bean定义的表达式支持中所示。

本章介绍表达式语言的特性、API及其语言语法。在一些地方，Inventor和Society类被用作表达评估的目标对象。这些类声明和用于填充它们的数据列在本章末尾。

表达式语言支持如下的功能:

* Literal expressions
* Boolean and relational operators
* Regular expressions
* Class expressions
* Accessing properties, arrays, lists, and maps
* Method invocation
* Relational operators
* Assignment
* Calling constructors
* Bean references
* Array construction
* Inline lists
* Inline maps
* Ternary operator
* Variables
* User-defined functions
* Collection projection
* Collection selection
* Templated expressions

