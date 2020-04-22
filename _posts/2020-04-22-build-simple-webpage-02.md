---
layout: post
title:  "搭建最简单的表格网站-02：API Gateway"
date:   2020-04-22 16:23:11 -0700
categories: build a simple webpage
---

## API Gateway
为了将我们的lambda service暴露给外部用户，让他们可以以rest api的形式call我们的lambda，我们需要为这个lambda创建一个api。

进入aws的api gateway界面，点击create api按钮，选择rest api，然后build一个新api，就叫它put_person吧。

然后在这个api下面，点击actions按钮，下拉选择put。

Integration type 选择Lambda Function，然后关联我们的test-ddb lambda。

然后再在actions里面选择deploy API，stage就取名叫test吧。

这样我们就有了这个api。

进入这api，选择Test，
然后在 Request Body中 复制如下测试数据Test


```json
{
  "email": "lsj2@test.com",
  "name": "lsj2"
}
```

再返回我们的ddb就可以看见新的item被成功写入了