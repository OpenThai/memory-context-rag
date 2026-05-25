# 5 — Agent State

> สถานะของ Agent ในระหว่างทำงาน — task progress, tool results, plan state

---

## Agent State คืออะไร

Agent state คือ **ข้อมูลทั้งหมดที่ Agent ใช้ในการติดตามความคืบหน้าของงาน** — มันบอกว่า Agent กำลังทำอะไรอยู่, ทำอะไรไปแล้ว, และมีอะไรค้างอยู่

ไม่เหมือน memory: Agent state เป็น **ข้อมูลชั่วคราวที่เกี่ยวข้องกับ task ปัจจุบัน** — เมื่อ task จบ state นี้จะถูก cleanup (หรือบางส่วนถูกบันทึกเป็น memory)

---

## องค์ประกอบของ Agent State

### 1. Task Progress

```
Task: "วิเคราะห์ repo และสร้าง report"
State:
  - status: in_progress
  - steps_completed: [scan_structure, read_config]
  - current_step: analyze_dependencies
  - steps_remaining: [generate_report]
  - error: None
```

### 2. Plan State

```
Plan:
  goal: "สร้าง landing page"
  steps:
    1. ✅ ติดตั้ง dependencies
    2. ✅ สร้าง component structure
    3. 🔄 implement hero section
    4. ⏳ implement features section
    5. ⏳ implement footer
```

### 3. Tool Results (Cache)

```
Tool Calls:
  - search("ประวัติพระนเรศวร"):
      result: [3 documents], time: 0.5s
  - read("doc-1"):
      result: "เนื้อหา...", time: 1.2s
```

### 4. Conversation Context

```
Session Context:
  - current_topic: "ประวัติศาสตร์ไทย อยุธยา"
  - mentioned_entities: [สมเด็จพระนเรศวร, พระมหาธรรมราชา]
  - last_question: "พระนเรศวรทรงทำอะไรบ้าง"
```

### 5. Error State

```
Error State:
  - last_error: "API rate limit exceeded"
  - retry_count: 2
  - backoff_seconds: 30
  - fallback_plan: "use cache"
```

---

## ทำไม Agent State ต้อง Explicit

Agent state ควรถูก track อย่างชัดเจน ไม่ใช่ซ่อนอยู่ใน conversation history:

```
❌ ไม่ดี:
  Agent: (ในใจ) "ฉันกำลังทำ step 3 อยู่นะ... แต่ไม่มีที่เก็บ state"
  → เมื่อ context ถูก truncate, Agent ลืมว่าทำ到ไหน

✅ ดี:
  Agent state file:
  {
    "task_id": "task-001",
    "status": "in_progress",
    "step": 3,
    "completed": ["step1", "step2"],
    "context_summary": "กำลังสร้าง landing page section"
  }
```

---

## State Management Patterns

### Ephemeral (ใน memory)

เก็บ state ในตัวแปรขณะรัน — หายเมื่อ process จบ

```
✅ เร็ว, ง่าย
❌ หายเมื่อ crash หรือ restart
❌ ข้าม session ไม่ได้
```

เหมาะกับ: task เดียว, interactive session

### Persistent (ใน database/file)

เก็บ state ใน storage — เรียกคืนได้เมื่อ restart

```
✅ ทนทาน
✅ ข้าม session ได้
❌ ช้ากว่า, ต้อง manage lifecycle
```

เหมาะกับ: long-running tasks, background agents

### Hybrid

เก็บ state ใน memory ระหว่างทำงาน, persist เป็นระยะ

```
✅ balance
✅ recovery ได้
❌ complexity สูงขึ้น
```

เหมาะกับ: complex multi-step workflows

---

## ตัวอย่าง State Flow

```
Initialize Plan
    │
    ▼
┌─────────────────────┐
│ State: planning     │──→ สร้าง step list
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ State: executing    │──→ ทำทีละ step
│ step: 2/5          │──→ เก็บ intermediate results
│ results: {...}     │
└─────────────────────┘
    │
    ├── Error → State: error, retry หรือ abort
    ├── Need tool → State: waiting_for_tool, store result
    └── Complete → State: completed, summarize → memory
    │
    ▼
┌─────────────────────┐
│ State: completed    │──→ cleanup หรือ archive
└─────────────────────┘
```

---

## Common Mistakes

- **Agent state ซ่อนใน history** — เมื่อ history ถูก truncate, state หาย
- **ไม่แยก state และ memory** — state ชั่วคราวไม่ควรปนกับ long-term memory
- **state ใหญ่เกินไป** — เก็บ intermediate results ทุกอย่างโดยไม่ summarize
- **ไม่ cleanup** — state ของ task เก่ายังค้าง, influence task ใหม่
- **ไม่มี recovery** — เมื่อ error, ไม่รู้ว่าต้องเริ่มจากตรงไหน

---

## Safety Notes

- Agent state อาจมี sensitive intermediate data (เช่น API keys ใน tool result)
- อย่า persistence state ที่มี credential
- cleanup state เมื่อ task จบ (หรือมี TTL)
- ระวัง state pollution — state ของ task หนึ่งไป影响อีก task

---

## Summary

- Agent state = สถานะการทำงานของ Agent ในขณะนั้น
- ต้อง explicit — ไม่ซ่อนใน history
- เลือก pattern: ephemeral, persistent, หรือ hybrid
- แยก state ออกจาก memory
- Cleanup เมื่อ task จบ

---

**ถัดไป:** [06 — Knowledgebase](06-knowledgebase.md)
