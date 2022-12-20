如何定制 Bean

主要有以下两个方向

### 1. LifeCycle Callback

InitializingBean  
DisposbleBean

### 2. xxxAware 接口
ApplicationContextAware  
BeanFactoryAware  
BeanNameAware  


在 AbstractBeanFactory 中获取 Bean

其中有个方法``getBean``，在其中的实现``doGetBean``获取 Bean，如果获取失败，则会进行创建

Bean 的创建
AbstractAutowareCapableBeanFactory  中有




