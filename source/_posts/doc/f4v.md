---
title: f4v文件格式
top_img: '/img/hy.jpeg'
cover: '/img/hy.jpeg'
---

### Adobe Flash Video File Format Specification</h1>

  Version 10.1


Introduction

Flash®是Web上动态媒体的事实标准，支持多种媒体格式，包括两种用于提供同步音频和视频流的核心容器格式：

-	F4V, for H.264/AAC–based content, and

-	FLV, for other supported codecs such as Sorensen Spark and On2 VP6.

本文档提供Adobe®产品支持的F4V和FLV视频文件格式的技术格式信息。
Adobe认真考虑所有对视频文件格式规范的反馈。 通过flashformat@adobe.com将规范中任何不清楚或可能错误的信息通过电子邮件发送给Adobe。 所有此类电子邮件提交均应遵守使用条款中的提交材料指南，网址为www.adobe.com/misc/copyright.html。

The F4V Video File Format

F4V视频文件格式的开放规范建立在标准IEC 14496-12（MPEG-4 Part 12）ISO基础媒体文件格式之上。 它具有灵活的结构，并定义了特定支持的编解码器和扩展。 因此，F4V视频文件格式简化了动态媒体软件的实现，促进了工具，服务和客户端之间的互操作性。

从Flash Player 9 Update 3（9,0,115,0）开始，Flash Player可以播放F4V文件。 有关F4V视频文件格式的详细信息，请参阅第1节.F4V文件格式
有关在F4V文件中使用元数据的信息，请参阅第3节.F4V元数据。

New in the F4V Video File Format

Flash Player 10,1,53,64的发布增加了Flash F4V视频文件格式中以下功能和框的支持。 斜体框由Adobe Systems定义。

新功能提示点，加密，提示，HTTP流媒体

New Boxes

``` javascript
abst	adaf	adkm	aeib	afra	afrt	ahdr	akey	amhp	amto	aprm	aps
asig	asrt	dinf	dref	edit	elst	enca	encr	encv	flxs	frma	hdlr
hmdh  mehd   mfhd	mfra	mfro	moof	mvex	nmhd   rtmp	schi	schm	sdtp
sinf	smhd   tfhd	tfra	traf	trex	trun	url	vmhd
```


The FLV Video File Format

FLV文件对同步的音频和视频流进行编码。 FLV文件中的音频和视频数据的编码方式与SWF文件中的音频和视频相同。本文档描述了FLV版本1.参见附录E. FLV文件格式

``` javascript  
注：
    UA: userAgent
    -- : 回车
```

<h3 id="a1">1. F4V文件格式</h3>
<h4 id="a1-1">1.1. Overview</h4>

Flash Player 9 Update 3（9,0,115,0）及更高版本可以播放F4V文件。 F4V格式基于ISO / IEC 14496-12：2008 ISO基础媒体文件格式。

将F4V格式与ISO基础媒体文件格式区分开来的很大一部分涉及F4V可以存储的元数据格式。 本章讨论除元数据之外的F4V格式的所有方面，第3节F4V元数据对此进行了介绍。




<h4 id="a1-2">1.2. 简单数据类型</h4>

This following data types are used in F4V files.

``` javascript
Type     	Definition
0x…	      Hexadecimal value …
4CC	      Four-character ASCII code, such as 'moov', encoded as UI32
SI8	      Signed 8-bit integer
SI8.8	    Signed 16-bit fixed point number having 8 fractional bits
SI16	    Signed 16-bit integer
SI16.16	  Signed 32-bit fixed point number having 16 fractional bits
SI24	    Signed 24-bit integer
SI32	    Signed 32-bit integer
SI64	    Signed 64-bit integer
STRING	  Sequence of Unicode 8-bit characters (UTF-8), terminated with 0x00 (unless otherwise specified)
UI8	      Unsigned 8-bit integer
UI16	    Unsigned 16-bit integer
UI16.16	  Unsigned 32-bit fixed point number having 16 fractional bits
UI24	    Unsigned 24-bit integer
UI32	    Unsigned 32-bit integer
UI64	    Unsigned 64-bit integer
UIn	      Bit field with unsigned n-bit integer, where n is in the range 1 to 31, excluding 8, 16, 24
xxx [ ]	  Array of type xxx. Number of elements to be inferred, for example from box size.
xxx [n]	  Array of n elements of type xxx
```


与SWF相比，多字节整数应以big-endian字节顺序存储，SWF使用little-endian字节顺序。 例如，作为SWF文件格式的UI16，表示数字300（0x12C）的字节序列是0x2C 0x01; 作为F4V文件格式的UI16，表示数字300的字节序列是0x01 0x2C。
<h4 id="a1-3">1.3. F4V box format</h4>

F4V文件的基本构建块是具有以下BOX格式的框：F4V框

``` javascript
Field	      Type	      Comment
Header	  BOXHEADER	    所有框都具有一致的标题
Payload	  UI8 [ ]	      字节数，其长度由boxheader定义。
```


每个框结构以一个BoxHeader结构开头：BoxHeader

``` javascript
Field	          Type	           Comment
TotalSize       UI32            框的总大小（以字节为单位），包括此标头。 0表示该框延伸到文件末尾。
BoxType	        UI32	          The type of the box, usually as 4CC

ExtendedSize    IF TotalSize    框的总64位长度（字节），包括此头
                == 1
                UI64
```


许多盒子的长度都小于4GB，可以将它们的大小存储在totalSize字段中。通过将32位totalSize字段设置为1并以extendedSize存储64位大小，该格式还支持非常大的框。每个框都用32位类型标识。对于大多数框，此32位类型可兼作人可读的四字符ASCII代码或4cc，例如“moov”（0x6d6f6f76）和“mdat”（0x6d646174）。

箱有效载荷紧跟箱头。有效负载的字节大小等于框的总大小减去8字节或16字节，具体取决于头的大小。

有关更多信息，请参见ISO/IEC 14496-12:2008第4.2节。
<h4 id="a1-4">1.4. f4v框层次</h4>

Table 1. The F4V Box Hierarchy

``` javascript
Box Type	                  Required?	                    Short Description
ftyp                        Y	                            文件类型和兼容性
pdin                        N	                            渐进式下载信息
afra                        See HTTP streaming            HTTP流的分段随机访问
abst                        See HTTP streaming            HTTP流的引导信息
    asrt                    Y	                            将片段映射到段
    afrt                    Y	                            将时间映射到片段
moov                        Y	                            结构元数据容器
    mvhd                    Y	                            Movie标题，整体声明
    trak                    Y	                            单个轨道的Container
        tkhd                Y	                            标题,主要属性
        edts                N	                            编辑列表容器
            elst            N	                            时间线映射
        mdia                Y	                            媒体轨属性容器
            mdhd            Y	                            媒体track属性
            hdlr            Y	                            处理程序，声明媒体类型
            minf            Y	                            媒体信息容器
                vmhd	                                    视频媒体标题
                smhd                                      声音媒体标题
                hmhd                                      提示媒体标题
                nmhd        Y,其中一个根据媒体类型            空媒体头
                dinf        Y	                            数据信息容器
                  dref      Y	                            数据参考
                      url   Y	                            网址参考
                stbl        Y	                            样本属性容器
                  stsd      Y	                            示例描述（编解码器类型等）
                  stts      Y	                           地图解码采样时间
                  ctts      N	                            将合成时间映射到样本
                  stsc      Y	                            将示例映射到块
                  stsz      N	                           样本大小
                  stco
                  co64      Y, stco or co64               块偏移
                  stss      N	                            同步示例表
                  sdtp      N	                            独立和一次性样品
    mvex                    N	                            Movie extends
        mehd                N	                            Movie extends header
        trex                Y	                            Track extends defaults
    auth                    N	                            Author metadata tag
    titl                    N	                            Title metadata tag
    dscp                    N	                            Description metadata tag
    cprt                    N	                            Copyright metadata tag
    udta                    N	                            User data
uuid                        N	                            XMP Metadata
moof                        N	                            Movie fragment
    mfhd                    Y	                            Movie fragment header
    traf                    N	                            Track fragment
      tfhd                  Y	                            Track fragment header
      trun                  N	                            Track fragment run
mdat                        Y 对于HTTP流以外的其他流          Media data container
meta                        N                           	Container for metadata boxes
    ilst                    N                           	Metadata tags
free                        N	                            Free space
skip                        N	                            Free space
mfra                        N                           	Movie fragment random access
    tfra                    N	                            Track fragment random access
    mfro                    Y	                            Movie fragment random access offset
```

<h4 id="a1-5">1.5. 示例描述框层次结构</h4>

Table 2 shows the hierarchy within the Sample Description box.

``` javascript
Box Name	                        Required?                       Short Description
stsd                              Y	                              Sample descriptions
    Mediatype-specific
    sample entry boxes            Y 除加密外                        此track的示例说明
    rtmp                          Y for HTTP streaming            Adobe MUX提示示例条目
        amhp                      Y	                              Adobe MUX提示过程
        amto                      N	                              Adobe Mux时间偏移
    encv
    enca
    encr                          Y for encryption                加密track的示例说明条目
        sinf                      Y	                              保护方案信息
            frma                  Y	                              原始格式
            schm                  Y	                              方案Type
            schi                  Y	                              方案信息
              adkm                Y                             	Adobe的DRM密钥管理系统
                ahdr              Y	                              Adobe DRM标题
                  aprm            Y	                              标准加密参数
                    aeib          Y	                              加密信息
                    akey          Y	                              关键信息
                      aps         N	                              frms v1.x参数
                      flxs        N	                              闪存访问v 2.0参数
                  asig            N	                              Adobe 签名
                adaf              Y	                              Adobe DRM 访问单元格式
```

<h4 id="a1-6">1.6. 处理不支持的boxes</h4>

ISO规范ISO/IEC 14496-12:2008和Apple QuickTime规范定义了本规范中未包含的其他盒子类型。这些框类型不是f4v文件格式的一部分，f4v播放器不需要支持它们。F4V播放器应忽略不支持的盒子及其内容，并继续播放文件。
<h4 id="a1-7">1.7. 排序</h4>

为了获得最佳的播放器性能，F4V文件中所需的顶级框应按以下顺序排列：

1.	File Type (ftyp),

2.	Movie (moov),

.	Media Data (mdat).

FTYP箱应位于MOOV和MDAT箱以及任何其他“重要”可变尺寸箱之前。ftyp框应该是文件中的第一个框，或者尽可能早地位于文件中。

虽然Flash播放器可以同时播放MOOV和MDAT盒的订单，但MOOV盒应始终位于MDAT盒之前，因为这样可以更快地启动和进行流式处理。

许多f4v文件创建工具以次优的顺序放置方框以便播放，在这种情况下，应应用后处理步骤以推荐的顺序放置方框。

有关HTTP流支持所需的框和框顺序，请参阅附录C.HTTP流：文件结构。
<h4 id="a1-8">1.8. Supported Media Types</h4>

下表描述了可以封装在F4V文件中的媒体类型。
<h5 id="a1-8-1">1.8.1. Supported audio types</h5>

``` javascript
Media type      	Comments
MP3               媒体类型.mp3（0x2e6d7033）表示曲目包含MP3音频数据。
                  点号字符hex 0x2e用于生成完整的四字符代码。
AAC               媒体类型MP4A（0x6D703461）表示该曲目使用AAC音频编码。
                  Flash播放器支持以下AAC配置文件，由它们的对象类型表示：
-	1 = main profile
-	2 = low complexity, a.k.a. LC
-	5 = high efficiency/scale band replication, a.k.a. HE/SBR
```


当音频编解码器为AAC时，示例表的STSD框中会出现一个ESDS框。此框包含AAC解码器解码流所需的初始化数据。有关此框结构的更多信息，请参见ISO/IEC 14496-3。
<h5 id="a1-8-2">1.8.2. Supported video types</h5>

``` javascript
Media type	         Comments
GIF                  媒体类型的gif（0x67696620）表示使用CompuServe gif格式压缩的视频数据的静止帧。
                    包括空格字符hex 0x20，以生成完整的四字符代码。
  
PNG                 PNG的媒体类型（0x706E6720）表示使用标准PNG格式压缩的视频数据的静止帧。
                    包括空格字符hex 0x20，以生成完整的四字符代码。

JPEG                jpeg（0x6a706567）的媒体类型表示使用标准jpeg格式压缩的视频数据的静止帧。

H.264               h264（0x48323634）、h264（0x68323634）或avc1（0x61766331）
                    的媒体类型表示该曲目使用H.264视频编码。Flash播放器支持以下H.264视频配置文件：

-	0 = 支持忽略设置配置文件的旧媒体
-	66 = baseline
-	77 = extended
-	88 = main
-	100 = YUV 4:2:0, 8 bits/sample, a.k.a. “High”
-	110 = YUV 4:2:0, 10 bits/sample, a.k.a. “High 10”
-	122 = YUV 4:2:2, 10 bits/sample, a.k.a. “High 4:2:2”
-	144 = YUV 4:4:4, 12 bits/sample, a.k.a. “High 4:4:4”
当视频编解码器为H.264时，在示例表的STSD框中会出现一个AVCC框。此框包含H.264解码器解码流所需的初始化数据。
BoxHeader后面的字节1和3分别包含AVC数据的概要文件和级别。有关AVCC箱其余部分的更多信息，
请参见ISO/IEC 14496-15第5.3.4.1节。

VP6                 以下媒体类型表示该曲目是用on2 vp6视频编码的。-VP6F（0x56503646）

-	VP6A (0x56503641)
-	VP60 (0x56503630)
-	VP61 (0x56503631)
-	VP62 (0x56503632)
```

<h5 id="a1-8-3">1.8.3. Supported data types</>

``` javascript
Media type       	Comments
Text              文本（0x74657874）或tx3g（0x74783367）
                  的媒体类型表示曲目包含通过ActionScript提供的文本数据。
AMF0              媒体类型amf0（0x616d6630）表示该曲目包含与操作脚本消息格式
                  （amf）的原始版本相对应的数据。
AMF3              媒体类型amf3（0x616d6633）表示该曲目包含与操作脚本消息格式（amf）版本3对应的数据。
```

<h3 id="a2">2. F4V Box Definitions</h3>

定义了f4v文件格式支持的框。
<h4 id="a2-1">2.1. File Type box</h4>

Box type: 'ftyp' 

Container: File 

Mandatory: Yes 

Quantity: One

f4v格式基于iso mpeg4格式，而iso mpeg4格式又基于Apple QuickTime容器格式。格式的子集支持不同的功能。文件类型（ftyp）框有助于识别程序播放特定文件所需支持的功能。

FTYP箱应尽早放置，并应位于任何可变长度箱之前。

flash player对ftyp框没有任何限制。如果文件包含Flash播放器可以解码的数据，Flash播放器会尝试播放。

ftyp box

``` javascript
Field	                Type           Comments
Header              	BOXHEADER	     BoxType = 'ftyp' (0x66747970)
MajorBrand            UI32          主要品牌标识符。对于f4v文件，majorbrand是'f4v'（0x66347620）。

MinorVersion          UI32          Minorversion仅提供信息。不得用于确定文件是否符合标准。
                                    它可能允许更精确地识别主要品牌，以便进行检查、调试或改进解码。
                                    compatible brands ui32[]任意数量的兼容品牌，直到包装盒结束
```


For more information, see section 4.3 of ISO/IEC 14496-12:2008.
<h4 id="a2-2">2.2. 渐进式下载信息框</h4>

Box type: 'pdin' 

Container: File 

Mandatory: No 

Quantity: One

渐进式下载信息（PDIN）框定义有关渐进式下载的信息。pdin框的有效负载提供了一些提示，提示玩家在安全开始播放之前要下载多少数据。

pdin框应尽可能早地放置在文件中，位于文件类型（ftyp）框之后，以获得最大的实用性。

pdin box

``` javascript
Field	          Type	        Comment
Header          BOXHEADER	    BoxType = 'pdin' (0x7064696E)
Version         UI8	          Expected to be 0
Flags           UI24	        Reserved. Set to 0
RateDelay       RATEDELAY[]	  任意数量的RATEDLAY记录，直到方框结束
Rate            UI32	        此记录要考虑的速率（字节/秒）
InitialDelay    UI32	        以此速率开始播放之前要延迟的毫秒数
```


For more information, see section 8.1.3 of ISO/IEC 14496-12:2008.
<h4 id="a2-3">2.3.  Movie box</h4>

Box type: 'moov' 

Container: File 

Mandatory: Yes 

Quantity: One

Movie（moov）框实际上是一个f4v文件的“头”。MOOV框本身包含一个或多个其他框，而其他框又包含定义F4V数据结构的其他框。

moov box

``` javascript
Field	      Type        	Comment
Header	    BOXHEADER	    BoxType = 'moov' (0x6D6F6F76)
Boxes	      BOX [ ]	      定义文件结构的任意框数
```


For more information, see section 8.2.1 of ISO/IEC 14496-12:2008.
<h4 id="a2-4">2.4. Movie Header box</h4>

Box type: 'mvhd' 

Container: Movie box ('moov') 

Mandatory: Yes 

Quantity: One

Movie标题（MVHD）框定义应用于整个F4V文件的播放信息。MVHD箱应首先放置在其容器中。

mvhd box

``` javascript
Field     	    Type	              Comment
Header	        BOXHEADER	          BoxType = 'mvhd' (0x6D766864)
Version	        UI8	                Either 0 or 1
Flags           UI24	              Reserved. Set to 0
CreationTime    IF Version == 0     f4v文件的创建时间，
                UI32                表示为自1904年1月1日午夜（UTC）以来经过的秒数。
                IF Version == 1
                UI64

ModificationTime	IF Version == 0   f4v文件的最后一次修改时间，
                UI32                表示为自1904年1月1日午夜（UTC）以来经过的秒数。
                IF Version == 1
                UI64
TimeScale       UI32                整个f4v文件的时间坐标系，以每秒的时间单位数表示。
                                    例如，100表示时间单位为1/100秒。
Duration        IF Version == 0
                UI32                以时间刻度单位表示的F4V文件的总长度
                IF Version == 1     也是文件中最长曲目的持续时间。
                UI64
Rate            SI16.16             首选播放速率，以固定点16.16数字表示
                                    （通常为0x00010000=1.0，或正常播放速率）
Volume          SI8.8               文件的主卷，表示为定点8.8号
                                    （通常为0x0100=1.0，或完整卷）。
Reserved        UI16	              Reserved. Set to 0
Reserved        UI32 [2]	          Reserved. Set to 0
Matrix          SI32 [9]            f4v文件的转换矩阵应为:
                                    {0x00010000, 0, 0,
                                    0, 0x00010000, 0,
                                    0, 0, 0x40000000}
Reserved        UI32 [6]	          Reserved. Set to 0
NextTrackID     UI32                要添加到演示文稿的下一个track的ID。
                                    该值不应为0，但可以全部为1，以表示未定义状态。
```


For more information, see section 8.2.2 of ISO/IEC 14496-12:2008.
<h4 id="a2-5">2.5. Track box</h4>

Box type: 'trak' 

Container: Movie box ('moov') 

Mandatory: Yes 

Quantity: One or more

每个trak框对应于f4v文件中的单个媒体曲目，并包含进一步定义媒体曲目属性的框。

trak box

``` javascript
Field	          Type               Comment
Header	        BOXHEADER	         BoxType = 'trak' (0x7472616B)
Boxes	          BOX [ ]	           定义媒体轨的任意框数
```


For more information, see section 8.3.1 of ISO/IEC 14496-12:2008.
<h5 id="a2-5-1">2.5.1. Track Header box</h5>

Box type: 'tkhd' 

Container: Movie box ('trak') 

Mandatory: Yes 

Quantity: One

The Track Header （TKHD）框描述磁道的主要属性。TKHD箱应首先放在其容器中。

tkhd box

``` javascript
Field	            Type	            Comment
Header	          BOXHEADER	        BoxType = 'tkhd' (0x746B6864)

Version	          UI8	              Either 0 or 1
Flags             UI24              Bit 0 = 开启
                                    Bit 1 = the track is part of the presentation
                                    Bit 2 = the track should be considered when previewing the F4V file
CreationTime      IF Version == 0
                  UI32              轨道的创建时间
                  IF Version == 1
                  UI64
ModificationTime	IF Version == 0
                  UI32              轨道的最后修改时间
                  IF Version == 1
                  UI64
TrackID	          UI32	            track唯一标识符
Reserved	        UI32	            Reserved. Set to 0
Duration          IF Version == 0
                  UI32              音轨的持续时间
                  IF Version == 1
                  UI64

Reserved          UI32 [2]	        Reserved. Set to 0
Layer             SI16              track前后排序的位置，对于f4v文件，预计为0。
AlternateGroup	  SI16	            0
Volume            SI8.8             音频轨为0x0100（定点8.8，数字代表1.0），否则为0
Reserved          UI16	            Reserved. Set to 0
TransformMatrix	  SI32[9]           定义透视变换的固定点值矩阵，应
                                    {0x00010000, 0, 0
                                    0, 0x00010000, 0
                                    0, 0, 0x40000000}
Width             UI16.16	          以固定点表示的宽度16.16数字
Height	          UI16.16	          以固定点表示的高度16.16数字
```


For more information, see section 8.6.5 of ISO/IEC 14496-12:2008.
<h5 id="a2-5-2">2.5.2. Edit box</h5>

Box type: 'edts' 

Container: Track box ('trak') 

Mandatory: No 

Quantity: One

编辑（EDTS）框将演示时间线映射到媒体时间线，因为它存储在F4V文件中。EDTS框是编辑列表的容器。

如果文件不包含EDTS框，则会隐式地对这些时间线进行一对一映射。EDTS框应位于媒体（MDIA）框之前。

edit box

``` javascript
Field	            Type	          Comment
Header            BOXHEADER	      BoxType = 'edts' (0x65647473)
Edit list box	    BOX	            An explicit time-line map
```


For more information, see section 8.6.5 of ISO/IEC 14496-12:2008.
<h5 id="a2-5-2-1">2.5.2.1. Edit List box</h5>

Box type: 'elst' 

Container: Edit box ('edts') 

Mandatory: No 

Quantity: One

tkhd box

编辑列表（ELST）框以编辑列表条目的形式包含显式时间线图。ELST框中的每个条目通过以下方式之一定义了演示时间线的一部分：

-通过映射媒体时间表的一部分

-通过指示空时间（即间隙）

-通过定义停留时间（即一个时间点停留一段时间的位置）

编辑列表框中的空编辑表示间隙。若要指定轨迹的起始偏移，请在轨迹的起始处插入空的编辑。

空编辑不应是曲目中的最后一个编辑。如果在“影片标题”框中指定的持续时间与曲目的实际持续时间不同，则会在曲目末尾放置一个隐式空编辑。

媒体应该在间隙后立即有一个关键帧。另外，flash播放器可以使用同步采样框中的数据在间隙后查找关键帧。

elst box

``` javascript
Field	              Type	            Comment
Header              BOXHEADER         BoxType = 'elst' (0x656C7374)
Version	            UI8               Either 0 or 1
Flags	              UI24	            Reserved. Set to 0
EntryCount  	      UI32	            Number of entries in the edit list entry table
EditListEntryTable	ELSTRECORD 
                    [EntryCount]	    An array of ELSTRECORD structures
SegmentDuration	    IF Version == 0
                    UI32              此编辑段的持续时间
                    IF Version == 1
                    UI64
MediaTime           IF Version == 0
                    SI32              此编辑段媒体中的开始时间作为合成时间，
                    IF Version == 1   以MDHD中定义的时间刻度单位表示？盒子。值-1指定空编辑。
                    SI64
MediaRateInteger	  SI16              播放此编辑段媒体的相对速率。默认值为1。值为0表示停留编辑。
MediaRateFraction	  SI16	            Reserved. Set to 0
```


For more information, see section 8.6.6 of ISO/IEC 14496-12:2008.
<h4 id="a2-6">2.6. Media box</h4>

Box type: 'mdia' 

Container: Track box ('trak') 

Mandatory: Yes 

Quantity: One

tkhd box

媒体（MDIA）框包含定义媒体曲目属性的框

mdia box

``` javascript
Field	            Type	          Comment
Header            BOXHEADER	      BoxType = 'mdia' (0x6D646961)
Edit list box	    BOX[]	          定义媒体跟踪属性的任意数量的框
```


For more information, see section 8.4 of ISO/IEC 14496-12:2008.
<h5 id="a2-6-1">2.6.1. Media Header box</h5>

Box type: 'mdhd' 

Container: Media box ('mdia') 

Mandatory: Yes 

Quantity: One

媒体标题（MDHD）框描述媒体曲目的属性。MDHD箱应首先放在其容器中。

mdhd box

``` javascript
Field	            Type	          Comment
Header	          BOXHEADER	      BoxType = 'mdhd' (0x6D646864)
Version     	    UI8	            Either 0 or 1
Flags	            UI24          	Reserved. Set to 0
CreationTime      IF Version == 0
                  UI32              The creation time of the box
                  IF Version == 1
                  UI64
ModificationTime	IF Version == 0
                  UI32              The last modification time of the box
                  IF Version == 1
                  UI64
TimeScale         UI32            此轨道的时间坐标系，以每秒的时间单位数表示
Duration          IF Version == 0
                  UI32             The total duration of this track
                  IF Version == 1
                  UI64
Pad	              UI1	            Padding, set to 0
Language          UI5 [3]         3字符代码指定语言（见ISO 639-2/T），
                                  每个字符解释为0x60+（5位）代码，以生成一个ASCII字符
Reserved	        UI16	          Reserved. Set to 0
```


For more information, see section 8.4.2 of ISO/IEC 14496-12:2008.


<h5 id="a2-6-2">2.6.2. Handler Reference box</h5>

Box type: 'hdlr' 

Container: Media box ('mdia') 

Mandatory: Yes 

Quantity: One

处理程序引用（HDLR）框声明跟踪中媒体数据的性质。hdlr框应位于媒体信息（minf）框之前。

hdlr box

``` javascript
Field	          Type	          Comment
Header          BOXHEADER	      BoxType = 'hdlr' (0x68646C72)
Version         UI8             Expected to be 0
Flags           UI24            Reserved. Set to 0.
Predefined      UI32            Set to 0.
HandlerType     UI32            包含以下4cc值的整数：“vide”=视频轨
                                'soun'=音频轨'data'=数据轨'hint'=提示轨
                                其他轨道类型将被忽略。
Reserved        UI32 [3]        Set to 0.
Name            String          以空结尾的命名跟踪类型的utf-8字符串，用于调试。
```


For more information, see section 8.4.3 of ISO/IEC 14496-12:2008.


<h4 id="a2-7">2.7. Media Information box</h4>

Box type: 'minf' 

Container: Media box ('mdia') 

Mandatory: Yes 

Quantity: One

媒体信息（minf）框包含定义曲目媒体信息的框。

minf框包含一个媒体头框，其类型与曲目的handlerType相对应。

minf box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'minf' (0x6D696E66)
Boxes         BOX [ ]         定义track媒体信息的任意框数
```


For more information, see section 8.4.3 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-1">2.7.1. Video Media Header box</h5>

Box type: 'vmhd' 

Container: Media Information box ('minf')  

Mandatory: Yes for a video track, otherwise no.  

Quantity: One for a video track, otherwise zero.

视频媒体标题（vmhd）框包含视频媒体的一般信息，与所使用的编码无关。vmhd箱应首先放在其容器中。

vmhd box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'vmhd' (0x766D6864)
Version       UI8             Expected to be 0
Flags         UI24            Set to 1.
GraphicsMode  UI16            视频轨的合成模式。默认值为0，这意味着复制现有图像。
OpColor       UI16 [3]        图形模式使用的一组3个RGB颜色值。默认值：（0，0，0）
```


For more information, see section 8.4.5.2 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-2">2.7.2. Sound Media Header box</h5>

Box type: 'smhd' 

Container:Media Information box ('minf') 

Mandatory: Yes for an audio track, otherwise no.  

Quantity: One for an audio track, otherwise zero.

声音媒体标题框包含音频媒体的一般信息，与所使用的编码无关。SMHD盒应首先放在其容器中。

smhd box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'smhd' (0x736D6864)
Version       UI8             Expected to be 0.
Flags         UI24            Set to 0.
Balance       SI8.8           固定点8.8号。将单声道音频曲目映射到立体声空间，如下所示：
                              -1.0=完全向左
                              0 =中心
                              1.0=完全正确
Reserved      UI16            Set to 0.
```


For more information, see section 8.4.5.3 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-3">2.7.3. Hint Media Header box</h5>

Box type: 'hmhd' 

Container: Media Information box ('minf') 

Mandatory: Yes for a hint track, otherwise no. 

Quantity: One for a hint track, otherwise zero.

提示媒体头（hmhd）框包含提示跟踪的一般信息，与所使用的协议无关。HMHD箱应首先放在其容器中。

hmhd box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'hmhd' (0x686D6864)
Version       UI8             Expected to be 0.
Flags         UI24            Set to 0.
MaxPDUSize    UI16            Size (in bytes) of the largest PDU in hint stream.
AvgPDUSize    UI16            Average size (in bytes) of a PDU in entire presentation
MaxBitRate    UI32            Maximum rate (in bits per second) over an interval of 1 second.
AvgBitRate    UI32            Average rate (in bits per second) over entire presentation.
Reserved      UI32            Set to 0.
```


For more information, see section 8.4.5.4 of ISO/IEC 14496-12.


<h5 id="a2-7-4">2.7.4. Null Media Header box</h5>

Box type: 'nmhd' 

Container:Media Information box ('minf') 

Mandatory: Yes for metadata track, otherwise no. 

Quantity: Zero for video, audio, and hint tracks, otherwise, one.

空媒体标题（NMHD）框包含视频和音频以外的曲目的常规信息。NMHD箱应首先放置在其容器中。

nmhd box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'nmhd' (0x6E6D6864)
Version       UI8             Expected to be 0.
Flags         UI24            Reserved. Set to 0
```


For more information, see section 8.4.5.5 of ISO/IEC 14496-12.


<h5 id="a2-7-5">2.7.5. Data Information box</h5>

Box type: 'dinf' 

Container: Media Information box ('minf') 

Mandatory: Yes 

Quantity: One

数据信息（DINF）框包含一个数据引用（DREF）框，用于声明媒体数据在某个磁道中的位置。DINF框应位于示例表（STBL）框之前。

dinf box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'dinf' (0x64696E66)
Data          Reference box   BOX	Table of data references, used to locate media data.
```


For more information, see section 8.7.1 of ISO/IEC 14496-12.


<h5 id="a2-7-6">2.7.6. Sample Table box</h5>

Box type: 'stbl' 

Container:Media Information box ('minf') 

Mandatory: Yes 

Quantity: One

“示例表”（STBL）框包含一些框，用于定义组成轨迹的示例的属性。

STBL框中的框应按以下顺序排列：样本描述（STSD）、解码时间到样本（STTS）、样本到块（STSC）、样本大小（STSZ）、块偏移（STCO或CO64）。

样本描述（STSD）框及其包含的框在第2.8节样本描述框结构中进行了规定。

stbl box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'stbl' (0x7374626C)
Boxes         BOX [ ]        定义轨道组成样本属性的任意数量的框。
```


For more information, see section 8.5 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-6-1">2.7.6.1. Decoding Time to Sample box</h5>

Box type: 'stts' 

Container:Sample Table box ('stbl') 

Mandatory: Yes 

Quantity: One

解码采样时间（STTS）框定义采样表的采样映射时间

stts box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'stts' (0x73747473)
Version       UI8             Expected to be 0
Flags         UI24            Reserved. Set to 0
Count         UI32            The number of STTSRECORD entries
Entries       STTSRECORD 
              [Count]         An array of STTSRECORD structures

Each STTSRECORD has the following format: 
STTSRECORD
SampleCount   UI32            此sttsrecord应用于的连续样本数
SampleDelta   UI32            Sample duration in TimeScale units defined in the mdhd box
```


For more information, see section 8.6.1.2 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-6-2">2.7.6.2. Composition Time to Sample box</h5>

Box type: 'ctts' 

Container:Sample Table box ('stbl') 

Mandatory: No 

Quantity: One

合成时间到样本（CTTS）框定义了样本表的合成时间到样本映射

ctts box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'ctts' (0x63747473)
Version       UI8             Expected to be 0
Flags         UI24            Reserved. Set to 0
Count         UI32            The number of CTTSRECORD entries
Entries       CTTSRECORD 
              [Count]         An array of CTTSRECORD structures

Each STTSRECORD has the following format: 
STTSRECORD
SampleCount     UI32          The number of consecutive samples that this CTTSRECORD applies to
SampleOffset    UI32          对于sampleCount字段指定的每个样本，此
                              字段包含一个正整数，它以MDHD框中定义的时间刻度单位指定解码时间的合成偏移量。
```


For more information, see section 8.6.1.3 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-6-3">2.7.6.3. Sample to Chunk box</h5>

Box type: 'stsc' 

Container: Sample Table box ('stbl') 

Mandatory: Yes 

Quantity: One

“采样到块（STSC）”框定义媒体轨采样表中的采样到块映射。

stsc box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'stsc' (0x73747363)
Version       UI8             Expected to be 0
Flags         UI24            Reserved. Set to 0
Count         UI32            The number of STSCRECORD entries
Entries       STSCRECORD 
              [Count]         An array of STSCRECORD structures

Each STTSRECORD has the following format: 
STTSRECORD
FirstChunk          UI32          The first chunk that this record applies to
SamplesPerChunk     UI32          The number of consecutive samples that this record applies to
SampleDescIndex     UI32          The sample description that describes this sequence of chunks
```


For more information, see section 8.7.4  of ISO/IEC 14496-12:2008.


<h5 id="a2-7-6-4">2.7.6.4. Sample Size box</h5>

Box type: 'stsz' 

Container:Sample Table box ('stbl') 

Mandatory: No 

Quantity: One

“样本大小”（STSZ）框指定样本表中每个样本的大小。

tkhd box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'stsz' (0x7374737A)
Version       UI8             Expected to be 0
Flags         UI24            Reserved. Set to 0
ConstantSize  UI32            如果所有样本的大小相同，则此字段将使用该常量大小设置，否则为0。
SizeCount     UI32	          The number of samples in the track
SizeTable     IF ConstantSize == 0
              UI32 [SizeCount]    样本量表。如果constantsize不是0，则此表为空
```


For more information, see section 8.7.3.2 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-6-5">2.7.6.5. Chunk Offset box</h5>

Box type: 'stco' or 'co64'

Container:Sample Table box ('stbl') 

Mandatory: Yes 

Quantity: One

每个样本表框应包含一个STCO或CO64类型的区块偏移框。STCO和CO64框为示例表中的每个块定义块偏移量。

stco and co64 boxes

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'stco' (0x7374636F) or 'co64' (0x636F3634)
Version       UI8             Expected to be 0
Flags         UI24            Reserved. Set to 0
OffsetCount   UI32            The number of offsets in the Offsets table
Offsets       IF BoxType == 'stco’
              UI32 [OffsetCount]    文件中的绝对块偏移量表
              ELSE IF BoxType == 'co64’
              UI64 [OffsetCount]
```


For more information, see section 8.7.5 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-6-6">2.7.6.6. Sync Sample box</h5>

Box type: 'stss' 

Container:Sample Table box ('stbl') 

Mandatory: No 

Quantity: One

同步样本（STSS）框指定样本表中的哪些样本是同步样本。同步样本是可以安全查找的样本。如果轨迹是视频轨迹，则同步采样是不依赖任何其他帧的任何数据的关键帧或帧内帧。

如果样本表（STBL）框不包含STSS框，则轨道中的所有样本均应视为同步样本。

stss box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'stss' (0x73747373)
Version       UI8             Expected to be 0
Flags         UI24            Reserved. Set to 0
SyncCount     UI32            The number of entries in SyncTable
SyncTable     UI32            同时也是同步样本的样本数表，按样本数的升序
              [SyncCount]
```


For more information, see section 8.6.2 of ISO/IEC 14496-12:2008.


<h5 id="a2-7-6-7">2.7.6.7. Independent and Disposable Samples box</h5>

Box type: 'sdtp' 

Container: Sample Table box ('stbl') or Track Fragment 'traf 

Mandatory: No 

Quantity: One in each of the stbl and traf boxes

一个STBL或TRAF盒可分别包含一个独立的一次性样品盒（SDTP）。SDTP盒有助于实现诸如快进和随机访问等功能。sdtp框说明样本是否为i-picture，并提供有关样本中存在的帧依赖性和冗余编码的信息。表中的条目数与“样本大小”框中的sampleCount值相同。

stss box

``` javascript
Field	              Type	                  Comment
Header              BOXHEADER               BoxType = 'sdtp' (0x73647470)
Version             UI8                     Expected to be 0
Flags               UI24                    Reserved. Set to 0
SampleDependency    SAMPLEDEPENDENCY []     每个示例的依赖关系信息，到框的末尾

Each SAMPLEDEPENDENCY record has the following structure: 
SAMPLEDEPENDENCY
Reserved            UI2                     Reserved. Set to 0.
SampleDependsOn     UI2
                                            0 = the sample dependency is unknown
                                            1 = this sample does depend on others (not an I picture)
                                            2 = this sample does not depend on others (I picture)
                                            3 = reserved
SampleIsDependedOn	UI2
                                            0 = the dependency of other samples on this sample is unknown
                                            1 = other samples may depend on this one (not disposable)
                                            2 = no other sample depends on this one (disposable)
                                            3 = reserved
SampleHasRedundancy  UI2
                                            0 = it is unknown whether there is redundant coding in this sample
                                            1 = there is redundant coding in this sample
                                            2 = there is no redundant coding in this sample
                                            3 = reserved
```


当存在冗余编码时，“sampleDependson”的值仅对应于主编码。参数“sampleisdependedon”独立于冗余编码的存在。

For more information, see section 8.6.4 of ISO/IEC 14496-12:2008.
<h4 id="a2-8">2.8. Sample Description Box Structure</h4>
<h5 id="a2-8-1">2.8.1. Sample Description box</h5>

Box type: 'stsd' 

Container:Sample Table box ('stbl') 

Mandatory: Yes 

Quantity: One

“示例说明”（STSD）框定义示例表的示例说明。STSD框可以包含一个曲目的多个描述，每个媒体类型包含一个描述。示例描述表提供了有关所用编码类型的详细信息，以及该编码所需的任何初始化信息。

表2显示了示例描述框中的层次结构。有关更多信息，请参见ISO/IEC 14496-12第8.5.2节。

stsd box

``` javascript
Field	               Type	                   Comment
Header              BOXHEADER               BoxType = 'stsd' (0x73747364)
Version             UI8                     Expected to be 0
Flags               UI24                    Reserved. Set to 0
Count               UI32
Number of entries, one for each media type contained in the track
Descriptions        DESCRIPTIONRECORD
                    [Count]                 一组框，每个框对应包含在曲目中的媒体类型。
```


Each DESCRIPTIONRECORD shall be one of the following boxes:

-	VisualSampleEntry, for HandlerType == 'vide' [video track],

-	AudioSampleEntry, for HandlerType == 'soun' [audio track],

-	MetaDataSampleEntry, for HandlerType == 'meta' [timed metadata track],

-	SampleEntry, for HandlerType == 'data' [data track],

-	HintSampleEntry, for HandlerType == 'hint' [hint track], or

-	AdobeMuxHintSampleEntry, for HandlerType == 'hint' [Adobe Multiplexed Hint Track]
<h5 id="a2-8-2">2.8.2. VisualSampleEntry box</h5>

Box type: one of the video media types specified in Section 1.8.2 Supported video types 

Container:Sample Table box ('stsd') 

Mandatory: Yes for video tracks 

Quantity: One for each video track

VisualSampleEntry框包含有关所用视频编码类型的详细信息，以及该编码所需的任何初始化信息。有关更多信息，请参见ISO/IEC 14496-12:2008第8.5.2节。

VisualSampleEntry box

``` javascript
Field	                 Type	          Comment
Header                 BOXHEADER      BoxType is one of the video media types specified in 1.8.2
Reserved               UI8 [6]        Set to 0
DataReferenceIndex     UI16	          用于检索与使用此示例说明的示例关联的数据引用的索引。数据引用存储在数据引用（DREF）框中。
Predefined             UI16           Set to 0
Reserved               UI16            Set to 0
Predefined             UI32 [3]       Set to 0
Width                 UI16            Max visual width (in pixels) from codec
Height                UI16            Max visual height (in pixels) from codec
HorizResolution       UI16.16         图像像素/英寸的分辨率，默认值为0x0048000（72 dpi）
VertResolution        UI16.16         图像像素/英寸的分辨率，默认值为0x0048000（72 dpi）
Reserved              UI32            Set to 0
FrameCount            UI16            每个样本中存储多少帧，默认值1（每个样本一帧）
CompressorName        UI8 [32]        压缩机名称（仅供参考）。第一个字节设置为第一个字节后可显示数据的字节数。
Depth                 UI16            比特深度。默认值0x0018（不带字母的颜色）
Predefined            SI16            Set to -1
Boxes                 BOX [ ]         为媒体类型或加密指定的附加框
```

<h5 id="a2-8-3">2.8.3. AudioSampleEntry box</h5>

Box type: one of the audio media types specified in Section 1.8.1 Supported audio types 

Container:Sample Table box ('stsd')  

Mandatory: Yes for audio tracks 

Quantity: One

audioSampleEntry框包含有关所用音频编码类型的详细信息，以及该编码所需的任何初始化信息。有关更多信息，请参见ISO/IEC 14496-12:2008第8.5.2节。

AudioSampleEntry box

``` javascript
Field	                Type	          Comment
Header                BOXHEADER       BoxType is one of the audio media types specified in 1.8.1
Reserved              UI8 [6]         Set to 0
DataReferenceIndex    UI16            用于检索与使用此示例说明的示例关联的数据引用的索引。数据引用存储在数据引用（DREF）框中。
Reserved              UI32 [2]        Set to 0
ChannelCount          UI16            频道数。默认值为2。
                                      1 = Mono
                                      2 = Stereo
SampleSize            UI16            Size of sample. Default value is 16
Predefined            UI16            Set to 0
Reserved              UI16            Set to 0
SampleRate            UI16.16         Sampling rate, fixed point 16.16 number
Boxes                 BOX [ ]         Additional boxes as specified for the media type, or encryption
```

<h5 id="a2-8-4">2.8.4. MetaDataSampleEntry box</h5>

Box type: depends on protocol used 

Container:Sample Table box ('stsd')  

Mandatory: Yes for metadata tracks 

Quantity: One for each metadata track

MetadataSampleEntry框包含有关所用元数据编码类型的详细信息，以及该编码所需的任何初始化信息。有关更多信息，请参见ISO/IEC 14496-12:2008第8.5.2节。

MetaDataSampleEntry box

``` javascript
Field	                  Type	          Comment
Header                  BOXHEADER       BoxType depends on protocol used
Reserved                UI8 [6          Set to 0
DataReferenceIndex      UI16            用于检索与使用此示例说明的示例关联的数据引用的索引。数据引用存储在数据引用（DREF）框中。
Data                    UI8 [ ]         Additional contents as specified in ISO/IEC 14496-12:2008
```

<h5 id="a2-8-5">2.8.5. SampleEntry box</h5>

Box type: one of the data media types specified in Section1.8.3 Supported data types 

Container:Sample Table box ('stsd') 

Mandatory: Yes for data tracks 

Quantity: One for each data track

sampleEntry框包含有关所用编码类型的详细信息，以及该编码所需的任何初始化信息。有关更多信息，请参见ISO/IEC 14496-12:2008第8.5.2节。

SampleEntry box

``` javascript
Field	                Type	          Comment
Header                BOXHEADER       BoxType is one of the data media types specified in Supported data types.
Reserved              UI8 [6]         Set to 0.
DataReferenceIndex	  UI16            用于检索与使用此示例说明的示例关联的数据引用的索引。数据引用存储在数据引用（DREF）框中。
Boxes	                BOX [ ]         Additional boxes as specified for the media type, or encryption
```


For more information, see section 8.aaaaaaaaaaaaaaaaaaaaaaaaaaa of ISO/IEC 14496-12:2008.


<h5 id="a2-8-6">2.8.6. HintSampleEntry box</h5>

Box type: 'hint' 

Container:Sample Table box ('stsd' 

Mandatory: Yes for hint tracks 

Quantity: One for each hint track

hintsampleEntry（提示）框包含正在使用的流协议的适当声明性数据以及提示轨的格式。有关更多信息，请参见ISO/IEC 14496-12:2008第8.5.2节。

HintSampleEntry box

``` javascript
Field	               Type	            Comment
Header               BOXHEADER         一般来说，boxType取决于使用的协议。在F4V文件中，只允许使用“提示”框类型。
                                       保留的ui8[6]设置为0
DataReferenceIndex	  UI16            用于检索与使用此示例说明的示例关联的数据引用的索引。数据引用存储在数据引用（DREF）框中。
Data                  UI8 [ ]         Arbitrary number of bytes until end of box
```

<h5 id="a2-8-7">2.8.7. Sample Descriptions for HTTP Streaming with Fragments</h5>
<h5 id="a2-8-7-1">2.8.7.1.  Adobe Mux Hint Sample Entry box</h5>

Box type: 'rtmp' 

Container:Sample Table box ('stsd') 

Mandatory: Yes for HTTP streaming support with F4V fragments 

Quantity: One for the hint track for HTTP streaming support with F4V fragments

Adobemux提示示例条目（rtmp）框描述了使用f4v片段的HTTP流中使用的提示轨。见附录C.HTTP流：文件结构。提示轨包含ADobemuxhintsamples。

rtmp box

``` javascript
Field	                  Type	                Comment
Header                  BOXHEADER             BoxType = 'rtmp' (0x72746D70)
Reserved                UI8 [6]               Set to 0
DataReferenceIndex      UI16                  用于检索与使用此示例说明的示例关联的数据引用的索引。数据引用存储在数据引用（DREF）框中。
HintTrackVersion        UI16                  正在使用的提示跟踪定义的版本。设置为1。
HighestCompatibleVersion	UI16                指定与旧版本的兼容性。
MaxPacketSize           UI16                  最大的Adobe多路复用提示跟踪示例包大小（字节）。
AdditionalData          BOX [ ]               一个Adobe MUX提示处理框和零个或一个Adobe MUX时间偏移框
```

<h5 id="a2-8-7-2">2.8.7.2. Adobe Mux Hint Process box</h5>

Box type: 'amhp' 

Container:Adobe Mux Hint Sample Entry ('rtmp') 

Mandatory: Yes 

Quantity: One

Adobe mux提示进程（amhp）框包含此曲目中使用的提示模式的描述

amhp box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'amhp' (0x616D6870))
Version       UI8             Expected to be 0
Flags         UI24            Reserved. Set to 0
ModeCount     UI8             此框中支持的模式配置数。此值也是相应提示轨中支持的模式数。
ENTRIES       MuxHintProcessEntry
              [ModeCount]         An array of MuxHintProcessEntry

Each MuxHintProcessEntry has the following format:
MuxHintProcessEntry
HintTrackMode           UI8         条目对应的模式（示例或立即模式）。有关模式的更多信息，请参阅附录C.5 Adobe多路提示跟踪格式。
TrailerLengthField      UI1         1表示此模式的adobemuxhintsample中存在trailerLength字段。如果为0，则使用trailerDefaultSize。
LengthField             UI1         1表示此模式的ADobemuxhintsample中存在提示样本长度字段。模式==2时，长度字段应为1。
ModeField               UI1         1表示此模式的ADobemuxhintsample中存在模式字段。当使用多种模式时，ModeField应为1。
ConstructorCountField   UI1         1表示此模式的adobEmuxHintSample中存在constructorCount字段。如果为0，则有一个构造函数
PacketCountField        UI1         1表示此模式的ADobemuxhintsample中存在packetcount字段。
Reserved                UI3         Set to 0
TrailerDefaultSize      UI8         提示示例有效负载之后拖车数据的默认大小（以字节为单位）。在没有TrailerLengthField时使用。
```


对于FLV兼容模式，trailerLengthField、lengthField、modeField、constructorCountField和packetCountField应为0。在这种情况下，使用即时节点提升提示模式。
<h5 id="a2-8-7-3">2.8.7.3. Adobe Mux Time Offset box</h5>

Box type: 'amto' 

Container:Adobe Mux Hint Sample Entry ('rtmp') 

Mandatory: No 

Quantity: One

Adobe mux time offset（amto）框存储此文件中第一个提示示例的时间戳

amto box

``` javascript
Field	        Type	          Comment
Header        BOXHEADER       BoxType = 'amto' (0x616D746F)
TimeOffset    UI32            文件中第一个提示示例的时间戳。
                              这个时间戳是从累计的样本持续时间中导出的每个样本的表示时间中添加的偏移量。
```

<h5 id="a2-8-8">2.8.8. Sample Descriptions for Protected Contents</h5>
<h5 id="a2-8-8-1">2.8.8.1. Encrypted Video box</h5>

Box type: 'encv' 

Container:Sample Table box ('stsd') 

Mandatory: Yes for encrypted video tracks 

Quantity: One for each encrypted video track

如附件D.2所述，加密视频（Encv）盒应为原始视觉样本，并附上“sinf”盒。有关更多信息，请参见ISO/IEC 14496-12:2008第8.12节。
<h5 id="a2-8-8-2">2.8.8.2. Encrypted Audio box</h5>

Box type: 'enca' 

Container: Sample Table box ('stsd')  

Mandatory:  Yes for encrypted audio tracks 

Quantity: One for each encrypted audio track

如附录D.2所述，加密音频（ENCA）盒应为原始音频采样中心，并附上“sinf”盒。有关更多信息，请参见ISO/IEC 14496-12:2008第8.12节。
<h5 id="a2-8-8-3">2.8.8.3. Encrypted Data box</h5>

Box type: 'encr' 
<
Container:Sample Table box ('stsd') 

Mandatory: Yes for encrypted video tracks 

Quantity: One for each encrypted video track

如附录D.2所述，加密数据（encr）箱应为原始样品中心，并附上“sinf”箱。有关更多信息，请参见ISO/IEC 14496-12:2008第8.12节。
<h5 id="a2-8-8-4">2.8.8.4. Protection Scheme Information box</h5>

Box type: 'sinf' 

Container:'encv' or 'enca' Sample Description Entry for the protected track in 'stsd' box 

Mandatory: Yes 

Quantity: One

保护方案信息（sinf）框是一个容器框，其中包含了解所应用的加密转换及其参数以及查找密钥管理系统类型和位置等其他信息所需的所有信息。它还记录了媒体的原始（未加密）格式。它应附加到具有四个字符代码（“encv”、“enca”或“encr”）的任何示例条目中，以指示受保护的流。

sinf box

``` javascript
Field	                  Type	                 Comment
Header                  BOXHEADER             BoxType = 'sinf'
OriginalFormatBox       OriginalFormatBox     The format of the original sample
SchemeTypeBox           SchemeTypeBox         The DRM type. Required. (This is optional in ISO 14496-12)
SchemeInformationBox    SchemeInformationBox  DRM details. Required. (This is optional in ISO 14496-12)
```


For more information, see section 8.12 of ISO/IEC 14496-12:2008.


<h5 id="a2-8-8-5">2.8.8.5. Original Format box</h5>

Box type: 'frma' 

Container:Protection Scheme Information box ('sinf')  

Mandatory: Yes 

Quantity: One

“原始格式”（FRMA）框指定原始样本的格式，例如，如果流包含受保护的MPEG-4视觉材料，“MP4V”。

frma box

``` javascript
Field	                        Type	                  Comment
Header                        BOXHEADER               BoxType = 'frma'
UnencryptedDataFormat         UI32                    原始未转换样本条目的四字符代码
```


For more information, see section 8.12.2 of ISO/IEC 14496-12:2008.


<h5 id="a2-8-8-6">2.8.8.6.  Scheme Type box</h5>

Box type: 'schm' 

Container: Protection Scheme Information box ('sinf') 

Mandatory: Yes 

Quantity: One

方案类型（schm）框指定用于管理密钥和内容解密的DRM系统。由于媒体文件格式可能支持除Adobe的DRM以外的其他密钥管理系统，因此使用中的密钥管理系统应在schemetype字段中以四字符代码（4cc）表示。

schm box

``` javascript
Field	        Type	            Comment
Header        BOXHEADER         BoxType = 'schm'
Version       UI8               Shall be 1
Flags         UI24              Shall be 0 or 1
SchemeType    UI32              方案类型。应为“adkm”，表示内容使用Adobe的DRM系统进行保护。
SchemeVersion	UI32              Shall be 1
SchemeUri     IF Flags == 1
              STRING
              Browser URI

```


For more information, see section 8.12.5 of ISO/IEC 14496-12:2008.


<h5 id="a2-8-8-7">2.8.8.7.  Scheme Information box</h5>

Box type: 'schi' 

Container:Protection Scheme Information box ('sinf') 

Mandatory: Yes 

Quantity: One

方案信息（Schi）框是一个包含DRM密钥/权限管理系统特定信息的容器框。对于Adobe的DRM，此框应包括一个Adobe DRM密钥管理系统框。可能还有其他盒子。为了与其他DRM的互操作性，Adobe DRM密钥管理系统框可以位于方案信息框中的任何位置。

schi box

``` javascript
Field	                      Type	          Comment
Header                      BOXHEADER       BoxType = 'schi'
OtherDRMSpecificData        BOX [ ]         （可选）包含其他DRM特定密钥管理信息的框
SchemeSpecificData          AdobeDRMKMSBox    Adobe DRM密钥管理系统框，指定密钥管理信息
OtherDRMSpecificData        BOX [ ]         （可选）包含其他DRM特定密钥管理信息的框
```


For more information, see section 8.12.6 of ISO/IEC 14496-12:2008.


<h5 id="a2-8-8-8">2.8.8.8. Boxes for Adobe's Protection Scheme</h5>

以下框由Adobe定义，未在ISO 14496-12:2008中记录。
<h5 id="a2-8-8-8-1">2.8.8.8.1.  Adobe DRM Key Management System box</h5>

Box type: 'adkm' 

Container:Scheme Information box ('schi') 

Mandatory: Yes 

Quantity: One

AdobeDRM密钥管理系统（ADKM）框指定加密和示例格式。

tkhd box

``` javascript
Field	         Type	             Comment
Header         BOXHEADER         BoxType = 'adkm'

Version        UI8               Shall be 1

Flags          UI24              Shall be 0

Header       AdobeDRMHeaderBox   AdobeDRM头框，指定如何检索密钥以及如何使用它来解密内容。
AUFormat	   AdobeDRMAUFormatBox	  Adobe DRM Access Unit格式框，指定每个示例预先准备的格式

```

<h5 id="a2-8-8-8-2">2.8.8.8.2. Adobe DRM Header box</h5>

Box type: 'ahdr' 

Container:Adobe DRM Key Management System box ('adkm') 

Mandatory: Yes 

Quantity: One

AdobeDRM头（ahdr）框指定加密格式和方法的版本。

ahdr box

``` javascript
Field	             Type	                      Comment
Header             BOXHEADER                   BoxType = 'ahdr'
Version            UI8                         Shall be 1 or 2, indicating the version of the encryption format.
                                              1 = FMRMS v1.x products.
                                              2 = Flash Access 2.0 products.
                                              使用任一版本保护的内容均已存在，因此应用程序应能够同时使用两个版本的内容。
Flags              UI24                        Shall be 0      
StdEncryptionBox	StandardEncryptionParamsBox	标准加密参数框，包含用于加密样本的加密方法属于“标准加密”类型。
Signature          IF Version == 1
                  AdobeSignatureBox           此文档中未描述AdobeSignatureBox
```

<h5 id="a2-8-8-8-3">2.8.8.8.3. Standard Encryption Params box</h5>

Box type: 'aprm' 

Container: Adobe DRM Header box ('ahdr') 

Mandatory: Yes 

Quantity: One

标准加密参数（APRM）框包含加密方法“标准”的参数。

tkhd box

``` javascript
Field	                          Type	                  Comment
Header                          BOXHEADER               BoxType = 'aprm'
Version                         UI8                     Shall be 1
Flags                           UI24                    Shall be 0
EncInfoBox                      EncryptionInfoBox       加密信息框，指定用于加密样本的加密算法
KeyInfoBox                      KeyInfoBox              密钥信息框，指定如何检索用于解密示例的密钥
```

<h5 id="a2-8-8-8-4">2.8.8.8.4. Encryption Information box</h5>

Box type: 'aeib' 

Container:标准加密参数框('aprm') 

Mandatory: Yes 

Quantity: One

加密信息（AEIB）框指定用于加密样本的加密算法。

aeib box

``` javascript
Field	                        Type	                  Comment
Header                          BOXHEADER               BoxType = 'aeib'
Version                         UI8                     Shall be 1
Flags                           UI24                    Shall be 0
EncryptionAlgorithm             STRING                  加密算法。应为“aes-cbc”，说明所使用的加密为“aes-cbc”，并根据RFC 2630进行填充。
KeyLength                       UI8                     加密/解密算法的密钥长度（字节）。应为16（即128位）
```

<h5 id="a2-8-8-8-5">2.8.8.8.5. Key Information box</h5>

Box type: 'akey' 

Container:Standard Encryption Params box ('aprm') 

Mandatory: Yes 

Quantity: One

密钥信息（akey）框包含用于检索用于解密样本的密钥的信息。

这些框中包含的条目的详细信息以及DRM客户机用于检索密钥的机制超出了本规范的范围。

akey box

``` javascript
Field	              Type	                         Comment
Header                BOXHEADER                      BoxType = 'akey'
Version               UI8                            Shall be 1
Flags                 UI24                           Shall be 0
Params      IF AdobeDRMHeaderBox.Version == 1  
                APSParamsBox                        本文件中未对apsparamsbox进行描述，因为它将不再由符合要求的应用程序生成。
            ELSE
                FMRMSv2ParamsBox
```

<h5 id="a2-8-8-8-6">2.8.8.8.6. Flash Access Params box</h5>

Box type: 'flxs' 

Container:Key Info box ('akey') 

Mandatory: Yes, if AdobeDRMHeaderBox.Version == 2, else No 

Quantity: One, if AdobeDRMHeaderBox.Version == 2, else Zero

flash access params（flxs）框包含用于检索用于解密样本的密钥的信息。

flxs box

``` javascript
Field	              Type	         Comment
Header                BOXHEADER      BoxType = 'flxs'
FmrmsV2Metadata       STRING        DRM客户端用于检索解密密钥的base64编码元数据
```

<h5 id="a2-8-8-8-7">2.8.8.8.7. Adobe DRM Access Unit Format box</h5>

Box type: 'adaf' 

Container:Key Info box ('adkm') 

Mandatory: Yes 

Quantity: One

访问单元格式（ADAF）框指定放置在样本上的头的格式。

tkhd box

``` javascript
Field	              Type	        Comment
Header                BOXHEADER     BoxType = 'tkhd'
Version               UI8           Shall be 1
Flags                 UI24          Shall be 0
SelectiveEncryption   UI1           指示使用选择性加密。应为1。
                                    1=启用选择性加密，即只加密部分样本，而不是全部样本。
                                    0=关闭选择性加密，并且对所有样本进行加密
Reserved              UI7           Shall be 0
Reserved              UI8           Shall be 0
IVLength              UI8           初始化向量的大小（字节）。该长度应与所用算法一致。应为16（128位）
```

<h3 id="a2-9">2.9.  Movie Extends box</h3>

Box type: 'mvex' 

Container:Movie box ('moov') 

Mandatory: No 

Quantity: One

如果f4v文件包含片段，则电影（moov）框包含一个电影扩展（mvex）框，否则没有mvex框。对于碎片，一个F4V文件应包含一个且仅一个MVEX盒。mvex框告诉读者此文件可能包含电影片段（moof）框。

mvex box

``` javascript
Field	        Type	          Comment
Header          BOXHEADER         BoxType = 'mvex' (0x6D766578)
Boxes           BOX [ ]           定义片段的轨迹默认值的框
```


For more information, see section 8.8.1 of ISO/IEC 14496-12:2008.


<h4 id="a2-9-1">2.9.1. Movie Extends Header box</h4>

Box type: 'mehd' 

Container:Movie Extends box ('mvex') 

Mandatory: No 

Quantity: One

Movie扩展头（mehd）框提供碎片Movie的持续时间。如果Movie扩展（mvex）框不包含mehd框，则通过检查所有片段来计算总持续时间。

mehd box

``` javascript
Field	                 Type	          Comment
Header                   BOXHEADER         BoxType = 'mehd' (0x6D766578)
Version                  UI8               Either 0 or 1
Flags                    UI24              Reserved. Set to 0
FragmentDuration        If Version==0
                            UI32            在mvhd框中定义的时间刻度单位中最长轨迹的持续时间
                        If Version ==1
                            UI64
```


For more information, see section 8.8.2 of ISO/IEC 14496-12:2008.


<h4 id="a2-9-2">2.9.2. Track Extends box</h4>

Box type: 'trex' 

Container:Movie Extends box ('mvex') 

Mandatory: Yes 

Quantity: One for each track in the Movie box

轨迹延伸”（Trex）框定义电影片段的默认值。

trex box

``` javascript
Field	                            Type	            Comment
Header                              BOXHEADER           BoxType = 'trex' (0x74726578)
Version                             UI8                 Expected to be 0
Flags                               UI24                Reserved. Set to 0
TrackID                             UI32                Identity of the associated track
DefaultSampleDescriptionIndex       UI32                要在跟踪片段中使用的默认sampleDescriptionIndex
DefaultSampleDuration               UI32                在跟踪片段中使用的默认sampleduration
DefaultSampleSize                   UI32                在track片段中使用的默认sampleSize
DefaultSampleFlags                  SAMPLEFLAGS         在跟踪片段中使用的默认sampleFlags

Each SAMPLEFLAGS record has the following layout: 
SAMPLEFLAGS

Reserved                            UI6                 Reserved. Set to 0.
SampleDependsOn                     UI2                 0 = 示例依赖项未知
                                                        1 = 此示例依赖于其他示例（不是i图片）
                                                        2 = 此示例不依赖于其他示例（图片）
                                                        3 = reserved
SampleIsDependedOn                  UI2                 0 = 其他示例对该示例的依赖性未知
                                                        1 = 其他样品可能依赖于此样品（非一次性样品）
                                                        2 = 其他样品不依赖于此样品（一次性）
                                                        3 = reserved
SampleHasRedundancy                 UI2                 0 = 尚不清楚此示例中是否存在冗余编码
                                                        1 = 此示例中存在冗余编码
                                                        2 = 此示例中没有冗余编码
                                                        3 = reserved
SamplePaddingValue                  UI3                 Reserved. Set to 0
SampleIsDifferenceSample            UI1                 0 = 密钥或同步示例
                                                        1 = 非键或非同步示例
SampleDegradationPriority           UI16                Reserved. Set to 1.
```


For more information, see section 8.8.3 of ISO/IEC 14496-12:2008.


<h4 id="a2-10">2.10. User Data box</h4>

Box type: 'udta' 

Container:Movie box ('moov') or Track box ('trak') 

Mandatory: No 

Quantity: One at each movie level or track level

用户数据（udta）框包含在电影（moov）框或曲目（trak）框中。最多，每个电影级别或曲目级别可能会出现一个UDTA框。UDTA框应最后放置在其包含框中。

UDTA框声明有关包含框及其数据（表示或跟踪）的自由格式用户信息。Flash播放器忽略UDTA框的内容。



udta box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'udta' (0x75647461)
UserData        BOX [ ]             具有自由格式用户数据的任意数量的框
```


For more information, see section 8.10.1 of ISO/IEC 14496-12:2008.


<h4 id="a2-11">2.11. F4V Boxes for HTTP Streaming</h4>
<h5 id="a2-11-1">2.11.2. Fragment Random Access box</h5>

Box type: 'afra' 

Container:File 

Mandatory: Yes for HTTP streaming support with F4V fragments, otherwise no. 

Quantity: One per fragment for HTTP streaming support with F4V fragments, otherwise zero.

片段随机访问（AFRA）框为一个或多个片段提供随机访问信息。

对于带有F4V片段的HTTP流支持，F4V文件可以为每个片段包含一个AFRA框。AFRA盒应位于片段的媒体数据（MDAT）和电影片段（MOOF）盒之前。AFRA框可用于查找包含给定时间内最近随机访问样本的F4V文件中的确切点。

AFRA框与给定片段关联（这里称为“关联片段”）。AFRA框还提供对同一段或不同段中其他片段中信息的随机访问。

AFRA框包含条目数组。每个条目包含随机访问样本的位置和表示时间。如果随机访问样本不在相关片段中，则条目还提供以下信息：

-段识别信息

-片段识别信息

-从包含段的开始到与此随机数关联的“afra”框的字节偏移量

接入点

-从关联的“afra”框到示例的字节偏移量
>片段中的每个随机访问样本不一定都有数组条目。AFRA盒的缺失并不意味着所有样本都是同步样本。“trun”、“traf”和“trex”中的随机访问信息设置适当，而不考虑此框的存在。

afra box

``` javascript
Field	                 Type	            Comment
Header                   BOXHEADER           BoxType = 'afra' (0x61667261)
Version                  UI8                 Either 0 or 1
Flags                    UI24                Reserved. Set to 0
LongIDs                  UI1                 控制全局框架项的段和片段字段的大小。
LongOffsets              UI1                 控制AfraEntry的偏移字段的大小。还控制全局框架项的afraOffset和offsetFromAfra字段的大小。
GlobalEntries            UI1                 值1表示存在GlobalRycount
Reserved                 UI5                 Set to 0
TimeScale                UI32                每秒在AfraEntry和GlobalAfraEntry的时间字段中使用的时间单位数。
EntryCount               UI32                LocalAccessEntries中的条目数
LocalAccessEntries      AFRAENTRY
                        [EntryCount]         随机访问此片段中的点。此数组不一定包含此片段中每个随机访问样本的条目。
GlobalEntryCount        IF GlobalEntries == 1 
                            UI32            GlobalAccessEntries中的条目数。如果globalentries==0，则此字段不存在，globalentrycount为0。
GlobalAccessEntries     GLOBALAFRAENTRY
                        [GlobalEntryCount]  随机访问此片段之外的点。

Each AFRAENTRY points to a sample within this fragment, and has the following format: 
AFRAENTRY

Time                    UI64                随机访问样本的表示时间，以时间刻度单位表示。
Offset              IF LongOffsets == 0
                        UI32                从片段随机访问框开始到样本的字节偏移量
                    ELSE
                        UI64
Each GLOBALAFRAENTRY points to a sample outside this fragment, and has the following format: 
GLOBALAFRAENTRY

Time                    UI64                 随机访问样本的表示时间，以时间刻度单位表示。
Segment             IF LongIDs == 0
                        UI16                包含此随机接入点的段的数目
                    ELSE
                        UI32
Fragment            IF LongIDs == 0
                        UI16                包含此随机访问点的片段的数目
                    ELSE
                        UI32
AfraOffset          IF LongOffsets == 0
                        UI32                从包含段开始到与此随机接入点关联的AFRA框的字节偏移量。
                    ELSE
                        UI64
OffsetFromAfra	IF LongOffsets == 0
                        UI32                从关联的AFRA框到样本的字节偏移量
                    ELSE
                        UI64
```

<h5 id="a2-11-2">2.11.2. Bootstrap Info box</h5>

Box type: 'abst' 

Container:File 

Mandatory: Yes for HTTP streaming support with F4V fragments, otherwise no. 

Quantity: One or more for HTTP streaming support with F4V fragments, otherwise zero.

引导信息（abst）框包含引导媒体演示URL从媒体客户端向HTTP服务器请求RFC1630所需的信息。媒体演示可以是现场演示，也可以是视频点播。此框包含有关服务器、电影和段信息的基本信息。它还包含一个或多个段运行表和段运行表。

在HTTP流段中，abt框是可选的，位于电影（moov）框之前。在HTTP流片段中，ABT框是必需的。有关HTTP流所需的框和结构的描述，请参阅附录C.HTTP流：文件结构。

abst box

``` javascript
Field	                Type	            Comment
Header                  BOXHEADER           BoxType = 'abst' (0x61627374)
Version                 UI8                 Either 0 or 1
Flags                   UI24                Reserved. Set to 0
BootstrapinfoVersion    UI32                引导信息的版本号。设置更新字段后，bootstrapinfoversion指示正在更新的版本号。
Profile                 UI2                 指示它是命名访问（0）还是范围访问（1）配置文件。为将来的配置文件保留一个位。
Live                    UI1                 指示媒体演示文稿是否为实时（1）。
Update                  UI1                 指示此表是引导程序框或文件的完整版本（0）还是以前定义（发送）的完整版本的更新（1）。
                                            更新不是完整的替换。它们只能包含更改的元素。服务器仅在引导信息更改时发送更新。
                                            更新应用于具有相同bootstrapinfoversion编号的完整版本。
                                            同一个bootstrapinfoversion号可能有多个更新。
                                            如果服务器发送多个更新，这些更新将应用于具有相同bootstrapinfoversion号的完整版本。
                                            每个更新都包括以前对同一个bootstrapinfoversion的所有更新。对于单个完整版本的多个更新，
                                            最新更新是基于当前的MediaTime确定的。

Reserved                UI4                 Reserved, set to 0
TimeScale               UI32                每秒的时间单位数。字段CurrentMediaTime使用此值表示准确的时间。通常，该值为1000，以毫秒为单位。

CurrentMediaTime        UI64                媒体演示文稿中最新可用片段的时间刻度单位时间戳。这个时间戳用于请求正确的片段号。
                                            CurrentMediaTime可以是总持续时间。对于非活动的媒体演示文稿，当前MediaTime可以为0。

SmpteTimeCodeOffset     UI64                当前媒体时间与smpte时间代码之间的偏移量，已转换为毫秒。
                                            此偏移量不是以时间刻度单位表示的。未使用时，此字段为零。
                                            服务器使用smpte时间代码模块24小时使偏移量为正。

MovieIdentifier         STRING              此演示文稿的标识符。标识符是以空结尾的UTF-8字符串。例如，它可以是URL中的文件名或路径名。见
                                            附录C.4 URL结构供使用

ServerEntryCount        UI8                 ServerEntryTable条目数。最小值为0。
ServerEntryTable        SERVERENTRY
                        [ServerEntryCount]  按优先顺序降序排列的服务器URL
QualityEntryCount       UI8                 QualityEntryTable条目数，也就是可用的质量级别数。最小值为0。
                                            可用的质量级别包括，例如，多比特率文件或技巧文件。

QualityEntryTable       QUALITYENTRY
                        [QualityEntryCount] 从高质量到低质量的质量文件参考
DrmData                 STRING              以NULL或NULL结尾的UTF-8字符串。此字符串包含数字版权管理元数据。
                                            加密文件使用此元数据获取解密和播放所需的密钥和许可证。
MetaData                STRING              包含元数据的以NULL或NULL结尾的UTF-8字符串
SegmentRunTableCount    UI8                 SegmentRunTableEntries中的条目数。最小值为1。通常，一个表包含所有段运行。
                                            但是，此计数提供了为每个质量级别（或技巧文件）单独定义段运行的灵活性。
SegmentRunTableEntries  SegmentRunTable
                    [SegmentRunTableCount]  Array of SegmentRunTable elements
FragmentRunTableCount       UI8             fragmentruntable中的条目数-条目数。最小值为1。
FragmentRunTableEntries   FragmentRunTable
                    [FragmentRunTableCount]     碎片可运行元素数组

Each SERVERENTRY has the following format: SERVERENTRY

ServerBaseURL           STRING               该服务器上此演示文稿的服务器基URL。该值是以空结尾的utf-8字符串，没有尾随的“/”。

Each QUALITYENTRY has the following format: QUALITYENTRY

QualitySegmentUrlModifier   STRING	         用于构造该质量媒体的正确URL的质量（段）文件的名称。
                                            该值是以空结尾的utf-8字符串，可以选择带有尾随的“/”。
```

<h5 id="a2-11-2-1">2.11.2.1. Segment Run Table box</h5>

Box type: 'asrt' 

Container:Bootstrap Info box ('abst')  

Mandatory: Yes 

Quantity: One or more

段运行表（ASRT）框可用于定位包含特定片段的段。可能有几个ASRT箱，每个箱的质量等级不同。

ASRT框使用压缩编码：

-段运行表可以表示几个质量级别的段运行。

-段运行表是紧凑编码的。每个条目给出一段运行的第一个段号

同样数量的碎片。具有相同片段计数的片段的计数可以通过从下一个条目中的第一个片段号减去此条目中的第一个片段号来计算。

asrt box

ASRT框使用压缩编码：

-段运行表可以表示几个质量级别的段运行。

-段运行表是紧凑编码的。每个条目给出一段运行的第一个段号

同样数量的碎片。具有相同片段计数的片段的计数可以通过从下一个条目中的第一个片段号减去此条目中的第一个片段号来计算。

``` javascript
Field                                Type	                    Comment
Header                               BOXHEADER                  BoxType = 'asrt' (0x61737274)
Version                              UI8                        Either 0 or 1
Flags                                UI24                       定义了以下值：
                                                                0=整张桌子。
                                                                1=此表中的记录是对先前定义的段运行表的更新（或要附加的新条目）。
                                                                当此标志为1时，包含引导信息框中的更新标志应为1。
QualityEntryCount                    UI8                                 后面的QualitySegmentUrlModifier（质量级别引用）的数量。如果为0，
                                                                则此段运行表适用于所有质量级别，引导信息框中只能有一个段运行表框。

QualitySegmentUrlModifiers           STRING
                                [QualityEntryCount]             此表应用于的质量级别的名称数组。名称是以空结尾的UTF-8字符串。
                                                                数组应是引导信息（abt）框中可输入的质量的子集。
                                                                这些名称不应出现在引导程序信息框中的任何其他段运行表中。
SegmentRunEntryCount                 UI32                       此SegmentRunEntryTable中的项数。最小值为1。
SegmentRunEntryTable                 SEGMENTRUNENTRY            段运行项数组
                        [           SegmentRunEntryCount ]

Each SEGMENTRUNENTRY has the following format: SEGMENTRUNENTRY

FirstSegment                         UI32                        包含相同数量片段的段运行中第一段的标识号。
                                                                 对应于下一个segmentrunentry中第一个segment的段将终止此运行。
FragmentsPerSegment                  UI32                        此运行中每个段中的片段数
```

<h5 id="a2-11-2-2">2.11.2.2. Fragment Run Table box</h5>

Box type: 'afrt' 

Container:Bootstrap Info box ('abst')  

Mandatory: Yes 

Quantity: One or more

片段运行表（AFRT）框可用于查找包含与给定时间对应的样本的片段。

片段由URL方案单独标识。碎片的持续时间和样本数量都可能不同。碎片的持续时间储存在AFRT箱中。

AFRT盒使用压缩编码：

-片段运行表可以表示多个质量级别的片段。

-片段运行表是紧凑编码的，因为每个条目都给出了运行的第一个片段号。

具有相同持续时间的片段。可以通过从下一个条目中的第一个片段号减去此条目中的第一个片段号来计算具有相同持续时间的片段数。

一个引导信息框中可能有几个片段运行表框，每个框用于不同的质量级别。

asrt box

``` javascript
Field	                            Type	                    Comment
Header                              BOXHEADER                   BoxType ='afrt' (0x61667274)
Version                             UI8                         Either 0 or 1
Flags                               UI24                        定义了以下值：
                                                                0=A full table
                                                                1=此表中的记录是以前定义的片段运行表的更新（或要追加的新条目
                                                                当此标志为1时，包含引导信息框中的更新标志应为1。
TimeScale                           UI32                        每秒在FirstFragmentTimestamp和FragmentDuration字段中使用的时间单位数。通常，值为1。
QualityEntryCount                   UI8                         后面的QualitySegmentUrlModifier（质量级别引用）的数量。
                                                                如果为0，则此片段运行表适用于所有质量级别，并且引导信息框中只能有一个片段运行表框。
QualitySegmentUrlModifiers          STRING
                                [QualityEntryCount]             此表应用于的质量级别的名称数组。名称是以空结尾的UTF-8字符串。
                                                                数组应是引导信息（abt）框中可输入的质量的子集。
                                                                这些名称不应出现在引导程序信息框中的任何其他片段运行表中。

FragmentRunEntryCount               UI32                        此FragmentRunEntryTable中的项数。最小值为1。
FragmentRunEntryTable               FRAGMENTRUNENTRY
                                [FragmentRunEntryCount]         片段运行项数组
每个fragmentrunentry的格式如下：fragmentrunentry

FirstFragment                       UI32                        具有相同持续时间的此片段运行中第一个片段的标识号。
                                                                与下一个fragmentrunentry中的第一个fragment对应的片段将终止此运行。
FirstFragmentTimestamp              UI64                        第一个片段的时间戳，以时间刻度单位表示。
                                                                此字段确保片段时间戳可以在开始时准确表示。
                                                                它还可以确保在由于持续时间准确性或时间戳不连续而发生偏移时同步时间戳。
FragmentDuration                    U32                         此运行中每个片段的持续时间（以时间刻度单位表示）
DiscontinuityIndicator          IF FragmentDuration == 0
                                    UI8                         表示时间戳和/或片段号中的不连续。此字段还用于标识（实时）演示文稿的结尾。
                                                                定义了以下值：
                                                                0=演示结束。
                                                                1=片段编号的不连续性。
                                                                2=时间戳的不连续性。
                                                                3=时间戳和片段编号的不连续性。
                                                                保留所有其他值。
                                                                在现场场景中，在带内发出演示结束的信号非常有用。
                                                                表示中的间隔被表示为零持续时间片段的运行，同时具有片段数和时间戳不连续性。
                                                                片段数不连续性对于片段编号方案中没有不连续性的跳变信号很有用
```

<h4 id="a2-12">2.12. Movie Fragment box</h4>

Box type: 'moof' 

Container: File 

Mandatory: Yes for HTTP streaming support with F4V fragments, otherwise no 

Quantity: One per fragment for HTTP streaming support with F4V fragments, otherwise zero.

Movie片段（MOOF）框提供特定于片段的信息，否则这些信息将出现在媒体（MOOV）框中。MOOF箱应按顺序排列。

moof box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'moof' (0x6D6F6F66)
Boxes           BOX [ ]             定义示例结构的多个框
```


For more information, see section 8.8.4 of ISO/IEC 14496-12:2008.


<h5 id="a2-12-1">2.12.1. Movie Fragment Header box</h5>

Box type: 'mfhd' 

Container: Movie Fragment box ('moof') 

Mandatory: Yes 

Quantity: One

Movie片段头（mfhd）框包含序列号，用于验证文件的完整性。

mfhd box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'mfhd' (0x6D666864)
Version         UI8                 Expected to be 0
Flags           UI24                Reserved. Set to 0
SequenceNumber  UI32                从1开始，并按文件中每个电影片段的出现顺序递增。
```


For more information, see section 8.8.5 of ISO/IEC 14496-12:2008.


<h5 id="a2-12-2">2.12.2. Track Fragment box</h5>

Box type: 'traf' 

Container: Movie Fragment box  

Mandatory: No 

Quantity: Zero or more

轨迹片段（traf）框对应于f4v文件中的轨迹。每个交通箱包含零个或多个轨道运行，其中包括该轨道的连续运行。

traf box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'traf' (0x74726166)
Boxes           BOX [ ]             定义片段中轨道运行的任意数量的框
```


For more information, see section 8.8.6 of ISO/IEC 14496-12:2008.
<h5 id="a2-12-2-1">2.12.2.1. Track Fragment Header box</h5>

Box type: 'tfhd' 

Container: Track Fragment box  

Mandatory: Yes 

Quantity: One

“轨迹片段头”（tfhd）框设置用于影片片段中样本运行的信息和默认值。每个电影片段可以向每个曲目添加零个或多个片段，而一个曲目片段可以添加零个或多个连续运行的示例。

tfhd box

``` javascript
Field	                     Type	            Comment
Header                       BOXHEADER           BoxType = 'tfhd' (0x74666864)
Version                      UI8                 Expected to be 0
Flags                        UI24                以下标志可用于任何组合：
                                                 0x000001=基础数据偏移量存在
                                                 0x000002=样本描述存在
                                                 0x000008=默认样本持续时间存在
                                                 0x0000010=默认样本大小存在
                                                 0x0000020=默认样本标志存在
                                                 0x10000=持续时间为空：没有样本用于提供的持续时间
                                                 TFHD框或Trex框中的默认采样持续时间。见下表注释
TrackID                      UI32                在“Track标题”框中指定的关联Track的标识
BaseDataOffset               IF Flags 
                             & 0x000001 == true
                                UI64            可选的。计算每个轨道运行中的数据偏移量时要使用的基准偏移量。默认值在表下面定义。
SampleDescriptionIndex      IF Flags
                            & 0x000002 == true
                                UI32            可选的。要在此片段中使用的sampleDescriptionIndex。
                                                这将覆盖该片段的trex框中的默认sampleDescriptionIndex。
DefaultSampleDuration       IF Flags 
                            & 0x000008 == true
                                UI32            可选的。此片段中要使用的默认sampleduration。
                                                这将覆盖该片段的Trex框中的默认采样持续时间。
DefaultSampleSize           IF Flags
                            & 0x000010 == true
                                UI32            可选的。此片段中使用的默认sampleSize。这将覆盖该片段的trex框中的默认sampleSize。
DefaultSampleFlags          IF Flags 
                            & 0x000020 == true
                                SAMPLEFLAGS     可选的。此片段中要使用的默认sampleFlags。这将覆盖该片段的trex框中的默认sampleFlags。
```


basedataoffset：如果此处未提供值，则电影片段中第一个曲目的默认值是封闭电影片段框的第一个字节的位置。对于后续的跟踪片段，默认值是由前面的片段定义的数据的结尾。以这种方式“继承”其偏移量的片段都应使用相同的数据引用，即，这些轨迹的数据应在同一个文件中。
>0x010000持续时间为空：如果一个F4V文档在MOOV框中有编辑列表，并且具有空的持续时间片段，则认为该文档格式不正确。

For more information, see section 8.8.7 of ISO/IEC 14496-12:2008.
<h5 id="a2-12-2-2">2.12.2.2. Track Fragment Run box</h5>

Box type: 'trun' 

Container: Track Fragment box ('traf') 

Mandatory: No 

Quantity: Zero or more

跟踪片段运行（trun）框定义跟踪的连续样本集。如果在“轨迹碎片框”（traf）框中设置了“持续时间为空”标志，则没有“躯干框”。

trun box

``` javascript
Field	                    Type	            Comment
Header                      BOXHEADER           BoxType = 'trun' (0x7472756E)
Version                     UI8                 Expected to be 0
Flags                       UI24                下列标志可用于任何组合，但0x000004和0x000400除外：
                                                0x000001=存在数据偏移量
                                                0x000004=存在第一个样本标志
                                                0x000100=存在样本持续时间
                                                0x000200=存在样本大小
                                                0x000400=存在样本标志
                                                0x00800=存在样品组成时间偏移
SampleCount                 UI32                sampleInformation中的条目数
DataOffset                  IF Flags 
                        & 0x000001 == true
                            SI32                可选的。要添加到在tfhd框中定义的数据偏移量的值。默认值在表下面定义。
FirstSampleFlags            IF Flags 
                        & 0x000004 == true
                            SAMPLEFLAGS         可选的。仅用于此Trun Box中描述的集合的第一个样本的标志。见下表文本。
SampleInformation           SampleInformationStructure
                            [SampleCount]       结构中的所有字段都是可选的

每个sampleInformationstructure记录都有以下布局：sampleInformationstructure
SampleDuration              IF Flags 
                        & 0x000100 == true
                            UI32                可选的。每个采样的持续时间，以媒体头中为此曲目定义的时间刻度单位为单位
                                                如果不存在，则使用默认值。
SampleSize                  IF Flags 
                        & 0x000200 == true
                            UI32                可选的。每个样本的大小。如果不存在，则使用默认值。
SampleFlags                 IF Flags 
                        & 0x000400 == true
                            SAMPLEFLAGS         可选的。每个样本的sampleFlags。如果不存在，则使用默认值。
SampleCompositionTimeOffset	IF Flags 
                        & 0x000800 == true
                            UI32                可选的。每个样本的合成时间偏移。如果不存在，则使用默认值。
```


data offset：如果不存在数据偏移量，则此运行的数据从两个位置之一开始。如果此运行是跟踪片段中的第一个，则它从由跟踪片段头定义的基础数据偏移量开始。否则，它会在上一次运行的数据之后立即启动。

FirstSampleFlags：仅覆盖第一个示例的默认标志。这使得记录一组帧成为可能，其中第一个帧是键，其余的帧是不同的帧，而不为每个样本提供显式标志。设置此标志时，不应出现示例标志。

For more information, see section 8.8.8 of ISO/IEC 14496-12:2008.
<h4 id="a2-13">2.13.  Media Data box</h4>

Box type: 'mdat' 

Container: File 

Mandatory: Yes 

Quantity: One

媒体数据（mdat）框包含f4v文件的媒体数据负载。所有视频示例、音频示例、数据示例以及提示轨和示例都包含在mdat框中。请参见1.8支持的媒体类型。

mdat框与media（moov）框一起出现在f4v文件的顶层。

无法单独理解mdat框，这也是文件中必须存在moov框的原因。

mdat box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'mdat' (0x6D646174)
Payload         UI8 [ ]             媒体数据字节，其结构在文件的MOOV框中定义。
```


For more information, see section 8.2.2 of ISO/IEC 14496-12:2008.


<h5 id="a2-13-1">2.13.1.  Hint Track Samples for HTTP Streaming</h5>

mdat框包含用于包含f4v片段的HTTP流的提示轨。提示轨包含ADobemuxhintsamples。Adobemux提示示例输入框（rtmp）描述了提示轨迹。
<h5 id="a2-13-1-1">2.13.1.1. AdobeMuxHintSample</h5>

一组adobemuxhintsamples组成了一个提示跟踪，它采用Adobe多路提示跟踪格式。Adobemuxhintsample具有以下布局：

AdobeMuxHintSample

``` javascript
Field	        Type	                    Comment
PacketCount     IF PacketCountField == 1
                UI8                         此adobemuxhintsample中的adobemuxPacket条目数。
                                            当packetCountField==0时，adobmuxPackets是自描述的，可以隐式确定数字。
Packets         AdobeMuxPacket
                [PacketCount]               AdobEmuxPacket元素数组
```

<h5 id="a2-13-1-2">2.13.1.2. AdobeMuxPacket</h5>

AdobEmuxPacket具有以下布局。EncryptionHeader及其之前的部分与E.4.1节中定义的flvtag中的相应部分相同。

AdobeMuxPacket

``` javascript
Field	                    Type	                    Comment
Reserved                    UI2                         Reserved for FMS, should be 0
Filter                      UI1                         指示是否筛选数据包。
                                                        0=不需要预处理。
                                                        1=在呈现数据包之前，需要对其进行预处理（如解密）。
                                                        未加密文件中应为0，加密标签应为1。过滤器的使用见附录F.FLV加密。
TagType                     UI5                         此标记的类型。定义了以下类型：
                                                        8＝音频
                                                        9＝视频
                                                        保留所有其他值
                                                        （18=不得使用脚本数据）
DataSize                    UI24                        消息的长度。streamid到数据包结尾后的字节数（等于数据包长度–11）
Timestamp                   UI24                        应用此标记中数据的时间（毫秒）。该值与flv文件中的第一个标记相关，该标记的时间戳始终为0。
TimestampExtended           UI8                         timestamp字段的扩展，以形成si32值。此字段表示高8位，而前一个时间戳字段表示低24位时间（毫秒）。
StreamID                    UI24                        Always 0.
AudioTagHeader              IF TagType == 8
                            AudioTagHeader              第E.4.2.1节中定义的audioTagHeader元素。
VideoTagHeader              IF TagType == 9
                            VideoTagHeader              第E.4.3.1节中定义的VideoTagHeader元素。
EncryptionHeader            IF Filter == 1
                            EncryptionTagHeader         如第F.3.1节所述，每个受保护样品应包括加密头。
ConstructorCount            IF ConstructorCountField == 1
                                UI8                     ADobemuxhintconstructors的数目。
                                                        当使用使用不同模式或原始样本的不同部分的多个数据块构造单个FLV标记或RTMP消息时，
                                                        特别使用此字段。如果constructorCountField==0，则constructorCount=1
DataEntry                   AdobeMuxHintConstructor
                            [ConstructorCount]          ADobEmuxHintConstructors元素数组
TrailerLength               If TrailerLengthField == 1
                                UI8                     拖车的长度，以字节为单位。
                                                        如果trailerLengthField=0，则trailerLength=trailerDefaultSize
Trailer                     UI8 
                            [TrailerLength]             其他数据，例如，为了兼容性。当处于FLV兼容模式时，此字段携带上一个标记大小。
```

<h5 id="a2-13-1-3">2.13.1.3. AdobeMuxHintConstructor</h5>

AdobEmuxHintConstructor具有以下布局：

AdobeMuxHintConstructor

``` javascript
Field	        Type	            Comment
Mode            If ModeField == 1
                UI8                 提示正在使用跟踪模式。
                                    当modefield==0时，可以从Adobe MUX提示进程（AMHP）框中确定模式。
HintInfo        If Mode == 2        如模式所示。尽管定义了三种提示模式，
                AdobeMuxHintSampleConstructor
                ELSE                但由于立即节点和立即节点提升模式都使用了ADobmUxHintimmediateConstructor，因此只指定了两个构造函数。
                AdobeMuxHintImmediateConstructor

```

<h5 id="a2-13-1-4">2.13.1.4. AdobeMuxHintImmediateConstructor</h5>

Adobmuxhintimmediteconstructor应在即时和即时节点提升模式中使用。这些模式以Adobe多路提示跟踪格式描述。

AdobEmuxHintimmediateConstructor具有以下布局：

AdobeMuxHintImmediateConstructor

``` javascript
Field	        Type	            Comment
Length          If LengthField == 1
                UI24                要从后面的数据中获取的字节数。
                                    如果lengthField==0，则不存在此字段，并且长度是根据adobmuxPacket.dataSize计算得出的。
Data            UI8 [Length]        要放入有效负载部分的数据字节
```


For more information, see section 8.aaaaaaaaaaaaaaaaaaaaaaaaaaa of ISO/IEC 14496-12:2008.
<h5 id="a2-13-1-5">2.13.1.5. AdobeMuxHintSampleConstructor</h5>

示例模式中应使用Adobemuxhintsampleconstructor。示例模式以Adobe多路提示跟踪格式描述。

AdobEmuxHintSampleConstructor具有以下布局：

AdobeMuxHintSampleConstructor

``` javascript
Field	        Type	        Comment
TrackRefIndex   SI8             指示示例数据将来自哪个跟踪的值。
                                值为0表示只引用了一个媒体曲目。
                                1到127之间的值是指向提示跟踪引用原子项的索引。
                                这些值指示要从哪个原始媒体跟踪样本。一个值
                                -1表示提示跟踪本身。也就是说，从与当前解析的提示示例相同的轨道获取示例。
Length          UI24            要复制的示例中的字节数。模式==2时，长度场应为1。
SampleNumber    UI32            轨道样本号。
SampleOffset    UI32            从样本开始到开始复制点的偏移量
```

<h4 id="a2-14">2.14. Meta box</h4>

Box type: 'meta' 

Container:  File, Movie box ('moov'), or Track box ('trak') 

Mandatory: No 

Quantity: Zero or one at each file level, movie level, or track level

元（meta）框的容器是一个f4v文件、一个电影（moov）框或一个轨道（trak）框。meta-box可以包含其他各种包含元数据的框。

meta box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'meta' (0x6D657461)
Version         UI8                 Reserved, set to 0
Flags           UI24                Reserved, set to 0
Boxes           BOX [ ]             定义文件元数据的任意框数
```


For more information, see section 8.11.1 of ISO/IEC 14496-12:2008.
<h4 id="a2-15">2.15. Free Space boxes</h4>

Box type:  'free' or 'skip'  

Container: File or any box  

Mandatory: No 

Quantity: Any

自由（free）和跳过（skip）框的内容是自由文件空间，播放机应忽略它们的内容。在允许使用盒子的地方可以使用盒子。这些框可以为将来扩展容器框中的数据保留空间。

mfra框应该是文件中的最后一个。mfra框中的最后一个框提供了mfra框中长度字段的副本。

free space box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'free' (0x66726565) or 'skip' (0x736b6970)
Void            UI8 [ ]             Arbitrary number of bytes to end of box
```


For more information, see section 8.aaaaaaaaaaaaaaaaaaaaaaaaaaa of ISO/IEC 14496-12:2008.


<h4 id="a2-16">2.16. Movie Fragment Random Access box</h4>

Box type: 'mfra' 

Container: File 

Mandatory: No 

Quantity: One

电影片段随机访问（mfra）框通过为轨迹（不一定是所有轨迹）提供轨迹片段随机访问（tfra）框，帮助在碎片化的f4v文件中查找随机访问点。此框中的信息不确定，只提供随机接入点的提示。

mfra box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'mfra' (0x6D667261)
Boxes           BOX [ ]             定义随机接入点的任意数量的框
```


For more information, see section 8.8.9 of ISO/IEC 14496-12:2008.


<h5 id="a2-16-1">2.16.1. Track Fragment Random Access box</h5>

Box type: 'tfra' 

Container: Movie Fragment Random Access box ('mfra') 

Mandatory: No 

Quantity:  Zero or more

每个跟踪片段随机访问（TFRA）框条目提供随机可访问样本的位置和表示时间。tfra框不需要包含跟踪中每个随机可访问样本的条目。此框的缺失并不意味着所有样本都是同步样本。

tfra box

``` javascript
Field	                    Type	            Comment
Header                      BOXHEADER           BoxType = 'tfra' (0x74667261)
Version                     UI8                 Either 0 or 1
Flags                       UI24                Reserved, set to 0
TrackID                     UI32                Identifies the track
Reserved                    UI26                Reserved. Set to 0
LengthSizeTrafNumMinus1     UI2                 RandomAccessStructure记录中TrafNumber字段的长度（以字节为单位），减去1
LengthSizeTrunNumMinus1     UI2                 RandoAccessStructure记录中的TrunNumber字段的长度（以字节为单位），减去1
LengthSizeSampleNumMinus1   UI2                 randomAccessStructure记录中sampleNumber字段的长度（以字节为单位），减去1
NumberEntry                 UI32                此曲目的条目数。如果为0，则每个样本都是随机接入点
RandomAccessSample          RandomAccessStructure
                            [NumberEntry]       随机访问样本的位置和呈现时间
每个randomaccessstructure记录都有以下布局：randomaccessstructure
Time                        IF Version == 0
                                UI32
                            IF Version == 1
                                UI64             随机访问样本的表示时间，以媒体标题中为此曲目定义的时间刻度单位表示。
MoofOffset                  IF Version == 0
                                UI32
                            IF Version == 1
                                UI64            对应电影片段框的字节偏移量，从文件开始
TrafNumber                  { UI8, UI16, UI24, UI32 } 
                            [LengthSizeTrafNumMinus1]   包含随机可访问样本的traf编号。每个MOOF中的第一个traf编号为1。
                                                        类型是按长度大小索引的ui8、ui16、ui24、ui32中的一个1
TrunNumber                  { UI8, UI16, UI24, UI32 } 
                            [LengthSizeTrunNumMinus1]   包含随机可访问样本的Trun编号。每个交通中的第一个通道编号为1。
SampleNumber                { UI8, UI16, UI24, UI32 }
                            [LengthSizeSampleNumMinus1]     包含随机可访问样本的样本号。每个耳轴的第一个样本编号为1。
```


For more information, see section 8.8.10 of ISO/IEC 14496-12:2008.


<h5 id="a2-16-2">2.16.2. Movie Fragment Random Access Offset box</h5>

Box type: 'mfro' 

Container: Movie Fragment Random Access box ('mfra') 

Mandatory: Yes 

Quantity: One

Movie片段随机访问偏移量（mfro）框提供Movie片段随机访问（mfra）框的长度字段的副本，并帮助查找mfra框。mfro箱应最后放置在mfra箱中。

mfro box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'mfro' (0x6d66726f)
Version         UI8                 Either 0 or 1
Flags           UI24                Reserved, set to 0
Size            UI32                封闭movie片段随机访问框的大小（字节）
```


For more information, see section 8.8.11 of ISO/IEC 14496-12:2008.




<h3 id="a3">3. F4V Metadata</h3>

本节介绍f4v文件格式支持的元数据。
<h4 id="a3-1">3.1. Tag box</h4>

Box type:  'auth', 'titl', 'dscp' and 'cprt'  

Container: Movie box ('moov') 

Mandatory: No 

Quantity: Zero or one of each type.

f4v文件格式支持包含在电影（moov）框中的四个可选标记框。一个f4v文件最多可以包含256个标记（包括这些框中的标记和在ILST框中定义的标记）。

Tag box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType应为以下类型之一：
                                    作者的“auth”（0x61757468）
                                    “titl”（0x7469746C）表示标题
                                    “dscp”（0x64736370）用于说明“cprt”（0x63707274）用于版权
Version         UI8                 Shall be 0
Flags           UI24                Reserved, set to 0
Pad             UI1                 Padding, set to 0
Language        UI5 [3]             指定语言的三字符代码（见ISO 639-2/T）。每个字符被解释为0x60+（5位）代码，以生成一个ASCII字符。
TagString       UI8 [ ]             标记字符串数据，占用框的其余部分。标记字符串长度不得超过65535字节。

```

<h4 id="a3-2">3.2. XMP Metadata box</h4>

Box type: 'uuid' 

Container: File 

Mandatory: No 

Quantity: One

从第10版开始，Flash播放器可以加载嵌入到F4V文件中的XMP数据。XMP是Adobe的可扩展元数据平台。有关详细信息，请参阅www.adobe.com/go/xmp。

XMP元数据框应立即跟随电影（MOOV）框，无中间框。xmp元数据框的大小不得超过64兆字节。

通过XMP元数据框，该文件可以通过actionscript将XMP元数据与SWF电影通信。xmpmetadata通过名为dat a的字符串属性向actionscript公开。

XMP Metadata box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = ‘uuid’ (0x75756964)
UUID            UI8 [16]            16字节（128位）通用唯一标识符（UUID）。
                                    uuid应为十六进制字节，即7a cf cb 97 a9 42 e8 9c 71 99 94 91 e3 af ac。
XMPMetadata     UI8 [ ]             XMP元数据，按照XMP元数据标准格式化
```

<h4 id="a3-3">3.3.  ilst box</h4>

Box type: 'ilst' 

Container: Meta box ('meta') 

Mandatory: No 

Quantity: One

ILST框出现在元（meta）框中，包含任意数量的元数据标记。f4v文件最多可包含256个标记（包括此框和“auth”、“titl”、“dscp”和“cprt”框中的标记）

ilst box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'ilst' (0x696C7374)
TagCount        UI32                在ILST框中枚举的标记数
Tags            TAGRECORD 
                [TagCount]          多个标记记录条目 
每个标记记录都有以下布局：TAGRECORD
TagLength       UI32                标记记录的总长度，包括此长度字段
TagName         UI8 [4]             4个字节，指示标记的名称。这些字节通常来自人类可读的ASCII集，但不总是
DataLength      UI32                标记记录的数据部分的总长度
DataTag         UI8 [4]             4个字节'd'、'a'、't'和'a'表示标记记录的数据部分
DataType        UI32                指定标记记录的数据负载中的数据类型
Reserved        UI32                Reserved, set to 0
Payload         UI8 [ ]             占用标记记录其余部分的任意字节数。精确的有效负载格式取决于数据类型
                                    数据类型支持的值为：
                                    -0:自定义数据。在'trkn'和'disk'标记类型的情况下，数据有效负载被解释为单个ui32
                                    - 1：文本数据
                                    -13，14：二进制数据
                                    -21：通用数据

```

<h4 id="a3-4">3.4. Text Track Metadata</h4>

Box type:  See below 

Container: Text samples ('text' or 'tx3g') in Media Data box ('mdat') 

Mandatory: No 

Quantity: Any

文本示例（“text”或“tx3g”）可以包含以下元数据框。它们的内容通过ontextdata属性暴露给正在运行的actionscript程序。
<h5 id="a3-4-1">3.4.1. Style box</h5>

样式（Styl）框包含文本样式规范。此信息通过st yl e属性公开给actionscript。

styl box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'styl' (0x7374796C)
Count           UI16                样式数组中的条目数
Styles          STYLERECORD
                [Count]             一组样式记录结构，每个结构都作为ActionScript对象
单个样式记录具有以下布局：STYLERECORD
StartChar       UI16                此样式记录应用到的第一个字符，通过名为startchar的双属性向actionscript公开
EndChar         UI16                此样式记录应用到的最后一个字符，通过名为endchar的双属性向actionscript公开
FontID          UI16                用于此样式的字体ID，通过名为fontid的双属性公开给actionscript
                                    FaceStyleFlags UI8通过名为facestyleflags的双属性用于ActionScript
FontSize        UI8                 用于字体的大小，通过属性fontsize用于actionscript
TextColor       UI32                文本的rgba颜色，通过属性textcolor公开给actionscript
```

<h5 id="a3-4-2">3.4.2. Highlight box</h5>

突出显示（HLIT）框指定要突出显示的文本范围。此信息通过highlight属性公开给actionscript。

hlit box

``` javascript
Field	        Type	             Comment
Header          BOXHEADER           BoxType = 'hlit' (0x686C6974)
StartChar       UI16                要突出显示的第一个字符，通过名为startchar的双属性向actionscript公开
EndChar         UI16                要突出显示的最后一个字符，通过名为endchar的双属性向actionscript公开。
```

<h5 id="a3-4-3">3.4.3. Highlight Color box</h5>

突出显示颜色（HCLR）框指定文本的突出显示颜色。此信息通过hi ghl i ght col或属性公开给actionscript。

hclr box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'hclr' (0x68636C72)
HighlightColor  UI16 [3]            通过名为hi-ghlightcolor的双属性向actionscript公开的三元素数组，它按顺序保存红色、绿色和蓝色组件的值。
```

<h5 id="a3-4-4">3.4.4. Karaoke box</h5>

Karaoke（krok）框指定Karaoke元数据。此信息通过karaoke属性公开给actionscript。时间以轨道定义的时间刻度单位表示。

krok box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'krok' (0x6B726F6B)
StartTime       UI32                通过名为StartTime的双属性向ActionScript公开
Count           UI16                KaraokeRecords数组中的条目数
KaraokeRecords  KARAOKEREC [Count]  karaokerec结构的数组，每个结构都作为对象暴露在actionscript中

An individual KARAOKEREC has the following structure: KARAOKEREC
EndTime         UI32                通过名为endtime的双属性向ActionScript公开
StartChar       UI16                通过名为startchar的双属性向ActionScript公开
EndChar         UI16                通过名为endchar的双属性向ActionScript公开
```

<h5 id="a3-4-5">3.4.5. Scroll Delay box</h5>

滚动延迟（dlay）框指定滚动延迟。此信息通过scr ol l del ay属性向actionscript公开，该属性以与跟踪相关的时间刻度单位表示。

dlay box

``` javascript
Field	        Type	             Comment
Header          BOXHEADER           BoxType = 'dlay' (0x646C6179)
ScrollDelay     UI32                通过名为ScrollDelay的双属性向ActionScript公开
```

<h5 id="a3-4-6">3.4.6. Drop Shadow Offset box</h5>

放置阴影（DRPO）框指定文本的放置阴影偏移坐标。

drpo box

``` javascript
Field	                Type	            Comment
Header                  BOXHEADER           BoxType = 'drpo' (0x6472706F)
DropShadowOffsetX       UI16                通过名为DropshadowOffsetx的双属性向ActionScript公开
DropShadowOffsetY       UI16                通过名为DropshadowOffsty的双属性向ActionScript公开
```

<h5 id="a3-4-7">3.4.7. Drop Shadow Alpha box</h5>

放置阴影alpha（drpt）框指定放置阴影alpha值。

drpt box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'drpt' (0x64727074)
DropShadowAlpha UI16                一个16位alpha值，通过名为dr opshadowal pha的双属性公开给actionscript。
```


For more information, see section 8.aaaaaaaaaaaaaaaaaaaaaaaaaaa of ISO/IEC 14496-12:2008.


<h5 id="a3-4-8">3.4.8. Hypertext box</h5>

超文本框（href）指定在文本范围内具有alt文本的超文本链接。此信息通过hyper t ext属性公开给actionscript。

href box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'href' (0x68726566)
StartChar       UI16                文本范围的开始字符，通过名为startchar的双属性向actionscript公开。
EndChar         UI16                文本范围的最后一个字符，通过名为endchar的双属性向actionscript公开
URLSize         UI8                 The length of the URL string
URL             UI8 [URLSize]       通过名为url的字符串属性向actionscript公开的url字符串
ALTSize         UI8                 The length of the ALT string
ALT             UI8 [ALTSize]       当用户鼠标悬停在链接上时显示的alt字符串，通过名为alt的字符串属性向actionscript公开。
```

<h5 id="a3-4-9">3.4.9. Text Box box</h5>

文本框（tbox）框定义文本框的坐标。此信息通过t ex t box属性公开给actionscript。

tbox box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'tbox' (0x74626F78)
Top             UI16                顶部像素坐标，通过名为top的双属性向actionscript公开
Left            UI16                左像素坐标，通过名为Left的双属性向ActionScript公开
Bottom          UI16    
Right           UI16
```

<h5 id="a3-4-10">3.4.10. Blinking box</h5>

Blinking（BLNK）框指定要设置闪烁的文本范围。此信息通过blink属性公开给actionscript。

blnk box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'blnk' (0x626C6E6B)
StartChar       UI16                闪烁范围中的第一个字符，通过名为startchar的双属性向actionscript公开
EndChar         UI16                闪烁范围中的结束字符，通过名为endchar的双属性向actionscript公开

```

<h5 id="a3-4-11">3.4.11. Text Wrap box</h5>

文本换行（twrp）框为文本设置换行标志。

twrp box

``` javascript
Field	        Type	            Comment
Header          BOXHEADER           BoxType = 'twrp' (0x74777270)
WrapFlag        UI8                 如果文本应该换行，则为非零的布尔值，通过名为wrapflag的双属性向actionscript公开。
```

<h3 id="a4">附件A.嵌入提示点</h3>
<h4 id="a4-1">A.1.Overview</h4>

使用Flash构建媒体应用程序的一个关键好处是能够混合媒体播放和丰富的交互式数据。f4v应用程序可以支持以amf样本的形式嵌入时间数据（提示点）。这些示例与音频和视频示例一起传输到Flash运行时，在那里它们被发送到应用程序脚本。
<h4 id="a4-2">A.2.AMF样本格式</h4>

AMF示例是包含类型化AMF值列表的AMF对象。

根据为数据轨指定的类型，AMF对象应为AMF0对象或AMF3对象。amf0和amf3的规范可以在以下网址找到：http://opensource.adobe.com/wiki/display/blazeds/developer+documentation

第一个值应为表示AMF样本名称的字符串。AMF值将被调度到具有此名称的方法。例如，如果第一个字段是名为“onfoo”的字符串，则在播放AMF示例时调用方法“onfoo”。

表3列出了为运行时保留且未调度到脚本的名称：

``` javascript
Table 3. Reserved names

attachAudio	    attachVideo	    call	close	getBufferInfo
onStatus	    pause	        play	play2	publish
receiveAudio	receiveVideo	seek	send	setBufferTime
```

<h4 id="a4-3">A.3.AMF数据跟踪结构</h4>

AMF样本可以存储在数据跟踪中。数据轨配置如下：

处理程序参考（HDLR）框中的处理程序类型应为“数据”，媒体头框类型应为“NMHD”。

样品描述（STSD）框应包含一份描述AMF样品的描述记录。

说明条目格式应为sampleEntry类型。

说明条目的框类型应为“amf0”或“amf3”，与样本的“amf”格式相对应。以下数据轨框应包含每个AMF样本的条目：

-解码时间采样（STTS）框，用于AMF样本的解码时间。

-样本大小（STSZ）框，用于AMF样本的大小。

-chunk offset（STCO或CO64）框，用于AMF样本的偏移。

-合成时间采样（CTTS）框，用于将AMF样本传递到ActionScript的时间

程序。

在媒体数据（mdat）框中，数据轨中的样本应与音频和视频样本交错。
<h5 id="a4-3-1">A.3.1.解码数据轨</h5>

播放一个f4v文件时，mdat框中的amf示例将传递给amf解码器。在STTS框中指定的时间，AMF解码器对样本进行解码。

在CTTS框中指定的时间，AMF解码器将解码后的AMF样本传递给actionscript程序。
<h4 id="a4-4">A.4.渐进式下载</h4>

通过适当的交织，该方法适用于渐进式下载。

AMF内容应与音频和视频内容在正确的时间交错，以确保在下载文件时数据在正确的时间可用。

AMF数据不应存储在文件的末尾，因为在这种情况下，在发送第一个AMF样本之前，必须下载整个文件，即使该样本在内容的时间非常早。
<h4 id="a4-5">A.5.多个数据轨</h4>

只有一个数据轨。
<h3 id="a5">附件B.Flash播放器元数据</h3>
<h4 id="a5-1">B.1.流属性</h4>

当flash player加载一个f4v文件时，可以通过net st r eam为正在运行的actionscript程序提供各种流属性。Onmet Adat a Property公司。可用属性因用于创建文件的软件而异。典型特性为：

-audi ocodeci d：一个包含四个字符的字符串，定义使用的音频编解码器（如果存在音频并且

使用Flash播放器可以解码的编解码器编码

-avcl-level：表示视频符合的avc级别的双精度符号，如果存在视频并对其进行编码。

采用AVC／H.264

-ile的avcpr：一个双精度符号，表示视频符合的avc配置文件（如果存在视频且

用AVC/H.264编码

-打开时的持续时间：以秒为单位表示电影总长度的两倍

-高度：表示视频高度的双精度符号，如果视频存在并使用编解码器编码

Flash播放器可以解码

-moovposition：一个双精度符号，表示f4v文件中moov框的绝对偏移量。此属性

用于确定文件是否将逐步加载

-videocodecid：一个包含四个字符的字符串，定义所使用的视频编解码器（如果存在视频且

使用Flash播放器可以解码的编解码器编码

-如果存在视频，则显示视频的平均视频帧速率。

使用Flash播放器可以解码的编解码器编码

-width：一个双精度符号，表示视频的宽度，如果视频存在，并用闪烁的编解码器编码。

玩家可以解码
<h4 id="a5-2">B.2.影像元数据</h4>

如果f4v示例是图像类型（gif、png或jpeg），则通过oni magedat a属性将数据提供给正在运行的actionscript程序。存在以下属性：

-数据：包含压缩图像数据（即原始jpeg、png或gif文件数据）的字节数组。

-trackID：指示此示例所属的轨道的双精度符号。
<h3 id="a6">附件C.HTTP流：文件结构</h3>
<h4 id="a6-1">C.1.概述</h4>

Flash播放器支持带有F4V片段的HTTP流。HTTP流表示由HTTP流清单文件（F4M）和HTTP流段（分段的F4V文件或F4F）组成。

表示沿时间线划分为HTTP流段，进一步划分为HTTP流段。演示文稿可以在多个质量级别上并行提供。可以通过片段（完全或部分）和质量级别缓存和交付演示文稿。

演示文稿的引导信息框指定演示文稿的数据结构和对其的访问。清单文件（fdm）在http://opensource.adobe.com/wiki/display/osmf/flash+media+manifest+file+format+specification中有进一步的描述。
<h4 id="a6-2">C.2.HTTP流段</h4>

HTTP流段是包含片段的完整F4V文件。该段只能属于一个质量级别。该段应包括一组方框，后跟一组HTTP流片段。

成套盒子应包括以下盒子，最好按此顺序排列（可选盒子用括号[]表示）：

-	ftyp

-	[afra]

-	[abst]

-	moov

-	rtmp

-	[mdat]

一套盒子可能包括AFRA和MDAT盒子。如果包含，AFRA盒应位于MDAT盒和ABT盒之前。

HTTP流段中HTTP流段外部的MOOF盒不得用于HTTP流。
<h4 id="a6-3">C.2.HTTP流片段</h4>

HTTP流片段应包括以下每个框中的一个，最好按此顺序：

-	afra

-	abst

-	moof

-	mdat

AFRA箱应位于所有其他箱之前。

片段不是完整的f4v文件。HTTP流片段中不允许使用框ftyp、pdin和moov。
<h4 id="a6-4">C.4.URL构造</h4>

每个HTTP流段都是一个单独的URL资源（文件）。URL方案可以唯一地寻址每个HTTP流片段。HTTP流片段的URL构造如下：

http://serverbaseurl>/<*movieidentifier><*qualityssegmenturlmodifier>seg<*segment number>-frag<*fragmentnumber>

其中，f4v规范定义了括号中的字段，数字没有前导零。如果serverEntryCount==0，<*serverBaseURL>和尾随斜杠应省略。

如果QualityEntryCount==0，<*QualitySegmenturModifier>应省略。

示例：http://adobe。com/mymovie/highseg1-frag210
<h4 id="a6-5">C.5.Adobe多路提示跟踪格式</h4>

用于HTTP流的F4V文件需要包含提示跟踪。提示跟踪提供了使流服务器能够创建传输数据包的信息。有关流的信息，请参阅ISO规范第7节。

Adobe支持Adobe多路提示跟踪格式。Adobe多路复用提示跟踪格式足够灵活，可以支持RTMP数据包以及类似FLV的格式，其中样本作为一个整体按时间顺序交错。FLV兼容模式是为提示样本和FLV格式之间的有效映射而定义的。可以对格式进行配置，以确保由一系列提示样本组成的mdat与对应于这些样本的flv的一部分相同。

Adobemux提示示例输入框（rtmp）描述了提示轨迹。提示轨包含一个AdobEmuxhintsamples集合，并位于媒体数据框中。

定义了三种提示模式：

-立即模式（模式=0）

在这种模式下，为了提高效率，多路复用轨道的有效载荷直接在提示样本本身中可用。但是，必须小心使用此模式，因为它可能会导致某些数据重复。

-立即节点提升模式（模式=1）

立即节点提升模式的定义是为了避免立即模式下的数据复制。在此配置中，原始（音频/视频）曲目的采样表中的偏移量被调整为物理指向包含媒体数据的（立即模式）提示样本（本地位于mdat位置）。因为块偏移量被更改为指向每个样本的提示样本（在头之后和样本开始的位置），所以所有块只包含一个样本。

因此，媒体框“mdat”只包含提示示例（没有音频或视频示例）。提示示例将在其即时数据字段中嵌入媒体数据。只有在Adobe多路复用数据包中使用完整样本（而不是在分块RTMP模式下）时，这才是可能的。此外，这也会增加区块偏移表的副作用，但与获得的效率相比，这是最小的。

-采样模式（模式=2）

在这种模式下，包头和尾部被定义为提示样本的一部分。多路复用磁道的有效负载通过引用具有长度和偏移量的媒体磁道中的特定样本从提示样本“指向”媒体磁道。
<h3 id="a7">附录D.F4V加密D.1概述</h3>
<h4 id="a7-1">D.1.加密过程</h4>

本节概述了如何使用Adobe的DRM密钥/权限管理系统来保护F4V内的媒体。保护仅适用于F4V的音频/视频曲目。其他曲目类型（如AMF曲目）的加密格式不在本规范的范围内。

本节应与ISO 14496-12:2008第8.12节（对受保护流的支持）一起阅读。在阅读下一节之前，读者必须理解上述文件格式。
<h4 id="a7-2">D.2.加密过程</h4>

加密过程将样本格式从纯文本更改为密码文本。在每个示例数据之前插入一个Adobe DRM访问单元头。由于将纯文本转换为密码文本，因此如果不使用适当的密钥，则无法使用应用程序访问基础媒体。

将转换描述加密样本的样本描述表中的样本描述条目。转换结构遵循ISO 14496-12:2008第8.12节。转换示例描述条目的目的是双重的：示例描述条目防止意外处理加密数据，就像它是未加密的一样，并记录应用的转换。

执行以下示例描述转换：

-样本描述条目的4cc替换为4cc，表示加密：

o“encv”表示加密视频流（而不是“mp4v”、“avc1”），o“enca”表示加密音频流（而不是“mp4a”、“samr”），o“encr”表示加密数据流。

-保护方案信息（sinf）框附加到示例描述条目后，保留所有其他框。

未修改的sinf框包含理解应用的加密转换及其参数以及查找其他信息（如密钥管理系统的类型和位置）所需的所有信息。它还记录了媒体的原始（未加密）格式。

-样本描述条目的原始格式4cc存储在原始格式（frma）框中，即

sinf框的子框。

-方案类型（schm）框也是sinf框的子框，并将加密方案指定为4cc和

它的版本。在f4v文件中，该方案4cc应为“adkm”，即Adobe的DRM密钥管理。

-在sinf框中，有一个“黑框”（方案信息（schi）框）空间，用于描述

控制对加密媒体内容的访问的密钥管理。schi框是一个容器框，仅由正在使用的方案解释。在F4V文件中，此框应为Adobe DRM密钥管理系统框。

图1–在f4v中存储保护信息的示例

图1说明了保护信息如何存储在F4V中。在示例中，将保护方案信息（sinf）框放入每个曲目的示例描述条目中，并将Adobe的DRM标识符指定为密钥/权限管理系统，以保护音频和视频曲目。

sinf框是每个示例描述框中的示例条目。虽然在一个样本描述框中可以有多个样本条目（每个曲目只能有一个样本描述框），但这并不常见。因此，上面的图表只显示每个轨道一个sinf框。但是，由于每个磁道可能有多个磁道，因此DRM Packager和DRM解码器都应该能够处理这种情况。
<h4 id="a7-3">D.3.样本加密</h4>

本节介绍在将DRM应用于F4V中的音频或视频轨时如何转换每个样本。
<h5 id="a7-3-1">D.3.1.接入单元头</h5>

表4中定义的访问单元头指定了由Adobe的DRM保护的每个样本单元的格式。媒体文件格式将媒体数据的布局指定为示例，但加密/解密过程需要在每个示例中包含其他信息。附加信息取决于所使用的DRM密钥管理。Adobe的DRM指定了自己的访问单元报头，该报头应位于每个编解码器特定的示例数据之前。f4v访问单元头与flv选择性加密过滤器参数相同。

``` javascript
Table 4. Access Unit Header
Access Unit Header
Field                 Type              Comment
EncryptedAU	          UI1               选择性加密指示器显示数据包是否加密。
                                            0=样本未加密
                                            1=样本已加密。     
Reserved              UI7               Shall be 0
IV              IF EncryptedAU == 1
                    UI8 [IVLength]      仅在样本加密时存在。包含16字节的AES-CBC IV数据
```


上述指定的访问单元头应添加到样本描述条目打开DRM（即存在保护方案信息（sinf）框）的每个样本中，即使特定样本未加密。报头是解码器知道特定样本是否加密的唯一方法（在selectiveencryption为1的情况下）。当只加密关键帧时，样本的选择性加密可以提高性能。
<h5 id="a7-3-2">D.3.2.加密样本的填充</h5>

所有加密样本应填充到块密码块长度的倍数。填料方案应如RFC 2630中所述，复制如下：

块密码期望输入数据是k个八位字节的倍数（对于aes 128，要求输入数据是16个八位字节的倍数），其中k大于1。对于此类算法，输入应在尾端填充k-（length mod k）八位字节，所有八位字节的值均为k-（length mod k），其中length是输入的长度。

因此，输入用下面的一个k字节序列填充在尾端，如表5所示。

``` javascript
Table 5. Padding the cipher block

Condition                   Bytes added to the end of the block
IF length mod k=k-1	                                    01
IF length mod k=k-2	                                02	02
...
IF length mod k=n                           ...  k-n   k-n
...
IF length mod k=0           k	k	…	k	k    k       k
```


由于所有输入都被填充，包括已经是块大小的倍数的输入值，并且没有填充序列是另一个的后缀，因此可以从填充块明确地确定填充的大小。最后一个八位字节表示要修剪多少个八位字节。
<h3 id="a8">附录E.FLV文件格式</h3>
<h4 id="a8-1">E.1.概述</h4>

flv文件中的每个标记类型构成一个单独的流。在一个FLV文件中，同步在一起的音频和视频流不得超过一个。FLV文件不应定义单个类型的多个独立流。FLV中使用的简单数据类型在SWF格式规范中定义。flv文件使用一个没有为swf文件定义的附加类型：ui24表示无符号的24位整数。

与SWF文件不同，FLV文件应以大端字节顺序存储多字节数。例如，作为SWF文件格式中的ui16，表示数字300（0x12c）的字节序列为0x2c 0x01；作为FLV文件格式中的ui16，表示数字300的字节序列为0x01 0x2c。

另请参见SWF文件格式规范，网址为http://www.adobe.com/go/swf_file_format。
<h4 id="a8-2">E.2.FLV报头</h4>

FLV文件应以FLV头开始：

FLV header

``` javascript
Signature           UI8	Signature byte always 'F' (0x46)
Signature           UI8	Signature byte always 'L' (0x4C)
Signature           UI8	Signature byte always 'V' (0x56)
Version             UI8	File version (for example, 0x01 for FLV version 1)
TypeFlagsReserved   UB [5]	Shall be 0
TypeFlagsAudio      UB [1]	1 = Audio tags are present
TypeFlagsReserved   UB [1]	Shall be 0
TypeFlagsVideo      UB [1]	1 = Video tags are present
DataOffset          UI32	The length of this header in bytes
```


对于FLV版本1，dataoffset字段的值通常为9。此字段用于在将来的版本中容纳较大的标题。
<h4 id="a8-3">E.3.FLV文件体</h4>

在flv头段之后，flv文件的其余部分应包含交替的反向指针和标签。它们交错排列，如下表所示：

FLV File Body

``` javascript
Field	            Type            Comment
PreviousTagSize0    UI32            Always 0
Tag1                FLVTAG          First tag
PreviousTagSize1    UI32            上一个标记（包括其标题）的大小（字节）。
                                    对于FLV版本1，该值为11加上上上一个标记的数据大小。
Tag2                FLVTAG          Second tag
...
PreviousTagSizeN-1  UI32            第二个到最后一个标记（包括其标题）的大小（字节）。
TagN                FLVTAG          Last tag
PreviousTagSizeN    UI32            最后一个标记（包括其标题）的大小（字节）。
```

<h4 id="a8-4">E.4.FLV标签定义</h4>
<H5 id="a8-4-1">E.4.1FLV标签</H5>

flv标记包含音频、视频或脚本的元数据、可选加密元数据和有效负载。FLVTAG

``` javascript
Field                       Type	        Comment
Reserved                    UB [2]          Reserved for FMS, should be 0
Filter                      UB [1]          指示是否筛选数据包。
                                            0=不需要预处理。
                                            1=在呈现数据包之前，需要对其进行预处理（如解密）。
                                            未加密文件中应为0，加密标签应为1。过滤器的使用见附录F.FLV加密。

TagType                     UB [5]          此标记中的内容类型。定义了以下类型：
                                            8＝音频
                                            9＝视频
                                            18=脚本数据

DataSize                    UI24            消息的长度。streamid到标记末尾后的字节数（等于标记的长度–11）
Timestamp                   UI24            应用此标记中数据的时间（毫秒）。该值与flv文件中的第一个标记相关，该标记的时间戳始终为0。
TimestampExtended           UI8             timestamp字段的扩展，以形成si32值。此字段表示高8位，而前一个时间戳字段表示低24位时间（毫秒）。
StreamID                    UI24            Always 0.
AudioTagHeader          IF TagType == 8     第E.4.2.1节中定义的audioTagHeader元素。
                            AudioTagHeader
VideoTagHeader          IF TagType == 9      第E.4.3.1节中定义的VideoTagHeader元素。
                            VideoTagHeader
EncryptionHeader        IF Filter == 1      如第F.3.1节所述，每个受保护样品应包括加密头。
                        EncryptionTagHeader
FilterParams            IF Filter == 1      根据第F.3.2节的规定，每个受保护样品应包括过滤器参数。
                            FilterParams

Data                    IF TagType == 8     每种媒体类型的特定数据。
                            AUDIODATA
                        IF TagType == 9
                            VIDEODATA
                        IF TagType == 18
                            SCRIPTDATA
```


在回放中，flv标记的时间顺序仅取决于flv时间戳。应忽略有效载荷数据格式中内置的任何定时机制。
<H5 id="a8-4-2">E.4.2音频标签</H5>

音频标签类似于SWF文件格式中的definesound标签。对于SWF中也支持的格式，FLV和SWF中的有效载荷数据是相同的。
<H5 id="a8-4-2-1">E.4.2.1音频数据</H5>

audioTagHeader包含音频特定的元数据

AudioTagHeader

``` javascript
Field               Type            Comment
SoundFormat         UB [4]          声音数据的格式。定义了以下值：
(见注释下表：为了特殊编码)              0=线性PCM，平台端
                                    1＝ADPCM
                                    2＝MP3
                                    3=线性PCM，小端
                                    4=16 kHz单声道Nellymoser
                                    5=8 kHz单声道Nellymoser
                                    6 = NelyMysor
                                    7=G.711 a-律对数pcm
                                    8=G.711μm对数pcm
                                    9 =保留
                                    10＝AAC
                                    11 = SeEX
                                    14＝MP3 8 kHz
                                    15=设备特定声音
                                    保留格式7、8、14和15。
                                    Flash Player 9、0115、0及更高版本支持AAC。
                                    Flash Player 10及更高版本支持speex。



SoundRate           UB [2]          采样率。定义了以下值：
                                    0＝5.5千赫
                                    1＝11千赫
                                    2＝22千赫
                                    3＝44千赫
SoundSize           UB [1]          每个音频样本的大小。此参数仅适用于未压缩格式。压缩格式总是在内部解码为16位。
                                    0=8位样本
                                    1=16位样本
SoundType           UB [1]          单声道或立体声
                                    0 =单声道
                                    1=立体声
AACPacketType       IF SoundFormat == 10
                    UI8             定义了以下值：
                                    0=AAC序列头
                                    1 = AAC原材料

```


格式3，线性PCM，存储原始PCM样本。如果数据为8位，则样本为无符号字节。如果数据是16位的，那么样本将存储为有符号的小尾数。如果数据是立体声的，则左右样本是交错存储的：左-右-左-右-等等。

格式0 pcm与格式3 pcm相同，只是格式0按照文件创建平台的尾数顺序存储16位pcm样本。因此，不应使用格式0。

8 kHz和16 kHz是特殊情况，因为声速场不能代表8或16 kHz的采样率。当在SoundFormat中指定Nellymser 8 kHz或Nellymser 16 kHz时，Flash播放器将忽略SoundRate和SoundType字段。对于其他Nellymser采样率，请指定正常的Nellymser SoundFormat，并像往常一样使用SoundRate和SoundType字段。

如果SoundFormat指示AAC，则SoundType应为1（立体声），SoundRate应为3（44 kHz）。但是，这并不意味着FLV中的AAC音频总是立体声的，44 kHz的数据。相反，flash播放器忽略这些值并提取通道，采样率数据编码在AAC比特流中。

如果SoundFormat指示为speex，则音频以16kHz的频率进行单采样压缩，声音速率应为0，声音大小应为1，声音类型应为0。有关存储在swf文件中的speex功能和限制的信息，请参阅http://www.adobe.com/go/swf_file_format上的swf文件格式规范。

音频数据段包含音频有效载荷。

AUDIODATA

``` javascript
Field	        Type	         Comment
IF Encrypted	                 详见附件F.FLV加密。
    Body	    EncryptedBody	按照第F.3.3节的规定加密的音频标签体。
ELSE
    Body        AudioTagBody
```


AudioTagBody保存音频有效负载。

AudioTagBody

``` javascript
Field	        Type	         Comment
SoundData
IF SoundFormat == 10
AACAUDIODATA
ELSE
Varies by format
```

<H5 id="a8-4-2-2">E.4.2.2 AACAUDIODATA</H5>

Flash Player 9、0115、0及更高版本支持AAC格式。

AACAUDIODATA

``` javascript
Field	    Type	                        Comment
Data        IF AACPacketType == 0	        
            udioSpecificConfig              AudioSpecificConfig在ISO中定义。
            ELSE IF AACPacketType == 1
            Raw AAC frame data in UI8 [ ]   14496～3。请注意，这与MP4/F4V文件中ESDS框的内容不同。


```

<H5 id="a8-4-3">E.4.3视频标签</H5>
视频标签类似于SWF文件格式的视频帧标签，其有效载荷数据相同。另请参见SWF文件格式规范，网址为http://www.adobe.com/go/swf_file_format。
<H5 id="a8-4-3-1">E.4.3.1视频数据</H5>

VideoTagHeader包含特定于视频的元数据

VideoTagHeader

``` javascript
Field	            Type	            Comment
Frame Type          UB [4]              视频帧的类型。定义了以下值：
                                        1=关键帧（对于AVC，可查找的帧）
                                        2=帧间（对于AVC，是不可查找的帧）
                                        3=一次性内框（仅H.263）
                                        4=生成的密钥帧（仅供服务器使用）
                                        5=视频信息/命令帧

CodecID             UB [4]              编解码器标识符。定义了以下值：
                                        2=Sorenson H.263
                                        3=屏幕视频
                                        4＝ON2 VP6
                                        5=ON2 VP6，带阿尔法通道
                                        6=屏幕视频版本2
                                        7 = AVC

AVCPacketType       IF CodecID == 7
                        UI8             定义了以下值：
                                        0=AVC序列头段
                                        1 = AVC NALU
                                        2=AVC序列结束（不需要或不支持低级NALU序列结束符
CompositionTime     IF CodecID == 7         
                        SI24            如果avcpacketype==1
                                        合成时间偏移
                                        否则
                                        零有关组成时间的说明，
                                        请参见ISO 14496-12，8.15.3。
                                        flv文件中的偏移量始终以毫秒为单位。


```


视频数据段包含视频元数据、可选加密元数据和视频有效负载。

VIDEODATA

``` javascript
Field	         Type	            Comment
IF Encrypted	                    详见附件F.FLV加密。
Body	        EncryptedBody	    视频标记体按照第F.3.3节的规定加密。
ELSE
Body
```


VideoTagBody

VideoTagBody包含视频帧有效负载。

VideoTagBody

``` javascript
Field               Type                                Comment
VideoTagBody        IF FrameType == 5                  视频帧有效载荷或帧信息 
                        UI8
                    ELSE (
                        IF CodecID == 2
                            H263VIDEOPACKET
                        IF CodecID == 3
                            SCREENVIDEOPACKET
                        IF CodecID == 4
                            VP6FLVVIDEOPACKET
                        IF CodecID == 5
                            VP6FLVALPHAVIDEOPACKET
                        IF CodecID == 6
                            SCREENV2VIDEOPACKET
                        IF CodecID == 7
                            AVCVIDEOPACKET
                    )                                  如果frametype==5，而不是视频有效负载，则视频数据主体包含一个具有以下含义的ui8：
                                                        0=客户端查找视频帧序列的开始
                                                        1=客户端查找视频帧序列结束 
除avcvideopacket以外的所有文件，请参阅SWF文件格式规范了解详细信息。
```

<H5 id="a8-4-3-2">E.4.3.2 AVCVIDEOPACKET</H5>

AVC视频包携带AVC视频数据的有效载荷。

AVCVIDEOPACKET

``` javascript
Field       Type	                        Comment
Data        IF AVCPacketType == 0
                AVCDecoderConfigurationRecord
            IF AVCPacketType == 1
                One or more NALUs (Full frames are required)
```


AVCDecoderConfigurationRecord的说明见ISO 14496-15，5.2.4.1。它包含的信息与存储在MP4/FLV文件中的AVCC框中的信息相同。
<H5 id="a8-4-4">E.4.4数据标签</H5>

数据标记封装了单个方法调用，通常在Flash播放器的Netstream对象上调用。数据标记由一个方法名和一组参数组成。
<H5 id="a8-4-4-1">E.4.4.1脚本数据</H5>

脚本数据段包含可选的加密元数据和脚本有效负载。

SCRIPTDATA

``` javascript
Field               Type	            Comment
IF Encrypted	                        See Annex F. FLV Encryption for details.
Body	        EncryptedBody	        ScriptTagBody encrypted as specified in Section F.3.3.
ELSE
Body            ScriptTagBody
```


ScriptTagBody包含以操作消息格式（AMF）编码的脚本数据，该格式是用于序列化ActionScript对象图的压缩二进制格式。AMF0的规范可从以下网址获得：http://opensource.adobe.com/wiki/display/blazeds/developer+documentation

ScriptTagBody

``` javascript
Field               Type                    Comment
Name                SCRIPTDATAVALUE	        方法或对象名称。scriptDataValue.Type=2（字符串）
Value               SCRIPTDATAVALUE         AMF参数或对象属性。scriptDataValue.Type=8（ECMA数组）
```

<H5 id="a8-4-4-2">E.4.4.2	SCRIPTDATAVALUE</H5>

ScriptDataValue记录包含类型化的ActionScript值。

SCRIPTDATAVALUE

``` javascript
Field               Type        Comment
Type                UI8         ScriptDataValue的类型。
                                定义了以下类型：
                                0＝数
                                1 =布尔
                                2 =字符串
                                3 =对象
                                4=movieclip（保留，不支持）
                                5 =空
                                6 =未定义
                                7 =参考文献
                                8＝ECMA阵列
                                9=对象结束标记
                                10=严格数组
                                11 =日期
                                12=长串
ScriptDataValue
                IF Type == 0     编写数据值脚本。
                DOUBLE
                IF Type == 1    布尔值为（scriptDataValue≠0）。
                UI8
                IF Type == 2
                SCRIPTDATASTRING
                IF Type == 3
                SCRIPTDATAOBJECT
                IF Type == 7
                UI16
                IF Type == 8
                SCRIPTDATAECMAARRAY
                IF Type == 10
                SCRIPTDATASTRICTARRAY
                IF Type == 11
                SCRIPTDATADATE
                IF Type == 12
                SCRIPTDATALONGSTRING

```

<H5 id="a8-4-4-3">E.4.4.3	SCRIPTDATADATE</H5>

脚本数据日期记录存储日期和时间。

SCRIPTDATADATE

``` javascript
Field                   Type        Comment
DateTime                DOUBLE      Number of milliseconds since Jan 1, 1970 UTC.
LocalDateTimeOffset     SI16        本地时间与UTC的时差（分钟）。对于位于英国格林威治以西的时区，该值为负数。英国格林威治以东的时区是正的。
```

<H5 id="a8-4-4-4">E.4.4.4	SCRIPTDATAECMAARRAY</H5>

A SCRIPTDATAECMAARRAY记录存储一个ecma数组。ECMA数组是一个关联数组，当actionscript数组包含非顺序索引时应使用该数组。所有的索引，无论是序数还是其他，都是字符串而不是整数。为了进行序列化，此类型与匿名ActionScript对象非常相似。该列表包含大约ecmaarrayllength个项目。ScriptDataObjectEnd记录在项目列表之后。

SCRIPTDATAECMAARRAY

``` javascript
Field                   Type                            Comment
ECMAArrayLength         UI32                            ECMA数组中的近似项数
Variables               SCRIPTDATAOBJECTPROPERTY [ ]    变量名和值列表
List Terminator         SCRIPTDATAOBJECTEND             列表终止符    
```

<H5 id="a8-4-4-5">E.4.4.5	SCRIPTDATALONGSTRING</H5>

scriptDataString和scriptDataLongstring记录存储字符串

SCRIPTDATALONGSTRING

``` javascript
Field           Type	Comment
StringLength    UI32	StringData length in bytes
StringData      STRING	String data, with no terminating NUL
```

<H5 id="a8-4-4-6">E.4.4.6	SCRIPTDATAOBJECT</H5>

ScriptDataObject记录对匿名ActionScript对象的属性进行编码。ScriptDataObjectEnd记录跟随属性列表。

SCRIPTDATAOBJECT

``` javascript
Field               Type                            Comment
ObjectProperties    SCRIPTDATAOBJECTPROPERTY [ ]	List of object properties
List Terminator     SCRIPTDATAOBJECTEND             List terminator
```

<H5 id="a8-4-4-7">E.4.4.7	SCRIPTDATAOBJECTEND</H5>

scriptDataObjectEnd记录终止scriptDataObjectProperty记录的列表。ScriptDataObjectEnd记录是一个带零长度字符串和对象结束标记的ScriptDataObjectProperty记录。

SCRIPTDATAOBJECTEND

``` javascript
Field           Type        Comment
ObjectEndMarker UI8 [3]     Shall be 0, 0, 9
```

<H5 id="a8-4-4-8">E.4.4.8	SCRIPTDATAOBJECTPROPERTY</H5>

ScriptDataObjectProperty记录定义ActionScript对象的对象属性或关联数组的变量。

SCRIPTDATAOBJECTPROPERTY

``` javascript
Field           Type                    Comment
PropertyName    SCRIPTDATASTRING        Name of the object property or variable
PropertyData    SCRIPTDATAVALUE         对象属性或变量的值和类型
```

<H5 id="a8-4-4-9">E.4.4.9	SCRIPTDATASTRICTARRAY</H5>

脚本数据严格数组记录存储严格数组。严格数组只包含顺序索引，而不存储在记录中。指数可以是密集的或稀疏的。索引之间稀疏区域中未定义的条目应序列化为未定义。该列表应包含严格的数组长度值。列表后面没有终止记录。

SCRIPTDATASTRICTARRAY

``` javascript
Field               Type	            Comment
StrictArrayLength   UI32                Number of items in the array
StrictArrayValue    SCRIPTDATAVALUE 
                [ StrictArrayLength ]   List of typed values
```

<H5 id="a8-4-4-10">E.4.4.10	SCRIPTDATASTRING</H5>

scriptDataString和scriptDataLongstring记录存储字符串。

脚本数据字符串记录可用于长度不超过65535个字符的字符串。

SCRIPTDATASTRING

``` javascript
Field           Type        Comment
StringLength    UI16        StringData length in bytes.
StringData      STRING      String data, up to 65535 bytes, with no terminating NUL
```

<h4 id="a8-5">E.5 onMetaData</h4>

flv元数据对象应包含在名为onmet adat a的scriptdata标记中。运行中的actionscript程序可通过net st r eam使用各种属性。Onmet Adat a Property公司。可用属性因创建FLV文件的软件而异。典型特性包括：

onMetadata properties

``` javascript
Property Name   Type        Comment
audiocodecid    Number	    文件中使用的音频编解码器ID（有关可用的SoundFormat值，请参阅E.4.2.1）
audiodatarate   Number      音频比特率（千比特/秒）
audiodelay      Number      音频编解码器引入的延迟（秒）
audiosamplerate Number      重播音频流的频率
audiosamplesize Number      单个音频样本的分辨率
canSeekToEnd    Boolean     表示最后一个视频帧是关键帧
creationdate    String      创建日期和时间
duration        Number      文件的总持续时间（秒）
filesize        Number      文件的总大小（字节）
framerate       Number      每秒帧数
height          Number      视频的高度（像素）
stereo          Boolean     指示立体声音频
videocodecid    Number      文件中使用的视频编解码器ID（有关可用的编解码器ID值，请参阅E.4.3.1）
videodatarate   Number      视频比特率（千比特/秒）
width           Number      视频的宽度（像素）
```

<h4 id="a8-6">E.6 flv中的xmp元数据</h4>

xmp元数据对象应包含在名为onxmpdat a的scriptData标记中。标记应及时放置。

0。标签应该在所有时间0之后，在所有时间0之前，在所有时间0之前，音频或视频标签，但读者不应该要求这样的排序。

XMPMetadata object

``` javascript
Property Name	Type	                Comment
liveXML         String or Long string	XMP元数据，根据XMP元数据规范格式化
```


For further details, see www.adobe.com/devnet/xmp/pdfs/XMPSpecificationPart3.pdf
<h3 id="a9">附件F.FLV加密</h3>
<h4 id="a9-1">F.1概述</h4>

1.加密头，包含解密flv所需的加密元数据，如加密

算法、密钥长度和内容加密密钥检索协议标识符在任何加密内容之前，在FLV头之后立即存储为脚本数据。

2.内容携带标签加密。

a.为了提高效率，可以选择只加密标签的子集，例如I帧。

b.如果标签被加密，那么包中的过滤器标志被打开。筛选标志指示

解码前需要对数据包进行预处理。加密筛选器在数据包中指定。不合规的玩家将忽略设置了筛选标志的标签，因为他们实际上有一个新的标签类型。

c.大多数元数据（例如，无论是音频或视频帧、关键帧还是I帧、编解码器类型）都是

保持清晰，以便服务器和客户端播放器可以处理元数据而无需解密内容。

d.根据加密算法和加密要求对内容进行加密。

关键。加密数据存储在包中。

此规范定义了头元数据和加密数据包的格式。
<h4 id="a9-2">F.2标题信息</h4>
<H5 id="a9-2-1">F.2.1 AdditionalHeader对象</H5>

在加密的FLV文件中，应存在额外的头对象，并且应包括加密头对象。

AdditionalHeader对象应包含在名为Additional Header的scriptData标记中。（注意名称中的竖线（“”））对象应该出现在flv的开头，时间戳为0，紧跟在onmet adat的scriptdata标记之后。这使FLV解码器能够在遇到任何加密标签之前访问加密元数据。

AdditionalHeader object

``` javascript
Property Name       Type                        Comment
Encryption          Encryption Header object	Encryption Header
```

<H5 id="a9-2-2">F.2.2加密头对象</H5>

encryption header对象包含解密flv所需的加密元数据。加密头对象

Encryption Header object

``` javascript
Property Name       Type        Comment
Version             Number      加密头的版本。
                                应为1或2，表示
                                加密格式。
                                1=FMRMS v1.x产品。
                                2=闪存访问2.0产品。
                                使用任一版本保护的内容都存在，
                                因此应用程序应能够同时使用两个版本的内容。
Method              string      Encryption method. Shall be ‘Standard’
Flags               Number      Encryption flags. Shall be 0.
Params          Standard Encoding 
                Parameters object       加密方法“标准”的参数
IF Version == 1
    SigFormat       String      在本文档中，没有提供有关sigformat的信息。
    Signature       Long string 本文件签名上未提供任何信息。
```

<H5 id="a9-2-3">F.2.3标准编码参数对象</H5>

此结构包含特定于“标准”加密方法的参数。

Standard Encoding Parameters object

``` javascript
Property Name           Type                Comment
Version                 Number              Version. Shall be 1.
EncryptionAlgorithm     String              加密算法。应为“aes-cbc”，
                                            其中规定所使用的加密为“aes-cbc”，
                                            并根据RFC 2630填充。

EncryptionParams        AES-CBC             加密算法“aes-cbc”的参数。
                        Encryption 
                        Parameters object

KeyInfo                 Key Information     获取解密密钥的信息
                        object	
```

<H5 id="a9-2-4">F.2.4 AES-CBC加密参数对象</H5>

这个结构包含特定于加密算法的参数，在本例中是aes-cbc_128。

AES-CBC Encryption Parameters object

``` javascript
Property Name           Type                Comment
KeyLength               Number              加密算法的密钥长度（字节）。应为16（即128位）
```

<H5 id="a9-2-5">F.2.5关键信息对象</H5>

密钥信息框包含用于检索用于解密示例的密钥的信息。这些框中包含的条目的详细信息以及DRM客户机用于检索密钥的机制不在本规范的范围内。

Key Information object

``` javascript
Property Name            Type        Comment
SubType                 String          如果encryptionheader.version==1
                                        aps'=（Adobe Policy Server）联机密钥协议协商协议
                                        否则
                                        FlashAccessv2'=联机密钥检索协议
Data                IF SubType == ‘APS’
                    Adobe Policy Server object
                    IF SubType == ’FlashAccessv2’
                    FlashAccessv2 object    子类型“aps”不再由符合要求的应用程序生成，因此没有提供进一步的信息
```

<H5 id="a9-2-6">F.2.6 FlashAccessv2对象</H5>

闪存访问服务器使用联机密钥检索协议提供解密密钥。

FlashAccessv2对象包含FlashAccessv2模块执行联机密钥检索所需的以下高级元素（这些元素的详细信息不在文档范围内）。

FlashAccessv2 object

``` javascript
Property Name       Type            Comment
Metadata            Long string     DRM客户机用于检索解密密钥的Base 64编码元数据。
```

<h4 id="a9-3">F.3内容加密</h4>

本节介绍如何加密FLV标记。

在加密的FLV文件中，每个FLV标记都可以指示其加密状态：

-筛选器标志可能指示在呈现数据包之前需要对其进行预处理。

-在版本2中，当设置过滤器标志时，选择性加密指示器可能进一步指示

数据包已加密。

无论文件是完全加密还是部分加密，在版本2中（encryptionheader.version==2），每个音频和视频包都应该设置flvtag.filter位。对于未加密的脚本数据，不应设置过滤位，使播放器能够定位onmetadata信息。

一小部分指定的字节保持清晰，以便在不解密其余内容的情况下启用智能客户端处理。
<h5 id="a9-3-1">F.3.1加密标记头</h5>

如果在flv标签中设置了filter标志，则在呈现前应对包内容进行预处理。加密标记头指定要应用的筛选器。过滤器指定加密类型并指示是否应用加密。

EncryptionTagHeader

``` javascript
Field               Type            Comments
NumFilters          UI8             Number of filters applied to the packet. Shall be 1.
FilterName          String          筛选器的名称。
                                    如果encryptionheader.version==1
                                    '加密'
                                    否则
                                    “SE”
                                    SE代表选择性加密。
Length              UI24            Length of FilterParams in bytes
```

<h5 id="a9-3-2">F.3.2滤波器参数</h5>

filterparams包含特定于解密方法的参数。

FilterParams

``` javascript
Field               Type                                Comments
FilterParams        IF FilterName = ‘Encryption’        特定于筛选器的参数。
                        EncryptionFilterParams
                    IF FilterName = ‘SE’
                        SelectiveEncryptionFilterParams
```


非选择性）加密的筛选器参数在encryptionfilterparams中定义。带有此字段的所有数据包都应加密。

EncryptionFilterParams

``` javascript
Field       Type        Comment
IV          UI8 [16]	包含16字节的AES-CBC IV数据。
```


选择加密的筛选参数在SelectiveEncryptionFilterParams中定义。

SelectiveEncryptionFilterParams

``` javascript
Field               Type            Comment
EncryptedAU         UB [1]          选择性加密指示器显示数据包是否加密。
                                    0=数据包未加密
                                    1=数据包已加密。
Reserved            UB [7]          Shall be 0
IV                  IF EncryptedAU == 1
                    UI8 [16]        仅当数据包加密时才存在。包含16字节的AES-CBC IV数据
```

<h5 id="a9-3-3">F.3.3加密正文</h5>

如果包是加密的，那么主体应包含本节所述的加密主体，否则主体应包含纯文本数据。

EncryptedBody

``` javascript
Field       Type                    Comment
Content     UI8 [Plaintext Length]	密码文本
Padding     UI8 [Padding Length]	加密填充。
```

<h5 id="a9-3-3-1">3.3.1填充</h5>

所有加密样本应填充到块密码块长度的倍数。填料方案应如RFC 2630中所述，复制如下：

块密码期望输入数据是k八位字节的倍数（对于aes 128，是16个八位字节的倍数），其中k大于1。对于此类算法，输入应在尾端填充k-（length mod k）八位字节，所有八位字节的值均为k-（length mod k），其中length是输入的长度。

填充使块大小达到块密码块长度的下一整数倍。即使纯文本可以被块长度平均整除，也会出现填充。

示例：如果k为16字节，长度为32字节，填充长度为16字节，包含0x10，块大小为48字节。
<h4 id="a9-4">F.4加密和元数据</h4>

当FLV加密时，OnMetadata脚本数据应始终保持清晰。

不同的flv解析器需要这一点来成功地传输flv，媒体播放器也需要这一点来为使用提供一些上下文信息。