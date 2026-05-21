# หน่วยที่ 4: React และ Next.js — Framework หลัก (Part 2)

> **หลักสูตร:** หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
> **ระดับ:** ปานกลาง (Intermediate)
> **ระยะเวลา:** 1.5 สัปดาห์ (Part 2 จาก 3 สัปดาห์)
> **ต่อจาก:** Part 1 — Component, Hooks, JSX, Next.js Routing
> **ปรับปรุงล่าสุด:** พฤษภาคม 2026

---

## วัตถุประสงค์การเรียนรู้ (Part 2)

เมื่อเรียนจบ Part 2 ผู้เรียนจะสามารถ:

1. อธิบายความแตกต่างระหว่าง Server Component และ Client Component และเลือกใช้ได้ถูกต้อง
2. ใช้ React Context API สำหรับ State ขนาดเล็กที่แชร์ใน Subtree ได้
3. สร้างและใช้งาน Zustand Store สำหรับ Global State Management ได้
4. เขียน Server Actions สำหรับ Form Submission และ Data Mutation ได้
5. ใช้ Rendering Strategies (SSR, SSG, ISR) ได้เหมาะสมกับแต่ละ Use Case
6. ใช้ `use()` Hook และ Suspense สำหรับ Async Data ได้
7. ทำ Performance Optimization ด้วย Lazy Loading, Bundle Analysis และ Core Web Vitals ได้
8. พัฒนาโปรเจกต์ Movie Finder ด้วย Next.js 15 + TypeScript + Tailwind + shadcn/ui ครบถ้วนได้

---

## 4.7 React Server Components (RSC) — การเปลี่ยนแปลงครั้งใหญ่ของ React

### ปัญหาที่ RSC แก้

ก่อน React Server Components ทุก Component ทำงานใน Browser ทั้งหมด:

```
ปัญหาของ Client-Only Rendering (แบบเก่า):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. ผู้ใช้เปิดเว็บ
2. Browser ดาวน์โหลด HTML เปล่า
3. Browser ดาวน์โหลด JavaScript Bundle (อาจใหญ่หลาย MB)
4. JavaScript รัน → ดึงข้อมูลจาก API
5. แสดงผล

ปัญหา:
❌ JavaScript Bundle ใหญ่ → โหลดช้า
❌ ผู้ใช้เห็นหน้าเปล่านาน (Poor LCP)
❌ Crawler (SEO) อ่าน HTML เปล่าไม่พบเนื้อหา
❌ Secret เช่น API Key ต้องส่งไป Browser ก็จะโดนอ่านได้
```

**RSC แก้ปัญหาด้วยการ Render บน Server:**

```
Server Component (ทำงานบน Server):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Server รับ Request
2. Server เรียก Database / API โดยตรง (ปลอดภัย — ไม่ผ่าน Browser)
3. Server Render Component เป็น HTML สำเร็จรูป
4. ส่ง HTML ไปให้ Browser แสดงผลทันที
5. ไม่ส่ง JavaScript ของ Component นั้นไปเลย

ผลลัพธ์:
✅ JavaScript Bundle เล็กลงมาก
✅ ผู้ใช้เห็นเนื้อหาเร็วขึ้น
✅ SEO ดีขึ้น (Crawler อ่าน HTML ได้ทันที)
✅ เข้าถึง Database / Secret ได้โดยตรงอย่างปลอดภัย
```

---

### Server Component vs Client Component

ใน Next.js 15 ทุก Component เป็น **Server Component โดย Default** — ต้องประกาศ `'use client'` ถ้าต้องการให้ทำงานใน Browser

```
โมเดลการทำงาน:

Server (ไม่มี JavaScript ไปที่ Browser)    Client (มี JavaScript)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Server Component                           Client Component
━━━━━━━━━━━━━━━━                          ━━━━━━━━━━━━━━━━━
✅ async/await โดยตรง                     ✅ useState
✅ เรียก DB / File System โดยตรง         ✅ useEffect
✅ ซ่อน Secret ได้ (API Key, Token)       ✅ useRef
✅ ไม่ส่ง JS ไป Client → Bundle เล็ก     ✅ Event Handlers (onClick ฯลฯ)
                                          ✅ Browser APIs (localStorage ฯลฯ)
❌ ไม่มี useState, useEffect             ✅ Real-time Updates
❌ ไม่มี Event Handlers                  
❌ ไม่มี Browser APIs                    ❌ ไม่เรียก DB โดยตรง
                                          ❌ ไม่ซ่อน Secret
```

**หลักการเลือกว่าควรเป็น Server หรือ Client:**

```
ถามตัวเองก่อนสร้าง Component ทุกครั้ง:

Q: Component นี้ต้องการ Interactivity (คลิก, พิมพ์, hover) ไหม?
   → ใช่ → Client Component ('use client')

Q: Component นี้ต้องการ State หรือ Effect ไหม?
   → ใช่ → Client Component ('use client')

Q: Component นี้แค่แสดงข้อมูลที่ดึงมาจาก Server ไหม?
   → ใช่ → Server Component (Default — ไม่ต้องทำอะไร)

Q: Component นี้เข้าถึง Database, File, หรือ Secret ไหม?
   → ใช่ → Server Component (Default)
```

---

### ตัวอย่างจริง: Server Component

```tsx
// app/products/page.tsx — Server Component (Default, ไม่มี 'use client')

import { ProductCard } from '@/components/ProductCard'
import { db } from '@/lib/db'  // เรียก Database โดยตรงได้เลย!

// async function ได้เลยใน Server Component
export default async function ProductsPage() {
  // ดึงข้อมูลจาก Database โดยตรง — ปลอดภัย, ไม่ผ่าน API
  // API_SECRET ไม่ถูกส่งไป Browser เลย
  const products = await db.product.findMany({
    where: { isActive: true },
    orderBy: { createdAt: 'desc' },
    take: 20,
  })

  return (
    <div>
      <h1 className="text-3xl font-bold mb-6">สินค้าทั้งหมด</h1>

      {products.length === 0 ? (
        <p className="text-gray-500 text-center py-12">ยังไม่มีสินค้า</p>
      ) : (
        <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
          {products.map(product => (
            // ProductCard เป็น Server Component ด้วย — ไม่ส่ง JS ไป Client
            <ProductCard key={product.id} product={product} />
          ))}
        </div>
      )}
    </div>
  )
}
```

```tsx
// components/ProductCard.tsx — Server Component แสดงผลอย่างเดียว
import Image from 'next/image'
import Link from 'next/link'
import { AddToCartButton } from './AddToCartButton' // Client Component

interface Product {
  id: number
  name: string
  price: number
  imageUrl: string
  category: string
}

export function ProductCard({ product }: { product: Product }) {
  return (
    <div className="border rounded-xl overflow-hidden shadow-sm hover:shadow-md transition">
      <Link href={`/products/${product.id}`}>
        <Image
          src={product.imageUrl}
          alt={product.name}
          width={400}
          height={300}
          className="w-full h-48 object-cover"
        />
        <div className="p-4">
          <p className="text-xs text-gray-500 uppercase tracking-wide mb-1">
            {product.category}
          </p>
          <h3 className="font-semibold text-gray-900 mb-2">{product.name}</h3>
          <p className="text-xl font-bold text-blue-600">
            ฿{product.price.toLocaleString('th-TH')}
          </p>
        </div>
      </Link>

      {/* Server Component ห่อ Client Component ได้ — ส่ง Data ผ่าน Props */}
      <div className="px-4 pb-4">
        <AddToCartButton productId={product.id} productName={product.name} />
      </div>
    </div>
  )
}
```

```tsx
// components/AddToCartButton.tsx — Client Component (ต้องการ onClick)
'use client'

import { useState } from 'react'

interface AddToCartButtonProps {
  productId: number
  productName: string
}

export function AddToCartButton({ productId, productName }: AddToCartButtonProps) {
  const [isAdding, setIsAdding] = useState(false)
  const [added, setAdded] = useState(false)

  const handleAddToCart = async () => {
    setIsAdding(true)
    // เรียก API เพื่อเพิ่มสินค้า
    await fetch('/api/cart', {
      method: 'POST',
      body: JSON.stringify({ productId }),
      headers: { 'Content-Type': 'application/json' },
    })
    setIsAdding(false)
    setAdded(true)
    setTimeout(() => setAdded(false), 2000) // Reset หลัง 2 วิ
  }

  return (
    <button
      onClick={handleAddToCart}
      disabled={isAdding}
      className={`w-full py-2 rounded-lg font-medium transition ${
        added
          ? 'bg-green-500 text-white'
          : 'bg-blue-600 text-white hover:bg-blue-700'
      } disabled:opacity-50`}
    >
      {isAdding ? 'กำลังเพิ่ม...' : added ? '✓ เพิ่มแล้ว!' : 'เพิ่มลงตะกร้า'}
    </button>
  )
}
```

---

### กฎการซ้อน Server/Client Component

```
กฎสำคัญที่ต้องจำ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Server Component สามารถ Import Client Component ได้
✅ Client Component สามารถรับ Server Component เป็น children ได้
❌ Client Component ไม่สามารถ Import Server Component ได้

ตัวอย่างที่ถูกต้อง:

// Server ห่อ Client (✅)
// ServerComponent.tsx
import { ClientButton } from './ClientButton' // ✅
export default function ServerPage() {
  return <div><ClientButton /></div>
}

// Client รับ Server เป็น children (✅)
// ClientWrapper.tsx ('use client')
export function ClientWrapper({ children }: { children: React.ReactNode }) {
  return <div onClick={() => {}}>{children}</div>
}

// app/page.tsx (Server)
import { ClientWrapper } from './ClientWrapper'
import { ServerContent } from './ServerContent' // Server Component
export default function Page() {
  return (
    <ClientWrapper>
      <ServerContent /> {/* ✅ ส่งเป็น children ได้ */}
    </ClientWrapper>
  )
}
```

---

### `use()` Hook — Async Data ใน React 19

**`use()`** เป็น Hook ใหม่ใน React 19 สำหรับอ่านค่าจาก Promise หรือ Context ใน Client Component:

```tsx
// app/products/[id]/page.tsx — Server Component
import { Suspense } from 'react'
import { ProductDetail } from '@/components/ProductDetail'

export default function ProductPage({ params }: { params: Promise<{ id: string }> }) {
  // สร้าง Promise แต่ยังไม่ await — ส่งไปให้ Client Component จัดการ
  const productPromise = fetchProduct(params)

  return (
    // Suspense แสดง Fallback ขณะ Promise ยังโหลดอยู่
    <Suspense fallback={<ProductDetailSkeleton />}>
      <ProductDetail productPromise={productPromise} />
    </Suspense>
  )
}

async function fetchProduct(params: Promise<{ id: string }>) {
  const { id } = await params
  const res = await fetch(`https://api.example.com/products/${id}`)
  return res.json()
}
```

```tsx
// components/ProductDetail.tsx — Client Component
'use client'

import { use } from 'react'  // use() Hook

interface Product {
  id: number
  name: string
  price: number
  description: string
}

export function ProductDetail({
  productPromise,
}: {
  productPromise: Promise<Product>
}) {
  // use() อ่านค่าจาก Promise — หากยังโหลดไม่เสร็จ Component จะ Suspend
  // (แสดง Fallback ของ <Suspense> แทน)
  const product = use(productPromise)

  return (
    <div className="max-w-2xl mx-auto">
      <h1 className="text-3xl font-bold">{product.name}</h1>
      <p className="text-2xl text-blue-600 my-4">
        ฿{product.price.toLocaleString('th-TH')}
      </p>
      <p className="text-gray-600 leading-relaxed">{product.description}</p>
    </div>
  )
}

function ProductDetailSkeleton() {
  return (
    <div className="max-w-2xl mx-auto animate-pulse">
      <div className="h-9 bg-gray-200 rounded w-3/4 mb-4" />
      <div className="h-8 bg-gray-200 rounded w-1/4 my-4" />
      <div className="space-y-2">
        <div className="h-4 bg-gray-200 rounded" />
        <div className="h-4 bg-gray-200 rounded w-5/6" />
        <div className="h-4 bg-gray-200 rounded w-4/6" />
      </div>
    </div>
  )
}
```

**Suspense กับหลาย Component พร้อมกัน:**

```tsx
// app/dashboard/page.tsx — Parallel Data Fetching
import { Suspense } from 'react'

export default function DashboardPage() {
  // สร้าง Promise พร้อมกันทั้งหมด (Parallel — ไม่รอกัน)
  const statsPromise = fetchStats()
  const ordersPromise = fetchRecentOrders()
  const usersPromise = fetchTopUsers()

  return (
    <div className="grid grid-cols-12 gap-6">
      {/* แต่ละส่วนโหลดอิสระจากกัน */}
      <div className="col-span-12">
        <Suspense fallback={<StatsSkeleton />}>
          <StatsCards statsPromise={statsPromise} />
        </Suspense>
      </div>

      <div className="col-span-8">
        <Suspense fallback={<TableSkeleton rows={5} />}>
          <RecentOrders ordersPromise={ordersPromise} />
        </Suspense>
      </div>

      <div className="col-span-4">
        <Suspense fallback={<ListSkeleton items={5} />}>
          <TopUsers usersPromise={usersPromise} />
        </Suspense>
      </div>
    </div>
  )
}
```

---

## 4.8 State Management — Context API และ Zustand

### เมื่อไหร่ควรใช้ State Management อะไร

ก่อนเลือก ต้องเข้าใจว่าแต่ละเครื่องมือเหมาะกับงานที่ต่างกัน:

```
State ระดับต่าง ๆ และเครื่องมือที่เหมาะสม:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Local State (ใน Component เดียว)
  └── useState, useReducer
  └── ตัวอย่าง: เปิด/ปิด Modal, ค่าใน Form, Tab ที่เลือก

Shared State ใน Subtree (Component ที่เกี่ยวข้องกัน)
  └── React Context API
  └── ตัวอย่าง: Theme (Dark/Light), ภาษา, User Preferences

Global State (ทั้งแอป)
  └── Zustand
  └── ตัวอย่าง: ข้อมูล User ที่ Login, ตะกร้าสินค้า, Notification

Server State (ข้อมูลจาก API)
  └── TanStack Query (เรียนใน Unit 5)
  └── ตัวอย่าง: รายการสินค้า, Profile, Orders
```

---

### React Context API — State ใน Subtree

**Context API** แก้ปัญหา **Prop Drilling** (พรอป ดริลลิง) — การส่ง Props ผ่านหลายชั้น Component ที่ไม่ได้ใช้ข้อมูลนั้น:

```
ปัญหา Prop Drilling (ก่อนใช้ Context):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

App (มี user)
  └── Layout (ส่ง user ต่อ → ไม่ได้ใช้เอง)
        └── Sidebar (ส่ง user ต่อ → ไม่ได้ใช้เอง)
              └── UserMenu (ใช้ user — แต่ต้องรับผ่าน 3 ชั้น)

หลังใช้ Context:
━━━━━━━━━━━━━━━
App (Provider ครอบ)
  └── Layout
        └── Sidebar
              └── UserMenu (ดึง user จาก Context ได้เลย — ข้ามชั้น)
```

**ตัวอย่างจริง: Theme Context:**

```tsx
// contexts/ThemeContext.tsx
'use client'

import { createContext, useContext, useState, useCallback } from 'react'

type Theme = 'light' | 'dark'

interface ThemeContextValue {
  theme: Theme
  toggleTheme: () => void
  isDark: boolean
}

// 1. สร้าง Context
const ThemeContext = createContext<ThemeContextValue | null>(null)

// 2. สร้าง Provider Component
export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<Theme>('light')

  const toggleTheme = useCallback(() => {
    setTheme(prev => {
      const next = prev === 'light' ? 'dark' : 'light'
      // ใส่ class บน <html> เพื่อให้ Tailwind dark: ทำงานได้
      document.documentElement.classList.toggle('dark', next === 'dark')
      return next
    })
  }, [])

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme, isDark: theme === 'dark' }}>
      {children}
    </ThemeContext.Provider>
  )
}

// 3. สร้าง Custom Hook สำหรับใช้งาน Context (ป้องกันลืม Provider)
export function useTheme(): ThemeContextValue {
  const context = useContext(ThemeContext)
  if (!context) {
    // Error ที่ชัดเจน — ช่วย Debug ง่าย
    throw new Error('useTheme ต้องใช้ภายใน <ThemeProvider>')
  }
  return context
}
```

```tsx
// app/layout.tsx — ครอบ Provider ที่ Root
import { ThemeProvider } from '@/contexts/ThemeContext'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="th">
      <body>
        <ThemeProvider>
          {children}
        </ThemeProvider>
      </body>
    </html>
  )
}
```

```tsx
// components/ThemeToggle.tsx — ใช้ Context ได้เลยไม่ต้องรับ Props
'use client'

import { useTheme } from '@/contexts/ThemeContext'

export function ThemeToggle() {
  const { theme, toggleTheme } = useTheme()

  return (
    <button
      onClick={toggleTheme}
      className="p-2 rounded-lg border hover:bg-gray-100 dark:hover:bg-gray-800 transition"
      aria-label={`สลับเป็น ${theme === 'light' ? 'Dark' : 'Light'} Mode`}
    >
      {theme === 'light' ? '🌙' : '☀️'}
    </button>
  )
}
```

**ตัวอย่างจริง: Auth Context:**

```tsx
// contexts/AuthContext.tsx
'use client'

import { createContext, useContext, useState, useEffect } from 'react'

interface User {
  id: string
  name: string
  email: string
  role: 'user' | 'admin'
  avatarUrl?: string
}

interface AuthContextValue {
  user: User | null
  isLoading: boolean
  isAuthenticated: boolean
  login: (email: string, password: string) => Promise<void>
  logout: () => Promise<void>
}

const AuthContext = createContext<AuthContextValue | null>(null)

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null)
  const [isLoading, setIsLoading] = useState(true) // เริ่มต้น true — กำลังตรวจสอบ session

  // ตรวจสอบ Session เมื่อแอปเปิด
  useEffect(() => {
    async function checkSession() {
      try {
        const res = await fetch('/api/auth/session')
        if (res.ok) {
          const data = await res.json()
          setUser(data.user)
        }
      } catch {
        // ไม่มี Session — ปกติ
      } finally {
        setIsLoading(false)
      }
    }
    checkSession()
  }, [])

  const login = async (email: string, password: string) => {
    const res = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password }),
    })

    if (!res.ok) {
      const error = await res.json()
      throw new Error(error.message || 'เข้าสู่ระบบไม่สำเร็จ')
    }

    const data = await res.json()
    setUser(data.user)
  }

  const logout = async () => {
    await fetch('/api/auth/logout', { method: 'POST' })
    setUser(null)
  }

  return (
    <AuthContext.Provider
      value={{
        user,
        isLoading,
        isAuthenticated: !!user,
        login,
        logout,
      }}
    >
      {children}
    </AuthContext.Provider>
  )
}

export function useAuth(): AuthContextValue {
  const context = useContext(AuthContext)
  if (!context) throw new Error('useAuth ต้องใช้ภายใน <AuthProvider>')
  return context
}
```

```tsx
// components/Navbar.tsx — ใช้ Auth Context
'use client'

import { useAuth } from '@/contexts/AuthContext'
import Image from 'next/image'
import Link from 'next/link'

export function Navbar() {
  const { user, isLoading, isAuthenticated, logout } = useAuth()

  return (
    <nav className="border-b bg-white dark:bg-gray-900">
      <div className="container mx-auto px-4 h-16 flex items-center justify-between">
        <Link href="/" className="text-xl font-bold">
          MyApp
        </Link>

        <div className="flex items-center gap-4">
          {isLoading ? (
            // แสดง Skeleton ขณะตรวจสอบ Session
            <div className="h-8 w-24 bg-gray-200 rounded animate-pulse" />
          ) : isAuthenticated && user ? (
            <>
              <span className="text-sm text-gray-600">สวัสดี, {user.name}</span>
              {user.avatarUrl && (
                <Image
                  src={user.avatarUrl}
                  alt={user.name}
                  width={32}
                  height={32}
                  className="rounded-full"
                />
              )}
              <button
                onClick={logout}
                className="text-sm text-red-500 hover:text-red-700"
              >
                ออกจากระบบ
              </button>
            </>
          ) : (
            <Link
              href="/login"
              className="px-4 py-2 bg-blue-600 text-white rounded-lg text-sm hover:bg-blue-700"
            >
              เข้าสู่ระบบ
            </Link>
          )}
        </div>
      </div>
    </nav>
  )
}
```

> **⚠️ ข้อควรระวังของ Context:** ทุกครั้งที่ Context Value เปลี่ยน ทุก Component ที่ `useContext` จะ Re-render ทั้งหมด ดังนั้นไม่ควรใส่ข้อมูลที่เปลี่ยนบ่อยลงใน Context เดียวกัน — ให้แยก Context ตามความถี่การเปลี่ยนแปลง

---

### Zustand — Global State Management

**Zustand** (ซุสแตนด์) คือ State Management Library ที่เรียบง่าย เบา และทรงพลัง เป็นมาตรฐานที่นิยมใช้ในปี 2026 แทนที่ Redux

**เปรียบเทียบ Zustand กับ Redux:**

```
Redux (แบบเก่า)                    Zustand (แบบใหม่)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
- ต้องเขียน Action, Reducer, Store   - เขียน Store ไฟล์เดียวจบ
- Boilerplate code เยอะ             - โค้ดกระชับ อ่านง่าย
- ต้องใช้ Provider ครอบ             - ไม่ต้องใช้ Provider
- Bundle ขนาดใหญ่                   - Bundle เล็กมาก (~1KB)
- Learning Curve สูง                - เรียนรู้ได้ภายในชั่วโมงเดียว
```

**ติดตั้ง Zustand:**

```bash
pnpm add zustand
```

**สร้าง Store แรก — ตะกร้าสินค้า:**

```tsx
// stores/cartStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'  // บันทึกลง localStorage อัตโนมัติ

interface CartItem {
  id: number
  name: string
  price: number
  quantity: number
  imageUrl: string
}

interface CartStore {
  // State
  items: CartItem[]
  isOpen: boolean

  // Computed (คำนวณจาก State)
  totalItems: () => number
  totalPrice: () => number

  // Actions (เปลี่ยน State)
  addItem: (item: Omit<CartItem, 'quantity'>) => void
  removeItem: (id: number) => void
  updateQuantity: (id: number, quantity: number) => void
  clearCart: () => void
  toggleCart: () => void
}

export const useCartStore = create<CartStore>()(
  // persist — บันทึก State ลง localStorage โดยอัตโนมัติ
  persist(
    (set, get) => ({
      // --- State เริ่มต้น ---
      items: [],
      isOpen: false,

      // --- Computed Values ---
      totalItems: () => get().items.reduce((sum, item) => sum + item.quantity, 0),
      totalPrice: () => get().items.reduce((sum, item) => sum + item.price * item.quantity, 0),

      // --- Actions ---
      addItem: (newItem) => {
        set(state => {
          const existingItem = state.items.find(item => item.id === newItem.id)

          if (existingItem) {
            // สินค้ามีอยู่แล้ว — เพิ่มจำนวน
            return {
              items: state.items.map(item =>
                item.id === newItem.id
                  ? { ...item, quantity: item.quantity + 1 }
                  : item
              ),
            }
          }

          // สินค้าใหม่ — เพิ่มเข้า Cart
          return {
            items: [...state.items, { ...newItem, quantity: 1 }],
          }
        })
      },

      removeItem: (id) => {
        set(state => ({
          items: state.items.filter(item => item.id !== id),
        }))
      },

      updateQuantity: (id, quantity) => {
        if (quantity <= 0) {
          // ลบออกถ้าจำนวนเป็น 0
          get().removeItem(id)
          return
        }
        set(state => ({
          items: state.items.map(item =>
            item.id === id ? { ...item, quantity } : item
          ),
        }))
      },

      clearCart: () => set({ items: [] }),

      toggleCart: () => set(state => ({ isOpen: !state.isOpen })),
    }),
    {
      name: 'cart-storage',  // Key ใน localStorage
      // เลือกว่าจะบันทึก State อะไร (isOpen ไม่ต้องบันทึก)
      partialize: (state) => ({ items: state.items }),
    }
  )
)
```

**ใช้งาน Store ใน Component:**

```tsx
// components/AddToCartButton.tsx
'use client'

import { useCartStore } from '@/stores/cartStore'

interface Product {
  id: number
  name: string
  price: number
  imageUrl: string
}

export function AddToCartButton({ product }: { product: Product }) {
  // ดึงเฉพาะ Action ที่ต้องการ — ไม่ Re-render เมื่อ items เปลี่ยน
  const addItem = useCartStore(state => state.addItem)

  return (
    <button
      onClick={() => addItem(product)}
      className="w-full py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition"
    >
      เพิ่มลงตะกร้า
    </button>
  )
}
```

```tsx
// components/CartIcon.tsx
'use client'

import { useCartStore } from '@/stores/cartStore'

export function CartIcon() {
  // ดึงเฉพาะสิ่งที่ต้องการ — Component นี้ Re-render เฉพาะเมื่อ totalItems เปลี่ยน
  const totalItems = useCartStore(state => state.totalItems())
  const toggleCart = useCartStore(state => state.toggleCart)

  return (
    <button
      onClick={toggleCart}
      className="relative p-2 hover:bg-gray-100 rounded-lg"
    >
      🛒
      {totalItems > 0 && (
        <span className="absolute -top-1 -right-1 bg-red-500 text-white text-xs rounded-full h-5 w-5 flex items-center justify-center">
          {totalItems > 99 ? '99+' : totalItems}
        </span>
      )}
    </button>
  )
}
```

```tsx
// components/CartDrawer.tsx
'use client'

import { useCartStore } from '@/stores/cartStore'
import Image from 'next/image'

export function CartDrawer() {
  const { items, isOpen, totalPrice, removeItem, updateQuantity, clearCart, toggleCart } =
    useCartStore()

  if (!isOpen) return null

  return (
    <>
      {/* Backdrop */}
      <div
        className="fixed inset-0 bg-black/40 z-40"
        onClick={toggleCart}
      />

      {/* Drawer */}
      <div className="fixed right-0 top-0 h-full w-full max-w-md bg-white shadow-2xl z-50 flex flex-col">
        {/* Header */}
        <div className="flex items-center justify-between p-4 border-b">
          <h2 className="text-lg font-bold">
            ตะกร้าสินค้า ({items.length} รายการ)
          </h2>
          <button onClick={toggleCart} className="text-gray-500 hover:text-gray-700 text-xl">
            ✕
          </button>
        </div>

        {/* Items */}
        <div className="flex-1 overflow-y-auto p-4 space-y-4">
          {items.length === 0 ? (
            <div className="text-center py-16 text-gray-500">
              <p className="text-4xl mb-3">🛒</p>
              <p>ตะกร้าว่างเปล่า</p>
            </div>
          ) : (
            items.map(item => (
              <div key={item.id} className="flex gap-3 p-3 border rounded-lg">
                <Image
                  src={item.imageUrl}
                  alt={item.name}
                  width={64}
                  height={64}
                  className="rounded-lg object-cover"
                />
                <div className="flex-1 min-w-0">
                  <p className="font-medium text-sm truncate">{item.name}</p>
                  <p className="text-blue-600 font-bold">
                    ฿{item.price.toLocaleString('th-TH')}
                  </p>

                  {/* Quantity Controls */}
                  <div className="flex items-center gap-2 mt-2">
                    <button
                      onClick={() => updateQuantity(item.id, item.quantity - 1)}
                      className="w-7 h-7 border rounded flex items-center justify-center hover:bg-gray-100"
                    >
                      −
                    </button>
                    <span className="w-8 text-center text-sm font-medium">
                      {item.quantity}
                    </span>
                    <button
                      onClick={() => updateQuantity(item.id, item.quantity + 1)}
                      className="w-7 h-7 border rounded flex items-center justify-center hover:bg-gray-100"
                    >
                      +
                    </button>
                    <button
                      onClick={() => removeItem(item.id)}
                      className="ml-auto text-red-400 hover:text-red-600 text-sm"
                    >
                      ลบ
                    </button>
                  </div>
                </div>
              </div>
            ))
          )}
        </div>

        {/* Footer */}
        {items.length > 0 && (
          <div className="border-t p-4 space-y-3">
            <div className="flex justify-between text-lg font-bold">
              <span>รวมทั้งหมด</span>
              <span>฿{totalPrice().toLocaleString('th-TH')}</span>
            </div>
            <button className="w-full py-3 bg-blue-600 text-white rounded-xl font-medium hover:bg-blue-700 transition">
              ดำเนินการสั่งซื้อ
            </button>
            <button
              onClick={clearCart}
              className="w-full py-2 text-sm text-red-500 hover:text-red-700"
            >
              ล้างตะกร้า
            </button>
          </div>
        )}
      </div>
    </>
  )
}
```

**Pattern การออกแบบ Store ที่ดี — แบ่งเป็น Slice:**

```tsx
// stores/index.ts — รวม Store หลายตัว

// สำหรับแอปใหญ่ แบ่ง Store ออกเป็น Slice แยกกัน
// แต่ละ Slice รับผิดชอบ State ของตัวเอง

// stores/cartSlice.ts
export interface CartSlice {
  cart: CartItem[]
  addToCart: (item: CartItem) => void
  removeFromCart: (id: number) => void
}

export const createCartSlice = (set: any): CartSlice => ({
  cart: [],
  addToCart: (item) => set((state: any) => ({ cart: [...state.cart, item] })),
  removeFromCart: (id) =>
    set((state: any) => ({ cart: state.cart.filter((i: CartItem) => i.id !== id) })),
})

// stores/uiSlice.ts
export interface UISlice {
  isCartOpen: boolean
  isMobileMenuOpen: boolean
  toggleCart: () => void
  toggleMobileMenu: () => void
}

export const createUISlice = (set: any): UISlice => ({
  isCartOpen: false,
  isMobileMenuOpen: false,
  toggleCart: () => set((state: any) => ({ isCartOpen: !state.isCartOpen })),
  toggleMobileMenu: () =>
    set((state: any) => ({ isMobileMenuOpen: !state.isMobileMenuOpen })),
})

// stores/useAppStore.ts — รวม Slice ทั้งหมด
import { create } from 'zustand'
import { createCartSlice, CartSlice } from './cartSlice'
import { createUISlice, UISlice } from './uiSlice'

type AppStore = CartSlice & UISlice

export const useAppStore = create<AppStore>()((set, get, api) => ({
  ...createCartSlice(set),
  ...createUISlice(set),
}))
```

---

## 4.9 Server Actions — เรียก Back-End Logic จาก Component โดยตรง

### Server Actions คืออะไร

**Server Actions** (เซิร์ฟเวอร์ แอ็กชัน) คือ Function ที่ทำงานบน Server แต่เรียกได้จาก Client Component โดยตรง โดยไม่ต้องสร้าง API Endpoint แยก

```
แบบเดิม (ก่อน Server Actions):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Client Component
  → fetch('/api/products', { method: 'POST', body: ... })
      → app/api/products/route.ts  (ต้องสร้าง API Route แยก)
          → Database

แบบใหม่ (Server Actions):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Client Component
  → เรียก Server Action โดยตรง (Next.js จัดการ HTTP ให้เอง)
      → Database
```

**ข้อดีของ Server Actions:**

```
✅ ไม่ต้องสร้าง API Route แยก — โค้ดน้อยลง
✅ Type-safe ตั้งแต่ Client ถึง Server — TypeScript ช่วยจับ Error
✅ ทำงานได้แม้ JavaScript Disabled — Progressive Enhancement
✅ Revalidate Cache ได้โดยตรง — ไม่ต้อง Refetch เอง
✅ ปลอดภัย — ไม่ expose Business Logic ให้ Client เห็น
```

---

### สร้าง Server Action แรก

```tsx
// actions/productActions.ts
'use server'  // ← บอก Next.js ว่า File นี้ทุก Function ทำงานบน Server

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import { db } from '@/lib/db'
import { z } from 'zod'  // สำหรับ Validation

// Schema ตรวจสอบข้อมูลก่อนบันทึก
const CreateProductSchema = z.object({
  name: z.string().min(1, 'ชื่อสินค้าห้ามว่าง').max(100, 'ชื่อสินค้ายาวเกินไป'),
  price: z.coerce.number().positive('ราคาต้องมากกว่า 0'),
  description: z.string().min(10, 'คำอธิบายต้องมีอย่างน้อย 10 ตัวอักษร'),
  category: z.string().min(1, 'กรุณาเลือกหมวดหมู่'),
})

// Return Type ที่ชัดเจน
interface ActionResult {
  success: boolean
  message: string
  errors?: Record<string, string[]>
  data?: { id: number }
}

export async function createProduct(
  prevState: ActionResult | null,
  formData: FormData
): Promise<ActionResult> {
  // 1. ดึงข้อมูลจาก FormData
  const rawData = {
    name: formData.get('name'),
    price: formData.get('price'),
    description: formData.get('description'),
    category: formData.get('category'),
  }

  // 2. Validate ด้วย Zod
  const result = CreateProductSchema.safeParse(rawData)

  if (!result.success) {
    return {
      success: false,
      message: 'ข้อมูลไม่ถูกต้อง กรุณาตรวจสอบใหม่',
      errors: result.error.flatten().fieldErrors,
    }
  }

  // 3. บันทึกลง Database (ปลอดภัย — ทำงานบน Server)
  try {
    const product = await db.product.create({
      data: result.data,
    })

    // 4. Revalidate Cache ของหน้า /products
    revalidatePath('/products')

    return {
      success: true,
      message: 'เพิ่มสินค้าสำเร็จ',
      data: { id: product.id },
    }
  } catch (error) {
    return {
      success: false,
      message: 'เกิดข้อผิดพลาด กรุณาลองใหม่',
    }
  }
}

export async function deleteProduct(id: number): Promise<ActionResult> {
  try {
    await db.product.delete({ where: { id } })
    revalidatePath('/products')
    return { success: true, message: 'ลบสินค้าสำเร็จ' }
  } catch {
    return { success: false, message: 'ลบสินค้าไม่สำเร็จ' }
  }
}

export async function updateProductStock(
  id: number,
  quantity: number
): Promise<ActionResult> {
  try {
    await db.product.update({
      where: { id },
      data: { stock: { increment: quantity } },
    })
    revalidatePath(`/products/${id}`)
    return { success: true, message: 'อัปเดต Stock สำเร็จ' }
  } catch {
    return { success: false, message: 'อัปเดต Stock ไม่สำเร็จ' }
  }
}
```

**ใช้งาน Server Action ใน Form — ด้วย `useActionState`:**

```tsx
// components/CreateProductForm.tsx
'use client'

import { useActionState, useEffect } from 'react'
import { createProduct } from '@/actions/productActions'
import { useRouter } from 'next/navigation'

export function CreateProductForm() {
  const router = useRouter()

  // useActionState — จัดการ State ของ Form Action
  // [state, action, isPending] = useActionState(serverAction, initialState)
  const [state, formAction, isPending] = useActionState(createProduct, null)

  // Redirect หลัง Success
  useEffect(() => {
    if (state?.success && state.data) {
      router.push(`/products/${state.data.id}`)
    }
  }, [state, router])

  return (
    <form action={formAction} className="space-y-4 max-w-lg">
      <h2 className="text-2xl font-bold">เพิ่มสินค้าใหม่</h2>

      {/* แสดง Error รวม */}
      {state && !state.success && (
        <div className="p-3 bg-red-50 border border-red-200 rounded-lg text-red-700 text-sm">
          {state.message}
        </div>
      )}

      {/* แสดง Success */}
      {state?.success && (
        <div className="p-3 bg-green-50 border border-green-200 rounded-lg text-green-700 text-sm">
          {state.message}
        </div>
      )}

      {/* ชื่อสินค้า */}
      <div>
        <label htmlFor="name" className="block text-sm font-medium mb-1">
          ชื่อสินค้า <span className="text-red-500">*</span>
        </label>
        <input
          id="name"
          name="name"
          type="text"
          placeholder="เช่น MacBook Air M3"
          className={`w-full border rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500 ${
            state?.errors?.name ? 'border-red-400' : 'border-gray-300'
          }`}
        />
        {state?.errors?.name && (
          <p className="text-red-500 text-xs mt-1">{state.errors.name[0]}</p>
        )}
      </div>

      {/* ราคา */}
      <div>
        <label htmlFor="price" className="block text-sm font-medium mb-1">
          ราคา (บาท) <span className="text-red-500">*</span>
        </label>
        <input
          id="price"
          name="price"
          type="number"
          min="0"
          step="0.01"
          placeholder="0.00"
          className={`w-full border rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500 ${
            state?.errors?.price ? 'border-red-400' : 'border-gray-300'
          }`}
        />
        {state?.errors?.price && (
          <p className="text-red-500 text-xs mt-1">{state.errors.price[0]}</p>
        )}
      </div>

      {/* คำอธิบาย */}
      <div>
        <label htmlFor="description" className="block text-sm font-medium mb-1">
          คำอธิบาย <span className="text-red-500">*</span>
        </label>
        <textarea
          id="description"
          name="description"
          rows={4}
          placeholder="อธิบายสินค้าอย่างละเอียด..."
          className={`w-full border rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500 resize-none ${
            state?.errors?.description ? 'border-red-400' : 'border-gray-300'
          }`}
        />
        {state?.errors?.description && (
          <p className="text-red-500 text-xs mt-1">{state.errors.description[0]}</p>
        )}
      </div>

      {/* หมวดหมู่ */}
      <div>
        <label htmlFor="category" className="block text-sm font-medium mb-1">
          หมวดหมู่ <span className="text-red-500">*</span>
        </label>
        <select
          id="category"
          name="category"
          className="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
        >
          <option value="">เลือกหมวดหมู่</option>
          <option value="electronics">อิเล็กทรอนิกส์</option>
          <option value="clothing">เสื้อผ้า</option>
          <option value="books">หนังสือ</option>
          <option value="food">อาหาร</option>
        </select>
        {state?.errors?.category && (
          <p className="text-red-500 text-xs mt-1">{state.errors.category[0]}</p>
        )}
      </div>

      {/* Submit Button */}
      <button
        type="submit"
        disabled={isPending}
        className="w-full py-3 bg-blue-600 text-white rounded-lg font-medium hover:bg-blue-700 disabled:opacity-50 disabled:cursor-not-allowed transition"
      >
        {isPending ? (
          <span className="flex items-center justify-center gap-2">
            <span className="w-4 h-4 border-2 border-white border-t-transparent rounded-full animate-spin" />
            กำลังบันทึก...
          </span>
        ) : (
          'บันทึกสินค้า'
        )}
      </button>
    </form>
  )
}
```

**Server Action แบบไม่ใช้ Form — เรียกโดยตรง:**

```tsx
// components/DeleteProductButton.tsx
'use client'

import { useState, useTransition } from 'react'
import { deleteProduct } from '@/actions/productActions'

export function DeleteProductButton({ productId }: { productId: number }) {
  const [isPending, startTransition] = useTransition()
  const [error, setError] = useState<string | null>(null)

  const handleDelete = () => {
    if (!confirm('แน่ใจว่าต้องการลบสินค้านี้?')) return

    startTransition(async () => {
      const result = await deleteProduct(productId)
      if (!result.success) {
        setError(result.message)
      }
    })
  }

  return (
    <div>
      <button
        onClick={handleDelete}
        disabled={isPending}
        className="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 disabled:opacity-50 transition text-sm"
      >
        {isPending ? 'กำลังลบ...' : 'ลบสินค้า'}
      </button>
      {error && <p className="text-red-500 text-xs mt-1">{error}</p>}
    </div>
  )
}
```

---

## 4.10 Rendering Strategies — เลือกให้เหมาะกับงาน

Next.js มีกลยุทธ์การ Render 4 แบบ แต่ละแบบเหมาะกับ Use Case ที่ต่างกัน:

### ภาพรวม Rendering Strategies

```
Static Site Generation (SSG) — Build Time
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
HTML สร้างตอน Build → เก็บบน CDN → เร็วมาก
✅ เหมาะกับ: หน้าที่ไม่เปลี่ยนบ่อย เช่น Blog, Landing Page, เอกสาร
❌ ไม่เหมาะกับ: ข้อมูล Real-time, ข้อมูลเฉพาะผู้ใช้

Server-Side Rendering (SSR) — Request Time
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
HTML สร้างใหม่ทุก Request → ข้อมูลล่าสุดเสมอ
✅ เหมาะกับ: Dashboard, หน้าที่ต้องการข้อมูล Real-time หรือข้อมูลผู้ใช้
❌ ช้ากว่า SSG เพราะต้อง Render ทุก Request

Incremental Static Regeneration (ISR) — จุดกึ่งกลาง
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
HTML สร้างครั้งแรก → Cache → Regenerate ตามเวลาที่กำหนด
✅ เหมาะกับ: หน้าสินค้า, บทความที่อัปเดตเป็นระยะ
✅ ดีที่สุดของทั้งสองโลก: เร็ว + ข้อมูลค่อนข้างใหม่

Client-Side Rendering (CSR) — Browser
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
HTML เปล่า → Browser โหลด JS → Fetch ข้อมูล → Render
✅ เหมาะกับ: ส่วน Interactive สูง, Dashboard ที่ต้องการ Real-time
❌ SEO ไม่ดี, โหลดช้าครั้งแรก
```

---

### ตัวอย่างโค้ดแต่ละ Strategy

**SSG — Static Generation:**

```tsx
// app/blog/[slug]/page.tsx

// Default ใน Next.js 15 คือ Static — ไม่ต้องทำอะไรพิเศษ
// เพียงแค่ไม่ใช้ dynamic data ที่เปลี่ยนตาม Request

export default async function BlogPost({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const { slug } = await params
  const post = await fetch(`https://cms.example.com/posts/${slug}`, {
    // cache: 'force-cache' คือ Default — บันทึก Cache ไว้ไม่มีวันหมดอายุ
    cache: 'force-cache',
  }).then(r => r.json())

  return (
    <article className="prose max-w-3xl mx-auto">
      <h1>{post.title}</h1>
      <time className="text-gray-500 text-sm">{post.publishedAt}</time>
      <div dangerouslySetInnerHTML={{ __html: post.contentHtml }} />
    </article>
  )
}

// บอก Next.js ว่ามี slug อะไรบ้าง (Build ล่วงหน้าทั้งหมด)
export async function generateStaticParams() {
  const posts = await fetch('https://cms.example.com/posts').then(r => r.json())
  return posts.map((post: { slug: string }) => ({ slug: post.slug }))
}
```

**SSR — Server-Side Rendering:**

```tsx
// app/dashboard/page.tsx

// กำหนด dynamic = 'force-dynamic' เพื่อ Render ใหม่ทุก Request
export const dynamic = 'force-dynamic'

export default async function DashboardPage() {
  // ข้อมูล User เฉพาะบุคคล — ต้อง Render ใหม่ทุกครั้ง
  const session = await getServerSession()
  
  if (!session) redirect('/login')

  // ดึงข้อมูลที่ขึ้นกับ User
  const [orders, analytics] = await Promise.all([
    fetchUserOrders(session.userId),
    fetchUserAnalytics(session.userId),
  ])

  return (
    <div>
      <h1>ยินดีต้อนรับ, {session.user.name}</h1>
      <OrdersSummary orders={orders} />
      <AnalyticsChart data={analytics} />
    </div>
  )
}
```

**ISR — Incremental Static Regeneration:**

```tsx
// app/products/[id]/page.tsx

export default async function ProductPage({
  params,
}: {
  params: Promise<{ id: string }>
}) {
  const { id } = await params

  const product = await fetch(`https://api.example.com/products/${id}`, {
    // next.revalidate = 3600 หมายถึง Regenerate ทุก 1 ชั่วโมง
    next: { revalidate: 3600 },
  }).then(r => r.json())

  return (
    <div>
      <h1>{product.name}</h1>
      <p>฿{product.price.toLocaleString('th-TH')}</p>
      {/* ราคาและชื่ออาจเก่าได้สูงสุด 1 ชั่วโมง — ยอมรับได้ */}
    </div>
  )
}

// Revalidate On-Demand — บังคับ Regenerate ทันทีเมื่อข้อมูลเปลี่ยน
// เรียกจาก Server Action หรือ API Route
import { revalidatePath, revalidateTag } from 'next/cache'

export async function updateProductPrice(id: number, price: number) {
  'use server'
  await db.product.update({ where: { id }, data: { price } })
  revalidatePath(`/products/${id}`) // Regenerate หน้านั้นทันที
}
```

**เลือก Strategy อย่างไรใน Project จริง:**

```
หน้าบ้านของร้านค้า (Landing Page)
  → SSG (ไม่เปลี่ยนบ่อย, ต้องการ SEO ดี)

หน้ารายการสินค้า (มีราคา/Stock)
  → ISR revalidate: 300 (5 นาที)
  → หรือ ISR + On-Demand Revalidation เมื่ออัปเดตสินค้า

หน้าบล็อก/บทความ
  → SSG + On-Demand Revalidation เมื่อ Publish

หน้า Dashboard ผู้ใช้
  → SSR (ข้อมูลเฉพาะบุคคล, ต้องล่าสุดเสมอ)

ส่วน Live Chat / Notification
  → CSR (Real-time, ไม่ต้อง SEO)
```

---

## 4.11 Performance Optimization

### Core Web Vitals — ตัวชี้วัด Performance ที่ Google ใช้

```
Core Web Vitals คือ 3 ตัวชี้วัดหลักที่ Google ใช้วัด UX:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

LCP (Largest Contentful Paint) — ความเร็วโหลด
  → วัดเวลาที่ Element ขนาดใหญ่สุดปรากฏบนหน้าจอ
  → ดี: < 2.5 วินาที
  → แก้ด้วย: next/image + priority, ลด Server Response Time

CLS (Cumulative Layout Shift) — ความเสถียรของ Layout
  → วัดการ "กระโดด" ของ Element เมื่อโหลด
  → ดี: < 0.1
  → แก้ด้วย: กำหนด width/height รูป, Skeleton UI

INP (Interaction to Next Paint) — ความตอบสนอง
  → วัดเวลาตั้งแต่คลิกจนเห็นการตอบสนอง
  → ดี: < 200ms
  → แก้ด้วย: ลด JavaScript, แตก Task ใหญ่เป็นชิ้นเล็ก
```

---

### Lazy Loading ด้วย `dynamic()`

**`dynamic()`** คือ `React.lazy()` ที่ Next.js ปรับปรุงให้ทรงพลังกว่า ใช้โหลด Component เมื่อต้องการจริง ๆ แทนที่จะโหลดทุกอย่างตั้งแต่แรก:

```tsx
// app/dashboard/page.tsx
import dynamic from 'next/dynamic'

// โหลด Component เมื่อต้องการ — ไม่รวมใน Initial Bundle
const HeavyChart = dynamic(() => import('@/components/HeavyChart'), {
  loading: () => (
    <div className="h-80 bg-gray-100 animate-pulse rounded-xl flex items-center justify-center">
      <p className="text-gray-400">กำลังโหลดกราฟ...</p>
    </div>
  ),
  ssr: false,  // ไม่ Render บน Server (เพราะใช้ Browser API)
})

const VideoPlayer = dynamic(() => import('@/components/VideoPlayer'), {
  loading: () => <div className="aspect-video bg-gray-900 animate-pulse rounded-xl" />,
  ssr: false,
})

// Rich Text Editor — โหลดเฉพาะหน้า Edit
const RichTextEditor = dynamic(() => import('@/components/RichTextEditor'), {
  ssr: false,
})

export default function DashboardPage() {
  return (
    <div className="space-y-8">
      {/* HeavyChart โหลดแยก — ไม่ทำให้ Initial Load ช้า */}
      <HeavyChart />
      <VideoPlayer src="/intro.mp4" />
    </div>
  )
}
```

**ตัวอย่างจริง — Modal ที่โหลดเฉพาะเมื่อเปิด:**

```tsx
// components/ProductPage.tsx
'use client'

import { useState } from 'react'
import dynamic from 'next/dynamic'

// ReviewModal โหลดเฉพาะเมื่อ User คลิก "เขียนรีวิว"
const ReviewModal = dynamic(() => import('./ReviewModal'), {
  loading: () => null,  // ไม่แสดงอะไรขณะโหลด Modal
})

export function ProductActions({ productId }: { productId: number }) {
  const [showReview, setShowReview] = useState(false)

  return (
    <div>
      <button
        onClick={() => setShowReview(true)}
        className="px-4 py-2 border rounded-lg hover:bg-gray-50"
      >
        เขียนรีวิว
      </button>

      {/* ReviewModal โหลดเฉพาะเมื่อ showReview = true */}
      {showReview && (
        <ReviewModal
          productId={productId}
          onClose={() => setShowReview(false)}
        />
      )}
    </div>
  )
}
```

---

### Font Optimization ด้วย `next/font`

```tsx
// app/layout.tsx
import { Inter, Sarabun } from 'next/font/google'
import localFont from 'next/font/local'

// Google Font — โหลดและ Self-host อัตโนมัติ (ไม่มี Request ไป Google)
const inter = Inter({
  subsets: ['latin'],
  variable: '--font-inter',  // ใช้เป็น CSS Variable ได้
  display: 'swap',           // แสดง Fallback Font ก่อน แล้วค่อย Swap
})

// Font ภาษาไทย
const sarabun = Sarabun({
  subsets: ['thai', 'latin'],
  weight: ['300', '400', '500', '600', '700'],
  variable: '--font-sarabun',
  display: 'swap',
})

// Local Font (ไม่ใช่ Google Font)
const customFont = localFont({
  src: [
    { path: '../fonts/MyFont-Regular.woff2', weight: '400' },
    { path: '../fonts/MyFont-Bold.woff2', weight: '700' },
  ],
  variable: '--font-custom',
})

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    // ใส่ CSS Variables บน html element
    <html lang="th" className={`${sarabun.variable} ${inter.variable}`}>
      <body className="font-sarabun">  {/* ใช้ Tailwind + CSS Variable */}
        {children}
      </body>
    </html>
  )
}
```

```typescript
// tailwind.config.ts — เชื่อมต่อ Font Variable กับ Tailwind
import type { Config } from 'tailwindcss'

export default {
  content: ['./app/**/*.{ts,tsx}', './components/**/*.{ts,tsx}'],
  theme: {
    extend: {
      fontFamily: {
        sarabun: ['var(--font-sarabun)', 'sans-serif'],
        inter: ['var(--font-inter)', 'sans-serif'],
      },
    },
  },
} satisfies Config
```

---

### Bundle Analysis — ตรวจสอบขนาด JavaScript

```bash
# ติดตั้ง Bundle Analyzer
pnpm add -D @next/bundle-analyzer
```

```typescript
// next.config.ts
import type { NextConfig } from 'next'
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
})

const nextConfig: NextConfig = {
  // ...
}

export default withBundleAnalyzer(nextConfig)
```

```bash
# รัน Build พร้อม Analyzer
ANALYZE=true pnpm build

# เบราว์เซอร์จะเปิดขึ้นมาแสดง Treemap ของ Bundle
# มองหา:
# - Package ขนาดใหญ่ที่ไม่คาดคิด
# - Code ที่ไม่ได้ใช้ (Dead Code)
# - Duplicate Dependencies
```

**วิธีลดขนาด Bundle:**

```tsx
// ❌ Import ทั้ง Library — Bundle ใหญ่
import _ from 'lodash'
const result = _.groupBy(items, 'category')

// ✅ Import เฉพาะฟังก์ชันที่ใช้ — Bundle เล็กกว่ามาก
import groupBy from 'lodash/groupBy'
const result = groupBy(items, 'category')

// ❌ Import ทั้ง date-fns
import { format, parseISO, addDays, subMonths } from 'date-fns'

// ✅ ยังโอเค เพราะ date-fns รองรับ Tree Shaking แล้ว
// แต่ควรตรวจสอบ Library อื่น ๆ ด้วย
```

---

### Image Optimization แบบลึก

```tsx
// components/HeroImage.tsx — รูปหลักที่ต้องโหลดเร็ว
import Image from 'next/image'

export function HeroSection() {
  return (
    <div className="relative h-[600px] overflow-hidden">
      <Image
        src="/hero.jpg"
        alt="Hero Banner — แบนเนอร์หน้าหลัก"
        fill
        priority          // โหลดก่อน — สำหรับรูปที่อยู่ Above the Fold
        quality={85}      // ลด Quality เล็กน้อยเพื่อลดขนาดไฟล์ (Default: 75)
        className="object-cover object-center"
        sizes="100vw"     // รูปนี้กว้าง 100% ของ Viewport เสมอ
        // placeholder="blur" — แสดง Blur ก่อนโหลด (ต้องมี blurDataURL)
        // blurDataURL="data:image/png;base64,..." — Base64 ของรูป Tiny
      />
      <div className="absolute inset-0 bg-gradient-to-b from-transparent to-black/60" />
      <div className="absolute bottom-12 left-12 text-white">
        <h1 className="text-5xl font-bold">Welcome to MyShop</h1>
      </div>
    </div>
  )
}

// components/ProductGrid.tsx — รูปในตาราง (lazy load)
export function ProductGrid({ products }: { products: Product[] }) {
  return (
    <div className="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
      {products.map((product, index) => (
        <div key={product.id} className="border rounded-xl overflow-hidden">
          <Image
            src={product.imageUrl}
            alt={product.name}
            width={400}
            height={400}
            // รูปแรก 4 อัน — โหลดก่อน (Above the Fold)
            // รูปที่เหลือ — Lazy Load (Default)
            priority={index < 4}
            className="w-full aspect-square object-cover"
            // sizes บอก Browser ว่ารูปกว้างเท่าไหร่ที่ Breakpoint ต่าง ๆ
            // ช่วยให้ Browser เลือก srcset ที่เหมาะสม ลดการดาวน์โหลดที่ไม่จำเป็น
            sizes="(max-width: 640px) 50vw, (max-width: 1024px) 33vw, 25vw"
          />
          <div className="p-3">
            <p className="font-medium text-sm">{product.name}</p>
          </div>
        </div>
      ))}
    </div>
  )
}
```

---

### Protected Routes ด้วย Middleware

```typescript
// middleware.ts — ทำงานก่อนทุก Request
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

// Routes ที่ต้อง Login
const protectedRoutes = ['/dashboard', '/profile', '/orders', '/admin']

// Routes สำหรับ Guest เท่านั้น (ถ้า Login แล้วจะ Redirect)
const authRoutes = ['/login', '/register']

export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl
  
  // อ่าน Session Token จาก Cookie
  const token = request.cookies.get('session-token')?.value
  const isAuthenticated = !!token

  // ถ้าเข้า Protected Route โดยไม่ได้ Login
  const isProtectedRoute = protectedRoutes.some(route =>
    pathname.startsWith(route)
  )

  if (isProtectedRoute && !isAuthenticated) {
    const loginUrl = new URL('/login', request.url)
    // เก็บ URL ปัจจุบันไว้ เพื่อ Redirect กลับหลัง Login
    loginUrl.searchParams.set('callbackUrl', pathname)
    return NextResponse.redirect(loginUrl)
  }

  // ถ้า Login แล้วพยายามเข้าหน้า Login/Register
  const isAuthRoute = authRoutes.some(route => pathname.startsWith(route))
  if (isAuthRoute && isAuthenticated) {
    return NextResponse.redirect(new URL('/dashboard', request.url))
  }

  return NextResponse.next()
}

// กำหนดว่า Middleware รันที่ Route ไหนบ้าง
export const config = {
  matcher: [
    '/((?!api|_next/static|_next/image|favicon.ico|public).*)',
  ],
}
```

---

## 4.12 โปรเจกต์ปฏิบัติ: Movie Finder

> พัฒนาเว็บแอป **"Movie Finder"** ด้วย Next.js 15 + TypeScript + Tailwind CSS + shadcn/ui: แสดงรายการหนัง, ค้นหา, ดูรายละเอียด, บันทึก Favorite, รองรับ Mobile ทุกขนาด

---

### ภาพรวมโปรเจกต์

```
Movie Finder — Feature ที่ต้องทำ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ หน้าแรก — แสดงหนังยอดนิยมและ Trending
✅ หน้าค้นหา — ค้นหาหนังด้วย Query
✅ หน้ารายละเอียด — Trailer, Cast, Rating, Overview
✅ ระบบ Favorite — บันทึก/ยกเลิก Favorite ด้วย Zustand + localStorage
✅ Responsive Design — Mobile, Tablet, Desktop
✅ Dark Mode — Toggle ด้วย Context API
✅ Loading States — Skeleton UI ทุกที่
✅ Error Handling — แสดง Error ที่สื่อความหมาย
```

**API ที่ใช้: TMDB (The Movie Database)**

สมัครฟรีที่ `themoviedb.org/settings/api` รับ API Key

---

### ขั้นตอนที่ 1 — ตั้งค่าโปรเจกต์

```bash
# สร้างโปรเจกต์
pnpm create next-app@latest movie-finder

# เข้าไปในโฟลเดอร์
cd movie-finder

# ติดตั้ง shadcn/ui
pnpm dlx shadcn@latest init

# ติดตั้ง Component ที่ต้องการ
pnpm dlx shadcn@latest add card badge button input skeleton

# ติดตั้ง Zustand
pnpm add zustand
```

```bash
# .env.local — ข้อมูลลับ ห้าม Commit ขึ้น Git
TMDB_API_KEY=your_api_key_here
TMDB_BASE_URL=https://api.themoviedb.org/3
NEXT_PUBLIC_TMDB_IMAGE_BASE=https://image.tmdb.org/t/p
```

---

### ขั้นตอนที่ 2 — Types และ API Layer

```typescript
// types/movie.ts — กำหนดโครงสร้างข้อมูลทั้งหมด
export interface Movie {
  id: number
  title: string
  overview: string
  poster_path: string | null
  backdrop_path: string | null
  vote_average: number
  vote_count: number
  release_date: string
  genre_ids: number[]
  popularity: number
  original_language: string
}

export interface MovieDetail extends Movie {
  genres: Genre[]
  runtime: number
  status: string
  tagline: string
  budget: number
  revenue: number
  production_companies: ProductionCompany[]
  videos: { results: Video[] }
  credits: {
    cast: CastMember[]
    crew: CrewMember[]
  }
}

export interface Genre {
  id: number
  name: string
}

export interface Video {
  id: string
  key: string
  site: string
  type: string
  official: boolean
}

export interface CastMember {
  id: number
  name: string
  character: string
  profile_path: string | null
  order: number
}

export interface CrewMember {
  id: number
  name: string
  job: string
  department: string
  profile_path: string | null
}

export interface ProductionCompany {
  id: number
  name: string
  logo_path: string | null
}

export interface PaginatedResponse<T> {
  page: number
  results: T[]
  total_pages: number
  total_results: number
}
```

```typescript
// lib/tmdb.ts — API Layer
const API_KEY = process.env.TMDB_API_KEY
const BASE_URL = process.env.TMDB_BASE_URL || 'https://api.themoviedb.org/3'

// Helper Function สำหรับ Fetch พร้อม Error Handling
async function tmdbFetch<T>(endpoint: string, params?: Record<string, string>): Promise<T> {
  const url = new URL(`${BASE_URL}${endpoint}`)
  url.searchParams.set('api_key', API_KEY!)
  url.searchParams.set('language', 'th-TH')  // ขอข้อมูลภาษาไทย

  if (params) {
    Object.entries(params).forEach(([key, value]) => {
      url.searchParams.set(key, value)
    })
  }

  const res = await fetch(url.toString(), {
    next: { revalidate: 3600 },  // Cache 1 ชั่วโมง
  })

  if (!res.ok) {
    throw new Error(`TMDB API Error: ${res.status} ${res.statusText}`)
  }

  return res.json()
}

// ฟังก์ชันดึงข้อมูลต่าง ๆ
export const tmdb = {
  // หนัง Trending
  getTrending: (timeWindow: 'day' | 'week' = 'week') =>
    tmdbFetch<PaginatedResponse<Movie>>(`/trending/movie/${timeWindow}`),

  // หนังยอดนิยม
  getPopular: (page = 1) =>
    tmdbFetch<PaginatedResponse<Movie>>('/movie/popular', { page: String(page) }),

  // หนัง Now Playing
  getNowPlaying: () =>
    tmdbFetch<PaginatedResponse<Movie>>('/movie/now_playing'),

  // หนัง Top Rated
  getTopRated: (page = 1) =>
    tmdbFetch<PaginatedResponse<Movie>>('/movie/top_rated', { page: String(page) }),

  // รายละเอียดหนัง
  getMovieDetail: (id: number) =>
    tmdbFetch<MovieDetail>(`/movie/${id}`, {
      append_to_response: 'videos,credits',
    }),

  // ค้นหา
  searchMovies: (query: string, page = 1) =>
    tmdbFetch<PaginatedResponse<Movie>>('/search/movie', {
      query,
      page: String(page),
    }),

  // หนังที่คล้ายกัน
  getSimilarMovies: (id: number) =>
    tmdbFetch<PaginatedResponse<Movie>>(`/movie/${id}/similar`),

  // URL รูปภาพ
  getImageUrl: (path: string | null, size: 'w200' | 'w500' | 'w780' | 'original' = 'w500') =>
    path
      ? `${process.env.NEXT_PUBLIC_TMDB_IMAGE_BASE}/${size}${path}`
      : '/images/no-poster.png',
}
```

---

### ขั้นตอนที่ 3 — Zustand Store สำหรับ Favorites

```typescript
// stores/favoriteStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'
import type { Movie } from '@/types/movie'

interface FavoriteStore {
  favorites: Movie[]
  isFavorite: (id: number) => boolean
  addFavorite: (movie: Movie) => void
  removeFavorite: (id: number) => void
  toggleFavorite: (movie: Movie) => void
  clearFavorites: () => void
}

export const useFavoriteStore = create<FavoriteStore>()(
  persist(
    (set, get) => ({
      favorites: [],

      isFavorite: (id) => get().favorites.some(movie => movie.id === id),

      addFavorite: (movie) => {
        if (get().isFavorite(movie.id)) return
        set(state => ({ favorites: [movie, ...state.favorites] }))
      },

      removeFavorite: (id) => {
        set(state => ({
          favorites: state.favorites.filter(movie => movie.id !== id),
        }))
      },

      toggleFavorite: (movie) => {
        if (get().isFavorite(movie.id)) {
          get().removeFavorite(movie.id)
        } else {
          get().addFavorite(movie)
        }
      },

      clearFavorites: () => set({ favorites: [] }),
    }),
    {
      name: 'movie-favorites',
    }
  )
)
```

---

### ขั้นตอนที่ 4 — Component หลัก

```tsx
// components/MovieCard.tsx
'use client'

import Image from 'next/image'
import Link from 'next/link'
import { useFavoriteStore } from '@/stores/favoriteStore'
import { tmdb } from '@/lib/tmdb'
import type { Movie } from '@/types/movie'

interface MovieCardProps {
  movie: Movie
  priority?: boolean
}

export function MovieCard({ movie, priority = false }: MovieCardProps) {
  const { isFavorite, toggleFavorite } = useFavoriteStore()
  const favorited = isFavorite(movie.id)

  const posterUrl = tmdb.getImageUrl(movie.poster_path, 'w500')
  const rating = movie.vote_average.toFixed(1)
  const year = movie.release_date?.split('-')[0] ?? 'N/A'

  // สี Rating ตามคะแนน
  const ratingColor =
    movie.vote_average >= 7
      ? 'text-green-400'
      : movie.vote_average >= 5
        ? 'text-yellow-400'
        : 'text-red-400'

  return (
    <div className="group relative rounded-xl overflow-hidden bg-gray-900 shadow-md hover:shadow-xl transition-all duration-300 hover:-translate-y-1">
      <Link href={`/movies/${movie.id}`}>
        <div className="relative aspect-[2/3]">
          <Image
            src={posterUrl}
            alt={`โปสเตอร์หนัง ${movie.title}`}
            fill
            priority={priority}
            className="object-cover transition-transform duration-300 group-hover:scale-105"
            sizes="(max-width: 640px) 50vw, (max-width: 1024px) 33vw, 20vw"
          />

          {/* Overlay เมื่อ Hover */}
          <div className="absolute inset-0 bg-black/0 group-hover:bg-black/40 transition-all duration-300 flex items-center justify-center">
            <span className="text-white opacity-0 group-hover:opacity-100 transition-opacity font-medium">
              ดูรายละเอียด
            </span>
          </div>
        </div>
      </Link>

      {/* Favorite Button */}
      <button
        onClick={() => toggleFavorite(movie)}
        className="absolute top-2 right-2 w-8 h-8 bg-black/60 backdrop-blur-sm rounded-full flex items-center justify-center hover:bg-black/80 transition z-10"
        aria-label={favorited ? 'ยกเลิก Favorite' : 'เพิ่ม Favorite'}
      >
        <span className={`text-lg transition-transform ${favorited ? 'scale-110' : ''}`}>
          {favorited ? '❤️' : '🤍'}
        </span>
      </button>

      {/* Rating Badge */}
      <div className="absolute top-2 left-2 bg-black/70 backdrop-blur-sm rounded-full px-2 py-0.5">
        <span className={`text-xs font-bold ${ratingColor}`}>⭐ {rating}</span>
      </div>

      {/* Info */}
      <div className="p-3">
        <h3 className="text-white font-medium text-sm leading-tight line-clamp-2">
          {movie.title}
        </h3>
        <p className="text-gray-400 text-xs mt-1">{year}</p>
      </div>
    </div>
  )
}

// Skeleton สำหรับ Loading State
export function MovieCardSkeleton() {
  return (
    <div className="rounded-xl overflow-hidden bg-gray-900 animate-pulse">
      <div className="aspect-[2/3] bg-gray-800" />
      <div className="p-3 space-y-2">
        <div className="h-4 bg-gray-800 rounded w-3/4" />
        <div className="h-3 bg-gray-800 rounded w-1/4" />
      </div>
    </div>
  )
}
```

```tsx
// components/MovieGrid.tsx — Server Component
import { MovieCard, MovieCardSkeleton } from './MovieCard'
import type { Movie } from '@/types/movie'

interface MovieGridProps {
  movies: Movie[]
  title?: string
}

export function MovieGrid({ movies, title }: MovieGridProps) {
  return (
    <section>
      {title && (
        <h2 className="text-2xl font-bold text-white mb-6">{title}</h2>
      )}
      <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4">
        {movies.map((movie, index) => (
          <MovieCard
            key={movie.id}
            movie={movie}
            priority={index < 5}  // รูปแรก 5 อัน โหลดก่อน
          />
        ))}
      </div>
    </section>
  )
}

export function MovieGridSkeleton({ count = 10 }: { count?: number }) {
  return (
    <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4">
      {Array.from({ length: count }).map((_, i) => (
        <MovieCardSkeleton key={i} />
      ))}
    </div>
  )
}
```

---

### ขั้นตอนที่ 5 — หน้าหลักและหน้าค้นหา

```tsx
// app/page.tsx — หน้าแรก (Server Component)
import { Suspense } from 'react'
import { tmdb } from '@/lib/tmdb'
import { MovieGrid, MovieGridSkeleton } from '@/components/MovieGrid'
import { HeroBanner } from '@/components/HeroBanner'

export const metadata = {
  title: 'Movie Finder — ค้นหาหนังที่ใช่',
}

export default async function HomePage() {
  // ดึงข้อมูลหลายอย่างพร้อมกัน (Parallel)
  const [trending, nowPlaying] = await Promise.all([
    tmdb.getTrending('week'),
    tmdb.getNowPlaying(),
  ])

  return (
    <main className="min-h-screen bg-gray-950 text-white">
      {/* Hero Banner จากหนัง Trending อันดับ 1 */}
      <HeroBanner movie={trending.results[0]} />

      <div className="container mx-auto px-4 py-12 space-y-12">
        {/* Trending Section */}
        <section>
          <div className="flex items-center gap-3 mb-6">
            <span className="text-2xl">🔥</span>
            <h2 className="text-2xl font-bold">Trending สัปดาห์นี้</h2>
          </div>
          <MovieGrid movies={trending.results.slice(1, 11)} />
        </section>

        {/* Now Playing */}
        <section>
          <div className="flex items-center gap-3 mb-6">
            <span className="text-2xl">🎬</span>
            <h2 className="text-2xl font-bold">ฉายอยู่ตอนนี้</h2>
          </div>
          <MovieGrid movies={nowPlaying.results.slice(0, 10)} />
        </section>
      </div>
    </main>
  )
}
```

```tsx
// app/search/page.tsx — หน้าค้นหา
import { tmdb } from '@/lib/tmdb'
import { MovieGrid } from '@/components/MovieGrid'
import { SearchBar } from '@/components/SearchBar'

interface SearchPageProps {
  searchParams: Promise<{ q?: string; page?: string }>
}

export async function generateMetadata({ searchParams }: SearchPageProps) {
  const { q } = await searchParams
  return {
    title: q ? `ค้นหา "${q}" — Movie Finder` : 'ค้นหาหนัง — Movie Finder',
  }
}

export default async function SearchPage({ searchParams }: SearchPageProps) {
  const { q: query = '', page = '1' } = await searchParams
  const currentPage = parseInt(page)

  // ค้นหาหรือแสดงหนังยอดนิยมถ้าไม่มี Query
  const data = query
    ? await tmdb.searchMovies(query, currentPage)
    : await tmdb.getPopular(currentPage)

  return (
    <main className="min-h-screen bg-gray-950 text-white">
      <div className="container mx-auto px-4 py-8">
        <h1 className="text-3xl font-bold mb-8">
          {query ? `ผลการค้นหา "${query}"` : 'ค้นหาหนัง'}
        </h1>

        {/* Search Bar — Client Component */}
        <SearchBar defaultValue={query} />

        {/* ผลลัพธ์ */}
        <div className="mt-8">
          {query && (
            <p className="text-gray-400 mb-4">
              พบ {data.total_results.toLocaleString()} รายการ
            </p>
          )}

          {data.results.length === 0 ? (
            <div className="text-center py-20">
              <p className="text-5xl mb-4">🎬</p>
              <p className="text-gray-400 text-lg">ไม่พบหนังที่ค้นหา</p>
              <p className="text-gray-600 text-sm mt-2">ลองค้นหาด้วยคำอื่น</p>
            </div>
          ) : (
            <MovieGrid movies={data.results} />
          )}
        </div>

        {/* Pagination */}
        {data.total_pages > 1 && (
          <Pagination
            currentPage={currentPage}
            totalPages={Math.min(data.total_pages, 50)}
            query={query}
          />
        )}
      </div>
    </main>
  )
}
```

```tsx
// components/SearchBar.tsx — Client Component
'use client'

import { useRouter, useSearchParams } from 'next/navigation'
import { useTransition, useState } from 'react'

export function SearchBar({ defaultValue = '' }: { defaultValue?: string }) {
  const router = useRouter()
  const [isPending, startTransition] = useTransition()
  const [query, setQuery] = useState(defaultValue)

  const handleSearch = (value: string) => {
    setQuery(value)
    startTransition(() => {
      if (value.trim()) {
        router.push(`/search?q=${encodeURIComponent(value.trim())}`)
      } else {
        router.push('/search')
      }
    })
  }

  return (
    <div className="relative max-w-2xl">
      <span className="absolute left-4 top-1/2 -translate-y-1/2 text-gray-400 text-lg">
        🔍
      </span>
      <input
        type="search"
        value={query}
        onChange={e => handleSearch(e.target.value)}
        placeholder="ค้นหาหนัง เช่น Avatar, Spider-Man..."
        className="w-full bg-gray-800 text-white border border-gray-700 rounded-xl pl-11 pr-4 py-3 text-base focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 placeholder:text-gray-500"
      />
      {isPending && (
        <span className="absolute right-4 top-1/2 -translate-y-1/2">
          <span className="w-5 h-5 border-2 border-blue-500 border-t-transparent rounded-full animate-spin block" />
        </span>
      )}
    </div>
  )
}
```

---

### ขั้นตอนที่ 6 — หน้ารายละเอียดหนัง

```tsx
// app/movies/[id]/page.tsx
import { Suspense } from 'react'
import { notFound } from 'next/navigation'
import Image from 'next/image'
import { tmdb } from '@/lib/tmdb'
import { FavoriteButton } from '@/components/FavoriteButton'
import { TrailerSection } from '@/components/TrailerSection'
import { CastSection } from '@/components/CastSection'
import { MovieGrid } from '@/components/MovieGrid'

interface MoviePageProps {
  params: Promise<{ id: string }>
}

export async function generateMetadata({ params }: MoviePageProps) {
  const { id } = await params
  try {
    const movie = await tmdb.getMovieDetail(parseInt(id))
    return {
      title: `${movie.title} — Movie Finder`,
      description: movie.overview,
      openGraph: {
        images: movie.backdrop_path
          ? [tmdb.getImageUrl(movie.backdrop_path, 'w780')]
          : [],
      },
    }
  } catch {
    return { title: 'ไม่พบหนัง' }
  }
}

export default async function MoviePage({ params }: MoviePageProps) {
  const { id } = await params
  const movieId = parseInt(id)

  if (isNaN(movieId)) notFound()

  const [movie, similar] = await Promise.all([
    tmdb.getMovieDetail(movieId).catch(() => null),
    tmdb.getSimilarMovies(movieId).catch(() => ({ results: [] })),
  ])

  if (!movie) notFound()

  const backdropUrl = tmdb.getImageUrl(movie.backdrop_path, 'w780')
  const posterUrl = tmdb.getImageUrl(movie.poster_path, 'w500')
  const trailer = movie.videos.results.find(
    v => v.type === 'Trailer' && v.site === 'YouTube' && v.official
  )
  const director = movie.credits.crew.find(c => c.job === 'Director')
  const mainCast = movie.credits.cast.slice(0, 8)

  const hours = Math.floor(movie.runtime / 60)
  const minutes = movie.runtime % 60
  const runtime = movie.runtime ? `${hours}ชม. ${minutes}น.` : 'N/A'

  return (
    <main className="min-h-screen bg-gray-950 text-white">
      {/* Backdrop */}
      <div className="relative h-[50vh] md:h-[60vh]">
        <Image
          src={backdropUrl}
          alt={movie.title}
          fill
          priority
          className="object-cover object-top"
        />
        <div className="absolute inset-0 bg-gradient-to-t from-gray-950 via-gray-950/60 to-transparent" />
      </div>

      <div className="container mx-auto px-4 -mt-32 relative z-10">
        <div className="flex flex-col md:flex-row gap-8">
          {/* Poster */}
          <div className="flex-shrink-0">
            <Image
              src={posterUrl}
              alt={`โปสเตอร์ ${movie.title}`}
              width={240}
              height={360}
              className="rounded-xl shadow-2xl mx-auto md:mx-0"
            />
          </div>

          {/* Info */}
          <div className="flex-1 pt-4">
            <div className="flex items-start justify-between gap-4">
              <div>
                <h1 className="text-3xl md:text-4xl font-bold">{movie.title}</h1>
                {movie.tagline && (
                  <p className="text-gray-400 italic mt-1">{movie.tagline}</p>
                )}
              </div>

              {/* Client Component — ต้องการ Interactive */}
              <FavoriteButton movie={movie} />
            </div>

            {/* Meta */}
            <div className="flex flex-wrap items-center gap-3 mt-4">
              <span className="text-yellow-400 font-bold text-lg">
                ⭐ {movie.vote_average.toFixed(1)}
              </span>
              <span className="text-gray-400 text-sm">
                ({movie.vote_count.toLocaleString()} รีวิว)
              </span>
              <span className="text-gray-600">•</span>
              <span className="text-gray-300 text-sm">
                {movie.release_date?.split('-')[0]}
              </span>
              <span className="text-gray-600">•</span>
              <span className="text-gray-300 text-sm">{runtime}</span>
            </div>

            {/* Genres */}
            <div className="flex flex-wrap gap-2 mt-4">
              {movie.genres.map(genre => (
                <span
                  key={genre.id}
                  className="px-3 py-1 bg-blue-600/20 text-blue-400 border border-blue-600/30 rounded-full text-sm"
                >
                  {genre.name}
                </span>
              ))}
            </div>

            {/* Overview */}
            <div className="mt-6">
              <h2 className="text-lg font-semibold mb-2">เรื่องย่อ</h2>
              <p className="text-gray-300 leading-relaxed">
                {movie.overview || 'ไม่มีข้อมูลเรื่องย่อภาษาไทย'}
              </p>
            </div>

            {/* Director */}
            {director && (
              <p className="text-gray-400 text-sm mt-4">
                กำกับโดย{' '}
                <span className="text-white font-medium">{director.name}</span>
              </p>
            )}
          </div>
        </div>

        {/* Trailer */}
        {trailer && (
          <section className="mt-12">
            <h2 className="text-2xl font-bold mb-4">🎬 ตัวอย่างหนัง</h2>
            <div className="relative aspect-video max-w-3xl rounded-xl overflow-hidden">
              <iframe
                src={`https://www.youtube.com/embed/${trailer.key}`}
                title={`Trailer: ${movie.title}`}
                allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
                allowFullScreen
                className="w-full h-full"
              />
            </div>
          </section>
        )}

        {/* Cast */}
        {mainCast.length > 0 && (
          <section className="mt-12">
            <h2 className="text-2xl font-bold mb-6">นักแสดง</h2>
            <div className="grid grid-cols-4 sm:grid-cols-6 md:grid-cols-8 gap-4">
              {mainCast.map(actor => (
                <div key={actor.id} className="text-center">
                  <div className="relative w-16 h-16 mx-auto mb-2">
                    <Image
                      src={
                        actor.profile_path
                          ? tmdb.getImageUrl(actor.profile_path, 'w200')
                          : '/images/no-avatar.png'
                      }
                      alt={actor.name}
                      fill
                      className="object-cover rounded-full"
                    />
                  </div>
                  <p className="text-xs font-medium text-white leading-tight line-clamp-2">
                    {actor.name}
                  </p>
                  <p className="text-xs text-gray-400 mt-0.5 line-clamp-1">
                    {actor.character}
                  </p>
                </div>
              ))}
            </div>
          </section>
        )}

        {/* Similar Movies */}
        {similar.results.length > 0 && (
          <section className="mt-12 mb-16">
            <h2 className="text-2xl font-bold mb-6">หนังที่คล้ายกัน</h2>
            <MovieGrid movies={similar.results.slice(0, 10)} />
          </section>
        )}
      </div>
    </main>
  )
}
```

---

### ขั้นตอนที่ 7 — หน้า Favorites

```tsx
// app/favorites/page.tsx
export const metadata = {
  title: 'หนังที่ชอบ — Movie Finder',
}

// หน้านี้ต้องเป็น Client Component เพราะดึงข้อมูลจาก Zustand
// ใช้ Client Component ครอบหน้าทั้งหมด
import { FavoritesContent } from '@/components/FavoritesContent'

export default function FavoritesPage() {
  return (
    <main className="min-h-screen bg-gray-950 text-white">
      <div className="container mx-auto px-4 py-8">
        <h1 className="text-3xl font-bold mb-8">❤️ หนังที่ชอบ</h1>
        <FavoritesContent />
      </div>
    </main>
  )
}
```

```tsx
// components/FavoritesContent.tsx
'use client'

import { useFavoriteStore } from '@/stores/favoriteStore'
import { MovieGrid } from '@/components/MovieGrid'
import Link from 'next/link'

export function FavoritesContent() {
  const { favorites, clearFavorites } = useFavoriteStore()

  if (favorites.length === 0) {
    return (
      <div className="text-center py-24">
        <p className="text-6xl mb-4">🎬</p>
        <p className="text-gray-400 text-xl mb-2">ยังไม่มีหนังที่ชอบ</p>
        <p className="text-gray-600 mb-8">กดหัวใจที่หนังที่สนใจเพื่อบันทึกไว้ที่นี่</p>
        <Link
          href="/"
          className="px-6 py-3 bg-blue-600 text-white rounded-xl font-medium hover:bg-blue-700 transition"
        >
          ค้นหาหนัง
        </Link>
      </div>
    )
  }

  return (
    <div>
      <div className="flex items-center justify-between mb-6">
        <p className="text-gray-400">{favorites.length} เรื่อง</p>
        <button
          onClick={() => {
            if (confirm('ล้างรายการหนังที่ชอบทั้งหมด?')) clearFavorites()
          }}
          className="text-sm text-red-400 hover:text-red-300 transition"
        >
          ล้างทั้งหมด
        </button>
      </div>
      <MovieGrid movies={favorites} />
    </div>
  )
}
```

---

### โครงสร้างสุดท้ายของโปรเจกต์

```
movie-finder/
├── app/
│   ├── layout.tsx                 ← Root Layout + Provider
│   ├── page.tsx                   ← หน้าแรก (SSG)
│   ├── search/
│   │   └── page.tsx               ← ค้นหา (SSR)
│   ├── movies/
│   │   └── [id]/
│   │       └── page.tsx           ← รายละเอียด (ISR 1hr)
│   └── favorites/
│       └── page.tsx               ← Favorites (Static)
│
├── components/
│   ├── MovieCard.tsx              ← Card + Skeleton
│   ├── MovieGrid.tsx              ← Grid Layout
│   ├── HeroBanner.tsx             ← Banner ใหญ่
│   ├── SearchBar.tsx              ← Client: Search Input
│   ├── FavoriteButton.tsx         ← Client: Toggle Favorite
│   ├── FavoritesContent.tsx       ← Client: รายการ Favorite
│   ├── Navbar.tsx                 ← Navigation
│   └── Pagination.tsx             ← Client: หน้า
│
├── stores/
│   └── favoriteStore.ts           ← Zustand Store
│
├── lib/
│   └── tmdb.ts                    ← API Layer
│
├── types/
│   └── movie.ts                   ← TypeScript Types
│
└── .env.local                     ← API Keys
```

---

## สรุป Part 2

สิ่งที่เรียนรู้ใน Part 2:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Server Component vs Client Component
   - Default = Server Component (ไม่ส่ง JS ไป Client)
   - 'use client' เมื่อต้องการ State, Effect, หรือ Event Handler
   - Server ห่อ Client ได้, Client รับ Server เป็น children ได้

✅ use() Hook + Suspense
   - ส่ง Promise ไปยัง Client Component แล้วอ่านด้วย use()
   - Suspense แสดง Fallback ขณะโหลด
   - Parallel Fetching ด้วยการสร้าง Promise หลายตัวพร้อมกัน

✅ Context API
   - แก้ Prop Drilling — ส่ง State ข้ามหลาย Component ได้
   - ใช้กับ Theme, Auth, User Preferences
   - สร้าง Custom Hook ครอบ useContext เสมอ

✅ Zustand — Global State Management
   - เบา, ง่าย, ไม่ต้องใช้ Provider
   - persist middleware บันทึกลง localStorage อัตโนมัติ
   - ดึงเฉพาะ State ที่ต้องการ — ลด Re-render

✅ Server Actions
   - Function ที่ทำงานบน Server เรียกได้จาก Client
   - ไม่ต้องสร้าง API Route — โค้ดน้อยลง
   - useActionState จัดการ Form State + isPending
   - revalidatePath / revalidateTag อัปเดต Cache

✅ Rendering Strategies
   - SSG: ไม่เปลี่ยนบ่อย — Blog, Landing Page
   - SSR: ข้อมูลเฉพาะบุคคล — Dashboard
   - ISR: เปลี่ยนเป็นระยะ — หน้าสินค้า, บทความ
   - CSR: Interactive สูง — Chat, Real-time

✅ Performance Optimization
   - dynamic() โหลด Component เมื่อต้องการจริง ๆ
   - next/font Self-host อัตโนมัติ ไม่มี Request ไป Google
   - Bundle Analysis หา Package ที่ใหญ่เกินไป
   - Core Web Vitals: LCP, CLS, INP

✅ Protected Routes ด้วย Middleware
   - ทำงานก่อนทุก Request
   - ตรวจสอบ Session → Redirect ถ้าไม่ได้ Login

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## คำศัพท์ที่ควรรู้ (Glossary)

| คำศัพท์ | คำอ่าน | ความหมาย |
|--------|--------|---------|
| Server Component | เซิร์ฟเวอร์ คอมโพเนนต์ | Component ที่ Render บน Server ไม่ส่ง JS ไป Client |
| Client Component | ไคลเอนต์ คอมโพเนนต์ | Component ที่ทำงานใน Browser มี State/Effect ได้ |
| Prop Drilling | พรอป ดริลลิง | ส่ง Props ผ่านหลายชั้น Component ที่ไม่ได้ใช้ |
| Context API | คอนเท็กซ์ เอพีไอ | วิธีแชร์ State ข้าม Component โดยไม่ต้องส่ง Props |
| Provider | โพรไวเดอร์ | Component ที่ห่อและจัดหา Context ให้ Component ลูก |
| Global State | โกลบอล สเตต | State ที่แชร์ทั่วทั้งแอป |
| Zustand | ซุสแตนด์ | State Management Library ที่เบาและง่าย |
| Store | สตอร์ | ที่เก็บ Global State รวมถึง Actions |
| Slice | สไลซ์ | ส่วนย่อยของ Store ที่รับผิดชอบ State เฉพาะส่วน |
| persist | เพอร์ซิสต์ | Middleware ที่บันทึก Zustand State ลง localStorage |
| Server Action | เซิร์ฟเวอร์ แอ็กชัน | Function ที่รันบน Server เรียกได้จาก Client |
| useActionState | ยูส แอ็กชัน สเตต | Hook จัดการ State ของ Form Action |
| useTransition | ยูส ทรานซิชัน | Hook แยก Urgent vs Non-urgent State Update |
| revalidatePath | รีแวลิเดตพาธ | บังคับ Regenerate Cache ของ Path ที่กำหนด |
| revalidateTag | รีแวลิเดตแท็ก | บังคับ Regenerate Cache ตาม Tag |
| SSG | เอสเอสจี | Static Site Generation — Build ครั้งเดียว |
| SSR | เอสเอสอาร์ | Server-Side Rendering — Render ทุก Request |
| ISR | ไอเอสอาร์ | Incremental Static Regeneration — Rebuild ตามเวลา |
| CSR | ซีเอสอาร์ | Client-Side Rendering — Render ใน Browser |
| Suspense | ซัสเพนส์ | React Component แสดง Fallback ขณะโหลด |
| Lazy Loading | เลซี โหลดดิง | โหลด Resource เมื่อต้องการ ไม่โหลดทั้งหมดตั้งแต่ต้น |
| Bundle | บันเดิล | ไฟล์ JavaScript ที่รวมโค้ดทั้งหมดไว้ |
| Tree Shaking | ทรี เชคกิง | การลบ Code ที่ไม่ได้ใช้ออกจาก Bundle |
| LCP | แอลซีพี | Largest Contentful Paint — ความเร็วโหลด Element ใหญ่สุด |
| CLS | ซีแอลเอส | Cumulative Layout Shift — การกระโดดของ Layout |
| INP | ไอเอ็นพี | Interaction to Next Paint — ความตอบสนองต่อ Input |
| Middleware | มิดเดิลแวร์ | Code ที่รันก่อน Request ถึง Handler |
| CDN | ซีดีเอ็น | Content Delivery Network — เครือข่ายกระจาย Static File |
| On-Demand Revalidation | ออน-ดีมานด์ รีแวลิเดชัน | Regenerate Cache ทันทีเมื่อข้อมูลเปลี่ยน |

---

## แหล่งเรียนรู้เพิ่มเติม

- **nextjs.org/docs/app/building-your-application/rendering** — Rendering Strategies อย่างละเอียด
- **nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations** — Server Actions
- **zustand-demo.pmnd.rs** — ตัวอย่าง Zustand หลายแบบ
- **github.com/pmndrs/zustand** — Zustand Source + Documentation
- **web.dev/articles/vitals** — Core Web Vitals อย่างละเอียด
- **developer.chrome.com/docs/devtools/performance** — วิธีวัด Performance ด้วย Chrome DevTools
- **nextjs.org/learn** — Tutorial อย่างเป็นทางการจาก Next.js Team

---

*หน่วยที่ 4 จบสมบูรณ์ → หน่วยถัดไป: หน่วยที่ 5 — การเชื่อมต่อ API, Authentication และการจัดการข้อมูล*
