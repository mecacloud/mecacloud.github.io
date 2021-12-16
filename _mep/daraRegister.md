---
layout: tutorial
title: Dara Register and Request Service
---

สำหรับผู้ใช้งานที่ต้องการใช้บริการ Dara Platform ต้องทำการละทะเบียนเพื่อใช้งานบริการต่างๆของ Dara Platform

### ตัวอย่างการลงทะเบียนใช้บริการ MEp

* ลงทะเบียนเพื่อขอใช้บริการโดยเข้าไปที่ URL [mep.meca.in.th](https://mep.meca.in.th/)

  ![alt text](./images/register/1.png 'Keycloak Login')

* ลงทะเบียนโดยคลิก `Register` > กรอก
  
  ![alt text](./images/register/2.png 'Keycloak Register')

* กรอกข้อมูล (* First name, Last name, Email, Username, Password) เมื่อกรอกข้อมูลเสร็จแล้ว กด `Register` 
  
  ![alt text](./images/register/3.png 'Keycloak Register')

* เมื่อกด `Register` ระบบจะทำการ Redirect เข้าสู่หน้าเว็ปไซต์ 
  
  ![alt text](./images/register/4.png 'Dara Platform Home Page')

* ที่ Menu Bar ด้านบน คลิกที่ `FIWARE` > `Services`
  
  ![alt text](./images/register/5.png 'Menu Bar')

* เมื่อเข้ามาที่หน้า Fiware Servicesคลิกที่ `Add New Service` (เครื่องหมาย +)
  
  ![alt text](./images/register/6.png 'New Fiware Service')

* กรอกชื่อที่ขอใช้บริการ > กด `CREATE`
  
  ![alt text](./images/register/7.png ' Servcie Name')

* แท็บ `SERVICE REQUESTS` จะพบกับ Service ที่ขอใช้บริการ แจ้ง Admin หรือรอสักครู่ เพื่อให้ Admin อนุมัติ Service
  
  ![alt text](./images/register/8.png 'Service Request')

* เมื่อ Admin อนุมัติ Service แล้ว Menu Bar จะมี Menu `Servcie` ขึ้นมา และหน้า `Fiware Service` ปรากฏ Service ที่ขอใช้บริการ ในตัวอย่างคือ `servcie-test`
  
  ![alt text](./images/register/9.png 'Service Request')
