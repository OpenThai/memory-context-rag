# Coding Agent Memory — ความจำสำหรับ Coding Agent

> เก็บ repo structure, conventions, commands, tests pattern, และ known issues

---

## Scenario

Coding agent ที่จำรายละเอียดของ codebase — structure, style, dependencies — เพื่อทำงานได้ถูกต้องตาม project convention

## Minimal Workflow

```
Agent receives task → Retrieve coding memory → Understand context → Execute task
```

## Input/Output

```
Coding Memory:
  - repo: memory-context-rag
  - structure: docs/, examples/, notes/
  - conventions: Thai content, Markdown files
  - test_cmd: (none yet)
  - known_issues: ["ไม่มี code implementation จนกว่า Phase 6"]
  - decisions: ["2026-05-20 — ใช้ Markdown แทน code จน prototype"]

Task: "เพิ่ม doc เกี่ยวกับ embedding"
→ Agent รู้ว่า:
  - ต้องใช้ Markdown
  - content เป็นภาษาไทย
  - เก็บใน docs/ directory
  - ใช้ heading style เดียวกับ docs อื่น
→ เขียน doc ที่ consistent
```

## Data Stored

- Project structure
- Framework และ dependencies
- Test commands
- Coding conventions
- Known issues
- Common patterns
- Architecture decisions

## Risk Level

**กลาง** — coding memory ผิด = Agent สร้าง code ที่ไม่ consistent

## Failure Cases

- Memory stale เมื่อ dependencies/best practices เปลี่ยน
- Convention ที่ไม่ถูกต้องถูกบันทึก
- Agent ยึดติดกับ pattern เก่า
- Memory จาก project อื่นมา influence project นี้

## Future Implementation

- Auto-scan repo เพื่อสร้าง memory
- Integration กับ git history
- Validation — มี test convention จริงไหม
- Memory per branch หรือ per feature
