## ref
### 见解
* 定义`基本数据类型`响应式变量

### 示例
* 创建一个`基本数据类型`响应式变量
```javascript
import { ref } from 'vue'
const count = ref(0)
```
* `ref` 对象是可更改的，也就是说你可以为 `.value` 赋予新的值
```javascript
const count = ref(0)
console.log(count.value) // 0
count.value++
console.log(count.value) // 1
```

### 语法糖
**相对于普通的 `JavaScript`变量，我们不得不用相对繁琐的`.value`来获取 `ref` 的值。这是一个受限于`JavaScript`语言限制的缺点。然而，通过编译时转换，我们可以让编译器帮我们省去使用`.value`的麻烦。**
```javascript
let count = $ref(0)
function increment() {
  // 无需 .value
  count++
}
```
---

## computed
### 见解 
* 接受一个 getter 函数，返回一个只读的响应式 ref 对象。该 ref 通过 .value 暴露 getter 函数的返回值。它也可以接受一个带有 get 和 set 函数的对象来创建一个可写的 ref 对象。

### 示例
* 创建一个只读的计算属性 `ref`
```javascript
import { ref } from 'vue'
const count = ref(0)
```

* 使用`computed`创建一个只读的计算属性 `ref`
```javascript
const count = ref(1)
const plusOne = computed(() => count.value + 1)
console.log(plusOne.value) // 2
plusOne.value++ // 错误
```

* 使用`computed`创建一个可写的计算属性 `ref`
```javascript
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => {
    count.value = val - 1
  }
})
plusOne.value = 1
console.log(count.value) // 0
```
---

## reactive
### 见解 
* 创建一个`引用数据类型`的响应式对象。

### 示例
* 创建一个响应式对象
```javascript
const obj = reactive({ count: 0 })
obj.count++
```

* 对 `ref` 的解包
```javascript
const count = ref(1)
const obj = reactive({ count })
// ref 会被解包
console.log(obj.count === count.value) // true
// 会更新 `obj.count`
count.value++
console.log(count.value) // 2
console.log(obj.count) // 2
// 也会更新 `count` ref
obj.count++
console.log(obj.count) // 3
console.log(count.value) // 3
```

* 当访问到某个响应式数组或` Map `这样的原生集合类型中的` ref `元素时，不会执行` ref `的解包
```javascript
const books = reactive([ref('Vue 3 Guide')])
// 这里需要 .value
console.log(books[0].value)
const map = reactive(new Map([['count', ref(0)]]))
// 这里需要 .value
console.log(map.get('count').value)
```

* 将一个` ref `赋值给为一个` reactive `属性时，该` ref `会被自动解包
```javascript
const count = ref(1)
const obj = reactive({})
obj.count = count
console.log(obj.count) // 1
console.log(obj.count === count.value) // true
```

---
## readonly
### 见解 
* 被readonly包裹的数据只能够读取，是一个深度只读，不能够修改。
* readonly深度只读的应用场景：
  * 比如我们登录后用户信息不会发生改变了。
```javascript
import {  reactive,shallowReadonly} from 'vue'
export default {
  name: 'App',
  setup(){
      let state=reactive({
          name:"张三",
          age:20,
          info:{
            h:1.90,
            w:'64kg'
          }
      })
      // shallowReadonly 浅只读
      // shallowReadonly中放了一个对象，对象中的直接属性是不可以修改的；
      // 如果对象下的属性下还有对象。那么这个对象下的属性就可以修改的
      let viewSate=shallowReadonly(state)
      function func1(){
        // 修改失败
        // viewSate.name="李四"

        // 修改成功
        viewSate.info.w="128斤"
      }
      return {viewSate,func1}
  },
}
```
### 示例
* 创建一个响应式对象
```javascript
const original = reactive({ count: 0 })
const copy = readonly(original)
watchEffect(() => {
  // 用来做响应性追踪
  console.log(copy.count)
})
// 更改源属性会触发其依赖的侦听器
original.count++
// 更改该只读副本将会失败，并会得到一个警告
copy.count++ // warning!
```
---

## watchEffect
### 见解 
* 立即运行一个函数，同时响应式地追踪其依赖，并在依赖更改时重新执行
### 示例
```javascript
const count = ref(0)
watchEffect(() => console.log(count.value))
// -> 输出 0
count.value++
// -> 输出 1
```
---

## watchPostEffect
### 见解 
* 立即运行一个函数，同时响应式地追踪其依赖，并在依赖更改时重新执行
---

## watchSyncEffect
### 见解 
* 立即运行一个函数，同时响应式地追踪其依赖，并在依赖更改时重新执行
### 示例
```javascript
const count = ref(0)
watchEffect(() => console.log(count.value))
// -> 输出 0
count.value++
// -> 输出 1
```
---

## watch
### 见解 
* 侦听一个或多个响应式数据源，并在数据源变化时调用所给的回调函数。
### 示例
* 侦听一个` getter `函数
```javascript
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)
```
* 侦听一个 `ref`
```javascript
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})
```
* 当侦听多个来源时，回调函数接受两个数组，分别对应来源数组中的新值和旧值
```javascript
watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
  /* ... */
})
```
---
