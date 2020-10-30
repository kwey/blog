---
title: js工具函数
tags: 'es6'
categories: 'Javascript'
top_img: '../../img/js.jpg'
---

``` javascript
        
        
// guid
function guid(count) {
    let out = ''
    for (let i = 0; i < count; i += 1) {
        /* eslint-disable*/
        out += (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1)
        /* eslint-enable */
        }
    return out
}
// 下载文件
function download(o) {
    const obj = {
        text: '',
        type: 'text/plain;charset=utf-8',
        fileName: 'text.txt',
        ...o
    }
    const blob = new Blob([obj.text], { type: obj.type })
    if (window.navigator.msSaveOrOpenBlob) {
    // For IE
        navigator.msSaveBlob(blob, obj.fileName)
    } else {
        const link = document.createElement('a')
        link.href = window.URL.createObjectURL(blob)
        link.download = obj.fileName
        link.style.display = 'none'
        link.target = '_blank'
        link.click()
        window.setTimeout(() => {
            link.remove()
            window.URL.revokeObjectURL(link.href)
        }, 3000)
    }
}
function upload(callback) {
// const uploader: any = $('<input type="file">');
const uploader: HTMLElement = document.createElement('input');
uploader.setAttribute('type', 'file');
(() => {
        uploader.click();
    })();
const reImport = () => {
        const reader = new FileReader();
        reader.readAsText(uploader['files'][0]);
        reader.onload = () => {
            callback(reader);
        };
};
if (!Utils.browser.version.trident && !Utils.browser.version.edge) {
        uploader.addEventListener('change', () => {
            reImport();
        });
} else {
        // IE系的浏览器因为浏览器本身的bug无法用click()触发input元素的change事件,这里做下处理.
        window['setTimeout'](() => {
            if (uploader.getAttribute('value').length > 0) {
                reImport();
            }
    }, 0);    
}
}
//是否能使用webp图片
function canUseWebP() {
    return !! [].map && document.createElement('canvas').toDataURL('image/webp').indexOf('data:image/webp') === 0;
}


function getCookie(cookieName) {
    const defaultResult = '';
    if (cookieName == null) {
        return defaultResult;
    }
    const cookies = document.cookie.split(';');
    const decodeCookieName = decodeURIComponent(cookieName);
    for (let i = 0; i < cookies.length; i++) {
        const [key, value] = cookies[i].trim().split('=');
        if (decodeURIComponent(key) === decodeCookieName) {
            return decodeURIComponent(value);
        }
}
return defaultResult;
}
function setCookie(name, value, days = 365) {
    const date = new Date();
    const encodeName = encodeURIComponent(name);
    const encodeValue = encodeURIComponent(value);
    date.setTime(date.getTime() + days * 24 * 60 * 60 * 1000);
    document.cookie = `${encodeName}=${encodeValue};
                    expires=${date.toUTCString()};
                    path=/; domain=.bilibili.com`;
}
function getLocalSettings(key, storage = localStorage): string {
    if (window[storage] && storage.getItem) {
        return storage.getItem(key);
    } else {
        return this.getCookie(key);
    }
}
function setLocalSettings(key, val, storage = localStorage ) {
    if (window[storage] && storage.setItem) {
        try {
            return storage.setItem(key, val);
        } catch (e) {
        }
} else {
        return this.setCookie(key, val);
    }
}

function  formatDate(date, format){
    date = date || new Date();
    format = format || 'yyyy-MM-dd mm:ss';
    const mapping = {
        'M+': date.getMonth() + 1,
        'd+': date.getDate(),
        'h+': date.getHours(),
        'm+': date.getMinutes(),
        's+': date.getSeconds(),
        'q+': Math.floor((date.getMonth() + 3) / 3),
        'S+': date.getMilliseconds(),
    };
    if (/(y+)/i.test(format)) {
        format = format.replace(
            RegExp.$1,
            date.getFullYear().toString().substr(4 - RegExp.$1.length)
                    );
    }
    for (const k in mapping) {
        if (new RegExp(`(${k})`).test(format)) {
            const n = RegExp.$1.length === 1
                    ? mapping[k]
                    : ('00' + mapping[k]).substr(mapping[k].toString().length);
            format = format.replace(RegExp.$1, n);
        }
}
return format;
}

function htmlEncode(str) {
    return str.replace(/&/g, '&amp;')
        .replace(/</g, '>')
        .replace(/>/g, '>')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#x27;')
        .replace(/\\//g, '&#x2f;')
        .replace(/\
        /g, '
        ');
}

function getDecoder() {
    if (window['TextDecoder']) {
        return new window['TextDecoder']();
    }
return {
        decode: (buf: any) => decodeURIComponent(window['escape'](
            String.fromCharCode.apply(String, new Uint8Array(buf)))),
    };
};

decoder = getDecoder();

decode(arrayBuffer.slice(0, 4)), // 4字节tag



function getScrollTop() {
    return (document.documentElement
        && document.documentElement.scrollTop
        ) || document.body.scrollTop;
}

function setScrollTop(value) {
        window.scrollTo(0, value);
        return value;
    }

var requestAnimFrame = (function() {
        return window.requestAnimationFrame ||
        window.webkitRequestAnimationFrame ||
        window.mozRequestAnimationFrame ||
        function(callback) {
            window.setTimeout(callback, 1000 / 60);
        };
})();
function scrollTo(to, duration) {
        if (duration < 0) {
            setScrollTop(0, to);
                    return;    
        }
        var diff = to - getScrollTop();
        if (diff === 0) return
        var step = diff / duration * 10;
        requestAnimationFrame(
            function() {
                if (Math.abs(step) > Math.abs(diff)) {
                    setScrollTop(getScrollTop() + diff);
                    return;
                }
            setScrollTop(getScrollTop() + step);
            if (diff > 0 && getScrollTop() >= to || diff < 0 && getScrollTop() <= to) {
                    return;
                }
            scrollTo(to, duration - 16);
            }
    );

}
function deepClone(values) {
    var copy;
    // Handle the 3 simple types, and null or undefined
    if (null == values || "object" != typeof values) return values;
    // Handle Date
    if (values instanceof Date) {
        copy = new
        Date();
        copy.setTime(values.getTime());
        return copy;
    }
    // Handle Array
    if (values instanceof Array) {
            copy = [];
            for (var i = 0,
            len = values.length; i < len; i++) {
                copy[i] = deepClone(values[i]);
            }
        return copy;
    }
    // Handle Object
    if (values instanceof Object) {
            copy = {};
        for (var attr in values) {
                if (values.hasOwnProperty(attr)) copy[attr] = deepClone(values[attr]);
            }
        return copy;
    }
    throw  new Error("Unable to copy values! Its type isn't supported.");
    
}
function assign(target, ...rest) {
    if (target == null) {
        throw new TypeError('Cannot convert undefined or null to object');
    }
    const to = Object(target);
    for (let i = 0; i < rest.length; i++) {
        const nextSource = rest[i];
        if (nextSource != null) { // Skip over if undefined or null
            for (const nextKey in nextSource) {
                // Avoid bugs when hasOwnProperty is shadowed
                if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
                    to[nextKey] = nextSource[nextKey];
                }
            }
        }
    }
    return to;
}
// 防抖用于search搜索。resize

function debounce(fn, wait) {
    var timer = null;
    return function () {
        var context = this
        var args = arguments
        if (timer) {
            clearTimeout(timer);
            timer = null;
        }
        timer = setTimeout(function () {
            fn.apply(context, args)
        }, wait)
    }
}
// 节流用于鼠标点击。scroll

function throttle(fn, gapTime) {
    let _lastTime = null;

    return function () {
        let _nowTime = + new Date()
        if (_nowTime - _lastTime > gapTime || !_lastTime) {
            fn();
            _lastTime = _nowTime
        }
    }
}
function browser() {
    const ua = navigator.userAgent.toLowerCase();
    const isSafari = 
        /(webkit)[ \\/]([\\w.]+).*(version)[ \\/]([\\w.]+).*(safari)[ \\/]([\\w.]+)/.test(ua)
        || /(version)(applewebkit)[ \\/]([\\w.]+).*(safari)[ \\/]([\\w.]+)/.test(ua);
    const match = /(chrome)[ \\/]([\\w.]+)/.exec(ua) || '';
    const matched = {
        browser:  match[1] || '',
        version:  match[2] || '0',
    };
    let version = 0;
    if (matched.browser) {
        version =  parseInt(matched.version, 10);
    }
    return {
        // 浏览器
        browser: matched.browser,
        version: version,
        
        // 系统
        linux: /Linux/i.test(ua),
        
        // 内核
        webKit: /AppleWebKit/i.test(ua),
        gecko: /Gecko/i.test(ua) && !/KHTML/i.test(ua),
        trident: /Trident/i.test(ua),
        presto: /Presto/i.test(ua),
        
        // 手机
        mobile: /AppleWebKit.*Mobile.*/i.test(ua),
        iOS: /Mac OS X[\\s_\\-\\/](\\d+[.\\-_]\\d+[.\\-_]?\\d*)/i.test(ua),
        iPhone: /iPhone/i.test(ua),
        iPad: /iPad/i.test(ua),
        webApp: !/Safari/i.test(ua),
        android: /Android/i.test(ua),
        windowsPhone: /Windows Phone/i.test(ua),
        microMessenger: /MicroMessenger/i.test(ua),
        
        // 桌面
        msie: /msie [\\w.]+/i.test(ua),
        edge: /edge/i.test(ua),
        edgeBuild16299: /(\\s|^)edge\\/16.16299(\\s|$)/i.test(ua),
        safari: isSafari,
        safariSupportMSE: isSafari && (/Version\\/1\\d/i).test(ua),
    };
},
// 暴露出这些属性和方法
export {
    guid,
    download,
    upload,

    canUseWebP,
    getCookie,

    setCookie,

    getLocalSettings,
    setLocalSettings ,
    formatDate,
    htmlEncode，

    getDecoder，

    setScrollTop,

    setScrollTop,

    scrollTo,    
    deepClone,

    assign,

    browser
}
```