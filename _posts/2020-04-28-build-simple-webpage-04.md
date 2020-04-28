---
layout: post
title:  "搭建最简单的表格网站-04：S3"
date:   2020-04-28 14:28:40 -0700
categories: build a simple webpage
---

## 部署网站到s3

S3是aws的一个静态文件存储服务，可以很方便的host我们的网站。

1. 创建一个s3bucket，将 block public access 设置为off， 因为我们希望网站能在互联网上被使用
2. 在permisson -> bucket policy中复制如下json，justoy-test-alumni替换为当前bucket的名字

    ```json
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "PublicReadGetObject",
                    "Effect": "Allow",
                    "Principal": "*",
                    "Action": "s3:GetObject",
                    "Resource": "arn:aws:s3:::justoy-test-alumni/*"
                }
            ]
        }
    ```
3. 上传我们的index.html文件
4. 在bucket的properties -> Static website hosting中，设置Index document 为我们的index.html。
    然后，就可以在Static website hosting 中到到 Endpoint了，形如 http://justoy-test-alumni.s3-website-us-west-2.amazonaws.com

