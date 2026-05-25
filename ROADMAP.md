# Roadmap

> แผนการพัฒนา Memory, Context, and RAG for AI Agents

---

## Phase 0 — Memory and Context Foundation

- [x] กำหนดขอบเขตเนื้อหา
- [x] สร้างโครงสร้าง repository
- [ ] เขียนอธิบายความแตกต่างระหว่าง Memory, Context, RAG
- [ ] อธิบาย Context Window และข้อจำกัด
- [ ] อธิบาย Short-term Memory และ Long-term Memory
- [ ] สร้าง GLOSSARY.md

## Phase 1 — Core Lessons

- [ ] เขียนบท Agent State: สถานะ Agent ระหว่างทำงาน
- [ ] เขียนบท Knowledgebase: การออกแบบฐานความรู้
- [ ] เขียนบท Context Engineering: การสร้าง context ที่มีประสิทธิภาพ
- [ ] เขียนบท Memory Writing: กลไกการเขียน อัปเดต และลบ memory
- [ ] เพิ่ม text diagrams ในทุกบท

## Phase 2 — RAG Concepts

- [ ] เขียนบท RAG Pipeline: query → retrieval → generation
- [ ] เขียนบท Chunking & Indexing: การแบ่งเอกสารและการสร้างดัชนี
- [ ] เขียนบท Embeddings & Vector Search: การค้นหาเชิงความหมาย
- [ ] เขียนบท Ranking & Reranking: การจัดลำดับความเกี่ยวข้อง
- [ ] เขียนบท RAG Evaluation: การวัดคุณภาพ

## Phase 3 — Practical Examples

- [ ] examples/simple-context: ตัวอย่าง context พื้นฐาน
- [ ] examples/project-memory: การจำ project facts
- [ ] examples/personal-memory: ความจำส่วนตัวผู้ใช้
- [ ] examples/simple-rag: การ检索เอกสาร
- [ ] examples/document-knowledgebase: การจัดระเบียบ document
- [ ] examples/coding-agent-memory: ความจำสำหรับ coding agent
- [ ] examples/local-first-memory: memory ที่เก็บในเครื่อง

## Phase 4 — Evaluation and Verification

- [ ] สร้าง test set สำหรับ RAG evaluation
- [ ] เขียนแนวทางการวัด faithfulness และ citation quality
- [ ] อธิบาย hallucination detection methods
- [ ] สร้าง benchmark สำหรับ memory system

## Phase 5 — Local-first and Privacy Patterns

- [ ] อธิบาย local-first architecture (SQLite, local vector store)
- [ ] แนวทางการ encrypt memory ที่ sensitive
- [ ] consent mechanism สำหรับการเก็บข้อมูลผู้ใช้
- [ ] audit trail สำหรับการเข้าถึง memory

## Phase 6 — Prototype Implementations

- [ ] Python prototype: simple memory system
- [ ] Python prototype: RAG pipeline พื้นฐาน
- [ ] ตัวอย่างการใช้ LangChain/LlamaIndex สำหรับ RAG
- [ ] ตัวอย่าง local vector database ด้วย SQLite + extensions
- [ ] ตัวอย่าง context builder สำหรับ Agent

## Phase 7 — Community Contributions

- [ ] รับแปลเนื้อหาภาษาอังกฤษ
- [ ] รับตัวอย่างเพิ่มเติมจาก community
- [ ] จัดทำ workshop materials
- [ ] เผยแพร่เป็น open learning resource
