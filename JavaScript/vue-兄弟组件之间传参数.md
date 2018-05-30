####简单的参数
简单的参数，在同级组件之间的传递，可以通过路由的query和param来传递。
####稍微复杂一点的参数
用eventBus（vue2.0以上）来传递参数 

具体用法参见[vue中eventbus被多次触发](https://www.jianshu.com/p/fde85549e3b0)这篇文章，博主写得很清楚。在实践中值得注意的是，两个组件切换时，组件的生命周期的改变，在A组件切换到B组件的时候，生命周期函数是这样展示的：

    B组件-========beforeCreate
	B组件-========created"
	B组件-========beforeMount
	A组件-========beforeDestroy
	A组件-========destroyed
	B组件-========mounted
	...

即，切换组件的时候，切换到的组件在挂载完成之前执行的是上一个组件的beforeDestroy 和destroy钩子，所以，我们可以在此页面的created钩子里绑定bus.$on监听事件，然后再在beforeDestroy或者，destroy里面提交事件。    
值得注意的是，eventBus的绑定用了之后，需要销毁，不然会引发多次触发，用Bus.$off注销事件。保证调用一次。
#### 大型项目的复杂传值
用vuex管理。	