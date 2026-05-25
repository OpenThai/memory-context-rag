# RAG Design Checklist

> Checklist สำหรับออกแบบระบบ RAG

---

## Source Documents

- [ ] กำหนด scope ของ knowledgebase
- [ ] เลือก documents ที่มีคุณภาพและ trustable
- [ ] จัดระเบียบ documents ตามหมวดหมู่
- [ ] กำหนด metadata schema
- [ ] กำหนด trust level สำหรับแต่ละ source

## Document Quality

- [ ] ตรวจสอบความถูกต้องของ content
- [ ] ตรวจสอบ freshness — ข้อมูลยังไม่ outdated
- [ ] ลบ documents ที่ซ้ำหรือไม่เกี่ยวข้อง
- [ ] ตรวจสอบ formatting — clean, parseable

## Chunking Strategy

- [ ] เลือก chunk method (fixed, semantic, recursive)
- [ ] กำหนด chunk size ที่เหมาะสม
- [ ] กำหนด chunk overlap
- [ ] preserve document structure (headings, sections)
- [ ] รักษา metadata ในแต่ละ chunk

## Metadata Strategy

- [ ] source identifier
- [ ] date / version
- [ ] trust level
- [ ] tags / categories
- [ ] chunk position (สำหรับ reconstruction)

## Embedding Model

- [ ] เลือก multilingual model (ถ้าต้องการภาษาไทย)
- [ ] ทดสอบ quality กับ use case จริง
- [ ] กำหนด normalization strategy
- [ ] re-index เมื่อเปลี่ยน model

## Search Method

- [ ] Vector search
- [ ] Keyword search (BM25)
- [ ] Hybrid fusion strategy
- [ ] ตั้งค่า similarity threshold

## Ranking / Reranking

- [ ] กำหนด initial ranking criteria
- [ ] ใช้ reranker หรือไม่
- [ ] กำหนด top-K สำหรับ context
- [ ] กำหนด diversity check

## Context Builder

- [ ] กำหนด context structure (sections)
- [ ] กำหนด token budget
- [ ] จัดลำดับ content ใน context
- [ ] เพิ่ม safety instructions
- [ ] รวม citations

## Citation Strategy

- [ ] ทุก chunk มี source identifier
- [ ] LLM ถูกบอกให้อ้าง source
- [ ] Citation format ที่ consistent
- [ ] ตรวจสอบ citation accuracy

## Hallucination Checks

- [ ] ตรวจสอบว่า answer อยู่ใน context หรือไม่
- [ ] ตรวจจับ contradiction ระหว่าง chunks
- [ ] ใช้ evaluation tools
- [ ] Fallback strategy เมื่อไม่ confident

## Evaluation

- [ ] สร้าง test set (in-domain + out-of-domain)
- [ ] วัด retrieval metrics (hit rate, MRR, precision, recall)
- [ ] วัด answer quality (faithfulness, relevance)
- [ ] วัด hallucination rate
- [ ] User feedback collection

## Monitoring

- [ ] Monitor retrieval quality
- [ ] Monitor hallucination rate
- [ ] Monitor token usage
- [ ] Monitor user satisfaction
- [ ] Alert เมื่อ metrics ต่ำกว่า threshold
