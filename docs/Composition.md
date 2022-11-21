# API 风格

**Vue 的组件可以按两种不同的风格书写：Options API 和  Composition API。**

**什么是 Composition API ？**

* 我们要先了解一下vue2 中是如何组织代码的。当我们需要某个功能时，需要把功能中的代码差分为不同的部分放在对应的位置上。
当我们代码很少时，使用option API 结构会很清晰，可是在大型组件中功能越来越多，代码增加脚本越来越臃肿，并且同一个功能的代码会拆分为不同的部分散落在脚本不同位置会使代码难以阅读，难以维护。所以Composition API就时来解决这个问题。
---
**想象一下如果我们编写一个组件包含🔍搜索和排序另两个功能**

在传统的OptionsAPI中我们需要将逻辑分散到以下六个部分
1. components
2. props
3. data
4. computed
5. methods
6. lifecycle methods

这样会是我们编辑一个逻辑不得不在代码中反复横跳
