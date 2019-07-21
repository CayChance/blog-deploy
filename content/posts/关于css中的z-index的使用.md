---
title: "关于css中的z-index的使用"
date: 2019-06-12T10:05:20+08:00
draft: false
toc: false
images:
tags: 
  - css
  - z-index
---
----

``` html
<header>
  <div class="nav">
    <ul>
      <li>
        产品服务
        <div class="content"></div>
      </li>
    </ul>
  </div>
</header>
<div class="swiper"></div>
```

![](https://blog-pics.pek3b.qingstor.com/c2ed4dc809ca.png)

如上代码，这几天在做一个需求的时候，突然发现z-index不生效了。

以上结构，header是网站的头部，div.swiper是header下面的轮播图。div.nav是头部的导航条，li是其中一个导航，li被鼠标hover的时候，显示div.content。最后的效果如图参考。

轮播图直接使用的swiper，还没有做swiper部分之前，hover那一块是没有问题的，swiper做了以后，发现hover就有问题了。后来排查了一下，是因为swiper的z-index设置为1。我心想这下好办了，把li的z-index设置成2不就好了。然而，并不好使。

- **z-index只对定位元素有效果** <br>
  position的属性值包括：absolute-绝对定位、relative-相对定位、fixed-固定定位、inherit-继承父元素定位，static-静态定位。前三者肯定有效；inherit取决于父元素；静态定位-static，否则z-index会无效。

- **同一个父元素下的元素的层叠效果会受父元素的z-index影响** <br>
  如果父元素的z-index值很小,那么子元素的z-index值很大也不起作用

- **当前标签不能含有float属性** <br>
  这个是在网上找到的，我自己验证了一下结论正好相反。






