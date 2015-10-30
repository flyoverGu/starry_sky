##背景
当在一些需要动态渲染html的页面时候，我们一般会选择一些html模版库来做这个事。  
但是又觉得现有的handlebars、mustcache等又太庞大，压根用不那么多功能。  
我们只需要简单的if else，和一些变量替换。 
然后在寻寻觅觅之后，找到了一个非常精巧的一个模版。  


##功能&使用
支持 if for else seitch case break 等

使用起来非常简单。

源码解析如下
````
function tkTemplate(select, data) {
    // 模版
    var tp = $(select).html();
    // 自定义识别区域 <%code%>
    var re = /<%([^%>]+)?%>/g;
    // 能识别的代码 
    var reCode = /(^( )?(if|for|else|switch|case|break|{|}))(.*)?/g;
    // 最后执行的code
    var code = 'var r = [];\n';
    // 识别的位置
    var cursor = 0;
    // 把识别到的文本，加到code中
    var add = function(line, js) {
        js ? (code += line.match(reCode) ? line + '\n' : 'r.push(' + line + ');\n') :
        (code += line != '' ? 'r.push("' + line.replace(/"/g, '\\"') + '");\n' : '');
        return add;
    }
    // 全文中找关键字
    while (match = re.exec(tp)) {
        // 把识别到的关键字之前的文本放入code，并且把关键字也放入code
        add(tp.slice(cursor, match.index))(match[1], true);
        // 改变游标位置
        cursor = match.index + match[0].length;
    }
    add(tp.substr(cursor, tp.length - cursor));
    code += 'return r.join("");';
    // 执行code
    return new Function(code.replace(/[\r\t\n]/g, '')).apply(data);
}

````
