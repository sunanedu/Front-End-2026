# 1/2569 By Sunan Edu

![ปก](/images/fe_cover.png)

# หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
### สำหรับผู้เริ่มต้น → สู่เส้นทางนักพัฒนา Front-End (ปรับปรุง พ.ค. 2026)

---

## ภาพรวมหลักสูตร

หลักสูตรนี้ออกแบบให้ผู้เรียนที่ไม่มีพื้นฐานสามารถเดินทางจาก "ไม่รู้อะไรเลย" ไปสู่การเป็น **Junior Front-End Developer** ได้อย่างมีโครงสร้าง แต่ละหน่วยสร้างต่อยอดจากหน่วยก่อนหน้า และมีโปรเจกต์ปฏิบัติจริงในทุกหน่วย

| หน่วย | ชื่อหน่วย | ระยะเวลาแนะนำ |
|-------|-----------|--------------|
| 1 | [พื้นฐานและเส้นทางของนักพัฒนา Front-End](unit1_frontend_foundation.md) | 1 สัปดาห์ |
| 2 | [HTML, CSS และการออกแบบ UI สมัยใหม่](unit2_html_css_modern_ui.md) | 2 สัปดาห์ |
| 3 | [JavaScript สมัยใหม่และ TypeScript](unit3_javascript_typescript.md) | 4 สัปดาห์ |
| 4 | [React และ Next.js — Framework หลัก (Part 1)](unit4_react_nextjs_part1.md) / [Part 2](unit4_react_nextjs_part2.md) | 3 สัปดาห์ |
| 5 | [การเชื่อมต่อ API, Authentication และการจัดการข้อมูล (Part 1)](unit5_api_auth_data_part1.md) / [Part 2](unit5_api_auth_data_part2.md) | 3 สัปดาห์ |
| 6 | [การทดสอบ, การ Deploy และการทำงานในทีมมืออาชีพ](unit6_testing_deployment_professional.md) | 2 สัปดาห์ |

---

## หน่วยที่ 1: พื้นฐานและเส้นทางของนักพัฒนา Front-End

### วัตถุประสงค์
ผู้เรียนเข้าใจภาพรวมของอาชีพ Front-End Developer ทั้งในด้านบทบาท เทคโนโลยี และเส้นทางการเติบโต รวมถึงการตั้งค่าสภาพแวดล้อมการทำงานได้จริง

### หัวข้อหลัก

#### 1.1 Front-End Development คืออะไร และทำไมถึงสำคัญ
- ความแตกต่างระหว่าง Front-End / Back-End / Full-Stack
- บทบาทของ Front-End Developer ในทีมพัฒนาซอฟต์แวร์ปี 2026
- ตัวอย่างผลงานจริง: เว็บแอป, Progressive Web App, Dashboard, E-commerce

#### 1.2 Roadmap นักพัฒนา Front-End ปี 2026
- เส้นทางเรียนรู้ที่ชัดเจน: HTML → CSS → JavaScript → TypeScript → React → Next.js
- ทักษะที่ตลาดงานต้องการในปัจจุบัน (อ้างอิง roadmap.sh/frontend)
- AI-Assisted Development: การใช้ GitHub Copilot และ Cursor เป็นเครื่องมือประจำวัน

#### 1.3 ตั้งค่าสภาพแวดล้อมการทำงาน (Development Environment)
- ติดตั้ง **Node.js LTS** และ **pnpm** (Package Manager ที่แนะนำ)
- ใช้ **VS Code** พร้อม Extension ที่จำเป็น: ESLint, Prettier, GitLens, Tailwind CSS IntelliSense, GitHub Copilot
- รู้จัก **Terminal / Command Line** เบื้องต้น: `cd`, `ls`, `mkdir`, `pnpm` commands
- สร้าง GitHub Account และเชื่อมต่อกับ VS Code

#### 1.4 Version Control ด้วย Git และ GitHub
- Git คืออะไร และทำไมทุกทีมถึงใช้
- คำสั่งพื้นฐาน: `git init`, `git add`, `git commit`, `git push`, `git pull`
- การสร้าง Repository บน GitHub และ push โค้ดขึ้นครั้งแรก
- **GitHub Flow**: branch, commit, pull request — วงจรการทำงานจริงในทีม

#### 1.5 รู้จักเครื่องมือและ Ecosystem ของ Front-End
- Browser DevTools: Inspect, Console, Network tab — เครื่องมือที่ใช้ทุกวัน
- **Vite**: Build Tool มาตรฐานที่แทนที่ Webpack แล้วในปัจจุบัน
- รู้จัก `package.json`, `node_modules`, และ `.gitignore`

### โปรเจกต์ปฏิบัติ
> ตั้งค่า Development Environment ทั้งหมด, สร้าง Repository แรกบน GitHub, เขียนไฟล์ `README.md` แนะนำตัวเองด้วย Markdown และ Push ขึ้น GitHub

---

## หน่วยที่ 2: HTML, CSS และการออกแบบ UI สมัยใหม่

### วัตถุประสงค์
ผู้เรียนสามารถสร้างหน้าเว็บที่สวยงาม Responsive และเข้าถึงได้ โดยใช้เทคนิคและเครื่องมือที่ทันสมัย

### หัวข้อหลัก

#### 2.1 HTML5 Semantic และ Accessibility
- Semantic HTML: `<header>`, `<main>`, `<section>`, `<article>`, `<nav>`, `<footer>`
- ทำไม Semantic HTML ถึงสำคัญ: SEO และ Accessibility ไปพร้อมกัน
- ARIA attributes เบื้องต้น: `aria-label`, `role`, `alt`
- HTML Forms ที่ถูกต้อง: `<label>`, `<input>`, `<fieldset>`, Validation

#### 2.2 CSS สมัยใหม่
- CSS Custom Properties: `--color-primary`, `--spacing-md`
- **Flexbox** และ **CSS Grid**: Layout หลักที่ใช้ในทุกโปรเจกต์
- CSS Nesting: เขียน CSS ซ้อนกันได้โดยไม่ต้องใช้ Preprocessor
- **Container Queries**: Responsive ตาม Container ไม่ใช่แค่ Viewport
- Mobile-First Responsive Design
- CSS Transitions และ Animations

#### 2.3 Tailwind CSS v4
- แนวคิด Utility-First CSS และทำไมถึงได้รับความนิยมสูง
- ติดตั้งและตั้งค่า Tailwind CSS v4 กับ Vite
- Class ที่ใช้บ่อย: layout, spacing, typography, colors, responsive breakpoints
- Dark Mode ด้วย Tailwind
- จัดการ Custom Design System ผ่าน `tailwind.config`

#### 2.4 shadcn/ui — UI Component Library
- แนวคิด Headless Component: แยก Logic ออกจาก Style
- ติดตั้งและใช้งาน shadcn/ui กับ Next.js
- Component ที่ใช้บ่อย: Button, Input, Dialog, Card, Table, Toast
- ปรับแต่ง Component ให้เข้ากับ Design ของโปรเจกต์

#### 2.5 Figma และ Design Handoff
- อ่าน Design จาก Figma และแปลงเป็น Code
- Dev Mode ใน Figma: อ่าน Spec, สี, spacing, font, export assets
- การสื่อสารระหว่าง Designer และ Developer อย่างมีประสิทธิภาพ

### โปรเจกต์ปฏิบัติ
> สร้าง Landing Page สินค้า/บริการ 1 หน้า (Mobile-Responsive) ด้วย HTML + Tailwind CSS + shadcn/ui โดย Clone Design จาก Figma ที่กำหนดให้ และ Push ขึ้น GitHub

---

## หน่วยที่ 3: JavaScript สมัยใหม่และ TypeScript

### วัตถุประสงค์
ผู้เรียนเข้าใจและเขียน JavaScript สมัยใหม่ (ES2022+) ได้ พร้อมใช้ TypeScript ซึ่งเป็นมาตรฐาน de facto ของทีมพัฒนาจริงในปี 2026

### หัวข้อหลัก

#### 3.1 JavaScript พื้นฐานที่จำเป็น
- Variables: `const` และ `let` เท่านั้น
- Data Types และ Array Methods: `map`, `filter`, `reduce`, `find`, `findIndex`
- Object Destructuring, Spread Operator, Optional Chaining `?.`, Nullish Coalescing `??`
- Template Literals และ Short-circuit Evaluation

#### 3.2 Functions และ Asynchronous JavaScript
- Arrow Functions และ Higher-Order Functions
- **Promises** และ **async/await**: รากฐานของการเรียก API ทุกชนิด
- Error Handling ด้วย `try/catch/finally`
- `fetch()` API: ดึงข้อมูลจากเซิร์ฟเวอร์และจัดการ Response

#### 3.3 DOM Manipulation และ Events
- เลือก Element: `querySelector`, `querySelectorAll`
- เปลี่ยนแปลง DOM: `textContent`, `classList`, `setAttribute`
- Event Listeners: `click`, `submit`, `input`, `keydown`
- Event Delegation: จัดการ Event อย่างมีประสิทธิภาพ

#### 3.4 ES Modules และ Tooling
- `import` / `export`: โครงสร้างโค้ดแบบ Module
- ESLint + Prettier: มาตรฐานโค้ดในทีม ตั้งค่าตั้งแต่ต้น
- บทบาทของ Vite ในกระบวนการ Build

#### 3.5 TypeScript
- TypeScript คืออะไร และทำไมทุกทีมใช้ในปี 2026
- Type Annotations: `string`, `number`, `boolean`, `Array<T>`, `Record<K,V>`
- Interface และ Type Alias: กำหนดโครงสร้างข้อมูล
- Generics เบื้องต้น: เขียน Function ที่ยืดหยุ่น
- การตั้งค่า TypeScript กับ Vite และ `tsconfig.json`

### โปรเจกต์ปฏิบัติ
> สร้างแอป "Task Manager" ด้วย Vanilla JavaScript + TypeScript: เพิ่ม/ลบ/แก้ไข Task, กรองตามสถานะ, บันทึกลง `localStorage`, มี Type ครบทุก Function

---

## หน่วยที่ 4: React และ Next.js — Framework หลัก

### วัตถุประสงค์
ผู้เรียนสามารถพัฒนาแอปพลิเคชันด้วย React 19 และ Next.js 15 พร้อมเข้าใจหลักการ Component-Based Architecture, State Management และ Server-Side Rendering

### หัวข้อหลัก

#### 4.1 Component-Based Architecture
- แนวคิด Component: หน่วยย่อยที่ประกอบกันเป็น UI
- การแบ่ง UI เป็น Component ย่อย (Component Decomposition)
- Props: ส่งข้อมูล Parent → Child
- Component Reusability: เขียนครั้งเดียว ใช้ได้ทั่วทั้ง App

#### 4.2 React 19 — Core Concepts
- JSX Syntax และ Functional Components
- React Hooks ที่จำเป็น: `useState`, `useEffect`, `useRef`, `useMemo`, `useCallback`
- Custom Hooks: แยก Logic ออกมาเพื่อใช้ซ้ำได้
- `use()` Hook: Pattern ใหม่สำหรับ Async Data ใน React 19
- React Server Components (RSC): Component ที่ Render บน Server โดยไม่ส่ง JavaScript ไป Client

#### 4.3 State Management ด้วย Zustand
- Local State vs Global State: เลือกใช้ให้เหมาะสม
- **React Context API**: สำหรับ State ขนาดเล็กที่แชร์ใน Subtree
- **Zustand**: Global State ที่ง่าย มีประสิทธิภาพ และเป็นมาตรฐานในปี 2026
- Pattern การออกแบบ Store ที่ดี: slice, actions, selectors

#### 4.4 Next.js 15 — Meta-Framework
- Next.js คืออะไร และทำไมถึงเป็น Standard สำหรับ React App
- **App Router**: โครงสร้างไฟล์ตาม Folder = Route
- **Server-Side Rendering (SSR)** vs **Static Site Generation (SSG)** vs **Incremental Static Regeneration (ISR)**
- Server Actions: เรียก Back-End Logic จาก Component โดยตรง
- `next/image`, `next/font`, `next/link`: Optimization Built-in

#### 4.5 Routing ใน Next.js
- File-based Routing: `app/page.tsx`, `app/about/page.tsx`
- Dynamic Routes: `app/products/[id]/page.tsx`
- Nested Layouts: `layout.tsx` ที่ใช้ร่วมกันระหว่าง Page
- Loading UI และ Error Boundary ด้วย `loading.tsx` และ `error.tsx`
- Protected Routes: ป้องกัน Page ที่ต้อง Login ก่อน

#### 4.6 Performance Optimization
- Lazy Loading ด้วย `dynamic()` และ `React.lazy()`
- Image Optimization และ Core Web Vitals: LCP, CLS, INP
- Bundle Analysis: ตรวจสอบขนาด JavaScript ที่ส่งไป Client

### โปรเจกต์ปฏิบัติ
> พัฒนาเว็บแอป "Movie Finder" ด้วย Next.js 15 + TypeScript + Tailwind + shadcn/ui: แสดงรายการหนัง, ค้นหา, ดูรายละเอียด, บันทึก Favorite, รองรับ Mobile ทุกขนาด

---

## หน่วยที่ 5: การเชื่อมต่อ API, Authentication และการจัดการข้อมูล

### วัตถุประสงค์
ผู้เรียนสามารถเชื่อมต่อแอปพลิเคชันกับ API ภายนอก จัดการ Authentication และเข้าใจ Flow ข้อมูลจาก Front-End ถึง Database

### หัวข้อหลัก

#### 5.1 RESTful API และ HTTP Protocol
- HTTP Methods: GET, POST, PUT, PATCH, DELETE — ใช้เมื่อไหร่
- HTTP Status Codes ที่ต้องรู้: 200, 201, 400, 401, 403, 404, 422, 500
- Request Headers: `Authorization`, `Content-Type`, `Accept`
- ทดสอบ API ด้วย **Bruno** (Open-source, เก็บ Collection ใน Git ได้)

#### 5.2 Data Fetching ด้วย TanStack Query v5
- ทำไม `useEffect` ไม่ใช่วิธีที่ดีในการดึงข้อมูล
- **TanStack Query**: Cache, Loading State, Error State, Background Refetch
- `useQuery`: ดึงข้อมูล, `useMutation`: เปลี่ยนแปลงข้อมูล
- Optimistic Updates: แสดงผลทันทีก่อน Server ตอบกลับ
- Infinite Scroll ด้วย `useInfiniteQuery`

#### 5.3 Authentication ด้วย Auth.js v5
- JWT (JSON Web Token): โครงสร้าง Header.Payload.Signature และวิธีเก็บที่ปลอดภัย (HttpOnly Cookie)
- **OAuth 2.0 / OpenID Connect**: Login ด้วย Google, GitHub — ไม่ต้องจัดการ Password เอง
- **Auth.js v5**: ติดตั้งและตั้งค่ากับ Next.js 15
- Session Management: ตรวจสอบสถานะ Login ฝั่ง Server และ Client
- Protected Routes: Middleware ใน Next.js สำหรับป้องกัน Route

#### 5.4 Supabase — Backend as a Service
- Supabase คืออะไร: PostgreSQL + Auth + Storage + Realtime ในที่เดียว
- ตั้งค่า Supabase Project และเชื่อมต่อกับ Next.js
- CRUD Operations ผ่าน Supabase Client Library
- Row Level Security (RLS): ควบคุมสิทธิ์การเข้าถึงข้อมูลระดับแถว
- Realtime Subscriptions: รับข้อมูล Update แบบ Live

#### 5.5 Web Security สำหรับ Front-End Developer
- XSS (Cross-Site Scripting): เกิดอย่างไร และป้องกันอย่างไร
- CORS: ทำความเข้าใจและวิธีแก้ปัญหาที่พบบ่อย
- Environment Variables: ข้อมูล Secret ต้องอยู่ใน `.env` เท่านั้น ห้ามอยู่ใน Code
- Content Security Policy (CSP) เบื้องต้น

### โปรเจกต์ปฏิบัติ
> ต่อยอดโปรเจกต์ Movie Finder จาก Unit 4: เพิ่มระบบ Login ด้วย Google (Auth.js v5), บันทึก Favorite Movies ลง Supabase, แสดงข้อมูลเฉพาะผู้ใช้ที่ Login, ป้องกัน Route ที่ต้อง Login

---

## หน่วยที่ 6: การทดสอบ, การ Deploy และการทำงานในทีมมืออาชีพ

### วัตถุประสงค์
ผู้เรียนเข้าใจกระบวนการพัฒนาซอฟต์แวร์จริงในทีม ตั้งแต่การอ่าน Spec, การทดสอบ, CI/CD จนถึงการส่งมอบงานอย่างมืออาชีพ

### หัวข้อหลัก

#### 6.1 การอ่านและทำความเข้าใจ Specification
- อ่าน **Functional Specification** และ **User Stories** แบบ Agile
- ทำความเข้าใจ UML Diagram เบื้องต้น: Use Case Diagram, Sequence Diagram
- **Agile / Scrum**: Sprint, Product Backlog, Daily Standup, Sprint Review
- ใช้ **Linear** จัดการงานและ Track Progress
- แปลง User Story → Component List → Task → Estimate

#### 6.2 Testing สำหรับ Front-End Developer

**Unit Testing ด้วย Vitest**
- แนวคิด Test-Driven Development (TDD)
- ติดตั้งและเขียน Test ด้วย **Vitest** (เร็วกว่า Jest และรองรับ Vite/Next.js โดยตรง)
- เขียน Test สำหรับ Pure Function และ Custom Hook

**Component Testing ด้วย React Testing Library**
- หลักการ: "Test พฤติกรรมที่ผู้ใช้เห็น ไม่ใช่ Implementation"
- `render`, `screen`, `userEvent`: เครื่องมือหลัก
- Test Form Submission, Async Loading, Error State

**End-to-End Testing ด้วย Playwright**
- เขียน Test จำลองผู้ใช้จริง: เปิดหน้า, Login, กรอกฟอร์ม, ตรวจสอบผล
- Mock API ด้วย **MSW (Mock Service Worker)**: ทดสอบ Network ได้โดยไม่ต้องพึ่ง Server จริง
- Visual Regression Testing: จับภาพและเปรียบเทียบ UI อัตโนมัติ

#### 6.3 CI/CD และ Deployment
- CI/CD คืออะไร: Continuous Integration / Continuous Deployment
- ตั้งค่า **GitHub Actions**: รัน Lint และ Test อัตโนมัติทุกครั้งที่ Push
- Deploy บน **Vercel**: เชื่อมต่อ GitHub และ Deploy อัตโนมัติ
- **Preview Deployment**: ทดสอบ Pull Request ก่อน Merge ด้วย URL เฉพาะ
- Environment Variables ใน Vercel: Production vs Preview vs Development

#### 6.4 Git Workflow ในทีมจริง
- **GitHub Flow**: branch → commit → pull request → review → merge — วงจรหลัก
- Branch Naming Convention: `feature/user-login`, `fix/cart-bug`, `chore/update-deps`
- **Conventional Commits**: `feat:`, `fix:`, `docs:`, `chore:` — เขียน Commit ที่มีความหมาย
- Code Review: วิธีให้และรับ Feedback อย่างสร้างสรรค์
- Merge Conflict: วิธีเกิด, วิธีแก้, และวิธีป้องกัน

#### 6.5 Debugging และ Error Handling
- Browser DevTools อย่างจริงจัง: Breakpoints, Network tab, Performance profiler
- **Error Boundary** ใน React: ดักจับ Error ระดับ Component
- **Sentry**: Error Tracking ใน Production — รู้เมื่อ User เจอ Bug
- การอ่าน Stack Trace และค้นหาต้นเหตุอย่างเป็นระบบ
- AI-Assisted Debugging: ใช้ GitHub Copilot และ Claude ช่วย Debug ได้อย่างถูกวิธี

#### 6.6 การจัดทำเอกสารและส่งมอบงาน
- เขียน **README.md** ที่สมบูรณ์: วิธีติดตั้ง, ใช้งาน, สถาปัตยกรรม, Environment Variables
- **TSDoc**: เขียน Comment อธิบาย Function และ Type
- Pre-deployment Checklist: ตรวจสอบก่อนขึ้น Production ทุกครั้ง
- **Semantic Versioning**: `1.0.0` (Major.Minor.Patch) และ Changelog

### โปรเจกต์ปฏิบัติ (Capstone Project)
> พัฒนาโปรเจกต์ Capstone เป็นทีม 2-3 คน: เลือกหัวข้อเอง, เขียน User Stories, พัฒนาด้วย Next.js 15 + TypeScript + Tailwind + Supabase, เขียน Test ด้วย Vitest และ Playwright, ตั้งค่า CI/CD ด้วย GitHub Actions, Deploy บน Vercel, จัดทำ README สมบูรณ์ และนำเสนอผลงาน

---

## เส้นทางหลังจากเรียนจบหลักสูตร

```
จบหลักสูตร 6 หน่วย
        │
        ├─► Junior Front-End Developer
        │   (พร้อม Apply งาน)
        │
        ├─► เจาะลึก React/Next.js Ecosystem
        │   (Server Actions, Streaming, Suspense, Parallel Routes)
        │
        ├─► Full-Stack Developer
        │   (Node.js + Hono, Prisma, PostgreSQL, tRPC)
        │
        └─► UI Engineering & Animation
            (Framer Motion, GSAP, Three.js + React Three Fiber)
```

---

## Technology Stack ที่ใช้ตลอดหลักสูตร (2026)

| หมวด | เครื่องมือ |
|------|-----------|
| Editor | VS Code + GitHub Copilot |
| Runtime | Node.js LTS |
| Package Manager | pnpm |
| Language | TypeScript |
| Framework | Next.js 15 (React 19) |
| Styling | Tailwind CSS v4 + shadcn/ui |
| State Management | Zustand |
| Data Fetching | TanStack Query v5 |
| Authentication | Auth.js v5 |
| Backend/Database | Supabase |
| Testing | Vitest + React Testing Library + Playwright |
| Mock | MSW (Mock Service Worker) |
| Version Control | Git + GitHub |
| CI/CD | GitHub Actions |
| Deployment | Vercel |
| API Testing | Bruno |
| AI Coding | GitHub Copilot + Cursor |

---

*ปรับปรุงล่าสุด: พฤษภาคม 2026*
