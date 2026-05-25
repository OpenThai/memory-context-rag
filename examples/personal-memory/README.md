# Personal Memory — ความจำส่วนตัวผู้ใช้

> เก็บ preferences และข้อมูลผู้ใช้ — พร้อม consent และ deletion

---

## Scenario

ผู้ช่วย AI ที่จำชื่อผู้ใช้ สไตล์การชอบ และการตั้งค่า — โดยผู้ใช้ควบคุมได้

## Minimal Workflow

```
1. ผู้ใช้: "我叫สมชาย ชอบคำตอบสั้นๆ"
2. Agent: "ขออนุญาตบันทึก: ชื่อสมชาย, ชอบคำตอบสั้น — ถูกต้องไหม?"
3. ผู้ใช้: "ใช่"
4. [บันทึก memory]

session ถัดไป:
1. ผู้ใช้: "สวัสดี"
2. Agent: "สวัสดีคุณสมชายครับ มีอะไรให้ช่วย?"
   (อ่านจาก personal memory)
```

## Input/Output

```
Input:
  User: "จำไว้ว่าผมไม่กินเผ็ด"

Output:
  Agent: "บันทึกแล้วครับ: ไม่กินเผ็ด"
  Agent: (ในการถามครั้งถัดไป) "ร้านนี้มีอาหารไม่เผ็ดด้วยนะครับ"
```

## Data Stored

- ชื่อผู้ใช้
- Preferences (รูปแบบการตอบ, topics ที่สนใจ)
- Settings (ภาษา, timezone)
- Interaction highlights (optional)

## Risk Level

**กลาง-สูง** — ข้อมูลส่วนตัว ต้องมี consent และ privacy protection

## Failure Cases

- จำข้อมูลผิดโดยไม่ตรวจสอบ
- ไม่ถาม consent
- ผู้ใช้ลืมว่าถูกบันทึกอะไรไว้
- sensitive data ถูกบันทึกโดยไม่ตั้งใจ

## Future Implementation

- Memory management UI (ดู/แก้ไข/ลบ)
- Automatic sensitive data detection
- Export memory — data portability
- Memory decay หรือ archive สำหรับข้อมูลเก่า
