## html

**超文本标记语言, 是一套标记标签, 使用标记标签来描述网页**

### 标签

使用尖括号包围标签类型, 一般成对出现, 简单实例如下

```html
<html>
<body>

<h1>第一个标题</h1>
<p>第一个段落<p>

</body>
<!-- body之间的内容是用户可见的 -->
</html>
```

### 基础元素

* 标题
   ```html
   <h1> - <h6> 六级标题, <hr /> 创建水平线
   ```
* 段落
   ```html
   <p> </p> ,  <br />插入空行
   ```

* 图像
   ```html
   <img src="test.png" width="100" height="120" />只用一对尖括号, src指定图片
   ```


* 注释
   ```html
   <!--- zhushi --->
   ```

###属性
  ```html
  <h1 align="center"> 标题居中对齐
  <body bgcolor="yellow"> 背景色为黄色
  ```
  属性值放在尖括号内

###样式
  ```html
  <body style="background-color:yellow">
  ```
  样式是一种属性,style代替了原本的bgcolor属性,属性字段会被逐渐淘汰

###链接
  ```html
  <a href="www.baidu.com" target="_blank"> baidu </a>
  ```
  target字段表示点击后在哪里打开,_blank为新窗口打开

###图像
  ```html
  <img src="url" alt="nopic" />
  ```
  alt是如果找不到图片,就显示alt里的文本.
  img是空标签,只包含属性,没有闭合标签.
