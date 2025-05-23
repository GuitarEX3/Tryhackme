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
