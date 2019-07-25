# Shiro 问题排查

org.springframework.web.filter.OncePerRequestFilter

``characterEncodingFilter``

``hiddenHttpMethodFilter``

``httpPutFormContentFilter``

``requestContextFilter``

``springShiroFilter``

``Tomcat WebSocket (JSR356)``

``org.apache.shiro.web.servlet.AbstractShiroFilter``

``org.springframework.web.servlet.HandlerExecutionChain`` // 获取一个 Handler 调用链



注意 ``AbstractShiroFilter``

```java
subject.execute(new Callable() {
    public Object call() throws Exception {
        updateSessionLastAccessTime(request, response);
        executeChain(request, response, chain);
        return null;
    }
});
```

> 设置回调函数，会对 session 进行 刷新，然后执行继续执行 过滤链

先进行 execute 然后再回调



进入 ``updateSessionLastAccessTime``

```java
if (!isHttpSessions()) { //'native' sessions
    Subject subject = SecurityUtils.getSubject();
    //Subject should never _ever_ be null, but just in case:
    if (subject != null) {
        Session session = subject.getSession(false);
        if (session != null) {
            try {
                session.touch();
            } catch (Throwable t) {
                log.error("session.touch() method invocation has failed.  Unable to update" +
                        "the corresponding session's last access time based on the incoming request.", t)
            }
        }
    }
}
```





然后继续进入过滤链

进入 ``org.apache.shiro.web.servlet.OncePerRequestFilter`` ，这个属于 Shiro 会进行一个 Filter 检查

``org.apache.shiro.web.servlet.AdviceFilter``



``org.apache.shiro.web.filter.PathMatchingFilter#preHandle``

```java
for (String path : this.appliedPaths.keySet()) {
    // If the path does match, then pass on to the subclass implementation for specific checks
    //(first match 'wins'):
    if (pathsMatch(path, request)) {
        log.trace("Current requestURI matches pattern '{}'.  Determining filter chain execution...", path);
        Object config = this.appliedPaths.get(path);
        return isFilterChainContinued(request, response, path, config);
    }
}
```

> 会对每个路径进行匹配，查看该路径所需要的权限，eg ``user:user:view``
>
> 



``org.springframework.web.servlet.DispatcherServlet#doDispatch`` // 所有请求的接收器



```java
HandlerExecutionChain mappedHandler = null;
// Determine handler for the current request.
// 看下有没有当前请求的 handler
mappedHandler = getHandler(processedRequest);
if (mappedHandler == null) {
	noHandlerFound(processedRequest, response);
	return;
}

// 如果有就继续获取 adapter
// org.springframework.web.servlet.HandlerAdapter
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());



```

