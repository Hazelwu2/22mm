---
title: '[Nodejs] bycrptjs 加密密碼'
date: 2021-10-18 10:06:38
tags:
- nodejs
---
打註冊（signup）API，使用者輸入的密碼，利用 Library bycrptjs 來加密密碼
成功畫面如下
![signup success crypt password](../../../images/20211018/20211018-brycptjs-pd.png)

## 安裝
會利用到 Mongoose、bycrptjs、validator，安裝後需重啟 Server
```
npm i brycrptjs
npm run start
```

## 建立 UserModel
建立 models/userModel.js，並定義 Schema，需要定義的欄位有 name, email, photo, password, passwordConfirm

``` js
const mongoose = require('mongoose')
const validator = require('validator')
const bycrpt = require('bcryptjs)

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Please tell us your name!'],
  },
  email: {
    type: String,
    required: [true, 'Please provide your email'],
    unique: true,
    lowercase: true,
    validate: [validator.isEmail, 'Please provide a valid email'],
  },
  photo: String,
  password: {
    type: String,
    required: [true, 'Please provide a password'],
    trim: true,
    minlength: 8,
  },
  passwordConfirm: {
    type: String,
    required: [true, 'Please confirm your password'],
    validate: {
      // This only works on CREATE or SAVE!!
      validator(el) {
        return el === this.password
      },
      message: 'Password are not the same!',
    },
  },
})

const User = mongoose.model('User', userSchema)
module.exports = User
```

## 定義 Route
routes/userRoutes.js
``` js
const express = require('express')
const userController = require('../controllers/userController')
const authController = require('../controllers/authController')

router.post('/signup', authController.signup)
```

## 定義 AuthController
controllers/authController.js
``` js
const User = require('../models/userModel')
const catchAsync = require('../utils/catchAsync')

exports.signup = catchAsync(async (req, res, next) => {
  const newUser = await User.create(req.body)

  res.status(201).json({
    status: 1,
    data: {
      user: newUser,
    },
  })
})
```

## 加密密碼
開啟 userModel.js，在存入 user 資料前做加密，會利用到 bycrpt.hash 方法

``` js
userSchema.pre('save', async function(next) {
  // Only run this function if password was actually modifeid
  if (!this.isModified('password')) return next()

  // Hash the password with cost of 12 加密密碼
  this.password = await bycrpt.hash(this.password, 12)

  // Delete passwordConfirm field
  this.passwordConfirm = undefined

  // next step
  next()
})
```

### 完整程式碼
``` js
const mongoose = require('mongoose')
const validator = require('validator')
const bycrpt = require('bcryptjs')

// name, email. photo, password, passwordConfirm

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Please tell us your name!'],
  },
  email: {
    type: String,
    required: [true, 'Please provide your email'],
    unique: true,
    lowercase: true,
    validate: [validator.isEmail, 'Please provide a valid email'],
  },
  photo: String,
  password: {
    type: String,
    required: [true, 'Please provide a password'],
    trim: true,
    minlength: 8,
  },
  passwordConfirm: {
    type: String,
    required: [true, 'Please confirm your password'],
    validate: {
      // This only works on CREATE or SAVE!!
      validator(el) {
        return el === this.password
      },
      message: 'Password are not the same!',
    },
  },
})

userSchema.pre('save', async function (next) {
  // Only run this function if password was actually modifeid
  if (!this.isModified('password')) return next()

  // Hash the password with cost of 12
  this.password = await bycrpt.hash(this.password, 12)

  // Delete passwordConfirm field
  this.passwordConfirm = undefined
  next()
})

const User = mongoose.model('User', userSchema)
module.exports = User
```

## Reference
- [[Udemy] Nodejs Express Mongodb Bootcamp - 126. Managing Password](https://www.udemy.com/course/nodejs-express-mongodb-bootcamp/learn/lecture/15065288#overview)