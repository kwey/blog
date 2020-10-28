---
title: parseInt() 函数
tags: 'Javascript'
categories: 'web'
top_img: '../../img/js.jpg'
---
> parseInt(string, radix)函数可解析一个字符串，并返回一个整数。

|param | 详情        |
|----   |     ----         |
|string | 必需。要被解析的字符串。 |
|radix  |可选。表示要解析的数字的基数。该值介于 2 ~ 36 之间。 如果省略该参数或其值为 0，则数字将以 10 为基础来解析。如果它以 “0x” 或 “0X” 开头，将以 16 为基数。如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN。|

### 返回值

返回解析后的数字。
当参数 radix 的值为 0，或没有设置该参数时，parseInt() 会根据 string 来判断数字的基数。
举例，如果 string 以 "0x" 开头，parseInt() 会把 string 的其余部分解析为十六进制的整数。如果 string 以 0 开头，那么 ECMAScript v3 允许 parseInt() 的一个实现把其后的字符解析为八进制或十六进制的数字。如果 string 以 1 ~ 9 的数字开头，parseInt() 将把它解析为十进制的整数。
> 注意
* 只有字符串中的第一个数字会被返回。

* 开头和结尾的空格是允许的。

* 如果字符串的第一个字符不能被转换为数字，那么 parseInt() 会返回 NaN。

*  radix参数为n 将会把第一个参数看作是一个数的n进制表示，而返回的值则是十进制的。例如：

``` javascript
parseInt('123', 5) 
// 将'123'看作5进制数，返回十进制数38 =&gt; 1*5^2 + 2*5^1 + 3*5^0 = 38
```
``` javascript
// 以下例子均返回15:
parseInt("0xF", 16);
parseInt("F", 16);
parseInt("17", 8);
parseInt(021, 8);
parseInt("015", 10);   // parseInt(015, 10); 返回 15
parseInt(15.99, 10);
parseInt("15,123", 10);
parseInt("FXX123", 16);
parseInt("1111", 2);
parseInt("15 * 3", 10);
parseInt("15e2", 10);
parseInt("15px", 10);
parseInt("12", 13);
```
``` javascript
// 以下例子均返回 NaN:
parseInt("Hello", 8); // 根本就不是数值
parseInt("546", 2);   // 除了“0、1”外，其它数字都不是有效二进制数字
```
``` javascript
// 以下例子均返回 -15：

parseInt("-F", 16);
parseInt("-0F", 16);
parseInt("-0XF", 16);
parseInt(-15.1, 10);
parseInt(" -17", 8);
parseInt(" -15", 10);
parseInt("-1111", 2);
parseInt("-15e1", 10);
parseInt("-12", 13);
```
``` javascript
// 下例中全部返回 4:

parseInt(4.7, 10);
parseInt(4.7 * 1e22, 10); // 非常大的数值变成 4
parseInt(0.00000000000434, 10); // 非常小的数值变成 4
```
``` javascript
// 下面的例子返回 224

parseInt("0e0",16);
```
## 没有指定 radix 参数时的八进制解析
尽管 ECMAScript 3 已经不赞成这种做法，且 ECMAScript 5 已经禁止了这种做法，但是仍然有很多实现环境仍然把以 0 开头的数值字符串（numeric string）解释为一个八进制数。下面的例子可能返回八进制的结果，也可能返回十进制的结果。总是指定一个基数（radix）可以避免这种不
``` javascript
// 可靠的行为。
parseInt("0e0"); 
// 0

parseInt("08"); 
// 0, '8' 不是八进制数字.<span aria-hidden="true">
```
## ECMAScript 5 移除了八进制解析

ECMAScript 5 规范不再允许parseInt函数的实现环境把以0字符开始的字符串作为八进制数值。ECMAScript 5 陈述如下：

>根据给定radix，parseInt函数产生一个由字符串参数内容解析过来的整数值。字符串中开头的空白会被忽略。如果radix没有指定或者为0，参数会被假定以10为基数来解析，如果数值以字符对0x或0X开头，会假定以16为基数来解析。

这与ECMAScript 3有所不同，ECMAScript 3仅仅是不提倡这种做法但并没有禁止这种做法。
直至2013年，很多实现环境并没有采取新的规范所规定的做法, 而且由于必须兼容旧版的浏览器，所以永远都要明确给出radix参数的值.







