---
layout: tutorial
title: Binary File S3
---

# Binary File S3

เมื่อต้องการเก็บข้อมูลต่างๆ ไว้ใน Bucket S3 ทางระบบ MEp มีบริการที่ผู้ใช้สามารถส่งข้อมูลไปเก็บไว้ที่ S3 โดยใช้ task.iterator

โดยในตัวอย่างนี้ จะจำลองการส่งข้อมูลไฟล์ภาพ ส่งไปยัง MEp และให้ MEp ส่งข้อมูลภาพไฟเก็บไว้ยัง S3

เขียนโปรแกรม ส่งรูปภาพไปที่ Endpoint โดยรับภาพที่ส่งมาไปเก็บไว้ที่ S3

### ตัวอย่างการเขียนโปรแกรมที่ใช้ส่งค่าไปที่ Endpoint ในตัวอย่างจะใช้ NodeJS ในการส่งข้อมูลไปยัง MEp

* ติดตั้ง Module

  ```
  npm install --save request
  npm install --save fs
  ```

* สร้างไฟล์ `example-4.js` แล้วเขียนโปรแกรมตามตัวอย่าง

  ```javascript
  const request = require("request-promise")
  const fs = require('fs')

  let postEndpoint = {
    method: 'POST',
    port: 443,
    uri: 'https://e.mep.meca.in.th/e/demo/s3Keeper',
    headers: {
      'Content-Type': 'multipart/form-data'
    },
    auth: {
      user: 'user',
      pass: 'pass'
    },
    formData : {
      image : null
    }
  }

  request('http://bugatti.nvfast.org/Snapshots/106.jpeg?015d2e2a-d444-4be2-aab3-9f2fb7f46d5b').pipe(fs.createWriteStream('s3.png')).on('close', function () {
    postEndpoint.formData.image = fs.createReadStream('./s3.png')
    request(postEndpoint).then(function (parsedBody) {
      console.log(Date().toString() + ': send to endpoint')
    }).catch(function (err) {
      console.log('post error\n' + err)
    })
  })
  ```

### ตัวอย่างการสร้าง Endpoint ใน MEp เพื่อรับข้อมูล

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.s3Keeper`
![alt text](./images/s3Keeper/1.png 'End-Point Form')

* กรอกข้อมูลในฟอร์ม > กด Create
![alt text](./images/s3Keeper/2.png 'End-Point Form')

  |**CLIEND OPTION**|  |
  |**AWS Access Key ID**| ตัวอย่าง `VYWLAU8OREV2DXRDGVXP` |
  |**AWS Secret Access Key**| ตัวอย่าง `halOmKuigFYlf6lmE3JCv4crzxwoMFmcHV1BEArE` |
  |**Use SSL**| `TRUE` |
  |**End-Point URL**| ตัวอย่าง `https://s3.meca.in.th` |
  |**PUT OBJECT OPTION**||
  |**Body**| ตัวอย่าง `multipart` |
  |**Bucket**| ตัวอย่าง `botdemo` |
  |**Key Prefix**| ตัวอย่าง `CCTV_Charleston/{{templateVariable.datetimeISOString}}.jpg`|
  <br />

* ในหน้า `End-Point` จะพบกับ end-point ที่สร้าง `mep-service:demo:endpoints:s3Keeper` กดเข้าไป จะพบกับหน้า `End-Point Detail` ตรวจสอบ end-point ที่สร้าง
  * Request History (Request ที่ส่งมาจาก NodeJS)
  ![alt text](./images/s3Keeper/3.png 'Request History')
  * Request Result (ผลลัพธ์ของ task.s3Keeper)
  ![alt text](./images/s3Keeper/4.png 'Request Result')
  * ตรวจสอบด้วย S3 Browser (ข้อมูลส่งมาจาก End-Point)
  ![alt text](./images/s3Keeper/5.png 's3 Browser')
