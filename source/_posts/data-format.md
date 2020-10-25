---
title: Date对象 format
tags: 'Javascript'
categories: 'web'
# top_img: '/img/404.jpg'
---
Date对象的内置方法以及自定义格式化

``` bash
var d = new Date();
console.log(d); // 输出：Mon Nov 04 2013 21:50:33 GMT+0800 (中国标准时间)
console.log(d.toDateString()); // 日期字符串，输出：Mon Nov 04 2013
console.log(d.toGMTString()); // 格林威治时间，输出：Mon, 04 Nov 2013 14:03:05 GMT
console.log(d.toISOString()); // 国际标准组织（ISO）格式，输出：2013-11-04T14:03:05.420Z
console.log(d.toJSON()); // 输出：2013-11-04T14:03:05.420Z
console.log(d.toLocaleDateString()); // 转换为本地日期格式，视环境而定，输出：2013年11月4日
console.log(d.toLocaleString()); // 转换为本地日期和时间格式，视环境而定，输出：2013年11月4日 下午10:03:05
console.log(d.toLocaleTimeString()); // 转换为本地时间格式，视环境而定，输出：下午10:03:05
console.log(d.toString()); // 转换为字符串，输出：Mon Nov 04 2013 22:03:05 GMT+0800 (中国标准时间)
console.log(d.toTimeString()); // 转换为时间字符串，输出：22:03:05 GMT+0800 (中国标准时间)
console.log(d.toUTCString()); // 转换为世界时间，输出：Mon, 04 Nov 2013 14:03:05 GMT
```
或者自定义

```bash
formatDate(date: Date | null, format?: string): string {
    const d = date || new Date();
    let f = format || 'yyyy-MM-dd mm:ss';
    const mapping = {
        'M+': d.getMonth() + 1,
        'd+': d.getDate(),
        'h+': d.getHours(),
        'm+': d.getMinutes(),
        's+': d.getSeconds(),
        'q+': Math.floor((d.getMonth() + 3) / 3),
        'S+': d.getMilliseconds(),
    };
    if (/(y+)/i.test(f)) {
            f = f.replace(RegExp.$1, d.getFullYear().toString().substr(4 - RegExp.$1.length));
    }
    for (const k in mapping) {
            if (new RegExp(`(${k})`).test(f)) {
                const n = RegExp.$1.length === 1 ? mapping[k] : ('00' + mapping[k]).substr(mapping[k].toString().length);
            f = f.replace(RegExp.$1, n);
        }
    }
    return f;
 }
```