# 02 - Technical

เก็บเอกสาร **การออกแบบเชิงเทคนิค (Technical Design)** เช่น

- System architecture / โครงสร้างระบบโดยรวม
- Database schema
- API design / data contract
- เทคโนโลยีและไลบรารีที่เลือกใช้ พร้อมเหตุผล

เอกสารในโฟลเดอร์นี้คือพิมพ์เขียวที่ทีมพัฒนาใช้อ้างอิงตอนลงมือเขียนโค้ด และเป็นฐานในการวางแผนทดสอบใน [[../../03-testing/01-test-plan/index|01-test-plan]]

เอกสาร **High-Level Architecture** (`{YYYYMMDD}-{NN}-{topic-slug}-architecture.md`) เป็น conceptual/high-level เท่านั้น ไม่ผูกมัดกับ technical stack เฉพาะเจาะจง สร้าง/ปรับปรุงด้วย skill `/create-architecture` (ดู `CLAUDE.md` ที่ root) ส่วนการเลือกเทคโนโลยีจริง (database schema, API design ฯลฯ) เป็นเอกสารแยกต่างหากที่ต่อยอดจาก architecture นี้
