# Glossary — คำศัพท์ Memory, Context, และ RAG

> คำอธิบายสั้นๆ สำหรับคำศัพท์สำคัญ เรียงตามตัวอักษร

---

### Agent State

สถานะของ Agent ในขณะทำงาน ประกอบด้วย task progress, intermediate results, tool outputs, plan state และข้อมูลชั่วคราวอื่นๆ ที่จำเป็นสำหรับการทำงานให้เสร็จ

### Chunking

กระบวนการแบ่งเอกสารขนาดใหญ่ออกเป็นส่วนย่อย (chunks) ก่อนนำไป index เพื่อให้检索ได้มีประสิทธิภาพ

### Citation

การอ้างอิงแหล่งที่มาของข้อมูลที่ Agent ใช้ในการตอบ ช่วยให้ผู้ใช้ตรวจสอบความถูกต้องของคำตอบได้

### Context

ทุกอย่างที่รวมอยู่ใน prompt ขณะนั้น — system instructions, conversation history, retrieved documents, memory, tool results — ที่ LLM ใช้ในการตอบ

### Context Engineering

การออกแบบและประกอบ context อย่างมีประสิทธิภาพ — การเลือกว่าอะไรควรอยู่ใน context, การจัดลำดับ, การตัดข้อมูลที่ไม่จำเป็น (compression), และการจัดการ token budget

### Context Window

ขีดจำกัดของจำนวน token ที่ LLM สามารถรับได้ในหนึ่งครั้ง การจัดการ context window ที่ดีคือการเลือกข้อมูลที่เกี่ยวข้องที่สุดให้อยู่ใน window

### Embedding

การแปลงข้อมูล (ข้อความ, รูปภาพ) เป็น vector (ชุดตัวเลข) ที่แสดงความหมายของข้อมูล ในพื้นที่(vector space) ที่คล้ายกันจะอยู่ใกล้กัน

### Episodic Memory

ความจำเกี่ยวกับเหตุการณ์หรือประสบการณ์เฉพาะ — Agent จำได้ว่า "เมื่อวานผู้ใช้ถามเกี่ยวกับ X" คล้าย episodic memory ในมนุษย์

### Evaluation

การวัดคุณภาพของระบบ — retrieval quality, answer faithfulness, citation accuracy, hallucination rate

### Forgetting

กลไกการลืม — การกำหนดให้ memory ค่อยๆ ถูกลบหรือลดความสำคัญเมื่อเวลาผ่านไป หรือเมื่อข้อมูลไม่ถูกต้อง

### Grounding

การทำให้คำตอบของ Agent เชื่อมโยงกับแหล่งข้อมูลที่ตรวจสอบได้ ป้องกัน hallucination

### Hallucination

การที่ LLM สร้างข้อมูลที่ดูเหมือนจริงแต่ไม่ถูกต้อง RAG และ memory system ช่วยลด hallucination โดยให้ context ที่ถูกต้อง

### Knowledgebase

คลังความรู้ที่มีโครงสร้าง — เอกสาร, notes, ข้อมูลอ้างอิง, FAQ — ที่ Agent สามารถ检索มาใช้ในการตอบ เครื่องมือ: document store, vector database, wiki

### Long-term Memory

ความจำระยะยาว — ข้อมูลที่ Agent เก็บไว้ข้าม session เช่น ผู้ใช้ชื่ออะไร, project นี้ใช้ framework อะไร, ข้อตกลงของทีม

### Memory

ข้อมูลที่ Agent เก็บไว้ใช้ในอนาคต — เป็นพื้นฐานสำหรับ learning, personalization, และการทำงานข้าม session

### Memory Consolidation

กระบวนการเปลี่ยน short-term memory ให้เป็น long-term memory — สรุป, merge, prune ข้อมูลเพื่อเก็บเฉพาะสิ่งที่สำคัญ

### Memory Decay

การลดความสำคัญของ memory เมื่อเวลาผ่านไป — ข้อมูลเก่ามี weight น้อยลงในการ检索

### Metadata

ข้อมูลกำกับ — source, date, author, tags, trust level — ที่ช่วยในการกรองและจัดระเบียบ document หรือ memory

### Project Memory

ความจำเกี่ยวกับ project — architecture decisions, conventions, TODOs, known issues, environment setup

### RAG (Retrieval-Augmented Generation)

เทคนิคการ检索ข้อมูลจาก knowledgebase หรือ document store แล้วนำมาใส่ใน context เพื่อให้ LLM ตอบโดยมีแหล่งอ้างอิง

### Ranking

การจัดลำดับผลลัพธ์ที่检索ได้ตามความเกี่ยวข้องกับคำถาม — เพื่อเลือกเฉพาะ chunk ที่ดีที่สุดใส่ context

### Reranking

การจัดลำดับใหม่ด้วย model เฉพาะเพื่อปรับปรุงคุณภาพ — หลังจาก initial retrieval

### Retrieval

กระบวนการค้นหาข้อมูลที่เกี่ยวข้อง — อาจใช้ vector search, keyword search, หรือ hybrid search

### Semantic Memory

ความจำเกี่ยวกับความรู้ทั่วไป — Agent รู้ว่า "Python เป็นภาษา programming" ไม่เกี่ยวกับเหตุการณ์เฉพาะ

### Short-term Memory

ความจำระยะสั้น — conversation history ใน session ปัจจุบัน, working memory, temporary state

### Similarity Search

การค้นหา vector ที่ใกล้ที่สุดใน vector space — ใช้ distance metrics เช่น cosine similarity, euclidean distance

### Token Budget

จำนวน token สูงสุดที่สามารถใช้ใน context — ต้องจัดสรรระหว่าง system prompt, memory, retrieved docs, tool results, และ conversation history

### User Profile

ข้อมูลเกี่ยวกับผู้ใช้ — preferences, settings, language preference, communication style — ใช้ personalise interaction

### Vector Database

ฐานข้อมูลที่เก็บ vectors และรองรับ similarity search — ใช้ในการค้นหา semantic matches

### Working Memory

ความจำขณะทำงาน — intermediate results, current task state, tool call results — ข้อมูลที่จำเป็นสำหรับ task ปัจจุบัน
