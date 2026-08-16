---
name: create-test-plan
description: สร้าง Test Plan (Acceptance Criteria + ขอบเขตการทดสอบ + Test Data + Test Case) จาก Requirement Spec, Backlog, Feature List, และ User Journey (และ Prototype ถ้ามี) ที่มีอยู่แล้วใน docs/ โดยรวมทุก topic หรือระบุเจาะจงเฉพาะ topic ก็ได้ ผลลัพธ์เก็บไว้ใน docs/03-testing/01-test-plan/{YYYYMMDD}-{NN}-{topic-slug}-test-plan.md ใช้เมื่อผู้ใช้ขอ "สร้าง test plan", "ทำ test case", "เขียน acceptance criteria", "วางแผนทดสอบ" ถ้ายังไม่มี spec ของ topic นั้นจะแนะนำให้ใช้ /create-requirement ก่อน
---

# Create Test Plan

Skill นี้ทำงานใน conversation หลัก (ไม่ใช่ subagent) เพราะต้องถามผู้ใช้กลับหลายจุด (การเลือก topic, edge case ที่ไม่ชัดเจน, การยืนยันแผน) ให้ทำตามขั้นตอนต่อไปนี้ตามลำดับ

โปรดอ่าน `CLAUDE.md` ที่ root ของ repo ก่อน (ถ้ายังไม่เคยอ่านใน session นี้) เพื่อเข้าใจโครงสร้าง `docs/` และ convention ของ vault

Skill นี้ทำงาน "ต่อยอด" จากเอกสารที่มีอยู่แล้วเท่านั้น (spec / feature list / user journey / prototype) — ไม่สร้าง requirement หรือ feature list/user journey ใหม่เอง ถ้ายังไม่มี ให้แนะนำผู้ใช้รัน `/create-requirement` และ/หรือ `/create-feature-journey` ก่อน

## ขั้นตอน

### 1. กำหนดขอบเขต (scope) ของ topic

เช็คว่าผู้ใช้ระบุ topic/feature เจาะจงมาในคำขอหรือไม่ (เช่น ชื่อไฟล์ spec, ชื่อฟีเจอร์)

- **ถ้าระบุเจาะจงมาแล้ว**: ใช้ Glob/Grep ยืนยันว่ามี spec ของ topic นั้นจริงใน `docs/01-requirements/01-spec/` ใช้เฉพาะ topic ที่ระบุ
- **ถ้าไม่ได้ระบุ**: ใช้ Glob ลิสต์ topic ทั้งหมดที่มี spec อยู่ใน `docs/01-requirements/01-spec/` แล้วถามผู้ใช้ด้วย AskUserQuestion (multiSelect ได้) ว่าต้องการทำ test plan สำหรับ topic ไหน อย่างน้อยควรมีตัวเลือกทำนอง:
  - **ทำทุก topic ที่มี spec ครบ** — ข้อดี: ได้ test plan ครบทั้งระบบในรอบเดียว / ข้อเสีย: ใช้เวลานาน ไฟล์เยอะ ตรวจสอบยากขึ้น
  - **เลือกเฉพาะบาง topic** (ให้ multiSelect รายชื่อ topic ที่เจอ) — ข้อดี: โฟกัสเฉพาะที่ต้องการ ตรวจสอบง่าย / ข้อเสีย: topic ที่เหลือยังไม่มี test plan
  - **ทำเฉพาะ topic ที่มี pipeline ครบ (spec + feature list + user journey)** — ข้อดี: มั่นใจว่า test plan อ้างอิงข้อมูลครบถ้วนสมบูรณ์ที่สุด / ข้อเสีย: topic ที่ยังทำ feature list/user journey ไม่เสร็จจะถูกข้าม

### 2. ตรวจสอบเอกสารต้นทางของแต่ละ topic ที่เลือก

สำหรับแต่ละ topic ที่จะทำ อ่าน/ตรวจสอบให้ครบทุกแหล่งต่อไปนี้ (ต้องอ่านให้ครบทุกอย่างที่มีจริง ห้ามข้ามเพราะรีบ):

1. `docs/01-requirements/01-spec/` — **บังคับต้องมี** ถ้าไม่มี ให้ข้าม topic นี้และแจ้งผู้ใช้ให้รัน `/create-requirement` ก่อน (ห้ามแต่ง spec เอง)
2. `docs/01-requirements/02-plan/` — feature list (แนะนำให้มี, ใช้กำหนด priority และเลขฟีเจอร์อ้างอิงใน test case)
3. `docs/02-design/01-prototypes/{YYYYMMDD}-*-{topic-slug}-user-journey.md` — user journey แบบ mermaid (แนะนำให้มี, ใช้หา touchpoint/pain point ที่ควรมี test case ครอบคลุม)
4. `docs/02-design/01-prototypes/{topic-slug}/` — UI/UX prototype ถ้ามี (ใช้หา state เช่น loading/empty/error ที่ควรมี test case ครอบคลุมเพิ่มเติม, ไม่บังคับ)
5. `docs/01-requirements/backlog.md` — อ่านเพื่อรู้สถานะปัจจุบัน (ไม่บังคับต้องมี)

ถ้า topic ใดขาด feature list หรือ user journey ให้ถามผู้ใช้ด้วย AskUserQuestion (อย่างน้อย 3 ตัวเลือก) เช่น:
- **หยุดรอ แนะนำให้รัน `/create-feature-journey` ก่อน** — ข้อดี: test plan จะอ้างอิงข้อมูลครบสมบูรณ์ / ข้อเสีย: ต้องรอ
- **ทำ test plan ต่อโดยอิงจาก spec อย่างเดียว** (ไม่มี feature list/user journey/prototype ประกอบ) — ข้อดี: ได้เห็น test case เร็ว / ข้อเสีย: อาจไม่ครอบคลุมทุก edge case/pain point ที่ควรทดสอบ
- **ข้าม topic นี้ไปก่อน ทำเฉพาะ topic อื่นที่พร้อม** — ข้อดี: ไม่เสียเวลารอ / ข้อเสีย: topic นี้ไม่มี test plan รอบนี้

### 3. เช็คว่าเป็นการเรียกซ้ำหรือไม่

สำหรับแต่ละ topic ใช้ Glob เช็ค `docs/03-testing/01-test-plan/*-{topic-slug}-test-plan.md` ว่ามีไฟล์ test plan ของ topic นี้อยู่แล้วหรือไม่

- **ถ้ายังไม่มี**: เป็น action `create_new`
- **ถ้ามีอยู่แล้ว**: เป็น action `update_existing` (แก้ไขไฟล์เดิมในที่เดิม ไม่สร้างไฟล์ใหม่ซ้ำ) ให้ Read ไฟล์เดิมมาดูก่อนว่ามี AC/test case อะไรอยู่แล้วบ้าง เพื่อไม่ให้ร่างซ้ำหรือขัดแย้งกับของเดิมโดยไม่ตั้งใจ ถ้าเนื้อหาที่จะเพิ่ม/แก้ขัดแย้งกับของเดิมอย่างมีนัยสำคัญ (เช่น requirement เปลี่ยนจนต้องรื้อ test case เดิมทั้งชุด) ให้ถามผู้ใช้ด้วย AskUserQuestion ก่อนว่าต้องการแก้ไข/แทนที่ส่วนไหนบ้าง

### 4. อ่านเอกสารต้นทางแบบเต็ม

ใช้ Read อ่าน spec, feature list, user journey, prototype (เท่าที่มี) ของแต่ละ topic ที่จะทำให้ครบ

### 5. ร่าง Acceptance Criteria

จากแต่ละฟีเจอร์ในฟีเจอร์ลิสต์ (หรือ user story ใน spec ถ้าไม่มีฟีเจอร์ลิสต์) ร่าง acceptance criteria แบบ Given-When-Then อย่างน้อย 1 ข้อต่อฟีเจอร์/user story โดยอ้างอิงเลขฟีเจอร์ (`#n`) กลับไปด้วย ให้ครอบคลุมทั้ง happy path และเงื่อนไข/กติกาทางธุรกิจสำคัญจาก "Business Rules" ในสเปค

### 6. ร่าง Test Scope และ Test Data

- **ขอบเขตการทดสอบ (in/out scope)**: อ้างอิงจาก scope ใน spec เป็นหลัก ปรับตามสิ่งที่ AC ครอบคลุมจริง
- **Test Data**: ระบุชุดข้อมูลตัวอย่างที่ต้องใช้ทดสอบ (เช่น บัญชีทดสอบ, ค่า edge เช่น จำนวนเป็น 0/ติดลบ, ข้อมูลที่ผิดรูปแบบ) ให้สอดคล้องกับ business rules และ AC ที่ร่างไว้

### 7. ร่าง Test Case

จาก AC แต่ละข้อ + ขั้นตอนใน user journey (ถ้ามี) + state ใน prototype (loading/empty/error ถ้ามี) ร่างตาราง test case โดยแต่ละแถวมี: ID (`TC-{topic-slug}-{NN}`), อ้างอิง AC ที่เกี่ยวข้อง, precondition, ขั้นตอนการทดสอบ, ผลลัพธ์ที่คาดหวัง, ประเภท (Positive / Negative / Edge case), Priority (อิง MoSCoW ของฟีเจอร์ที่เกี่ยวข้อง: Must→High, Should→Medium, Could/Won't→Low)

ให้มีทั้ง positive case (happy path ตาม AC), negative case (ข้อมูลผิด/เงื่อนไขไม่ผ่าน), และ edge case (ค่าขอบ, pain point จาก user journey ถ้ามี) อย่างน้อยครอบคลุมทุกฟีเจอร์ที่เป็น Must/Should

### 8. ถามเมื่อไม่ชัดเจน (บังคับ)

ทุกครั้งที่มีส่วนใดของ AC/test case ที่ไม่ชัดเจนพอจะร่างได้ (เช่น ไม่รู้ผลลัพธ์ที่คาดหวังเมื่อเงื่อนไขขัดแย้งกัน, ไม่แน่ใจว่า edge case ไหนสำคัญพอต้องมี test case, priority ไม่ชัดจากฟีเจอร์ลิสต์) ให้ใช้ **AskUserQuestion** ถามกลับ โดยต้องมีตัวเลือกให้เลือก **อย่างน้อย 3 แนวทาง** เสมอ (ไม่นับ "Other" อัตโนมัติ) พร้อมข้อดี/ข้อเสียของแต่ละแนวทาง ส่วนรายละเอียดปลีกย่อยที่ไม่กระทบภาพรวม (เช่น ถ้อยคำใน test case) ใช้ดุลยพินิจได้โดยไม่ต้องถาม

### 9. เสนอแผนให้ผู้ใช้ยืนยันก่อนสร้างไฟล์จริง (บังคับทุกครั้ง)

ก่อนเรียก subagent เขียนไฟล์ ให้สรุปแผนเป็นข้อความให้ผู้ใช้อ่านทวนก่อนเสมอ ประกอบด้วย:

- topic ที่จะทำ และเอกสารต้นทางที่ใช้ต่อ topic (spec / feature list / user journey / prototype — ระบุว่ามีหรือขาดอะไร)
- action ต่อ topic: `create_new` หรือ `update_existing` (พร้อม path เดิมถ้าเป็นกรณีหลัง)
- จำนวน AC และจำนวน test case ต่อ topic พร้อมตัวอย่างคร่าวๆ 2-3 ข้อ

จากนั้นถามผู้ใช้ด้วย **AskUserQuestion**: "ดำเนินการตามแผนนี้หรือไม่" พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น:
- **ดำเนินการตามแผนนี้เลย** — เริ่มสร้าง/แก้ไขไฟล์ตามที่สรุปไว้ทันที
- **ขอปรับแผนก่อน** — ให้ผู้ใช้บอกจุดที่อยากแก้ แล้ววนกลับไปปรับแผนใหม่ (ย้อนไปข้อ 5-9) ก่อนถามยืนยันอีกครั้ง
- **ยกเลิก** — ไม่ต้องสร้าง test plan รอบนี้

**ห้ามเรียก subagent เขียนไฟล์ก่อนได้รับการยืนยัน "ดำเนินการตามแผนนี้เลย" อย่างชัดเจน**

### 10. มอบหมายให้ subagent `test-plan-writer` เขียนไฟล์จริง

เรียก Agent tool ด้วย `subagent_type: test-plan-writer` (รันแบบ foreground คือ `run_in_background: false` เพราะต้องรอผลมารายงานผู้ใช้ต่อ) โดย prompt ต้องมีข้อมูลครบ ไม่ทิ้งให้ subagent ต้องตัดสินใจเนื้อหาเอง:

ต่อแต่ละ topic:
- topic slug และชื่อเรื่อง
- path ของ spec / feature list / user journey / prototype ต้นทาง (เฉพาะที่มีจริง)
- action: `create_new` หรือ `update_existing` (พร้อม path ของไฟล์เดิมถ้าเป็นกรณีหลัง)
- เนื้อหาฉบับสมบูรณ์: Acceptance Criteria ทุกข้อ, ขอบเขตการทดสอบ (in/out), Test Data, ตาราง Test Case ทุกแถว — ตัดสินใจสุดท้ายแล้วจากข้อ 5-9 ไม่ใช่ให้ subagent ร่างเอง
- วันที่วันนี้ (YYYYMMDD) — ดึงจาก system context ของ conversation

subagent จะเป็นผู้จัดการ: หา running number และตั้งชื่อไฟล์ (กรณี `create_new`), เขียน/แก้ไขไฟล์ test plan, เชื่อมลิงก์กลับไปยัง spec/feature-list/user-journey/prototype, อัปเดตสถานะใน `docs/01-requirements/backlog.md`, และเขียน log ที่ `docs/05-log/{YYYYMMDD}-log.md`

### 11. รายงานผลให้ผู้ใช้

สรุปให้ผู้ใช้ทราบว่าไฟล์อะไรถูกสร้าง/แก้ไขบ้าง (พร้อม path) และข้อสันนิษฐานใดๆ ที่ subagent ระบุมา เพื่อให้ผู้ใช้ตรวจสอบ/แก้ไขได้ทัน
