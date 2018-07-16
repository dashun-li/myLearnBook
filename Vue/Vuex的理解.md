# Vuex的理解
Vuex是为vue.js设计的状态管理架构，用于统一管理和维护vue组件的可变化的一些状态。（可以理解为很多的组件可以共用数据）
## vuex五个核心概念
### 1.state
由于vuex是单一状态树，即用一个对象包含了定义的状态。state作为构造器选项，定义了所有我们需要的基本状态参数。（相当于是在state对象里面定义了状态变量，保存了所有的组件的公共数据。）
	
	//将多个组件的公共data抽取出来  
	vuex中的写法
	const store = new Vuex.Store({
	  state:{ 
	    a:'11111'
	  }
	})
	//a页面
	export default {
	    data () {
	        return {
	            a : this.$store.state.a //获取store中state的数据
	        }
	    }
	}
	//b页面
	export default {
	    data () {
	        return {
	            a : this.$store.state.a //获取store中state的数据
	        }
	    }
	}
### 2.getters
getters属性可以理解为store的的computed属性,getters的返回值会根据它的依赖被缓存起来，且只有当它的依赖值(state.X)发生了改变才会被重新计算。

    const store = new Vuex.Store({
	      state:{
		    b:2
	      },
	      getters:{ //添加getters
		    reduceB: (state) => {
		     let saleProducts = state.b => {
			    	b/2
			      }
			      return reduceB;
			    }
			}
	    })
	//c页面
	export default {
	    data () {
	        return {
	            c : this.$store.getters.reduceB 
	        }
	    }
	}
### 3.mutations
mutations对象中保存着更改数据的回调函数(相当于是包含很多的改变数据的方法),该函数名官方规定叫type（相当于方法名）, 第一个参数是state, 第二参数是payload(自定义的参数).
##### 注意：mutations用于同步的改变状态；mutations改变状态，是通过commit提交(this.$store.commit(state,payload))

 	const store = new Vuex.Store({
	      state:{
		    b:2
	      },
	      getters:{ //添加getters
		    reduceB: (state) => {
		     let saleProducts = state.b => {
			    	b/2
			      }
			      return reduceB;
			    }
			}，
		 mutations:{
			changeB(state,payLoad){
				 state.b += payload
				}
			}
	    })
	//页面
	<template>
		<div>
			<span>{{b}}</span>
			<button @click="add">点我</button> //添加按钮
		</div>
	</template>
	//页面中js代码
	export default {
		    data () {
		        return {
		            b: this.$store.state.b
		        }
		    },
		    methods: {
		        add() {
		            this.$store.commit('changeB', 2); //利用mutations里的回调函数改变state里面的数据b的值
		        }
		    }
		}

### 4.actions
actions 相当于是可异步的 mutations，它的特点是：   
1.actions提交的是mutations而不是直接变更状态   
2.actions中可以包含异步操作, mutations中绝对不允许出现异步   
3.actions中的回调函数的第一个参数是context, 是一个与store实例具有相同属性和方法的对象
 	
	const store = new Vuex.Store({
	      state:{
		    b:2
	      },
	      getters:{ //添加getters
		    reduceB: (state) => {
		     let saleProducts = state.b => {
			    	b/2
			      }
			      return reduceB;
			    }
			}，
		 mutations:{
			changeB(state,payLoad){
				 state.b += payload
				}
			},
		//actions
		actions:{
			changeBAsync(context,payload){//这里的context就相当于是store
				setTimeOut(()=>{
					context.commit('changeB',payload)
				},2000)
			}
		}
	    })
	//页面
	<template>
		<div>
			<span>{{b}}</span>
			<button @click="add">点我</button> //添加按钮
			<button @click="addAsync">点我（2秒后有奇迹）</button>//延迟2秒增加
		</div>
	</template>
	//页面中js代码
	export default {
		    data () {
		        return {
		            b: this.$store.state.b
		        }
		    },
		    methods: {
		        add() {
		            this.$store.commit('changeB', 2); //利用mutations里的回调函数改变state里面的数据b的值
		        },
				addAsync(){
					this.$store.dispatch("changeBAsync",2)//用dispatch改变变量的状态。原理上还是在内部改变mutations
				}
		    }
		}

### 5.modules
由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。分成单独的模块之后，每一个模块就可以单独使用自己的state，还可以通过自己模块里的mutations/actions改变state里面的状态

    const moduleA = {
      state: { ... },
      mutations: { ... },
      actions: { ... },
      getters: { ... }
    }
    
    const moduleB = {
      state: { ... },
      mutations: { ... },
      actions: { ... },
	  getters: { ... }
    } 

    const store = new Vuex.Store({
      modules: {
	    a: moduleA,
	    b: moduleB
      }
    })
    
    store.state.a // -> moduleA 的状态
    store.state.b // -> moduleB 的状态
