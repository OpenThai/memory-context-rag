# ความปลอดภัยและความเป็นส่วนตัวในระบบ Memory, Context, และ RAG

> แนวทางการป้องกันความเสี่ยงด้านความปลอดภัยและความเป็นส่วนตัว

---

## ความเสี่ยงหลัก

### 1. ข้อมูลส่วนบุคคลรั่วไหล (Personal Data Risk)

Memory system เก็บข้อมูลผู้ใช้ — ถ้าไม่ป้องกันอาจรั่วไหล

**แนวทางป้องกัน:**
- อย่าเก็บข้อมูล sensitive โดยไม่จำเป็น
- ใช้ automatic redaction สำหรับ PII (ชื่อ, เบอร์โทร, อีเมล, ที่อยู่)
- เข้ารหัส memory ที่เก็บใน database
- กำหนด access control ให้ชัดเจน

### 2. Sensitive Memory Risk

ข้อมูลที่ Agent จำไว้อาจถูกเรียกใช้โดยไม่ตั้งใจใน context ที่ไม่เหมาะสม

**แนวทางป้องกัน:**
- แยก user memory, project memory, system state
- ให้ผู้ใช้เลือกได้ว่า memory ไหน sensitive
- มี warning เมื่อ memory ที่ sensitive ถูก retrieve

### 3. Prompt Injection ผ่าน Retrieved Documents

Attacker อาจแทรกคำสั่งอันตรายใน document ที่检索มา

**แนวทางป้องกัน:**
- แยก retrieved content ออกจาก system instructions
- ไม่ trust retrieved content โดยตรง
- ตรวจสอบ content ก่อนใส่ context
- ใช้ sandboxing สำหรับ document content

### 4. Stale Memory — ข้อมูลที่ล้าสมัย

Agent ตอบโดยใช้ memory ที่ไม่ถูกต้องอีกต่อไป

**แนวทางป้องกัน:**
- timestamp ทุก memory
- ตรวจสอบ freshness ก่อนใช้
- ให้ผู้ใช้แก้ไขหรือลบ memory ได้
- มี mechanism สำหรับ memory decay

### 5. Wrong Memory — ข้อมูลที่บันทึกผิด

Agent อาจตีความผิดและบันทึก memory ที่ไม่ถูกต้อง

**แนวทางป้องกัน:**
- human review สำหรับ memory ที่สำคัญ
- ให้ผู้ใช้ยืนยันก่อนบันทึก
- editable memory
- confidence score ใน memory

### 6. Over-retrieval

Retrieve ข้อมูลมากเกินความจำเป็น — เพิ่มความเสี่ยงข้อมูลรั่วไหลและ waste tokens

**แนวทางป้องกัน:**
- retrieve เฉพาะที่เกี่ยวข้องกับ query
- จำกัดจำนวน documents ต่อ query
- ใช้ metadata filters

### 7. Data Leakage

ข้อมูลรั่วไหลผ่านระบบ — context ที่มีข้อมูลผู้ใช้อาจถูกส่งไปยัง third-party

**แนวทางป้องกัน:**
- local-first design
- อย่าส่งข้อมูลที่ไม่จำเป็นไปยัง LLM API
- anonymize หรือ redact ข้อมูลก่อนส่ง
- audit log การเข้าถึงข้อมูล

### 8. Access Control

ใครเข้าถึง memory ของใครได้บ้าง

**แนวทางป้องกัน:**
- user isolation: memory ผู้ใช้ A ≠ ผู้ใช้ B
- project isolation: memory project นี้ ≠ project อื่น
- role-based access สำหรับ project memory
- ไม่อนุญาต cross-user retrieval

---

## Safe Defaults

| Setting | Default | เหตุผล |
|---|---|---|
| เก็บ conversation history | ไม่เก็บ | ลดความเสี่ยงข้อมูลรั่วไหล |
| บันทึก memory อัตโนมัติ | ปิด | ต้อง explicit consent |
| retrieve memories จากต่าง user | ปิด | privacy |
| แชร์ project memory ข้าม project | ปิด | isolation |
| ส่งข้อมูลไป third-party LLM | ต้องเลือก | control |

---

## Local-first Storage

- ข้อมูลผู้ใช้ควรเก็บในเครื่องก่อน
- ใช้ local database (SQLite, DuckDB) แทน cloud
- local vector store (chroma, lancedb) สำหรับ memory search
- sync เป็น optional และต้อง encrypted

---

## Memory Deletion

- ผู้ใช้ต้องลบ memory แต่ละรายการได้
- ผู้ใช้ต้องลบ memory ทั้งหมดได้ (account deletion)
- การลบต้อง complete (ไม่ใช่แค่ mark as deleted)
- ควรมี grace period ก่อนลบจริง

---

## User Consent

- ขอ consent ก่อนเก็บข้อมูลส่วนตัว
- อธิบายว่าข้อมูลจะถูกใช้ยังไง
- ให้ผู้ใช้ถอน consent ได้
- ไม่เก็บข้อมูลเมื่อ consent ถูกถอน

---

## Audit Logs

ทุกการทำงานกับ memory ควรมี log:

| Event | ข้อมูลที่ log |
|---|---|
| Memory read | timestamp, user, memory type, context |
| Memory write | timestamp, user, content summary, consent |
| Memory delete | timestamp, user, reason |
| Memory update | timestamp, user, old → new |
| Access denied | timestamp, user, resource |

---

## Human Review

สำหรับ sensitive memory writes ควรมี human review:

- ข้อมูลการเงิน
- ข้อมูลสุขภาพ
- ข้อมูล personal identification
- ข้อมูลที่ผู้ใช้ mark as sensitive
- การเปลี่ยนแปลง project-level memory ที่สำคัญ
