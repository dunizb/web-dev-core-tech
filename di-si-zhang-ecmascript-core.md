# 第四章 ECMAScript Core

ECMAScript是一种由Ecma国际（前身为欧洲计算机制造商协会）通过ECMA-262标准化的脚本程序设计语言。这种语言在万维网上应用广泛，它往往被称为JavaScript或JScript，但实际上后两者是ECMA-262标准的实现和扩展。

## 历史

1995年12月，升阳公司与网景公司公司一起引入了JavaScript。1996年3月，网景公司发表了支持JavaScript的网景导航者 2.0。由于JavaScript作为网页的客户端脚本语言非常成功，微软于1996年8月引入了Internet Explorer 3.0，这个软件支持一个“约”与JavaScript兼容的JScript。

1996年11月，网景公司将JavaScript提交给欧洲计算机制造商协会进行标准化。ECMA-262的第一个版本于1997年6月被Ecma组织采纳。

1998年6月，ECMAScript 2.0版发布。

1999年12月，ECMAScript 3.0版发布，成为JavaScript的通行标准，得到了广泛支持。

2007年10月，ECMAScript 4.0版草案发布，对3.0版做了大幅升级，预计次年8月发布正式版本。草案发布后，由于4.0版的目标过于激进，各方对于是否通过这个标准，发生了严重分歧。以Yahoo、Microsoft、Google为首的大公司，反对JavaScript的大幅升级，主张小幅改动；以JavaScript创造者Brendan Eich为首的Mozilla公司，则坚持当前的草案。

2008年7月，由于对于下一个版本应该包括哪些功能，各方分歧太大，争论过于激进，ECMA开会决定，中止ECMAScript 4.0的开发，将其中涉及现有功能改善的一小部分，发布为ECMAScript 3.1，而将其他激进的设想扩大范围，放入以后的版本，由于会议的气氛，该版本的项目代号起名为Harmony（和谐）。会后不久，ECMAScript 3.1就改名为ECMAScript 5。

2009年12月，ECMAScript 5.0版正式发布。Harmony项目则一分为二，一些较为可行的设想定名为JavaScript.next继续开发，后来演变成ECMAScript 6；一些不是很成熟的设想，则被视为JavaScript.next.next，在更远的将来再考虑推出。

2011年6月，ECMAscript 5.1版发布，并且成为ISO国际标准（ISO/IEC 16262:2011）。

2013年3月，ECMAScript 6草案冻结，不再添加新功能。新的功能设想将被放到ECMAScript 7。

2013年12月，ECMAScript 6草案发布。然后是12个月的讨论期，听取各方反馈。

2015年6月17日，ECMAScript 6发布正式版本，即ECMAScript 2015。

ECMAScript是由ECMA-262标准化的脚本语言的名称。JavaScript和JScript与ECMAScript兼容，但包含超出ECMAScript的功能。

## 版本

至今为止有七个ECMA-262版本发表。

| 版本 | 发表日期 | 与前版本的差异 |
| :--- | :--- | :--- |
| 1 | 1997年6月 | 首版 |
| 2 | 1998年6月 | 格式修正，以使得其形式与ISO/IEC16262国际标准一致 |
| 3 | 1999年12月 | 强大的正则表达式，更好的词法作用域链处理，新的控制指令，异常处理，错误定义更加明确，数据输出的格式化及其它改变 |
| 4 | 放弃 | 由于关于语言的复杂性出现分歧,第4版本被放弃,其中的部分成为了第5版本及Harmony的基础。 |
| 5 | 2009年12月 | 新增“严格模式（strict mode）”，一个子集用作提供更彻底的错误检查,以避免结构出错。澄清了许多第3版本的模糊规范，增加了部分新功能,如getters及setters，支持JSON以及在对象属性上更完整的反射。 |
| 6 | 2015年6月 | 多个新的概念和语言特性。ECMAScript Harmony将会以“ECMAScript 6”发布。 |
| 7 | 2016年6月 | 多个新的概念和语言特性 |

2004年6月Ecma组织发表了ECMA-357标准，它是ECMAScript的一个扩延，也被称为E4X（ECMAScript for XML）。

## ECMAScript 和 JavaScript 的关系

要讲清楚这个问题，需要回顾历史。1996 年 11 月，JavaScript 的创造者 Netscape 公司，决定将 JavaScript 提交给国际标准化组织 ECMA，希望这种语言能够成为国际标准。次年，ECMA 发布 262 号标准文件（ECMA-262）的第一版，规定了浏览器脚本语言的标准，并将这种语言称为 ECMAScript，这个版本就是 1.0 版。

该标准从一开始就是针对 JavaScript 语言制定的，但是之所以不叫 JavaScript，有两个原因。一是商标，Java 是 Sun 公司的商标，根据授权协议，只有 Netscape 公司可以合法地使用 JavaScript 这个名字，且 JavaScript 本身也已经被 Netscape 公司注册为商标。二是想体现这门语言的制定者是 ECMA，不是 Netscape，这样有利于保证这门语言的开放性和中立性。

因此，ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现（另外的 ECMAScript 方言还有 Jscript 和 ActionScript）。**日常场合，这两个词是可以互换的**。

---

本部分内容涉及 ECMAScript 6 的一些内容

---

## 参考资料

* [维基百科](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=3&cad=rja&uact=8&ved=0ahUKEwjj9qGvn4fYAhXCsY8KHRyBAZ8QFggvMAI&url=https%3a%2f%2fzh.wikipedia.org%2fzh-cn%2fECMAScript&usg=AOvVaw2sNKrPO9W4IE-M2nUlXqnE)-ECMAScript
* [百度百科](https://baike.baidu.com/item/ECMAScript)--ECMAScript
* [ECMAScript6入门](http://es6.ruanyifeng.com/#docs/intro)



