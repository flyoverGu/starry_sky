首先我们创建一个dog对象，并且一个属性和一个一个方法像这样
````
var dog = {
    name: "is dog",
    say: function() {
        console.log(this.name);
    }
}
````
接着我们调用的dog的say方法的时候
````
dao.say();
"is dog"
````
然后当cat对象也想使用dog的say方法的时候
````
var cat = {
    name: "is cat"
}
dog.say.apply(cat);
"is cat"
````
这一切看起来很顺利，但是我们的dog比较霸道，不愿意和别人共享它的方法。  
那么如何做到say方法的上下文环境永远绑定到dog上呢？  
像这样
````
var dog = {
    name: "is dog",
    say: function() {
        console.log(this.name);
    },
    bindSay: function() {
        this.say = this.say.bind(this); // 调用了 function.bind 方法
    }
}

var cat = {
    name: "is cat"
}

dog.bindSay();
dog.say.apply(cat);

// 结果
"is dog"
````
