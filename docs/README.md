# Vue2 ⚔ Vue3
为什么需要选择vue3？
### **Vue2**

从我的角度来看，最大的功臣莫过于这三个：
* `响应式数据。`
* `Options API`
* `SFC（单文件组件）`
  
在一个组件仅承担单一逻辑的时候，使用 Options API 来书写组件是很清晰的。

但是在我们实际的业务场景中，一个父组件总是要包含多个子组件，父组件需要给子组件传值、处理子组件事件、直接操作子组件以及处理各种各样的调接口的逻辑，这时候我们的父组件的逻辑就会变得复杂。

从代码维护者的角度出发，假设这个时候有 10 个函数方法，自然我们要把他们放入到methods中，而这个 10 个函数方法又分别操作 10 个数据，这个 10 个数据又分别需要进行Watch操作。

这时候，我们根据Vue2的Options API的写法，就写完了 10 个method、10 个data、10 个watch，我们就将本来 10 个的函数方法，分割在 30 个不同的地方。

这时候父组件的代码，对代码维护者来说是很不友好。


### **vue3**
* 更强的性能，更好的优化
* Composition API + setup
* 更好地支持 TypeScript


SFC 写法变化

![SFC 写法变化](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/19a5449cbc1b45f4b6bb8941fe940180~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp? "SFC 写法变化")

**响应式原理的不同**
1. **vue2.x** ：实现双向数据绑定原理，是通过es5的 Object.defineProperty，根据具体的key去读取和修改。其中的setter方法来实现数据劫持的，getter实现数据的修改。但是必须先知道想要拦截和修改的key是什么，所以vue2对于新增的属性无能为力，比如无法监听属性的添加和删除、数组索引和长度的变更，vue2的解决方法是使用Vue.set(object, propertyName, value) 等方法向嵌套对象添加响应式。
   
2. **Vue3.x** ：使用了ES2015的更快的原生proxy 替代 Object.defineProperty。Proxy可以理解成，在对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy可以直接监听对象而非属性，并返回一个新对象，具有更好的响应式支持。

## Vue2 options写法

```vue
<template>
  <div>
    <p>{{ person.name }}</p>
    <p>{{ car.name }}</p>
  </div>
</template>

<script>
export default {
  name: "Person",

  data() {
    return {
      person: {
        name: "w",
      },
      car: {
        name: "奥迪",
        price: "40w",
      }
    };
  },

  watch:{
      'person.name': (value) => {
          console.log(`名字被修改了, 修改为 ${value}`)
      },
  },

  methods: {
    changePersonName() {
      this.person.name = "wan666";
    },

    changeCarPrice() {
      this.car.price = "80w";
    }
  },
};
</script>
```

## Vue3 Composition API 重写

```vue
<template>
  <p>{{ person.name }}</p>
  <p>{{ car.name }}</p>
</template>

<script lang="ts" setup>
import { reactive, watch } from "vue";

// person的逻辑
const person = reactive<{ name: string; sex: string }>({
  name: "w",
});
watch(
  () => [person.name, person.sex],
  ([nameVal, sexVal]) => {
    console.log(`名字被修改了, 修改为 ${nameVal}`);
  }
);
function changePersonName() {
  person.name = "wan666";
}

// car的逻辑
const car = reactive<{ name: string; price: string }>({
  name: "奥迪",
  price: "40w",
});
function changeCarPrice() {
  car.price = "80w";
}
</script>
```

采用 Vue3 自定义 Hook 的方式，进一步拆分

```vue
<template>
  <p>{{ person.name }}</p>
  <p>{{ car.name }}</p>
  <p>{{ animal.name }}</p>
</template>

<script lang="ts" setup>
import { usePerson, useCar, useAnimal } from "./hooks";

const { person, changePersonName } = usePerson();

const { car } = useCar();
</script>
```

```javascript
// usePerson.ts
import { reactive, watch } from "vue";

export default function usePerson() {
  const person = reactive<{ name: string; sex: string }>({
    name: "w",
    sex: "male",
  });
  watch(
    () => [person.name, person.sex],
    ([nameVal, sexVal]) => {
      console.log(`名字被修改了, 修改为 ${nameVal}`);
      console.log(`名字被修改了, 修改为 ${sexVal}`);
    }
  );
  function changePersonName() {
    person.name = "wan666";
  }
  return {
    person,
    changePersonName,
  };
}
```

```javascript
// useCar.ts
import { reactive } from "vue";

export default function useCar() {
  const car = reactive<{ name: string; price: string }>({
    name: "奥迪",
    price: "40w",
  });
  function changeCarPrice() {
    car.price = "80w";
  }
  return {
    car,
    changeCarPrice,
  };
}
```
---
### 性能方面

Vue3 主要在这几个方面进行了提升

* 编译阶段。对 `diff` 算法优化、静态提升等等
* 响应式系统。`Proxy()`替代`Object.defineProperty()`监听对象。监听一个对象，不需要再深度遍历，`Proxy()`就可以劫持整个对象
* 体积包减少。`Compostion API `的写法，可以更好的进行 `tree shaking`，减少上下文没有引入的代码，减少打包后的文件体积
* 新增片段特性。Vue 文件的`<template>`标签内，不再需要强制声明一个的`<div>`标签，节省额外的节点开
---
**TreeShaking 简述：**

**[TreeShaking 术语解析](https://juejin.cn/post/7135217402983235592)**
 


