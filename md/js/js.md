## JavaScript基础知识

### 1.使用
html中,JavaScript代码必须位于<script> </script>之间
```html
<p id="demo"></p>

<script>
    document.getElementById("demo").innerHTML="test"
</script>
```
#### 将JavaScript函数代码块放在代码块中
script标签可以放在<body>或者<head>中
```html
<p id="demo"></p>

<script>
function func() {
    document.getElementById("demo").innerHTML="func"
}
</script>

<button type="button" onclick="func()"></button>
```
#### 将JavaScript放在外部文件中
使用script标签将js文件导入
```html
<script src="my.js"></script>
```

### 2.输出
### innerHTML
使用document.getElementById(id)方法访问HTML元素
innerHTML方法可以改变HTML的内容
```HTML
<p id="t"></p>
<script>
document.getElementById("t").innerHTML="test"
</script>
```

### document.write()
类似于print
```JavaScript
document.write(1+1)
```

### window.alert()
以警示框来显示数据
```JavaScript
window.alert("test")
```

### console.log()
在f12调试器的console中打印log
```JavaScript
console.log(1+1)
```

### 3.数据类型
```JavaScript
var length = 1; // 数值,只有一种数值类型,不区分float和int
var name = "w"; // string用单引号双引号都可以
var cars = [1,2,3]; // 数组, 里面可以是任意类型
var x = {firstName:"bill",lastName:"gates"}; // duixiang
```
js是弱类型语言,可以转换变量的类型,使用typeof来判断变量的类型.

### 4.函数
```JavaScript
function test(v1, v2) {
  return v1+v2;
}
```
不指定返回参数,目前看是只支持一个返回参数

### 5.对象
```JavaScript
var person = {
    firstname: "bill",
    lastname: "gates",
    id: 678,
    fullname: function() {
        return this.firstname + " " + this.lastname;
    }
};
```
