---
name: create-feature-journey
description: รับ topic ที่มี requirement spec อยู่แล้วใน docs/01-requirements/01-spec/ มาแตกเป็น feature list (docs/01-requirements/02-plan/) และ user journey แบบ mermaid diagram (docs/02-design/01-prototypes/) ที่เชื่อมโยงกัน ใช้เมื่อผู้ใช้ขอ "สร้าง feature list", "ทำ user journey", "แตกฟีเจอร์จาก spec" ถ้าไม่พบ spec ต้นทางของ topic นั้นจะแนะนำให้ใช้ /create-requirement ก่อน
---

# Create Feature & Journey

Skill นี้ทำงานใน conversation หลัก (ไม่ใช่ subagent) เพราะต้องถามผู้ใช้กลับเมื่อไม่แน่ใจ ให้ทำตามขั้นตอนต่อไปนี้ตามลำดับ

โปรดอ่าน `CLAUDE.md` ที่ root ของ repo ก่อน (ถ้ายังไม่เคยอ่านใน session นี้) เพื่อเข้าใจโครงสร้าง `docs/` และ convention ของ vault

Skill นี้ทำงาน "ต่อยอด" จากเอกสารที่มีอยู่แล้วเท่านั้น — ไม่สร้าง requirement ใหม่เอง

## ขั้นตอน

### 1. ระบุ spec ต้นทาง

ถ้าผู้ใช้ระบุไฟล์/topic ชัดเจนอยู่แล้วในคำขอ ให้ใช้ Read เปิดอ่านไฟล์นั้นตรงๆ

ถ้าผู้ใช้บอกมาแค่ชื่อฟีเจอร์/หัวข้อคร่าวๆ ให้ใช้ Glob/Grep ค้นใน `docs/01-requirements/01-spec/` หา spec ที่ใกล้เคียง แล้วถามยืนยันว่าหมายถึงไฟล์ไหน (ถ้าเจอมากกว่า 1 ไฟล์ที่เป็นไปได้)

ถ้าค้นหาแล้ว**ไม่พบ spec ที่ตรงกับ topic นี้เลย** ให้แจ้งผู้ใช้ว่า topic นี้ยังไม่มี requirement spec และแนะนำให้รัน `/create-requirement` เพื่อสร้าง spec ก่อน แล้วหยุดรอ ไม่ดำเนินการต่อ (ห้ามเดา/แต่ง requirement เองเพื่อสร้าง feature list)

### 2. เช็คของเดิม

ใช้ Glob ค้นใน `docs/01-requirements/02-plan/` และ `docs/02-design/01-prototypes/` หาไฟล์ที่มี topic slug เดียวกับ spec ต้นทางอยู่แล้วหรือไม่ (รูปแบบไฟล์: `{YYYYMMDD}-{NN}-{topic-slug}-feature-list.md` และ `...-user-journey.md`)

ตัดสินใจแยกต่อไฟล์ว่าเป็น `create_new` หรือ `update_existing` (feature list กับ user journey อาจมีสถานะต่างกันได้ เช่น มี feature list อยู่แล้วแต่ยังไม่มี user journey)

### 3. อ่าน spec เต็ม

ใช้ Read อ่านเนื้อหา spec ต้นทางทั้งหมด (Feature Requirements, User Stories/Use Cases, Business Rules, Scope)

### 4. ร่าง feature list

แตก "Feature Requirements" และ "User Stories" ในสเปคออกเป็นรายการฟีเจอร์ย่อยที่เป็นหน่วยพัฒนา/ทดสอบได้จริง แต่ละฟีเจอร์กำหนด priority ด้วย **MoSCoW** (Must / Should / Could / Won't) และอ้างอิงกลับไปยัง user story ที่มา (ถ้ามี)

### 5. ร่าง user journey

หา persona จาก User Stories ในสเปค (เช่น ลูกค้า, พนักงาน) แต่ละ persona ร่างเป็นลำดับขั้นตอน (step-by-step) พร้อม touchpoint และอ้างอิงเลขฟีเจอร์จาก feature list ที่ร่างไว้ในข้อ 4 ระบุจุดที่เป็น pain point หรือจุดตัดสินใจ/แตกทาง (ถ้ามี) ไว้ด้วย เพื่อนำไปวาดเป็น mermaid flowchart ต่อไป

### 6. ถามเมื่อไม่ชัดเจน (บังคับ)

ทุกครั้งที่มีส่วนใดไม่ชัดเจนพอจะเขียนเป็นเอกสารได้ ให้ใช้ **AskUserQuestion** ถามกลับ โดยคำถามต้องมีตัวเลือกแนวทางให้เลือก **อย่างน้อย 3 แนวทาง** เสมอ (ไม่นับ "Other" ที่ระบบเพิ่มให้อัตโนมัติ) พร้อมคำอธิบายสั้นๆ ต่อแนวทาง

อย่าเดาเอาเองในกรณีต่อไปนี้ — ต้องถามเสมอ:

- spec เขียนคลุมเครือจนแตกเป็น feature ได้หลายแบบ
- priority ของฟีเจอร์ไม่ชัดจาก spec
- persona ไม่ชัดเจน หรือไม่แน่ใจว่าควรแยก persona กี่กลุ่ม
- กรณี `update_existing` ที่เนื้อหาเดิมขัดแย้งกับสิ่งที่ร่างใหม่อย่างมีนัยสำคัญ

ส่วนที่เป็นรายละเอียดปลีกย่อยจริงๆ (ถ้อยคำ, การจัดลำดับ step ย่อยที่ไม่กระทบความหมาย) ใช้ดุลยพินิจได้โดยไม่ต้องถาม

### 7. มอบหมายให้ subagent `feature-journey-writer` เขียนไฟล์จริง

เรียก Agent tool ด้วย `subagent_type: feature-journey-writer` (รันแบบ foreground คือ `run_in_background: false` เพราะต้องรอผลมารายงานผู้ใช้ต่อ) โดย prompt ต้องมีข้อมูลครบ ไม่ทิ้งให้ subagent ต้องตัดสินใจเนื้อหาเอง:

- path ของ spec ต้นทาง และหัวข้อ/ชื่อเรื่อง
- topic slug (ต้องตรงกับที่ใช้ในชื่อไฟล์ spec เดิม)
- เนื้อหา feature list ฉบับสมบูรณ์ (รายการฟีเจอร์ พร้อม priority MoSCoW และอ้างอิง user story)
- เนื้อหา user journey ฉบับสมบูรณ์ (แยกตาม persona แต่ละ persona มีเป้าหมาย, ลำดับขั้นตอนพร้อม node id, touchpoint, ฟีเจอร์ที่เกี่ยวข้อง, จุดที่เป็น pain point/จุดตัดสินใจ)
- action สำหรับแต่ละไฟล์: `create_new` หรือ `update_existing` (พร้อม path เดิมถ้าเป็นกรณีหลัง)
- วันที่วันนี้ (YYYYMMDD) — ดึงจาก system context ของ conversation

subagent จะเป็นผู้จัดการ: ตั้งชื่อไฟล์พร้อม running number, เขียนเอกสารทั้งสองไฟล์, วาด mermaid flowchart ของ user journey, เชื่อมลิงก์ระหว่างไฟล์ทั้งหมด (spec ↔ feature list ↔ user journey), อัปเดตสถานะใน `docs/01-requirements/backlog.md`, และเขียน log ที่ `docs/05-log/{YYYYMMDD}-log.md`

### 8. รายงานผลให้ผู้ใช้

สรุปให้ผู้ใช้ทราบว่าไฟล์อะไรถูกสร้าง/แก้ไขบ้าง (พร้อม path) และข้อสันนิษฐานใดๆ ที่ subagent ระบุมา เพื่อให้ผู้ใช้ตรวจสอบ/แก้ไขได้ทัน
