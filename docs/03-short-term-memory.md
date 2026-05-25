# 3 — Short-term Memory

> ความจำระยะสั้นของ Agent: conversation memory, working memory, temporary state

---

## Short-term Memory คืออะไร

Short-term memory คือ **ข้อมูลที่ Agent เก็บไว้เฉพาะใน session ปัจจุบัน** — เมื่อ session จบ ข้อมูลนี้จะหายไป (หรือถูก consolidate เป็น long-term)

เปรียบเทียบ: Short-term memory เหมือน **กระดาษจด** ที่เราเขียนระหว่างประชุม — จดไว้ใช้ตอนประชุม แต่ทิ้งเมื่อเลิกประชุม

---

## ประเภทของ Short-term Memory

### 1. Conversation Memory

ประวัติการสนทนาภายใน session เดียวกัน

```
User: "我叫สมชาย"
Agent: "สวัสดีคุณสมชาย"

User: "เมื่อกี้我叫อะไร"
Agent: "คุณชื่อสมชายค่ะ" (อ่านจาก conversation memory)
```

### 2. Working Memory

ข้อมูลที่ Agent เก็บระหว่างทำงาน task ใด task หนึ่ง

```
Task: สร้างเว็บไซต์ร้านอาหาร
Working Memory:
  - client name: "ร้านข้าวแกงป้าไพลิน"
  - chosen template: "modern-bistro"
  - colors: #FF6B35, #2D2D2D
  - pages needed: home, menu, contact
```

### 3. Temporary Tool State

ผลลัพธ์จากการเรียก tool ที่ยังไม่จบ workflow

```
Tool: search_web("ร้านอาหารญี่ปุ่น ย่านสยาม")
Result: [list of 10 restaurants]

Tool: get_reviews("restaurant-1")
Result: 4.5 stars, 120 reviews
```

### 4. Task Progress

สถานะของ task ที่กำลังทำ — steps ที่ทำไปแล้ว, steps ที่เหลือ

```
Task: "เขียนบทความเกี่ยวกับ AI"
Progress:
  ✅ ค้นหาข้อมูล
  ✅ เขียน outline
  🔄 เขียนเนื้อหา
  ⏳ ตรวจทาน
```

---

## วงจรชีวิตของ Short-term Memory

```
สร้าง → ใช้งาน → จบ session
  │                      │
  │                      ▼
  │                 ถูกลบทิ้ง
  │                      │
  │                      ▼
  │              (หรือ) Consolidate → Long-term
  │
  └── อาจถูก summarize หรือ highlight
```

---

## ทำไมต้องมี Short-term Memory

- **Follow-up questions** — ผู้ใช้ถามต่อได้โดยไม่ต้องบอกใหม่
- **Multi-step tasks** — Agent ทำงานหลายขั้นตอนโดยไม่ลืม
- **Tool orchestration** — ผลลัพธ์จาก tool หนึ่งใช้ต่อในอีก tool
- **Coherent conversation** — การสนทนาต่อเนื่อง ไม่สะดุด

---

## การจัดการ Short-term Memory

### กลยุทธ์ที่ 1: Recent N Messages

เก็บ N ข้อความล่าสุด, ตัดข้อความเก่า

```
✅ ง่าย เร็ว
❌ ถ้า N น้อย → เสียบริบท
❌ ไม่มี prioritization
```

### กลยุทธ์ที่ 2: Sliding Window + Summary

เก็บ N ข้อความล่าสุด + summary ของข้อความที่เก่ากว่า

```
✅ เก็บบริบทสำคัญ
✅ ประหยัด token
❌ summary อาจเสียรายละเอียด
```

### กลยุทธ์ที่ 3: Highlight + Window

เก็บเฉพาะข้อความที่ marked as "important" + N ข้อความล่าสุด

```
✅ เลือกเก็บเฉพาะสำคัญ
✅ ประหยัด token ที่สุด
❌ ต้องมี mechanism ในการ mark importance
```

---

## Common Mistakes

- **ไม่แยก short-term และ long-term** — เก็บทุกอย่างใน session เป็น long-term ทันที ข้อมูลรก
- **เก็บ history ทั้งหมดไม่จำกัด** — context overflow
- **ไม่ summarize หรือ highlight** — จำไม่ได้ว่าอะไรสำคัญ
- **ลืม clear working memory** — เมื่อ task จบ ข้อมูล task เก่ายังค้าง
- **ข้าม session ไม่ได้** — ถ้าผู้ใช้กลับมาทีหลัง session หาย

---

## Safety Notes

- Session data ควรถูก cleanup เมื่อจบ
- อย่าเก็บข้อมูล sensitive ใน working memory นานเกินจำเป็น
- Short-term memory ไม่ควรมี persistent storage โดย default
- ระวังข้อมูลรั่วข้าม session

---

## Summary

- Short-term memory = ข้อมูลชั่วคราวใน session
- มีหลายประเภท: conversation, working, tool state, task progress
- เลือกกลยุทธ์: recent N, sliding window + summary, หรือ highlight + window
- Session จบ → ข้อมูลหาย (หรือ consolidate)
- อย่าสับสนกับ long-term memory

---

**ถัดไป:** [04 — Long-term Memory](04-long-term-memory.md)
