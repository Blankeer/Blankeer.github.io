---
title: Huginn 监控天气情况
tags: [Huginn,天气,监控]
date: 2018-03-22 00:42:14
categories: Huginn
---

## 需求
监控天气是入门 agent 了,官方例子也有,我使用国内天气源,实现每两个小时通知我天气情况(温度,天气, pm2.5等)
## API
天气情况当然得找 API 了,使用了这个 [repo](https://github.com/jokermonn/-Api/blob/master/CenterWeather.md) 里的
## 实现
```json
{
  "expected_update_period_in_days": "2",
  "url": "http://tj.nineton.cn/Heart/index/all?city=替换成自己城市 code",
  "type": "json",
  "mode": "on_change",
  "extract": {
    "city_name": {
      "path": "weather[0].city_name"
    },
    "now_info": {
      "path": "weather[0].now.text"
    },
    "temperature": {
      "path": "weather[0].now.temperature"
    },
    "pm25": {
      "path": "weather[0].now.air_quality.city.pm25"
    },
    "air_quality": {
      "path": "weather[0].now.air_quality.city.quality"
    }
  }
}
```
## 通知
通知到 slack / email 就可以了.