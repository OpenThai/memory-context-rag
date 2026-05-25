# Memory, Context, and RAG for AI Agents

> เรียนรู้วิธีการออกแบบระบบ Memory Context และ Retrieval-Augmented Generation สำหรับ AI Agent — เนื้อหาภาษาไทย

---

## สรุปสั้น

AI Agent ที่ดีต้อง "จำ" เป็น จัดการ "บริบท" ได้ และใช้ "ความรู้ภายนอก" อย่างปลอดภัย

Repo นี้รวบรวมแนวคิด การออกแบบ ข้อควรระวัง และตัวอย่างเชิงปฏิบัติของระบบ **Memory**, **Context**, และ **RAG** ที่ใช้ใน AI Agent — เขียนเป็นภาษาไทย อธิบายให้เข้าใจง่าย แต่ไม่ลดทอนความลึกของเนื้อหา

---

## Memory, Context, RAG ต่างกันยังไง

| แนวคิด | ความหมาย | ตัวอย่าง |
|---|---|---|
| **Memory** | สิ่งที่ Agent จำไว้ข้าม session — ความชอบผู้ใช้ ข้อเท็จจริง project ประวัติการทำงาน | Agent จำได้ว่าผู้ใช้ชอบตอบแบบสั้น |
| **Context** | ทุกอย่างที่อยู่ใน prompt ขณะนั้น — คำสั่ง system, memory ที่เรียกมา, document ที่检索, tool results | ขณะถาม "สรุปให้หน่อย" มี history 10 ข้อความล่าสุดใน context |
| **RAG** | กระบวนการ检索ข้อมูลจาก knowledgebase มาใส่ใน context เพื่อให้ตอบถูกต้อง | Agent ค้นหาจากคู่มือ แล้วตอบคำถามลูกค้า |

ทั้งสามอย่างทำงานร่วมกัน: **Memory** → เก็บข้อมูลระยะยาว | **RAG** →检索ความรู้ที่ตรงกับคำถาม | **Context** → รวมทุกอย่างเข้าด้วยกันใน prompt

---

## ความสัมพันธ์กับ OpenThai

OpenThai สนับสนุนการพัฒนา **Personal AI, AI Agent, AI Coding Agent, Memory & Context System, Tool-use, MCP, Agentic Workflow, Verification, และ AI Infrastructure สำหรับผู้ใช้ไทย**

Repo นี้เป็นส่วนหนึ่งของชุดความรู้ด้าน Agent Engineering:

| Repo | เน้น |
|---|---|
| [ai-agent-fundamentals](https://github.com/OpenThai/ai-agent-fundamentals) | พื้นฐาน AI Agent |
| [agentic-design-patterns](https://github.com/OpenThai/agentic-design-patterns) | รูปแบบการออกแบบ Agent |
| [tool-use-for-ai-agents](https://github.com/OpenThai/tool-use-for-ai-agents) | การใช้ Tools, Function Calling, MCP |
| **memory-context-rag** (นี้) | Memory, Context, RAG |

---

## ใครควรอ่าน

- นักพัฒนาที่เริ่มสร้าง AI Agent
- ทีมที่ต้องการออกแบบระบบ记忆และ检索
- ผู้สนใจ RAG และ Context Engineering
- ผู้ใช้ไทยที่อยากเข้าใจระบบ AI ระดับ deeper

---

## เนื้อหา

### พื้นฐาน Memory และ Context

| บท | หัวข้อ | คำอธิบาย |
|---|---|---|
| [01](docs/01-what-are-memory-context-rag.md) | Memory, Context, RAG คืออะไร | ความหมายและความแตกต่าง |
| [02](docs/02-context-window.md) | Context Window | ขีดจำกัดของ token และการจัดการ |
| [03](docs/03-short-term-memory.md) | Short-term Memory | ความจำระยะสั้นสำหรับ session |
| [04](docs/04-long-term-memory.md) | Long-term Memory | ความจำระยะยาวข้าม session |
| [05](docs/05-agent-state.md) | Agent State | สถานะของ Agent ในระหว่างทำงาน |
| [06](docs/06-knowledgebase.md) | Knowledgebase | การออกแบบฐานความรู้ |
| [11](docs/11-memory-writing-and-updating.md) | Memory Writing | การเขียนและอัปเดต memory |
| [12](docs/12-context-engineering.md) | Context Engineering | การประกอบ context ที่มีประสิทธิภาพ |

### Retrieval-Augmented Generation (RAG)

| บท | หัวข้อ | คำอธิบาย |
|---|---|---|
| [07](docs/07-retrieval-augmented-generation.md) | RAG Pipeline | กระบวนการ检索และ生成 |
| [08](docs/08-chunking-and-indexing.md) | Chunking & Indexing | การแบ่งและจัดทำดัชนี |
| [09](docs/09-embeddings-and-vector-search.md) | Embeddings & Vector Search | การค้นหาเชิงความหมาย |
| [10](docs/10-ranking-and-reranking.md) | Ranking & Reranking | การจัดลำดับความเกี่ยวข้อง |
| [13](docs/13-rag-evaluation.md) | RAG Evaluation | การวัดคุณภาพ RAG |

### ความปลอดภัยและความเป็นส่วนตัว

| บท | หัวข้อ | คำอธิบาย |
|---|---|---|
| [14](docs/14-privacy-and-safety.md) | Privacy & Safety | ความเป็นส่วนตัวและความปลอดภัย |

---

## ตัวอย่าง

| ตัวอย่าง | สถานการณ์ |
|---|---|
| [simple-context](examples/simple-context/) | สร้าง context โดยไม่มีความจำระยะยาว |
| [project-memory](examples/project-memory/) | จดจำข้อเท็จจริงและการตัดสินใจของ project |
| [personal-memory](examples/personal-memory/) | ความจำส่วนตัวผู้ใช้แบบมี consent |
| [simple-rag](examples/simple-rag/) | ค้นหาจากเอกสารมาตอบคำถาม |
| [document-knowledgebase](examples/document-knowledgebase/) | จัดระเบียบเอกสารพร้อม metadata |
| [coding-agent-memory](examples/coding-agent-memory/) | ความจำของ coding agent |
| [local-first-memory](examples/local-first-memory/) | Memory ที่เก็บในเครื่องผู้ใช้ |

---

## แนวทางการออกแบบ

ดูเพิ่มเติมได้ที่:

- [PRINCIPLES.md](PRINCIPLES.md) — หลักการออกแบบ
- [ARCHITECTURE.md](ARCHITECTURE.md) — สถาปัตยกรรมระบบ
- [GLOSSARY.md](GLOSSARY.md) — คำศัพท์
- [ROADMAP.md](ROADMAP.md) — แผนการพัฒนา
- [SECURITY.md](SECURITY.md) — ความปลอดภัยและข้อมูลส่วนบุคคล

---

## สถานะ

**Early educational material** — เนื้อหากำลังพัฒนาอย่างต่อเนื่อง
