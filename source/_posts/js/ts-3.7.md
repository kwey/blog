---
title: TypeScript 3.7、TypeScript 3.8 Beta
tags: 'typescript'
categories: 'Javascript'
top_img: '/img/js.jpg'
cover: '/img/js.jpg'
---


原文链接： <a href="https://link.zhihu.com/?target=http%3A//www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html" target="_blank" rel="nofollow noreferrer">TypeScript 3.7</a>
        
        
        
<a href="https://zhuanlan.zhihu.com/p/90871356?from_voters_page=true" target="_blank">翻译地址</a>

<a href="https://cloud.tencent.com/developer/article/1575422" target="_blank">TypeScript 3.8 Beta</a>


## <ul><li>1、可选链</li></ul>## <ul><li>2、空值联合</li></ul>
    <a href="https://www.webq.top/article/926" target="_blank" style="font-size: 14px;">参照上文：es2020</a>

## <ul><li>3、断言函数</li></ul>    
<span style="font-weight: normal;">      <span style="font-size: large;">它们是一些特殊函数的集合。当一些非预期的事情发生的时候它们将抛出 error。它们被称为断言函数




``` javascript
function isString(val: any): val is string {
    return typeof val === "string";
}

function yell(str: any) {
    if (isString(str)) {
        return str.toUppercase();
    }
    throw "Oops!";
}
```


<ul><li>4、对返回never的函数更好的支持</li></ul>
        


<ul><li>5、（更多）递归类型别名</li></ul>



<ul><li>6、--declaration和--allowJs</li></ul>



<ul><li>
7、useDefineForClassFields标志和declare属性修饰符</li></ul>

<ul><li>8、基于项目引用的免构建编辑</li></ul>

</li><li>9、未调用函数检查</li></ul>

<ul><li>10、TypeScript 文件中的// @ts-nocheck</li></ul>

<ul><li>11、关于分号的格式化选项</li></ul>

<ul><li>12、3.7 中破坏性的改动</li><ul><li>DOM 类型库的更新</li><li>类字段</li><li>函数存在性检查</li><li>本地和导入的类型声明发生冲突</li><li>API 变动</li></ul></ul>