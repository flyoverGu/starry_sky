## 列出我知道的clone方法

- 利用JSON  
    ````
        function clone(obj) {
            return JSON.parse(JSON.stringify(obj));
        }
    ````
    当对象不能被JSON.stringify的时候就GG了。例如对象中有方法的时候。

- 递归逐个复制属性
    ````  
        function clone (obj) {
            if (obj === null || typeof obj !== 'object') return obj;
            var newObj = {};
            for (var i in obj) {
                newObj[i] = this.clone(obj[i]);
            }
            return newObj;
        };
    ````

- 利用constructor方法
    ````
        var obj = {
            clone: function(o) {
                return new this.constructor(o);
            }
        }
    ````
    不能clone对象的方法

