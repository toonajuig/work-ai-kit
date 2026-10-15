# Work AI Kit

ชุดเตรียม environment, คำสั่งส่วนตัว และ skills สำหรับ **Claude Code + Codex บน Windows** เพื่อให้เครื่องทำงานมีเครื่องมือ dev ตาม baseline ของเครื่องส่วนตัว และใช้แนวทางเดียวกัน: ตอบไทย วางแผนก่อนแก้โค้ด แก้เฉพาะจุด และตรวจผลก่อนส่งงาน

repo นี้เป็นแหล่งไฟล์สำหรับติดตั้ง ไม่ใช่โปรเจกต์ React/Next.js และไม่มี installer ที่ต้องรัน

## เริ่มใช้ที่เครื่องทำงาน

repo เป็น private ให้ใช้บัญชี GitHub ที่มีสิทธิ์เข้าถึง ถ้ามี Git แล้วให้ clone ผ่าน HTTPS หรือวิธีที่บริษัทอนุญาต:

```powershell
git clone https://github.com/toonajuig/work-ai-kit.git
cd work-ai-kit
```

**ถ้ามีแค่ VS Code แต่ยังไม่มี Git/Node:** เปิด [หน้า repo](https://github.com/toonajuig/work-ai-kit) ใน browser ที่ล็อกอิน GitHub แล้ว เลือก **Code → Download ZIP** แตกไฟล์และเปิดโฟลเดอร์ด้วย VS Code วิธีนี้ไม่ต้องใช้ Git/Node/WinGet เพื่ออ่านคู่มือ ([GitHub source archives](https://docs.github.com/en/repositories/working-with-files/using-files/downloading-source-code-archives))

ต้องมี AI ที่อ่านไฟล์และสั่งงานบนเครื่องได้ก่อน หากยังไม่มี ให้เปิดใช้ Claude Code/Codex ผ่านแอปหรือ extension ที่บริษัทอนุญาต README ไม่สามารถติดตั้งโปรแกรมเองเพียงเพราะเปิดใน VS Code

เปิดโฟลเดอร์นี้ด้วย Claude Code หรือ Codex แล้วส่งข้อความนี้:

```text
อ่าน README.md และ ENVIRONMENT.md ให้ครบ แล้วช่วยเตรียมเครื่อง Windows นี้
ให้ใช้งาน dev กับ Claude Code และ Codex ตาม Work AI Kit

เริ่มจากตรวจ environment แบบอ่านอย่างเดียว เปรียบเทียบ baseline ของเครื่องต้นทาง
และกฎของ repo งาน สรุปสิ่งที่ขาด/เวอร์ชันที่ต่าง พร้อมเสนอแผนติดตั้งให้ฉันยืนยัน
ลงเฉพาะ Core และ optional ที่ฉันเลือก ไม่ downgrade หรือเขียนทับของเดิมเอง

หลัง environment ผ่านแล้ว ค่อยติดตั้ง global instructions และ skills ตาม README
สำรองไฟล์ก่อนแก้ ถ้ามีเนื้อหาต่างกันให้เสนอวิธีรวมและรอฉันยืนยัน
ไม่เปลี่ยน permissions/sandbox หรือย้ายข้อมูลล็อกอิน เมื่อเสร็จให้รายงาน
เวอร์ชัน/path ที่ตรวจได้ และสิ่งที่ต้องตรวจต่อหลังเปิด terminal/session ใหม่
```

หาก AI ไม่สามารถเข้าถึง repo private ผ่านลิงก์ได้ ให้ clone หรือ Download ZIP ด้วยตัวเองก่อน แล้วให้ AI อ่านไฟล์ในเครื่อง ไม่ต้องส่ง token ให้ AI

## ไฟล์ใน repo

```text
work-ai-kit/
  README.md
  ENVIRONMENT.md
  claude/
    CLAUDE.md
  codex/
    AGENTS.md
  skills/
    karpathy-guidelines/
    diagnose/
      scripts/hitl-loop.template.sh
    scrutinize/
    codebase-design/
      DEEPENING.md
      DESIGN-IT-TWICE.md
      agents/openai.yaml
```

แต่ละโฟลเดอร์ skill มี `SKILL.md` ของตัวเอง ต้องคัดลอกทั้งโฟลเดอร์เพื่อรักษาไฟล์อ้างอิงให้ครบ

| ไฟล์ต้นทาง | ปลายทางเริ่มต้นบน Windows |
| --- | --- |
| `claude/CLAUDE.md` | `%USERPROFILE%\.claude\CLAUDE.md` |
| `codex/AGENTS.md` | `%USERPROFILE%\.codex\AGENTS.md` |
| `skills/<name>/` สำหรับ Claude Code | `%USERPROFILE%\.claude\skills\<name>\` |
| `skills/<name>/` สำหรับ Codex | `%USERPROFILE%\.agents\skills\<name>\` |

Codex ใช้ `CODEX_HOME` แทน `.codex` ได้ หากเครื่องตั้งตัวแปรนี้ไว้ ให้ใช้ค่าจริงสำหรับ global `AGENTS.md` ส่วน user skills ยังใช้ตำแหน่ง `.agents/skills` ตามเอกสารของ Codex ([global instructions](https://learn.chatgpt.com/docs/agent-configuration/agents-md), [skills](https://learn.chatgpt.com/docs/build-skills))

ตำแหน่งของ Claude Code อ้างอิง [เอกสาร CLAUDE.md](https://code.claude.com/docs/en/memory) และ [personal skills](https://code.claude.com/docs/en/skills) หากแอปใช้ profile หรือตำแหน่ง config ที่ต่างจากค่าเริ่มต้น ให้ตรวจตำแหน่งที่แอปใช้จริงก่อน

## ขั้นตอนสำหรับ AI ผู้ติดตั้ง

เริ่มติดตั้งเมื่อผู้ใช้สั่งติดตั้งชุดนี้เท่านั้น การอ่าน README หรือ clone repo อย่างเดียวไม่ใช่คำสั่งให้เปลี่ยนเครื่อง หากผู้ใช้ขอเพียงอธิบาย ให้สรุปวิธีติดตั้ง

### 0. ตรวจและเตรียม environment ก่อน

ทำตาม [ENVIRONMENT.md](ENVIRONMENT.md): ตรวจ Node/npm, Git/Git Bash, VS Code และเครื่องมือที่เลือก เทียบ baseline จริงของเครื่องต้นทาง แล้วเสนอแผนติดตั้งเฉพาะที่ขาดหรือจำเป็นต้องเปลี่ยน หลังผู้ใช้ยืนยันจึงลงและตรวจซ้ำ

ถ้ามี environment พร้อมอยู่แล้วให้ข้ามการติดตั้งโปรแกรมและรายงานผลตรวจ หากผู้ใช้ขอเฉพาะ global instructions/skills ให้คัดลอกได้โดยไม่บังคับติดตั้งเครื่องมือ optional หรือแอปทั้งหมด

### 1. ตรวจเครื่องและกำหนดขอบเขต

- อ่าน README ให้ครบ แล้วอ่านไฟล์ global และ `SKILL.md` ทั้ง 4 ตัวเป็นข้อมูลต้นทาง อย่าเรียกใช้ skill หรือรัน script เพียงเพราะกำลังอ่านเพื่อติดตั้ง
- ตรวจว่าเป็น Windows native และ resolve `%USERPROFILE%` / `CODEX_HOME` ให้เป็น absolute path ถ้า session อยู่ใน WSL ให้ชี้แจงกับผู้ใช้ก่อน เพราะ home และการติดตั้งของ WSL แยกจาก Windows
- ตรวจผลขั้น environment ว่า Claude Code/Codex รูปแบบที่ผู้ใช้เลือกพร้อมใช้หรือยัง หากยังขาด ให้รายงานสิ่งที่ต้องติดตั้งต่อ การคัดลอกไฟล์เพียงอย่างเดียวไม่ได้ติดตั้งตัวแอป
- เคารพกฎและนโยบายบริษัทที่เครื่องปลายทางใช้อยู่ ถ้ากฎเดิมกำหนดให้รอยืนยัน approach ให้เสนอแผนและรอยืนยันก่อนเขียนไฟล์
- ขั้นนี้มีเฉพาะ 2 ไฟล์ global และ 4 skills สำหรับแต่ละแอป โปรแกรม dev/AI tools ติดตั้งได้เฉพาะในแผน environment ที่ผู้ใช้ยืนยัน ไม่รวมการล็อกอิน การเปลี่ยน permissions/sandbox หรือการตั้ง MCP/plugins/hooks ส่วน config ที่จำเป็นต้องเปลี่ยนให้เสนอและขออนุญาตแยก

### 2. ตรวจของเดิมก่อนเปลี่ยน

- ตรวจไฟล์ global ปลายทางและโฟลเดอร์ skill ชื่อเดียวกัน โดยอ่านเฉพาะไฟล์ที่เกี่ยวข้อง ไม่อ่าน credential files, `.env` หรือประวัติแชต
- ถ้าไม่มีปลายทาง ให้เตรียมสร้างตามตาราง ถ้าเนื้อหาเหมือนกันอยู่แล้ว ให้ข้ามและรายงานว่าไม่ต้องเปลี่ยน
- ถ้ามีเนื้อหาต่างกัน ให้แสดงสรุปความต่างและเสนอการรวมที่รักษากฎเดิมของที่ทำงาน แล้วรอผู้ใช้ยืนยันก่อนแก้ไข การอนุญาตติดตั้งไม่ได้อนุญาตให้ทิ้งกฎเดิม
- ตรวจ `AGENTS.override.md` ใน Codex home ถ้ามี ให้รายงานว่าไฟล์นี้อาจทำให้ global `AGENTS.md` ไม่ถูกเลือก ห้ามลบ เปลี่ยนชื่อ หรือเขียนทับ override เพื่อบังคับใช้ชุดนี้
- ถ้าพบ symlink/junction/hard link ให้ตรวจปลายทางของ link ก่อน ห้ามเขียนผ่าน link ที่อาจเปลี่ยนไฟล์อื่นโดยไม่ชี้แจงผู้ใช้

### 3. สำรองและติดตั้ง

- ก่อนแก้ไขไฟล์เดิมที่ได้รับอนุญาตแล้ว ให้สำรองไว้ใต้ `%LOCALAPPDATA%\work-ai-kit\backups\<timestamp>\` และบันทึกว่าแต่ละสำเนามาจาก absolute path ใด เลือก timestamp ที่ไม่ชนกับของเดิม
- เก็บ backup นอกโฟลเดอร์ skills ที่แอปโหลด เพื่อไม่ให้สำเนา `SKILL.md` กลายเป็น skill ซ้ำ
- สร้างโฟลเดอร์ปลายทางที่ขาด แล้วคัดลอกไฟล์ตามตาราง ใช้ไฟล์และโฟลเดอร์ปกติ ไม่ต้องสร้าง links หรือต้องใช้สิทธิ์ Administrator
- ติดตั้ง skills ทั้ง 4 ตัวไปยังทั้ง `.claude/skills` และ `.agents/skills` โดยคัดลอกไฟล์ประกอบครบ อย่าซ้อนเป็น `skills/skills/<name>`
- ถ้าผู้ใช้เลือก merge ให้เขียนเฉพาะเนื้อหาที่ตกลง ไม่แก้ไฟล์ต้นฉบับใน repo และไม่แก้ไฟล์โปรเจกต์งานอื่น
- อย่ารัน `diagnose/scripts/hitl-loop.template.sh` ระหว่างติดตั้ง เพราะเป็นตัวอย่างสำหรับงานดีบัก ไม่ใช่ installer
- ติดตั้งซ้ำได้โดยข้ามไฟล์ที่ไม่เปลี่ยน ไม่สร้าง backup เพิ่มหากไม่ได้เปลี่ยนอะไร และไม่ลบ skills อื่นที่ผู้ใช้ติดตั้งอยู่

### 4. ตรวจผลก่อนรายงานเสร็จ

- ตรวจว่ามี global files ตาม absolute path ที่ resolve จริง และอ่านเป็น UTF-8 ได้ถูกต้อง
- สำหรับแต่ละแอป ตรวจว่ามี `SKILL.md` ทั้ง 4 ตัวและไฟล์ประกอบครบ รวมถึงของเดิมที่ตกลงเก็บ ไม่ดูแค่ชื่อโฟลเดอร์
- ทุกไฟล์ที่คัดลอกตรง ๆ ทั้ง global และ skills ต้องมี hash/เนื้อหาตรงกับต้นทาง ถ้า merge ให้ตรวจ diff ว่ามีเฉพาะสิ่งที่ผู้ใช้อนุญาต
- ถ้ามี override หรือนโยบายที่ทำให้แอปไม่โหลดไฟล์ ให้รายงานข้อจำกัดตามจริง อย่าสรุปว่าใช้งานแล้วเพียงเพราะคัดลอกสำเร็จ
- รายงานแยกให้ชัด: คัดลอกไฟล์แล้ว / ตรวจไฟล์แล้ว / ยืนยันจาก session ใหม่แล้ว พร้อม path ปลายทางและ backup ที่สร้าง

### 5. ตรวจการโหลดใน session ใหม่

ไฟล์ใหม่อาจยังไม่มีผลใน session ที่ใช้ติดตั้ง ให้ผู้ใช้เริ่ม session ใหม่ของแต่ละแอป ไม่ปิด session ปัจจุบันหรืออ้างว่าตรวจการโหลดแล้วโดยยังไม่ได้ตรวจ

- **Claude Code:** เปิด `/memory` ตรวจว่าเห็น global `CLAUDE.md` และเปิด `/skills` ตรวจ skills ที่ติดตั้ง
- **Codex:** ใน session ใหม่ ให้ถามว่า “ระบุ absolute path ของ global AGENTS.md ที่โหลดและสรุปกฎหลัก โดยยังไม่แก้ไฟล์” แล้วตรวจว่ารู้จัก `karpathy-guidelines`
- ทั้งสองแอปควรตอบไทยและเสนอ approach ก่อนแก้โค้ด เว้นแต่กฎที่มีลำดับสูงกว่ากำหนดต่างออกไป

หาก AI ตรวจ session ใหม่เองไม่ได้ ให้รายงานเฉพาะสิ่งที่ตรวจได้ และส่งขั้นตอนตรวจที่เหลือให้ผู้ใช้

## ก่อนใช้กับงานบริษัท

ไฟล์ global ใน repo คัดลอกจากเครื่องต้นทางตามเดิม ทั้งสองไฟล์มีเนื้อหาเหมือนกัน รวมถึงหัวข้อ “Global Claude Instructions” ใน `AGENTS.md`

- มี Obsidian vault path `D:\Obsidian\ToonVault` ให้ตรวจว่าใช้จริงหรือไม่ และเสนอปรับ/ตัดในสำเนาปลายทางก่อนติดตั้ง ไม่สร้าง vault นี้ขึ้นมาเอง
- มีกฎถามก่อนแก้โค้ด/config และก่อน commit, Conventional Commits และ PR ภาษาอังกฤษ 3 sections: Summary / Files Changed / Test plan
- กฎ React Starter เลือก Vite + npm + `.jsx` สำหรับโปรเจกต์ใหม่ หาก repo ที่ทำงานใช้ TypeScript, Next.js หรือ package manager อื่น ให้ตกลงกฎที่เหมาะสมก่อนเริ่มงาน ไม่เปลี่ยนโปรเจกต์เดิมให้ตรง starter
- Markdown instructions เป็นแนวทางให้ AI ไม่ใช่กลไกบังคับ permissions ของแอป ([Claude Code: instructions และ settings](https://code.claude.com/docs/en/memory))

## Skills ที่แนบมา

| Skill | ใช้เมื่อ |
| --- | --- |
| `karpathy-guidelines` | เขียนโค้ดโดยลดความซับซ้อน แก้เฉพาะจุด และกำหนดผลที่ตรวจสอบได้ |
| `diagnose` | ทำซ้ำบั๊ก ทดสอบสมมติฐาน และยืนยันผลการแก้ |
| `scrutinize` | รีวิวความจำเป็นของการเปลี่ยนและเส้นทางทำงานจริง พร้อมหลักฐาน |
| `codebase-design` | ออกแบบ interface และจุดทดสอบของโมดูล |

ตัวอย่างคำสั่งหลังติดตั้ง: “ใช้ scrutinize รีวิว diff นี้ เฉพาะปัญหาที่มีผลจริง” หรือ “ใช้ diagnose วิเคราะห์ error นี้ก่อนเสนอวิธีแก้”

ข้อจำกัดของสำเนาที่แนบมา:

- `diagnose` มี Bash template เสริม หากจะใช้ในงานดีบัก ให้ปรับขั้นตอนให้ตรงงานและใช้ Bash เช่น Git Bash/WSL ตรวจและแปลง CRLF เป็น LF ในสำเนาที่จะรันหากจำเป็น โดยไม่ต้องแปลงระหว่างติดตั้ง
- `diagnose` แนะนำ `improve-codebase-architecture` สำหรับงานต่อยอด ซึ่งไม่ได้อยู่ในชุดนี้ ให้เสนอเป็นงานแยกหากจำเป็น
- โหมด Design It Twice ของ `codebase-design` ใช้หลาย subagents ต้องมีเครื่องมือรองรับและอาจใช้โควตาเพิ่ม การอ่านหลักออกแบบทั่วไปไม่ต้องใช้โหมดนี้
- Skills เป็น snapshot จากเครื่องต้นทาง ไม่ได้อัปเดต upstream อัตโนมัติ หาก session ไม่มีเครื่องมือตามชื่อที่ skill กล่าวถึง ให้ใช้เครื่องมือเทียบเท่าที่มีหรือแจ้งข้อจำกัด

## อัปเดตและย้อนกลับ

หลัง repo มีอัปเดต ให้ `git pull --ff-only` ใน checkout ที่ไม่มีงานแก้ค้าง แล้วทำขั้นตอนตรวจของเดิมและติดตั้งซ้ำ เปรียบเทียบก่อนคัดลอกทุกครั้ง อย่าทับกฎที่ผู้ใช้ปรับบนเครื่องทำงาน ถ้าเริ่มจาก ZIP จะใช้ `git pull` ไม่ได้ ให้ดาวน์โหลด ZIP ใหม่หรือ clone แยกหลังมี Git แล้ว

ถ้าจะย้อนกลับ ให้ดู path จากรายงาน backup แล้วเสนอการคืนไฟล์ให้ผู้ใช้ยืนยันก่อน สำหรับไฟล์ที่เพิ่มใหม่และไม่มี backup ให้ถามก่อนลบ ไม่ลบทั้ง `.claude`, `.codex` หรือ `.agents`

Claude Code และ Codex ใช้สำเนาแยกกัน เมื่อแก้กฎหรือ skills ให้ตรวจทั้งสองชุดเพื่อไม่ให้เนื้อหาต่างกันโดยไม่ได้ตั้งใจ

## สิ่งที่ไม่ได้รวม

ไม่รวม credentials, API keys, `.env`, ประวัติแชต, project memory, caches, sessions, `settings.json`, `settings.local.json`, `config.toml`, hooks, MCP connections, plugin cache หรือรายการอนุญาตคำสั่งของเครื่องส่วนตัว บัญชีและการเชื่อมต่อของที่ทำงานต้องตั้งแยก

ไม่รวมคำสั่ง Obsidian, ข่าว, forex หรือ automation ส่วนตัว และไม่รวมคำสั่งที่ขาดไฟล์ประกอบจากเครื่องเดิม ไม่มี ZIP หรือ checksum ของแพ็กเกจเดิมใน repo นี้ เพราะ README และเนื้อหา repo จะมีการอัปเดตต่อผ่าน Git
