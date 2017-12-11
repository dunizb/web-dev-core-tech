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

## 二、过渡\(Transition\)、转换\(Transform\)、动画\(Animation\)

这三个特性是CSS3新增的和动画相关的特性。

### Transition

Transition可以在当元素从一种样式变换为另一种样式时为元素添加效果，而不用使用Flash动画或JavaScript。

Transition有如下属性：

* transition-property: 规定应用过渡的CSS属性的名称。
* transition-duration: 规定完成过渡效果需要多长时间。
* transition-delay: 规定过渡效果何时开始，默认是0。
* transition-timing-function: 规定过渡效果的时间曲线，默认是”ease”，还有linear、ease-in、ease-out、ease-in-out和cubic-bezier等过渡类型。
* transition: 简写属性，用于在一个属性中设置四个过渡属性。

### Transform

Transform用来向元素应用各种2D和3D转换，该属性允许我们对元素进行旋转、缩放、移动或倾斜等操作。使用方式如下：

```css
div{
    transform:rotate(7deg);
    -ms-transform:rotate(7deg);     /* IE 9 */
    -moz-transform:rotate(7deg);    /* Firefox */
    -webkit-transform:rotate(7deg); /* Safari 和 Chrome */
    -o-transform:rotate(7deg);  /* Opera */
}
```

transform可以有各种变换类型，即属性值：

1. none: 定义不进行转换。
2. matrix\(n,n,n,n,n,n\): 定义2D转换，使用六个值的矩阵。
3. matrix3d\(n,n,n,n,n,n,n,n,n,n,n,n,n,n,n,n\): 定义3D转换，使用16个值的4x4矩阵。
4. translate\(x,y\): 定义2D位移转换。
5. translate3d\(x,y,z\): 定义3D位移转换。
6. translateX\(x\): 定义位移转换，只是用X轴的值。
7. translateY\(y\): 定义位移转换，只是用Y轴的值。
8. translateZ\(z\): 定义3D位移转换，只是用Z轴的值。
9. scale\(x,y\): 定义2D缩放转换。
10. scale3d\(x,y,z\): 定义3D缩放转换。
11. scaleX\(x\): 通过设置X轴的值来定义缩放转换。
12. scaleY\(y\): 通过设置Y轴的值来定义缩放转换。
13. scaleZ\(z\): 通过设置Z轴的值来定义3D缩放转换。
14. rotate\(angle\): 定义2D旋转，在参数中规定角度。
15. rotate3d\(x,y,z,angle\): 定义3D旋转。
16. rotateX\(angle\): 定义沿着X轴的3D旋转。
17. rotateY\(angle\): 定义沿着Y轴的3D旋转。
18. rotateZ\(angle\): 定义沿着Z轴的3D旋转。
19. skew\(x-angle,y-angle\): 定义沿着X和Y轴的2D倾斜转换。
20. skewX\(angle\): 定义沿着X轴的2D倾斜转换。
21. skewY\(angle\): 定义沿着Y轴的2D倾斜转换。
22. perspective\(n\): 为3D转换元素定义透视视图。

### Animation

Animation让CSS拥有了可以制作动画的功能。使用CSS3的Animation制作动画我们可以省去复杂的js代码。使用方法大概如下：

```css
@-webkit-keyframes anim1 { 
   0% { 
        opacity: 0; 
        font-size: 12px; 
   } 
   100% { 
        opacity: 1; 
        font-size: 24px; 
   } 
} 
.anim1Div { 
   -webkit-animation-name: anim1 ; 
   -webkit-animation-duration: 1.5s; 
   -webkit-animation-iteration-count: 4; 
   -webkit-animation-direction: alternate; 
   -webkit-animation-timing-function: ease-in-out; 
}
```

具体用法可以参考教程：[CSS3 Animation](http://www.w3cplus.com/content/css3-animation)。

## 二、边框

CSS3新增了三个边框属性，分别是：

* border-radius，创建圆角边框
* box-shadow，为元素添加阴影
* border-image，使用图片来绘制边框

IE9+支持border-radius和box-shadow属性。Firefox、Chrome以及Safari支持所有新的边框属性。







