title: vue1和vue2使用上的区别

categories: js

---

### 前言
>由于刚好同时在使用vue1和vue2，所以总结下使用过程中有区别的地方。

### component载入触发的钩子
> vue1 载入component后在调用ready(){}，而vue2调用的是created(){},如下代码：
```js
// vue1
export default{
    ready(){
        console.log('work fine')//
    }
}

// vue2
export default{
    created(){
        console.log('work fine');
    }
}
```

### vue-router的区别

> 在vue1中，可以使用`this.$router.go('/home')`；但是在vue2使用上述代码并不会起作用，因为`this.$router.go()`这个方法调整为类似`window.history.go()`这个方法了，传入的数值，指向history对应的路径。如果在vue2需要跳转指定的地址，使用`this.$router.push('/home')`即可。

> 在vue1中。a便签添加`v-link="/home"`用来跳转页面，但是在vue2则是使用`<router-link to="/home"></router-link>`来跳转

### vuex的区别

> 首先state用法都是一样的

> 在vuex1中，触发`Mutations`使用的是dispatch,所以的action是挂载component上直接使用this调用对方的方法即可。

> 在vuex2中，触发`Mutations`使用的是vuex新增的commit。然后如果要调用action的话，是需要调用$store上的dispatch来实现的。具体代码如下(不论vuex1还是xuex2我都是喜欢基于调用类型，把state,mutations,还有actions写在同一个文件里，便于管理)：
```js
// vuex1 和vuex2相同的代码
const state = {
    isLogin:false
};

const mutations = {
    LOGIN_SUCCESS(state){
        state.isLogin = true;
    }
}

// 在vuex1 这样使用
const actions = {
    userLogin({dispatch}){
        dispatch('LOGIN_SUCCESS');
    }
}

// 我是把所有的action都挂载了vue的$actions下面，这样方便所有的组件调用，如下代码,actions为所有action的对象集合，使用webpack可以轻松把不同文件里的action聚合在一起，方便使用
Vue.$actions = Vue.prototype.$actions = actions;

// 然后在组件里这样引用组件
import Vue from 'vue';
export default{
    vuex:{...Vue.$action},
    ready(){
        this.userLogin();
    }
}


// 在vuex2中这么处理
const actions = {
    userLogin({commit}){
        commit('LOGIN_SUCCESS')
    }
};

// 然后在组件中这么调用即可
export default{
    created(){
        this.$store.dispatch('userLogin');
    }
}
```

### 未完待续
> 其他的异同等发现了来完善把，整体来说。vue2升级后，大部分api还是正常使用的。比起angular1 升级到angular2的几乎所有api重写。