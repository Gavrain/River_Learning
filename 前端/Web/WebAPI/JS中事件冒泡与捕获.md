# JS中事件冒泡与捕获

## 事件冒泡与事件捕获

事件冒泡和事件捕获分别由微软和网景公司提出，这两个概念都是为了解决页面中事件流（事件发生顺序）的问题。考虑下面这段代码，

```html
<div id="outer">
    <p id="inner">Click me!</p>
</div>
```

上面的代码当中一个div元素当中有一个p子元素，如果两个元素都有一个click的处理函数，那么我们怎么才能知道哪一个函数会首先被触发呢？
为了解决这个问题微软和网景提出了两种几乎完全相反的概念。

## 事件冒泡

微软提出了名为事件冒泡(event bubbling)的事件流。事件冒泡可以形象地比喻为把一颗石头投入水中，泡泡会一直从水底冒出水面。也就是说，事件会从最内层的元素开始发生，一直向上传播，直到document对象。

因此在事件冒泡的概念下在p元素上发生click事件的顺序应该是**p -> div -> body -> html -> document**

## 事件捕获

网景提出另一种事件流名为事件捕获(event capturing)。与事件冒泡相反，事件会从最外层开始发生，直到最具体的元素。

因此在事件捕获的概念下在p元素上发生click事件的顺序应该是**document -> html -> body -> div -> p**

## addEventListener的第三个参数

网景 和 微软 曾经的战争还是比较火热的，当时，网景主张捕获方式，微软主张冒泡方式。后来 w3c 采用折中的方式，平息了战火，制定了统一的标准——**先捕获再冒泡**。
addEventListener的第三个参数就是为冒泡和捕获准备的.
addEventListener有三个参数：

> element.addEventListener(event, function, useCapture)

- 第一个参数是需要绑定的事件
- 第二个参数是触发事件后要执行的函数
- 第三个参数默认值是false，表示在**事件冒泡阶段**调用事件处理函数;如果参数为true，则表示在**事件捕获阶段**调用处理函数。

### 冒泡的案例

```html
<div id="s1">s1
    <div id="s2">s2</div>
</div>
<script>
    s1.addEventListener("click",function(e){
        console.log("s1 冒泡事件");
    },false);
    s2.addEventListener("click",function(e){
        console.log("s2 冒泡事件");
    },false);
</script>
```

当我们点击s2的时候，执行结果如下：

![mark](http://man.hhaxmm.cn/blog/20190515/c047tlHkBjjQ.gif)

> 冒泡是先执行里面的，后执行外面的

### 捕获的案例

```html
<div id="s1">s1
    <div id="s2">s2</div>
</div>
<script>
    s1.addEventListener("click",function(e){
        console.log("s1 捕获事件");
    },true);
    s2.addEventListener("click",function(e){
        console.log("s2 捕获事件");
    },true);
</script>
```

当我们点击s2的时候，执行结果如下：

![mark](http://man.hhaxmm.cn/blog/20190515/uzHEGdStt9q9.gif)

> 捕获是先执行外面的，后执行里面的

## 事件捕获vs事件冒泡

当事件捕获和事件冒泡一起存在的情况，事件又是如何触发呢。
这里记被点击的DOM节点为target节点

1. document 往 target节点，捕获前进，遇到注册的捕获事件立即触发执行
2. 到达target节点，触发事件（对于target节点上，是先捕获还是先冒泡则捕获事件和冒泡事件的注册顺序，先注册先执行）
3. target节点 往 document 方向，冒泡前进，遇到注册的冒泡事件立即触发

### 总结下就是:

- 对于非target节点则先执行捕获在执行冒泡
- 对于target节点则是先执行先注册的事件，无论冒泡还是捕获

```html
<div id="s1">s1
    <div id="s2">s2</div>
</div>
<script>
s1.addEventListener("click",function(e){
        console.log("s1 冒泡事件");         
},false);
s2.addEventListener("click",function(e){
        console.log("s2 冒泡事件");
},false);
        
s1.addEventListener("click",function(e){
        console.log("s1 捕获事件");
},true);
        
s2.addEventListener("click",function(e){
        console.log("s2 捕获事件");
},true);
</script>
```

当我们点击s2的时候,执行结果如下：

这里大体分析下执行结果：

> 点击s2，click事件从document->html->body->s1->s2(捕获前进)
> 这里在s1上发现了捕获注册事件，则输出**"s1 捕获事件"**
> 到达s2，已经到达目的节点，
> s2上注册了冒泡和捕获事件，先注册的冒泡后注册的捕获，则先执行冒泡，输出**"s2 冒泡事件"**
> 再在s2上执行后注册的事件，即捕获事件，输出**"s2 捕获事件"**
> 下面进入冒泡阶段，按照s2->s1->body->html->documen(冒泡前进)
> 在s1上发现了冒泡事件，则输出**"s1 冒泡事件"**

## 事件冒泡与事件捕获应用:事件代理

在实际的开发当中，利用事件流的特性，我们可以使用一种叫做事件代理的方法。

```html
<ul id="color-list">
    <li>red</li>
    <li>yellow</li>
    <li>blue</li>
    <li>green</li>
    <li>black</li>
    <li>white</li>
</ul>
```

如果点击页面中的li元素，然后输出li当中的颜色，我们通常会这样写:

```js
(function(){
    var color_list = document.getElementById('color-list');
    var colors = color_list.getElementsByTagName('li');
    for(var i=0;i<colors.length;i++){                          
        colors[i].addEventListener('click',showColor,false);
    };
    function showColor(e){
        var x = e.target;
        console.log("The color is " + x.innerHTML);
    };
})();
```

利用事件流的特性，我们只绑定一个事件处理函数也可以完成：

```html
(function(){
    var color_list = document.getElementById('color-list');
    color_list.addEventListener('click',showColor,false);
    function showColor(e){
        var x = e.target;
        if(x.nodeName.toLowerCase() === 'li'){
            console.log('The color is ' + x.innerHTML);
        }
    }
})();
```

使用事件代理的好处不仅在于将多个事件处理函数减为一个，而且对于不同的元素可以有不同的处理方法。假如上述列表元素当中添加了其他的元素（如：a、span等），我们不必再一次循环给每一个元素绑定事件，直接修改事件代理的事件处理函数即可。

## 冒泡还是捕获？

对于事件代理来说，在事件捕获或者事件冒泡阶段处理并没有明显的优劣之分，但是由于事件冒泡的事件流模型被所有主流的浏览器兼容，从兼容性角度来说还是建议大家使用事件冒泡模型。

