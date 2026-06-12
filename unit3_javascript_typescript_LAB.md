# หน่วยที่ 3: JavaScript สมัยใหม่และ TypeScript — แลปปฏิบัติ

> **หลักสูตร:** หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
> **ระดับ:** ผู้เริ่มต้น (Beginner)
> **ระยะเวลา:** 4 สัปดาห์
> **ปรับปรุงล่าสุด:** พฤษภาคม 2026

---

## วัตถุประสงค์การเรียนรู้

เมื่อเรียนจบหน่วยนี้ ผู้เรียนจะสามารถ:

1. เขียน JavaScript สมัยใหม่ (ES2022+) ด้วย Syntax ที่ถูกต้องและกระชับได้
2. ใช้ Array Methods, Destructuring, และ Operator สมัยใหม่ได้อย่างคล่องแคล่ว
3. เขียน Asynchronous JavaScript ด้วย `async/await` และจัดการ Error ได้
4. ดึงข้อมูลจาก API ด้วย `fetch()` และจัดการ Response ได้
5. จัดการ DOM และ Event ได้อย่างมีประสิทธิภาพ
6. จัดโครงสร้างโค้ดด้วย ES Modules และตั้งค่า ESLint + Prettier ได้
7. เขียน TypeScript ด้วย Type Annotations, Interface และ Generics ได้

---

## 🗂️ การเตรียมโฟลเดอร์สำหรับแลปปฏิบัติ

### ที่อยู่โปรเจกต์บนเครื่อง Windows 10

นักเรียนแต่ละคนให้สร้างโฟลเดอร์งานไว้ที่:

```
C:\Users\<ชื่อของตัวเอง>\Documents\<ชื่อของตัวเอง>\unit3-js-lab\
```

**ตัวอย่าง:** ถ้าชื่อ `abcd` ให้สร้างที่:

```
C:\Users\abcd\Documents\abcd\unit3-js-lab\
```

### วิธีสร้างโฟลเดอร์

เปิด **Command Prompt** (กด `Win + R` พิมพ์ `cmd` แล้วกด Enter) แล้วพิมพ์:

```cmd
mkdir "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab"
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab"
```

ตรวจสอบว่าอยู่ในโฟลเดอร์ที่ถูกต้อง:

```cmd
echo %CD%
```

ผลลัพธ์ที่ควรเห็น:
```
C:\Users\abcd\Documents\abcd\unit3-js-lab
```

จากนั้นเปิด VS Code ในโฟลเดอร์นี้:

```cmd
code .
```

---

## 📁 โครงสร้างโฟลเดอร์ทั้งหมดของแลปนี้

```
C:\Users\abcd\Documents\abcd\unit3-js-lab\
├── lab01-variables\
│   └── variables.js
├── lab02-array-methods\
│   └── array-methods.js
├── lab03-destructuring\
│   └── destructuring.js
├── lab04-optional-chaining\
│   └── optional-chaining.js
├── lab05-functions\
│   └── functions.js
├── lab06-async-await\
│   └── async-await.js
└── lab07-typescript\          ← สร้างด้วย Vite (แยกต่างหาก)
    └── ...
```

สร้างโฟลเดอร์ย่อยทั้งหมดในครั้งเดียว:

```cmd
mkdir lab01-variables lab02-array-methods lab03-destructuring lab04-optional-chaining lab05-functions lab06-async-await
```

---

## 3.1 JavaScript พื้นฐานที่จำเป็น

### JavaScript คืออะไร

**JavaScript** (จาวาสคริปต์) คือภาษาโปรแกรมมิงที่ทำให้หน้าเว็บ "มีชีวิต" ถ้า HTML คือโครงกระดูก CSS คือเสื้อผ้า JavaScript คือกล้ามเนื้อที่ทำให้ทุกอย่างขยับและโต้ตอบได้

```
HTML  → โครงสร้าง (อะไรอยู่ที่ไหน)
CSS   → รูปลักษณ์ (หน้าตาเป็นอย่างไร)
JS    → พฤติกรรม (เกิดอะไรขึ้นเมื่อผู้ใช้โต้ตอบ)
```

ตัวอย่างสิ่งที่ JavaScript ทำได้:
- เปิด/ปิด Mobile Menu เมื่อกดปุ่ม
- ตรวจสอบ Form ก่อนส่ง
- ดึงข้อมูลสินค้าจาก API แล้วแสดงผลบนหน้า
- อัปเดตตะกร้าสินค้าแบบ Real-time

---

### 🔬 Lab 01 — Variables

**ไฟล์ที่ต้องสร้าง:**
```
C:\Users\abcd\Documents\abcd\unit3-js-lab\lab01-variables\variables.js
```

**วิธีสร้างไฟล์ใน VS Code:**
ใน VS Code ให้คลิก Explorer แล้วคลิกขวาที่โฟลเดอร์ `lab01-variables` → **New File** → พิมพ์ `variables.js`

**โค้ดที่ต้องพิมพ์ลงในไฟล์ `variables.js`:**

```javascript
// === Lab 01: Variables ===
// ไฟล์: lab01-variables/variables.js

// const — ค่าที่ไม่เปลี่ยนแปลง (ใช้เป็นค่า default เสมอ)
const siteName = 'Task Manager'
const MAX_TASKS = 100
const user = { name: 'สมชาย', age: 28 }

// let — ค่าที่ต้องเปลี่ยนแปลง
let count = 0
let isLoading = false
let currentPage = 1

// แสดงผลค่าต่าง ๆ
console.log('=== ค่า const ===')
console.log('siteName:', siteName)
console.log('MAX_TASKS:', MAX_TASKS)
console.log('user:', user)

console.log('\n=== ค่า let ===')
console.log('count เริ่มต้น:', count)
count = count + 1
console.log('count หลังบวก 1:', count)

console.log('\n=== Data Types ===')
const name = 'สมชาย'
const age = 28
const isActive = true
const nothing = null
let notYet

console.log(typeof name)          // string
console.log(typeof age)           // number
console.log(typeof isActive)      // boolean
console.log(typeof nothing)       // object (quirk ของ JS)
console.log(typeof notYet)        // undefined

const fruits = ['มะม่วง', 'ส้ม', 'กล้วย']
console.log(Array.isArray(fruits)) // true

console.log('\n=== Type Coercion (ระวัง!) ===')
console.log('5' + 3)              // "53" (ต่อ string)
console.log('5' - 3)              // 2 (ลบ number)
console.log('' == false)          // true  ← อย่าใช้ ==
console.log('' === false)         // false ← ใช้ === เสมอ
```

**วิธีรัน:**

เปิด **Command Prompt** แล้วพิมพ์:

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab01-variables"
node variables.js
```

**Output ที่ควรเห็น:**

```
=== ค่า const ===
siteName: Task Manager
MAX_TASKS: 100
user: { name: 'สมชาย', age: 28 }

=== ค่า let ===
count เริ่มต้น: 0
count หลังบวก 1: 1

=== Data Types ===
string
number
boolean
object
undefined
true

=== Type Coercion (ระวัง!) ===
53
2
true
false
```

---

### 🔬 Lab 02 — Array Methods

**ไฟล์ที่ต้องสร้าง:**
```
C:\Users\abcd\Documents\abcd\unit3-js-lab\lab02-array-methods\array-methods.js
```

Array Methods เหล่านี้ทำงานโดย **ไม่แก้ไข Array เดิม** (Immutable) และ Return ค่าใหม่ออกมา — เป็นหัวใจของการเขียน JavaScript สมัยใหม่

**โค้ดที่ต้องพิมพ์ลงในไฟล์ `array-methods.js`:**

```javascript
// === Lab 02: Array Methods ===
// ไฟล์: lab02-array-methods/array-methods.js

// ============================
// map — แปลงทุก Element
// ============================
console.log('=== map ===')

const prices = [100, 250, 80, 320]
const pricesWithVAT = prices.map(price => price * 1.07)
console.log('ราคาก่อน VAT:', prices)
console.log('ราคาหลัง VAT 7%:', pricesWithVAT)

const tasks = [
  { id: 1, title: 'เรียน JavaScript', done: false },
  { id: 2, title: 'ทำโปรเจกต์', done: true },
]
const titles = tasks.map(task => task.title)
console.log('titles:', titles)

const tasksWithLabel = tasks.map(task => ({
  ...task,
  label: task.done ? '✅ เสร็จแล้ว' : '⏳ ยังไม่เสร็จ'
}))
console.log('tasksWithLabel:', tasksWithLabel)

// ============================
// filter — กรองเฉพาะที่ต้องการ
// ============================
console.log('\n=== filter ===')

const allTasks = [
  { id: 1, title: 'เรียน JavaScript', done: false, priority: 'high' },
  { id: 2, title: 'ทำโปรเจกต์', done: true, priority: 'high' },
  { id: 3, title: 'อ่านหนังสือ', done: false, priority: 'low' },
]
const pending = allTasks.filter(task => !task.done)
console.log('Task ที่ยังไม่เสร็จ:', pending.map(t => t.title))

const urgentPending = allTasks.filter(task => !task.done && task.priority === 'high')
console.log('Task เร่งด่วนที่ยังไม่เสร็จ:', urgentPending.map(t => t.title))

// ============================
// reduce — รวมเป็นค่าเดียว
// ============================
console.log('\n=== reduce ===')

const total = prices.reduce((sum, price) => sum + price, 0)
console.log('ยอดรวมราคาทั้งหมด:', total)

const statusTasks = [
  { status: 'done' }, { status: 'pending' },
  { status: 'done' }, { status: 'pending' }, { status: 'pending' }
]
const count = statusTasks.reduce((acc, task) => {
  acc[task.status] = (acc[task.status] || 0) + 1
  return acc
}, {})
console.log('นับตามสถานะ:', count)

// ============================
// find และ findIndex
// ============================
console.log('\n=== find และ findIndex ===')

const taskList = [
  { id: 1, title: 'เรียน JavaScript' },
  { id: 2, title: 'ทำโปรเจกต์' },
  { id: 3, title: 'อ่านหนังสือ' },
]
const found = taskList.find(t => t.id === 2)
console.log('find id=2:', found)

const idx = taskList.findIndex(t => t.id === 2)
console.log('findIndex id=2:', idx)

const afterDelete = taskList.filter((_, i) => i !== idx)
console.log('หลังลบ index', idx, ':', afterDelete.map(t => t.title))

// ============================
// some และ every
// ============================
console.log('\n=== some และ every ===')

const checkTasks = [
  { id: 1, done: true },
  { id: 2, done: false },
  { id: 3, done: true },
]
console.log('มีอย่างน้อย 1 ที่เสร็จ (some):', checkTasks.some(t => t.done))
console.log('ทุกอันเสร็จหมด (every):', checkTasks.every(t => t.done))
```

**วิธีรัน:**

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab02-array-methods"
node array-methods.js
```

**Output ที่ควรเห็น:**

```
=== map ===
ราคาก่อน VAT: [ 100, 250, 80, 320 ]
ราคาหลัง VAT 7%: [ 107, 267.5, 85.6, 342.4 ]
titles: [ 'เรียน JavaScript', 'ทำโปรเจกต์' ]
tasksWithLabel: [
  { id: 1, title: 'เรียน JavaScript', done: false, label: '⏳ ยังไม่เสร็จ' },
  { id: 2, title: 'ทำโปรเจกต์', done: true, label: '✅ เสร็จแล้ว' }
]

=== filter ===
Task ที่ยังไม่เสร็จ: [ 'เรียน JavaScript', 'อ่านหนังสือ' ]
Task เร่งด่วนที่ยังไม่เสร็จ: [ 'เรียน JavaScript' ]

=== reduce ===
ยอดรวมราคาทั้งหมด: 750
นับตามสถานะ: { done: 2, pending: 3 }

=== find และ findIndex ===
find id=2: { id: 2, title: 'ทำโปรเจกต์' }
findIndex id=2: 1
หลังลบ index 1 : [ 'เรียน JavaScript', 'อ่านหนังสือ' ]

=== some และ every ===
มีอย่างน้อย 1 ที่เสร็จ (some): true
ทุกอันเสร็จหมด (every): false
```

---

### 🔬 Lab 03 — Destructuring และ Spread Operator

**ไฟล์ที่ต้องสร้าง:**
```
C:\Users\abcd\Documents\abcd\unit3-js-lab\lab03-destructuring\destructuring.js
```

**โค้ดที่ต้องพิมพ์ลงในไฟล์ `destructuring.js`:**

```javascript
// === Lab 03: Destructuring และ Spread Operator ===
// ไฟล์: lab03-destructuring/destructuring.js

// ============================
// Object Destructuring
// ============================
console.log('=== Object Destructuring ===')

const user = { name: 'สมชาย', age: 28, city: 'กรุงเทพฯ' }

// แบบเก่า
const nameOld = user.name
const ageOld = user.age
console.log('แบบเก่า:', nameOld, ageOld)

// แบบ Destructuring
const { name, age } = user
console.log('แบบ Destructuring:', name, age)

// เปลี่ยนชื่อตัวแปร
const { name: userName, age: userAge } = user
console.log('เปลี่ยนชื่อ:', userName, userAge)

// Default Value
const { name: n2, role = 'user' } = user
console.log('Default value — role:', role)  // 'user' เพราะ user ไม่มี role

// ============================
// Array Destructuring
// ============================
console.log('\n=== Array Destructuring ===')

const colors = ['red', 'green', 'blue']
const [first, second] = colors
console.log('first:', first, '| second:', second)

const [, , third] = colors  // ข้ามค่าที่ไม่ต้องการ
console.log('third (ข้ามสองตัวแรก):', third)

// ============================
// Destructuring ใน Function Parameter
// ============================
console.log('\n=== Destructuring ใน Function ===')

function renderTask({ id, title, done }) {
  return `[${id}] ${title} ${done ? '✅' : '⏳'}`
}

const task = { id: 1, title: 'เรียน JavaScript', done: false }
console.log(renderTask(task))

// ============================
// Spread Operator — Array
// ============================
console.log('\n=== Spread Operator (Array) ===')

const original = [1, 2, 3]
const copy = [...original]
const extended = [...original, 4, 5]
console.log('original:', original)
console.log('copy:', copy)
console.log('extended:', extended)

const a = [1, 2]
const b = [3, 4]
const combined = [...a, ...b]
console.log('combined:', combined)

// ============================
// Spread Operator — Object
// ============================
console.log('\n=== Spread Operator (Object) ===')

const taskObj = { id: 1, title: 'เรียน JS', done: false }
const updated = { ...taskObj, done: true }
console.log('original task:', taskObj)
console.log('updated task:', updated)

const defaults = { theme: 'light', lang: 'th', fontSize: 16 }
const userPrefs = { theme: 'dark' }
const settings = { ...defaults, ...userPrefs }
console.log('settings:', settings)

// ============================
// Rest Parameter
// ============================
console.log('\n=== Rest Parameter ===')

function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0)
}
console.log('sum(1,2,3,4,5):', sum(1, 2, 3, 4, 5))

const { id, ...rest } = { id: 1, title: 'งาน A', priority: 'high' }
console.log('id:', id)
console.log('rest:', rest)
```

**วิธีรัน:**

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab03-destructuring"
node destructuring.js
```

**Output ที่ควรเห็น:**

```
=== Object Destructuring ===
แบบเก่า: สมชาย 28
แบบ Destructuring: สมชาย 28
เปลี่ยนชื่อ: สมชาย 28
Default value — role: user

=== Array Destructuring ===
first: red | second: green
third (ข้ามสองตัวแรก): blue

=== Destructuring ใน Function ===
[1] เรียน JavaScript ⏳

=== Spread Operator (Array) ===
original: [ 1, 2, 3 ]
copy: [ 1, 2, 3 ]
extended: [ 1, 2, 3, 4, 5 ]
combined: [ 1, 2, 3, 4 ]

=== Spread Operator (Object) ===
original task: { id: 1, title: 'เรียน JS', done: false }
updated task: { id: 1, title: 'เรียน JS', done: true }
settings: { theme: 'dark', lang: 'th', fontSize: 16 }

=== Rest Parameter ===
sum(1,2,3,4,5): 15
id: 1
rest: { title: 'งาน A', priority: 'high' }
```

---

### 🔬 Lab 04 — Optional Chaining และ Nullish Coalescing

**ไฟล์ที่ต้องสร้าง:**
```
C:\Users\abcd\Documents\abcd\unit3-js-lab\lab04-optional-chaining\optional-chaining.js
```

**โค้ดที่ต้องพิมพ์ลงในไฟล์ `optional-chaining.js`:**

```javascript
// === Lab 04: Optional Chaining และ Nullish Coalescing ===
// ไฟล์: lab04-optional-chaining/optional-chaining.js

// ============================
// Optional Chaining ?.
// ============================
console.log('=== Optional Chaining (?.) ===')

const user = {
  profile: {
    name: 'สมชาย',
    address: null
  }
}

// แบบเก่า — ต้องเช็คทีละชั้น
const cityOld = user && user.profile && user.profile.address && user.profile.address.city
console.log('แบบเก่า (cityOld):', cityOld)   // undefined

// แบบ Optional Chaining — กระชับและปลอดภัย
const city = user?.profile?.address?.city
console.log('Optional Chaining (city):', city) // undefined (ไม่ crash)

// กับ Array
const userWithTags = { tags: ['js', 'ts'] }
const firstTag = userWithTags?.tags?.[0]
console.log('firstTag:', firstTag)             // 'js'

const userNoTags = null
const noTag = userNoTags?.tags?.[0]
console.log('noTag (user = null):', noTag)     // undefined

// กับ Method Call
const userWithMethod = {
  getDisplayName: () => 'สมชาย เก่งมาก'
}
const displayName = userWithMethod?.getDisplayName?.()
console.log('displayName:', displayName)

const userNoMethod = {}
const noDisplay = userNoMethod?.getDisplayName?.()
console.log('noDisplay (ไม่มี method):', noDisplay)  // undefined

// ============================
// Nullish Coalescing ??
// ============================
console.log('\n=== Nullish Coalescing (??) ===')

const zeroCount = 0
const emptyScore = ''

// || จะแทนที่ 0 และ '' ด้วย (ผิด!)
console.log('0 || 10 =', zeroCount || 10)           // 10  ❌
console.log("'' || 'ไม่ระบุ' =", emptyScore || 'ไม่ระบุ') // 'ไม่ระบุ' ❌

// ?? จะแทนเฉพาะ null และ undefined เท่านั้น (ถูก!)
console.log('0 ?? 10 =', zeroCount ?? 10)           // 0   ✅
console.log("'' ?? 'ไม่ระบุ' =", emptyScore ?? 'ไม่ระบุ') // ''  ✅

// ใช้คู่กันกับ Optional Chaining
const username = user?.profile?.name ?? 'ผู้ใช้ไม่ระบุชื่อ'
console.log('\nusername:', username)  // 'สมชาย'

const cart = null
const itemCount = cart?.items?.length ?? 0
console.log('itemCount (cart = null):', itemCount)  // 0

// ============================
// Template Literals
// ============================
console.log('\n=== Template Literals ===')

const studentName = 'สมชาย'
const score = 95

// แบบเก่า
const msgOld = 'สวัสดี ' + studentName + '! คะแนนของคุณคือ ' + score + ' คะแนน'
console.log('แบบเก่า:', msgOld)

// Template Literal
const msg = `สวัสดี ${studentName}! คะแนนของคุณคือ ${score} คะแนน`
console.log('Template Literal:', msg)

// Expression ใน Template Literal
const grade = `เกรด: ${score >= 80 ? 'A' : score >= 70 ? 'B' : 'C'}`
console.log(grade)

// ============================
// Short-circuit Evaluation
// ============================
console.log('\n=== Short-circuit ===')

const isLoggedIn = true
isLoggedIn && console.log('เข้าสู่ระบบแล้ว (&&)')

const isLoggedOut = false
isLoggedOut && console.log('บรรทัดนี้จะไม่แสดง')

const nickname = null
const fullName = 'สมชาย ใจดี'
const displayNameFinal = nickname || fullName || 'ไม่ระบุ'
console.log('displayNameFinal:', displayNameFinal)
```

**วิธีรัน:**

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab04-optional-chaining"
node optional-chaining.js
```

**Output ที่ควรเห็น:**

```
=== Optional Chaining (?.) ===
แบบเก่า (cityOld): undefined
Optional Chaining (city): undefined
firstTag: js
noTag (user = null): undefined
displayName: สมชาย เก่งมาก
noDisplay (ไม่มี method): undefined

=== Nullish Coalescing (??) ===
0 || 10 = 10
'' || 'ไม่ระบุ' = ไม่ระบุ
0 ?? 10 = 0
'' ?? 'ไม่ระบุ' = 

username: สมชาย
itemCount (cart = null): 0

=== Template Literals ===
แบบเก่า: สวัสดี สมชาย! คะแนนของคุณคือ 95 คะแนน
Template Literal: สวัสดี สมชาย! คะแนนของคุณคือ 95 คะแนน
เกรด: A

=== Short-circuit ===
เข้าสู่ระบบแล้ว (&&)
displayNameFinal: สมชาย ใจดี
```

---

## 3.2 Functions และ Asynchronous JavaScript

### 🔬 Lab 05 — Functions และ Higher-Order Functions

**ไฟล์ที่ต้องสร้าง:**
```
C:\Users\abcd\Documents\abcd\unit3-js-lab\lab05-functions\functions.js
```

**โค้ดที่ต้องพิมพ์ลงในไฟล์ `functions.js`:**

```javascript
// === Lab 05: Functions และ Higher-Order Functions ===
// ไฟล์: lab05-functions/functions.js

// ============================
// Arrow Functions
// ============================
console.log('=== Arrow Functions ===')

// Function Declaration แบบเดิม
function addOld(a, b) {
  return a + b
}

// Arrow Function แบบกระชับ
const add = (a, b) => a + b
const double = n => n * 2
const greet = name => `สวัสดี ${name}!`
const makeUser = (name, age) => ({ name, age })

console.log('addOld(3, 4):', addOld(3, 4))
console.log('add(3, 4):', add(3, 4))
console.log('double(7):', double(7))
console.log('greet("สมชาย"):', greet('สมชาย'))
console.log('makeUser:', makeUser('สมชาย', 28))

// กับ Array Methods
const nums = [100, 200, 300]
const doubled = nums.map(p => p * 2)
const expensive = nums.filter(p => p > 150)
const total = nums.reduce((sum, p) => sum + p, 0)
console.log('doubled:', doubled)
console.log('expensive (> 150):', expensive)
console.log('total:', total)

// ============================
// Higher-Order Functions
// ============================
console.log('\n=== Higher-Order Functions ===')

function doTwice(fn, value) {
  return fn(fn(value))
}
console.log('doTwice(double, 3):', doTwice(n => n * 2, 3))   // 12
console.log('doTwice(s+"!", "Hi"):', doTwice(s => s + '!', 'Hi')) // 'Hi!!'

// Function ที่ Return Function (Closure)
function createMultiplier(factor) {
  return (number) => number * factor
}
const triple = createMultiplier(3)
const quadruple = createMultiplier(4)
console.log('triple(5):', triple(5))      // 15
console.log('quadruple(5):', quadruple(5)) // 20

// ตัวอย่างจริง — Validator
function createValidator(min, max) {
  return (value) => {
    if (value < min) return `ต้องมากกว่า ${min}`
    if (value > max) return `ต้องน้อยกว่า ${max}`
    return null  // null = valid
  }
}

const validateAge = createValidator(0, 120)
const validateScore = createValidator(0, 100)

console.log('\n=== Validator ===')
console.log('validateAge(25):', validateAge(25))     // null (valid)
console.log('validateAge(-5):', validateAge(-5))     // 'ต้องมากกว่า 0'
console.log('validateScore(150):', validateScore(150)) // 'ต้องน้อยกว่า 100'
console.log('validateScore(85):', validateScore(85)) // null (valid)
```

**วิธีรัน:**

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab05-functions"
node functions.js
```

**Output ที่ควรเห็น:**

```
=== Arrow Functions ===
addOld(3, 4): 7
add(3, 4): 7
double(7): 14
greet("สมชาย"): สวัสดี สมชาย!
makeUser: { name: 'สมชาย', age: 28 }
doubled: [ 200, 400, 600 ]
expensive (> 150): [ 200, 300 ]
total: 600

=== Higher-Order Functions ===
doTwice(double, 3): 12
doTwice(s+"!", "Hi"): Hi!!

triple(5): 15
quadruple(5): 20

=== Validator ===
validateAge(25): null
validateAge(-5): ต้องมากกว่า 0
validateScore(150): ต้องน้อยกว่า 100
validateScore(85): null
```

---

### 🔬 Lab 06 — Async/Await และ Fetch

**ไฟล์ที่ต้องสร้าง:**
```
C:\Users\abcd\Documents\abcd\unit3-js-lab\lab06-async-await\async-await.js
```

> **หมายเหตุ:** `fetch()` เป็น Web API ที่ใช้ในเบราว์เซอร์ สำหรับ Node.js เวอร์ชัน 18+ จะมี `fetch()` ในตัว แต่ถ้าต้องการดึงข้อมูลจาก API จริงต้องมีอินเทอร์เน็ต

**โค้ดที่ต้องพิมพ์ลงในไฟล์ `async-await.js`:**

```javascript
// === Lab 06: Async/Await และ Fetch ===
// ไฟล์: lab06-async-await/async-await.js

// ============================
// Promise พื้นฐาน
// ============================
console.log('=== Promise พื้นฐาน ===')

const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve({ data: 'ข้อมูลที่ได้รับหลัง 500ms' })
  }, 500)
})

myPromise
  .then(result => console.log('Promise resolved:', result.data))
  .catch(error => console.error('Error:', error.message))
  .finally(() => console.log('finally: ทำงานเสมอ'))

// ============================
// async/await — จำลอง API Call
// ============================
console.log('\n=== async/await ===')

// จำลอง delay เหมือนดึงข้อมูลจาก API
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms))
}

async function fetchUserData(userId) {
  console.log(`กำลังดึงข้อมูล user ${userId}...`)
  await delay(300)  // รอ 300ms เหมือน API response
  return { id: userId, name: 'สมชาย', role: 'developer' }
}

async function main() {
  const user = await fetchUserData(1)
  console.log('ได้ข้อมูล user:', user)
}

main()

// ============================
// try/catch/finally
// ============================
console.log('\n=== try/catch/finally ===')

async function riskyOperation(shouldFail) {
  try {
    await delay(200)
    if (shouldFail) {
      throw new Error('เกิดข้อผิดพลาดจากเซิร์ฟเวอร์')
    }
    const result = { tasks: ['งาน A', 'งาน B', 'งาน C'] }
    console.log('สำเร็จ! ได้รับ:', result.tasks)
    return result

  } catch (error) {
    console.error('จับ Error ได้:', error.message)
    return []  // Return ค่า Default

  } finally {
    console.log('finally: ปิด loading เสมอ')
  }
}

async function runTests() {
  console.log('--- ทดสอบ กรณีสำเร็จ ---')
  await riskyOperation(false)

  console.log('\n--- ทดสอบ กรณีล้มเหลว ---')
  await riskyOperation(true)
}

// รัน main และ runTests แบบ sequence
async function start() {
  await main()
  await runTests()

  // ============================
  // fetch() จาก API จริง (JSONPlaceholder)
  // ============================
  console.log('\n=== fetch() จาก API จริง ===')

  try {
    console.log('กำลังดึงข้อมูลจาก JSONPlaceholder...')
    const response = await fetch('https://jsonplaceholder.typicode.com/todos?_limit=3')

    if (!response.ok) {
      throw new Error(`HTTP Error: ${response.status}`)
    }

    const todos = await response.json()
    console.log(`ได้รับ ${todos.length} รายการ:`)
    todos.forEach(todo => {
      console.log(` - [${todo.done ? '✅' : '⬜'}] ${todo.title.substring(0, 30)}...`)
    })

  } catch (error) {
    if (error instanceof TypeError) {
      console.error('ไม่สามารถเชื่อมต่ออินเทอร์เน็ตได้')
    } else {
      console.error('เกิดข้อผิดพลาด:', error.message)
    }
  }
}

start()
```

**วิธีรัน:**

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab06-async-await"
node async-await.js
```

**Output ที่ควรเห็น** (ต้องมีอินเทอร์เน็ตสำหรับส่วน fetch):

```
=== Promise พื้นฐาน ===

=== async/await ===

=== try/catch/finally ===
กำลังดึงข้อมูล user 1...
ได้ข้อมูล user: { id: 1, name: 'สมชาย', role: 'developer' }
--- ทดสอบ กรณีสำเร็จ ---
สำเร็จ! ได้รับ: [ 'งาน A', 'งาน B', 'งาน C' ]
finally: ปิด loading เสมอ

--- ทดสอบ กรณีล้มเหลว ---
จับ Error ได้: เกิดข้อผิดพลาดจากเซิร์ฟเวอร์
finally: ปิด loading เสมอ

=== fetch() จาก API จริง ===
กำลังดึงข้อมูลจาก JSONPlaceholder...
ได้รับ 3 รายการ:
 - [⬜] delectus aut autem...
 - [✅] quis ut nam facilis et officia qui...
 - [⬜] fugiat veniam minus...
Promise resolved: ข้อมูลที่ได้รับหลัง 500ms
finally: ทำงานเสมอ
```

> **หมายเหตุ:** ลำดับบางบรรทัดอาจสลับกันเล็กน้อยเพราะ Promise ทำงานแบบ Async

---

## 3.5 TypeScript

### 🔬 Lab 07 — TypeScript กับ Vite (โปรเจกต์แยก)

Lab TypeScript นี้ใช้ **Vite** เป็น Build Tool จึงต้องสร้างโปรเจกต์ใหม่แยกต่างหาก

---

#### ขั้นตอนที่ 1 — ตรวจสอบ Node.js และ pnpm

เปิด **Command Prompt** แล้วพิมพ์:

```cmd
node --version
pnpm --version
```

**Output ที่ควรเห็น:**
```
v20.x.x   (หรือสูงกว่า)
9.x.x     (หรือสูงกว่า)
```

ถ้ายังไม่มี pnpm ให้ติดตั้ง:
```cmd
npm install -g pnpm
```

---

#### ขั้นตอนที่ 2 — สร้างโปรเจกต์ TypeScript

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab"
pnpm create vite@latest lab07-typescript --template vanilla-ts
cd lab07-typescript
pnpm install
```

**Output ที่ควรเห็น:**
```
> Scaffolding project in ...\lab07-typescript...
Done. Now run:

  cd lab07-typescript
  pnpm install
  pnpm run dev
```

---

#### ขั้นตอนที่ 3 — ตรวจสอบโครงสร้างโปรเจกต์

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab07-typescript"
code .
```

โครงสร้างไฟล์ที่ได้:
```
lab07-typescript\
├── src\
│   ├── main.ts          ← แก้ไขไฟล์นี้
│   ├── style.css
│   └── vite-env.d.ts
├── index.html
├── tsconfig.json
└── package.json
```

---

#### ขั้นตอนที่ 4 — แก้ไข `tsconfig.json`

เปิดไฟล์ `tsconfig.json` แล้วแทนที่เนื้อหาทั้งหมดด้วย:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

---

#### ขั้นตอนที่ 5 — เขียนโค้ด TypeScript ใน `src/main.ts`

เปิดไฟล์ `src/main.ts` แล้ว **ลบโค้ดเดิมทั้งหมด** แล้วพิมพ์โค้ดด้านล่างแทน:

```typescript
// === Lab 07: TypeScript Basics ===
// ไฟล์: lab07-typescript/src/main.ts

// ============================
// Type Annotations พื้นฐาน
// ============================

const siteName: string = 'Task Manager'
const maxTasks: number = 100
const isDarkMode: boolean = false

// Type Inference — TypeScript อนุมาน Type อัตโนมัติ
const count = 0        // TypeScript รู้ว่าเป็น number
const appName = 'App'  // TypeScript รู้ว่าเป็น string

// Array
const tags: string[] = ['work', 'personal', 'urgent']
const scores: number[] = [85, 92, 78]

// Union Type
let taskId: string | number = 1
taskId = 'abc-123'  // ✅ ได้ทั้งสองแบบ

// Literal Type
let status: 'pending' | 'done' | 'cancelled' = 'pending'
status = 'done'    // ✅
// status = 'unknown'  // ❌ จะ Error ถ้า uncomment

// ============================
// Interface
// ============================

interface Task {
  id: number
  title: string
  done: boolean
  priority: 'low' | 'medium' | 'high'
  createdAt: string
  tags: string[]
  dueDate?: string   // Optional
}

interface FilterState {
  status: 'all' | 'pending' | 'done'
  searchQuery: string
  priority?: 'low' | 'medium' | 'high'
}

// ============================
// Functions กับ Type Annotations
// ============================

function createTask(title: string, priority: Task['priority'] = 'medium'): Task {
  return {
    id: Date.now(),
    title: title.trim(),
    done: false,
    priority,
    createdAt: new Date().toISOString(),
    tags: [],
  }
}

function toggleTask(tasks: Task[], id: number): Task[] {
  return tasks.map(task =>
    task.id === id ? { ...task, done: !task.done } : task
  )
}

function deleteTask(tasks: Task[], id: number): Task[] {
  return tasks.filter(task => task.id !== id)
}

function filterTasks(tasks: Task[], filter: FilterState): Task[] {
  return tasks.filter(task => {
    if (filter.status === 'done' && !task.done) return false
    if (filter.status === 'pending' && task.done) return false
    if (filter.priority && task.priority !== filter.priority) return false
    if (filter.searchQuery) {
      const q = filter.searchQuery.toLowerCase()
      if (!task.title.toLowerCase().includes(q)) return false
    }
    return true
  })
}

// ============================
// Generics
// ============================

function getFirst<T>(arr: T[]): T | undefined {
  return arr[0]
}

function saveToStorage<T>(key: string, value: T): void {
  localStorage.setItem(key, JSON.stringify(value))
}

function loadFromStorage<T>(key: string, defaultValue: T): T {
  try {
    const item = localStorage.getItem(key)
    return item ? (JSON.parse(item) as T) : defaultValue
  } catch {
    return defaultValue
  }
}

// ============================
// Utility Types
// ============================

type TaskUpdate = Partial<Task>               // ทุก Property เป็น Optional
type TaskPreview = Pick<Task, 'id' | 'title' | 'done'>  // เลือกบาง Property
type NewTask = Omit<Task, 'id'>              // ตัด id ออก
type TasksByPriority = Record<'low' | 'medium' | 'high', Task[]>

// ============================
// ทดสอบการทำงาน
// ============================

const taskStorage = loadFromStorage<Task[]>('tasks', [])

const initialTasks: Task[] = taskStorage.length > 0 ? taskStorage : [
  createTask('เรียน TypeScript', 'high'),
  createTask('ทำโปรเจกต์ Task Manager', 'high'),
  createTask('อ่านเอกสาร MDN', 'medium'),
  createTask('ฝึก Destructuring', 'low'),
]

saveToStorage('tasks', initialTasks)

// ============================
// แสดงผลบนหน้า HTML
// ============================

function escapeHTML(str: string): string {
  return str
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
}

const priorityColor: Record<Task['priority'], string> = {
  high: '#ef4444',
  medium: '#f59e0b',
  low: '#22c55e',
}

function renderApp(tasks: Task[], filter: FilterState): void {
  const filtered = filterTasks(tasks, filter)

  document.querySelector<HTMLDivElement>('#app')!.innerHTML = `
    <div style="font-family: sans-serif; max-width: 600px; margin: 40px auto; padding: 20px;">
      <h1 style="color: #1e293b; border-bottom: 2px solid #3b82f6; padding-bottom: 10px;">
        📋 Task Manager — TypeScript Lab
      </h1>

      <!-- Form เพิ่ม Task -->
      <div style="display:flex; gap:8px; margin-bottom:16px;">
        <input
          id="new-task-input"
          type="text"
          placeholder="ชื่องาน..."
          style="flex:1; padding:8px 12px; border:1px solid #cbd5e1; border-radius:6px; font-size:14px;"
        />
        <select id="priority-select" style="padding:8px; border:1px solid #cbd5e1; border-radius:6px;">
          <option value="low">🟢 ต่ำ</option>
          <option value="medium" selected>🟡 กลาง</option>
          <option value="high">🔴 สูง</option>
        </select>
        <button
          id="add-btn"
          style="padding:8px 16px; background:#3b82f6; color:white; border:none; border-radius:6px; cursor:pointer; font-size:14px;"
        >+ เพิ่ม</button>
      </div>

      <!-- Filter -->
      <div style="display:flex; gap:8px; margin-bottom:16px; align-items:center;">
        <label style="font-size:13px; color:#64748b;">กรอง:</label>
        <select id="filter-status" style="padding:6px; border:1px solid #cbd5e1; border-radius:6px; font-size:13px;">
          <option value="all" ${filter.status === 'all' ? 'selected' : ''}>ทั้งหมด</option>
          <option value="pending" ${filter.status === 'pending' ? 'selected' : ''}>ยังไม่เสร็จ</option>
          <option value="done" ${filter.status === 'done' ? 'selected' : ''}>เสร็จแล้ว</option>
        </select>
        <input
          id="search-input"
          type="text"
          placeholder="ค้นหา..."
          value="${escapeHTML(filter.searchQuery)}"
          style="padding:6px 10px; border:1px solid #cbd5e1; border-radius:6px; font-size:13px;"
        />
        <span style="font-size:13px; color:#64748b; margin-left:auto;">
          ${filtered.filter(t => t.done).length}/${filtered.length} เสร็จ
        </span>
      </div>

      <!-- Task List -->
      <ul id="task-list" style="list-style:none; padding:0; margin:0;">
        ${filtered.length === 0
          ? '<li style="text-align:center;color:#94a3b8;padding:20px;">ไม่มีงานในรายการ</li>'
          : filtered.map(task => `
            <li
              data-id="${task.id}"
              style="display:flex; align-items:center; gap:10px; padding:10px 12px;
                     margin-bottom:8px; background:${task.done ? '#f8fafc' : 'white'};
                     border:1px solid #e2e8f0; border-radius:8px;
                     opacity:${task.done ? '0.6' : '1'};"
            >
              <button class="toggle-btn" style="background:none;border:none;cursor:pointer;font-size:18px;">
                ${task.done ? '✅' : '⬜'}
              </button>
              <span style="flex:1; text-decoration:${task.done ? 'line-through' : 'none'}; color:#1e293b;">
                ${escapeHTML(task.title)}
              </span>
              <span style="font-size:11px; padding:2px 8px; border-radius:999px; color:white;
                           background:${priorityColor[task.priority]};">
                ${task.priority}
              </span>
              <button class="delete-btn"
                style="background:none;border:none;cursor:pointer;font-size:16px;color:#ef4444;">
                🗑️
              </button>
            </li>
          `).join('')
        }
      </ul>

      <p style="font-size:12px; color:#94a3b8; margin-top:16px; text-align:center;">
        ข้อมูลบันทึกลง localStorage อัตโนมัติ — รีเฟรชหน้าแล้วข้อมูลยังอยู่ครบ ✅
      </p>
    </div>
  `
}

// ============================
// State และ Event Handlers
// ============================

let appTasks: Task[] = initialTasks
let appFilter: FilterState = { status: 'all', searchQuery: '' }

function persist(): void {
  saveToStorage('tasks', appTasks)
}

function render(): void {
  renderApp(appTasks, appFilter)
  setupEvents()
}

function setupEvents(): void {
  // เพิ่ม Task
  document.querySelector('#add-btn')?.addEventListener('click', () => {
    const input = document.querySelector<HTMLInputElement>('#new-task-input')!
    const select = document.querySelector<HTMLSelectElement>('#priority-select')!
    const title = input.value.trim()
    if (!title) return
    const task = createTask(title, select.value as Task['priority'])
    appTasks = [...appTasks, task]
    persist()
    render()
    input.value = ''
  })

  // Event Delegation — Toggle และ Delete
  document.querySelector('#task-list')?.addEventListener('click', (e) => {
    const target = e.target as HTMLElement
    const li = target.closest<HTMLElement>('[data-id]')
    if (!li) return
    const id = Number(li.dataset.id)

    if (target.matches('.toggle-btn')) {
      appTasks = toggleTask(appTasks, id)
      persist()
      render()
    }
    if (target.matches('.delete-btn')) {
      if (confirm('ลบงานนี้ใช่ไหม?')) {
        appTasks = deleteTask(appTasks, id)
        persist()
        render()
      }
    }
  })

  // Filter Status
  document.querySelector('#filter-status')?.addEventListener('change', (e) => {
    appFilter = { ...appFilter, status: (e.target as HTMLSelectElement).value as FilterState['status'] }
    render()
  })

  // Search
  document.querySelector('#search-input')?.addEventListener('input', (e) => {
    appFilter = { ...appFilter, searchQuery: (e.target as HTMLInputElement).value }
    render()
  })

  // Enter เพิ่ม Task
  document.querySelector('#new-task-input')?.addEventListener('keydown', (e) => {
    if ((e as KeyboardEvent).key === 'Enter') {
      document.querySelector<HTMLButtonElement>('#add-btn')?.click()
    }
  })
}

// ============================
// console.log แสดงผลใน Terminal (สำหรับ TypeScript Lab)
// ============================

console.log('=== TypeScript Lab ===')
console.log('siteName:', siteName)
console.log('maxTasks:', maxTasks)
console.log('isDarkMode:', isDarkMode)

console.log('\n=== สร้าง Task ด้วย createTask() ===')
const sampleTask = createTask('ทดสอบ TypeScript', 'high')
console.log('createTask():', sampleTask)

console.log('\n=== Generics: getFirst<T> ===')
console.log('getFirst([1,2,3]):', getFirst([1, 2, 3]))         // 1
console.log('getFirst(["a","b"]):', getFirst(['a', 'b']))    // "a"
console.log('getFirst([]):', getFirst([]))                    // undefined

console.log('\n=== filterTasks() ===')
const sampleTasks: Task[] = [
  createTask('งาน A', 'high'),
  createTask('งาน B', 'low'),
  createTask('งาน C', 'medium'),
]
sampleTasks[0].done = true
const pendingOnly = filterTasks(sampleTasks, { status: 'pending', searchQuery: '' })
console.log('pending tasks:', pendingOnly.map(t => t.title))

// Init App
render()
```

---

#### ขั้นตอนที่ 6 — รัน Development Server

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab07-typescript"
pnpm dev
```

**Output ใน Command Prompt ที่ควรเห็น:**

```
VITE v5.x.x  ready in xxx ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

เปิดเบราว์เซอร์ไปที่ `http://localhost:5173` จะเห็นแอป Task Manager ที่สร้างด้วย TypeScript

**ใน Browser Console** (กด `F12` → Console) จะเห็น:

```
=== TypeScript Lab ===
siteName: Task Manager
maxTasks: 100
isDarkMode: false

=== สร้าง Task ด้วย createTask() ===
createTask(): { id: 1234567890, title: 'ทดสอบ TypeScript', done: false, priority: 'high', ... }

=== Generics: getFirst<T> ===
getFirst([1,2,3]): 1
getFirst(["a","b"]): a
getFirst([]): undefined

=== filterTasks() ===
pending tasks: (2) ['งาน B', 'งาน C']
```

---

#### ขั้นตอนที่ 7 — ตรวจสอบ TypeScript Compiler

เปิด **Command Prompt** ใหม่ (อีกหน้าต่าง) แล้วรัน:

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab07-typescript"
pnpm exec tsc --noEmit
```

**Output ที่ควรเห็น** (ถ้าไม่มี Error):
```
(ไม่มีข้อความใด ๆ — หมายถึงผ่านทั้งหมด ✅)
```

ถ้ามี Error TypeScript จะแสดงรายละเอียด เช่น:
```
src/main.ts:10:5 - error TS2322: Type 'string' is not assignable to type 'number'.
```

---

## 🛑 หยุด Dev Server

เมื่อต้องการหยุด Vite ให้กด `Ctrl + C` ใน Command Prompt แล้วกด `Y` เพื่อยืนยัน

---

## 📌 สรุป Path ไฟล์ทั้งหมดของแลปนี้

| Lab | ไฟล์ | วิธีรัน |
|-----|------|---------|
| Lab 01 | `...\lab01-variables\variables.js` | `node variables.js` |
| Lab 02 | `...\lab02-array-methods\array-methods.js` | `node array-methods.js` |
| Lab 03 | `...\lab03-destructuring\destructuring.js` | `node destructuring.js` |
| Lab 04 | `...\lab04-optional-chaining\optional-chaining.js` | `node optional-chaining.js` |
| Lab 05 | `...\lab05-functions\functions.js` | `node functions.js` |
| Lab 06 | `...\lab06-async-await\async-await.js` | `node async-await.js` |
| Lab 07 | `...\lab07-typescript\src\main.ts` | `pnpm dev` → เปิดเบราว์เซอร์ |

> **`...`** ย่อจาก `C:\Users\<ชื่อ>\Documents\<ชื่อ>\unit3-js-lab\`

---

## โปรเจกต์ปฏิบัติสุดท้าย — Task Manager (Full TypeScript)

> **ใช้โปรเจกต์ `lab07-typescript` ที่สร้างไว้แล้ว** โปรเจกต์นี้คือโปรเจกต์ส่งงานจริง

### โครงสร้างไฟล์เป้าหมาย

นักเรียนต้องแยกโค้ดออกเป็นไฟล์ต่างๆ ดังนี้:

```
lab07-typescript\
├── src\
│   ├── main.ts                   ← Entry Point
│   ├── style.css
│   ├── vite-env.d.ts
│   ├── types\
│   │   └── task.ts               ← สร้างใหม่: Interface ทั้งหมด
│   ├── utils\
│   │   ├── storage.ts            ← สร้างใหม่: localStorage helpers
│   │   └── dom.ts                ← สร้างใหม่: DOM helpers
│   └── services\
│       └── taskService.ts        ← สร้างใหม่: Business Logic
├── index.html
├── tsconfig.json
└── package.json
```

สร้างโฟลเดอร์:

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab07-typescript\src"
mkdir types utils services
```

### `src/types/task.ts`

```typescript
export type Priority = 'low' | 'medium' | 'high'
export type TaskStatus = 'all' | 'pending' | 'done'

export interface Task {
  id: number
  title: string
  description?: string
  done: boolean
  priority: Priority
  createdAt: string
  dueDate?: string
  tags: string[]
}

export interface FilterState {
  status: TaskStatus
  priority?: Priority
  searchQuery: string
}

export interface AppState {
  tasks: Task[]
  filter: FilterState
  editingTaskId: number | null
}
```

### `src/utils/storage.ts`

```typescript
export function saveToStorage<T>(key: string, value: T): void {
  localStorage.setItem(key, JSON.stringify(value))
}

export function loadFromStorage<T>(key: string, defaultValue: T): T {
  try {
    const item = localStorage.getItem(key)
    return item ? (JSON.parse(item) as T) : defaultValue
  } catch {
    return defaultValue
  }
}

export function clearStorage(key: string): void {
  localStorage.removeItem(key)
}
```

### `src/services/taskService.ts`

```typescript
import type { Task, FilterState, Priority } from '../types/task.js'

export function createTask(title: string, priority: Priority = 'medium'): Task {
  return {
    id: Date.now(),
    title: title.trim(),
    done: false,
    priority,
    createdAt: new Date().toISOString(),
    tags: [],
  }
}

export function toggleTask(tasks: Task[], id: number): Task[] {
  return tasks.map(task =>
    task.id === id ? { ...task, done: !task.done } : task
  )
}

export function updateTask(tasks: Task[], id: number, updates: Partial<Task>): Task[] {
  return tasks.map(task =>
    task.id === id ? { ...task, ...updates } : task
  )
}

export function deleteTask(tasks: Task[], id: number): Task[] {
  return tasks.filter(task => task.id !== id)
}

export function filterTasks(tasks: Task[], filter: FilterState): Task[] {
  return tasks.filter(task => {
    if (filter.status === 'done' && !task.done) return false
    if (filter.status === 'pending' && task.done) return false
    if (filter.priority && task.priority !== filter.priority) return false
    if (filter.searchQuery) {
      const q = filter.searchQuery.toLowerCase()
      if (!task.title.toLowerCase().includes(q)) return false
    }
    return true
  })
}

export function sortTasks(tasks: Task[]): Task[] {
  const priorityOrder: Record<Priority, number> = { high: 0, medium: 1, low: 2 }
  return [...tasks].sort((a, b) => {
    if (a.done !== b.done) return a.done ? 1 : -1
    return priorityOrder[a.priority] - priorityOrder[b.priority]
  })
}
```

### ข้อกำหนดของโปรเจกต์

```
Features ที่ต้องมี:
✅ เพิ่ม Task ใหม่พร้อม Priority (low/medium/high)
✅ Toggle Task ว่าเสร็จหรือยัง
✅ ลบ Task พร้อม Confirm Dialog
✅ กรองตาม Status (all/pending/done)
✅ ค้นหา Task ตามชื่อ
✅ บันทึกลง localStorage อัตโนมัติ
✅ โหลดข้อมูลจาก localStorage เมื่อเปิดหน้าใหม่

TypeScript:
✅ ทุกตัวแปรมี Type — ไม่มี any
✅ ทุก Function มี Type Annotations
✅ ใช้ Interface กำหนดโครงสร้าง Task และ State
✅ ใช้ Generics ใน Storage Functions

Code Quality:
✅ แบ่งโค้ดเป็น Module ตามหน้าที่
✅ ผ่าน TypeScript Compiler โดยไม่มี Error (pnpm exec tsc --noEmit)
✅ Build สำเร็จ (pnpm build)

Bonus:
⭐ แก้ไขชื่อ Task ได้ (Inline Edit)
⭐ จัด Sort ตาม Priority อัตโนมัติ
⭐ แสดงสรุป x/y เสร็จแล้ว
⭐ Dark Mode
```

### คำสั่งก่อนส่งงาน

```cmd
cd "%USERPROFILE%\Documents\%USERNAME%\unit3-js-lab\lab07-typescript"

:: ✅ ตรวจสอบ TypeScript ต้องไม่มี Error
pnpm exec tsc --noEmit

:: ✅ Build ต้องสำเร็จ
pnpm build
```

ถ้าทั้งสองคำสั่งผ่านโดยไม่มี Error — พร้อมส่งงาน ✅

---

## สรุปหน่วยที่ 3

```
สิ่งที่เรียนรู้และฝึกปฏิบัติในหน่วยนี้
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Lab 01 — const/let เท่านั้น, ห้ามใช้ var
   ทดสอบ: node variables.js

✅ Lab 02 — Array Methods: map, filter, reduce, find, some, every
   ทดสอบ: node array-methods.js

✅ Lab 03 — Destructuring และ Spread (...) ทำให้โค้ดกระชับและ Immutable
   ทดสอบ: node destructuring.js

✅ Lab 04 — Optional Chaining (?.) และ Nullish Coalescing (??) ป้องกัน Bug
   ทดสอบ: node optional-chaining.js

✅ Lab 05 — Arrow Functions และ Higher-Order Functions
   ทดสอบ: node functions.js

✅ Lab 06 — async/await ทำให้เขียน Async Code อ่านเหมือน Sync
   try/catch/finally จัดการ Error, fetch() ดึงข้อมูลจาก API จริง
   ทดสอบ: node async-await.js

✅ Lab 07 — TypeScript เพิ่ม Type Safety ทำให้ Bug หายก่อน Runtime
   Interface กำหนดโครงสร้าง, Generics ทำให้ Function ยืดหยุ่น
   Utility Types: Partial, Pick, Omit, Record ใช้บ่อยมากในทีม
   ทดสอบ: pnpm dev → เปิดเบราว์เซอร์ http://localhost:5173
```

---

## คำศัพท์ที่ควรรู้ (Glossary)

| คำศัพท์ | คำอ่าน | ความหมาย |
|---------|--------|---------|
| Asynchronous | อะซิงโครนัส | โค้ดที่ไม่รอผลก่อน ทำงานถัดไปได้เลย |
| Promise | พรอมมิส | Object ที่แทน "ค่าที่จะมีในอนาคต" |
| async/await | อะซิงก์/อะเวต | Syntax สำหรับจัดการ Promise ให้อ่านง่าย |
| fetch() | เฟตช์ | Web API สำหรับส่ง HTTP Request |
| DOM | ดีโอเอ็ม | โครงสร้าง Object ที่เบราว์เซอร์สร้างจาก HTML |
| Event Listener | อีเวนต์ ลิสเทนเนอร์ | ฟังก์ชันที่รอฟัง Event เช่น click, submit |
| Event Delegation | อีเวนต์ เดลิเกชัน | เพิ่ม Listener ที่ Parent แทนที่จะเพิ่มทุก Child |
| Destructuring | ดีสตรัคเจอริง | แกะค่าออกจาก Object/Array ด้วย Syntax กระชับ |
| Spread Operator | สเปรด โอเปอเรเตอร์ | `...` ใช้กระจายหรือคัดลอก Array/Object |
| Optional Chaining | ออปชันนัล เชนนิง | `?.` เข้าถึง Property โดยไม่ Crash ถ้า null |
| Nullish Coalescing | นัลลิช โคแอลเลสซิง | `??` ใช้ค่า Default เฉพาะเมื่อเป็น null/undefined |
| Higher-Order Function | ไฮเออร์ ออร์เดอร์ ฟังก์ชัน | Function ที่รับหรือ Return Function |
| Closure | โคลเจอร์ | Function ที่จำค่าจาก Scope รอบนอก |
| ES Modules | อีเอส โมดูลส์ | ระบบ import/export มาตรฐานของ JavaScript |
| TypeScript | ไทป์สคริปต์ | JavaScript ที่เพิ่มระบบ Type |
| Type Annotation | ไทป์ แอนโนเทชัน | การระบุ Type ของตัวแปรหรือ Parameter |
| Type Inference | ไทป์ อินเฟอเรนซ์ | TypeScript อนุมาน Type อัตโนมัติ |
| Interface | อินเทอร์เฟซ | กำหนดโครงสร้างของ Object |
| Generic | เจเนริก | Type ที่ยืดหยุ่น กำหนดตอนเรียกใช้ |
| Utility Types | ยูทิลิตี ไทป์ส | Type Helper ที่ TypeScript มีให้: Partial, Omit, Pick |
| XSS | เอกซ์เอสเอส | Cross-Site Scripting — ช่องโหว่จาก HTML Injection |
| ESLint | อีเอสลินต์ | เครื่องมือตรวจสอบโค้ดและหา Bug |
| Prettier | พรีทเทียร์ | เครื่องมือจัดรูปแบบโค้ดอัตโนมัติ |

---

## แหล่งเรียนรู้เพิ่มเติม

- **javascript.info** — บทเรียน JavaScript ภาษาอังกฤษที่ครบและดีที่สุด
- **developer.mozilla.org/en-US/docs/Web/JavaScript** — เอกสาร JavaScript อ้างอิง MDN
- **typescriptlang.org/docs** — เอกสาร TypeScript อย่างเป็นทางการ
- **typescriptlang.org/play** — ลองเขียน TypeScript ออนไลน์ เห็นผลทันที
- **roadmap.sh/typescript** — Roadmap การเรียน TypeScript

---

*หน่วยถัดไป → หน่วยที่ 4: React และ Next.js — Framework หลัก*
