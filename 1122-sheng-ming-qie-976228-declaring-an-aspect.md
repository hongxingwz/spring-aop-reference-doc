# 声明一个切面

激活了@Aspect的支持，任何一个在你应用上下文中定义的bean（标注@Aspect注解的类）将会自动被Spring发现，且用来配置Spring AOP,下面的例子展示了最小化的定义，定义了一个不是非常有用的切面：

一个在应用上下文中定义的普通bean， 指向一个bean的class标注了@Aspect注解：

```
<bean id="myAspect" class="org.xyz.NotVeryUsefulAspect">
    <!-- configure properties of aspect here as normal -->
</bean>
```



