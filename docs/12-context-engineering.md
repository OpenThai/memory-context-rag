# 12 — Context Engineering

> การออกแบบและประกอบ context ที่มีประสิทธิภาพสำหรับ AI Agent

---

## Context Engineering คืออะไร

Context engineering คือ **การออกแบบว่าอะไรควรอยู่ใน context, จัดลำดับอย่างไร, และจัดการ token budget อย่างไร** เพื่อให้ LLM ตอบได้ดีที่สุด

```
Context Engineering ≠ Prompt Engineering

Prompt Engineering: writing better instructions
Context Engineering: choosing what goes into the prompt
```

---

## ส่วนประกอบของ Context

```
┌─────────────────────────────────────────────┐
│                CONTEXT                       │
│                                              │
│  [SYSTEM] Instructions, rules, constraints   │
│                                              │
│  [MEMORY] Relevant user/project memories    │
│                                              │
│  [KNOWLEDGE] Retrieved documents (RAG)       │
│                                              │
│  [HISTORY] Recent conversation turns         │
│                                              │
│  [TOOLS] Tool call results (if any)          │
│                                              │
│  [QUERY] Current user request                │
│                                              │
│  [OUTPUT] Expected format, constraints       │
└─────────────────────────────────────────────┘
```

---

## Context Assembly Flow

```
User Request
    │
    ▼
┌─────────────────────────────┐
│ 1. Parse Request             │
│    → intent, query, entities │
└─────────────────────────────┘
    │
    ▼
┌─────────────────────────────┐
│ 2. Collect Components        │
│    ├── System instructions   │
│    ├── Relevant memory       │
│    ├── Retrieved documents   │
│    ├── Conversation history  │
│    └── Tool results          │
└─────────────────────────────┘
    │
    ▼
┌─────────────────────────────┐
│ 3. Prioritize & Filter       │
│    → relevance               │
│    → freshness               │
│    → token budget            │
│    → safety check            │
└─────────────────────────────┘
    │
    ▼
┌─────────────────────────────┐
│ 4. Structure & Order         │
│    → logical flow            │
│    → clear separation        │
│    → reduce redundancy       │
└─────────────────────────────┘
    │
    ▼
┌─────────────────────────────┐
│ 5. Add Safety Instructions   │
│    → "answer from context"  │
│    → "say if uncertain"     │
│    → "no sensitive info"    │
└─────────────────────────────┘
    │
    ▼
  Final Prompt → LLM
```

---

## Context Structure Template

```
<system>
คุณคือ AI Assistant สำหรับ [purpose]
- ตอบจาก context ที่ให้เท่านั้น
- ถ้าไม่รู้ ให้บอกว่าไม่ทราบ
- อย่าใช้ข้อมูลนอกเหนือจากที่ให้
- อ้างอิง source ทุกครั้ง
- ตอบเป็น [ภาษาไทย/English]
</system>

<memory>
--- user preferences ---
- ชื่อ: สมชาย
- ชอบคำตอบสั้น กระชับ
- timezone: ICT (UTC+7)

--- project facts ---
- project: memory-context-rag
- language: markdown
</memory>

<context>
--- source: docs/rag-pipeline.md ---
เนื้อหาที่检索มา...
--- source: docs/chunking.md ---
เนื้อหาที่检索มา...
</context>

<history>
User: RAG คืออะไร
Assistant: RAG คือการ检索...
User: แล้ว chunking คืออะไร
</history>

<tools>
[search_web: "RAG best practices"] → result: [...]
</tools>

<query>
ช่วยอธิบายว่า Chunking และ Indexing ต่างกันยังไง
</query>

<output>
ตอบสั้น ไม่เกิน 3 ประโยค
ถ้าไม่แน่ใจให้บอกว่าไม่ทราบ
อ้างอิง source จาก <context> เท่านั้น
</output>
```

---

## Component Priority

| Priority | Component | Token Budget |
|---|---|---|
| 1 (สูงสุด) | System Instructions | 10% |
| 2 | User Query | 5% |
| 3 | Relevant Memory | 10% |
| 4 | Retrieved Documents | 35% |
| 5 | Tool Results | 15% |
| 6 | Conversation History | 20% |
| 7 (ต่ำสุด) | Output Format | 5% |

% เป็น approximate — ปรับตาม use case

---

## Context Selection Strategies

### 1. Recent-first

ใส่เฉพาะประวัติล่าสุด + summarize ที่เก่า

### 2. Relevance-based

检索 memory/document อัตโนมัติ → ใส่เฉพาะที่เกี่ยวข้อง

### 3. Hybrid

Recent N + relevant search results

### 4. Structured (แนะนำ)

แบ่ง context เป็น sections ชัดเจน → LLM เข้าใจบริบทได้ดีขึ้น

---

## Context Compression

เมื่อ context ใหญ่เกินไป:

| Method | Description | Lossy? |
|---|---|---|
| Summarize history | สรุป conversation history | yes |
| Top-K selection | เลือกเฉพาะ relevant chunks | yes |
| Chunk trimming | ตัดเนื้อหาที่ไม่จำเป็นใน chunk | yes |
| Dedup | ลบ chunks ซ้ำ | no |
| Tool result summary | สรุป tool output | yes |
| Message pruning | เก็บเฉพาะ messages ที่ relevant | yes |

---

## Common Mistakes

- **ไม่มี structure** — context เป็นก้อนเดียวกัน, LLM สับสน
- **ใส่ทุกอย่างที่检索ได้** — noise ลด quality
- **สำคัญ memory มากกว่า query** — memory ครอบงำคำตอบ
- **ไม่บอก LLM ว่าอะไรคืออะไร** — ไม่แยก system/memory/context
- **ไม่มี token budget** — เติม context จนเต็ม window
- **ไม่ตรวจสอบ context ก่อนส่ง** — อาจมี sensitive data

---

## Safety Notes

- แยก retrieved content จาก system instructions (ป้องกัน prompt injection)
- ตรวจสอบ context ว่าไม่มี sensitive data ที่ไม่จำเป็น
- ใส่ safety instructions ที่ท้าย context (ใกล้ query)
- มีการตรวจสอบ output หลัง generation
- Log context ที่ส่งให้ LLM (สำหรับ audit)

---

## Summary

- Context engineering = การออกแบบว่าอะไรเข้า prompt บ้าง
- แบ่ง context เป็น sections ชัดเจน
- จัดลำดับความสำคัญและ token budget
- ใช้ compression เมื่อจำเป็น
- ตรวจสอบ safety ก่อนส่ง context

---

**ถัดไป:** [13 — RAG Evaluation](13-rag-evaluation.md)
