Vue - MVVM 的实现者



### MVVM

* 低耦合 -  视图（View）可以独立于 Model 变化和修改，一个 ViewModel 可以绑定到不同的 View 上，当 View 变化的时候 Model 可以不变，当 Model 变化的时候 View 也可以不变。

* 可复用 - 你可以把一些视图逻辑放在一个 ViewModel 里面，让很多 View 重用这段视图逻辑。
* 独立开发 - 开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计。
* 可测试 - 
* 界面素来是比较难于测试的，而现在测试可以针对 ViewModel 来写。



```html
<div id="app">
    {{message}}
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>

<script type="text/javascript">
    var vm = new Vue({
        el: "#app",
        data: {
            message: "Hello Vue"
        }
    })
</script>
```



这其实就是一个简单的 MVVM

``<div id="app">`` 就是 view, var vm = new Vue({}) 就是 ViewModel，data 就是 Model



```javascript
this.$emit("remove", index);	// 子类组件调用父类组件事件 remove 方法，index 为参数
```

```javascript
this.items.splice(index, 1);  // 数组从 index 开始减少 1 个
```