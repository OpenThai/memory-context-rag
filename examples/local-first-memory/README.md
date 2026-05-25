# Local-first Memory — Memory ที่เก็บในเครื่องผู้ใช้

> แนวคิดการเก็บ memory ใน local device — privacy first, cloud sync เป็น optional

---

## Scenario

ผู้ใช้ต้องการ AI assistant ที่เก็บข้อมูลส่วนตัวในเครื่อง — ไม่ส่งข้อมูลไป cloud โดยไม่จำเป็น

## Minimal Workflow

```
User Data → Local Storage (SQLite/File) → Local Retrieval → Local LLM / API
                                                                    │
                                                            (optional, redacted)
```

## Input/Output

```
Storage Location: ~/.local-memory/
  - user-preferences.json       ← ข้อมูลทั่วไป
  - memory-store.db             ← SQLite + vector extension
  - conversations/              ← encrypted history (optional)
  - memory-config.yaml          ← settings, TTL, access control

Query → Local Memory Store → retrieve → local or remote LLM
```

## Data Stored

- User preferences
- Project memories (per directory)
- Session history (encrypted)
- All in local filesystem

## Risk Level

**ต่ำ** (สำหรับ privacy) — ข้อมูลไม่离开เครื่องโดย default
**กลาง** (สำหรับ data loss) — local storage อาจหาย ถ้าไม่มี backup

## Failure Cases

- Data loss เมื่อ device เสียหาย (ไม่มี backup)
- ไม่สามารถ sync ข้าม device
- Local storage เต็ม
- การเข้ารหัสไม่เพียงพอ

## Future Implementation

- SQLite + sqlite-vec สำหรับ local vector search
- Encrypted export/import
- Optional cloud sync (end-to-end encrypted)
- Cross-device sync protocol
- Backup automation
