---
layout: post
title: ES6 async&await简单入门
category: 技术
tags: [ES6, async, await]
keywords: ES6, async, await
description: 
---

> 网上太多例子了，但说实话让菜鸟的我看的一脸懵逼。话不多说，请看代码(仅展示关键部分)

```
<template>
  <div>
    <el-upload
      :action="action"
      :data="data"
      :before-upload="beforeAvatarUpload">
    </el-upload>
  </div>
</template>

<script>
export default {
  methods: {
    // async 证明该方法是异步的(加上就完事了)
    async beforeAvatarUpload(file) {
      // getToken返回的是 Promise; await意思是等待Promise执行完毕(也就是resolve); r是resolve的结果
      const r = await this.getToken(file)
      console.log(r)
      return true
    },
    // 该方法不仅仅可以返回Primise, 也可以设置setTimeout延迟
    getToken(file) {
      return new Promise((resolve) => {
        this.$get('/company/qiniu/token/getToken', {}).then(r => {
            resolve(r.data.data)
          })
      })
    }
  }
}
</script>
```

涉及的内容不够深入，但对于我这种菜鸟来说足够用了；

> 总结

 - await必须在async定义的方法内
 - await等待异步方法结束后下面的代码才会继续执行
 - 对于Primise不需要调用`.then()`，直接`return new Promise((resolve) => {resolve(123)})`即可
 - await不局限于Promise； setTimeout和ajax等异步操作同样适用
