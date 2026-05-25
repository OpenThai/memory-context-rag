# 4 — Long-term Memory

> ความจำระยะยาวที่ Agent เก็บไว้ใช้ข้าม session

---

## Long-term Memory คืออะไร

Long-term memory คือ **ข้อมูลที่ Agent เก็บไว้อย่างถาวร (หรือยาวนาน) เพื่อใช้ข้าม session** — 不同于 short-term memory ที่หายไปเมื่อจบ session

เปรียบเทียบ: Long-term memory เหมือน **สมุดบันทึก** ที่ Agent เขียนข้อมูลสำคัญไว้ และกลับมาอ่านเมื่อจำเป็น

---

## ประเภทของ Long-term Memory

### 1. User Preferences

สิ่งที่ผู้ใช้ชอบ ไม่ชอบ ตั้งค่าไว้

```
User Profile Memory:
  - ชื่อ: สมชาย
  - ภาษา: ไทย
  - รูปแบบการตอบ: สั้น กระชับ
  - ไม่ชอบ: คำตอบยาวๆ
  - timezone: ICT (UTC+7)
```

### 2. Project Facts

ข้อเท็จจริงเกี่ยวกับ project ที่ทำงาน

```
Project Memory:
  - project: memory-context-rag
  - framework: Markdown-based
  - language: ไทย
  - target: developers
  - decision: 2026-05-20 — ใช้ Markdown แทน code จนกว่า Phase 6
```

### 3. Durable Knowledge

ความรู้ที่ Agent สะสมจากการทำงาน

```
Learned Facts:
  - ผู้ใช้ชอบ PostgreSQL มากกว่า MySQL
  - project นี้ใช้ MIT License
  - client ต้องการ deploy บน Linux เท่านั้น
```

### 4. Interaction History (Selected)

ประวัติการโต้ตอบที่สำคัญ (ไม่ใช่ทั้งหมด)

```
Interaction Highlights:
  - 2026-05-20: ผู้ใช้แสดงความสนใจใน local-first architecture
  - 2026-05-22: ตัดสินใจใช้ SQLite สำหรับ prototype
```

---

## วงจรชีวิตของ Long-term Memory

```
                   ┌─────────────┐
                   │  สร้าง (Write) │
                   └──────┬──────┘
                          ▼
                   ┌─────────────┐
                   │  เก็บ (Store) │
                   └──────┬──────┘
                          ▼
                   ┌─────────────┐
            ┌─────│  ใช้งาน (Read) │─────┐
            │     └─────────────┘      │
            ▼                          ▼
   ┌──────────────┐          ┌──────────────┐
   │ อัปเดต/แก้ไข   │          │  ลืม/ลบ       │
   │ (Update)      │          │ (Forget)     │
   └──────────────┘          └──────────────┘
```

---

## Memory Lifecycle Management

### Creation

- **Intentional** — ผู้ใช้บอกให้จำ
- **Automatic (selective)** — Agent detect ข้อมูลสำคัญ
- **Confirmed** — Agent ถามก่อนบันทึก

### Storage

- เก็บในรูปแบบที่检索ได้ (vector + structured fields)
- มี metadata: timestamp, source, confidence, type
- เข้ารหัสสำหรับข้อมูล sensitive

### Retrieval

- 检索เมื่อ relevant กับ query ปัจจุบัน
- จัดลำดับด้วย relevance + recency + importance
- จำกัดจำนวนที่ retrieve ต่อครั้ง

### Update

- ข้อมูลใหม่แทนที่ข้อมูลเก่า
- Version history (optional)
- Merge เมื่อข้อมูลเสริมกัน

### Deletion

- ผู้ใช้ลบได้
- Auto-expire ตาม TTL
- Correction → ลบของเก่า

---

## Memory Consolidation

กระบวนการเปลี่ยน short-term → long-term หรือ summarize memory เก่า:

```
Session 1: ผู้ใช้บอก "ผมทำงานที่บริษัท A"
Session 2: ผู้ใช้บอก "ที่บริษัท A ใช้ Python"
Session 3: ผู้ใช้บอก "ทีมผมที่ A มี 5 คน"

After Consolidation:
  "ผู้ใช้ทำงานที่บริษัท A — ใช้ Python, ทีม 5 คน"
```

**Consolidation triggers:**
- Session จบ
- ถึงเวลาที่กำหนด (cron)
- จำนวน memory เกิน threshold
- ผู้ใช้ร้องขอ

---

## Common Mistakes

- **เก็บทุกอย่างเป็น long-term** — ข้อมูลรก, retrieval quality ลดลง
- **ไม่มี TTL หรือ decay** — ข้อมูลเก่าที่ไม่เกี่ยวข้องกองอยู่
- **ไม่มีการ consolidate** — ข้อมูลกระจัดกระจาย, retrieve ยาก
- **ไม่มี user control** — ผู้ใช้แก้ไขหรือลบ memory ไม่ได้
- **ไม่ตรวจสอบ freshness** — ใช้ memory ที่ outdated

### ตัวอย่าง: Long-term Memory ที่ไม่ดี

```
Memory Store:
  - "ผู้ใช้ชื่อสมชาย" (2026-05-01)
  - "ผู้ใช้ชื่อสมชาย" (2026-05-02)
  - "ผู้ใช้ชอบกาแฟ" (2026-01-01 — อาจไม่จริงแล้ว)
  - "ผู้ใช้บอกว่า project ใช้ React" (แต่เปลี่ยนเป็น Vue แล้ว)
  - (ซ้ำกัน 5 รายการ, ไม่มี dedup)
```

---

## Safety Notes

- ตรวจสอบ sensitive content ก่อนบันทึก
- ผู้ใช้ต้องดูและลบ memory ได้
- มี audit trail สำหรับ write และ read
- ระวัง data leak ข้าม user หรือ project
- Human review สำหรับ memory ที่ sensitive

---

## Summary

- Long-term memory = ข้อมูลถาวรข้าม session
- ต้อง intentional ในการเขียน — ไม่ใช่ทุกอย่างที่ควรจำ
- มี lifecycle: create → store → retrieve → update → delete
- ต้อง consolidate เพื่อลด redundancy
- ผู้ใช้ต้องควบคุม memory ของตัวเองได้

---

**ถัดไป:** [05 — Agent State](05-agent-state.md)
