---
layout: post
title:  "搭建最简单的表格网站-01：lambda and DynamoDb"
date:   2020-04-20 19:37:01 -0700
categories: build a simple webpage
---

## 起因

本科学院下发了一个腾讯云表格，希望大家能在校庆前填上个人信息。

各届所有校友成千上万人的个人信息都用这一张表格，而且这个云表格没有任何权限管理，隐私真是暴露得一干二净。

实际上，腾讯云表格的scability并不好，不到一千行数据就卡顿严重，筛选功能也会出错。多人同时更改时，体验更是无与伦比的糟糕。

利用云服务搭建一个填表网站，数据库权限仅仅暴露给管理员，其实是一个非常简单的做法，同时很好地在理论保护了隐私。现在云服务支撑千万用户都毫无压力。存储全部校友的话费估计也在10美金以内。

## 架构
网页前端用vue，数据库用DynamoDb，service用lambda，网页文件存储在S3

这个架构的好处是serveless，不需要考虑ops，专注代码，而且实现起来也异常简单

下面就是我一下午做出来的网站
http://justoy-test-alumni.s3-website-us-west-2.amazonaws.com/

## DynamoDb
ddb是一个按需付费，自动scale的key-value数据库，点点鼠标即可创建

ddb部分很简单，进入aws console，创建一个新table即可。

创建时选择ondemend table，按需付钱，对个人用户几乎免费。

primary key是uid，即一个uuid。好处是分布均匀，具备唯一行。
sort key选择用户email

## Lambda
lambda也是按需付费，自动scale，点点鼠标即可创建

我们先创建一个lambda，就叫作test-ddb吧。用python3作为运行环境。

创建lambda时需要创建一个iam role给lambda，就叫做test-ddb-role吧。

这个role需要两个权限，AmazonDynamoDBFullAccess 和 AWSLambdaBasicExecutionRole。前者是读写ddbb的权限，后者是lambda运行的权限。

这个lambda只需要
1. 读取前端传来的用户信息
2. 生成uuid作为用户的uid
3. 將信息写入ddb

代码如下

```python
import json
import boto3
import uuid

dynamodb = boto3.resource('dynamodb', region_name='us-west-2')
table = dynamodb.Table('test-db')

def lambda_handler(event, context):
    id = str(uuid.uuid1())
    response = table.put_item(
        Item={
            'uid': id,
            'email': event['email'],
            'name': event['name']
        }
    )
    return "Succeeded! Your Id is {}".format(id);
```
代码极其简单，但因为aws文档的糟糕，我还是浪费了挺久时间才写出来。

我们可以用如下的json文件来测试这个lambda
```json
{
  "email": "lsj@test.com",
  "name": "lsj"
}
```

点击test之后，我们就能在ddb中看见如下数据：
```json
{
  "email": "lsj@test.com",
  "name": "lsj",
  "uid": "96f1b872-8288-11ea-8760-6208b13c88fa"
}
```