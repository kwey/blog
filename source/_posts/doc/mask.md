---
title: CSS Masking Module Level 1
top_img: '/img/hy.jpeg'
cover: '/img/hy.jpeg'
---

### CSS Masking Module Level 1
----W3C候选人推荐，2014年8月26日

此版本：

<a href="http://www.w3.org/TR/2014/CR-css-masking-1-20140826/" target="_blank" rel="noopener noreferrer">http://www.w3.org/TR/2014/CR-css-masking-1-20140826/</a>

最新版本：

<a href="http://www.w3.org/TR/css-masking-1" target="_blank" rel="noopener noreferrer">http://www.w3.org/TR/css-masking-1</a>

编辑草案：

<a href="http://dev.w3.org/fxtf/css-masking-1/" target="_blank" rel="noopener noreferrer">http://dev.w3.org/fxtf/css-masking-1/</a>

先前版本：

<a href="http://www.w3.org/TR/2014/WD-css-masking-1 -20140522" target="_blank" rel="noopener noreferrer">http://www.w3.org/TR/2014/WD-css-masking-1 -20140522</a>

反馈：

public-fx@w3.org with subject line “[css-masking] … message topic …”(archives)

测试套件：

<a href="http://test.csswg.org/suites/css-masking/nightly-unstable/" target="_blank" rel="noopener noreferrer">http://test.csswg.org/suites/css-masking/nightly-unstable/</a>

编辑：

Dirk Schulze（Adobe Systems Inc.）

Brian Birtles（Mozilla日本）

Tab Atkins Jr.（Google ）

翻译： 雪人

Copyright © 2014 W3C® (MIT, ERCIM, Keio, Beihang), All Rights Reserved. W3C liability, trademark and document use rules apply.
<h3>摘要</h3>

CSS Masking提供了两种方法来部分或完全隐藏可视元素的部分：掩蔽和剪切。 

Masking描述了如何使用另一个图形元素或图像作为亮度或alpha蒙版。通常，通过CSS或SVG渲染元素可以在概念上描述为将元素（包括其子元素）绘制到缓冲区中，然后将该缓冲区合成到元素的父元素中。亮度和alpha蒙版在合成阶段之前影响此缓冲区的透明度。

Clipping描述了视觉元素的可见区域。可以通过使用某些SVG图形元素或基本形状来描述该区域。此区域之外的任何内容都不会呈现。

CSS是一种语言，用于描述屏幕，纸上，演讲等结构化文档（如HTML和XML）的呈现。
<h3>本文档的状态</h3>

本节描述了本文档的状态。出版物。其他文件可能会取代本文件。有关当前W3C出版物的列表以及该技术报告的最新修订版，请参见W3C技术报告索引，网址为<a href="http://www.w3.org/TR/" target="_blank" rel="noopener noreferrer">http://www.w3.org/TR/</a>

本文档由CSS工作组（样式活动的一部分）和SVG工作组（图形活动的一部分）作为候选推荐书制作。

本文档考虑了前一次呼叫期间的评论;请参阅最近最后一次通话的评论处理和之前的通话。还提供了初步实施报告。没有风险的功能。

候选推荐书是一份经过广泛审查并准备实施的文件。 W3C鼓励每个人实施此规范并将评论返回到（存档的）公共邮件列表public-fx@w3.org（请参阅说明）。发送电子邮件时，请在主题中加入“css-masking”文本，最好是这样：“[css-masking] ...评论摘要......”

作为候选推荐标准的出版并不意味着W3C会员资格的认可。这是一份草案文件，可能随时被其他文件更新，替换或废弃。除了正在进行的工作之外，引用此文档是不恰当的。 

本文件由根据2004年2月5日W3C专利政策运营的团体制作。 W3C维护任何专利公开（CSS）的公开列表以及与每个组的可交付成果相关的任何专利公开（SVG）的公开列表;这些页面还包括披露专利的说明。具有个人认为包含基本要求的专利的实际知识的个人必须根据W3C专利政策的第6节披露该信息。 

将在候选推荐阶段制定CSS屏蔽模块1级的测试套件和实施报告，该阶段将持续至少6个月，至少持续到2015年2月26日。有关详细信息，请参阅“CR退出标准”部分细节。
<h3 id="a1">1. 简介</h3>

本节不是规范性的。 

此规范定义了两个不同的图形操作，它们完全或部分隐藏了对象的部分：剪切和屏蔽。 
<h4 id="a1-1">1.1. 剪切</h4>

闭合矢量路径，形状或多边形定义了一个所谓的剪切路径。此剪切路径是一个区域（在没有抗锯齿的情况下），允许该区域“内部”的所有内容显示，但外部的所有内容都“被剪掉”，并且不会出现在画布上。

<img src="https://www.w3.org/TR/css-masking-1/images/clipping-path.svg" alt=""/>

clip-path属性可以使用指定的基本形状作为剪切路径，或者引用具有要用作剪切路径的图形元素的clipPath元素。 
<h4 id="a1-2">1.2. Masking </h4>

将蒙版应用于图形对象的效果就好像图形对象将通过蒙版绘制到背景上，从而完全或部分遮盖图形对象的某些部分。

<img src="https://www.w3.org/TR/css-masking-1/images/luminance-mask.svg" alt=""/>

 使用mask-image或mask-border-source属性应用蒙版。 

mask-image属性可以引用mask元素。掩模元素的内容用作掩模。

或者，对于许多简单的用途，mask-image属性可以直接引用要用作mask的图像，从而放弃对显式mask元素的需要。然后可以使用蒙版位置，蒙版大小和其他特征属性，像CSS背景图像一样调整和调整此蒙版的大小和位置。 

mask-border-source属性将一个mask分成9个部分。可以以各种方式对片进行切片，缩放和拉伸以适合掩模边界图像区域的大小。 mask-border属性用作mask-border-source和其他特征属性的简写属性。

mask属性用作所有mask-border和mask-image附属属性的简写属性。
<p class="note">注意：虽然屏蔽为增强的图形效果提供了许多可能性，并且通常提供对内容的“可见部分”的更多控制，但剪切路径可以更好地执行并且基本形状更容易插值。
<h3 id="a2">2. 模块交互</h3>

此规范定义了一组CSS属性，这些属性会影响应用这些属性的元素的可视化呈现。根据[CSS21]中的可视化格式模型确定元素的大小和位置后，将应用这些效果。这些属性的某些值导致创建堆叠上下文。此外，此规范取代了[CSS21]中的Clipping：clip属性部分。

合成模型遵循SVG合成模型[SVG11]：首先，在没有滤镜效果，遮罩，剪裁和不透明度的情况下对元素进行样式设置。然后在临时画布上绘制元素及其后代。在最后一步中，以下效果按顺序应用于元素：滤镜效果[FILTER-EFFECTS]，剪裁，蒙版和不透明度。

此规范允许使用CSS合成和混合[COMPOSITING-1]中定义的Porter Duff合成运算符合成多个遮罩层。

术语对象边界框遵循SVG 1.1 [SVG11]中的定义。
<h3 id="a3">3. Values </h3>

此规范遵循[CSS21]中的CSS属性定义约定。基本形状在CSS形状模块级别1 [CSS-SHAPES]中定义。未在这些规范中定义的值类型在CSS值和单位模块级别3 [CSS3VAL]中定义。 

除了定义中列出的特定于属性的值之外，本规范中定义的所有属性还接受CSS范围的关键字，例如inherit作为其属性值[CSS3VAL]。为了便于阅读，它没有明确重复。 
<h3 id="a4">4. 术语</h3>

本规范中CSS属性和值的定义类似于CSS背景和边框[CSS3BG]中的定义。为避免冗余，本规范依赖于CSS背景和边框的描述和定义。 CSS背景和边框中的以下术语在本规范中具有以下含义：

Term in CSS Masking	----- |-----Term in [CSS3BG]

mask layer image	   ----- |------->   background images

mask painting area	   ----- |------->   background painting area

mask-size	           ----- |------->   background-size

mask-position	       ----- |------->   background-position

mask positioning area  ----- |------->	 background positioning area

mask border image	   ----- |------->   border-image

mask border image area ----- |------->   border image area
<h3 id="a5">5. 剪切路径</h3>

剪切路径限制可以应用绘制的区域，即所谓的剪切区域。从概念上讲，不绘制位于该区域之外的图形的任何部分。这包括应用剪切路径的元素的任何内容，背景，边框，文本修饰，轮廓和可见滚动机制，以及其后代的内容。 

元素的祖先也可以剪辑其内容的一部分（例如，通过它们自己的剪辑或剪辑路径属性和/或如果它们的溢出属性不可见）。渲染的是累积交叉点。 

如果剪辑区域超出了UA文档窗口的边界，则本机操作环境可能会将内容剪切到该窗口。 

剪切路径会影响元素的渲染。它不会影响元素的固有几何形状。剪切元素的几何形状（即通过clip-path属性引用clipPath元素的元素，或引用元素的子元素）必须保持与未剪裁的相同。 

 考虑由应用于祖先的剪切路径剪切的形状：

<'g' clip-path="circle()"><'path' id='shape' d="M0,0 L10,10, L 20,0 z"/></'g'>

 The shape is referenced by a <'use'> element:

<'use' xlink:href="#shape"/>

几何形状不受圆形剪裁的影响路径。 

默认情况下，不能在不可见区域上调度指针事件。例如，尺寸为10px到10px的元素被剪裁为半径为5px的圆将不会在剪切区域外接收点击事件。
<h3 id="a5-1">5.1. 剪切形状：剪辑路径属性</h3>

名称：clip-path 

Value： clip-source | [ basic-shape || geometry-box ] | none

Initial：none 

适用于：所有元素。在SVG中，它适用于容器元素，不包括元素和所有图形元素

Inherited：no 

Media：visual 

Computed value：指定，但值为absolute 

Percentages：指定为

Animatable：as为 [CSS-SHAPES]指定，否则为no

 指定基本形状或引用clipPath元素以创建clip-path。
<h4>----clip-source = url</h4>
<h4>----geometry-box = shape-box | fill-box | stroke-box | view-box</h4>
<h4>basic-shape</h4>

CSS Shapes模块[CSS-SHAPES]中定义的基本形状函数。基本形状使用指定的参考框来调整基本形状的大小和位置。如果未指定引用框，则边框将用作引用框。 
<h4>geometry-box</h4>

如果与组合指定，它将提供的参考框。 

如果由其自身指定，则使用指定框的边，包括任何边角（例如，由border-radius [CSS3BG]定义）作为剪切路径。另请参见“框值的形状”[CSS-SHAPES]。
<h4>fill-box</h4>

使用对象边界框作为参考框。
<h4>stroke-box</h4>

使用笔划边界框作为参考框。 
<h4>view-box</h4>

使用最近的SVG视口作为参考框。 

如果为SVG视口创建元素指定了'viewBox'属性：

----引用框位于由'viewBox'属性建立的坐标系的原点。 

----参考框的尺寸设置为'viewBox'属性的宽度和高度值。 
<h4>none </h4>

没有创建剪切路径。

对于没有关联CSS布局框的SVG元素，内容框，填充框，边框框和边框的使用值是填充框。

对于具有关联CSS布局框的元素，填充框，描边框和视图框的使用值是边框。 

计算值不是none会导致创建堆叠上下文[CSS21]，就像CSS不透明度[CSS3COLOR]对除1以外的值所做的那样。

如果URI引用无效（例如，它指向如果对象不存在或对象不是clipPath元素，则不应用裁剪。

此示例演示如何使用基本形状作为剪切路径。每个空格分隔的长度对代表多边形的一个点。可视化的剪切路径可以在介绍中看到。

clip-path: polygon(15px 99px, 30px 87px, 65px 99px, 85px 55px,

122px 57px, 184px 73px, 198px 105px, 199px 150px,

145px 159px, 155px 139px, 126px 120px, 112px 138px,

80px 128px, 39px 126px, 24px 104px);

.

在此示例中，clip-path属性引用SVG clipPath元素。每个逗号分隔的长度对表示多边形的一个点。对于前面的例子，可以在引言中看到可视化的剪切路径。

clip-path: url("#clip1");

<'clipPath id="clip1">

----<'polygon points="15,99 30,87 65,99 85,55 122,57 184,73 198,105

 ------99,150 145,159 155,139 126,120 112,138 80,128 39,126

-------24,104"/>

<'clipPath'>
<h3 id="a6">6. SVG剪切路径源</h3>
<h3 id="a6-1">6.1. The clipPath element</h3>
<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>clipPath</td>
    </tr>
    <tr>
        <td>Categories：</td>
        <td>无。</td>
    </tr>
    <tr>
        <td>Content model:</td>
        <td>
            
            以下任意数量的元素： 
            
            descriptive — <'desc>, <'title>, <'metadata>
            
            animation — <'animate>, <'animateColor>, <'animateMotion>, <'animateTransform>, <'set>
            
            descriptive — <'desc>, <'title>, <'metadata>
            
            shape — <'circle>, <'ellipse>, <'line>, <'path>, <'polygon>, <'polyline>, <'rect>
            
            <'text>
            
            <'use>
            
            <'script>
        </td>
    </tr>
    <tr>
        <td>Attributes:</td>
        <td>
            
            conditional processing attributes — ‘requiredFeatures’, ‘requiredExtensions’, ‘systemLanguage’
            
            core attributes — ‘id’, ‘xml:base’, ‘xml:lang’, ‘xml:space’
            
            presentation attributes — alignment-baseline, baseline-shift, clip, clip-path, clip-rule, color, color-interpolation, color-interpolation-filters, color-profile, color-rendering, cursor, direction, display, dominant-baseline, enable-background, fill, fill-opacity, fill-rule, filter, flood-color, flood-opacity, font, font-family, font-size, font-size-adjust, font-stretch, font-style, font-variant, font-weight, glyph-orientation-horizontal, glyph-orientation-vertical, image-rendering, kerning, letter-spacing, lighting-color, marker, marker-end, marker-mid, marker-start, mask, opacity, overflow, pointer-events, shape-rendering, stop-color, stop-opacity, stroke, stroke-dasharray, stroke-dashoffset, stroke-linecap, stroke-linejoin, stroke-miterlimit, stroke-opacity, stroke-width, text-anchor, text-decoration, text-rendering, unicode-bidi, visibility, word-spacing, writing-mode
            
            ‘class’
            
            ‘style’
            
            ‘externalResourcesRequired’
            
            ‘transform’
            
            ‘clipPathUnits’
        </td>
    </tr>
    <tr>
        <td>DOM Interfaces:</td>
        <td>SVGClipPathElement</td>
    </tr>
</tbody>
</table>


属性定义：

clipPathUnits =“userSpaceOnUse | objectBoundingBox”

--定义clipPath内容的坐标系。 

--userSpaceOnUse 

----clipPath的内容表示当引用clipPath元素时当前用户坐标系中的值（即，通过clip-path属性引用clipPath元素的元素的用户坐标系）。

--objectBoundingBox 

--坐标系的原点位于剪切路径所适用的元素的边界框的左上角，以及此边界框的相同宽度和高度。用户坐标的大小等同于CSS px单元。 

--如果未指定属性clipPathUnits，则效果就像指定了userSpaceOnUse的值一样。 

--Animatable: yes.

CSS属性从其祖先继承到clipPath元素;属性不会从引用clipPath元素的元素继承。 

clipPath元素永远不会直接呈现;它们的唯一用法是可以使用clip-path属性引用的东西。 display属性不适用于clipPath元素;因此，即使将display属性设置为none以外的值，也不会直接呈现clipPath元素，即使clipPath元素或其任何祖先的display属性设置为none，也可以使用clipPath元素进行引用。 

clipPath元素可以包含元素，元素，基本形状（如）或元素。如果元素是clipPath元素的子元素，则它必须直接引用，或基本形状元素。间接引用是一个错误，必须忽略clipPath元素。

每个子元素的原始几何体（不包括渲染属性，例如clipPath中的填充，笔触，笔触宽度）在概念上定义了1位蒙版（可能除了沿着几何体边缘的抗锯齿），它代表了轮廓与该元素关联的图形。对象轮廓之外的任何东西都被掩盖了。如果通过显示或可见性使子元素不可见，则它对剪切路径没有贡献。当clipPath元素包含多个子元素时，子元素的轮廓在逻辑上被“或”在一起以创建单个轮廓，然后用于限制可以应用油漆的区域。因此，如果一个点位于clipPath的任何子节点内，则它位于剪切路径内。

对于给定的图形元素，使用的实际剪切路径将是其剪辑路径属性（如果有）指定的剪切路径与其祖先上的任何剪切路径的交集，由剪辑路径属性指定。建立新视口的元素。 （参见[SVG11]）

一些新增内容：

1. clipPath元素本身及其子元素不会从clipPath元素的祖先继承剪切路径。

2. clipPath元素或其任何子元素都可以指定属性clip-path。 

如果有效的剪辑路径引用放在clipPath元素上，则生成的剪切路径是clipPath元素的内容与引用的剪切路径的交集。 

如果有效的剪辑路径引用放在clipPath元素的其中一个子元素上，则在将子元素的轮廓与其他子元素的轮廓进行OR运算之前，会通过引用的剪切路径剪切给定的子元素。 

3. 空剪切路径将完全剪掉已应用剪辑路径属性的元素。
<h3 id="a6-2">6.2. 绕组规则：clip-rule属性</h3>



<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>clip-rule </td>
    </tr>
    <tr>
        <td>Value</td>
        <td>非零| evenodd </td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>nonzero </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>适用于SVG图形元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>yes</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>指定</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


clip-rule属性指示用于确定给定点是否在用图形元素创建的剪切区域的形状内的算法。算法的定义和剪辑规则值遵循fill-rule属性的定义。请参阅SVG 1.1 [SVG11]中的“填充属性”部分。 
<h4>nonzero</h4>

请参阅fill-rule属性[SVG11]的说明。
<h4>evenodd </h4>

请参阅fill-rule属性[SVG11]的说明。

clip-rule属性仅适用于clipPath元素中包含的图形元素。 
<p class="note">注意：clip-rule属性不适用于。

下图说明了非零规则：

<img src="https://www.w3.org/TR/css-masking-1/images/cliprule-nonzero.svg" alt=""/>

下图说明了偶数规则：

<img src="https://www.w3.org/TR/css-masking-1/images/cliprule-evenodd.svg" alt=""/>

以下代码片段将导致奇数剪切规则应用于剪切路径，因为在规则上指定了剪辑规则定义剪裁形状的元素：

下面的代码片段将导致奇偶剪切规则应用于剪切路径，因为在定义裁剪形状的元素上指定了剪辑规则：

<'g clip-rule="nonzero">

  <'clipPath id="MyClip">

    <'path d="..." clip-rule="evenodd" />

  </'clipPath>

  <'rect clip-path="url(#MyClip)" ... />

</'g>

而下面的代码片段不会导致应用evenodd剪切规则，因为剪辑规则是在引用元素上指定的，而不是在定义剪裁形状的对象上：

<'g clip-rule="nonzero">

  <'clipPath id="MyClip">

    <'path d="..." />

  </'clipPath>

  <'rect clip-path="url(#MyClip)" clip-rule="evenodd" ... />

</'g>
<h3 id="a7">7. 定位Mask</h3>
<h3 id="a7-1">7.1. Mask Image Source：mask-image属性</h3>
<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-image  </td>
    </tr>
    <tr>
        <td>Value</td>
        <td><'mask-reference></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>none  </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>如指定的那样，但是URI为绝对值</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


此属性设置元素的遮罩层图像。其中：

mask-reference = none | <‘image> | <’mask-source>

mask-source = url
<h4><'url></h4>

--对<'mask>元素（例如url（commonmasks.svgmask））或CSS映像的URL引用。 
<h4>none </h4>

--值为none将计为透明黑色图像层

除非之外的计算值导致创建堆叠上下文[CSS21]，就像CSS不透明度[CSS3COLOR]对除1以外的值所做的那样。

作为空图像的mask引用（零宽度或零）高度），无法下载，不是对mask元素的引用，不存在，或者无法显示（例如因为它不是支持的图像格式）仍然被视为透明黑色的图像层。 

有关如何处理遮罩层图像的信息，请参阅“遮罩处理”部分。
<p class="note">注意：列表中的值none可能会影响屏蔽操作，具体取决于mask-composite指定的使用的合成运算符
<p class="note">注意：计为遮罩层，可以与或其他列表项组合在一个可重复的列表中
<p class="note">注意：元素也可以使用mask-border-source进行mask。有关该属性与mask-image的交互，请参阅mask-border-source。

Examples for mask references:

body { mask-image: linear-gradient(black 0%, transparent 100%) }
p { mask-image: none }
div { mask-image: url(resources.svg#mask2) }

有关mask-image如何与其他逗号分隔的遮罩属性进行交互以形成每个遮罩层，请参阅“分层多个遮罩层图像”一节。
<h3 id="a7-2">7.2. Mask Image Interpretation: the mask-mode property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-mode  </td>
    </tr>
    <tr>
        <td>Value</td>
        <td><'mask-mode></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>auto  </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>as specified</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


mask-mode属性指示 mask-reference是被视为亮度 mask还是alpha mask。 （参见蒙版处理。）

--masking-mode = alpha | luminance | auto
<h4>alpha</h4>

alpha值表示mask图层图像的alpha值应该用作mask值。请参见计算蒙版值。 
<h4>luminance</h4>

亮度值表示掩模层图像的亮度值应该用作掩模值。请参见计算蒙版值。
<h4>auto</h4>

如果mask-image属性的属于类型，则应使用掩模层图像的亮度值作为mask值。 

如果mask-image属性的是类型，则应使用遮罩层图像的alpha值作为遮罩值。 

在下面的示例中，mask-type属性将mask元素的mask类型值设置为alpha。 mask-image属性具有对此mask元素的引用，mask-mode属性具有brightness值。 mask-mode属性会将mask-type的定义覆盖为亮度。 

mask-mode属性不得影响mask-border-source的mask模式。

<'mask id="SVGMask" mask-type="alpha" maskContentUnits="objectBoundingBox">

  <'radialGradient id="radialFill">

    <'stop stop-color="white" offset="0"/>

    <'stop stop-color="black" offset="1"/>

  </'radialGradient>

  <'circle fill="url(#radialFill)" cx="0.5" cy="0.5" r="0.5"/>

</'mask>


<'style>

  rect {

    mask-image: url(#SVGMask);

    mask-mode: luminance;

  }

</'style>

<'rect width="200" height="200" fill="green"/>

有关掩模模式如何与其他逗号分隔的mask属性进行交互以形成每个遮罩层，请参阅“分层多个遮罩层图像”一节。
<h3 id="a7-3">7.3. Tiling Mask Images: The mask-repeat property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-repeat  </td>
    </tr>
    <tr>
        <td>Value</td>
        <td><'repeat-style></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>no-repeat  </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>Consists of: two keywords, one per dimension</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


指定在调整大小和位置后，如何平铺遮罩层图像。 

请参阅background-repeat属性[CSS3BG]以获取属性值的定义。

body {

    background-color: blue;

    mask-image: url(dot-mask.png) luminance;

    mask-repeat: space;

}

<img src="https://www.w3.org/TR/css-masking-1/images/mask-repeat.svg" alt=""/>

有关mask-repeat如何与其他逗号分隔的遮罩属性进行交互以形成每个遮罩层，请参阅“分层多个遮罩层图像”一节。
<h3 id="a7-4">7.4. Positioning Mask Images: the mask-position property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-position  </td>
    </tr>
    <tr>
        <td>Value</td>
        <td><'position></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>no-repeat  </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>由以下两个关键字组成：表示原点的两个关键字和来自该原点的两个偏移量，每个关键字以绝对长度（如果给定）给出，否则以百分比形式给出。 </td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>百分比：指掩模绘制区域的大小减去掩模层图像的大小;看文本背景位置[CSS3BG] </td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>作为长度，百分比或计算的简单列表的可重复列表</td>
    </tr>
</tbody>
</table>


有关属性值的定义，请参阅background-position属性[CSS3BG]。

在下面的示例中，（单个）图像位于视口的右下角。 

body {

mask-image：url（“logo.png”）; 

mask-position：100％100％; 

mask-repeat：no-repeat; 

} 

面具位置也可以相对于左上角的其他角落。例如，下面将背景图像从底部放置10px，从右边放置3em：

mask-position：right 3em bottom 10px 

请参阅“分层多个遮罩层图像”部分，了解掩模位置如何与其他逗号分隔的交互掩模属性以形成每个遮罩层。
<h3 id="a7-5">7.5. Masking Area: the mask-clip property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-clip  </td>
    </tr>
    <tr>
        <td>Value</td>
        <td>[ <'geometry-box> | no-clip ]</td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>border-box </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>as specified</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


 确定蒙版绘制区域，该区域确定受蒙版影响的区域。元素的绘制内容必须限制在此区域。 

Values具有以下含义：
<h4>content-box </h4>

绘制的内容仅限于（剪切到）content-box框。
<h4>padding-box </h4>

绘制的内容仅限于（剪裁到）padding-box框。
<h4>border-box </h4>

绘制的内容仅限于（剪切到）边框框。 
<h4>margin-box </h4>

绘制的内容仅限于（剪切到）margin外边框。
<h4>fill-box</h4>

绘制的内容仅限于（剪切到）fill-box边界框。 
<h4>stroke-box </h4>

绘制的内容仅限于（剪裁到）stroke-box边界框。 
<h4>view-box </h4>

使用最近的SVG视口作为参考框。

如果为SVG视口创建元素指定了'viewBox'属性：

--引用框位于由'viewBox'属性建立的坐标系的原点。

--参考框的尺寸设置为'viewBox'属性的宽度和高度值。 
<h4>no-clip </h4>

绘制的内容不受限制（未剪裁）。

对于没有关联CSS布局框的SVG元素，值content-box，padding-box，border-box和margin-box计算到填充框。 

对于具有关联CSS布局框的元素，值fill-box，stroke-box和view-box计算为mask-clip的初始值。

请参阅“分层多个遮罩层图像”一节，了解遮罩剪辑如何与其他逗号分隔的遮罩属性交互以形成每个遮罩层。
<h3 id="a7-6">7.6. Positioning Area: the mask-origin property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-origin  </td>
    </tr>
    <tr>
        <td>Value</td>
        <td>[ <'geometry-origin> ]</td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>border-box </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>as specified</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


对于呈现为单个框的元素，指定蒙版定位区域。对于呈现为多个框的元素（例如，几行上的内联框，多个页面上的框）指定框装饰中断操作的框以确定掩模定位区域。 
<h4>content-box </h4>

该位置相对于content-box框。
<h4>padding-box </h4>

该位置相对于padding-box框。 （对于单个框0 0是填充边缘的左上角，100％100％是右下角。）
<h4>padding-box </h4>

该位置相对于padding-box框。
<h4>margin-box</h4>

该位置相对于margin外边。
<h4>fill-box </h4>

该位置相对于fill-box边界框。 
<h4></h4>
<h4>stroke-box </h4>

该位置相对于stroke-box边界框。 
<h4></h4>
<h4>view-box </h4>

使用最近的SVG视口作为参考框。 

如果为SVG视口创建元素指定了'viewBox'属性：

--引用框位于由'viewBox'属性建立的坐标系的原点。 

--参考框的尺寸设置为'viewBox'属性的宽度和高度值。

对于没有关联CSS布局框的SVG元素，值content-box，padding-box，border-box和margin-box计算到填充框。 

对于具有关联CSS布局框的元素，值fill-box，stroke-box和view-box计算为mask-origin的初始值。
<p class="note"> 注意：如果mask-clip是padding-box，mask-origin是border-box，mask-position是左上角（初始值），元素有一个非零边框，那么mask层的顶部和左边图像将被剪裁。 

请参阅“分层多个遮罩层图像”部分，了解遮罩原点如何与其他逗号分隔的遮罩属性进行交互以形成每个遮罩层。
<h3 id="a7-7">7.7. Sizing Mask Images: the mask-size property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-size  </td>
    </tr>
    <tr>
        <td>Value</td>
        <td> <'bg-size></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>auto </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>as specified, but with lengths made absolute</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>作为长度，百分比或计算的简单列表的可重复列表（这意味着关键字值不可动画。）</td>
    </tr>
</tbody>
</table>


指定遮罩层图像的大小。 

请参阅background-size属性[CSS3BG]以获取属性值的定义。 

请参阅“分层多个遮罩层图像”部分，了解遮罩大小如何与其他逗号分隔的遮罩属性交互以形成每个遮罩层。
<h3 id="a7-8">7.8. Compositing mask layers: the mask-composite property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-composite </td>
    </tr>
    <tr>
        <td>Value</td>
        <td><'compositing-operator></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>add </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>as specified</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


compositing-operator = add | subtract | intersect | exclude

每个关键字代表一个Porter-Duff合成操作符[COMPOSITING-1]，它定义了当前遮罩层上使用的合成操作以及它下面的遮罩层。 

在下面，当前的mask层被称为source，它下面的所有mask层（应用了相应的合成运算符）都被引用到destination。 
<h4>add </h4>

源位于目的地之上。 （有关详细信息，请参阅Porter-Duff合成运算符源。）
<h4>subtract </h4>

放置源，它位于目标之外。 （有关详细信息，请参阅Porter-Duff合成操作符源。）
<h4>intersect </h4>

与目标重叠的源部分，替换目标。 （请参阅Porter-Duff合成运算符源。）
<h4>exclude </h4>

组合源和目标的非重叠区域。 （请参阅Porter-Duff合成运算符XOR。）

如果没有其他mask图层，则必须忽略合成运算符。遮罩层不能与元素的内容或元素后面的内容合成，而是必须将它们表现为渲染到隔离组中。 

在应用当前遮罩层的合成操作之前，必须合成当前遮罩层下面的所有遮罩层。 

<img src="https://www.w3.org/TR/css-masking-1/images/mask-source-destination.svg" alt=""/>

两个遮罩层图像都是具有mask-image属性的引用：

mask-image：circle.svg，rect.svg; 

带有rect.svg的遮罩层位于使用circle.svg的遮罩层下方。这意味着circle.svg比rect.svg更接近用户。 

使用属性mask-composite，作者可以选择不同的方法来组合多个遮罩层。 

--add在rect.svg上绘制circle.svg。该行为由合成运算符源描述。 

-----mask-composite：add;

<img src="https://www.w3.org/TR/css-masking-1/images/mask-composite-add.svg" alt=""/>

-----mask-composite: subtract;

-----

<img src="https://www.w3.org/TR/css-masking-1/images/mask-composite-subtract.svg" alt=""/>

-----mask-composite: intersect;

<img src="https://www.w3.org/TR/css-masking-1/images/mask-composite-intersect.svg" alt=""/>

-----mask-composite: exclude;

<img src="https://www.w3.org/TR/css-masking-1/images/mask-composite-exclude.svg" alt=""/>

以下示例指定了两个遮罩层和两个合成运算符。 

mask-image：rect.svg，circle.svg; 

mask-composite：add，exclude; 

rect.svg和circle.svg使用add compositing运算符。没有其他遮罩层可以使用排除，因此会忽略exclude。 

这是具有不同合成运算符的3个遮罩层的示例。 

mask-image：trapeze.svg，circle.svg，rect.svg; 

mask-composite：减去，加; 

首先，circle.svg被“添加”到rect.svg。在第二步中，从前两层“减去”trapeze.svg。

<img src="https://www.w3.org/TR/css-masking-1/images/mask-composite-subtract-add.svg" alt=""/>

有关mask-composite如何与其他逗号分隔的遮罩属性进行交互以形成每个遮罩层，请参阅“分层多个遮罩层图像”一节。
<h3 id="a7-9">7.9.  Mask Shorthand: the mask property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask</td>
    </tr>
    <tr>
        <td>Value</td>
        <td><'mask-layer></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>see individual properties </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>see individual properties</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>see individual properties</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>see individual properties</td>
    </tr>
</tbody>
</table>


mask-layer = <'mask-reference> <'masking-mode>? || <'position> [ / <'bg-size> ]? ||
<'repeat-style> || <'geometry-box> || [ <'geometry-box> | no-clip ] || <'compositing-operator>

如果存在一个值，则它将mask-origin和mask-clip都设置为该值。如果存在两个值，则第一个设置mask-origin和第二个mask-clip。 

如果引用了mask元素，则属性mask-repeat，mask-position，mask-clip，mask-origin和mask-size的使用值必须无效。在这种情况下，元素定义掩模层图像的位置，大小和剪裁。 

mask速记也会将mask-border重置为其初始值。因此，建议作者使用mask速记而不是其他短号或单个属性来覆盖级联中较早的任何mask设置。这将确保mask边框也已重置，以允许新样式生效。 
<h3 id="a7-10">7.10. mask图像渲染模型</h3>

对于使用CSS框模型格式化的元素，将mask-image属性应用于none以外的值将以与CSS不透明度[CSS3COLOR]相同的方式建立堆叠上下文，并且所有元素的后代作为一个组一起呈现，并将整个屏蔽应用于组。 

mask-image属性对任何元素的CSS框的几何或命中测试没有影响。
<h3 id="a7-10-1">7.10.1. Mask processing</h3>

在下一节中，蒙版图像指的是蒙版图层图像或蒙版边框图像。关于计算将与目标α值相乘的掩模值，可以使用两种不同方法中的一种来解释掩模图像。 

计算mask值的第一个也是最简单的方法是使用mask图像的alpha通道。在这种情况下，给定点处的mask值只是该点处的alpha通道的值。颜色通道对掩模值没有贡献。 

计算掩模值的第二种方法是使用掩模图像的亮度。在这种情况下，使用以下过程从颜色通道值和alpha通道值计算给定点处的掩模值。 

1、根据颜色通道值计算亮度值。 

-----如果mask元素上的颜色插值的计算值是linearRGB，则将原始图像颜色值（可能在sRGB颜色空间中）转换为linearRGB颜色空间。 

-----然后，使用非预乘RGB颜色值，应用亮度到alpha系数（如滤镜原语[SVG11]中所定义）将RGB颜色值转换为亮度值。 

2、将计算出的亮度值乘以相应的alpha值，以产生遮罩值。 

无论使用何种方法，计算mask值的过程都假定mask的内容是四通道RGBA图形对象。对于其他类型的图形对象，需要进行如下特殊处理。 

对于在掩模中使用的三通道RGB图形对象（例如，当引用三通道图像文件时），效果就好像对象被转换为具有alpha通道的四通道RGBA图像一致设置为1. 

对于掩模中使用的单通道图像（例如，当引用单通道灰度图像文件时），效果就好像对象被转换为四通道RGBA图像，其中引用对象的单个通道用于计算三个颜色通道，alpha通道统一设置为1。
<p class="note">注意：引用灰度图像文件时，在计算颜色通道时必须考虑将编码灰度值与线性光值相关联的传输曲线。 
<p class="note">注意：出于屏蔽操作的目的，SVG图形元素（例如，或）都被视为四通道RGBA图像。 

mask的效果与没有mask时发生的效果相同，而是给定对象的alpha通道与mask的结果mask值相乘。 

未被遮罩图像覆盖的区域被视为透明黑色。mask值为0. 
<p class="note">注意：具有重复mask图像切片的mask可能彼此偏移。掩模图像之间的空间被视为透明的黑色掩模。 
<h3 id="a7-10-2">7.10.2. 分层多个蒙版图像</h3>

盒子的蒙版可以有多个图层。层数由mask-image属性的逗号分隔值数决定。具有其他的值列表中的值none仍会创建一个图层。 

请参阅分层多个背景图像[CSS3BG]。 

所有遮罩层图像都转换为alpha蒙版（如果需要，请参见遮罩处理），并通过合成将mask-composite指定的合成运算符考虑在内。
<h3 id="a8">8. Border-Box Mask</h3>

使用蒙版边框，图像可以分为九个部分：四个角，四个边和中间部分，如下图所示。

<img src="https://www.w3.org/TR/css-masking-1/images/mask-box-image-mask.svg" alt=""/>

可以以各种方式对这些片进行切片，缩放和拉伸以适合掩模边界图像区域的尺寸。然后将该失真图像用作掩模。 mask-border的语法对应于CSS Background和Borders [CSS3BG]的border-image属性。 

以下示例中的蒙版边框图像被分割为四个角，尺寸为75像素，四个边以及拉伸和缩放的中间部分。 

<img src="https://www.w3.org/TR/css-masking-1/images/mask-box-image.svg" alt=""/>

掩码边框示例。左侧的对象是要屏蔽的对象。第二个图像是alpha蒙版，最后一个图像是蒙版对象。 

div {

background：线性渐变（底部，F27BAA 0％，FCC8AD 100％）; 

mask-border-slice：25填充; 

mask-border-repeat：stretch; 

mask-border-source：url（mask.png）; 

}
<h3 id="a8-1">8.1.  Mask Border Image Source: the mask-border-source property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-border-source</td>
    </tr>
    <tr>
        <td>Value</td>
        <td>none | <'image></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>none</td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>无或带有URI的图像是绝对的</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


指定要用作蒙版边框图像的图像。 

无法下载，不存在或无法显示（例如，因为它不是支持的图像格式）的空图像（零宽度或零高度）的图像被忽略。它仍然计为遮罩边框图像，但不会遮盖该元素。 

有关如何处理蒙版边框图像的信息，请参阅“蒙版处理”。 

除非之外的计算值导致创建堆叠上下文[CSS21]，就像CSS不透明度[CSS3COLOR]对除1以外的值所做的那样。

mask-border-source和mask-image可以指定相互独立。如果两个属性都具有非none值，则该元素将被两个屏蔽操作一个接一个地屏蔽。 
<p class="note">注意：如果在mask-border-source之前或之后将mask-image应用于元素并不重要。两个操作顺序都会导致相同的渲染。
<h3 id="a8-2">8.2. Mask Border Image Interpretation: the mask-border-mode property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-border-mode</td>
    </tr>
    <tr>
        <td>Value</td>
        <td>luminance | alpha</td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>alpha</td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>无或带有URI的图像是绝对的</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


指定要用作蒙版边框图像的图像。 

无法下载，不存在或无法显示（例如，因为它不是支持的图像格式）的空图像（零宽度或零高度）的图像被忽略。它仍然计为遮罩边框图像，但不会遮盖该元素。 

有关如何处理蒙版边框图像的信息，请参阅“蒙版处理”。 

除非之外的计算值导致创建堆叠上下文[CSS21]，就像CSS不透明度[CSS3COLOR]对除1以外的值所做的那样。

mask-border-source和mask-image可以指定相互独立。如果两个属性都具有非none值，则该元素将被两个屏蔽操作一个接一个地屏蔽。 
<p class="note">注意：如果在mask-border-source之前或之后将mask-image应用于元素并不重要。两个操作顺序都会导致相同的渲染。
<h3 id="a8-3">8.3. Mask Border Image Slicing: the mask-border-slice property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-border-slice</td>
    </tr>
    <tr>
        <td>Value</td>
        <td>[<number> | <percentage>]{1,4} fill?</td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>0</td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>as specified</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>指掩模边框图像的大小</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


此属性指定蒙版边​​框图像的顶部，右侧，底部和左侧边缘的向内偏移，将其分为九个区域：四个角，四个边和一个中间。除非存在fill关键字，否则丢弃中间图像部分并将其视为完全不透明的白色（中间部分覆盖的内容不会被遮盖并照亮）。 

请参阅border-image-slice属性[CSS3BG]以获取属性值的定义
<h3 id="a8-4">8.4. Masking Areas: the mask-border-width property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-border-width</td>
    </tr>
    <tr>
        <td>Value</td>
        <td>[ <length> | <percentage> | <number> | auto ]{1,4}</td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>auto</td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>所有 'length>s为绝对值，否则为指定</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>相对于掩码边框图像区域的宽度/高度</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


掩模边框图像绘制在称为掩模边框图像区域的区域内。这是一个默认边界对应边框的区域，请参阅mask-border-outset。 

有关属性值的定义，请参阅border-image-width属性[CSS3BG]。 
<p class="note">注意：对于没有关联布局框的SVG元素，border-width被视为0。
<h3 id="a8-5">8.5. Edge Overhang: the mask-border-outset property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-border-outset</td>
    </tr>
    <tr>
        <td>Value</td>
        <td>	[ <length> | <number> ]{1,4}</td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>0</td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>所有 'length>s为绝对值，否则为指定</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


这些值指定蒙版边​​框图像区域延伸到边框之外的量。如果它有四个值，则按顺序设置顶部，右侧，底部和左侧的开始。如果左侧缺失，则与右侧相同;如果底部缺失，则与顶部相同;如果缺少权利，则与顶部相同。 

与mask-border-width一样，表示相应border-width的倍数。任何mask-border-outset值都不允许使用负值。 
<p class="note">注意：对于没有关联布局框的SVG元素，边框宽度被视为0。
<h3 id="a8-6">8.6.  Mask Border Image Tiling: the mask-border-repeat property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-border-repeat</td>
    </tr>
    <tr>
        <td>Value</td>
        <td>[ stretch | repeat | round | space ]{1,2}</td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>	stretch</td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>所有元素。在SVG中，它适用于除元素和所有图形元素之外的容器元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>as specified</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


此属性指定如何缩放和平铺蒙版边框图像的边和中间部分的图像。第一个关键字适用于水平边，第二个关键字适用于垂直边。如果第二个关键字不存在，则假定它与第一个相同。 

请参阅border-image-repeat属性[CSS3BG]以获取属性值的定义。 

使用蒙版边框图像蒙版部分中给出了缩放和平铺蒙版边框图像部分的确切过程
<h3 id="a8-7">8.7.Mask Border Image Shorthand: the mask-border property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-border</td>
    </tr>
    <tr>
        <td>Value</td>
        <td><‘mask-border-source’> || <‘mask-border-slice’> [ / <‘mask-border-width’>? [ / <‘mask-border-outset’> ]? ]? || <‘mask-border-repeat’> || <‘mask-border-mode’></td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>See individual properties</td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>See individual properties</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>See individual properties</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>指掩模边框图像的大小</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>See individual properties</td>
    </tr>
</tbody>
</table>


这是设置mask-border-source，mask-border-slice，mask-border-width，mask-border-outset和mask-border-repeat的简写属性。省略的值设置为其初始值。 
<p class="note">注意：蒙版速记重置属性mask-border，mask-border-source，mask-border-mode，mask-border-slice，mask-border-width，mask-border-outset和mask-border-repeat。 
<h3 id="a8-8">8.8. 使用蒙版边框图像蒙版</h3>

在蒙版边框切片给出的蒙版边框图像被蒙版边框切片值切片后，生成的九个图像被缩放，定位并平铺到相应的蒙版边框图像中如绘制边框图像[CSS3BG]一节中所述的四个步骤中的区域。 

将mask-border-source属性应用于使用CSS框模型格式化的元素，以与CSS不透明[CSS3COLOR]相同的方式建立堆叠上下文，并将所有元素的后代一起渲染为一个组。掩蔽应用于整个群体。 

mask-border-source属性对任何元素的CSS框的几何或命中测试没有影响。
<h3 id="a9">9. SVG Mask Sources</h3>
<h3 id="a9-1">9.1 The mask element</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask</td>
    </tr>
    <tr>
        <td>类别：</td>
        <td>容器元素</td>
    </tr>
    <tr>
        <td>内容模型</td>
        <td>
            
            任意数量的以下元素，按任何顺序：
            
            animation — <'animate>, <'animateColor>, <'animateMotion>, <'animateTransform>, <'set>
            
            descriptive — <'desc>, <'title>, <'metadata>
            
            shape — <'circle>, <'ellipse>, <'line>, <'path>, <'polygon>, <'polyline>, <'rect>
            
            structural — <'defs>, <'g>, <'svg>, <'symbol>, <'use>
            
            gradient — <'linearGradient>, <'radialGradient>
            
            <'a>
            
            <'clipPath>
            
            <'color-profile>
            
            <'cursor>
            
            <'filter>
            
            <'font>
            
            <'font-face>
            
            <'foreignObject>
            
            <'image>
            
            <'marker>
            
            <'mask>
            
            <'pattern>
            
            <'script>
            
            <'style>
            
            <'switch>
            
            <'view>
            
            <'text>
            
            <'altGlyphDef>
            
            
            
            
            
            
            
            
            
            
            
            
        </td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>See individual properties</td>
    </tr>
    <tr>
        <td>属性：</td>
        <td>
            
            conditional processing attributes — ‘requiredFeatures’, ‘requiredExtensions’, ‘systemLanguage’
            
            core attributes — ‘id’, ‘xml:base’, ‘xml:lang’, ‘xml:space’
            
            presentation attributes — alignment-baseline, baseline-shift, clip, clip-path, clip-rule, color, color-interpolation, color-interpolation-filters, color-profile, color-rendering, cursor, direction, display, dominant-baseline, enable-background, fill, fill-opacity, fill-rule, filter, flood-color, flood-opacity, font, font-family, font-size, font-size-adjust, font-stretch, font-style, font-variant, font-weight, glyph-orientation-horizontal, glyph-orientation-vertical, image-rendering, kerning, letter-spacing, lighting-color, marker, marker-end, marker-mid, marker-start, mask, opacity, overflow, pointer-events, shape-rendering, stop-color, stop-opacity, stroke, stroke-dasharray, stroke-dashoffset, stroke-linecap, stroke-linejoin, stroke-miterlimit, stroke-opacity, stroke-width, text-anchor, text-decoration, text-rendering, unicode-bidi, visibility, word-spacing, writing-mode
            
            ‘class’
            
            ‘style’
            
            ‘x’
            
            ‘y’
            
            ‘width’
            
            ‘height’
            
            ‘maskUnits’
            
            ‘maskContentUnits’
        </td>
    </tr>
    <tr>
        <td>DOM Interfaces:	</td>
        <td>SVGMaskElement </td>
    </tr>
</tbody>
</table>


属性定义：
<h4>maskUnits =“userSpaceOnUse | objectBoundingBox”</h4>

--定义属性x，y，宽度和高度的坐标系。 
<h4>--userSpaceOnUse </h4>

----x，y，width和height表示当前用户坐标系[CSS3-TRANSFORMS]中引用掩码元素时的值（即，通过掩码元素引用的元素的用户坐标系）面具属性）。 
<h4>--objectBoundingBox </h4>

----x，y，width和height表示应用蒙版的元素的对象边界框的分数或百分比。用户坐标的大小等同于CSS px单元。 

--如果未指定属性maskUnits，则效果就像指定了objectBoundingBox的值一样。 

--Animatable：yes。 
<h4>maskContentUnits =“userSpaceOnUse | objectBoundingBox”</h4>

--定义掩码内容的坐标系。 
<h4>--userSpaceOnUse </h4>

----mask元素内容的用户坐标系是引用mask元素时的当前用户坐标系（即，通过mask属性引用mask元素的元素的用户坐标系） ）。 
<h4>--objectBoundingBox </h4>

----坐标系的原点位于剪切路径所适用的元素的边界框的左上角，以及此边界框的相同宽度和高度。用户坐标的大小等同于CSS px单元。 

--如果未指定属性maskContentUnits，则效果就像指定了userSpaceOnUse的值一样。 

--Animatable：yes。 

x = <'length> | <'percentage>

矩形一角的x轴坐标，用于最大可能的屏幕外缓冲区。如果未指定属性但指定了y，width或height属性中的至少一个，则效果就好像指定了-10％的值。 

--Animatable：yes。 

y = <'length> | <'percentage>

矩形的一个角的y轴坐标，用于最大可能的屏幕外缓冲区。 

如果未指定属性但指定了属性x，width或height中的至少一个，则效果就好像指定了-10％的值。

--Animatable：yes。 

width = <'length> | <'percentage>

最大可能的屏幕外缓冲区的宽度。负值或零值禁用元素的渲染。

如果未指定属性但指定了属性x，y或height中的至少一个，则效果就好像指定了120％的值。

--Animatable：yes。 

height = <'length> | <'percentage>

最大可能的屏幕外缓冲区的高度。 

最大可能的屏幕外缓冲区的高度。 

如果未指定属性但指定了属性x，y或宽度中的至少一个，则效果就好像指定了120％的值。

--Animatable：yes。 

如果指定了属性x，y，宽度或高度中的至少一个，则给定对象和由x，y，宽度和高度定义的矩形建立当前剪切路径。必须通过此当前剪切路径剪切蒙版的渲染内容。 

CSS属性从其祖先继承到mask元素;属性不会从引用mask元素的元素继承。 

mask元素永远不会直接渲染;它们的唯一用法是可以使用mask属性引用的东西。不透明度，滤镜和显示属性不适用于蒙版元素;因此，即使将display属性设置为none以外的值，也不会直接呈现mask元素，即使mask元素或其任何祖先的display属性设置为none，mask元素也可用于引用。
<h3 id="a9-2">9.2 Mask Source Interpretation: the mask-type property</h3>

<table class="code">
<tbody>
    <tr>
        <td>名称：</td>
        <td>mask-type</td>
    </tr>
    <tr>
        <td>Value</td>
        <td>luminance | alpha</td>
    </tr>
    <tr>
        <td>Initial</td>
        <td>luminance</td>
    </tr>
    <tr>
        <td>适用于</td>
        <td>mask元素</td>
    </tr>
    <tr>
        <td>Inherited：</td>
        <td>no</td>
    </tr>
    <tr>
        <td>Media：</td>
        <td>visual </td>
    </tr>
    <tr>
        <td>Computed value:</td>
        <td>as specified</td>
    </tr>
    <tr>
        <td>Percentages:</td>
        <td>n/a</td>
    </tr>
    <tr>
        <td>Animatable:</td>
        <td>no</td>
    </tr>
</tbody>
</table>


mask-type属性定义是否将mask元素的内容视为亮度掩码或alpha掩码，如计算掩码值中所述。 

值具有以下含义：
<h4>luminance </h4>

--表示应使用掩码的亮度值。 
<h4>alpha </h4>

--表示应使用掩码的alpha值。 

mask-type属性允许mask元素的作者指定首选的屏蔽模式。但是，作者可以通过将掩码模式值设置为与掩码内容上的auto不同的值来覆盖此首选项。 

在下面的例子中，mask-type的计算值是亮度，mask-mode的计算值是auto。 UA必须遵循掩码元素上定义的首选掩码模式。

<'svg>

  <'mask style="mask-type: luminance;" id="mask">

    ...

  </'mask>

</'svg>

<'p style="mask-image: url(#mask); mask-mode: auto;">

  This is the masked content.

</'p>

在下一个示例中，掩模模式的计算值是alpha，并覆盖对亮度计算的掩码元素的首选项。掩模层图像用作alpha掩模。

<'svg>

  <'mask style="mask-type: luminance;" id="mask2">

    ...

  </'mask>

</'svg>

<'p style="mask-image: url(#mask2); mask-mode: alpha;">

  This is the masked content.

</'p>

mask-type属性是SVG元素的表示属性。
<h3 id="a10">10. Security</h3>

重要的是，屏蔽操作的定时独立于源像素和目标像素。必须以这样的方式实现掩蔽操作：无论像素值如何，它们总是花费相同的时间。如果不遵循此规则，攻击者可以推断信息并进行定时攻击。 

定时攻击是一种获取有关内容的信息的方法，这些内容受到其他方面的保护，基于研究操作发生所需的时间。例如，如果红色像素比绿色像素花费的时间更长，则可能能够重建正在渲染的元素的粗略图像，而无需访问元素的内容。 

<'mask-source>s and <'clip-source>s对获取资源有特殊要求。 

UA必须使用[HTML5]规范定义的 potentially CORS-enabled fetch方法，操作所有的在mask-image，mask-border-source和clip-path上的mask-source,clip-source,image的值。When fetching，UA必须使用“匿名”模式，将引用来源设置为样式表的URL，并将原点设置为包含文档的URL。如果这导致网络错误，则效果就像没有指定值none一样。
<h3 id="a11">11. 致谢</h3>

感谢Elika J. Etemad，Cameron McCormack，Liam R. E. Quin，BjörnHöhrmann，Alan Stearns和Sara Soueidan的细心评论，评论和更正。特别感谢CJ Gammon提供的图形资产。

<table>
    <thead>
        <tr><th>Name</th><th>Value</th><th>Initial</th><th>Applies to</th><th>Inh.</th><th>%ages</th><th>Media</th><th>Animatable</th><th>Computed value</th></tr>
    </thead>
    <tbody>
        <tr><th><a href="#a5-1" title="clip-path">clip-path</a></th><td>&lt;clip-source&gt; | [ &lt;basic-shape&gt; || &lt;geometry-box&gt; ] | none</td><td>none</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>as specified</td><td>visual</td><td>as specified for &lt;basic-shape&gt; [CSS-SHAPES], otherwise no</td><td>as specified, but with &lt;url&gt; values made absolute
        </td></tr><tr><th><a href="#a6-2" title="clip-rule">clip-rule</a></th><td>nonzero | evenodd</td><td>nonzero</td><td>Applies to SVG graphics elements</td><td>yes</td><td>n/a</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a7-1" title="mask-image">mask-image</a></th><td>&lt;mask-reference&gt;#</td><td>none</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>as specified, but with URIs made absolute
        </td></tr><tr><th><a href="#a7-2" title="mask-mode">mask-mode</a></th><td>&lt;masking-mode&gt;#</td><td>auto</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a7-3" title="mask-repeat">mask-repeat</a></th><td>&lt;repeat-style&gt;#</td><td>no-repeat</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>Consists of: two keywords, one per dimension
        </td></tr><tr><th><a href="#a7-4" title="mask-position">mask-position</a></th><td>&lt;position&gt;#</td><td>center</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>refer to size of mask painting area minus size of mask layer image; see text background-position [CSS3BG]</td><td>visual</td><td>as repeatable list of simple list of length, percentage, or calc</td><td>Consisting of: two keywords representing the origin and two offsets from that origin, each given as an absolute length (if given a &lt;length&gt;), otherwise as a percentage.
        </td></tr><tr><th><a href="#a7-5" title="mask-clip">mask-clip</a></th><td>[ &lt;geometry-box&gt; | no-clip ]#</td><td>border-box</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a7-6" title="mask-origin">mask-origin</a></th><td>&lt;geometry-box&gt;#</td><td>border-box</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a7-7" title="mask-size">mask-size</a></th><td>&lt;bg-size&gt;#</td><td>auto</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>as repeatable list of simple list of length, percentage, or calc (This means keyword values are not animatable.)</td><td>as specified, but with lengths made absolute
        </td></tr><tr><th><a href="#a7-8" title="mask-composite">mask-composite</a></th><td>&lt;compositing-operator&gt;#</td><td>add</td><td>All elements. In SVG, it applies to container elements without the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a7-9" title="mask">mask</a></th><td>&lt;mask-layer&gt;#</td><td>see individual properties</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>see individual properties</td><td>visual</td><td>see individual properties</td><td>see individual properties
        </td></tr><tr><th><a href="#a8-1" title="mask-border-source">mask-border-source</a></th><td>none | &lt;image&gt;</td><td>none</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>none or the image with its URI made absolute
        </td></tr><tr><th><a href="#a8-2" title="mask-border-mode">mask-border-mode</a></th><td>luminance | alpha</td><td>alpha</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a8-3" title="mask-border-slice">mask-border-slice</a></th><td>[&lt;number&gt; | &lt;percentage&gt;]{1,4} fill?</td><td>0</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>refer to size of the mask border image</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a8-4" title="mask-border-width">mask-border-width</a></th><td>[ &lt;length&gt; | &lt;percentage&gt; | &lt;number&gt; | auto ]{1,4}</td><td>auto</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>relative to width/height of the mask border image area</td><td>visual</td><td>no</td><td>all &lt;length&gt;s made absolute, otherwise as specified
        </td></tr><tr><th><a href="#a8-5" title="mask-border-outset">mask-border-outset</a></th><td>[ &lt;length&gt; | &lt;number&gt; ]{1,4}</td><td>0</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>all &lt;length&gt;s made absolute, otherwise as specified
        </td></tr><tr><th><a href="#a8-6" title="mask-border-repeat">mask-border-repeat</a></th><td>[ stretch | repeat | round | space ]{1,2}</td><td>stretch</td><td>All elements. In SVG, it applies to container elements excluding the &lt;defs&gt; element and all graphics elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a8-7" title="mask-border">mask-border</a></th><td>&lt;‘mask-border-source’&gt; || &lt;‘mask-border-slice’&gt; [ / &lt;‘mask-border-width’&gt;? [ / &lt;‘mask-border-outset’&gt; ]? ]? || &lt;‘mask-border-repeat’&gt; || &lt;‘mask-border-mode’&gt;</td><td>See individual properties</td><td>See individual properties</td><td>no</td><td>n/a</td><td>visual</td><td>See individual properties</td><td>See individual properties
        </td></tr><tr><th><a href="#a9-2" title="mask-type">mask-type</a></th><td>luminance | alpha</td><td>luminance</td><td>mask elements</td><td>no</td><td>n/a</td><td>visual</td><td>no</td><td>as specified
        </td></tr><tr><th><a href="#a10" title="clip">clip</a></th><td>&lt;rect()&gt; | auto</td><td>auto</td><td>Absolutely positioned elements. In SVG, it applies to elements which establish a new viewport, &lt;pattern&gt; elements and mask elements.</td><td>no</td><td>n/a</td><td>visual</td><td>as rectangle</td><td>as specified</td></tr>
    </tbody>
</table>

