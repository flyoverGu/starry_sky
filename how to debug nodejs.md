# 如何调试node.js

一直以为nodejs没办法调试，查了官方文档才知道原来也是能debug的。文档[在此](https://nodejs.org/api/debugger.html#debugger_watchers)

## 调试器

V8通过简单的tcp端口实现调试。node通过构建客户端来进行调试。  
怎么使用呢？就是在使用`node`命令的时候增加`debug`参数：

````
node debug test.js
debugger listening on port 5858
connecting... ok
break in test.js:1
  1 var x = 5;
  2 var foo = function() {
  3     debugger;
debug>
````

node的调试器没有提供全部node命令，但是简单的命令像：下一步和进入函数等命令还是有的。  
可以通过在源码中声明`debugger`，进行打断点。
看我写的一个例子：

````
1 var x = 5;
2 var foo = function() {
3     debugger;  // <------ 设置的断点
4     console.log('in foo');
5     console.log(x);
6 }
7 x ++;
8 console.log('out foo');
9 foo();
````

我把断点设置在第三行，然后执行`node debug test.js`

````
node debug test.js
< debugger listening on port 5858
connecting... ok
break in test.js:1   // <----- 现在断点在第一行
  1 var x = 5;
  2 var foo = function() {
  3     debugger;
debug>
````

命令`n`或者`next`下一步，即第二行。

````
debug> n 
break in test.js:2  // <----- 执行了n 之后断点在第2行了
  1 var x = 5;
  2 var foo = function() {
  3     debugger;
  4     console.log('in foo');
debug>
````

命令`c`或者`cont`下一个断点处。
````
debug> c
< out foo
break in test.js:3  // <----- 执行了c 之后到了 我设置的断点处
 1 var x = 5;
 2 var foo = function() {
 3     debugger;    // <----- 我是断点
 4     console.log('in foo');
 5     console.log(x);
debug>
````

命令`repl`, 开启一个当前上下文环境的命令行模式，可以用来求值等。非常好用！  
````
debug> repl
Press Ctrl + C to leave debug repl
> x         // 这里x为什么等于6呢？ 因为按代码执行的顺序，是先执行 x++ 
6           // 然后执行函数foo。所以在函数内部断点处 x 就等于 6
> x + x  
12
debug>quit  // <----- 退出
````


## 命令介绍

* cont,c 继续执行（到一下个断点处）
* next,n 下一步
* step,s 进去函数体
* out,o 跳出函数
* pause 暂停执行
* repl 开启一个当前上下文环境的命令行模式

