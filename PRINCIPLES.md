# หลักการออกแบบ Memory, Context, และ RAG

> หลักการเหล่านี้ใช้เป็นแนวทางในการออกแบบระบบ Memory, Context, และ RAG สำหรับ AI Agent

---

## 1. Context ไม่ใช่ Memory

Context คือทุกอย่างที่อยู่ใน prompt ขณะนั้น — Memory คือสิ่งที่ Agent เก็บไว้ใช้ข้าม session

- Context มีอายุสั้น ถูกสร้างใหม่ทุกครั้งที่มีการเรียกใช้
- Memory ต้องถูกเขียน intentionally ไม่ใช่บันทึกทุกอย่างอัตโนมัติ
- อย่าใช้ Context Window เป็นระบบ记忆

## 2. Retrieval ต้องตรวจสอบได้ (Grounded and Traceable)

ข้อมูลที่检索มาใช้ในการตอบ ต้องมีที่ไปที่มา

- ทุก chunk ที่检索ได้ควรมี source identifier
- ระบบควรบอกได้ว่าคำตอบนี้มาจาก document ไหน
- อย่าให้ Agent ตอบจากความน่าจะเป็นของ model อย่างเดียว

## 3. Memory Writes ต้องมีความตั้งใจ (Intentional)

ไม่ใช่ทุก interaction ที่ควรถูกบันทึก

- กำหนดเงื่อนไขชัดเจนว่าเมื่อไหร่ควรเขียน memory
- เนื้อหาที่ sensitive ควรต้องมี human review
- ใช้ explicit save commands หรือ confirmation ก่อนเขียน

## 4. ผู้ใช้ควบคุมความจำส่วนตัว

Personal memory ต้องเป็นของผู้ใช้ ไม่ใช่ของระบบ

- ผู้ใช้ควรลบ memory ได้
- ผู้ใช้ควรดู memory ที่ถูกเก็บได้
- ผู้ใช้ควรแก้ไข memory ที่ผิดได้
- ต้องขอ consent ก่อนบันทึกข้อมูลส่วนตัว

## 5. เลือก Context ที่จำเป็นเท่านั้น

Context ที่มากเกินไปทำให้คุณภาพคำตอบลดลง

- ให้ context ที่เกี่ยวข้องกับคำถามเท่านั้น
- จัดลำดับความสำคัญของ context: system > memory > retrieved docs > tool results
- ใช้ token budget อย่างมีประสิทธิภาพ

## 6. ตรวจสอบข้อมูลที่检索มา

Retrieved context อาจผิด เก่า หรือไม่เกี่ยวข้อง

- ตรวจสอบ freshness ของข้อมูล
- ตรวจจับ contradiction ระหว่าง retrieved chunks
- อย่าเชื่อข้อมูลที่检索มาทั้งหมด

## 7. แยก User Memory, Project Memory, และ System State

Memory แต่ละประเภทมีอายุ ownership และ access control ต่างกัน

| ประเภท | เจ้าของ | อายุ | ใครเข้าถึงได้ |
|---|---|---|---|
| User Memory | ผู้ใช้ | indefinite | เฉพาะ session ที่ผู้ใช้เกี่ยวข้อง |
| Project Memory | ทีม | ตลอด project | สมาชิก project |
| System State | ระบบ | ชั่วคราว | เฉพาะระบบ |

## 8. หลีกเลี่ยงการเก็บข้อมูล sensitive โดยค่าเริ่มต้น

- กำหนด whitelist ว่าอะไรเก็บได้ อะไรเก็บไม่ได้
- ใช้ automatic redaction สำหรับข้อมูลส่วนตัว
- เข้ารหัส memory ที่ sensitive
- มี audit trail สำหรับการเข้าถึงข้อมูล

## 9. Memory ต้องแก้ไขและตรวจสอบได้ (Editable and Inspectable)

- ต้องมี API หรือ UI สำหรับดู memory
- ต้องแก้ไข memory ที่ผิดได้
- ต้องลบ memory ได้
- ควรมี version history ของ memory

## 10. ออกแบบสำหรับการลืมและการแก้ไข

Forgetting เป็น feature ไม่ใช่ bug

- กำหนด TTL (time-to-live) สำหรับ memory
- มี consolidation process เพื่อ merge หรือ summarize memory เก่า
- correction ควร propagate ไปยังข้อมูลที่เกี่ยวข้อง
- การลบข้อมูลควร complete ไม่ใช่แค่ mark as deleted

## 11. เลือกใช้ Local-first เมื่อเป็นไปได้

- ข้อมูลผู้ใช้ควรเก็บในเครื่องก่อน
- ใช้ vector database แบบ local (เช่น SQLite + vector extension)
- Synchronization เป็นทางเลือก ไม่ใช่ข้อบังคับ
- Local-first = privacy first

## 12. Practical over Hype

เลือก solution ที่ใช้งานได้จริง ปลอดภัย และ maintainable

- RAG ไม่จำเป็นต้อง complex — บางครั้ง keyword search ก็พอ
- Embedding ไม่ใช่คำตอบสำหรับทุกปัญหา
- เริ่มจาก simple แล้วค่อยเพิ่ม complexity เมื่อจำเป็น
- วัดผลก่อนตัดสินใจเปลี่ยน approach
