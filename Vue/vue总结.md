## 深入理解vue
#### computed
两者不同的是计算属性是基于它的依赖进行缓存的。计算属性只有在它的相关依赖发生改变时才会重新求值,也就是如下：
   
    var vm = new Vue({
      el: '#example',
      data: {
        message: 'Hello'
      },
      computed: {
        // 计算属性的 getter 
        reversedMessage: function () {//当message的值改变的时候，reversedMessage才会重新求值
          // `this` 指向 vm 实例
          return this.message.split('').reverse().join('')
        }
      }
    })

；若是不改变message的值，计算属性中，直接获取的就是之前的值。这个就是说的computed缓存
    
    computed: {
      now: function () {
        return Date.now()
      }
    }//这个计算属性并没有依赖data里面的值，所以 ，不管Date.now()是否有变化，now不会实时更新。
    
设置缓存的原因：假设我们有一个性能开销比较大的计算属性 A，它需要遍历一个巨大的数组并做大量的计算。然后我们可能有其他的计算属性依赖于 A 。如果没有缓存，我们将不可避免的多次执行 A 的 getter！

#### watch 
计算属性在大多数情况下比watch更合适，但有时也需要一个`自定义`的侦听器。这就是为什么 Vue 通过 watch 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行`异步`或`开销较大`的操作时，这个方式是最有用的
#### v-for与v-if 
在相同节点上，v-for 的权重比v-if要高，可以在遍历数据，部分不满足条件的数据不显示的场景使用。
#### 父组件引用子组件的值
在父组件中，因为子组件已经被exports出来，可以直接在父组件中通过ref获取到子组件，从而调用它的方法