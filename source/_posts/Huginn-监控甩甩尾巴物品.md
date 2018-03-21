---
title: Huginn 监控甩甩尾巴物品
tags: [Huginn,数字尾巴,甩甩尾巴,监控]
date: 2018-03-22 00:40:18
categories: Huginn
---

## 需求
当数字尾巴的甩甩尾巴有指定物品发布时,通知我!
## 实现
1. 甩甩尾巴首页:http://trade.dgtle.com/dgtle_module.php?mod=trade&ac=index&typeid=&PName=&searchsort=0&page=1,page 参数是页码,可以同时抓取2-3页,以免时间间隔过大发布的物品太多导致爬不到.
2. 抓取每个物品的标题,url,地址,价格,时间,是否交易完成,提取这几个属性,使用 xpath 或者 css selector,在 w3c 上都有相关教程,推荐两个 chrome 插件,可以检查表达式的结果,[CSS Selector Tester](https://chrome.google.com/webstore/detail/css-selector-tester/bbklnaodgoocmcdejoalmbjihhdkbfon),[XPath Helper](https://chrome.google.com/webstore/detail/xpath-helper/hgimnogjllphhhkhlmebbmlgjoejdpjl)
3. 提取数据,通过 chrome 开发者工具,可以粗略提取下,然后自己修改下表达式,基本就可以了,通过上面的插件测试下,基本就差不多了.![image.png](http://upload-images.jianshu.io/upload_images/1340121-f176a8bbecb5fa95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4. 贴上我写好的配置文件,新建一个 Website Agent 导入就可以了.
```json
{
  "expected_update_period_in_days": "2",
  "url": [
    "http://trade.dgtle.com/dgtle_module.php?mod=trade&ac=index&typeid=&PName=&searchsort=0&page=1",
    "http://trade.dgtle.com/dgtle_module.php?mod=trade&ac=index&typeid=&PName=&searchsort=0&page=2",
    "http://trade.dgtle.com/dgtle_module.php?mod=trade&ac=index&typeid=&PName=&searchsort=0&page=3"
  ],
  "type": "html",
  "mode": "on_change",
  "extract": {
    "title": {
      "css": "div.tradetop > p.tradetitle > a",
      "value": "string(.)"
    },
    "info": {
      "xpath": "//*[@id=\"wp\"]/div[3]/div/p[1]/.",
      "value": "normalize-space(.)"
    },
    "state": {
      "css": "div.tradepic",
      "value": "not(contains(@class,'comp'))"
    },
    "date": {
      "css": ".tradedateline",
      "value": "string(.)"
    },
    "img": {
      "css": ".tradepic > a > img",
      "value": "@src"
    },
    "url": {
      "css": ".tradepic > a",
      "value": "concat('http://trade.dgtle.com',@href)"
    }
  }
}
```
5. 过滤指定物品,需要创建一个`Trigger Agent`,过滤出`小米`相关物品,只要标题包含这个关键字就可以了,代码:
```json
{
  "expected_receive_period_in_days": "2",
  "keep_event": "true",
  "rules": [
    {
      "type": "regex",
      "value": "小米",
      "path": "title"
    }
  ],
  "message": "甩甩尾巴有关 `小米` 的物品更新了"
}
```
6. 将 event 通知到 Slack, 也可以邮件或者其他.