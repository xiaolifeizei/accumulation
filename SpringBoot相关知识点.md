### 配置加载顺序和优先级
JVM环境变量优先级>操作系统环境变量>application.properties>application.yml

### SpringBoot 加载顺序
#### 一、生成一个SpringApplication对象
1、WebApplicationType = 推测Web应用类型（NONE、REACTIVE、SERVLET）
2、从spring.factories中获取BootstrapRegistryInitializer对象
3、initializers = 从spring.factiories中获取ApplicationContextInitializer对象
4、listeners = 从spring.factiories中获取ApplicationListener对象
#### 二、SpringApplication对象.run()
1、获取SpringApplicationRunListener-->EventPublishingRunListener
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
