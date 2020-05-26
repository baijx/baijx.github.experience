# Vue-给对象新增属性（使用Vue.$set()）

在开发过程中，我们时常会遇到这样一种情况：当vue的data里边声明或者已经赋值过的对象或者数组（数组里边的值是对象）时，向对象中添加新的属性，如果更新此属性的值，是不会更新视图的。

根据官方文档定义：如果在实例创建之后添加新的属性到实例上，它不会触发视图更新。

受现代 JavaScript 的限制 (以及废弃 Object.observe)，Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。

## 解决方案
官方定义：

Vue 不允许在已经创建的实例上动态添加新的根级响应式属性 (root-level reactive property)。然而它可以使用 Vue.set(object, key, value) 方法将响应属性添加到嵌套的对象上：

Vue.set(vm.obj, 'e', 0)
您还可以使用 vm.$set 实例方法，这也是全局 Vue.set 方法的别名：

this.$set(this.obj,'e',02)
