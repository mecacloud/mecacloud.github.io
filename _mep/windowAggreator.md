---
layout: tutorial
title: Window Aggregator and Logger
---

# Window Aggregator and Logger

เขียนโปรแกรม ดึงค่า Free memory ในคอมพิวเตอร์ โดยใช้ command `free -m` และส่งไปที่ Endpoint โดยรับค่าที่ได้ มาหาค่าเฉลี่ยของ Free memory ในทุกทุก 5 นาที และแสดงผลออกมาทาง Log

### ตัวอย่างการเขียนโปรแกรมที่ใช้ส่งค่าไปที่ Endpoint ในตัวอย่างจะใช้ NodeJS ในการส่งข้อมูลไปยัง MEp

* ติดตั้ง Module

  ```
  npm install --save request
  npm install --save request-promise
  ```

* สร้างไฟล์ `example-3.js` แล้วเขียนโปรแกรมตามตัวอย่าง

  ```javascript
  const request = require("request-promise")
  const { exec } = require("child_process")

  let postEndpoint = {
    method: 'POST',
    uri: 'https://e.mep.meca.in.th/e/demo/windowAggregatorLogger',
    auth: {
      user: 'user',
      pass: 'pass'
    },
    body: null,
    json: true
  }
  let mem = null
  let template = {
    at: new Date().toISOString(),
    total: null,
    used: null,
    free: null
  }

  exec("free -m | grep Mem", (error, stdout, stderr) => {
    if (error) {
        console.log(`error: ${error.message}`)
        return
    }
    if (stderr) {
        console.log(`stderr: ${stderr}`)
        return
    }
    mem = stdout.split((/\s+/))
    template.total = Number(mem[1])
    template.used = Number(mem[2])
    template.free = Number(mem[3])
    postEndpoint.body = template
    let timerId = setInterval(() => {
      request(postEndpoint)
        .then(function (parsedBody) {
          console.log(`${Date().toString()}: send to endpoint`)
        })
        .catch(function (err) {
          console.log(`error: ${err}`)
      })
    }, 5 * 60 * 1000)
    setTimeout(() => {
      clearInterval(timerId)
    }, 8.64e+7)
  })
  ```

### ตัวอย่างการสร้าง Endpoint ใน MEp เพื่อรับข้อมูล

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.windowAggregator`
![alt text](./images/windowAggregatorLogger/1.png 'End-Point Form')

* กรอกข้อมูลในฟอร์ม
![alt text](./images/windowAggregatorLogger/2.png 'End-Point Form')

  |**Data Type**
  |**Key name**|Key ของ task.windowAggragator ตัวอย่าง `free_mem` |
  |**Value**|Value ของ task.windowAggragator ตัวอย่าง {% raw %}`{{paylaod.free}}`{% endraw %} ได้มาจาก payload response |
  |**End of Task**
  |**By**|สิ่งที่ให้ task ทำงาน ตัวอย่าง `timeout`|
  |**Operation**|ตัวดำเนินการ ตัวอย่าง `eq` (Equal)|
  |**Hash Key**|Hash Key |
  |**Value**|Value ของ end of task ตัวอย่าง `300` (5 นาที) |
  |**Return Value**|ผลลัพธ์ที่ถูกส่งออกของ task.windowAggragator ตัวอย่าง `avg` (Average) |

<br />
* คลิกไอคอนด้านขวามือของ `windowAggregator` > เลือก `Add Group`
![alt text](./images/windowAggregatorLogger/3.png 'Add Group')

* คลิกไอคอนด้านขวามือของ `Group 1` > เลือก `task.logger` > กด Create
![alt text](./images/windowAggregatorLogger/4.png 'task.logger')

* ในหน้า `End-Point` จะพบกับ end-point ที่สร้าง `mep-service:demo:endpoints:windowAggregatorLogger` กดเข้าไป จะพบกับหน้า `End-Point Detail` ตรวจสอบ end-point ที่สร้าง
  * Request History (Request ที่ส่งมาจาก NodeJS)
  ![alt text](./images/windowAggregatorLogger/5.png 'Request History')
  * Logger (ผลลัพธ์ของ task.windowAggregator แสดงค่าเฉลี่ย)
  ![alt text](./images/windowAggregatorLogger/6.png 'Request Result')