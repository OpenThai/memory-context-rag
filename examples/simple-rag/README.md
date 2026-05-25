# Simple RAG — การ检索เอกสารมาประกอบการตอบ

> ค้นหาจาก knowledgebase เล็กๆ เพื่อตอบคำถาม

---

## Scenario

Support chatbot ที่检索จากคู่มือผลิตภัณฑ์เพื่อตอบคำถามลูกค้า

## Minimal Workflow

```
Query → Search Knowledgebase → Retrieve Chunks → Build Context → LLM → Response
```

## Input/Output

```
Knowledgebase:
  [doc-01] "วิธีคืนสินค้า: ลูกค้าสามารถคืนสินค้าได้ภายใน 14 วัน..."
  [doc-02] "การจัดส่ง: จัดส่งฟรีเมื่อซื้อครบ 500 บาท..."

Input:
  Query: "คืนสินค้าได้กี่วัน"

Output:
  "คืนสินค้าได้ภายใน 14 วัน ตามนโยบายการคืนสินค้าครับ"
  (พร้อม citation: doc-01)
```

## Data Stored

- Knowledgebase documents (fixed)
- Query history (optional, สำหรับ analytics)
- No personal data

## Risk Level

**ต่ำ-กลาง** — ขึ้นอยู่กับ content ใน knowledgebase

## Failure Cases

- Knowledgebase ไม่ครอบคลุมคำถาม
- Retrieval ได้ chunks ไม่เกี่ยวข้อง
- Chunks ที่检索มา contradict กัน
- คำถามนอก domain (ควรตอบว่าไม่ทราบ)

## Future Implementation

- Multi-format document support (PDF, HTML)
- Hybrid search (vector + keyword)
- Citation with source links
- "I don't know" detection
