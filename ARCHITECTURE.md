# สถาปัตยกรรมระบบ Memory, Context, และ RAG

> ภาพรวมของ component ต่างๆ ในระบบและวิธีการทำงานร่วมกัน

---

## ภาพรวมระดับสูง

```
┌─────────────────────────────────────────────────────────┐
│                    ผู้ใช้ (User Input)                     │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              🔹 Agent / Orchestrator                     │
│  ┌───────────┐ ┌──────────┐ ┌──────────────┐           │
│  │ Task      │ │ Tool     │ │ Verification │           │
│  │ Manager   │ │ Runner   │ │ Layer        │           │
│  └───────────┘ └──────────┘ └──────────────┘           │
└────────────────────────┬────────────────────────────────┘
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ Memory Layer │ │  RAG Layer   │ │ Context      │
│              │ │              │ │ Builder      │
└──────┬───────┘ └──────┬───────┘ └──────┬───────┘
       │                │                │
       ▼                ▼                ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ Memory Store  │ │ Knowledgebase│ │ Prompt       │
│ (vectors +    │ │ (documents,  │ │ Assembly     │
│  structured)  │ │  chunks)     │ │              │
└──────────────┘ └──────────────┘ └──────────────┘
```

---

## Read Path (เวลาตอบคำถาม)

```
User Input
    │
    ▼
[1] Context Builder
    ├── รับ user input + current session state
    ├── query Memory Store → ได้ relevant memories
    ├── query Knowledgebase → ได้ relevant documents (RAG)
    └── query Agent State → ได้ task progress, tool results
    │
    ▼
[2] Ranking / Selection
    ├── จัดลำดับ memories ตาม relevance
    ├── จัดลำดับ documents ตาม relevance
    ├── ใช้ metadata filters (date, source, trust)
    └── ตัด chunk ที่ซ้ำหรือไม่เกี่ยวข้อง
    │
    ▼
[3] Context Assembly
    ├── System instructions
    ├── Relevant memories
    ├── Retrieved documents
    ├── Conversation history (ล่าสุด)
    ├── Tool results (ถ้ามี)
    └── Safety constraints
    │
    ▼
[4] LLM Generation
    │
    ▼
[5] Verification
    ├── ตรวจสอบ citation
    ├── ตรวจสอบ hallucination
    └── ตรวจสอบ safety
    │
    ▼
   Output
```

---

## Write Path (เวลาบันทึก memory)

```
User Interaction หรือ Task Completion
    │
    ▼
[1] Memory Trigger
    ├── user explicitly saves
    ├── important fact detected
    ├── decision or agreement made
    └── task completed with result
    │
    ▼
[2] Memory Writer
    ├── extract key information
    ├── classify memory type
    ├── check for sensitive content
    └── assign metadata (timestamp, source, confidence)
    │
    ▼
[3] Memory Store
    ├── write to short-term or long-term
    ├── update existing if duplicate
    ├── create vector embedding for search
    └── trigger consolidation if needed
```

---

## Memory vs Document Retrieval

| มิติ | Memory Retrieval | Document Retrieval (RAG) |
|---|---|---|
| ข้อมูล | ประวัติผู้ใช้, preferences, facts | เอกสาร, knowledgebase |
| อายุข้อมูล | dynamic, เปลี่ยนแปลงได้ | static (until updated) |
| Ownership | ผู้ใช้ / Agent | เจ้าของเอกสาร |
| Update frequency | ทุก interaction | เมื่อมีเอกสารใหม่ |
| Privacy risk | สูง | ขึ้นอยู่กับเอกสาร |
| Retrieval criteria | relevance + recency + importance | relevance + authority |

---

## Local-first Architecture (ทางเลือก)

```
┌────────── User Device ──────────┐
│                                  │
│  ┌──────────┐  ┌──────────────┐  │
│  │ Local     │  │ Local Vector │  │
│  │ SQLite    │  │ Store        │  │
│  │ (memory)  │  │ (chroma/     │  │
│  │           │  │  lancedb)    │  │
│  └──────────┘  └──────────────┘  │
│         │              │         │
│         ▼              ▼         │
│  ┌──────────────────────────┐   │
│  │   Local LLM / API Call   │   │
│  └──────────────────────────┘   │
│                                  │
└──────────────────────────────────┘
         │
         │ (optional sync)
         ▼
┌────────── Cloud ──────────┐
│  Backup / Multi-device     │
└────────────────────────────┘
```

---

## Privacy และ Audit

- ทุกการอ่าน memory ควร log
- ทุกการเขียน memory ควรมี source identifier
- sensitive memory ควร encrypt
- user ต้องลบ memory ได้
- system state ไม่ควรเก็บถาวร

---

## Component Diagram แยกตาม Layer

```
Layer 1: Data Store
  Memory Store (vector + key-value)
  Knowledgebase (documents + metadata)
  Agent State (task progress, tool results)

Layer 2: Retrieval
  Memory Retriever
  Document Retriever
  Hybrid Search (vector + keyword)

Layer 3: Processing
  Ranking / Reranking
  Context Assembly
  Memory Writer / Consolidation

Layer 4: Agent
  Task Manager
  Tool Runner
  Verification Layer (citation, hallucination, safety)
```
