---
title: 正则表达式基本语法
tags: 'Javascript'
categories: 'web'
top_img: '/img/js.jpg'
---


``` javascript

"^The"：表示所有以"The"开始的字符串（"There"，"The cat"等）；
"of despair$"：表示所以以"of despair"结尾的字符串；
"^abc$"：表示开始和结尾都是"abc"的字符串——呵呵，只有"abc"自己了；
"notice"：表示任何包含"notice"的字符串。

"ab*"：表示一个字符串有一个a后面跟着零个或若干个b。
（"a", "ab", "abbb",……）；
"ab+"：表示一个字符串有一个a后面跟着至少一个b或者更多；
"ab?"：表示一个字符串有一个a后面跟着零个或者一个b；
"a?b+$"：表示在字符串的末尾有零个或一个a跟着一个或几个b。

必须要指定下限

"ab{2}"：表示一个字符串有一个a跟着2个b（"abb"）；
"ab{2,}"：表示一个字符串有一个a跟着至少2个b；
"ab{3,5}"：表示一个字符串有一个a跟着3到5个b。

"hi|hello"：表示一个字符串里有"hi"或者"hello"；
"(b|cd)ef"：表示"bef"或"cdef"；
"(a|b)*c"：表示一串"a""b"混合的字符串后面跟一个"c"；

. 表示任意字符

"a.[0-9]"：表示一个字符串有一个"a"后面跟着一个任意字符和一个数字；
"^.{3}$"：表示有任意三个字符的字符串（长度为3个字符）；


方括号表示某些字符允许在一个字符串中的某一特定位置出现：
"[ab]"：表示一个字符串有一个"a"或"b"（相当于"a¦b"）；
"[a-d]"：表示一个字符串包含小写的'a'到'd'中的一个（相当于"a|b|c|d"或者"[abcd]"）；
"^[a-zA-Z]"：表示一个以字母开头的字符串；
"[0-9]%"：表示一个百分号前有一位的数字；
",[a-zA-Z0-9]$"：表示一个字符串以一个逗号后面跟着一个字母或数字结束。




```
<h1>特殊字符    <a href="https://www.w3cschool.cn/tools/index?name=create_reg" target="_blank" style="font-size: 14px;">正则表达式在线生成工具</a>      <a href="https://www.w3cschool.cn/regexp/tfua1pq5.html" target="_blank" style="font-size: 14px;">W3C文档</a></h1><table cellspacing="1" cellpadding="3"><tbody><tr><td>字符</td><td>含意</td></tr><tr><td>\\</td><td>
做为转意，即通常在"\\"后面的字符不按原来意义解释，如/b/匹配字符"b"，当b前面加了反斜杆后/\\b/，转意为匹配一个单词的边界。 
-或- 
对正则表达式功能字符的还原，如"*"匹配它前面元字符0次或多次，/a*/将匹配a,aa,aaa，加了"\\"后，/a\\*/将只匹配"a*"。</td></tr><tr><td>^</td><td>匹配一个输入或一行的开头，/^a/匹配"an A"，而不匹配"An a"</td></tr><tr><td>$</td><td>匹配一个输入或一行的结尾，/a$/匹配"An a"，而不匹配"an A"</td></tr><tr><td>*</td><td>匹配前面元字符0次或多次，/ba*/将匹配b,ba,baa,baaa</td></tr><tr><td>+</td><td>匹配前面元字符1次或多次，/ba*/将匹配ba,baa,baaa</td></tr><tr><td>?</td><td>匹配前面元字符0次或1次，/ba*/将匹配b,ba</td></tr><tr><td>(x)</td><td>匹配x保存x在名为$1...$9的变量中</td></tr><tr><td>x|y</td><td>匹配x或y</td></tr><tr><td>{n}</td><td>精确匹配n次</td></tr><tr><td>{n,}</td><td>匹配n次以上</td></tr><tr><td>{n,m}</td><td>匹配n-m次</td></tr><tr><td>[xyz]</td><td>字符集(character set)，匹配这个集合中的任一一个字符(或元字符)</td></tr><tr><td>[^xyz]</td><td>不匹配这个集合中的任何一个字符</td></tr><tr><td>[\\b]</td><td>匹配一个退格符</td></tr><tr><td>\\b</td><td>匹配一个单词的边界</td></tr><tr><td>\\B</td><td>匹配一个单词的非边界</td></tr><tr><td>\\cX</td><td>X是一个控制符，/\\cM/匹配Ctrl-M</td></tr><tr><td>\\d</td><td>匹配一个字数字符，/\\d/ = /[0-9]/</td></tr><tr><td>\\D</td><td>匹配一个非字数字符，/\\D/ = /[^0-9]/</td></tr><tr><td>\
</td><td>匹配一个换行符</td></tr><tr><td>\\r</td><td>匹配一个回车符</td></tr><tr><td>\\s</td><td>匹配一个空白字符，包括\
,\\r,\\f,\\t,\\v等</td></tr><tr><td>\\S</td><td>匹配一个非空白字符，等于/[^\
    \\f\\r\\t\\v]/</td></tr><tr><td>\\t</td><td>匹配一个制表符</td></tr><tr><td>\\v</td><td>匹配一个重直制表符</td></tr><tr><td>\\w</td><td>匹配一个可以组成单词的字符(alphanumeric，这是我的意译，含数字)，包括下划线，如[\\w]匹配"$5.98"中的5，等于[a-zA-Z0-9]</td></tr><tr><td>\\W</td><td>匹配一个不可以组成单词的字符，如[\\W]匹配"$5.98"中的$，等于[^a-zA-Z0-9]。</td></tr></tbody></table><table width="100%" border="1"><tbody><tr><td>
    用re = new RegExp("pattern",["flags"]) 的方式比较好 
    pattern : 正则表达式 
    flags: g （全文查找出现的所有 pattern） 
    i （忽略大小写） 
    m （多行查找）</td><td>vaScript动态正则表达式问题
    请问正则表达式可以动态生成吗? 
    例如JavaScript中: 
    var str = "strTemp"; 
    要生成: 
    var re = /strTemp/; 
    如果是字符连接: 
    var re = "/" + str + "/"即可 
    但是要生成表达式,可以实现吗?怎样实现?</td><td> </td></tr></tbody></table>
