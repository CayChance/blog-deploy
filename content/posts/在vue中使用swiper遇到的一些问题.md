---
title: "在vue中使用swiper遇到的一些问题"
date: 2019-06-16T16:40:47+08:00
draft: false
toc: false
images:
tags: 
  - swiper
  - 组件
  - vue
---
----
最近做需求，需要做一个轮播图，由于项(ben)目(ren)时(bi)间(jiao)紧(lan)，所以没有自己去写，而是去选择找了一个现成的，用的是swiper。

swiper官网上的不是针对vue的，但是API是一样的。vue中的用的是——[vue-awesome-swiper](https://github.com/surmon-china/vue-awesome-swiper)。

有两件事情需要解决：

1. 分页器pagination的样式需要单独设定；
2. 给分页器添加事件，做到点击分页器跳转至其对应的轮播图。

思路1：刚开始我的思路是可以使用swiper提供的api，来配置分页器,配置如图(PS:不知道还有没有别的办法)。然后再在页面mounted钩子的时候，操作dom给每个分页器添加方法。

但是，这个思路的问题：
开启自动轮播以后，轮播图的每次轮播，都会执行renderCustom方法来重新生成dom，总不能一直给dom添加方法吧。

``` js
... 
data() {
  return {
    swiperOption: {
      //分页器
      pagination: {
        el: ".swiper-pagination",
        type: "custom",
        paginationClickable: true,
        renderCustom: function(swiper, current, total) {
          const activeOpacity = 0.7;
          const normalOpacity = 0.3;
          let opacity = "";
          let paginationStyle = "";
          let html = "";
          for (let i = 1; i <= total; i++) {
            if (i === current) {
              opacity = activeOpacity;
            } else {
              opacity = normalOpacity;
            }
            paginationStyle = `background:#fff;border-radius:0;opacity:${opacity};width:45px;height:5px;margin-right:10px;border:3px solid;`;
            html += `<span class="swiper-pagination-bullet" style=${paginationStyle}></span>`;
          }
          return html;
        }
      },
      //前端后退按钮
      // navigation: {
      //   nextEl: ".swiper-button-next",
      //   prevEl: ".swiper-button-prev"
      // },
      autoplay: true,
      loop: true,
      speed: 800
      // some swiper options/callbacks
      // 所有的参数同 swiper 官方 api 参数
      // ...
    }
  };
}
```

思路2：我先给分页器添加点击事件，然后再想办法处理样式问题。swiper官网上的示例，是直接操作dom给分页器添加事件的，不知道有没有更简单的方法。

这个思路的问题是：class等于swiper-pagination的样式在我当前页面是没法修改的，之前也遇到过类似情况，大多都是因为css设置了scoped属性倒置。

后来，就突发奇想，可以在node_modules包中把swiper的css修改。于是就找到`swiper/dist/css/swiper.css`文件，修改其中的对应处的css，然后把`import "swiper/dist/css/swiper.css";`注释掉，引入我本地替换后的文件`import "@assets/css/swiper.css";`。
``` js
<template>
  <div>
    <swiper :options="swiperOption" ref="mySwiper" @someSwiperEvent="callback">
      <swiper-slide>
      </swiper-slide>
      <div ref="swiperPagination" class="swiper-pagination" slot="pagination"></div>
    </swiper>
  </div>
</template>
<script>
/**
 * 按照文档的要求是需要引入第一行的
 * 由于样式需要可配置化
 * 所以在node_modules中找到该css并修改对应的部分
 */
// import "swiper/dist/css/swiper.css";
import "@assets/css/swiper.css";
import { swiper, swiperSlide } from "vue-awesome-swiper";

export default {
  data(){
    return {}
  },
  computed: {
    swiper() {
      return this.$refs.mySwiper.swiper;
    }
  },
  methods: {
    init() {
      let swiperChildrenList = this.$refs.swiperPagination.children;
      swiperChildrenList[0].style;
      for (let i = 0, len = swiperChildrenList.length; i < len; i++) {
        swiperChildrenList[i].addEventListener("click", () => {
          this.swiper.slideTo(i + 1, 1000, false);
        });
      }
    }
  },
  mounted() {
    this.init();
  },
  components: {
    swiper,
    swiperSlide
  }
}
</script>
```