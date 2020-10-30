---
title: JS HEX十六进制与RGB, HSL颜色的相互转换
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---

> JavaScript颜色转换的核心就是进制间的转换。RGB格式其实就是十进制表示法，所以，十六进制颜色与RGB颜色的转换就是十六进制与十进制之间的转换。


十六进制转换为十进制相对容易些，核心代码如下示例：parseInt("0xFF")，其结果就是255，”0x”就表明当前是16进制，由于parseInt后面无参数，默认就是转换为10进制了。

## 一：十进制----->十六进制颜色
``` javascript
getHexColor(colorValue: number): string {
    return '#' + ('000000' + colorValue.toString(16)).slice(-6);
},
```
## 二：十六进制----->rgb颜色

``` javascript
    
var hexToRgb = function(hex) {
        var rgb = [];
        hex = hex.substr(1);//去除前缀 # 号
        if (hex.length === 3) { // 处理 "#abc" 成 "#aabbcc"
            hex = hex.replace(/(.)/g, '$1$1');
        }
        hex.replace(/../g, function(color){
            rgb.push(parseInt(color, 0x10));//按16进制将字符串转换为数字
        });
        return "rgb(" + rgb.join(",") + ")";
    };
```
## 三：rgb----->十六进制颜色## 
``` javascript

var rgbToHex = function(rgb) {
        // rgb(x, y, z)
        var color = rgb.toString().match(/\\d+/g); // 把 x,y,z 推送到 color 数组里
        var hex = "#";
        for (var i = 0; i &lt; 3; i++) {
            // 'Number.toString(16)' 是JS默认能实现转换成16进制数的方法.
            // 'color[i]' 是数组，要转换成字符串.
            // 如果结果是一位数，就在前面补零。例如： A变成0A
            hex += ("0" + Number(color[i]).toString(16)).slice(-2);
        }
    return hex;
};
```
## 四：十进制----->rgb颜色
``` javascript
    
方法一：
function getColor(number) {
    const alpha = number > 24 &amp; 0xff;
    const red = number > 16 &amp; 0xff;
    const green = number > 8 &amp; 0xff;
    const blue = number &amp; 0xff;
    return `rgba(${red}, ${green}, ${blue}, ${alpha})`;
}
方法二：
function getColor(number) {    
    let color = number;    
    const blue = parseInt(color % 0x100, 10);
    color = color > 8;    
    const green = parseInt(color % 0x100, 10);    
    color = color > 8;    
    const red = parseInt(color % 0x100, 10);    
    const alpha = (parseInt(color > 8, 10) / 0xFF).toFixed(1);       
    return  `rgba(${red}, ${green}, ${blue}, ${alpha})`;   
    }
```

    
## 五：hsl----->rgb
``` javascript

/**
    * HSL颜色值转换为RGB. 
    * 换算公式改编自 http://en.wikipedia.org/wiki/HSL_color_space.
    * h, s, 和 l 设定在 [0, 1] 之间
    * 返回的 r, g, 和 b 在 [0, 255]之间
    *
    * @param   Number  h       色相
    * @param   Number  s       饱和度
    * @param   Number  l       亮度
    * @return  Array           RGB色值数值
    */
function hslToRgb(h, s, l) {
        var r, g, b;
    
        if(s == 0) {
            r = g = b = l; // achromatic
        } else {
            var hue2rgb = function hue2rgb(p, q, t) {
                if(t &lt; 0) t += 1;
                if(t > 1) t -= 1;
                if(t &lt; 1/6) return p + (q - p) * 6 * t;
                if(t &lt; 1/2) return q;
                if(t &lt; 2/3) return p + (q - p) * (2/3 - t) * 6;
                return p;
            }

        var q = l &lt; 0.5 ? l * (1 + s) : l + s - l * s;
        var p = 2 * l - q;
        r = hue2rgb(p, q, h + 1/3);
        g = hue2rgb(p, q, h);
        b = hue2rgb(p, q, h - 1/3);
    }

    return [Math.round(r * 255), Math.round(g * 255), Math.round(b * 255)];
}
```
## 六：rgb----->hls
``` javascript
    
/**
    * RGB 颜色值转换为 HSL.
    * 转换公式参考自 http://en.wikipedia.org/wiki/HSL_color_space.
    * r, g, 和 b 需要在 [0, 255] 范围内
    * 返回的 h, s, 和 l 在 [0, 1] 之间
    *
    * @param   Number  r       红色色值
    * @param   Number  g       绿色色值
    * @param   Number  b       蓝色色值
    * @return  Array           HSL各值数组
    */
function rgbToHsl(r, g, b) {
        r /= 255, g /= 255, b /= 255;
        var max = Math.max(r, g, b), min = Math.min(r, g, b);
        var h, s, l = (max + min) / 2;
    
        if (max == min){ 
            h = s = 0; // achromatic
        } else {
            var d = max - min;
            s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
            switch(max) {
                case r: h = (g - b) / d + (g &lt; b ? 6 : 0); break;
                case g: h = (b - r) / d + 2; break;
                case b: h = (r - g) / d + 4; break;
            }
        h /= 6;
    }

    return [h, s, l];
}
```


## 七：rgb---->hsb/hsv
``` javascript

function rgbToHsv(r, g, b) {
        r = r / 255;
        g = g / 255;
        b = b / 255;
        var h, s, v;
        var min = Math.min(r, g, b);
        var max = v = Math.max(r, g, b);
        var l = (min + max) / 2;
        var difference = max - min;
    
        if (max == min) {
            h = 0;
        } else {
            switch (max) {
            case r:
                h = (g - b) / difference + (g &lt; b ? 6 : 0);
                break;
            case g:
                h = 2.0 + (b - r) / difference;
                break;
            case b:
                h = 4.0 + (r - g) / difference;
                break;
            }
        h = Math.round(h * 60);
    }
    if (max == 0) {
            s = 0;
        } else {
            s = 1 - min / max;
        }
    s = Math.round(s * 100);
    v = Math.round(v * 100);
    return [h, s, v];
}
```