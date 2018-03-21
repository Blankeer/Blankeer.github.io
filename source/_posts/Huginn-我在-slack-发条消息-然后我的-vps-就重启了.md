---
title: '[Huginn]我在 slack 发条消息,然后我的 vps 就重启了'
tags: [Huginn,VPS,Slack]
date: 2018-03-22 00:43:51
categories: Huginn
---

## 需求
有时候重启 vps,需要登录在网页上操作,很麻烦,查了下有相关的 [api](https://www.alibabacloud.com/help/zh/doc-detail/25502.htm?spm=a3c0i.o25485zh.a3.16.18627bd1b51rF5) 做这个事,最好是我在 slack 里发条消息(重启 xx 主机),然后自动重启.
## 重点
实现的重点是 怎么让 Huginn 收到 slack 的消息,huginn 上的 slack agent 是发送消息到 slack, 而不能反过来,查了下 slack 文档,能实现的是 [bot](https://api.slack.com/bot-users) 和 [app](https://api.slack.com/slack-apps),决定采用 app
## 实现
首先在 huginn 创建 `WebhookAgent `,options 如下:
```json
{
  "secret": "123456",//这里随便填
  "expected_receive_period_in_days": 1,
  "payload_path": ".",
  "code": "200",
  "response": "{{challenge}}"
}
```
response 必须是`{{challenge}}`,然后创建,可以看到 webhook api url ,一般是这种形式 http://1.2.3.4/users/1/web_requests/1/123456, 记下来,下一步会用.
然后在 slack 创建 app, 然后创建 Event Subscriptions,![image.png](http://upload-images.jianshu.io/upload_images/1340121-37da6845c7df611f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后填上上一步的 url, 添加 event, 填写 url 后会检查,如果失败,请检查上一步创建的 agent.
![image.png](http://upload-images.jianshu.io/upload_images/1340121-b39fb2b07a3871b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
install APP, 然后授权下
![image.png](http://upload-images.jianshu.io/upload_images/1340121-eb52e6cfc886dfdf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
以上步骤就 ok 了,然后你在公共 channel 里发条消息,检查下 agent events 有没有相关 event,类似这样:![image.png](http://upload-images.jianshu.io/upload_images/1340121-ebe61920ae3c44b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后写一个 `trigger agent` 过滤出`重启` 的消息,然后传递给一个 `Post agent`去调用 vps 的 API,测试下就 ok 了.

