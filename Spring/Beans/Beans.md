## Beans

- #### **Bean标签解析**

  > - **默认标签(parseDefaultElement)**
  >
  >   > import
  >   >
  >   > alias
  >   >
  >   > bean
  >   >
  >   > beans
  >   >
  >   > ~~~java
  >   > //Bean标签解析方法
  >   > protected void processBeanDefinition(Element ele, BeanDefinitionParserDelegate delegate) {
  >   >        // 元素解析返回XML实例 包含beanName ，alias数组，id 属性
  >   >         BeanDefinitionHolder bdHolder = delegate.parseBeanDefinitionElement(ele);
  >   >       // 对自定义标签处理
  >   >      if (bdHolder != null) {
  >   >             bdHolder = delegate.decorateBeanDefinitionIfRequired(ele, bdHolder);
  >   >             try {
  >   >                 BeanDefinitionReaderUtils.registerBeanDefinition(bdHolder, this.getReaderContext().getRegistry());
  >   >             } catch (BeanDefinitionStoreException var5) {
  >   >                 this.getReaderContext().error("Failed to register bean definition with name '" + bdHolder.getBeanName() + "'", ele, var5);
  >   >             }
  >   >           // 对当前bd对象注册
  >   >             this.getReaderContext().fireComponentRegistered(new BeanComponentDefinition(bdHolder));
  >   >         }
  >   >     }
  >   > ~~~
  >   >
  >   > BeanDefinition是配置文件<bean>元素标签在容器中内部表示形式
  >   >
  >   > BeanDefinition的实现子类有RootBeanDefinition，ChildBean，Definition及GenericBeanDefinition
  >   >
  >   >  BeanDefinitionRegistry 是它们的容器，以map形式存入内存，后续直接读取map中配置信息
  >   >
  >   > <img src="image-20220128165503263.png" alt="image-20220128165503263" style="zoom:50%;" /> 
  >
  > - **自定义标签**
  >
  >   > TODO
  >
  > - 
