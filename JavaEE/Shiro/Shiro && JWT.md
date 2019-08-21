# Shiro && JWT

记录一下自己踩得坑..

需求：用户需要使用账号密码和登录，且前后端校验使用 token

方案一：

1. 在登录时并不调用 Shiro 的 Subject.login，而是仅仅生成一个 token 给前端
2. 在每次访问的时候则使用 ``JwtRealm`` 分别进行 ``鉴权`` 和 ``授权``



## 配置



### 1. SecurityManager

首先是 ``Shiro`` 的 ``SecurityManager `` ，禁用 session，``SubjectDao`` 的作用就是往 ``session`` 中存取权限消息，所以需要在 subjectDao 中配置

```java
@Bean
public SecurityManager securityManager() {
    DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
    defaultWebSecurityManager.setRealm(jwtRealm());
    DefaultSubjectDAO subjectDAO = new DefaultSubjectDAO();
    subjectDAO.setSessionStorageEvaluator(sessionStorageEvaluator());
    defaultWebSecurityManager.setSubjectDAO(subjectDAO);
    return defaultWebSecurityManager;
}

@Bean
protected SessionStorageEvaluator sessionStorageEvaluator() {
    DefaultWebSessionStorageEvaluator sessionStorageEvaluator = new DefaultWebSessionStorageEvaluator();
    sessionStorageEvaluator.setSessionStorageEnabled(false);
    return sessionStorageEvaluator;
}
```



### 2. Filter

首先是 ``ShiroFilter`` ，需要定义哪些访问路径需要鉴权，哪些访问路径需要放过

```java
@Bean("shiroFilter")
public ShiroFilterFactoryBean shiroFilter(SecurityManager securityManager) {
    ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
    shiroFilterFactoryBean.setSecurityManager(securityManager);
    shiroFilterFactoryBean.setFilters(new MapUtil().put("jwt", jwtFilter()));
    shiroFilterFactoryBean.setFilterChainDefinitionMap(getUrlFilterMap());
    shiroFilterFactoryBean.setUnauthorizedUrl("/403");
    shiroFilterFactoryBean.setSuccessUrl("/");
    return shiroFilterFactoryBean;
}

protected JwtFilter jwtFilter() {
    return new JwtFilter();
}
```

配置 filter，除了 ``shiroFilter`` 以外，还需要再配置一个我们自定义的 ``JwtFilter``，来做一些**获取token**和鉴权的工作

```java
public class JwtFilter extends AuthenticatingFilter {

    @Override
    protected AuthenticationToken createToken(ServletRequest request, ServletResponse response) throws Exception {
        String token = getToken(request);
        assertLegalJwt(token);
        return new JwtToken(token);
    }

    void assertLegalJwt(String jwts) {
    }

    @Override
    protected boolean onAccessDenied(ServletRequest request, ServletResponse response) throws Exception {
        if (isLoginRequest(request, response)) {
            return true;
        }
        return executeLogin(request, response);
    }

    private String getToken(ServletRequest request) {
        String token = WebUtils.toHttp(request).getHeader("Authorization");
        return token;
    }
}
```

注意第 18 行的 ``executeLogin`` 方法，此方法就是我们负责登录的核心类，在这里就相当于 ``Subject.login`` ，所以只要经过这个方法，当前线程的 subject 已经拥有，不需要再次手动调用，也不用担心 subject 搞丢

### 3. 一些 Realm 和凭证匹配器，Shiro 钩子函数之类的

```java
@Bean
public LifecycleBeanPostProcessor lifecycleBeanPostProcessor() {
    return new LifecycleBeanPostProcessor();
}
@Bean
public JwtRealm jwtRealm() {
    JwtRealm jwtRealm = new JwtRealm();
    jwtRealm.setCredentialsMatcher(hashedCredentialsMatcher());
    return jwtRealm;
}
@Bean
public HashedCredentialsMatcher hashedCredentialsMatcher() {
    HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();
    hashedCredentialsMatcher.setHashAlgorithmName("md5");
    hashedCredentialsMatcher.setHashIterations(2);
    return hashedCredentialsMatcher;
}
```



### 4. 开启注解检查

```java
@Bean
public DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator() {
    DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
    advisorAutoProxyCreator.setProxyTargetClass(true);
    return advisorAutoProxyCreator;
}
@Bean
public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(SecurityManager securityManager) {
    AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor = new AuthorizationAttributeSourceAdvisor();
    authorizationAttributeSourceAdvisor.setSecurityManager(securityManager);
    return authorizationAttributeSourceAdvisor;
}
```

​                               

### 5. Realm 授权和鉴权

