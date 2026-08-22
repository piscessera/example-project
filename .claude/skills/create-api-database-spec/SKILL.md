---
name: create-api-database-spec
description: สร้างหรือปรับปรุงเอกสาร API Spec และ Database Spec แบบ conceptual (ไม่ผูกมัดกับ technical stack เฉพาะเจาะจง เช่น ภาษาโปรแกรม, framework, database engine, รูปแบบ API เฉพาะ) จาก Requirement Spec, Feature List, User Journey, และ Architecture (ถ้ามี) ที่มีอยู่แล้วใน docs/ โดยรวมทุก topic หรือระบุเจาะจงเฉพาะ topic ก็ได้ ผลลัพธ์คือเอกสาร API Spec (docs/02-design/02-technical/{YYYYMMDD}-{NN}-{topic-slug}-api-spec.md) และ Database Spec พร้อม ER Diagram และรายละเอียดแต่ละตาราง (docs/02-design/02-technical/{YYYYMMDD}-{NN}-{topic-slug}-database-spec.md) ใช้เมื่อผู้ใช้ขอ "สร้าง api spec", "ทำ database schema", "ออกแบบฐานข้อมูล", "ER diagram", "data contract", "conceptual database" ถ้ายังไม่มี spec หรือ user journey ของ topic นั้นจะแนะนำให้รัน /create-requirement และ/หรือ /create-feature-journey ก่อน
---

# Create API & Database Spec

Skill นี้ทำงานใน conversation หลัก (ไม่ใช่ subagent) เพราะต้องถามผู้ใช้กลับหลายจุด (การเลือก topic, การตัดสินใจเชิงแนวคิดที่ไม่ชัดเจน, การยืนยันแผน) ให้ทำตามขั้นตอนต่อไปนี้ตามลำดับ

โปรดอ่าน `CLAUDE.md` ที่ root ของ repo ก่อน (ถ้ายังไม่เคยอ่านใน session นี้) เพื่อเข้าใจโครงสร้าง `docs/` และ convention ของ vault

Skill นี้ทำงาน "ต่อยอด" จากเอกสารที่มีอยู่แล้วเท่านั้น (spec / feature list / user journey / architecture) — ไม่สร้าง requirement หรือ feature list/user journey ใหม่เอง ถ้ายังไม่มี ให้แนะนำผู้ใช้รัน `/create-requirement` และ/หรือ `/create-feature-journey` ก่อน

**หลักการสำคัญของเอกสารนี้**: ต้องเป็นระดับ **conceptual** เท่านั้น ห้ามผูกมัดกับ technical stack เฉพาะเจาะจง

- API Spec: ห้ามระบุ HTTP method/verb เฉพาะเจาะจง (GET/POST/PUT/DELETE), URL path, รูปแบบ payload เฉพาะ (JSON/XML/gRPC/GraphQL schema), หรือชื่อ framework ใดๆ ให้อธิบายเป็น "การดำเนินการ (operation)" เชิงบทบาทหน้าที่แทน (เช่น "ดำเนินการสร้างออเดอร์ใหม่" แทน "POST /orders")
- Database Spec: ห้ามระบุชื่อ database engine (เช่น PostgreSQL, MySQL, MongoDB) หรือชนิดข้อมูลเฉพาะของภาษา/เอนจิ้นใดๆ (เช่น VARCHAR(255), ObjectId) ให้ใช้ชนิดข้อมูลเชิงแนวคิด (ข้อความ, ตัวเลข, วันที่-เวลา, ค่าจริง/เท็จ, รายการอ้างอิง ฯลฯ) แทน

## ขั้นตอน

### 1. กำหนดขอบเขต (scope) ของ topic

เช็คว่าผู้ใช้ระบุ topic/feature เจาะจงมาในคำขอหรือไม่ (เช่น ชื่อไฟล์ spec, ชื่อฟีเจอร์)

- **ถ้าระบุเจาะจงมาแล้ว**: ใช้ Glob/Grep ยืนยันว่ามี spec ของ topic นั้นจริงใน `docs/01-requirements/01-spec/` ใช้เฉพาะ topic ที่ระบุ
- **ถ้าไม่ได้ระบุ**: ใช้ Glob ลิสต์ topic ทั้งหมดที่มี spec อยู่ใน `docs/01-requirements/01-spec/` แล้วถามผู้ใช้ด้วย AskUserQuestion (multiSelect ได้) ว่าต้องการทำ API & Database spec สำหรับ topic ไหน อย่างน้อยควรมีตัวเลือกทำนอง:
  - **ทำทุก topic ที่มี spec ครบ** — ข้อดี: ได้ data contract ครบทั้งระบบในรอบเดียว มองเห็นความสัมพันธ์ข้ามระบบ / ข้อเสีย: ใช้เวลานาน ตรวจสอบยากขึ้น
  - **เลือกเฉพาะบาง topic** (ให้ multiSelect รายชื่อ topic ที่เจอ) — ข้อดี: โฟกัสเฉพาะที่ต้องการ ตรวจสอบง่าย / ข้อเสีย: topic ที่เหลือยังไม่มี spec นี้
  - **ทำเฉพาะ topic ที่มี architecture document แล้ว** — ข้อดี: มั่นใจว่า entity/component ที่ใช้ร่าง table/operation อ้างอิงของจริงได้ครบ ไม่ต้องเดา / ข้อเสีย: topic ที่ยังไม่มี architecture จะถูกข้าม

### 2. ตรวจสอบเอกสารต้นทางของแต่ละ topic ที่เลือก

สำหรับแต่ละ topic ที่จะทำ อ่าน/ตรวจสอบให้ครบทุกแหล่งต่อไปนี้ (ต้องอ่านให้ครบทุกอย่างที่มีจริง ห้ามข้ามเพราะรีบ):

1. `docs/01-requirements/01-spec/` — **บังคับต้องมี** ถ้าไม่มี ให้ข้าม topic นี้และแจ้งผู้ใช้ให้รัน `/create-requirement` ก่อน (ห้ามแต่ง spec เอง)
2. `docs/02-design/01-prototypes/{YYYYMMDD}-*-{topic-slug}-user-journey.md` — **แนะนำให้มี** ใช้ยืนยันว่า operation ที่ร่างครอบคลุมทุกขั้นตอนที่ผู้ใช้ต้องทำจริง ถ้าไม่มี ให้ถามผู้ใช้ (ดูรายละเอียดด้านล่าง)
3. `docs/01-requirements/02-plan/` — feature list (แนะนำให้มี, ใช้ตรวจว่า operation/table ที่ร่างครอบคลุมทุกฟีเจอร์)
4. `docs/02-design/02-technical/{YYYYMMDD}-*-{topic-slug}-architecture.md` — architecture document ถ้ามี (ใช้เป็นฐานของ conceptual entity/component เพื่อแตกเป็น table และ operation ให้สอดคล้องกัน ไม่บังคับแต่แนะนำอย่างยิ่ง)
5. `docs/01-requirements/backlog.md` — อ่านเพื่อรู้สถานะปัจจุบัน (ไม่บังคับต้องมี)

ถ้า topic ใดไม่มี user journey หรือ architecture ให้ถามผู้ใช้ด้วย AskUserQuestion (อย่างน้อย 3 ตัวเลือก) เช่น:
- **หยุดรอ แนะนำให้รัน `/create-feature-journey` และ/หรือ `/create-architecture` ก่อน** — ข้อดี: operation และ table จะอ้างอิงขั้นตอน/entity จริงได้ครบถ้วน สอดคล้องกับภาพรวมระบบ / ข้อเสีย: ต้องรอ
- **ทำต่อโดยอิงจาก spec (และ feature list ถ้ามี) อย่างเดียว** — ข้อดี: ได้เห็น data contract เร็ว / ข้อเสีย: อาจไม่ครอบคลุมทุกขั้นตอนจริงหรือไม่ตรงกับภาพรวม architecture ต้องแก้ภายหลัง
- **ข้าม topic นี้ไปก่อน ทำเฉพาะ topic อื่นที่พร้อม** — ข้อดี: ไม่เสียเวลารอ / ข้อเสีย: topic นี้ไม่มีเอกสารนี้รอบนี้

### 3. เช็คว่าเป็นการเรียกซ้ำหรือไม่

สำหรับแต่ละ topic ใช้ Glob เช็คทั้งสองไฟล์แยกกัน:
- `docs/02-design/02-technical/*-{topic-slug}-api-spec.md`
- `docs/02-design/02-technical/*-{topic-slug}-database-spec.md`

แต่ละไฟล์พิจารณา action แยกกันได้ (เช่น อาจมี database spec อยู่แล้วแต่ยังไม่มี api spec):
- **ถ้ายังไม่มี**: เป็น action `create_new`
- **ถ้ามีอยู่แล้ว**: เป็น action `update_existing` (แก้ไขไฟล์เดิมในที่เดิม ไม่สร้างไฟล์ใหม่ซ้ำ) ให้ Read ไฟล์เดิมมาดูก่อนว่ามี operation/table อะไรอยู่แล้วบ้าง เพื่อไม่ให้ร่างซ้ำหรือขัดแย้งกับของเดิมโดยไม่ตั้งใจ ถ้าเนื้อหาที่จะเพิ่ม/แก้ขัดแย้งกับของเดิมอย่างมีนัยสำคัญ (เช่น requirement เปลี่ยนจนต้องรื้อ table หลักเดิม) ให้ถามผู้ใช้ด้วย AskUserQuestion ก่อนว่าต้องการแก้ไข/แทนที่ส่วนไหนบ้าง

### 4. อ่านเอกสารต้นทางแบบเต็ม

ใช้ Read อ่าน spec, feature list, user journey, architecture (เท่าที่มี) ของแต่ละ topic ที่จะทำให้ครบ

### 5. ร่าง Database Spec — Key Entities → Tables

จาก spec + architecture (ถ้ามี key conceptual entities อยู่แล้วให้ใช้เป็นฐาน ไม่ต้องคิดใหม่) แตกแต่ละ entity เป็น "ตารางเชิงแนวคิด" พร้อม:
- รายชื่อ field/attribute พร้อมชนิดข้อมูลเชิงแนวคิด (ข้อความ, ตัวเลข, วันที่-เวลา, ค่าจริง/เท็จ, รายการอ้างอิง ฯลฯ — **ห้ามระบุชนิดข้อมูลเฉพาะเอนจิ้น**)
- ระบุ field ที่จำเป็น (required), ค่าที่ต้องไม่ซ้ำ (unique), และ field ที่อ้างอิงไปยังตารางอื่น (foreign reference)
- ความสัมพันธ์ระหว่างตาราง (หนึ่งต่อหนึ่ง/หนึ่งต่อกลุ่ม/กลุ่มต่อกลุ่ม) พร้อมเหตุผลอ้างอิงจาก business rule ใน spec

### 6. ร่าง ER Diagram

วาด mermaid `erDiagram` ที่ครอบคลุมทุกตารางจากข้อ 5 พร้อมความสัมพันธ์ (ใช้ cardinality ให้ถูกต้องตาม business rule ใน spec)

### 7. ร่าง API Spec — Operations ตาม User Journey/Feature List

จาก user journey (ไล่ตามลำดับขั้นตอนของแต่ละ persona) และ feature list ระบุ "การดำเนินการ (operation)" เชิงแนวคิดที่ระบบต้องรองรับ ต่อแต่ละ operation ระบุ:
- ชื่อ operation และประเภทเชิงแนวคิด (สร้าง/อ่าน/แก้ไข/ลบ/ค้นหา-สอบถาม)
- จุดประสงค์ และอ้างอิงขั้นตอน/node id จาก user journey (ถ้ามี) หรือ feature จาก feature list
- ข้อมูลนำเข้าเชิงแนวคิด (conceptual input) — อ้างอิง field จาก table ในข้อ 5
- ข้อมูลส่งออกเชิงแนวคิด (conceptual output)
- เงื่อนไข/กติกาทางธุรกิจที่เกี่ยวข้อง (อ้างอิง business rule ใน spec) และกรณีผิดพลาดเชิงแนวคิดที่ควรพิจารณา (เช่น "ข้อมูลไม่ครบ", "ไม่พบรายการอ้างอิง", "ละเมิดกติกาทางธุรกิจ X")

**ห้ามระบุ HTTP method, URL path, status code เฉพาะเจาะจง, หรือรูปแบบ payload ใดๆ**

### 8. ถามเมื่อไม่ชัดเจน (บังคับ)

ทุกครั้งที่มีส่วนใดของ table/operation ที่ไม่ชัดเจนพอจะร่างได้ (เช่น ไม่รู้ว่า entity สองอย่างควรแยกตารางหรือรวมกัน, ไม่แน่ใจว่าความสัมพันธ์ควรเป็นหนึ่งต่อกลุ่มหรือกลุ่มต่อกลุ่ม, ไม่แน่ใจว่า operation หนึ่งควรแยกเป็นหลาย operation ย่อยหรือไม่) ให้ใช้ **AskUserQuestion** ถามกลับ โดยต้องมีตัวเลือกให้เลือก **อย่างน้อย 3 แนวทาง** เสมอ (ไม่นับ "Other" อัตโนมัติ) พร้อมข้อดี/ข้อเสียของแต่ละแนวทาง ส่วนรายละเอียดปลีกย่อยที่ไม่กระทบภาพรวม (เช่น ถ้อยคำอธิบาย) ใช้ดุลยพินิจได้โดยไม่ต้องถาม

### 9. เสนอแผนให้ผู้ใช้ยืนยันก่อนสร้างไฟล์จริง (บังคับทุกครั้ง)

ก่อนเรียก subagent เขียนไฟล์ ให้สรุปแผนเป็นข้อความให้ผู้ใช้อ่านทวนก่อนเสมอ ประกอบด้วย:

- topic ที่จะทำ และเอกสารต้นทางที่ใช้ต่อ topic (spec / feature list / user journey / architecture — ระบุว่ามีหรือขาดอะไร)
- action ต่อไฟล์ (api-spec / database-spec แยกกัน): `create_new` หรือ `update_existing` (พร้อม path เดิมถ้าเป็นกรณีหลัง)
- รายชื่อตารางที่ร่างไว้ พร้อมจำนวน field และความสัมพันธ์หลัก
- รายชื่อ operation ที่ร่างไว้ พร้อมประเภทและจุดประสงค์สั้นๆ

จากนั้นถามผู้ใช้ด้วย **AskUserQuestion**: "ดำเนินการตามแผนนี้หรือไม่" พร้อมตัวเลือกอย่างน้อย 3 แบบ เช่น:
- **ดำเนินการตามแผนนี้เลย** — เริ่มสร้าง/แก้ไขไฟล์ตามที่สรุปไว้ทันที
- **ขอปรับแผนก่อน** — ให้ผู้ใช้บอกจุดที่อยากแก้ แล้ววนกลับไปปรับแผนใหม่ (ย้อนไปข้อ 5-9) ก่อนถามยืนยันอีกครั้ง
- **ยกเลิก** — ไม่ต้องสร้างเอกสารนี้รอบนี้

**ห้ามเรียก subagent เขียนไฟล์ก่อนได้รับการยืนยัน "ดำเนินการตามแผนนี้เลย" อย่างชัดเจน**

### 10. มอบหมายให้ subagent `api-database-spec-writer` เขียนไฟล์จริง

เรียก Agent tool ด้วย `subagent_type: api-database-spec-writer` (รันแบบ foreground คือ `run_in_background: false` เพราะต้องรอผลมารายงานผู้ใช้ต่อ) โดย prompt ต้องมีข้อมูลครบ ไม่ทิ้งให้ subagent ต้องตัดสินใจเนื้อหาเอง:

ต่อแต่ละ topic:
- topic slug และชื่อเรื่อง
- path ของ spec / feature list / user journey / architecture ต้นทาง (เฉพาะที่มีจริง)
- action ของ api-spec และ database-spec แยกกัน: `create_new` หรือ `update_existing` (พร้อม path ของไฟล์เดิมถ้าเป็นกรณีหลัง)
- เนื้อหาฉบับสมบูรณ์: รายชื่อตารางทั้งหมดพร้อม field/ชนิดข้อมูล/constraint/ความสัมพันธ์, รายชื่อ operation ทั้งหมดพร้อมรายละเอียดตามข้อ 7 — ตัดสินใจสุดท้ายแล้วจากข้อ 5-9 ไม่ใช่ให้ subagent ร่างเอง
- วันที่วันนี้ (YYYYMMDD) — ดึงจาก system context ของ conversation

subagent จะเป็นผู้จัดการ: หา running number และตั้งชื่อไฟล์ (กรณี `create_new`), เขียน/แก้ไขไฟล์ database spec พร้อมวาด ER diagram, เขียน/แก้ไขไฟล์ api spec, เชื่อมลิงก์ระหว่างสองไฟล์และกลับไปยัง spec/feature-list/user-journey/architecture, อัปเดตสถานะใน `docs/01-requirements/backlog.md`, และเขียน log ที่ `docs/05-log/{YYYYMMDD}-log.md`

### 11. รายงานผลให้ผู้ใช้

สรุปให้ผู้ใช้ทราบว่าไฟล์อะไรถูกสร้าง/แก้ไขบ้าง (พร้อม path) และข้อสันนิษฐานใดๆ ที่ subagent ระบุมา เพื่อให้ผู้ใช้ตรวจสอบ/แก้ไขได้ทัน
