---
name: create-detailed-design
description: สร้างหรือปรับปรุงเอกสาร Detailed Design แบบ conceptual (ไม่ผูกมัดกับ technical stack เฉพาะเจาะจง) จาก Requirement Spec, User Journey, Architecture, และ API/Database Spec ที่มีอยู่แล้วใน docs/ โดยมี **sequence flow แบบ mermaid `sequenceDiagram` เป็นอย่างน้อย** ต่อทุก operation/สถานการณ์หลัก รวมทั้ง pre-condition, main flow, alternate/exception flow เชิงแนวคิด โดยรวมทุก topic หรือระบุเจาะจงเฉพาะ topic ก็ได้ ผลลัพธ์เก็บไว้ใน docs/02-design/02-technical/{YYYYMMDD}-{NN}-{topic-slug}-detailed-design.md ใช้เมื่อผู้ใช้ขอ "สร้าง detailed design", "ทำ sequence diagram", "ออกแบบ flow การทำงานละเอียด", "conceptual detailed design", "ลงรายละเอียด architecture" ถ้ายังไม่มี spec ของ topic นั้นจะแนะนำให้รัน /create-requirement ก่อน และถ้ายังไม่มี architecture หรือ api/database spec จะถามผู้ใช้ก่อนว่าจะทำต่อแบบไหน
---

# Create Detailed Design

Skill นี้ทำงานใน conversation หลัก (ไม่ใช่ subagent) เพราะต้องถามผู้ใช้กลับหลายจุด (การเลือก topic, การตัดสินใจเชิงแนวคิดที่ไม่ชัดเจน, การยืนยันแผน) ให้ทำตามขั้นตอนต่อไปนี้ตามลำดับ

โปรดอ่าน `CLAUDE.md` ที่ root ของ repo ก่อน (ถ้ายังไม่เคยอ่านใน session นี้) เพื่อเข้าใจโครงสร้าง `docs/` และ convention ของ vault

Skill นี้ทำงาน "ต่อยอด" จากเอกสารที่มีอยู่แล้วเท่านั้น (spec / user journey / architecture / api spec / database spec) — ไม่สร้าง requirement, user journey, architecture หรือ api/database spec ใหม่เอง ถ้ายังไม่มี ให้แนะนำผู้ใช้รัน skill ที่เกี่ยวข้องก่อน

**หลักการสำคัญของเอกสารนี้**: ต้องเป็นระดับ **conceptual** เท่านั้น ห้ามผูกมัดกับ technical stack เฉพาะเจาะจง (ห้ามระบุชื่อภาษาโปรแกรม, framework, database engine, HTTP method, message queue เฉพาะยี่ห้อ ฯลฯ) ให้อธิบาย component/operation/table ด้วยคำเชิงบทบาทหน้าที่ตามที่ปรากฏใน architecture/api spec/database spec แทน — Detailed Design ต่างจาก Architecture ตรงที่ **ลงลึกถึงลำดับขั้นตอนการทำงานภายในของแต่ละ operation** (ใครเรียกใคร ก่อน-หลัง, เงื่อนไขแตกแขนง, กรณีผิดพลาด) ไม่ใช่แค่ภาพรวม component และ data flow แบบกว้างๆ

**เอกสารนี้ต้องมี sequence flow (mermaid `sequenceDiagram`) เป็นอย่างน้อยต่อทุก operation/สถานการณ์หลักที่ทำ** ห้ามส่งมอบเอกสารที่ไม่มี sequence diagram

## ขั้นตอน

### 1. กำหนดขอบเขต (scope) ของ topic

เช็คว่าผู้ใช้ระบุ topic/feature เจาะจงมาในคำขอหรือไม่ (เช่น ชื่อไฟล์ spec, ชื่อฟีเจอร์)

- **ถ้าระบุเจาะจงมาแล้ว**: ใช้ Glob/Grep ยืนยันว่ามี spec ของ topic นั้นจริงใน `docs/01-requirements/01-spec/` ใช้เฉพาะ topic ที่ระบุ
- **ถ้าไม่ได้ระบุ**: ใช้ Glob ลิสต์ topic ทั้งหมดที่มี spec อยู่ใน `docs/01-requirements/01-spec/` แล้วถามผู้ใช้ด้วย AskUserQuestion (multiSelect ได้) ว่าต้องการทำ detailed design สำหรับ topic ไหน อย่างน้อยควรมีตัวเลือกทำนอง:
  - **ทำทุก topic ที่มี architecture หรือ api/database spec แล้ว** — ข้อดี: sequence flow อ้างอิง component/operation/table จริงได้ครบ ไม่ต้องเดา / ข้อเสีย: topic ที่ยังไม่มีเอกสารต้นทางจะถูกข้าม
  - **เลือกเฉพาะบาง topic** (ให้ multiSelect รายชื่อ topic ที่เจอ) — ข้อดี: โฟกัสเฉพาะที่ต้องการ ตรวจสอบง่าย / ข้อเสีย: topic ที่เหลือยังไม่มี detailed design
  - **ทำทุก topic ที่มี spec** (รวมที่ยังไม่มี architecture/api spec) — ข้อดี: ได้เห็น sequence flow เร็วแม้เอกสารต้นทางยังไม่ครบ / ข้อเสีย: อาจต้องแก้ไขซ้ำเมื่อ architecture/api spec ตามมาทีหลัง

### 2. ตรวจสอบเอกสารต้นทางของแต่ละ topic ที่เลือก

สำหรับแต่ละ topic ที่จะทำ อ่าน/ตรวจสอบให้ครบทุกแหล่งต่อไปนี้ (ต้องอ่านให้ครบทุกอย่างที่มีจริง ห้ามข้ามเพราะรีบ):

1. `docs/01-requirements/01-spec/` — **บังคับต้องมี** ถ้าไม่มี ให้ข้าม topic นี้และแจ้งผู้ใช้ให้รัน `/create-requirement` ก่อน (ห้ามแต่ง spec เอง)
2. `docs/02-design/02-technical/{YYYYMMDD}-*-{topic-slug}-architecture.md` — **แนะนำให้มี** ใช้เป็นฐานของ component ที่จะปรากฏใน sequence diagram ถ้าไม่มี ให้ถามผู้ใช้ (ดูรายละเอียดด้านล่าง)
3. `docs/02-design/02-technical/{YYYYMMDD}-*-{topic-slug}-api-spec.md` และ `...-database-spec.md` — **แนะนำให้มี** ใช้เป็นฐานของ operation และ field/table ที่จะอ้างอิงในแต่ละขั้นตอน ถ้าไม่มี ให้ถามผู้ใช้ (ดูรายละเอียดด้านล่าง)
4. `docs/02-design/01-prototypes/{YYYYMMDD}-*-{topic-slug}-user-journey.md` — ใช้ช่วยยืนยันลำดับขั้นตอนที่ผู้ใช้จริงเจอ (ไม่บังคับ แต่ช่วยให้ sequence flow สอดคล้องกับประสบการณ์ผู้ใช้)
5. `docs/01-requirements/backlog.md` — อ่านเพื่อรู้สถานะปัจจุบัน (ไม่บังคับต้องมี)

ถ้า topic ใดไม่มี architecture หรือไม่มี api/database spec เลย ให้ถามผู้ใช้ด้วย AskUserQuestion (อย่างน้อย 3 ตัวเลือก) เช่น:
- **หยุดรอ แนะนำให้รัน `/create-architecture` และ/หรือ `/create-api-database-spec` ก่อน** — ข้อดี: sequence diagram จะอ้างอิง component/operation/table จริงได้ครบถ้วน สอดคล้องกับภาพรวมระบบ / ข้อเสีย: ต้องรอ
- **ทำต่อโดยอิงจาก spec (และ user journey ถ้ามี) อย่างเดียว** (ตั้งชื่อ component/operation ชั่วคราวเชิงแนวคิดเอง แล้วระบุเป็นสมมติฐานในเอกสาร) — ข้อดี: ได้เห็น sequence flow เร็ว / ข้อเสีย: อาจไม่ตรงกับ component/operation จริงที่จะตามมาทีหลัง ต้องแก้ไขซ้ำ
- **ข้าม topic นี้ไปก่อน ทำเฉพาะ topic อื่นที่พร้อม** — ข้อดี: ไม่เสียเวลารอ / ข้อเสีย: topic นี้ไม่มี detailed design รอบนี้

### 3. เช็คว่าเป็นการเรียกซ้ำหรือไม่

สำหรับแต่ละ topic ใช้ Glob เช็ค `docs/02-design/02-technical/*-{topic-slug}-detailed-design.md` ว่ามีไฟล์ detailed design ของ topic นี้อยู่แล้วหรือไม่

- **ถ้ายังไม่มี**: เป็น action `create_new`
- **ถ้ามีอยู่แล้ว**: เป็น action `update_existing` (แก้ไขไฟล์เดิมในที่เดิม ไม่สร้างไฟล์ใหม่ซ้ำ) ให้ Read ไฟล์เดิมมาดูก่อนว่ามี sequence flow อะไรอยู่แล้วบ้าง เพื่อไม่ให้ร่างซ้ำหรือขัดแย้งกับของเดิมโดยไม่ตั้งใจ ถ้าเนื้อหาที่จะเพิ่ม/แก้ขัดแย้งกับของเดิมอย่างมีนัยสำคัญ (เช่น operation เปลี่ยนจนต้องรื้อ sequence flow เดิม) ให้ถามผู้ใช้ด้วย AskUserQuestion ก่อนว่าต้องการแก้ไข/แทนที่ส่วนไหนบ้าง

### 4. อ่านเอกสารต้นทางแบบเต็ม

ใช้ Read อ่าน spec, architecture, api spec, database spec, user journey (เท่าที่มี) ของแต่ละ topic ที่จะทำให้ครบ

### 5. เลือก operation/สถานการณ์ที่จะร่าง sequence flow

ไล่รายการ operation จาก api spec (ถ้ามี) หรือ use case หลักจาก spec (ถ้าไม่มี api spec) จับคู่กับขั้นตอนใน user journey (ถ้ามี) เพื่อยืนยันลำดับ ระบุให้ครบทุก operation ที่มีนัยสำคัญต่อ business logic (ไม่จำเป็นต้องทำทุก operation ย่อยที่ตรงไปตรงมา เช่น อ่านข้อมูลธรรมดา — ใช้ดุลยพินิจ แต่ต้องระบุเหตุผลถ้าข้าม operation ใดไป)

### 6. ร่าง Sequence Flow ต่อ operation (บังคับต้องมี)

ต่อแต่ละ operation/สถานการณ์ที่เลือกในข้อ 5 ร่าง:
- **Pre-condition**: เงื่อนไขก่อนเริ่ม operation นี้ (เชิงแนวคิด)
- **Main flow**: ลำดับขั้นตอนปกติ เป็น mermaid `sequenceDiagram` ที่มี participant คือ conceptual component จาก architecture (หรือชื่อชั่วคราวถ้ายังไม่มี architecture) แสดงลำดับการเรียกระหว่าง component พร้อมข้อมูลที่ส่งต่อ (อ้างอิง field จาก database spec ถ้ามี)
- **Alternate/Exception flow**: เงื่อนไขแตกแขนงและกรณีผิดพลาดหลักๆ (อ้างอิง business rule ใน spec หรือ "กรณีผิดพลาดที่ควรพิจารณา" ใน api spec ถ้ามี) อธิบายเป็นข้อความหรือแตกแขนงเพิ่มใน sequence diagram ด้วย `alt`/`opt` block ก็ได้
- **Post-condition**: ผลลัพธ์/สถานะข้อมูลหลังจบ operation นี้เชิงแนวคิด

**ห้ามระบุชื่อเทคโนโลยี, HTTP method, protocol เฉพาะเจาะจงในแผนภาพหรือคำอธิบาย**

### 7. ถามเมื่อไม่ชัดเจน (บังคับ)

ทุกครั้งที่มีส่วนใดของ sequence flow ที่ไม่ชัดเจนพอจะร่างได้ (เช่น ไม่รู้ว่าลำดับการเรียกระหว่าง component ควรเป็นแบบ synchronous หรือ asynchronous เชิงแนวคิด, ไม่แน่ใจว่ากรณีผิดพลาดควรจัดการที่ component ไหน, ไม่แน่ใจว่า operation หนึ่งควรแตกเป็นหลาย sequence diagram ย่อยหรือรวมเป็นอันเดียว) ให้ใช้ **AskUserQuestion** ถามกลับ โดยต้องมีตัวเลือกให้เลือก **อย่างน้อย 3 แนวทาง** เสมอ (ไม่นับ "Other" อัตโนมัติ) พร้อมข้อดี/ข้อเสียของแต่ละแนวทาง ส่วนรายละเอียดปลีกย่อยที่ไม่กระทบภาพรวม (เช่น ถ้อยคำอธิบาย) ใช้ดุลยพินิจได้โดยไม่ต้องถาม

### 8. เสนอแผนให้ผู้ใช้ยืนยันก่อนสร้างไฟล์จริง (บังคับทุกครั้ง)

ก่อนเรียก subagent เขียนไฟล์ ให้สรุปแผนเป็นข้อความให้ผู้ใช้อ่านทวนก่อนเสมอ ประกอบด้วย:

- topic ที่จะทำ และเอกสารต้นทางที่ใช้ต่อ topic (spec / architecture / api spec / database spec / user journey — ระบุว่ามีหรือขาดอะไร)
- action ต่อ topic: `create_new` หรือ `update_existing` (พร้อม path เดิมถ้าเป็นกรณีหลัง)
- รายชื่อ operation/สถานการณ์ที่จะร่าง sequence flow พร้อมสรุปสั้นๆ ว่าแต่ละอันมี component อะไรเกี่ยวข้องและมี alternate/exception flow อะไรบ้าง

จากนั้นถามผู้ใช้ด้วย **AskUserQuestion**: "ดำเนินการตามแผนนี้หรือไม่" พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น:
- **ดำเนินการตามแผนนี้เลย** — เริ่มสร้าง/แก้ไขไฟล์ตามที่สรุปไว้ทันที
- **ขอปรับแผนก่อน** — ให้ผู้ใช้บอกจุดที่อยากแก้ แล้ววนกลับไปปรับแผนใหม่ (ย้อนไปข้อ 5-8) ก่อนถามยืนยันอีกครั้ง
- **ยกเลิก** — ไม่ต้องสร้างเอกสารนี้รอบนี้

**ห้ามเรียก subagent เขียนไฟล์ก่อนได้รับการยืนยัน "ดำเนินการตามแผนนี้เลย" อย่างชัดเจน**

### 9. มอบหมายให้ subagent `detailed-design-writer` เขียนไฟล์จริง

เรียก Agent tool ด้วย `subagent_type: detailed-design-writer` (รันแบบ foreground คือ `run_in_background: false` เพราะต้องรอผลมารายงานผู้ใช้ต่อ) โดย prompt ต้องมีข้อมูลครบ ไม่ทิ้งให้ subagent ต้องตัดสินใจเนื้อหาเอง:

ต่อแต่ละ topic:
- topic slug และชื่อเรื่อง
- path ของ spec / architecture / api spec / database spec / user journey ต้นทาง (เฉพาะที่มีจริง)
- action: `create_new` หรือ `update_existing` (พร้อม path ของไฟล์เดิมถ้าเป็นกรณีหลัง)
- เนื้อหาฉบับสมบูรณ์: รายชื่อ operation/สถานการณ์ทั้งหมดพร้อม pre-condition/main flow/alternate-exception flow/post-condition และ component ที่เกี่ยวข้องตามข้อ 5-6 — ตัดสินใจสุดท้ายแล้วจากข้อ 5-8 ไม่ใช่ให้ subagent ร่างเอง
- วันที่วันนี้ (YYYYMMDD) — ดึงจาก system context ของ conversation

subagent จะเป็นผู้จัดการ: หา running number และตั้งชื่อไฟล์ (กรณี `create_new`), เขียน/แก้ไขไฟล์ detailed design พร้อมวาด mermaid `sequenceDiagram` ต่อทุก operation, เชื่อมลิงก์กลับไปยัง spec/architecture/api-spec/database-spec/user-journey, อัปเดตสถานะใน `docs/01-requirements/backlog.md`, และเขียน log ที่ `docs/05-log/{YYYYMMDD}-log.md`

### 10. รายงานผลให้ผู้ใช้

สรุปให้ผู้ใช้ทราบว่าไฟล์อะไรถูกสร้าง/แก้ไขบ้าง (พร้อม path) และข้อสันนิษฐานใดๆ ที่ subagent ระบุมา เพื่อให้ผู้ใช้ตรวจสอบ/แก้ไขได้ทัน
