# Simple Context — ตัวอย่าง Context พื้นฐาน

> สร้าง context โดยไม่มีความจำระยะยาว — เฉพาะ conversation history + system instructions

---

## Scenario

Agent ตอบคำถามลูกค้าตามข้อมูลใน context ที่ให้ โดยไม่มี long-term memory

## Minimal Workflow

```
System Instructions → Conversation History → User Query → LLM
```

## Input/Output

```
Input:
  System: "คุณคือ support agent สำหรับร้านอาหาร"
  History: ["ลูกค้า: มีโต๊ะว่าง今晚ไหม", "Agent: มีครับ กี่ท่าน"]
  Query: "2 ท่านครับ"

Output:
  "ได้ครับ จองโต๊ะ 2 ท่าน今晚  เวลากี่โมงดีครับ"
```

## Data Stored

- Conversation history (ชั่วคราว, ใน session)
- No persistent memory

## Risk Level

**ต่ำ** — ไม่มีการเก็บข้อมูลถาวร

## Failure Cases

- Context overflow เมื่อ history ยาวเกินไป
- ไม่มี personalization — ไม่จำลูกค้า
- ทุก session เริ่มต้นใหม่หมด

## Future Implementation

- ใช้ sliding window + summary
- เพิ่ม recent N messages
- ใช้ system prompt ที่ปรับตาม query type
