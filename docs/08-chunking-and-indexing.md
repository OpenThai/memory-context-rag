# 8 — Chunking and Indexing

> การแบ่งเอกสารเป็นส่วนย่อย และการทำดัชนีเพื่อการ检索ที่มีประสิทธิภาพ

---

## Chunking คืออะไร

Chunking คือ **กระบวนการแบ่งเอกสารขนาดใหญ่ออกเป็นส่วนย่อย (chunks)** ก่อนนำไป index เพื่อ检索

ทำไมต้อง chunk: LLM มี context window จำกัด และ retrieval จะมีประสิทธิภาพกว่าถ้าเราส่งเฉพาะส่วนที่เกี่ยวข้อง

```
เอกสารต้นฉบับ (10,000 คำ)
  │
  ├── Chunk 1: "วิธีติดตั้ง PostgreSQL..."
  ├── Chunk 2: "การตั้งค่าเบื้องต้น..."
  ├── Chunk 3: "การจัดการ users..."
  └── Chunk 4: "การ backup..."
```

---

## วิธีการ Chunking

### 1. Fixed-size Chunking

แบ่งตามจำนวนตัวอักษรหรือ token ที่แน่นอน

```
✅ ง่าย, เร็ว
❌ ตัดเนื้อหาขาดกลางประโยคหรือหัวข้อ
❌ ไม่ respect document structure
```

**ตัวอย่าง:** ทุก 500 tokens → 1 chunk (overlap 50 tokens)

### 2. Semantic Chunking

แบ่งตาม natural boundaries — หัวข้อ, ย่อหน้า, ประโยค

```
✅ respect structure
✅ แต่ละ chunk มีเนื้อหาสมบูรณ์ในตัวเอง
❌ ขนาดไม่เท่ากัน
❌ implementation ซับซ้อนกว่า
```

**ตัวอย่าง:** แบ่งตาม heading level หรือ paragraph breaks

### 3. Recursive Chunking

แบ่งหลาย level — เริ่มจากใหญ่ (section) → เล็ก (paragraph) → เล็กกว่า (sentence)

```
✅ ยืดหยุ่น
✅ ทำงานกับ document หลากหลาย
❌ management ซับซ้อน
```

---

## ขนาด Chunk ที่เหมาะสม

| Data Type | Chunk Size | Overlap | เหตุผล |
|---|---|---|---|
| Code | 100-300 lines | 10-20 lines | ฟังก์ชันมักสั้น |
| Documentation | 500-1000 tokens | 100-200 tokens | section มักยาว |
| News article | 300-500 tokens | 50 tokens | 1 article = 1 topic |
| Conversation | 200-400 tokens | 50 tokens | turn สั้น |
| Legal document | 1000-2000 tokens | 200 tokens | clauses ยาว |

**ไม่มีขนาดที่ perfect สำหรับทุกกรณี** — ต้องทดลองกับ data และ use case ของคุณ

---

## Overlap

Overlap คือการให้ chunk ที่ติดกันมีเนื้อหาซ้อนทับกันบางส่วน

```
Chunk 1: [A B C D E F G]
Chunk 2:        [E F G H I J K]
Chunk 3:              [H I J K L M N]

Overlap = 3 units (E, F, G)
```

**ประโยชน์:**
- ป้องกันการตัดเนื้อหาสำคัญตรงรอยต่อ
- เก็บ context ของขอบเขตหัวข้อ

**ข้อเสีย:**
- เสียพื้นที่ storage
- อาจ retrieve chunks ที่ซ้ำกัน

---

## Metadata

ข้อมูลกำกับที่ช่วยในการ检索และการกรอง:

```yaml
chunk_id: "doc-001-chunk-03"
source: "docs/installation.md"
doc_title: "วิธีติดตั้ง PostgreSQL"
section: "การตั้งค่าเบื้องต้น"
heading: "การ config"
heading_level: 2
chunk_index: 3
total_chunks: 8
date: "2026-05-20"
version: "1.2"
tags: [postgresql, installation, config]
trust_level: official
```

---

## Indexing

หลัง chunking → สร้าง index เพื่อให้检索ได้เร็ว

### Vector Index

แปลงแต่ละ chunk เป็น vector (embedding) → เก็บใน vector database

```
Chunk → Embedding Model → Vector [0.1, 0.5, ...] → Vector DB
```

### Keyword Index (Inverted Index)

สร้าง index ของคำสำคัญในแต่ละ chunk

```
"PostgreSQL" → [doc-001-chunk-01, doc-001-chunk-03, doc-002-chunk-05]
"Ubuntu"    → [doc-001-chunk-01, doc-003-chunk-02]
```

### Hybrid Index

รวมทั้งสองแบบ → retrieval ที่ robust กว่า

---

## Common Mistakes

- **Chunk ขนาดเดียวสำหรับทุก document** — ควรปรับตาม type
- **ไม่มี overlap** — ข้อมูลที่รอยต่อของ chunks หาย
- **ไม่มี metadata** — retrieve ได้แต่บอกที่มาไม่ได้
- **Chunk ใหญ่เกินไป** — context waste, relevance ลดลง
- **Chunk เล็กเกินไป** — missing context, retrieve ไม่ตรง
- **ไม่ preserve structure** — chunk ที่ตัดกลางประโยค

---

## Safety Notes

- ตรวจสอบว่า chunking ไม่ตัด sensitive content ออก
- metadata ต้องถูกต้อง — error ใน metadata ทำให้ retrieval ผิดพลาด
- เมื่อ document เปลี่ยน → ต้อง re-chunk และ re-index
- ระวัง chunk ที่มีข้อมูลหลากหลาย topics → retrieval quality ลดลง

---

## Summary

- Chunking แบ่ง document เป็นส่วนย่อยเพื่อ检索
- เลือกวิธี chunking ตามประเภท document
- Overlap ช่วยกันข้อมูลหายที่รอยต่อ
- Metadata ช่วยกรองและติดตาม source
- Indexing ทำให้检索เร็ว — อาจใช้ vector, keyword, หรือ hybrid

---

**ถัดไป:** [09 — Embeddings and Vector Search](09-embeddings-and-vector-search.md)
