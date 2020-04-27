---
layout: post
title:  "搭建最简单的表格网站-03：Vue"
date:   2020-04-27 15:15:53 -0700
categories: build a simple webpage
---

## Vue
这就是我们的页面 http://justoy-test-alumni.s3-website-us-west-2.amazonaws.com/

我们的页面只有一个表格，用于填写email和name，然后一个按钮用于submit信息。

前端部分很简陋，因此选择用Vue来写。Vue相比较Angular轻量许多，非常容易上手。

### 写页面
#### 新建一个index.html文件。
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Contact</title>
</head>
<body>
</body>

<script>
</script>

</html>
```

#### 从CDN引入Vue
CDN地址可以在Vue官网找到
```html
<head>
  <meta charset="UTF-8">
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <title>Contact</title>
</head>
```

#### 创建表格和按钮
```html
<body>
<div id="app">
    <div>
      <input type="text" placeholder="email" v-model="contact.email" />
      <input type="text" placeholder="name" v-model="contact.name" />
      <input type="button" @click="put_contact" value="Submit">
    </div>
  </div>
</div>
</body>
```
上述input中，
id="app"用于绑定我们的vue组件，我们的组件就命名为app。

v-model用于绑定我们的vue组件中的contact变量。

@click用于绑定vue组建中的put_contact函数。

#### 创建vue组件
该组件就叫做app，有一个变量叫做contact用于存储email和name，put_contact函数用于向提交email和name

```html
<script>
var app = new Vue({
  el: '#app',
  data: function() {
    return {
      contact: {}
    };
  },

  methods: {
    put_contact: function() {
    }
  }
});
</script>
```

### 与后端交互
我们用axios作为我们的http client向后端发送请求

如下，引入axios
```html
<head>
  <meta charset="UTF-8">
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
  <title>Contact</title>
</head>
```

在put_contact 函数中，用put方法向后端发送数据，put中的uri就是api gateway中的uri
```javascript
  methods: {
    put_contact: function() {
      axios
        .put("https://rjo8dgds7k.execute-api.us-west-2.amazonaws.com/test", {
          email: this.contact.email,
          name: this.contact.name
        })
        .then(function(response) {
          console.log(response);
        })
        .catch(function(error) {
          console.log(error);
        });
    }
  }
```

### 完整代码
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
  <title>Contact</title>
</head>
<body>
<div id="app">
    <div>
      <input type="text" placeholder="email" v-model="contact.email" />
      <input type="text" placeholder="name" v-model="contact.name" />
      <input type="button" @click="put_contact" value="Submit">
    </div>
  </div>
</div>
</body>

<script>
var app = new Vue({
  el: '#app',
  data: function() {
    return {
      contact: {}
    };
  },

  methods: {
    put_contact: function() {
      axios
        .put("https://rjo8dgds7k.execute-api.us-west-2.amazonaws.com/test", {
          email: this.contact.email,
          name: this.contact.name
        })
        .then(function(response) {
          console.log(response);
        })
        .catch(function(error) {
          console.log(error);
        });
    }
  }
});
</script>

</html>
```

### 启动CORS以解决4xx错误
用浏览器打开index.html,随便测试一下，然后就得到了4xx的错误 (%_%)

这是因为我们的api不支持[CORS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)，浏览器就很大妈地为了我们的安全阻止了这个请求。

进入aws的api gateway，选择我们的api，在actions中选择enable CORS。

在我们的api下，可以看见多了一个option方法，这时候再测试就成功了。