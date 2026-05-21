# หน่วยที่ 4: React และ Next.js — Framework หลัก (Part 1)

> **หลักสูตร:** หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
> **ระดับ:** ปานกลาง (Intermediate)
> **ระยะเวลา:** 1.5 สัปดาห์ (Part 1 จาก 3 สัปดาห์)
> **ปรับปรุงล่าสุด:** พฤษภาคม 2026

---

## วัตถุประสงค์การเรียนรู้ (Part 1)

เมื่อเรียนจบ Part 1 ผู้เรียนจะสามารถ:

1. อธิบายแนวคิด Component-Based Architecture และแบ่ง UI เป็น Component ได้
2. สร้าง Functional Component ด้วย JSX และส่ง Props ระหว่าง Component ได้
3. จัดการ State ด้วย `useState` และ Lifecycle ด้วย `useEffect` ได้อย่างถูกต้อง
4. ใช้ `useRef`, `useMemo`, `useCallback` ได้เหมาะสม
5. สร้าง Custom Hook เพื่อแยก Logic ออกมาใช้ซ้ำได้
6. สร้างโปรเจกต์ Next.js 15 ด้วย App Router และเข้าใจโครงสร้างไฟล์ได้
7. ใช้ File-based Routing, Dynamic Routes และ Nested Layouts ใน Next.js ได้

---

## ก่อนเริ่ม: ทำไมถึงต้องมี React?

ก่อนจะเรียน React เราต้องเข้าใจก่อนว่า **ปัญหาที่ React แก้คืออะไร**

สมมติเราสร้างแอปรายการงาน (Todo List) ด้วย JavaScript ธรรมดา:

```javascript
// JavaScript ธรรมดา — ไม่มี React
let tasks = []

function addTask(title) {
  tasks.push({ id: Date.now(), title, done: false })
  renderTasks() // ต้อง "บอก" DOM ให้อัปเดตเองทุกครั้ง
}

function renderTasks() {
  const list = document.getElementById('task-list')
  list.innerHTML = '' // ล้างทิ้งก่อน
  
  tasks.forEach(task => {
    const li = document.createElement('li')
    li.textContent = task.title
    li.onclick = () => toggleTask(task.id) // ผูก Event ใหม่ทุกครั้ง
    list.appendChild(li)
  })
}
```

ปัญหาที่เกิดขึ้นเมื่อแอปใหญ่ขึ้น:

```
❌ ต้องจัดการ DOM เอง — ทุกครั้งที่ข้อมูลเปลี่ยน ต้องเขียนโค้ดอัปเดต UI เอง
❌ โค้ดพันกัน — Logic และ UI ปนกันจนแก้ยาก
❌ ประสิทธิภาพต่ำ — Render ทั้งหมดซ้ำทุกครั้งที่ข้อมูลเปลี่ยนแม้เล็กน้อย
❌ ใช้ซ้ำยาก — ถ้าจะใช้ UI ชิ้นเดิมในหลายที่ต้อง Copy Code
```

**React แก้ปัญหาเหล่านี้ด้วยแนวคิด 3 ข้อ:**

```
1. Component — แบ่ง UI เป็นชิ้นเล็ก ๆ ที่ใช้ซ้ำได้
2. Declarative — "บอกว่าอยากได้อะไร" ไม่ใช่ "บอกวิธีทำ"
3. Virtual DOM — React จัดการ DOM ให้อย่างมีประสิทธิภาพ
```

แนวคิด Declarative เปรียบเทียบให้เห็นชัด:

```
Imperative (JavaScript ธรรมดา) = แบบ Manual
"สร้าง Element → กำหนด Class → ใส่ Text → ผูก Event → Append ลง DOM"
— ต้องบอกทุกขั้นตอน

Declarative (React) = แบบ Automatic
"ให้แสดงรายการงานทั้งหมดในรูปแบบนี้"
— React จัดการขั้นตอนให้เอง
```

---

## 4.1 Component-Based Architecture

### Component คืออะไร

**Component** (คอมโพเนนต์) คือชิ้นส่วนของ UI ที่มี Logic และ Appearance ในตัวเอง เปรียบเหมือน "อิฐ" ที่นำมาประกอบเป็นบ้าน

ลองดูหน้าเว็บ E-commerce ทั่วไป:

```
┌────────────────────────────────────────────────┐
│                  <Navbar />                     │
│  Logo    Search Bar    Cart(3)    Login         │
├────────────────────────────────────────────────┤
│                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐     │
│  │<ProductCard│  │<ProductCard│  │<ProductCard│  │
│  │  />      │  │  />      │  │  />      │     │
│  │ 🖼 รูป   │  │ 🖼 รูป   │  │ 🖼 รูป   │     │
│  │ ชื่อสินค้า│  │ ชื่อสินค้า│  │ ชื่อสินค้า│     │
│  │ ราคา     │  │ ราคา     │  │ ราคา     │     │
│  │ [ซื้อเลย] │  │ [ซื้อเลย] │  │ [ซื้อเลย] │     │
│  └──────────┘  └──────────┘  └──────────┘     │
│                                                 │
├────────────────────────────────────────────────┤
│                  <Footer />                     │
└────────────────────────────────────────────────┘
```

จากภาพด้านบน เราแบ่งได้เป็น Component ต่าง ๆ ดังนี้:

- `<Navbar />` — แถบนำทาง ใช้ทุกหน้า
- `<ProductCard />` — การ์ดสินค้า ใช้ซ้ำหลาย ๆ อัน
- `<Footer />` — ส่วนล่าง ใช้ทุกหน้า

**ประโยชน์ของการแบ่ง Component:**

| ประโยชน์ | อธิบาย |
|---------|-------|
| **Reusability** | สร้าง `ProductCard` ครั้งเดียว ใช้ได้ทั้งหน้า Home, Search, Category |
| **Maintainability** | แก้ Bug ใน `ProductCard` ที่เดียว แก้ทุกที่พร้อมกัน |
| **Testability** | ทดสอบ Component แยกกันได้ ไม่กระทบส่วนอื่น |
| **Collaboration** | Developer หลายคนทำคนละ Component พร้อมกันได้ |

---

### Component Decomposition — การแบ่ง UI เป็น Component

**หลักการแบ่ง Component ที่ดี (Single Responsibility Principle):**

> Component หนึ่งอันควรทำหน้าที่ "หนึ่งอย่าง" เท่านั้น

ลองฝึกแบ่ง Component จากหน้า Profile:

```
หน้า Profile (ก่อนแบ่ง — ทำทุกอย่างในไฟล์เดียว)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
รูปโปรไฟล์ + ชื่อ + ตำแหน่ง + ปุ่ม Follow
แท็บ: Posts | Followers | Following
รายการโพสต์
```

```
หน้า Profile (หลังแบ่ง — แต่ละ Component ทำหน้าที่เดียว)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

<ProfilePage>
  ├── <ProfileHeader>           ← รูป + ชื่อ + ปุ่ม Follow
  │     ├── <Avatar />          ← รูปโปรไฟล์อย่างเดียว
  │     └── <FollowButton />    ← ปุ่ม Follow อย่างเดียว
  │
  ├── <ProfileTabs />           ← แท็บ Posts/Followers/Following
  │
  └── <PostList>                ← รายการโพสต์
        └── <PostCard />        ← โพสต์แต่ละอัน (ใช้ซ้ำได้)
```

**คำถามที่ช่วยตัดสินใจว่าควรแยก Component ไหม:**

```
1. ใช้ซ้ำได้ไหม? → ถ้าใช่ ควรแยก
2. ซับซ้อนเกินไปไหม? → ถ้าไฟล์มีโค้ดเยอะมาก ควรแยก
3. เปลี่ยนแปลงบ่อยแค่ไหน? → ถ้าเปลี่ยนบ่อยคนละเหตุผล ควรแยก
4. ทดสอบแยกกันได้ไหม? → ถ้าทดสอบได้แยก ควรแยก
```

---

### Props — ส่งข้อมูลระหว่าง Component

**Props** (พรอปส์) ย่อมาจาก Properties คือข้อมูลที่ส่งจาก **Parent** (พาเรนต์ — Component แม่) ไปยัง **Child** (ไชลด์ — Component ลูก)

เปรียบเหมือนพารามิเตอร์ของ Function:

```tsx
// Child Component — รับ Props มาแสดง
// กำหนด Type ของ Props ด้วย TypeScript
interface ProductCardProps {
  name: string
  price: number
  imageUrl: string
  isOnSale?: boolean  // ? หมายถึง Optional (ไม่จำเป็นต้องส่งมาก็ได้)
}

function ProductCard({ name, price, imageUrl, isOnSale = false }: ProductCardProps) {
  return (
    <div className="border rounded-lg p-4">
      <img src={imageUrl} alt={name} className="w-full h-48 object-cover" />
      <h3 className="text-lg font-bold mt-2">{name}</h3>
      <div className="flex items-center gap-2">
        <span className="text-xl font-bold text-blue-600">
          ฿{price.toLocaleString()}
        </span>
        {isOnSale && (
          <span className="bg-red-100 text-red-600 text-sm px-2 py-0.5 rounded">
            ลดราคา
          </span>
        )}
      </div>
    </div>
  )
}
```

```tsx
// Parent Component — ส่ง Props ให้ Child
function ProductList() {
  const products = [
    { id: 1, name: 'MacBook Air M3', price: 42900, imageUrl: '/mac.jpg', isOnSale: false },
    { id: 2, name: 'iPhone 16 Pro', price: 38900, imageUrl: '/iphone.jpg', isOnSale: true },
    { id: 3, name: 'AirPods Pro 2', price: 8990,  imageUrl: '/airpods.jpg', isOnSale: true },
  ]

  return (
    <div className="grid grid-cols-3 gap-4">
      {products.map(product => (
        // ส่ง Props ให้ ProductCard แต่ละอัน
        <ProductCard
          key={product.id}          // key จำเป็นเสมอเมื่อ Render List
          name={product.name}
          price={product.price}
          imageUrl={product.imageUrl}
          isOnSale={product.isOnSale}
        />
      ))}
    </div>
  )
}
```

**กฎสำคัญของ Props:**

```
✅ Props ไหลทางเดียวเท่านั้น: Parent → Child (One-way Data Flow)
✅ Child ห้ามแก้ไข Props โดยตรง — Props เป็น Read-only เสมอ
✅ ใส่ key ทุกครั้งที่ Render List — ช่วยให้ React อัปเดต DOM ได้ถูกต้อง
```

---

### `children` Prop — Component ที่ห่อ Component อื่น

`children` เป็น Prop พิเศษที่รับ JSX ที่อยู่ระหว่าง Tag เปิดและปิด:

```tsx
// Card Component ที่ห่อเนื้อหาใด ๆ ได้
interface CardProps {
  title: string
  children: React.ReactNode  // รับ JSX ใด ๆ ก็ได้
}

function Card({ title, children }: CardProps) {
  return (
    <div className="border rounded-xl shadow-sm p-6">
      <h2 className="text-xl font-bold mb-4 border-b pb-2">{title}</h2>
      {children}
    </div>
  )
}

// ใช้งาน — ใส่ JSX ใด ๆ ระหว่าง <Card> และ </Card>
function App() {
  return (
    <div>
      <Card title="ข้อมูลผู้ใช้">
        <p>ชื่อ: สมชาย ใจดี</p>
        <p>อีเมล: somchai@example.com</p>
      </Card>

      <Card title="สถิติ">
        <div className="grid grid-cols-2 gap-4">
          <div>โพสต์: 42</div>
          <div>ผู้ติดตาม: 1,024</div>
        </div>
      </Card>
    </div>
  )
}
```

`children` มีประโยชน์มากสำหรับสร้าง Layout Component เช่น `Card`, `Modal`, `Section` ที่ห่อเนื้อหาต่าง ๆ ได้

---

## 4.2 JSX — เขียน HTML ใน JavaScript

**JSX** (เจเอสเอ็กซ์) ย่อมาจาก JavaScript XML คือ Syntax Extension ที่ทำให้เขียน HTML-like ใน JavaScript ได้

```tsx
// JSX ที่เราเขียน
function Greeting({ name }: { name: string }) {
  return (
    <div className="greeting">
      <h1>สวัสดี, {name}!</h1>
      <p>วันนี้วันที่ {new Date().toLocaleDateString('th-TH')}</p>
    </div>
  )
}

// JavaScript จริง ๆ ที่ React แปลงให้ (ไม่ต้องเขียนเอง)
function Greeting({ name }) {
  return React.createElement(
    'div',
    { className: 'greeting' },
    React.createElement('h1', null, `สวัสดี, ${name}!`),
    React.createElement('p', null, `วันนี้วันที่ ${new Date().toLocaleDateString('th-TH')}`)
  )
}
```

**ความแตกต่างระหว่าง JSX กับ HTML:**

| JSX | HTML | เหตุผล |
|-----|------|-------|
| `className` | `class` | `class` เป็น Reserved Keyword ใน JavaScript |
| `htmlFor` | `for` | `for` เป็น Reserved Keyword ใน JavaScript |
| `onClick` | `onclick` | JSX ใช้ camelCase สำหรับ Event |
| `style={{ color: 'red' }}` | `style="color: red"` | JSX ใช้ Object ไม่ใช่ String |
| Tag ต้องปิดเสมอ | บางอันไม่ต้องปิด | `<br />` ไม่ใช่ `<br>` |

**กฎสำคัญของ JSX:**

```tsx
// ✅ ถูกต้อง — ต้องมี Root Element เดียว
function MyComponent() {
  return (
    <div>
      <h1>หัวข้อ</h1>
      <p>เนื้อหา</p>
    </div>
  )
}

// ✅ ถูกต้อง — ใช้ Fragment แทน div ที่ไม่จำเป็น
function MyComponent() {
  return (
    <>
      <h1>หัวข้อ</h1>
      <p>เนื้อหา</p>
    </>
  )
}

// ❌ ผิด — มีสอง Root Element
function MyComponent() {
  return (
    <h1>หัวข้อ</h1>
    <p>เนื้อหา</p>
  )
}
```

**Conditional Rendering — แสดงผลตามเงื่อนไข:**

```tsx
interface StatusBadgeProps {
  isOnline: boolean
  isPremium: boolean
  unreadCount: number
}

function StatusBadge({ isOnline, isPremium, unreadCount }: StatusBadgeProps) {
  return (
    <div className="flex items-center gap-2">
      
      {/* วิธีที่ 1: Ternary Operator — สำหรับ if/else */}
      <span className={isOnline ? 'text-green-500' : 'text-gray-400'}>
        {isOnline ? '🟢 ออนไลน์' : '⚫ ออฟไลน์'}
      </span>

      {/* วิธีที่ 2: && Operator — สำหรับ if เท่านั้น (ไม่มี else) */}
      {isPremium && (
        <span className="bg-yellow-100 text-yellow-700 px-2 py-0.5 rounded text-sm">
          ⭐ Premium
        </span>
      )}

      {/* วิธีที่ 3: ตัวเลขต้องระวัง — 0 จะถูกแสดงออกมา! */}
      {/* ❌ ผิด: {unreadCount && <span>{unreadCount}</span>} */}
      {/* ✅ ถูก: แปลงเป็น boolean ก่อน */}
      {unreadCount > 0 && (
        <span className="bg-red-500 text-white rounded-full px-2 text-xs">
          {unreadCount}
        </span>
      )}
    </div>
  )
}
```

**Rendering List:**

```tsx
interface Task {
  id: number
  title: string
  done: boolean
  priority: 'low' | 'medium' | 'high'
}

function TaskList({ tasks }: { tasks: Task[] }) {
  // กรณีที่ยังไม่มีข้อมูล
  if (tasks.length === 0) {
    return (
      <div className="text-center py-12 text-gray-500">
        <p className="text-4xl mb-2">📝</p>
        <p>ยังไม่มีงาน เพิ่มงานแรกได้เลย!</p>
      </div>
    )
  }

  const priorityColors = {
    low: 'bg-gray-100 text-gray-600',
    medium: 'bg-yellow-100 text-yellow-700',
    high: 'bg-red-100 text-red-600',
  }

  return (
    <ul className="space-y-2">
      {tasks.map(task => (
        // key ต้องเป็นค่าที่ unique และ stable — ใช้ id ไม่ใช่ index
        <li
          key={task.id}
          className={`flex items-center gap-3 p-3 rounded-lg border ${
            task.done ? 'opacity-50' : ''
          }`}
        >
          <span
            className={`text-xs px-2 py-0.5 rounded-full font-medium ${priorityColors[task.priority]}`}
          >
            {task.priority}
          </span>
          <span className={task.done ? 'line-through text-gray-400' : ''}>
            {task.title}
          </span>
        </li>
      ))}
    </ul>
  )
}
```

> **⚠️ ทำไม `key` ถึงสำคัญมาก:** React ใช้ `key` เพื่อระบุว่า Element ไหนใน List เปลี่ยนแปลง หากใช้ Index แทน key เมื่อเรียงใหม่หรือลบรายการ React จะ Re-render ผิดพลาดทำให้ Bug หาได้ยากมาก

---

## 4.3 React Hooks — หัวใจของ React สมัยใหม่

**Hook** (ฮุก) คือ Function พิเศษที่ให้ Functional Component เข้าถึงความสามารถของ React ได้ ชื่อขึ้นต้นด้วย `use` เสมอ

**กฎของ Hook (Rules of Hooks):**

```
✅ เรียก Hook ที่ระดับบนสุดของ Component เท่านั้น
✅ เรียก Hook ใน React Function Component หรือ Custom Hook เท่านั้น

❌ ห้ามเรียก Hook ใน if, for, หรือ Function ซ้อนกัน
```

```tsx
// ❌ ผิด — เรียก Hook ในเงื่อนไข
function BadComponent({ isAdmin }: { isAdmin: boolean }) {
  if (isAdmin) {
    const [count, setCount] = useState(0) // Error!
  }
}

// ✅ ถูก — เรียก Hook ที่ระดับบนสุดเสมอ
function GoodComponent({ isAdmin }: { isAdmin: boolean }) {
  const [count, setCount] = useState(0) // Hook อยู่ระดับบนสุด
  
  if (!isAdmin) return null
  return <div>{count}</div>
}
```

---

### `useState` — จัดการ State

**State** (สเตต) คือข้อมูลที่เมื่อเปลี่ยนแปลง React จะ Re-render Component นั้นให้อัตโนมัติ

```tsx
// Signature: const [state, setState] = useState(initialValue)
import { useState } from 'react'

function Counter() {
  // state = ค่าปัจจุบัน, setState = function สำหรับเปลี่ยนค่า
  const [count, setCount] = useState(0)

  return (
    <div className="flex items-center gap-4">
      <button
        onClick={() => setCount(count - 1)}
        className="px-4 py-2 bg-red-100 rounded"
      >
        −
      </button>
      <span className="text-2xl font-bold w-12 text-center">{count}</span>
      <button
        onClick={() => setCount(count + 1)}
        className="px-4 py-2 bg-green-100 rounded"
      >
        +
      </button>
      <button
        onClick={() => setCount(0)}
        className="px-4 py-2 bg-gray-100 rounded text-sm"
      >
        Reset
      </button>
    </div>
  )
}
```

**Functional Update — ใช้เมื่อค่าใหม่ขึ้นอยู่กับค่าเดิม:**

```tsx
function Counter() {
  const [count, setCount] = useState(0)

  // ❌ อาจมีปัญหาเมื่อ Update หลายครั้งเร็ว ๆ กัน
  const addThree_wrong = () => {
    setCount(count + 1)  // count = 0 → 1
    setCount(count + 1)  // count ยังเป็น 0! → 1 (ไม่ใช่ 2)
    setCount(count + 1)  // count ยังเป็น 0! → 1 (ไม่ใช่ 3)
    // ผลลัพธ์: 1 (ผิด)
  }

  // ✅ ถูกต้อง — ใช้ Functional Update (prev = ค่าล่าสุดจริง ๆ)
  const addThree_correct = () => {
    setCount(prev => prev + 1)  // 0 → 1
    setCount(prev => prev + 1)  // 1 → 2
    setCount(prev => prev + 1)  // 2 → 3
    // ผลลัพธ์: 3 (ถูกต้อง)
  }

  return (
    <div>
      <p>นับ: {count}</p>
      <button onClick={addThree_correct}>+3</button>
    </div>
  )
}
```

**State กับ Object และ Array — ต้องสร้างใหม่เสมอ:**

```tsx
interface UserProfile {
  name: string
  email: string
  age: number
}

function ProfileForm() {
  const [profile, setProfile] = useState<UserProfile>({
    name: 'สมชาย',
    email: 'somchai@example.com',
    age: 28,
  })

  // ❌ ผิด — แก้ไขตรง ๆ React จะไม่รู้ว่าเปลี่ยน
  const updateName_wrong = (name: string) => {
    profile.name = name  // Mutate โดยตรง!
    setProfile(profile)  // React เห็น Reference เดิม ไม่ Re-render
  }

  // ✅ ถูกต้อง — Spread เพื่อสร้าง Object ใหม่
  const updateName_correct = (name: string) => {
    setProfile(prev => ({ ...prev, name }))
  }

  // สำหรับ Array — เพิ่มรายการ
  const [tags, setTags] = useState<string[]>(['JavaScript', 'React'])

  const addTag = (tag: string) => {
    // ❌ ผิด: tags.push(tag) แล้ว setTags(tags)
    // ✅ ถูกต้อง: สร้าง Array ใหม่
    setTags(prev => [...prev, tag])
  }

  const removeTag = (tagToRemove: string) => {
    setTags(prev => prev.filter(tag => tag !== tagToRemove))
  }

  return (
    <div className="space-y-4">
      <input
        value={profile.name}
        onChange={e => updateName_correct(e.target.value)}
        className="border rounded px-3 py-2 w-full"
        placeholder="ชื่อ"
      />
      <div className="flex flex-wrap gap-2">
        {tags.map(tag => (
          <span
            key={tag}
            className="bg-blue-100 text-blue-700 px-3 py-1 rounded-full text-sm cursor-pointer"
            onClick={() => removeTag(tag)}
          >
            {tag} ✕
          </span>
        ))}
      </div>
    </div>
  )
}
```

---

### `useEffect` — จัดการ Side Effects

**Side Effect** (ไซด์ เอฟเฟกต์) คือการกระทำที่ส่งผลนอก Component เช่น การดึงข้อมูลจาก API, การตั้ง Timer, การ Subscribe Event

```tsx
import { useState, useEffect } from 'react'

// Signature:
// useEffect(callback, dependencies?)
//   callback — function ที่รัน
//   dependencies — Array ที่บอกว่าเมื่อไหร่ให้รัน callback

function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState<User | null>(null)
  const [isLoading, setIsLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    // ฟังก์ชันดึงข้อมูล — ต้องประกาศ async ข้างใน ไม่ใช่ที่ callback
    async function fetchUser() {
      try {
        setIsLoading(true)
        setError(null)
        const response = await fetch(`/api/users/${userId}`)
        
        if (!response.ok) {
          throw new Error(`HTTP error: ${response.status}`)
        }
        
        const data = await response.json()
        setUser(data)
      } catch (err) {
        setError(err instanceof Error ? err.message : 'เกิดข้อผิดพลาด')
      } finally {
        setIsLoading(false)
      }
    }

    fetchUser()
  }, [userId]) // [userId] = รัน useEffect ใหม่ทุกครั้งที่ userId เปลี่ยน

  if (isLoading) return <div className="animate-pulse">กำลังโหลด...</div>
  if (error) return <div className="text-red-500">Error: {error}</div>
  if (!user) return null

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  )
}
```

**ความหมายของ Dependencies Array:**

```tsx
// 1. ไม่มี Dependencies Array → รันทุกครั้งที่ Component Re-render
useEffect(() => {
  console.log('รันทุกครั้ง')
})

// 2. Empty Array [] → รันครั้งเดียวตอน Mount (Component ปรากฏบนหน้าจอ)
useEffect(() => {
  console.log('รันครั้งเดียวตอน Mount')
}, [])

// 3. มีค่าใน Array → รันเมื่อค่านั้นเปลี่ยน
useEffect(() => {
  console.log('userId เปลี่ยนเป็น:', userId)
}, [userId])
```

**Cleanup Function — ล้างค้างเมื่อ Component Unmount:**

```tsx
function SearchInput({ onSearch }: { onSearch: (query: string) => void }) {
  const [query, setQuery] = useState('')

  useEffect(() => {
    // ตั้ง Timer 500ms หลังจากพิมพ์หยุด (Debounce)
    const timer = setTimeout(() => {
      if (query) onSearch(query)
    }, 500)

    // Cleanup Function — รันก่อนที่ Effect ถัดไปจะทำงาน หรือก่อน Unmount
    return () => {
      clearTimeout(timer) // ยกเลิก Timer เดิมก่อนตั้งอันใหม่
    }
  }, [query, onSearch])

  return (
    <input
      value={query}
      onChange={e => setQuery(e.target.value)}
      placeholder="ค้นหา... (หน่วงเวลา 500ms)"
      className="border rounded px-3 py-2 w-full"
    />
  )
}
```

**ตัวอย่างอื่น ๆ ที่ต้องใช้ Cleanup:**

```tsx
// Event Listener ที่เพิ่มเองต้องลบออกด้วย
useEffect(() => {
  const handleResize = () => setWidth(window.innerWidth)
  window.addEventListener('resize', handleResize)
  
  return () => window.removeEventListener('resize', handleResize)
}, [])

// WebSocket ต้องปิดเมื่อ Unmount
useEffect(() => {
  const ws = new WebSocket('wss://api.example.com/live')
  ws.onmessage = (event) => setMessages(prev => [...prev, event.data])
  
  return () => ws.close()
}, [])

// Subscription ต้อง Unsubscribe
useEffect(() => {
  const unsubscribe = store.subscribe(setState)
  return () => unsubscribe()
}, [])
```

---

### `useRef` — อ้างอิง DOM และเก็บค่าที่ไม่ Re-render

**`useRef`** มีสองการใช้งานหลัก:

**1. อ้างอิง DOM Element โดยตรง:**

```tsx
import { useRef } from 'react'

function VideoPlayer() {
  // สร้าง ref สำหรับชี้ไปยัง <video> element
  const videoRef = useRef<HTMLVideoElement>(null)

  const handlePlay = () => {
    // .current คือ DOM Element จริง ๆ
    videoRef.current?.play()
  }

  const handlePause = () => {
    videoRef.current?.pause()
  }

  const handleSeek = (seconds: number) => {
    if (videoRef.current) {
      videoRef.current.currentTime = seconds
    }
  }

  return (
    <div>
      {/* ผูก ref กับ element ด้วย attribute ref={} */}
      <video ref={videoRef} src="/sample.mp4" className="w-full" />
      <div className="flex gap-2 mt-2">
        <button onClick={handlePlay}>▶ เล่น</button>
        <button onClick={handlePause}>⏸ หยุด</button>
        <button onClick={() => handleSeek(0)}>⏮ ต้น</button>
      </div>
    </div>
  )
}
```

**2. เก็บค่าที่ไม่ต้องการให้ Re-render:**

```tsx
function StopWatch() {
  const [time, setTime] = useState(0)
  const [isRunning, setIsRunning] = useState(false)
  
  // เก็บ intervalId ใน ref — ไม่ต้องการให้ Component Re-render เมื่อเปลี่ยน
  const intervalRef = useRef<ReturnType<typeof setInterval> | null>(null)

  const start = () => {
    setIsRunning(true)
    intervalRef.current = setInterval(() => {
      setTime(prev => prev + 1)
    }, 1000)
  }

  const stop = () => {
    setIsRunning(false)
    if (intervalRef.current) {
      clearInterval(intervalRef.current)
    }
  }

  const reset = () => {
    stop()
    setTime(0)
  }

  return (
    <div className="text-center">
      <p className="text-6xl font-mono mb-4">{time}s</p>
      <div className="flex gap-2 justify-center">
        {!isRunning ? (
          <button onClick={start} className="px-4 py-2 bg-green-500 text-white rounded">
            เริ่ม
          </button>
        ) : (
          <button onClick={stop} className="px-4 py-2 bg-red-500 text-white rounded">
            หยุด
          </button>
        )}
        <button onClick={reset} className="px-4 py-2 bg-gray-200 rounded">
          Reset
        </button>
      </div>
    </div>
  )
}
```

**ความแตกต่างระหว่าง `useState` และ `useRef`:**

| | `useState` | `useRef` |
|--|-----------|---------|
| **Re-render เมื่อเปลี่ยน** | ✅ ใช่ | ❌ ไม่ |
| **ค่าอยู่ข้าม Render** | ✅ ใช่ | ✅ ใช่ |
| **ใช้กับ DOM** | ❌ ไม่ | ✅ ใช่ |
| **ใช้เมื่อ** | ต้องแสดงผลใน UI | เก็บค่า Internal ที่ไม่ต้องแสดง |

---

### `useMemo` — Cache ผลลัพธ์การคำนวณ

**`useMemo`** ใช้เมื่อมีการคำนวณที่ "แพง" (ใช้เวลานาน) และไม่ต้องการให้คำนวณซ้ำทุกครั้งที่ Re-render

```tsx
import { useState, useMemo } from 'react'

interface Product {
  id: number
  name: string
  price: number
  category: string
  inStock: boolean
}

function ProductFilter({ products }: { products: Product[] }) {
  const [search, setSearch] = useState('')
  const [selectedCategory, setSelectedCategory] = useState('all')
  const [sortBy, setSortBy] = useState<'name' | 'price'>('name')

  // useMemo — คำนวณ filteredProducts ใหม่เฉพาะเมื่อ dependencies เปลี่ยน
  // ถ้าไม่ใช้ useMemo: คำนวณซ้ำแม้แต่เมื่อ State อื่นเปลี่ยน (เช่น แค่กด Toggle ที่ไม่เกี่ยว)
  const filteredProducts = useMemo(() => {
    console.log('กำลังกรองสินค้า...') // ดูใน Console ว่าทำงานเมื่อไหร่
    
    return products
      .filter(product => {
        const matchSearch = product.name.toLowerCase().includes(search.toLowerCase())
        const matchCategory = selectedCategory === 'all' || product.category === selectedCategory
        return matchSearch && matchCategory && product.inStock
      })
      .sort((a, b) => {
        if (sortBy === 'price') return a.price - b.price
        return a.name.localeCompare(b.name, 'th')
      })
  }, [products, search, selectedCategory, sortBy]) // คำนวณใหม่เมื่อ 4 ค่านี้เปลี่ยน

  // useMemo สำหรับดึง categories จาก products (ไม่ซ้ำ)
  const categories = useMemo(() => {
    const cats = new Set(products.map(p => p.category))
    return ['all', ...cats]
  }, [products]) // คำนวณใหม่เฉพาะเมื่อ products เปลี่ยน

  return (
    <div>
      <div className="flex gap-2 mb-4">
        <input
          value={search}
          onChange={e => setSearch(e.target.value)}
          placeholder="ค้นหาสินค้า..."
          className="border rounded px-3 py-2 flex-1"
        />
        <select
          value={selectedCategory}
          onChange={e => setSelectedCategory(e.target.value)}
          className="border rounded px-3 py-2"
        >
          {categories.map(cat => (
            <option key={cat} value={cat}>
              {cat === 'all' ? 'ทุกหมวด' : cat}
            </option>
          ))}
        </select>
      </div>
      
      <p className="text-sm text-gray-500 mb-2">
        พบ {filteredProducts.length} รายการ
      </p>
      
      <div className="grid grid-cols-2 md:grid-cols-3 gap-4">
        {filteredProducts.map(product => (
          <div key={product.id} className="border rounded-lg p-3">
            <p className="font-medium">{product.name}</p>
            <p className="text-blue-600">฿{product.price.toLocaleString()}</p>
          </div>
        ))}
      </div>
    </div>
  )
}
```

> **💡 เมื่อไหรควรใช้ `useMemo`:** ใช้เมื่อการคำนวณ "แพง" จริง ๆ เช่น กรอง/เรียงรายการขนาดใหญ่, คำนวณคณิตศาสตร์ซับซ้อน ไม่ควรใช้กับการคำนวณง่าย ๆ เพราะ `useMemo` เองก็มี Overhead เล็กน้อย

---

### `useCallback` — Cache Function

**`useCallback`** คล้ายกับ `useMemo` แต่ Cache ตัว **Function** แทนที่จะเป็นค่า

```tsx
import { useState, useCallback, memo } from 'react'

// memo() — ห่อ Component เพื่อไม่ให้ Re-render ถ้า Props ไม่เปลี่ยน
const TaskItem = memo(function TaskItem({
  task,
  onToggle,
  onDelete,
}: {
  task: Task
  onToggle: (id: number) => void
  onDelete: (id: number) => void
}) {
  console.log(`TaskItem ${task.id} rendered`) // ดูว่า Render กี่ครั้ง

  return (
    <li className="flex items-center gap-3 p-3 border rounded">
      <input
        type="checkbox"
        checked={task.done}
        onChange={() => onToggle(task.id)}
      />
      <span className={task.done ? 'line-through text-gray-400' : ''}>
        {task.title}
      </span>
      <button
        onClick={() => onDelete(task.id)}
        className="ml-auto text-red-400 hover:text-red-600"
      >
        ลบ
      </button>
    </li>
  )
})

function TaskManager() {
  const [tasks, setTasks] = useState<Task[]>([
    { id: 1, title: 'เรียน React', done: false },
    { id: 2, title: 'ทำโปรเจกต์', done: true },
  ])
  const [newTitle, setNewTitle] = useState('')

  // ❌ ถ้าไม่ใช้ useCallback: สร้าง Function ใหม่ทุกครั้งที่ Re-render
  // ทำให้ memo() ของ TaskItem ไม่ได้ผล เพราะ Props เปลี่ยนทุกครั้ง

  // ✅ useCallback — สร้าง Function ใหม่เฉพาะเมื่อ dependencies เปลี่ยน
  const handleToggle = useCallback((id: number) => {
    setTasks(prev =>
      prev.map(task => (task.id === id ? { ...task, done: !task.done } : task))
    )
  }, []) // [] = ไม่มี dependencies → Function เดิมตลอด

  const handleDelete = useCallback((id: number) => {
    setTasks(prev => prev.filter(task => task.id !== id))
  }, [])

  const handleAdd = () => {
    if (!newTitle.trim()) return
    setTasks(prev => [
      ...prev,
      { id: Date.now(), title: newTitle.trim(), done: false },
    ])
    setNewTitle('')
  }

  return (
    <div className="max-w-md mx-auto p-4">
      <div className="flex gap-2 mb-4">
        <input
          value={newTitle}
          onChange={e => setNewTitle(e.target.value)}
          onKeyDown={e => e.key === 'Enter' && handleAdd()}
          placeholder="เพิ่มงานใหม่..."
          className="border rounded px-3 py-2 flex-1"
        />
        <button
          onClick={handleAdd}
          className="px-4 py-2 bg-blue-500 text-white rounded"
        >
          เพิ่ม
        </button>
      </div>

      <ul className="space-y-2">
        {tasks.map(task => (
          <TaskItem
            key={task.id}
            task={task}
            onToggle={handleToggle}
            onDelete={handleDelete}
          />
        ))}
      </ul>
    </div>
  )
}
```

**สรุปความสัมพันธ์ระหว่าง `useMemo`, `useCallback`, และ `memo`:**

```
memo(Component)
  └── ป้องกัน Re-render ถ้า Props ไม่เปลี่ยน
      ├── ต้องใช้ร่วมกับ useCallback เมื่อ Props เป็น Function
      └── ต้องใช้ร่วมกับ useMemo เมื่อ Props เป็น Object/Array ที่คำนวณ

useCallback(fn, deps)  ≈  useMemo(() => fn, deps)
  └── Cache ตัว Function
      └── มักใช้คู่กับ memo() เพื่อไม่ให้ Child Re-render โดยไม่จำเป็น
```

---

## 4.4 Custom Hooks — แยก Logic ออกมาใช้ซ้ำ

**Custom Hook** คือ Function ที่ขึ้นต้นด้วย `use` และใช้ React Hooks ข้างใน เป็นวิธีที่ดีที่สุดในการ "แยก Logic ออกจาก UI"

ลองดูตัวอย่างก่อนและหลังใช้ Custom Hook:

**ก่อน — Logic และ UI ปนกัน:**

```tsx
// ❌ ก่อน: Component ทำหลายอย่างพร้อมกัน อ่านยาก และนำไปใช้ซ้ำไม่ได้
function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState(null)
  const [isLoading, setIsLoading] = useState(true)
  const [error, setError] = useState(null)

  useEffect(() => {
    async function fetch() {
      // ... โค้ดดึงข้อมูล 10+ บรรทัด
    }
    fetch()
  }, [userId])

  // ... UI Code อีกยาว
}
```

**หลัง — แยก Logic ออกเป็น Custom Hook:**

```tsx
// ✅ หลัง: Custom Hook จัดการ Logic ทั้งหมด
// hooks/useFetch.ts

interface FetchState<T> {
  data: T | null
  isLoading: boolean
  error: string | null
  refetch: () => void
}

function useFetch<T>(url: string): FetchState<T> {
  const [data, setData] = useState<T | null>(null)
  const [isLoading, setIsLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)
  const [trigger, setTrigger] = useState(0) // ใช้สำหรับ refetch

  useEffect(() => {
    let cancelled = false // ป้องกัน Race Condition

    async function fetchData() {
      try {
        setIsLoading(true)
        setError(null)

        const response = await fetch(url)
        if (!response.ok) throw new Error(`HTTP ${response.status}`)

        const json = await response.json()
        if (!cancelled) setData(json) // ตรวจก่อนว่ายัง Mount อยู่
      } catch (err) {
        if (!cancelled) {
          setError(err instanceof Error ? err.message : 'เกิดข้อผิดพลาด')
        }
      } finally {
        if (!cancelled) setIsLoading(false)
      }
    }

    fetchData()

    return () => {
      cancelled = true // Cleanup — ป้องกัน setState หลัง Unmount
    }
  }, [url, trigger])

  const refetch = useCallback(() => setTrigger(prev => prev + 1), [])

  return { data, isLoading, error, refetch }
}
```

```tsx
// Component สะอาด — แค่แสดงผล
function UserProfile({ userId }: { userId: number }) {
  const { data: user, isLoading, error, refetch } = useFetch<User>(
    `/api/users/${userId}`
  )

  if (isLoading) return <Skeleton />
  if (error) return <ErrorMessage message={error} onRetry={refetch} />
  if (!user) return null

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <button onClick={refetch}>รีโหลด</button>
    </div>
  )
}

// ใช้ Hook เดิมในอีก Component ได้เลย!
function ProductDetail({ productId }: { productId: string }) {
  const { data: product, isLoading } = useFetch<Product>(
    `/api/products/${productId}`
  )
  // ...
}
```

**ตัวอย่าง Custom Hook ที่มีประโยชน์จริง:**

**`useLocalStorage` — ซิงค์ State กับ localStorage:**

```tsx
// hooks/useLocalStorage.ts
function useLocalStorage<T>(key: string, initialValue: T) {
  // อ่านค่าจาก localStorage ตอน Initialize
  const [storedValue, setStoredValue] = useState<T>(() => {
    if (typeof window === 'undefined') return initialValue // SSR Guard
    try {
      const item = window.localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch {
      return initialValue
    }
  })

  const setValue = useCallback((value: T | ((prev: T) => T)) => {
    setStoredValue(prev => {
      const newValue = value instanceof Function ? value(prev) : value
      try {
        window.localStorage.setItem(key, JSON.stringify(newValue))
      } catch (err) {
        console.error('localStorage error:', err)
      }
      return newValue
    })
  }, [key])

  const removeValue = useCallback(() => {
    localStorage.removeItem(key)
    setStoredValue(initialValue)
  }, [key, initialValue])

  return [storedValue, setValue, removeValue] as const
}

// ใช้งาน
function ThemeToggle() {
  const [theme, setTheme] = useLocalStorage<'light' | 'dark'>('theme', 'light')

  return (
    <button onClick={() => setTheme(prev => prev === 'light' ? 'dark' : 'light')}>
      {theme === 'light' ? '🌙 Dark Mode' : '☀️ Light Mode'}
    </button>
  )
}
```

**`useDebounce` — หน่วงเวลาการค้นหา:**

```tsx
// hooks/useDebounce.ts
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay)
    return () => clearTimeout(timer)
  }, [value, delay])

  return debouncedValue
}

// ใช้งาน
function SearchBar() {
  const [searchTerm, setSearchTerm] = useState('')
  const debouncedSearch = useDebounce(searchTerm, 500) // หน่วง 500ms

  // เรียก API เฉพาะเมื่อ debouncedSearch เปลี่ยน (หลังหยุดพิมพ์ 500ms)
  const { data: results } = useFetch<SearchResult[]>(
    debouncedSearch ? `/api/search?q=${encodeURIComponent(debouncedSearch)}` : ''
  )

  return (
    <div>
      <input
        value={searchTerm}
        onChange={e => setSearchTerm(e.target.value)}
        placeholder="ค้นหา... (รอ 500ms หลังหยุดพิมพ์)"
        className="border rounded px-3 py-2 w-full"
      />
      {debouncedSearch && (
        <p className="text-sm text-gray-500 mt-1">
          ค้นหา: "{debouncedSearch}"
        </p>
      )}
    </div>
  )
}
```

---

## 4.5 เริ่มต้นกับ Next.js 15

### Next.js คืออะไร และทำไมถึงสำคัญ

**Next.js** (เน็กต์ เจเอส) คือ **Meta-Framework** (เมตา เฟรมเวิร์ก) — Framework ที่ครอบ React อีกทีหนึ่ง ให้ความสามารถที่ React ล้วน ๆ ไม่มี

```
React (Library)          Next.js (Meta-Framework)
━━━━━━━━━━━━━━━━        ━━━━━━━━━━━━━━━━━━━━━━━━
✅ UI Components         ✅ ทุกอย่างใน React +
✅ State Management      ✅ Routing (File-based)
✅ Event Handling        ✅ Server-Side Rendering (SSR)
                         ✅ Static Site Generation (SSG)
                         ✅ API Routes
                         ✅ Image Optimization
                         ✅ Font Optimization
                         ✅ Server Components
                         ✅ Server Actions
```

**ทำไม Next.js ถึงกลายเป็นมาตรฐาน:**

React เป็นแค่ Library สำหรับสร้าง UI แต่เมื่อสร้างแอปจริง ยังต้องการอีกหลายอย่าง:

```
ปัญหาเมื่อใช้ React เปล่า ๆ:
├── Routing — ต้องติดตั้ง React Router เอง
├── SEO — React Render ใน Browser → Crawler อ่าน HTML เปล่า
├── Performance — JavaScript Bundle ใหญ่ → โหลดช้า
├── Image Optimization — ต้องทำเอง
└── API — ต้องสร้าง Backend แยก

Next.js แก้ทุกปัญหาเหล่านี้ในที่เดียว
```

---

### สร้างโปรเจกต์ Next.js 15

```bash
# สร้างโปรเจกต์ใหม่ด้วย pnpm
pnpm create next-app@latest my-app

# คำถามที่จะถาม (แนะนำตอบดังนี้):
# ✔ Would you like to use TypeScript? → Yes
# ✔ Would you like to use ESLint? → Yes
# ✔ Would you like to use Tailwind CSS? → Yes
# ✔ Would you like your code inside a `src/` directory? → No
# ✔ Would you like to use App Router? (recommended) → Yes
# ✔ Would you like to use Turbopack for next dev? → Yes
# ✔ Would you like to customize the import alias (@/* by default)? → No

cd my-app
pnpm dev
# เปิดเบราว์เซอร์ไปที่ http://localhost:3000
```

---

### โครงสร้างโปรเจกต์ Next.js 15

```
my-app/
├── app/                          ← App Router (โฟลเดอร์หลัก)
│   ├── layout.tsx                ← Root Layout (ห่อทุกหน้า)
│   ├── page.tsx                  ← หน้าแรก → route: /
│   ├── globals.css               ← Global Styles
│   ├── favicon.ico
│   │
│   ├── about/
│   │   └── page.tsx              ← route: /about
│   │
│   ├── products/
│   │   ├── page.tsx              ← route: /products
│   │   └── [id]/
│   │       └── page.tsx          ← route: /products/123 (Dynamic)
│   │
│   └── dashboard/
│       ├── layout.tsx            ← Dashboard Layout (ใช้เฉพาะ /dashboard/*)
│       ├── page.tsx              ← route: /dashboard
│       └── settings/
│           └── page.tsx          ← route: /dashboard/settings
│
├── components/                   ← Shared Components
│   ├── ui/                       ← shadcn/ui Components
│   │   ├── button.tsx
│   │   └── card.tsx
│   ├── Navbar.tsx
│   └── Footer.tsx
│
├── lib/                          ← Utility Functions, API Clients
│   ├── utils.ts
│   └── api.ts
│
├── hooks/                        ← Custom Hooks
│   ├── useFetch.ts
│   └── useLocalStorage.ts
│
├── types/                        ← TypeScript Type Definitions
│   └── index.ts
│
├── public/                       ← Static Files (รูปภาพ, favicon)
│   └── images/
│
├── .env.local                    ← Environment Variables (อย่า Commit!)
├── next.config.ts
├── tailwind.config.ts
└── tsconfig.json
```

---

### ไฟล์สำคัญใน App Router

**`app/layout.tsx` — Root Layout:**

```tsx
// app/layout.tsx
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'

const inter = Inter({ subsets: ['latin'] })

// Metadata — กำหนด <title> และ <meta> อัตโนมัติ
export const metadata: Metadata = {
  title: {
    default: 'My App',
    template: '%s | My App', // เช่น "About | My App"
  },
  description: 'แอปพลิเคชันตัวอย่าง Next.js 15',
  openGraph: {
    title: 'My App',
    description: 'แอปพลิเคชันตัวอย่าง Next.js 15',
    images: ['/og-image.png'],
  },
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="th">
      <body className={`${inter.className} min-h-screen bg-white`}>
        <Navbar />
        <main className="container mx-auto px-4 py-8">
          {children}
        </main>
        <Footer />
      </body>
    </html>
  )
}
```

**`app/page.tsx` — หน้าแรก:**

```tsx
// app/page.tsx
import Link from 'next/link'
import Image from 'next/image'

// Metadata สำหรับหน้านี้โดยเฉพาะ (Override Root Metadata)
export const metadata = {
  title: 'หน้าแรก',  // กลายเป็น "หน้าแรก | My App"
}

export default function HomePage() {
  return (
    <div className="text-center py-20">
      <Image
        src="/hero.png"
        alt="Hero Image"
        width={800}
        height={400}
        priority  // โหลดก่อนเพราะอยู่บน Fold
        className="mx-auto rounded-2xl"
      />
      <h1 className="text-5xl font-bold mt-8 mb-4">
        ยินดีต้อนรับสู่ My App
      </h1>
      <p className="text-xl text-gray-600 mb-8">
        สร้างด้วย Next.js 15, React 19, Tailwind CSS
      </p>
      <div className="flex gap-4 justify-center">
        <Link
          href="/products"
          className="px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition"
        >
          ดูสินค้าทั้งหมด
        </Link>
        <Link
          href="/about"
          className="px-6 py-3 border border-gray-300 rounded-lg hover:bg-gray-50 transition"
        >
          เกี่ยวกับเรา
        </Link>
      </div>
    </div>
  )
}
```

---

## 4.6 Routing ใน Next.js 15

### File-based Routing — โฟลเดอร์ = Route

ใน Next.js ไม่ต้องตั้งค่า Router เลย แค่สร้างไฟล์ในโฟลเดอร์ที่ถูกต้อง:

```
ไฟล์                              Route (URL)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
app/page.tsx                    →  /
app/about/page.tsx              →  /about
app/blog/page.tsx               →  /blog
app/blog/[slug]/page.tsx        →  /blog/my-post (Dynamic)
app/shop/[...path]/page.tsx     →  /shop/a/b/c (Catch-all)
app/(auth)/login/page.tsx       →  /login (Route Group)
app/api/users/route.ts          →  /api/users (API Route)
```

**ไฟล์พิเศษในแต่ละโฟลเดอร์:**

| ไฟล์ | หน้าที่ |
|------|--------|
| `page.tsx` | หน้าที่แสดงผล (Route) |
| `layout.tsx` | Layout ที่ใช้ร่วมกัน |
| `loading.tsx` | Skeleton UI ขณะโหลด |
| `error.tsx` | หน้า Error |
| `not-found.tsx` | หน้า 404 |
| `route.ts` | API Endpoint |

---

### Dynamic Routes — Route ที่มีพารามิเตอร์

```tsx
// app/products/[id]/page.tsx
// Route นี้รองรับ: /products/1, /products/abc, /products/macbook-air

// กำหนด Type ของ params
interface ProductPageProps {
  params: Promise<{ id: string }>
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>
}

// ใน Next.js 15 params เป็น Promise → ต้อง await
export default async function ProductPage({ params, searchParams }: ProductPageProps) {
  const { id } = await params
  const { tab } = await searchParams  // /products/1?tab=reviews

  // ดึงข้อมูลบน Server (ไม่ส่ง JavaScript ไป Client)
  const product = await fetchProduct(id)

  if (!product) {
    notFound() // แสดงหน้า not-found.tsx
  }

  return (
    <div>
      <h1>{product.name}</h1>
      <p>฿{product.price.toLocaleString()}</p>
      
      {/* Tab Navigation */}
      <div className="flex gap-4 border-b mt-6">
        {['details', 'reviews', 'shipping'].map(t => (
          <Link
            key={t}
            href={`/products/${id}?tab=${t}`}
            className={`pb-2 capitalize ${
              tab === t
                ? 'border-b-2 border-blue-600 text-blue-600'
                : 'text-gray-600'
            }`}
          >
            {t}
          </Link>
        ))}
      </div>
    </div>
  )
}

// Static Generation — บอก Next.js ว่ามี Path อะไรบ้าง (ทำ Build ล่วงหน้า)
export async function generateStaticParams() {
  const products = await fetchAllProducts()
  
  return products.map(product => ({
    id: product.id.toString(),
  }))
}

// Metadata แบบ Dynamic
export async function generateMetadata({ params }: ProductPageProps) {
  const { id } = await params
  const product = await fetchProduct(id)

  return {
    title: product?.name ?? 'สินค้าไม่พบ',
    description: product?.description,
    openGraph: {
      images: product?.imageUrl ? [product.imageUrl] : [],
    },
  }
}
```

---

### Nested Layouts — Layout ซ้อนกัน

Layout ช่วยให้แชร์ UI ระหว่างหลาย Page ได้โดยไม่ต้อง Re-render:

```tsx
// app/dashboard/layout.tsx
// Layout นี้ครอบ /dashboard, /dashboard/settings, /dashboard/analytics ฯลฯ

import { Sidebar } from '@/components/Sidebar'
import { DashboardHeader } from '@/components/DashboardHeader'

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <div className="flex min-h-screen">
      {/* Sidebar คงอยู่เมื่อ Navigate ระหว่าง Dashboard Pages */}
      <Sidebar className="w-64 border-r bg-gray-50" />
      
      <div className="flex-1 flex flex-col">
        <DashboardHeader />
        <main className="flex-1 p-6">
          {children}  {/* เนื้อหาของแต่ละ Page */}
        </main>
      </div>
    </div>
  )
}
```

```
การ Render เมื่อไปที่ /dashboard/settings:

RootLayout (app/layout.tsx)
  └── DashboardLayout (app/dashboard/layout.tsx)
        └── SettingsPage (app/dashboard/settings/page.tsx)
```

**ประโยชน์ของ Nested Layout:**

```
เมื่อ Navigate จาก /dashboard/settings → /dashboard/analytics:
✅ RootLayout ไม่ Re-render (Navbar, Footer คงอยู่)
✅ DashboardLayout ไม่ Re-render (Sidebar คงอยู่)
✅ เฉพาะ children (เนื้อหา) เปลี่ยน → เร็วมาก
```

---

### Route Groups — จัดกลุ่มโดยไม่กระทบ URL

วงเล็บ `(groupName)` ใช้จัดกลุ่ม Route ที่ใช้ Layout เดียวกัน โดยไม่ปรากฏใน URL:

```
app/
├── (marketing)/           ← Route Group (ไม่ปรากฏใน URL)
│   ├── layout.tsx         ← Layout สำหรับหน้า Marketing
│   ├── page.tsx           → /
│   ├── about/
│   │   └── page.tsx       → /about
│   └── pricing/
│       └── page.tsx       → /pricing
│
└── (dashboard)/           ← Route Group อีกกลุ่ม
    ├── layout.tsx         ← Layout สำหรับ Dashboard (มี Sidebar)
    ├── dashboard/
    │   └── page.tsx       → /dashboard
    └── analytics/
        └── page.tsx       → /analytics
```

---

### `loading.tsx` และ `error.tsx` — UI States

```tsx
// app/products/loading.tsx
// แสดงอัตโนมัติขณะโหลด /products

export default function ProductsLoading() {
  return (
    <div className="grid grid-cols-3 gap-4">
      {Array.from({ length: 6 }).map((_, i) => (
        <div key={i} className="border rounded-lg p-4 animate-pulse">
          <div className="bg-gray-200 h-48 rounded-lg mb-3" />
          <div className="bg-gray-200 h-4 rounded w-3/4 mb-2" />
          <div className="bg-gray-200 h-4 rounded w-1/2" />
        </div>
      ))}
    </div>
  )
}
```

```tsx
// app/products/error.tsx
// แสดงเมื่อเกิด Error ใน /products
'use client' // error.tsx ต้องเป็น Client Component เสมอ

export default function ProductsError({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  return (
    <div className="text-center py-20">
      <p className="text-6xl mb-4">😵</p>
      <h2 className="text-2xl font-bold mb-2">เกิดข้อผิดพลาด</h2>
      <p className="text-gray-600 mb-6">{error.message}</p>
      <button
        onClick={reset}
        className="px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700"
      >
        ลองใหม่อีกครั้ง
      </button>
    </div>
  )
}
```

```tsx
// app/not-found.tsx
// แสดงเมื่อ Route ไม่พบ (404)
import Link from 'next/link'

export default function NotFound() {
  return (
    <div className="text-center py-20">
      <p className="text-8xl font-bold text-gray-200 mb-4">404</p>
      <h2 className="text-2xl font-bold mb-2">ไม่พบหน้าที่ต้องการ</h2>
      <p className="text-gray-600 mb-6">หน้าที่คุณกำลังมองหาอาจถูกลบหรือย้ายไปแล้ว</p>
      <Link href="/" className="px-6 py-3 bg-blue-600 text-white rounded-lg">
        กลับหน้าแรก
      </Link>
    </div>
  )
}
```

---

### `next/link` และ `next/image` — Built-in Optimization

**`next/link` — Client-side Navigation:**

```tsx
import Link from 'next/link'

function Navbar() {
  return (
    <nav className="flex gap-6">
      {/* Link ทำ Client-side Navigation — ไม่ Reload หน้า */}
      <Link href="/" className="font-medium hover:text-blue-600">
        หน้าแรก
      </Link>
      <Link href="/products">สินค้า</Link>
      <Link href="/about">เกี่ยวกับ</Link>

      {/* Prefetch — โหลด Page ล่วงหน้าเมื่อ Link ปรากฏบนหน้าจอ (Default) */}
      <Link href="/dashboard" prefetch={true}>
        Dashboard
      </Link>

      {/* External Link — ใช้ <a> ธรรมดาหรือกำหนด target */}
      <Link href="https://github.com" target="_blank" rel="noopener noreferrer">
        GitHub
      </Link>
    </nav>
  )
}
```

**`next/image` — Image Optimization:**

```tsx
import Image from 'next/image'

function ProductCard({ product }: { product: Product }) {
  return (
    <div className="border rounded-xl overflow-hidden">
      {/* Next.js จัดการให้อัตโนมัติ:
          - แปลงเป็น WebP/AVIF (ขนาดเล็กกว่า 30-50%)
          - Lazy Load (โหลดเมื่อใกล้ถึง viewport)
          - ป้องกัน Layout Shift (กำหนด width/height ล่วงหน้า)
          - Responsive (สร้าง srcset อัตโนมัติ) */}
      <Image
        src={product.imageUrl}
        alt={product.name}
        width={400}
        height={300}
        className="w-full h-48 object-cover"
      />
      
      {/* สำหรับรูปที่ fill Container */}
      <div className="relative h-64">
        <Image
          src="/hero.jpg"
          alt="Hero"
          fill                     // เต็ม Container
          className="object-cover"
          sizes="(max-width: 768px) 100vw, 50vw"  // ช่วย Browser เลือก Size
        />
      </div>

      <div className="p-4">
        <h3>{product.name}</h3>
      </div>
    </div>
  )
}
```

---

## สรุป Part 1

สิ่งที่เรียนรู้ใน Part 1:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Component คือหน่วยย่อยของ UI — แบ่งให้ถูกทำให้ใช้ซ้ำและแก้ไขง่าย

✅ Props ส่งข้อมูล Parent → Child ทิศทางเดียว Children ห้ามแก้ Props

✅ JSX คือ HTML ใน JavaScript — ใช้ className, camelCase Event, ปิด Tag เสมอ

✅ useState จัดการข้อมูลที่เปลี่ยนแปลงใน Component
   - เปลี่ยน Object/Array ต้องสร้างใหม่ ห้าม Mutate โดยตรง

✅ useEffect จัดการ Side Effect เช่น ดึงข้อมูล, Subscribe Event
   - Dependencies Array ควบคุมว่า Effect รันเมื่อไหร่
   - Cleanup Function ป้องกัน Memory Leak

✅ useRef อ้างอิง DOM Element หรือเก็บค่าที่ไม่ต้องการให้ Re-render

✅ useMemo Cache ผลลัพธ์การคำนวณ, useCallback Cache Function
   - ใช้คู่กับ memo() เพื่อป้องกัน Child Re-render โดยไม่จำเป็น

✅ Custom Hook = แยก Logic ออกมาเป็น Function ขึ้นต้นด้วย use
   - ทำให้ Logic นำไปใช้ซ้ำได้, Component อ่านง่ายขึ้น

✅ Next.js 15 App Router — โฟลเดอร์ = Route
   - page.tsx, layout.tsx, loading.tsx, error.tsx
   - Dynamic Routes ด้วย [id], Nested Layout

✅ next/link ทำ Client Navigation, next/image Optimize รูปอัตโนมัติ

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## คำศัพท์ที่ควรรู้ (Glossary)

| คำศัพท์ | คำอ่าน | ความหมาย |
|--------|--------|---------|
| Component | คอมโพเนนต์ | ชิ้นส่วน UI ที่ใช้ซ้ำได้ |
| Props | พรอปส์ | ข้อมูลที่ส่งจาก Parent ไป Child |
| State | สเตต | ข้อมูลที่เมื่อเปลี่ยนทำให้ Re-render |
| Hook | ฮุก | Function พิเศษที่เข้าถึงความสามารถ React |
| Re-render | รี-เรนเดอร์ | React วาด UI ใหม่เพราะข้อมูลเปลี่ยน |
| Virtual DOM | เวอร์ชวล ดอม | DOM จำลองใน Memory ที่ React ใช้คำนวณผล |
| JSX | เจเอสเอ็กซ์ | Syntax เขียน HTML ใน JavaScript |
| Side Effect | ไซด์ เอฟเฟกต์ | การกระทำที่ส่งผลนอก Component |
| Cleanup | คลีนอัพ | Function ล้างค้างเมื่อ Component Unmount |
| Mount | เมาท์ | Component ปรากฏบนหน้าจอครั้งแรก |
| Unmount | อันเมาท์ | Component หายไปจากหน้าจอ |
| Memoize | เมโมไอซ์ | Cache ผลลัพธ์เพื่อไม่ต้องคำนวณซ้ำ |
| Custom Hook | คัสตอม ฮุก | Hook ที่สร้างเองโดยใช้ Hook อื่น |
| App Router | แอป เราเตอร์ | ระบบ Routing ของ Next.js ที่ใช้ Folder |
| Server Component | เซิร์ฟเวอร์ คอมโพเนนต์ | Component ที่ Render บน Server |
| Client Component | ไคลเอนต์ คอมโพเนนต์ | Component ที่ทำงานใน Browser |
| Route | เราต์ | URL Path ที่แมปกับหน้า |
| Dynamic Route | ไดนามิก เราต์ | Route ที่มีพารามิเตอร์เปลี่ยนได้ |
| Layout | เลเอาต์ | โครงสร้างที่ใช้ร่วมกันระหว่าง Page |
| Nested Layout | เนสเต็ด เลเอาต์ | Layout ซ้อนกันหลายชั้น |
| Meta-Framework | เมตา เฟรมเวิร์ก | Framework ที่ครอบ Framework อีกทีหนึ่ง |
| Prefetch | พรีเฟทช์ | โหลดข้อมูลล่วงหน้าก่อนที่ผู้ใช้จะคลิก |
| Race Condition | เรซ คอนดิชัน | Bug เมื่อหลาย Async ทำงานพร้อมกันแล้วผลลัพธ์ไม่แน่นอน |

---

## แหล่งเรียนรู้เพิ่มเติม

- **react.dev** — เอกสาร React อย่างเป็นทางการ (ใหม่และสมบูรณ์มาก)
- **nextjs.org/docs** — เอกสาร Next.js อย่างเป็นทางการ
- **react.dev/learn/thinking-in-react** — วิธีคิดแบบ React
- **react.dev/reference/react** — Reference ของ Hook ทั้งหมด

---

*Part 1 จบแล้ว → ต่อไป Part 2: React Server Components, State Management ด้วย Zustand, Server Actions และ Performance Optimization*
