---
layout: tutorial
title: Iterator
---

เมื่อต้องจัดการกับข้อมูลที่เป็น Array ระบบของเราก็มีบริการรองรับการใช้งานของผู้ใช้ ที่ช่วยจัดการข้อมูลที่เป็น Array ที่ส่งมาจากที่ใดก็ตาม โดยจะนำค่า Payload ที่ส่งมามาจัดการต่อใน task.iterator

โดยในตัวอย่างนี้ จะจำลองการส่งข้อมูลสภาพอากาศแต่ละเมือง โดยใช้ Weather API และส่งไปที่ MEp

เขียนโปรแกรม Call Weather API และนำค่า Payload Response ส่งไปที่ Endpoint โดยรับค่าที่ได้มาปรับแต่งให้เป็นรูปแบบ Jinja Template

### ตัวอย่างการ Call Weather API
[Weather API](https://api.openweathermap.org/data/2.5/find?lat=13.764968&lon=100.538245&cnt=10&appid=f47eeabecaa43594079886ab0fcd1508&units=metric)

### ตัวอย่าง Payload Response
```json
{
  "message": "accurate",
  "cod": "200",
  "count": 10,
  "list": [
    {
      "id": 7026885,
      "name": "Ratchathewi",
      "coord": {
        "lat": 13.759,
        "lon": 100.5336
      },
      "main": {
        "temp": 27.43,
        "feels_like": 29.5,
        "temp_min": 26.62,
        "temp_max": 27.95,
        "pressure": 1013,
        "humidity": 69
      },
      "dt": 1636510446,
      "wind": {
        "speed": 3.6,
        "deg": 10
      },
      "sys": {
        "country": "TH"
      },
      "rain": null,
      "snow": null,
      "clouds": {
        "all": 40
      },
      "weather": [
        {
          "id": 802,
          "main": "Clouds",
          "description": "scattered clouds",
          "icon": "03d"
        }
      ]
    },
    {
      "id": 7026870,
      "name": "Phaya Thai",
      "coord": {
        "lat": 13.7801,
        "lon": 100.5427
      },
      "main": {
        "temp": 27.43,
        "feels_like": 29.5,
        "temp_min": 26.63,
        "temp_max": 27.96,
        "pressure": 1013,
        "humidity": 69
      },
      "dt": 1636510446,
      "wind": {
        "speed": 3.6,
        "deg": 10
      },
      "sys": {
        "country": "TH"
      },
      "rain": null,
      "snow": null,
      "clouds": {
        "all": 40
      },
      "weather": [
        {
          "id": 802,
          "main": "Clouds",
          "description": "scattered clouds",
          "icon": "03d"
        }
      ]
    }
  ]
}
```

จากตัวอย่าง payload response key ที่ต้องการใช้คือ `list` ซึ่งเป็น array ต้องสร้าง end-point เพื่อนำค่าที่อยู่ใน list มาจัดการให้อยู่ในรูปแบบ jinja template

### ตัวอย่างการสร้าง Endpoint ใน MEp เพื่อรับข้อมูล

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.iterator`
![alt text](./images/iteratorJinja/1.png 'End-Point Form')

* กรอกข้อมูลในฟอร์ม
![alt text](./images/iteratorJinja/2.png 'End-Point Form')

  __Earch__ - การเข้าถึง payload ที่เป็น array

{% raw %}
    ```jinja
      {{payload.list|tojson}}    
    ```
{% endraw %}

* คลิกไอคอนด้านขวามือของ `iterator` > เลือก `Add Group`
![alt text](./images/iteratorJinja/3.png 'Add Group')

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.jinja`
![alt text](./images/iteratorJinja/4.png 'task.jinja')

* กรอกข้อมูลในช่อง Template String > กด Create
  * ในการอ้างอิงถึง iterator จะใช้ `args` ลำดับการทำงานที่ 0 หมายถึง task ล่าสุด และลำดับการทำงานจะเพิ่มขึ้นตาม task ก่อนหน้า
    {% raw %}
    ```jinja
    {
      "at": "{{templateVariable.datetimeISOString}}",
      "city": "{{args.0.name}}",
      "temperature": {{args.0.main.temp}},
      "humidity": {{args.0.main.humidity}}
    }  
    ```
  {% endraw %}

### ตัวอย่างการเขียนโปรแกรมที่ใช้ส่งค่าไปที่ Endpoint ในตัวอย่างจะใช้ NodeJS ในการส่งข้อมูลไปยัง MEp

* ติดตั้ง Module

  ```
  npm install --save request-promise
  ```

* สร้างไฟล์ `example-2.js` แล้วเขียนโปรแกรมตามตัวอย่าง
  ```javascript
  const request = require("request-promise")
  const getData = {
    uri: 'https://api.openweathermap.org/data/2.5/find?lat=13.764968&lon=100.538245&cnt=10&appid=f47eeabecaa43594079886ab0fcd1508&units=metric',
    json: true
  }
  let postEndpoint = {
    method: 'POST',
    uri: 'https://e.mep.meca.in.th/e/<SERVICE_NAME>/<ENDPOINT_NAME>',
    auth: {
      user: 'user',
      pass: 'pass'
    },
    body: null,
    json: true
  }

  const fetchWeather = () => {
    request(getData)
      .then(function (repos) {
        postEndpoint.body = repos
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
    }

  fetchWeather()

  const timerId = setInterval(fetchWeather, 5 * 60 * 1000)
    setTimeout(() => {
      clearInterval(timerId)
    }, 8.64e+7)
  
  ```

### ตรวจสอบการทำงานของ Endpoint

* ในหน้า `End-Point` จะพบกับ end-point ที่สร้าง `mep-service:demo:endpoints:iteratorJinja` กดเข้าไป จะพบกับหน้า `End-Point Detail` ตรวจสอบ end-point ที่สร้าง
  * Request History (Request ที่ส่งมาจาก NodeJS)
  ![alt text](./images/iteratorJinja/5.png 'Request History')
  * Request Result (ผลลัพธ์ของ task.jinja)
  ![alt text](./images/iteratorJinja/6.png 'Request Result')