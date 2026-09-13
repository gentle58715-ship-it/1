# Secure temporary Windows RDP

เครื่อง Windows ชั่วคราวสำหรับการทดสอบผ่าน GitHub Actions และ Tailscale

> เครื่องถูกลบเมื่อ job จบ ไฟล์และโปรแกรมในเครื่องจะไม่ถูกเก็บไว้สำหรับรอบถัดไป ห้ามใช้แทนคอมพิวเตอร์ถาวร และควรใช้งานตามข้อกำหนดของ GitHub Actions

## การตั้งค่าครั้งแรก

ไปที่ **Settings → Secrets and variables → Actions → New repository secret** แล้วเพิ่ม:

- `TS_AUTHKEY` — Tailscale auth key แบบ **ephemeral**, จำกัดสิทธิ์ และตั้งวันหมดอายุสั้น
- `RDP_PASSWORD` — รหัสผ่านเฉพาะสำหรับเซสชันนี้ ควรยาวและไม่ซ้ำกับบัญชีอื่น
- `TS_TAILNET` — ชื่อ tailnet ใช้เฉพาะ workflow cleanup
- `TS_API_KEY` — Tailscale API key จำกัดสิทธิ์ ใช้เฉพาะ workflow cleanup

อย่าใส่ค่าจริงของ secrets ลงในไฟล์ repository, README หรือ Actions log

## เริ่มใช้งาน

1. ติดตั้ง Tailscale และแอป Remote Desktop บนโทรศัพท์
2. เข้า Tailscale บนโทรศัพท์ด้วยบัญชีเดียวกับ tailnet
3. เปิดแท็บ **Actions** ใน repository
4. เลือก **Secure temporary Windows RDP (A)** แล้วกด **Run workflow**
5. เปิด log ของขั้นตอน **Create private RDP account** เพื่อดูที่อยู่ Tailscale
6. เชื่อมด้วยชื่อผู้ใช้ `PrivateRemote` และรหัสจาก secret `RDP_PASSWORD`

RDP ถูกจำกัดให้รับการเชื่อมต่อจากช่วงที่อยู่ Tailscale เท่านั้น ไม่มีการเปิดพอร์ต RDP สู่สาธารณะ ไม่มีการอัปโหลดภาพหน้าจอ และไม่มีการส่งต่อ workflow อัตโนมัติ

## ระยะเวลาการใช้งาน

ตั้งค่าได้ 5–330 นาทีต่อรอบ เมื่อหมดเวลาเครื่องจะถูกยกเลิก ใช้ workflow B เป็นตัวสำรองสำหรับการเริ่มด้วยตนเองเท่านั้น ไม่ใช่การต่อเวลาอัตโนมัติ

## การล้างอุปกรณ์เก่า

เปิด **Tailscale cleanup** ในแท็บ Actions โดยเริ่มด้วย `dry_run: true` เพื่อตรวจรายการก่อน หากรายการถูกต้องจึงรันใหม่โดยปิด dry run

## การเก็บข้อมูล

บันทึกโค้ดกลับ GitHub และเก็บเอกสารสำคัญไว้ในบริการจัดเก็บของบัญชีตัวเองก่อนปิดเซสชัน อย่าเก็บข้อมูลส่วนตัวหรือบัญชีสำคัญไว้บน runner ชั่วคราว
