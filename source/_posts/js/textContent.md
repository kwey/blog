---
title: Node.textContent
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---

> Node.textContent 属性表示一个节点及其后代的文本内容。


<ul><li>1、如果 element 是 Document，DocumentType 或者 Notation 类型节点，则 textContent 返回 null。如果你要获取整个文档的文本以及CDATA数据，可以使用<a rel="nofollow" href="https://developer.mozilla.org/zh-CN/docs/DOM/document.documentElement" title="DOM/document.documentElement">document.documentElement</a>.textContent。</li></ul><ul><li>2、如果节点是个CDATA片段，注释，ProcessingInstruction节点或一个文本节点，textContent 返回节点内部的文本内容（即 <a href="https://developer.mozilla.org/zh-CN/docs/DOM/Node.nodeValue" title="DOM/Node/NodeValue/Node.nodeValue">nodeValue</a>）。</li></ul><ul><li>3、对于其他节点类型，textContent 将所有子节点的 textContent 合并后返回，除了注释、ProcessingInstruction节点。如果该节点没有子节点的话，返回一个空字符串。</li></ul><ul><li>4、在节点上设置 textContent 属性的话，会删除它的所有子节点，并替换为一个具有给定值的文本节点。</li></ul>

## 与innerText的区别

IE引入了<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Node/innerText" title="Node.innerText 是一个表示一个节点及其后代的“渲染”文本内容的属性。">node.innerText</a>。意图类似，但有以下区别：

1、textContent 会获取所有元素的内容，包括script 和style 元素，然而 innerText 不会。

2、innerText 受 CSS 样式的影响，并且不会返回隐藏元素的文本，而textContent会。

3、由于 innerText 受 CSS 样式的影响，它会触发重排（reflow），但textContent 不会。

4、与 extContent 不同的是, 在 IE (对于小于等于 IE11 的版本) 中对 innerText 进行修改， 不仅会移除当前元素的子节点，而且还会永久性地破坏所有后代文本节点（所以不可能再次将节点再次插入到任何其他元素或同一元素中）。
## 与innerHTML的区别
innerHTML 返回 HTML 文本。通常，为了在元素中检索或写入文本，人们使用innerHTML。但是，textContent通常具有更好的性能，因为文本不会被解析为HTML。此外，使用textContent可以防止  <abbr title="cross-site scripting">XSS</abbr> 攻击。

## innerHTML与outerHTML的区别？

<ul><li><span style="font-size: large;">innerHTML 设置或获取位于对象起始和结束标签内的 HTML</li><li><span style="font-size: large;">outerHTML 设置或获取对象及其内容的 HTML 形式</li><li><span style="font-size: large;">innerText 设置或获取位于对象起始和结束标签内的文本</li><li><span style="font-size: large;"><span style="text-decoration-line: line-through;">outerText <span style="color: rgb(194, 79, 74);">是一个非标准的属性。作为一个获得器，它返回与<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Node/innerText" title="Node.innerText 是一个表示一个节点及其后代的“渲染”文本内容的属性。">Node.innerText</a>一致的值。 作为一个设置器，它将删除当前节点并将其替换为给定的文本。</li></ul>


 比如对于这样一个HTML元素：
 content
 。
 <ul><li>innerHTML：内部HTML，content
 ；</li><li>outerHTML：外部HTML，
 content
 ；</li><li>innerText：内部文本，content；</li></ul>
 


