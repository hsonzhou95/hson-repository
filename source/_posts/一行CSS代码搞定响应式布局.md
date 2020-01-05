---
title: 一行CSS代码搞定响应式布局
date: 2019-12-24 13:42:56
tags:
- 响应式
- 样式
comments: true
type: "HTML5、CSS3"
categories:
- HTML5、CSS3
---
## 一行CSS代码搞定响应式布局

#### 在这篇文章中，我将教你如何使用 CSS Grid 来创建一个超酷的图像网格图，它将根据屏幕的宽度来改变列的数量。最精彩的地方在于：所有的响应特性被添加到了一行 css 代码中。这意味着我们不必将 HTML 与丑陋的类名(如col-sm-4, col-md-8)混杂在一起，也不必为每个屏幕创建媒体查询。
<!-- more -->
### 下面是我们初始化网格的外观：

![avator](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/responseType/initCss1.png?token=AMCTPY2FVPFKQUKNA4QKILS57HAYS)

### HTML代码：

```html
 <div class="container">
     <div class="item1">1</div>
     <div class="item2">2</div>
     <div class="item3">3</div>
     <div class="item4">4</div>
     <div class="item5">5</div>
     <div class="item6">6</div>
 </div>
```

### CSS代码

```css
.container {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 50px 50px;
}
.item1 {
    background: darkblue;
}

.item2 {
    background: darkgoldenrod;
}

.item3 {
    background: darkmagenta;
}

.item4 {
    background: darkorange;
}

.item5 {
    background: deeppink;
}

.item6 {
    background: forestgreen;
}
```

##### 如果这段代码让你感到困惑，我建议你去好好这篇文章https://www.freecodecamp.org/news/learn-css-grid-in-5-minutes-f582e87b1228/教程，其中就详细的解释了布局的基础知识，让我们让列开始具有自适应特性吧。

### 基础响应单位：fraction

> CSS栅格布局带来了一个全新的值：fraction单位，fraction单位通常简写为fr,他允许你根据需要将容器拆分为多个块。

##### 让我们将每一列更改为一个fraction单位宽：

```css
.container {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    grid-template-rows: 50px 50px;
}
```

##### 结果是栅格布局将会把整个宽度分为三个fraction,每列占据一个fraction单位，效果如下：

![avator](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/responseType/initCss2.png?token=AMCTPYYBWEAU4YFLDSAVXGC57HAFC)

##### 如果我们将grid-template-columns的值更改为1fr 2fr 1fr,，第二列的宽度将会是其它两列的两倍。总宽现在是四个 fraction 单位，第二列占据两个 fraction 单位，其它列各占一个 fraction。效果如下：

![avator](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/responseType/initCss3.png?token=AMCTPYYIQIMVBBBMDW4OPWC57HAHW" alt="avator")

##### 总的来说，fraction单位值将使你可以很容易的更改列的宽度

### 高级响应

##### 然而，上面列子并没有给出我们想要的响应性，因为网格总是三列宽。我们希望网格能根据容器的宽度改变列的数量。要做到这一点，你必须学习如下三个概念：

#### repeat()

##### 首先我们学校repeat()函数。这是一个强大的指定列和行的方法。让我们使用repeat()函数来更改网格：

```css
.container {
    display: grid;
    grid-template-columns: repeat(3, 100px);
    grid-template-rows: repeat(2, 50px);
}
```

##### 在上面代码中，repeat(3, 100px)等于100px 100px 100px。第一个参数指定行与列的数量，第二个参数指定它们的宽度，因此它将为我们提供与开始时完全相同的布局：

<img src="https://raw.githubusercontent.com/hsonzhou95/blogImg/master/responseType/initCss4.png?token=AMCTPY6M4IIU2XMAJL5H4T257HAL6" />

#### auto-fit

##### 然后是auto-fit。让我们跳过固定数量的列，将3替换为自适应数量：

```Css
.container {
    display: grid;
    grid-gap: 5px;
    grid-template-columns: repeat(auto-fit, 100px);
    grid-template-rows: repeat(2, 100px);
}
```

##### 效果如下：

![avator](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/responseType/initCss5.png?token=AMCTPY6WRDICC4GA4N4XWTK57HAOG)

##### 现在，栅格将会根据容器的宽度调整其数量。它会尝试在容器中容纳尽可能多的 100px 宽的列。但如果我们将所有列硬写为 100px，我们将永远没法获得所需的弹性，因为它们很难填充整个宽度。正如你在上图看到的，网格通常在右侧留有空白。

#### minmax()

##### 为了解决上述问题，我们需要minmax()。我们将100px替换为minmax(100px,1fr)，代码如下：

```css
.container {
    display: grid;
    grid-gap: 5px;
    grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
    grid-template-rows: repeat(2, 100px);
}
```

> 请注意，所以响应都发生在一行css代码中

##### 效果如下：

![avator](https://raw.githubusercontent.com/hsonzhou95/blogImg/master/responseType/initCss6.png?token=AMCTPY33OBW6CTDFAT555AC57HAP2)

##### 正如你所见，效果完美，minmax()函数定义的范围大于或等于min，小于或等于max。



##### 因此，现在每列将至少为100px。但如果有更多的可用空间，栅格布局将简单地将其均分给每列，因为这些列变成了 fraction 单位，而不是 100px。

##### 到这里，我们就已经实现了响应式的布局，我们还可以在盒子里面添加你任何想要随之改变的内容，这里就不做过多的演示了。

### 浏览器兼容性

##### 在结束本文前，我提下浏览器支持情况，全球77%的网站将支持 CSS Grid，而且比例还在逐步攀升。

##### 2018将是CSSS网格布局的元年。它将获得突破，并成为前端开发者的必备技能，像过去几年CSS Flexbox布局发生的情况一样。

### 来自：知乎，作者：icepy

### 链接：https://zhuanlan.zhihu.com/p/69809343

### 原文作者：Per Harald Borgen

### 原文链接：https://medium.com/free-code-camp/how-to-make-your-html-responsive-by-adding-a-single-line-of-css-2a62de81e431



