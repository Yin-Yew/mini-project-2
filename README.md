

# Mini Project #2 — ระบบจัดการสมาชิก (Account Workshop)


เว็บแอปพลิเคชันสำหรับจัดการข้อมูลสมาชิก (User Management System) พัฒนาด้วย **Node.js + Express.js** สำหรับ Backend, **HTML + JavaScript** สำหรับ Frontend และ **MongoDB** สำหรับจัดเก็บข้อมูลสมาชิก


ระบบรองรับการสมัครสมาชิก เข้าสู่ระบบ แสดงรายชื่อสมาชิก และลบสมาชิก โดยเชื่อมต่อ Frontend กับ Backend ผ่าน REST API และจัดเก็บรหัสผ่านในรูปแบบ bcrypt hash


> **ข้อจำกัดสำคัญ:** โปรเจกต์นี้จัดทำขึ้นเพื่อการศึกษาและทดลองใช้งานบนเครื่องเท่านั้น API สำหรับดูรายชื่อและลบสมาชิกยังไม่มีการตรวจสอบสิทธิ์ และระบบ Login ยังไม่มี Session หรือ Token จึงไม่ควรนำไปใช้งานบนเซิร์ฟเวอร์สาธารณะโดยไม่มีการพัฒนาด้านความปลอดภัยเพิ่มเติม


## 1. ความสามารถของระบบ


- **Register:** สมัครสมาชิกใหม่ โดยกรอก Username, Name, Email และ Password
- **Login:** เข้าสู่ระบบด้วย Username และ Password
- **View Users:** แสดงรายชื่อสมาชิกจากฐานข้อมูล MongoDB
- **Delete User:** ลบข้อมูลสมาชิกตาม Username
- **Password Hashing:** แฮชรหัสผ่านด้วย bcryptjs ก่อนจัดเก็บ
- **Login Tracking:** บันทึกจำนวนครั้งและเวลาที่เข้าสู่ระบบล่าสุด


## 2. เทคโนโลยีที่ใช้


| ส่วนของระบบ | เทคโนโลยี |
| --- | --- |
| Frontend | HTML, JavaScript |
| Backend | Node.js, Express.js |
| Database | MongoDB |
| Database Driver | mongodb |
| Password Hashing | bcryptjs |
| API Communication | Fetch API |
| Version Control | Git, GitHub |


## 3. โครงสร้างโปรเจกต์


```text
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
```


## 4. วิธีติดตั้งและใช้งาน


### 4.1 โปรแกรมที่จำเป็น


- Node.js เวอร์ชันที่รองรับ MongoDB Driver 7 (แนะนำ Node.js 22 LTS หรือใหม่กว่า)
- MongoDB Community Server
- เว็บเบราว์เซอร์ เช่น Google Chrome หรือ Microsoft Edge


### 4.2 ดาวน์โหลดโปรเจกต์


```bash
git clone https://github.com/Yin-Yew/mini-project-2.git
```


เข้าไปยังโฟลเดอร์ที่มีไฟล์ `package.json`


```bash
cd mini-project-2/account-workshop
```


### 4.3 ติดตั้ง Dependencies


```bash
npm install
```


### 4.4 เริ่มต้น MongoDB


ตรวจสอบว่า MongoDB Server ทำงานอยู่ โดยใช้ Connection URL เริ่มต้น:


```text
mongodb://127.0.0.1:27017
```


หากใช้ MongoDB Server อื่น สามารถกำหนดตัวแปรสภาพแวดล้อม `MONGO_URL` ให้ตรงกับเซิร์ฟเวอร์ที่ใช้งาน


### 4.5 เริ่มต้น Backend Server


```bash
npm run dev
```


เมื่อระบบเริ่มทำงานสำเร็จ Terminal จะแสดงข้อความ:


```text
เชื่อมต่อ MongoDB สำเร็จ
Server running at http://localhost:3000
```


หากไม่ต้องการใช้ Watch Mode สามารถใช้คำสั่ง:


```bash
npm start
```


### 4.6 เปิดเว็บไซต์


เปิดเว็บเบราว์เซอร์แล้วเข้าไปที่:


http://localhost:3000


จากนั้นสามารถทดลองสมัครสมาชิก เข้าสู่ระบบ แสดงรายชื่อสมาชิก และลบสมาชิกได้ผ่านหน้าเว็บไซต์


## 5. API Endpoints


| Method | Endpoint | คำอธิบาย |
| --- | --- | --- |
| POST | `/api/register` | สมัครสมาชิกใหม่ |
| POST | `/api/login` | ตรวจสอบชื่อผู้ใช้และรหัสผ่าน |
| GET | `/api/users` | ดึงรายชื่อสมาชิก โดยไม่ส่ง `passwordHash` |
| DELETE | `/api/users/:username` | ลบสมาชิกตาม Username |


### 5.1 สมัครสมาชิก (Register)


**Endpoint:** `POST /api/register`


ตัวอย่าง Request Body:


```json
{
  "username": "student01",
  "name": "Student One",
  "email": "student01@example.com",
  "password": "examplepass123"
}
```


Backend จะตรวจสอบข้อมูลก่อนบันทึก โดย Username ต้องประกอบด้วยตัวอักษรภาษาอังกฤษตัวพิมพ์เล็ก ตัวเลข หรือ `_` ความยาว 3–20 ตัวอักษร และ Password ต้องมีอย่างน้อย 8 ตัวอักษร


เมื่อสมัครสมาชิกสำเร็จ ระบบจะบันทึกข้อมูลลง MongoDB และตอบกลับด้วย HTTP Status `201 Created`


### 5.2 เข้าสู่ระบบ (Login)


**Endpoint:** `POST /api/login`


ตัวอย่าง Request Body:


```json
{
  "username": "student01",
  "password": "examplepass123"
}
```


Backend จะค้นหาสมาชิกในฐานข้อมูลและตรวจสอบรหัสผ่านด้วย bcryptjs


เมื่อเข้าสู่ระบบสำเร็จ ระบบจะเพิ่มจำนวนครั้งที่ Login บันทึกเวลาล่าสุด และส่งข้อความยืนยันกลับไปยัง Frontend


### 5.3 แสดงรายชื่อสมาชิก (View Users)


**Endpoint:** `GET /api/users`


ใช้ดึงข้อมูลสมาชิกจาก MongoDB โดยไม่ส่งข้อมูล `passwordHash` กลับไปยัง Frontend


### 5.4 ลบสมาชิก (Delete User)


**Endpoint:** `DELETE /api/users/:username`


ตัวอย่าง:


```http
DELETE /api/users/student01
```


เมื่อพบและลบสมาชิกสำเร็จ Backend จะตอบกลับด้วย HTTP Status `204 No Content` และหากไม่พบสมาชิกจะตอบกลับด้วย `404 Not Found`


## 6. การเชื่อมต่อฐานข้อมูล


ระบบใช้ MongoDB ในการจัดเก็บข้อมูลสมาชิก


| รายการ | ค่า |
| --- | --- |
| Database | `account-db` |
| Collection | `users` |
| Default Connection | `mongodb://127.0.0.1:27017` |


ข้อมูลสมาชิกประกอบด้วย Username, Name, Email และ Password Hash


## 7. หลักฐานการดีบัก (Debugging Screenshots)


### ภาพที่ 1 — การเชื่อมต่อ Backend และ MongoDB


ภาพแสดงการรันโปรเจกต์ผ่าน Terminal ด้วยคำสั่ง `npm run dev` โดยสามารถเชื่อมต่อ MongoDB และเริ่มต้น Express Server ได้สำเร็จ


![หลักฐานการดีบักภาพที่ 1](account-workshop/screenshots/debug-1.png)


### ภาพที่ 2 — การทดสอบ Frontend และ API


ภาพแสดงการทดสอบเข้าสู่ระบบผ่านหน้าเว็บไซต์ และตรวจสอบ HTTP Request ผ่านแท็บ Network ของ Browser Developer Tools


โดยคำขอ `POST /api/login` ได้รับสถานะ **200 OK** ซึ่งแสดงว่า API ตอบกลับสำเร็จ


![หลักฐานการดีบักภาพที่ 2](account-workshop/screenshots/debug-2.png)


## 8. ผลการทดสอบระบบ


จากการทดลองใช้งานระบบ พบว่า:


- สามารถเริ่มต้น Backend Server ได้สำเร็จ
- สามารถเชื่อมต่อ MongoDB ได้
- สามารถเปิดเว็บไซต์ผ่าน `http://localhost:3000` ได้
- สามารถสมัครสมาชิกใหม่และบันทึกข้อมูลลงฐานข้อมูลได้
- สามารถเข้าสู่ระบบด้วยบัญชีที่สมัครไว้ได้
- สามารถแสดงรายชื่อสมาชิกบนหน้าเว็บไซต์ได้
- API สำหรับเข้าสู่ระบบตอบกลับด้วย HTTP Status `200 OK`


## 9. ข้อจำกัดและข้อควรระวัง


- โปรเจกต์นี้เป็นระบบตัวอย่างสำหรับการศึกษาเท่านั้น
- API `GET /api/users` และ `DELETE /api/users/:username` ยังไม่มีการตรวจสอบสิทธิ์
- ระบบ Login ยังไม่มี Session หรือ Authentication Token
- ไม่ควรนำระบบไปใช้งานบนเซิร์ฟเวอร์สาธารณะโดยไม่มีการเพิ่มระบบ Authentication และ Authorization
- ข้อมูล MongoDB อยู่ภายในเครื่องและไม่ได้ถูกอัปโหลดไปพร้อมกับ GitHub Repository
- ไม่ควร Commit ไฟล์ `.env` รหัสผ่าน หรือโฟลเดอร์ `node_modules`


## 10. สรุป


Mini Project #2 — Account Workshop เป็นเว็บแอปพลิเคชันตัวอย่างที่แสดงการทำงานร่วมกันของ Frontend, Backend และ Database โดยใช้ Node.js, Express.js และ MongoDB


การพัฒนาโปรเจกต์นี้ช่วยให้เข้าใจการสร้าง REST API การจัดเก็บข้อมูลสมาชิก การตรวจสอบรหัสผ่านด้วย bcryptjs การรับส่งข้อมูลด้วย Fetch API และการดีบักระบบผ่าน Terminal และ Browser Developer Tools


---


**GitHub Repository:** https://github.com/Yin-Yew/mini-project-2



