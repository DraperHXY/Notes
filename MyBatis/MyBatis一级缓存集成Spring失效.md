# 一、MyBatis的一级缓存
MyBatis的一级缓存是在 SqlSession 的基础上实现，如果是同一个 SqlSession，在开启一级缓存的前提下就可以实现缓存，如果是不同的 SqlSession，缓存就无法实现，且事务的提交，回滚也是通过 同一个 sqlSession 进行调用。但 Spring 中，若不开启事务，每次执行 Sql 都会重新生成 sqlSession，故无法实现缓存

具体代码如下


SqlSessionTemplate.SqlSessionInterceptor 的这个方法这个类中，会检测是否开启事务


```java
private class SqlSessionInterceptor implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
      SqlSession sqlSession = getSqlSession(SqlSessionTemplate.this.sqlSessionFactory,
          SqlSessionTemplate.this.executorType, SqlSessionTemplate.this.exceptionTranslator);
      try {
        Object result = method.invoke(sqlSession, args);
        // 重点在这里
        if (!isSqlSessionTransactional(sqlSession, SqlSessionTemplate.this.sqlSessionFactory)) {
          sqlSession.commit(true);
        }
        return result;
      } catch (Throwable t) {
        //...
      } finally {
        //...
      }
    }
  }
```

```java
public static boolean isSqlSessionTransactional(SqlSession session, SqlSessionFactory sessionFactory) {
  notNull(session, NO_SQL_SESSION_SPECIFIED);
  notNull(sessionFactory, NO_SQL_SESSION_FACTORY_SPECIFIED);
  //在这里会获取 session
  SqlSessionHolder holder = (SqlSessionHolder) TransactionSynchronizationManager.getResource(sessionFactory);
  return (holder != null) && (holder.getSqlSession() == session);
}
```

```java
@Nullable
public static Object getResource(Object key) {
	Object actualKey = TransactionSynchronizationUtils.unwrapResourceIfNecessary(key);
    // 重点在这里
	Object value = doGetResource(actualKey);
	if (value != null && logger.isTraceEnabled()) {
		logger.trace("Retrieved value [" + value + "] for key [" + actualKey + "] bound to thread [" +
				Thread.currentThread().getName() + "]");
	}
	return value;
}
```

```java
@Nullable
private static Object doGetResource(Object actualKey) {
	Map<Object, Object> map = resources.get();
	if (map == null) {
		return null;
	}
	Object value = map.get(actualKey);
	// Transparently remove ResourceHolder that was marked as void...
	if (value instanceof ResourceHolder && ((ResourceHolder) value).isVoid()) {
		map.remove(actualKey);
		// Remove entire ThreadLocal if empty...
		if (map.isEmpty()) {
			resources.remove();
		}
		value = null;
	}
	return value;
}
```

简单说有个 map，会在事务结束前，就是 @Transactional 这个代码块里会计数，当所有SQL执行后，事务提交，会将map清零，从而导致 sqlSession 被 close，这样缓存就消失了

## 结论
Spring 集成 MyBatis 必须结合事务才能实现一级缓存