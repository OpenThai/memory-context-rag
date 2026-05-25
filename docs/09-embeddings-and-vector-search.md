# 9 — Embeddings and Vector Search

> การแปลงข้อความเป็น vector และการค้นหาเชิงความหมาย

---

## Embedding คืออะไร

Embedding คือ **การแปลงข้อมูล (ข้อความ, รูปภาพ) เป็น vector — ชุดตัวเลขที่แสดงความหมายของข้อมูล**

```
"แมว"     → [0.2, -0.5, 0.8, 0.1, ...]  (256 หรือ 768 หรือ 1536 มิติ)
"cat"     → [0.3, -0.4, 0.7, 0.2, ...]  (ใกล้กับ "แมว")
"รถยนต์"   → [-0.1, 0.6, -0.3, 0.9, ...] (ไกลจาก "แมว")
```

ข้อความที่มีความหมายคล้ายกัน → vector ที่อยู่ใกล้กัน

---

## Visualizing Embeddings

```
                  Vector Space (2D simplification)

                    รถยนต์ ●
                          ● รถจักรยานยนต์
                  
        ● ส้ม                           ● แมว
                 ● กล้วย              ● สุนัข
        ● แอปเปิ้ล

    ────────────────┼──────────────────────
                   │
              ● หนังสือ
                   │     ● ปากกา
              ● บทความ
                   │
```

สังเกตว่า:
- ผลไม้ อยู่ใกล้กัน
- สัตว์ อยู่ใกล้กัน
- ยานพาหนะ อยู่ใกล้กัน
- ของใช้ อยู่ใกล้กัน

---

## Vector Search (Similarity Search)

เมื่อมี query → แปลง query เป็น vector → หา vector ที่ใกล้ที่สุด

```
Query: "วิธีเลี้ยงแมว"
  → embedding → [0.3, -0.4, 0.7, ...]
  → search vector database
  → ได้ chunks:
      1. "อาหารแมวที่เหมาะสม" (similarity: 0.92)
      2. "การดูแลสุขภาพแมว"   (similarity: 0.88)
      3. "พันธุ์แมว"          (similarity: 0.65)
```

---

## Distance Metrics

| Method | Formula | ค่า Range | เมื่อไหร่ใช้ |
|---|---|---|---|
| Cosine Similarity | cos(A,B) | -1 to 1 | common สำหรับ text |
| Euclidean Distance | distance(A,B) | 0 to ∞ | เมื่อ magnitude 很重要 |
| Dot Product | A·B | -∞ to ∞ | normalized vectors |

ส่วนใหญ่ใช้ **Cosine Similarity** สำหรับ text embedding

---

## Vector Database Options

| Database | Local/Cloud | Notes |
|---|---|---|
| Chroma | Local | easy to use, Python-friendly |
| LanceDB | Local | embedded, fast |
| SQLite + vector | Local | ใช้ extension |
| Qdrant | Both | production-ready |
| Weaviate | Both | scalable |
| Pinecone | Cloud | managed |
| pgvector | Local/Cloud | PostgreSQL extension |

สำหรับ local-first: Chroma, LanceDB, SQLite + vector extension

---

## ข้อจำกัดของ Embedding

### 1. Semantic Drift

คำเดียวกัน在不同 context มีความหมายต่างกัน

```
"apple" → ผลไม้? บริษัท? (embedding อาจไม่แยก)
"แอปเปิ้ล" → กินได้? หรือ iPhone?
```

### 2. Out-of-vocabulary

คำที่ model ไม่เคยเห็นมาก่อน → embedding ไม่ดี

### 3. ไม่เข้าใจ negation เสมอไป

```
"ไม่ชอบแมว" กับ "ชอบแมว" อาจมี embedding ใกล้กัน
```

### 4. Language Sensitivity

Embedding model ที่ train ด้วยภาษาอังกฤษ อาจทำงานกับภาษาไทยได้ไม่ดี

### 5. No Exact Match

ค้นหาชื่อคนหรือรหัสเฉพาะ → vector search อาจไม่ตรง

---

## Keyword Search vs Vector Search

| มิติ | Keyword Search | Vector Search |
|---|---|---|
| วิธีการ | match exact words | similarity in meaning |
| Synonyms | ไม่เจอ | เจอ |
| Typos | ไม่เจอ | เจอ (ถ้าใกล้) |
| Context | ไม่เข้าใจ | เข้า |
| Exact match | เจอแน่ | อาจไม่เจอ |
| Speed | เร็วมาก | เร็ว (แต่ต้อง index) |

**Hybrid search** = รวมทั้งสองแบบ — เหมาะที่สุดในหลายกรณี

---

## Common Mistakes

- **ใช้ embedding model ที่ไม่เหมาะกับภาษาไทย** — ควรใช้ multilingual model
- **ไม่ normalize vectors** — cosine similarity ต้อง normalized vectors
- **เชื่อ similarity score มากเกินไป** — similarity 0.7 ≠ เกี่ยวข้องเสมอ
- **ใช้ embedding อย่างเดียว** — hybrid (vector + keyword) มักดีกว่า
- **ไม่ตั้ง threshold** — retrieve chunks ที่ similarity ต่ำ → noise

---

## Safety Notes

- Embedding อาจ encode bias จาก training data
- ข้อมูล sensitive เมื่อถูก embedding → still sensitive
- Vector database ต้องมี access control
- Embedding model version change → embeddings เปลี่ยน → ต้อง re-index

---

## Summary

- Embedding แปลงข้อความเป็น vector
- Vector search หา semantic matches
- เลือก distance metric ให้เหมาะสม
- Hybrid search (vector + keyword) ดีที่สุดในหลายกรณี
- embedding มีข้อจำกัด — โดยเฉพาะ negation และ exact match

---

**ถัดไป:** [10 — Ranking and Reranking](10-ranking-and-reranking.md)
