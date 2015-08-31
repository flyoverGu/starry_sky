## 场景
在项目中有一小需求，点击菜单中某个选项，让菜单失去hover。  
然后我并没有找到jquery中有这样的方法。 但是找到了可替代的方案。  
那就是用mouseenter和mouseleave或者mouseover和mouseout模拟。  

## 实现
[mouseenter](http://api.jquery.com/mouseenter/): 当鼠标**进入**被绑定元素的时候触发此事件
[mouseleave](http://api.jquery.com/mouseleave/): 当鼠标**离开**被绑定元素的时候触发此事件
[mouseover](http://api.jquery.com/mouseover/): 当鼠标**进入**被绑定元素的时候触发此事件 **对其子元素同样有效果**
[mouseout](http://api.jquery.com/mouseout/): 当鼠标**离开**被绑定元素的时候触发此事件 **对其子元素同样有效果**

看一个 [demo](http://jsfiddle.net/flyover/yq46rm5n/1/)  
看代码:  
html  
````
<div class="outer1">
    <div class="inner1">
    </div>
</div>

<div class="outer2">
    <div class="inner2">
    </div>
</div>

<span class="out"></span>
````
css  
````
div {
    border: 1px solid red;
}

.outer1, .outer2 {
    width: 150px;
    height: 150px;
}

.inner1, .inner2 {
    width: 100px;
    height: 100px;
}
````
js  
````
$('.outer1').on('mouseover', function() {
    $('.out').html($('.out').html() + " mouseover ")
}).on('mouseout', function() {
    $('.out').html($('.out').html() + " mouseout ")
})

$('.outer2').on('mouseenter', function() {
    $('.out').html($('.out').html() + " mouseenter ")
}).on('mouseleave', function() {
    $('.out').html($('.out').html() + " mouseleave ")
})
````

**直观的区别在于当鼠标进入inner1的时候，先触发outer1的mouseout事件，然后再触发inner1的mouseover事件**



