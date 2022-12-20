## 创建索引
``127.0.0.1:9200/shopping`` PUT 请求会创建一个 index
推荐使用 GET、PUT、HEAD、DELETE，不能使用 POST

---

## 获取索引
---


## 创建文档
PUT 说明具有幂等性，POST 不具备幂等性

---


## 查询文档
GET ``127.0.0.1:9200/shopping/_doc/1002``

---

## 映射关系创建
PUT ``127.0.0.1:9200/user/_mapping``
```json
{
    "properties": {
        "name": {
            "type": "text",
            "index": true
        },
        "sex": {
            "type": "keyword",
            "index": true
        },
        "tel": {
            "type": "keyword",
            "index": false
        }
    }
}
```

> 1. 注意这里的 ``type``，``text``支持分词，也就是模糊查询，但是 ``keyword`` 不支持
> 2. 当``index``为``false``时代表不被索引，不能通过这个查询