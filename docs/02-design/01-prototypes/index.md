# 01 - Prototypes

เก็บ **ต้นแบบหน้าตาของระบบ (UI/UX Prototype)** เช่น

- Wireframe / mockup ของแต่ละหน้าจอ
- User flow และ navigation flow
- Design system เบื้องต้น เช่น สี ฟอนต์ คอมโพเนนต์หลัก

ใช้สำหรับสื่อสารและตกลงหน้าตาของระบบก่อนลงมือพัฒนาจริง โดยอ้างอิงความต้องการจาก [[../../01-requirements/01-spec/index|01-spec]] และส่งต่อรายละเอียดเชิงระบบให้ [[../02-technical/index|02-technical]]

## โครงสร้างไฟล์

- ไฟล์ `{YYYYMMDD}-{NN}-{topic-slug}-user-journey.md` ที่อยู่ตรงนี้โดยตรง — user journey แบบ mermaid flowchart ต่อ topic (สร้างโดย skill `/create-feature-journey`)
- โฟลเดอร์ `{topic-slug}/` ต่อ topic — เก็บ UI/UX prototype (wireframe แบบ text-based พร้อม component mapping อ้างอิง [[../DESIGN.md|DESIGN.md]]) แบ่งเป็น **Folder Version** ย่อย `v{NN}-{YYYYMMDD}/` เพื่อเก็บประวัติการเปลี่ยนแปลง prototype ของ topic นั้น โดยมี `index.md` ของ topic คอยสรุปว่าเวอร์ชันไหนล่าสุด (สร้าง/แก้ไขโดย skill `/create-prototype`)
