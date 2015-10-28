## 理解generator函数

理解它主要是为了更好得理解es6对同步操作的支持。  
首先看个例子 

```
function * foo(a) {
    let b = yield a;
    let c = yield b;
    return c;
}

let it = foo(1);
it.next(2); {value: 1}
it.next(3); {value: 3}
it.next(4); {value: 4}
```

万万没有想到啊！ 当调用`it.next(3)`时候竟然返回了3。   
看阮老师的[文章](http://es6.ruanyifeng.com/#docs/generator)说next的参数是上个yield的返回值。  
这样似乎能理解为什么返回是3了。 但是为什么是上一个yield的返回值呢？
其实当`it.next(2)`执行之后函数内部是停在`yield a`这个位置，然后当调用`it.next(3)`的时候把3当做`yield a`的返回值，然后继续执行。

