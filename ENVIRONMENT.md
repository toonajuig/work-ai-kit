# ตรวจและติดตั้ง environment บนเครื่องทำงาน

คู่มือสำหรับ AI ที่รันคำสั่งบน Windows เครื่องปลายทางได้ อ่านคู่กับ [README.md](README.md) และทำขั้นตอนนี้ก่อนติดตั้ง global instructions / skills

เป้าหมายคือให้เครื่องทำงานมีเครื่องมือ dev ที่จำเป็นและเวอร์ชันตาม baseline ของเครื่องต้นทาง โดยตรวจของเดิมก่อน ไม่ใช่คัดลอก PATH หรือโปรแกรมทั้งเครื่อง

## Baseline ที่ตรวจจากเครื่องต้นทาง

สำรวจจริงวันที่ **15 ตุลาคม 2026** บน Windows x64 ด้วย executable ที่ติดตั้งแยกจาก Codex ไม่ใช่เลขเวอร์ชันที่คาดเดาจากรายการโปรแกรม

| เครื่องมือ | เวอร์ชันที่ตรวจได้ | การติดตั้งที่ต้องการ |
| --- | --- | --- |
| Node.js | `24.14.0` | Core สำหรับงาน React / Next.js |
| npm | `11.9.0` | Core ตรวจเวอร์ชันหลังลง Node แยกอีกครั้ง |
| Git for Windows | `2.53.0.windows.1` | Core และต้องมี Git Bash ด้วย |
| Git Bash | GNU Bash `5.2.37(1)-release`, `x86_64-pc-msys` | ใช้ตัวที่มากับ Git for Windows ไม่ติดตั้ง Bash อีกชุด |
| VS Code | `1.140.0`, x64 | Core ถ้ามีแล้วให้ตรวจและรายงานเวอร์ชันก่อน ไม่ติดตั้งซ้ำ |
| WinGet | `1.29.380` | ตัวช่วยติดตั้ง ไม่ต้องบังคับให้เวอร์ชันตรง |
| Corepack | `0.34.6` | เลือกเพิ่มเมื่อ repo ใช้ pnpm / Yarn |
| GitHub CLI | `2.92.0` | เลือกเพิ่มเมื่อใช้ `gh` / PR workflow |
| Claude Code CLI | `2.1.258` | ตรวจหากต้องการใช้ CLI เวอร์ชัน extension แยกจากนี้ |
| Codex CLI | `0.159.2` | ตรวจหากต้องการใช้ CLI ไม่ใช่เวอร์ชัน desktop app |
| Python | `3.14.5` | เลือกเพิ่มเฉพาะงานที่ใช้ Python |
| uv | `0.11.14` | เลือกเพิ่มเฉพาะงาน Python ที่ใช้ uv |
| Docker CLI | `29.6.1` | เลือกเพิ่มเฉพาะงาน containers ยังไม่ได้ทดสอบ Docker daemon ต้นทาง |

เครื่องต้นทางพบ `pnpm` และ `yarn` เป็น Corepack shims แต่ยังไม่ได้ยืนยันเวอร์ชัน package manager ที่ shim จะเลือกใช้ จึงไม่ระบุว่าเวอร์ชันใดติดตั้งพร้อมใช้ ไม่พบ Bun หรือ nvm ใน PATH ที่สำรวจ

PowerShell `7.6.5` ที่ session ต้นทางใช้อยู่มาจาก runtime ของ Codex จึงไม่ตั้งเป็นข้อบังคับให้เครื่องทำงานติดตั้ง PowerShell รุ่นนี้ ใช้ Windows PowerShell ที่มีอยู่ตรวจเบื้องต้นได้

### กติกาเลือกเวอร์ชัน

1. เริ่มจาก exact versions ในตารางสำหรับเครื่องมือที่ผู้ใช้เลือกติดตั้ง แต่ให้ตรวจข้อกำหนดของ repo งานก่อน เช่น `engines`, `packageManager`, `.nvmrc`, `.node-version` และ version manager ที่มีอยู่
2. หาก repo งานหรือนโยบายบริษัทต้องใช้เวอร์ชันอื่น ให้รายงานความต่างและตกลงเป้าหมายก่อนติดตั้ง ไม่เปลี่ยนไฟล์โปรเจกต์ให้ตรงเครื่องส่วนตัว
3. ถ้ามีโปรแกรมอยู่แล้วแต่เวอร์ชันต่างกัน ให้จัดเป็น `different-version` ไม่ใช่ `missing` ห้าม downgrade, uninstall หรือติดตั้ง runtime ซ้อน version manager โดยอัตโนมัติ
4. Baseline เป็น snapshot ไม่ใช่คำรับรองว่าเป็นรุ่นล่าสุด ตรวจสถานะรองรับ/ประกาศด้านความปลอดภัยจากผู้ผลิตด้วย หากต้องเสนอเวอร์ชันใหม่ ให้ระบุเหตุผลและรอผู้ใช้เลือก ไม่เปลี่ยนเป้าหมายเป็น `latest` เอง
5. ถ้า exact version หาไม่ได้จากช่องทางที่อนุญาต ให้รายงาน `version-unavailable` และเสนอทางเลือก ไม่อ้างว่าเครื่องตรง baseline แล้ว

## 1. ตรวจเครื่องแบบอ่านอย่างเดียว

ก่อนติดตั้ง ให้รวบรวมเฉพาะข้อมูลที่เกี่ยวข้อง:

- Windows version / architecture, PowerShell version, Windows native หรือ WSL และข้อจำกัดการติดตั้งของบริษัท
- executable path ทั้งหมดและ version ของ Node, npm, Git, Git Bash, VS Code, WinGet และ AI tools ที่จะใช้
- Corepack / pnpm / Yarn / version manager ที่มีอยู่ เพื่อเลี่ยงการติดตั้งซ้อน
- PATH ที่เกี่ยวกับเครื่องมือเหล่านี้เฉพาะส่วนที่จำเป็น ไม่ dump environment variables ทั้งหมด เพราะอาจมี secrets
- VS Code extensions ที่ติดตั้งใน profile เป้าหมาย ถ้ารัน `code` ไม่ได้ ให้ตรวจว่า VS Code มีอยู่แต่ CLI ยังไม่อยู่ใน PATH หรือไม่

ตัวอย่างคำสั่งอ่านอย่างเดียวใน PowerShell ให้รันแต่ละคำสั่งแยกกันและจัดการกรณีไม่พบโปรแกรม:

```powershell
Get-Command node,npm.cmd,git,code,winget,claude,codex -All -ErrorAction SilentlyContinue
node --version
npm.cmd --version
git --version
code --version
code --list-extensions --show-versions
winget --version
```

ใช้ `npm.cmd` / `npx.cmd` เมื่อ PowerShell ไม่อนุญาตให้รัน `.ps1` ไม่เปลี่ยน Execution Policy ทั้งเครื่องเพื่อแก้ปัญหานี้

อย่านับ executable ใต้ cache/runtime ของ Codex ว่าเครื่องมี Node/Git สำหรับ terminal ปกติแล้ว เช่น `codex-runtimes` และอย่านับ Windows Store alias ที่ยังไม่เปิดใช้งานว่าเป็น runtime ที่พร้อมใช้

### ตรวจ Git Bash แยกจาก WSL

การพบ `bash` จาก `Get-Command bash` ยังไม่เพียงพอ เครื่องต้นทางเองก็พบ `C:\Windows\System32\bash.exe` ก่อน Git Bash

หา root ของ Git for Windows จาก executable/ข้อมูลติดตั้งจริง แล้วตรวจและรัน `<Git root>\bin\bash.exe --version` ตัวอย่างสำหรับตำแหน่งมาตรฐาน:

```powershell
& 'C:\Program Files\Git\bin\bash.exe' --version
```

ถ้า Git อยู่ตำแหน่งอื่น ให้ใช้ absolute path ที่พบจริง ห้ามใช้ System32/WindowsApps `bash.exe` แทน และไม่เพิ่ม `<Git>\usr\bin` ทั้งก้อนเข้า Windows PATH เพื่อแก้ทางลัด ([Git wrapper](https://gitforwindows.org/git-wrapper.html))

## 2. เสนอแผนติดตั้งจากผลตรวจ

แสดงตารางก่อนลงโปรแกรม: `tool | current version/path | target version | status | install source/scope | action`

ใช้สถานะ `ready`, `missing`, `different-version`, `blocked` หรือ `version-unavailable` แยกกัน เลือก Core ก่อน ส่วน optional ให้ผู้ใช้เลือกตามงาน

เสนอรายการที่จะลง แหล่งดาวน์โหลด เวอร์ชัน สิทธิ์ที่ต้องใช้ และการเปลี่ยน PATH แล้วรอยืนยันแผนตามกฎของผู้ใช้ เมื่อตกลงแล้วทำซ้ำเฉพาะงานในแผนจนตรวจผ่าน ไม่ถามซ้ำทุกคำสั่ง

ถ้าไม่มีสิทธิ์ Administrator, Microsoft Store/WinGet ถูกปิด หรือบริษัทบล็อก download ให้รายงาน blocker และช่องทาง IT อย่า bypass นโยบาย, UAC, certificate/proxy verification หรือขอรหัสผ่านจากผู้ใช้

## 3. ติดตั้งเฉพาะรายการที่ตกลง

### เมื่อใช้ WinGet ได้

ตรวจ package ID, เวอร์ชัน, architecture และ installer scope ก่อน ตัวอย่างการค้นรายการเวอร์ชัน:

```powershell
winget show --id Git.Git --exact --source winget --versions
winget show --id OpenJS.NodeJS.LTS --exact --source winget --versions
```

AI ต้องเลือก version ที่มีจริงแล้วใช้ `winget install` พร้อม `--id`, `--exact`, `--source winget` และ `--version` ที่ตรวจแล้วสำหรับแผน exact version ไม่ใช้ `winget upgrade --all` และไม่เปลี่ยนเป็น latest เมื่อไม่พบเวอร์ชันเป้าหมาย

| โปรแกรม | Package ID สำหรับตรวจ |
| --- | --- |
| Git for Windows | `Git.Git` |
| Node.js LTS | `OpenJS.NodeJS.LTS` |
| VS Code | `Microsoft.VisualStudioCode` |
| GitHub CLI (optional) | `GitHub.cli` |
| Claude Code (เมื่อเลือกช่องทาง WinGet) | `Anthropic.ClaudeCode` |

เลขเวอร์ชัน Git ใน catalog อาจไม่ใช่ string เดียวกับผล `git --version` เช่น suffix `.windows.1` ให้ตรวจ mapping กับ official release ก่อน ไม่เดาเลขที่ส่งให้ WinGet

`--scope user` ใช้ได้เมื่อ installer รองรับ และไม่ได้รับประกันว่าจะไม่มี UAC ตรวจ scope ของ package เวอร์ชันนั้นก่อน ([WinGet install](https://learn.microsoft.com/en-us/windows/package-manager/winget/install), [scope caveat](https://learn.microsoft.com/en-us/windows/package-manager/winget/troubleshooting#scope-for-specific-user-vs-machine-wide))

ตรวจ exit code และข้อความผลของ installer ทุกครั้ง ถ้าล้มเหลวให้รายงานก่อนทำขั้นตอนที่พึ่งโปรแกรมนั้น หากต้อง restart ให้แจ้งผู้ใช้ ไม่ restart เครื่องอัตโนมัติ

### เมื่อไม่มี WinGet หรือไม่มีเวอร์ชันเป้าหมาย

ใช้ official installer หรือช่องทาง software deployment ของบริษัท: [Git for Windows](https://git-scm.com/install/windows), [Node.js 24.14.0 archive](https://nodejs.org/download/release/v24.14.0/), [VS Code Windows setup](https://code.visualstudio.com/docs/setup/windows)

เลือก installer ตาม architecture ของเครื่องปลายทาง ตรวจแหล่งที่มาและ checksum/signature ที่ผู้ผลิตให้ก่อนรัน ถ้า installer ต้องใช้สิทธิ์ที่ไม่มี ให้รายงาน ไม่สลับไป portable distribution โดยไม่ตกลงกับผู้ใช้

WinGet เป็นตัวช่วย ไม่ใช่ prerequisite ของ kit ถ้าต้องการเพิ่ม ให้ใช้ [App Installer/WinGet ตามเอกสาร Microsoft](https://learn.microsoft.com/en-us/windows/package-manager/winget/) หรือช่องทาง IT

### ลำดับและรายละเอียด

1. **Git for Windows:** ติดตั้งแบบที่มี Git Bash แล้วตรวจ Git และ Bash แยกกัน ไม่ต้องเปิด WSL เพื่อให้เหมือนเครื่องต้นทาง
2. **Node.js + npm:** ใช้วิธีที่เข้ากับ version manager เดิม ถ้าไม่มีจึงใช้ installer ตรวจ npm หลังลง Node; ถ้าไม่ใช่ `11.9.0` ให้รายงานและเสนอปรับเฉพาะ npm ตามแผน ไม่ assume ว่า Node เวอร์ชันเดียวกันทำให้ npm ตรงเสมอ
3. **VS Code:** ถ้ามีอยู่แล้วให้คง installation/profile เดิม การเปลี่ยนเวอร์ชันต้องอยู่ในแผนที่ตกลง ไม่เขียนทับ settings หรือเปิด Settings Sync ส่วนตัวโดยอัตโนมัติ
4. **pnpm/Yarn:** ติดตั้ง/เปิดใช้เมื่อ repo ต้องใช้ เลือกเวอร์ชันจาก `packageManager`/lockfile และใช้เครื่องมือจัดการเวอร์ชันที่เหมาะสม อย่าลงทั้งสองแบบ global โดยเดาเวอร์ชันจาก shim
5. **Claude Code/Codex:** ตรวจ CLI, desktop app และ VS Code extension แยกกัน หากขาดเฉพาะ CLI ให้ใช้ขั้นตอน Windows จาก [Claude Code setup](https://code.claude.com/docs/en/setup) / [Codex CLI](https://learn.chatgpt.com/docs/codex/cli) ที่ตรวจในวันติดตั้ง การติดตั้งแอปใหม่/คนละช่องทางต้องระบุในแผนก่อน ล็อกอินผ่าน UI ของแอปโดยผู้ใช้ ไม่คัดลอก session/token จากเครื่องส่วนตัว
6. **เครื่องมือ optional:** Python/uv/Docker/GitHub CLI ลงเฉพาะที่เลือก ตรวจ prerequisites ของโปรแกรมนั้นก่อน โดยเฉพาะ Docker CLI ที่มีเวอร์ชันไม่ได้ยืนยันว่า daemon พร้อมใช้งาน

ถ้า Claude Code หา Git Bash ไม่เจอทั้งที่ executable ใช้งานได้ ให้เสนอการตั้ง `CLAUDE_CODE_GIT_BASH_PATH` ตาม absolute path จริงเป็นการแก้ config แยกที่ต้องได้รับอนุญาตก่อน ([เอกสาร Windows](https://code.claude.com/docs/en/setup))

## 4. VS Code extensions

รายการต่อไปนี้สำรวจจาก profile ปัจจุบันบนเครื่องต้นทาง ไม่ใช่คำสั่งให้ลงทั้งหมด:

| Extension ID | เวอร์ชันต้นทาง | ใช้เมื่อ |
| --- | --- | --- |
| `dbaeumer.vscode-eslint` | `3.0.34` | repo ใช้ ESLint |
| `esbenp.prettier-vscode` | `12.4.0` | repo ใช้ Prettier |
| `bradlc.vscode-tailwindcss` | `0.16.0` | repo ใช้ Tailwind |
| `anthropic.claude-code` | `2.1.296` | ใช้ Claude Code ใน VS Code |
| `openai.chatgpt` | `26.51007.21434` | ใช้ extension ของ OpenAI ที่เครื่องต้นทางมี |

ตรวจ extension ID และเวอร์ชันที่ Marketplace/บริษัทอนุญาตก่อน สามารถใช้ `code --install-extension publisher.extension@version` เมื่อตกลง exact version แล้ว โดยแทนค่าด้วยรายการจริง ไม่ใช้ `--force` เหมารวมและไม่ปิด auto-update เพื่อ pin ทั้งเครื่อง ([VS Code CLI](https://code.visualstudio.com/docs/configure/command-line))

ไม่ย้ายธีม, extensions ส่วนตัวอื่น, keybindings หรือ settings ทั้ง profile โดยอัตโนมัติ หากเลือกเวอร์ชัน extension ไม่ตรง baseline ให้รายงานความต่าง

## 5. ตรวจผลใน environment ใหม่

หลัง installer เปลี่ยน PATH ให้เปิด terminal ใหม่จาก Windows และตรวจซ้ำ ถ้าใช้ integrated terminal ของ VS Code ให้ผู้ใช้บันทึกงานแล้วเปิด VS Code ใหม่ก่อนตรวจ อย่าปิดแอปเองจนงานค้างหายหรือถือว่า terminal ใหม่ใน VS Code เดิมได้ PATH ใหม่แน่นอน ([VS Code Windows setup](https://code.visualstudio.com/docs/setup/windows))

ตรวจ executable path และ version อีกครั้งจาก terminal ปกติที่ไม่พึ่ง runtime ของ Codex และตรวจซ้ำใน VS Code เมื่อเปิดใหม่:

- `node --version` และ `npm.cmd --version` ตรงเป้าหมายที่ตกลง
- `node -e "console.log(process.version, process.arch)"` ทำงานได้
- `git --version` และ absolute path ของ Git Bash รัน `--version` ได้
- `code --version` และรายการ extensions ใน profile เป้าหมายตรงแผน
- CLI ของ AI tools ที่เลือกติดตั้งรัน `--version` ได้ ส่วนการล็อกอินให้ผู้ใช้ทดสอบเอง

ไม่สร้างโปรเจกต์ใหม่ ไม่รัน `npm install` ใน repo งาน ไม่เพิ่ม global packages หรือทดสอบ Docker โดยเปิด service นอกแผน เพียงเพื่อพิสูจน์ว่าติดตั้ง environment แล้ว

หาก AI ตรวจ terminal/session ใหม่ไม่ได้ ให้รายงาน `awaiting-new-session-verification` และคำสั่งที่เหลือ อย่ารายงานว่าตรง baseline ครบแล้ว

## 6. เกณฑ์เสร็จและงานต่อ

สรุปตาราง before/after พร้อมเวอร์ชันและ absolute path ที่ตรวจจริง รายการที่ลง/ข้าม ความต่างจาก baseline, blockers และสิ่งที่ผู้ใช้ยังต้องตรวจ ไม่มีการรายงาน token หรือ environment ทั้งชุด

ถือว่า environment พร้อมเมื่อ Core ที่เลือกใช้งานได้จาก terminal ปกติและ VS Code, เวอร์ชันตรงเป้าหมายที่ตกลง และไม่มี unresolved blocker ของ Core ส่วน optional ที่ไม่ได้เลือกให้แสดงว่า skipped

จากนั้นกลับไปทำขั้นตอนติดตั้ง global instructions และ skills ใน [README.md](README.md) การสำเร็จขั้น environment ไม่ได้แปลว่าติดตั้งคำสั่ง AI หรือยืนยันการโหลดใน session ใหม่แล้ว
