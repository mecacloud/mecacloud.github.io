---
layout: tutorial
title: Jinja and Template
---

# Template and Jinja
บ่อยครั้ง ที่ข้อมูลที่ได้รับ จากอุปกรณ์ หรือระบบอื่น มีรูปแบบที่ไม่เข้ากันได้กับ ระบบที่ต้องการรับข้อมูล จึงมีความต้องการที่จะเขียนโปรแกรมเพื่อเปลี่ยนแปลงรูปแบบของข้อมูลที่ได้รับ ระบบ MEp มาพร้อมกับ ระบบ Template ที่จะช่วยให้การเขียน การตั้งค่าสามารถใช้ Jinja Template Language เพื่อดึงข้อมูลจาก Payload ที่ได้รับ มาใช้ได้

โดยในตัวอย่างนี้ จะจำลองการส่งข้อมูลสภาพอากาศ โดยใช้ Weather API และส่งไปที่ MEp

เขียนโปรแกรม Call Weather API และนำค่า Payload Response ส่งไปที่ Endpoint โดยรับค่าที่ได้มาปรับแต่งให้เป็นรูปแบบ Jinja Template

### ตัวอย่างการ Call Weather API
[Weather API](https://api.openweathermap.org/data/2.5/weather?q=Bangkok&appid=f47eeabecaa43594079886ab0fcd1508&units=metric)

### ตัวอย่าง Payload Response
```json
{
  "coord": {
    "lon": 100.5167,
    "lat": 13.75
  },
  "weather": [
    {
      "id": 804,
      "main": "Clouds",
      "description": "overcast clouds",
      "icon": "04d"
    }
  ],
  "base": "stations",
  "main": {
    "temp": 29.84,
    "feels_like": 33.54,
    "temp_min": 27.17,
    "temp_max": 31.06,
    "pressure": 1007,
    "humidity": 65
  },
  "visibility": 10000,
  "wind": {
    "speed": 3.02,
    "deg": 49,
    "gust": 4.78
  },
  "clouds": {
    "all": 99
  },
  "dt": 1636364645,
  "sys": {
    "type": 2,
    "id": 2003771,
    "country": "TH",
    "sunrise": 1636326887,
    "sunset": 1636368515
  },
  "timezone": 25200,
  "id": 1609350,
  "name": "Bangkok",
  "cod": 200
}
```

### ตัวอย่างการเขียน Jinja Template เพื่อแปลงข้อมูลให้ได้รูปแบบตามต้องการ 

โดยตัวแปรที่อยู่ภายในเครื่องหมาย {% raw %}`{{ }}`{% endraw %} จะถูกแทนที่ด้วยค่าของตัวแปร

```jinja
{% raw %}
{
  "at": "{{templateVariable.datetimeISOString}}",
  "temperature": {{payload.main.temp}},
  "humidity": {{payload.main.humidity}}
}
{% endraw %}
```

ตัวแปรที่เรียกใช้ได้ใน Template
<br />

  |**ตัวแปร**|**คำอธิบาย**|
  | `templateVariable` |  ตัวแปรของ Template เก็บค่าที่ช่วยในการใช้งาน เช่น datetimeISOString เก็บค่าวัน เวลาปัจจุบันในรูปแบบของ ISO String  |
  | `payload` |  ค่าที่ได้รับจาก Request ในส่วนที่เป็น Payload  |
  | `headers` |  ค่าที่ได้รับจาก Request ในส่วนที่เป็น Header  |
  | `params` |  ค่าที่ได้รับจาก Request ในส่วนที่เป็น URL Parameter  |
  | `args` |  Array ของค่าที่ได้รับจาก Task ก่อนหน้า  |


### ตัวอย่างการสร้าง Endpoint ใน MEp เพื่อรับข้อมูล

* เข้าไปที่ URL [mep.meca.in.th](https://mep.meca.in.th/) > เลือกเมนู `END-POINT` > กด Add new End-Point (เครื่องหมาย +)
![alt text](./images/jinjaTemplate/1.png 'Add new End-Point')

* กรอกข้อมูลตามแบบฟอร์มที่กำหนด
![alt text](./images/jinjaTemplate/2.png 'End-Point Form')
  
  |**Name**|ชื่อ End-Point|
  |**Authentication**
  |**Methods**|รูปแบบการ Authentication|
  |**Key**|username|
  |**Secret**|password หรือ secret key|
  |**Parse Request**
  |**Error Handle**|กรณีเกิด Error กับ end-point ให้ดำเนินการต่ออย่างไร (bytes, rise, drop)|
  |**Processors**| Task ของ end-point ที่ใช้ในการจัดการข้อมูล|
  <br />

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.jinja`
![alt text](./images/jinjaTemplate/4.png 'End-Point Form')

* กรอกข้อมูลในช่อง Template String > กด Create
  * `templateVariable` เป็นตัวแปรในระบบที่สามารถเรียกใช้ได้ทันที ตัวอย่าง `templateVariable.datetimeISOString` ผลลัพธ์ที่ได้คือ _2021-11-09T09:29:49.305107+00:00_
  * ในการอ้างอิงถึง payload response จะใช้ `payload`

    ```jinja
    {% raw %}
      {
        "at": "{{templateVariable.datetimeISOString}}",
        "temperature": {{payload.main.temp}},
        "humidity": {{payload.main.humidity}}
      }
    {% endraw %}
    ```

* ในหน้า `End-Point` จะพบกับ end-point ที่สร้าง `mep-service:demo:endpoints:jinjaTemplate` กดเข้าไป จะพบกับหน้า `End-Point Detail` ตรวจสอบ end-point ที่สร้าง
  * Request History (Request ที่ส่งมาจาก NodeJS)
  ![alt text](./images/jinjaTemplate/5.png 'Request History')
  * Request Result (ผลลัพธ์ของ task.jinja)
  ![alt text](./images/jinjaTemplate/6.png 'Request Result')
  * กรณีเกิด Exception ขึ้นอยู่ที่การ Config ของ Error Handle ตัวอย่างเลือก Rise เพื่อให้แสดง Error ที่เกิดขึ้น
  ![alt text](./images/jinjaTemplate/7.png 'Exception')
  * Logger (แสดง Log ของ task.logger)
  ![alt text](./images/jinjaTemplate/8.png 'Logger')
  
  
### ตัวอย่างการเขียนโปรแกรมที่ใช้ส่งค่าไปที่ Endpoint ในตัวอย่างจะใช้ NodeJS ในการส่งข้อมูลไปยัง MEp

* ติดตั้ง Module

  ```
  npm install --save request
  npm install --save request-promise
  ```

* สร้างไฟล์ `example-1.js` แล้วเขียนโปรแกรมตามตัวอย่าง

  ```javascript
  const request = require("request-promise")
  const getData = {
    uri: 'https://api.openweathermap.org/data/2.5/weather?q=Bangkok&appid=f47eeabecaa43594079886ab0fcd1508&units=metric',
    json: true
  }
  let postEndpoint = {
    method: 'POST',
    uri: 'https://e.mep.meca.in.th/e/<SERVICE_NAME>/<ENDPOINT_NAME>',
    auth: {
      user: '<USER>',
      pass: '<PASSWORD>'
    },
    body: null,
    json: true
  }

  const timerId = setInterval(() => {
    request(getData)
      .then(function (json) {
        postEndpoint.body = json
        request(postEndpoint)
          .then(function (parsedBody) {
            console.log(Date().toString() + ': send to endpoint')
          })
          .catch(function (err) {
            console.log('post error\n' + err)
        })
      })
      .catch(function (err) {
        console.log('get error:\n' + err)
      })
    }, 5 * 60 * 1000)
    setTimeout(() => {
      clearInterval(timerId)
    }, 8.64e+7)
  ```