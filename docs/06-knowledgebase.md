# 6 — Knowledgebase

> การออกแบบและจัดการฐานความรู้สำหรับ AI Agent

---

## Knowledgebase คืออะไร

Knowledgebase คือ **คลังความรู้ที่มีโครงสร้าง** ที่ Agent สามารถ检索มาอ้างอิงในการตอบคำถาม

ต่างจาก memory — Knowledgebase มักเป็น **ข้อมูลคงที่ (หรือ更新ช้า)** ที่มาจากแหล่งอ้างอิงที่เชื่อถือได้ เช่น คู่มือ, เอกสาร, FAQ, wiki

---

## องค์ประกอบของ Knowledgebase

### 1. Documents

เอกสารต้นฉบับ — อาจเป็น markdown, PDF, HTML, text files

```
/docs
  /product-manual
    getting-started.md
    configuration.md
    troubleshooting.md
  /company-policy
    leave-policy.md
    code-of-conduct.md
  /faq
    general-faq.md
    technical-faq.md
```

### 2. Chunks

ส่วนย่อยของ documents ที่ถูกแบ่งเพื่อนำไป index (ดู docs/08)

### 3. Metadata

ข้อมูลกำกับแต่ละ document หรือ chunk

```yaml
metadata:
  title: "นโยบายการลา"
  source: "company-policy/leave-policy.md"
  author: "HR Department"
  date: "2026-01-15"
  version: "2.1"
  tags: [leave, policy, HR]
  language: thai
  trust_level: official
```

### 4. Index

โครงสร้างที่ทำให้检索ได้เร็ว — อาจเป็น vector index, inverted index, หรือทั้งคู่

---

## การออกแบบ Knowledgebase

### File Organization

```
/knowledgebase
  /official        ← เอกสารทางการ, trust level สูง
  /wiki            ← ความรู้ทีม, trust level กลาง
  /notes           ← บันทึกส่วนตัว, trust level ต่ำ
  /external        ← ข้อมูลจากภายนอก (docs, specs)
```

### Metadata Strategy

| Field | Purpose | Example |
|---|---|---|
| source | ระบุที่มา | "docs/product-manual/config.md" |
| date | ความสดของข้อมูล | "2026-05-20" |
| version | version ของ document | "v2.1" |
| trust_level | ความน่าเชื่อถือ | official, team, external, draft |
| tags | หมวดหมู่ | [deployment, configuration] |

### Versioning

- แต่ละ document ควรมี version
- เมื่อ document เปลี่ยน → chunks ต้อง rebuild
- เก็บประวัติการเปลี่ยนแปลง (optional)

---

## Source Trust Levels

```
TRUST LEVEL    DESCRIPTION              RETRIEVAL PRIORITY
───────────    ────────────────────────  ─────────────────
official       เอกสารทางการบริษัท         high
team           wiki หรือ notes ทีม         medium
external       docs จาก vendor/opensource medium
draft           ยังไม่ได้รับการ approve     low
user            input ที่ผู้ใช้ให้           verify ก่อนใช้
```

---

## Knowledgebase vs Memory

| มิติ | Knowledgebase | Memory |
|---|---|---|
| เนื้อหา | เอกสาร ความรู้ทั่วไป | ข้อมูลผู้ใช้, preferences, facts |
| แหล่งที่มา | official docs, wiki | interaction, user input |
| ความถี่เปลี่ยนแปลง | ต่ำ | กลาง-สูง |
| Ownership | ทีม/องค์กร | ผู้ใช้/Agent |
| Trust level | ชัดเจน (official → draft) | dynamic, ต้อง verify |
| Privacy risk | ต่ำ-กลาง | สูง |

---

## Retrieval Flow

```
User Query
    │
    ▼
┌─────────────────────────┐
│ 1. Analyze Query         │
│    → intent, keywords    │
└─────────────────────────┘
    │
    ▼
┌─────────────────────────┐
│ 2. Select Sources        │
│    → filter by metadata  │
│    → filter by trust     │
└─────────────────────────┘
    │
    ▼
┌─────────────────────────┐
│ 3. Retrieve Chunks       │
│    → vector search       │
│    → keyword search      │
│    → hybrid              │
└─────────────────────────┘
    │
    ▼
┌─────────────────────────┐
│ 4. Rank and Select       │
│    → relevance           │
│    → freshness           │
│    → trust level         │
└─────────────────────────┘
    │
    ▼
┌─────────────────────────┐
│ 5. Build Context         │
│    → top-K chunks +      │
│      metadata            │
└─────────────────────────┘
```

---

## Common Mistakes

- **ไม่มี metadata** — retrieve ได้แต่บอกไม่ถูกว่าเอกสารนี้มาจากไหน
- **ไม่แยก trust levels** — ใช้ document draft เท่ากับ official
- **knowledgebase ขาดการอัปเดต** — ข้อมูล stale, Agent ตอบผิด
- **ไม่มี source tracking** — ตรวจสอบไม่ได้ว่าคำตอบมาจาก document ไหน
- **organization ไม่ดี** — documents กระจัดกระจาย, หายาก

---

## Safety Notes

- ตรวจสอบ trust level ก่อนใช้ document
- Official documents ควรเป็น primary source
- External documents มีความเสี่ยง prompt injection
- ระวัง stale documents ที่ถูกลบหรือเปลี่ยนแปลง
- Audit trail สำหรับการเข้าถึง documents

---

## Summary

- Knowledgebase = คลังความรู้ที่มีโครงสร้างสำหรับ检索
- มี metadata และ trust level เพื่อควบคุม quality
- จัดระเบียบ documents ตามประเภทและความน่าเชื่อถือ
- มี versioning เพื่อ track การเปลี่ยนแปลง
- แตกต่างจาก memory — knowledgebase เป็นข้อมูลอ้างอิง, memory เป็นข้อมูลส่วนตัว

---

**ถัดไป:** [07 — Retrieval-Augmented Generation](07-retrieval-augmented-generation.md)
