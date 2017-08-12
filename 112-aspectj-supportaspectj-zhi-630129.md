# 11.2 @AspectJ 支持

@AspectJ提供了用标记有注解的普通Java类作为切面的风格。@AspectJ风格是通过AspectJ project的AspectJ 5 release引入的。Spring 具备解释AspectJ 5的能力，使用AspectJ提供的一个用来解析切点和匹配的库。AOP运行时是纯用Spring AOP实现的，没有依赖于AspectJ 编译或织入。

> 使用AspectJ 编译和织入可以使用全部的AspectJ language,这在Section 11.8 "Using AspectJ with Spring applications"有讨论



