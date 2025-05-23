The explanation will be in both Thai and English.
คำอธิบายจะมีทั้งภาษาไทยและภาษาอังกฤษ

Hello everyone, today I'm going to explain a room called 'Bugged'. Let's get started.
สวัสดีทุกคนวันนี้ผมจะมาอธิบายห้องที่มีชื่อว่า Bugged พร้อมเเล้วไปลุยกัน 

Let's start with the first step.
มาเริ่มกันที่ขั้นตอนแรกเลย

nmap -sV <ip>


![Image](https://github.com/user-attachments/assets/076e4653-bd17-4d51-9a85-705350c27606)



You can see that no ports were found out of the 1000 scanned.
จะเห็นได้ว่าไม่เจอพอร์ตเลยจาก1000พอร์ค


So we'll use RustScan for faster scanning.
เราจึงจะใช้rustscanเพื่อความรวดเร็ว

rustscan -a <ip>



![Image](https://github.com/user-attachments/assets/bac20879-239f-4467-a0a6-bd1e95bc2260)




As you can see, port 1883 (MQTT) was found.
จะเห็นได้ว่าเจอพอร์ต 1883 mqtt


Port 1883 is the default port for the MQTT (Message Queuing Telemetry Transport) protocol, which is a lightweight communication protocol commonly used for IoT (Internet of Things) devices.
พอร์ต 1883 คือพอร์ตเริ่มต้นของโปรโตคอล MQTT (Message Queuing Telemetry Transport) ซึ่งเป็นโปรโตคอลที่ใช้สื่อสารแบบ lightweight สำหรับอุปกรณ์ IoT (Internet of Things)



What is it used for?
:It allows sensors, smart home devices, or CCTV cameras to send data to a server (called a “Broker”).
:Examples of well-known brokers include: Mosquitto, EMQX, HiveMQ.
ใช้ทำอะไร?
 :ให้เซนเซอร์, อุปกรณ์สมาร์ตโฮม หรือกล้องวงจรปิด ส่งข้อมูลไปยัง server (เรียกว่า “Broker”)
 :ตัวอย่าง broker ที่รู้จัก: Mosquitto, EMQX, HiveMQ

Let's try this command.
เรามาลองคำสั่งนี้

 nmap -sV -sC -vvv -p 1883

The command used:
nmap -sV -sC -vvv -p 1883

Explanation of the options:

  -sV: Detects which service is running and its version (Service/version detection).

  -sC: Runs basic NSE scripts that come with Nmap to gather additional information (equivalent to --script=default).

  -vvv: Enables the highest level of verbosity, showing very detailed output about the scan process.

  -p 1883: Specifically scans only port 1883 (the MQTT port).

 nmap -sV -sC -vvv -p 1883
 
 อธิบายว่าทำไมถึงลองใช้คำสั่งนี้
 
 -sV	ตรวจสอบว่าเปิดบริการอะไร และใช้เวอร์ชันอะไร (Service/version detection)
 
 -sC	ใช้สคริปต์ NSE พื้นฐานที่มากับ Nmap เพื่อตรวจหาข้อมูลเพิ่มเติม (เหมือน --script=default)
 
 -vvv	แสดงผลแบบ verbose มากที่สุด (เห็นรายละเอียดการทำงานแบบละเอียดสุด ๆ)
 
 -p 1883	เจาะจงสแกนเฉพาะพอร์ต 1883 (พอร์ต MQTT)


What is it used for?
This command is used to deeply investigate the MQTT service on port 1883, such as:

  Whether the port is actually open

  Which broker is running

  If there are any banners or service information revealed

  Sometimes it may expose configuration details or credentials if the broker is publicly accessible

 ใช้ทำอะไร?
คำสั่งนี้ใช้ เจาะลึกข้อมูลของบริการ MQTT บนพอร์ต 1883 ว่า:

  เปิดจริงไหม?

  ใช้ broker อะไร?

  มีข้อมูล banner หรือ service ใดโผล่ไหม?

  บางครั้งอาจเผย config หรือ credential หากเปิด public



The result/output is...
ผลลัพธ์



![Image](https://github.com/user-attachments/assets/dbb326aa-9ef8-41f1-b882-545d5975b214)






![Image](https://github.com/user-attachments/assets/1e140ec2-5b88-4d9a-ba78-2178938a785b)






![Image](https://github.com/user-attachments/assets/96c27f7f-f042-409f-861b-5a463e7d21b8)




------------------------------------------------------------------------------------------------------------------------

Summary of findings:

  patio/lights — Shows {"color":"ORANGE", "status":"ON"} (the lights are on with orange color)

  kitchen/toaster — Indicates the toaster is on, temperature at 144°C

  livingroom/speaker — Gain is set to 74

  frontdeck/camera — Contains camera position data (x, y, zoom, movement)

  $SYS/... — These are system topics reporting load, connected clients, message counts, etc.

อธิบายสิ่งที่เจอ คร่าวๆ
     
patio/lights	{"color":"ORANGE", "status":"ON"} 

kitchen/toaster	กำลังเปิดเครื่อง, อุณหภูมิ 144°C

livingroom/speaker	ปรับ gain = 74

frontdeck/camera	มีข้อมูลตำแหน่งกล้อง (x, y, zoom, movement)

$SYS/...	เป็น system topics ที่รายงาน load, client, message count

------------------------------------------------------------------------------------------------------------------------

Security points of interest:

  🔓 The broker is unsecured! Anyone can subscribe to the topics freely.

  🕵️ IoT device data inside the home/building is exposed in plain text (unencrypted MQTT).

  💡 By writing an MQTT client script (for example, using mosquitto_sub), anyone can immediately subscribe to these topics.

จุดที่น่าสนใจ (ด้านความปลอดภัย):

  🔓 Broker ไม่ได้ล็อก! ใครก็ subscribe topic ได้เลย

  🕵️ ข้อมูลอุปกรณ์ IoT ภายในบ้าน/อาคาร หลุดออกมาโดยไม่เข้ารหัส (plain MQTT)

  💡 ถ้าเขียนสคริปต์ MQTT client (เช่นใช้ mosquitto_sub) ก็สามารถ subscribe topic พวกนี้ได้ทันที

------------------------------------------------------------------------------------------------------------------------

search mqtt hacktricks
ค้นหา mqtt hacktricks



![Image](https://github.com/user-attachments/assets/a171b56a-7c7a-4a6d-bbc4-f7d6b089332c)





![Image](https://github.com/user-attachments/assets/7a6bae57-5f9d-4ebd-b1ce-c03f944e46b7)




Try going to Pentesting IoT.
ลองไปที่ตัว Pentesting IoT




![Image](https://github.com/user-attachments/assets/8b0fbb43-f365-4c8f-ab23-10eb2a5e987a)




I found some interesting explanations and useful commands — let’s try them out!
เจอคำอธิบายที่น่าสนใจเเละคำสั่งที่น่าสนใจไปลองกัน




------------------------------------------------------------------------------------------------------------------------

Try :mosquitto_sub -t "#" -h 10.10.246.79
ลอง :mosquitto_sub -t "#" -h 10.10.246.79

------------------------------------------------------------------------------------------------------------------------

mosquitto_sub is a command from the Mosquitto client used to subscribe (receive data).

  -t "#" tells it to subscribe to all topics (because # is a wildcard).

  -h 10.10.246.79 points to the IP address of the MQTT broker (the target server).
  
mosquitto_sub	คือคำสั่งจาก Mosquitto client ที่ใช้ subscribe (รับข้อมูล)
 
  -t "#" 	บอกว่าให้ subscribe ทุก topic (เพราะ # เป็น wildcard)
  
  -h 10.10.246.79	ชี้ไปยัง IP ที่เป็น MQTT broker (ปลายทาง)
  
------------------------------------------------------------------------------------------------------------------------


![Image](https://github.com/user-attachments/assets/0d0dc36e-87b6-4f5d-91df-937ec25a982c)


















 


 
















