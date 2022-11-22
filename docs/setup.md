# `setup()` 和 `<script setup>`

## setup 组合式 API

`setup()` 钩子是在组件中使用组合式 API 的入口，通常只在以下情况下使用
* 需要在非单文件组件中使用组合式 API 时。
* 需要在基于选项式 API 的组件中集成基于组合式 API 的代码时。

其他情况下，都应优先使用`<script setup>`语法。

**注意：** 在模板中访问从 `setup` 返回的 `ref` 时，它会自动浅层解包，因此你无须再在模板中为它写 `.value`。当通过`this`访问时也会同样如此解包。

**TIP：** `setup`自身并不含对组件实例的访问权，即在`setup`中访问`this`会是`undefined`。你可以在`选项式API`中访问`组合式API`暴露的值，但反过来则不行。

### 访问 Props
 `setup` 函数的第一个参数是组件的 `props`。和标准的组件一致，一个 `setup` 函数的 `props` 是响应式的，并且会在传入新的 `props` 时同步更新
```javascript
export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```
解构了 `props` 对象，解构出的变量将会丢失响应性。因此我们推荐通过 `props.xxx` 的形式来使用其中的 `props`。


---
## script setup

`<script setup>` 是在单文件组件 (SFC) 中使用组合式 API 的编译时语法糖。当同时使用 SFC 与组合式 API 时该语法是默认推荐。相比于普通的`<script>` 语法，它具有更多优势：

* 更少的样板内容，更简洁的代码。
* 能够使用纯 TypeScript 声明 props 和自定义事件。
* 更好的运行时性能 (其模板会被编译成同一作用域内的渲染函数，避免了渲染上下文代理对象)。
* 更好的 IDE 类型推导性能 (减少了语言服务器从代码中抽取类型的工作)。
