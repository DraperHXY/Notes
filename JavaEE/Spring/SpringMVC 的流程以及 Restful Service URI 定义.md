# SpringMVC 的流程以及 Restful Service URI 定义

## DispatcherServlet 类

这是我们关注的重点，根据 Servlet 的配置原则，和在学习 Spring5 测时候，我们都会将这个类在 ``web.xml`` 中进行配置，对于相应映射的请求，会在 DispatcherServlet 中进行处理。重点看 doService 这个方法，会有一个 doDispatch，这个里面就是 SpringMVC 的核心逻辑。

在 ``doPispatch`` 中，主要分为四个部分

1. 绑定一些 Attribute

> WebApplicationContext / LocaleResolver / ThemeResolver

2. 处理 Multipart

> 如果

3. Handler 处理

> 执行 Controller 的前置和后置处理器逻辑

4. 处理返回的 Model，呈现视图(并非渲染视图)





如何实现 Restful Web Service

* 识别资源
* 选择合适的资源粒度
* 设计 URI
* 选择合适的 Http 方法和返回码
* 设计资源的表述



| URI          | Http 方法 | 含义             |
| ------------ | --------- | ---------------- |
| /coffee/     | GET       | 获取全部咖啡信息 |
| /coffee/     | POST      | 添加新的咖啡信息 |
| /coffee/{id} | GET       | 获取特定咖啡信息 |
| /coffee/{id} | DELETE    | 删除特定咖啡信息 |
| /coffee/{id} | PUT       | 修改特定咖啡信息 |





```
vue init webpack hello-vue
npm i element-ui -S
npm i --save ant-design-vue
npm install vue-router --save-dev
npm install sass-loader node-sass webpack --save-dev
npm install style-loader css-loader --save-dev
npm install axios -s
npm install vuex --save

npm install

```

