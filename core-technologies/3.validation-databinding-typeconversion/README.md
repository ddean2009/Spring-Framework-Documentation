# 3.验证，数据绑定，和类型转换

将验证视为业务逻辑是有利弊的，Spring提供了一种验证（和数据绑定）设计，不排除其中任何一种验证。具体来说，验证不应该绑定到Web层，应该易于本地化，并且应该可以插入任何可用的验证程序。考虑到这些问题，Spring提出了一个Validator接口，它既基础又在应用程序的每一层都非常可用。

数据绑定对于让用户输入动态绑定到应用程序的域模型（或用于处理用户输入的任何对象）非常有用。Spring提供了恰当命名的DataBinder来实现这一点。Validator和DataBinder组成validation包，该包主要用于但不限于MVC框架。

BeanWrapper是Spring框架中的一个基本概念，在很多地方都有使用。但是，你可能不需要直接使用BeanWrapper。然而，由于这是参考指南，我们还是要按规则做一些说明。我们在本章中解释BeanWrapper，因为如果你要使用它，那么在尝试将数据绑定到对象时，你很可能会这样做。

Spring的DataBinder和较低级别的BeanWrapper都使用PropertyEditorsSupport实现来解析和格式化属性值。PropertyEditor和PropertyEditorSupport类型是JavaBeans规范的一部分，本章也将对此进行解释。Spring3引入了一个core.convert包，它提供了一个通用的类型转换工具，以及一个更高级的“格式”包，用于格式化UI字段值。你可以使用这些包作为PropertyEditorSupport实现的简单替代方案。本章还将讨论这些问题。

> JSR-303/JSR-349 Bean Validation
>
> 从4.0版开始，Spring框架支持Bean Validation 1.0 \(JSR-303\) 和Bean Validation 1.1（JSR-349），以支持安装并使其适应Spring的Validator接口。
>
> 应用程序可以选择全局启用一次bean验证，如Spring验证中所述，并专门用于所有验证需求。
>
> 应用程序还可以为每个DataBinder实例注册额外的Spring Validator实例，如配置DataBinder中所述。这对于在不使用注解的情况下插入验证逻辑可能很有用。

