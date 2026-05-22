# หน่วยที่ 5: การเชื่อมต่อ API, Authentication และการจัดการข้อมูล (Part 1)

> **หลักสูตร:** หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
> **ระดับ:** ปานกลาง–สูง (Intermediate–Advanced)
> **ระยะเวลา:** 1.5 สัปดาห์ (Part 1 จาก 3 สัปดาห์)
> **ต่อจาก:** Unit 4 — React 19, Next.js 15, App Router, Server Components
> **ปรับปรุงล่าสุด:** พฤษภาคม 2026

---

## วัตถุประสงค์การเรียนรู้ (Part 1)

เมื่อเรียนจบ Part 1 ผู้เรียนจะสามารถ:

1. อธิบาย HTTP Protocol, Methods และ Status Codes ที่ใช้งานจริงได้อย่างถูกต้อง
2. ออกแบบและทดสอบ API Request ด้วย Bruno ได้อย่างเป็นระบบ
3. อธิบายได้ว่าทำไม `useEffect` ไม่เหมาะกับการดึงข้อมูล และใช้ TanStack Query แทนได้
4. ใช้ `useQuery` ดึงข้อมูล, จัดการ Loading/Error State และ Cache ได้
5. ใช้ `useMutation` สร้าง/แก้ไข/ลบข้อมูล และทำ Cache Invalidation ได้
6. ทำ Optimistic Updates เพื่อประสบการณ์ที่ลื่นไหลได้
7. ใช้ `useInfiniteQuery` สำหรับ Infinite Scroll ได้

---

## ก่อนเริ่ม: แอปที่ "มีชีวิต" ต้องการข้อมูลจากภายนอก

ในหน่วยที่ผ่านมา เราสร้าง UI ที่สวยงาม มี Component, State, Routing แต่ข้อมูลทั้งหมดยัง **hardcode** อยู่ในโค้ด

แอปจริงในโลกการทำงานแตกต่างกันมาก:

```
แอปจริง ต้องการ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ ดึงรายการสินค้าจาก Database ของบริษัท
✅ บันทึกคำสั่งซื้อของผู้ใช้ลงเซิร์ฟเวอร์
✅ แสดงผลการค้นหาจาก Search Engine
✅ Login ด้วย Google / Facebook
✅ รับ Notification แบบ Real-time
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ทั้งหมดนี้ต้องการ "การสื่อสาร" กับเซิร์ฟเวอร์
```

**API (Application Programming Interface)** คือ "สะพาน" ที่ Front-End ใช้พูดคุยกับ Back-End และ Database

Part 1 ของหน่วยนี้จะสอนให้คุณเข้าใจการสื่อสารนั้นตั้งแต่รากฐาน และใช้งานได้อย่างมืออาชีพ

---

## 5.1 RESTful API และ HTTP Protocol — พื้นฐานที่ต้องแน่น

### HTTP คืออะไร?

**HTTP (HyperText Transfer Protocol)** คือภาษากลางที่ Browser และ Server ใช้สื่อสารกัน ทุกครั้งที่คุณเปิดเว็บ, กดปุ่ม, หรือ Login — มี HTTP Request เกิดขึ้นเบื้องหลังเสมอ

```
วงจรของ HTTP Request/Response:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Browser (Client)                          Server
     │                                      │
     │  ── HTTP Request ──────────────────► │
     │    Method: GET                       │
     │    URL: /api/movies                  │
     │    Headers: { Authorization: ... }   │  ← ประมวลผล
     │    Body: (ว่างสำหรับ GET)            │  ← ค้นหาข้อมูล
     │                                      │  ← จัดเตรียม
     │  ◄── HTTP Response ─────────────── │
     │    Status: 200 OK                    │
     │    Headers: { Content-Type: ... }    │
     │    Body: [{ id: 1, title: "..." }]   │
     │                                      │
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### HTTP Methods — "คำกริยา" ของ API

แต่ละ Method บอกว่าเราต้องการ **ทำอะไร** กับข้อมูล:

| Method | ใช้เพื่อ | ตัวอย่าง |
|--------|---------|---------|
| `GET` | **อ่าน** ข้อมูล (ไม่เปลี่ยนแปลง) | ดึงรายการหนัง, ดูโปรไฟล์ผู้ใช้ |
| `POST` | **สร้าง** ข้อมูลใหม่ | สมัครสมาชิก, เพิ่มสินค้าในตะกร้า |
| `PUT` | **แทนที่** ข้อมูลทั้งหมด | อัปเดตโปรไฟล์ทั้งหมด |
| `PATCH` | **แก้ไขบางส่วน** ของข้อมูล | เปลี่ยนเฉพาะชื่อผู้ใช้ |
| `DELETE` | **ลบ** ข้อมูล | ลบบัญชีผู้ใช้, ลบโพสต์ |

**ความแตกต่างระหว่าง PUT กับ PATCH ในทางปฏิบัติ:**

```typescript
// สมมติข้อมูล User ปัจจุบัน:
// { id: 1, name: "สมชาย", email: "som@example.com", age: 25 }

// PUT — ต้องส่งข้อมูล ทั้งหมด
// ถ้าไม่ส่ง field ไหน field นั้นจะถูก null/ลบออก
PUT /api/users/1
Body: { name: "สมชาย ใจดี", email: "som@example.com", age: 25 }
// ต้องส่ง email และ age ซ้ำ แม้ไม่ได้เปลี่ยน

// PATCH — ส่งเฉพาะ field ที่เปลี่ยน
PATCH /api/users/1
Body: { name: "สมชาย ใจดี" }
// Server จะเปลี่ยนแค่ name, ส่วน email และ age คงเดิม
```

---

### HTTP Status Codes — "สัญญาณไฟจราจร" ของ API

Status Code คือตัวเลข 3 หลักที่ Server ส่งกลับมาบอกผลลัพธ์ของ Request:

```
กลุ่ม 2xx — สำเร็จ ✅
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
200 OK          → สำเร็จทุกอย่าง (ใช้กับ GET, PUT, PATCH)
201 Created     → สร้างสำเร็จ (ใช้กับ POST)
204 No Content  → สำเร็จแต่ไม่มีข้อมูลส่งคืน (ใช้กับ DELETE)

กลุ่ม 4xx — ผู้ใช้/Client ทำผิด ❌
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
400 Bad Request        → ส่งข้อมูลมาผิดรูปแบบ
401 Unauthorized       → ยังไม่ได้ Login (ไม่มี Token)
403 Forbidden          → Login แล้ว แต่ไม่มีสิทธิ์
404 Not Found          → ไม่พบข้อมูลที่ขอ
409 Conflict           → ข้อมูลซ้ำ (เช่น Email ซ้ำในระบบ)
422 Unprocessable      → ข้อมูลถูก Format แต่ Logic ผิด (เช่น อายุติดลบ)
429 Too Many Requests  → เรียก API เร็วเกินกำหนด (Rate Limit)

กลุ่ม 5xx — Server ทำผิด / มีปัญหา 🔥
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
500 Internal Server Error → Server พัง (bug ฝั่ง Back-end)
502 Bad Gateway           → Server ที่ต่อต่อไปตอบสนองผิดพลาด
503 Service Unavailable   → Server ปิดอยู่ หรือ Overload
```

> **เคล็ดลับ:** ในฐานะ Front-End Developer คุณต้องจัดการ Response ที่ต่างกัน เช่น ถ้าได้ 401 ให้ redirect ไปหน้า Login, ถ้าได้ 404 ให้แสดงข้อความ "ไม่พบข้อมูล", ถ้าได้ 5xx ให้แสดง "เกิดข้อผิดพลาด กรุณาลองใหม่"

---

### Request Headers — "ซองจดหมาย" ของ API

Headers คือข้อมูลที่แนบไปกับ Request เพื่อบอก Context ต่าง ๆ:

```typescript
// ตัวอย่าง Request Headers ที่ใช้บ่อย
const headers = {
  // บอก Server ว่าเราส่งข้อมูลในรูปแบบอะไร
  'Content-Type': 'application/json',

  // บอกว่าเราต้องการรับข้อมูลในรูปแบบอะไร
  'Accept': 'application/json',

  // ส่ง Token เพื่อยืนยันตัวตน (Authentication)
  'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIs...',

  // บอก Server ว่า Client ใช้ภาษาอะไร
  'Accept-Language': 'th-TH, en-US',
}
```

**ทำไม `Content-Type: application/json` สำคัญ?**

```typescript
// ❌ ไม่มี Content-Type → Server อาจอ่านข้อมูลไม่ออก
fetch('/api/users', {
  method: 'POST',
  body: JSON.stringify({ name: 'สมชาย' }),
})

// ✅ ถูกต้อง — บอก Server ว่า Body เป็น JSON
fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ name: 'สมชาย' }),
})
```

---

### REST Architecture — หลักการออกแบบ API ที่ดี

**REST (Representational State Transfer)** คือแนวทางออกแบบ API ที่ใช้กันทั่วโลก หัวใจสำคัญคือการใช้ URL เป็น "ที่อยู่" ของข้อมูล และใช้ HTTP Method บอก Action:

```
การออกแบบ URL แบบ RESTful:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Resource: Movies (ทรัพยากรหลัก)
─────────────────────────────────────────────────────────────────
GET    /api/movies           → ดึงรายการหนังทั้งหมด
GET    /api/movies?page=2    → ดึงหนัง หน้า 2
GET    /api/movies/123       → ดึงหนัง ID=123 อย่างเดียว
POST   /api/movies           → สร้างหนังใหม่
PUT    /api/movies/123       → แทนที่ข้อมูลหนัง ID=123 ทั้งหมด
PATCH  /api/movies/123       → แก้ไขบางส่วนของหนัง ID=123
DELETE /api/movies/123       → ลบหนัง ID=123

Resource: Reviews ที่สัมพันธ์กับ Movie (Nested Resource)
─────────────────────────────────────────────────────────────────
GET    /api/movies/123/reviews      → รีวิวทั้งหมดของหนัง 123
POST   /api/movies/123/reviews      → เพิ่มรีวิวให้หนัง 123
DELETE /api/movies/123/reviews/456  → ลบรีวิว 456 ของหนัง 123
```

**หลักการตั้งชื่อ URL ที่ดี:**

```
✅ ใช้ Noun (คำนาม) ไม่ใช่ Verb (คำกริยา)
   /api/movies         ✅ (คำนาม)
   /api/getMovies      ❌ (มี verb ซ้อน — HTTP Method ทำหน้าที่นี้อยู่แล้ว)

✅ ใช้ตัวพิมพ์เล็กและ kebab-case
   /api/movie-genres   ✅
   /api/MovieGenres    ❌

✅ ใช้พหูพจน์สำหรับ Collection
   /api/movies         ✅
   /api/movie          ❌

✅ ใช้ Query Parameters สำหรับ Filter/Sort/Paginate
   /api/movies?genre=action&sort=rating&page=1   ✅
   /api/movies/action/sort-by-rating/page-1      ❌
```

---

### ทดสอบ API ด้วย Bruno

ก่อนเขียนโค้ดใน React คุณควรทดสอบ API ให้แน่ใจก่อนเสมอ **Bruno** คือเครื่องมือ GUI ที่ช่วยสร้างและรัน HTTP Request โดยไม่ต้องเขียนโค้ด

**ทำไมถึงแนะนำ Bruno มากกว่า Postman?**

```
Bruno vs Postman:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Bruno                          Postman
─────────────────────────────────────────────────────
✅ Open-source ฟรีทุก Feature  ⚠️  Feature สำคัญต้องจ่าย
✅ เก็บ Collection เป็น .bru   ❌  เก็บบน Cloud ของ Postman
   ไฟล์ → commit ลง Git ได้       → ไม่เหมาะกับ Version Control
✅ ทำงาน Offline ได้ 100%     ⚠️  บาง Feature ต้องออนไลน์
✅ เบา เร็ว ไม่ต้องสมัครสมาชิก ❌  ต้องสร้าง Account
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**ติดตั้ง Bruno:**

ดาวน์โหลดจาก [https://www.usebruno.com](https://www.usebruno.com) — มีทั้ง Windows, macOS, Linux

**สร้าง API Collection แรก:**

```
1. เปิด Bruno → คลิก "Create Collection"
2. ตั้งชื่อ "Movie API" → เลือก Folder ที่อยู่ใน Git Repository ของโปรเจกต์
3. คลิก "New Request"
4. ตั้งค่า:
   - Method: GET
   - URL: https://api.themoviedb.org/3/movie/popular
   - Query Params:
       api_key = [your_api_key]
       language = th-TH
       page = 1
5. คลิก "Send" → ดูผลลัพธ์ใน Panel ด้านขวา
```

**ตัวอย่าง Request หลายประเภทใน Bruno:**

```
Collection: Movie API
├── movies/
│   ├── GET popular-movies.bru
│   ├── GET movie-detail.bru     (params: movie_id)
│   └── GET search-movies.bru   (params: query)
├── auth/
│   ├── POST login.bru           (body: email, password)
│   └── POST refresh-token.bru  (body: refresh_token)
└── user/
    ├── GET my-profile.bru      (header: Authorization)
    └── PATCH update-profile.bru
```

> **เคล็ดลับมืออาชีพ:** เก็บ Bruno Collection ไว้ใน folder `bruno/` ภายใน Git Repository เดียวกับโค้ด ทีมทุกคนจะได้ใช้ Collection เดียวกันและไม่ต้องตั้งค่าซ้ำ

---

## 5.2 Data Fetching ด้วย TanStack Query v5

### ปัญหาของการใช้ `useEffect` ดึงข้อมูล

หลังเรียน React ผู้เรียนมักจะเขียนโค้ดดึงข้อมูลแบบนี้:

```typescript
// ❌ วิธีที่ "ใช้งานได้" แต่มีปัญหามาก
function MovieList() {
  const [movies, setMovies] = useState([])
  const [isLoading, setIsLoading] = useState(false)
  const [error, setError] = useState(null)

  useEffect(() => {
    setIsLoading(true)
    
    fetch('/api/movies')
      .then(res => res.json())
      .then(data => {
        setMovies(data)
        setIsLoading(false)
      })
      .catch(err => {
        setError(err)
        setIsLoading(false)
      })
  }, [])

  if (isLoading) return <div>กำลังโหลด...</div>
  if (error) return <div>เกิดข้อผิดพลาด</div>
  return <ul>{movies.map(m => <li key={m.id}>{m.title}</li>)}</ul>
}
```

โค้ดนี้ดูใช้งานได้ แต่มีปัญหาที่ซ่อนอยู่มากมาย:

```
ปัญหาที่พบบ่อยกับ useEffect + fetch:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
❌ Race Condition
   ผู้ใช้กดปุ่ม Filter เร็ว ๆ → มี Request หลายอันบินออก
   Response อาจกลับมาผิดลำดับ → แสดงข้อมูลผิด

❌ ไม่มี Cache
   ผู้ใช้กลับมาหน้าเดิม → ดึงข้อมูลซ้ำทุกครั้ง ทั้งที่ข้อมูลไม่เปลี่ยน
   → Server โหลดหนัก, User รอนาน

❌ Memory Leak
   Component ถูก Unmount ระหว่างที่ Request ยังไม่จบ
   → พยายาม setMovies() หลัง Unmount → Warning ใน Console
   (ต้องใช้ AbortController แก้ — ซับซ้อน)

❌ Stale Data
   ข้อมูลในหน้านี้อาจล้าสมัยแล้ว แต่ไม่มีการ Refetch อัตโนมัติ

❌ ต้องเขียน Loading/Error State ซ้ำทุก Component
   ทุก Component ที่ fetch ข้อมูล ต้องเขียน useState และ logic ซ้ำ

❌ ไม่มี Background Updates
   ถ้าข้อมูลเปลี่ยนบน Server ระหว่างที่ผู้ใช้เปิดหน้าอยู่
   → ผู้ใช้ไม่รู้เลย จนกว่าจะ Refresh หน้า
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**TanStack Query (เดิมชื่อ React Query) แก้ปัญหาทั้งหมดนี้:**

```
TanStack Query จัดการให้อัตโนมัติ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Intelligent Caching    — จำข้อมูลไว้ ไม่ดึงซ้ำถ้าไม่จำเป็น
✅ Background Refetch     — อัปเดตข้อมูลเบื้องหลังอัตโนมัติ
✅ Loading/Error States   — จัดการให้ทั้งหมด ไม่ต้องเขียน useState เอง
✅ Race Condition Safe    — ยกเลิก Request เก่าให้อัตโนมัติ
✅ Deduplication          — ถ้าหลาย Component ขอข้อมูลเดียวกัน
                            ส่ง Request เดียว แชร์ผลลัพธ์
✅ Window Focus Refetch   — Refetch อัตโนมัติเมื่อผู้ใช้กลับมาที่หน้าต่าง
✅ Retry on Failure       — ลองใหม่อัตโนมัติถ้า Request ล้มเหลว
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### ติดตั้ง TanStack Query

```bash
pnpm add @tanstack/react-query @tanstack/react-query-devtools
```

**ตั้งค่า Provider ใน Next.js 15:**

เนื่องจาก TanStack Query ใช้ Context ซึ่งต้องการ Client Component เราต้องสร้าง Provider แยก:

```typescript
// app/providers.tsx
'use client'

import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'
import { useState } from 'react'

export function Providers({ children }: { children: React.ReactNode }) {
  // ใช้ useState เพื่อให้แต่ละ user ได้ QueryClient ของตัวเอง
  // (สำคัญมากสำหรับ SSR — ป้องกัน Data ปนกันระหว่าง User)
  const [queryClient] = useState(
    () =>
      new QueryClient({
        defaultOptions: {
          queries: {
            // ข้อมูลถือว่า "สด" นาน 1 นาที ก่อนที่จะ Refetch ใหม่
            staleTime: 60 * 1000,
            // ลองใหม่ 1 ครั้งถ้าล้มเหลว (default คือ 3 ครั้ง)
            retry: 1,
          },
        },
      })
  )

  return (
    <QueryClientProvider client={queryClient}>
      {children}
      {/* DevTools แสดงเฉพาะ Development */}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  )
}
```

```typescript
// app/layout.tsx
import { Providers } from './providers'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="th">
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  )
}
```

---

### สร้าง API Client — แยกโค้ด Fetch ออกมาต่างหาก

ก่อนใช้ TanStack Query เราควรแยก "โค้ดที่คุยกับ API" ออกมาเป็น Layer ต่างหาก:

```typescript
// lib/api.ts
// ← ไฟล์นี้จัดการ HTTP Request ทั้งหมด ไม่มี React ในนี้เลย

const API_BASE_URL = process.env.NEXT_PUBLIC_API_URL || 'https://api.example.com'

// Helper function สำหรับ fetch ที่จัดการ Error ให้
async function apiRequest<T>(
  endpoint: string,
  options: RequestInit = {}
): Promise<T> {
  const url = `${API_BASE_URL}${endpoint}`

  const response = await fetch(url, {
    headers: {
      'Content-Type': 'application/json',
      'Accept': 'application/json',
      ...options.headers,
    },
    ...options,
  })

  // ถ้า Response ไม่ใช่ 2xx → throw Error
  if (!response.ok) {
    const errorBody = await response.json().catch(() => ({}))
    throw new Error(
      errorBody.message || `HTTP Error: ${response.status} ${response.statusText}`
    )
  }

  // 204 No Content → ไม่มี Body ส่งคืน
  if (response.status === 204) {
    return null as T
  }

  return response.json() as Promise<T>
}

// ─── Types ───────────────────────────────────────────────────────

export interface Movie {
  id: number
  title: string
  overview: string
  poster_path: string
  release_date: string
  vote_average: number
  genre_ids: number[]
}

export interface MovieListResponse {
  page: number
  results: Movie[]
  total_pages: number
  total_results: number
}

export interface MovieDetail extends Movie {
  genres: { id: number; name: string }[]
  runtime: number
  tagline: string
}

// ─── API Functions ──────────────────────────────────────────────

export const moviesApi = {
  // ดึงหนัง Popular
  getPopular: (page = 1) =>
    apiRequest<MovieListResponse>(`/movies/popular?page=${page}`),

  // ค้นหาหนัง
  search: (query: string, page = 1) =>
    apiRequest<MovieListResponse>(
      `/movies/search?query=${encodeURIComponent(query)}&page=${page}`
    ),

  // ดึงรายละเอียดหนัง
  getById: (id: number) =>
    apiRequest<MovieDetail>(`/movies/${id}`),

  // บันทึก Favorite
  addFavorite: (movieId: number) =>
    apiRequest<{ success: boolean }>('/user/favorites', {
      method: 'POST',
      body: JSON.stringify({ movieId }),
    }),

  // ลบ Favorite
  removeFavorite: (movieId: number) =>
    apiRequest<void>(`/user/favorites/${movieId}`, {
      method: 'DELETE',
    }),

  // ดึงรายการ Favorite ของ User
  getFavorites: () =>
    apiRequest<Movie[]>('/user/favorites'),
}
```

> **ทำไมต้องแยกออกมา?** เพราะถ้า API URL เปลี่ยน หรือ เพิ่ม Header ใหม่ คุณแก้แค่ที่เดียว ไม่ต้องวิ่งหาทุก Component ที่ใช้ `fetch()`

---

### useQuery — ดึงข้อมูลอย่างถูกวิธี

`useQuery` คือ Hook หลักของ TanStack Query สำหรับ **อ่านข้อมูล (GET)**:

```typescript
// components/MovieList.tsx
'use client'

import { useQuery } from '@tanstack/react-query'
import { moviesApi } from '@/lib/api'

export function MovieList() {
  const {
    data,      // ข้อมูลที่ได้จาก API
    isLoading, // true ระหว่างโหลดครั้งแรก (ยังไม่มี Cache)
    isFetching,// true ทุกครั้งที่กำลัง Fetch (รวมถึง Background Refetch)
    isError,   // true ถ้าเกิด Error
    error,     // Error object
    refetch,   // Function สำหรับ Refetch ด้วยตนเอง
  } = useQuery({
    queryKey: ['movies', 'popular'],  // ← กุญแจสำคัญ! (อธิบายด้านล่าง)
    queryFn: () => moviesApi.getPopular(),
  })

  if (isLoading) return <MovieListSkeleton />
  if (isError) return <ErrorMessage message={error.message} onRetry={refetch} />

  return (
    <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
      {data?.results.map(movie => (
        <MovieCard key={movie.id} movie={movie} />
      ))}
    </div>
  )
}
```

---

### Query Key — หัวใจของ TanStack Query

**Query Key** คือ Array ที่ TanStack Query ใช้เป็น "ที่อยู่" ของข้อมูลใน Cache:

```typescript
// Query Key ต้อง "อธิบาย" ข้อมูลที่ต้องการได้ครบ

// ─── ตัวอย่างที่ 1: หนัง Popular ─────────────────────────────
{ queryKey: ['movies', 'popular'] }
// Cache key: "movies > popular"

// ─── ตัวอย่างที่ 2: หนัง Popular หน้า 3 ─────────────────────
{ queryKey: ['movies', 'popular', { page: 3 }] }
// Cache key: "movies > popular > {page: 3}"

// ─── ตัวอย่างที่ 3: รายละเอียดหนัง ID 550 ────────────────────
{ queryKey: ['movies', 'detail', 550] }
// Cache key: "movies > detail > 550"

// ─── ตัวอย่างที่ 4: ผลค้นหาด้วยคำว่า "avengers" ──────────────
{ queryKey: ['movies', 'search', 'avengers'] }
// Cache key: "movies > search > avengers"
```

**กฎทอง:** ถ้า Query Key ต่างกัน = Cache คนละอัน ถ้า Query Key เหมือนกัน = แชร์ Cache เดียวกัน

```typescript
// ตัวอย่างที่เห็นชัด: Query Key เปลี่ยนตาม Input
function MovieSearch({ searchQuery }: { searchQuery: string }) {
  const { data } = useQuery({
    // ← searchQuery อยู่ใน Key ดังนั้นเมื่อ searchQuery เปลี่ยน
    // TanStack Query จะ fetch ใหม่อัตโนมัติ
    queryKey: ['movies', 'search', searchQuery],
    queryFn: () => moviesApi.search(searchQuery),

    // ไม่ Fetch ถ้า searchQuery ว่างเปล่า
    enabled: searchQuery.length > 0,
  })

  return (
    <div>
      {data?.results.map(movie => (
        <MovieCard key={movie.id} movie={movie} />
      ))}
    </div>
  )
}
```

---

### ตัวอย่างจริง: ดึงข้อมูลพร้อม Pagination

```typescript
// components/PopularMovies.tsx
'use client'

import { useState } from 'react'
import { useQuery } from '@tanstack/react-query'
import { moviesApi } from '@/lib/api'

export function PopularMovies() {
  const [page, setPage] = useState(1)

  const { data, isLoading, isPlaceholderData } = useQuery({
    queryKey: ['movies', 'popular', page],
    queryFn: () => moviesApi.getPopular(page),
    
    // placeholderData: ขณะที่กำลังโหลดหน้าใหม่
    // ให้ใช้ข้อมูลของหน้าก่อนหน้าแสดงไปก่อน (UI ไม่กระพริบ)
    placeholderData: (previousData) => previousData,
  })

  return (
    <div>
      <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
        {isLoading ? (
          <MovieListSkeleton count={8} />
        ) : (
          data?.results.map(movie => (
            <MovieCard key={movie.id} movie={movie} />
          ))
        )}
      </div>

      {/* Pagination */}
      <div className="flex items-center justify-center gap-4 mt-8">
        <button
          onClick={() => setPage(p => Math.max(1, p - 1))}
          disabled={page === 1}
          className="px-4 py-2 rounded bg-gray-200 disabled:opacity-50"
        >
          ← ก่อนหน้า
        </button>

        <span className="text-sm text-gray-600">
          หน้า {page} จาก {data?.total_pages ?? '...'}
        </span>

        <button
          onClick={() => setPage(p => p + 1)}
          // ปิดปุ่มถ้า: กำลังโหลด หรือ อยู่หน้าสุดท้ายแล้ว
          disabled={isPlaceholderData || page === data?.total_pages}
          className="px-4 py-2 rounded bg-blue-600 text-white disabled:opacity-50"
        >
          ถัดไป →
        </button>
      </div>
    </div>
  )
}
```

---

### Stale Time vs Cache Time — ทำความเข้าใจระบบ Cache

นี่คือแนวคิดที่ผู้เรียนสับสนมากที่สุด:

```
ไทม์ไลน์ของข้อมูลใน TanStack Query:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Fetch สำเร็จ
     │
     │◄──── staleTime (default: 0ms) ────►│
     │      ข้อมูลถือว่า "สด" (fresh)      │
     │      ไม่ Refetch แม้ Component       │
     │      Mount ใหม่                      │
     │                                     │
     │                              ข้อมูลกลายเป็น "เก่า" (stale)
     │                              → จะ Refetch เมื่อ:
     │                                - Component Mount ใหม่
     │                                - หน้าต่างกลับมา Focus
     │                                - Network กลับมา Online
     │
     │◄────────── gcTime (default: 5 นาที) ──────────────────────►│
     │            ข้อมูลยังอยู่ใน Memory (Cache)                   │
     │            แม้ไม่มี Component ใช้งาน                        │
     │                                                             │
     │                                                      ลบออกจาก Cache
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

สรุป:
staleTime = "นานแค่ไหนที่ข้อมูลถือว่ายังใช้ได้โดยไม่ต้อง Refetch"
gcTime    = "นานแค่ไหนที่จะเก็บข้อมูลไว้ใน Memory หลังจากไม่ใช้แล้ว"
```

**เลือก staleTime ให้เหมาะกับข้อมูล:**

```typescript
// ข้อมูลที่เปลี่ยนบ่อย → staleTime น้อย
const { data: notifications } = useQuery({
  queryKey: ['notifications'],
  queryFn: fetchNotifications,
  staleTime: 30 * 1000,       // 30 วินาที
  refetchInterval: 60 * 1000, // Refetch ทุก 1 นาที (Polling)
})

// ข้อมูลที่เปลี่ยนน้อย → staleTime มาก
const { data: genres } = useQuery({
  queryKey: ['movie-genres'],
  queryFn: fetchGenres,
  staleTime: 60 * 60 * 1000, // 1 ชั่วโมง (Genre แทบไม่เปลี่ยน)
})

// ข้อมูลคงที่ตลอดกาล → staleTime = Infinity
const { data: countries } = useQuery({
  queryKey: ['countries'],
  queryFn: fetchCountries,
  staleTime: Infinity, // ไม่ Refetch เลยตลอด Session
})
```

---

### useMutation — เปลี่ยนแปลงข้อมูล (POST, PUT, PATCH, DELETE)

`useMutation` ใช้สำหรับ Action ที่ **เปลี่ยนแปลงข้อมูล** บน Server:

```typescript
// components/AddToFavorites.tsx
'use client'

import { useMutation, useQueryClient } from '@tanstack/react-query'
import { moviesApi } from '@/lib/api'
import { toast } from 'sonner'

export function AddToFavorites({ movieId }: { movieId: number }) {
  // queryClient ใช้สำหรับ Invalidate Cache หลัง Mutation สำเร็จ
  const queryClient = useQueryClient()

  const { mutate, isPending } = useMutation({
    // mutationFn คือ function ที่เรียกเมื่อกด
    mutationFn: (id: number) => moviesApi.addFavorite(id),

    onSuccess: () => {
      // ✅ Mutation สำเร็จ → บอกให้ข้อมูล favorites เป็น "เก่า"
      // TanStack Query จะ Refetch ใหม่อัตโนมัติ
      queryClient.invalidateQueries({ queryKey: ['user', 'favorites'] })

      toast.success('เพิ่มในรายการโปรดแล้ว!')
    },

    onError: (error) => {
      // ❌ Mutation ล้มเหลว
      toast.error(`เกิดข้อผิดพลาด: ${error.message}`)
    },
  })

  return (
    <button
      onClick={() => mutate(movieId)}
      disabled={isPending}
      className="flex items-center gap-2 px-4 py-2 bg-red-500 text-white rounded"
    >
      {isPending ? (
        <>
          <Spinner className="w-4 h-4" />
          <span>กำลังเพิ่ม...</span>
        </>
      ) : (
        <>
          <HeartIcon className="w-4 h-4" />
          <span>เพิ่มในรายการโปรด</span>
        </>
      )}
    </button>
  )
}
```

**ตัวอย่าง Mutation สำหรับ Form — เพิ่ม Review:**

```typescript
// components/AddReviewForm.tsx
'use client'

import { useState } from 'react'
import { useMutation, useQueryClient } from '@tanstack/react-query'

interface ReviewInput {
  movieId: number
  rating: number
  comment: string
}

async function addReview(input: ReviewInput) {
  const res = await fetch(`/api/movies/${input.movieId}/reviews`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ rating: input.rating, comment: input.comment }),
  })
  if (!res.ok) throw new Error('ไม่สามารถเพิ่มรีวิวได้')
  return res.json()
}

export function AddReviewForm({ movieId }: { movieId: number }) {
  const [rating, setRating] = useState(5)
  const [comment, setComment] = useState('')
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: addReview,
    onSuccess: () => {
      // Refetch รีวิวของหนังเรื่องนี้
      queryClient.invalidateQueries({
        queryKey: ['movies', 'reviews', movieId],
      })
      // Reset form
      setComment('')
      setRating(5)
    },
  })

  const handleSubmit = () => {
    if (!comment.trim()) return
    mutation.mutate({ movieId, rating, comment })
  }

  return (
    <div className="space-y-4">
      <div>
        <label className="block text-sm font-medium mb-1">คะแนน</label>
        <StarRating value={rating} onChange={setRating} />
      </div>

      <div>
        <label className="block text-sm font-medium mb-1">ความคิดเห็น</label>
        <textarea
          value={comment}
          onChange={(e) => setComment(e.target.value)}
          rows={4}
          className="w-full border rounded-lg p-3 resize-none"
          placeholder="เขียนความคิดเห็นของคุณ..."
        />
      </div>

      {mutation.isError && (
        <p className="text-red-500 text-sm">{mutation.error.message}</p>
      )}

      <button
        onClick={handleSubmit}
        disabled={mutation.isPending || !comment.trim()}
        className="w-full py-2 bg-blue-600 text-white rounded-lg disabled:opacity-50"
      >
        {mutation.isPending ? 'กำลังส่ง...' : 'ส่งรีวิว'}
      </button>
    </div>
  )
}
```

---

### Cache Invalidation — กลยุทธ์อัปเดต Cache

หลัง Mutation สำเร็จ เราต้องบอกให้ TanStack Query รู้ว่าข้อมูลไหน "เก่า" แล้ว:

```typescript
const queryClient = useQueryClient()

// ─── กลยุทธ์ที่ 1: Invalidate ทั้ง Query ─────────────────────
// ข้อมูลทั้งหมดที่ขึ้นต้นด้วย ['movies'] จะถูก Refetch
queryClient.invalidateQueries({ queryKey: ['movies'] })

// ─── กลยุทธ์ที่ 2: Invalidate เฉพาะ ─────────────────────────
// เฉพาะ Query ที่ Key เป็น ['movies', 'popular'] เท่านั้น
queryClient.invalidateQueries({
  queryKey: ['movies', 'popular'],
  exact: true, // exact: true = ตรงทั้งหมด ไม่ใช่แค่ prefix
})

// ─── กลยุทธ์ที่ 3: Set Cache โดยตรง (ไม่ต้อง Refetch) ──────
// เหมาะเมื่อ Server ส่งข้อมูลอัปเดตกลับมาพร้อมกับ Response
const updateMovieMutation = useMutation({
  mutationFn: ({ id, data }: { id: number; data: Partial<Movie> }) =>
    moviesApi.update(id, data),

  onSuccess: (updatedMovie) => {
    // อัปเดต Cache โดยตรงจาก Response — ไม่ต้อง Refetch
    queryClient.setQueryData(
      ['movies', 'detail', updatedMovie.id],
      updatedMovie
    )
  },
})
```

---

### Optimistic Updates — ประสบการณ์ที่ลื่นไหล

**Optimistic Update** คือการอัปเดต UI **ทันที** ก่อนที่ Server จะตอบกลับ โดยสมมติว่าจะสำเร็จ และถ้าล้มเหลว ค่อย Rollback กลับ:

```
การทำงานแบบปกติ (Pessimistic):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ผู้ใช้กด "Like"
   ↓
รอ Server... (กดปุ่มไม่ได้ระหว่างนี้)
   ↓
Server ตอบกลับ OK
   ↓
UI อัปเดต ❤️ → ผู้ใช้รู้สึกช้า

การทำงานแบบ Optimistic:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ผู้ใช้กด "Like"
   ↓
UI อัปเดตทันที ❤️ (สมมติว่าสำเร็จ) ← ผู้ใช้รู้สึกเร็วมาก
   ↓
ส่ง Request ไป Server (เบื้องหลัง)
   ↓
ถ้าสำเร็จ → ไม่ต้องทำอะไร ✅
ถ้าล้มเหลว → Rollback กลับ ❌ + แจ้งผู้ใช้
```

**ตัวอย่าง Optimistic Update — Toggle Like:**

```typescript
// components/LikeButton.tsx
'use client'

import { useMutation, useQueryClient } from '@tanstack/react-query'

interface Movie {
  id: number
  title: string
  isLiked: boolean
  likeCount: number
}

export function LikeButton({ movie }: { movie: Movie }) {
  const queryClient = useQueryClient()

  const toggleLikeMutation = useMutation({
    mutationFn: (movieId: number) =>
      fetch(`/api/movies/${movieId}/like`, { method: 'POST' }).then(r => r.json()),

    // ─── onMutate: ทำงานทันทีก่อนส่ง Request ────────────────
    onMutate: async (movieId) => {
      // 1. ยกเลิก Request ที่กำลัง Refetch อยู่
      //    (ป้องกันการ Overwrite ข้อมูลที่เราอัปเดตแบบ Optimistic)
      await queryClient.cancelQueries({ queryKey: ['movies', 'detail', movieId] })

      // 2. บันทึกค่าเดิมไว้ (เผื่อต้อง Rollback)
      const previousMovie = queryClient.getQueryData<Movie>(['movies', 'detail', movieId])

      // 3. อัปเดต Cache ทันที (Optimistic)
      queryClient.setQueryData<Movie>(
        ['movies', 'detail', movieId],
        (old) => {
          if (!old) return old
          return {
            ...old,
            isLiked: !old.isLiked,
            likeCount: old.isLiked ? old.likeCount - 1 : old.likeCount + 1,
          }
        }
      )

      // 4. ส่งค่าเดิมกลับ เพื่อใช้ใน onError
      return { previousMovie }
    },

    // ─── onError: ถ้า Server ล้มเหลว ─────────────────────────
    onError: (error, movieId, context) => {
      // Rollback กลับค่าเดิม
      if (context?.previousMovie) {
        queryClient.setQueryData(
          ['movies', 'detail', movieId],
          context.previousMovie
        )
      }
    },

    // ─── onSettled: ทำงานทั้งสำเร็จและล้มเหลว ──────────────
    onSettled: (data, error, movieId) => {
      // Sync กับ Server เพื่อให้แน่ใจว่าข้อมูลถูกต้อง
      queryClient.invalidateQueries({ queryKey: ['movies', 'detail', movieId] })
    },
  })

  return (
    <button
      onClick={() => toggleLikeMutation.mutate(movie.id)}
      className={`flex items-center gap-2 px-4 py-2 rounded-full transition-colors ${
        movie.isLiked
          ? 'bg-red-100 text-red-600'
          : 'bg-gray-100 text-gray-600 hover:bg-red-50'
      }`}
    >
      <HeartIcon className={`w-5 h-5 ${movie.isLiked ? 'fill-current' : ''}`} />
      <span>{movie.likeCount}</span>
    </button>
  )
}
```

---

### useInfiniteQuery — Infinite Scroll

Infinite Scroll คือการโหลดข้อมูลเพิ่มเมื่อผู้ใช้ Scroll ลงมาถึงด้านล่าง:

```typescript
// hooks/useInfiniteMovies.ts
import { useInfiniteQuery } from '@tanstack/react-query'
import { moviesApi } from '@/lib/api'

export function useInfiniteMovies(genre?: string) {
  return useInfiniteQuery({
    queryKey: ['movies', 'infinite', genre],
    
    // pageParam คือค่าที่ TanStack Query ส่งมาให้ บอกว่าต้องโหลดหน้าไหน
    queryFn: ({ pageParam }) =>
      moviesApi.getPopular(pageParam as number),

    // หน้าเริ่มต้น
    initialPageParam: 1,

    // getNextPageParam: บอก TanStack Query ว่า "หน้าถัดไป" คืออะไร
    // ถ้า return undefined = ไม่มีหน้าถัดไปแล้ว
    getNextPageParam: (lastPage) => {
      if (lastPage.page < lastPage.total_pages) {
        return lastPage.page + 1
      }
      return undefined // จบแล้ว
    },
  })
}
```

```typescript
// components/InfiniteMovieList.tsx
'use client'

import { useEffect, useRef } from 'react'
import { useInfiniteMovies } from '@/hooks/useInfiniteMovies'

export function InfiniteMovieList() {
  const {
    data,
    fetchNextPage,    // โหลดหน้าถัดไป
    hasNextPage,      // มีหน้าถัดไปหรือไม่
    isFetchingNextPage, // กำลังโหลดหน้าถัดไปอยู่หรือไม่
    isLoading,
    isError,
  } = useInfiniteMovies()

  // ─── Intersection Observer สำหรับ Auto-Load ──────────────
  const loadMoreRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        const first = entries[0]
        // ถ้า Element สุดท้ายเข้ามาใน Viewport และยังมีข้อมูลต่อ
        if (first.isIntersecting && hasNextPage && !isFetchingNextPage) {
          fetchNextPage()
        }
      },
      { threshold: 0.1 } // Trigger เมื่อเห็น 10% ของ Element
    )

    if (loadMoreRef.current) {
      observer.observe(loadMoreRef.current)
    }

    return () => observer.disconnect()
  }, [fetchNextPage, hasNextPage, isFetchingNextPage])

  if (isLoading) return <MovieListSkeleton count={12} />
  if (isError) return <ErrorMessage />

  // data.pages คือ Array ของ Response แต่ละหน้า
  // ต้อง flatMap เพื่อรวม movies ทั้งหมดเข้าด้วยกัน
  const movies = data?.pages.flatMap((page) => page.results) ?? []

  return (
    <div>
      <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
        {movies.map((movie) => (
          <MovieCard key={movie.id} movie={movie} />
        ))}
      </div>

      {/* ─── Trigger Element ─── */}
      <div ref={loadMoreRef} className="py-8 flex justify-center">
        {isFetchingNextPage ? (
          <div className="flex items-center gap-2 text-gray-500">
            <Spinner className="w-5 h-5" />
            <span>กำลังโหลดเพิ่มเติม...</span>
          </div>
        ) : hasNextPage ? (
          <span className="text-gray-400 text-sm">Scroll ลงเพื่อดูเพิ่มเติม</span>
        ) : (
          <span className="text-gray-400 text-sm">แสดงผลครบทุกรายการแล้ว</span>
        )}
      </div>
    </div>
  )
}
```

---

### Custom Hook — รวม Query Logic ไว้ที่เดียว

วิธีที่ดีที่สุดคือสร้าง Custom Hook เพื่อห่อ Query Logic ไว้:

```typescript
// hooks/useMovies.ts
// ← รวม Query ทั้งหมดที่เกี่ยวกับ Movie ไว้ที่นี่

import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'
import { moviesApi } from '@/lib/api'

// Query Keys จัดการที่เดียว — ง่ายต่อการแก้ไขและป้องกัน Typo
export const movieKeys = {
  all: ['movies'] as const,
  popular: (page: number) => ['movies', 'popular', page] as const,
  detail: (id: number) => ['movies', 'detail', id] as const,
  search: (query: string) => ['movies', 'search', query] as const,
  favorites: ['movies', 'favorites'] as const,
}

// ─── usePopularMovies ─────────────────────────────────────────
export function usePopularMovies(page = 1) {
  return useQuery({
    queryKey: movieKeys.popular(page),
    queryFn: () => moviesApi.getPopular(page),
    staleTime: 5 * 60 * 1000, // 5 นาที
    placeholderData: (prev) => prev,
  })
}

// ─── useMovieDetail ───────────────────────────────────────────
export function useMovieDetail(id: number) {
  return useQuery({
    queryKey: movieKeys.detail(id),
    queryFn: () => moviesApi.getById(id),
    staleTime: 10 * 60 * 1000, // 10 นาที
    enabled: id > 0, // ไม่ Fetch ถ้า id ไม่ถูกต้อง
  })
}

// ─── useSearchMovies ──────────────────────────────────────────
export function useSearchMovies(query: string) {
  return useQuery({
    queryKey: movieKeys.search(query),
    queryFn: () => moviesApi.search(query),
    enabled: query.trim().length >= 2, // ค้นหาเมื่อพิมพ์อย่างน้อย 2 ตัวอักษร
  })
}

// ─── useAddFavorite ───────────────────────────────────────────
export function useAddFavorite() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: moviesApi.addFavorite,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: movieKeys.favorites })
    },
  })
}
```

**การใช้งานใน Component — สะอาดมาก:**

```typescript
// components/MovieDetailPage.tsx
'use client'

import { useMovieDetail, useAddFavorite } from '@/hooks/useMovies'

export function MovieDetailPage({ movieId }: { movieId: number }) {
  const { data: movie, isLoading } = useMovieDetail(movieId)
  const addFavorite = useAddFavorite()

  if (isLoading) return <MovieDetailSkeleton />
  if (!movie) return <NotFound />

  return (
    <div>
      <h1 className="text-3xl font-bold">{movie.title}</h1>
      <p className="text-gray-600 mt-2">{movie.overview}</p>

      <button
        onClick={() => addFavorite.mutate(movie.id)}
        disabled={addFavorite.isPending}
      >
        {addFavorite.isPending ? 'กำลังเพิ่ม...' : 'เพิ่มในรายการโปรด'}
      </button>
    </div>
  )
}
// Component สะอาด ไม่มี fetch() ไม่มี useState ไม่มี useEffect
// ← ทุกอย่างอยู่ใน Hook ที่ Test ได้ง่าย
```

---

### TanStack Query DevTools — เห็น Cache ชัดเจน

DevTools ช่วยให้คุณเห็นสถานะ Cache ได้แบบ Real-time:

```
เปิด DevTools โดยคลิกไอคอน TanStack Query มุมล่างขวาของหน้า
จะเห็น Panel แสดง:

┌─────────────────────────────────────────────────────────┐
│  TanStack Query Devtools                                │
├─────────────────────────────────────────────────────────┤
│  ● movies > popular > 1    fresh    2 observers  5m    │
│  ● movies > detail > 550   stale    1 observer   8m    │
│  ● user > favorites        fresh    1 observer   1m    │
└─────────────────────────────────────────────────────────┘

สี:
🟢 fresh   — ข้อมูลสดใหม่ ไม่ต้อง Refetch
🟡 stale   — ข้อมูลเก่า จะ Refetch เมื่อมีโอกาส
🔵 fetching — กำลัง Fetch อยู่
🔴 error   — Fetch ล้มเหลว
⚫ inactive — ไม่มี Component ใช้งาน (รอ gcTime)
```

---

## สรุป Part 1

ในส่วนนี้คุณได้เรียนรู้:

```
✅ HTTP Protocol — Methods, Status Codes, Headers
   └─ GET/POST/PUT/PATCH/DELETE ใช้เมื่อไหร่
   └─ 2xx/4xx/5xx แปลว่าอะไร
   └─ Authorization, Content-Type Header

✅ REST API Design — หลักการออกแบบ URL ที่ดี
   └─ Resource-based URL
   └─ Query Parameters สำหรับ Filter/Sort

✅ Bruno — ทดสอบ API ก่อนเขียนโค้ด
   └─ สร้าง Collection และ Request
   └─ เก็บไว้ใน Git ร่วมกับทีม

✅ TanStack Query v5 — Data Fetching มืออาชีพ
   └─ useQuery: ดึงข้อมูล + Cache + Loading/Error State
   └─ Query Key: หัวใจของระบบ Cache
   └─ staleTime vs gcTime: ควบคุมพฤติกรรม Cache
   └─ useMutation: สร้าง/แก้ไข/ลบข้อมูล
   └─ Cache Invalidation: บอกให้ Refetch หลัง Mutation
   └─ Optimistic Updates: UI ลื่นไหล ไม่ต้องรอ Server
   └─ useInfiniteQuery: Infinite Scroll
   └─ Custom Hooks: จัดการ Query Logic อย่างเป็นระบบ
```

---

## แบบฝึกหัด Part 1

### แบบฝึกหัดที่ 1: สำรวจ API ด้วย Bruno

1. สมัครรับ API Key จาก [The Movie Database (TMDB)](https://www.themoviedb.org/settings/api)
2. สร้าง Bruno Collection ชื่อ "TMDB API"
3. สร้าง Request ต่อไปนี้:
   - `GET /3/movie/popular` — ดูหนัง Popular (ใส่ api_key เป็น Query Param)
   - `GET /3/movie/550` — ดูรายละเอียดหนัง Fight Club
   - `GET /3/search/movie?query=avengers` — ค้นหาหนัง
   - `GET /3/genre/movie/list` — ดูรายการ Genre

4. ตอบคำถาม:
   - Response ของ `/3/movie/popular` มีโครงสร้างอย่างไร?
   - `poster_path` ต้องนำไป Combine กับ URL ใดเพื่อแสดงรูป?
   - `/3/movie/99999` (ID ที่ไม่มีจริง) ให้ Status Code อะไร?

---

### แบบฝึกหัดที่ 2: ตั้งค่า TanStack Query

1. สร้างโปรเจกต์ Next.js 15 ใหม่ หรือใช้โปรเจกต์ Movie Finder จาก Unit 4
2. ติดตั้ง `@tanstack/react-query` และ `@tanstack/react-query-devtools`
3. สร้างไฟล์ `app/providers.tsx` และ wrap `RootLayout` ด้วย `QueryClientProvider`
4. ตรวจสอบว่า DevTools ปรากฏที่มุมล่างขวาของหน้าเว็บ

---

### แบบฝึกหัดที่ 3: useQuery — ดึงหนัง Popular

สร้าง Component `PopularMovies` ที่:

1. ใช้ `useQuery` ดึงหนัง Popular จาก TMDB API
2. แสดง Loading State ด้วย Skeleton Card (ไม่ใช่แค่ข้อความ)
3. แสดง Error State พร้อมปุ่ม "ลองใหม่"
4. แสดงการ์ดหนังด้วย Poster, ชื่อ และคะแนน
5. เพิ่ม Pagination (หน้า 1 ถึง 5)

---

### แบบฝึกหัดที่ 4: useMutation — บันทึก Favorite

1. สร้าง Custom Hook `useToggleFavorite` ที่ใช้ `useMutation`
2. เพิ่มปุ่ม ❤️ ในทุก Movie Card
3. เมื่อกด:
   - เพิ่ม/ลบ movieId ใน localStorage (ใช้เป็น "ฐานข้อมูล" ชั่วคราว)
   - ทำ Optimistic Update: ไอคอนเปลี่ยนสีทันที
   - Invalidate Query `['favorites']` เมื่อสำเร็จ
4. สร้างหน้า `/favorites` ที่ดึงและแสดงหนัง Favorite ของผู้ใช้

---

### แบบฝึกหัดที่ 5: useInfiniteQuery — Infinite Scroll

แทนที่ Pagination ด้วย Infinite Scroll:

1. ใช้ `useInfiniteQuery` ดึงหนัง Popular
2. ใช้ `Intersection Observer` ตรวจว่า Scroll ถึง Element สุดท้าย
3. โหลดหน้าถัดไปอัตโนมัติ
4. แสดง Loading Indicator ระหว่างโหลดหน้าถัดไป
5. แสดงข้อความ "แสดงผลครบทุกรายการแล้ว" เมื่อถึงหน้าสุดท้าย

---

## สิ่งที่จะเรียนใน Part 2

```
Part 2 จะครอบคลุม:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

5.3 Authentication ด้วย Auth.js v5
    └─ JWT และการเก็บ Token แบบปลอดภัย
    └─ OAuth 2.0 — Login ด้วย Google
    └─ Session Management ฝั่ง Server และ Client
    └─ Protected Routes ด้วย Middleware

5.4 Supabase — Backend as a Service
    └─ ตั้งค่า Project และเชื่อมต่อกับ Next.js
    └─ CRUD Operations ผ่าน Supabase Client
    └─ Row Level Security (RLS)
    └─ Realtime Subscriptions

5.5 Web Security สำหรับ Front-End Developer
    └─ XSS, CORS, Environment Variables
    └─ Content Security Policy เบื้องต้น
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

*ปรับปรุงล่าสุด: พฤษภาคม 2026*
