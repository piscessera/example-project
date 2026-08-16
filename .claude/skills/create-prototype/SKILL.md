---
name: create-prototype
description: สร้าง UI/UX Prototype (wireframe แบบ text-based ต่อหน้าจอ พร้อม component mapping อ้างอิง DESIGN.md) จาก Requirement, Backlog, Feature List, และ User Journey ที่มีอยู่แล้วใน docs/ โดยรวมทุก topic หรือระบุเจาะจงเฉพาะ topic ก็ได้ ผลลัพธ์เก็บเป็น Folder Version ใน docs/02-design/01-prototypes/{topic-slug}/v{NN}-{YYYYMMDD}/ ใช้เมื่อผู้ใช้ขอ "สร้าง prototype", "ทำ wireframe", "ทำ mockup หน้าจอ", "ออกแบบหน้าตา UI" ถ้ายังไม่มี docs/02-design/DESIGN.md จะถามผู้ใช้ให้สร้างก่อน และทุกครั้งก่อนลงมือสร้างไฟล์จริงจะเสนอแผนให้ผู้ใช้ยืนยันก่อนเสมอ
---

# Create Prototype

Skill นี้ทำงานใน conversation หลัก (ไม่ใช่ subagent) เพราะต้องถามผู้ใช้กลับหลายจุด (design system, การเลือก topic, การยืนยันแผน, การเลือก folder version) ให้ทำตามขั้นตอนต่อไปนี้ตามลำดับ **ห้ามข้ามขั้นตอนใดเพื่อความเร็ว**

โปรดอ่าน `CLAUDE.md` ที่ root ของ repo ก่อน (ถ้ายังไม่เคยอ่านใน session นี้) เพื่อเข้าใจโครงสร้าง `docs/` และ convention ของ vault

Skill นี้ทำงาน "ต่อยอด" จากเอกสารที่มีอยู่แล้วเท่านั้น (spec / feature list / user journey) — ไม่สร้าง requirement หรือ feature list/user journey ใหม่เอง ถ้ายังไม่มี ให้แนะนำผู้ใช้รัน `/create-requirement` และ/หรือ `/create-feature-journey` ก่อน

## ขั้นตอน

### 1. เช็ค Design System (`docs/02-design/DESIGN.md`) — บังคับก่อนทุกอย่าง

ใช้ Glob เช็คว่ามีไฟล์ `docs/02-design/DESIGN.md` อยู่หรือไม่

**ถ้ามีแล้ว**: ใช้ Read อ่านคร่าวๆ เพื่อรู้ว่ามี design tokens/component อะไรบ้าง (รายละเอียดเชิงลึกปล่อยให้ subagent อ่านเองตอนเขียนไฟล์จริง) แล้วไปข้อ 2 ต่อ

**ถ้ายังไม่มี**: หยุดขั้นตอนสร้าง prototype ไว้ก่อน แจ้งผู้ใช้ว่าต้องมี design system ก่อนถึงจะสร้าง prototype ที่สอดคล้องกันได้ แล้วถามด้วย **AskUserQuestion** ว่าต้องการสร้าง `DESIGN.md` ด้วยแนวทางไหน โดยต้องมีอย่างน้อย 3 ตัวเลือกพร้อมข้อดี/ข้อเสีย เช่น:

- **เลือกโทนสี/สไตล์จากชุดคำถามสำเร็จรูป** (เช่น เลือก primary color, สไตล์ Playful/Minimal/Corporate, ฟอนต์ที่รองรับภาษาไทย) — ข้อดี: เร็ว ตอบได้ทันที ไม่ต้องมีไฟล์ภาพ / ข้อเสีย: อาจไม่ตรงกับแบรนด์จริงเป๊ะๆ
- **ส่งภาพโลโก้หรือภาพตัวอย่างที่อยากให้ยึดโทน** — ข้อดี: ได้สีและสไตล์ที่ตรงกับแบรนด์จริงที่สุด / ข้อเสีย: ต้องรอผู้ใช้แนบไฟล์ก่อน และต้องตีความภาพเพิ่ม
- **อธิบายแนวทางเป็นข้อความอิสระ หรืออ้างอิงเว็บ/แอปอื่นที่ชอบ** — ข้อดี: ยืดหยุ่นที่สุด / ข้อเสีย: อาจต้องถามต่ออีกหลายรอบกว่าจะได้ข้อมูลครบ

เมื่อผู้ใช้ตอบ (หรือแนบภาพ/ลิงก์มา) ให้ใช้ข้อมูลนั้นร่างเนื้อหา DESIGN.md ในใจให้ครบตามโครง: Brand Identity & CI (ความหมายโลโก้/สไตล์, brand personality), Design Tokens (Colors: primary scale + neutral + semantic, Typography, Spacing, Radius & Shadow, Breakpoints), UI Components & Patterns (ปุ่ม, การ์ด, badge, table, ฯลฯ ตามที่ระบบต้องใช้จริงจาก spec ที่มีอยู่ใน `docs/01-requirements/01-spec/`), และ UX Guidelines & Rules ถ้ามีส่วนไหนไม่ชัดเจนแม้ถามแล้ว ให้ถามผู้ใช้เพิ่มเติมแบบ AskUserQuestion (อย่างน้อย 3 ตัวเลือกเช่นเดิม) จนกว่าจะร่างได้ครบ แล้วมอบหมายให้ subagent `prototype-writer` เขียนไฟล์จริงพร้อมกับงาน prototype ในขั้นตอนที่ 9 (ระบุ action `create_design_system` พร้อมเนื้อหาฉบับสมบูรณ์)

### 2. กำหนดขอบเขต (scope) ของ topic

เช็คว่าผู้ใช้ระบุ topic/feature เจาะจงมาในคำขอหรือไม่ (เช่น ชื่อไฟล์ spec, ชื่อฟีเจอร์)

- **ถ้าระบุเจาะจงมาแล้ว**: ใช้ Glob/Grep ยืนยันว่ามี spec ของ topic นั้นจริงใน `docs/01-requirements/01-spec/` ใช้เฉพาะ topic ที่ระบุ
- **ถ้าไม่ได้ระบุ**: ใช้ Glob ลิสต์ topic ทั้งหมดที่มี spec อยู่ใน `docs/01-requirements/01-spec/` แล้วถามผู้ใช้ด้วย AskUserQuestion (multiSelect ได้) ว่าต้องการสร้าง prototype สำหรับ topic ไหน อย่างน้อยควรมีตัวเลือกทำนอง:
  - **ทำทุก topic ที่มี spec ครบ** — ข้อดี: ได้ prototype ครบทั้งระบบในรอบเดียว / ข้อเสีย: ใช้เวลานาน ไฟล์เยอะ ตรวจสอบยากขึ้น
  - **เลือกเฉพาะบาง topic** (ให้ multiSelect รายชื่อ topic ที่เจอ) — ข้อดี: โฟกัสเฉพาะที่ต้องการ ตรวจสอบง่าย / ข้อเสีย: topic ที่เหลือยังไม่มี prototype
  - **ทำเฉพาะ topic ที่มี pipeline ครบ (spec + feature list + user journey)** — ข้อดี: มั่นใจว่า prototype อ้างอิงข้อมูลครบถ้วนสมบูรณ์ที่สุด / ข้อเสีย: topic ที่ยังทำ feature list/user journey ไม่เสร็จจะถูกข้าม

### 3. ตรวจสอบเอกสารต้นทางของแต่ละ topic ที่เลือก

สำหรับแต่ละ topic ที่จะทำ:

1. `docs/01-requirements/01-spec/` — **บังคับต้องมี** ถ้าไม่มี ให้ข้าม topic นี้และแจ้งผู้ใช้ให้รัน `/create-requirement` ก่อน (ห้ามแต่ง spec เอง)
2. `docs/01-requirements/02-plan/` — feature list (แนะนำให้มี)
3. `docs/02-design/01-prototypes/` — user journey แบบ mermaid ที่มีอยู่แล้ว (แนะนำให้มี, ใช้กำหนดหน้าจอ/ลำดับ flow ของ prototype)
4. `docs/01-requirements/backlog.md` — อ่านเพื่อรู้สถานะปัจจุบัน (ไม่บังคับต้องมี)

ถ้า topic ใดขาด feature list หรือ user journey ให้ถามผู้ใช้ด้วย AskUserQuestion (อย่างน้อย 3 ตัวเลือก) เช่น:
- **หยุดรอ แนะนำให้รัน `/create-feature-journey` ก่อน** — ข้อดี: prototype จะอ้างอิงข้อมูลครบสมบูรณ์ / ข้อเสีย: ต้องรอ
- **ทำ prototype ต่อโดยอิงจาก spec อย่างเดียว** (ไม่มี feature list/user journey ประกอบ) — ข้อดี: ได้เห็นภาพเร็ว / ข้อเสีย: อาจไม่ครอบคลุมทุกจุด pain point/ฟีเจอร์ย่อย
- **ข้าม topic นี้ไปก่อน ทำเฉพาะ topic อื่นที่พร้อม** — ข้อดี: ไม่เสียเวลารอ / ข้อเสีย: topic นี้ไม่มี prototype รอบนี้

### 4. เช็คว่าเป็นการเรียกซ้ำหรือไม่ (บังคับถามทุกครั้งที่เจอของเดิม)

สำหรับแต่ละ topic ใช้ Glob เช็ค `docs/02-design/01-prototypes/{topic-slug}/v*/` ว่ามี folder version อยู่แล้วหรือไม่

- **ถ้ายังไม่มี folder ของ topic นี้เลย**: นี่คือการสร้างครั้งแรก ไม่ต้องถาม ให้เป็น `v01` โดยอัตโนมัติ
- **ถ้ามีอยู่แล้ว (เช่น มี requirement ใหม่ หรือผู้ใช้ขอปรับ prototype เดิม)**: ต้องถามผู้ใช้ด้วย **AskUserQuestion ทุกครั้ง** ว่าต้องการ:
  - **สร้าง Folder Version ใหม่** (`v{N+1}`) — เก็บ prototype เวอร์ชันก่อนหน้าไว้ครบ ย้อนดู/เทียบเวอร์ชันได้ เหมาะกับกรณีมี requirement ใหม่เข้ามา หรือปรับเปลี่ยน flow/หน้าจอแบบมีนัยสำคัญ — **ข้อเสีย**: ไฟล์สะสมเยอะขึ้นเรื่อยๆ ถ้าสร้างบ่อยเกินไป
  - **แก้ไข Folder เวอร์ชันล่าสุดเดิม** (`v{N}` ปัจจุบัน) — ไม่มีไฟล์ซ้ำซ้อน สะอาด เหมาะกับการแก้ไขเล็กน้อย/แก้ตามรีวิว/แก้ทีละจุด — **ข้อเสีย**: เวอร์ชันก่อนแก้ไขจะหายไป ย้อนกลับไม่ได้ถ้าไม่ใช้ git history

  ให้ระบุคำแนะนำไว้ในคำถามด้วยว่า: ถ้าเป็นการเปลี่ยนแปลงจาก **requirement/scope ใหม่ หรือปรับ flow ใหญ่** แนะนำ **สร้าง version ใหม่**; ถ้าเป็นการ **แก้ไขจุดเล็กน้อยจากรีวิว** (เช่น ปรับข้อความ, แก้ layout จุดเดียว) แนะนำ **แก้ไข version ล่าสุด**

### 5. อ่านเอกสารต้นทางแบบเต็ม

ใช้ Read อ่าน spec, feature list, user journey (เท่าที่มี) ของแต่ละ topic ที่จะทำให้ครบ

### 6. ร่างหน้าจอ (screens) ของ prototype

จากขั้นตอน/touchpoint ใน user journey (ถ้ามี) และฟีเจอร์ใน feature list ให้ร่างรายการหน้าจอที่ต้องมี prototype แต่ละหน้าจอกำหนด: ชื่อหน้าจอ, node/ขั้นตอน journey ที่เกี่ยวข้อง, ฟีเจอร์ที่เกี่ยวข้อง (เลข # จาก feature list), wireframe แบบ text/ASCII box-layout คร่าวๆ, การ map แต่ละส่วนของ wireframe ไปยัง component/token ใน DESIGN.md (เช่น `3.4 Data Table`, `color.primary.500`), และ state ที่สำคัญ (loading/empty/error) ถ้าเกี่ยวข้อง

ถ้า topic ไม่มี user journey ให้อิงจาก feature list หรือ spec โดยตรงแทน

### 7. ถามเมื่อไม่ชัดเจน (บังคับ)

ทุกครั้งที่มีส่วนใดของ wireframe/หน้าจอที่ไม่ชัดเจนพอจะร่างได้ (เช่น ไม่รู้ว่าควรแยกเป็นกี่หน้าจอ, layout จุดใดคลุมเครือ, component ไหนควรใช้) ให้ใช้ **AskUserQuestion** ถามกลับ โดยต้องมีตัวเลือกให้เลือก **อย่างน้อย 3 แนวทาง** เสมอ (ไม่นับ "Other" อัตโนมัติ) พร้อมข้อดี/ข้อเสียของแต่ละแนวทาง ส่วนรายละเอียดปลีกย่อยที่ไม่กระทบภาพรวม (เช่น ถ้อยคำใน label) ใช้ดุลยพินิจได้โดยไม่ต้องถาม

### 8. เสนอแผนให้ผู้ใช้ยืนยันก่อนสร้างไฟล์จริง (บังคับทุกครั้ง)

ก่อนเรียก subagent เขียนไฟล์ ให้สรุปแผนเป็นข้อความให้ผู้ใช้อ่านทวนก่อนเสมอ ประกอบด้วย:

- topic ที่จะทำ และเอกสารต้นทางที่ใช้ต่อ topic (spec / feature list / user journey — ระบุว่ามีหรือขาดอะไร)
- action ต่อ topic: `v01` ใหม่ทั้งหมด / เพิ่ม `v{N+1}` ใหม่ / แก้ไข `v{N}` เดิม (พร้อมเหตุผลสั้นๆ ที่แนะนำ)
- รายชื่อหน้าจอ (screens) ที่จะสร้าง/แก้ไขต่อ topic พร้อมคำอธิบายสั้นๆ 1 บรรทัดต่อหน้าจอ
- ถ้ามี action `create_design_system` ให้ระบุด้วยว่าจะสร้าง `DESIGN.md` พร้อมสรุปแนวทาง (โทนสี/สไตล์) ที่จะใช้

จากนั้นถามผู้ใช้ด้วย **AskUserQuestion**: "ดำเนินการตามแผนนี้หรือไม่" พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น:
- **ดำเนินการตามแผนนี้เลย** — เริ่มสร้าง/แก้ไขไฟล์ตามที่สรุปไว้ทันที
- **ขอปรับแผนก่อน** — ให้ผู้ใช้บอกจุดที่อยากแก้ แล้ววนกลับไปปรับแผนใหม่ (ย้อนไปข้อ 6-8) ก่อนถามยืนยันอีกครั้ง
- **ยกเลิก** — ไม่ต้องสร้าง prototype รอบนี้

**ห้ามเรียก subagent เขียนไฟล์ก่อนได้รับการยืนยัน "ดำเนินการตามแผนนี้เลย" อย่างชัดเจน**

### 9. มอบหมายให้ subagent `prototype-writer` เขียนไฟล์จริง

เรียก Agent tool ด้วย `subagent_type: prototype-writer` (รันแบบ foreground คือ `run_in_background: false` เพราะต้องรอผลมารายงานผู้ใช้ต่อ) โดย prompt ต้องมีข้อมูลครบ ไม่ทิ้งให้ subagent ต้องตัดสินใจเนื้อหาเอง:

- ถ้ามี: action `create_design_system` พร้อมเนื้อหา DESIGN.md ฉบับสมบูรณ์ (ตามโครงในข้อ 1)
- ต่อแต่ละ topic:
  - topic slug และชื่อเรื่อง
  - path ของ spec / feature list / user journey ต้นทาง (เฉพาะที่มีจริง)
  - action: `create_v1` (topic ใหม่ทั้งหมด) / `create_new_version` (พร้อม version number ใหม่และ path ของ version ล่าสุดเดิมไว้อ้างอิง) / `update_existing_version` (พร้อม path ของ version ที่จะแก้)
  - เนื้อหาหน้าจอฉบับสมบูรณ์ทุกหน้าจอ (wireframe text-based, component mapping, states, journey step/feature ที่เกี่ยวข้อง) — ตัดสินใจสุดท้ายแล้วจากข้อ 6-8 ไม่ใช่ให้ subagent ร่างเอง
- วันที่วันนี้ (YYYYMMDD) — ดึงจาก system context ของ conversation

subagent จะเป็นผู้จัดการ: สร้าง/แก้ไข `DESIGN.md` ถ้ามีคำสั่ง, หา version number และตั้งชื่อ folder, เขียนไฟล์ prototype ทุก topic, อัปเดต index.md ของแต่ละ topic folder, เชื่อมลิงก์กลับไปยัง spec/feature-list/user-journey, อัปเดตสถานะใน `docs/01-requirements/backlog.md`, และเขียน log ที่ `docs/05-log/{YYYYMMDD}-log.md`

### 10. รายงานผลให้ผู้ใช้

สรุปให้ผู้ใช้ทราบว่าไฟล์/folder อะไรถูกสร้าง/แก้ไขบ้าง (พร้อม path) และข้อสันนิษฐานใดๆ ที่ subagent ระบุมา เพื่อให้ผู้ใช้ตรวจสอบ/แก้ไขได้ทัน
