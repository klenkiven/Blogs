# 02-vue模板语法

>  **内容预告：**
>
>  + “Mustache”语法：
>
>  + v-once 指令：数据改变一次
>  + v-html指令：原生html
>  + v-bind指令：绑定各种Attribute

## 插值

### 文本插值

数据绑定最简单的一个例子，使用**“Mustache”语法** (双大括号) 的文本插值：

```html
<span>Message: {{ msg }}</span>
<span v-once>这个将不会改变: {{ msg }}</span>
```

+ 使用 **[v-once 指令](https://cn.vuejs.org/v2/api/#v-once)**，你也能执行**一次性地插值**，当数据改变时，插值处的内容不会更新。

  **注意：**会影响到该节点上的其它数据绑定

### 原始 HTML

双大括号会将数据解释为普通文本，而非 HTML 代码。为了输出真正的 HTML，你需要使用 **[`v-html` 指令](https://cn.vuejs.org/v2/api/#v-html)**：

```html
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

+ `v-html` 指令：里面的 `rawHtml` 是数据绑定的
+ 不管是大括号还是 `v-html` 指令，里面的rawHtml的 Mustache 语法是不能被解析的

### Attribute

Mustache 语法不能作用在 HTML attribute 上，遇到这种情况应该使用 **[`v-bind` 指令](https://cn.vuejs.org/v2/api/#v-bind)：**

```html
<div v-bind:id="dynamicId"></div>
```

> 在html的属性部分想要使用数据绑定的时候，就需要使用 `v-bind` 指令绑定。

布尔Attribute：对于布尔 attribute (它们只要存在就意味着值为 `true`)，`v-bind` 工作起来略有不同，在这个例子中：

```html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

如果 `isButtonDisabled` 的值是 **`null`、`undefined` 或 `false`**，则 `disabled` attribute 甚至不会被包含在渲染出来的 `<button>` 元素中。

### javascript表达式

对于所有的数据绑定，Vue.js 都提供了完全的 JavaScript 表达式支持。

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

## 指令

> 指令 (Directives) 是带有 `v-` 前缀的特殊 attribute。

指令 attribute 的值预期是**单个 JavaScript 表达式** (`v-for` 是例外情况，稍后我们再讨论)。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

`v-if` 指令将根据表达式 `seen` 的值的真假来插入/移除 `<p>` 元素。

```html
<p v-if="seen">现在你看到我了</p>
```

### 参数

>  一些指令能够接收一个“参数”，在指令名称之后以冒号表示。

+ `v-bind` 指令可以用于响应式地更新 HTML attribute
+ `v-on` 指令，它用于监听 DOM 事件：