### 配置加载顺序和优先级
JVM环境变量优先级>操作系统环境变量>application.properties>application.yml

#### 1、配置文件所在的位置

```
默认值为：

classpath:/ < classpath:/config/ < file:./ < file:./config/

最先加载file:./config/位置的配置文件，如果配置了spring.config.location属性则取这个属性的值

```

#### 2、配置文件扩展名加载顺序

```

.yaml < .yml < .xml < .properties

```

#### 3、相同属性覆盖问题

```

相同属性值的规则为：先读取到的属性先生效，后读取到的配置文件会加载，但是不会生效

```

#### 4、属性互补

```

不同配置文件中的属性具有互补性，最终得到的是所有配置文件属性的合集

```

### SpringBoot 加载顺序
#### 一、生成一个SpringApplication对象

1、WebApplicationType = 推测Web应用类型（NONE、REACTIVE、SERVLET）

2、从spring.factories中获取BootstrapRegistryInitializer对象

3、initializers = 从spring.factories中获取ApplicationContextInitializer对象

4、listeners = 从spring.factories中获取ApplicationListener对象


#### 二、SpringApplication对象.run()

1、获取SpringApplicationRunListener-->EventPublishingRunListener（发布事件）

2、SpringApplicationRunListener.starting()

3、创建一个Spring容器

4、ApplicationContextInitializer-->初始化Spring容器

5、SpringApplicationRunListener.contextPrepared()

6、把传给run方法的配置类注册成为一个Bean

7、SpringApplicationRunListener.contextLoaded()

8、解配置类、扫描、启动Tomcat\Jetty\Undertow（@Import注解导入AutoConfigurationImportSelector，DeferredImportSelector）

9、SpringApplicationRunListener.started()

10、callRunners()-->调用实现了ApplicationRunner\ComandLineRunner接口的run方法

11、SpringApplicationRunListener.ready()

### SpringBoot 自动配置解析顺序

1、先扫描（如@Compontent、@Bean、@Configuration等）-->得到自定义的配置

2、执行AutonConfigurationImportSelector.selectImports()方法得到spring.factories中的自动配置类-->解析

### springboot 项目里使用.xml文件

#### 1、添加.xml到resource目录下

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
 
    <bean id="helloService" class="com.dc.springboot01.service.HelloService"/>
 
</beans>
```

#### 2、在启动类或config类上添加注解

```
@ImportResource(locations = {"classpath:beans.xml"})
```

