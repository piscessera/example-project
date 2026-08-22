# 02 - Technical

เก็บเอกสาร **การออกแบบเชิงเทคนิค (Technical Design)** เช่น

- System architecture / โครงสร้างระบบโดยรวม
- Database schema
- API design / data contract
- เทคโนโลยีและไลบรารีที่เลือกใช้ พร้อมเหตุผล

เอกสารในโฟลเดอร์นี้คือพิมพ์เขียวที่ทีมพัฒนาใช้อ้างอิงตอนลงมือเขียนโค้ด และเป็นฐานในการวางแผนทดสอบใน [[../../03-testing/01-test-plan/index|01-test-plan]]

เอกสาร **High-Level Architecture** (`{YYYYMMDD}-{NN}-{topic-slug}-architecture.md`) เป็น conceptual/high-level เท่านั้น ไม่ผูกมัดกับ technical stack เฉพาะเจาะจง สร้าง/ปรับปรุงด้วย skill `/create-architecture` (ดู `CLAUDE.md` ที่ root) ส่วนการเลือกเทคโนโลยีจริง (database schema, API design ฯลฯ) เป็นเอกสารแยกต่างหากที่ต่อยอดจาก architecture นี้

เอกสาร **API Spec** และ **Database Spec** (`{YYYYMMDD}-{NN}-{topic-slug}-api-spec.md`, `...-database-spec.md`) เป็น conceptual เช่นกัน (ไม่ผูกมัดกับ HTTP method, payload format, หรือ database engine เฉพาะเจาะจง) สร้าง/ปรับปรุงด้วย skill `/create-api-database-spec`

เอกสาร **Detailed Design** (`{YYYYMMDD}-{NN}-{topic-slug}-detailed-design.md`) ต่อยอดจาก architecture และ API/Database spec ข้างต้น ลงรายละเอียดลำดับขั้นตอนการทำงานภายในของแต่ละ operation แบบ conceptual โดยมี sequence flow (mermaid `sequenceDiagram`) เป็นอย่างน้อยต่อทุก operation/สถานการณ์หลัก สร้าง/ปรับปรุงด้วย skill `/create-detailed-design`
