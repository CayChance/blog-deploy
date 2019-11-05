---
title: "IView使用中遇到的一些坑"
date: 2019-10-31T11:53:35+08:00
draft: false
toc: false
images:
tags: 
  - css 
  - UI组件
---
----

最近开发中使用iView遇到了一个问题，困扰了很久。使用了iView的Modal组件和Form组件。

### 问题总结

iView的Modal组件中暴露两个事件——点击确定和取消的回调。`@on-ok`和`@on-cancel`。如果Modal中嵌入表单并且表单需要校验的话，不可以使用Modal提供的`on-ok`方法。需要自己加一个Button单独添加事件来处理。

``` html
<Modal 
  title="修改名称" 
  v-model="setAliasModal" 
  :mask-closable="false"
  @on-cancel="resetSetAlias">
  <Form ref="setalias" :model="setAliasData" :label-width="80" :rules="aliasrules">
    <FormItem label="方法名称" prop="aliasName">
      <Input v-model="setAliasData.aliasName" :maxlength="25" placeholder="请输入新的方法名(最长不超过25)"></Input>
    </FormItem>
  </Form>

  <template slot="footer">
    <Button type="text" @click="resetSetAlias">取消</Button>
    <Button type="primary" @click="setAliasHandleSubmit">保存</Button>
  </template>
</Modal>
```

### 需求描述
需求是Modal组件中嵌套Form组件，需要进行表单规则校验，如果校验通过，则关闭Modal进行后续逻辑；如果校验不通过，那么Modal不能关闭，提示用户规则校验不通过。

Form表单提供了 `this.$refs["xxx"].validate` 方法来校验规则。

<span style="color: red">如果使用Modal提供的`@on-ok`事件来处理的话，不管校验是否通过，都会默认关闭Modal的。</span>

所以处理的方法，是不要使用Modal提供的@on-ok方法，自己写两个Button来分别处理取消和确定的事件。



