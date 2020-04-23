---
layout: post
title:  "搭建最简单的表格网站-02：API Gateway"
date:   2020-04-22 16:23:11 -0700
categories: build a simple webpage
---

## API Gateway

### 创建API
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

### 测试API
在Stages中，点击我们的PUT API,可以看见如下字样
```
Invoke URL: https://rjo8dgds7k.execute-api.us-west-2.amazonaws.com/test/
```
这就是我们的API的url。
我们可以用postman或者curl来call这个api
例如
```shell
curl -X PUT \
  https://rjo8dgds7k.execute-api.us-west-2.amazonaws.com/test \
  -d '{
  "email": "lsj@test.com",
  "name": "lsj"
}'
```