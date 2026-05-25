# 1 — Memory, Context, และ RAG คืออะไร

> ปูพื้นฐานแนวคิดสำคัญสามอย่างที่ AI Agent ทุกตัวต้องเข้าใจ

---

## ความหมาย

### Memory

Memory คือ **ข้อมูลที่ Agent เก็บไว้ใช้ข้าม session** — เหมือนสมองของ Agent ที่จำสิ่งต่างๆ ได้

Agent ที่ไม่มี memory คือ Agent ที่ลืมทุกอย่างหลังจากตอบแต่ละคำถาม มันไม่รู้ว่าคุณชื่ออะไร ไม่รู้ว่าเคยคุยอะไรกันมาก่อน ไม่รู้ว่าคุณชอบอะไร

**ตัวอย่าง:**
- ผู้ใช้: "我叫สมชาย"
- Agent ที่ไม่มี memory: "สวัสดีค่ะ คุณชื่ออะไรคะ?"
- Agent ที่มี memory: "สวัสดีค่ะ คุณสมชาย!"

### Context

Context คือ **ทุกอย่างที่อยู่ใน prompt ขณะที่ Agent กำลังตอบ** — ประกอบด้วย:

- System instructions
- Conversation history (ข้อความล่าสุด)
- Memory ที่เรียกมาใช้
- เอกสารที่检索มา
- Tool call results
- ข้อจำกัด和安全 notes

Context มีอายุสั้นมาก — มันถูกสร้างใหม่ทุกครั้งที่มีการเรียกใช้ LLM และหายไปเมื่อการตอบเสร็จสิ้น

### RAG (Retrieval-Augmented Generation)

RAG คือ **กระบวนการ检索ข้อมูลภายนอกมาใส่ใน context เพื่อให้ Agent ตอบโดยมีแหล่งอ้างอิง** — แทนที่จะตอบจากความจำของ LLM อย่างเดียว

**ตัวอย่าง:**
- ผู้ใช้: "นโยบายการคืนสินค้าคืออะไร?"
- Agent ที่ไม่มี RAG: "โดยทั่วไปนโยบายคืนสินค้าคือ 30 วัน..." (อาจถูกหรือผิด)
- Agent ที่มี RAG: "ตาม document นโยบายบริษัท ข้อ 3.2: คืนสินค้าได้ภายใน 14 วัน" (มี source อ้างอิง)

---

## ความสัมพันธ์

```
Memory → เก็บข้อมูลระยะยาว (ข้าม session)
RAG    → Retrieval ข้อมูลจาก knowledgebase (ตาม query)
Context → รวมทุกอย่างเข้าด้วยกันใน prompt
```

```
User Query
    │
    ▼
┌─────────────────────────────────────────────┐
│              Context Builder                 │
│                                              │
│  System Instructions (fixed)                │
│  + Relevant Memory (from Memory Store)      │
│  + Retrieved Documents (from RAG pipeline)  │
│  + Conversation History (last N messages)   │
│  + Tool Results (if tools were called)      │
│  + Safety Constraints                       │
│                                              │
│  = FINAL PROMPT → LLM                       │
└─────────────────────────────────────────────┘
```

---

## ทำงานร่วมกันยังไง

1. ผู้ใช้ถามคำถาม
2. Agent สร้าง context:
   - 检索 memory ที่เกี่ยวข้อง → ได้ข้อมูลผู้ใช้
   - 检索 knowledgebase → ได้ document ที่ตรงกับคำถาม (RAG)
   - รวม conversation history
3. Agent ส่ง context + คำถามไปให้ LLM
4. LLM ตอบโดยอ้างอิงข้อมูลใน context
5. ถ้ามีข้อมูลสำคัญใหม่ → Agent อาจ **write memory** (จะพูดถึงใน docs/11)

---

## ข้อควรจำ

| Concept | เก็บที่ไหน | อายุ | ใช้ทำไม |
|---|---|---|---|
| Memory | Database / File | ข้าม session | จดจำ preferences, facts |
| Context | Prompt (ชั่วคราว) | แค่ครั้งเดียว | ให้ข้อมูลที่จำเป็นตอนตอบ |
| RAG | Knowledgebase → Context | ตาม query | Retrieval ความรู้ภายนอก |

---

## ข้อผิดพลาดที่พบบ่อย

- **เข้าใจว่า Context = Memory** — Context หายไปเมื่อจบการตอบ ต้องมีระบบเก็บ memory แยก
- **ใส่ทุกอย่างใน context** — context ยาวไม่ใช่สิ่งดีเสมอไป (ดู docs/02)
- **ใช้ RAG โดยไม่สนใจ quality** — retrieval ที่แย่ทำให้คำตอบแย่กว่าไม่ใช้ RAG
- **ไม่แยก memory types** — เก็บทุกอย่างปนกัน ทำให้ retrieval quality ลดลง

---

## Summary

- **Memory** = สิ่งที่ Agent จำข้าม session
- **Context** = ทุกอย่างใน prompt ขณะนั้น
- **RAG** = Retrieval ข้อมูลมาใส่ context
- ทั้งสามอย่างทำงานร่วมกันเพื่อให้ Agent ตอบได้แม่นยำและเป็นส่วนตัว

---

**ถัดไป:** [02 — Context Window](02-context-window.md)
