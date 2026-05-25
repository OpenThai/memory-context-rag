# 14 — Privacy and Safety

> การรักษาความเป็นส่วนตัวและความปลอดภัยในระบบ Memory, Context, และ RAG

---

## ทำไมต้องใส่ใจ

ระบบ Memory และ RAG เกี่ยวข้องกับข้อมูลผู้ใช้โดยตรง:

- Memory เก็บ preferences, history, personal data
- RAG retrieves documents ที่อาจมีข้อมูล sensitive
- Context มีข้อมูลที่ส่งไป LLM (ซึ่งอาจเป็น third-party)

**ความผิดพลาดด้าน privacy = ความเสี่ยงทางกฎหมายและความเชื่อมั่น**

---

## ความเสี่ยงหลัก

### 1. Personal Data Leakage

ข้อมูลส่วนตัวรั่วไหลไปยัง:

- LLM provider (ผ่าน context)
- ผู้ใช้อื่น (ผ่าน cross-user retrieval)
- Logs และ monitoring systems

### 2. Sensitive Memory Exposure

ข้อมูลที่ถูกบันทึกเป็น memory ถูก retrieve ใน context ที่ไม่เหมาะสม

### 3. Prompt Injection ผ่าน Documents

ผู้ไม่หวังดีแทรกคำสั่งใน document → เมื่อ Agent retrieve → สั่งการผิด

### 4. Data Retention ที่ไม่จำเป็น

เก็บข้อมูลนานเกินไป → เสี่ยงเมื่อเกิด data breach

---

## หลักการ Privacy-by-Design

```
1. เก็บเท่าที่จำเป็น (Data Minimization)
2. ผู้ใช้เป็นเจ้าของข้อมูล (User Ownership)
3. Local-first เมื่อเป็นไปได้
4. เข้ารหัสข้อมูล sensitive
5. มี access control ที่ชัดเจน
6. มี audit trail
7. ลบได้เมื่อผู้ใช้ต้องการ
```

---

## แนวทางปฏิบัติ

### 1. การจัดเก็บ

| ข้อมูล | เก็บที่ไหน | เข้ารหัส? | TTL |
|---|---|---|---|
| User preferences | Local DB | optional | indefinite |
| Conversation history | ใน session | no | จบ session |
| Sensitive memory | Local encrypted | yes | ตาม user กำหนด |
| Project facts | Team DB | yes | ตลอด project |

### 2. การ检索

- Memory retrieval: เฉพาะ user ของตัวเองเท่านั้น
- Document retrieval: จำกัดตาม access level
- ไม่ cross-retrieve ระหว่าง users โดยเด็ดขาด

### 3. Context Assembly

- ตรวจสอบ context ก่อนส่ง LLM
- Redact หรือ anonymize ข้อมูล sensitive ที่ไม่จำเป็น
- อย่าส่ง credentials หรือ API keys ใน context

### 4. Consent

```
ก่อนเก็บข้อมูลส่วนตัว:
  - แจ้ง用户ว่าจะเก็บอะไร
  - แจ้ง用户ว่าจะใช้ทำอะไร
  - ขออนุญาตก่อนเก็บ
  - ให้用户ถอน consent ได้
```

---

## Data Classification

| Level | ตัวอย่าง | มาตรการ |
|---|---|---|
| Public | project name, README | ไม่ต้องป้องกัน |
| Internal | team wiki, architecture | access control |
| Sensitive | user email, preferences | encrypt + access control |
| Highly Sensitive | passwords, health data, financial | encrypt + local-only + minimal retention |

---

## การจัดการเมื่อมี Data Breach

```
1. ตรวจพบ → หยุดการทำงาน
2. ระบุ scope — ข้อมูลอะไรที่รั่ว
3. แจ้งผู้ใช้ที่เกี่ยวข้อง
4. ลบข้อมูลที่รั่วไหล
5. แก้ไขช่องโหว่
6. audit ระบบใหม่
7. ปรับปรุง policy
```

---

## Safe Defaults

```
❌  default เปิดให้เก็บ memory ทั้งหมด
✅ default ปิด — ต้อง explicit enable

❌  default ส่ง context ไป LLM พร้อมข้อมูล sensitive
✅ default redact sensitive data

❌  default เก็บ history ตลอดไป
✅ default TTL 30 วัน

❌  default ทุกคน access memory ได้
✅ default user isolation
```

---

## การลบข้อมูล

### Hard Delete

ลบข้อมูลออกจาก storage จริง — ไม่สามารถกู้คืน

ใช้เมื่อ: ผู้ใช้ request deletion, sensitive data leak

### Soft Delete

Mark as deleted — ยังมีใน database แต่ไม่ถูก检索

ใช้เมื่อ: ผู้ใช้ลบเองโดยไม่ได้ตั้งใจ (มี grace period)

### Anonymization

แทนที่ข้อมูลส่วนตัวด้วยค่าที่ไม่ระบุตัวตน

ใช้เมื่อ: ต้องการ data สำหรับ analytics

---

## Common Mistakes

- **เก็บทุกอย่างโดยไม่ถาม** — ขาด consent
- **ไม่มีการเข้ารหัส** — ข้อมูล plain text
- **ไม่แยก user data** — ค้นหา memory ข้าม user ได้
- **ไม่ cleanup** — ข้อมูลเก่าไม่เคยถูกลบ
- **เชื่อ LLM provider 100%** — ข้อมูลที่ส่งไป API อาจถูกใช้ train
- **ไม่มี audit log** — ไม่รู้ว่าใคร access ข้อมูลอะไรบ้าง

---

## Verification Checklist

- [ ] ข้อมูล sensitive ถูกเข้ารหัสหรือไม่?
- [ ] User isolation ทำงานถูกต้องหรือไม่?
- [ ] มี consent mechanism หรือไม่?
- [ ] ลบข้อมูลได้หมดจดหรือไม่?
- [ ] Context ถูก redact ก่อนส่ง LLM หรือไม่?
- [ ] มี audit trail หรือไม่?
- [ ] Default เป็น safe หรือไม่?
- [ ] มีกระบวนการเมื่อ data breach หรือไม่?

---

## Summary

- Privacy ต้อง design-in ตั้งแต่ต้น ไม่ใช่เพิ่มทีหลัง
- เก็บเท่าที่จำเป็น, local-first เมื่อเป็นไปได้
- เข้ารหัสข้อมูล sensitive
- ขอ consent ก่อนเก็บ
- ผู้ใช้ต้องลบข้อมูลได้
- มี audit trail และ safe defaults
- ตรวจสอบและปรับปรุงอย่างต่อเนื่อง

---

**📚 เนื้อหาทั้งหมด:**
- [01 — Memory, Context, RAG](01-what-are-memory-context-rag.md)
- [02 — Context Window](02-context-window.md)
- [03 — Short-term Memory](03-short-term-memory.md)
- [04 — Long-term Memory](04-long-term-memory.md)
- [05 — Agent State](05-agent-state.md)
- [06 — Knowledgebase](06-knowledgebase.md)
- [07 — RAG](07-retrieval-augmented-generation.md)
- [08 — Chunking & Indexing](08-chunking-and-indexing.md)
- [09 — Embeddings & Vector Search](09-embeddings-and-vector-search.md)
- [10 — Ranking & Reranking](10-ranking-and-reranking.md)
- [11 — Memory Writing & Updating](11-memory-writing-and-updating.md)
- [12 — Context Engineering](12-context-engineering.md)
- [13 — RAG Evaluation](13-rag-evaluation.md)
- [14 — Privacy & Safety](14-privacy-and-safety.md)
