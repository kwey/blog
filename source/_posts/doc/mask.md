---
title: CSS Masking Module Level 1
top_img: '/img/hy.jpeg'
cover: '/img/hy.jpeg'
---

### Media Source Extensions™
----W3C编辑的草案2016年11月8日

<a href="http://w3c.github.io/media-source/" target="_blank" rel="noopener noreferrer">http://w3c.github.io/media-source/</a>

最新发布的版本：

<a href="https://www.w3.org/TR/media-source/" target="_blank" rel="noopener noreferrer">https://www.w3.org/TR/media-source/</a>

最新编辑的草稿：

<a href="http://w3c.github.io/media-source/" target="_blank" rel="noopener noreferrer">http://w3c.github.io/media-source/</a>

 实施报告：

<a href="http://tidoust.github.io/media-source-testcoverage/" target="_blank" rel="noopener noreferrer">http://tidoust.github.io/media-source-testcoverage/</a>

编辑：

Matthew Wolenetz，Google Inc.; Jerry Smith，微软公司; Mark Watson，Netflix Inc.; Aaron Colwell（2015年4月），Google Inc.; Adrian Bateman（2015年4月），Microsoft Corporation 

Repository: 

<a href="https://github.com/w3c/media-source/"  target="_blank" rel="noopener noreferrer">We are on GitHub</a>

<a href="https://github.com/w3c/media-source/issues" target="_blank" rel="noopener noreferrer">File a bug</a>

<a href="https://github.com/w3c/media-source/commits/gh-pages/media-source-respec.html" target="_blank" rel="noopener noreferrer">Commit history</a>

Mailing list:

<a href="https://lists.w3.org/Archives/Public/public-html-media/" target="_blank" rel="noopener noreferrer">public-html-media@w3.org</a>

Implementation:

<a href="https://caniuse.com/#feat=mediasource" target="_blank" rel="noopener noreferrer">Can I use Media Source Extensions?</a>

<a href="http://w3c-test.org/media-source/" target="_blank" rel="noopener noreferrer">Test Suite</a>

<a href="https://github.com/web-platform-tests/wpt/tree/master/media-source" target="_blank" rel="noopener noreferrer">Test Suite repository</a>


Copyright © 2016 W3C® (MIT, ERCIM, Keio, Beihang). W3C liability, trademark and document use rules apply.
<h3>摘要</h3>

此规范扩展了HTMLMediaElement [HTML51]，允许JavaScript生成用于回放的媒体流。允许JavaScript生成流有助于各种用例，如自适应流和时移实时流。 

如果您希望以编辑跟踪的方式对本文档发表评论或提出错误，请通过我们的公共错误数据库提交。

文档状态

本节介绍本文档发布时的状态。其他文件可能会取代本文件。可以在W3C技术报告索引（<a href="https://www.w3.org/TR/" target="_blank" rel="noopener noreferrer">https://www.w3.org/TR/</a>）中找到当前W3C出版物列表和本技术报告的最新版本。 

工作组维护编辑尚未尝试解决的所有错误报告的列表;在之前的bug跟踪器中可能还存在漏洞。该草案强调了工作组仍有待讨论的一些悬而未决的问题。尚未就这些问题的结果作出决定，包括它们是否有效。 

Implementors应该知道这个规范不稳定。没有参与讨论的实施者可能会发现规范以不兼容的方式从它们下面改变。有兴趣在最终达到候选推荐阶段之前实施本规范的供应商应加入下面提到的邮件列表并参与讨论。

为了使该规范退出候选推荐阶段，将需要两个独立的实现，如CR退出标准（公共许可版本3）文档中所详述，以通过由HTML Media Extensions WG开发的MSE测试套件中的每个测试。 

本文档由HTML Media Extensions Working Group作为编辑草案发布。如果您想对本文档发表评论，请发送至public-html-media

w3.org（订阅，存档）。欢迎所有评论。 

请参阅工作组的实施报告。 

作为编辑草案的出版并不意味着W3C会员资格的认可。这是一份草案文件，可能随时被其他文件更新，替换或废弃。除了正在进行的工作之外，引用此文档是不恰当的。

本文件由一个在2004年2月5日的W3C专利政策下运营的集团制作。 W3C维护一份与该集团可交付成果有关的任何专利披露的公开名单;该页面还包括披露专利的说明。具有个人认为包含基本要求的专利的实际知识的个人必须根据W3C专利政策的第6部分披露信息。 

本文档受2015年9月1日W3C流程文档的约束。
``` javascript 
注：
    UA: userAgent
    -- : 回车
```

<h3 id="a1">1. 简介</h3>

 本节不具有规范性。 

这规范允许javascript为”audio“和”video“动态构建媒体流。它定义了一个MediaSource对象，可以作为HTMLMediaElement的媒体数据源。 MediaSource对象具有一个或多个SourceBuffer对象。应用程序将数据段附加到SourceBuffer对象，并可以根据系统性能和其他因素调整附加数据的质量。来自SourceBuffer对象的数据被管理为用于解码和播放的音频，视频和文本数据的轨道缓冲区。与这些扩展一起使用的字节流规范在字节流格式注册表[MSE-REGISTRY]中可用。

<img src="https://w3c.github.io/media-source/pipeline_model.svg" alt=""/>

<h4 id="a1-1">1.1. 目标</h4>

本规范的设计考虑了以下目标：

--允许JavaScript构建媒体流，而与提取媒体的方式无关。

--定义拼接和缓冲模型，以促进自适应流，广告插入，时移和视频编辑等用例。 

--最小化JavaScript中媒体解析的需要。 

--尽可能地利用浏览器缓存。 

--提供字节流格式规范的要求。 

--不需要支持任何特定的媒体格式或编解码器。 

此规范定义：

--UA的规范行为，用于在处理媒体数据时启用UA和Web应用程序之间的互操作性。 

--规范要求，以使其他规范能够定义在本规范中使用的媒体格式。
<h4 id="a1-2"> 1.2. 定义</h4>

Active Track Buffers 

--轨道缓冲区，为启用的audioTracks，选定的videoTracks以及“显示”或“隐藏”textTracks提供编码帧。所有这些轨道都与activeSourceBuffers列表中的SourceBuffer对象相关联。

Append Window 

--用于在附加时过滤出编码帧的演示时间戳范围。追加窗口表示单个连续时间范围，具有单个开始时间和结束时间。允许将具有此范围内的呈现时间戳的编码帧附加到SourceBuffer，同时过滤掉该范围之外的编码帧。追加窗口的开始和结束时间分别由appendWindowStart和appendWindowEnd属性控制。

Coded Frame 

--具有显示时间戳，解码时间戳和编码帧持续时间的媒体数据单元。

编码帧持续时间

--编码帧的持续时间。对于视频和文本，持续时间表示视频帧或文本应显示多长时间。对于音频，持续时间表示编码帧内包含的所有样本的总和。例如，如果音频帧包含441个样本44100Hz，则帧持续时间将是10毫秒。 

Coded Frame End Timestamp 

--编码帧呈现时间戳及其编码帧持续时间的总和。它表示紧跟在编码帧之后的表示时间戳。

Coded Frame Group

--一组编码帧，它们相邻并具有单调增加的解码时间戳，没有任何间隙。由编码帧处理算法和abort（）调用检测到的不连续性触发新编码帧组的开始。

Decode Timestamp

--解码时间戳指示帧的最新时间，假设对此帧和任何相关帧进行即时解码和渲染（这等于最早帧的呈现时间戳，按呈现顺序，即取决于这个框架）。如果帧可以从呈现顺序解码，则解码时间戳必须存在于字节流中或者可以从字节流中导出。如果不是这种情况，则UA必须运行追加错误算法。如果帧不能从呈现顺序解码并且字节流中不存在解码时间戳，则解码时间戳等于呈现时间戳。

Initialization Segment

--一个字节序列，包含解码一系列媒体段所需的所有初始化信息。这包括编解码器初始化数据，多路复用段的轨道ID映射和时间戳偏移（例如，编辑列表）。
>字节流格式注册表[MSE-REGISTRY]中的字节流格式规范包含格式特定示例。

Media Segment 

--一个字节序列，包含媒体时间轴的一部分的打包和带时间戳的媒体数据。媒体段始终与最近附加的初始化段相关联。
>字节流格式注册表[MSE-REGISTRY]中的字节流格式规范包含格式特定示例。

MediaSource object URL

--A MediaSource对象URL是由createObjectURL（）创建的唯一Blob URI [FILE-API]。它用于将MediaSource对象附加到HTMLMediaElement。 

--这些URL与Blob URI相同，除了引用File和Blob对象的该功能定义中的任何内容在此扩展为也适用于MediaSource对象。 

--MediaSource对象URL的原点是在调用createObjectURL（）期间的相关设置对象。
>例如，MediaSource对象URL的来源会影响画布使用媒体元素的方式。

 Parent Media Source

--SourceBuffer对象的父媒体源是创建它的MediaSource对象。

Presentation Start Time 

--演示开始时间是演示文稿中最早的时间点，指定初始播放位置和最早可能的位置。使用此规范创建的所有演示文稿的演示文稿开始时间均为0
>为了确定HTMLMediaElement.buffered是否包含包含当前播放位置的TimeRange，实现可以选择允许在演示时或之后的当前播放位置开始时间和第一个TimeRange播放第一个TimeRange之前，如果TimeRange在一个相当短的时间内开始，比如1秒，在演示开始时间之后。这种容差适应了这样的现实：多路复用流通常不会在演示开始时精确地开始所有轨道。无论此容差如何，实现都必须报告实际缓冲范围。 

Presentation Interval 

--编码帧的表示间隔是从其表示时间戳到表示时间戳加上编码帧的持续时间的时间间隔。例如，如果编码帧具有10秒的呈现时间戳和100毫秒的编码帧持续时间，则呈现间隔将是[10-10.1]。请注意，范围的起点是包含的，但范围的结尾是独占的。

Presentation Order 

--编码帧在演示文稿中呈现的顺序。通过按照其呈现时间戳以单调递增的顺序对编码帧进行排序来实现呈现顺序。

Presentation Timestamp

--对演示文稿中特定时间的引用。编码帧中的呈现时间戳指示何时应该呈现帧。

Random Access Point 

--媒体片段中的一个位置，可以在不依赖片段中任何先前数据的情况下开始解码和连续播放。对于视频，这往往是I帧的位置。在音频的情况下，大多数音频帧可以被视为随机接入点。由于视频轨道往往具有更稀疏的随机接入点分布，因此这些点的位置通常被认为是多路复用流的随机接入点。

SourceBuffer字节流格式规范

--特定的字节流格式规范，描述SourceBuffer实例接受的字节流的格式。根据传递给创建对象的addSourceBuffer（）调用的类型，选择SourceBuffer对象的字节流格式规范。

SourceBuffer configuration

--一个特定的一组轨道，分布在一个或多个由单个MediaSource实例拥有的SourceBuffer对象上。

--Implementations必须支持至少1个具有以下配置的MediaSource对象：

----单个SourceBuffer，带有1个音轨和/或1个视

----Two SourceBuffers，其中一个处理单个音轨，另一个处理单个视频轨道。

--MediaSource对象必须支持上述每个配置，但它们一次只需要支持一个配置。一次支持多个配置或其他配置是实施质量问题。

Track Description

--字节流格式特定结构，为单个轨道提供Track ID，编解码器配置和其他元数据。单个初始化段内的每个轨道描述都具有唯一的轨道ID。如果Track ID在初始化段中不唯一，则UA必须运行追加错误算法。 

Track ID 

--Track ID是一种字节流格式特定的标识符，它将字节流的各部分标记为特定轨道的一部分。轨道描述中的轨道ID标识媒体段的哪些部分属于该轨道。
<h3 id="a2"> 2. MediaSource对象</h3>

MediaSource对象表示HTMLMediaElement的媒体数据源。它跟踪此源的readyState以及可用于将媒体数据添加到演示文稿的SourceBuffer对象列表。 MediaSource对象由Web应用程序创建，然后附加到HTMLMediaElement。应用程序使用sourceBuffers中的SourceBuffer对象将媒体数据添加到此源。在播放期间需要时，HTMLMediaElement从MediaSource对象中提取此媒体数据。 

每个MediaSource对象都有一个实时可搜索范围变量，用于存储规范化的TimeRanges对象。创建MediaSource对象时，此变量初始化为空的TimeRanges对象，由setLiveSeekableRange（）和clearLiveSeekableRange（）维护，并在HTMLMediaElement Extensions中用于修改HTMLMediaElement.seekable行为。
``` javascript
enum ReadyState {
    "closed",
    "open",
    "ended"
}; 
```

closed:

表示源当前未附加到媒体元素。

open:

open源已由媒体元素打开，可以将数据附加到sourceBuffers中的SourceBuffer对象。

ended:

ends源仍然附加到媒体元素，但是已经调用了endOfStream（）
``` javascript
enum EndOfStreamError {
    "network",
    "decode"
}; 
```

network:

终止播放并发出网络错误信号。
>JavaScript应用程序应使用此状态代码终止播放时出现网络错误。例如，如果在获取媒体数据时发生网络错误。

decode:

终止播放并发出解码错误信号。
>JavaScript应用程序应使用此状态代码终止播放并出现解码错误。例如，如果在处理带外媒体数据时发生解析错误。

``` javascript
    [Constructor]
    interface MediaSource : EventTarget {
        readonly attribute SourceBufferList sourceBuffers;
        readonly attribute SourceBufferList activeSourceBuffers;
        readonly attribute ReadyState       readyState;
                 attribute unrestricted double duration;
                 attribute EventHandler  onsourceopen;
                 attribute EventHandler  onsourceended;
                 attribute EventHandler  onsourceclose;
        SourceBuffer addSourceBuffer(DOMString type);
        void         removeSourceBuffer(SourceBuffer sourceBuffer);
        void         endOfStream(optional EndOfStreamError error);
        void         setLiveSeekableRange(double start, double end);
        void         clearLiveSeekableRange();
        static boolean isTypeSupported(DOMString type);
```
<h4 id="a2-1"> 2.1. 属性</h4>
<h5>sourceBuffers of type SourceBufferList, readonly</h5>

包含与此MediaSource关联的SourceBuffer对象列表。当readyState等于“已关闭”时，此列表将为空。一旦readyState转换为“open”，可以使用addSourceBuffer（）将SourceBuffer对象添加到此列表中。
<h5>activeSourceBuffers of type SourceBufferList, readonly</h5>

包含提供所选视频轨道的sourceBuffers子集，启用的音频轨道以及“显示”或“隐藏”文本轨道。

此列表中的SourceBuffer对象必须以与它们在sourceBuffers属性中出现的顺序相同的顺序出现;例如，如果只有sourceBuffers [0]和sourceBuffers [3]在activeSourceBuffers中，那么activeSourceBuffers [0]必须等于sourceBuffers [0]，而activeSourceBuffers [1]必须等于sourceBuffers [3]。 
>对选定/启用的跟踪状态部分的更改描述了如何更新此属性。
<h5>readyState of type ReadyState, readonly</h5>

指示MediaSource对象的当前状态。创建MediaSource时，readyState必须设置为“closed”。
<h5>duration of type unrestricted double</h5>

允许Web应用程序设置演示文稿持续时间。创建MediaSource对象时，持续时间最初设置为NaN。

在获取时，运行以下步骤：

1.如果readyState属性为“closed”，则返回NaN并中止这些步骤。

2.返回属性的当前值。

On设置，运行以下步骤：

1.如果设置的值为负或NaN，则抛出TypeError异常并中止这些步骤。

2.如果readyState属性不是“打开”，则抛出InvalidStateError异常并中止这些步骤。

3.如果sourceBuffers中的任何SourceBuffer上的更新属性等于true，则抛出InvalidStateError异常并中止这些步骤。

4.运行持续时间更改算法，并将新持续时间设置为分配给此属性的值。
>如果存在具有较高结束时间的任何当前缓冲的编码帧，则持续时间改变算法将调整新的持续时间。
>appendBuffer（）和endOfStream（）可以在某些情况下更新持续时间。 EventHandler类型的
<h5>onsourceopen of type EventHandler</h5>

sourceopen事件的事件处理程序。
<h5>onsourceended of type EventHandler</h5>

sourceended事件的事件处理程序。
<h5>onsourceclose of type EventHandler</h5>

sourceclose事件的事件处理程序。
<h4 id="a2-2">2.2. 方法</h4>
<h5>addSourceBuffer</h5>

向sourceBuffers添加新的SourceBuffer。

Parameter:

type: DOMString

返回类型：SourceBuffer 

调用此方法时，UA必须运行以下步骤：

1.如果type是空字符串，则抛出TypeError异常并中止这些步骤。

2.如果type包含不受支持的MIME类型或包含在sourceBuffers中为其他SourceBuffer对象指定的类型不支持的MIME类型，则抛出NotSupportedError异常并中止这些步骤。

3.如果UA无法处理任何更多SourceBuffer对象，或者如果基于类型创建SourceBuffer将导致不支持的SourceBuffer配置，则抛出QuotaExceededError异常并中止这些步骤。
>例如，如果媒体元素已到达HAVE_METADATA readyState，则UA可能会抛出QuotaExceededError异常。如果UA的媒体引擎不支持在播放期间添加更多曲目，则会发生这种情况。 

4.如果readyState属性未处于“打开”状态，则抛出InvalidStateError异常并中止这些步骤。

5.创建一个新的SourceBuffer对象和相关资源。

6.将新对象上的generate timestamps标志设置为与type关联的字节流格式注册表[MSE-REGISTRY]条目的“Generate Timestamps Flag”列中的值。 

7.如果generate timestamps标志等于true：

----将新对象的mode属性设置为“sequence”。

--否则：

----将新对象的mode属性设置为“segments”。 

8.将新对象添加到sourceBuffers并将任务排队以在sourceBuffers上触发名为addsourcebuffer的简单事件。

9.返回新对象。
<h5>removeSourceBuffer</h5>

向sourceBuffers添加新的SourceBuffer。

Parameter:

type: DOMString

返回类型：SourceBuffer 

调用此方法时，UA必须运行以下步骤：

1.如果sourceBuffer指定了不在sourceBuffers中的对象，则抛出NotFoundError异常并中止这些步骤。

2.如果sourceBuffer.updating属性等于true，则运行以下步骤：

2.1.如果缓冲区附加算法正在运行，则中止该算法。

2.2.将sourceBuffer.updating属性设置为false。

2.3.Queue一个任务，在sourceBuffer上触发一个名为abort的简单事件。

2.4.Queue一个任务，在sourceBuffer上触发一个名为updateend的简单事件。

3.让SourceBuffer audioTracks列表等于sourceBuffer.audioTracks返回的AudioTrackList对象。 

4.如果SourceBuffer audioTracks列表不为空，则运行以下步骤：

4.1.让HTMLMediaElement audioTracks列表等于HTMLMediaElement上audioTracks属性返回的AudioTrackList对象。

4.2.对于SourceBuffer audioTracks列表中的每个AudioTrack对象，请运行以下步骤：

4.2.1.将AudioTrack对象上的sourceBuffer属性设置为null。

4.2.2.从HTMLMediaElement audioTracks列表中删除AudioTrack对象。
>这应该触发AudioTrackList [HTML51]逻辑，将任务排队以激活名为removetrack的可信事件，该事件不会冒泡且不可取消，并且使用TrackEvent接口，并将track属性初始化为AudioTrack对象，at HTMLMediaElement audioTracks列表。如果AudioTrack对象的enabled属性在此删除步骤开始时为true，那么这也应该触发AudioTrackList [HTML51]逻辑，将任务排队以在HTMLMediaElement audioTracks列表中触发名为change的简单事件

4.2.3.从SourceBuffer audioTracks列表中删除AudioTrack对象。
>这应该触发AudioTrackList [HTML51]逻辑，将任务排队以激活名为removetrack的可信事件，该事件不会冒泡且不可取消，并且使用TrackEvent接口，并将track属性初始化为AudioTrack对象，at SourceBuffer audioTracks列表。如果AudioTrack对象的enabled属性在此删除步骤开始时为true，那么这也应该触发AudioTrackList [HTML51]逻辑来排队任务以在SourceBuffer audioTracks列表中触发名为change的简单事件

5.Let SourceBuffer videoTracks list等于sourceBuffer.videoTracks返回的VideoTrackList对象。

6.如果SourceBuffer videoTracks列表不为空，则运行以下步骤：

6.1.让HTMLMediaElement videoTracks列表等于HTMLMediaElement上videoTracks属性返回的VideoTrackList对象。

6.2.对于SourceBuffer videoTracks列表中的每个VideoTrack对象，请运行以下步骤：

6.2.1.将VideoTrack对象上的sourceBuffer属性设置为null。

6.2.2.从HTMLMediaElement videoTracks列表中删除VideoTrack对象。
>这应该触发VideoTrackList [HTML51]逻辑，将任务排队以触发名为removetrack的可信事件，该事件不会冒泡且不可取消，并且使用TrackEvent接口，并将track属性初始化为VideoTrack对象，at HTMLMediaElement videoTracks列表。如果在此删除步骤开始时VideoTrack对象上的所选属性为true，那么这也应该触发VideoTrackList [HTML51]逻辑，将任务排队以在HTMLMediaElement videoTracks列表中触发名为change的简单事件

6.2.3.删除VideoTrack对象来自SourceBuffer videoTracks列表。
>这应该触发VideoTrackList [HTML51]逻辑来排队任务以触发名为removetrack的可信事件，该事件不会冒泡且不可取消，并且使用TrackEvent接口，并将track属性初始化为VideoTrack对象，位于SourceBuffer videoTracks名单。如果在此删除步骤开始时VideoTrack对象上的所选属性为true，那么这也应该触发VideoTrackList [HTML51]逻辑，将任务排队以在SourceBuffer videoTracks列表中触发名为change的简单事件

7.Let SourceBuffer textTracks list等于sourceBuffer.textTracks返回的TextTrackList对象。

8.如果SourceBuffer textTracks列表不为空，则运行以下步骤：

8.1.让HTMLMediaElement textTracks列表等于HTMLMediaElement上textTracks属性返回的TextTrackList对象。

8.2.对于SourceBuffer textTracks列表中的每个TextTrack对象，请运行以下步骤：

8.2.1.将TextTrack对象上的sourceBuffer属性设置为null。

8.2.2.从HTMLMediaElement textTracks列表中删除TextTrack对象。 
>这应该触发TextTrackList [HTML51]逻辑，将任务排队以激活名为removetrack的可信事件，该事件不会冒泡且不可取消，并使用TrackEvent接口，并将track属性初始化为TextTrack对象，at HTMLMediaElement textTracks列表。如果TextTrack对象上的mode属性在此删除步骤开始时“显示”或“隐藏”，那么这也应该触发TextTrackList [HTML51]逻辑，将任务排队以在HTMLMediaElement textTracks列表中触发名为change的简单事件。 

8.2.3.从SourceBuffer textTracks列表中删除TextTrack对象。
>这应该触发TextTrackList [HTML51]逻辑来排队任务以触发名为removetrack的可信事件，该事件不会冒泡且不可取消，并且使用TrackEvent接口，并将track属性初始化为TextTrack对象，位于SourceBuffer textTracks名单。如果TextTrack对象上的mode属性在此删除步骤开始时“显示”或“隐藏”，那么这也应该触发TextTrackList [HTML51]逻辑来排队任务以在SourceBuffer textTracks列表中触发名为change的简单事件。 

9.如果sourceBuffer在activeSourceBuffers中，则从activeSourceBuffers中删除sourceBuffer并对任务进行排队，以在activeSourceBuffers返回的SourceBufferList中触发名为removedourcebuffer的简单事件。

10.从sourceBuffers中删除sourceBuffer并对任务进行排队，以在SourceBuffers返回的SourceBufferList中触发名为removedourcebuffer的简单事件。

11.销毁sourceBuffer的所有资源。
<h5>endOfStream</h5>

表示流的结尾。

Parameter:

error: EndOfStreamError

返回类型：void  

调用此方法时，UA必须运行以下步骤：

1.如果readyState属性未处于“打开”状态，则抛出一个InvalidStateError异常并中止这些步骤。

2.如果sourceBuffers中的任何SourceBuffer上的更新属性等于true，则抛出InvalidStateError异常并中止这些步骤。

3.运行流算法结束，并将error参数设置为error。
<h5>setLiveSeekableRange</h5>

更新HTMLMediaElement Extensions中使用的实时可搜索范围变量，以修改HTMLMediaElement.seekable行为。 。

Parameter:

start: double

范围的开始，以演示开始时间为单位的秒数。设置时，如果持续时间等于正无穷大，HTMLMediaElement.seekable将返回一个非空的TimeRanges对象，其最低范围的开始时间戳不大于start。 

end: double

范围的结束，以演示开始时间为单位的秒数。设置时，如果持续时间等于正无穷大，HTMLMediaElement.seekable将返回一个非空的TimeRanges对象，其最大范围结束时间戳不小于end。 

返回类型：void  

调用此方法时，UA必须运行以下步骤：

1.如果readyState属性不是“open”，则抛出InvalidStateError异常并中止这些步骤。

2.如果start为负或大于end，则抛出TypeError异常并中止这些步骤。

3.将实时可搜索范围设置为新的标准化TimeRanges对象，该对象包含单个范围，其起始位置为开始，结束位置为结束。
<h5>clearLiveSeekableRange</h5>

更新HTMLMediaElement Extensions中使用的实时可搜索范围变量，以修改HTMLMediaElement.seekable行为。 

没有参数。

返回类型：void  

调用此方法时，UA必须运行以下步骤：

1.如果readyState属性不是“open”，则抛出InvalidStateError异常并中止这些步骤。

2.如果实时可搜索范围包含范围，则将实时可搜范范围设置为新的空TimeRanges对象。
<h5>isTypeSupported, static</h5>

检查MediaSource是否能够为指定的MIME类型创建SourceBuffer对象。 
>如果从此方法返回true，则仅表示MediaSource实现能够为指定的MIME类型创建SourceBuffer对象。如果没有足够的资源来支持添加新的SourceBuffer，则addSourceBuffer（）调用仍然会失败。
>此方法返回true表示HTMLMediaElement.canPlayType（）将返回“可能”或“可能”，因为对于MediaSource来说，支持HTMLMediaElement知道它无法播放的类型是没有意义的。 

向sourceBuffers添加新的SourceBuffer。

Parameter:

type: DOMString

返回类型：boolean 

调用此方法时，UA必须运行以下步骤：

1.如果type为空字符串，则返回false。

2.如果type不包含有效的MIME类型字符串，则返回false。

3.如果type包含MediaSource不支持的媒体类型或媒体子类型，则返回false。

4.如果type包含MediaSource不支持的编解码器，则返回false。

5.如果MediaSource不支持指定的媒体类型，媒体子类型和编解码器组合，则返回false。

6.返回true。
<h4 id="a2-3">2.3. 事件摘要</h4>

sourceopen:

readyState transitions from "closed" to "open" or from "ended" to "open".

sourceended:

readyState transitions from "open" to "ended".

sourceclose:

readyState transitions from "open" to "closed" or "ended" to "closed".
<h3 id="a2-4">2.4. 算法</h3>
<h4 id="a2-41">2.4.1. 附加到媒体元素</h4>

 通过将MediaSource对象URL分配给media元素src属性或media元素内的src属性，可以将MediaSource对象附加到媒体元素。通过将MediaSource对象传递给createObjectURL（）来创建MediaSource对象URL。

 如果使用作为MediaSource对象的媒体提供程序对象或其对象是MediaSource对象的URL记录调用资源获取算法，则让模式为本地,跳过资源获取算法中的第一步（否则可以将模式设置为远程）并将下面的步骤和说明添加到资源获取算法的“其他（模式是本地）”部分。

> 资源获取算法的第一步预计最终与为对象为媒体提供者对象的URL记录选择本地模式一致。目的是，如果HTMLMediaElement的src属性或选定的子的src属性是一个blob：在上次更改相应的src属性时匹配MediaSource对象URL的URL，则该MediaSource对象将用作媒体提供程序对象，资源获取算法中本地模式逻辑中的当前媒体资源。这也意味着当附加MediaSource对象时，将跳过包含遵守任何preload属性的远程模式逻辑。即使最终更改为[HTML51]，当前媒体资源是MediaSource对象时，仍然需要在本地模式逻辑开头执行以下步骤。

> 相对于触发媒体元素的资源选择算法的动作，这些步骤是异步的。在允许调用资源选择算法的任务继续并且达到稳定状态之后运行资源获取算法。实现可能会延迟下面“OTHER”子句中的步骤，直到MediaSource对象可以使用。

如果readyState未设置为“关闭”

运行“如果根本无法获取媒体数据，由于网络错误，导致UA放弃尝试获取资源”资源获取算法的媒体数据处理步骤的步骤名单。

否则

1.将media元素的delaying-the-load-event-flag设置为false。

2.将readyState属性设置为“open”。

3.Queue一项任务是在MediaSource上触发一个名为sourceopen的简单事件。

4.通过运行剩余的“其他（模式是本地）”步骤继续资源获取算法，并进行以下说明：

4.1资源获取算法中的文本或引用“下载”，“接收的字节”的媒体数据处理步骤列表或“当当前媒体资源的新数据变得可用时”指的是通过appendBuffer（）传入的数据。

4.2资源获取算法中的HTTP引用和媒体数据处理步骤列表不适用，因为HTMLMediaElement在附加MediaSource时不通过HTTP获取媒体数据。
> 附加的MediaSource不使用资源获取算法中的远程模式步骤，因此media元素不会触发“挂起”事件。虽然此规范的未来版本可能会从具有附加MediaSource的媒体元素中删除“进度”和“停顿”事件，但符合此版本规范的UA仍可能触发这两个事件，因为这些[HTML51]引用在实现后发生了更改本规范的稳定性。

<h4 id="a2-42"> 2.4.2. 从媒体元素中分离</h4>

在媒体元素将转换为NETWORK_EMPTY并将任务排队以在media元素上触发名为emptied的简单事件的任何情况下，都会运行以下步骤。这些步骤应该在转换之前运行。

1.将readyState属性设置为“closed”。 

2.更新NaN的持续时间。

3.从activeSourceBuffers中删除所有SourceBuffer对象。

4.Queue一个任务，用于在activeSourceBuffers中触发一个名为removedourcebuffer的简单事件

5.从sourceBuffers中删除所有SourceBuffer对象。

6.Queue一个任务，在sourceBuffers上触发一个名为removedourcebuffer的简单事件。

7.Queue任务在MediaSource上触发一个名为sourceclose的简单事件。
> 接下来，该算法旨在从外部调用并在任何必须从媒体元素分离附加的MediaSource如果有的话）的情况下运行。当媒体元素转换为NETWORK_EMPTY时，除了或代替，还可以在HTMLMediaElement[HTML51]操作（如load（）和资源获取算法失败）上调用它。资源获取算法失败是中止资源获取算法或资源选择算法的失败，除了“最终步骤”[HTML51]不被视为触发分离的失败。
<h4 id="a2-43">2.4.3. Seeking</h4>

 运行以下步骤作为“等待UA确定新播放位置的媒体数据是否可用，以及是否可用，直到它已解码足够的数据以播放该位置”的一部分“寻道算法：
>1. media元素在activeSourceBuffers中的每个SourceBuffer对象中查找包含新播放位置的媒体段。TimeMedn中HTMLChiaElement.buffered属性当前值中的任何位置都具有为该位置缓冲的所有必要媒体段。

 如果新的播放位置不在HTMLMediaElement.buffered的任何TimeRange中

1.1如果HTMLMediaElement.readyState属性大于HAVE_METADATA，则将HTMLMediaElement.readyState属性设置为HAVE_METADATA。
> 根据HTMLMediaElement就绪状态[HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。

1.2.media元素一直等到appendBuffer（）调用导致编码的帧处理算法将HTMLMediaElement.readyState属性设置为大于HAVE_METADATA的值。
> Web应用程序可以使用buffered和HTMLMediaElement.buffered来确定媒体元素恢复播放所需的内容。

否则

Continue
>如果readyState属性为“已结束”且新播放位置在当前HTMLMediaElement.buffered中的TimeRange内，则即使当前选定或启用的一个或多个轨道缓冲区的最大范围结束时间戳较小，搜索操作也必须在此处继续完成比新的播放位置。只有当readyState“结束”时缓冲中的逻辑才会发生这种情况。

2.媒体元素重置所有解码器并用来自适当初始化段的数据初始化每个解码器。

3.媒体元件将来自活动轨道缓冲器的编码帧从新播放位置之前的最接近的随机访问点开始馈送到解码器。

4.在“等待稳定状态”步骤中恢复搜索算法
<h4 id="a2-44">2.4.4 SourceBuffer监控</h4>

在播放期间定期运行以下步骤，以确保activeSourceBuffers中的所有SourceBuffer对象都具有足够的数据以确保不间断的播放。对activeSourceBuffers的更改也会导致这些步骤运行，因为它们会影响触发状态转换的条件

具有足够的数据以确保不间断的回放是一种特定于实现的条件，其中UA确定它当前具有足够的数据来播放该呈现而不会拖延一段有意义的时间。不断评估此条件以确定何时将媒体元素转换为HAVE_ENOUGH_DATA就绪状态和从HAVE_ENOUGH_DATA就绪状态转换出来。这些转换指示UA何时认为其具有足够的数据缓冲或分别需要更多数据
>实现可以选择使用缓冲的字节，时间缓冲，附加速率或它认为适合的任何其他度量来确定何时具有足够的数据。使用的指标可能会在播放期间发生变化，因此Web应用程序应仅依赖HTMLMediaElement.readyState的值来确定是否需要更多数据。
>当媒体元素需要更多数据时，UA应尽早将其从HAVE_ENOUGH_DATA转换为HAVE_FUTURE_DATA，以使Web应用程序能够响应而不会导致播放中断。例如，当当前播放位置在缓冲数据结束之前500ms时转换为应用程序提供大约500ms以在播放停顿之前附加更多数据。

如果HTMLMediaElement.readyState属性等于HAVE_NOTHING：

中止这些步骤。

如果 HTMLMediaElement.buffered不包含当前播放位置的TimeRange：

1.将HTMLMediaElement.readyState属性设置为HAVE_METADATA。
>根据HTMLMediaElement就绪状态[HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。

2.中止这些步骤。

如果HTMLMediaElement.buffered包含一个TimeRange，它包含当前播放位置和足够的数据以确保不间断播放：

1将HTMLMediaElement.readyState属性设置为HAVE_ENOUGH_DATA。
>根据HTMLMediaElement就绪状态[HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件

2.如果先前通过转换到HAVE_CURRENT_DATA暂停播放，则此时可以恢复播放。

3.中止这些步骤。

如果HTMLMediaElement.buffered包含一个TimeRange，其中包含当前播放位置和超出当前播放位置的时间，然后运行以下步骤

1.将HTMLMediaElement.readyState属性设置为HAVE_FUTURE_DATA。
>Per HTMLMediaElement ready states [HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。

2.Playback如果之前因过渡到HAVE_CURRENT_DATA而暂停，则可能会在此时恢复。

3.中止这些步骤。

如果HTMLMediaElement.buffered包含一个TimeRange，它以当前播放位置结束，并且没有覆盖当前位置之后的时间范围：

1.将HTMLMediaElement.readyState属性设置为HAVE_CURRENT_DATA
>Per HTMLMediaElement ready states [HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。

Playback此时暂停，因为媒体元素没有足够的数据来推进媒体时间线

3.中止这些步骤。
<h4 id="a2-45">2.4.5更改选定/启用的跟踪状态</h4>

在播放期间，如果所选视频轨道，启用的音频轨道或文本轨道模式发生变化，则需要更新activeSourceBuffers。当发生这些变化中的一个或多个时，需要遵循以下步骤

如果选定的视频轨道更改，然后运行以下步骤：

1.如果与先前选择的视频轨道关联的SourceBuffer未与任何其他启用的轨道关联，请运行以下步骤：

1.1从activeSourceBuffers中删除SourceBuffer。 

1.2Queue在activeSourceBuffers 上触发名为removedourcebuffer的简单事件的任务

2.如果与新选择的视频轨道关联的SourceBuffer尚未在activeSourceBuffers中，请运行以下步骤：

2.1将SourceBuffer添加到activeSourceBuffers

2.2Queue一个任务，在activeSourceBuffers中触发一个名为addsourcebuffer的简单事件

如果音频轨道被禁用且与此轨道关联的SourceBuffer未与任何其他启用或选定的轨道相关联，则执行以下步骤：

1从activeSourceBuffers Queue移除与音频轨道关联的SourceBuffer，以触发一个简单事件

2.在activeSourceBuffers 上命名removedourcebuffer

如果音频轨道已启用且与此轨道关联的SourceBuffer尚未在activeSourceBuffers中，则执行以下步骤：

1.将与音频轨道关联的SourceBuffer添加到activeSourceBuffers 

2.Queue任务以触发简单事件在activeSourceBuffers 中命名为addsourcebuffer

如果文本跟踪模式变为“禁用”且与此轨道关联的SourceBuffer未与任何其他启用或选定的轨道关联，则运行以下步骤：

1.从activeSourceBuffers 中删除与文本轨道关联的SourceBuffer

2.将任务队列化以在activeSourceBuffers中触发名为removedourcebuffer的简单事件

如果文本跟踪模式变为“显示”或“隐藏”且与此轨道关联的SourceBuffer尚未在activeSourceBuffers中，则执行以下步骤：

1.将与文本轨道关联的SourceBuffer添加到activeSourceBuffers 

2.Queue任务以触发在activeSourceBuffers中名为addsourcebuffer的简单事件
<h4 id="a2-46">2.4.6持续时间的变化</h4>

当持续时间需要更改为新的持续时间时，请执行以下步骤。

1.如果持续时间的当前值等于新的持续时间，则返回。

2.如果新持续时间小于sourceBuffers中所有SourceBuffer对象的任何缓冲编码帧的最高显示时间戳，则抛出InvalidStateError异常并中止这些步骤。
>不允许截断当前缓冲媒体的持续时间减少。如果需要截断，请在更新持续时间之前使用remove（）减少缓冲范围。

3.让最高结束时间为源缓冲区中所有SourceBuffer对象上所有轨道缓冲区的最大轨道缓冲区范围结束时间。

4.如果新的持续时间小于最高结束时间，那么
>出现这种情况是因为编码帧移除算法保留了在移除范围开始之前开始的编码帧

4.1将新持续时间更新为最高结束时间。

5.更新持续时间到新的持续时

6.将媒体持续时间更新为新持续时间并运行HTMLMediaElement持续时间更改算法
<h4 id="a2-47">2.4.7流结束算法</h4>

当应用程序通过endOfStream（）调用发出流的结束信号或算法需要发出解码错误信号时，将调用此算法。此算法采用一个错误参数，指示是否将发出错误信号。

1.将readyState属性值更改为“已结束”

2.Queue一个任务，用于在MediaSource上触发一个名为sourceended的简单事件。

3.如果未设置错误

3.1运行持续时间更改算法，并将新持续时间设置为跨源所有SourceBuffer对象中所有轨道缓冲区的最大跟踪缓冲区范围结束时间。

>这允许持续时间适当地反映附加媒体段的结束。例如，如果持续时间明确设置为10秒，并且在调用endOfStream（）之前仅追加0到5秒的媒体段，则持续时间将更新为5秒。

3.2通知媒体元素它现在拥有所有媒体数据。

4.如果错误设置为“network”

4.1如果HTMLMediaElement.readyState属性等于HAVE_NOTHING 

运行“如果根本无法获取媒体数据，由于网络错误，导致UA放弃尝试获取资源”步骤资源获取算法的媒体数据处理步骤列表。

4.2如果HTMLMediaElement.readyState属性大于HAVE_NOTHING 

运行“如果在收到某些媒体数据后连接中断，导致UA放弃尝试获取资源”资源获取算法的媒体数据处理步骤步骤列表。

5.如果错误设置为“decode”

5.1如果HTMLMediaElement.readyState属性等于HAVE_NOTHING 

运行“如果可以获取媒体数据但是通过检查发现它是不支持的格式，或者根本不能渲染”步骤资源获取算法的媒体数据处理步骤列表。 

5.2如果HTMLMediaElement.readyState属性大于HAVE_NOTHING 

运行，则媒体数据是资源获取算法的媒体数据处理步骤列表的损坏步骤。
<h3 id="a3">3. SourceBuffer对象</h3>
``` javascript enum AppendMode {
    "segments",
    "sequence"
};```

segments:

媒体段中的时间戳确定编码帧在演示中的放置位置。媒体片段可以按任何顺序附加

sequence:

媒体片段将被视为与时间相邻，与媒体片段中的时间戳无关。新媒体片段中的编码帧将紧接在先前媒体片段中的编码帧之后。如果需要新的偏移量以使新媒体段与先前媒体段相邻，则将更新timestampOffset属性。在“序列”模式下设置timestampOffset属性允许将媒体段放置在时间线中的特定位置，而不需要知道媒体段中的时间戳
``` javascript
    interface SourceBuffer : EventTarget {
        attribute AppendMode          mode; 
        readonly attribute boolean             updating;
        readonly attribute TimeRanges          buffered;
        attribute double              timestampOffset;
        readonly attribute AudioTrackList      audioTracks;
        readonly attribute VideoTrackList      videoTracks;
        readonly attribute TextTrackList       textTracks;
        attribute double              appendWindowStart;
        attribute unrestricted double appendWindowEnd;
        attribute EventHandler        onupdatestart;
        attribute EventHandler        onupdate;
        attribute EventHandler        onupdateend;
        attribute EventHandler        onerror;
        attribute EventHandler        onabort;
        void appendBuffer(BufferSource data);
        void abort();
        void remove(double start, unrestricted double end);
```
<h3 id="a3-1">3.1. 属性</h3>
<h5>mode of type AppendMode:</h5>

控制如何处理媒体片段序列。创建对象后，此属性最初由addSourceBuffer（）设置。

On getting，返回初始值或成功设置的最后一个值。 

在设置中，运行以下步骤：

1.如果已从父媒体源的sourceBuffers属性中删除此对象，则抛出InvalidStateError异常并中止这些步骤。 

2.如果更新属性等于true，则抛出InvalidStateError异常并中止这些步骤。

3.让新模式等于分配给此属性的新值。

4.如果生成timestamps标志等于true，新模式等于“segments”，则抛出TypeError异常并中止这些步骤。

5.如果父媒体源的readyState属性处于“已结束”状态，则执行以下步骤：

5.1将父媒体源的readyState属性设置为“open”

5.2

6.如果append状态等于PARSING_MEDIA_SEGMENT，则抛出InvalidStateError并中止这些步骤。

7.如果新模式等于“sequence”，则将组开始时间戳设置为组结束时间戳。

8.将属性更新为新模式。
<h5>updating of type boolean, readonly</h5>

指示是否仍在处理appendBuffer（）或remove（）操作的异步延续。创建对象时，此属性最初设置为false
<h5>buffered of type TimeRanges, readonly</h5>

指示SourceBuffer中缓冲的TimeRanges。创建对象时，此属性最初设置为空TimeRanges对象。

读取属性时，必须执行以下步骤：

1.如果已从父媒体源的sourceBuffers属性中删除此对象，则抛出InvalidStateError异常并中止这些步骤。 

2.最高结束时间是此SourceBuffer对象管理的所有跟踪缓冲区中最大的跟踪缓冲区范围结束时间。

3.令交集范围等于TimeRange对象，该对象包含从0到最高结束时间的单个范围。

4.对于此SourceBuffer管理的每个音频和视频轨道缓冲区，请运行以下步骤：
>文本跟踪缓冲区包含在上面的最高结束时间的计算中，但在此处从缓冲范围计算中排除。它们不一定是连续的，当其他媒体轨道在相同时间范围内连续时，它们内的任何不连续性也不会触发播放停顿。

4.1.让跟踪范围等于当前跟踪缓冲区的跟踪缓冲区范围。

4.2.如果readyState为“已结束”，则将轨道范围中最后一个范围的结束时间设置为最高结束时间。

4.3.设新的交叉点范围等于交叉点范围和轨道范围之间的交点。

4.4.用新的交叉点范围替换交叉点范围内的范围。

5.如果交集范围不包含与此属性的当前值完全相同的范围信息，则将此属性的当前值更新为交集范围。

6.返回此属性的当前值
<h5>timestampOffset of type double</h5>

控制应用于附加到此SourceBuffer的后续媒体段内的时间戳的偏移量。 timestampOffset最初设置为0，表示没有应用偏移量。

On getting，返回初始值或成功设置的最后一个值。

在设置中，运行以下步骤：

1.让新时间戳偏移量等于分配给此属性的新值。 

2.如果已从父媒体源的sourceBuffers属性中删除此对象，则抛出InvalidStateError异常并中止这些步骤。

3.如果更新属性等于true，则抛出InvalidStateError异常并中止这些步骤。

4.如果父媒体源的readyState属性处于“已结束”状态，则执行以下步骤：

4.1.将父媒体源的readyState属性设置为“open”

4.2.Queue任务以触发名为sourceopen的简单事件at父媒体来源。

5.如果append状态等于PARSING_MEDIA_SEGMENT，则抛出InvalidStateError并中止这些步骤。

6.如果mode属性等于“sequence”，则将组开始时间戳设置为新的时间戳偏移量。

7.将属性更新为新的时间戳偏移量。
<h5>audioTracks of type AudioTrackList, readonly</h5>

此对象创建的AudioTrack对象列表。
<h5>videoTracks of type VideoTrackList, readonly</h5>

此对象创建的VideoTrack对象列表。 
<h5>textTracks of type TextTrackList, readonly</h5>

此对象创建的TextTrack对象列表。
<h5>appendWindowStart of type double</h5>

附加窗口开始的显示时间戳。该属性最初设置为演示开始时间。

On getting，返回初始值或成功设置的最后一个值。

在设置中，运行以下步骤：

1.如果已从父媒体源的sourceBuffers属性中删除此对象，则抛出InvalidStateError异常并中止这些步骤。

2.如果更新属性等于true，则抛出InvalidStateError异常并中止这些步骤.

3.如果新值小于0或大于或等于appendWindowEnd，则抛出TypeError异常并中止这些步骤。

4.将属性更新为新值。
<h5>appendWindowEnd of type unrestricted double</h5>

追加窗口结束的显示时间戳。此属性最初设置为正无穷大。

On getting，返回初始值或成功设置的最后一个值。 

在设置中，运行以下步骤：

1.如果已从父媒体源的sourceBuffers属性中删除此对象，则抛出InvalidStateError异常并中止这些步骤。

2.如果更新属性等于true，则抛出InvalidStateError异常并中止这些步骤。

3.如果新值等于NaN，则抛出TypeError并中止这些步骤。

4.如果新值小于或等于appendWindowStart，则抛出TypeError异常并中止这些步骤。

5.将属性更新为新值。
<h5>onupdatestart of type EventHandler</h5>

updatestart事件的事件处理程序。 
<h5>onupdate of type EventHandler</h5>

 update事件的事件处理程序。 
<h5>onupdateend of type EventHandler</h5>

updateend事件的事件处理程序。 
<h5>onerror of type EventHandler</h5>

error事件的事件处理程序。 
<h5>onabort of type EventHandler</h5>

abort事件的事件处理程序。 
<h3 id="a3-2">3.2. 方法</h3>
<h5>appendBuffer</h5>

将段数据在BufferSource [WEBIDL]中追加到源缓冲区。

data: BufferSource

Return type: void

调用此方法时，UA会执行以下步骤：

1.运行prepare append算法。

2.将数据添加到输入缓冲区的末尾。

3.将更新属性设置为true。

4.Queue一个任务，在这个SourceBuffer对象上触发一个名为updatestart的简单事件。

5.异步运行缓冲区附加算法。
<h5>abort</h5>

中止当前段并重置段解析器。 

No parameters.

Return type: void

调用此方法时，UA会执行以下步骤：

1.如果已从父媒体源的sourceBuffers属性中删除此对象，则抛出InvalidStateError异常并中止这些步骤。 

2.如果父媒体源的readyState属性未处于“打开”状态，则抛出InvalidStateError异常并中止这些步骤。 

3.如果范围删除算法正在运行，则抛出InvalidStateError异常并中止这些步骤。 

4.如果更新属性等于true，则执行以下步骤：

4.1.如果缓冲区附加算法正在运行，则中止该算法。 

4.2.将更新属性设置为false。

4.3.Queue一个任务，在这个SourceBuffer对象上触发一个名为abort的简单事件。

4.4.Queue一个任务，在这个SourceBuffer对象上触发一个名为updateend的简单事件。 

5.运行重置解析器状态算法。 

6.将appendWindowStart设置为演示文稿的开始时间。

7.设置appendWindowEnd到正无限。
<h5>remove</h5>

 删除特定时间范围内的媒体。

Parameter: 

start: double

删除范围的开始，以演示开始时间为单位的秒数。

end: double 无限制

移除范围的结束，以演示开始时间为单位的秒数。

Return type: void

调用此方法时，UA会执行以下步骤：

1.如果已从父媒体源的sourceBuffers属性中删除此对象，则抛出InvalidStateError异常并中止这些步骤。 

2.如果更新属性等于true，则抛出InvalidStateError异常并中止这些步骤。

3.如果duration等于NaN，则抛出TypeError异常并中止这些步骤。 

4.如果start为负或大于duration，则抛出TypeError异常并中止这些步骤

5.如果end小于或等于start或end等于NaN，则抛出TypeError异常并中止这些步骤。

6.如果父媒体源的readyState属性处于“已结束”状态，则执行以下步骤：

6.1.将父媒体源的readyState属性设置为“open”

6.2.Queue任务以触发名为sourceopen的简单事件at父媒体来源。 

7.运行范围删除算法，将start和end作为删除范围的开始和结束。
<h3 id="a3-3">3.3. Track Buffers</h3>

轨道缓冲器存储单个轨道的轨道描述和编码帧。当初始化段和媒体段附加到SourceBuffer时，轨道缓冲区被更新。 

每个轨道缓冲区具有最后一个解码时间戳变量，该变量存储附加在当前编码帧组中的最后一个编码帧的解码时间戳。最初未设置该变量以指示尚未附加编码帧。 

每个轨道缓冲器具有最后帧持续时间变量，其存储附加在当前编码帧组中的最后编码帧的编码帧持续时间。最初未设置该变量以指示尚未附加编码帧。

每个轨道缓冲区具有最高结束时间戳变量，该变量存储当前编码帧组中附加到该轨道缓冲区的所有编码帧的最高编码帧结束时间戳。最初未设置该变量以指示尚未附加编码帧。

每个跟踪缓冲区都需要一个随机访问点标志变量，用于跟踪跟踪缓冲区是否在等待随机访问点编码帧。该变量最初设置为true，表示在将任何内容添加到轨道缓冲区之前需要随机访问点编码帧。 

每个轨道缓冲区都有一个轨道缓冲区范围变量，表示当前存储在轨道缓冲区中的编码帧占用的显示时间范围。
> 对于跟踪缓冲区范围，这些显示时间范围基于呈现时间戳，帧持续时间以及跨多个源缓冲区中的跟踪缓冲区中的编码帧组的潜在编码帧组开始时间。 

出于规范目的，此信息被视为存储在规范化的TimeRanges对象中。相交的跟踪缓冲区范围用于报告HTMLMediaElement.buffered，因此必须支持HTMLMediaElement.buffered的每个范围内的不间断回放。
>这些编码帧组开始时间与编码帧处理算法中提到的略有不同，因为它们是不连续后所有轨道缓冲区中最早的表示时间戳。不管模式如何，不连续性可以在编码帧处理算法内发生，或者由编码帧移除算法产生。用于确定跟踪缓冲区范围的不相交性的阈值是特定于实现的。例如，为了减少意外的回放停顿，实现可以通过合并由小于目前在该轨道缓冲器中缓冲的最大帧持续时间的2倍的间隙分开的相邻范围来近似编码帧处理算法的不连续性检测逻辑。实现还可以使用编码的帧组开始时间作为跨越多路径SourceBuffer中的轨道缓冲器的范围开始时间，以进一步减少意外的播放停顿。

<h3 id="a3-4">3.4. Event Summary</h3>

updatestart:

updating从false转换为true。

update

追加或删除已成功完成。updating从true转换为false。

updateend

追加或删除已结束。

error

附加期间发生错误。updating从true转换为false。

abort

通过abort（）调用中止追加或删除。updating从true到false的转换。
<h3 id="a3-5"> 3.5. 算法</h3>
<h4 id="a3-51">3.5.1. 段分析器循环</h4>

所有SourceBuffer对象都有一个内部追加状态变量，用于跟踪高级段解析状态。它最初设置为WAITING_FOR_SEGMENT，并且可以在附加数据时转换为以下状态。

追加状态名称描述:

WAITING_FOR_SEGMENT：等待要追加的初始化段或媒体段的开始。

PARSING_INIT_SEGMENT：目前正在解析初始化段。

PARSING_MEDIA_SEGMENT：目前正在解析媒体片段。

输入缓冲区是一个字节缓冲区，用于在appendBuffer（）调用中保存未解析的字节。创建SourceBuffer对象时，缓冲区为空。 

缓冲区满标志跟踪是否允许appendBuffer（）接受更多字节。创建SourceBuffer对象时将其设置为false，并在添加和删除数据时进行更新。

组启动时间戳变量在“序列”模式下跟踪新编码帧组的起始时间戳。创建SourceBuffer对象时未设置，并且当mode属性等于“sequence”并且设置了timestampOffset属性或运行编码的帧处理算法时更新。 

组结束时间戳变量存储当前编码帧组中所有编码帧的最高编码帧结束时间戳。创建SourceBuffer对象时将其设置为0，并通过编码的帧处理算法进行更新。
> 组结束时间戳存储SourceBuffer中所有轨道缓冲区的最高编码帧结束时间戳。因此，在添加时间戳未跨轨道对齐的多路复用段时，应注意设置mode属性。

生成时间戳标志是一个布尔变量，用于跟踪是否需要为传递给编码帧处理算法的编码帧生成时间戳。创建SourceBuffer对象时，此标志由addSourceBuffer（）设置。 

调用段解析器循环算法时，请运行以下步骤：

1.循环顶部：如果输入缓冲区为空，则跳转到下面需要更多数据步骤。 

2.如果输入缓冲区包含违反SourceBuffer字节流格式规范的字节，则运行追加错误算法并中止此算法。 

3，删除字节流格式规范所说的必须从输入缓冲区的开头忽略的任何字节。 

4.如果append状态等于WAITING_FOR_SEGMENT，则运行以下步骤：

4.1.如果输入缓冲区的开头指示初始化段的开始，则将追加状态设置为PARSING_INIT_SEGMENT。 

4.2.如果输入缓冲区的开头指示媒体段的开始，请将附加状态设置为PARSING_MEDIA_SEGMENT。 

4.3.跳到上面的循环顶部。 

5.如果append状态等于PARSING_INIT_SEGMENT，则运行以下步骤：

5.1.如果输入缓冲区尚未包含完整的初始化段，则跳转到下面需要更多数据步骤。 

5.2.运行初始化段接收算法。 

5.3.从输入缓冲区的开头删除初始化段字节。 

5.4.将状态添加到WAITING_FOR_SEGMENT。

5.5.跳到上面的循环顶部。 

6.如果append状态等于PARSING_MEDIA_SEGMENT，则执行以下步骤：

6.1.如果第一个初始化段接收标志为false，则运行追加错误算法并中止此算法。

6.2.如果输入缓冲区包含一个或多个完整的编码帧，则运行编码帧处理算法。
> 编码帧处理算法运行的频率是特定于实现的。当输入缓冲器包含完整媒体段时可以调用编码帧处理算法，或者可以在将完整编码帧添加到输入缓冲器时多次调用它。

6.3.如果此SourceBuffer已满并且无法接受更多媒体数据，则将buffer full标志设置为true。

6.4.如果输入缓冲区不包含完整的媒体段，则跳转到下面需要更多数据步骤。 

6.5.从输入缓冲区的开头删除媒体段字节。 

6.6.将状态添加到WAITING_FOR_SEGMENT。

6.7.跳到上面的循环顶部。 

7.需要更多数据：将控制权返回给调用算法。
<h4 id="a3-52"> 3.5.2. 重置解析器状态</h4>

当需要重置解析器状态时，运行以下步骤：

1.如果追加状态等于PARSING_MEDIA_SEGMENT并且输入缓冲区包含一些完整的编码帧，则运行编码帧处理算法，直到处理完所有这些完整编码帧。 

2.取消设置所有轨道缓冲区的最后一个解码时间戳。

3.取消设置所有轨道缓冲区的最后一帧持续时间。

4.取消设置所有轨道缓冲区的最高结束时间戳。

5.将所有轨道缓冲区上的需要随机访问点标志设置为true。

6.如果mode属性等于“sequence”，则将组开始时间戳设置为组结束时间戳

7.从输入缓冲区中删除所有字节

8.将状态添加到WAITING_FOR_SEGMENT。
<h4 id="a3-53">3.53. 追加错误算法</h4>

在附加期间发生错误时调用此算法。 

1.运行重置解析器状态算法。

2.将更新属性设置为false。

3.Queue一个任务，在这个SourceBuffer对象上触发一个名为error的简单事件。

4.Queue一个任务，在这个SourceBuffer对象上触发一个名为updateend的简单事件。 

5.运行流算法结束，并将error参数设置为“decode”
<h4 id="a3-54">3.5.4. 准备附加算法</h4>

 当追加操作开始时，运行以下步骤以验证和准备SourceBuffer。 

1.如果已从父媒体源的sourceBuffers属性中删除SourceBuffer，则抛出InvalidStateError异常并中止这些步骤。

2.如果更新属性等于true，则抛出InvalidStateError异常并中止这些步骤。

3.如果HTMLMediaElement.error属性不为null，则抛出InvalidStateError异常并中止这些步骤。

4.如果父媒体源的readyState属性处于“已结束”状态，则执行以下步骤：

4.1.将父媒体源的readyState属性设置为“open”

4.2.Queue任务以触发名为sourceopen的简单事件at父媒体来源。

5.运行编码帧驱逐算法。 

6.如果缓冲区满标志等于true，则抛出QuotaExceededError异常并中止这些步骤。
>这是执行无法驱逐足够数据以容纳附加信号或附加信息过大的信号。 Web应用程序应使用remove（）显式释放空间和/或减小追加的大小。
<h4 id="a3-55">3.5.5. 缓冲附加算法</h4>

调用appendBuffer（）时，将运行以下步骤来处理附加数据。 

1.运行段解析器循环算法。

2.如果上一步中的段解析器循环算法被中止，则中止该算法。 

3.将更新属性设置为false。

4.Queue一个任务，在这个SourceBuffer对象上触发一个名为update的简单事件。 

5.Queue一个任务，在这个SourceBuffer对象上触发一个名为updateend的简单事件。
<h4 id="a3-56"> 3.5.6. 范围去除</h4>

当调用者需要启动阻止其他SourceBuffer更新的JavaScript可见范围删除操作时，请遵循以下步骤：

1.让start等于删除范围的起始显示时间戳，以演示文稿开始时间为单位，以秒为单位。 

2.结束等于删除范围的结束呈现时间戳，以演示开始时间为单位的秒数。

3.将更新属性设置为true。

4.Queue一个任务，在这个SourceBuffer对象上触发一个名为updatestart的简单事件。

5.将控制权返回给调用者并异步运行其余步骤。

6.运行编码帧删除算法，将start和end作为删除范围的开始和结束。

7.将更新属性设置为false

8.Queue一个任务，在这个SourceBuffer对象上触发一个名为update的简单事件。

9.Queue一个任务，在这个SourceBuffer对象上触发一个名为updateend的简单事件。
<h4 id="a3-57">3.5.7. 收到初始化段</h4>

当段解析器循环成功解析完整的初始化段时，将运行以下步骤：

每个SourceBuffer对象都有一个内部的第一个初始化段接收标志，用于跟踪该算法是否附加和接收了第一个初始化段。当SourceBuffer由下面的算法创建和更新时，此标志设置为false。 

1.如果当前等于NaN，则更新持续时间属性：

1.1.如果初始化段包含持续时间：

运行持续时间更改算法，并将新持续时间设置为初始化段中的持续时间。

1.2.否则：

运行持续时间更改算法，将新持续时间设置为正无穷大。

2.如果初始化段没有音频，视频或文本轨道，则运行追加错误算法并中止这些步骤。 

3.如果第一个初始化段接收标志为true，则运行以下步骤：

3.1.验证以下属性。如果任何检查失败，则运行追加错误算法并中止这些步骤。

音频，视频和文本轨道的数量与第一个初始化段中的相匹配。 

每个轨道的编解码器匹配第一个初始化段中指定的内容。

如果存在单个类型的多个轨道（例如，2个音频轨道），则轨道ID与第一初始化段中的轨道ID匹配。

3.2.将此初始化段中的相应轨道描述添加到每个轨道缓冲区。 

3.3.将所有轨道缓冲区上的需要随机访问点标志设置为true。

4.让活动跟踪标志等于false。

5.如果第一个初始化段接收标志为false，则执行以下步骤

5.1.如果初始化段包含UA不支持的带编解码器的轨道，则运行追加错误算法并中止这些步骤。

5.如果第一个初始化段接收标志为false，则执行以下步骤
>如果未在传递给addSourceBuffer（）的type参数中指定编解码器，则UA可以将此处支持的编解码器视为“不支持”。 例如，MediaSource.isTypeSupported（'video / webm; codecs =“vp8，vorbis”'）可能返回true，但是如果使用'video / webm; codecs =“vp8”'调用addSourceBuffer（）并且出现Vorbis轨道在初始化段中，UA可以使用该步骤来触发解码错误。

5.2.对于初始化段中的每个音频轨道，请执行以下步骤：

5.2.1.让音频字节流轨道ID成为正在处理的当前轨道的轨道ID。

5.2.2.让音频语言成为此轨道初始化段中指定语言的BCP 47语言标记，如果不存在语言信息，则为空字符串。 

5.2.3.如果音频语言等于'und'BCP 47值，则将空字符串分配给音频语言。

5.2.4.允许音频标签是在此轨道的初始化段中指定的标签，如果没有标签信息，则为空字符串

5.2.5.允许音频种类是在该轨道的初始化段中指定的一系列种类字符串，或者如果没有提供种类信息则在其中具有单个空字符串元素的序列。 

5.2.6.对于音频类型中的每个值，请运行以下步骤：

5.2.6.1.让当前音频类型等于循环迭代的音频种类的值。 

5.2.6.2.让新的音轨成为新的AudioTrack对象。

5.2.6.3.生成唯一ID并将其分配给新音频轨道上的id属性。 

5.2.6.4.将音频语言分配给新音频轨道上的语言属性。 

5.2.6.5.将音频标签分配给新音频轨道上的label属性。

5.2.6.6.将当前音频类型分配给新音频轨道上的kind属性。

5.2.6.7.如果audioTracks.length等于0，则运行以下步骤：

5.2.6.7.1.将新音频轨道上的enabled属性设置为true。 

5.2.6.7.2.将活动跟踪标志设置为true

5.2.6.8.将新音频轨道添加到此SourceBuffer对象上的audioTracks属性。
>这应触发AudioTrackList [HTML51]逻辑，将任务排队，以触发名为addtrack的可信事件，该事件不会冒泡且不可取消，并且在AudioTrackList对象上使用TrackEvent接口，并将track属性初始化为新音频轨道由此SourceBuffer对象上的audioTracks属性引用。 

5.2.6.9.将新音轨添加到HTMLMediaElement上的audioTracks属性。 
>这应该触发AudioTrackList [HTML51]逻辑，将任务排队以触发名为addtrack的可信事件，该事件不会冒泡且不可取消，并且使用TrackEvent接口，并将track属性初始化为新的音频轨道，at HTMLMediaElement上的audioTracks属性引用的AudioTrackList对象。

5.2.7.创建一个新的轨道缓冲区来存储该轨道的编码帧。

5.2.8.将此曲目的曲目描述添加到曲目缓冲区。

5.3.对于初始化段中的每个视频轨道，请运行以下步骤 

5.3.1.让视频字节流轨道ID成为正在处理的当前轨道的轨道ID。 

5.3.2.让视频语言成为此轨道初始化段中指定语言的BCP 47语言标记，如果不存在语言信息，则为空字符串。

5.3.3.如果视频语言等于'und'BCP 47值，则将空字符串分配给视频语言。 

5.3.4.允许视频标签是在此轨道的初始化段中指定的标签，如果没有标签信息，则为空字符串。

5.3.5.允许视频种类是在该轨道的初始化段中指定的种类字符串序列，或者如果没有提供种类信息则在其中具有单个空字符串元素的序列

5.3.6.对于视频类型中的每个值，请运行以下步骤：

5.3.6.1.让当前视频类型等于循环迭代的视频种类的值。 

5.3.6.2.让新视频轨道成为新的VideoTrack对象。 

5.3.6.3.生成唯一ID并将其分配给新视频轨道上的id属性。

5.3.6.4.将视频语言分配给新视频轨道上的语言属性。

5.3.6.5.将视频标签分配给新视频轨道上的label属性。

5.3.6.6.将当前视频类型分配给新视频轨道上的kind属性。 

5.3.6.7.如果videoTracks.length等于0，则运行以下步骤：

5.3.6.7.1.将新视频轨道上的selected属性设置为true。 

5.3.6.7.2.将活动跟踪标志设置为true。  

5.3.6.8.将新视频轨添加到此SourceBuffer对象上的videoTracks属性。 
>这应触发VideoTrackList [HTML51]逻辑，将任务排队，以触发名为addtrack的可信事件，该事件不会冒泡且不可取消，并且在VideoTrackList对象中使用TrackEvent接口，并将track属性初始化为新视频轨道由此SourceBuffer对象上的videoTracks属性引用。

5.3.6.9.将新视频曲目添加到HTMLMediaElement上的videoTracks属性。
>这应该触发VideoTrackList [HTML51]逻辑，将任务排队以触发名为addtrack的可信事件，该事件不会冒泡且不可取消，并且使用TrackEvent接口，并将track属性初始化为新的视频轨道，at HTMLMediaElement上videoTracks属性引用的VideoTrackList对象。

5.3.7.创建一个新的轨道缓冲区来存储该轨道的编码帧。

5.3.8.将此曲目的曲目描述添加到曲目缓冲区。

5.4.对于初始化段中的每个文本轨道，执行以下步骤：

5.4.1.让文本字节流轨道ID成为正在处理的当前轨道的轨道ID。

5.4.2.允许文本语言为此轨道的初始化段中指定的语言的BCP 47语言标记，如果不存在语言信息，则为空字符串。 

5.4.3.如果文本语言等于'und'BCP 47值，则将空字符串分配给文本语言。 

5.4.4.let text label是在此轨道的初始化段中指定的标签，如果没有标签信息，则为空字符串。

5.4.5.let text types是在此轨道的初始化段中指定的类型字符串序列，或者如果没有提供类型信息则在其中包含单个空字符串元素的序列。 

5.4.6.对于文本类型中的每个值，请运行以下步骤：

5.4.6.1.让当前文本类型等于循环迭代的文本种类的值。

5.4.6.2.让新文本轨道成为新的TextTrack对象。

5.4.6.3.生成唯一ID并将其分配给新文本轨道上的id属性。

5.4.6.4.将文本语言分配给新文本轨道上的语言属性。

5.4.6.5.将文本标签分配给新文本轨道上的label属性。

5.4.6.6.将当前文本类型分配给新文本轨道上的kind属性。

5.4.6.7.使用初始化段中的相应信息填充新文本轨道上的其余属性。

5.4.6.8.如果新文本轨道上的mode属性等于“shows”或“hidden”，则将active track flag设置为true。 

5.4.6.9.将新文本轨道添加到此SourceBuffer对象上的textTracks属性。
>这应该触发TextTrackList [HTML51]逻辑，将任务排队，以触发名为addtrack的可信事件，该事件不会冒泡且不可取消，并且在TextTrackList对象中使用TrackEvent接口，并将track属性初始化为新文本轨道由此SourceBuffer对象上的textTracks属性引用。

5.4.6.10.将新文本轨道添加到HTMLMediaElement上的textTracks属性。
>这应该触发TextTrackList [HTML51]逻辑，将任务排队以触发名为addtrack的可信事件，该事件不会冒泡且不可取消，并且使用TrackEvent接口，并将track属性初始化为新文本轨道，at HTMLMediaElement上的textTracks属性引用的TextTrackList对象。 

5.4.7.创建一个新的轨道缓冲区来存储该轨道的编码帧。

5.4.8.将此曲目的曲目描述添加到曲目缓冲区。

5.5.如果活动跟踪标志等于true，则运行以下步骤：

5.5.1.将此SourceBuffer添加到activeSourceBuffers。

5.5.2.Queue一个任务，在activeSourceBuffers

5.6.中触发一个名为addsourcebuffer的简单事件。将第一个初始化段接收标志设置为true。

6.如果HTMLMediaElement.readyState属性为HAVE_NOTHING，则运行以下步骤：

6.1.如果sourceBuffers中的一个或多个对象将第一个初始化段接收标志设置为false，则中止这些步骤。 

6.2.将HTMLMediaElement.readyState属性设置为HAVE_METADATA。 
>Per HTMLMediaElement ready states [HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。此特定转换应触发HTMLMediaElement逻辑，以对任务进行排队，以在media元素处触发名为loadedmetadata的简单事件。 

7.如果活动跟踪标志等于true且HTMLMediaElement.readyState属性大于HAVE_CURRENT_DATA，则将HTMLMediaElement.readyState属性设置为HAVE_METADATA。 
>Per HTMLMediaElement ready states [HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。
<h4 id="a3-58"> 3.5.8. 编码帧处理</h4>

当段解析器循环解析完整的编码帧后，运行以下步骤：

1.对于媒体段中的每个编码帧，运行以下步骤：

1.1.Loop Top：

如果生成timestamps标志等于true：

Let presentation时间戳等于0. 

令解码时间戳等于0. 

否则：

表示时间戳是编码帧的显示时间戳的双精度浮点表示，以秒为单位。
>可能需要特殊处理来确定定时文本帧的呈现和解码时间戳，因为该信息可能未明确地存在于基础格式中或者可能取决于帧的顺序。某些元数据文本轨道（如MPEG2

令解码时间戳是编码帧的解码时间戳的双精度浮点表示，以秒为单位。
>Implementations不必在双精度浮点表示中内部存储时间戳。此处使用此表示形式，因为它是HTML规范中时间戳的表示。这里的目的是使行为清晰，而不会给算法增加不必要的复杂性，以处理添加timestampOffset可能导致字节流格式使用的基础时间戳表示中的时间戳翻转的事实。实现可以使用他们希望的任何内部时间戳表示，但是添加timestampOffset的行为应该与使用双精度浮点表示时的情况类似。

1.2.设帧持续时间是编码帧的持续时间的双精度浮点表示，以秒为单位。

1.3.如果mode等于“sequence”并且设置了group start timestamp，则运行以下步骤：

1.3.1.set timestampOffset等于group start timestamp - presentation timestamp。 

1.3.2.设置组结束时间戳等于组开始时间戳。 

1.3.3.将所有轨道缓冲区上的需要随机访问点标志设置为true。 

1.3.4.Unset组开始时间戳。 

1.4.如果timestampOffset不为0，则运行以下步骤：

1.4.1.将timestampOffset添加到演示文稿时间戳。 

1.4.2.将timestampOffset添加到解码时间戳。

1.5.让跟踪缓冲区等于编码帧将被添加到的轨道缓冲区。

1.6.如果设置了跟踪缓冲区的最后一个解码时间戳并且解码时间戳小于上一个解码时间戳：

or

如果设置了跟踪缓冲区的最后一个解码时间戳，并且解码时间戳和最后一个解码时间戳之间的差异大于最后帧持续时间的2倍：

--如果mode等于“segments”：

----将组结束时间戳设置为显示时间戳。 

--如果mode等于“sequence”：

----将组开始时间戳设置为等于组结束时间戳。 

--取消设置所有轨道缓冲区的最后一个解码时间戳。 

--取消设置所有轨道缓冲区的最后一帧持续时间。 

--取消设置所有轨道缓冲区的最高结束时间戳。 

--将所有轨道缓冲区上的需要随机访问点标志设置为true。 

--跳到上面的Loop Top步骤重新开始处理当前编码帧。

否则：

--继续。

1.7.令帧结束时间戳等于呈现时间戳和帧持续时间的总和。

1.8.如果表示时间戳小于appendWindowStart，则将需要随机访问点标志设置为true，删除编码帧，并跳转到循环顶部以开始处理下一个编码帧。
>某些实现可能会选择收集其中一些编码帧，其显示时间戳小于appendWindowStart，并使用它们在第一个编码帧生成拼接，该拼接帧的显示时间戳大于或等于appendWindowStart，即使该帧不是随机接入点。支持这一点需要多个解码器或比实时解码更快，所以现在这种行为不是规范要求。

1.9.如果帧结束时间戳大于appendWindowEnd，则将需要随机访问点标志设置为true，删除编码帧，并跳转到循环顶部以开始处理下一个编码帧。
>一些实现可以选择收集编码帧，其显示时间戳小于appendWindowEnd，帧结束时间戳大于appendWindowEnd，并且使用它们在收集时在追加窗口内的收集的编码帧的部分上生成拼接，并且开始部分后来处理的帧只与收集的编码帧的末尾部分重叠。支持这一点需要多个解码器或比实时解码更快，所以现在这种行为不是规范要求。结合收集跨越appendWindowStart的编码帧，实现可以因此支持无间隙音频拼接。

1.10.如果轨道缓冲区上需要随机访问点标志等于true，则执行以下步骤：

--如果编码帧不是随机访问点，则删除编码帧并跳转到循环顶部以开始处理下一个编码帧。

--将轨道缓冲区上的需要随机访问点标志设置为false。

1.11.Let拼接音频帧是用于保存音频拼接信息的未设置变量

1.12.Let拼接定时文本帧是用于保存定时文本拼接信息的未设置变量

1.13.如果未设置跟踪缓冲区的最后一个解码时间戳并且呈现时间戳落在跟踪缓冲区中编码帧的呈现间隔内，则执行以下步骤：

1.13.1.将重叠帧作为跟踪缓冲区中与上述条件匹配的编码帧。

1.13.2.如果轨道缓冲区包含音频编码帧：

--运行音频拼接帧算法，如果返回拼接帧，则将其分配给拼接音频帧。

如果轨道缓冲区包含视频编码帧：

--删除窗口时间戳等于重叠帧显示时间戳加1微秒。 

--如果演示文稿时间戳小于删除窗口时间戳，则从轨道缓冲区中删除重叠帧。
>这是为了补偿在双精度浮点数和有理数之间来回转换时出现的帧时间戳计算中的微小错误。只要在现有帧的开始时间的1微秒内，该容差允许帧替换现有帧。在现有帧之前略微出现的帧由下面的删除步骤处理。

如果轨道缓冲区包含定时文本编码帧：

--运行文本拼接帧算法，如果返回拼接帧，则将其分配给拼接定时文本帧。

1.14.删除轨道缓冲区中的现有编码帧：

1.14.1.如果未设置轨道缓冲区的最高结束时间戳：

--从轨道缓冲区中删除所有具有大于或等于显示时间戳且小于帧结束时间戳的显示时间戳的编码帧。

1.14.2.如果设置了跟踪缓冲区的最高结束时间戳并且小于或等于显示时间戳：

--从轨道缓冲区中删除所有具有大于或等于最高结束时间戳且小于帧结束时间戳的表示时间戳的编码帧

1.15.删除所有可能的通过从在前两个步骤中移除的那些帧与在那些移除的帧之后的下一个随机访问点之间的轨道缓冲器中移除所有编码帧，解码对前两个步骤中移除的编码帧的依赖性。
>删除所有编码帧，直到下一个随机访问点是解码依赖关系的保守估计，因为它假设删除的帧和下一个随机访问点之间的所有帧都取决于被删除的帧。

1.16.如果设置了拼接音频帧：

--将拼接音频帧添加到轨道缓冲区。

如果设置了拼接定时文本框：

--将拼接定时文本框添加到轨道缓冲区。

otherwise：

--将带有演示时间戳，解码时间戳和帧持续时间的编码帧添加到轨道缓冲区。

1.17.设置跟踪缓冲区的最后解码时间戳以解码时间戳。

1.18.将轨道缓冲区的最后帧持续时间设置为帧持续时间。

1.19.如果未设置跟踪缓冲区的最高结束时间戳或帧结束时间戳大于最高结束时间戳，则将跟踪缓冲区的最高结束时间戳设置为帧结束时间戳。
>需要大于检查，因为编码帧之间的双向预测可能导致呈现时间戳不单调增加，即使解码时间戳单调增加。

1.20.如果帧结束时间戳大于组结束时间戳，则将组结束时间戳设置为等于帧结束时间戳。

1.21.如果生成timestamps标志等于true，则将timestampOffset设置为等于帧结束时间戳。

2.如果HTMLMediaElement.readyState属性为HAVE_METADATA，并且新编码的帧导致HTMLMediaElement.buffered具有当前播放位置的TimeRange，则将HTMLMediaElement.readyState属性设置为HAVE_CURRENT_DATA。 
>Per HTMLMediaElement ready states [HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。 

3.如果HTMLMediaElement.readyState属性为HAVE_CURRENT_DATA，并且新编码的帧导致HTMLMediaElement.buffered具有包含当前播放位置和超出当前播放位置的时间的TimeRange，则将HTMLMediaElement.readyState属性设置为HAVE_FUTURE_DATA。 
>Per HTMLMediaElement ready states [HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。 

4.如果HTMLMediaElement.readyState属性为HAVE_FUTURE_DATA，并且新编码的帧导致HTMLMediaElement.buffered具有包含当前播放位置的TimeRange和足以确保不间断播放的数据，则将HTMLMediaElement.readyState属性设置为HAVE_ENOUGH_DATA。 
>Per HTMLMediaElement ready states [HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。

5.如果媒体段包含超出当前持续时间的数据，则运行持续时间更改算法，并将新持续时间设置为当前持续时间和组结束时间戳的最大值。
<h4 id="a3-59">3.5.9编码帧删除算法</h4>

当需要从SourceBuffer中删除特定时间范围的编码帧时，请遵循以下步骤：

1.让start为删除范围的起始显示时间戳。

2.Let end是删除范围的结束演示时间戳。

3.对于此源缓冲区中的每个跟踪缓冲区，请运行以下步骤：

3.1.let remove endstamp是duration 

3.2.的当前值如果此跟踪缓冲区的随机访问点时间戳大于或等于end，则更新删除到该随机访问点时间戳的结束时间戳。 
>随机访问点时间戳可以跨轨道不同，因为轨道内编码帧之间的依赖关系通常不同于另一轨道中的依赖关系。 

3.3.从此跟踪缓冲区中删除包含大于或等于start且小于remove end timestamp的起始时间戳的所有媒体数据。

3.3.1.对于每个删除的帧，如果帧的解码时间戳等于帧轨道的最后一个解码时间戳，请运行以下步骤

If mode equals "segments":

--将组结束时间戳设置为显示时间戳。 

如果mode等于“sequence”：

--将组开始时间戳设置为等于组结束时间戳。 

3.3.3.取消设置所有轨道缓冲区的最后一帧持续时间。

3.3.4.取消设置所有轨道缓冲区的最高结束时间戳。 

3.3.5.将所有轨道缓冲区上的需要随机访问点标志设置为true。

3.4.删除在上一步骤中删除的编码帧上的所有可能的解码依赖性，方法是删除在前一步骤中删除的帧与删除帧之后的下一个随机访问点之间的此轨道缓冲区中的所有编码帧。
>删除所有编码帧直到下一个随机访问点是解码依赖性的保守估计，因为它假设移除的帧与下一个随机访问点之间的所有帧都取决于被移除的帧。 

3.5.如果此对象位于activeSourceBuffers中，则当前播放位置大于或等于start并小于remove end timestamp，并且HTMLMediaElement.readyState大于HAVE_METADATA，然后将HTMLMediaElement.readyState属性设置为HAVE_METADATA并停止播放。 
>Per HTMLMediaElement ready states [HTML51]逻辑，HTMLMediaElement.readyState更改可能会触发HTMLMediaElement上的事件。 
>发生此转换是因为当前位置的媒体数据已被删除。在附加当前播放位置的媒体或所选/启用的曲目改变之前，播放无法进行。 

4.如果缓冲区满标志等于true，并且此对象已准备好接受更多字节，则将缓冲区满标志设置为false。
<h4 id="a3-510"> 3.5.10. 编码帧驱逐算法</h4>

运行此算法以在附加新数据时释放此源缓冲区中的空间。

1.让新数据等于即将附加到此SourceBuffer的数据。

2.如果缓冲区满标志等于false，则中止这些步骤。

3.让删除范围等于可以从演示文稿中逐出的演示时间范围列表，以便为新数据腾出空间。
>Implementations可能使用不同的方法来选择删除范围，因此Web应用程序不应该依赖于特定的行为。 Web应用程序可以使用buffered属性来观察缓冲数据的某些部分是否已被驱逐。 

4.对于移除范围中的每个范围，运行编码帧移除算法，其开始和结束分别等于移除范围开始和结束时间戳。
<h4 id="a3-511">3.5.11. 音频拼接帧算法</h4>

当编码帧处理算法需要为两个重叠的音频编码帧生成拼接帧时，请遵循以下步骤：

1.让轨道缓冲区成为将包含拼接的轨道缓冲区。

2.让新的编码帧成为新的编码帧，将其添加到轨道缓冲区，从而触发了对拼接的需求。

3.令演示时间戳是新编码帧的表示时间戳

4.Let解码时间戳是新编码帧的解码时间戳。

5.令帧持续时间是新编码帧的编码帧持续时间。

6.令重叠帧为轨道缓冲区中的编码帧，其呈现间隔包含呈现时间戳。

7.根据重叠帧中音频的采样率，更新演示时间戳并将时间戳解码为最近的音频采样时间戳。如果时间戳与两个音频采样时间戳等距，则使用更高的时间戳（例如，floor（x * sample_rate + 0.5）/ sample_rate）。
>例如，给定以下值：重叠帧的显示时间戳等于10. 重叠帧的采样率等于8000 Hz .显示时间戳等于10.01255 .decode timestamp等于10.01255 .presentation timestamp和decode timestamp更新为10.0125 10.01255比10 + 101/8000（10.012625）更接近10 + 100/8000（10.0125）

8.如果UA不支持交叉淡化，则执行以下步骤：

8.1.从轨道缓冲区中删除重叠帧

8.2.使用以下属性添加静音帧以跟踪缓冲区：

--将显示时间戳设置为重叠的帧显示时间戳。 

--解码时间戳设置为重叠帧解码时间戳。 

--编码帧持续时间设置为显示时间戳和重叠帧显示时间戳之间的差异。 
>某些实现可能会对插入静音两侧的静音到编码帧应用淡入淡出，以使转换不那么刺耳。

8.3.返回呼叫者而不提供拼接框架。
>这是为了允许将新的编码帧添加到轨道缓冲区，就好像重叠帧没有在轨道缓冲区中开始一样。

9.让帧结束时间戳等于表示时间戳和帧持续时间的总和。

10.let splice end timestamp等于表示时间戳和5毫秒的拼接持续时间之和。 

11.淡出编码帧等于重叠帧以及轨道缓冲区中任何其他帧，其显示时间戳大于显示时间戳并小于拼接结束时间戳。

12.从轨道缓冲区中删除淡出编码帧中包含的所有帧。 

13.返回具有以下属性的拼接框架

--演示文稿时间戳设置为重叠的框架演示文稿时间戳。

--解码时间戳设置为重叠帧解码时间戳。

--编码帧持续时间设置为帧结束时间戳和重叠帧显示时间戳之间的差异。

--淡出编码帧等于淡出编码帧。

--淡入编码帧等于新编码帧。
>如果新编码帧的持续时间小于5毫秒，则需要在新编码帧之后附加的编码帧才能正确渲染拼接。

--拼接时间戳等于表示时间戳。
>有关如何渲染此拼接帧的详细信息，请参阅音频拼接渲染算法
<h4 id="a3-512">3.5.12. 音频拼接渲染算法</h4>

当由音频拼接帧算法生成的拼接帧需要由媒体元素渲染时，运行以下步骤：

1.让淡出编码的帧是在拼接期间淡出的编码帧。

2.让编码帧中的淡入淡出是在拼接期间淡入的编码帧。

3.Let presentation timestamp是淡出编码帧中第一个编码帧的表示时间戳。

4.令结束时间戳是呈现时间戳和编码帧中淡入淡出中最后一帧的编码帧持续时间的总和。

5.let splice timestamp是拼接开始的演示时间戳。这与编码帧中淡入淡出中第一帧的呈现时间戳相对应。

6.让splice结束时间戳等于拼接时间戳加上5毫秒。

7.淡出样本是通过解码淡出编码帧产生的样本。

8.Trim淡出样本，使其仅包含演示时间戳和拼接结束时间戳之间的样本。

9.淡化样本是通过解码编码帧中的淡入淡出而产生的样本。

10.如果淡出样本并且淡入样本没有共同的采样率和通道布局，则转换淡出样本并将样本淡入到常见的采样率和通道布局。

11.让输出样本成为保存输出样本的缓冲区。

12.对淡出样本中的拼接时间戳和拼接结束时间戳之间的样本应用线性增益淡出，起始增益为1，结束增益为0。

13.对样本中淡入淡出的拼接时间戳和拼接结束时间戳之间的样本应用线性增益淡入，起始增益为0，结束增益为1。

14.在演示时间戳之间复制样本，以将时间戳从淡出样本拼接到输出样本中。

15.对于拼接时间戳和拼接结束时间戳之间的每个样本，计算淡出样本的样本和淡入样本中的相应样本的总和，并将结果存储在输出样本中。

16.将样本淡入到输出样本的拼接结束时间戳到结束时间戳之间的样本复制。

17.渲染输出样本。
> 以下是此算法的图形表示。<br><img src="https://w3c.github.io/media-source/audio_splice.png" alt="">
<h4 id="a3-513">3.5.13. 文本拼接帧算法</h4>

当编码帧处理算法需要为两个重叠的定时文本编码帧生成拼接帧时，请遵循以下步骤：

1.让跟踪缓冲区成为包含接头的跟踪缓冲区。

2.让新的编码帧成为新的编码帧，将其添加到轨道缓冲区，从而触发了对拼接的需求。

3.令演示时间戳是新编码帧的表示时间戳

4.Let解码时间戳是新编码帧的解码时间戳。

5.令帧持续时间是新编码帧的编码帧持续时间。

6.令帧结束时间戳等于表示时间戳和帧持续时间的总和。

7.让第一个重叠的帧是轨道缓冲区中的编码帧，其中包含一个包含表示时间戳的表示间隔。

8.Let重叠的表示时间戳是第一个重叠帧的表示时间戳。

9.让重叠的帧等于第一个重叠的帧以及轨道缓冲区中的任何其他帧，它们的表示时间戳大于表示时间戳并小于帧结束时间戳。

10.从轨道缓冲区中删除重叠帧中包含的所有帧。

11.将第一个重叠帧的编码帧持续时间更新为演示时间戳 - 重叠的演示时间戳。 

12.将第一个重叠帧添加到轨道缓冲区。

13.返回调用者而不提供拼接框架。
>这是为了允许将新的编码帧添加到轨道缓冲区，就好像它没有重叠轨道缓冲区中的任何帧一样。
<h3 class="4">4. SourceBufferList对象</h3>

SourceBufferList是SourceBuffer对象的简单容器对象。它在修改列表时提供只读数组访问和触发事件。
``` javascript   
    interface SourceBufferList : EventTarget {
        readonly&nbsp; attribute&nbsp; &nbsp; &nbsp; unsigned long length;
        attribute&nbsp; &nbsp; &nbsp; EventHandler  onaddsourcebuffer;
        attribute&nbsp; &nbsp; &nbsp; EventHandler  onremovesourcebuffer;
        getter&nbsp; &nbsp; &nbsp; &nbsp;SourceBuffer (unsigned long index);
    };
```
<h4 id="a4-1">4.1. Attributes</h4>
<h5>length of type unsigned long, readonly</h5>

指示列表中SourceBuffer对象的数量。 
<h5>onaddsourcebuffer of type EventHandler</h5>

addsourcebuffer事件的事件处理程序。
<h5>onremovesourcebuffer of type EventHandler</h5>

removedourcebuffer事件的事件处理程序。
<h4 id="a4-2">4.2. 方法</h4>

getter

--允许使用数组运算符（即[]）访问列表中的SourceBuffer对象。

Parameter:

index: unsigned long

返回类型：SourceBuffer

调用此方法时，UA必须执行以下步骤：

--如果index大于或等于length属性，则返回undefined并中止这些步骤。

--返回列表中索引的SourceBuffer对象。
<h4 id="a4-3">4.3. 事件摘要</h4>

addsourcebuffer:

--将SourceBuffer添加到列表中时。

removesourcebuffer

--从列表中删除SourceBuffer时
<h3 id="a5">5. URL对象扩展</h3>

本节指定URL [FILE-API]对象定义的扩展。
``` javascript
    [Exposed=Window]
    partial interface URL {
        static DOMString createObjectURL(MediaSource mediaSource);
    };
```
<h4 id="a5-1"> 5.1. 方法</h4>

createObjectURL, static

为MediaSource对象创建URL。
>此算法旨在镜像createObjectURL（）[FILE-API]方法的行为，该方法不会自动撤消创建的URL。建议Web作者对附加到媒体元素不再需要的任何MediaSource对象URL使用revokeObjectURL（）[FILE-API]。

Parameter:

mediaSource: MediaSource

Return type: DOMString

调用此方法时，UA必须执行以下步骤：

--返回可用于取消引用mediaSource参数的唯一MediaSource对象URL。
<h3 id="a6">6. HTMLMediaElement扩展</h3>

此部分指定将MediaSource附加到元素时HTMLMediaElement上的现有属性必须返回的内容。

HTMLMediaElement.seekable属性返回基于以下步骤创建的新的静态规范化TimeRanges对象：

--如果duration等于NaN：

----返回一个空的TimeRanges对象。

--如果持续时间等于正无穷大：

----如果实时可搜索范围不为空：

------允许联合范围是实时可搜索范围和HTMLMediaElement.buffered属性的并集

------返回单个范围，其开始时间等于联合范围中的最早开始时间，结束时间等于联合范围中的最高结束时间，并中止这些步骤。

----如果HTMLMediaElement.buffered属性返回空TimeRanges对象，则返回空TimeRanges对象并中止这些步骤。

------返回单个范围，其开始时间为0，结束时间等于HTMLMediaElement.buffered属性报告的最高结束时间。

--否则：

----返回单个范围，开始时间为0，结束时间等于持续时间

HTMLMediaElement.buffered属性根据以下步骤返回静态规范化的TimeRanges对象。

1.让交集范围等于空TimeRanges对象。

2.如果activeSourceBuffers.length不等于0，则运行以下步骤：

2.1.设置活动范围是activeSourceBuffers中每个SourceBuffer对象缓冲返回的范围。

2.2.让最高结束时间成为有效范围内的最大范围结束时间。

2.3.令交集范围等于TimeRange对象，该对象包含从0到最高结束时间的单个范围

2.4.对于activeSourceBuffers中的每个SourceBuffer对象，运行以下步骤：

2.4.1.let source range等于当前SourceBuffer上buffered属性返回的范围。 

2.4.2.如果readyState为“已结束”，则将源范围中最后一个范围的结束时间设置为最高结束时间。 

2.4.3.设新的交点范围等于交点范围和源范围之间的交点。

2.4.4.用新的交叉点范围替换交叉点范围内的范围。

3.如果此算法未设置此属性的当前值，或者交集范围不包含与此属性的当前值完全相同的范围信息，则将此属性的当前值更新为交集范围。

4.返回此属性的当前值。
<h3 id="a7"> 7. AudioTrack扩展</h3>

本节指定HTML AudioTrack定义的扩展。 
``` javascript
    partial interface AudioTrack {
        readonly attribute SourceBuffer? sourceBuffer;
    }; 
```
<h5>sourceBuffer of type SourceBuffer, readonly , nullable</h5>

返回创建此轨道的SourceBuffer。如果此轨道不是由SourceBuffer创建的，或者SourceBuffer已从其父媒体源的sourceBuffers属性中删除，则返回null。
<h3 id="a8"> 8. VideoTrack扩展</h3>

本节指定HTML VideoTrack定义的扩展。 
``` javascript
    partial interface VideoTrack {
        readonly attribute SourceBuffer? sourceBuffer;
    };
```
<h5>sourceBuffer of type SourceBuffer, readonly , nullable</h5>

返回创建此轨道的SourceBuffer。如果此轨道不是由SourceBuffer创建的，或者SourceBuffer已从其父媒体源的sourceBuffers属性中删除，则返回null。
<h3 id="a9">9. TextTrack扩展</h3>

本节指定HTML VideoTrack定义的扩展。 
``` javascript
    partial interface TextTrack {
        readonly attribute SourceBuffer? sourceBuffer;
    };
```
<h5>sourceBuffer of type SourceBuffer, readonly , nullable</h5>

返回创建此轨道的SourceBuffer。如果此轨道不是由SourceBuffer创建的，或者SourceBuffer已从其父媒体源的sourceBuffers属性中删除，则返回null。
<h3 id="a10">10. 字节流格式</h3>

通过appendBuffer（）为SourceBuffer提供的字节形成逻辑字节流。这些字节流的格式和语义以字节流格式规范定义。字节流格式注册表[MSE-REGISTRY]提供可以传递给addSourceBuffer（）或isTypeSupported（）的MIME类型与使用该MIME类型创建的SourceBuffer所期望的字节流格式之间的映射。鼓励实现注册它们支持的字节流格式的映射，以促进互操作性。字节流格式注册表[MSE-REGISTRY]是这些映射的权威来源。如果实现声称支持注册表中列出的MIME类型，则其SourceBuffer实现必须符合注册表项中列出的字节流格式规范。 
>注册表中的字节流格式规范不用于定义新的存储格式。它们只是概述了本规范实现可接受的现有存储格式结构的子集。
>Byte流格式解析和验证在段解析器循环算法中实现。

本节提供了所有字节流格式规范的一般要求：

--字节流格式规范必须定义初始化段和媒体段。

--字节流格式应提供从初始化段中的数据中获取AudioTrack，VideoTrack和TextTrack属性值的参考。
>如果字节流格式覆盖类似于带内轨道规范[INBANDTRACKS]中所覆盖的格式，那么它应该尝试使用相同的属性映射，以便Media Source Extensions播放和非媒体源扩展回放提供相同的赛道信息。 

--必须能够通过单独检查字节流来识别段边界和段类型（初始化或介质）。

--当满足以下任何条件时，UA必须运行追加错误算法：

1.轨道的数量和类型不一致。
>例如，如果第一个初始化段有2个音轨和1个视频轨，那么字节流中跟随它的所有初始化段必须描述2个音轨和1个视频轨。对于描述单个类型的多个轨道（例如，2个音轨）的段，

2.轨道ID在初始化段之间是不同的。

3.Codecs在初始化段之间发生变化。
>例如，不允许以初始化段开始的字节流，该初始化段指定单个AAC轨道，并且稍后包含指定单个AMR-WB轨道的初始化段。使用多个SourceBuffer对象处理对多个编解码器的支持。 

--UA必须支持以下内容：

1.如果段仅描述每种类型的一个轨道，则跟踪在初始化段之间更改的ID。

2.视频帧尺寸变化。UA必须支持无缝播放。
>如果Web应用程序不使用CSS或HTML属性（宽度/高度）来约束元素大小，这将导致显示区域的大小发生变化。

3.音频通道数变化。UA可以无缝地支持它并且可以触发缩混。
>这是一个实施质量问题，因为更改频道数可能需要重新初始化音频设备，重采样器和通道混音器，这些音频设备往往是听得见的。

--以下规则适用于字节流中的所有媒体段。UA必须：

1.将所有时间戳映射到同一媒体时间轴。

2.支持无缝播放时间戳间隔小于音频帧大小的媒体段。UA不得在缓冲属性中反映这些间隙。
>这是为了简化音频流之间的切换，其中帧边界并不总是在编码之间排列（例如，Vorbis）。

--当初始化段和任何连续的媒体段序列的任何组合满足以下条件时，UA必须运行追加错误算法：

1.媒体段中所有轨道的数量和类型（音频，视频，文本等）没有确定。

2.未提供解码每个轨道所需的解码能力（即，编解码器和编解码器参数）。

3.不为所有加密轨道提供解密内容所需的加密参数（加密密钥本身除外）。

4.不提供解码和呈现媒体段序列中最早的随机访问点和序列中的所有子序列样本（在呈现时间中）所需的所有信息。这尤其包括

----信息，用于确定视频的固有宽度和高度（具体而言，这需要图片或像素长宽比以及编码分辨率）。

----将视频解码器输出转换为适合于显示的格式所必需的信息

5.未提供计算媒体段序列中每个样本的全局呈现时间戳所需的信息。

例如，如果I1与M1，M2，M3相关联，则上述必须适用于所有组合I1 + M1，I1 + M2，I1 + M1 + M2，I1 + M2 + M3等。

Byte流规格必须至少定义确保上述要求成立的约束条件。可以定义附加约束，例如以简化实现。
<h3 id="a11">11. 一致性</h3>

除了标记为非规范性的部分外，本规范中的所有创作指南，图表，示例和注释都是非规范性的。本规范中的其他所有内容都是规范性的。 关键词可以，必须，不得，应该，也不应该按照[RFC2119]中的描述进行解释。
<h3 id="a12">12. Examples</h3>

``` javascript
 function onSourceOpen(videoTag, e) {
    var mediaSource = e.target;
    if (mediaSource.sourceBuffers.length > 0)return;
    var sourceBuffer = mediaSource.addSourceBuffer('video/webm; codecs="vorbis,vp8"');

    videoTag.addEventListener('seeking', onSeeking.bind(videoTag, mediaSource));
    videoTag.addEventListener('progress', onProgress.bind(videoTag, mediaSource));

    var initSegment = GetInitializationSegment();

    if (initSegment == null) {
      // Error fetching the initialization segment. Signal end of stream with an error.
      mediaSource.endOfStream("network");
      return;
    }

    // Append the initialization segment.
    var firstAppendHandler = function(e) {
      var sourceBuffer = e.target;
      sourceBuffer.removeEventListener('updateend', firstAppendHandler);

      // Append some initial media data.
      appendNextMediaSegment(mediaSource);
    };
    sourceBuffer.addEventListener('updateend', firstAppendHandler);
    sourceBuffer.appendBuffer(initSegment);
  }

  function appendNextMediaSegment(mediaSource) {
    if (mediaSource.readyState == "closed")
      return;

    // If we have run out of stream data, then signal end of stream.
    if (!HaveMoreMediaSegments()) {
      mediaSource.endOfStream();
      return;
    }

    // Make sure the previous append is not still pending.
    if (mediaSource.sourceBuffers[0].updating)
        return;

    var mediaSegment = GetNextMediaSegment();

    if (!mediaSegment) {
      // Error fetching the next media segment.
      mediaSource.endOfStream("network");
      return;
    }

    // NOTE: If mediaSource.readyState == “ended”, this appendBuffer() call will
    // cause mediaSource.readyState to transition to "open". The web application
    // should be prepared to handle multiple “sourceopen” events.
    mediaSource.sourceBuffers[0].appendBuffer(mediaSegment);
  }

  function onSeeking(mediaSource, e) {
    var video = e.target;

    if (mediaSource.readyState == "open") {
      // Abort current segment append.
      mediaSource.sourceBuffers[0].abort();
    }

    // Notify the media segment loading code to start fetching data at the
    // new playback position.
    SeekToMediaSegmentAt(video.currentTime);

    // Append a media segment from the new playback position.
    appendNextMediaSegment(mediaSource);
  }

  function onProgress(mediaSource, e) {
    appendNextMediaSegment(mediaSource);
  }
```

``` html

<video id="v" autoplay> </video>

<script>
  var video = document.getElementById('v');
  var mediaSource = new MediaSource();
  mediaSource.addEventListener('sourceopen', onSourceOpen.bind(this, video));
  video.src = window.URL.createObjectURL(mediaSource);
</script>

```