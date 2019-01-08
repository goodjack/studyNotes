### CSS 三大特性

层叠性，继承性，优先级，特殊性

### css 一些技巧

行高可以使文字垂直居中

对于 div 内的 img 推荐使用 background 样式移动 img

盒子居中对齐 margin : 0 auto

**嵌套块元素垂直外边距的合并**

解决方案：

1. 在父元素用 border : 1px ;
2. 在父元素用 padding : 1px
3. 父元素 overflow : hidden

float 默认让元素转化为行内块



### 清除浮动

清除浮动主要为了解决父级元素因为子级浮动引起内部高度为 0 的问题

给父级元素添加 `overflow : hidden`

`:after ` 伪元素清除浮动

```html
.clearfix::before,.clearfix::after {
	content:'';
	display:table;
}
.clearfix::after {
	clear:both;
}
.clearfix {
	*zoom: 1;
}
```

针对 IE6 IE7 使用 `.clearfix { *zoom:1;}`



### 定位position

静态定位（static）：作用取消定位 `position：static`

相对定位（relative）：

1. 每次移动位置，以自己的左上角为基点
2. 相对定位，通过偏移移动位置，但是原来的所占位置，继续占有

绝对定位（absolute）：

1. 没有父级元素或父级元素未定位，以浏览器屏幕为基点定位
2. 父级元素有定位，则是将元素依据最近的已经定位（绝对、固定或相对定位）的父元素（祖先）进行定位
3. 不会占据原来的位置

一般定位使用 **子绝父相**

使用 position 将盒子模型居中：

```css
.ceshi {
	height: 100px;
    weight:200px;
	position: absolute;	// relative 不行
	left: 50%;			// 水平居中
    margin-left: -100px;	// 水平居中  width 的一半
    top: 50%;			// 垂直居中
    margin-top: -50px;	// 垂直居中 height 的一半
}
```

fix 固定定位：和父元素没有关系，只以浏览器为基点



### 叠放次序 （z-index）



当 div border 重叠时，可以使用 `margin-left : -1` 



`vertical-align` 通常用来控制图片/表单与文字的对齐，不能控制文字居中，不影响块级元素中的内容对齐，只针对行内元素或者行内块元素，特别是行内块元素



### css 精灵技术

**css 精灵是一种处理网页背景图像的方式**，它将一个页面涉及到的所有零星背景图像都集中到一张大图中去，然后将大图应用于网页。这样当用户访问该页面时，**只需向服务发送一次请求**，网页中的背景图像即可全部展示出来，通常情况下，这个由很多小的背景图像合成的大图被称为精灵图，主要使用 `background-position`

当背景图片很少的时候没有必要使用精灵图



### 滑动门原理

![1540224197950](assets/1540224197950.png)

类似 [微信首页](weixin.qq.com)

获取上图的左右两边的圆角

```html
<style>
    a {
        display: inline-block;
        background: url() no-repeat;	/* 此处默认是 left */
        color: #fff;
        text-decoration: none;
        height: 33px;
        margin: 100px;
        line-height: 33px;
        padding-left: 15px;
    }
    
    span {
        display: inline-block;
        height: 33px;
        background: url() no-repeat right;
        padding-right: 15px;
    }
</style>

<a href="#">
    <span>首页</span>
</a>
```



### 使用字体图标

```html
<style>
    @font-face {
        font-family: 'icommon';	 /* 这个名字可以随意改 */
        src:	url('fonts/icomoon.eot?7kkyc2');	/* 问号后的是为了兼容IE */
        src:	url('fonts/icomoon.eot?7kkyc2#iefix') format('embedded-opentype'),
            url('fonts/icomoon.ttf?7kkyc2') format('truetype'),
            url('font/icomoon.woff?7kkyc2') format('woff'),
            url('font/icomoon.svg?7kkyc2#icomoon') format('svg');
        font-style: normal;	/* 对字体图标一般使用 i em 所以使用这个属性 */
    }
</style>
```



去除图片底侧缝隙，因为图片适合基线对齐

```css
img {
    vertical-align: top; /* 做初始化 css */
}
```



轮播图结构

```html
<div class="lunbotu">
    <div class="arrow">
        <a href="#" class="arr-l"> < </a>
        <a href="#" class="arr-r"> > </a>
    </div>
        
        <ol>
            <li> 此处是轮播图下面的小圆点 </li>
        </ol>
        
        <ul>
            <li><a href="#"><img src=""></a></li>
        </ul>
</div>
```



图片自适应，等比例缩放

```html
<style>
    div {
        border: 1px solid #000;
        float: left;
        width: 390px;
        height: 130px;
    }
    img {
        width: 100%;	/*设置图片的宽度和父亲一样宽*/
    }
</style>
<div>
    <img src="">
</div>
```



tab 栏切换

```html
<div class="news">
    <div class="tab-hd">
        <a href="javascript:;" class="cuxiao">促销</a>
        <a href="javascript:;">公告</a>
        <a href="#" class="more">更多</a>
        <div class="line"></div>	//这个是下划线，使用position：absoulte定位
    </div>
</div>
```



CSS 指定超出几行自动隐藏

```css
-webkit-line-clamp: n;	// 设置行数，n 为行数 （必选）
display: -webkit-box;	// 盒子模型 （必选）
-webkit-box-orient: vertical;	// 元素排列方式 （必选）

// 案例
-webkit-line-clamp: 3;
display:-webkit-box;
-webkit-box-orient:vertical;
overflow:hidden;	// 隐藏溢出的内容
text-overflow:ellipsis;	// 超出的内容显示省略号
```

