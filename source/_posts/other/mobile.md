---
title: 移动端知识的一些总结
tags: 'mobile'
categories: 'other'
top_img: '/img/git.png'
---


# 一.css部分
1.meta标签
``` html
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no"/>
```
移动端加上这个标签才是真正的自适应，不加的话，假如你把一个980px宽度(手机端常规是980)的PC网页 放在手机上显示，倒也能正常显示不出现滚动条，不过是移动设备对页面 做了缩小优化，所以字体等都相应缩小了 （980px是相对于手机像素的，我的是超过1000px多一些就出现滚动条了，这个没具体研究）。
关于 initial-scale=1 ，这个参照iphone5的尺寸320568，如果你页面按照6401136做的话，scale就设为0.5
``` html
<!-- IOS中safari允许全屏浏览 -->
<meta content="yes" name="apple-mobile-web-app-capable">   
<!-- IOS中Safari顶端状态条样式 -->
<meta content="black" name="apple-mobile-web-app-status-bar-style">  
<!-- 忽略将数字变为电话号码 -->
<meta content="telephone=no" name="format-detection">  
<!-- 忽略识别email -->
<meta content="email=no" name="format-detection">   
```   
2.图片尺寸 做全屏显示的图片时，一般为了兼容大部分的手机，图片尺寸一般设为 640*960（我是觉得这个尺寸好，也看不少的图片也是这个尺寸，视情况而定）

3.去除 webkit的滚动条
```
element::-webkit-scrollbar{

    display: none;

}
```

如果要去除全部的，就把 element去掉 同时这个属性可让在 div里的滚动如丝般顺滑： -webkit-overflow-scrolling : touch;

4.去除 button在 ios上的默认样式
```css
-webkit-appearance: none;

border-radius: 0
```

5. placeholder元素样式的修改

```css
input::-webkit-input-placeholder{color:red;}

input:focus::-webkit-input-placeholder{color:green;}
```

6.不想让按钮 touch时有蓝色的边框或半透明灰色遮罩（根据系统而定）
```css
-webkit-tap-highlight-color:rgba(0,0,0,0);
```

7.在移动端做动画效果的话，如果通过改变绝对定位的 top，或者 margin的话做出来的效果很差，很不流畅，而使用 css3的 transition、 transform或者 animation的效果将会非常棒（这一方面 IOS比 android又要好不少）。 如果用 **3d（translate3d）来实现动画，会开启 <a href="https://cloud.tencent.com/product/gpu" target="_blank">GPU</a>加速，动画会更加流畅，但硬件配置差的安卓用起来会耗很多性能

8.使用图片时，会发现图片下总是有大概 4px的空白，（原因据说图片是inline，触发baseline什么的。。。）常用解决方法有

```css
img{display:block}；

img{vertical-align:top}
```

也可取其他几个值，视情况而定
9.关于使用弹性盒子 box布局 暂时有三种方案：
```css
display:-webkit-box; // 早期的版本

display:-webkit-box-flex; // 过渡版本

display:-webkit-flex; // 最新的版本
```

我暂时使用的是 display:-webkit-box;暂时只有这个的兼容性最好， flex是最好用，还可以如 float一样排多个元素时自动换行，只是兼容性还太差，（即使是第一种，在火狐下兼容性也不是很好） 对于第一种常见用法是：
```html
<ul>

<li>1</li>

<li>2</li>

<li>3</li>

</ul>

.box {

display: -webkit-box;

　　-webkit-box-align: center; // 设置里面的元素垂直居中

　　-webkit-box-pack: center; // 设置水平居中
}

.box .box_flex1 {

　　box-flex: 1;

　　-webkit-box-flex: 1;

　　-moz-box-flex: 1;

　　display: block;

　　width: 0;
}
```

要想三个 li元素平分 box的宽度， width:0是关键（在这个坑里陷了好久，直到偶然的一次设置了 width：0突然就好了，后来在天猫上也看到了同样的设置 width:0） 

10.使用 a 标签的话，尽量让 a 标签 block ，尽量让用户可点击区域最大化 

11.对两个 div使用了 transform之后， div下的 z-index有时就会失效，我遇到过，但没去认真探究，只是把 z-index提高就好了 

12.在 iOS中，当你点击比如 input 准备输入时，虚拟键盘弹出，整个视窗的 高度 就会变为 减去键盘 的高度，加入你在底部有 fixed的元素比如 btn，这个元素就会跑上来，一般都不会太美观。我是当 focus时就把它设为 absolute，视情况而定，也有比如显示一个新的层，将含有 fixed按钮的那一层隐藏的情况等等。 另外一种情况（一般在页面内容很少时 iphone5及以上常发生），当输入框弹出时 fixed元素挤到输入框上，当输入框消失时， fixed元素并没有随着输入框的消失而回到底部，这是因为整个视窗的高度还保持在 减去键盘的 高度，需要手动去触发让视窗高度回到正常，然后试了很多方法都没成功，后来的方案是输入框消失时给页面加隐形的很大的 padding比如 1000px并在 30ms后改为正常

13.禁止用户选中文字 -webkit-user-select:none

14.当把 input设为 width:100%时，有时可能会遇到 input的宽度超出了屏幕，这时可对 input加一个属性 box-sizing:border-box

关于 box-sizing:border-box，此属性是把边框的宽度和 padding包含在盒子的高宽中，假如你设置两个元素 float且各占 50%，又都有 border时，用这个属性就可以完美地让它们能显示在同一行

15.做一个方向箭头比如 “>” 时，可以用一个正方形的 div，设置 border:1px 1px 0 0；然后 rotate(45deg)，这样就省去了一个图片

16. CSS权重： style是 1000， id是 100， class是 10，普通如 li、 div和伪类是 1，通用如 *是 0；

17.使用 rem时，设某个 div比如的 height:3rem;line-height:1.5rem;overflow:hidden;时，在某些 android手机上可能会出现显示不止两行,第三行会显示点头部。 解决：利用 js获取文字 line-height再去设置 div高度 

18.使用 background时， background-position里使用了比如 center left后不能再加具体的数值去定位，比如 center 10px left（暂时兼容性差）

19.使用 rem布局时，由于 webkit支持的最小字体大小是 12px，所以使用 html使用 62.5%实际是 12px，这样很难计算，我的做法是设置成 625%即 100px，然后 1rem就相当于 100px

20.移动端字体使用 font-family: Helvetica,sans-serif;我看这也是天猫使用的

21.在 iphone原生键盘上用 keyup统计字符数时，系统不会自动监控你选择文字的事件，比如打了多个拼音，可能选择的实际文字比输入的字符数多或者少，但无法在用户确定自己的输入时监控到 keyup（其他事件也一样）判断字符数。 改成 input事件就可以了

22.实现毛玻璃效果，透过背景看其他元素模糊，自身元素不模糊。
```css
-webkit-backdrop-filter: saturate(180%) blur(20px);

background: rgba(0,0,0,0.5);
```

这个效果暂时只有 IOS9上的 safari可以。 用 css3的 blur效果的话，是整层元素全部模糊，而透过模糊层看其他的元素不模糊

23.如果需要展示小于 12px的文字，用 transform:scale(%);它将元素缩小，但本身应该占的空间并不会变小，所以比如要元素居左对齐的话，还需设置 translateX 24.在移动端对 input框使用 disabled属性，会导致元素里面 value值在页面上被隐藏看不见，可以对元素使用 css3效果 pointer-event:none，意思就是此元素对鼠标事件无效

# 二.js部分
1.如果使用 jquery绑定 touch事件的话，获取 touchstart， touchmove的触点坐标用 e.originalEvent.targetTouches[0].pageX，获取 touchend则用 e.originalEvent.changedTouches[0].pageX 2.利用 style获取获取 transform的 rotate值<li>parseInt(/rotateX\\((.*?)\\)/.exec(getALL.style.webkitTransform)[1])</li><li></li><li>如果页面一开始没有`style`值，`rotate`是写在`CSS`里的，需要用到`getComputedStyle`，具体请看[这里][5]  </li>

3.有些版本的 iphone4中， audio和 video默认播放事件不会触发，比如使用 window.onload或计时器等都不能触发播放，必须用 JS写事件让用户手动点击触发才会开始播放，比如
```javascript
$(document).one('touchstart',function(){
    audio.play();
})
```

4.想要在 touchmove:function(e,参数一)加一个参数，结果直接使用 e.preventDefault()就会 e 报错，处理方法为
```javascript
touchmove:function(e,参数一){

　　var e=arguments[0]

　　e.preventDefault()

}
```

5.移动端的一些小页面可能没必要用到 zepto.js这些工具库，就可以使用一些新一点的 api，比如选取元素用 querySelector(".class #id")和 querySelectorAll(".class element"),操作 class可以用 classList

6.点击一个元素时，使用 touchstart会立即触发，而使用 click则用有大概 0.3s的延迟 想模拟一个立即触发的点击事件有两种方法， fastclick.js和 zepto.js里的 tap事件。

不过 zepto的 tap事件有一个事件穿透的问题。假如你 tap一个弹出层元素（这个元素遮罩了一个 a标签），这个元素立即消失，这样由于上述的 0.3s延迟 tap事件就会传递给 a标签成 click事件造成a标签跳转。 以上面的例子来说，解决方案视情况而定： 

① tap时让遮罩渐隐消失，这个过程超过 400ms就不会穿透到下一层去了 

② 在 touchend事件回调中加入 preventDefault, 并在下一层中加上 pointer-events:none。（这个没用过） 

③ 有的时候比如弹出一个 iphone上滑动出来的层，点击黑色半透明区域弹出层消失，这种可以在黑色区域绑定 touchend也是和 tap差不多的效果

7.当弹窗出现时，想禁止屏幕的滑动，给那个遮罩层添加 touchmove事件即可，用 e.preventDefault()会阻止的 scroll， click等事件，消失时再 off掉，

```javascript
$(".body_cover").on("touchmove", function(e) {
　　e.preventDefault();
});
```

8.使用 input file上传文件时，可以指定接受的类型， accept="image/png,image/jpeg,image/gif" ，同时在 android上默认不能使用相机，可以加 capture="camera" 同时，由于原生的样式不好看，可以通过设置input的display:none,然后使用id.click()去触发input元素的点击。

9.给元素加:active伪类时，如果要求高，兼容性好的话，就得用js监控touchstart然后加相应的class
```javascript
$(document).on("touchstart", function(e) {
    
    var target = $(e.target);

    if (target.hasClass("is_hover")) {

        target.addClass("hover_css");

    }

});
```

三、微信部分
1.判断是否来自微信浏览器
```javascript
function isFromWeiXin() {
    
    var ua = navigator.userAgent.toLowerCase();

    if (ua.match(/MicroMessenger/i) == "micromessenger") {

        return true;

    }

    return false;
}    
```

2.判断手机的类型
```javascript
var user="";
if (/android/i.test(navigator.userAgent)){
    //  android
    user="1";
}
if (/ipad|iphone|mac/i.test(navigator.userAgent)){
    //  ios
    user="0";
}
```
3.如果在网页里嵌套一个 iframe， src为其他的网址等，当在微信浏览器打开时，如果 iframe里的页面过大，则 iframe的 src不能加载（具体多大不知道，只是遇到过）。

4.微信 jssdk里预览图片接口，图片的 url不能填 base64编码， ios上会很卡，估摸着微信得卡个 5秒左右才打得开，而 android上压跟就卡住或者一直卡在读取图片界面

5.微信当在输入框里输入消息时，会提示警示框不要输入qq密码等，这个可以在公众平台的功能设置的业务域名设置

6.微信里假如页面一使用 ajax获取数据，当你进入下一页面再按返回键返回页面一的时候，有些情况不会去请求 ajax数据，会使用缓存，然而 ajax来的数据又并没有存在缓存里。要设置 cache:false（iphone、android的某些手机都可能出现）

7.在微信 js config的时候，如果 URL的参数有如 ?a={"param": "1"},会导致签名失败。给参数用 encodeURIComponent编码后再传过去也是失败！研究了好久。（后台代码没做任何修改，只在我前端修改代码）。所以最后还是使用了 ?param=1这样的格式


<a href="https://segmentfault.com/a/1190000003908191" target="_blank">原文地址</a>