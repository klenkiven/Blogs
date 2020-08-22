# 01-vue实例

## 创建vue实例

一个 Vue 应用由一个通过 `new Vue` 创建的**根 Vue 实例**，以及可选的嵌套的、可复用的**组件树**组成。

```
根实例
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>Vue first demo</title>
</head>

<body>
    <div id="app">
        {{ message }}
    </div>
</body>

<script src="./lib/vue.js"></script>
<script>
    //从这里开始创建一个新的vue实例
    var app = new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue!'
        }
    })
</script>

</html>
```

+ Vue对象接受**选项对象**（data, methods, computed等等）
+ 所有的**Vue组件**都是Vue实例

## 数据与方法

Vue 实例被创建时，它将 `data` 对象中的所有的 **property** 加入到 Vue 的**响应式系统**中。当这些 property 的值**发生改变**时，视图将会产生“响应”，即当这些数据改变时，视图的相应位置也会随之改变。

```js
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})
```

> 按照我的理解是，data被vue作为接受的选项对象以后，data里面的 property 会作为vue的 "属性"。譬如，`vm.a = 2` 时，`data.a` 也会变成2。

只有当实例被创建时就已经存在于 `data` 中的 property 才是**响应式**的。

> 比如，后知后觉，在对象vm里面添加一个新的 property `vm.b = ‘hi’` 那么这样对b的改动不是响应式的。因此，你在晚些需要一个 property 的时候，你需要放到 data 里面去，并且 “随便” 赋值。

***！***使用 `Object.freeze()`，这会**阻止修改**现有的 property，也意味着响应系统无法再*追踪*变化。

```js
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
```

> 例如上面的例子，作为vue接受的选项对象，obj里面的 property 是无法更改的。

Vue 实例还暴露了一些有用的**实例 property 与方法**。它们都有前缀 `$`。

> 例如， `vm.$data` ,  `vm.$el` ,  `vm.$watch`
>
> 其中`$watch` 是一个实例方法，其他两个是实例对象。

## 实例生命周期钩子

每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，这给了用户在不同阶段添加自己的代码的机会。

> 注意！！
>
> 不要在选项 property 或回调上使用[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，比如 `created: () => console.log(this.a)` 或 `vm.$watch('a', newValue => this.myMethod())`。因为箭头函数并没有 `this`，`this` 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 `Uncaught TypeError: Cannot read property of undefined` 或 `Uncaught TypeError: this.myMethod is not a function` 之类的错误。

## 生命周期演示

![Vue 实例生命周期](https://cn.vuejs.org/images/lifecycle.png)