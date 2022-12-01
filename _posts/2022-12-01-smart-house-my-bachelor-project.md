---
layout: post
title:  "Smart Home - My bachelor project"
author: alin
categories: [ Internet Of Things ]
image: https://i.postimg.cc/ryFw0YZN/smarthome.jpg
featured: false
hidden: false
---

## Smart Home 🏡
I developed a Smart House in miniature. I used a wide range of tools such as Node.js, WebSocket, Ionic, Angular, Swagger, MongoDB, Arduino, Python, Raspberry PI, Docker, Sensors, NodeMCU ESP8266 modules, RFID module and many other hardware compoments.

The Smart House has the following functionalities: monitoring of several parameters (temperature, humidity, gas, soil humidify, air quality), fire and water leak warning, lighting and air conditioning control. Access system based on RFID system with the possibility to add new users to the system in real time and access monitoring.

#### Awards
🏆 This project won the First Place at [Session of Student Scientific Circles 2020](https://iesc.unitbv.ro/%C8%99tiri-%C8%99i-evenimente/470-sesiunea-cercurilor-stiintifice-studentesti-2020.html) - University of Transylvania, Electrical Engineering and Computer Science, Specialization in Telecommunications Technologies and Systems.

#### Architecture
In the diagram below we can see that I used 4 x NodeMCU ESP8266 WIFI modules for data transmission of which 3 x modules were used for data transmission from sensors to Node.js Server via WebSocket and the fourth module was used to control lighting and air conditioning.

![Diagram](/assets/images/smarthome/diagram1_en.png)


I used a NoSQL database to store the data from sensors, the server is written in Node.js and the access system based on RFID in python. 

#### API Documentation
![Swagger](/assets/images/smarthome//swagger.jpg)

#### Communication and data flow diagram
![Communication](/assets/images/smarthome//all_en.png)

#### The process of developing the hardware side
![Hands On](/assets/images/smarthome//hardware1.jpg)

![Hands On](/assets/images/smarthome//diagram3.jpeg)

#### Behind the scenes
![Smart House](/assets/images/smarthome//shome1.jpeg)


#### The system up and running by Docker Compose
![Docker](/assets/images/smarthome//docker.png)

#### Mobile Application - First Iteration

![First Iteration](/assets/images/smarthome//shomeapp1.jpg)


#### Mobile Application - Second Iteration
Learning more and more things I developed a new design and new functionalities to the app.

![Second Iteration](/assets/images/smarthome//shomeapp2.jpg)


#### The Access System interface
I created an interface for the access system, in which we can add/delete new users to the system in real time, we can see user access logs and the status of the door with a magnetic sensor.

![Smart House](/assets/images/smarthome//shomeapp3.jpg)


#### Results
I built a miniature Smart House where I learned a lot
![Smart House](/assets/images/smarthome//home1.jpeg)
![Smart House](/assets/images/smarthome//home2.jpeg)
![Smart House](/assets/images/smarthome//home3.jpeg)
![Smart House](/assets/images/smarthome//home4.jpeg)


### Future directions 
- Security: data encryption, authentication
on several levels, HTTPS
- New sensors: vibration, water level
- Application standardization: flexibility
- New features: IP surveillance cameras,
alarm system
- Data processing using Kafka Apache and MQTT
- Migrate backend in Spring Boot

