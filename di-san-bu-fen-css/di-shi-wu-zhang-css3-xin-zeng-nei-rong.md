# 第十五章 CSS3新增内容

除了html5的新特性，CSS3的新特性也是面试中经常被问到的。

## 一、选择器

CSS3中新添加了很多选择器，解决了很多之前需要用javascript才能解决的布局问题。

1. element1~element2: 选择前面有element1元素的每个element2元素。
2. \[attribute^=value\]: 选择某元素attribute属性是以value开头的。
3. \[attribute$=value\]: 选择某元素attribute属性是以value结尾的。
4. \[attribute\*=value\]: 选择某元素attribute属性包含value字符串的。
5. E:first-of-type: 选择属于其父元素的首个E元素的每个E元素。
6. E:last-of-type: 选择属于其父元素的最后E元素的每个E元素。
7. E:only-of-type: 选择属于其父元素唯一的E元素的每个E元素。
8. E:only-child: 选择属于其父元素的唯一子元素的每个E元素。
9. E:nth-child\(n\): 选择属于其父元素的第n个子元素的每个E元素。
10. E:nth-last-child\(n\): 选择属于其父元素的倒数第n个子元素的每个E元素。
11. E:nth-of-type\(n\): 选择属于其父元素第n个E元素的每个E元素。
12. E:nth-last-of-type\(n\): 选择属于其父元素倒数第n个E元素的每个E元素。
13. E:last-child: 选择属于其父元素最后一个子元素每个E元素。
14. :root: 选择文档的根元素。
15. E:empty: 选择没有子元素的每个E元素（包括文本节点\)。
16. E:target: 选择当前活动的E元素。
17. E:enabled: 选择每个启用的E元素。
18. E:disabled: 选择每个禁用的E元素。
19. E:checked: 选择每个被选中的E元素。
20. E:not\(selector\): 选择非selector元素的每个元素。
21. E::selection: 选择被用户选取的元素部分。



