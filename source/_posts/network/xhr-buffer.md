---
title: XHR获取arraybuffer
tags: 'xhr'
categories: 'brower'
top_img: '/img/brower.jpeg'
---
> ArrayBuffer对象代表原始的二进制数据，TypedArray视图用来读写简单类型的二进制数据，DataView视图用来读写复杂类型的二进制数据。


``` javascript
        
1. 文件格式 (mask file layout)

1.0 综述 (overview)
-----------------------------------
| mask file header |
-----------------------------------
| mask frame indexing table |
-----------------------------------
| ... ... |
| ... ... |
-----------------------------------
| mask frames data segment |
-----------------------------------
图 1.0

总体而言, 文件的格式为: 文件头 + 帧索引表 + 若干帧数据段, 这三部分数据紧邻地存放于mask文件
中, 位置关系如图1.0所示.

1.1 文件头 (mask file header)

| 1 bytes   |  1 bytes  |   1 byte  |   1 bytes  |    1 bytes   |
--------------------------------------------------------
| file tag  |  version  | codec_id  |  reserved  | entry_num |
--------------------------------------------------------
图 1.1
说明:
文件头固定为16字节, 位于mask文件的前16字节. 文件头的组成由图2.1所示
file tag: 表示文件标识, 固定值为 \\"AAAA\\", 占用4字节, 可以视作 magic number
version: 表示文件版本号, 占用4字节, 网络序, 无符号整型, 目前合法值为 1, 遇到高版本需视为无效文件
codec_id: 表示编码方式, 占用1字节, 无符号整型, 描述了帧和帧数据段的编码格式, 目前合法值为:

| codec_id | 帧编码方式 | 帧数据段格式 |
--------------------------------------------------------------------------
| 0x0 | bitstream | 一系列帧按照pts_time_ms从小到大紧密排列, 而后使用gzip压缩 |
---------------------------------------------------------------------------
| 0x1 | svg | 一系列帧按照pts_time_ms从小到大紧密排列, 不压缩 |
------------------------------------------------------------------------
| 0x2 | svg | 一系列帧按照pts_time_ms从小到大紧密排列, 后使用gzip压缩 | 
-----------------------------------------------------------------------
图 1.2

reserved: 保留字段, 占用*字节, 目前填充为0
entry_num: 帧索引表项的个数, 占用*字节, 网络序,无符号整型, 帧索引表项的长度固定为*字节

1.2 帧索引表 (frame indexing table)

|     8 bytes      |        8 bytes       |
-----------------------------------------------------------
| pts_time_ms      |      file_offset      |
-----------------------------------------------------------
| pts_time_ms      |     file_offset       |
-----------------------------------------------------------
| ... ... | ... ... |
| ... ... | ... ... |
-----------------------------------------------------------
| pts_time_ms     | file_offset          |
-----------------------------------------------------------
图 1.3
说明:
帧索引表由若干个长度相同的表项 (entry) 紧密排列组成, 每一个表项的长度固定为16字节每个表项的构成如
图1.3所示: 由 pts_time_ms 和 file_offset 组成

帧索引表用于在文件中索引每个帧, 更准确地, 每一个表项均指向了文件中的一个帧数据段: 表项
中的pts_time_ms是该帧数据段所包含的帧的起始pts_time, 表项中的file_offset就是该帧数据段
在文件中的偏移量

pts_time_ms: 8字节, 网络序, 无符号整型, 本表项指向的帧数据段的起始pts_time, 单位是ms
file_offset: 8字节, 网络序, 无符号整型, 本表项指向的帧数据段在mask文件的偏移

特别地, 帧索引表中的表项按照其pts_time_ms大小顺序存放, 这使得我们能快速检索某个已知其pts_time_ms的
帧所在的帧数据段, 并且, 如果表项A在B前面, 那么A所指向的帧数据段的长度为B.file_offset - A.file_offset
, 对于最后一个表项, 则是其file_offset到文件尾的长度

1.3 帧数据段 (mask frames data segment)

----------------------------------------------------------------------
| mask frames sorted by pts_time_ms, optionally compressed |
----------------------------------------------------------------------
| ... ... |
| ... ... |
----------------------------------------------------------------------
| mask frames sorted by pts_time_ms, optionally compressed |
----------------------------------------------------------------------
图 1.4
说明:
每一个帧数据段都包含了若干连续的帧, 这些帧按照其pts_time_ms从大到小紧密排列, 是否压缩取决
于codec_id值

2. 编码 (mask codec id)

2.1 codec_id = 0x0 (bitstream, gzip compressed)

帧格式:

| 2 bytes  |  2 bytes  |    8 bytes      |  (width * height)/8 bytes |
----------------------------------------------------------------
|   width   |  height   | pts_time_ms |           data                      | 
----------------------------------------------------------------
图 2.1 bitstream 编码方式

说明:
帧的组成如图2.1所示, 由: 帧宽度 + 帧高度 + 帧PTS + 帧数据 组成
width: 帧的宽度, 占用2字节, 网络序, 无符号整型
height: 帧的高度, 占用2字节, 网络序, 无符号整型
pts_time_ms: 帧的pts时间, 占用8字节, 网络序, 无符号整型, 单位 ms
data: 帧的二进制数据, 占用 (width * height)/8 字节, 每个bit位代表一个像素点, 宽度优先存储

帧数据段格式:
一系列帧按照其pts_time_ms从小到大紧密排列, 之后使用gzip压缩算法压缩而成, gzip level 9

2.2 codec_id = 0x1 (svg)

帧格式:

| 4 bytes    |       8 bytes       | data_size bytes |
-------------------------------------------------------------
| data_size  |  pts_time_ms        |        data     |
-------------------------------------------------------------
图 2.2 svg 编码方式

说明:
帧的组成如图2.2所示, 由: 帧数据长度 + 帧PTS + 帧数据 组成
data_size: 帧数据的长度, 占用4字节, 网络序, 无符号整型, 
帧数据长度不包含data_size字段和pts_time_ms字段本身
pts_time_ms: 帧的pts时间, 占用8字节, 网络序, 无符号整型, 单位 ms
data: 帧的二进制数据, 占用 data_size 字节, svg格式

帧数据段格式:
一系列帧按照其pts_time_ms从小到大紧密排列, 不压缩

2.3 codec_id = 0x2 (svg, gzip compressed)

帧格式:

|    4 bytes  |     8 bytes     | data_size bytes |
-------------------------------------------------------------
| data_size   | pts_time_ms     |        data     |
-------------------------------------------------------------
图 2.2 svg 编码方式

说明:
帧的组成如图2.2所示, 由: 帧数据长度 + 帧PTS + 帧数据 组成
data_size: 帧数据的长度, 占用4字节, 网络序, 帧数据长度不包含data_size字段和pts_time_ms字段本身
pts_time_ms: 帧的pts时间, 占用8字节, 网络序, 单位 ms
data: 帧的二进制数据, 占用 data_size 字节, svg格式

帧数据段格式:
一系列帧按照其pts_time_ms从小到大紧密排列, 之后使用gzip压缩算法压缩而成, gzip level 9


        xhr获取指定长度、位置的arraybuffer


    getData(data: ApiConfig) {
            let xhr = this.xhr = new XMLHttpRequest();
            xhr.open('GET', data.url, true);
            xhr.responseType = 'arraybuffer';
            xhr.onload = () => {
                    data.success(xhr['response']);
                };
            xhr.onerror = (error: any) => {
                    // data.error(error);
                };
            if (data.withCredentials) {
                    xhr.withCredentials = true;
                }
            if (data.range) {
                // 需要服务端设置允许请求头
                xhr.setRequestHeader('Range', data.range);
            }
            xhr.send();
    }
    
getBufferRange(from: number, to: number, cb: Function) {
    
    const range = to === -1 ? `bytes=${from}-` : `bytes=${from}-${to}`;
    getData（{
        url,
        range,
        success(data) {
            cb(data)
            console.log(data)
        }
    }）
}
```