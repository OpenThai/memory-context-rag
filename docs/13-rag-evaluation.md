# 13 — RAG Evaluation

> การวัดคุณภาพของระบบ RAG — retrieval quality, answer faithfulness, hallucination checks

---

## ทำไมต้อง Evaluation

RAG ที่ไม่มี evaluation คือ:

- ไม่รู้ว่า retrieval ดีพอหรือยัง
- ไม่รู้ว่าคำตอบถูกต้องหรือ hallucinate
- ไม่รู้ว่าควรปรับ chunk size หรือ embedding model
- ไม่รู้ว่าผู้ใช้ได้ประโยชน์จริงหรือไม่

---

## RAG Evaluation สามระดับ

```
Level 1: Retrieval Quality
    → 检索ได้ documents ที่เกี่ยวข้องหรือไม่

Level 2: Answer Quality
    → LLM ตอบถูกต้อง อ้างอิงถูกไหม

Level 3: User Experience
    → ผู้ใช้พึงพอใจหรือไม่
```

---

## Level 1: Retrieval Quality

### Metrics

| Metric | วัดอะไร | ค่า |
|---|---|---|
| Hit Rate | % ของ queries ที่ retrieve ได้ relevant chunk |越高越好 |
| MRR (Mean Reciprocal Rank) | relevant chunk อยู่ในอันดับที่เท่าไหร่ |越高越好 |
| Precision@K | % ของ retrieved chunks ที่ relevant |越高越好 |
| Recall@K | % ของ relevant chunks ทั้งหมดที่ retrieve มาได้ |越高越好 |

### Example

```
Query: "วิธี backup PostgreSQL"

Relevant chunks ใน corpus: [A, C, F]

Retrieved (K=5): [A, B, C, D, E]
Hit Rate: 100% (A และ C ถูก retrieve)
Precision@5: 2/5 = 0.4 (2 จาก 5 relevant)
Recall@5: 2/3 = 0.67 (2 จาก 3 relevant chunks)
MRR: (1/1 + 1/3) / 2 = 0.67
```

---

## Level 2: Answer Quality

### Metrics

| Metric | วัดอะไร |
|---|---|
| Faithfulness | คำตอบสอดคล้องกับ context หรือไม่ |
| Answer Relevance | คำตอบตรงคำถามหรือไม่ |
| Citation Accuracy | citation ถูกต้องตรง source หรือไม่ |
| Hallucination Rate | สัดส่วนของคำตอบที่ไม่มีใน context |

### การวัด Faithfulness

```
Context: "PostgreSQL เริ่มต้นด้วยคำสั่ง sudo systemctl start postgresql"

Response A: "ใช้ sudo systemctl start postgresql ในการเริ่ม PostgreSQL"
→ Faithful ✅

Response B: "ใช้ sudo service postgresql start ในการเริ่ม PostgreSQL"
→ Not faithful ❌ (ข้อมูลไม่ตรงกับ context)

Response C: "PostgreSQL เริ่มต้นด้วย systemctl"
→ Partially faithful (สั้นไป, ข้อมูลถูกแต่ไม่ครบ)
```

### Citation Accuracy

```
Response: "ตาม docs/postgres-setup.md: ใช้ sudo systemctl start postgresql"
→ Citation ถูกต้อง ✅ (มีใน source จริง)

Response: "ตาม docs/postgres-setup.md: ใช้ sudo service postgresql start"
→ Citation ผิด ❌ (ไม่มีใน source)
```

---

## Level 3: User Experience

- User satisfaction survey
- Task completion rate
- Time to answer
- Follow-up question rate (越低越好 = ตอบชัด)

---

## การสร้าง Test Set

### Methodology

```
1. เลือก documents ตัวแทน
2. สร้าง Q&A pairs:
   - คำถามที่ตอบได้จาก document
   - คำถามที่ต้อง检索หลาย chunks
   - คำถามนอกโดเมน (ควรตอบว่าไม่ทราบ)
3. กำหนด relevant chunks สำหรับแต่ละคำถาม
4. รัน RAG pipeline → evaluate
```

### Example Test Set

```yaml
- query: "วิธีติดตั้ง PostgreSQL"
  expected_sources: ["docs/postgres-setup.md"]
  expected_answer_contains: ["sudo apt install", "postgresql"]
  type: simple

- query: "PostgreSQL กับ MySQL ต่างกันยังไง"
  expected_sources: ["docs/postgres-vs-mysql.md"]
  expected_answer_contains: ["ACID", "licensing"]
  type: comparative

- query: "weather today"
  expected_sources: []
  expected_answer: "I don't know"  # out of domain
  type: out_of_domain
```

---

## Evaluation Tools

| Tool | Description | Notes |
|---|---|---|
| RAGAS | metric-based RAG evaluation | faithfulness, relevance |
| TruLens | LLM app evaluation | feedback functions |
| DeepEval | evaluation framework | modular metrics |
| LangSmith | tracing + evaluation | integrated with LangChain |
| Manual | human evaluation | gold standard |

---

## Common Mistakes

- **วัดแค่ retrieval** — ข้าม answer quality
- **测试 set ไม่ cover edge cases** — เช่น out-of-domain queries
- **ไม่วัด hallucination** — คำตอบอาจดูดีแต่ผิด
- **ใช้ metrics อย่างเดียว** — ไม่ดู qualitative feedback
- **ไม่ iterate** — evaluate ครั้งเดียวแล้วหยุด
- **ไม่แยก evaluation components** — ไม่รู้ว่าปัญหาอยู่ที่ retrieval, ranking, หรือ generation

---

## Continuous Evaluation

```
After each change:
  - เปลี่ยน chunk size → re-evaluate
  - เปลี่ยน embedding model → re-evaluate
  - เปลี่ยน reranker → re-evaluate
  - เพิ่ม documents → re-evaluate

Monthly:
  - รีวิว test set — ยัง relevant อยู่ไหม
  - เพิ่ม edge cases
  - ตรวจสอบ user feedback
```

---

## Safety Notes

- Test set ไม่ควรมี sensitive data
- Evaluation ไม่ควรใช้ production data โดยไม่ anonymize
- Human evaluation จำเป็นสำหรับ safety-critical apps
- ระวัง evaluation bias — evaluator model อาจมี bias

---

## Summary

- RAG evaluation มี 3 levels: retrieval, answer, user experience
- วัด faithfulness, citation accuracy, hallucination rate
- สร้าง test set ที่ cover หลาย scenarios
- ใช้ tools ช่วย evaluation
- Continuous evaluation — iterate หลังจากทุก change

---

**ถัดไป:** [14 — Privacy and Safety](14-privacy-and-safety.md)
