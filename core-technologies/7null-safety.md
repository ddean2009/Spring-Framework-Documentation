# 7.Null-safety

虽然Java不允许用它的类型系统来表示null-safety，但是Spring框架现在在org.springframework.lang包中提供以下注解，以允许你声明API和字段的可空性：

@Nullable：注解，指示特定参数、返回值或字段可以为空。 @NonNull：表示特定参数、返回值或字段不能为空的注解（对于分别应用@NonNullApi 和@NonNullFields的参数/返回值和字段不需要）。 @NonNullApi：包级别的注解，声明非空作为参数和返回值的默认语义。 @NonNullFields:包级别的注解，声明非空字段作为字段的默认语义。

Spring框架本身利用这些注解，但它们也可以用于任何基于Spring的Java项目中，以声明 null-safe API和可选的 null-safe 字段。尚不支持泛型类型参数、varargs和数组元素为空性，但应在即将发布的版本中提供，有关最新信息，请参阅spr-15942。可以为空性声明预计将在Spring框架版本之间进行微调，包括较小的版本。方法体内部使用的类型的Nullability超出了此功能的范围。

> 其他公共库（如Reactor和spring data）提供了null-safe API，这些API使用类似的空性安排，为spring应用程序开发人员提供了一致的总体体验。

## 7.1 使用案例

除了为SpringFrameworkAPI的可空性提供显式声明外，这些注解还可以由一个IDE（如IDEA或Eclipse）使用，以提供与null-safety相关的有用警告，以避免在运行时发生NullPointerException。

它们还用于在Kotlin项目中使SpringAPI为空，因为Kotlin本身支持空安全。Kotlin支持文档中提供了更多详细信息。

## 7.2 JSR-305 元注解

Spring注解是用JSR 305注解（一个隐藏的，但广泛分布的JSR）进行元注解的。JSR-305元注解允许工具供应商（如IDEA或Kotlin）以通用的方式提供空安全支持，而无需对Spring注解进行硬编码支持。

没有必要也不建议向项目类路径添加JSR-305依赖项，以利用Spring空安全API。只有基于Spring的库等项目在其代码库中使用空安全注解时，才应添加com.google.code.findbugs:jsr305:3.0.2和compileOnly Gradle配置或maven provided范围以避免编译警告。

