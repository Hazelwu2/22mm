---
title: nodejs-排程工具node-schedule
date: 2022-02-11 07:59:53
tags:
category:
---

## 安裝
if you only want to do something like "run this function every 5 minutes", toad-scheduler would be a better choice. But if you want to, say, "run this function at the :20 and :50 of every hour on the third Tuesday of every month
```
npm i node-schedule
```

每五分鐘執行一次
``` js
const schedule = require('node-schedule');
const { scheduleJob } = require('node-schedule');
const CRON_TIME=*/5 * * * *

const job = schedule.scheduleJob(CRON_TIME, function(){
  console.log('每五分鐘你都會看到我');
});
```