# 2 — Context Window

> ขีดจำกัดของ token และการจัดการ context อย่างมีประสิทธิภาพ

---

## Context Window คืออะไร

Context window คือ **จำนวน token สูงสุดที่ LLM สามารถรับได้ในการเรียกหนึ่งครั้ง**

Token คือหน่วยย่อยของข้อความ — 1 token ≈ 0.75 คำในภาษาอังกฤษ, แต่ในภาษาไทย 1 token ≈ 1-2 ตัวอักษร

```
ข้อความ: "สวัสดีครับ วันนี้อากาศดี"
tokens:   [สวัส][ดี][ครับ][ ][วัน][นี้][อากาศ][ดี]
```

---

## ขนาด Context Window (คร่าวๆ)

| Model | Max Tokens | เทียบกับข้อความ |
|---|---|---|
| GPT-4o | ~128K | ≈ หน้าหนังสือ 90 หน้า |
| Claude 4 | ~200K | ≈ หน้าหนังสือ 150 หน้า |
| Gemini 1.5 Pro | ~2M | ≈ หน้าหนังสือ 1,500 หน้า |
| Local models | 4K-32K | ≈ 2-25 หน้า |

---

## อะไรกิน Token ใน Context

```
┌─────────────────────────────────┐
│ System Instructions   ~500-2K    │
│ Conversation History  ~1K-10K   │
│ Retrieved Documents   ~2K-20K   │
│ Memories              ~500-5K   │
│ Tool Results          ~500-5K   │
│ User Query            ~50-500   │
│ Output (response)     ~500-4K   │
├─────────────────────────────────┤
│ Total                           │
└─────────────────────────────────┘
```

---

## Context Overflow

เมื่อ context เต็ม สิ่งที่เกิดขึ้น:

1. **Truncation** — ตัดประวัติส่วนที่เก่าที่สุดทิ้ง (common approach)
2. **Summarization** — สรุป history แทนการเก็บทั้งหมด (ดีกว่า truncation)
3. **Error** — บาง API คืน error ถ้าเกิน limit

**ข้อควรระวัง:** Truncation อาจตัดข้อมูลสำคัญทิ้ง ควร summarize หรือเลือก highlight แทน

---

## More Context ≠ Better

เชื่อหรือไม่ว่า **context ที่มากเกินไปอาจทำให้คำตอบแย่ลง**:

- **Lost in the Middle** — LLM มักจำข้อมูลที่อยู่ต้นและท้าย context ได้ดี แต่ลืมข้อมูลตรงกลาง (Liu et al., 2024)
- **Noise-to-Signal Ratio** — ข้อมูลที่ไม่เกี่ยวข้องใน context รบกวนความแม่นยำ
- **Computational Cost** — ยิ่ง context ยาว ยิ่งเสียเวลาและเงิน

---

## แนวทางการจัดการ Context Window

### 1. Priority Order

จัดลำดับความสำคัญของ content ใน context:

```
1. System Instructions (ตั้งต้น)
2. User Query (ล่าสุด)
3. Relevant Memory (检索มาเฉพาะที่จำเป็น)
4. Retrieved Documents (ที่เกี่ยวข้องจริงๆ)
5. Tool Results (ถ้ามี)
6. Conversation History (เท่าที่จำเป็น)
```

### 2. Context Selection

- อย่าใส่ memory ทุกอันที่มี — เลือกเฉพาะที่ relevant
- อย่าใส่ทุก document ที่检索เจอ — เลือก top-K
- อย่าเก็บ conversation history ทั้งหมด — ใช้ recent + summarized

### 3. Context Compression

- Summarize conversation history
- Summarize long documents ก่อนใส่ context
- ใช้ smaller chunks สำหรับ retrieved documents

### 4. Token Budget

กำหนด budget ล่วงหน้า:

```
System:      2,000 tokens (fixed)
Memory:      1,000 tokens (variable)
Documents:   4,000 tokens (top-3 chunks)
History:     3,000 tokens (last N + summary)
Tool Output: 1,000 tokens (if any)
Safety:        500 tokens (fixed)
─────────────────────────────
Total:      11,500 / 16,000 tokens (มี buffer)
```

---

## ตัวอย่างการจัดการ Context Overflow

```
Scenario: สนทนา 50 ข้อความ, context window = 8K tokens

❌ ไม่ดี: เก็บทั้งหมด 50 ข้อความ → truncate ตอนเกิน → เสียข้อมูล
✅ ดี:   เก็บ 10 ข้อความล่าสุด + summary 40 ข้อความแรก
✅ ดีกว่า: เก็บ 10 ข้อความล่าสุด + highlight important facts
```

---

## Common Mistakes

- **ใส่ทุกอย่างจนเต็ม window** → Lost in the Middle
- **ไม่มีการ summarize** → 浪費 token
- **ใช้ truncation อย่างเดียว** → ตัดข้อมูลสำคัญ
- **ไม่รู้ token count ของภาษาไทย** → tokenizer ของแต่ละ model ต่างกัน
- **ไม่มีการ monitoring** → ไม่รู้ว่าตัวเองกิน token เท่าไหร่

---

## Safety Notes

- ตรวจสอบว่า context ไม่มี sensitive data ที่ไม่จำเป็น
- อย่าใส่ memory ทั้งหมดลง context โดยไม่กรอง
- ระวัง token bombing (attacker ส่ง context ยาวๆ เพื่อทำให้ system ล้ม)

---

## Summary

- Context window มีจำกัด — ต้องบริหารจัดการ
- จัดลำดับความสำคัญของ content
- ใช้ compression และ selection
- More context ≠ better — เลือกเฉพาะที่เกี่ยวข้อง
- Monitor token usage

---

**ถัดไป:** [03 — Short-term Memory](03-short-term-memory.md)
