# Software Requirements Specification (SRS)

## Table of Contents
1. Introduction
   1.1 Purpose
   1.2 Document Conventions
   1.3 Intended Audience and Reading Suggestions
   1.4 Product Scope
   1.5 References
2. Overall Description
   2.1 Product Perspective
   2.2 Product Functions
   2.3 User Classes and Characteristics
   2.4 Operating Environment
   2.5 Design and Implementation Constraints
   2.6 User Documentation
   2.7 Assumptions and Dependencies
3. External Interface Requirements
   3.1 User Interfaces
   3.2 Hardware Interfaces
   3.3 Software Interfaces
   3.4 Communications Interfaces
4. System Features
   4.1 Feature: Centralized Calendar & Status Aggregation (M1)
   4.2 Feature: Double-Booking Prevention (M2)
   4.3 Feature: Admin Review Workflow & Audit Trail (M3, M6)
5. Other Nonfunctional Requirements
   5.1 Performance Requirements
   5.2 Safety Requirements
   5.3 Security Requirements
   5.4 Software Quality Attributes
   5.5 Business Rules
6. Other Requirements
Appendix A: Glossary
Appendix B: Analysis Models
Appendix C: To Be Determined List

---

## Revision History
| Name | Date | Reason For Changes | Version |
|---|---|---|---|
| Team A | July 19, 2026 | Initial Release: Baseline Requirements from Day 3 Workflow | 1.0 |

---

## 1. Introduction
### 1.1 Purpose
เอกสารฉบับนี้จัดทำขึ้นเพื่อกำหนดความต้องการทางซอฟต์แวร์สำหรับ ระบบจองห้องประชุมส่วนกลาง (Meeting Room Booking Subsystem) ซึ่งเป็นโมดูลย่อยภายใต้โครงการ Campus Resource Booking System (CRBS) โดยเอกสารเวอร์ชัน 1.0 นี้ มุ่งเน้นไปที่การแก้ไขปัญหาการจองห้องซ้ำซ้อน (Double-booked) และการไม่ซิงค์กันของข้อมูลตารางจองส่วนกลาง

### 1.2 Document Conventions
1. ทุกความต้องการระบุสิทธิ์เด็ดขาดโดยใช้คำว่า "ระบบต้อง (System shall...)" เพื่อผูกมัดพฤติกรรมของระบบ
2. ความต้องการในระดับฟังก์ชัน (Functional Requirements) จะได้รับลำดับความสำคัญ (Priority) สืบทอดโดยตรงจาก MoSCoW Prioritization ที่ได้ตกลงใน P05

### 1.3 Intended Audience and Reading Suggestions
1. **Developers:** ให้อ่านส่วนของ Product Functions (2.2) เพื่อเข้าใจภาพรวม และ System Features (Section 4) เพื่อนำไปพัฒนาระบบหลังบ้าน
2. **Testers / QA:** ให้เน้นอ่านส่วน System Features (Section 4) และศึกษาเงื่อนไข Given-When-Then จากตารางสรุปพฤติกรรมระบบเพื่อนำไปออกแบบ Test Case
3. **Policy Owners / Admin:** ให้โฟกัสที่หมวด Business Rules (Section 5) และ Appendix C (To Be Determined List) เพื่อตรวจทานนโยบายควบคุมคิว

### 1.4 Product Scope
ระบบนี้เป็นระบบซอฟต์แวร์ปฏิทินส่วนกลางที่ทำหน้าที่เป็น Single Source of Truth เพื่อตัดการทำงานแมนนวลผ่าน Excel และช่องทางสื่อสารภายนอก (LINE/Email)
1. **Objectives:** เพื่อให้การแสดงผล Availability ของห้องประชุมมีความโปร่งใส และสะท้อนข้อมูลผู้ใช้ทุกคนเห็นตรงกันในเวลาจริง
2. **Benefits:** ลดความเสี่ยงในการพาลูกค้าหรือผู้มีส่วนได้ส่วนเสียภายนอกเข้ามาชนกันหน้าห้องประชุม (Zero Double-Booking)

### 1.5 References
1. ENGSE206 Day 3 Workflow Contract (P05, P06, P07 Project Artifacts)
2. IEEE Std 830-1998 Recommended Practice for Software Requirements Specifications

---

## 2. Overall Description
### 2.1 Product Perspective
ระบบจองห้องประชุมส่วนกลางนี้ เป็นระบบที่พัฒนาขึ้นใหม่เพื่อเป็นระบบอิสระ (Self-contained Product) ในเฟสแรกมีข้อจำกัด (Constraint) ว่าจะต้องสามารถทำงานได้โดยไม่มีระบบ Integration ภายนอก และตัดช่องทางแมนนวลเดิมออกทั้งหมด

### 2.2 Product Functions
ฟังก์ชันการทำงานหลักที่ระบบต้องรองรับ (Core Minimum Viable Product) ประกอบด้วย
1. **Booking Submission:** การกรอกและยื่นคำขอจองห้องประชุมพร้อมเวลา
2. **Conflict Prevention:** การตรวจสอบเวลาชนและล็อกธุรกรรมซ้ำซ้อนโดยอัตโนมัติ
3. **Centralized Queue Display:** การรวมคิวและแสดงปฏิทินสถานะแบบ Real-time
4. **Admin Approval Workflow:** หน้าต่างจัดการเปลี่ยนผ่านสถานะสำหรับแอดมิน
5. **Audit Logging:** การบันทึกประวัติร่องรอยตรวจสอบ (Audit Trail)

### 2.3 User Classes and Characteristics
1. **ผู้ขอจอง (Requester):** พนักงานหรืออาจารย์ (เช่น ทีม Sales, ทีม Project) มีพฤติกรรมการจองเพื่อใช้งานห้อง ต้องการการรับรู้สถานะที่ชัดเจนเพื่อไม่ให้เกิดข้อผิดพลาดหน้าห้องประชุม
2. **เจ้าหน้าที่แอดมิน (Admin):** ผู้ดูแลระบบส่วนกลาง มีหน้าที่ใช้ดุลยพินิจและนโยบายในการเปลี่ยนผ่านสถานะคำขอ (Approve/Reject)

### 2.4 Operating Environment
**Client Platform:** เว็บแอปพลิเคชัน รองรับ Responsive Design บน Web Browser (Chrome, Safari, Edge) ทั้งบนเครื่องคอมพิวเตอร์และอุปกรณ์เคลื่อนที่

### 2.5 Design and Implementation Constraints
**CON-A-01:** ระบบรุ่นแรกทำได้เพียง 5 ความสามารถหลัก และห้ามทำการเชื่อมต่อ API กับระบบภายนอกใด ๆ รวมถึง LINE Notification และการผูก Calendar ส่วนตัวของผู้ใช้ (Outlook/Google)

### 2.6 User Documentation
ระบบจะมีเฉพาะ Online User Manual สำหรับคู่มือแอดมินและการใช้งานเบื้องต้นของผู้ขอจองส่งมอบพร้อมซอฟต์แวร์

### 2.7 Assumptions and Dependencies
1. **Assumption 01:** สมมติว่าพนักงานผู้ใช้งานทุกคนในองค์กรเข้าถึงระบบผ่านระบบยืนยันตัวตนส่วนกลางชุดเดียวกันของบริษัทได้
2. **Dependency 01:** ฟังก์ชันการล็อกเวลาชน (FR-A-02) พึ่งพาความเสถียรและการ Sync ข้อมูลของฐานข้อมูลกลางชุดเดียวกัน (FR-A-01)

---

## 3. External Interface Requirements
### 3.1 User Interfaces
1. ระบบต้องมีหน้าจอภาพปฏิทินตารางเวลา (Calendar View) แสดงสถานะช่วงเวลาของห้อง BOARD ROOM A ให้ชัดเจน
2. สถานะต้องแสดงเป็นคำสากลชุดเดียวกัน (Received, Pending Approval, Approved, Rejected, Cancelled) แยกตามแถบสีที่กำหนดใน P07

### 3.2 Hardware Interfaces
ไม่มีข้อกำหนดเฉพาะด้านฮาร์ดแวร์อินเตอร์เฟสเนื่องจากเป็น Web-based Subsystem

### 3.3 Software Interfaces
**Database Interface:** เชื่อมต่อกับฐานข้อมูลกลางระบบจองทรัพยากรส่วนกลางเพื่อทำธุรกรรมคำขอและเก็บข้อมูลประวัติย้อนหลัง (Audit Trail Log)

### 3.4 Communications Interfaces
รองรับ HTTPS Protocol มาตรฐานสำหรับการส่งข้อมูลคำขอจองระหว่างเว็บเบราว์เซอร์และเซิร์ฟเวอร์ส่วนกลาง

---

## 4. System Features

### 4.1 Feature: Centralized Calendar & Status Aggregation (M1)
**4.1.1 Description and Priority**
ระบบรวมคิวจองและแสดงผลตารางสถานะคำขอจองห้องประชุมผ่านปฏิทินกลางชุดเดียวกันแบบ Real-time
* Priority: Must Have

**4.1.2 Stimulus/Response Sequences**
* **Stimulus:** ผู้ขอจองยื่นธุรกรรมคำขอจองหรือแอดมินเปลี่ยนสิทธิ์สถานะคิว
* **Response:** ระบบดึงข้อมูลจากฐานข้อมูลกลางและอัปเดตหน้าจอภาพตารางปฏิทินส่วนกลางให้ผู้ใช้ทุกคนเห็นตรงกันในกรอบ 2 วินาที

**4.1.3 Functional Requirements**
* **FR-A-01:** ระบบต้องแสดงตารางสถานะคำขอด้วยชุดสถานะกลาง (Received, Pending Approval, Approved, Rejected, Cancelled) จากแหล่งข้อมูลชุดเดียวกันแบบ Real-time
* **FR-A-03:** ระบบต้องรองรับให้ผู้ใช้ส่งคำขอจองโดยกรอกข้อมูลขั้นต่ำที่จำเป็น (ชื่อผู้จอง, ทีม, วัตถุประสงค์, วันเวลา และห้อง)

### 4.2 Feature: Double-Booking Prevention (M2)
**4.2.1 Description and Priority**
การตรวจสอบความขัดแย้งของทรัพยากรห้องประชุมและช่วงเวลา เพื่อล็อกและสกัดการจองชนกันอัตโนมัติ
* Priority: Must Have

**4.2.2 Stimulus/Response Sequences**
* **Stimulus:** ผู้ใช้ส่งคำขอจองห้องในช่วงเวลาที่มีรายการอื่นได้รับสิทธิ์ Approved หรือกำลังรอพิจารณาอยู่ก่อนแล้ว
* **Response:** ระบบตรวจสอบฐานข้อมูลแบบ Real-time และปิดกั้น (Block) ปุ่มส่งคำขอ พร้อมแสดงข้อความปฏิเสธ

**4.2.3 Functional Requirements**
* **FR-A-02:** ระบบต้องทำหน้าที่ตรวจสอบช่วงเวลาคาบเกี่ยวกันโดยอัตโนมัติ และไม่อนุญาตให้สร้างธุรกรรมการจองทับซ้อนกับช่วงเวลาที่มีคำขอสถานะ "Approved" เด็ดขาด

### 4.3 Feature: Admin Review Workflow & Audit Trail (M3, M6)
**4.3.1 Description and Priority**
หน้าต่างสำหรับให้เจ้าหน้าที่แอดมินจัดการสิทธิ์คำขอ และระบบลงประวัติร่องรอยการเปลี่ยนแปลง
* Priority: Must Have

**4.3.2 Stimulus/Response Sequences**
* **Stimulus:** แอดมินตรวจสอบรายละเอียดแล้วเลือกคำสั่งอนุมัติ หรือปฏิเสธคำขอ
* **Response:** ระบบเปลี่ยนสิทธิ์สถานะ บันทึกร่องรอยเหตุผลประกอบ และบันทึกลง Audit log

**4.3.3 Functional Requirements**
* **FR-A-04:** ระบบต้องจัดเตรียมหน้าต่างให้แอดมินกดอนุมัติ (Approve) หรือปฏิเสธ (Reject) โดยหากกดปฏิเสธ ระบบต้องบังคับให้พิมพ์ระบุเหตุผลประกอบเสมอ
* **FR-A-05:** ระบบต้องรองรับการกดยกเลิก (Cancel) รายการจองโดยผู้ขอจองที่เป็นเจ้าของคำขอนั้น เพื่อคืนพื้นที่ห้องว่างให้แก่ส่วนกลาง

---

## 5. Other Nonfunctional Requirements
### 5.1 Performance Requirements
* ระบบต้องอัปเดตและซิงค์ข้อมูลสถานะการจองให้แสดงผลบนตารางปฏิทินส่วนกลางภายในกรอบเวลา 2 วินาที (Target-to-Validate) ภายหลังมีธุรกรรมใหม่เกิดขึ้น

### 5.2 Safety Requirements
* ข้อมูลทั้งหมดต้องได้รับการสำรอง (Backup) เป็นประจำเพื่อป้องกันการสูญหายของประวัติการจองและ Audit Trail

### 5.3 Security Requirements
* สิทธิ์การเข้าถึงเมนูหน้าต่างจัดการอนุมัติคิวจองและการปฏิเสธคำขอ ต้องถูกจำกัดให้เฉพาะกลุ่มผู้ใช้งานที่มี Role เป็น "Admin" เท่านั้น ไม่อนุญาตให้ Requester เข้าใช้งาน

### 5.4 Software Quality Attributes
* **Auditability (ความสามารถในการตรวจสอบได้):** ระบบจะต้องบันทึกประวัติรหัสผู้กระทำ วันเวลา และสถานะการเปลี่ยนแปลงทุกธุรกรรมอย่างแม่นยำ (Audit Trail)
* **Usability (ความสะดวกในการใช้งาน):** UI จะต้องสามารถลดความซับซ้อนในการกรอกข้อมูล ให้ผู้ใช้งานสามารถกรอกรายละเอียดการจองขั้นต่ำแล้วเสร็จใน 1 หน้าต่าง

### 5.5 Business Rules
* **BR-A-01:** การได้สิทธิ์เข้าใช้ห้องประชุมต้องเกิดจากรายการที่มีสถานะเปลี่ยนเป็น "Approved (อนุมัติแล้ว)" เท่านั้น ห้ามผู้ขอจองเข้าใช้งานหากยังเป็นแค่สถานะ Received หรือ Pending Approval

---

## 6. Other Requirements
ไม่มีข้อกำหนดเพิ่มเติม

---

## Appendix A: Glossary
* **Single Source of Truth:** หลักการการใช้ฐานข้อมูลเพียงชุดเดียวเป็นตัวชี้วัดความจริง เพื่อป้องกันความขัดแย้งของข้อมูล
* **Audit Trail:** ร่องรอยหลักฐานการทำงานและการเปลี่ยนแปลงสถานะที่ระบบเก็บรวบรวมไว้
* **Cutoff Time:** เวลาเส้นตายในการอนุญาตให้กดยกเลิกคำขอจองก่อนเริ่มเวลาการประชุม

## Appendix B: Analysis Models
*(รูปภาพ Use Case Diagram สามารถเปิดดูได้ในไฟล์โปรเจกต์)*
![Use Case Diagram](usecase-diagram.png)

## Appendix C: To Be Determined List
* **TBD-01 (OI-A-01):** เวลากำหนดการยกเลิกสิทธิ์ (Cutoff Time) ที่อนุญาตให้ผู้ใช้กดยกเลิกคำขอได้ช้าสุดกี่ชั่วโมงก่อนการประชุม
* **TBD-02 (OI-A-02):** นโยบายการนำรายการเดิมที่ถูก Rejected กลับมาแก้ไขเวลาเพื่อส่งคำขอใหม่
* **TBD-03 (OI-A-03):** กฎเกณฑ์กลางในการพิจารณาคิวของแอดมิน (เช่น ลูกค้าภายนอกได้รับความสำคัญก่อน)
