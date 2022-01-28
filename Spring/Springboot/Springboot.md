## SpringBoot

- #### **Springboot 优点**

  1. 创建独立的Spring应用
  2. 嵌入式Tomcat，Jetty，undertow服务
  3. 自动装配Spring
  4. 提供生成就绪功能，指标，健康检测，外部配置
  5. 无XML配置

- #### **Springboot自动装配**

  > @SpringBootApplication 注解
  >
  > <img src="image-20220128125758202.png" alt="image-20220128125758202" style="zoom:50%;" /> 
  >
  > - **@SpringBootConfiguration & @Component  & @Bean**
  >
  >   > ~~~java
  >   > public @interface Configuration {
  >   >     @AliasFor(annotation = Component.class)
  >   >     String value() default "";
  >   >     // Configuration中Bean开启CGLIB代理（反射创建对象并放到IOC容器中）
  >   >     boolean proxyBeanMethods() default true;
  >   > }
  >   > 
  >   > //使用new方式创建组件，使用时创建
  >   > public @interface Component {
  >   >     String value() default "";
  >   > }
  >   > 
  >   > // initMethod、destroyMethod 钩子方法
  >   > public @interface Bean {
  >   >     @AliasFor("name")
  >   >     String[] value() default {};
  >   >     @AliasFor("value")
  >   >     String[] name() default {};
  >   >     boolean autowireCandidate() default true;
  >   >     String initMethod() default "";
  >   >     String destroyMethod() default "(inferred)";
  >   > }
  >   > 
  >   > ~~~
  >   >
  >   > ---
  >   >
  >   > @Component ，@controller，@repository，@service 功能相同，标识为扫描类
  >   >
  >   > ---
  >   >
  >   > org.springframework.context.annotation.ConfigurationClassPostProcessor处理@Configuation注解
  >   >
  >   > ---
  >   >
  >
  >
  > - **@EnableAutoConfiguration 自动读取配置**
  >
  >   > ~~~java
  >   > @AutoConfigurationPackage
  >   > @Import({AutoConfigurationImportSelector.class})
  >   > public @interface EnableAutoConfiguration {
  >   >     String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";
  >   >     Class<?>[] exclude() default {};
  >   >     String[] excludeName() default {};
  >   > }
  >   > // AutoConfigurationPackage 使用SpringBoot自动配置，用来将主配置类所在包及其子包中的类加载到Spring容器
  >   > @Import({Registrar.class})
  >   > public @interface AutoConfigurationPackage {
  >   >     String[] basePackages() default {};
  >   >     Class<?>[] basePackageClasses() default {};
  >   > }
  >   > 
  >   > //加载第三方依赖
  >   > public class AutoConfigurationImportSelector {
  >   >   
  >   > }
  >   > ~~~
  >   >
  >   > <img src="image-20220128152626452.png" alt="image-20220128152626452" style="zoom:50%;" /> 
  >   >
  >   > spring.factories 第三方配置类
  >
  > - **@ComponentScan 自动扫描组件**
  >
  > 
  
  

