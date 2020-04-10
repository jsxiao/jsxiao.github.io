---
layout: post
title: 七牛云表单上传
category: 技术
tags: [七牛云]
keywords: 七牛云, 表单上传
description: 
---

> 环境

- jdk1.8
- 七牛云sdk 7.2.18
- vue 2.6.10
- element-ui 2.12.0


> 表单上传必要参数

- `action`: 上传地址
- `upload_token`: 上传凭证
- `file`: 文件本身

最终呈现的form表单

```
<form method="post" action="<action>" enctype="multipart/form-data">
  <input name="token" type="hidden" value="<upload_token>">
  <input name="file" type="<file>" />
  <input type="submit" value="上传文件" />
</form>
```

---

> 使用`java sdk`生成上传凭证

```
        String url = "/material/fileObjects/e12d0djdkwkq32.jpg"
        String fileKey = Paths.get("ARCard/res/", url).toString();

        StringMap formPolicy = new StringMap();
        // 限制仅能上传图片
        formPolicy.put("mimeLimit", "image/*");
        // 服务端收到该请求后需要返回 content-type: application/json 格式的内容; 多个url以 [;]分隔;
        formPolicy.put("callbackUrl", "");
        // 回调请求参数; 默认为post请求, content-type为application/x-www-form-urlencoded
        formPolicy.put("callbackBody", JSONUtils.toJson(callbackBody));
        // 回调请求类型改为: application/json
        formPolicy.put("callbackBodyType", "application/json");

        String appKey = "your app key";
        String appSecret = "your app secret";
        Auth auth = Auth.create(appKey, appSecret);
        // 300 意思是token有效时间为300秒, 从当前时间开始计算
        // 因为我的每张图片的key是不一样的, 所以每次都需要生成token; 如果上传策略非 <bucket>:<key> 模式的话，可以将300改为3600
        long validSecond = 300;
        // 最终生成的 token
        String uploadToken = auth.uploadToken(QiNiuBucket.KC_Image.getValue(), fileKey, validSecond, formPolicy, true);
```

> 前端`js`代码; 仅展示关键部分

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
  data() {
    return {
      action: '',
      data: {}
    }
  },
  methods: {
    async beforeAvatarUpload(file) {
      const r = await this.getToken(file)
      // 设置 <action>
      this.action = 'https://upload.qiniup.com'
      // 设置参数
      this.data = {
        token: r.token, // 后端返回的token
        key: r.fileKey // key为 <resource_key> 自己业务中定义
      }
      return true
    },
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

至此form表单上传完成。 七牛云回调(`callbackBody`和`callbakUrl`)用于服务端存储上传文件的信息。

#### 参考

[七牛云表单上传文档](https://developer.qiniu.com/kodo/manual/1272/form-upload)

[存储区域](https://developer.qiniu.com/kodo/manual/1671/region-endpoint)

[上传凭证](https://developer.qiniu.com/kodo/manual/1208/upload-token)

[上传策略](https://developer.qiniu.com/kodo/manual/1206/put-policy)

[变量](https://developer.qiniu.com/kodo/manual/1235/vars#magicvar)
