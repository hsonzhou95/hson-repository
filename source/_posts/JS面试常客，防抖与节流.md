---
title: JS面试常客，防抖与节流
date: 2020-01-07 09:17:11
tags:
- 防抖
- 节流
comments: true
type: "JavaScript"
categories:
- JavaScript
---
## JS面试常客，防抖与节流
#### Js中防抖与节流的应用场景，以及在Vue中如何实现防抖与节流
<!-- more -->
### 为什么会用到防抖与节流

> 在项目中常常会使用到提交按钮和搜索框的功能，但在使用过程中，如果一直点击提交按钮或者搜索输入的内容进行发起请求，那么这样做会大大的影响性能的问题，为考虑到性能方面的优化问题，所以就会用到防抖与节流。

### 概念

#### 防抖函数（debounce）：

>  在滚动事件中需要做个复杂计算或者实现一个按钮的防二次点击操作 。

#### 节流函数（throttle）：

> 规定在某一个单位内，只触发一次函数，如果这个单位时间内触发多次函数，只有一次生效；典型的按理就是鼠标不断点击触发，规定在n秒内多次点击只有一次生效。

**注意：在setTimeout中的this会失效，在setTimeout当中this的指向是window，解决办法是在setTimeout函数外面定义一个that=this就可以了**

#### 在Vue中的实际代码：

##### 1、输入框，输入最后一个字2.5秒后执行（防抖：debounce）

```vue
<template>
    <el-input v-modle="msg" @keyup="debounce"></el-input>
</template>

<script>

    let timer;//注意不要将thimer定义在debounce内部，将他放在全局
    export default {
        name: "deoucn",
        data(){
            return{
                msg:''
            }
        },
        methods:{
            debounce(){
                let that = this;
                if(timer){
                    clearTimeout(timer)
                }
                timer = setTimeout(function(){
                    console.log('输入内容');
                },2500)
            }
        }
    }
</script>

<style scoped>

</style>

```

##### 2、在2.5秒内点击多次，只有一次生效（节流，throttle）

```vue
<template>
    <el-button @click="throlle">保存</el-button>
</template>

<script>

    let lastTime;//注意不要将thimer定义在debounce内部，将他放在全局
    export default {
        name: "deoucn",
        data(){

        },
        methods:{
            throlle(){
                let that = this;
                let time = +new Date();
                if (lastTime && lastTime - time < 2500){
                    clearTimeout(lastTime)
                }

                lastTime = setTimeout(function(){
                    console.log('使劲点');
                    lastTime = +new Date();
                },100)
            }
        }
    }
</script>

<style scoped>

</style>

```

> 好了，到这里一个简单的防抖函数和节流函数就完成了，是不是看上去超级简单！！！


## 参考文献： https://www.cnblogs.com/yalong/p/10556665.html 
#### 声明：文章著作权归作者所有，如有侵权，请联系删除。

