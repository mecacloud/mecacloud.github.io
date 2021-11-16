---
layout: default
---
# Documentations

## Presentation
รวบรวม การนำเสนอ และอบรม ที่เกี่ยวข้องกับ MECAs

| ชื่อการอบรม        | วัน เวลา         | สถานที่ | ดาวน์โหลด |
|:-------------|:------------------|:------|:------|
| แนะนำการประยุกต์ใช้งานด้าน IoT บนบริการ MECAs | วันศุกร์ที่ 10 มกราคม 2563 เวลา 08.30 - 16.30 น.  |  ห้องบุษกร อาคารเนคเทค  | [Presentation](/assets/pdf/2020-01-10-MECAS-IoT.pdf) / [Demo - IoT DIY](/assets/pdf/2020-01-10-Demo-IoT-DIY.pdf) [ESP8266 Manual](/assets/pdf/2020-01-10-ESP8266-Manual.pdf)|
| สัมมนาและแลกเปลี่ยนเรียนรู้ Special Talks หัวข้อ “Cloud Computing”  | วันอังคารที่ 9 กรกฎาคม 2562 เวลา 09.00 - 12.00 น.  |  ห้องบุษกร อาคารเนคเทค  | [Presentation](/assets/pdf/2019-07-09-MECAs-Update.pdf) |
| การใช้บริการ MECAs (Cloud Container Service Platform)  | วันอังคารที่ 2 เมษายน 2562 เวลา 08.30 - 16.30 น.  |  ห้องบุษกร อาคารเนคเทค  | [Presentation](/assets/pdf/2019-04-02-MECAs-Intro.pdf) / [Hand on](/assets/pdf/2019-04-02-MECAs-Application-Workshop.pdf) |
| ทำความรู้จัก Micro Service, Container Technology และการใช้ Docker | วันพฤหัสบดีที่ 7 มีนาคม 2562 เวลา 08.30 - 16.30 น.  |  ห้อง CC306 อาคารศูนย์ประชุมอุทยานวิทยาศาสตร์ประเทศไทย  | [Presentation](/assets/pdf/2019-03-07-Microservices-Basic-Docker.pdf) / [Windows Setup](/assets/pdf/2019-03-07-Prepare-Windows.pdf) / [Ubuntu Setup](/assets/pdf/2019-03-07-Prepare-Ubuntu.pdf) / [Hand on#1](/assets/pdf/2019-03-07-Hand-on-Create-new-Django-application.pdf) / [Hand on#2](/assets/pdf/2019-03-07-Hand-on-Docker-build.pdf) / [Example YAML](/assets/pdf/2019-03-07--example-yaml.zip) |

## Installation
{% for install in site.installs %}
  * [{{ install.title }}]({{ install.url }})
{% endfor %}

## Tutorial
{% for tutorial in site.tutorials %}
  * [{{ tutorial.title }}]({{ tutorial.url }})
{% endfor %}

## Concept
{% for concept in site.concepts %}
  * [{{ concept.title }}]({{ concept.url }})
{% endfor %}

