# CLAUDE.md

ไฟล์นี้ให้คำแนะนำแก่ Claude Code (claude.ai/code) เมื่อทำงานกับโค้ดในโปรเจกต์นี้

## ประเภทของ repository

repository นี้ **ไม่ใช่ codebase ของซอฟต์แวร์** แต่เป็น Obsidian vault ที่ใช้เก็บเอกสารโปรเจกต์ ไม่มี source code, build system, linter หรือชุดทดสอบ (test suite) เนื้อหาทั้งหมดเป็น Markdown และเอกสารที่มีอยู่ส่วนใหญ่เขียนเป็นภาษาไทย จึงไม่มีคำสั่งสำหรับ build, lint หรือ test งานในนี้คือการอ่าน สร้าง และเชื่อมโยง (cross-link) โน้ต Markdown ภายใต้ `docs/`

## โครงสร้างและ workflow

`docs/` ถูกจัดเรียงเป็น pipeline ตามลำดับหมายเลข สะท้อนวงจรชีวิตของโปรเจกต์ ตั้งแต่ requirements ไปจนถึง retrospective แต่ละ stage มีไฟล์ `index.md` อธิบายจุดประสงค์ (เป็นภาษาไทย) และเชื่อมโยงไปมาระหว่าง stage ที่เกี่ยวข้องด้วย syntax แบบ Obsidian `[[wikilink]]`:

- `01-requirements/` — ความต้องการของโปรเจกต์ แบ่งเป็น:
  - `01-spec/` — ข้อกำหนด/ต้นทาง (source of truth) เช่น feature requirements, user stories, business rules, scope
  - `02-plan/` — roadmap, milestone/phase, priority (แตกมาจาก `01-spec`)
  - `03-task/` — งานย่อยที่ลงมือทำได้จริง พร้อมสถานะ/ผู้รับผิดชอบ/deadline (แตกมาจาก `02-plan`)
- `02-design/` — งานออกแบบ แบ่งเป็น:
  - `01-prototypes/` — wireframe/mockup ของ UI/UX, user flow, design system เบื้องต้น
  - `02-technical/` — architecture, database schema, API/data contract, การเลือกเทคโนโลยี
- `03-testing/` — การทดสอบ แบ่งเป็น:
  - `01-test-plan/` — test case, test data, ขอบเขตการทดสอบ (อ้างอิงจาก spec + design)
  - `02-test-result/` — ผลการทดสอบจริง (pass/fail) และบั๊กที่พบ
- `04-retrospectives/` — สรุปบทเรียนหลังจบแต่ละ phase/sprint/milestone (สิ่งที่ทำได้ดี, สิ่งที่ควรปรับปรุง, action item) อ้างอิงจากผลทดสอบและ log
- `05-log/` — บันทึกความเคลื่อนไหวตามลำดับเวลา (changelog / decision log / เหตุการณ์สำคัญ) ใช้เป็นหลักฐานอ้างอิงตอนสรุปบทเรียน
- `00-archived/` — เอกสารที่เลิกใช้งานหรือถูกยกเลิกแล้ว **ห้ามลบเอกสารออกจาก vault โดยตรง ให้ย้ายมาไว้ที่นี่แทน** เพื่อรักษาประวัติการตัดสินใจ

## ข้อตกลง (Conventions) ที่ต้องปฏิบัติตาม

- ทุกโฟลเดอร์มี `index.md` อธิบายจุดประสงค์และลิงก์ไปยังโฟลเดอร์ที่เกี่ยวข้อง — ให้อ่าน `index.md` ของโฟลเดอร์นั้นก่อนเพิ่มเอกสารใหม่ และอัปเดตมันด้วยหากขอบเขตของโฟลเดอร์เปลี่ยนไป
- เชื่อมโยงเอกสารที่เกี่ยวข้องกันด้วย syntax wikilink แบบ Obsidian: `[[relative/path/index|ข้อความที่แสดง]]`
- รักษาลำดับการไหลของงานจากต้นน้ำถึงปลายน้ำเมื่อเพิ่มเนื้อหา: spec → plan → task → design (prototype → technical) → test plan → test result → retrospective โดยมี log คอยบันทึกการตัดสินใจระหว่างทาง เอกสารใหม่ควรอยู่ใน stage ที่มันควรอยู่ และลิงก์กลับไปยัง stage ที่เป็นต้นเหตุ
- ใช้ภาษาไทยตามธรรมเนียมเดิมของเอกสาร เว้นแต่ผู้ใช้จะระบุเป็นอย่างอื่น
- เมื่อเอกสารใดล้าสมัย ให้ย้ายไปไว้ใน `00-archived/` แทนการลบทิ้ง
