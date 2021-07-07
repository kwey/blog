---
title: 弹幕优化历程
type: "code"
---

一：

<!-- ## [性能指标制定](http://bi.bilibili.co/page/overview)

![](/hide/bi.png) -->


## [计算机的核心 - CPU和GPU](https://www.bilibili.com/video/BV13y4y1z7HP)
![](/hide/cpu.png)
![](/hide/gpu.png)
> 绿色的是计算单元，橙红色的是存储单元，橙黄色的是控制单元。

> CPU是通用计算，而GPU是专用计算

### 显卡
> 由GPU、显存、电路板，还有BIOS固件组成
1: 独立显卡主要由GPU、显存和接口电路构成
2: 集成显卡没有独立显存而是使用主板上的内存


> DRAM 存储器（cpu/gpu 与存储器是两个不同的芯片）
1：主存储器————内存
2：辅助存储器

> 速度来讲，就是：Register > Cache > 内存 > 硬盘。越上层，速度就越快、价格越高、容量越低。

### CPU
> 中央处理器（Central Processing Unit）是一块超大规模的集成电路，是一台计算机的运算核心和控制核心。它的功能主要是解释计算机指令以及处理计算机软件中的数据

串行地一件接着一件处理交给它的任务
在CPU上快速在多个任务间切换，对于使用者来说，就像并发（Concurrent）地执行了多个任务一样

![](/hide/cpu0.jpeg)
![](/hide/cpu1.jpeg)

### GPU
NVIDIA公司在1999年发布 Geforce256图形处理芯片时首先提出GPU的概念。从此 NVIDIA显卡的芯就用GPU来称呼。GPU使显卡减少了对CPU的依赖，并进行部分原本CPU的工作，尤其是在3D图形处理时。

> GPU（Graphics Processing Unit）是图形处理器又称显示核心、视觉处理器、显示芯片，是一种 专门在个人电脑、工作站、游戏机和一些移动设备上运行图像运算工作的微处理器。

GPGPU（General-Purpose computing on Graphics Processing Units）即图形处理单元上的通 用计算，利用处理图形任务的图形处理器来计算原本由中央处理器（CPU）处理的通用计算任务。





### 英伟达Turing GPU架构
TU102 GPU包含6个图像处理集群（GPC）、36个纹理处理集群（TPC）和72个流式多元处理器（SM）。

每个GPC均包含一个专用的光栅化引擎和6个TPC，且每个TPC均包含两个SM。

每个SM包含64个CUDA核心、8个Tensor核心、1个256KB寄存器堆、4个纹理单元以及96KB的L1或共享内存，且我们可根据计算或图形工作负载将这些内存设置为不同容量。每个SM中的全新RT核心处理引擎负责执行光线追踪加速。
![](/hide/tur.webp)
![](/hide/gpu0.png)

### GPU 图形渲染流水线
![](/hide/gpu1.png)

> 1: 顶点着色器（Vertex Shader）
该阶段的输入是 顶点数据（Vertex Data） 数据，比如以数组的形式传递 3 个 3D 坐标用来表示一个三角形。顶点数据是一系列顶点的集合。顶点着色器主要的目的是把 3D 坐标转为另一种 3D 坐标，同时顶点着色器可以对顶点属性进行一些基本处理。


>2: 形状装配（Shape Assembly），又称 图元装配
该阶段将顶点着色器输出的所有顶点作为输入，并将所有的点装配成指定图元的形状。图中则是一个三角形。图元（Primitive） 用于表示如何渲染顶点数据，如：点、线、三角形。

>3: 几何着色器（Geometry Shader）
该阶段把图元形式的一系列顶点的集合作为输入，它可以通过产生新顶点构造出新的（或是其它的）图元来生成其他形状。例子中，它生成了另一个三角形。

>4: 光栅化（Rasterization）
该阶段会把图元映射为最终屏幕上相应的像素，生成片段。片段（Fragment） 是渲染一个像素所需要的所有数据。

>5: 片段着色器（Fragment Shader）
该阶段首先会对输入的片段进行 裁切（Clipping）。裁切会丢弃超出视图以外的所有像素，用来提升执行效率。

>6: 测试与混合（Tests and Blending）
该阶段会检测片段的对应的深度值（z 坐标），判断这个像素位于其它物体的前面还是后面，决定是否应该丢弃。此外，该阶段还会检查 alpha 值（ alpha 值定义了一个物体的透明度），从而对物体进行混合。因此，即使在片段着色器中计算出来了一个像素输出的颜色，在渲染多个三角形的时候最后的像素颜色也可能完全不同。

> GPU 采用如下公式进行计算，并得出最后的颜色。

最终的颜色值就是 S（上面像素） 的颜色 + D（下面像素） 的颜色 * （1 - S（上面像素） 颜色的透明度）。

R = S + D * (1 - Sa)



## 浏览器渲染原理
> 进程（Process）
系统进行资源分配和调度的基本单位，是操作系统结构的继承

>线程（Thread）
进程中的实际运作单位，是程序执行的最小单位


### 以基于Blink内核的Chromium浏览器为例
![](/hide/bro.jpg)
> Browser进程：这是浏览器的主进程，负责浏览器界面的显示、各个页面的管理。每次我们打开浏览器，都会启动一个Browser进程，结束该进程就会关闭我们的浏览器。

浏览器进程有很多负责不同工作的线程（worker thread）

1: 其中包括绘制浏览器顶部按钮和导航栏输入框等组件的UI线程（UI thread）

2: 管理网络请求的网络线程（network thread）

3: 控制文件读写的存储线程（storage thread）等。

当在导航栏里面输入一个URL的时候，其实就是UI线程在处理你的输入。
1： UI线程（UI thread）首先会询问： 输入的字符串是一些搜索的关键词（search query）还是一个URL地址
![](/hide/bro2.jpg)

2： UI线程会叫网络线程（network thread）初始化一个网络请求来获取站点的内容

3：网络线程在收到HTTP响应的主体（payload）流（stream）时，在必要的情况下它会先检查一下流的前几个字节以确定响应主体的具体媒体类型（MIME Type）。响应主体的媒体类型一般可以通过HTTP头部的Content-Type来确定，不过Content-Type有时候会缺失或者是错误的，这种情况下浏览器就要进行MIME类型嗅探来确定响应类型了。MIME类型嗅探并不是一件容易的事情，你可以从Chrome的源代码的注释来了解不同浏览器是如何根据不同的Content-Type来判断出主体具体是属于哪个媒体类型的。

如果响应的主体是一个HTML文件，浏览器会将获取的响应数据交给渲染进程（renderer process）来进行下一步的工作。如果拿到的响应数据是一个压缩文件（zip file）或者其他类型的文件，响应数据就会交给下载管理器（download manager）来处理。

网络线程在把内容交给渲染进程之前还会对内容做SafeBrowsing检查。如果请求的域名或者响应的内容和某个已知的病毒网站相匹配，网络线程会给用户展示一个警告的页面。除此之外，网络线程还会做CORB（Cross Origin Read Blocking）检查来确定那些敏感的跨站数据不会被发送至渲染进程。

4：寻找一个渲染进程（renderer process）

在网络线程做完所有的检查后并且能够确定浏览器应该导航到该请求的站点，它就会告诉UI线程所有的数据都已经被准备好了。UI线程在收到网络线程的确认后会为这个网站寻找一个渲染进程（renderer process）来渲染界面。

> 在第二步中当UI线程发送URL链接给网络线程后，它其实已经知晓它们要被导航到哪个站点了，所以在网络线程干活的时候，UI线程会主动地为这个网络请求启动一个渲染线程

![](/hide/bro5.jpg)

5: 提交（commit）导航
数据和渲染进程都已经准备好了，浏览器进程（browser process）会通过IPC告诉渲染进程去提交本次导航（commit navigation）.除此之外浏览器进程还会将刚刚接收到的响应数据流传递给对应的渲染进程让它继续接收到来的HTML数据。一旦浏览器进程收到渲染线程的回复说导航已经被提交了（commit），导航这个过程就结束了，文档的加载阶段（document loading phase）会正式开始。

到了这个时候，导航栏会被更新，安全指示符（security indicator）和站点设置UI（site settings UI）会展示新页面相关的站点信息。当前tab的会话历史（session history）也会被更新

![](/hide/bro4.jpg)

6: 初始加载完成（Initial load complete）
当导航提交完成后，渲染进程开始着手加载资源以及渲染页面。我会在后面的文章中讲述渲染进程渲染页面的具体细节。一旦渲染进程“完成”（finished）渲染，它会通过IPC告知浏览器进程（注意这发生在页面上所有帧（frames）的onload事件都已经被触发了而且对应的处理函数已经执行完成了的时候），然后UI线程就会停止导航栏上旋转的圈圈。

![](/hide/bro3.jpg)

> Renderer进程：这是网页的渲染进程，负责页面的渲染工作，一般来说，一个页面都会对应一个Renderer进程，不过也有例外。

>GPU进程：如果页面启动了硬件加速，浏览器就会开启一个GPU进程，但是最多只能有一个，当且仅当GPU硬件加速打开的时候才会被创建。
负责独立于其它进程的GPU任务。它之所以被独立为一个进程是因为它要处理来自于不同tab的渲染请求并把它在同一个界面上画出来。

> ...

![](/hide/bro1.jpg)


### [浏览器渲染的几个名词](https://juejin.cn/post/6844903959425974280)

在概念上有四种并行树结构，它们的渲染目的略有不同：

> 1: DOM树，这是我们的基本模型

>2: RenderObject树，它与DOM树的可见节点有1:1的映射。RenderObjects知道如何绘制相应的DOM节点。

> 3: RenderLayer树，由映射到RenderObject树上RenderObject的RenderLayer组成。映射是多对一的，因为每个RenderObject要么与自己的RenderLayer关联，要么与拥有RenderLayer的第一个祖先的RenderLayer关联。RenderLayer树保留层之间的z顺序。

> 4: GraphicsLayer树，将GraphicsLayer映射为一对多renderlayer

![](/hide/tree.png)

> 注： webkit 内核里面称为 RenderObject、RenderLayer
     Blink 里面称为 LayoutObject、PaintLayer

#### 布局对象（LayoutObject）
> 一个 DOM 节点对应了一个布局对象

#### 绘制层（PaintLayer）
>根据层叠上下文，不同坐标空间的的布局对象将形成多个渲染层，以体现它们的层叠关系，对于满足形成层叠上下文条件的布局对象，浏览器会自动为其创建新的渲染层

1：DOM树的Document节点对应的RenderView节点
2：DOM树中Document节点的子女节点，也就是HTML节点对应的RenderBlock节点
3：显式指定CSS位置的节点（position为absolute或者fixed）
4：具有透明效果的节点
5：具有CSS 3D属性的节点
6：使用Canvas元素或者Video元素的节点

#### 图形层（GraphicsLayer）
> GraphicsLayer 其实是一个负责生成最终准备呈现的内容图形的层模型，它拥有一个图形上下文（GraphicsContext），GraphicsContext 会负责输出该层的位图

每个RenderLayer要么有自己的GraphicsLayer（如果是合成层），要么使用它的第一个祖先的GraphicsLayer。这类似于RenderObject与RenderLayers的关系。

每个GraphicsLayer都有一个GraphicsContext供相关的renderlayer绘制。在随后的合成过程中，合成器最终负责将GraphicsContexts的位图输出合并成最终的屏幕图像。

#### 合成层（CompositingLayer）
> 满足某些特殊条件的渲染层，会被浏览器自动提升为合成层。合成层拥有单独的 GraphicsLayer，而其他不是合成层的渲染层，则和其第一个拥有 GraphicsLayer 的父层共用一个


### 浏览器的渲染过程
webkit渲染引擎流程
![](/hide/webkit.png)


#### Parse HTML
1: DOM树的建立
![](/hide/html.jpg)
2: CSSOM树的建立
![](/hide/css.jpg)

#### Layout阶段：
> 经历Parse HTML之后得到DOM与CSSOM，计算每个元素的样式，得到一颗Layout tree，接着计算每个元素在页面中的尺寸与位置。前者称为样式计算，后者称为Layout计算。
针对输入的DOM与CSS树，计算样式，输出Layout tree、PaintLayer tree并计算每个节点所对应的尺寸和位置。

##### 样式计算
浏览器会遍历DOM中每个元素，从CSSOM中查找该元素匹配的样式定义，接着进行CSS选择器优先级排序，得到该元素最终计算后的样式，以类ComputedStyle体现

##### 创建LayoutObject
以类Element、类ComputedStyle为参数，来实现针对某个DOM元素及其样式创建什么样的LayoutObject

浏览器在构建Layout tree的同时，还会同时生成另外一棵树：PainterLayer tree，它用来记录元素的渲染顺序；

##### Layout计算
根据CSS：box model遍历并计算每个节点的尺寸位置，这些信息保存在类LayoutBox的实例变量frame_rect_中

#### Paint阶段
> 记录绘制操作

例如：生成一个DI，记录在屏幕的什么位置绘制一个红色的矩形（每个layout object 对应多个DI，背景，前景（文本）、轮廓...
![](/hide/paint.png)



举例：一个页面中有个元素div，开发者给它设置了CSS动画（围绕Z轴旋转），页面内其它元素保持不变。

> 1: 动画操作放到另外一个线程(称为Composite线程)去做；
2: JS线程绘制每个节点的逻辑实现，并不是立即的真正绘制，而是仅仅记录绘制的操作以及参数（称为draw commands或者draw records）；
3: 当另外一个线程真正处理动画时，根据位置判断动画节点是否与其它节点有重叠，如果有则重新绘制相关节点；

> PaintLayer和PaintLayerStackingNode是一个CSS概念产生的类，为了更好的区分代码，并与后面的Composite阶段桥接起来， 浏览器创建了一个新的类GraphicsLayer(简称：GL)，并形成一颗新的树GraphicsLayer tree；

GraphicsLayer与PaintLayer是一对多的关系，即：一个或多个PainterLayer在一个GraphicsLayer上绘制。

> 拥有部分动画的DOM元素，对应的PaintLayer对象会创建一个GraphicsLayer对象;

## 优化对象

![](/hide/per1.png)
![](/hide/per2.png)

## 上代码

1：减少做碰撞检测的弹幕条数（按照行 从顶部往下排）
2: 优化双重遍历,以最小的代价找到要进行碰撞的弹幕

```javascript

    setY(txt: IRenderExtInterface, index: number) {
        let top = 0;
        let height = txt.height;
        const { pool, border, attr, stime } = txt.textData;
        let bottom = top + height;

        let floor = this.lists[index];

        // 设置y坐标
        const setY = () => {
            txt.setY(top, index);
            floor.cinsert(txt, this.sortFn);
        };

        if (!floor) {
            this.lists[index] = new BinaryArray<IRenderExtInterface>();
            floor = this.lists[index];

            setY();
            return;
        }

        const area = (txt.vDistance * this.config.danmakuArea) / 100;

        // 弹幕区域控制： 字幕、发送弹幕、高赞弹幕，不过区域控制
        const mustShow = pool >= 1 || border || attr === 2;

        const vDistance = (this.config.preventShade ? 0.85 : 1) * txt.vDistance;

        let space: IRenderExtInterface;
        for (let i = 0; i < floor.length; i++) {
            space = floor[i];
            if (bottom > vDistance) {
                break;
            }
            // 在当前弹幕前面的行做跳过处理
            if (space.bottom <= top) {
                continue;
            }

            // :前一行为空
            if (space.y > bottom) {
                setY();
                return;
            }

            if (space.y <= bottom && space.bottom >= top) {
                if (this.scroll) {
                    if (txt.x > space._x + space.width && txt.middle > space.end) {
                        if (bottom <= space.bottom) {
                            // 判断后面是否还有弹幕要做碰撞检测
                            if (this.hasDm(i, top, bottom, floor)) {
                                continue;
                            }
                            setY();
                            return;
                        } else {
                            continue;
                        }
                    }
                } else {
                    if (stime > space.end) {
                        if (bottom <= space.bottom) {
                            setY();
                            return;
                        } else {
                            continue;
                        }
                    }
                }
            }

            top = space.bottom + 1;
            bottom = top + height;

            if (area && !mustShow) {
                if (bottom > area) {
                    txt.rest = -1;
                    return;
                }
            }
        }

        if (bottom <= vDistance) {
            setY();
            return;
        }

        if (area && !mustShow) {
            txt.rest = -1;
        } else {
            this.setY(txt, index + 1);
        }
    }


```

```javascript
/**
* 判断后面是否还有弹幕要做碰撞检测
*/
hasDm(i: number, top: number, bottom: number, floor: BinaryArray<IRenderExtInterface>) {
    let space: IRenderExtInterface;
    for (let j = i; j < floor.length; j++) {
        space = floor[j];
        // 在当前弹幕前面的行做跳过处理
        if (space.bottom < top) {
            continue;
        }
        // 在当前弹幕后面的行直接退出循环
        if (space.y > bottom) {
            break;
        }
        return true;
    }
    return false;
}
```

3： 弹幕宽高通过计算获取，

```javascript
reset(dom: HTMLElement, style: string) {
    if (!dom) {
        Word.sizeScale = undefined;
        return;
    }
    if (!Word.fragment) {
        Word.fragment = document.createElement('div');
        for (const key in Word.sizeTest) {
            const test: ISizeTestVal = Word.sizeTest[key];
            test.ele = document.createElement('span');
            test.ele.textContent = test.val;
            test.ele.style.cssText = `margin:0;padding:0;line-height:1;font-size:20px;font-family:inherit;font-weight:inherit;`;
            Word.fragment.appendChild(test.ele);
        }
    }
    Word.fragment.style.cssText = 'opacity:0;pointer-events:none;' + style;
    dom.appendChild(Word.fragment);
    Word.sizeScale = {} as ISizeScale;
    for (const key in Word.sizeTest) {
        const test: ISizeTestVal = Word.sizeTest[key];
        Word.sizeScale[key] = test.ele!.getBoundingClientRect().width / test.def / 20;
    }
    dom.removeChild(Word.fragment);
}
getLen(str: string) {
    if (!str || !Word.sizeScale) {
        return 0;
    }
    let code: number;
    let len = 0;
    const full = Word.sizeScale.fullAngle;
    const falf = full / 2;

    for (let i = 0; i < str.length; i++) {
        code = str.charCodeAt(i);
        // ascii 码 阿拉伯数字
        if (code >= 32 && code <= 64) {
            len += ascii[code] * Word.sizeScale.number;
            continue;
        }

        // 一般标点符号 ["2000","206F"]  [8192, 8303]
        // 下标及上标 ['2070', '209F']  [8304, 8351]
        // 货币符号 ['20A0', '20CF']  [8352, 8399]
        // 符号用组合附加符号 ["20D0","20FF"]  [8400, 8447]
        // 似字母符号 ['2100', '214F']  [8448, 8527]
        // 数字形式  ['2150', '218F'] [8528, 8591]
        // 箭头符号  ['2190', '21FF'] [8592, 8703]
        // 数学运算符号  ["2200","22FF"] [8704, 8959]
        // 混合专门符号  ["2300","23FF"] [8960,9215]
        // 控制图像  ["2400","243F"] [9216,9279]
        // 光学字符识别  ["2440","245F"] [9280,9311]
        // 括号字母数字 ["2460","24FF"][9312,9471]
        // 制表符 ["2500","257F"][9472,9599]
        // 区块组件 ["2580","259F"] [9600,9631]
        // 几何形状 ["25A0","25FF"] [9632,9727]
        // 混合什锦符号 ["2600","26FF"] [9728,9983]
        // 什锦符号 ["2700","27BF"] [9984,10175]
        if (code >= 8192 && code <= 10175) {
            len += full;
            continue;
        }

        // 中日韩部首补充 ["2E80","2EFF"][11904,12031]
        // 康熙部首 ["2F00","2FDF"][12032,12255]
        // 汉字结构描述字符 ["2FF0","2FFF"] [12272,12287]
        // 中日韩符号和标点 ["3000","303F"][12288,12351]
        // 平假名 ["3040","309F"][12352,12447]
        // 片假名 ["30A0","30FF"][12448,12543]
        // 注音符号 ["3100","312F"][12544,12591]
        // 谚文兼容字母 ["3130","318F"] [12592,12687]
        //  汉文标注号 ["3190","319F"][12688,12703]
        //  注音符号扩充 ["31A0","31BF"][12704,12735]
        //  中日韩笔画部件  ["31C0","31EF"][12736,12783]
        //  片假名音标扩充  ["31F0","31FF"][12784,12799]
        //  中日韩括号字母及月份 ["3200","32FF"][12800,13055]
        //  中日韩兼容字符 ["3300","33FF"][13056,13311]
        //  中日韩统一表意文字扩充A ["3400","4DBF"][13312,19903]
        //  易经六十四卦象 ["4DC0","4DFF"][19904,19967]
        //  中日韩统一表意文字 ["4E00","9FFF"] [19968,40959]
        if (code >= 11904 && code <= 40959) {
            len += full;
            continue;
        }

        //  易经六十四卦象 ["4DC0","4DFF"][19904,19967]
        // if (code >= 19904 && code <= 19967) {
        //     len += 0.875
        //     continue;
        // }

        //  谚文音节 ["AC00","D7AF"][44032,55215]
        // if (code >= 44032 && code <= 55215) {
        //     len += 0.875
        //     continue;
        // }
        //  中日韩兼容表意文字 ["F900","FAFF"][63744,64255]
        if (code >= 63744 && code <= 64255) {
            len += full;
            continue;
        }

        //  竖式标点 ["FE10","FE1F"][65040,65055]
        if (code >= 65040 && code <= 65055) {
            len += full;
            continue;
        }
        //  中日韩相容形式 ["FE30","FE4F"][65072,65103]
        //  小写变体 ["FE50","FE6F"][65104,65135]
        if (code >= 65072 && code <= 65103) {
            len += full;
            continue;
        }
        // 半角及全角字符 ["FF00","FFEF"] [65280,65519]
        // 全角符号
        if (code >= 65280 && code <= 65376) {
            len += full;
            continue;
        }
        if (code >= 65471 && code <= 65473) {
            len += full;
            continue;
        }
        if (code >= 65480 && code <= 65481) {
            len += full;
            continue;
        }
        if (code >= 65488 && code <= 65489) {
            len += full;
            continue;
        }
        if (code >= 65496 && code <= 65497) {
            len += full;
            continue;
        }
        if (code >= 65501 && code <= 65512) {
            len += full;
            continue;
        }
        if (code === 65519) {
            len += full;
            continue;
        }
        // 半角符号
        if (code >= 65377 && code <= 65470) {
            len += falf;
            continue;
        }
        if (code >= 65474 && code <= 65479) {
            len += falf;
            continue;
        }
        if (code >= 65482 && code <= 65487) {
            len += falf;
            continue;
        }
        if (code >= 65490 && code <= 65495) {
            len += falf;
            continue;
        }
        if (code >= 65498 && code <= 65500) {
            len += falf;
            continue;
        }
        if (code >= 65513 && code <= 65518) {
            len += falf;
            continue;
        }

        return 0;
    }
    return len;
}
```

## 下步计划

优化 实时fps

![](/hide/cwnd.png)


## 存在问题

1：弹幕 css3 动画会到主线程去执行



## 参考文章：

[5分钟让你看懂CPU的结构和工作原理](https://zhuanlan.zhihu.com/p/52396782)1  
[最通俗易懂的显卡参数讲解](https://zhuanlan.zhihu.com/p/52184953)

[深入GPU硬件架构及运行机制](https://www.cnblogs.com/timlly/p/11471507.html)
[图形图像渲染原理](http://chuquan.me/2018/08/26/graphics-rending-principle-gpu/)
[Chromium Blink流程 PaintLayer和GraphicsLayer](https://zhuanlan.zhihu.com/p/48515392)

[一文看懂Chrome浏览器工作原理](https://juejin.cn/post/6844904046411644941)
[GPU Accelerated Compositing in Chrome](http://dev.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome)
