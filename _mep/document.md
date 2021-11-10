# Template and Jinja

เขียนโปรแกรม Call Weather API และนำค่า Payload Response ส่งไปที่ Endpoint โดยรับค่าที่ได้มาปรับแต่งให้เป็นรูปแบบ Jinja Template

* `Jinja` - Template Variable ที่สามารถปรับแต่งในรูปแบบ JSON
* `Payload` - Response Message ที่ได้จากการ Call API

### Call Weather API
[Weather API](https://api.openweathermap.org/data/2.5/weather?q=Bangkok&appid=f47eeabecaa43594079886ab0fcd1508&units=metric)

Payload Response
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
### Jinja Template.

```jinja
{
  "at": "{{templateVariable.datetimeISOString}}",
  "temperature": {{main.temp}},
  "humidity": {{main.humidity}}
}
```

โปรแกรมที่ใช้ส่งค่าไปที่ Endpoint ในตัวอย่างจะใช้ NodeJS ในการส่งข้อมูล

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
### สร้าง Endpoint เพื่อรับข้อมูล

* เข้าไปที่ URL [mep.meca.in.th](https://mep.meca.in.th/) > เลือกเมนู `END-POINT` > กด Add new End-Point (เครื่องหมาย +)
![alt text](./images/jinjaTemplate/1.png 'Add new End-Point')

* กรอกข้อมูลตามแบบฟอร์มที่กำหนด
![alt text](./images/jinjaTemplate/2.png 'End-Point Form')

  |   |   |
  |---|---|
  |__Name__|  ชื่อ End-Point  |
  | __Authentication__ ||
  |__Methods__| รูปแบบการ Authentication|
  |__Key__| username|
  |__Secret__| password หรือ secret key|
  | __Parse Request__ ||
  | __Error Handle__ |  กรณีเกิด Error กับ end-point ให้ดำเนินการต่ออย่างไร (bytes, rise, drop)|
  |__Processors (Tasks)__| Task ของ end-point ที่ใช้ในการจัดการข้อมูล|

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.jinja`
![alt text](./images/jinjaTemplate/4.png 'End-Point Form')

* กรอกข้อมูลในช่อง Template String > กด Create
  * `templateVariable` เป็นตัวแปรในระบบที่สามารถเรียกใช้ได้ทันที ตัวอย่าง `templateVariable.datetimeISOString` ผลลัพธ์ที่ได้คือ _2021-11-09T09:29:49.305107+00:00_
  * ในการอ้างอิงถึง payload response จะใช้ `payload`

    ```jinja
    {
      "at": "{{templateVariable.datetimeISOString}}",
      "temperature": {{payload.main.temp}},
      "humidity": {{payload.main.humidity}}
    }
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
  
# Iterator

เขียนโปรแกรม Call Weather API และนำค่า Payload Response ส่งไปที่ Endpoint โดยรับค่าที่ได้มาปรับแต่งให้เป็นรูปแบบ Jinja Template

### Call Weather API
[Weather API](https://api.openweathermap.org/data/2.5/find?lat=13.764968&lon=100.538245&cnt=10&appid=f47eeabecaa43594079886ab0fcd1508&units=metric)

Payload Response
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
    },
    {
      "id": 1610858,
      "name": "Dusit",
      "coord": {
        "lat": 13.7775,
        "lon": 100.5198
      },
      "main": {
        "temp": 27.41,
        "feels_like": 29.47,
        "temp_min": 26.64,
        "temp_max": 27.97,
        "pressure": 1013,
        "humidity": 69
      },
      "dt": 1636509926,
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
      "id": 1607327,
      "name": "Pom Prap Sattru Phai",
      "coord": {
        "lat": 13.7582,
        "lon": 100.5133
      },
      "main": {
        "temp": 27.39,
        "feels_like": 29.43,
        "temp_min": 26.62,
        "temp_max": 27.95,
        "pressure": 1013,
        "humidity": 69
      },
      "dt": 1636509925,
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
      "id": 1609350,
      "name": "Bangkok",
      "coord": {
        "lat": 13.75,
        "lon": 100.5167
      },
      "main": {
        "temp": 27.4,
        "feels_like": 30.3,
        "temp_min": 26.61,
        "temp_max": 27.94,
        "pressure": 1013,
        "humidity": 77,
        "sea_level": 1013,
        "grnd_level": 1012
      },
      "dt": 1636510149,
      "wind": {
        "speed": 1.04,
        "deg": 346
      },
      "sys": {
        "country": "TH"
      },
      "rain": null,
      "snow": null,
      "clouds": {
        "all": 96
      },
      "weather": [
        {
          "id": 804,
          "main": "Clouds",
          "description": "overcast clouds",
          "icon": "04d"
        }
      ]
    },
    {
      "id": 7026842,
      "name": "Pathum Wan",
      "coord": {
        "lat": 13.7365,
        "lon": 100.5239
      },
      "main": {
        "temp": 27.46,
        "feels_like": 30.43,
        "temp_min": 26.64,
        "temp_max": 27.97,
        "pressure": 1013,
        "humidity": 77,
        "sea_level": 1013,
        "grnd_level": 1012
      },
      "dt": 1636510446,
      "wind": {
        "speed": 1.08,
        "deg": 359
      },
      "sys": {
        "country": "TH"
      },
      "rain": null,
      "snow": null,
      "clouds": {
        "all": 96
      },
      "weather": [
        {
          "id": 804,
          "main": "Clouds",
          "description": "overcast clouds",
          "icon": "04d"
        }
      ]
    },
    {
      "id": 6695671,
      "name": "Sukhumvit",
      "coord": {
        "lat": 13.735,
        "lon": 100.5561
      },
      "main": {
        "temp": 27.51,
        "feels_like": 30.54,
        "temp_min": 26.63,
        "temp_max": 27.96,
        "pressure": 1013,
        "humidity": 77,
        "sea_level": 1013,
        "grnd_level": 1012
      },
      "dt": 1636510444,
      "wind": {
        "speed": 1.11,
        "deg": 14
      },
      "sys": {
        "country": "TH"
      },
      "rain": null,
      "snow": null,
      "clouds": {
        "all": 96
      },
      "weather": [
        {
          "id": 804,
          "main": "Clouds",
          "description": "overcast clouds",
          "icon": "04d"
        }
      ]
    },
    {
      "id": 1619278,
      "name": "Bangrak",
      "coord": {
        "lat": 13.7306,
        "lon": 100.5239
      },
      "main": {
        "temp": 27.42,
        "feels_like": 30.35,
        "temp_min": 26.59,
        "temp_max": 27.92,
        "pressure": 1013,
        "humidity": 77,
        "sea_level": 1013,
        "grnd_level": 1012
      },
      "dt": 1636509927,
      "wind": {
        "speed": 1.12,
        "deg": 4
      },
      "sys": {
        "country": "TH"
      },
      "rain": null,
      "snow": null,
      "clouds": {
        "all": 96
      },
      "weather": [
        {
          "id": 804,
          "main": "Clouds",
          "description": "overcast clouds",
          "icon": "04d"
        }
      ]
    },
    {
      "id": 1608132,
      "name": "Nonthaburi",
      "coord": {
        "lat": 13.75,
        "lon": 100.5
      },
      "main": {
        "temp": 27.37,
        "feels_like": 30.24,
        "temp_min": 26.62,
        "temp_max": 27.95,
        "pressure": 1013,
        "humidity": 77,
        "sea_level": 1013,
        "grnd_level": 1012
      },
      "dt": 1636509926,
      "wind": {
        "speed": 1.09,
        "deg": 340
      },
      "sys": {
        "country": "TH"
      },
      "rain": null,
      "snow": null,
      "clouds": {
        "all": 95
      },
      "weather": [
        {
          "id": 804,
          "main": "Clouds",
          "description": "overcast clouds",
          "icon": "04d"
        }
      ]
    },
    {
      "id": 1606640,
      "name": "Samphanthawong",
      "coord": {
        "lat": 13.7315,
        "lon": 100.5141
      },
      "main": {
        "temp": 27.44,
        "feels_like": 30.39,
        "temp_min": 26.63,
        "temp_max": 27.96,
        "pressure": 1013,
        "humidity": 77,
        "sea_level": 1013,
        "grnd_level": 1012
      },
      "dt": 1636509925,
      "wind": {
        "speed": 1.12,
        "deg": 359
      },
      "sys": {
        "country": "TH"
      },
      "rain": null,
      "snow": null,
      "clouds": {
        "all": 95
      },
      "weather": [
        {
          "id": 804,
          "main": "Clouds",
          "description": "overcast clouds",
          "icon": "04d"
        }
      ]
    }
  ]
}
```

### Jinja Template.

```jinja
{
  "at": "{{templateVariable.datetimeISOString}}",
  "city": "{{name}}",
  "temperature": {{main.temp}},
  "humidity": {{main.humidity}}
}
```

จากตัวอย่าง payload response key ที่ต้องการใช้คือ `list` ซึ่งเป็น array ต้องสร้าง end-point เพื่อนำค่าที่อยู่ใน list มาจัดการให้อยู่ในรูปแบบ jinja template

โปรแกรมที่ใช้ส่งค่าไปที่ Endpoint ในตัวอย่างจะใช้ NodeJS ในการส่งข้อมูล

* สร้างไฟล์ `example-2.js` แล้วเขียนโปรแกรมตามตัวอย่าง
  ```javascript
  const request = require("request-promise")
  const getData = {
    uri: 'https://api.openweathermap.org/data/2.5/find?lat=13.764968&lon=100.538245&cnt=10&appid=f47eeabecaa43594079886ab0fcd1508&units=metric',
    json: true
  }
  let postEndpoint = {
    method: 'POST',
    uri: 'https://e.mep.meca.in.th/e/demo/iteratorJinja',
    auth: {
      user: 'user',
      pass: 'pass'
    },
    body: null,
    json: true
  }

  const timerId = setInterval(() => {
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
    }, 1 * 60 * 1000)
    setTimeout(() => {
      clearInterval(timerId)
    }, 8.64e+7)
  ```

### สร้าง Endpoint เพื่อรับข้อมูล

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.iterator`
![alt text](./images/iteratorJinja/1.png 'End-Point Form')

* กรอกข้อมูลในฟอร์ม
![alt text](./images/iteratorJinja/2.png 'End-Point Form')

  |   |   |
  |---|---|
  |__Each__|  การเข้าถึง payload ที่เป็น array  |
  ```jinja
  {{payload.list|tojson}}
  ```

* คลิกไอคอนด้านขวามือของ `iterator` > เลือก `Add Group`
![alt text](./images/iteratorJinja/3.png 'Add Group')

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.jinja`
![alt text](./images/iteratorJinja/4.png 'task.jinja')

* กรอกข้อมูลในช่อง Template String > กด Create
  * ในการอ้างอิงถึง iterator จะใช้ `args` ลำดับการทำงานที่ 0 หมายถึง task ล่าสุด และลำดับการทำงานจะเพิ่มขึ้นตาม task ก่อนหน้า

    ```jinja
    {
      "at": "{{templateVariable.datetimeISOString}}",
      "city": "{{args.0.name}}",
      "temperature": {{args.0.main.temp}},
      "humidity": {{args.0.main.humidity}}
    }
    ```
* ในหน้า `End-Point` จะพบกับ end-point ที่สร้าง `mep-service:demo:endpoints:iteratorJinja` กดเข้าไป จะพบกับหน้า `End-Point Detail` ตรวจสอบ end-point ที่สร้าง
  * Request History (Request ที่ส่งมาจาก NodeJS)
  ![alt text](./images/iteratorJinja/5.png 'Request History')
  * Request Result (ผลลัพธ์ของ task.jinja)
  ![alt text](./images/iteratorJinja/6.png 'Request Result')