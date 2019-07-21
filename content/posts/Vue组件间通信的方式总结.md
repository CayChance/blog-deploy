---
title: "Vue组件间通信的方式总结"
date: 2019-07-05T18:48:17+08:00
draft: false
toc: false
images:
tags: 
  - vue
  - vuex
  - 组件通信
---
----

Vue组件之间通讯的常见方法总结

### 1. props/$emit

最常用的一种方法。

- 父组件传值给子组件

子组件中通过声明`props`传递`itemList`，父组件引入子组件以后，传入`itemList`为`fruitList`

- 子组件传值给父组件

子组件中传递值通过`$emit`的第二个参数。父组件接受值通过声明方法`addItem`，并获取其中的参数。

``` JavaScript
// 父组件
<template>
  <div class="test">
    <h3>this is parents page</h3>
    <cChildA v-on:changeItemList='addItem' v-bind:itemList="fruitList" />
  </div>
</template>

<script>
import cChildA from "./a";
export default {
  data() {
    return {
      fruitList: ["peach", "banana", "lemon"]
    };
  },
  methods:{
    addItem(item){
      console.log(item);
    }
  },
  components: {
    cChildA
  }
};

//子组件
<template>
  <div>
    <h3>{{childTitle}}</h3>
    <ul>
      <li v-on:click="changeItemList" v-for="(item,index) in itemList" v-bind:key="index">{{item}}</li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    itemList: {
      type: Array
    }
  },
  data() {
    return {
      childTitle: `this is child page`
    };
  },
  methods: {
    changeItemList() {
      this.$emit("changeItemList", this.childTitle);
    }
  }
};
</script>
```

### 2. Vuex

>
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

在网上看到有人把这个也归类到Vue组件间通信的方法。这么说肯定没错的哈。但是看一下Vuex的定义，它是Vue中用来管理所有组件的状态的。它都可以用于管理所有组件的状态了，父子组件之间肯定可以使用它进行通讯。杀鸡焉用牛刀，所以该方法并不适用父子组件通讯这个场景。

具体使用方法就不详细说了，具体可以参考看一下[Vuex文档](https://vuex.vuejs.org/zh/)。

平时使用Vuex的时候，需要注意的一个问题，是Vuex的数据不是持久化的，也就是说，页面刷新，或者跳转到第三方页面的时候，Vuex中的数据会清空，所以一般都会配套使用[vuex-persistedstate](https://github.com/robinvdvleuten/vuex-persistedstate)。

### 3. $emit/$on

该方法的思路是通过一个空的Vue实例作为事件中心，用来触发事件和监听事件。

假设index页面引入了a，b，c三个组件。a和b组件需要传值给c组件。bus.js就是new了一个空的Vue并暴露出来。

这个方法之前没有接触过，所以就详细的写出来具体的用法。总结一下：

- 使用**一个**空的Vue实例作为中央事件总线(事件中心)
- 把事件暴露出去使用 `Bus.$emit("event-name", this.data);`，就可以把`this.data`暴露出去
- 接收方接受数据使用`Bus.$on("event-name", data => {this.data = data;});`
- 暴露和接受方法使用的`event-name`相同即可

``` js
//index.vue
<template>
  <div class="test">
    <h3>this is parents page</h3>
    <c-child-a></c-child-a>
    <c-child-b></c-child-b>
    <c-child-c></c-child-c>
  </div>
</template>

<script>
import cChildA from "./a";
import cChildB from "./b";
import cChildC from "./c";

export default {
  data() {
    return {
    };
  },
  methods:{
    
  },
  components: {
    cChildA,
    cChildB,
    cChildC
  }
};
</script>

// a.vue
<template>
  <div>
    <h3>A组件{{name}}</h3>
    <button v-on:click="send">传值给C</button>
  </div>
</template>

<script>
import Bus from './bus';

export default {
  data() {
    return {
      name: 'Chance'
    };
  },
  methods: {
    send() {
      Bus.$emit("com-a", this.name);
    }
  }
};
</script>

<style scoped>
button{
  width: 100px;
  height: 20px;
  border-radius: 10px;
  padding: 0;
}
</style>

// b.vue
<template>
  <div>
    <h3>B组件{{age}}</h3>
    <button v-on:click="send">传值给C</button>
  </div>
</template>

<script>
import Bus from './bus';

export default {
  data() {
    return {
      age: 27
    };
  },
  methods: {
    send() {
      Bus.$emit("com-b", this.age);
    }
  }
};
</script>

<style scoped>
button{
  width: 100px;
  height: 20px;
  border-radius: 10px;
  padding: 0;
}
</style>

// c.vue
<template>
  <div class="c">
    <h3>C组件</h3>
    <div>接受来自A/B组件的值{{name}}{{age}}</div>
  </div>
</template>

<script>
import Bus from './bus';

export default {
  data() {
    return {
      name: "",
      age: ""
    };
  },
  mounted() {
    Bus.$on("com-a", name => {
      this.name = name;
    });
    Bus.$on("com-b", age => {
      this.age = age;
    });
  }
};
</script>

//bus.js
import Vue from 'vue'
const Bus = new Vue()
export default Bus

```

### 4. $parent/$children/ref

>
ref 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 $refs 对象上。如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例。

>
$parent 父实例，如果当前实例有的话。

>
$children 当前实例的直接子组件。

### 5. provide/inject

>
2.2.0新增。
这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

`provide/inject`使用起来很方便，它的适用场景就是爷父子孙这种的组件关系，只能从祖先组件向子孙后台往下流。

通过在父组件`povide`钩子中声明变量暴露出数据；在子组件`inject`钩子中接受数据即可。

``` js
// index.vue
<template>
  <div class="test">
    <h3>this is parents page</h3>
    <c-child-c></c-child-c>
    <c-child-b></c-child-b>
  </div>
</template>

<script>
import cChildC from "./c";
import cChildB from "./b";

export default {
  provide: {
    name: 'chance',
    age: '27'
  },
  components: {
    cChildC,
    cChildB
  }
};
</script>

// b.vue
<template>
  <div class="b">
    <h3>B组件</h3>
    <div>接受来自父组件组件的值{{name}}{{age}}</div>
  </div>
</template>

<script>
export default {
  inject: ['name','age']
};
</script>
```

### 6. $attrs/$listeners

来自官方的说明：

>
$attrs
>
包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。


>
$listeners
>
包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用。

- $attrs

看着是有些晦涩的，用自己的话，来解释一下就是。在index组件中，data中声明了foo、boo、coo、doo、name、age共6个变量，其中前4个变量和title，index组件都传给了子组件childCom1；在childCom1组件中用props接受了foo变量，剩余的4个变量都是通过$attrs来接受的。往下的传递同理。

该属性需配合inheritAttrs来决定是否可以获取父组件中定义的数据。(PS:试了一下不太好使，不知道是不是没有用对)

- $listeners

这一块还没有完全弄明白，暂时的理解就是子组件可以调用父组件中的方法。父组件中定义了两个方法，eventClick1和eventClick2。在childCom1组件的mounted钩子的时候，就触发了event1；在childCom2组件中button标签中声明handleClick去触发了event2。

下面的例子中：index.vue为根组件，childCom1是index的子组件，childCom2是childCom1的子组件，childCom3是childCom2的子组件。


``` js
// index.vue
<template>
  <div>
    <h2>index page</h2>
    <child-com1
      :foo="foo"
      :boo="boo"
      :coo="coo"
      :doo="doo"
      title="前端"
      @event1='eventClick1'
      @event2='eventClick2'
    ></child-com1>
  </div>
</template>
<script>
const childCom1 = () => import("./childCom1.vue");
export default {
  components: { childCom1 },
  data() {
    return {
      foo: "Javascript",
      boo: "Html",
      coo: "CSS",
      doo: "Vue",
      name: "chance",
      age: 17
    };
  },
  methods:{
    eventClick1(){
      console.log('this is event click1')
    },
    eventClick2(){
      console.log('this is event click2')
    }
  }
};
</script>

// childCom1.vue
<template class="border">
  <div>
    <p>foo: {{ foo }}</p>
    <p>childCom1的$attrs: {{ $attrs }}</p>
    <child-com2 v-bind="$attrs" v-on="$listeners"></child-com2>
  </div>
</template>
<script>
const childCom2 = () => import("./childCom2.vue");
export default {
  components: {
    childCom2
  },
  inheritAttrs: false, // 可以关闭自动挂载到组件根元素上的没有在props声明的属性
  props: {
    foo: String // foo作为props属性绑定
  },
  mounted(){
    this.$emit('event1')
  },
  created() {
    console.log('c1',this.$attrs); // { "boo": "Html", "coo": "CSS", "doo": "Vue", "title": "前端" }
  }
};
</script>

// childCom2.vue
<template>
  <div class="border">
    <p>boo: {{ boo }}</p>
    <p>childCom2: {{ $attrs }}</p>
    <button @click="handleClick">fire</button>
    <child-com3 v-bind="$attrs" v-on="$listeners"></child-com3>
  </div>
</template>
<script>
const childCom3 = () => import("./childCom3.vue");
export default {
  components: {
    childCom3
  },
  inheritAttrs: false,
  props: {
    boo: String
  },
  mounted() {},
  methods: {
    handleClick() {
      this.$emit("event2");
      console.log('fried');
    }
  },
  created() {
    console.log("c2", this.$attrs); // { "coo": "CSS", "doo": "Vue", "title": "前端" }
  }
};
</script>

// childCom3.vue
<template>
  <div class="border">
    <p>childCom3: {{ $attrs }}</p>
  </div>
</template>
<script>
export default {
  props: {
    coo: String,
    title: String
  },
  created(){
    console.log('c3',this.$attrs) // { "doo": "Vue"}
  }
};
</script>
```

### 总结

| 方法 | 使用场景 |
| :-: | :-: |
| props/$emit | 正常的父子组件传输 |
| Vuex | 多组件之间的状态共享 |
| $emit/$on | 兄弟组件之间传输，不通过父组件统一下发数据来解决 |
| $parent/$children/ref | 兄弟组件不适用 |
| provide/inject | 爷=>父=>子=>孙 数据流向 |
