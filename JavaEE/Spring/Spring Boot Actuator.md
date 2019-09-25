# Spring Boot Actuator

最近在 SpringCloud 中使用到了该组件，遂来记录一下

Spring Boot Actuator 最核心的功能一句话就是**用来监控和管理 Spring Boot 应用**，而 Spring Cloud 也是属于 SpringBoot 应用。细分一下其功能有以下几种

* 健康检查
* 审计
* 统计
* Http 追踪
* ...



## 一、健康检查

已制定方式检查服务下挂载的实例(Instance)的健康度，从而确认该实例(Instance)是否能够提供服务。根据检查结果，实例(Instance)会被判断为健康或不健康。对服务发出解析请求时，不健康的实例(Instance)不会返回给客户端



[Spring Boot Actuator:健康检查、审计、统计和监控](https://www.jianshu.com/p/d5943e303a1f)



