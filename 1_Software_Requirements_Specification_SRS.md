# Software Requirements Specification (SRS)

**Project Name:** Meeting Room Booking System (ระบบจองห้องประชุมส่วนกลาง)  
**Date:** [ระบุวันที่]  
**Version:** 1.0  

## TABLE OF CONTENTS
1. INTRODUCTION
   1.1 SOFTWARE PURPOSE
   1.2 SOFTWARE SCOPE
   1.3 DEFINITIONS, ACRONYMS, AND ABBREVIATIONS
   1.4 KEY REFERENCES
   1.5 SOFTWARE OVERVIEW
2. OVERALL DESCRIPTION
   2.1 PRODUCT FUNCTIONS
   2.2 ENVIRONMENTAL CONDITIONS
   2.3 USER CHARACTERISTIC
   2.4 EXTERNAL INTERFACES
   2.5 CONSTRAINTS
   2.6 ASSUMPTIONS AND DEPENDENCIES
3. REQUIREMENTS MANAGEMENT
   3.1 RESOURCES AND FUNDING
   3.2 REPORTING PROCEDURES
   3.3 TRAINING
4. SPECIFIC REQUIREMENTS
   4.1 FUNCTIONAL REQUIREMENTS
   4.2 PERFORMANCE REQUIREMENTS
   4.3 EXTERNAL INTERFACE REQUIREMENTS
   4.4 OTHER REQUIREMENTS
5. ACCEPTANCE
6. DOCUMENTATION
7. MAINTENANCE

---

## 1. INTRODUCTION
เอกสารข้อกำหนดความต้องการซอฟต์แวร์ (Software Requirement Specification: SRS) นี้ เป็นเอกสารข้อกำหนดความต้องการสำหรับอธิบายรายละเอียดและข้อกำหนดทั้งหมดของงานพัฒนา **Meeting Room Booking System** โดยมีเป้าหมายที่จะใช้เอกสารนี้เป็นหลักอ้างอิงให้แก่ผู้พัฒนาโปรแกรมและบุคคลที่เกี่ยวข้อง

### 1.1 SOFTWARE PURPOSE
**Meeting Room Booking System** เป็นระบบที่ออกแบบและพัฒนาเพื่อให้พนักงานและแอดมินสามารถใช้เป็นเครื่องมือจัดการและติดตามการจองห้องประชุมส่วนกลาง โดยมีจุดประสงค์หลักเพื่อ:
1. เป็นศูนย์กลางข้อมูลชุดเดียว (Single Source of Truth) เพื่อแก้ปัญหาข้อมูลไม่อัปเดต (Data Out of Sync)
2. ป้องกันไม่ให้มีการจองเวลาซ้อนทับกัน (Double-Booking Prevention)
3. ให้พนักงานสามารถส่งคำขอจองห้องประชุมได้สะดวกและตรวจสอบสถานะได้แบบ Real-time
4. ให้แอดมินสามารถตรวจสอบคิว อนุมัติ หรือปฏิเสธคำขอจองพร้อมระบุเหตุผลได้ผ่านระบบเดียว
5. ลดความผิดพลาดของการจดจำข้อมูลและการทำงานแบบแมนนวลผ่าน Excel/LINE/Email

### 1.2 SOFTWARE SCOPE
ระบบนี้จะช่วยให้พนักงานสามารถส่งคำขอจองห้องและเห็นสถานะที่แท้จริงของห้องประชุมได้ทันที ในขณะที่แอดมินจะใช้ระบบในการจัดการคิวและตัดสินใจอนุมัติ โดยระบบจะบังคับใช้กฎธุรกิจ (Business Rule) ว่าผู้ใช้จะได้สิทธิ์เข้าห้องก็ต่อเมื่อสถานะเป็น "Approved" เท่านั้น เพื่อลดปัญหาพาลูกค้ามาชนกันหน้าห้อง

### 1.3 DEFINITIONS, ACRONYMS, AND ABBREVIATIONS
- **SRS:** Software Requirements Specification
- **Single Source of Truth:** ฐานข้อมูลกลางระบบเดียวในการบันทึกและแสดงสถานะ
- **Requester:** ผู้ใช้งานทั่วไป/พนักงานที่มีความประสงค์ขอจองห้องประชุม
- **Admin:** เจ้าหน้าที่ผู้มีสิทธิ์ตรวจสอบความถูกต้องและอนุมัติ/ปฏิเสธคำขอจอง
- **Double-booked:** อาการคิวซ้อนทับกันเนื่องจากข้อมูลไม่อัปเดต
- **Audit Trail:** การบันทึกประวัติการเปลี่ยนแปลงข้อมูลในระบบ (ใครทำอะไร เวลาไหน)

### 1.4 KEY REFERENCES
- ข้อกำหนดและ Use Case จากกระบวนการ Quality Clinic (อ้างอิงเอกสาร P05-P07)

### 1.5 SOFTWARE OVERVIEW
Meeting Room Booking System ประกอบไปด้วย 2 ส่วนหลัก คือ:
1. **Requester Module:** สำหรับให้พนักงานดูตารางปฏิทินที่ว่าง ส่งคำขอจอง และกดยกเลิกคำขอจองของตนเอง
2. **Admin Module:** สำหรับเจ้าหน้าที่ตรวจสอบคิว อนุมัติ/ปฏิเสธคำขอจอง พร้อมเก็บบันทึกประวัติการทำงาน (Audit Trail)

---

## 2. OVERALL DESCRIPTION

### 2.1 PRODUCT FUNCTIONS
1. **Booking System (ระบบจองห้อง):**
   - แสดงตารางปฏิทินกลางแบบ Real-time
   - ส่งคำขอจองพร้อมระบุข้อมูลกิจกรรม
   - ระบบตรวจสอบและล็อกช่วงเวลาเพื่อป้องกันคิวซ้อนทับ
2. **Approval System (ระบบอนุมัติ):**
   - แอดมินสามารถดูรายการคำขอทั้งหมด (Received)
   - อนุมัติ (Approved) หรือปฏิเสธ (Rejected) พร้อมระบุเหตุผล
3. **Status & History Tracking (ระบบสถานะและประวัติ):**
   - แสดงสถานะ Received, Pending Approval, Approved, Rejected, Cancelled
   - บันทึก Audit Trail ทุกการเปลี่ยนสถานะ

### 2.2 ENVIRONMENTAL CONDITIONS
- **Platform:** Web-based Application
- **Server:** Cloud Hosting หรือ On-Premise Server ขององค์กร
- **Client:** Web Browser มาตรฐาน (Chrome, Edge, Safari) รองรับการใช้งานผ่าน PC และ Mobile

### 2.3 USER CHARACTERISTIC
- **ผู้ขอจอง (Requester):** พนักงานในองค์กรที่ต้องการใช้ห้องประชุม มีทักษะการใช้งาน Web Application พื้นฐาน
- **เจ้าหน้าที่แอดมิน (Admin):** ผู้ดูแลการจัดสรรห้องประชุม ต้องการระบบที่ทำงานรวดเร็วและตรวจสอบข้อมูลย้อนหลังได้

### 2.4 EXTERNAL INTERFACES
- ระบบทำงานแบบ Standalone (ไม่เชื่อมต่อระบบภายนอกอย่าง LINE หรือ Email ในเวอร์ชันแรกตามที่ระบุในข้อกำหนด)

### 2.5 CONSTRAINTS
- การใช้งานและพิจารณาคิวต้องทำผ่านหน้าระบบกลางเท่านั้น ห้ามใช้ช่องทางแมนนวล
- ข้อจำกัดห้ามพึ่งพาระบบ Notification ภายนอก (ตามการตัด Design Bias)

### 2.6 ASSUMPTIONS AND DEPENDENCIES
- ผู้ใช้งานทุกคนต้องปฏิบัติตาม Business Rule: "การได้สิทธิ์เข้าใช้ห้องประชุมต้องเกิดจากสถานะ Approved เท่านั้น"

---

## 3. REQUIREMENTS MANAGEMENT

### 3.1 RESOURCES AND FUNDING
**Hardware Requirements:**
- Server: CPU 4 Cores, RAM 8GB ขึ้นไป
- Client: คอมพิวเตอร์หรือสมาร์ทโฟนทั่วไปที่เชื่อมต่ออินเทอร์เน็ตได้

### 3.2 REPORTING PROCEDURES
- รายงานประวัติการจองและ Audit Trail รายเดือนสำหรับแอดมิน

### 3.3 TRAINING
- จัดทำคู่มือออนไลน์ (User Manual) และจัดอบรมแอดมินเพื่อทำความเข้าใจ Workflow ใหม่

---

## 4. SPECIFIC REQUIREMENTS

### 4.1 FUNCTIONAL REQUIREMENTS
| ID | Requirement | Priority |
|---|---|---|
| **FR-A-01** | แสดงสถานะคำขอด้วยชุดสถานะกลางผ่านระบบส่วนกลางแบบ Real-time | Must |
| **FR-A-02** | ตรวจความขัดแย้งของทรัพยากรและเวลา เพื่อล็อกการจองซ้ำซ้อนอัตโนมัติ | Must |
| **FR-A-03** | ผู้ใช้สามารถส่งคำขอพร้อมข้อมูลขั้นต่ำของแผนงานและเวลา | Must |
| **FR-A-04** | ผู้มีสิทธิ์ (แอดมิน) สามารถอนุมัติ/ปฏิเสธพร้อมระบุเหตุผลผ่านระบบได้ | Must |
| **FR-A-05** | ดูประวัติการเปลี่ยนสถานะของคำขอจองและผู้ใช้กดยกเลิกสิทธิ์ตนเองได้ | Should |
| **BR-A-01** | การได้สิทธิ์เข้าใช้ห้องประชุมต้องเกิดจากสถานะ "Approved" เท่านั้น (Business Rule) | Must |

### 4.2 PERFORMANCE REQUIREMENTS
- **Real-time Sync:** ระบบต้องแสดงการอัปเดตสถานะล่าสุดให้ผู้ใช้งานทุกคนเห็นบนตารางปฏิทินส่วนกลางภายในกรอบ 2 วินาที (อ้างอิง: AC-A-09)

### 4.3 EXTERNAL INTERFACE REQUIREMENTS
- Web Browser User Interface ที่รองรับ Responsive Design

### 4.4 OTHER REQUIREMENTS (Non-Functional)
- **NFR-A-01:** บันทึก Audit Trail การเปลี่ยนสถานะ (ผู้กระทำ วันเวลา สถานะเดิมและใหม่) อย่างแม่นยำ

---

## 5. ACCEPTANCE
อ้างอิงเกณฑ์การยอมรับ (Acceptance Criteria) ตามที่ระบุไว้ในเอกสาร P06 (AC-A-01 ถึง AC-A-10)

## 6. DOCUMENTATION
- System Architecture Document
- User Manual (สำหรับ Requester และ Admin)
- Software Requirement Specification (SRS) - *เอกสารฉบับนี้*

## 7. MAINTENANCE
- รับประกันระบบและแก้ไขข้อบกพร่อง (Bug Fixes) ภายหลังการ Go-Live
- ดูแลระบบฐานข้อมูลเพื่อป้องกันข้อมูลสูญหาย

---

## APPENDIX A REQUIREMENTS TRACEABILITY MATRIX (RTM)

| Requirement ID | Description | Coverage (Use Case / AC) | Priority |
|---|---|---|---|
| FR-A-01 | แสดงสถานะส่วนกลาง Real-time | UC-A-01, UC-A-02, AC-A-03, AC-A-09 | H |
| FR-A-02 | ล็อกการจองซ้ำซ้อน | UC-A-01, AC-A-06 | H |
| FR-A-03 | ส่งคำขอจองข้อมูลขั้นต่ำ | UC-A-01, AC-A-01, AC-A-02 | H |
| FR-A-04 | แอดมินอนุมัติ/ปฏิเสธ | UC-A-02, AC-A-04, AC-A-07, AC-A-10 | H |
| FR-A-05 | ยกเลิกคำขอจอง | UC-A-03, AC-A-07 | M |
| BR-A-01 | สิทธิ์เกิดจาก Approved เท่านั้น | UC-A-02, AC-A-05 | H |
| NFR-A-01 | บันทึก Audit Trail | UC-A-02, AC-A-02, AC-A-08 | H |

## APPENDIX B DIAGRAMS
*(หมายเหตุ: สามารถนำรูปภาพ Use Case Diagram และ State Diagram จากเอกสาร P05-P07 มาใส่ในหมวดนี้ได้เมื่อนำไปจัดหน้าในโปรแกรม MS Word)*

## APPENDIX C GUI DESIGN
*(รอการออกแบบจากทีม UI/UX ในเฟสถัดไป)*
