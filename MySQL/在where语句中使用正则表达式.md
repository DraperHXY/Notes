# 在 where 语句中使用正则表达式

```SQL
SELECT pro_name
FROM production
WHERE pro_name REGEXP '.000'
```

| prod         |
| ------------ |
| JetPack 1000 |
| JetPack 2000 |



在这里看起来 ``LIKE`` 比较相像，但 LIKE 要加入像 % 的通配符才能起到一样的作用



在 MySQL 中的正则表达式在 3.23.4 后不区分大小写，如果要区分大小写则需要写入关键字 ``BINARY``，如 ``WHERE prod_name REGEXP BINARY 'JetPack .000'``

