# หน่วยที่ 6: การทดสอบ, การ Deploy และการทำงานในทีมมืออาชีพ

> **หลักสูตร:** หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
> **ระดับ:** ปานกลาง–สูง (Intermediate–Advanced)
> **ระยะเวลา:** 3 สัปดาห์
> **ต่อจาก:** Unit 5 — API, Authentication, Supabase
> **ปรับปรุงล่าสุด:** พฤษภาคม 2026

---

## วัตถุประสงค์การเรียนรู้

เมื่อเรียนจบหน่วยนี้ ผู้เรียนจะสามารถ:

1. อ่านและแปลง User Stories เป็น Task ที่พัฒนาได้จริงใน Sprint ของทีม Agile
2. เขียน Unit Test ด้วย Vitest สำหรับ Pure Function และ Custom Hook ได้
3. เขียน Component Test ด้วย React Testing Library โดยทดสอบจากมุมมองผู้ใช้ได้
4. เขียน End-to-End Test ด้วย Playwright จำลองการใช้งานจริงได้
5. ใช้ MSW Mock API เพื่อทดสอบแบบ Isolated ได้
6. ตั้งค่า GitHub Actions ให้รัน CI Pipeline อัตโนมัติได้
7. Deploy แอปพลิเคชัน Next.js บน Vercel พร้อมตั้งค่า Environment Variables ได้
8. ทำงานด้วย GitHub Flow, Conventional Commits และ Code Review อย่างมืออาชีพได้
9. Debug อย่างเป็นระบบด้วย DevTools, Error Boundary และ Sentry ได้
10. จัดทำเอกสาร README และส่งมอบงานตาม Pre-deployment Checklist ได้

---

## ภาพรวมหน่วยที่ 6

```
หน่วยที่ 6 — กระบวนการพัฒนาซอฟต์แวร์จริงในทีม
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  6.1 Specification & Agile
  └── อ่าน User Stories → แตก Task → ประเมินงาน

  6.2 Testing
  ├── Unit Test (Vitest)
  ├── Component Test (React Testing Library)
  └── E2E Test (Playwright + MSW)

  6.3 CI/CD & Deployment
  ├── GitHub Actions (CI Pipeline)
  └── Vercel (Deployment)

  6.4 Git Workflow ในทีม
  ├── GitHub Flow
  ├── Conventional Commits
  └── Code Review

  6.5 Debugging & Error Handling
  ├── DevTools อย่างจริงจัง
  ├── Error Boundary
  └── Sentry

  6.6 เอกสารและการส่งมอบงาน
  ├── README.md ที่สมบูรณ์
  ├── TSDoc
  └── Pre-deployment Checklist

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 6.1 การอ่านและทำความเข้าใจ Specification

### ทำไมนักพัฒนาต้องอ่าน Spec ให้เป็น?

ในโลกการทำงานจริง นักพัฒนาไม่ได้รับโจทย์ชัดเจนว่า "เขียน Function นี้" แต่ได้รับ **User Story** (ยูเซอร์ สตอรี — เรื่องราวจากมุมมองผู้ใช้) หรือ **Functional Specification** (ฟังก์ชันนัล สเปซิฟิเคชัน — เอกสารระบุสิ่งที่ระบบต้องทำ) แทน

Developer ที่อ่าน Spec ไม่เป็น จะเกิดปัญหาเหล่านี้เสมอ:
- สร้าง Feature ผิดจาก Requirement
- ทำงานเสร็จแล้วต้องแก้ใหม่ทั้งหมด (Rework)
- เสียเวลาประชุมเพื่ออธิบายความเข้าใจผิด
- Pull Request ถูก Reject เพราะไม่ตรง Spec

---

### User Story คืออะไร?

**User Story** เป็นวิธีอธิบาย Requirement ในรูปแบบที่ทุกคนในทีมเข้าใจตรงกัน โดยยึดมุมมองของผู้ใช้เป็นหลัก

**รูปแบบมาตรฐาน:**
```
As a [ประเภทผู้ใช้]
I want to [สิ่งที่ต้องการทำ]
So that [เหตุผล/ประโยชน์ที่ได้รับ]
```

**ตัวอย่างจริงสำหรับโปรเจกต์ Movie Finder:**

```
User Story #1:
As a registered user
I want to save movies to my favorites list
So that I can find them easily later without searching again

Acceptance Criteria (เงื่อนไขที่ต้องผ่านก่อนถือว่าเสร็จ):
✅ ปุ่ม "เพิ่มใน Favorite" แสดงในทุก Movie Card
✅ เมื่อกดแล้ว สถานะเปลี่ยนเป็น "บันทึกแล้ว" ทันที
✅ ข้อมูลถูกบันทึกลง Supabase และผูกกับ User ที่ Login
✅ ผู้ใช้ที่ไม่ได้ Login กดแล้ว Redirect ไปหน้า Login
✅ หน้า /favorites แสดงเฉพาะหนังที่ผู้ใช้นั้นบันทึกไว้
```

```
User Story #2:
As a movie enthusiast
I want to search movies by title
So that I can quickly find the movie I'm looking for

Acceptance Criteria:
✅ Search Box อยู่ที่หน้าหลัก มองเห็นได้ชัดเจน
✅ ผลการค้นหาแสดงทันทีหลังพิมพ์ (Debounce 300ms)
✅ หากไม่พบผล แสดงข้อความ "ไม่พบหนังที่ค้นหา"
✅ ใช้งานได้บนมือถือ (Responsive)
✅ Loading State แสดงระหว่างรอผล
```

---

### Acceptance Criteria และ Definition of Done

**Acceptance Criteria** (แอคเซปแตนซ์ ไครทีเรีย) คือเงื่อนไขที่ต้องครบก่อนถือว่า Story นั้นเสร็จ เขียนโดย Product Manager หรือ Tech Lead

**Definition of Done (DoD)** คือมาตรฐานที่ทีมตกลงร่วมกัน ว่า Feature หนึ่งถือว่า "เสร็จสมบูรณ์" เมื่อไหร่

```
Definition of Done ของทีม (ตัวอย่าง):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
☐ โค้ดผ่าน Lint และ Type Check
☐ เขียน Unit Test ครอบคลุม Logic หลัก
☐ เขียน Component Test สำหรับ UI สำคัญ
☐ PR ได้รับ Approve จาก Reviewer อย่างน้อย 1 คน
☐ Deploy บน Preview Environment แล้วทดสอบ
☐ Acceptance Criteria ทุกข้อผ่าน
☐ README อัปเดตถ้ามีการเปลี่ยนแปลงสำคัญ
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### Agile / Scrum ในทีม Front-End

**Agile** (แอไจล์) คือแนวทางการพัฒนาซอฟต์แวร์ที่เน้นความยืดหยุ่น ส่งมอบงานทีละส่วนเล็ก ๆ และรับ Feedback เร็ว

**Scrum** (สครัม) คือ Framework หนึ่งที่นำ Agile มาปฏิบัติ โดยแบ่งการทำงานเป็น **Sprint** (สปรินต์ — รอบการทำงาน)

```
Sprint 2 สัปดาห์ — วงจรทั่วไปในทีม:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Sprint Planning                Daily Standup           Sprint Review
(วันแรก — 2 ชม.)               (ทุกวัน — 15 นาที)     (วันสุดท้าย — 1 ชม.)
       │                              │                        │
       ▼                              ▼                        ▼
 เลือก Story           "เมื่อวานทำอะไร?"          Demo ผลงานให้
 จาก Backlog           "วันนี้จะทำอะไร?"           Stakeholder ดู
 มาทำใน Sprint         "มีอุปสรรคอะไร?"           รับ Feedback
                                                           │
                                                           ▼
                                                    Sprint Retrospective
                                                    (ทบทวนกระบวนการ)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**บทบาทใน Scrum:**

| บทบาท | ความรับผิดชอบ |
|-------|-------------|
| **Product Owner** | กำหนด Priority ของ Backlog, ตัดสินใจว่าทำอะไรก่อน |
| **Scrum Master** | ดูแลกระบวนการ Scrum, ขจัดอุปสรรค |
| **Development Team** | คุณอยู่ที่นี่ — พัฒนา Feature ตาม Sprint |

---

### แปลง User Story เป็น Task จริง

ขั้นตอนที่นักพัฒนาต้องทำเมื่อได้รับ User Story:

```
User Story: "ผู้ใช้สามารถ Login ด้วย Google ได้"
         │
         ▼
Step 1: ทำความเข้าใจ Acceptance Criteria ทุกข้อ
         │
         ▼
Step 2: แตก Story เป็น Task ย่อย
  ├── Task 1: ติดตั้งและตั้งค่า Auth.js v5           (2 ชม.)
  ├── Task 2: สร้าง Google OAuth Provider             (1 ชม.)
  ├── Task 3: สร้าง Login Page UI                    (2 ชม.)
  ├── Task 4: ตั้งค่า Session และ Middleware          (1.5 ชม.)
  ├── Task 5: เขียน Test สำหรับ Protected Route       (2 ชม.)
  └── Task 6: ทดสอบ E2E Flow ทั้งหมด                 (1.5 ชม.)
         │
         ▼
Step 3: Estimate (ประเมิน) เวลาแต่ละ Task
         │
         ▼
Step 4: สร้าง Issue ใน Linear/GitHub Issues และ Assign ตัวเอง
```

**การใช้ Linear จัดการงาน:**

Linear (ลีเนียร์) คือเครื่องมือ Project Management ที่ทีม Tech ชั้นนำใช้มากในปี 2026 เน้นความเร็วและ Keyboard-first

```
โครงสร้างใน Linear:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Team: Frontend
  └── Cycle (Sprint) 12
      ├── 🔴 Backlog
      │   └── FEAT-42: Implement favorites feature
      ├── 🟡 In Progress
      │   └── FEAT-38: Search with debounce
      └── 🟢 Done
          └── FEAT-35: Movie detail page
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### UML Diagram เบื้องต้น

**UML (Unified Modeling Language)** เป็นภาษาแผนภาพสำหรับอธิบายระบบซอฟต์แวร์ Front-End Developer ที่ต้องรู้มี 2 แบบหลัก:

**1. Use Case Diagram — "ใครทำอะไรได้บ้าง"**

```
          ┌─────────────────────────────────────────┐
          │         Movie Finder System             │
          │                                         │
          │   ┌─────────────────┐                   │
          │   │ ค้นหาหนัง       │◄──────── Guest     │
          │   └─────────────────┘                   │
          │                                         │
          │   ┌─────────────────┐                   │
          │   │ ดูรายละเอียดหนัง │◄──────── Guest     │
          │   └─────────────────┘                   │
          │                                         │
          │   ┌─────────────────┐                   │
          │   │ บันทึก Favorite │◄──── Logged-in     │
          │   └─────────────────┘       User         │
          │                                         │
          │   ┌─────────────────┐                   │
          │   │ ดู My Favorites │◄──── Logged-in     │
          │   └─────────────────┘       User         │
          └─────────────────────────────────────────┘
```

**2. Sequence Diagram — "ข้อมูลไหลอย่างไร"**

```
User          Browser          Next.js          Supabase
 │                │                │                │
 │  กด Favorite   │                │                │
 │──────────────►│                │                │
 │                │  POST /api/    │                │
 │                │  favorites     │                │
 │                │───────────────►│                │
 │                │                │  INSERT row    │
 │                │                │───────────────►│
 │                │                │  { success }   │
 │                │                │◄───────────────│
 │                │  { id, movie } │                │
 │                │◄───────────────│                │
 │  UI อัปเดต     │                │                │
 │◄──────────────│                │                │
```

---

## 6.2 Testing สำหรับ Front-End Developer

### ทำไมต้องเขียน Test?

Developer มือใหม่มักมองว่า Test คือ "งานพิเศษ" ที่ทำเมื่อมีเวลา แต่ในทีมมืออาชีพ Test คือส่วนหนึ่งของการ "เขียนโค้ดให้เสร็จ"

```
โลกที่ไม่มี Test:                    โลกที่มี Test:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
แก้ Bug ในหน้า A                    แก้ Bug ในหน้า A
→ หน้า B พัง (ไม่รู้)               → Test รัน อัตโนมัติ
→ User รายงาน Bug                   → พบว่าหน้า B พัง
→ ต้องแก้อีกครั้ง                  → แก้ก่อน Push
→ Reputation เสียหาย               → User ไม่เคยเห็น Bug
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Testing Pyramid — ลำดับความสำคัญ:**

```
           /\
          /E2E\          ← ทดสอบ User Flow ทั้งหมด (น้อยแต่สำคัญ)
         /──────\
        /  Integ- \      ← ทดสอบ Component + API ร่วมกัน
       /  ration   \
      /──────────────\
     /   Unit Tests   \  ← ทดสอบ Function เดี่ยว ๆ (มากที่สุด)
    /──────────────────\

ยิ่งล่าง: เร็วกว่า, ถูกกว่า, เขียนง่ายกว่า, เขียนเยอะกว่า
ยิ่งบน:  ช้ากว่า, แพงกว่า, แต่ครอบคลุม Scenario จริงมากกว่า
```

---

### การตั้งค่า Testing Environment

ก่อนเริ่มเขียน Test ต้องติดตั้งเครื่องมือที่จำเป็น:

```bash
# ติดตั้ง Vitest, React Testing Library และ MSW
pnpm add -D vitest @vitejs/plugin-react jsdom
pnpm add -D @testing-library/react @testing-library/user-event @testing-library/jest-dom
pnpm add -D msw
pnpm add -D @playwright/test
```

**ตั้งค่า `vitest.config.ts`:**

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',           // จำลอง Browser environment
    globals: true,                  // ใช้ describe, it, expect โดยไม่ต้อง import
    setupFiles: ['./src/test/setup.ts'],
    coverage: {
      reporter: ['text', 'html'],   // รายงาน Coverage ทั้งใน Terminal และ HTML
      exclude: ['node_modules/', 'src/test/'],
    },
  },
})
```

**สร้าง `src/test/setup.ts`:**

```typescript
// src/test/setup.ts
import '@testing-library/jest-dom'
// ไฟล์นี้รันก่อน Test ทุกไฟล์
// @testing-library/jest-dom เพิ่ม Matcher เช่น toBeInTheDocument(), toHaveValue()
```

**เพิ่ม Script ใน `package.json`:**

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage",
    "test:ui": "vitest --ui",
    "test:e2e": "playwright test"
  }
}
```

---

### Unit Testing ด้วย Vitest

**Unit Test** (ยูนิต เทสต์) คือการทดสอบ "หน่วยเล็กสุด" ของโค้ด ซึ่งมักหมายถึง Function เดี่ยว ๆ ที่ไม่ขึ้นกับสิ่งอื่น

#### แนวคิด TDD (Test-Driven Development)

**TDD** คือการ "เขียน Test ก่อน แล้วค่อยเขียนโค้ดให้ Test ผ่าน" — ฟังดูย้อนแย้งแต่ให้ผลลัพธ์ที่ดีมาก

```
วงจร TDD (Red → Green → Refactor):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. RED    — เขียน Test ที่ Fail (เพราะยังไม่มีโค้ด)
2. GREEN  — เขียนโค้ดให้น้อยที่สุดเพื่อให้ Test ผ่าน
3. REFACTOR — ปรับปรุงโค้ดให้สะอาดขึ้น โดย Test ยังผ่าน
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

#### โครงสร้าง Test ด้วย Vitest

```typescript
// ไวยากรณ์หลักของ Vitest
describe('ชื่อ Group', () => {
  it('ควรทำสิ่งนี้เมื่อเกิดสิ่งนั้น', () => {
    // Arrange — เตรียมข้อมูล
    // Act     — ดำเนินการ
    // Assert  — ตรวจสอบผลลัพธ์
  })
})
```

**รูปแบบ AAA (Arrange-Act-Assert)** คือมาตรฐานการเขียน Test ที่ชัดเจนที่สุด:

```typescript
describe('formatPrice', () => {
  it('ควรแสดงราคาพร้อมหน่วยบาทอย่างถูกต้อง', () => {
    // Arrange — เตรียมข้อมูลทดสอบ
    const price = 1500

    // Act — เรียกใช้ Function ที่ต้องการทดสอบ
    const result = formatPrice(price)

    // Assert — ตรวจสอบว่าผลลัพธ์ถูกต้อง
    expect(result).toBe('฿1,500.00')
  })
})
```

#### ตัวอย่างจริง: ทดสอบ Utility Functions

สมมติมีไฟล์ `src/lib/utils.ts`:

```typescript
// src/lib/utils.ts

/**
 * แปลงคะแนนตัวเลข (0-10) เป็น Grade ตัวอักษร
 */
export function scoreToGrade(score: number): string {
  if (score < 0 || score > 10) throw new Error('Score must be between 0 and 10')
  if (score >= 9) return 'A'
  if (score >= 7) return 'B'
  if (score >= 5) return 'C'
  return 'F'
}

/**
 * กรอง Movie ที่มี Rating เกินกว่า threshold
 */
export function filterByRating(movies: Movie[], minRating: number): Movie[] {
  return movies.filter(movie => movie.rating >= minRating)
}

/**
 * แปลง minutes เป็น "2h 30m"
 */
export function formatRuntime(minutes: number): string {
  const hours = Math.floor(minutes / 60)
  const mins = minutes % 60
  if (hours === 0) return `${mins}m`
  if (mins === 0) return `${hours}h`
  return `${hours}h ${mins}m`
}
```

เขียน Test สำหรับ utils.ts:

```typescript
// src/lib/utils.test.ts
import { describe, it, expect } from 'vitest'
import { scoreToGrade, filterByRating, formatRuntime } from './utils'

// ── scoreToGrade ──────────────────────────────────────────────
describe('scoreToGrade', () => {
  it('ควรคืนค่า A เมื่อ score เป็น 9 ขึ้นไป', () => {
    expect(scoreToGrade(9)).toBe('A')
    expect(scoreToGrade(10)).toBe('A')
    expect(scoreToGrade(9.5)).toBe('A')
  })

  it('ควรคืนค่า B เมื่อ score อยู่ระหว่าง 7-8.9', () => {
    expect(scoreToGrade(7)).toBe('B')
    expect(scoreToGrade(8)).toBe('B')
    expect(scoreToGrade(8.9)).toBe('B')
  })

  it('ควรคืนค่า C เมื่อ score อยู่ระหว่าง 5-6.9', () => {
    expect(scoreToGrade(5)).toBe('C')
    expect(scoreToGrade(6.9)).toBe('C')
  })

  it('ควรคืนค่า F เมื่อ score ต่ำกว่า 5', () => {
    expect(scoreToGrade(4.9)).toBe('F')
    expect(scoreToGrade(0)).toBe('F')
  })

  it('ควร throw Error เมื่อ score อยู่นอกช่วง 0-10', () => {
    expect(() => scoreToGrade(-1)).toThrow('Score must be between 0 and 10')
    expect(() => scoreToGrade(11)).toThrow('Score must be between 0 and 10')
  })
})

// ── filterByRating ────────────────────────────────────────────
describe('filterByRating', () => {
  const mockMovies = [
    { id: 1, title: 'Movie A', rating: 8.5 },
    { id: 2, title: 'Movie B', rating: 6.0 },
    { id: 3, title: 'Movie C', rating: 9.2 },
    { id: 4, title: 'Movie D', rating: 7.0 },
  ]

  it('ควรกรองเฉพาะหนังที่ rating ≥ minRating', () => {
    const result = filterByRating(mockMovies, 7.5)
    expect(result).toHaveLength(2)
    expect(result.map(m => m.id)).toEqual([1, 3])
  })

  it('ควรคืน array ว่างเมื่อไม่มีหนังผ่าน threshold', () => {
    const result = filterByRating(mockMovies, 10)
    expect(result).toHaveLength(0)
    expect(result).toEqual([])
  })

  it('ควรคืนทุกหนังเมื่อ minRating เป็น 0', () => {
    const result = filterByRating(mockMovies, 0)
    expect(result).toHaveLength(4)
  })
})

// ── formatRuntime ─────────────────────────────────────────────
describe('formatRuntime', () => {
  it('ควรแสดงเฉพาะนาทีเมื่อน้อยกว่า 60 นาที', () => {
    expect(formatRuntime(45)).toBe('45m')
  })

  it('ควรแสดงเฉพาะชั่วโมงเมื่อหารลงตัว', () => {
    expect(formatRuntime(120)).toBe('2h')
  })

  it('ควรแสดงทั้งชั่วโมงและนาที', () => {
    expect(formatRuntime(150)).toBe('2h 30m')
    expect(formatRuntime(95)).toBe('1h 35m')
  })
})
```

#### ทดสอบ Custom Hook

Custom Hook มักทดสอบยากเพราะ Hook ทำงานได้เฉพาะใน React Component เราต้องใช้ `renderHook` จาก React Testing Library:

```typescript
// src/hooks/useDebounce.ts
import { useState, useEffect } from 'react'

export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value)
    }, delay)

    return () => clearTimeout(timer)
  }, [value, delay])

  return debouncedValue
}
```

```typescript
// src/hooks/useDebounce.test.ts
import { describe, it, expect, vi, beforeEach, afterEach } from 'vitest'
import { renderHook, act } from '@testing-library/react'
import { useDebounce } from './useDebounce'

describe('useDebounce', () => {
  // ใช้ Fake Timer เพื่อควบคุมเวลาในการทดสอบ
  beforeEach(() => {
    vi.useFakeTimers()
  })

  afterEach(() => {
    vi.restoreAllMocks()
  })

  it('ควรคืนค่าเริ่มต้นทันที', () => {
    const { result } = renderHook(() => useDebounce('hello', 500))
    expect(result.current).toBe('hello')
  })

  it('ไม่ควรอัปเดตค่าทันทีเมื่อค่าเปลี่ยน', () => {
    const { result, rerender } = renderHook(
      ({ value }) => useDebounce(value, 500),
      { initialProps: { value: 'hello' } }
    )

    rerender({ value: 'world' })

    // ยังไม่ผ่าน 500ms — ค่ายังคงเป็น 'hello'
    expect(result.current).toBe('hello')
  })

  it('ควรอัปเดตค่าหลังผ่านเวลา delay', () => {
    const { result, rerender } = renderHook(
      ({ value }) => useDebounce(value, 500),
      { initialProps: { value: 'hello' } }
    )

    rerender({ value: 'world' })

    // เลื่อนเวลาไปข้างหน้า 500ms
    act(() => {
      vi.advanceTimersByTime(500)
    })

    expect(result.current).toBe('world')
  })

  it('ควร Reset timer เมื่อค่าเปลี่ยนหลายครั้งติดกัน', () => {
    const { result, rerender } = renderHook(
      ({ value }) => useDebounce(value, 500),
      { initialProps: { value: 'a' } }
    )

    // เปลี่ยนค่าหลายครั้งติดกัน
    rerender({ value: 'ab' })
    act(() => { vi.advanceTimersByTime(200) })  // ผ่านไป 200ms

    rerender({ value: 'abc' })
    act(() => { vi.advanceTimersByTime(200) })  // ผ่านไป 400ms รวม

    // ยังไม่ถึง 500ms นับจากครั้งล่าสุด
    expect(result.current).toBe('a')

    act(() => { vi.advanceTimersByTime(300) })  // ผ่านไป 500ms นับจากครั้งล่าสุด

    expect(result.current).toBe('abc')
  })
})
```

**Matchers ที่ใช้บ่อยใน Vitest:**

```typescript
// ค่าเท่ากัน
expect(value).toBe(5)             // === (Strict Equality)
expect(obj).toEqual({ a: 1 })     // Deep Equality (เปรียบเทียบ Object)
expect(value).not.toBe(null)      // .not ใช้กลับเงื่อนไข

// ตัวเลข
expect(value).toBeGreaterThan(5)
expect(value).toBeLessThanOrEqual(10)
expect(value).toBeCloseTo(3.14, 2)  // ทดสอบทศนิยม (2 ตำแหน่ง)

// String
expect(str).toContain('hello')
expect(str).toMatch(/^[A-Z]/)     // Regular Expression

// Array
expect(arr).toHaveLength(3)
expect(arr).toContain('item')
expect(arr).toEqual(expect.arrayContaining(['a', 'b']))

// Object
expect(obj).toHaveProperty('name', 'John')

// Error
expect(() => fn()).toThrow()
expect(() => fn()).toThrow('specific message')

// Async
await expect(promise).resolves.toBe('value')
await expect(promise).rejects.toThrow('error message')

// Mock
expect(mockFn).toHaveBeenCalled()
expect(mockFn).toHaveBeenCalledTimes(2)
expect(mockFn).toHaveBeenCalledWith('arg1', 'arg2')
```

---

### Component Testing ด้วย React Testing Library

**React Testing Library (RTL)** ถูกสร้างด้วยหลักการที่สำคัญมาก:

> *"The more your tests resemble the way your software is used, the more confidence they can give you."*
> — Kent C. Dodds, ผู้สร้าง RTL

หมายความว่า: **ทดสอบจากมุมมองของผู้ใช้ ไม่ใช่จาก Implementation**

```
❌ ผิด — ทดสอบ Implementation:
"เมื่อกดปุ่ม state.isOpen ควรเป็น true"

✅ ถูก — ทดสอบพฤติกรรม:
"เมื่อกดปุ่ม Dropdown ควรแสดงขึ้นมาให้ผู้ใช้เห็น"
```

#### เครื่องมือหลักของ RTL

**`render`** — วาด Component ลงใน DOM เสมือน:
```typescript
const { container } = render(<MovieCard movie={mockMovie} />)
```

**`screen`** — ค้นหา Element จาก DOM เสมือน:
```typescript
// Query Methods — วิธีค้นหา Element
screen.getByText('Inception')           // หาด้วยข้อความ (throw ถ้าไม่พบ)
screen.getByRole('button', { name: /add to favorites/i })  // หาด้วย Role (แนะนำ)
screen.getByLabelText('Email')          // หาจาก Label (ดีสำหรับ Form)
screen.getByPlaceholderText('ค้นหาหนัง')  // หาจาก Placeholder
screen.getByTestId('movie-card')        // หาจาก data-testid (ใช้เป็น Last Resort)

// queryBy — คืน null ถ้าไม่พบ (ไม่ throw)
screen.queryByText('Loading...')

// findBy — Async, รอจนกว่าจะพบ (ใช้กับ Async Operation)
await screen.findByText('Movies loaded')

// AllBy — คืน array
screen.getAllByRole('listitem')
```

**`userEvent`** — จำลองการกระทำของผู้ใช้ (แนะนำแทน `fireEvent`):
```typescript
import userEvent from '@testing-library/user-event'

const user = userEvent.setup()

await user.click(button)
await user.type(input, 'search text')
await user.clear(input)
await user.selectOptions(select, 'option-value')
await user.keyboard('{Enter}')
await user.hover(element)
```

#### ตัวอย่างจริง: ทดสอบ SearchBar Component

```typescript
// src/components/SearchBar.tsx
'use client'
import { useState } from 'react'
import { useDebounce } from '@/hooks/useDebounce'

interface SearchBarProps {
  onSearch: (query: string) => void
  placeholder?: string
}

export function SearchBar({ onSearch, placeholder = 'ค้นหาหนัง...' }: SearchBarProps) {
  const [query, setQuery] = useState('')
  const debouncedQuery = useDebounce(query, 300)

  // เรียก onSearch เมื่อ debouncedQuery เปลี่ยน
  // (ตัดส่วน useEffect ออกเพื่อความกระชับในตัวอย่างนี้)

  return (
    <div role="search">
      <label htmlFor="movie-search" className="sr-only">
        ค้นหาหนัง
      </label>
      <input
        id="movie-search"
        type="search"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder={placeholder}
        aria-label="ค้นหาหนัง"
      />
      {query && (
        <button onClick={() => setQuery('')} aria-label="ล้างการค้นหา">
          ✕
        </button>
      )}
    </div>
  )
}
```

```typescript
// src/components/SearchBar.test.tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { SearchBar } from './SearchBar'

describe('SearchBar', () => {
  it('ควรแสดง Input สำหรับค้นหา', () => {
    render(<SearchBar onSearch={vi.fn()} />)
    // ค้นหาด้วย Role — เหมือนที่ Screen Reader มองเห็น
    expect(screen.getByRole('searchbox')).toBeInTheDocument()
  })

  it('ควรแสดง Placeholder ตามที่กำหนด', () => {
    render(<SearchBar onSearch={vi.fn()} placeholder="ค้นหาด้วยชื่อ" />)
    expect(screen.getByPlaceholderText('ค้นหาด้วยชื่อ')).toBeInTheDocument()
  })

  it('ควรอัปเดตค่าเมื่อผู้ใช้พิมพ์', async () => {
    const user = userEvent.setup()
    render(<SearchBar onSearch={vi.fn()} />)

    const input = screen.getByRole('searchbox')
    await user.type(input, 'Inception')

    expect(input).toHaveValue('Inception')
  })

  it('ปุ่มล้างค้นหาไม่ควรแสดงตอนที่ยังไม่พิมพ์', () => {
    render(<SearchBar onSearch={vi.fn()} />)
    expect(screen.queryByRole('button', { name: /ล้างการค้นหา/ })).not.toBeInTheDocument()
  })

  it('ปุ่มล้างค้นหาควรแสดงเมื่อมีข้อความ และล้างค่าเมื่อกด', async () => {
    const user = userEvent.setup()
    render(<SearchBar onSearch={vi.fn()} />)

    const input = screen.getByRole('searchbox')
    await user.type(input, 'hello')

    // ปุ่มควรปรากฏ
    const clearButton = screen.getByRole('button', { name: /ล้างการค้นหา/ })
    expect(clearButton).toBeInTheDocument()

    // กดแล้ว Input ควรว่าง
    await user.click(clearButton)
    expect(input).toHaveValue('')
  })
})
```

#### ทดสอบ Async Component (Loading/Error/Success States)

สถานการณ์ที่พบบ่อยที่สุดในโลกจริงคือ Component ที่ดึงข้อมูล มี 3 สถานะ: Loading, Error, Success

```typescript
// src/components/MovieList.test.tsx
import { describe, it, expect, vi, beforeAll, afterAll, afterEach } from 'vitest'
import { render, screen, waitFor } from '@testing-library/react'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { server } from '@/test/mocks/server'  // MSW Server (อธิบายในหัวข้อถัดไป)
import { http, HttpResponse } from 'msw'
import { MovieList } from './MovieList'

// Helper: สร้าง QueryClient ใหม่สำหรับแต่ละ Test (ป้องกัน Cache ปนกัน)
function createWrapper() {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        retry: false,  // ไม่ Retry ใน Test เพื่อความเร็ว
      },
    },
  })
  return ({ children }: { children: React.ReactNode }) => (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  )
}

describe('MovieList', () => {
  it('ควรแสดง Loading Spinner ระหว่างโหลดข้อมูล', () => {
    render(<MovieList />, { wrapper: createWrapper() })
    expect(screen.getByRole('status', { name: /กำลังโหลด/i })).toBeInTheDocument()
  })

  it('ควรแสดงรายการหนังเมื่อโหลดสำเร็จ', async () => {
    render(<MovieList />, { wrapper: createWrapper() })

    // รอจนกว่าข้อมูลจะโหลดเสร็จ (MSW จะ Mock Response ให้)
    await screen.findByText('Inception')
    expect(screen.getByText('The Dark Knight')).toBeInTheDocument()
  })

  it('ควรแสดงข้อความ Error เมื่อ API ล้มเหลว', async () => {
    // Override MSW handler ให้ return Error สำหรับ Test นี้โดยเฉพาะ
    server.use(
      http.get('/api/movies', () => {
        return HttpResponse.json({ error: 'Server error' }, { status: 500 })
      })
    )

    render(<MovieList />, { wrapper: createWrapper() })

    await screen.findByText(/ไม่สามารถโหลดข้อมูลได้/i)
    expect(screen.queryByRole('list')).not.toBeInTheDocument()
  })

  it('ควรแสดง Empty State เมื่อไม่มีหนัง', async () => {
    server.use(
      http.get('/api/movies', () => {
        return HttpResponse.json([])
      })
    )

    render(<MovieList />, { wrapper: createWrapper() })

    await screen.findByText(/ไม่พบหนัง/i)
  })
})
```

#### ทดสอบ Form Submission

```typescript
// src/components/LoginForm.test.tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { LoginForm } from './LoginForm'

describe('LoginForm', () => {
  it('ควรแสดง Error เมื่อ Submit โดยไม่กรอก Email', async () => {
    const user = userEvent.setup()
    render(<LoginForm onSubmit={vi.fn()} />)

    // กด Submit โดยไม่กรอกข้อมูล
    await user.click(screen.getByRole('button', { name: /เข้าสู่ระบบ/i }))

    expect(await screen.findByText(/กรุณากรอก Email/i)).toBeInTheDocument()
  })

  it('ควรแสดง Error เมื่อรูปแบบ Email ไม่ถูกต้อง', async () => {
    const user = userEvent.setup()
    render(<LoginForm onSubmit={vi.fn()} />)

    await user.type(screen.getByLabelText(/email/i), 'notanemail')
    await user.click(screen.getByRole('button', { name: /เข้าสู่ระบบ/i }))

    expect(await screen.findByText(/รูปแบบ Email ไม่ถูกต้อง/i)).toBeInTheDocument()
  })

  it('ควรเรียก onSubmit พร้อมข้อมูลที่ถูกต้องเมื่อ Form ถูกต้อง', async () => {
    const user = userEvent.setup()
    const mockSubmit = vi.fn()
    render(<LoginForm onSubmit={mockSubmit} />)

    await user.type(screen.getByLabelText(/email/i), 'test@example.com')
    await user.type(screen.getByLabelText(/รหัสผ่าน/i), 'password123')
    await user.click(screen.getByRole('button', { name: /เข้าสู่ระบบ/i }))

    expect(mockSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123',
    })
  })

  it('ปุ่ม Submit ควรถูก Disable ระหว่าง Loading', async () => {
    const user = userEvent.setup()
    // Mock onSubmit ให้ใช้เวลา (จำลอง Async)
    const mockSubmit = vi.fn(() => new Promise(resolve => setTimeout(resolve, 1000)))
    render(<LoginForm onSubmit={mockSubmit} />)

    await user.type(screen.getByLabelText(/email/i), 'test@example.com')
    await user.type(screen.getByLabelText(/รหัสผ่าน/i), 'password123')
    await user.click(screen.getByRole('button', { name: /เข้าสู่ระบบ/i }))

    // ทันทีหลัง Submit — ปุ่มควรถูก Disable
    expect(screen.getByRole('button', { name: /กำลังเข้าสู่ระบบ/i })).toBeDisabled()
  })
})
```

---

### Mock API ด้วย MSW (Mock Service Worker)

**MSW (Mock Service Worker)** คือเครื่องมือ Mock Network Request ที่ดีที่สุดในปี 2026 ทำงานโดยสกัดกั้น HTTP Request จริง ๆ (ไม่ใช่แค่ Mock Function) ทำให้ Test ใกล้เคียงความเป็นจริงมากที่สุด

**ข้อดีของ MSW เทียบกับวิธีอื่น:**
```
❌ Mock fetch() โดยตรง:
   - Test ผูกกับ Implementation (ถ้าเปลี่ยนจาก fetch เป็น axios ต้องเขียน Mock ใหม่)
   - ไม่ได้ทดสอบ Network Layer จริง

✅ MSW:
   - สกัดกั้น Request ระดับ Network
   - Test เหมือนกันทั้งใน Unit Test, Component Test และ Browser จริง
   - Handler เขียนครั้งเดียว ใช้ได้ทุกที่
```

#### ตั้งค่า MSW

```typescript
// src/test/mocks/handlers.ts
import { http, HttpResponse } from 'msw'

// กำหนด Mock Data
const mockMovies = [
  {
    id: 1,
    title: 'Inception',
    rating: 8.8,
    poster: '/inception.jpg',
    year: 2010,
    genre: ['Action', 'Sci-Fi'],
  },
  {
    id: 2,
    title: 'The Dark Knight',
    rating: 9.0,
    poster: '/dark-knight.jpg',
    year: 2008,
    genre: ['Action', 'Crime'],
  },
]

// กำหนด Handlers — สิ่งที่ MSW จะ Intercept
export const handlers = [
  // GET /api/movies — ดึงรายการหนัง
  http.get('/api/movies', ({ request }) => {
    const url = new URL(request.url)
    const query = url.searchParams.get('q')

    if (query) {
      const filtered = mockMovies.filter(m =>
        m.title.toLowerCase().includes(query.toLowerCase())
      )
      return HttpResponse.json(filtered)
    }

    return HttpResponse.json(mockMovies)
  }),

  // GET /api/movies/:id — ดึงหนังตาม ID
  http.get('/api/movies/:id', ({ params }) => {
    const movie = mockMovies.find(m => m.id === Number(params.id))

    if (!movie) {
      return HttpResponse.json({ error: 'Movie not found' }, { status: 404 })
    }

    return HttpResponse.json(movie)
  }),

  // POST /api/favorites — เพิ่ม Favorite
  http.post('/api/favorites', async ({ request }) => {
    const body = await request.json() as { movieId: number }
    return HttpResponse.json(
      { id: Date.now(), movieId: body.movieId, userId: 'user-1' },
      { status: 201 }
    )
  }),

  // DELETE /api/favorites/:id — ลบ Favorite
  http.delete('/api/favorites/:id', () => {
    return HttpResponse.json({ success: true })
  }),
]
```

```typescript
// src/test/mocks/server.ts
import { setupServer } from 'msw/node'
import { handlers } from './handlers'

// สร้าง Mock Server สำหรับ Node.js Environment (Vitest รันใน Node)
export const server = setupServer(...handlers)
```

```typescript
// src/test/setup.ts (อัปเดต)
import '@testing-library/jest-dom'
import { server } from './mocks/server'
import { beforeAll, afterAll, afterEach } from 'vitest'

// เริ่ม Mock Server ก่อนทุก Test Suite
beforeAll(() => server.listen({ onUnhandledRequest: 'error' }))

// Reset Handlers หลังแต่ละ Test (ป้องกัน Override Leak)
afterEach(() => server.resetHandlers())

// หยุด Mock Server หลังทุก Test Suite
afterAll(() => server.close())
```

---

### End-to-End Testing ด้วย Playwright

**E2E Test (End-to-End Test)** คือการทดสอบที่จำลองผู้ใช้จริง เปิด Browser จริง นำทางไปยังหน้าต่าง ๆ กรอกฟอร์ม และตรวจสอบผลลัพธ์

**Playwright** (เพลย์ไรท์) คือเครื่องมือ E2E Testing จาก Microsoft ที่รองรับทุก Browser (Chromium, Firefox, WebKit) และมี API ที่ใช้งานง่ายที่สุดในปัจจุบัน

#### ตั้งค่า Playwright

```bash
# ติดตั้ง Playwright และ Browser สำหรับ Testing
pnpm add -D @playwright/test
pnpm exec playwright install
```

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',              // โฟลเดอร์เก็บ E2E Test
  fullyParallel: true,           // รัน Test พร้อมกันได้
  retries: process.env.CI ? 2 : 0,  // Retry 2 ครั้งบน CI เพื่อลด Flaky Test
  reporter: 'html',              // รายงานผลเป็น HTML

  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',     // บันทึก Trace เมื่อ Test Fail
    screenshot: 'only-on-failure',  // Screenshot เมื่อ Fail
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 13'] },
    },
  ],

  // เริ่ม Dev Server อัตโนมัติก่อนรัน E2E Test
  webServer: {
    command: 'pnpm dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
})
```

#### เขียน E2E Test จริง

```typescript
// e2e/movie-search.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Movie Search', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/')
  })

  test('ผู้ใช้สามารถค้นหาหนังและเห็นผลลัพธ์ได้', async ({ page }) => {
    // หา Search Input
    const searchInput = page.getByRole('searchbox', { name: /ค้นหาหนัง/i })
    await expect(searchInput).toBeVisible()

    // พิมพ์คำค้นหา
    await searchInput.fill('Inception')

    // รอผลลัพธ์แสดง
    await expect(page.getByText('Inception')).toBeVisible({ timeout: 5000 })

    // ตรวจสอบว่า URL อัปเดต
    await expect(page).toHaveURL(/q=Inception/)
  })

  test('แสดง Empty State เมื่อไม่พบหนัง', async ({ page }) => {
    const searchInput = page.getByRole('searchbox', { name: /ค้นหาหนัง/i })
    await searchInput.fill('xyzxyzxyznotamovie12345')

    await expect(page.getByText(/ไม่พบหนัง/i)).toBeVisible({ timeout: 5000 })
  })
})
```

```typescript
// e2e/auth-flow.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Authentication Flow', () => {
  test('ผู้ใช้ที่ไม่ Login ถูก Redirect ไปหน้า Login เมื่อเข้าหน้า Favorites', async ({ page }) => {
    await page.goto('/favorites')

    // ควรถูก Redirect ไปหน้า Login
    await expect(page).toHaveURL(/\/login/)
    await expect(page.getByText(/เข้าสู่ระบบ/i)).toBeVisible()
  })

  test('แสดงหน้า Login ครบถ้วน', async ({ page }) => {
    await page.goto('/login')

    await expect(page.getByRole('heading', { name: /เข้าสู่ระบบ/i })).toBeVisible()
    await expect(page.getByRole('button', { name: /เข้าสู่ระบบด้วย Google/i })).toBeVisible()
  })
})
```

```typescript
// e2e/favorites.spec.ts — ทดสอบ Flow ที่ต้อง Login
import { test, expect } from '@playwright/test'

// ใช้ Storage State เพื่อ Mock การ Login (ไม่ต้อง Login จริงทุกครั้ง)
test.use({ storageState: 'e2e/auth/user.json' })

test.describe('Favorites (Authenticated)', () => {
  test('ผู้ใช้สามารถเพิ่มหนังใน Favorites ได้', async ({ page }) => {
    await page.goto('/')

    // หา Movie Card แรก
    const firstMovie = page.locator('[data-testid="movie-card"]').first()
    const movieTitle = await firstMovie.locator('h3').textContent()

    // กดปุ่ม Add to Favorites
    await firstMovie.getByRole('button', { name: /เพิ่มใน Favorite/i }).click()

    // ตรวจสอบว่าปุ่มเปลี่ยนสถานะ
    await expect(
      firstMovie.getByRole('button', { name: /บันทึกแล้ว/i })
    ).toBeVisible()

    // ไปหน้า Favorites และตรวจสอบว่าหนังอยู่ที่นั่น
    await page.goto('/favorites')
    await expect(page.getByText(movieTitle!)).toBeVisible()
  })

  test('ผู้ใช้สามารถลบหนังออกจาก Favorites ได้', async ({ page }) => {
    await page.goto('/favorites')

    const firstFavorite = page.locator('[data-testid="favorite-item"]').first()
    const movieTitle = await firstFavorite.locator('h3').textContent()

    await firstFavorite.getByRole('button', { name: /ลบออก/i }).click()

    // Confirm Dialog
    await page.getByRole('button', { name: /ยืนยัน/i }).click()

    // หนังควรหายไป
    await expect(page.getByText(movieTitle!)).not.toBeVisible()
  })
})
```

#### Visual Regression Testing

Visual Regression Test คือการถ่าย Screenshot แล้วเปรียบเทียบกับ Baseline (ภาพต้นฉบับ) ป้องกัน UI เปลี่ยนโดยไม่ตั้งใจ

```typescript
// e2e/visual.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Visual Regression', () => {
  test('หน้า Home ควรดูเหมือนเดิม', async ({ page }) => {
    await page.goto('/')

    // รอให้ Image โหลดเสร็จ
    await page.waitForLoadState('networkidle')

    // เปรียบเทียบกับ Snapshot ที่บันทึกไว้
    await expect(page).toHaveScreenshot('homepage.png', {
      maxDiffPixels: 100,  // ยอมรับความแตกต่างได้ 100 pixel
    })
  })

  test('Movie Card ควรดูเหมือนเดิม', async ({ page }) => {
    await page.goto('/')
    const firstCard = page.locator('[data-testid="movie-card"]').first()
    await expect(firstCard).toHaveScreenshot('movie-card.png')
  })
})
```

**คำสั่งที่ใช้บ่อย:**
```bash
# รัน E2E Test ทั้งหมด
pnpm test:e2e

# รันเฉพาะไฟล์ที่ต้องการ
pnpm exec playwright test e2e/movie-search.spec.ts

# รันใน UI Mode (ดู Browser ขณะทดสอบ)
pnpm exec playwright test --ui

# รันแบบ Debug (หยุดที่แต่ละ Step)
pnpm exec playwright test --debug

# อัปเดต Visual Snapshots ที่ Baseline เปลี่ยน
pnpm exec playwright test --update-snapshots

# ดู Report ผลการทดสอบ
pnpm exec playwright show-report
```

---

## 6.3 CI/CD และ Deployment

### CI/CD คืออะไร?

**CI (Continuous Integration)** หมายถึงการรัน Test และตรวจสอบโค้ดอัตโนมัติทุกครั้งที่มีการ Push โค้ดขึ้น Repository ป้องกันโค้ดที่พังเข้าไปอยู่ใน Main Branch

**CD (Continuous Deployment)** หมายถึงการ Deploy โค้ดไปยัง Production อัตโนมัติเมื่อผ่าน CI แล้ว

```
วงจร CI/CD ในโลกจริง:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Developer                GitHub              GitHub Actions            Vercel
     │                      │                      │                     │
     │  git push             │                      │                     │
     │─────────────────────►│                      │                     │
     │                      │  trigger workflow    │                     │
     │                      │─────────────────────►│                     │
     │                      │                      │  pnpm install       │
     │                      │                      │  pnpm lint          │
     │                      │                      │  pnpm type-check    │
     │                      │                      │  pnpm test:run      │
     │                      │                      │  pnpm build         │
     │                      │  ✅ All checks pass  │                     │
     │                      │◄─────────────────────│                     │
     │                      │                      │                     │
     │                      │  deploy trigger      │                     │
     │                      │─────────────────────────────────────────►  │
     │                      │                      │   Preview URL       │
     │◄─────────────────────────────────────────────────────────────────│
     │                      │                      │                     │

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### ตั้งค่า GitHub Actions

**GitHub Actions** (กิตฮับ แอคชันส์) คือระบบ CI/CD ที่ฝังอยู่ใน GitHub โดยตรง ไม่ต้องใช้บริการภายนอก

โครงสร้างไฟล์:
```
.github/
└── workflows/
    ├── ci.yml          ← รันเมื่อ Push/PR ทุกครั้ง
    └── deploy.yml      ← รันเมื่อ Merge เข้า main เท่านั้น
```

**`.github/workflows/ci.yml` — CI Pipeline หลัก:**

```yaml
# .github/workflows/ci.yml
name: CI

# กำหนดว่าจะรัน Workflow นี้เมื่อไหร่
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  # ── Job 1: Lint และ Type Check ─────────────────────────────
  lint-and-typecheck:
    name: Lint & Type Check
    runs-on: ubuntu-latest

    steps:
      # Step 1: Checkout โค้ดจาก Repository
      - name: Checkout code
        uses: actions/checkout@v4

      # Step 2: ติดตั้ง Node.js
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '22'

      # Step 3: ติดตั้ง pnpm
      - name: Setup pnpm
        uses: pnpm/action-setup@v3
        with:
          version: 9

      # Step 4: Cache node_modules เพื่อให้รันครั้งต่อไปเร็วขึ้น
      - name: Cache dependencies
        uses: actions/cache@v4
        with:
          path: ~/.pnpm-store
          key: ${{ runner.os }}-pnpm-${{ hashFiles('**/pnpm-lock.yaml') }}
          restore-keys: |
            ${{ runner.os }}-pnpm-

      # Step 5: ติดตั้ง Dependencies
      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      # Step 6: รัน ESLint
      - name: Run ESLint
        run: pnpm lint

      # Step 7: ตรวจสอบ TypeScript Type
      - name: TypeScript type check
        run: pnpm type-check

  # ── Job 2: Unit และ Component Test ─────────────────────────
  test:
    name: Unit & Component Tests
    runs-on: ubuntu-latest
    needs: lint-and-typecheck  # รันหลังจาก lint ผ่านแล้ว

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
      - uses: pnpm/action-setup@v3
        with:
          version: 9
      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Run tests with coverage
        run: pnpm test:coverage

      # อัปโหลด Coverage Report (ดูได้ใน GitHub Actions Tab)
      - name: Upload coverage report
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: coverage-report
          path: coverage/

  # ── Job 3: Build ────────────────────────────────────────────
  build:
    name: Build
    runs-on: ubuntu-latest
    needs: test  # รันหลังจาก test ผ่านแล้ว

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
      - uses: pnpm/action-setup@v3
        with:
          version: 9
      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      # ใช้ Secrets ที่ตั้งค่าใน GitHub Repository Settings
      - name: Build application
        run: pnpm build
        env:
          NEXT_PUBLIC_SUPABASE_URL: ${{ secrets.NEXT_PUBLIC_SUPABASE_URL }}
          NEXT_PUBLIC_SUPABASE_ANON_KEY: ${{ secrets.NEXT_PUBLIC_SUPABASE_ANON_KEY }}

  # ── Job 4: E2E Test (เฉพาะ Pull Request) ────────────────────
  e2e:
    name: E2E Tests
    runs-on: ubuntu-latest
    needs: build
    if: github.event_name == 'pull_request'  # รันเฉพาะ PR

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
      - uses: pnpm/action-setup@v3
        with:
          version: 9
      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      # ติดตั้ง Playwright Browsers
      - name: Install Playwright browsers
        run: pnpm exec playwright install --with-deps

      - name: Run E2E tests
        run: pnpm test:e2e
        env:
          NEXT_PUBLIC_SUPABASE_URL: ${{ secrets.NEXT_PUBLIC_SUPABASE_URL }}
          NEXT_PUBLIC_SUPABASE_ANON_KEY: ${{ secrets.NEXT_PUBLIC_SUPABASE_ANON_KEY }}

      # อัปโหลด Playwright Report เมื่อ Fail
      - name: Upload Playwright report
        uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

**ผลลัพธ์ที่เห็นใน GitHub:**

```
Pull Request #42: Add search feature
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Checks

✅ CI / Lint & Type Check (1m 23s)
✅ CI / Unit & Component Tests (2m 05s)
✅ CI / Build (1m 45s)
✅ CI / E2E Tests (3m 12s)
✅ Vercel — Preview ready (view deployment)

All checks have passed
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### Deploy บน Vercel

**Vercel** (เวอร์เซล) คือ Hosting Platform ที่ออกแบบมาเพื่อ Next.js โดยเฉพาะ (Vercel สร้าง Next.js เอง) มีความสามารถพิเศษที่ Platform อื่นทำไม่ได้เท่า

#### การเชื่อมต่อ GitHub กับ Vercel

1. ไปที่ [vercel.com](https://vercel.com) และ Sign Up ด้วย GitHub
2. คลิก **"Add New Project"**
3. เลือก Repository จาก GitHub
4. ตั้งค่า Build Settings (Vercel ตรวจ Next.js ได้อัตโนมัติ)
5. เพิ่ม Environment Variables
6. คลิก **"Deploy"**

หลังจากนี้ทุกครั้งที่ Push ขึ้น GitHub, Vercel จะ Deploy ให้อัตโนมัติ

#### Preview Deployment

คุณสมบัติที่ทรงพลังที่สุดของ Vercel:

```
Push ขึ้น Branch ใดก็ตาม
         │
         ▼
Vercel สร้าง Preview URL อัตโนมัติ
เช่น: https://movie-finder-git-feature-search-yourteam.vercel.app
         │
         ▼
แชร์ URL ให้ Designer หรือ Stakeholder ทดสอบ
ก่อน Merge เข้า main เสมอ
```

ใน Pull Request จะเห็น Comment จาก Vercel:
```
🔍 Vercel Preview URL
Preview: https://movie-finder-git-feature-login-abc123.vercel.app
Deployed to: Preview
Inspect: https://vercel.com/team/movie-finder/deployments/abc123
```

#### ตั้งค่า Environment Variables ใน Vercel

Environment Variables มี 3 ระดับ:

```
Production  → ใช้เมื่อ Deploy จาก main branch
Preview     → ใช้กับทุก Branch ที่ไม่ใช่ main
Development → ใช้เมื่อรัน vercel dev บนเครื่องท้องถิ่น
```

```
Vercel Dashboard > Project Settings > Environment Variables:

Key                              Value                    Environment
─────────────────────────────────────────────────────────────────────
NEXT_PUBLIC_SUPABASE_URL        https://xxx.supabase.co  Production, Preview, Development
NEXT_PUBLIC_SUPABASE_ANON_KEY   eyJhbGci...              Production, Preview, Development
SUPABASE_SERVICE_ROLE_KEY       eyJhbGci...              Production only  ← ระวัง!
AUTH_SECRET                     random-32-char-string    Production, Preview
AUTH_GOOGLE_ID                  123456789.apps.google..  Production, Preview
AUTH_GOOGLE_SECRET              GOCSPX-...               Production, Preview
```

> **⚠️ ข้อควรระวัง:** ตัวแปรที่ขึ้นต้นด้วย `NEXT_PUBLIC_` จะถูกส่งไปยัง Browser ได้ ดังนั้นห้ามใส่ข้อมูลลับ (เช่น Service Role Key) ใน `NEXT_PUBLIC_` โดยเด็ดขาด

#### `vercel.json` — ตั้งค่า Advanced

```json
// vercel.json
{
  "buildCommand": "pnpm build",
  "installCommand": "pnpm install",
  "framework": "nextjs",

  // Redirect เช่น www → non-www
  "redirects": [
    {
      "source": "https://www.moviefinder.app/:path*",
      "destination": "https://moviefinder.app/:path*",
      "permanent": true
    }
  ],

  // Headers สำหรับ Security
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        }
      ]
    }
  ]
}
```

---

## 6.4 Git Workflow ในทีมจริง

### GitHub Flow — มาตรฐานที่ใช้กันทั่วโลก

**GitHub Flow** คือ Workflow ที่เรียบง่ายแต่ทรงพลัง เหมาะสำหรับทีม Front-End ที่ Deploy บ่อย

```
main branch (Production)
     │
     │  สร้าง Branch ใหม่
     ├─────────────────► feature/user-authentication
     │                         │
     │                         │  ทำงานใน Feature Branch
     │                         │  commit, commit, commit
     │                         │
     │                   เปิด Pull Request
     │◄─────────────────────────
     │    Code Review
     │    CI ผ่าน
     │    Preview Deploy ✅
     │
     │  Squash and Merge
     │──────────────────►  main (Deploy อัตโนมัติ)
```

**กฎที่ต้องรู้:**
- **ห้าม Push ตรงไปยัง `main`** เด็ดขาด
- ทุก Feature ต้องผ่าน Pull Request เสมอ
- Branch อายุสั้น ทำงานเสร็จ Merge แล้วลบทิ้ง

---

### Branch Naming Convention

ชื่อ Branch ที่ดีต้องบอกได้ทันทีว่าทำอะไร:

```
รูปแบบ: <type>/<short-description>

ประเภทที่ใช้บ่อย:
  feature/  — Feature ใหม่
  fix/      — แก้ Bug
  chore/    — งานซ่อมบำรุง เช่น อัปเดต Dependency
  docs/     — เอกสาร
  refactor/ — ปรับโครงสร้างโค้ดโดยไม่เปลี่ยน Behavior
  test/     — เพิ่ม Test

ตัวอย่างที่ดี:
  feature/user-authentication
  feature/movie-search-debounce
  fix/favorites-not-saving
  fix/login-redirect-loop
  chore/upgrade-next-15-1
  docs/update-api-readme
  refactor/movie-card-component

ตัวอย่างที่ไม่ดี:
  my-branch          ← ไม่บอกว่าทำอะไร
  fix                ← กว้างเกินไป
  johns-feature      ← ใช้ชื่อคนไม่เหมาะสม
  temp               ← ไม่มีความหมาย
```

**คำสั่ง Git ที่ใช้บ่อย:**

```bash
# สร้าง Branch ใหม่และสลับไปทันที
git checkout -b feature/movie-search

# หรือใน Git เวอร์ชันใหม่
git switch -c feature/movie-search

# ดู Branch ทั้งหมด
git branch -a

# สลับ Branch
git switch main

# ดึง Branch ใหม่จาก Remote
git fetch origin
git switch feature/shared-team-branch

# ลบ Branch หลัง Merge แล้ว
git branch -d feature/movie-search
git push origin --delete feature/movie-search  # ลบบน Remote ด้วย
```

---

### Conventional Commits

**Conventional Commits** คือมาตรฐานการเขียน Commit Message ที่ทีมมืออาชีพใช้ ทำให้:
- ดู History แล้วเข้าใจทันทีว่าแต่ละ Commit ทำอะไร
- สร้าง CHANGELOG อัตโนมัติได้
- Trigger CI/CD บางอย่างตาม Type ได้

**รูปแบบ:**
```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types ที่ใช้บ่อย:**

| Type | ใช้เมื่อ | ตัวอย่าง |
|------|---------|---------|
| `feat` | เพิ่ม Feature ใหม่ | `feat(auth): add Google OAuth login` |
| `fix` | แก้ Bug | `fix(search): debounce not working on mobile` |
| `docs` | แก้ไขเอกสาร | `docs(readme): add deployment instructions` |
| `style` | แก้ Format/Whitespace (ไม่เปลี่ยน Logic) | `style: fix indentation in MovieCard` |
| `refactor` | ปรับโครงสร้างโค้ด ไม่เพิ่ม Feature ไม่แก้ Bug | `refactor(api): extract fetch helper function` |
| `test` | เพิ่ม/แก้ Test | `test(favorites): add unit tests for filtering` |
| `chore` | งานบำรุงรักษา | `chore: upgrade to Next.js 15.2` |
| `perf` | ปรับปรุง Performance | `perf(images): lazy load movie posters` |
| `ci` | แก้ไข CI Configuration | `ci: add E2E test step to pipeline` |

**ตัวอย่าง Commit Messages ที่ดี:**

```
feat(favorites): implement add/remove favorites with Supabase

- Add FavoriteButton component with optimistic updates
- Create /api/favorites endpoint for CRUD operations
- Add useToggleFavorite custom hook
- Connect favorites to user session via Auth.js

Closes #42

---

fix(auth): redirect loop when session expires

Sessions that expire during navigation caused an infinite
redirect between /login and protected routes. Fixed by
checking session validity before triggering redirect.

Fixes #67

---

refactor(movie-card): extract rating display to RatingBadge

The rating display logic was duplicated in MovieCard and
MovieDetail. Extracted to a shared RatingBadge component.

---

chore: upgrade dependencies to latest minor versions

- next: 15.0.3 → 15.1.0
- @tanstack/react-query: 5.59 → 5.62
- typescript: 5.6 → 5.7
```

**ตั้งค่า Commitlint เพื่อบังคับ Convention:**

```bash
# ติดตั้ง Commitlint
pnpm add -D @commitlint/config-conventional @commitlint/cli husky
```

```javascript
// commitlint.config.js
export default {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'docs', 'style', 'refactor',
      'test', 'chore', 'perf', 'ci', 'revert'
    ]],
    'subject-max-length': [2, 'always', 72],
  },
}
```

```bash
# ตั้งค่า Husky ให้รัน Commitlint ก่อน Commit
pnpm exec husky init
echo "pnpm exec commitlint --edit \$1" > .husky/commit-msg
```

---

### Code Review อย่างมืออาชีพ

Code Review ไม่ใช่แค่การหา Bug — มันคือกระบวนการ **แบ่งปันความรู้, รักษามาตรฐาน, และป้องกัน Technical Debt**

#### การเปิด Pull Request ที่ดี

```markdown
## PR #45: Add movie search with debounce

### 📋 สิ่งที่เปลี่ยนแปลง
- เพิ่ม SearchBar component พร้อม Debounce 300ms
- เพิ่ม useMovieSearch hook สำหรับดึงผลการค้นหา
- อัปเดต URL เมื่อมีการค้นหา (Shareable URL)

### 🎯 Story ที่เกี่ยวข้อง
Closes FEAT-38

### 🧪 วิธีทดสอบ
1. ไปที่หน้าหลัก
2. พิมพ์ในช่องค้นหา
3. ควรเห็นผลลัพธ์หลัง 300ms
4. URL ควรเปลี่ยนเป็น /?q=ค้นหา

### 📸 Screenshots
[แนบ Screenshot ก่อน/หลัง]

### ⚠️ สิ่งที่ต้องระวัง
- SearchBar ใช้ `data-testid="search-input"` สำหรับ E2E Test
```

#### การให้ Feedback ใน Code Review

**หลักการ KISS (Keep It Simple and Specific):**

```
❌ Comment ที่ไม่ดี:
"โค้ดนี้ไม่ดี"
"แก้ด้วย"

✅ Comment ที่ดี:
"Function นี้ทำหลายอย่างเกินไป (SRP violation) แนะนำให้แยก
 การ fetch data ออกไปเป็น hook ต่างหาก เช่น:
 const { data } = useMovieSearch(query) แล้วค่อยส่งผ่าน props
 เพื่อให้ SearchResults reusable มากขึ้น"

"Typo: 'recieve' ควรเป็น 'receive'"

"พิจารณาเพิ่ม empty check ตรงนี้:
 if (!movies.length) return <EmptyState />
 เพราะ map() บน array ว่างจะไม่แสดงอะไร อาจทำให้ผู้ใช้งง"
```

**ประเภท Comment ที่ควรใช้:**

```
nit: ... — เรื่องเล็กน้อย ผู้รีวิวอาจจะทำหรือไม่ก็ได้
           เช่น "nit: ใช้ const แทน let ได้เพราะไม่มีการ reassign"

suggestion: ... — ข้อแนะนำ แต่ไม่บังคับ
           เช่น "suggestion: อาจลองใช้ Array.from() แทน spread operator ตรงนี้"

question: ... — ถามเพื่อทำความเข้าใจ ไม่ใช่การวิจารณ์
           เช่น "question: ทำไมถึงเลือกใช้ useCallback ตรงนี้? มี Re-render issue ไหม?"

must: ... — ต้องแก้ก่อน Merge เพราะกระทบ Correctness/Security
           เช่น "must: ไม่ควร console.log ใน Production code กรุณาลบออก"
```

#### การรับ Feedback

```
หลักการรับ Code Review:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Comment กำลัง Review โค้ด ไม่ใช่ Review ตัวคุณ
✅ ขอบคุณสำหรับ Feedback ที่มีประโยชน์
✅ ถ้าไม่เข้าใจ ถามก่อนแก้ทันที
✅ ถ้าไม่เห็นด้วย อธิบายเหตุผลอย่างสุภาพ
✅ เมื่อแก้แล้ว Resolve Comment และ Reply ว่าแก้อย่างไร
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### Merge Conflict — วิธีเกิด วิธีแก้ วิธีป้องกัน

**Merge Conflict** เกิดขึ้นเมื่อสองคนแก้ไขโค้ดส่วนเดียวกัน แล้ว Git ไม่รู้ว่าจะเลือกเวอร์ชันไหน

```
ตัวอย่างการเกิด Conflict:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
main:         const LIMIT = 10
สมชาย:       const LIMIT = 20    (แก้ใน feature/pagination)
สมหญิง:      const LIMIT = 5     (แก้ใน feature/mobile-optimization)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
เมื่อ Merge ทั้งสอง Branch → Git ไม่รู้จะใช้ 20 หรือ 5
```

**หน้าตาของ Conflict ในไฟล์:**

```typescript
<<<<<<< HEAD (main)
const LIMIT = 20
=======
const LIMIT = 5
>>>>>>> feature/mobile-optimization
```

**วิธีแก้ Conflict:**

```bash
# 1. ดึง main ล่าสุดก่อนเสมอ
git fetch origin
git rebase origin/main  # แนะนำกว่า merge เพราะ History สะอาดกว่า

# 2. Git จะหยุดที่ Conflict — แก้ไขในไฟล์
# 3. เมื่อแก้เสร็จ:
git add src/config.ts
git rebase --continue

# หรือถ้าใช้ merge:
git add src/config.ts
git commit
```

**การแก้ใน VS Code:**

VS Code มี UI ช่วยแก้ Conflict โดยไม่ต้องแก้ manual:
- "Accept Current Change" — เลือกของ main
- "Accept Incoming Change" — เลือกของ Branch ตัวเอง
- "Accept Both Changes" — เอาทั้งคู่มาเรียงกัน
- แก้เองตาม Logic ที่ถูกต้อง

**การป้องกัน Conflict:**

```
1. Sync กับ main บ่อย ๆ
   git fetch origin && git rebase origin/main
   (ทำทุกเช้าก่อนเริ่มงาน)

2. Branch อายุสั้น — ทำงานให้เสร็จใน 1-3 วัน

3. แบ่งงานให้ชัดเจน ป้องกันการทำไฟล์เดียวกันพร้อมกัน

4. Communicate ในทีมเมื่อจะแก้ไขไฟล์สำคัญ (เช่น schema, config)
```

---

## 6.5 Debugging และ Error Handling

### Browser DevTools อย่างจริงจัง

DevTools ไม่ใช่แค่ Inspect Element — มันคืออาวุธหลักในการ Debug

#### Console Tab

```javascript
// วิธีใช้ console อย่างมืออาชีพ

// console.log ธรรมดา — ใช้บ่อยที่สุด
console.log('user:', user)

// console.table — แสดง Array/Object สวยงาม
console.table(movies)  // แสดงเป็นตารางพร้อม Column

// console.group — จัดกลุ่ม Log ที่เกี่ยวข้อง
console.group('Favorites Action')
  console.log('Before:', favorites)
  console.log('Adding:', movieId)
  console.log('After:', [...favorites, movieId])
console.groupEnd()

// console.time — วัดเวลาการทำงาน
console.time('fetchMovies')
const movies = await fetchMovies()
console.timeEnd('fetchMovies')  // แสดง: fetchMovies: 234.5ms

// console.error / console.warn
console.error('API Error:', error)  // แสดงเป็นสีแดง
console.warn('Deprecated:', 'ใช้ newFunction แทน')  // สีส้ม
```

#### Sources Tab — JavaScript Debugger

แทนที่จะใส่ `console.log` ทุกที่ ให้ใช้ **Breakpoint** แทน:

```
วิธีตั้ง Breakpoint:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. เปิด DevTools > Sources Tab
2. กด Ctrl+P / Cmd+P เพื่อค้นหาไฟล์
3. คลิกที่เลขบรรทัดเพื่อวาง Breakpoint (จุดสีน้ำเงิน)
4. รีเฟรชหน้าหรือทำ Action ที่ต้องการ Debug
5. โปรแกรมจะหยุดที่ Breakpoint
6. ใช้:
   F8 / ▶ — Continue (ไปต่อ)
   F10 / ↗ — Step Over (ข้ามไปบรรทัดถัดไป)
   F11 / ↘ — Step Into (เข้าไปใน Function)
   Shift+F11 / ↙ — Step Out (ออกจาก Function)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Conditional Breakpoint** — หยุดเฉพาะเมื่อ Condition เป็นจริง:
```
คลิกขวาที่ Breakpoint → "Edit breakpoint"
พิมพ์: movie.rating > 9
→ หยุดเฉพาะเมื่อ rating > 9 (มีประโยชน์มากเมื่อ loop data จำนวนมาก)
```

#### Network Tab

Network Tab ช่วย Debug API Request ได้ดีที่สุด:

```
สิ่งที่ดูใน Network Tab:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Request URL   — API ที่เรียก ถูกต้องไหม?
Method        — GET/POST/PUT/DELETE ถูกไหม?
Status Code   — 200, 400, 401, 404, 500?
Request Headers — Authorization Token ถูกส่งไปไหม?
Request Body  — ส่ง Payload ถูกต้องไหม?
Response Body — Server ตอบกลับอะไร?
Timing        — ใช้เวลานานแค่ไหน?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Tip ที่มีประโยชน์:**
```
กด XHR/Fetch filter — ดูเฉพาะ API Request
เลือก Request แล้วกด Copy > "Copy as fetch" 
→ ได้โค้ด fetch() ที่ทดสอบใน Console ได้ทันที
```

#### Performance Tab

ใช้เมื่อแอปทำงานช้าและต้องหาต้นเหตุ:

```
วิธีใช้:
1. เปิด Performance Tab
2. กด Record (⏺)
3. ทำ Action ที่ช้า
4. กด Stop
5. ดู Flame Chart หา Function ที่ใช้เวลานาน
6. หา "Long Tasks" (แถบสีแดง) — งานที่บล็อก Main Thread > 50ms
```

---

### React Error Boundary

**Error Boundary** คือ Component พิเศษที่ดักจับ Error จาก Child Component ป้องกันทั้งแอปล้มเหลวเพราะ Error จุดเดียว

```
โดยปกติ:
Error ใน MovieCard → ทั้งแอปพัง → ผู้ใช้เห็นหน้าจอขาว

ด้วย Error Boundary:
Error ใน MovieCard → แสดง Fallback UI แค่ส่วนนั้น
                     ส่วนอื่นของแอปยังทำงานปกติ
```

**สร้าง Error Boundary Component:**

```typescript
// src/components/ErrorBoundary.tsx
'use client'
import React from 'react'

interface ErrorBoundaryProps {
  children: React.ReactNode
  fallback?: React.ReactNode
  onError?: (error: Error, info: React.ErrorInfo) => void
}

interface ErrorBoundaryState {
  hasError: boolean
  error: Error | null
}

export class ErrorBoundary extends React.Component<
  ErrorBoundaryProps,
  ErrorBoundaryState
> {
  constructor(props: ErrorBoundaryProps) {
    super(props)
    this.state = { hasError: false, error: null }
  }

  // เรียกเมื่อ Child Component throw Error
  static getDerivedStateFromError(error: Error): ErrorBoundaryState {
    return { hasError: true, error }
  }

  // เรียกหลังจาก Error เกิด — ใช้สำหรับ Logging
  componentDidCatch(error: Error, info: React.ErrorInfo) {
    console.error('Error caught by ErrorBoundary:', error, info)
    // ส่งไปยัง Error Tracking Service เช่น Sentry
    this.props.onError?.(error, info)
  }

  render() {
    if (this.state.hasError) {
      if (this.props.fallback) {
        return this.props.fallback
      }

      return (
        <div role="alert" className="p-4 border border-red-200 rounded-lg bg-red-50">
          <h2 className="text-red-800 font-semibold">เกิดข้อผิดพลาด</h2>
          <p className="text-red-600 text-sm mt-1">
            ส่วนนี้ไม่สามารถแสดงได้ในขณะนี้
          </p>
          <button
            onClick={() => this.setState({ hasError: false, error: null })}
            className="mt-2 text-sm text-red-700 underline"
          >
            ลองใหม่
          </button>
        </div>
      )
    }

    return this.props.children
  }
}
```

**วิธีใช้งาน:**

```typescript
// app/page.tsx
import { ErrorBoundary } from '@/components/ErrorBoundary'
import { MovieList } from '@/components/MovieList'
import { Recommendations } from '@/components/Recommendations'

export default function HomePage() {
  return (
    <main>
      <h1>Movie Finder</h1>

      {/* ถ้า MovieList พัง จะแสดง Fallback แทน ไม่กระทบส่วนอื่น */}
      <ErrorBoundary
        fallback={<p>ไม่สามารถโหลดรายการหนังได้</p>}
        onError={(error) => console.error('MovieList Error:', error)}
      >
        <MovieList />
      </ErrorBoundary>

      {/* Recommendations มี Error Boundary ของตัวเอง */}
      <ErrorBoundary>
        <Recommendations />
      </ErrorBoundary>
    </main>
  )
}
```

**Next.js มี `error.tsx` ในตัว:**

```typescript
// app/error.tsx — Error Boundary ระดับ Route
'use client'
import { useEffect } from 'react'

interface ErrorProps {
  error: Error & { digest?: string }
  reset: () => void
}

export default function Error({ error, reset }: ErrorProps) {
  useEffect(() => {
    // Log error ไปยัง Sentry หรือ Service ที่ใช้
    console.error('Route error:', error)
  }, [error])

  return (
    <div className="flex flex-col items-center justify-center min-h-[50vh] gap-4">
      <h2 className="text-xl font-semibold">เกิดข้อผิดพลาดบางอย่าง</h2>
      <p className="text-muted-foreground">{error.message}</p>
      <button
        onClick={reset}
        className="px-4 py-2 bg-primary text-white rounded-md"
      >
        ลองอีกครั้ง
      </button>
    </div>
  )
}
```

---

### Sentry — Error Tracking ใน Production

**Sentry** (เซนทรี) คือบริการติดตาม Error ใน Production แบบ Real-time เมื่อผู้ใช้เจอ Bug ใน Production คุณจะได้รับแจ้งเตือนทันที พร้อม Stack Trace เต็มรูปแบบ

```
ทำไมต้องใช้ Sentry?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
โดยไม่มี Sentry:
ผู้ใช้เจอ Bug → อาจแจ้งหรือไม่แจ้ง → คุณไม่รู้เลย

ด้วย Sentry:
ผู้ใช้เจอ Bug → Sentry จับ Error ทันที →
แจ้งเตือนทาง Slack/Email → คุณรู้ทันที มี Stack Trace ครบ
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**ติดตั้งและตั้งค่า Sentry กับ Next.js:**

```bash
pnpm add @sentry/nextjs
pnpm exec @sentry/wizard@latest -i nextjs
```

Wizard จะสร้างไฟล์ให้อัตโนมัติ:

```typescript
// sentry.client.config.ts — รันในฝั่ง Browser
import * as Sentry from '@sentry/nextjs'

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,

  // บันทึก Performance ด้วย (ดู Slow Request, Core Web Vitals)
  tracesSampleRate: 0.1,  // เก็บ 10% ของ Transaction

  // ไม่เก็บ Error ใน Development
  enabled: process.env.NODE_ENV === 'production',

  // กรอง Error ที่ไม่ต้องการติดตาม
  ignoreErrors: [
    'ResizeObserver loop limit exceeded',
    'Non-Error promise rejection',
  ],
})
```

```typescript
// sentry.server.config.ts — รันในฝั่ง Server
import * as Sentry from '@sentry/nextjs'

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  tracesSampleRate: 0.1,
  enabled: process.env.NODE_ENV === 'production',
})
```

**Capture Error ด้วยตนเองเมื่อต้องการ:**

```typescript
import * as Sentry from '@sentry/nextjs'

// Capture Exception
try {
  const result = await saveFavorite(movieId)
} catch (error) {
  Sentry.captureException(error, {
    extra: {
      movieId,
      userId: session.user.id,
    },
    tags: {
      feature: 'favorites',
    },
  })
  // แสดง Error แก่ผู้ใช้
  toast.error('ไม่สามารถบันทึก Favorite ได้')
}

// Capture Message (ไม่ใช่ Error แต่อยากรู้)
Sentry.captureMessage('User reached rate limit', 'warning')
```

**เพิ่ม User Context เพื่อ Debug ง่ายขึ้น:**

```typescript
// ตั้งค่า User หลัง Login
Sentry.setUser({
  id: session.user.id,
  email: session.user.email,
})

// ล้างเมื่อ Logout
Sentry.setUser(null)
```

---

### การอ่าน Stack Trace อย่างเป็นระบบ

Stack Trace คือรายงานเส้นทางที่ Error เดินทางมา อ่านจาก **ล่างขึ้นบน** เพื่อหาต้นเหตุ:

```
Error: Cannot read properties of undefined (reading 'title')
    at MovieCard (MovieCard.tsx:15:30)        ← บรรทัด 15, column 30
    at renderWithHooks (react-dom.js:14985:18)
    at mountIndeterminateComponent (react-dom.js:17811:13)
    at beginWork (react-dom.js:19049:16)
    ...

การอ่าน:
1. ดูบรรทัดแรก: Error message — "Cannot read properties of undefined"
   → มี undefined ที่ไม่คาดคิด

2. ดูบรรทัดที่สองต่อมาที่เป็นโค้ดของเรา:
   "at MovieCard (MovieCard.tsx:15:30)"
   → ปัญหาอยู่ที่ MovieCard.tsx บรรทัดที่ 15

3. เปิดไฟล์ MovieCard.tsx บรรทัด 15 แล้วดูว่า .title ถูกเรียกบนอะไร
   → อาจเป็น: movie.title แต่ movie เป็น undefined
   → แก้: ใช้ Optional Chaining: movie?.title หรือเพิ่ม null check
```

---

### AI-Assisted Debugging

ในปี 2026 การใช้ AI ช่วย Debug กลายเป็นทักษะสำคัญ แต่ต้องใช้อย่างถูกวิธี:

```
❌ ผิด: "โค้ดฉันพัง ช่วยแก้ด้วย" (วาง code ทั้งหมด)

✅ ถูก:
"ฉันได้รับ Error นี้:
[TypeError: Cannot read properties of undefined (reading 'title')]

ที่บรรทัดนี้ใน MovieCard.tsx:
const title = movie.title

movie มาจาก TanStack Query ผ่าน useQuery
ฉันคิดว่าปัญหาอาจมาจาก movie เป็น undefined ตอน Initial Render
แต่ไม่แน่ใจว่าจะ Handle อย่างไรดีกับ TypeScript

ควรใช้ Optional Chaining หรือ Type Guard หรือมีวิธีอื่นที่ดีกว่า?"
```

---

## 6.6 การจัดทำเอกสารและส่งมอบงาน

### README.md ที่สมบูรณ์

README.md คือหน้าตาแรกที่คนเห็นเมื่อเข้า Repository README ที่ดีช่วยให้:
- Developer ใหม่ในทีม Onboard ได้เร็ว
- ผู้สัมภาษณ์งานเข้าใจโปรเจกต์ของคุณ
- ตัวคุณเองกลับมาแก้ไขได้หลัง 6 เดือน

**โครงสร้าง README ที่สมบูรณ์:**

```markdown
# 🎬 Movie Finder

> แอปค้นหาและบันทึกหนังโปรดส่วนตัว พัฒนาด้วย Next.js 15 + TypeScript

[![CI](https://github.com/username/movie-finder/actions/workflows/ci.yml/badge.svg)](...)
[![Deploy](https://img.shields.io/badge/deploy-vercel-black)](...)
[![License](https://img.shields.io/badge/license-MIT-blue)](...)

![Screenshot](docs/screenshot.png)

---

## ✨ Features

- 🔍 ค้นหาหนังแบบ Real-time พร้อม Debounce
- ❤️ บันทึก Favorite Movies (ต้อง Login)
- 🔐 Login ด้วย Google (OAuth 2.0)
- 📱 Responsive บนทุกขนาดหน้าจอ
- ⚡ Server Components + Streaming สำหรับประสิทธิภาพสูงสุด

---

## 🛠 Tech Stack

| หมวด | เทคโนโลยี |
|------|---------|
| Framework | Next.js 15 (App Router) |
| Language | TypeScript 5.7 |
| Styling | Tailwind CSS v4 + shadcn/ui |
| Auth | Auth.js v5 (Google OAuth) |
| Database | Supabase (PostgreSQL) |
| Data Fetching | TanStack Query v5 |
| Testing | Vitest + Playwright |
| Deploy | Vercel |

---

## 🚀 เริ่มต้นพัฒนา

### ความต้องการ

- Node.js 22 LTS
- pnpm 9+
- บัญชี Supabase (ฟรี)
- Google OAuth credentials

### ติดตั้ง

```bash
# Clone repository
git clone https://github.com/username/movie-finder.git
cd movie-finder

# ติดตั้ง dependencies
pnpm install

# สร้างไฟล์ environment variables
cp .env.example .env.local
```

### Environment Variables

แก้ไข `.env.local` ด้วยค่าของคุณ:

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key

# Auth.js
AUTH_SECRET=your-random-secret  # สร้างด้วย: openssl rand -base64 32
AUTH_GOOGLE_ID=your-google-client-id
AUTH_GOOGLE_SECRET=your-google-client-secret

# TMDB API (สำหรับข้อมูลหนัง)
TMDB_API_KEY=your-tmdb-api-key
```

> ดูรายละเอียดการตั้งค่าแต่ละรายการใน [docs/setup-guide.md](docs/setup-guide.md)

### รันในโหมด Development

```bash
pnpm dev
# เปิด http://localhost:3000
```

---

## 📁 โครงสร้างโปรเจกต์

```
movie-finder/
├── app/                    # Next.js App Router
│   ├── (auth)/             # Route Group: หน้าที่เกี่ยวกับ Auth
│   │   └── login/page.tsx
│   ├── (protected)/        # Route Group: ต้อง Login
│   │   └── favorites/page.tsx
│   ├── movies/[id]/        # Dynamic Route
│   │   └── page.tsx
│   ├── api/                # API Routes
│   │   ├── auth/           # Auth.js handlers
│   │   └── favorites/      # Favorites CRUD
│   └── layout.tsx
├── components/             # Shared Components
│   ├── ui/                 # shadcn/ui components
│   └── movies/             # Movie-specific components
├── hooks/                  # Custom Hooks
├── lib/                    # Utilities, API clients
├── types/                  # TypeScript type definitions
├── e2e/                    # Playwright E2E Tests
└── docs/                   # เอกสารเพิ่มเติม
```

---

## 🧪 การทดสอบ

```bash
# Unit & Component Tests
pnpm test

# รันแบบดู Coverage
pnpm test:coverage

# E2E Tests
pnpm test:e2e

# E2E แบบ UI Mode
pnpm exec playwright test --ui
```

---

## 📦 Scripts

| คำสั่ง | ทำอะไร |
|--------|--------|
| `pnpm dev` | รัน Development Server |
| `pnpm build` | Build สำหรับ Production |
| `pnpm start` | รัน Production Server |
| `pnpm lint` | ตรวจสอบ ESLint |
| `pnpm type-check` | ตรวจสอบ TypeScript |
| `pnpm test` | รัน Unit/Component Tests |
| `pnpm test:e2e` | รัน E2E Tests |

---

## 🤝 Contributing

1. Fork repository
2. สร้าง Branch: `git checkout -b feature/amazing-feature`
3. Commit: `git commit -m 'feat: add amazing feature'`
4. Push: `git push origin feature/amazing-feature`
5. เปิด Pull Request

---

## 📄 License

MIT © 2026 [ชื่อของคุณ]
```

---

### TSDoc — เขียน Comment อธิบาย Function

**TSDoc** คือมาตรฐานการเขียน Comment สำหรับ TypeScript ที่ IDE อ่านได้ แสดงเป็น Tooltip เมื่อ Hover

```typescript
/**
 * กรอง Movies ที่มีคะแนน Rating เกินกว่า threshold
 *
 * @param movies - รายการ Movie ที่ต้องการกรอง
 * @param minRating - คะแนนต่ำสุดที่ยอมรับ (0-10)
 * @returns รายการ Movie ที่ผ่าน threshold
 *
 * @example
 * ```typescript
 * const highRated = filterByRating(movies, 8.0)
 * // คืนเฉพาะหนังที่ rating ≥ 8.0
 * ```
 */
export function filterByRating(movies: Movie[], minRating: number): Movie[] {
  return movies.filter(movie => movie.rating >= minRating)
}

/**
 * Hook สำหรับจัดการ Favorite Movies ของผู้ใช้
 *
 * @remarks
 * ต้องใช้ภายใต้ QueryClientProvider และผู้ใช้ต้อง Login แล้ว
 *
 * @returns Object ที่มี:
 *   - `favorites`: รายการ Favorite ปัจจุบัน
 *   - `addFavorite`: Function เพิ่ม Movie เข้า Favorites
 *   - `removeFavorite`: Function ลบ Movie ออกจาก Favorites
 *   - `isFavorite`: Function ตรวจสอบว่า Movie นั้นเป็น Favorite หรือไม่
 *
 * @example
 * ```tsx
 * function MovieCard({ movie }: { movie: Movie }) {
 *   const { isFavorite, addFavorite, removeFavorite } = useFavorites()
 *
 *   return (
 *     <button onClick={() => isFavorite(movie.id)
 *       ? removeFavorite(movie.id)
 *       : addFavorite(movie.id)
 *     }>
 *       {isFavorite(movie.id) ? '❤️' : '🤍'}
 *     </button>
 *   )
 * }
 * ```
 */
export function useFavorites() {
  // ...
}
```

---

### Pre-deployment Checklist

ก่อน Deploy ขึ้น Production ทุกครั้ง ตรวจสอบรายการนี้:

```markdown
# Pre-deployment Checklist

## ✅ Code Quality
- [ ] ไม่มี `console.log` ที่ไม่จำเป็นเหลืออยู่
- [ ] ไม่มี `TODO` หรือ `FIXME` ที่ยังค้างอยู่ (หรือมีใน Issue Tracker แล้ว)
- [ ] `pnpm lint` ผ่านโดยไม่มี Error
- [ ] `pnpm type-check` ผ่านโดยไม่มี Error
- [ ] `pnpm test:run` ผ่านทุก Test
- [ ] `pnpm build` สำเร็จโดยไม่มี Error หรือ Warning สำคัญ

## ✅ Security
- [ ] ไม่มี API Key หรือ Secret อยู่ใน Code
- [ ] Environment Variables ทั้งหมดตั้งค่าใน Vercel แล้ว
- [ ] `.env.example` อัปเดตแล้ว (ไม่มีค่าจริง)
- [ ] ตรวจสอบ Row Level Security (RLS) ใน Supabase

## ✅ Functionality
- [ ] ทดสอบ Happy Path ทุก Feature หลัก
- [ ] ทดสอบ Error Cases (Network Error, Invalid Input)
- [ ] ทดสอบบนมือถือ (ใช้ DevTools Device Mode)
- [ ] ทดสอบ Dark Mode (ถ้ามี)
- [ ] ตรวจสอบว่า Loading States แสดงถูกต้อง

## ✅ Performance
- [ ] รัน Lighthouse บน Preview URL ผ่าน 90+ (Performance, Accessibility)
- [ ] ไม่มี Image ขนาดใหญ่ที่ไม่ได้ Optimize
- [ ] ไม่มี Bundle ขนาดใหญ่ผิดปกติ (ตรวจด้วย `pnpm build` แล้วดู Bundle Report)

## ✅ Accessibility
- [ ] ทุกปุ่มและ Link มี Label ที่ชัดเจน
- [ ] ทุกรูปภาพมี `alt` text
- [ ] Keyboard Navigation ทำงานได้ถูกต้อง

## ✅ Documentation
- [ ] README.md อัปเดตสำหรับ Feature ใหม่ที่เพิ่ม
- [ ] CHANGELOG.md อัปเดต (ถ้ามี)
- [ ] ไม่มี Comment ที่ Outdated หรือไม่ตรงกับโค้ดจริง
```

---

### Semantic Versioning

**Semantic Versioning (SemVer)** คือมาตรฐานการตั้งเลขเวอร์ชัน `Major.Minor.Patch`

```
ตัวอย่าง: v2.5.3

  2  .  5  .  3
  │     │     │
  │     │     └── PATCH — แก้ Bug (Backward Compatible)
  │     │           เมื่อไหร่: แก้ Bug ที่ไม่กระทบ API
  │     │
  │     └── MINOR — Feature ใหม่ (Backward Compatible)
  │           เมื่อไหร่: เพิ่ม Feature โดยไม่ทำลาย Feature เดิม
  │
  └── MAJOR — เปลี่ยนแปลงใหญ่ (Breaking Change)
        เมื่อไหร่: เปลี่ยน API ที่ทำให้โค้ดเก่าใช้ไม่ได้

ตัวอย่างในโปรเจกต์จริง:
1.0.0 → 1.0.1 : แก้ Bug Search ที่ไม่ทำงานบน Safari
1.0.1 → 1.1.0 : เพิ่มฟีเจอร์ Filter หนังตาม Genre
1.1.0 → 2.0.0 : เปลี่ยน Auth Provider ใหม่ทั้งหมด (ต้อง Login ใหม่)
```

**การสร้าง Release บน GitHub:**

```bash
# 1. อัปเดตเวอร์ชันใน package.json
npm version patch   # 1.0.0 → 1.0.1
npm version minor   # 1.0.0 → 1.1.0
npm version major   # 1.0.0 → 2.0.0

# คำสั่งนี้จะ:
# - แก้ version ใน package.json อัตโนมัติ
# - สร้าง Git Commit: "1.0.1"
# - สร้าง Git Tag: "v1.0.1"

# 2. Push พร้อม Tag
git push && git push --tags
```

**ตัวอย่าง `CHANGELOG.md`:**

```markdown
# Changelog

All notable changes to this project will be documented here.

## [1.2.0] — 2026-05-15

### Added
- เพิ่มฟีเจอร์ Filter หนังตาม Genre
- เพิ่ม Infinite Scroll ในหน้า Home
- Dark Mode

### Fixed
- แก้ Bug ที่ Favorites ไม่อัปเดตทันทีหลัง Login

## [1.1.0] — 2026-04-20

### Added
- ระบบ Login ด้วย Google
- บันทึก Favorite Movies

### Changed
- ปรับปรุง Performance ของ Search ด้วย Debounce

## [1.0.0] — 2026-03-01

### Added
- Release แรก
- ค้นหาหนัง
- ดูรายละเอียดหนัง
```

---

## โปรเจกต์ปฏิบัติ: Capstone Project

### ภาพรวมโปรเจกต์

โปรเจกต์นี้คือสิ่งที่รวมทุกอย่างที่เรียนมาตลอดหลักสูตรเข้าด้วยกัน พัฒนาเป็นทีม 2-3 คน ใช้เวลา 3 สัปดาห์

### Phase 1: Planning (3 วัน)

**สิ่งที่ต้องทำ:**

```
1. เลือกหัวข้อโปรเจกต์ (ตัวอย่าง):
   - Recipe Sharing Platform
   - Personal Finance Tracker
   - Language Learning App
   - Event Planning Tool
   - Local Business Directory

2. สร้าง User Stories อย่างน้อย 8-10 Story
   แต่ละ Story ต้องมี Acceptance Criteria ครบ

3. ออกแบบโครงสร้างข้อมูล (Database Schema ใน Supabase)

4. วาง Component Architecture เบื้องต้น

5. แบ่ง Task ใน Linear และ Assign ให้สมาชิก

6. ตั้งค่า Repository, Branch Protection และ CI/CD
```

**ตัวอย่าง User Stories สำหรับ Recipe Sharing:**

```
Story 1: As a user, I want to browse recipes
Story 2: As a user, I want to search recipes by ingredient
Story 3: As a chef, I want to create and share my recipes
Story 4: As a user, I want to save recipes to my collection
Story 5: As a user, I want to rate and review recipes
Story 6: As a chef, I want to see how many people saved my recipe
Story 7: As a user, I want to filter recipes by dietary needs
Story 8: As a user, I want to see recipes from people I follow
```

### Phase 2: Development (2 สัปดาห์)

**Sprint 1 (สัปดาห์ที่ 1):**
- ตั้งค่าโปรเจกต์ Next.js + Tailwind + Supabase + Auth.js
- สร้าง Database Schema และ RLS Policies
- พัฒนา Core Features (Stories 1-4)
- เขียน Unit Test สำหรับ Logic หลัก

**Sprint 2 (สัปดาห์ที่ 2):**
- พัฒนา Additional Features (Stories 5-8)
- เขียน Component Tests
- เขียน E2E Tests สำหรับ Critical Flow
- Performance Optimization
- Bug Fixes

### Phase 3: Polish & Deploy (4 วัน)

**Checklist ก่อนนำเสนอ:**

```
Technical:
☐ CI Pipeline ผ่านทุก Check
☐ Deploy บน Vercel Production แล้ว
☐ Test Coverage > 60% สำหรับ Core Functions
☐ ผ่าน Pre-deployment Checklist ทุกข้อ
☐ README.md สมบูรณ์

Code Quality:
☐ ไม่มี Any Type ใน TypeScript (หรือมี Comment อธิบาย)
☐ ไม่มี TODO ที่ไม่ได้อยู่ใน Issue Tracker
☐ Component ไม่ยาวเกิน 200 บรรทัด (ถ้ายาวกว่านั้น พิจารณาแยก)

User Experience:
☐ Loading States ครบทุก Async Operation
☐ Error Messages ที่ผู้ใช้เข้าใจได้
☐ Responsive บนมือถือ
```

### Phase 4: Presentation (วันสุดท้าย)

**โครงสร้างการนำเสนอ 15-20 นาที:**

```
1. Demo Live (7 นาที)
   - เปิดแอปจริงบน Vercel
   - Walk through User Stories หลัก
   - แสดง Mobile Responsive

2. Technical Deep Dive (5 นาที)
   - สถาปัตยกรรมที่น่าสนใจ
   - ปัญหาที่เจอและวิธีแก้
   - สิ่งที่ภูมิใจที่สุดในโค้ด

3. Testing & CI/CD Demo (3 นาที)
   - แสดง GitHub Actions ที่รันผ่าน
   - Coverage Report
   - Preview Deployment

4. ถาม-ตอบ (5 นาที)
```

---

## สรุปหน่วยที่ 6

```
สิ่งที่เรียนรู้ในหน่วยนี้
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ อ่าน User Stories และแปลงเป็น Task ด้วย Acceptance Criteria
   ที่ชัดเจน ใช้ Definition of Done เป็นมาตรฐานร่วม

✅ เขียน Unit Test ด้วย Vitest ครอบคลุม Pure Functions และ
   Custom Hooks ด้วยรูปแบบ AAA และ Fake Timers

✅ เขียน Component Test ด้วย React Testing Library โดยทดสอบ
   พฤติกรรม ไม่ใช่ Implementation ครอบคลุม Async States

✅ Mock Network Request ด้วย MSW เพื่อ Isolate Tests
   จาก API จริง และ Override Handler สำหรับ Edge Cases

✅ เขียน E2E Test ด้วย Playwright จำลอง User Flow จริง
   รวมถึง Visual Regression Testing

✅ ตั้งค่า GitHub Actions CI Pipeline ที่รัน Lint, Type Check,
   Test, Build และ E2E อัตโนมัติ

✅ Deploy Next.js บน Vercel พร้อม Preview Deployments และ
   จัดการ Environment Variables อย่างปลอดภัย

✅ ทำงานด้วย GitHub Flow, Branch Naming, Conventional Commits
   และ Code Review อย่างมืออาชีพ

✅ Debug ด้วย DevTools Breakpoints, Error Boundary, และ
   Sentry ใน Production

✅ จัดทำ README.md สมบูรณ์, TSDoc, และผ่าน Pre-deployment
   Checklist ก่อนส่งมอบงานทุกครั้ง

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## คำศัพท์ที่ควรรู้ (Glossary)

| คำศัพท์ | คำอ่าน | ความหมาย |
|--------|--------|---------|
| User Story | ยูเซอร์ สตอรี | การอธิบาย Feature จากมุมมองผู้ใช้ |
| Acceptance Criteria | แอคเซปแตนซ์ ไครทีเรีย | เงื่อนไขที่ต้องผ่านก่อนถือว่าเสร็จ |
| Definition of Done | เดฟฟินิชัน ออฟ ดัน | มาตรฐานของทีมว่า Feature "เสร็จ" คืออะไร |
| Agile | แอไจล์ | แนวทางพัฒนาซอฟต์แวร์ที่เน้นความยืดหยุ่น |
| Sprint | สปรินต์ | รอบการทำงาน 1-4 สัปดาห์ใน Scrum |
| Unit Test | ยูนิต เทสต์ | ทดสอบ Function เดี่ยว ๆ |
| Component Test | คอมโพเนนต์ เทสต์ | ทดสอบ React Component |
| E2E Test | อีทูอี เทสต์ | ทดสอบ Flow ทั้งหมดจาก User มุมมอง |
| Mock | ม็อก | จำลองสิ่งที่ไม่ต้องการทดสอบจริง |
| MSW | เอ็มเอสดับเบิลยู | Mock Service Worker — สกัดกั้น Network Request |
| CI | ซีไอ | Continuous Integration — ตรวจสอบโค้ดอัตโนมัติ |
| CD | ซีดี | Continuous Deployment — Deploy อัตโนมัติ |
| GitHub Actions | กิตฮับ แอคชันส์ | ระบบ CI/CD ของ GitHub |
| Vercel | เวอร์เซล | Platform สำหรับ Deploy Next.js |
| Preview Deployment | พรีวิว ดีพลอยเมนต์ | URL ทดสอบสำหรับแต่ละ Pull Request |
| GitHub Flow | กิตฮับ โฟลว์ | Workflow การทำงานกับ Git ในทีม |
| Conventional Commits | คอนเวนชันนัล คอมมิตส์ | มาตรฐานการเขียน Commit Message |
| Error Boundary | เอเรอร์ เบาน์ดารี | Component ที่ดักจับ Error ของ Child |
| Sentry | เซนทรี | บริการติดตาม Error ใน Production |
| Stack Trace | สแต็ก เทรส | รายงานเส้นทางที่ Error เดินทางมา |
| TSDoc | ทีเอสด็อก | มาตรฐาน Comment สำหรับ TypeScript |
| SemVer | เซมเวอร์ | Semantic Versioning — มาตรฐานเลขเวอร์ชัน |
| Breakpoint | เบรคพอยต์ | จุดหยุดโปรแกรมเพื่อ Debug |
| Flame Chart | เฟลม ชาร์ต | กราฟแสดง Performance ใน DevTools |
| Visual Regression | วิชวล รีเกรชัน | การตรวจสอบว่า UI ไม่เปลี่ยนโดยไม่ตั้งใจ |
| Flaky Test | เฟลกกี้ เทสต์ | Test ที่ Fail แบบสุ่ม ไม่ Consistent |
| Coverage | คัฟเวอเรจ | ร้อยละของโค้ดที่ถูกทดสอบ |

---

## แหล่งเรียนรู้เพิ่มเติม

**Testing:**
- **testing-library.com** — เอกสาร React Testing Library อย่างเป็นทางการ
- **playwright.dev** — เอกสาร Playwright พร้อม Best Practices
- **vitest.dev** — เอกสาร Vitest
- **mswjs.io** — เอกสาร Mock Service Worker

**CI/CD & Deployment:**
- **docs.github.com/actions** — GitHub Actions Documentation
- **vercel.com/docs** — Vercel Documentation
- **docs.sentry.io** — Sentry Documentation

**Git & Teamwork:**
- **conventionalcommits.org** — Conventional Commits Specification
- **commitlint.js.org** — Commitlint Documentation

**Performance & Debugging:**
- **web.dev/performance** — Core Web Vitals และ Performance Best Practices
- **developer.chrome.com/docs/devtools** — Chrome DevTools Guide

---

*หน่วยนี้เป็นหน่วยสุดท้ายของหลักสูตร — ยินดีด้วยที่เดินทางมาถึงจุดนี้!*

*เส้นทางหลังจากนี้ → Junior Front-End Developer / Full-Stack Developer / UI Engineering*

---

*ปรับปรุงล่าสุด: พฤษภาคม 2026*
