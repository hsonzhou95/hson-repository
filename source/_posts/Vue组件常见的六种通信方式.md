---
title: Vue组件常见的六种通信方式
date: 2019-12-14 13:54:23
tags:
- 兄弟通信
- 父子通信
- 孙子通信
comments: true
categories: 
- Vue
---
## Vue组件常见的六种通信方式

> Vue.js 组件实例的作用域是相互独立的，不同组件之间的数据不能互相访问，组件有父级组件、子级组件、兄弟组件，如何选择组件之间的通信方式？针对常用的 props、$emit/$on、vuex、$parent/$children、$attrs/$listeners、provide/inject 进行讲解，对比各自的区别以及使用场景。 
<!-- more -->
### 1.props

> 子组件使用props 接收父组件传递的值，子组件通过 $emit ，让父组件接收事件，改变父组件的data里面的值； 

代码示例：

```vue
<!--父组件-->
<template>
    <title :title='title' @change='changeHandle'></title>
</template>
<script>
	export default
{
    data(){
        return{
            title:'Hello'
        };
    },
    components:{
        title
    },
    methods:{
        changeHandle(val){
            this.title = val;
        }
    }
}
</script>
<!--子组件-->
<template>
    <div @click='changeHandle'>{{title}}</div>
</template>
<script>
export default
 {
    props: {        
    title:String   
},
    methods:{
        changeHandle(){
            this.$emit('change', 'Hello Vue');        
            } 
	}
}
</script>
```

-  子组件通过事件向父组件传递值，父组件更改自己的数据，子组件接收更改后的值； 

### 2. $emit/$on

> 通过Vue 的实例触发事件和监听事件，实现了跨级组件的通信； 

代码示例：

```vue
<!--在Vue的原型链上添加$bus 属性，赋值为Vue实例-->
const EventBus = new Vue();
Object.defineProperties(Vue.prototype, {
    $bus: {
        get() {
            return EventBus;
        }
    }
});
window.globalVue = EventBus;

<!--使用-->
// 组件A
<template>
    <div @click='changeHandle'>触发事件</div>
</template>
<script>
export default 
{
    methods: 
	{
        changeHandle(){
            window.globalVue.$emit('change', '456');
        }
    
	}
}
</script>

// 组件 B
<template>    
	<div>监听事件</div>
</template>
<script>
export default 
{
    mounted(){
        window.globalVue.$on('change', str => { console.log(str);
        });
    }
}
</script>
```

-  在组件B中监听固定的事件名，当A组件触发change事件后，监听到事件触发，触发事件时机不确定，一般在created 或 mounted 中监听事件； 

### 3.Vuex

> Vuex 是一个单向的数据流的状态管理模式，state 存放数据，当需要改变state 的数据时，只能通过 mutation 更改，如果有异步操作，可以使用 action，最终action 还是通过mutation 更改state 的数据 

- Vue Components：Vue 组件，可以执行dispatch 方法触发action；
- dispatch：组件触发action 的方法；
- actions：负责接收组件触发的行为，可以在这里面调用commit()方法更改state中的数据；
- commit：执行mutation的方法，commit('mutation 名称')；
- mutations：更改 state 数据的方法，只能进行同步操作；
- state：存储数据的容器对象,即初始状态树；

代码演示：

```vue
<template>
    <div>{{title}}</div>
</template>
<script>
import {mapState} from 'vuex';
export default 
{
    computed: {        
    ...mapState({
    	title: state => state.title
    	})
    }
};
</script>
```

### 4. $parent/$children

> $parent/$children 可以访问到父组件或子组件的实例，通过 $ref 也可以获取到子组件实例，可以访问子组件属性和方法； 

代码示例：

```vue
<!--父组件A-->
<template>  
    <h1>父组件</h1>    
    <child ref='child'></child>
</template>

<script>
export default 
{
    components: {
        child    
        },
    mounted(){
        console.log(this.$children);
 // 子组件实例数组
        console.log(this.$refs.child);
 // 子组件child实例
    }
}
</script>


<!--子组件child-->
<template>
    <h1>子组件</h1>
</template>
<script>
export default
 {
    mounted(){
        console.log(this.$parent); 
	// 父组件实例
    }
}
</script>
```

### 5. $attrs/$listeners

> this.$attrs 包含了父作用域中不作为prop被识别的特性绑定(class和style除外)，解释就是，父组件传递了props ，但是子组件没有全部使用props 接收，没被子组件接收的那些props，在$attrs中就可以获取到；$listeners 即是包含了父作用域中的(不含.native修饰器的)v-on 事件监听器，可以使用 v-on='$listeners'传入到内部组件；在跨级组件中传递属性和事件非常有用 

代码示例：
```
<!--顶级组件A-->
<template>
    <h1>顶级组件A</h1>
    <!--子级组件B-->
    <componentb data1='data1'
                data2='data2' 
                @event1='handleEvent1'
                @event2='handleEvent2'>
    </componentb>
</template>
<script>
export default{
    data(){
    return{
            data1:'data1',
            data2:'data2,
    };
},
    components:{
        componentb
        
    },
    mounted() {
        handleEvent1(){
        console.log('子级组件触发的事件');
        },
        handleEvent2(){
           console.log('孙子级组件触发的事件');
            
        }
    }
}
</script>
<!--子组件B-->
<template>
    <h1>子组件B</h1>
    data1的值：{{data1}}
    <componentc v-bind='$attrs'
                b-on='$listeners'>
    </componentc>
</template>
<script>
export default{
    props:{
        data1 
// 这里只获取了父级传递的props 的data1属性
},
    components:{
        componentc
        
    },
    mounted(){
        console.log(this.$attrs);
 // 打印 'data2'}

}
</script>

<!--孙子组件c-->
<template>
    <h1>孙子组件c</h1>
</template>
<script>
export default{
    props:{
    'data2'
    },
    mounted(){
    this.$emit('event2');
    }
}
</script>
```
### 6. provide/inject
>祖先组件通过 provide 向子孙级组件提供一个对象或返回一个对象的函数，该对象包含可以注入子孙组件的属性。

### Vue 不建议直接应用于程序代码中，主要是为高阶插件/组件库提供使用
代码示例：
```
<!--祖先组件A-->
<template>
    <h1>祖先组件A</h1>
    <componenta></componenta>
</template>
<script>
export default{
    data(){
    return{
            name:'祖先组件A'
        
    };
    
    },
    components:{
        componenta
    },
    provide(){
        return{
            'father':this
            
        };
    },
}
</script>

<!--子级组件A-->
<template>
    <h1>子级组件A</h1>
    <componentb></componentb>
</template>
<script>
export default{
    data(){
        return{           
            name:'子级组件A'
        };
        
    },
    components:{
        componentb
    },
    inject:['father'],
    mounted(){
        console.log(this.father.name);
 // '祖先组件A'
    }
}
</script>

<!--子孙级组件B-->
<template>
    <h1>子孙级组件B</h1>
</template>
<script>
export default{
    data(){
        return{
            name:'子孙级组件B'
        };
    },
    inject:['father'],
    mounted(){
        console.log(this.father.name);
 // '祖先组件A'
    }
}
</script>
```
- 需要注意的是 provide 和 inject 绑定并不是可响应的;这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

比如下面这个例子：
```
<!--祖先组件A-->
<template>
    <h1 @clidk='changeName'>祖先组件A</h1>
    <componenta></componenta>
</template>
<script>
export default{
    data(){
        return{
            name:'祖先组件A'
        };
},
    components:{
        componenta
    },
    provide(){
        return{
            father:{
                name:this.name
                
            }
        };
    },
    method:{
        changeName(){
            this.name ='祖先组件A改变了值了';
        }
    }
}
</script>


<!--子级组件A-->
<template>
    <h1>子级组件A</h1>
    父级组件name值：{{father.name}}
</template>
<script>
export default{
    data(){
        return{
            name:'子级组件A'
        };
  },
    components:
 {
        componentb
    },
    inject:['father'],
    mounted(){
        console.log(this.father.name);
 // '祖先组件A'
    }
}
</script>
```
### 当父级组件触发了点击事件，将 name 值改变后，子级组件渲染的值并不会变化；
解决办法：

- 祖先级组件提供子级的实例，在子孙级组件注入依赖，这样子孙级组件就可以访问到祖先级组件的属性，缺点是这个提供的实例挂了很多无用的属性和方法；
- 使用 Vue2.6 的Vue.observable 方法；
 
代码示例：
```
<!--祖先组件A-->
<template>
    <h1 @clidk='changeName'>祖先组件A</h1>
    <componenta></componenta>
</template>
<script>
export default{
    data(){
        return{
            name:''
        };
    },
    components:{
        componenta
    },
    provide(){
        return{
            father:{
                name:Vue.observable({
                    name:'祖先组件A'
                })
                
            }
        };
    },
    method:{
        changeName(){
            this.name ='祖先组件A改变了值了';
        }
    }
}
</script>


<!--子级组件A-->
<template>
    <h1>子级组件A</h1>
    父级组件name值：{{father.name}}
</template>
<script>
export default{
    data(){
        return{
            name:'子级组件A'
        };
    },
    components:{
        componentb
    },
    inject:['father'],
    mounted(){
        console.log(this.father.name);
 // '祖先组件A'
    }
}
</script>
```
- 当祖先级组件改变data值，子孙级组件就能获取数据更新值，重新渲染新值；
 
### 总结
- 父子级通信推荐使用：props/$emit、$parent/$children/$refs、$attrs/$listeners
- 兄弟组件通信推荐使用：$emit/$on、Vuex
- 跨级组件通信推荐使用：provide/inject、$emit/$on、Vuex、$attrs/$listeners

#### 声明：文章著作权归作者所有，如有侵权，请联系删除。