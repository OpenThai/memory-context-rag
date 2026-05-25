# Memory Design Checklist

> Checklist สำหรับออกแบบระบบ memory ของ AI Agent

---

## พื้นฐาน

- [ ] กำหนด purpose ของ memory — ใช้ทำอะไร? เพื่อใคร?
- [ ] เลือก memory type ที่เหมาะสม (user, project, system)
- [ ] กำหนด storage location (local, cloud, hybrid)
- [ ] กำหนด data ownership — ใครเป็นเจ้าของ?
- [ ] กำหนด data format (structured, unstructured, vector)

## Write Policy

- [ ] กำหนด trigger — เมื่อไหร่ควรเขียน memory
- [ ] กำหนด content — อะไรควรบันทึก อะไรไม่ควร
- [ ] ต้องมี intentional write หรือ automatic?
- [ ] ต้อง human review หรือไม่?
- [ ] มี deduplication mechanism หรือไม่?

## Read Policy

- [ ] กำหนด retrieval trigger — เมื่อไหร่ควร检索
- [ ] กำหนด ranking criteria — relevance, freshness, importance
- [ ] กำหนด maximum memories per query
- [ ] มี user isolation — memory ของ user อื่นไม่ถูก检索

## Update Policy

- [ ] กำหนด update trigger — เมื่อไหร่ควร update
- [ ] วิธี update: replace, merge, หรือ append
- [ ] มี version history หรือไม่
- [ ] มี confirmation ก่อน update หรือไม่

## Delete Policy

- [ ] ผู้ใช้ลบ memory ได้
- [ ] มี TTL สำหรับ memory
- [ ] Hard delete หรือ soft delete
- [ ] มี grace period สำหรับ recovery
- [ ] เมื่อ user account ถูกลบ → memory ถูกลบด้วย

## Access Control

- [ ] User isolation
- [ ] Role-based access สำหรับ project memory
- [ ] Encrypted storage สำหรับ sensitive memory
- [ ] Audit log สำหรับ read/write/delete

## Sensitive Data

- [ ] มี automatic redaction สำหรับ PII
- [ ] มี classification levels
- [ ] ไม่เก็บ credentials หรือ secret โดย default
- [ ] มี mechanism สำหรับ user-report sensitive content

## Review & Evaluation

- [ ] มี mechanism ให้ user ดู memory ของตัวเอง
- [ ] ผู้ใช้แก้ไข memory ที่ผิดได้
- [ ] มี memory quality metrics
- [ ] มี periodic review (consolidation, pruning)
- [ ] มี feedback loop — user บอกได้ว่า memory ผิด
