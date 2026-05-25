# Memory Taxonomy

> ตารางจำแนกประเภทของ memory ในระบบ AI Agent

---

| Memory Type | ใช้เก็บอะไร | อายุข้อมูล | ตัวอย่าง | ความเสี่ยง | วิธีตรวจสอบ | ควรให้ผู้ใช้แก้ไขได้ไหม |
|---|---|---|---|---|---|---|
| **Conversation Memory** | ประวัติการสนทนาใน session | จบ session | ข้อความ 10 ล่าสุด | ต่ำ (session) | ดู history ได้ | ไม่จำเป็น (ชั่วคราว) |
| **Working Memory** | ข้อมูลระหว่างทำงาน task | จบ task | task progress, intermediate results | ต่ำ | log | ไม่จำเป็น (internal) |
| **User Profile Memory** | ข้อมูลส่วนตัวผู้ใช้ | indefinite | ชื่อ, preferences, language | สูง | ดู/edit/delete | ✅ จำเป็น |
| **Project Memory** | ข้อเท็จจริง project | ตลอด project | framework, decisions, conventions | กลาง | review โดย team | ✅ (ต้อง approval) |
| **Episodic Memory** | เหตุการณ์หรือ interaction | 1-6 months | "ผู้ใช้ถามเกี่ยวกับ X เมื่อวันก่อน" | กลาง | แสดง timeline | ✅ ควร |
| **Semantic Memory** | ความรู้ทั่วไปที่ Agent เรียนรู้ | indefinite | "Python เป็น dynamic language" | ต่ำ | ตรวจสอบ accuracy | อาจจะ |
| **Procedural Memory** | ขั้นตอน วิธีการทำงาน | indefinite | "วิธี deploy: git push → CI → deploy" | กลาง | review steps | ✅ ควร |
| **Tool Result Memory** | ผลลัพธ์จากการเรียก tool | จบ task | "search_web('weather') → 25°C" | ต่ำ | log | ไม่จำเป็น (cache) |
| **System State** | สถานะของระบบในขณะนั้น | ชั่วคราว | current mode, active tools, flags | ต่ำ | dashboard | ไม่จำเป็น |
| **Retrieved Context** | ข้อมูลที่检索มาใช้ตอบ | 1 response | chunks จาก knowledgebase | กลาง | audit log | ไม่จำเป็น (ชั่วคราว) |

---

## สรุป

| คุณสมบัติ | ควรมี | Notes |
|---|---|---|
| ผู้ใช้แก้ไขได้ | User, Episodic, Project, Procedural | แล้วแต่นโยบาย |
| ต้องการ consent | User, Episodic | การเก็บข้อมูลส่วนตัว |
| ควรมี TTL | Episodic, Conversation | ลด data bloat |
| ควรมี audit | User, Project, Episodic | สำหรับ accountability |
| ควรเข้ารหัส | User (sensitive) | privacy |
