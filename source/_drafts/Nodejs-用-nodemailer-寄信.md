---
title: Nodejs 用 nodemailer 寄信
date: 2021-10-22 08:06:20
tags:
- nodejs
---

新增 utils/email.js，利用 library nodemailer 來實現寄信功能

總共分為三步驟
1. Creat a transporter
2. Define the email options
3. Actually send the email

## 安裝
``` bash
npm i nodemailer
```

#