# 10 — Ranking and Reranking

> การจัดลำดับความเกี่ยวข้องของผลลัพธ์ที่检索ได้

---

## Ranking คืออะไร

Ranking คือ **การจัดลำดับผลลัพธ์ที่检索ได้ตามความเกี่ยวข้องกับ query** — เพื่อเลือกเฉพาะ chunks ที่ดีที่สุดใส่ context

```
Retrieved: 20 chunks
Ranker: จัดลำดับตาม relevance
Selected: top-5 chunks
```

---

## Initial Ranking (Retriever-level)

เกิดขึ้นในขั้นตอน retrieval โดย ranking เริ่มต้น:

### Vector Similarity Ranking

```
Query: "วิธี backup ฐานข้อมูล"

Chunk A: "คำสั่ง pg_dump สำหรับ backup ฐานข้อมูล"    score: 0.91
Chunk B: "การติดตั้ง PostgreSQL เบื้องต้น"              score: 0.72
Chunk C: "วิธี restore ฐานข้อมูลจาก backup"            score: 0.88
Chunk D: "การ config PostgreSQL สำหรับ production"    score: 0.55
Chunk E: "pg_restore: วิธีคืนค่าจาก backup"            score: 0.85
```

### Keyword Ranking (BM25)

```
Chunk A: "backup ฐานข้อมูล" - 2 matches     score: 8.5
Chunk C: "restore ฐานข้อมูล" - 1 match      score: 4.2
Chunk E: "backup" - 1 match                 score: 3.8
```

### Hybrid Fusion

รวมคะแนนจาก vector + keyword → ranking ที่ดีขึ้น

---

## Reranking (Second-level)

Reranking คือ **การจัดลำดับใหม่ด้วย model เฉพาะ** เพื่อปรับปรุงคุณภาพหลังจาก initial retrieval

```
Initial Retrieval: 20 chunks (ง่าย, ถูก, เร็ว)
         ↓
Reranker: จัดลำดับใหม่ (ยาก, แพงกว่า, แม่นยำกว่า)
         ↓
Final: top-3 chunks
```

### ทำไมต้อง Reranker

- Initial retriever (embedding + vector search) เป็น approximate
- Reranker ใช้ cross-encoder ที่แม่นยำกว่า
- แต่ reranker ช้ากว่า — จึงใช้กับ top-N เท่านั้น

### Cross-encoder Reranker

```
Input: (query, chunk_i)
Output: relevance score (0-1)

Query: "วิธี backup ฐานข้อมูล"
  (query, Chunk A) → score: 0.95
  (query, Chunk C) → score: 0.92
  (query, Chunk E) → score: 0.87
  (query, Chunk B) → score: 0.45 ← ถูกปรับลง
  (query, Chunk D) → score: 0.30
```

---

## Ranking Criteria

นอกจาก relevance ยังมี criteria อื่นๆ:

### 1. Freshness

ข้อมูลใหม่ควรได้ priority สูงกว่า:

```
score = relevance * 0.7 + freshness * 0.3

Chunk 2026: score ปกติ
Chunk 2025: score * 0.9
Chunk 2024: score * 0.7
```

### 2. Trust Level

ข้อมูลที่น่าเชื่อถือควรได้ priority สูง:

```
official  → score * 1.0
team      → score * 0.8
external  → score * 0.6
draft     → score * 0.4
```

### 3. Diversity

avoid chunks ที่ซ้ำกัน:

```
Group similar chunks → select representative
```

### 4. Specificity

chunk ที่ตรงกับ query เฉพาะเจาะจงกว่า:

```
"PostgreSQL" → chunk about PostgreSQL > chunk about database in general
```

---

## Ranking Pipeline ตัวอย่าง

```
Query: "จะ deploy Django บน Railway ได้ยังไง"

Step 1: Vector Search → 30 chunks
Step 2: Keyword Filter → 20 chunks (filter by "Django", "Railway", "deploy")
Step 3: Hybrid Score → 10 chunks (vector + BM25)
Step 4: Reranker → top-3 chunks
Step 5: Apply filters → freshness, trust level
Step 6: Diversity check → deduplicate

Final: 3 chunks สำหรับ context
```

---

## การตั้งค่า Threshold

```
ถ้า similarity < 0.7:  ไม่ retrieve
ถ้า similarity 0.7-0.8: low confidence (ใช้เป็น supplementary)
ถ้า similarity 0.8-0.9: medium confidence
ถ้า similarity > 0.9: high confidence
```

**ข้อควรระวัง:** Threshold ขึ้นอยู่กับ embedding model — ต้องทดสอบกับ data จริง

---

## Common Mistakes

- **ข้าม reranking** — initial retrieval อาจมี noise สูง
- **rerank ทุก chunk** — ช้าและแพง ใช้เฉพาะ top-20 หรือน้อยกว่า
- **ใช้ relevance อย่างเดียว** — ไม่สน freshness, trust, diversity
- **threshold ต่ำเกินไป** — noise ใน context
- **threshold สูงเกินไป** — ไม่เจอข้อมูล (low recall)
- **ไม่มี diversity check** — 3 chunks ที่ retrieve มาอาจซ้ำกัน

---

## Safety Notes

- Reranker model อาจมี bias — ควรทดสอบกับ data หลากหลาย
- อย่าใช้ freshness เป็น primary criteria สำหรับข้อมูล timeless
- ระวัง ranking manipulation — attacker อาจสร้าง document ที่มี keyword ตรงเพื่อ boost ranking
- ตรวจสอบว่า ranking ไม่ discriminate ข้อมูลบางประเภท

---

## Summary

- Ranking จัดลำดับ relevance ของ retrieved chunks
- Initial ranking: vector similarity หรือ BM25
- Reranking: cross-encoder model เพื่อ precision ที่สูงขึ้น
- นอกจาก relevance ยังต้องพิจารณา freshness, trust, diversity
- ตั้ง threshold ที่เหมาะสม
- Rerank เฉพาะ top-N (ไม่ใช่ทุก chunk)

---

**ถัดไป:** [11 — Memory Writing and Updating](11-memory-writing-and-updating.md)
