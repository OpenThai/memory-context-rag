# Project Memory — ความจำสำหรับโปรเจกต์

> เก็บข้อเท็จจริง การตัดสินใจ TODOs และสถาปัตยกรรมของ project

---

## Scenario

Coding agent ที่จำรายละเอียดของ project — framework, conventions, decisions — เพื่อทำงานได้ consistent

## Minimal Workflow

```
User: "เพิ่ม API endpoint ใหม่"
  → Agent 检索 project memory
  → รู้ว่า project ใช้ FastAPI, PEP8, pytest
  → สร้าง endpoint ที่ consistent กับ codebase
```

## Input/Output

```
Input:
  Project Memory:
    - framework: FastAPI
    - database: PostgreSQL
    - test: pytest
    - style: PEP8
    - decision: "ไม่ใช้ ORM — ใช้ raw SQL"
  Query: "เพิ่ม endpoint GET /users"

Output:
  - สร้าง endpoint ตาม convention ของ project
  - ใช้ raw SQL (ตาม decision)
  - มี test ด้วย pytest
```

## Data Stored

- Framework และ dependencies
- Architecture decisions
- Coding conventions
- Known issues
- TODO items
- Environment setup

## Risk Level

**กลาง** — project memory มีผลต่อทุก task ใน project

## Failure Cases

- Memory stale เมื่อ dependencies อัปเดต
- Decision ที่ไม่ถูกต้องถูกบันทึก
- ไม่มี review process สำหรับ project-level memory

## Future Implementation

- Versioned project memory (สนับสนุน rollback)
- Auto-detect changes ใน project
- Integration กับ git history
