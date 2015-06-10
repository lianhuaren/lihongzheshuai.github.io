---
layout: post
title: 字符编码介绍 通俗易懂 强烈推荐
date: 2012-07-27 21:32
author: onecoder
comments: true
categories: [ascii, unicode, UTF-8, 博文转载, 编码]
---
今天中午，我突然想搞清楚Unicode和UTF-8之间的关系，于是就开始在网上查资料。<br />
<br />
结果，这个问题比我想象的复杂，从午饭后一直看到晚上9点，才算初步搞清楚。<br />
下面就是我的笔记，主要用来整理自己的思路。但是，我尽量试图写得通俗易懂，希望能对其他朋友有用。毕竟，字符编码是计算机技术的基石，想要熟练使用计算机，就必须懂得一点字符编码的知识。<br />
<br />
<strong>1. ASCII码</strong><br />
<br />
我们知道，在计算机内部，所有的信息最终都表示为一个二进制的字符串。每一个二进制位（bit）有0和1两种状态，因此八个二进制位就可以组合出256种状态，这被称为一个字节（byte）。也就是说，一个字节一共可以用来表示256种不同的状态，每一个状态对应一个符号，就是256个符号，从0000000到11111111。<br />
上个世纪60年代，美国制定了一套字符编码，对英语字符与二进制位之间的关系，做了统一规定。这被称为ASCII码，一直沿用至今。<br />
ASCII码一共规定了128个字符的编码，比如空格&ldquo;SPACE&rdquo;是32（二进制00100000），大写的字母A是65（二进制01000001）。这128个符号（包括32个不能打印出来的控制符号），只占用了一个字节的后面7位，最前面的1位统一规定为0。<br />
<br />
<strong>2、非ASCII编码</strong><br />
<br />
英语用128个符号编码就够了，但是用来表示其他语言，128个符号是不够的。比如，在法语中，字母上方有注音符号，它就无法用ASCII码表示。于是，一些欧洲国家就决定，利用字节中闲置的最高位编入新的符号。比如，法语中的&eacute;的编码为130（二进制10000010）。这样一来，这些欧洲国家使用的编码体系，可以表示最多256个符号。<br />
但是，这里又出现了新的问题。不同的国家有不同的字母，因此，哪怕它们都使用256个符号的编码方式，代表的字母却不一样。比如，130在法语编码中代表了&eacute;，在希伯来语编码中却代表了字母Gimel (ג)，在俄语编码中又会代表另一个符号。但是不管怎样，所有这些编码方式中，0&mdash;127表示的符号是一样的，不一样的只是128&mdash;255的这一段。<br />
至于亚洲国家的文字，使用的符号就更多了，汉字就多达10万左右。一个字节只能表示256种符号，肯定是不够的，就必须使用多个字节表达一个符号。比如，简体中文常见的编码方式是GB2312，使用两个字节表示一个汉字，所以理论上最多可以表示256x256=65536个符号。<br />
中文编码的问题需要专文讨论，这篇笔记不涉及。这里只指出，虽然都是用多个字节表示一个符号，但是GB类的汉字编码与后文的Unicode和UTF-8是毫无关系的。<br />
<br />
<strong>3.Unicode</strong><br />
<br />
正如上一节所说，世界上存在着多种编码方式，同一个二进制数字可以被解释成不同的符号。因此，要想打开一个文本文件，就必须知道它的编码方式，否则用错误的编码方式解读，就会出现乱码。为什么电子邮件常常出现乱码？就是因为发信人和收信人使用的编码方式不一样。<br />
可以想象，如果有一种编码，将世界上所有的符号都纳入其中。每一个符号都给予一个独一无二的编码，那么乱码问题就会消失。这就是Unicode，就像它的名字都表示的，这是一种所有符号的编码。<br />
Unicode当然是一个很大的集合，现在的规模可以容纳100多万个符号。每个符号的编码都不一样，比如，U+0639表示阿拉伯字母Ain，U+0041表示英语的大写字母A，U+4E25表示汉字&ldquo;严&rdquo;。具体的符号对应表，可以查询unicode.org，或者专门的汉字对应表。<br />
<br />
<strong>4. Unicode的问题</strong><br />
<br />
需要注意的是，Unicode只是一个符号集，它只规定了符号的二进制代码，却没有规定这个二进制代码应该如何存储。<br />
比如，汉字&ldquo;严&rdquo;的unicode是十六进制数4E25，转换成二进制数足足有15位（100111000100101），也就是说这个符号的表示至少需要2个字节。表示其他更大的符号，可能需要3个字节或者4个字节，甚至更多。<br />
这里就有两个严重的问题，第一个问题是，如何才能区别unicode和ascii？计算机怎么知道三个字节表示一个符号，而不是分别表示三个符号呢？第二个问题是，我们已经知道，英文字母只用一个字节表示就够了，如果unicode统一规定，每个符号用三个或四个字节表示，那么每个英文字母前都必然有二到三个字节是0，这对于存储来说是极大的浪费，文本文件的大小会因此大出二三倍，这是无法接受的。<br />
它们造成的结果是：1）出现了unicode的多种存储方式，也就是说有许多种不同的二进制格式，可以用来表示unicode。2）unicode在很长一段时间内无法推广，直到互联网的出现。<br />
<br />
<strong>5.UTF-8</strong><br />
<br />
互联网的普及，强烈要求出现一种统一的编码方式。UTF-8就是在互联网上使用最广的一种unicode的实现方式。其他实现方式还包括UTF-16和UTF-32，不过在互联网上基本不用。重复一遍，这里的关系是，UTF-8是Unicode的实现方式之一。<br />
UTF-8最大的一个特点，就是它是一种变长的编码方式。它可以使用1~4个字节表示一个符号，根据不同的符号而变化字节长度。<br />
UTF-8的编码规则很简单，只有二条：<br />
1）对于单字节的符号，字节的第一位设为0，后面7位为这个符号的unicode码。因此对于英语字母，UTF-8编码和ASCII码是相同的。<br />
2）对于n字节的符号（n&gt;1），第一个字节的前n位都设为1，第n+1位设为0，后面字节的前两位一律设为10。剩下的没有提及的二进制位，全部为这个符号的unicode码。<br />
下表总结了编码规则，字母x表示可用编码的位。<br />
<br />
Unicode符号范围 | UTF-8编码方式<br />
(十六进制) | （二进制）<br />
--------------------+---------------------------------------------<br />
0000 0000-0000 007F | 0xxxxxxx<br />
0000 0080-0000 07FF | 110xxxxx 10xxxxxx<br />
0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx<br />
0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx<br />
<br />
下面，还是以汉字&ldquo;严&rdquo;为例，演示如何实现UTF-8编码。<br />
已知&ldquo;严&rdquo;的unicode是4E25（100111000100101），根据上表，可以发现4E25处在第三行的范围内（0000 0800-0000 FFFF），因此&ldquo;严&rdquo;的UTF-8编码需要三个字节，即格式是&ldquo;1110xxxx 10xxxxxx 10xxxxxx&rdquo;。然后，从&ldquo;严&rdquo;的最后一个二进制位开始，依次从后向前填入格式中的x，多出的位补0。这样就得到了，&ldquo;严&rdquo;的UTF-8编码是&ldquo;11100100 10111000 10100101&rdquo;，转换成十六进制就是E4B8A5。<br />
<br />
<strong>6. Unicode与UTF-8之间的转换</strong><br />
<br />
通过上一节的例子，可以看到&ldquo;严&rdquo;的Unicode码是4E25，UTF-8编码是E4B8A5，两者是不一样的。它们之间的转换可以通过程序实现。<br />
在Windows平台下，有一个最简单的转化方法，就是使用内置的记事本小程序Notepad.exe。打开文件后，点击&ldquo;文件&rdquo;菜单中的&ldquo;另存为&rdquo;命令，会跳出一个对话框，在最底部有一个&ldquo;编码&rdquo;的下拉条。<br />
<br />
里面有四个选项：ANSI，Unicode，Unicode big endian 和 UTF-8。<br />
1）ANSI是默认的编码方式。对于英文文件是ASCII编码，对于简体中文文件是GB2312编码（只针对Windows简体中文版，如果是繁体中文版会采用Big5码）。<br />
2）Unicode编码指的是UCS-2编码方式，即直接用两个字节存入字符的Unicode码。这个选项用的little endian格式。<br />
3）Unicode big endian编码与上一个选项相对应。我在下一节会解释little endian和big endian的涵义。<br />
4）UTF-8编码，也就是上一节谈到的编码方法。<br />
选择完&rdquo;编码方式&ldquo;后，点击&rdquo;保存&ldquo;按钮，文件的编码方式就立刻转换好了。<br />
<br />
<strong>7. Little endian和Big endian</strong><br />
<br />
上一节已经提到，Unicode码可以采用UCS-2格式直接存储。以汉字&rdquo;严&ldquo;为例，Unicode码是4E25，需要用两个字节存储，一个字节是4E，另一个字节是25。存储的时候，4E在前，25在后，就是Big endian方式；25在前，4E在后，就是Little endian方式。<br />
这两个古怪的名称来自英国作家斯威夫特的《格列佛游记》。在该书中，小人国里爆发了内战，战争起因是人们争论，吃鸡蛋时究竟是从大头(Big-Endian)敲开还是从小头(Little-Endian)敲开。为了这件事情，前后爆发了六次战争，一个皇帝送了命，另一个皇帝丢了王位。<br />
因此，第一个字节在前，就是&rdquo;大头方式&ldquo;（Big endian），第二个字节在前就是&rdquo;小头方式&ldquo;（Little endian）。<br />
那么很自然的，就会出现一个问题：计算机怎么知道某一个文件到底采用哪一种方式编码？<br />
Unicode规范中定义，每一个文件的最前面分别加入一个表示编码顺序的字符，这个字符的名字叫做&rdquo;零宽度非换行空格&ldquo;（ZERO WIDTH NO-BREAK SPACE），用FEFF表示。这正好是两个字节，而且FF比FE大1。<br />
如果一个文本文件的头两个字节是FE FF，就表示该文件采用大头方式；如果头两个字节是FF FE，就表示该文件采用小头方式。<br />
<br />
<strong>8. 实例</strong><br />
下面，举一个实例。<br />
打开&rdquo;记事本&ldquo;程序Notepad.exe，新建一个文本文件，内容就是一个&rdquo;严&ldquo;字，依次采用ANSI，Unicode，Unicode big endian 和 UTF-8编码方式保存。<br />
然后，用文本编辑软件UltraEdit中的&rdquo;十六进制功能&ldquo;，观察该文件的内部编码方式。<br />
1）ANSI：文件的编码就是两个字节&ldquo;D1 CF&rdquo;，这正是&ldquo;严&rdquo;的GB2312编码，这也暗示GB2312是采用大头方式存储的。<br />
2）Unicode：编码是四个字节&ldquo;FF FE 25 4E&rdquo;，其中&ldquo;FF FE&rdquo;表明是小头方式存储，真正的编码是4E25。<br />
3）Unicode big endian：编码是四个字节&ldquo;FE FF 4E 25&rdquo;，其中&ldquo;FE FF&rdquo;表明是大头方式存储。<br />
4）UTF-8：编码是六个字节&ldquo;EF BB BF E4 B8 A5&rdquo;，前三个字节&ldquo;EF BB BF&rdquo;表示这是UTF-8编码，后三个&ldquo;E4B8A5&rdquo;就是&ldquo;严&rdquo;的具体编码，它的存储顺序与编码顺序是一致的。<br />
<br />
<strong>9. 延伸阅读</strong><br />
* The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets（关于字符集的最基本知识）<br />
* 谈谈Unicode编码<br />
* RFC3629：UTF-8, a transformation format of ISO 10646（如果实现UTF-8的规定）<br />
（完）<br />
转自：<a href="http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html">http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html</a>
<p>
	OneCoder注：延伸阅读中的《The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets》正在翻译中。稍后放出&hellip;&hellip;</p>

