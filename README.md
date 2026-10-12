Mini Project #2 — ระบบจัดการสมาชิก (Account Workshop)
เว็บแอปพลิเคชันสำหรับจัดการข้อมูลสมาชิก (User Management System) พัฒนาด้วย Node.js + Express.js สำหรับ Backend, HTML + JavaScript สำหรับ Frontend และ MongoDB สำหรับจัดเก็บข้อมูลสมาชิก
ระบบสามารถสมัครสมาชิก เข้าสู่ระบบ แสดงรายชื่อสมาชิก และลบข้อมูลสมาชิกได้ โดยใช้ REST API ในการรับส่งข้อมูลระหว่าง Frontend และ Backend พร้อมจัดเก็บรหัสผ่านในรูปแบบ bcrypt hash เพื่อไม่ให้บันทึกรหัสผ่านเป็นข้อความธรรมดา
1. ความสามารถของระบบ
Register: สมัครสมาชิกใหม่ โดยกรอก Username, ชื่อ, Email และ Password
Login: เข้าสู่ระบบด้วย Username และ Password
View Users: แสดงรายชื่อสมาชิกที่จัดเก็บใน MongoDB
Delete User: ลบข้อมูลสมาชิกตาม Username
Password Hashing: แฮชรหัสผ่านด้วย bcryptjs ก่อนจัดเก็บลงฐานข้อมูล
Login Tracking: บันทึกจำนวนครั้งที่เข้าสู่ระบบและเวลาที่เข้าสู่ระบบล่าสุด
2. เทคโนโลยีที่ใช้
ส่วนของระบบ
เทคโนโลยี
Frontend
HTML, JavaScript
Backend
Node.js, Express.js
Database
MongoDB
Database Driver
mongodb
Password Hashing
bcryptjs
API Communication
Fetch API
Version Control
Git, GitHub

3. โครงสร้างโปรเจกต์
mini-project-2/
├── README.md
└── account-workshop/
    ├── server/
    │   └── app.js
    ├── public/
    │   ├── index.html
    │   └── script.js
    ├── screenshots/
    │   ├── debug-1.png
    │   └── debug-2.png
    ├── .env.example
    ├── .gitignore
    ├── package.json
    └── package-lock.json
คำอธิบายไฟล์
server/app.js — จัดการ Backend, REST API และการเชื่อมต่อ MongoDB
public/index.html — หน้าเว็บไซต์สำหรับจัดการสมาชิก
public/script.js — รับส่งข้อมูลกับ Backend ผ่าน Fetch API
screenshots/ — เก็บภาพหลักฐานการดีบักระบบ
package.json — กำหนด Dependencies และคำสั่งรันโปรเจกต์
.env.example — ตัวอย่างค่าตัวแปรสภาพแวดล้อม
4. วิธีติดตั้งและใช้งาน
4.1 โปรแกรมที่จำเป็น
Node.js เวอร์ชันที่รองรับ MongoDB Driver 7 (แนะนำ Node.js 22 LTS หรือใหม่กว่า)
MongoDB Community Server
เว็บเบราว์เซอร์ เช่น Google Chrome หรือ Microsoft Edge
4.2 ดาวน์โหลดโปรเจกต์
git clone https://github.com/Yin-Yew/mini-project-2.git
เข้าไปยังโฟลเดอร์ที่มีไฟล์ package.json
cd mini-project-2/account-workshop
4.3 ติดตั้ง Dependencies
npm install
4.4 เริ่มต้น MongoDB
ตรวจสอบว่า MongoDB Server ทำงานอยู่ โดยใช้ Connection URL เริ่มต้น:
mongodb://127.0.0.1:27017
หากใช้ MongoDB Server อื่น สามารถกำหนดตัวแปรสภาพแวดล้อม MONGO_URL ให้ตรงกับเซิร์ฟเวอร์ที่ใช้งาน
4.5 เริ่มต้น Backend Server
npm run dev
เมื่อระบบเริ่มทำงานสำเร็จ Terminal จะแสดงข้อความ:
เชื่อมต่อ MongoDB สำเร็จ
Server running at http://localhost:3000
หากไม่ต้องการใช้ Watch Mode สามารถรันด้วยคำสั่ง:
npm start
4.6 เปิดเว็บไซต์
เปิดเว็บเบราว์เซอร์แล้วเข้าไปที่:
http://localhost:3000
จากนั้นสามารถทดลองสมัครสมาชิก เข้าสู่ระบบ แสดงรายชื่อสมาชิก และลบสมาชิกได้ผ่านหน้าเว็บไซต์
5. API Endpoints
Method
Endpoint
คำอธิบาย
POST
/api/register
สมัครสมาชิกใหม่
POST
/api/login
ตรวจสอบชื่อผู้ใช้และรหัสผ่าน
GET
/api/users
ดึงรายชื่อสมาชิก โดยไม่ส่ง passwordHash
DELETE
/api/users/:username
ลบสมาชิกตาม Username

5.1 สมัครสมาชิก (Register)
Endpoint: POST /api/register
ตัวอย่าง Request Body:
{
  "username": "student01",
  "name": "Student One",
  "email": "student01@example.com",
  "password": "examplepass123"
}
Backend จะตรวจสอบข้อมูลก่อนบันทึก โดย Username ต้องประกอบด้วยตัวอักษรภาษาอังกฤษตัวพิมพ์เล็ก ตัวเลข หรือ _ ความยาว 3–20 ตัวอักษร และ Password ต้องมีอย่างน้อย 8 ตัวอักษร
เมื่อสมัครสมาชิกสำเร็จ ระบบจะบันทึกข้อมูลลง MongoDB และตอบกลับด้วย HTTP Status 201 Created
5.2 เข้าสู่ระบบ (Login)
Endpoint: POST /api/login
ตัวอย่าง Request Body:
{
  "username": "student01",
  "password": "examplepass123"
}
Backend จะค้นหาสมาชิกในฐานข้อมูลและตรวจสอบรหัสผ่านด้วย bcryptjs
เมื่อเข้าสู่ระบบสำเร็จ ระบบจะเพิ่มจำนวนครั้งที่ Login บันทึกเวลาล่าสุด และส่งข้อความยืนยันกลับไปยัง Frontend
5.3 แสดงรายชื่อสมาชิก (View Users)
Endpoint: GET /api/users
ใช้ดึงข้อมูลสมาชิกจาก MongoDB โดยเรียงตามวันที่สมัครจากใหม่ไปเก่า และไม่ส่งข้อมูล passwordHash กลับไปยัง Frontend
5.4 ลบสมาชิก (Delete User)
Endpoint: DELETE /api/users/:username
ตัวอย่าง:
DELETE /api/users/student01
เมื่อพบและลบสมาชิกสำเร็จ Backend จะตอบกลับด้วย HTTP Status 204 No Content และหากไม่พบสมาชิกจะตอบกลับด้วย 404 Not Found
6. การเชื่อมต่อฐานข้อมูล
ระบบใช้ MongoDB ในการจัดเก็บข้อมูลสมาชิก
รายการ
ค่า
Database
account-db
Collection
users
Default Connection
mongodb://127.0.0.1:27017

ข้อมูลสมาชิกประกอบด้วย Username, Name, Email, Password Hash, Role, วันที่สมัครสมาชิก และข้อมูลการเข้าสู่ระบบ
Backend กำหนด Unique Index สำหรับ Username และ Email เพื่อป้องกันการสมัครสมาชิกด้วยข้อมูลซ้ำ
7. หลักฐานการดีบัก (Debugging Screenshots)
ภาพที่ 1 — การเชื่อมต่อ Backend และ MongoDB
ภาพแสดงการรันโปรเจกต์ผ่าน Terminal ด้วยคำสั่ง npm run dev โดยสามารถเชื่อมต่อ MongoDB และเริ่มต้น Express Server ได้สำเร็จ
ภาพที่ 2 — การทดสอบ Frontend และ API
ภาพแสดงการทดสอบเข้าสู่ระบบผ่านหน้าเว็บไซต์ และตรวจสอบ HTTP Request ผ่านแท็บ Network ของ Browser Developer Tools
โดยคำขอ POST /api/login ได้รับสถานะ 200 OK ซึ่งแสดงว่า API ตอบกลับสำเร็จ
8. ผลการทดสอบระบบ
จากการทดลองใช้งานระบบ พบว่า:
สามารถเริ่มต้น Backend Server ได้สำเร็จ
สามารถเชื่อมต่อ MongoDB ได้
สามารถเปิดเว็บไซต์ผ่าน http://localhost:3000 ได้
สามารถสมัครสมาชิกใหม่และบันทึกข้อมูลลงฐานข้อมูลได้
สามารถเข้าสู่ระบบด้วยบัญชีที่สมัครไว้ได้
สามารถแสดงรายชื่อสมาชิกบนหน้าเว็บไซต์ได้
API สำหรับเข้าสู่ระบบตอบกลับด้วย HTTP Status 200 OK
9. ข้อจำกัดและข้อควรระวัง
ข้อจำกัดสำคัญ: โปรเจกต์นี้เป็นเวิร์กช็อปสำหรับการศึกษาและทดลองใช้งานบนเครื่องเท่านั้น ยังไม่เหมาะสำหรับการนำไปใช้งานจริงบนเซิร์ฟเวอร์สาธารณะ
API GET /api/users และ DELETE /api/users/:username ยังไม่มีการตรวจสอบสิทธิ์ผู้ใช้งาน
ระบบ Login ยังไม่มีการสร้าง Session หรือ Authentication Token
ไม่ควรใช้ข้อมูลส่วนบุคคลจริงโดยไม่มีมาตรการรักษาความปลอดภัยเพิ่มเติม
ข้อมูล MongoDB ถูกจัดเก็บภายในเครื่องและไม่ได้อัปโหลดไปพร้อมกับ GitHub Repository
ไม่ควร Commit ไฟล์ .env รหัสผ่าน หรือโฟลเดอร์ node_modules
10. สรุป
Mini Project #2 — Account Workshop เป็นเว็บแอปพลิเคชันตัวอย่างที่แสดงการทำงานร่วมกันของ Frontend, Backend และ Database โดยใช้ Node.js, Express.js และ MongoDB
การพัฒนาโปรเจกต์นี้ช่วยให้เข้าใจการสร้าง REST API การจัดเก็บข้อมูลสมาชิก การตรวจสอบรหัสผ่านด้วย bcryptjs การรับส่งข้อมูลด้วย Fetch API และการดีบักระบบผ่าน Terminal และ Browser Developer Tools

GitHub Repository: https://github.com/Yin-Yew/mini-project-2
