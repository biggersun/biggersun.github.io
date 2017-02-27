---
layout: post
title: javascript基础笔记
description: "Just test."
modified: 2016-03-17
tags: [javascript]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---
# javascript
## 前言  
总结一些我在javacript学习中的难点和容易忽略的地方。
 
附上javascript[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)  

还有几本好书的网上资源：
  
- [javascript秘密花园](http://bonsaiden.github.io/JavaScript-Garden/zh/)  
- [jQurey的基本原理](http://docs.huihoo.com/jquery/jquery-fundamentals/zh-cn/index.html) 

 - - -

### null和undefined  
- undefined 变量声明之后未被初始化，这时变量的默认值为 undefined 

{% highlight js %}
var a;  
alert(undefined == a);//ture  
var b=[1,2,3]
typeof(a[3])//undefined  
{% endhighlight %}
 
- null 表示尚未存在的对象，例如函数返回的不存在的对象，得到null  
	
	>alert(null == document.getElementById('notExistElement'));  //ture  

### 运算符短路逻辑的运用
- 访问对象属性时，事先检测是否为空
	>var name = o && o.getName;
- 设置默认值
	>var name = otherName || "default";
- 三元操作符
	>var allowed = (age>18) ? "yes" : "no";

### 如何访问用预留关键字做名称的值

{% highlight js %}
obj.for = "sun";//语法错误，for 是一个预留关键字
obj["for"] = "sun";//ok
{% endhighlight %}

### for循环的技巧
- 正常for循环,每次循环都要计算一次长度
	>for (var i = 0;i < a.length;i++){}
- 改进版，定义两个变量

	>for (var i = 0,len = a.length; i < len; i++){}

- 如果在已知数组中不含假值(如undefined)时，还有一种更好的方法

	>for (var i = 0,item ;item = a[++];){  //通过item代替a[i]  }  
	
     这种方法还是判断for的中间部分表达式(item = a[i])的真假，i每递增1，数组元素逐个传递给item，直到遇到假元素(如undefined)循环结束
     
### arguments对象

- 编写一个加法函数

{% highlight js %}
function add(a,b){  
	return a + b;  
}
{% endhighlight %}
	
当传入三个实参时

{% highlight js %}
add(2,3,4);  
5//忽略了最后一个参数
{% endhighlight %}
	
如果重写方法接受所有被传入的参数

{% highlight js %}
function add(){  
	var sum = 0;  
	for(var i = 0; j = arguments.length; i++){  
	sum +=arguments[i];
	}  
	return sum;  
}
{% endhighlight %}

函数实际上是访问的就是函数体中的arguments内部对象，类似数组的对象，包括了所有被传入的参数。

- 那在计算平均数的时候传入是一个数组怎么办？
  
我们可以在函数里面遍历数组

{% highlight js %}
function avgArray(arr){
	var sum = 0;
	for(var i=0,j = arr.length; i < j ;i++){
		sum +=arr[i];
	}	
	return sum/arr.length;
}
{% endhighlight %} 

当然可以用更简单的方法，用apply()重用我们已经创建的avg方法

{% highlight js %}
avg.apply(null,[1,2,4,5]);
//3
{% endhighlight %}
  
### 嵌套函数与闭包  
    
- 嵌套函数是在函数内部定义函数
		
{% highlight js %}
function out(){
	var a=1;
	function in(){
		return a++;
	}		
	return in();
}
{% endhighlight %}
 

>注意:函数内部定义变量时一定要使用var，如果不用，实际上是声明了一个全局变量！

{% highlight js %}
function c(){
	n = 10;
}
c();
alert(n);//10
{% endhighlight %}

嵌套函数可以访问父函数中的变量,当某个函数依赖于其它一两个对其余代码没有影响时，可以将这一两个函数嵌套在会被调用的函数内部，减少全局作用域下的函数数量，利于编写易于维护的代码。
	
- 闭包

{% highlight js %}
function mAdder(a){
	return function(b){
		return a+b;
	}
}
a = mAdder(1);
b = mAdder(2);
a(2);//?
b(3);//?  
{% endhighlight %}

闭包与嵌套函数十分相似，唯一的不同是闭包返回了一个函数。更有意思的是

{% highlight js %}
a(2);//3
b(3);//5
{% endhighlight %}
	
为什么？局部变量不应该在函数结束时释放了么？但是结果告诉我们局部变量仍然存在，否则add()函数不能正常工作，也就是说这里的局部变量被保留了。

>当返回函数时保留了一个指向函数创建变量的作用域的引用，导致这个作用域对象即局部变量不会被垃圾回收器回收直到指向返回的那个函数的引用计数为零（返回的内部函数被执行完成）。  
>一个闭包就是一个函数和被创建的函书中的作用域对象的组合。

### 闭包的内存泄漏  
- 闭包很容易发生无意识的内存泄漏(IE)
	
{% highlight js %}
function changeColor(){
	var el = document.getElementById('el');
	el.onclick = function(){
		el.style.backgroundColor('red');
	}
}
{% endhighlight %}

当点击时变红就会发生内存泄漏，因为对el的引用不小心放在一个匿名的内部函数中，这就在javascript对象(这个内部函数)和本地对象(el)之间创建了一个循环引用。  
当然也有很多解决办法，最简单的就是不使用el

{% highlight js %}
function changeColor(){
	document.getElementById('el').onclick = function(){
		this.style.backgroundColor('red');
	}
}
{% endhighlight %}

还可以添加另外的闭包来破坏因为闭包导致的循环引用 

{% highlight js %}
function changrColor(){
	var clickC = function(){
		this.style.backgroundColor('red');
	}
	(function(){
		var el = document.getElementById('el');
		el.onclick = clickC();
	})();
}
{% endhighlight %}

内部函数被直接执行，并在clickC创建的闭包中隐藏了它的内容。

### javascript对象的理解

- 数字的字面量是不是对象？

{% highlight js %}
2.toString();//出错：SyntaxError    
其实是javascript解析器的一个错误，它试图将点操作符解析为浮点数字面值的一部分  
可以通过下面的方法让数字的字面量"变得"正常

2..toString();//第二个点可以正常解析
2 .toString();//点号前加空格
(2).toString();//2先被计算
{% endhighlight %}
