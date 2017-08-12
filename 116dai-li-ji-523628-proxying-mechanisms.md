# 11.6 Proxying mechanisms

Spring AOP对于给定的目标对象使用JDK动态代理或CGLIB来创建代理\(JDK动态代理是首选的无论你选择哪一种创建代理的方式\)

如果要代理的目标对象实现了至少一个接口将会使用JDK动态代理。所有实现了目标类型的接口都会被代理。如果目标对象没有实现任何接口将会使用CGLIB来创建代理。

如果你想强制使用CGLIB代理（例如，代理目标对象定义的每一个方法，而不是实现了接口的方法\)你可以这样做。然而，这有一些你要考虑的建议：

* f**inal **方法不可以被通知，因为其不能被重写
* 从Spring3.2之后，不需要添加CGLIB到你的项目的类路径\(classpath\)，因为CGLIB类被重新打包在org.springframework并直接包括在spring-core JAR下。这意味着基于CGLIB的代理与JDK动态代理以同样的方式工作\(不需要引入额外的JAR包\)。
* 自从Spring4.0，你被代理的对象的构造器将不会被调用两次因为CGLIB代理实例将作为Objenesis被创建。仅当你的JVM对象不允许constructor bypassing，你可能看见两次调用和相关的debug log entries 从 Spring's AOP support。

为了强制使用CGLIB代理，设置&lt;aop:config&gt;元素的**proxy-target-clas**s属性值为**true**

```xml
<aop:config proxy-target-class="true">
    <!-- other beans defined here ... -->
</aop:config>
```

当使用@AspectJ自动代理支行的时间为了强制使用CGLIB代理，设置**&lt;aop:aspectj-autoproxy&gt;**元素的**"proxy-target-class"**属性为**true：**

```
<aop:aspectj-autoproxy proxy-target-class="true"/>
```

....... 没看懂

> 多重的&lt;aop:config/&gt;部分会在运被合并成一个单一的auto-proxy创建者工具，适用于最强壮代理的设置&lt;aop:config/&gt;部分指定。这也适用于&lt;tx:annotation-driver/&gt; 和 &lt;aop:aspectj-autoproxy&gt;元素。

> 要清楚：使用proxy-target-class="true"在&lt;tx:annotation-driven/&gt;，&lt;aop:aspect-autoproxy/&gt;或者使用&lt;aop:config/&gt;元素将强制使用CGLIB代理对于他们三个所有的元素



