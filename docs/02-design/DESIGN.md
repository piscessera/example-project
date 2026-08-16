# DESIGN.md — Design System

> สร้างเมื่อ 2026-08-16

เอกสารนี้เป็น **design system กลาง** ของผลิตภัณฑ์ ครอบคลุมทั้ง 3 ส่วนของระบบที่อ้างอิงจาก [[../01-requirements/index|01-requirements]]:

1. หน้าสั่งกาแฟผ่าน QR code จากโต๊ะ (customer-facing, mobile web, ไม่ต้องติดตั้งแอพ) — [[../01-requirements/01-spec/20260802-01-cafe-table-self-order|spec]] / [[01-prototypes/20260815-01-cafe-table-self-order-user-journey|user journey]]
2. หน้า Sales Dashboard (เจ้าของร้าน/ผู้จัดการ, desktop-first) — [[../01-requirements/01-spec/20260802-02-sales-dashboard|spec]] / [[01-prototypes/20260815-02-sales-dashboard-user-journey|user journey]]
3. Logging & PDPA compliance (privacy notice, log ที่เจ้าของร้านเข้าถึงได้) — [[../01-requirements/01-spec/20260802-03-logging-pdpa-compliance|spec]] / [[01-prototypes/20260815-03-logging-pdpa-compliance-user-journey|user journey]]

เอกสารนี้เป็นต้นทาง (source of truth) ด้าน visual/UX ที่ [[02-technical/index|02-technical]] และการพัฒนาจริงควรอ้างอิงเมื่อ implement UI

---

## 1. Brand Identity & CI

### 1.1 โลโก้และความหมาย

โลโก้เป็นรูป **ใบไม้สีเขียวภายในวงกลม (leaf-in-circle)** — เส้นวงกลมสีเขียวล้อมรอบใบไม้ที่มีเส้นก้านโค้งพลิ้ว สื่อถึง:

- **ความสด/ธรรมชาติ** — เข้ากับธีมร้านกาแฟที่เน้นวัตถุดิบสดและบรรยากาศเป็นมิตรกับสิ่งแวดล้อม
- **ความเรียบง่าย** — เส้นน้อย ไม่ซับซ้อน สอดคล้องกับหลักการของโปรดักต์ที่ตั้งใจทำ "เท่าที่จำเป็น" (เช่น แนวทาง PDPA แบบ minimal ใน [[../01-requirements/01-spec/20260802-03-logging-pdpa-compliance|logging-pdpa-compliance spec]])
- **การเติบโต** — วงกลมเปิดที่ปลายใบสื่อถึงการเติบโต/ความต่อเนื่อง

### 1.2 Brand Personality

| ลักษณะ | คำอธิบาย |
|---|---|
| เป็นมิตร (Friendly) | โทนสีเขียวสด ฟอนต์โค้งมน ไม่ทางการจนเกินไป เหมาะกับลูกค้าทั่วไปที่นั่งสั่งจากโต๊ะ |
| น่าเชื่อถือ (Trustworthy) | โดยเฉพาะฝั่ง dashboard และหน้า privacy notice ต้องดูสะอาด เป็นระเบียบ อ่านง่าย เพื่อสร้างความมั่นใจเรื่องข้อมูล |
| เรียบง่าย ไม่ซับซ้อน (Minimal) | ไม่ใช้ decoration เกินจำเป็น สอดคล้องกับหลักการ "เก็บเท่าที่จำเป็น" ของทั้งโปรดักต์ |
| รวดเร็ว (Efficient) | ลูกค้าสแกน QR แล้วต้องสั่งอาหารได้เร็ว ไม่มี friction — UI ต้องโหลดไว ขั้นตอนสั้น |

### 1.3 การใช้งานโลโก้ (Logo Usage)

- **Clear space**: เว้นระยะรอบโลโก้อย่างน้อยเท่ากับความสูงของใบไม้ภายในวงกลม ไม่ให้มี element อื่นมาชิดขอบ
- **ขนาดต่ำสุด**: ไม่เล็กกว่า 24×24px (favicon/mobile) เพื่อให้เส้นใบไม้ยังคมชัด
- **พื้นหลัง**: ใช้บนพื้นขาว/พื้นสว่างเป็นหลัก หากใช้บนพื้นเข้มให้สลับเป็นเวอร์ชัน reverse (โลโก้สีขาว/เขียวอ่อน)
- **ห้าม**: บิดสัดส่วน, เปลี่ยนสีนอกเหนือจาก brand palette, ใส่เงา/gradient เพิ่มเติม, หมุนโลโก้

---

## 2. Design Tokens

### 2.1 Colors

โทนสีหลักดึงมาจากสีเขียวของโลโก้ (leaf green) ขยายเป็น scale สำหรับใช้งานจริงในระบบ

#### Primary — Leaf Green

| Token | Hex | การใช้งาน |
|---|---|---|
| `color.primary.50` | `#EAF7EB` | พื้นหลังอ่อนมาก เช่น background ของ success banner |
| `color.primary.100` | `#C8EAC9` | พื้นหลัง badge/tag สถานะ |
| `color.primary.200` | `#A3DCA6` | hover state บนพื้นอ่อน |
| `color.primary.300` | `#7DCE81` | border/divider แนวเขียว |
| `color.primary.400` | `#60C364` | icon accent |
| `color.primary.500` | `#4CAF50` | **Brand primary** — ปุ่มหลัก, ลิงก์, จุดเน้นหลักของ UI |
| `color.primary.600` | `#439246` | hover/active state ของปุ่มหลัก |
| `color.primary.700` | `#38763A` | text บนพื้นอ่อน (ต้องการ contrast สูงขึ้น) |
| `color.primary.800` | `#2C5A2E` | ใช้เฉพาะจุด เช่น header ของ dashboard |
| `color.primary.900` | `#1F3F20` | ใช้เฉพาะจุดที่ต้องการเน้นสุด (rare) |

#### Neutral (สำหรับ text, background, border — ใช้เยอะในฝั่ง dashboard/log)

| Token | Hex | การใช้งาน |
|---|---|---|
| `color.neutral.0` | `#FFFFFF` | พื้นหลังหลัก |
| `color.neutral.50` | `#F7F9F7` | พื้นหลังรอง (card บนพื้นขาว, section แยก) |
| `color.neutral.100` | `#EEF1EE` | พื้นหลัง table row สลับ, disabled background |
| `color.neutral.200` | `#DCE3DC` | border/divider ปกติ |
| `color.neutral.400` | `#98A498` | placeholder text, icon รอง |
| `color.neutral.600` | `#5B665A` | body text รอง (secondary text) |
| `color.neutral.800` | `#2B332A` | body text หลัก (primary text) |
| `color.neutral.900` | `#181D18` | heading text ที่ต้องการ contrast สูงสุด |

#### Semantic Colors

| Token | Hex | การใช้งาน |
|---|---|---|
| `color.success` | `#2E9E5B` | ยืนยันออเดอร์สำเร็จ, สถานะ "เสิร์ฟแล้ว" — แยกจาก primary เล็กน้อยเพื่อไม่ให้ปนกับสี brand |
| `color.warning` | `#F5A623` | แจ้งเตือนที่ต้องระวัง เช่น ออเดอร์รอนาน, log ใกล้ครบกำหนด retention |
| `color.error` | `#E5484D` | error state, system log ระดับ error, ฟอร์มกรอกผิด |
| `color.info` | `#3B82F6` | ข้อความแจ้งข้อมูลทั่วไป เช่น privacy notice, ข้อจำกัดของข้อมูล (เช่น "ยอดออเดอร์ ไม่ใช่ยอดเงินสดที่กระทบยอดแล้ว") |

> **กฎการใช้สี**: ใช้ `color.primary.500` เป็นสีเน้นหลักของปุ่ม/action สำคัญเท่านั้น ไม่ใช้สี primary กับ status badge ทั่วไป (ให้ใช้ semantic colors แทน) เพื่อไม่ให้ผู้ใช้สับสนระหว่าง "แบรนด์" กับ "สถานะ"

### 2.2 Typography

- **Heading font**: `Prompt` (Google Font, รองรับภาษาไทย) — โค้งมน เป็นมิตร เหมาะกับหัวข้อ/ปุ่ม
- **Body font**: `Sarabun` (Google Font, รองรับภาษาไทย) — อ่านง่ายในขนาดเล็ก เหมาะกับตัวเลข/ตารางใน dashboard และข้อความ privacy notice ที่ต้องอ่านชัดเจน
- **Fallback**: `-apple-system, "Segoe UI", Roboto, sans-serif`

| Token | Font | Size / Line-height | Weight | ใช้กับ |
|---|---|---|---|---|
| `type.display` | Prompt | 32px / 40px | 700 | หัวข้อหน้าหลัก (ไม่ใช้บ่อย) |
| `type.h1` | Prompt | 28px / 36px | 700 | ชื่อหน้า เช่น "เมนู", "Sales Dashboard" |
| `type.h2` | Prompt | 24px / 32px | 600 | หัวข้อ section |
| `type.h3` | Prompt | 20px / 28px | 600 | หัวข้อย่อย, ชื่อ card |
| `type.body-lg` | Sarabun | 16px / 24px | 400 | เนื้อหาหลัก, ชื่อเมนูอาหาร |
| `type.body` | Sarabun | 14px / 22px | 400 | ตัวหนังสือทั่วไป, ตาราง |
| `type.caption` | Sarabun | 12px / 18px | 400 | label, timestamp, privacy notice, log detail |
| `type.numeric` | Sarabun (tabular figures) | ตาม context | 600 | ตัวเลขยอดขาย/ราคา — ใช้ tabular-nums เพื่อให้ตัวเลขเรียงคอลัมน์ตรงกันในตาราง |

**กฎการใช้ font**: ข้อความภาษาไทยห้ามใช้ font ที่ไม่รองรับวรรณยุกต์/สระ (เช่น system font บางตัวบน Windows เก่า) ให้ fallback ไปที่ `Sarabun`/`Prompt` เสมอสำหรับข้อความภาษาไทย

### 2.3 Spacing

ใช้ grid แบบ 4px เป็นฐาน

| Token | Value |
|---|---|
| `space.1` | 4px |
| `space.2` | 8px |
| `space.3` | 12px |
| `space.4` | 16px |
| `space.6` | 24px |
| `space.8` | 32px |
| `space.12` | 48px |
| `space.16` | 64px |

### 2.4 Radius & Shadow

| Token | Value | ใช้กับ |
|---|---|---|
| `radius.sm` | 4px | input, checkbox |
| `radius.md` | 8px | ปุ่ม, card ทั่วไป |
| `radius.lg` | 12px | card เมนูอาหาร, modal |
| `radius.full` | 999px | badge, tag, avatar |
| `shadow.sm` | `0 1px 2px rgba(24,29,24,0.06)` | card บนพื้นราบ |
| `shadow.md` | `0 4px 12px rgba(24,29,24,0.10)` | card ที่ลอยขึ้นมา เช่น cart summary |
| `shadow.lg` | `0 8px 24px rgba(24,29,24,0.14)` | modal, dialog, dropdown |

### 2.5 Breakpoints

| Token | Range | บริบท |
|---|---|---|
| `breakpoint.mobile` | < 576px | หน้าสั่งอาหาร QR (หลัก) |
| `breakpoint.tablet` | 576–991px | หน้าสั่งอาหาร QR (แนวนอน), dashboard บน tablet |
| `breakpoint.desktop` | ≥ 992px | Sales Dashboard (หลัก) |

---

## 3. UI Components & Patterns

### 3.1 Buttons

| Variant | การใช้งาน | สี |
|---|---|---|
| Primary | action หลัก เช่น "ยืนยันคำสั่งซื้อ", "เพิ่มลงตะกร้า" | พื้นหลัง `color.primary.500`, text ขาว, hover `color.primary.600` |
| Secondary | action รอง เช่น "ยกเลิก", "กลับไปเมนู" | พื้นหลังโปร่งใส, border `color.primary.500`, text `color.primary.700` |
| Ghost/Text | action เล็กน้อย เช่น "ดูรายละเอียด" ใน dashboard | ไม่มี border/พื้นหลัง, text `color.primary.700`, underline เมื่อ hover |
| Destructive | ไม่ใช้บ่อยในระบบนี้ (ไม่มี delete โดยผู้ใช้ทั่วไป) — สงวนไว้สำหรับ admin action เช่น ลบ log ก่อนกำหนด | พื้นหลัง `color.error` |

**ขนาดปุ่มขั้นต่ำ**: สูง 44px บนหน้าจอมือถือ (ตาม guideline touch target — ดูหัวข้อ UX Guidelines) และ 36px บน dashboard desktop

### 3.2 Menu Item Card (หน้าสั่งอาหาร QR)

- ภาพเมนู (อัตราส่วน 1:1 หรือ 4:3), ชื่อเมนู (`type.body-lg`), ราคา (`type.numeric`)
- ปุ่ม "+" แบบวงกลมมุมขวาล่างของรูปเพื่อเพิ่มลงตะกร้าโดยไม่ต้องเข้าไปหน้ารายละเอียด
- radius `radius.lg`, shadow `shadow.sm`

### 3.3 Status Badge / Tag

ใช้แสดงสถานะออเดอร์ (รอยืนยัน/กำลังทำ/เสิร์ฟแล้ว) และสถานะ log (active/expired)

- รูปทรง pill (`radius.full`), padding `space.1` × `space.3`, font `type.caption` weight 600
- สีพื้นหลังใช้ semantic color เวอร์ชันอ่อน (เช่น `color.primary.100` + text `color.primary.700` สำหรับ "เสิร์ฟแล้ว", `color.warning` โทนอ่อน + text เข้มสำหรับ "รอดำเนินการ")

### 3.4 Data Table (Sales Dashboard, Log Viewer)

- Header row: พื้นหลัง `color.neutral.50`, text `color.neutral.800` weight 600, sticky เมื่อ scroll
- แถวสลับสี: `color.neutral.0` / `color.neutral.100` (zebra striping) เพื่อให้อ่านแถวยาวๆ ง่ายขึ้น โดยเฉพาะตาราง log
- ตัวเลข (ยอดขาย, จำนวน) ชิดขวา ใช้ `type.numeric`
- คอลัมน์ timestamp ใช้รูปแบบ `DD/MM/YYYY HH:mm` (พ.ศ. หรือ ค.ศ. ให้เลือกใช้ตามความคุ้นเคยของเจ้าของร้าน แต่ต้องสอดคล้องกันทั้งระบบ)

### 3.5 Chart (แนวโน้มยอดขายรายวัน)

- เส้นกราฟหลักใช้ `color.primary.500`, พื้นที่ใต้เส้น (area fill) ใช้ `color.primary.500` opacity 12%
- แกน/label ใช้ `color.neutral.400`
- จุดข้อมูล (data point) แสดง tooltip เมื่อ hover/tap พร้อมตัวเลขยอดขายแบบเต็ม

### 3.6 Preset Time Range Selector

- ปุ่มกลุ่ม (segmented control) สำหรับ "วันนี้ / สัปดาห์นี้ / เดือนนี้" — ปุ่มที่เลือกอยู่ใช้พื้นหลัง `color.primary.500` text ขาว ปุ่มอื่น text `color.neutral.600`

### 3.7 Privacy Notice Banner

component เฉพาะสำหรับ [[../01-requirements/01-spec/20260802-03-logging-pdpa-compliance|logging-pdpa-compliance]] — แสดงบนหน้าสั่งอาหาร QR

- แถบข้อความ (ไม่ใช่ modal, ไม่บล็อกการใช้งาน) วางไว้ท้ายหน้าหรือใต้ header แบบ dismissible เบาๆ
- พื้นหลัง `color.neutral.50`, ไอคอน info (`color.info`), text `type.caption` สี `color.neutral.600`
- **ห้ามมีปุ่ม "ยอมรับ/ปฏิเสธ"** เพราะเป็น implicit notice ตาม business rule ของ spec ไม่ใช่ explicit consent — มีได้แค่ปุ่มปิด (dismiss) เพื่อซ่อนแถบ ไม่ใช่ปุ่มยินยอม

### 3.8 Toast / Inline Feedback

- ใช้ toast แบบสั้น (auto-dismiss 3 วินาที) สำหรับ feedback ที่ไม่ block เช่น "เพิ่มลงตะกร้าแล้ว"
- ใช้ inline message (ไม่ใช่ toast) สำหรับข้อผิดพลาดที่ต้องการให้ผู้ใช้แก้ไข เช่น ฟอร์มไม่ครบ

### 3.9 Navigation

- **หน้าสั่งอาหาร (customer)**: ไม่มี navigation bar ซับซ้อน — floating cart summary bar ด้านล่างจอ (sticky bottom) แสดงจำนวนรายการ + ยอดรวม + ปุ่ม "ดูตะกร้า"
- **Sales Dashboard**: sidebar ซ้าย (desktop) หรือ top tab (tablet) พื้นหลัง `color.neutral.900`, item ที่ active ใช้ text `color.primary.400`

---

## 4. UX Guidelines & Rules

### 4.1 Mobile-first สำหรับหน้าสั่งอาหาร

- ลูกค้าสแกน QR แล้วต้องเห็นเมนูภายใน 1 tap ไม่มีหน้า login/signup คั่น (สอดคล้องกับ business rule "ไม่ต้องติดตั้งแอพ" ใน [[../01-requirements/01-spec/20260802-01-cafe-table-self-order|spec]])
- ทุก interactive element ต้องมี touch target ≥ 44×44px เพื่อรองรับการใช้งานด้วยนิ้วบนมือถือ
- ออกแบบให้ใช้งานได้ด้วยมือเดียว (thumb zone) — action สำคัญ (ยืนยันคำสั่งซื้อ) วางไว้ครึ่งล่างของจอ

### 4.2 Desktop-first สำหรับ Sales Dashboard

- เจ้าของร้าน/ผู้จัดการใช้งานผ่านคอมพิวเตอร์เป็นหลัก ออกแบบ layout ให้ใช้พื้นที่แนวนอนแสดงกราฟ+ตารางพร้อมกันได้ แต่ต้อง responsive ลงไปถึง tablet เป็นอย่างน้อย
- ต้องระบุข้อจำกัดของข้อมูลให้ชัดเจนบนหน้าจอ (เช่น "ยอดขายนี้คือยอดออเดอร์ ไม่ใช่ยอดเงินสดที่กระทบยอดแล้ว" ตาม business rule ของ [[../01-requirements/01-spec/20260802-02-sales-dashboard|sales-dashboard spec]]) โดยใช้ inline note สี `color.info` ใต้ตัวเลขสรุป ไม่ใช่ tooltip ที่ต้อง hover ถึงจะเห็น

### 4.3 ความชัดเจนเรื่อง Privacy/Logging

- ข้อความ privacy notice ต้องอ่านเข้าใจได้ในสายตาแรก ไม่ใช้ศัพท์กฎหมาย — บอกตรงๆ ว่าเก็บอะไร (เวลาที่สั่ง, IP address) เพื่ออะไร (การดำเนินงานและความปลอดภัย)
- หน้าจอที่แสดง log (access log, error log) เป็นพื้นที่จำกัดสิทธิ์เฉพาะเจ้าของร้าน/ผู้ดูแลระบบ — ต้อง visually แยกให้ชัดจาก dashboard ทั่วไป (เช่น ใส่ label "ข้อมูลจำกัดการเข้าถึง" กำกับ) แม้เวอร์ชันนี้ยังไม่มีระบบ login จริง
- แสดงอายุของ log หรือวันที่จะถูกลบ (ตาม retention policy 90 วัน) ในหน้า log viewer เพื่อความโปร่งใส

### 4.4 Accessibility

- อัตราส่วน contrast ระหว่างข้อความกับพื้นหลังต้องผ่านเกณฑ์ WCAG AA (≥ 4.5:1 สำหรับ body text, ≥ 3:1 สำหรับ text ขนาดใหญ่/heading)
- ห้ามสื่อความหมายด้วยสีเพียงอย่างเดียว (เช่น status badge ต้องมี label ข้อความกำกับ ไม่ใช่ใช้แค่สี)
- รองรับ font-size scaling ของ browser/OS โดย layout ไม่พัง

### 4.5 Performance & Network

- ร้านกาแฟอาจมี wifi ที่ไม่เสถียร — หน้าสั่งอาหารต้องมี loading state ที่ชัดเจนทุกจุดที่รอ network (เพิ่มลงตะกร้า, ยืนยันคำสั่งซื้อ) และแจ้ง error แบบเข้าใจง่ายเมื่อเชื่อมต่อไม่ได้ พร้อมปุ่ม "ลองใหม่"
- ภาพเมนูอาหารต้อง optimize ขนาดไฟล์ให้โหลดเร็วบน mobile data/wifi ร้าน

### 4.6 ภาษาและการแสดงตัวเลข

- ภาษาไทยเป็นภาษาหลักของ UI ทั้งหมด (ตามธรรมเนียมเอกสารในโปรเจกต์)
- แสดงราคาด้วยหน่วย "บาท" หรือสัญลักษณ์ "฿" อย่างสม่ำเสมอทั้งระบบ, ใช้ทศนิยม 2 ตำแหน่งเมื่อจำเป็น
- ตัวเลขในตาราง/กราฟใช้ thousand separator (เช่น 1,250) เพื่อให้อ่านยอดขายได้ง่าย

### 4.7 Empty & Error States

- หน้า dashboard เมื่อยังไม่มีออเดอร์ในช่วงเวลาที่เลือก ต้องแสดง empty state ที่บอกสาเหตุชัดเจน (เช่น "ยังไม่มีออเดอร์ในช่วงเวลานี้") ไม่ใช่กราฟ/ตารางว่างเปล่าเฉยๆ
- ข้อความ error ต้องบอกสิ่งที่เกิดขึ้นและสิ่งที่ผู้ใช้ทำได้ต่อ ไม่ใช้ error code ดิบๆ ให้ผู้ใช้ทั่วไปเห็น

---

## เอกสารที่เกี่ยวข้อง

- [[index|02-design]] — ภาพรวมของ stage การออกแบบ
- [[01-prototypes/index|01-prototypes]] — user journey ที่ design system นี้นำไปใช้ออกแบบหน้าจอจริง
- [[02-technical/index|02-technical]] — ที่ implement design tokens เป็นโค้ดจริง (เช่น CSS variables, component library)
- [[../01-requirements/index|01-requirements]] — ต้นทาง requirement ของทั้ง 3 ฟีเจอร์ที่ design system นี้ครอบคลุม
