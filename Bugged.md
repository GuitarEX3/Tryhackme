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




Summary of findings from MQTT on port 1883 (IP: 10.10.246.79):

After subscribing to the MQTT broker, you found that:

   Multiple IoT devices are sending data without authentication.

   The data includes:

   🔥 Toaster: reports temperature, usage status, and toasting time
   💡 RGB lights: color and ON/OFF status
   🌡️ Temperature sensors: located in various parts of the house
   📷 CCTV cameras: camera position, zoom level, and movement
   🔊 Speakers: volume gain level
   📦 Base64-encoded messages: possibly configs or tokens hiding sensitive info

Summary: The MQTT system exposes a lot of internal information, which is a significant security risk. This exposure could be exploited to control devices or launch further attacks.

Also, some data was found encoded in base64, such as this string:
eyJpZCI6ImNkZDFiMWMwLTFjNDAtNGIwZi04ZTIyLTYxYjM1NzU0OGI3ZCIsInJlZ2lzdGVyZWRfY29tbWFuZHMiOlsiSEVMUCIsIkNNRCIsIlNZUyJdLCJwdWJfdG9waWMiOiJVNHZ5cU5sUXRmLzB2b3ptYVGNkNIZy9wdWIiLCJzdWJfdG9waWMiOiJYRDJyZlI5QmV6L0dxTXBSU0VvYmgvVHZMUWVoTWcwRS9zdWIifQ==


สรุปสิ่งที่เจอจาก MQTT บนพอร์ต 1883 (IP: 10.10.246.79)
คุณทำการ subscribe MQTT แล้วพบว่า:
   มี หลายอุปกรณ์ IoT ส่งข้อมูลแบบ ไม่มี authentication
    ข้อมูลที่ได้รวมถึง:

   🔥 เครื่องปิ้งขนมปัง: รายงานอุณหภูมิ, สถานะการใช้งาน, เวลาในการปิ้ง

   💡 ไฟ RGB: สีและสถานะ (ON/OFF)

   🌡️ เซนเซอร์อุณหภูมิ: หลายจุดในบ้าน

   📷 กล้องวงจรปิด: ตำแหน่งกล้อง, การซูม, การเคลื่อนไหว

   🔊 ลำโพง: ระดับเสียง (gain)

   📦 ข้อความ base64: อาจเป็น config/token ที่ซ่อนข้อมูลสำคัญ

สรุป: ระบบ MQTT เปิดเผยข้อมูลภายในจำนวนมาก ซึ่งเป็นจุดเสี่ยงด้านความปลอดภัย และอาจนำไปใช้ในการควบคุมอุปกรณ์หรือโจมตีต่อได้

เเละ! เจอบางอย่างถูกเข้ารหัสด้วย base64 
eyJpZCI6ImNkZDFiMWMwLTFjNDAtNGIwZi04ZTIyLTYxYjM1NzU0OGI3ZCIsInJlZ2lzdGVyZWRfY29tbWFuZHMiOlsiSEVMUCIsIkNNRCIsIlNZUyJdLCJwdWJfdG9waWMiOiJVNHZ5cU5sUXRmLzB2b3ptYVGNkNIZy9wdWIiLCJzdWJfdG9waWMiOiJYRDJyZlI5QmV6L0dxTXBSU0VvYmgvVHZMUWVoTWcwRS9zdWIifQ==


Try
echo "eyJpZCI6ImNkZDFiMWMwLTFjNDAtNGIwZi04ZTIyLTYxYjM1NzU0OGI3ZCIsInJlZ2lzdGVyZWRfY29tbWFuZHMiOlsiSEVMUCIsIkNNRCIsIlNZUyJdLCJwdWJfdG9waWMiOiJVNHZ5cU5sUXRmLzB2b3ptYVp5TFQvMTVIOVRGNkNIZy9wdWIiLCJzdWJfdG9waWMiOiJYRDJyZlI5QmV6L0dxTXBSU0VvYmgvVHZMUWVoTWcwRS9zdWIifQ=" | base64 -d

ลอง
echo "eyJpZCI6ImNkZDFiMWMwLTFjNDAtNGIwZi04ZTIyLTYxYjM1NzU0OGI3ZCIsInJlZ2lzdGVyZWRfY29tbWFuZHMiOlsiSEVMUCIsIkNNRCIsIlNZUyJdLCJwdWJfdG9waWMiOiJVNHZ5cU5sUXRmLzB2b3ptYVp5TFQvMTVIOVRGNkNIZy9wdWIiLCJzdWJfdG9waWMiOiJYRDJyZlI5QmV6L0dxTXBSU0VvYmgvVHZMUWVoTWcwRS9zdWIifQ=" | base64 -d



![Image](https://github.com/user-attachments/assets/c7c32335-799b-4351-8917-2e01ebb45d61)



The output:
{"id":"cdd1b1c0-1c40-4b0f-8e22-61b357548b7d","registered_commands":["HELP","CMD","SYS"],"pub_topic":"U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub","sub_topic":"XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub"}

   "id": Unique identifier of the device in the system (UUID)

   "registered_commands": Commands that this device supports, such as HELP, CMD, SYS

   "pub_topic": The topic this device uses to publish data

   "sub_topic": The topic this device subscribes to (waiting for commands)

With commands like these, there is a possibility of command injection through MQTT.


สิ่งที่ออกมา
{"id":"cdd1b1c0-1c40-4b0f-8e22-61b357548b7d","registered_commands":["HELP","CMD","SYS"],"pub_topic":"U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub","sub_topic":"XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub"}

"id"	รหัสเฉพาะของอุปกรณ์ในระบบ (UUID)

"registered_commands"	คำสั่งที่อุปกรณ์นี้รองรับ เช่น HELP, CMD, SYS

"pub_topic"	Topic ที่อุปกรณ์นี้ใช้ "ส่ง" ข้อมูล

"sub_topic"	Topic ที่อุปกรณ์นี้ "รับ" ข้อมูล (รอฟังคำสั่ง)

มีคอมมานเเบบนี้อาจจะเป็น command injection ผ่าน MQTT



If we look closely:

   "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub" is the topic that the device uses to publish data.

   mosquitto_sub is the command used to subscribe or listen for messages from an MQTT broker.

   -t "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub" specifies the topic to listen to — in this case, the topic named
    "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub".

   -h 10.10.246.79 tells the client the host or IP address of the MQTT broker to connect to (the server providing MQTT service).

This command connects to the MQTT broker at IP 10.10.246.79 and listens for all messages sent to the topic "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub".

You can subscribe to see what data the device is sending by using this command:
sub_topic is the channel where the device receives incoming data.

Try: 
mosquitto_sub -t "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub" -h 10.10.246.79
After running it, if the terminal shows nothing, it probably means it’s waiting to receive data.



เราสังเกตุดูดีๆ 

   "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub" เป็น topic ที่อุปกรณ์ใช้ ส่งข้อมูลออก
    
   mosquitto_sub
    คือคำสั่งที่ใช้สำหรับ subscribe หรือ รอฟังข้อความ จาก MQTT broker
     
   -t "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub"
     คือการระบุ topic ที่จะรอฟังข้อมูลอยู่ — ในที่นี้คือ topic ที่ชื่อ
    "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub"
     
   -h 10.10.246.79
    คือการบอก host หรือ IP address ของ MQTT broker ที่จะเชื่อมต่อไป (เครื่องที่ให้บริการ MQTT)
    
   คำสั่งนี้คือการเชื่อมต่อไปยัง MQTT broker ที่อยู่ที่ IP 10.10.246.79 และ รอฟังข้อความทุกอย่างที่ถูกส่งมาที่    topic "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub"
      

   คุณสามารถ subscribe ดูว่าอุปกรณ์นี้กำลังส่งข้อมูลอะไร โดยใช้คำสั่งนี้:
    sub_topic = ช่องทางที่อุปกรณ์ รับข้อมูลเข้า
    
   we try
    mosquitto_sub -t "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub" -h 10.10.246.79
    
   หลังจากรันไป้เเล้วมันโล่งนั้นอาจหมายถึงรอรับข้อมูล




![Image](https://github.com/user-attachments/assets/2c79eca6-1cec-46e8-a281-3eb8a29d0095)





Try this command:
mosquitto_pub -t "XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub" -m "test_message" -h 10.10.246.79
    mosquitto_pub is the command used to publish or send a message to an MQTT broker.

   -t "XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub" specifies the topic to send the message to — in this case, the topic that the device subscribes to (from the previous information).

   -m "test_message" is the message to send — here, it is "test_message".

   -h 10.10.246.79 is the IP address of the MQTT broker you are connecting to and sending the message.

This command sends the message "test_message" to the device that is listening on the topic XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub on the MQTT broker at IP 10.10.246.79.

If the device is listening


try:
    mosquitto_pub -t "XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub" -m "test_message"    -h 10.10.246.79
    
   mosquitto_pub
คือคำสั่งที่ใช้สำหรับ publish หรือ ส่งข้อความ ไปยัง MQTT broker

-t "XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub"
ระบุ topic ที่จะส่งข้อความไป — ในกรณีนี้คือ topic ที่อุปกรณ์ subscribe อยู่ (จากข้อมูลก่อนหน้า)

-m "test_message"
คือ ข้อความที่ต้องการส่ง — ในที่นี้คือ "test_message"

-h 10.10.246.79
คือ IP ของ MQTT broker ที่คุณจะเชื่อมต่อและส่งข้อความไป

คำสั่งนี้ใช้เพื่อ ส่งข้อความ “test_message” ไปยังอุปกรณ์ที่กำลังฟังอยู่บน topic XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub บน MQTT broker ที่อยู่ที่ IP 10.10.246.79

ถ้าอุปกรณ์นั้นฟังอยู่




![Image](https://github.com/user-attachments/assets/ff811d4e-cca7-4229-936a-5853c2e58366)







![Image](https://github.com/user-attachments/assets/86e4cbe2-a482-473a-a2d9-8e9f05cc0f69)






After running the command, you may notice that something gets sent back, and the data is encoded in Base64.

หลังจากรันจะสังเกตุได้ว่ามีบางอย่างส่งมาเเละเข้ารหัสbase64 





![Image](https://github.com/user-attachments/assets/8b9ec265-4486-4dd4-8701-7f5925e4e7e7)






After decoding, we see the following:

Invalid message format.
Format:
base64({"id": "<backdoor id>", "cmd": "<command>", "arg": "<argument>"})

This indicates something interesting.

Let's go to base64encode.org and try modifying the values of <backdoor id>, <command>, and <argument>.

We'll use the previously extracted ID:
cdd1b1c0-1c40-4b0f-8e22-61b357548b7d

Then, we’ll try sending it.


หลังจากถอดรหัส
Invalid message format.
Format: base64({"id": "<backdoor id>", "cmd": "<command>", "arg": "<argument>"}) 

จะเห็นได้ว่ามีบางอย่างที่น่าสนใจ
ไปที่ base64encode.org เเล้วลองเปลี่ยน <backdoor id>,<command>,<argument>

จะลองใช้ไอดีของตัวก่อนหน้านี้ที่เเครกออกมา
cdd1b1c0-1c40-4b0f-8e22-61b357548b7d

เเล้วลองส่ง



![Image](https://github.com/user-attachments/assets/a4b11e03-13ea-4da2-802e-24aaeba534f9)






![Image](https://github.com/user-attachments/assets/73471cf3-7c36-46a6-a05f-6a098d9409bf)






We can see that it responds based on the values we changed, so after that, we can modify it to retrieve the flag directly.

จะเห็นได้ว่ามันทำงานตามค่าที่เปลี่ยนหลังจากนั้นเราก็เปลี่ยนเป็น เอาธงได้เลย





![Image](https://github.com/user-attachments/assets/76db0977-44d0-4be7-be46-c85bd3563f21)





![Image](https://github.com/user-attachments/assets/a5edbb19-db18-4786-b257-f7a2f219cd6d)





complete! ✅
เเล้วก็เสร็จเป็นที่เรียบร้อย


🛡️ Vulnerability Summary:

   Lack of access control on the MQTT broker
   Anyone can connect, subscribe, and publish without authentication.

   A backend system automatically executes commands received via MQTT
   This leads to a command injection over MQTT vulnerability.

   The command format is base64(JSON)
   The pattern is predictable and can be exploited systematically.


สรุปช่องโหว่:

   การไม่มีการป้องกันการเข้าถึง MQTT broker

   มีระบบ backend ที่รันคำสั่งจากข้อมูลที่มาทาง MQTT อย่างอัตโนมัติ (command injection over MQTT)

   รูปแบบคำสั่งเป็น base64(JSON) → มี pattern ที่ชัดเจนให้ exploit ต่อได้









 


 
















