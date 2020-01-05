---
title: 基于Vue+Ueditor搭建富文本编辑器
date: 2019-11-30 14:32:43
tags:
- Ueditor
comments: true
categories: 
- Vue

---
##  一、下载Ueditor并将它放在Vue项目中的static目录当中
<!-- more -->

![avator](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/lg.png?token=AMCTPY3XNRSRPNHILMW4CJ254C3EG)

##  二、创建Editor编辑界面

#### 基于Vue组件式的写法

##### component组件代码

```
<template>

        <div ref="editorId" :style="{width: 100+'%',height:height+'px'}"></div>

</template>
<script>
    export default {
        name: "EditorView",
        data() {
            return {
                editor: null,
                id: 'editor' + new Date().getTime()
            }
        },
        props: {
            value: {
                type: String,
                default: null,
            },
            height: {
                type:Number,
                default:null,
            },
            config: {
                type: Object,
                default: ()=>{},
            }
        },
        watch: {
            'value': function (val) {
                this.editor.setContent(val)
            }
        },
        mounted() {
            this.$nextTick(function f1() {
                this.$refs.editorId.id = this.id //创建动态id,由于封装的是拖拉拽的富文本编辑器，
                所以会用到动态的id进行操作
                this.editor = UE.getEditor(this.id,this.config)
                // 保证 this.$el 已经插入文档
                this.editor.ready(function f2() {
                    this.editor.setContent(this.value || '')
                    this.editor.addListener("contentChange", function () {
                        const wordCount = this.editor.getContentLength(true)
                        const content = this.editor.getContent()
                        const plainTxt = this.editor.getPlainTxt()
                        const htmlCont = this.editor.getAllHtml()
                        // 编辑器内容有变动,通知父组件
                        this.$emit('input',content);
                    }.bind(this))
                    // editor初始化后操作
                    this.$emit('ready', this.editor)
                }.bind(this))
            })
        },
        methods:{
            /**
             * 保存
             */
            save () {
                const content = this.editor.getContent();
                this.$emit('input',content);
            }
        },

    }
</script>
```

##### 在父组件中进行调用

```
import EditorView from "你的component路径"
```

#### 使用代码

```
<template>
	<edior-view   v-model=""
                  :config="config"
                  :height="200">
    </edior-view>
<template>
<script>
    import EdiorView from "./EdiorView";
    export default {
        components:{
            EdiorView
        },
        name: "Ueditor",
        data() {
            return {
                zIndex:2000, //可以提供富文本编辑器的层级，可写可不写
                elementPathEnabled : false,//去除底部左下角的元素路径提示，可写可不写
                config:{ //编辑栏配置项
                    toolbars: [
                        [ 'source', //源代码
                            'undo', //撤销
                            'redo', //重做
                            'bold', //加粗
                            'indent', //首行缩进
                            'italic', //斜体
                            'underline', //下划线
                            'strikethrough', //删除线
                            'fontborder', //字符边框
                            'formatmatch', //格式刷
                            'horizontal', //分隔线
                            'removeformat', //清除格式
                            'inserttitle', //插入标题
                            'mergecells', //合并多个单元格
                            'cleardoc', //清空文档
                            'justifyleft', //居左对齐
                            'justifyright', //居右对齐
                            'justifycenter', //居中对齐
                            'justifyjustify', //两端对齐
                            'simpleupload', //单图上传
                            'insertimage', //多图上传
                            'fontfamily', //字体
                            'fontsize', //字号
                            'customstyle', //自定义标题
                            'paragraph', //段落格式
                            'emotion', //表情
                            'spechars', //特殊字符
                            'forecolor', //字体颜色
                            'directionalityltr', //从左向右输入
                            'directionalityrtl', //从右向左输入
                            'imagenone', //默认
                            'imageleft', //左浮动
                            'imageright', //右浮动
                            'imagecenter', //居中
                            'lineheight', //行间距
                            'edittip ', //编辑提示
                        ]
                    ]
                }
            }
        },
        watch: {

        },
        mounted() {

        },
        methods: {

        },
    }
</script>

<style scoped>

</style>
```

#### 这样就可以封装一个基于Vue+Ueditor的富文本编辑器

## 参考文献1： http://fex.baidu.com/ueditor/ 
## 参考文献2： 
## https://www.cnblogs.com/xiaoyaoxingchen/p/9082666.html 