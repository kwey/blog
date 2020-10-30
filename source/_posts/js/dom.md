---
title: HTML DOM (文档对象模型)
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---
> 文档对象模型 (DOM) 是HTML和XML文档的编程接口。它提供了对文档的结构化的表述，并定义了一种方式可以使从程序中对该结构进行访问，从而改变文档的结构，样式和内容


DOM 将文档解析为一个由节点和对象（包含属性和方法的对象）组成的结构集合。简言之，它会将web页面和脚本或程序语言连接起来。
说白了DOM就是浏览器为JavaScript提供的一系列接口（通过window.documnet提供的），通过这些接口我们可以操作web页面。 但DOM并不是编程语言，它是文档对象的模型，该模型是独立于编程语言的。

## DOM 创建
DOM节点（Node）通常对应于一个标签，一个文本，或者一个HTML属性。DOM节点有一个nodeType属性用来表示当前元素的类型，它是一个整数：<ol><li>Element，元素 ------1</li><li>Attribute，属性 --------2</li><li>Text，文本 ------3</li><li>comment 节点 ------8</li><li>Document 节点 ------9</li><li>DocumentFrament节点 ------11</li><li><a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Node/nodeType" target="_blank">其他的参见这</a>
</li></ol>
DOM节点创建最常用的便是<a href="http://www.w3school.com.cn/xmldom/met_document_createelement.asp">document.createElement</a>和document.createTextNode方法：


``` javascript
var el1 = document.createElement('div');
var el2 = document.createElement('input');
var node = document.createTextNode('hello world!');
```


## DOM 查询
元素查询的API返回的的结果是DOM节点或者DOM节点的列表。document提供了两种Query方法：


``` javascript
// 返回当前文档中第一个类名为 "myclass" 的元素
var el = document.querySelector(".myclass");

// 返回一个文档中所有的class为"note"或者 "alert"的div元素
var els = document.querySelectorAll("div.note, div.alert");

// 获取元素
var el = document.getElementById('xxx');
var els = document.getElementsByClassName('highlight');
var els = document.getElementsByTagName('td');

var els = document.getElementsByName('name'); // name属性
```

## DOM事件
1. element.<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener" target="_blank">addEventListener</a>(type, listener, [, options])
给元素添加指定事件type以及响应该事件的回调函数listener。options也可以为Boolean值表示捕获阶段还是冒泡阶段执行默认为false，冒泡阶段执行
2. element.<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/removeEventListener" target="_blank">removeEventListener</a>(type, listener, [, options])
移除元素上指定事件，如果元素上分别在捕获和冒泡阶段都注册了事件，需要分别移除。
3. document.<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createEvent" target="_blank">createEvent</a>()
创建一个自定义事件，随后必须使用init进行初始化。
4. element.<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/dispatchEvent" target="_blank">dispatchEvent</a>(event)
对指定元素触发一个事件。

``` javascript
// 创建事件
var event = document.createEvent('Event');

// 定义事件名为'build'.
event.initEvent('build', true, true);

// 监听事件
elem.addEventListener('build', function (e) {
        // e.target matches elem
    }, false);

// 触发对象可以是任何元素或其他事件目标
elem.dispatchEvent(event);
```

## 样式操作
1、js如何操作class

``` javascript
加类：   dom.classList.add(className);
移除类： dom.classList.remove(className);
替换类： dom.classList.replace(className1,className2);
判断类： dom.classList.contains(className);　　　　// 这个只能一次判断一个 类
```


2、js如何操作style

如果属性有'-'号，就写成驼峰的形式（如textAlign）  如果想保留 - 号，
就中括号的形式  element.style['text-align'] = '100px';


3、设置style的属性

``` javascript
element.setAttribute('style', 'height: 100px !important');
```


4、使用setProperty  如果要设置!important，推荐用这种方法设置第三个参数

``` javascript
element.style.setProperty('height', '300px', 'important');
```

5、设置cssText
``` javascript
element.style.cssText += 'height: 100px !important';
```

6、insertRule()、addRule
``` javascript
[sheet].insertRule([CSS样式]，指定位置)
    
[sheet]表示某个样式表，它可以通过document.styleSheets来获得
```
然后用JS获取这个样式表：



最后我们就可以给这个样式表中添加样式了：
``` javascript
var sheet = document.styleSheets[0];
sheet.addRule('.box', 'height: 100px');
```

sheet.insertRule('#box{width: 300px; height: 300px; background-color: #0f0;}',0);
或者
``` javascript
// 或者插入新样式时操作
    var styleEl = document.createElement('style'),
        styleSheet = styleEl.sheet;

    styleSheet.addRule('.box', 'height: 100px');
    styleSheet.insertRule('.box {height: 100px}', 0);

    document.head.appendChild(styleEl);  
```

    
    
## DOM元素的属性和方法

<table><thead><tr><th>方法</th><th>描述</th></tr></thead><tbody><tr><td>element.id</td><td>设置或者返回元素的 id 属性</td></tr><tr><td>element.className</td><td>设置或者返回元素的 class 属性</td></tr><tr><td>element.style</td><td>设置或返回元素的 style 属性</td></tr><tr><td>element.title</td><td>设置或者返回元素的 title 属性</td></tr><tr><td>element.innerHTML</td><td>设置或返回元素的内容</td></tr><tr><td>element.tagName</td><td>返回元素的标签名</td></tr><tr><td>element.lastChiled</td><td>返回元素的最后一个子元素</td></tr><tr><td>element.nodeValue</td><td>设置或返回元素的值</td></tr><tr><td>element.nodeName</td><td>返回元素的名称</td></tr><tr><td>element.nodeType</td><td>返回元素的节点类型</td></tr><tr><td>element.toString()</td><td>把元素转为字符串</td></tr><tr><td>element.textContent</td><td>设置或者返回节点及其后代的内容</td></tr><tr><td>element.parentNode</td><td>返回元素的父节点</td></tr><tr><td>element.appendChild()</td><td>向元素添加新的子节点，作为最后一个子节点</td></tr><tr><td>element.cloneNode()</td><td>克隆元素</td></tr><tr><td>element.getAttribute()</td><td>返回元素节点的指定属性值</td></tr><tr><td>element.getAttributeNode()</td><td>返回指定的属性节点</td></tr><tr><td>element.removeAttribute()</td><td>从元素中移除指定属性</td></tr><tr><td>element.removeAttributeNode()</td><td>移除指定的属性节点，并返回被移除的节点</td></tr><tr><td>element.hasAttribute()</td><td>如果元素拥有指定属性，则返回true,否则返回false</td></tr><tr><td>element.hasAttributes()</td><td>如果元素拥有属性，则返回true,否则返回false</td></tr><tr><td>element.hasChildNodes()</td><td>如果元素拥有子节点，则返回true,否则返回false</td></tr><tr><td>element.clientWidth</td><td>返回元素的可见宽度</td></tr><tr><td>element.clientHeight</td><td>返回元素的可见高度</td></tr><tr><td>element.offsetWidth</td><td>返回元素的宽度</td></tr><tr><td>element.offsetHeight</td><td>返回元素的高度</td></tr><tr><td>element.offsetLeft</td><td>返回元素的水平偏移位置</td></tr><tr><td>element.offsetTop</td><td>返回元素的垂直偏移位置</td></tr><tr><td>element.offsetParent</td><td>返回元素的偏移容器</td></tr><tr><td>element.scrollWidth</td><td>返回元素的整体宽度</td></tr><tr><td>element.scrollHeight</td><td>返回元素的整体高度</td></tr><tr><td>element.scrollLeft</td><td>返回元素左边缘与视图之间的距离</td></tr><tr><td>element.scrollTop</td><td>返回元素上边缘与视图之间的距离</td></tr><tr><td>element.childNodes</td><td>范湖元素子节点的 NodeList 。</td></tr><tr><td>nodelist.item()</td><td>返回 NodeList 中位于指定下标的节点</td></tr><tr><td>nodelist.length</td><td>返回 NodeList 中的节点数</td></tr></tbody></table>

<h3>元素样式尺寸</h3>
1. window.getComputedStyle(elem)
获取elem所有应用了css后的属性值。返回一个实时的 CSSStyleDeclaration 对象。
2. elem.getBoundingClientRect()
返回元素的大小以及相对于视口的位置。返回一个DOMRect对象。包括元素的 left right top bottom width height x y 属性值。

![](/img/dom.gif)
