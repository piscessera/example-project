# CLAUDE.md

ไฟล์นี้ให้คำแนะนำแก่ Claude Code (claude.ai/code) เมื่อทำงานกับโค้ดในโปรเจกต์นี้

## ประเภทของ repository

repository นี้ **ไม่ใช่ codebase ของซอฟต์แวร์** แต่เป็น Obsidian vault ที่ใช้เก็บเอกสารโปรเจกต์ ไม่มี source code, build system, linter หรือชุดทดสอบ (test suite) เนื้อหาส่วนใหญ่เป็น Markdown และเอกสารที่มีอยู่ส่วนใหญ่เขียนเป็นภาษาไทย จึงไม่มีคำสั่งสำหรับ build, lint หรือ test งานในนี้คือการอ่าน สร้าง และเชื่อมโยง (cross-link) โน้ต Markdown ภายใต้ `docs/`

**ข้อยกเว้นเดียว**: version folder ของ prototype ที่ `docs/02-design/01-prototypes/{topic-slug}/v{NN}-{YYYYMMDD}/` อาจมีไฟล์ `prototype.html` อยู่คู่กับ `index.md` — เป็น interactive prototype แบบ self-contained ไฟล์เดียว (inline CSS/JS ทั้งหมด ไม่โหลดทรัพยากรภายนอก ไม่มี build step) ที่ skill `/create-prototype` สร้างให้เพื่อ demo ให้คลิกโต้ตอบได้จริง ไม่ถือเป็น source code ของผลิตภัณฑ์จริง เป็นเพียงเอกสารประกอบการรีวิวเช่นเดียวกับ wireframe

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

## Automation สำหรับสร้าง requirement

โปรเจกต์นี้มี skill `/create-requirement` (`.claude/skills/create-requirement/SKILL.md`) สำหรับรับ requirement ดิบจากผู้ใช้แล้วแปลงเป็นเอกสารใน `docs/01-requirements/01-spec/`, อัปเดต `docs/01-requirements/backlog.md`, และบันทึกสรุปที่ `docs/05-log/{YYYYMMDD}-log.md` skill นี้จะถามผู้ใช้กลับ (พร้อมตัวเลือกอย่างน้อย 3 แนวทาง) เมื่อเนื้อหาไม่ชัดเจน แล้วมอบหมายงานเขียนไฟล์จริงให้ subagent `requirement-writer` (`.claude/agents/requirement-writer.md`) ใช้ skill นี้แทนการสร้างเอกสาร requirement ด้วยมือ

## Automation สำหรับสร้าง feature list และ user journey

โปรเจกต์นี้มี skill `/create-feature-journey` (`.claude/skills/create-feature-journey/SKILL.md`) สำหรับรับ topic ที่มี requirement spec อยู่แล้วใน `docs/01-requirements/01-spec/` มาแตกเป็น feature list แบบ MoSCoW ใน `docs/01-requirements/02-plan/` และ user journey แบบ mermaid flowchart (พร้อมตารางอธิบายใต้กราฟ) ใน `docs/02-design/01-prototypes/` ที่เชื่อมโยงกันเอง, อัปเดตสถานะใน `docs/01-requirements/backlog.md`, และบันทึกสรุปที่ `docs/05-log/{YYYYMMDD}-log.md` skill นี้ทำงานต่อยอดจากเอกสารที่มีอยู่เท่านั้น (ถ้ายังไม่มี spec ของ topic นั้นจะแนะนำให้รัน `/create-requirement` ก่อน) และจะถามผู้ใช้กลับเมื่อเนื้อหาไม่ชัดเจน แล้วมอบหมายงานเขียนไฟล์จริงให้ subagent `feature-journey-writer` (`.claude/agents/feature-journey-writer.md`) ใช้ skill นี้แทนการสร้างเอกสารเหล่านี้ด้วยมือ

## Automation สำหรับสร้าง prototype

โปรเจกต์นี้มี skill `/create-prototype` (`.claude/skills/create-prototype/SKILL.md`) สำหรับรวบรวม Requirement, Backlog, Feature List, และ User Journey ที่มีอยู่แล้ว (ทุก topic หรือระบุเจาะจงเฉพาะ topic ก็ได้) มาสร้าง UI/UX prototype ที่อ้างอิง `docs/02-design/DESIGN.md` โดยแต่ละเวอร์ชันได้ผลลัพธ์ 2 ไฟล์เสมอ: wireframe แบบ text-based พร้อม component/token mapping (`index.md`) และ interactive prototype แบบ HTML/CSS/JS ไฟล์เดียวที่คลิกโต้ตอบได้จริง (`prototype.html`) เก็บเป็น **Folder Version** ใน `docs/02-design/01-prototypes/{topic-slug}/v{NN}-{YYYYMMDD}/` แล้วเผยแพร่ `prototype.html` เป็น Claude Artifact ให้ผู้ใช้พรีวิวทันทีหลังสร้างเสร็จ ถ้ายังไม่มี `DESIGN.md` skill จะถามผู้ใช้ก่อนเพื่อสร้างขึ้น (เลือกโทนสี/สไตล์ หรือส่งภาพโลโก้ตัวอย่าง) เมื่อเรียกซ้ำและพบว่า topic นั้นมี prototype อยู่แล้ว skill จะถามผู้ใช้ทุกครั้งว่าจะสร้าง folder version ใหม่หรือแก้ไข version ล่าสุด (พร้อมคำแนะนำว่าควรเลือกแบบไหน) และก่อนลงมือสร้างไฟล์จริงจะสรุปแผนให้ผู้ใช้ยืนยันก่อนเสมอ แล้วมอบหมายงานเขียนไฟล์จริงให้ subagent `prototype-writer` (`.claude/agents/prototype-writer.md`) ใช้ skill นี้แทนการสร้างเอกสาร prototype ด้วยมือ

## Automation สำหรับสร้าง high-level architecture

โปรเจกต์นี้มี skill `/create-architecture` (`.claude/skills/create-architecture/SKILL.md`) สำหรับรับ topic ที่มี requirement spec และ user journey อยู่แล้วใน `docs/01-requirements/01-spec/` และ `docs/02-design/01-prototypes/` มาสร้างเอกสาร High-Level Architecture แบบ **conceptual** (ไม่ผูกมัดกับ technical stack เฉพาะเจาะจง เช่น ภาษาโปรแกรม, framework, database engine) ประกอบด้วย conceptual component, แผนภาพองค์ประกอบ, data flow ตามลำดับขั้นตอนใน user journey, ข้อมูลหลักที่ระบบต้องจัดการ, และข้อกังวลเชิงคุณภาพระดับแนวคิด ลงในไฟล์เดียวที่ `docs/02-design/02-technical/{YYYYMMDD}-{NN}-{topic-slug}-architecture.md` skill นี้ทำงานต่อยอดจากเอกสารที่มีอยู่เท่านั้น (ถ้ายังไม่มี spec หรือ user journey ของ topic นั้นจะแนะนำให้รัน `/create-requirement` และ/หรือ `/create-feature-journey` ก่อน) และจะถามผู้ใช้กลับพร้อมตัวเลือกอย่างน้อย 3 แนวทางเมื่อเนื้อหาไม่ชัดเจน เมื่อเรียกซ้ำและพบว่า topic นั้นมี architecture document อยู่แล้วจะแก้ไขไฟล์เดิมในที่เดิม (ไม่สร้างไฟล์ซ้ำ) และก่อนลงมือสร้างไฟล์จริงจะสรุปแผนให้ผู้ใช้ยืนยันก่อนเสมอ แล้วมอบหมายงานเขียนไฟล์จริงให้ subagent `architecture-writer` (`.claude/agents/architecture-writer.md`) ใช้ skill นี้แทนการสร้างเอกสาร architecture ด้วยมือ

## Automation สำหรับสร้าง API spec และ database spec

โปรเจกต์นี้มี skill `/create-api-database-spec` (`.claude/skills/create-api-database-spec/SKILL.md`) สำหรับรับ topic ที่มี requirement spec อยู่แล้วใน `docs/01-requirements/01-spec/` (และ feature list/user journey/architecture ถ้ามี) มาสร้างเอกสาร API Spec และ Database Spec แบบ **conceptual** (ไม่ผูกมัดกับ technical stack เฉพาะเจาะจง เช่น ภาษาโปรแกรม, framework, database engine, รูปแบบ API เฉพาะ) โดย Database Spec ประกอบด้วย ER Diagram แบบ mermaid และรายละเอียดแต่ละตาราง (field, ชนิดข้อมูลเชิงแนวคิด, constraint, ความสัมพันธ์) ส่วน API Spec ประกอบด้วยรายการการดำเนินการ (operation) เชิงแนวคิดพร้อมข้อมูลนำเข้า/ส่งออกและกติกาทางธุรกิจ เก็บไว้ที่ `docs/02-design/02-technical/{YYYYMMDD}-{NN}-{topic-slug}-database-spec.md` และ `docs/02-design/02-technical/{YYYYMMDD}-{NN}-{topic-slug}-api-spec.md` ตามลำดับ (เชื่อมโยงกันเอง) skill นี้ทำงานต่อยอดจากเอกสารที่มีอยู่เท่านั้น (ถ้ายังไม่มี spec ของ topic นั้นจะแนะนำให้รัน `/create-requirement` และ/หรือ `/create-feature-journey` ก่อน) และจะถามผู้ใช้กลับพร้อมตัวเลือกอย่างน้อย 3 แนวทางเมื่อเนื้อหาไม่ชัดเจน เมื่อเรียกซ้ำและพบว่า topic นั้นมีเอกสารใดอยู่แล้วจะแก้ไขไฟล์เดิมในที่เดิม (ไม่สร้างไฟล์ซ้ำ) และก่อนลงมือสร้างไฟล์จริงจะสรุปแผนให้ผู้ใช้ยืนยันก่อนเสมอ แล้วมอบหมายงานเขียนไฟล์จริงให้ subagent `api-database-spec-writer` (`.claude/agents/api-database-spec-writer.md`) ใช้ skill นี้แทนการสร้างเอกสาร API/database spec ด้วยมือ

## Automation สำหรับสร้าง test plan

โปรเจกต์นี้มี skill `/create-test-plan` (`.claude/skills/create-test-plan/SKILL.md`) สำหรับรวบรวม Requirement Spec, Backlog, Feature List, User Journey (และ Prototype ถ้ามี) ที่มีอยู่แล้ว (ทุก topic หรือระบุเจาะจงเฉพาะ topic ก็ได้) มาสร้าง Acceptance Criteria แบบ Given-When-Then, ขอบเขตการทดสอบ, test data, และตาราง test case ลงในไฟล์เดียวที่ `docs/03-testing/01-test-plan/{YYYYMMDD}-{NN}-{topic-slug}-test-plan.md` skill นี้ทำงานต่อยอดจากเอกสารที่มีอยู่เท่านั้น (ถ้ายังไม่มี spec ของ topic นั้นจะแนะนำให้รัน `/create-requirement` ก่อน) เมื่อเรียกซ้ำและพบว่า topic นั้นมี test plan อยู่แล้วจะแก้ไขไฟล์เดิมในที่เดิม (ไม่สร้างไฟล์ซ้ำ) และก่อนลงมือสร้างไฟล์จริงจะสรุปแผนให้ผู้ใช้ยืนยันก่อนเสมอ แล้วมอบหมายงานเขียนไฟล์จริงให้ subagent `test-plan-writer` (`.claude/agents/test-plan-writer.md`) ใช้ skill นี้แทนการสร้างเอกสาร test plan ด้วยมือ
