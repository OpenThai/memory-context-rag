# 11 — Memory Writing and Updating

> กลไกการเขียน อัปเดต ผสาน และลบ memory

---

## การเขียน Memory (Memory Writing)

Memory writing คือ **กระบวนการที่ Agent บันทึกข้อมูลลงใน long-term memory**

### เมื่อไหร่ควรเขียน

| สถานการณ์ | ตัวอย่าง | ควรเขียน? |
|---|---|---|
| ผู้ใช้แนะนำตัว | "我叫สมชาย" | ✅ |
| ผู้ใช้บอก preference | "ผมชอบคำตอบสั้นๆ" | ✅ |
| ผู้ใช้ให้ข้อมูล project | "project นี้ใช้ MIT License" | ✅ |
| ผู้ใช้บอกความรู้สึก | "วันนี้เหนื่อยจัง" | ❌ (ชั่วคราว) |
| Agent ค้นพบข้อเท็จจริง | "codebase นี้ใช้ FastAPI" | ✅ (แต่ตรวจสอบก่อน) |
| ผลลัพธ์จาก tool | "API returns 200" | ❌ (part of task) |

### Writing Strategies

#### 1. Explicit Save

ผู้ใช้บอกให้จำโดยตรง

```
User: "จำไว้นะว่าผมชอบกินข้าวผัด"
Agent: "บันทึกแล้ว: ผู้ใช้ชอบกินข้าวผัด"
```

#### 2. Implicit Save (Selective)

Agent detect ข้อมูลสำคัญและบันทึก

```
User: "ที่ทำงานผมใช้ Python กับ FastAPI ครับ"
Agent: [detect: workplace info] → บันทึก reference
Agent: "เข้าใจครับ" (อาจถามยืนยันก่อน)
```

#### 3. Confirmation-based

Agent ถามผู้ใช้ก่อนบันทึก

```
Agent: "ผมขอบันทึกว่าคุณทำงานที่บริษัท A ใช้ Python และ FastAPI — ถูกต้องไหมครับ?"
User: "ใช่"
Agent: "บันทึกแล้ว"
```

---

## การอัปเดต Memory (Memory Update)

เมื่อข้อมูลเปลี่ยน → ต้องอัปเดต memory

### Replace

```
Old: "ผู้ใช้ใช้ React"
New: "ผู้ใช้เปลี่ยนมาใช้ Vue"
→ Replace memory (หรือ add version)
```

### Merge

```
Existing: "ผู้ใช้ชอบ database: PostgreSQL"
New: "ผู้ใช้กำลังลองใช้ SQLite"
→ Merge: "ผู้ใช้ชอบ PostgreSQL, กำลังลอง SQLite"
```

### Append

```
Existing: "project: memory-context-rag"
New: "ใช้ MIT License"
→ Append: "project: memory-context-rag, license: MIT"
```

---

## การลบ Memory (Memory Deletion)

### Explicit Deletion

```
User: "ลบข้อมูลที่จำเกี่ยวกับฉันทั้งหมด"
Agent: "ลบ memory ต่อไปนี้: [name, preferences, history]"
```

### TTL-based Deletion

```
created_at: 2026-01-01
ttl: 90 days
expires_at: 2026-04-01
→ เมื่อถึงวันที่ 2026-04-01 → auto-delete
```

### Correction-based Deletion

```
User: "ที่จริงผมทำงานที่บริษัท B นะ ไม่ใช่ A"
→ Delete: "ทำงานที่ A"
→ Create: "ทำงานที่ B"
```

---

## Memory Schema

โครงสร้างของ memory แต่ละรายการ:

```yaml
memory_id: "mem-20260520-001"
type: "user_preference"    # user_info, project_fact, interaction, ...
content: "ผู้ใช้ชอบคำตอบสั้นๆ กระชับ"
confidence: 0.9            # 0-1
source: "user_explicit"    # user_explicit, inferred, tool, ...
created_at: "2026-05-20T10:30:00Z"
updated_at: "2026-05-20T10:30:00Z"
ttl: null                   # null = indefinite
access_level: "private"    # private, shared, public
tags: [preference, style]
version: 1
```

---

## Deduplication

ป้องกัน memory ซ้ำ:

```
❌ มี 3 รายการ:
  - "ผู้ใช้ชื่อสมชาย"
  - "ผู้ใช้ชื่อสมชาย" (ซ้ำ)
  - "ผู้ใช้บอกว่าชื่อสมชาย" (ซ้ำ)

✅ หลัง dedup:
  - "ผู้ใช้ชื่อสมชาย" (confidence: 0.95, updated: 2026-05-20)
```

---

## Common Mistakes

- **เขียน memory ทุก interaction** — ข้อมูลรก, retrieval quality ลดลง
- **ไม่ตรวจสอบก่อนเขียน** — อาจบันทึกข้อมูลผิด
- **ไม่มี update mechanism** — memory stale แต่ไม่ถูกแก้ไข
- **ไม่ถามผู้ใช้** — บันทึกข้อมูลส่วนตัวโดยไม่ consent
- **ไม่มี dedup** — memory ซ้ำกันหลายรายการ
- **ลบ memory ไม่ complete** — mark as deleted แต่ยังมีข้อมูลเหลือ

---

## Safety Notes

- Explicit consent ก่อนเขียน (โดยเฉพาะข้อมูล sensitive)
- editable และ deletable โดยผู้ใช้
- audit trail สำหรับทุก write, update, delete
- sensitive memory ควรเข้ารหัส
- correction mechanism — เมื่อข้อมูลเปลี่ยน
- backup ก่อน bulk delete

---

## Summary

- เขียน memory เมื่อมีข้อมูลสำคัญ — ไม่ใช่ทุกอย่าง
- อัปเดต: replace, merge, หรือ append ตามสถานการณ์
- ลบ: explicit, TTL, หรือ correction-based
- มี schema และ dedup เพื่อ quality
- ผู้ใช้ควบคุม memory ของตัวเองได้

---

**ถัดไป:** [12 — Context Engineering](12-context-engineering.md)
