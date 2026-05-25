# Context Engineering Checklist

> Checklist สำหรับออกแบบและประกอบ context

---

## Pre-processing

- [ ] วิเคราะห์ user intent
- [ ] ระบุ entities และ keywords
- [ ] กำหนดความซับซ้อนของคำถาม
- [ ] จำเป็นต้อง检索 memory หรือไม่?
- [ ] จำเป็นต้อง检索 knowledgebase หรือไม่?

## System Instructions

- [ ] Role และ purpose ชัดเจน
- [ ] Rules และ constraints ครบถ้วน
- [ ] ไม่ยาวเกินไป (กิน token โดยไม่จำเป็น)
- [ ] ปลอดภัย — ไม่มี sensitive info
- [ ] อัปเดตตาม use case

## Memory Selection

- [ ] 检索เฉพาะ memory ที่ relevant กับ query
- [ ] จำกัดจำนวน memory (top-K)
- [ ] จัดลำดับตาม relevance + recency
- [ ] แยก user memory, project memory, system state
- [ ] redact sensitive content ถ้าไม่จำเป็น

## Document Selection (RAG)

- [ ] 检索เฉพาะ chunks ที่ relevant
- [ ] จำกัดจำนวน chunks (top-K)
- [ ] จัดลำดับด้วย reranker (ถ้ามี)
- [ ] ตรวจสอบ freshness
- [ ] ตรวจสอบ diversity (no duplicates)

## Tool Results

- [ ] Summarize ถ้าผลลัพธ์ยาว
- [ ] ระบุว่าเป็นผลจาก tool ไหน
- [ ] ตรวจสอบ error ก่อนใส่ context
- [ ] อย่าใส่ credential หรือ raw API response

## Conversation History

- [ ] เก็บ N ข้อความล่าสุด
- [ ] Summarize ข้อความที่เก่ากว่า
- [ ] ระบุ speaker (user/assistant)
- [ ] ไม่เก็บ iterations ที่ล้มเหลว

## Safety & Constraints

- [ ] "ตอบจาก context ที่ให้เท่านั้น"
- [ ] "ถ้าไม่รู้ให้บอกว่าไม่ทราบ"
- [ ] "อย่าสร้างข้อมูลเพิ่มเติม"
- [ ] "อย่าใช้ข้อมูลนอกเหนือจาก context"
- [ ] "อ้างอิง source ทุกครั้ง"

## Output Format

- [ ] กำหนด format ที่ชัดเจน (text, JSON, bullet)
- [ ] กำหนด length (สั้น/ยาว)
- [ ] กำหนด style (เป็นทางการ/ casual)
- [ ] กำหนด language

## Token Budget

- [ ] กำหนด total budget (ไม่เกิน context window)
- [ ] จัดสรร budget ตาม priority
- [ ] มี padding สำหรับ response
- [ ] Monitor actual usage

## Verification

- [ ] ตรวจสอบ context completeness
- [ ] ตรวจสอบว่าไม่มี sensitive data
- [ ] ตรวจสอบ citation ถูกต้อง
- [ ] ตรวจสอบว่าไม่ contradict กันเอง
- [ ] Post-generation verification
