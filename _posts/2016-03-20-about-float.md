---
layout: post
title: 关于浮动的包裹性和破化性
description: "浮动的理解"
modified: 2016-03-21
tags: [css]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

## 关于浮动的包裹性和破坏性

究其根本浮动是用来干嘛的？  

最初的float可没想到它在现在能有这么能干，就是为了能够实现文字环绕图片显示效果。为了能实现这一效果，float属性直接破坏了元素的line-boxes特性(也就是高度)使其从文档中顺着它的方位属性靠边排列。  
比如说：

{% highlight html %}
<p>这是一段文字<img>加上些</p>
<p>这是一段文字<img style="float：left" src="img.jpg"/>加上些</p>
{% endhighlight %}

上面代码里面p标签是content box，但是文字是属于隐藏的inline boxes，对于line-boxes它的高度是由line-height属性决定的，但是对于图片自身有个隐藏的实体高度，而line-boxes高度是子元素最高的元素高度，所以没加浮动之前 li元素的高度和图片高度一致，文字和图片按顺序在一排排列。但是加了浮动属性之后，图片沿着left向左浮动并且破坏inline boxes的高度，使得文字所占高度回复本身，但是图片并没有脱离文档，所以还是在其中占有位置，使文字可以环绕加了float属性的图片。  

可以把绝对定位和浮动结合理解，绝对定位的元素也具有"包裹性"，而且是对所有元素都具有，但是浮动元素跟绝对元素唯一不同的是，绝对定位元素是因为脱离了文档流才具有的"包裹性"，而脱离文档流之后其它的元素就会占有它的位置而使其重叠，而浮动元素是因为它是破坏了高度，而没有脱离文档，位置还有。这也是能够实现文字环绕的重要条件。

当然这只是浮动的**"初心"**,而因为浮动的兼容性和方便对于css理解还有限的我们现在经常使用浮动来实现各种布局，但是我们在使用浮动的时候，别被它的外表迷惑，还是有很多坑要注意。当标签里面的元素没有实体高度的时候，浮动就会导致父元素高度坍塌。而解决这一问题的方案就是清除浮动。  

### 清除浮动的几种办法

不推荐的方法：添加子标签'<div style="clear:both"></div>'。虽然这种方法兼容性很强，但是对于现在的前端大环境，都在往标准化的道路上走，语义话的破化，而且每次都要加空标签。所以是果断摒弃的。 

选用的方法：利用overflow+zoom的方法，因为overflow的auto属性使IE有滚动条，最好使用hidden，但是hidden也会在某些时候，比如子元素需要一个负的margin或者绝对定位的话就会被剪掉。所以要看情况使用。

最全面的方法：就是利用after+zoom

{% highlight css %}
.fix{zoom:1}
.fix:after{display:table,content:".";line-height:0;visibility:hidden;}
{% endhighlight %}

给需要清楚浮动的元素加上fix类。


### 参考文章

[CSS float浮动的深入研究、详解及拓展(一)](http://www.zhangxinxu.com/wordpress/?p=583)  
[CSS float浮动的深入研究、详解及拓展(二)](http://www.zhangxinxu.com/wordpress/?p=594)  