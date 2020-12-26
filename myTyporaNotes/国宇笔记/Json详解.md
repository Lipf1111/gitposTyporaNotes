### json详解（JS风格）

- JSON的全称是”JavaScript Object Notation”，意思是JavaScript对象表示法

- 它是一种基于文本，独立于语言的轻量级数据交换格式

- 相对于txt，word来说，json格式更加明确，获取重要信息非常方便。

  相对于xml来说，json格式更加简洁，存储同样的文件，花费的内存更小。

  相对于Excel来说，json更适合存储字符类文件。Excel相当于比较简单的数据库了。

  相对于数据库来说，json更加方便，数据库我们还需要做一些设置，安装一些软件。json可以直接使用。

- XML也是一种数据交换格式，为什么没有选择XML呢？因为XML虽然可以作为跨平台的数据交换格式，但是在JS(JavaScript的简写)中处理XML非常不方便，同时XML标记比数据多，增加了交换产生的流量，而JSON没有附加的任何标记，在JS中可作为对象处理，所以我们更倾向于选择JSON来交换数据。

#### JSON的两种结构

JSON有两种表示结构，对象和数组。
对象结构以”{”大括号开始，以”}”大括号结束。中间部分由0或多个以”，”分隔的”key(关键字)/value(值)”对构成，关键字和值之间以”：”分隔，语法结构如代码。

```json
{ 
    key1:value1,
 	key2:value2,
 	...
}
```

其中关键字是字符串，而值可以是字符串，数值，true,false,null,对象或数组

数组结构以”[”开始，”]”结束。中间由0或多个以”，”分隔的值列表组成，语法结构如代码。

```json
[  
    {    
        key1:value1,
        key2:value2
    },
    {
        key3:value3,
        key4:value4 
    }
]
```

#### 认识JSON字符串

之前我一直有个困惑，分不清普通字符串，json字符串和json对象的区别。经过一番研究终于给弄明白了。比如在js中。

字符串：这个很好解释，指使用“ ”双引号或’ ’单引号包括的字符。例如：`var comStr = 'this is string';`
json字符串：指的是符合json格式要求的js字符串。例如：

`var jsonStr = "{StudentID:'100',Name:'tmac',Hometown:'usa'}";`
json对象：指符合json格式要求的js对象。例如：

`var jsonObj = { StudentID: "100", Name: "tmac", Hometown: "usa" };`

#### 在JS中如何使用JSON

JSON是JS的一个子集，所以可以在JS中轻松地读写JSON。读和写JSON都有两种方法，分别是利用”.”操作符和“[key]”的方式。
我们首先定义一个JSON对象，代码如下。

```json
var obj = {
    1: "value1",
    "2": "value2",
    count: 3,
    person:
    [ //数组结构JSON对象，可以嵌套使用
        {
            id: 1,
            name: "张三"
         },
        {
            id: 2,
            name: "李四"
        }
    ],
    object:
    { //对象结构JSON对象        
        id: 1,
        msg: "对象里的对象"
    }
};
```

1，从JSON中读数据

```json
function ReadJSON() {
    alert(obj.1); //会报语法错误，可以用alert(obj["1"]);说明数字最好不要做关键字      
    alert(obj.2); //同上
    alert(obj.person[0].name); //或者alert(obj.person[0]["name"])      
    alert(obj.object.msg); //或者alert(obj.object["msg"])    
}
```

2，向JSON中写数据

比如要往JSON中增加一条数据，代码如下：

```json
function Add() {//往JSON对象中增加了一条记录
    obj.sex="男"//或者obj["sex"]="男"
}
```

增加数据后的JSON对象如图：

![img](.\Pic\151606513913065.jpg)

3，修改JSON中的数据

我们现在要修改JSON中count的值，代码如下：

```json
function Update() {      
    obj.count = 10; //或obj["count"]=10
}
```

修改后的JSON如图。

![img](.\Pic\151608389851529.jpg)

4，删除JSON中的数据

我们现在实现从JSON中删除count这条数据，代码如下：

```json
function Delete() {
    delete obj.count;
}
```

　　删除后的JSON如图

![img](.\Pic\151609318915431.jpg)

可以看到count已经从JSON对象中被删除了。

5，遍历JSON对象(JS中)

可以使用for…in…循环来遍历JSON对象中的数据，比如我们要遍历输出obj对象的值，代码如下：

```json
function Traversal() {
    for (var c in obj) {
        console.log(c + ":", obj[c]);
    }
}
```

　　程序输出结果为：

![img](.\Pic\151610325328972.jpg)