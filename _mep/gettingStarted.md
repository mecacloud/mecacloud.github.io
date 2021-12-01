---
layout: tutorial
title: Getting Started
---

บริการ MECAs Endpoint (MEp) เป็นบริการสร้าง HTTP Endpoint สำหรับรองรับข้อมูลผ่าน Protocol HTTP เหมาะสำหรับการ integrate ข้อมูลจากแหล่งข้อมูล เพื่อแปลงข้อมูล และส่งต่อ ข้อมูลให้กับแหล่งข้อมูลอื่น 

## Endpoint

เป็นส่วนที่ให้ผู้ใช้งาน สร้าง HTTP Endpoint โดยผู้ใช้จะได้รับ HTTP Path สำหรับรับข้อมูล ซึ่ง ผู้ใช้สามารถตั้งค่า ที่เกี่ยวข้องกับ HTTP Endpoint ได้

![alt text](./images/gettingStarted/EndpointForm.png 'Endpoint Form')

  |**Name**|ชื่อ End-Point|
  |**Authentication**
  |**Methods**|รูปแบบการ Authentication|
  |**Key**|username|
  |**Secret**|password หรือ secret key|
  |**Parse Request**
  |**Error Handle**|กรณีเกิด Error กับ end-point ให้ดำเนินการต่ออย่างไร (bytes, rise, drop)|
  |**Processors (Tasks)**| Task ของ end-point ที่ใช้ในการจัดการข้อมูล|

## การประมวลผล (Task)

เมื่อได้รับข้อมูลมาแล้ว ส่วนของข้อมูลสามารถนำไปผ่านตัวประมวลผล (Task) ต่าง ๆ โดยลำดับของการประมวล จะแบ่งออกเป็น Group ซึ่งจะทำงานคู่ขนานกัน (Parallel)

![alt text](./images/gettingStarted/Processor.png 'Endpoint Form')

และภายใน Group จะมี Task ที่ทำงานต่อเนื่องกันตามลำดับ (Sequence) โดยสามารถอ้างอิงผลลัพท์ของการประมวลใน Task ก่อนหน้าได้ ผ่านตัวแปร `args[0]` โดย `0` จะแทนลำดับของการประมวลผลก่อนหน้า ล่าสุด สามารถใช้ จำนวนอื่นในการอ้างอิงได้

### Task ที่มีให้บริการ

| **Task** | **คำอธิบาย** |
| **logger** | จัดเก็บค่าต่าง ๆ ลงในฐานข้อมูล เพื่อตรวจดูค่าที่ได้รับ และการทำงานของ Template ในการตั้งค่า Task |
| **HTTP Request** | ส่งค่า HTTP ไปยังแหล่งอื่น |
| **S3 Keeper** | จัดเก็บข้อมูลใน S3 Compatible Storage โดยสามารถเลือกส่วนที่จะจัดเก็บได้ เช่น ส่วนที่เป็น Binary Payload หรือ จัดเก็บในรูปแบบ String |
| **Iterator** | วนซ้ำข้อมูลที่ได้รับ ในกรณีที่ข้อมูลอยู่ในรูปของ Array ให้ประมวลแต่ละ Element ของ Array ให้นำไปทำงาน Task ย่อยได้ |
| **Window Aggreator** | จัดเก็บ และประมวลข้อมูล เช่น ค่าที่ได้รับ ผลรวม ค่าสูงสุด ค่าต่ำสุด ค่าเฉลี่ย เป็นชุด ตามเงือนไข ต่างๆ เช่น ค่าที่ได้รับ ระยะเวลาที่กำหนด จำนวน element ในชุดข้อมูล หรือ จำนวน key ของข้อมูล เมื่อครบตามเงื่อนไข จะนำผลลัพท์ไปทำงานใน Task ย่อยถัดไป |

## การใช้ Template

ระบบ MEp มาพร้อมกับ ระบบ Template ที่จะช่วยให้การเขียน การตั้งค่าสามารถใช้ Jinja Template Language เพื่อดึงข้อมูลจาก Payload ที่ได้รับ และค่าต่าง ๆ ที่เกี่ยวข้องกับ Request มาใช้ได้ โดยตัวแปรที่อยู่ภายในเครื่องหมาย {% raw %}`{{ }}`{% endraw %} จะถูกแทนที่ด้วยค่าของตัวแปร

### ตัวแปรที่เรียกใช้ได้ใน Template

  |**ตัวแปร**|**คำอธิบาย**|
  | `templateVariable` |  ตัวแปรของ Template เก็บค่าที่ช่วยในการใช้งาน เช่น datetimeISOString เก็บค่าวัน เวลาปัจจุบันในรูปแบบของ ISO String  |
  | `payload` |  ค่าที่ได้รับจาก Request ในส่วนที่เป็น Payload  |
  | `headers` |  ค่าที่ได้รับจาก Request ในส่วนที่เป็น Header  |
  | `params` |  ค่าที่ได้รับจาก Request ในส่วนที่เป็น URL Parameter  |
  | `args` |  Array ของค่าที่ได้รับจาก Task ก่อนหน้า  |

และสามารถใช้ `.` ใช้การเข้าถึงตัวแปรที่เป็นชนิด object หรือ array ได้ เช่น `payload.field` หรือ `payload.0` 

นอกจากนี้ ภายใน {% raw %}`{{ }}`{% endraw %} สามารถใช้ expression ของ Python ในการแทนที่ข้อมูลได้ เช่น

{% raw %}
```django
{{ payload.allow == 'YES' }}
```
{% endraw %}

จากตัวอย่าง จะมีการประมวลโดยใช้ expression จะเปรียบเทียบค่าของ `payload.allow` กับ string YES แล้วคืนค่าเป็น `True` หรือ `False`

โดย Template จะสามารถใช้ได้กับ การตั้งค่าในแบบฟอร์มต่างๆ ของ MEp ดังภาพ

![alt text](./images/gettingStarted/jinja-form.png 'Jinja Form')