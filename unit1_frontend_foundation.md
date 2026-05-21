# หน่วยที่ 1: พื้นฐานและเส้นทางของนักพัฒนา Front-End

> **หลักสูตร:** หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
> **ระดับ:** ผู้เริ่มต้น (Beginner)
> **ระยะเวลา:** 1 สัปดาห์
> **ปรับปรุงล่าสุด:** พฤษภาคม 2026

---

## วัตถุประสงค์การเรียนรู้

เมื่อเรียนจบหน่วยนี้ ผู้เรียนจะสามารถ:

1. อธิบายความหมายและบทบาทของ Front-End Developer ในทีมพัฒนาซอฟต์แวร์ได้
2. วางแผนเส้นทางการเรียนรู้ของตนเองได้อย่างมีทิศทาง
3. ติดตั้งและตั้งค่าสภาพแวดล้อมการทำงาน (Development Environment) ได้ครบถ้วน
4. ใช้คำสั่ง Git เบื้องต้นและทำงานกับ GitHub ได้
5. รู้จักเครื่องมือและ Ecosystem พื้นฐานของการพัฒนา Front-End

---

## 1.1 Front-End Development คืออะไร และทำไมถึงสำคัญ

### ซอฟต์แวร์บนเว็บทำงานอย่างไร

เมื่อคุณเปิดเว็บไซต์อย่าง Lazada หรือ Netflix ในเบราว์เซอร์ สิ่งที่คุณมองเห็นและสัมผัสได้ทั้งหมด — ปุ่ม, รูปภาพ, เมนู, สีสัน, แอนิเมชัน — ล้วนเป็นผลงานของ **Front-End Developer** ทั้งสิ้น

แต่เบื้องหลังฉากนั้น ยังมีระบบอีกส่วนที่ทำงานอยู่ เช่น การประมวลผล การเก็บข้อมูล การยืนยันตัวตน ซึ่งเป็นงานของฝั่งที่เรียกว่า **Back-End**

---

### ความแตกต่างระหว่าง Front-End / Back-End / Full-Stack

```
┌─────────────────────────────────────────────────────────────┐
│                        เบราว์เซอร์ของผู้ใช้                 │
│                                                             │
│   ┌───────────────────────────────────────────────────┐    │
│   │               FRONT-END                           │    │
│   │   HTML  •  CSS  •  JavaScript  •  React           │    │
│   │   สิ่งที่ผู้ใช้มองเห็นและโต้ตอบได้               │    │
│   └────────────────────┬──────────────────────────────┘    │
└────────────────────────│────────────────────────────────────┘
                         │  HTTP Request/Response
                         │  (การรับ-ส่งข้อมูลผ่านอินเทอร์เน็ต)
┌────────────────────────▼────────────────────────────────────┐
│                        เซิร์ฟเวอร์                          │
│                                                             │
│   ┌───────────────────────────────────────────────────┐    │
│   │               BACK-END                            │    │
│   │   Node.js / Python / Java / Go / PHP              │    │
│   │   ประมวลผล, ตรวจสอบสิทธิ์, Business Logic        │    │
│   └────────────────────┬──────────────────────────────┘    │
│                        │                                    │
│   ┌────────────────────▼──────────────────────────────┐    │
│   │               DATABASE                            │    │
│   │   PostgreSQL / MySQL / MongoDB                    │    │
│   │   เก็บข้อมูลทั้งหมด                               │    │
│   └───────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

| บทบาท | ความหมาย | ตัวอย่างเทคโนโลยี |
|--------|----------|-----------------|
| **Front-End Developer** | สร้างส่วนที่ผู้ใช้มองเห็นและโต้ตอบในเบราว์เซอร์ | HTML, CSS, JavaScript, React, Next.js |
| **Back-End Developer** | สร้างระบบเบื้องหลัง: API, ฐานข้อมูล, ความปลอดภัย | Node.js, Python, PostgreSQL, Redis |
| **Full-Stack Developer** | ทำได้ทั้ง Front-End และ Back-End | รวมทั้งสองฝั่ง |

> **💡 คำแนะนำ:** สำหรับผู้เริ่มต้น ให้โฟกัสที่ Front-End ก่อน เมื่อเชี่ยวชาญแล้วค่อยขยายไปทาง Full-Stack จะง่ายกว่าและมีทิศทางชัดเจนกว่าการพยายามเรียนทุกอย่างพร้อมกัน

---

### บทบาทของ Front-End Developer ในทีม

ในทีมพัฒนาซอฟต์แวร์จริง Front-End Developer ทำงานร่วมกับบุคคลหลายฝ่าย:

```
         Product Manager
         (กำหนดว่าต้องสร้างอะไร)
                │
                ▼
         UX/UI Designer
         (ออกแบบหน้าตาและประสบการณ์ใช้งาน)
                │
                ▼
    ┌─── Front-End Developer ───┐
    │   (คุณ — แปล Design        │
    │    ให้เป็นโค้ดที่ทำงานได้) │
    └───────────┬───────────────┘
                │  เรียกใช้ API
                ▼
         Back-End Developer
         (สร้าง API และระบบหลังบ้าน)
                │
                ▼
         DevOps / SRE
         (ดูแลเซิร์ฟเวอร์และการ Deploy)
```

**สิ่งที่ Front-End Developer ทำจริงในแต่ละวัน:**

- แปลง Design จาก Figma (ฟิกม่า — โปรแกรมออกแบบ UI) ให้เป็นโค้ด
- พัฒนา Component (คอมโพเนนต์ — ชิ้นส่วน UI ที่ใช้ซ้ำได้) เช่น ปุ่ม, ฟอร์ม, เมนู
- เชื่อมต่อกับ API (เอพีไอ — ช่องทางรับ-ส่งข้อมูลกับเซิร์ฟเวอร์)
- ดูแลให้เว็บทำงานได้บนอุปกรณ์และเบราว์เซอร์ต่าง ๆ
- ปรับปรุงประสิทธิภาพให้เว็บโหลดเร็ว
- แก้ Bug (บัก — ข้อผิดพลาดในโปรแกรม) และ Review Code ของเพื่อนร่วมทีม

---

### ตัวอย่างผลงานจริงของ Front-End Developer

**1. เว็บแอปพลิเคชัน (Web Application)**
เช่น Google Docs, Notion, Figma — ผู้ใช้ทำงานได้โดยตรงในเบราว์เซอร์ ไม่ต้องติดตั้งโปรแกรม

**2. Progressive Web App (PWA — พีดับเบิลยูเอ)**
เว็บที่มีความสามารถใกล้เคียง Native App เช่น ทำงานได้แบบ Offline, แจ้งเตือนได้, ติดตั้งลงหน้าจอมือถือได้
ตัวอย่าง: Twitter (X), Starbucks

**3. Dashboard (แดชบอร์ด)**
หน้าสรุปข้อมูลสำหรับผู้บริหารหรือทีมงาน แสดงกราฟ, ตาราง, สถิติแบบ Real-time

**4. E-commerce (อีคอมเมิร์ซ)**
เช่น Shopee, Lazada — ระบบซื้อขายออนไลน์ที่ซับซ้อน มีทั้งตะกร้า, ชำระเงิน, ติดตามพัสดุ

---

## 1.2 Roadmap นักพัฒนา Front-End ปี 2026

### เส้นทางเรียนรู้ที่ชัดเจน

การเรียน Front-End มีลำดับที่ชัดเจน ห้ามข้ามขั้น เพราะแต่ละทักษะเป็นรากฐานของสิ่งถัดไป:

```
ขั้นที่ 1: HTML
(โครงสร้างของหน้าเว็บ — เหมือนโครงกระดูก)
    │
    ▼
ขั้นที่ 2: CSS
(ความสวยงาม สีสัน Layout — เหมือนเสื้อผ้า)
    │
    ▼
ขั้นที่ 3: JavaScript
(พฤติกรรม ความโต้ตอบ — เหมือนกล้ามเนื้อ)
    │
    ▼
ขั้นที่ 4: TypeScript
(JavaScript ที่มีความปลอดภัยด้านข้อมูลมากขึ้น)
    │
    ▼
ขั้นที่ 5: React
(Library สำหรับสร้าง UI แบบ Component)
    │
    ▼
ขั้นที่ 6: Next.js
(Framework ครบครันสำหรับ React App จริง)
    │
    ▼
ขั้นที่ 7: ทักษะรอบด้าน
(Testing, Git, CI/CD, Security, Performance)
```

> **📌 อ้างอิง:** เส้นทางนี้สอดคล้องกับ [roadmap.sh/frontend](https://roadmap.sh/frontend) ซึ่งเป็นแหล่งอ้างอิงมาตรฐานที่ Developer ทั่วโลกใช้

---

### ทักษะที่ตลาดงานต้องการในปี 2026

จากการวิเคราะห์ประกาศรับสมัครงาน Front-End Developer ในไทยและต่างประเทศ:

| ทักษะ | ความสำคัญ | หมายเหตุ |
|-------|----------|---------|
| HTML / CSS | ⭐⭐⭐⭐⭐ | พื้นฐานที่ขาดไม่ได้ |
| JavaScript (ES2022+) | ⭐⭐⭐⭐⭐ | หัวใจของ Front-End |
| TypeScript | ⭐⭐⭐⭐⭐ | บริษัทส่วนใหญ่บังคับใช้แล้ว |
| React | ⭐⭐⭐⭐⭐ | Framework ที่ได้รับความนิยมสูงสุด |
| Next.js | ⭐⭐⭐⭐ | มาตรฐานใหม่สำหรับ React App |
| Git / GitHub | ⭐⭐⭐⭐⭐ | ทำงานเป็นทีมไม่ได้โดยไม่มีสิ่งนี้ |
| Tailwind CSS | ⭐⭐⭐⭐ | CSS Framework ที่ได้รับความนิยมสูงสุดในปัจจุบัน |
| Responsive Design | ⭐⭐⭐⭐⭐ | เว็บต้องรองรับมือถือเสมอ |
| API / REST | ⭐⭐⭐⭐ | เชื่อมต่อกับ Back-End |
| Testing (Vitest/Playwright) | ⭐⭐⭐ | บริษัทใหญ่บังคับ |

---

### AI-Assisted Development: เครื่องมือ AI ในการพัฒนา

ในปี 2026 การใช้ AI ช่วยเขียนโค้ดกลายเป็นทักษะมาตรฐาน ไม่ใช่ทางลัด Developer ที่ใช้ AI ได้ดีจะทำงานเร็วขึ้นและแก้ปัญหาได้ดีขึ้น

**เครื่องมือที่นิยมใช้:**

| เครื่องมือ | ลักษณะการใช้งาน |
|-----------|--------------|
| **GitHub Copilot** (กิตฮับ โคไพลอต) | Plugin ใน VS Code ช่วยเติมโค้ดอัตโนมัติ, อธิบายโค้ด, แนะนำการแก้ Bug |
| **Cursor** (เคอร์เซอร์) | Editor ที่ฝัง AI ไว้ในตัว สั่งงานด้วยภาษาธรรมชาติได้ |
| **Claude** (คลอด) | ช่วยคิด Architecture, อธิบายแนวคิด, Review Code |

**วิธีใช้ AI ให้ถูกต้อง:**

```
✅ ถูกต้อง:
- ใช้ AI เป็น "ผู้ช่วย" ที่ให้ตัวเลือก แต่ตัดสินใจเองเสมอ
- อ่านโค้ดที่ AI สร้างให้ทุกบรรทัด ก่อน Copy ไปใช้
- ใช้ AI ช่วยอธิบายแนวคิดที่ไม่เข้าใจ
- ใช้ AI ช่วย Debug เมื่อตันนานเกิน 30 นาที

❌ ผิดพลาด:
- Copy โค้ดจาก AI โดยไม่อ่านและไม่เข้าใจ
- พึ่ง AI 100% โดยไม่พยายามเองก่อน
- เชื่อ AI ทุกอย่างโดยไม่ตรวจสอบ
```

> **💡 หลักการสำคัญ:** AI ช่วยให้คุณทำงานเร็วขึ้น แต่ไม่สามารถแทนที่ความเข้าใจพื้นฐาน ผู้ที่พื้นฐานแน่นจะใช้ AI ได้ดีกว่าผู้ที่ไม่มีพื้นฐานเสมอ

---

## 1.3 ตั้งค่าสภาพแวดล้อมการทำงาน (Development Environment)

**Development Environment** (ดีเวลอปเมนต์ เอนไวรอนเมนต์) หมายถึง ชุดเครื่องมือและการตั้งค่าที่ Developer ใช้ในการพัฒนาโปรแกรม เปรียบเหมือนโต๊ะทำงานและอุปกรณ์ของช่างฝีมือ

---

### Node.js และ pnpm

**Node.js** (โนด เจเอส) คือ Runtime Environment (รันไทม์ เอนไวรอนเมนต์) ที่ทำให้รัน JavaScript นอกเบราว์เซอร์ได้ — บนเครื่องคอมพิวเตอร์โดยตรง

เปรียบง่าย ๆ: ปกติ JavaScript ทำงานได้เฉพาะในเบราว์เซอร์ Node.js ทำให้ JavaScript ทำงานบนเครื่องของเราได้ ทำให้สามารถรันเครื่องมือต่าง ๆ เช่น Compiler, Build Tool ได้

**LTS** (เอลทีเอส) ย่อมาจาก **Long-Term Support** หมายถึงเวอร์ชันที่ได้รับการดูแลและอัปเดตความปลอดภัยระยะยาว เหมาะสำหรับใช้งานจริง

**pnpm** (พีเอ็นพีเอ็ม) คือ Package Manager (แพ็กเกจ แมเนเจอร์) — โปรแกรมจัดการไลบรารีและเครื่องมือที่โปรเจกต์ต้องการ pnpm เร็วกว่าและประหยัดพื้นที่ดิสก์กว่า npm (เอ็นพีเอ็ม) ซึ่งเป็น Package Manager เดิม

**วิธีติดตั้ง Node.js (แนะนำผ่าน nvm):**

`nvm` (เอ็นวีเอ็ม — Node Version Manager) คือตัวจัดการเวอร์ชัน Node.js ช่วยให้สลับเวอร์ชันได้สะดวก

```bash
# macOS / Linux — ติดตั้ง nvm ก่อน
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# หลังติดตั้ง nvm แล้ว ติดตั้ง Node.js LTS
nvm install --lts
nvm use --lts

# ตรวจสอบว่าติดตั้งสำเร็จ
node --version   # ควรแสดง เช่น v22.14.0
npm --version    # ควรแสดง เช่น 10.9.2
```

```powershell
# Windows — ดาวน์โหลด nvm-windows จาก:
# https://github.com/coreybutler/nvm-windows/releases
# จากนั้นเปิด PowerShell แล้วรัน:
nvm install lts
nvm use lts

node --version
npm --version
```

**ติดตั้ง pnpm:**

```bash
# ติดตั้ง pnpm ผ่าน npm (ทำครั้งเดียว)
npm install -g pnpm

# ตรวจสอบ
pnpm --version   # ควรแสดง เช่น 9.15.0
```

**คำสั่ง pnpm ที่ใช้บ่อย:**

```bash
pnpm install          # ติดตั้ง dependencies ทั้งหมดในโปรเจกต์
pnpm add react        # เพิ่ม package ชื่อ react
pnpm add -D vitest    # เพิ่ม package สำหรับ Development เท่านั้น (-D = devDependency)
pnpm remove lodash    # ลบ package
pnpm run dev          # รัน script ชื่อ "dev" ที่กำหนดใน package.json
pnpm run build        # รัน script ชื่อ "build"
```

---

### VS Code และ Extension ที่จำเป็น

**VS Code** (วีเอส โค้ด — Visual Studio Code) คือ Code Editor (โค้ด เอดิเตอร์) ที่ได้รับความนิยมสูงสุดในโลก พัฒนาโดย Microsoft ใช้ฟรี

**ดาวน์โหลด VS Code:** [code.visualstudio.com](https://code.visualstudio.com)

**Extension** (เอกซ์เทนชัน) คือปลั๊กอินที่เพิ่มความสามารถให้ VS Code ติดตั้งได้จากแถบ Extensions ด้านซ้าย (ไอคอนสี่เหลี่ยม 4 ชิ้น)

**Extension ที่ต้องติดตั้ง:**

| Extension | ประโยชน์ |
|-----------|---------|
| **ESLint** (อีเอสลินต์) | ตรวจสอบโค้ดว่าเขียนถูกต้องตามมาตรฐาน แจ้งเตือน Error และ Warning แบบ Real-time |
| **Prettier** (พริตตีเออร์) | จัดรูปแบบโค้ดให้สวยงามอัตโนมัติเมื่อกด Save |
| **GitLens** (กิตเลนส์) | แสดงประวัติ Git ใน Editor เห็นได้ทันทีว่าใครเขียนโค้ดบรรทัดไหน เมื่อไหร่ |
| **Tailwind CSS IntelliSense** | แนะนำ Class ของ Tailwind อัตโนมัติขณะพิมพ์ |
| **GitHub Copilot** | AI ช่วยเขียนโค้ด (ต้องสมัครใช้งานที่ github.com/features/copilot) |

**การตั้งค่า VS Code ที่แนะนำ** (กด `Ctrl+Shift+P` พิมพ์ "Open User Settings JSON"):

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.tabSize": 2,
  "editor.wordWrap": "on",
  "editor.fontSize": 14,
  "editor.fontFamily": "JetBrains Mono, Fira Code, Consolas, monospace",
  "editor.fontLigatures": true,
  "files.autoSave": "onFocusChange",
  "terminal.integrated.defaultProfile.windows": "Git Bash"
}
```

> **💡 Tip:** ฟอนต์ **JetBrains Mono** หรือ **Fira Code** รองรับ Ligatures (ลิเกเจอร์) ซึ่งทำให้สัญลักษณ์ เช่น `=>`, `!==`, `>=` แสดงผลสวยงามกว่า ดาวน์โหลดได้ฟรีที่ [jetbrains.com/lp/mono](https://www.jetbrains.com/lp/mono/)

---

### Terminal และ Command Line เบื้องต้น

**Terminal** (เทอร์มินัล) หรือ **Command Line** (คอมมานด์ไลน์) คือโปรแกรมที่ให้เราสั่งงานคอมพิวเตอร์ด้วยการพิมพ์ข้อความ แทนการคลิกเมาส์

ใน VS Code เปิด Terminal ได้ด้วย `` Ctrl+` `` (ปุ่ม Backtick ด้านบนซ้ายของแป้นพิมพ์)

**คำสั่งพื้นฐานที่ต้องรู้:**

```bash
# แสดงไดเรกทอรี (โฟลเดอร์) ปัจจุบัน
pwd
# ตัวอย่างผลลัพธ์: /Users/somchai/Documents

# แสดงรายการไฟล์และโฟลเดอร์
ls          # macOS/Linux
dir         # Windows

# แสดงแบบละเอียด (รวมไฟล์ที่ซ่อนอยู่)
ls -la      # macOS/Linux

# เปลี่ยนไดเรกทอรี (cd = change directory)
cd Documents              # เข้าโฟลเดอร์ Documents
cd ..                     # ย้อนกลับขึ้นหนึ่งระดับ
cd ~/Documents/projects   # ไปยัง path เต็ม (~ = โฟลเดอร์ Home)

# สร้างโฟลเดอร์ใหม่ (mkdir = make directory)
mkdir my-project

# สร้างไฟล์ใหม่
touch index.html          # macOS/Linux
echo. > index.html        # Windows

# ล้างหน้าจอ Terminal
clear       # macOS/Linux
cls         # Windows
```

**ตัวอย่างการใช้งานจริง — สร้างโปรเจกต์ใหม่:**

```bash
# 1. ไปยังโฟลเดอร์ที่ต้องการเก็บโปรเจกต์
cd ~/Documents
mkdir projects
cd projects

# 2. สร้างโปรเจกต์ Next.js ใหม่ด้วย pnpm
pnpm create next-app@latest my-first-app

# 3. เข้าโฟลเดอร์โปรเจกต์
cd my-first-app

# 4. เปิดใน VS Code
code .
# จุด (.) หมายถึง "โฟลเดอร์ปัจจุบัน"

# 5. รัน Development Server
pnpm dev
# จากนั้นเปิดเบราว์เซอร์ไปที่ http://localhost:3000
```

> **💡 Tip:** `localhost` (โลคัลโฮสต์) คือชื่อเรียกของเครื่องคอมพิวเตอร์ตัวเอง `:3000` คือหมายเลข Port (พอร์ต) เปรียบเหมือนประตูบ้านแต่ละบาน Development Server มักใช้ Port 3000 หรือ 5173

---

## 1.4 Version Control ด้วย Git และ GitHub

### Git คืออะไร และทำไมทุกทีมถึงใช้

**Git** (กิต) คือระบบ Version Control (เวอร์ชัน คอนโทรล) — เครื่องมือติดตามการเปลี่ยนแปลงของโค้ดตลอดเวลา

**ทำไมถึงจำเป็น? ลองนึกภาพสถานการณ์นี้:**

```
❌ ไม่มี Git:
my-project/
├── index.html
├── index_backup.html
├── index_final.html
├── index_final_v2.html
├── index_FINAL_USE_THIS.html
└── index_FINAL_USE_THIS_fixed.html

ปัญหา: ไม่รู้ว่าไฟล์ไหนล่าสุด, ถ้าทีมทำงานพร้อมกันจะชนกัน,
ถ้าพังแล้วอยากย้อนกลับไม่รู้จะย้อนยังไง

✅ มี Git:
my-project/
└── index.html  ← ไฟล์เดียว แต่ Git จำประวัติทั้งหมดไว้ให้

Git จำทุกการเปลี่ยนแปลง พร้อมบอกว่า "ใครเปลี่ยน, เมื่อไหร่, ทำไม"
ย้อนกลับไปเวอร์ชันไหนก็ได้ ทำงานเป็นทีมโดยไม่ขัดกัน
```

**GitHub** (กิตฮับ) คือเว็บไซต์สำหรับเก็บโค้ดบน Cloud และทำงานร่วมกันเป็นทีม เปรียบได้กับ Google Drive แต่ออกแบบมาสำหรับโค้ดโดยเฉพาะ

---

### ติดตั้ง Git

```bash
# macOS — ติดตั้งผ่าน Homebrew
brew install git

# หรือดาวน์โหลดจาก https://git-scm.com/download/mac

# Windows — ดาวน์โหลด Git for Windows จาก
# https://git-scm.com/download/win
# (ติดตั้งแล้วจะมี Git Bash ให้ใช้ด้วย)

# ตรวจสอบ
git --version   # ควรแสดง เช่น git version 2.47.0
```

**ตั้งค่าข้อมูลส่วนตัว (ทำครั้งแรกครั้งเดียว):**

```bash
git config --global user.name "ชื่อจริงของคุณ"
git config --global user.email "email@example.com"

# ตั้ง Editor เริ่มต้นเป็น VS Code
git config --global core.editor "code --wait"

# ดูการตั้งค่าทั้งหมด
git config --list
```

---

### คำสั่ง Git พื้นฐาน

**แนวคิดหลักของ Git:**

```
Working Directory        Staging Area           Repository (Local)
(โฟลเดอร์โปรเจกต์)      (พื้นที่เตรียม Commit)   (ประวัติโค้ดทั้งหมด)
      │                       │                        │
      │  git add              │  git commit            │
      │──────────────────────►│───────────────────────►│
      │                       │                        │
      │                       │                        │  git push
      │                       │                        │──────────► GitHub
```

**คำสั่งที่ใช้บ่อย:**

```bash
# ──────────────────────────────────────────
# เริ่มต้นโปรเจกต์ใหม่
# ──────────────────────────────────────────

# สร้าง Git Repository ใหม่ในโฟลเดอร์ปัจจุบัน
git init

# ──────────────────────────────────────────
# ดูสถานะและประวัติ
# ──────────────────────────────────────────

# ดูสถานะไฟล์ปัจจุบัน (มีไฟล์ไหนเปลี่ยนแปลงบ้าง)
git status

# ดูประวัติ Commit ทั้งหมด
git log

# ดูประวัติแบบย่อ (สวยงาม)
git log --oneline --graph

# ──────────────────────────────────────────
# บันทึกการเปลี่ยนแปลง
# ──────────────────────────────────────────

# เพิ่มไฟล์เข้า Staging Area (เตรียม Commit)
git add index.html          # เพิ่มไฟล์เดียว
git add src/                # เพิ่มทั้งโฟลเดอร์
git add .                   # เพิ่มทุกไฟล์ที่เปลี่ยนแปลง

# บันทึก Commit พร้อมข้อความอธิบาย
git commit -m "feat: add navigation menu"

# ──────────────────────────────────────────
# ทำงานกับ GitHub
# ──────────────────────────────────────────

# เชื่อมต่อกับ GitHub Repository
git remote add origin https://github.com/username/my-project.git

# ส่งโค้ดขึ้น GitHub (push ครั้งแรก)
git push -u origin main

# ส่งโค้ดขึ้น GitHub (ครั้งต่อไป)
git push

# ดึงโค้ดล่าสุดจาก GitHub
git pull
```

**การเขียน Commit Message ที่ดี:**

Commit Message คือข้อความอธิบายว่าเราเปลี่ยนแปลงอะไรใน Commit นั้น ควรสื่อความหมายได้ชัดเจน

```bash
# ✅ ดี — บอกได้ว่าทำอะไร
git commit -m "feat: add user login form"
git commit -m "fix: correct typo in homepage title"
git commit -m "style: update button color to match design"
git commit -m "docs: add README setup instructions"

# ❌ ไม่ดี — ไม่บอกว่าทำอะไร
git commit -m "update"
git commit -m "fix bug"
git commit -m "แก้แล้ว"
git commit -m "aaa"
```

> **📌 รูปแบบ Conventional Commits:** `<type>: <description>` โดย type ที่ใช้บ่อย ได้แก่ `feat` (feature ฟีเจอร์ใหม่), `fix` (แก้ Bug), `docs` (documentation เอกสาร), `style` (จัดรูปแบบ), `chore` (งานทั่วไป), `refactor` (ปรับโครงสร้างโค้ดโดยไม่เปลี่ยนการทำงาน), `test` (เพิ่มหรือแก้ไขระบบทดสอบ), `perf` หรือ performance (ปรับปรุงประสิทธิภาพ), `build` (งานเกี่ยวกับ build system หรือ dependency), `ci` หรือ continuous integration (งานเกี่ยวกับ CI/CD), และ `revert` (ย้อนกลับ commit ก่อนหน้า)

---

### สร้าง Repository บน GitHub และ Push ครั้งแรก

**ขั้นตอน:**

1. ไปที่ [github.com](https://github.com) → สมัครบัญชีฟรี
2. คลิก **New** เพื่อสร้าง Repository ใหม่
3. ตั้งชื่อ Repository เช่น `my-first-project`
4. เลือก **Public** (สาธารณะ) หรือ **Private** (ส่วนตัว)
5. คลิก **Create repository**

จากนั้น GitHub จะแสดงคำสั่งให้ทำตาม:

```bash
# ในโฟลเดอร์โปรเจกต์ของคุณ
git init
git add .
git commit -m "feat: initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/my-first-project.git
git push -u origin main
```

หลังจากนี้ไปดูที่ GitHub จะเห็นโค้ดของคุณบนเว็บแล้ว 🎉

---

### GitHub Flow — วงจรการทำงานในทีม

**Branch** (บรานช์) คือเส้นทางพัฒนาที่แยกออกมาจากโค้ดหลัก ช่วยให้ทำงานได้โดยไม่กระทบโค้ดที่ใช้งานจริง

```
main branch (สาขาหลัก — โค้ดที่ใช้งานจริง)
─────────────────────────────────────────────►
        │                           │
        │ git checkout -b           │ Merge หลังผ่าน Review
        ▼                           │
feature/user-login ─────────────────┘
(สาขาสำหรับพัฒนาฟีเจอร์ Login)
```

```bash
# 1. สร้างและสลับไป Branch ใหม่
git checkout -b feature/user-login
# หรือ (คำสั่งใหม่)
git switch -c feature/user-login

# 2. พัฒนาโค้ดใน Branch นี้
# ... เขียนโค้ด ...
git add .
git commit -m "feat: add login form component"

# 3. Push Branch ขึ้น GitHub
git push origin feature/user-login

# 4. สร้าง Pull Request บน GitHub
# (ทำผ่านเว็บ GitHub — คลิก "Compare & pull request")
# เพื่อให้เพื่อนร่วมทีม Review โค้ด

# 5. หลังผ่านการ Review แล้ว Merge เข้า main
# (ทำผ่านเว็บ GitHub หรือคำสั่ง)
git checkout main
git merge feature/user-login
```

**Pull Request** (พูล รีเควสต์ หรือ PR) คือการขอ Merge โค้ดจาก Branch หนึ่งเข้าอีก Branch หนึ่ง พร้อมให้ทีมตรวจสอบโค้ดก่อน

---

## 1.5 รู้จักเครื่องมือและ Ecosystem ของ Front-End

### Browser DevTools — เครื่องมือที่ใช้ทุกวัน

**DevTools** (ดีฟทูลส์ — Developer Tools) คือชุดเครื่องมือที่ฝังอยู่ในเบราว์เซอร์ ช่วยให้ Developer ตรวจสอบ, ทดสอบ, และแก้ปัญหาได้โดยตรง

**เปิด DevTools:**
- **Chrome / Edge:** กด `F12` หรือ `Ctrl+Shift+I` (Windows) / `Cmd+Option+I` (Mac)
- **Firefox:** กด `F12` หรือ `Ctrl+Shift+I`

**แท็บที่ใช้บ่อย:**

**Elements (เอลิเมนต์)**
ดูและแก้ไข HTML และ CSS ของหน้าเว็บแบบ Real-time

```
ใช้สำหรับ:
- ตรวจสอบว่า HTML ถูกสร้างมาอย่างไร
- ทดลองแก้ CSS โดยไม่ต้องแก้ไฟล์จริง
- วัดขนาด/ระยะห่างของ Element
```

**Console (คอนโซล)**
แสดงข้อผิดพลาด (Error) และให้รัน JavaScript ได้โดยตรง

```javascript
// ใน Console พิมพ์ JavaScript ได้เลย
console.log("Hello, World!")         // แสดงข้อความ
console.log(document.title)          // แสดงชื่อหน้า
document.body.style.background = "red"  // เปลี่ยนสีพื้นหลังทันที
```

**Network (เน็ตเวิร์ก)**
ดูการรับ-ส่งข้อมูลทั้งหมดระหว่างเบราว์เซอร์กับเซิร์ฟเวอร์

```
ใช้สำหรับ:
- ดูว่า API ถูกเรียกไปหรือไม่
- ตรวจสอบข้อมูลที่ส่งและรับ
- วัดเวลาโหลดของแต่ละ Request
- ดู Status Code (200 = สำเร็จ, 404 = ไม่พบ, 500 = Server Error)
```

---

### Vite — Build Tool มาตรฐาน

**Vite** (วีท — ออกเสียงตามภาษาฝรั่งเศส แปลว่า "เร็ว") คือ Build Tool (บิลด์ ทูล) ที่ทำหน้าที่:

1. **Development Server:** รันเว็บในเครื่องระหว่างพัฒนา พร้อม Hot Reload (อัปเดตหน้าเว็บทันทีเมื่อแก้โค้ด โดยไม่ต้อง Refresh)
2. **Bundler:** รวบรวมไฟล์ JavaScript, CSS ทั้งหมดเพื่อ Deploy

**ทำไม Vite ถึงแทนที่ Webpack:**

| คุณสมบัติ | Webpack (เดิม) | Vite (ปัจจุบัน) |
|----------|--------------|----------------|
| เวลาเริ่ม Dev Server | 30-60 วินาที | < 1 วินาที |
| Hot Reload | ช้า (รีบิลด์ทั้งหมด) | เร็วมาก (เฉพาะส่วนที่เปลี่ยน) |
| การตั้งค่า | ซับซ้อน | เรียบง่าย |

**สร้างโปรเจกต์ด้วย Vite:**

```bash
# สร้างโปรเจกต์ React + TypeScript ด้วย Vite
pnpm create vite@latest my-app --template react-ts

# เข้าโฟลเดอร์และติดตั้ง dependencies
cd my-app
pnpm install

# รัน Development Server
pnpm dev
# เปิดเบราว์เซอร์ที่ http://localhost:5173
```

---

### รู้จัก package.json

**`package.json`** คือไฟล์หัวใจของโปรเจกต์ JavaScript ทุกโปรเจกต์ บอกข้อมูลเกี่ยวกับโปรเจกต์และ dependencies (ดีเพนเดนซี — ไลบรารีที่โปรเจกต์ต้องพึ่งพา)

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "โปรเจกต์ Front-End แรกของฉัน",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^19.0.0",
    "react-dom": "^19.0.0"
  },
  "devDependencies": {
    "@types/react": "^19.0.0",
    "typescript": "^5.7.0",
    "vite": "^6.0.0"
  }
}
```

**อธิบายแต่ละส่วน:**

- **`name`**: ชื่อโปรเจกต์
- **`version`**: เวอร์ชันโปรเจกต์ (ใช้รูปแบบ Major.Minor.Patch)
- **`scripts`**: คำสั่งลัดที่รันด้วย `pnpm run <ชื่อ>` เช่น `pnpm run dev`
- **`dependencies`**: ไลบรารีที่ต้องใช้ในโปรแกรมจริง (Production)
- **`devDependencies`**: ไลบรารีที่ใช้เฉพาะตอนพัฒนา เช่น TypeScript, Vite

**ตัวเลขเวอร์ชัน `^19.0.0` หมายความว่าอะไร:**

```
^ (Caret) หมายถึง ยอมรับเวอร์ชันที่ใหม่กว่าใน Minor และ Patch
^19.0.0 = ยอมรับ 19.0.0, 19.1.0, 19.2.5 แต่ไม่ยอมรับ 20.0.0

~ (Tilde) หมายถึง ยอมรับเฉพาะเวอร์ชันที่ใหม่กว่าใน Patch
~19.0.0 = ยอมรับ 19.0.0, 19.0.5 แต่ไม่ยอมรับ 19.1.0
```

---

### รู้จัก node_modules และ .gitignore

**`node_modules/`** คือโฟลเดอร์ที่เก็บไลบรารีทั้งหมดที่โปรเจกต์ต้องการ อาจมีขนาดหลายร้อย MB และมีไฟล์หลายแสนไฟล์

**ทำไมไม่ควร Push node_modules ขึ้น GitHub:**
- ขนาดใหญ่มาก อาจถึงหลาย GB
- สามารถสร้างใหม่ได้ทุกเมื่อด้วย `pnpm install`
- แต่ละคนในทีมสร้างเองได้จาก `package.json`

**`.gitignore`** คือไฟล์บอก Git ว่าไฟล์/โฟลเดอร์ไหนไม่ต้องติดตาม (ไม่ต้อง Commit)

```gitignore
# .gitignore — ไฟล์ที่ Git จะไม่ติดตาม

# Dependencies
node_modules/

# Build output
dist/
.next/
out/

# Environment variables (ข้อมูลลับ เช่น API Key)
.env
.env.local
.env.production

# OS files
.DS_Store        # macOS
Thumbs.db        # Windows

# Editor
.vscode/settings.json
```

> **⚠️ สำคัญมาก:** ไฟล์ `.env` ที่เก็บ API Key หรือรหัสผ่านต้องอยู่ใน `.gitignore` เสมอ ห้าม Push ขึ้น GitHub เด็ดขาด เพราะทุกคนในโลกสามารถดูได้หากเป็น Public Repository

---

### โครงสร้างโปรเจกต์ Next.js ที่ควรรู้จัก

```
my-next-app/
├── app/                    ← โฟลเดอร์หลัก (App Router)
│   ├── layout.tsx          ← Layout หลักที่ทุกหน้าใช้ร่วมกัน
│   ├── page.tsx            ← หน้าแรก (route: /)
│   └── about/
│       └── page.tsx        ← หน้า About (route: /about)
├── components/             ← Component ที่ใช้ซ้ำได้
│   ├── Navbar.tsx
│   └── Button.tsx
├── public/                 ← ไฟล์ static เช่น รูปภาพ, favicon
│   └── logo.png
├── .gitignore
├── next.config.ts          ← การตั้งค่า Next.js
├── package.json
├── tailwind.config.ts      ← การตั้งค่า Tailwind CSS
└── tsconfig.json           ← การตั้งค่า TypeScript
```

---

### Markdown — ภาษาสำหรับเขียนเอกสาร

**Markdown** (มาร์กดาวน์) คือภาษาเขียนเอกสารที่ใช้ข้อความธรรมดาแต่แสดงผลสวยงาม ใช้เขียน README, เอกสาร, บันทึก

ไฟล์ Markdown มีนามสกุล `.md` GitHub จะแสดงผล README.md โดยอัตโนมัติในหน้า Repository

**ตัวอย่างการเขียน Markdown:**

```markdown
# หัวข้อระดับ 1 (ใหญ่สุด)
## หัวข้อระดับ 2
### หัวข้อระดับ 3

**ข้อความหนา** และ *ข้อความเอียง*

- รายการแบบ bullet
- ข้อที่สอง
  - ข้อย่อย

1. รายการแบบตัวเลข
2. ข้อที่สอง

`inline code` — โค้ดในบรรทัด

```javascript
// Code block — โค้ดหลายบรรทัด
const greeting = "Hello, World!"
console.log(greeting)
```

```[ลิงก์](https://github.com) — ข้อความลิงก์ ```

```![รูปภาพ](./logo.png) — รูปภาพ```

```> คำพูดอ้างอิง (Blockquote)``` เช่น
> คำพูดอ้างอิง (Blockquote)

```

**ตัวอย่าง README.md ที่ดีสำหรับโปรเจกต์:**

```markdown
# ชื่อโปรเจกต์

คำอธิบายสั้น ๆ ว่าโปรเจกต์นี้ทำอะไร

## 🚀 เริ่มต้นใช้งาน

### ความต้องการ
- Node.js 22+
- pnpm 9+

### ติดตั้ง

```bash
git clone https://github.com/username/my-project.git
cd my-project
pnpm install
pnpm dev
```


## 🛠 สรุป เทคโนโลยีที่แนะนำ

- [Next.js 15](https://nextjs.org/)
- [TypeScript](https://www.typescriptlang.org/)
- [Tailwind CSS](https://tailwindcss.com/)


---

## สรุปหน่วยที่ 1

สิ่งที่เรียนรู้ในหน่วยนี้
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Front-End คือการสร้างส่วนที่ผู้ใช้มองเห็นต่างจาก Back-End ที่ทำงานเบื้องหลัง

✅ เส้นทางที่ชัดเจน: HTML → CSS → JavaScript → TypeScript → React → Next.js

✅ เครื่องมือที่ติดตั้งแล้ว: Node.js LTS, pnpm, VS Code + Extensions, Git

✅ Git เก็บประวัติโค้ด, GitHub เก็บโค้ดบน Cloud คำสั่งหลัก: add → commit → push

✅ Vite คือ Build Tool ที่เร็วและทันสมัย  package.json บอกข้อมูลและ dependencies ของโปรเจกต์

✅ node_modules และ .env ต้องอยู่ใน .gitignore เสมอ

---

## คำศัพท์ที่ควรรู้ (Glossary)

| คำศัพท์ | คำอ่าน | ความหมาย |
|--------|--------|---------|
| Front-End | ฟรอนต์ เอนด์ | ส่วนที่ผู้ใช้มองเห็นในเบราว์เซอร์ |
| Back-End | แบค เอนด์ | ระบบเซิร์ฟเวอร์เบื้องหลัง |
| Full-Stack | ฟูล สแต็ก | Developer ที่ทำทั้ง Front และ Back |
| Runtime Environment | รันไทม์ เอนไวรอนเมนต์ | สภาพแวดล้อมสำหรับรันโปรแกรม |
| Package Manager | แพ็กเกจ แมเนเจอร์ | โปรแกรมจัดการไลบรารี |
| Dependency | ดีเพนเดนซี | ไลบรารีที่โปรเจกต์ต้องพึ่งพา |
| Version Control | เวอร์ชัน คอนโทรล | ระบบติดตามการเปลี่ยนแปลงของโค้ด |
| Repository | รีพอสิทอรี (Repo) | ที่เก็บโค้ดและประวัติทั้งหมด |
| Commit | คอมมิต | การบันทึก Snapshot ของโค้ด ณ ช่วงเวลาหนึ่ง |
| Branch | บรานช์ | สาขาการพัฒนาที่แยกออกมาจากหลัก |
| Pull Request (PR) | พูล รีเควสต์ | การขอ Merge โค้ดพร้อมให้ทีม Review |
| Build Tool | บิลด์ ทูล | เครื่องมือแปลงโค้ดสำหรับใช้งานจริง |
| Hot Reload | ฮอต รีโหลด | อัปเดตเบราว์เซอร์อัตโนมัติเมื่อแก้โค้ด |
| DevTools | ดีฟทูลส์ | เครื่องมือ Developer ที่ฝังในเบราว์เซอร์ |
| LTS | เอลทีเอส | Long-Term Support — เวอร์ชันที่รับการดูแลระยะยาว |
| localhost | โลคัลโฮสต์ | ที่อยู่ของเครื่องคอมพิวเตอร์ตัวเอง |
| Port | พอร์ต | ช่องทางการสื่อสารในเครือข่าย เช่น :3000 |
| Environment Variable | เอนไวรอนเมนต์ แวริเอเบิล | ตัวแปรสภาพแวดล้อม เก็บค่าลับเช่น API Key |
| Markdown | มาร์กดาวน์ | ภาษาเขียนเอกสารแบบเรียบง่าย ไฟล์ .md |

---

## แหล่งเรียนรู้เพิ่มเติม

- **roadmap.sh/frontend** — แผนที่การเรียน Front-End Developer ที่สมบูรณ์ที่สุด
- **git-scm.com/doc** — เอกสารอย่างเป็นทางการของ Git
- **docs.github.com** — คู่มือ GitHub อย่างเป็นทางการ
- **vitejs.dev** — เอกสาร Vite
- **code.visualstudio.com/docs** — คู่มือ VS Code

---

*หน่วยถัดไป → หน่วยที่ 2: HTML, CSS และการออกแบบ UI สมัยใหม่*

NOTE****
หลังเรียนจบหน่วยที่ 1 โปรเจกต์บน GitHub ของคุณจะมีไฟล์เหล่านี้:

**`README.md`** — ไฟล์ที่คุณ _เขียนเอง_ ตามโจทย์ มีเนื้อหาประมาณนี้:

````markdown
# สวัสดี ฉันชื่อ [ชื่อคุณ]

ฉันกำลังเรียน Front-End Development

## 🛠 เครื่องมือที่ติดตั้งแล้ว
- Node.js v22 LTS
- pnpm
- VS Code + Extensions
- Git

## 🚀 วิธีรันโปรเจกต์นี้
```bash
pnpm install
pnpm dev
```

## 📚 สิ่งที่เรียนในหน่วยนี้
- Front-End vs Back-End คืออะไร
- Git & GitHub เบื้องต้น
- Vite Build Tool
````

***

**`.gitignore`** — คัดลอกมาตามที่บทเรียนสอน ไม่ต้องเขียนเอง:

```
node_modules/
dist/
.env
.DS_Store
```

***

**`package.json`** — Vite สร้างให้อัตโนมัติตอนรัน `pnpm create vite@latest`:

```json
{
  "name": "my-profile",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "vite build"
  },
  "devDependencies": {
    "vite": "^6.0.0"
  }
}
```

***

**`index.html`**, **`src/main.js`**, **`vite.config.js`** — Vite สร้างให้ทั้งหมด คุณ _แทบไม่ต้องแตะ_ ในหน่วย 1 โจทย์หลักคือ README.md กับการ Push ขึ้น GitHub ได้สำเร็จ
