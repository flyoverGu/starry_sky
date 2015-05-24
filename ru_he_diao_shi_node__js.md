# 如何调试node.js

一直以为nodejs没办法打包，查了官方文档才知道原来也是能debug的。文档[在此](https://nodejs.org/api/debugger.html#debugger_watchers)

## 调试器

V8通过简单的tcp端口实现调试。node通过构建客户端来进行调试。怎么是使用呢？就是在实用`node`命令的时候增加`debug`参数：

    > node debug test.js
    < debugger listening on port 5858
    connecting... ok
    break in test.js:1
      1 var x = 5;
      2 var foo = function() {
      3     debugger;
    debug>

node的调试器没有提供全部node命令，但是简单的命令像：下一步和进入函数等还是有的。通过在源码中声明`debugger`，进行打断点。
看我写的一个例子：

    1 var x = 5;
    2 var foo = function() {
    3     debugger;
    4     console.log('in foo');
    5     console.log(x);
    6 }
    7 x ++;
    8 console.log('out foo');
    9 foo();

我看断点设置在第三行，然后执行`node debug test.js`

    node debug test.js
    < debugger listening on port 5858
    connecting... ok
    break in test.js:1
      1 var x = 5;
      2 var foo = function() {
      3     debugger;
    debug>

首先默认从第一行开始。 

    debug> n
    break in test.js:2
      1 var x = 5;
      2 var foo = function() {
      3     debugger;
      4     console.log('in foo');
    debug>

命令`n`或者`next`下一步，即第二行。
    debug> c
    < out foo
    break in test.js:3
     1 var x = 5;
     2 var foo = function() {
     3     debugger;
     4     console.log('in foo');
     5     console.log(x);
    debug>
命令`c`或者`cont`下一个断点处。

    debug> repl
    Press Ctrl + C to leave debug repl
    > x
    6
    > x + x
    12
    >
命令`repl`, 开启一个当前上下文环境的命令行模式，可以用来求值等。非常好用！

   debug>quit

退出

## 命令介绍

* cont,c 继续执行（到一下个断点处）
* next,n 下一步
* step,s 进去函数体
* out,o 跳出函数
* pause 暂停执行
* repl 开启一个当前上下文环境的命令行模式

