# 7 — Retrieval-Augmented Generation (RAG)

> กระบวนการ检索ความรู้มาใช้ประกอบการตอบของ AI Agent

---

## RAG คืออะไร

RAG (Retrieval-Augmented Generation) คือ **เทคนิคที่ให้ LLM检索ข้อมูลจากแหล่งความรู้ภายนอกก่อนตอบ** — แทนที่จะตอบจาก paramateric knowledge (ความจำของ model) อย่างเดียว

```
Without RAG:
  User: "นโยบายการลา company คืออะไร?"
  LLM: "โดยปกติบริษัทมักให้ลาประมาณ 10-15 วัน..." ← general, อาจไม่ตรง

With RAG:
  User: "นโยบายการลา company คืออะไร?"
  →检索 company-policy/leave-policy.md
  →ได้ chunk: "พนักงานมีสิทธิลาพักร้อน 12 วันต่อปี..."
  LLM: "ตามนโยบายบริษัท (leave-policy.md): พนักงานมีสิทธิลาพักร้อน 12 วันต่อปี" ← ตรง, มี source
```

---

## RAG Pipeline

```
                      ┌──────────────┐
                      │  Knowledgebase│
                      │  (Documents)  │
                      └──────┬───────┘
                             │
User Query ──► [1] Retriever ──► [2] Ranker ──► [3] Context Builder ──► [4] LLM ──► Response
                  │                │                │
                  ▼                ▼                ▼
            chunks ที่เกี่ยวข้อง   top-K chunks    context ที่ประกอบแล้ว
                                                    │
                                                    ▼
                                              [5] Verification
                                                    │
                                                    ▼
                                              Response + Citations
```

---

## Step-by-step

### Step 1: Retriever

รับ query → ค้นหา chunks ที่เกี่ยวข้องจาก knowledgebase

```
Query: "วิธีติดตั้ง PostgreSQL บน Ubuntu"

Techniques:
  - Vector search: 找 semantic similarity
  - Keyword search: 找 exact matches (PostgreSQL, Ubuntu, ติดตั้ง)
  - Hybrid: รวมทั้งสองแบบ
```

### Step 2: Ranker

จัดลำดับและเลือก chunks ที่ดีที่สุด

```
Retrieved 20 chunks → Ranker → เลือก top-5

Ranking criteria:
  - Relevance score (cosine similarity)
  - Freshness (วันที่อัปเดต)
  - Trust level (official > wiki)
  - Diversity (avoid duplicate content)
```

### Step 3: Context Builder

ประกอบ chunks ที่เลือก + คำแนะนำการใช้งาน

```
---BEGIN CONTEXT---
ที่มา: docs/postgres-setup.md (อัปเดต 2026-05-01)
ระดับความน่าเชื่อถือ: official

เนื้อหา:
1. sudo apt update && sudo apt install postgresql
2. sudo systemctl start postgresql
3. sudo -u postgres psql
...

---END CONTEXT---

คำแนะนำ: ตอบโดยอ้างอิงจาก context นี้เท่านั้น
ถ้า context ไม่มีข้อมูลที่เพียงพอ ให้บอกว่าไม่ทราบ
```

### Step 4: LLM Generation

LLM รับ context + query → ตอบ

```
Context: [ข้อมูลการติดตั้ง PostgreSQL]
Query: "วิธีติดตั้ง PostgreSQL บน Ubuntu"

Response:
"ตามคู่มือการติดตั้ง (docs/postgres-setup.md):
1. รัน 'sudo apt update && sudo apt install postgresql'
2. เริ่ม service ด้วย 'sudo systemctl start postgresql'
3. เข้าใช้งานด้วย 'sudo -u postgres psql'"
```

### Step 5: Verification

ตรวจสอบคุณภาพของคำตอบ

```
Checks:
  ✅ คำตอบอ้างอิงจาก context (ไม่ hallucinate)
  ✅ citation ถูกต้อง (มี source บอก)
  ✅ context เพียงพอต่อการตอบ
  ❌ ไม่มีข้อมูลที่ขัดแย้งใน context
  ✅ ปลอดภัย ไม่มี sensitive content
```

---

## RAG Variants

| Variant | Description | เมื่อไหร่ควรใช้ |
|---|---|---|
| Simple RAG | Retrieve → Generate | query เดี่ยว, knowledgebase เล็ก |
| Multi-hop RAG | Retrieve → Generate → Retrieve → Generate | คำถามซับซ้อน ต้อง检索หลายรอบ |
| Agentic RAG | Agent ตัดสินใจเองว่าเมื่อไหร่ควร检索 | complex workflow |
| Corrective RAG | ตรวจสอบ quality ของ retrieved docs ถ้าไม่ดี → retrieve ใหม่ | ต้องการ reliability สูง |
| Self-RAG | LLM ตรวจสอบ retrieved chunks เอง | ต้องการ grounded response |

---

## Common Mistakes

- **Retrieve แล้วใช้ทุก chunk** — ranking ไม่ดี, context มี noise
- **ไม่บอก LLM ว่าข้อมูลมาจากไหน** — LLM อาจ ignore context
- **ไม่ verify citation** — LLM อาจอ้าง source ที่ไม่มีใน context
- **ใช้ RAG โดยไม่จำเป็น** — สำหรับคำถาม simple, ใช้ LLM knowledge ก็พอ
- **RAG pipeline ช้าเกินไป** — retrieval + ranking + generation = latency สูง

---

## Safety Notes

- ตรวจสอบ retrieved documents ก่อนเข้า context (prompt injection risk)
- ใช้ในกรณีที่ information freshness หรือ accuracy สำคัญ
- มี fallback เมื่อ retrieval ได้คุณภาพต่ำ
- บอกผู้ใช้เมื่อตอบจาก RAG vs ตอบจากความรู้ LLM
- Monitor hallucination rate

---

## Summary

- RAG = Retrieval + Generation
- 5 steps: Retriever → Ranker → Context Builder → LLM → Verification
- มีหลาย variant ตามความซับซ้อน
- RAG ช่วย reduce hallucination แต่ต้องมี quality control
- อย่าใช้ RAG เมื่อไม่จำเป็น

---

**ถัดไป:** [08 — Chunking and Indexing](08-chunking-and-indexing.md)
