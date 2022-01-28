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
  >   > 通过BeanDefinitionParserDelegate的命名空间判断是否是自定义配置
  >   >
  >   > <img src="image-20220128172728436.png" alt="image-20220128172728436" style="zoom:50%;" /> 

- #### **Bean的加载**

  > org.springframework.beans.factory.BeanFactory
  >
  > <img src="image-20220128173529246.png" alt="image-20220128173529246" style="zoom:50%;" /> 
  >
  >  ~~~java
  >  // doGetBean 重载
  >  public Object getBean(String name){
  >    return doGetBean(name,null,null,false);
  >  }
  >  // doGetBean 加载方法
  >  protected <T> T doGetBean(String name, @Nullable Class<T> requiredType, @Nullable Object[] args, boolean typeCheckOnly) throws BeansException {
  >          String beanName = this.transformedBeanName(name);
  >          Object sharedInstance = this.getSingleton(beanName);
  >          Object beanInstance;
  >          if (sharedInstance != null && args == null) {
  >              if (this.logger.isTraceEnabled()) {
  >                  if (this.isSingletonCurrentlyInCreation(beanName)) {
  >                      this.logger.trace("Returning eagerly cached instance of singleton bean '" + beanName + "' that is not fully initialized yet - a consequence of a circular reference");
  >                  } else {
  >                      this.logger.trace("Returning cached instance of singleton bean '" + beanName + "'");
  >                  }
  >              }
  >  
  >              beanInstance = this.getObjectForBeanInstance(sharedInstance, name, beanName, (RootBeanDefinition)null);
  >          } else {
  >              if (this.isPrototypeCurrentlyInCreation(beanName)) {
  >                  throw new BeanCurrentlyInCreationException(beanName);
  >              }
  >  
  >              BeanFactory parentBeanFactory = this.getParentBeanFactory();
  >              if (parentBeanFactory != null && !this.containsBeanDefinition(beanName)) {
  >                  String nameToLookup = this.originalBeanName(name);
  >                  if (parentBeanFactory instanceof AbstractBeanFactory) {
  >                      return ((AbstractBeanFactory)parentBeanFactory).doGetBean(nameToLookup, requiredType, args, typeCheckOnly);
  >                  }
  >  
  >                  if (args != null) {
  >                      return parentBeanFactory.getBean(nameToLookup, args);
  >                  }
  >  
  >                  if (requiredType != null) {
  >                      return parentBeanFactory.getBean(nameToLookup, requiredType);
  >                  }
  >  
  >                  return parentBeanFactory.getBean(nameToLookup);
  >              }
  >  
  >              if (!typeCheckOnly) {
  >                  this.markBeanAsCreated(beanName);
  >              }
  >  
  >              StartupStep beanCreation = this.applicationStartup.start("spring.beans.instantiate").tag("beanName", name);
  >  
  >              try {
  >                  if (requiredType != null) {
  >                      beanCreation.tag("beanType", requiredType::toString);
  >                  }
  >  
  >                  RootBeanDefinition mbd = this.getMergedLocalBeanDefinition(beanName);
  >                  this.checkMergedBeanDefinition(mbd, beanName, args);
  >                  String[] dependsOn = mbd.getDependsOn();
  >                  String[] var12;
  >                  if (dependsOn != null) {
  >                      var12 = dependsOn;
  >                      int var13 = dependsOn.length;
  >  
  >                      for(int var14 = 0; var14 < var13; ++var14) {
  >                          String dep = var12[var14];
  >                          if (this.isDependent(beanName, dep)) {
  >                              throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
  >                          }
  >  
  >                          this.registerDependentBean(dep, beanName);
  >  
  >                          try {
  >                              this.getBean(dep);
  >                          } catch (NoSuchBeanDefinitionException var31) {
  >                              throw new BeanCreationException(mbd.getResourceDescription(), beanName, "'" + beanName + "' depends on missing bean '" + dep + "'", var31);
  >                          }
  >                      }
  >                  }
  >  
  >                  if (mbd.isSingleton()) {
  >                      sharedInstance = this.getSingleton(beanName, () -> {
  >                          try {
  >                              return this.createBean(beanName, mbd, args);
  >                          } catch (BeansException var5) {
  >                              this.destroySingleton(beanName);
  >                              throw var5;
  >                          }
  >                      });
  >                      beanInstance = this.getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
  >                  } else if (mbd.isPrototype()) {
  >                      var12 = null;
  >  
  >                      Object prototypeInstance;
  >                      try {
  >                          this.beforePrototypeCreation(beanName);
  >                          prototypeInstance = this.createBean(beanName, mbd, args);
  >                      } finally {
  >                          this.afterPrototypeCreation(beanName);
  >                      }
  >  
  >                      beanInstance = this.getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
  >                  } else {
  >                      String scopeName = mbd.getScope();
  >                      if (!StringUtils.hasLength(scopeName)) {
  >                          throw new IllegalStateException("No scope name defined for bean '" + beanName + "'");
  >                      }
  >  
  >                      Scope scope = (Scope)this.scopes.get(scopeName);
  >                      if (scope == null) {
  >                          throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
  >                      }
  >  
  >                      try {
  >                          Object scopedInstance = scope.get(beanName, () -> {
  >                              this.beforePrototypeCreation(beanName);
  >  
  >                              Object var4;
  >                              try {
  >                                  var4 = this.createBean(beanName, mbd, args);
  >                              } finally {
  >                                  this.afterPrototypeCreation(beanName);
  >                              }
  >  
  >                              return var4;
  >                          });
  >                          beanInstance = this.getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
  >                      } catch (IllegalStateException var30) {
  >                          throw new ScopeNotActiveException(beanName, scopeName, var30);
  >                      }
  >                  }
  >              } catch (BeansException var32) {
  >                  beanCreation.tag("exception", var32.getClass().toString());
  >                  beanCreation.tag("message", String.valueOf(var32.getMessage()));
  >                  this.cleanupAfterBeanCreationFailure(beanName);
  >                  throw var32;
  >              } finally {
  >                  beanCreation.end();
  >              }
  >          }
  >  
  >          return this.adaptBeanInstance(name, beanInstance, requiredType);
  >      }
  >  ~~~
  >
  > 
