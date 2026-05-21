# หน่วยที่ 3: JavaScript สมัยใหม่และ TypeScript

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

### Variables: `const` และ `let` เท่านั้น

ในปี 2026 ไม่มีเหตุผลใดที่จะใช้ `var` อีกต่อไป ใช้แค่สองตัวนี้เท่านั้น:

```javascript
// const — ค่าที่ไม่เปลี่ยนแปลง (ใช้เป็นค่า default เสมอ)
const siteName = 'Task Manager'
const MAX_TASKS = 100
const user = { name: 'สมชาย', age: 28 }  // Object ยัง mutate ได้ แต่ตัวแปรชี้ไม่เปลี่ยน

// let — ค่าที่ต้องเปลี่ยนแปลง (ใช้เมื่อจำเป็นจริง ๆ)
let count = 0
let isLoading = false
let currentPage = 1

// ❌ ห้ามใช้ var เด็ดขาด — มีปัญหาเรื่อง Scope และ Hoisting
var oldStyle = 'อย่าใช้'
```

**กฎง่าย ๆ:** เริ่มด้วย `const` เสมอ ถ้า ESLint บอก error ค่อยเปลี่ยนเป็น `let`

---

### Data Types ที่ต้องรู้

```javascript
// Primitive Types — ค่าพื้นฐาน
const name = 'สมชาย'          // string
const age = 28                  // number
const isActive = true           // boolean
const nothing = null            // null (ตั้งใจให้ว่าง)
let notYet                      // undefined (ยังไม่ได้กำหนดค่า)

// Reference Types — เก็บหลายค่า
const fruits = ['มะม่วง', 'ส้ม', 'กล้วย']   // Array
const person = { name: 'สมชาย', age: 28 }     // Object

// ตรวจสอบ Type
console.log(typeof name)        // "string"
console.log(typeof age)         // "number"
console.log(typeof isActive)    // "boolean"
console.log(Array.isArray(fruits)) // true

// Type Coercion — JavaScript แปลงค่าอัตโนมัติ (ระวัง!)
console.log('5' + 3)    // "53" (ต่อ string ไม่ใช่บวก)
console.log('5' - 3)    // 2 (ลบได้ปกติ)
console.log('' == false) // true (Loose equality — อย่าใช้)
console.log('' === false) // false (Strict equality — ใช้นี้เสมอ)
```

> **💡 กฎ:** ใช้ `===` และ `!==` เสมอ ห้ามใช้ `==` และ `!=` เพราะแปลงค่าอัตโนมัติจนทำให้ Bug หาได้ยาก

---

### Array Methods ที่ใช้ทุกวัน

Array Methods เหล่านี้ทำงานโดย **ไม่แก้ไข Array เดิม** (Immutable) และ Return ค่าใหม่ออกมา — เป็นหัวใจของการเขียน JavaScript สมัยใหม่

**`map` — แปลงทุก Element:**

```javascript
const prices = [100, 250, 80, 320]

// เพิ่ม VAT 7% ทุกราคา
const pricesWithVAT = prices.map(price => price * 1.07)
// [107, 267.5, 85.6, 342.4]

// แปลง Array of Objects
const tasks = [
  { id: 1, title: 'เรียน JavaScript', done: false },
  { id: 2, title: 'ทำโปรเจกต์', done: true },
]

const titles = tasks.map(task => task.title)
// ['เรียน JavaScript', 'ทำโปรเจกต์']

const tasksWithLabel = tasks.map(task => ({
  ...task,
  label: task.done ? '✅ เสร็จแล้ว' : '⏳ ยังไม่เสร็จ'
}))
```

**`filter` — กรองเฉพาะที่ต้องการ:**

```javascript
const tasks = [
  { id: 1, title: 'เรียน JavaScript', done: false, priority: 'high' },
  { id: 2, title: 'ทำโปรเจกต์', done: true, priority: 'high' },
  { id: 3, title: 'อ่านหนังสือ', done: false, priority: 'low' },
]

// กรองเฉพาะ Task ที่ยังไม่เสร็จ
const pending = tasks.filter(task => !task.done)
// [{ id: 1, ... }, { id: 3, ... }]

// กรองหลายเงื่อนไข
const urgentPending = tasks.filter(task => !task.done && task.priority === 'high')
// [{ id: 1, ... }]
```

**`reduce` — รวมเป็นค่าเดียว:**

```javascript
const prices = [100, 250, 80, 320]

// รวมยอดทั้งหมด
const total = prices.reduce((sum, price) => sum + price, 0)
// 750

// นับจำนวนแต่ละสถานะ
const tasks = [
  { status: 'done' }, { status: 'pending' },
  { status: 'done' }, { status: 'pending' }, { status: 'pending' }
]

const count = tasks.reduce((acc, task) => {
  acc[task.status] = (acc[task.status] || 0) + 1
  return acc
}, {})
// { done: 2, pending: 3 }
```

**`find` และ `findIndex` — หาตัวแรกที่ตรงเงื่อนไข:**

```javascript
const tasks = [
  { id: 1, title: 'เรียน JavaScript' },
  { id: 2, title: 'ทำโปรเจกต์' },
  { id: 3, title: 'อ่านหนังสือ' },
]

// find — ได้ Object ตัวแรกที่ตรง (หรือ undefined)
const task = tasks.find(t => t.id === 2)
// { id: 2, title: 'ทำโปรเจกต์' }

// findIndex — ได้ Index (หรือ -1)
const index = tasks.findIndex(t => t.id === 2)
// 1

// ใช้ findIndex เพื่อลบ Task
const updatedTasks = tasks.filter((_, i) => i !== index)
```

**`some` และ `every` — ตรวจสอบเงื่อนไข:**

```javascript
const tasks = [
  { id: 1, done: true },
  { id: 2, done: false },
  { id: 3, done: true },
]

const hasAnyDone = tasks.some(t => t.done)   // true — มีอย่างน้อย 1
const allDone = tasks.every(t => t.done)      // false — ทุกตัวต้องเป็น true
```

---

### Object Destructuring และ Spread Operator

**Destructuring — แกะค่าออกจาก Object/Array:**

```javascript
// Object Destructuring
const user = { name: 'สมชาย', age: 28, city: 'กรุงเทพฯ' }

// แบบเก่า
const name = user.name
const age = user.age

// แบบ Destructuring — กระชับกว่า
const { name, age } = user
const { name: userName, age: userAge } = user  // เปลี่ยนชื่อตัวแปร

// Default Value เมื่อค่าเป็น undefined
const { name, role = 'user' } = user
// role = 'user' เพราะ user ไม่มี role

// Array Destructuring
const colors = ['red', 'green', 'blue']
const [first, second] = colors
// first = 'red', second = 'green'

const [, , third] = colors  // ข้ามค่าที่ไม่ต้องการ
// third = 'blue'

// Destructuring ใน Function Parameter — ใช้บ่อยมากใน React
function renderTask({ id, title, done }) {
  return `<li data-id="${id}">${title} ${done ? '✅' : ''}</li>`
}
```

**Spread Operator `...` — กระจาย/คัดลอกค่า:**

```javascript
// Copy Array โดยไม่แก้ของเดิม
const original = [1, 2, 3]
const copy = [...original]           // [1, 2, 3] — Object ใหม่
const extended = [...original, 4, 5] // [1, 2, 3, 4, 5]

// รวม Array
const a = [1, 2]
const b = [3, 4]
const combined = [...a, ...b]  // [1, 2, 3, 4]

// Copy Object โดยไม่แก้ของเดิม (Shallow Copy)
const task = { id: 1, title: 'เรียน JS', done: false }
const updated = { ...task, done: true }
// { id: 1, title: 'เรียน JS', done: true }

// Merge Object
const defaults = { theme: 'light', lang: 'th', fontSize: 16 }
const userPrefs = { theme: 'dark' }
const settings = { ...defaults, ...userPrefs }
// { theme: 'dark', lang: 'th', fontSize: 16 }
// userPrefs ทับค่าใน defaults
```

**Rest Parameter `...` — รวม Arguments:**

```javascript
// รับ Arguments ไม่จำกัดจำนวน
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0)
}
sum(1, 2, 3, 4, 5)  // 15

// แยกบางค่าออก แล้วเก็บที่เหลือ
const { id, ...rest } = { id: 1, title: 'งาน A', priority: 'high' }
// id = 1
// rest = { title: 'งาน A', priority: 'high' }
```

---

### Optional Chaining `?.` และ Nullish Coalescing `??`

**Optional Chaining `?.` — เข้าถึงค่าโดยไม่ Crash เมื่อค่าเป็น null/undefined:**

```javascript
const user = {
  profile: {
    name: 'สมชาย',
    address: null
  }
}

// แบบเก่า — ยาวและน่าเบื่อ
const city = user && user.profile && user.profile.address && user.profile.address.city

// แบบ Optional Chaining — กระชับและปลอดภัย
const city = user?.profile?.address?.city
// undefined — ไม่ crash แม้ address เป็น null

// กับ Array
const firstTag = user?.tags?.[0]

// กับ Method Call
const displayName = user?.getDisplayName?.()

// ตัวอย่างจริง — Response จาก API
async function getUsername(userId) {
  const response = await fetchUser(userId)
  return response?.data?.user?.profile?.username ?? 'ไม่ระบุชื่อ'
}
```

**Nullish Coalescing `??` — ค่า Default เฉพาะเมื่อเป็น null หรือ undefined:**

```javascript
// ?? ต่างจาก || ตรงที่ 0 และ '' ไม่ถือว่าเป็น "falsy"
const count = 0
const score = ''

console.log(count || 10)   // 10  ❌ ผิด! 0 ก็ถูกแทนที่
console.log(count ?? 10)   // 0   ✅ ถูก! 0 เป็นค่าที่ valid

console.log(score || 'ไม่ระบุ')   // 'ไม่ระบุ' ❌ '' ก็ถูกแทนที่
console.log(score ?? 'ไม่ระบุ')   // ''  ✅ '' เป็นค่าที่ valid

// ใช้คู่กับ Optional Chaining บ่อยมาก
const username = user?.profile?.name ?? 'ผู้ใช้ไม่ระบุชื่อ'
const itemCount = cart?.items?.length ?? 0
```

---

### Template Literals และ Short-circuit Evaluation

**Template Literals — ต่อ String อย่างสวยงาม:**

```javascript
const name = 'สมชาย'
const score = 95

// แบบเก่า
const message = 'สวัสดี ' + name + '! คะแนนของคุณคือ ' + score + ' คะแนน'

// Template Literal — อ่านง่ายกว่ามาก
const message = `สวัสดี ${name}! คะแนนของคุณคือ ${score} คะแนน`

// ใส่ Expression ได้เลย
const grade = `เกรด: ${score >= 80 ? 'A' : score >= 70 ? 'B' : 'C'}`

// Multi-line String
const html = `
  <div class="card">
    <h2>${name}</h2>
    <p>คะแนน: ${score}</p>
  </div>
`
```

**Short-circuit Evaluation — เงื่อนไขแบบกระชับ:**

```javascript
// && (AND) — ทำสิ่งที่สองเมื่อสิ่งแรกเป็น true
const isLoggedIn = true
isLoggedIn && console.log('เข้าสู่ระบบแล้ว')

// ใช้บ่อยใน JSX ของ React
const hasError = true
const element = hasError && '<div class="error">เกิดข้อผิดพลาด</div>'

// || (OR) — ใช้ค่าแรกที่ไม่ใช่ falsy
const displayName = user.nickname || user.name || 'ไม่ระบุ'

// ลำดับความสำคัญ: ใช้คู่กัน
const label = task.customLabel || task.title || 'ไม่มีชื่อ'
const count = data?.count ?? 0
```

---

## 3.2 Functions และ Asynchronous JavaScript

### Arrow Functions

**Arrow Function** คือ Syntax ที่กระชับและทันสมัย เป็นมาตรฐานในการเขียน JavaScript ปัจจุบัน:

```javascript
// Function Declaration แบบเดิม
function add(a, b) {
  return a + b
}

// Arrow Function แบบเต็ม
const add = (a, b) => {
  return a + b
}

// Arrow Function แบบกระชับ (1 บรรทัด — return อัตโนมัติ)
const add = (a, b) => a + b

// Parameter เดียว ไม่ต้องมีวงเล็บ
const double = n => n * 2
const greet = name => `สวัสดี ${name}!`

// Return Object — ต้องใส่วงเล็บครอบ
const makeUser = (name, age) => ({ name, age })

// ใช้กับ Array Methods — อ่านง่ายมาก
const prices = [100, 200, 300]
const doubled = prices.map(p => p * 2)
const expensive = prices.filter(p => p > 150)
const total = prices.reduce((sum, p) => sum + p, 0)
```

---

### Higher-Order Functions

**Higher-Order Function** คือ Function ที่รับ Function เป็น Argument หรือ Return Function ออกมา — เป็นแนวคิดหลักของ JavaScript:

```javascript
// Function ที่รับ Function เป็น Argument
function doTwice(fn, value) {
  return fn(fn(value))
}

doTwice(n => n * 2, 3)   // 12 (3 → 6 → 12)
doTwice(s => s + '!', 'Hi')  // 'Hi!!'

// Function ที่ Return Function ออกมา (Closure)
function createMultiplier(factor) {
  return (number) => number * factor
}

const double = createMultiplier(2)
const triple = createMultiplier(3)
double(5)   // 10
triple(5)   // 15

// ตัวอย่างจริง — สร้าง Validator
function createValidator(min, max) {
  return (value) => {
    if (value < min) return `ต้องมากกว่า ${min}`
    if (value > max) return `ต้องน้อยกว่า ${max}`
    return null
  }
}

const validateAge = createValidator(0, 120)
const validateScore = createValidator(0, 100)

validateAge(25)    // null (valid)
validateAge(-5)    // 'ต้องมากกว่า 0'
validateScore(150) // 'ต้องน้อยกว่า 100'
```

---

### Promises และ async/await

**ทำไม JavaScript ต้องการ Asynchronous:**

เมื่อดึงข้อมูลจาก API หรืออ่านไฟล์ JavaScript ไม่รอ — มันทำงานต่อแล้วค่อยกลับมาเมื่อข้อมูลพร้อม ถ้าไม่มี Async Code ทั้งหน้าเว็บจะ Freeze ระหว่างรอข้อมูล

**Promise — สัญญาว่าจะมีค่าในอนาคต:**

```javascript
// Promise มี 3 สถานะ
// pending  — กำลังรอ
// fulfilled — สำเร็จ (ได้ข้อมูล)
// rejected  — ล้มเหลว (เกิด Error)

const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    const success = Math.random() > 0.3
    if (success) {
      resolve({ data: 'ข้อมูลที่ต้องการ' })
    } else {
      reject(new Error('เกิดข้อผิดพลาด'))
    }
  }, 1000)
})

promise
  .then(result => console.log(result.data))
  .catch(error => console.error(error.message))
  .finally(() => console.log('ทำงานเสมอ ไม่ว่าจะสำเร็จหรือไม่'))
```

**async/await — เขียน Async Code ให้อ่านเหมือน Sync:**

```javascript
// เพิ่ม async ที่ Function
async function fetchUser(userId) {
  // await หยุดรอ Promise โดยไม่ Freeze Browser
  const response = await fetch(`/api/users/${userId}`)
  const data = await response.json()
  return data
}

// เรียกใช้
fetchUser(1).then(user => console.log(user))

// หรือใน async Function อื่น
async function loadProfile() {
  const user = await fetchUser(1)
  console.log(user)
}
```

---

### Error Handling ด้วย try/catch/finally

```javascript
async function fetchTasks() {
  try {
    // โค้ดที่อาจ Error
    const response = await fetch('/api/tasks')

    // ตรวจสอบ HTTP Status
    if (!response.ok) {
      throw new Error(`HTTP Error: ${response.status}`)
    }

    const tasks = await response.json()
    return tasks

  } catch (error) {
    // จัดการ Error ตามประเภท
    if (error instanceof TypeError) {
      // TypeError = Network ล้มเหลว (ไม่มีอินเทอร์เน็ต)
      console.error('ไม่สามารถเชื่อมต่อเครือข่ายได้')
    } else {
      console.error('เกิดข้อผิดพลาด:', error.message)
    }

    // Return ค่า Default เพื่อให้ App ยังทำงานได้
    return []

  } finally {
    // ทำงานเสมอ — ใช้ปิด Loading State
    setIsLoading(false)
  }
}

// ตัวอย่างแบบครบถ้วนที่ใช้จริง
async function loadAndDisplayTasks() {
  setIsLoading(true)
  setError(null)

  try {
    const tasks = await fetchTasks()
    displayTasks(tasks)
  } catch (error) {
    setError('โหลดข้อมูลไม่สำเร็จ กรุณาลองใหม่')
  } finally {
    setIsLoading(false)
  }
}
```

---

### fetch() API: ดึงข้อมูลจากเซิร์ฟเวอร์

`fetch()` เป็น Web API มาตรฐานสำหรับส่ง HTTP Request:

```javascript
// GET — ดึงข้อมูล
async function getTasks() {
  const response = await fetch('https://jsonplaceholder.typicode.com/todos')
  const tasks = await response.json()
  return tasks
}

// GET พร้อม Query Parameters
async function searchTasks(query) {
  const params = new URLSearchParams({ q: query, limit: '10' })
  const response = await fetch(`/api/tasks?${params}`)
  return response.json()
}

// POST — สร้างข้อมูลใหม่
async function createTask(taskData) {
  const response = await fetch('/api/tasks', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${getToken()}`,
    },
    body: JSON.stringify(taskData),
  })

  if (!response.ok) {
    const error = await response.json()
    throw new Error(error.message || 'สร้าง Task ไม่สำเร็จ')
  }

  return response.json()
}

// PUT — อัปเดตข้อมูลทั้งหมด
async function updateTask(id, taskData) {
  const response = await fetch(`/api/tasks/${id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(taskData),
  })
  return response.json()
}

// PATCH — อัปเดตบางส่วน
async function toggleTask(id, done) {
  const response = await fetch(`/api/tasks/${id}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ done }),
  })
  return response.json()
}

// DELETE — ลบข้อมูล
async function deleteTask(id) {
  const response = await fetch(`/api/tasks/${id}`, {
    method: 'DELETE',
  })
  if (!response.ok) throw new Error('ลบ Task ไม่สำเร็จ')
}
```

**Helper Function สำหรับ fetch — ใช้ในโปรเจกต์จริง:**

```javascript
// สร้าง Wrapper ที่จัดการ Error อัตโนมัติ
async function api(url, options = {}) {
  const baseURL = 'https://api.example.com'
  const token = localStorage.getItem('token')

  const response = await fetch(`${baseURL}${url}`, {
    headers: {
      'Content-Type': 'application/json',
      ...(token && { Authorization: `Bearer ${token}` }),
    },
    ...options,
  })

  if (!response.ok) {
    const error = await response.json().catch(() => ({}))
    throw new Error(error.message || `Request failed: ${response.status}`)
  }

  // 204 No Content — ไม่มี body
  if (response.status === 204) return null
  return response.json()
}

// ใช้งาน
const tasks = await api('/tasks')
const newTask = await api('/tasks', {
  method: 'POST',
  body: JSON.stringify({ title: 'งานใหม่' }),
})
```

---

## 3.3 DOM Manipulation และ Events

### DOM คืออะไร

**DOM** (ดีโอเอ็ม — Document Object Model) คือโครงสร้างที่เบราว์เซอร์สร้างจาก HTML ทำให้ JavaScript สามารถอ่านและแก้ไขหน้าเว็บได้:

```
HTML ที่เขียน:                DOM ที่เบราว์เซอร์สร้าง:
<body>                         document
  <main>              →          └─ body
    <h1>หัวข้อ</h1>                 └─ main
    <ul>                                ├─ h1 (text: "หัวข้อ")
      <li>Task 1</li>                   └─ ul
    </ul>                                   └─ li (text: "Task 1")
  </main>
</body>
```

---

### เลือก Element

```javascript
// querySelector — เลือก Element แรกที่ตรง
const title = document.querySelector('h1')
const btn = document.querySelector('#submit-btn')         // by ID
const card = document.querySelector('.card')              // by class
const input = document.querySelector('input[type="email"]') // by attribute

// querySelectorAll — เลือกทุก Element ที่ตรง (NodeList)
const allCards = document.querySelectorAll('.card')
const listItems = document.querySelectorAll('ul li')

// วนลูปผ่าน NodeList
allCards.forEach(card => {
  card.classList.add('visible')
})

// แปลงเป็น Array เพื่อใช้ Array Methods
const cards = [...document.querySelectorAll('.card')]
const visibleCards = cards.filter(card => !card.hidden)
```

---

### เปลี่ยนแปลง DOM

```javascript
const taskItem = document.querySelector('#task-1')

// เปลี่ยนข้อความ
taskItem.textContent = 'Task ที่แก้ไขแล้ว'

// เปลี่ยน HTML ภายใน (ระวัง XSS!)
taskItem.innerHTML = '<span class="badge">ใหม่</span> Task ที่แก้ไขแล้ว'

// จัดการ Class
taskItem.classList.add('completed')         // เพิ่ม class
taskItem.classList.remove('pending')        // ลบ class
taskItem.classList.toggle('highlight')      // toggle on/off
taskItem.classList.contains('completed')    // ตรวจสอบ

// จัดการ Attribute
taskItem.setAttribute('data-id', '1')
taskItem.getAttribute('data-id')            // '1'
taskItem.removeAttribute('hidden')

// จัดการ Style โดยตรง (ใช้น้อย — ควรใช้ class แทน)
taskItem.style.opacity = '0.5'
taskItem.style.display = 'none'

// สร้าง Element ใหม่
const newTask = document.createElement('li')
newTask.className = 'task-item'
newTask.textContent = 'Task ใหม่'
newTask.dataset.id = '5'  // data-id="5"

// เพิ่มเข้า DOM
const list = document.querySelector('#task-list')
list.appendChild(newTask)               // เพิ่มท้าย
list.prepend(newTask)                   // เพิ่มหัว
list.insertBefore(newTask, list.firstChild) // เพิ่มก่อน Element

// ลบออกจาก DOM
taskItem.remove()
```

---

### Event Listeners

```javascript
const btn = document.querySelector('#add-task-btn')
const form = document.querySelector('#task-form')
const searchInput = document.querySelector('#search')

// click — กดปุ่ม
btn.addEventListener('click', () => {
  console.log('กดปุ่มแล้ว')
})

// submit — ส่ง Form
form.addEventListener('submit', (event) => {
  event.preventDefault()  // ป้องกัน Browser Reload หน้า

  const formData = new FormData(event.target)
  const title = formData.get('title')

  if (title.trim()) {
    addTask(title)
    event.target.reset()  // ล้าง Form
  }
})

// input — พิมพ์ข้อความ (ทุก keystroke)
searchInput.addEventListener('input', (event) => {
  const query = event.target.value
  filterTasks(query)
})

// keydown — กดปุ่มบนคีย์บอร์ด
searchInput.addEventListener('keydown', (event) => {
  if (event.key === 'Escape') {
    searchInput.value = ''
    filterTasks('')
  }
  if (event.key === 'Enter') {
    event.preventDefault()
  }
})

// change — เปลี่ยนค่า (เมื่อ blur หรือ change)
const select = document.querySelector('#filter-select')
select.addEventListener('change', (event) => {
  const filter = event.target.value  // 'all' | 'done' | 'pending'
  applyFilter(filter)
})

// ลบ Event Listener
function handleClick() { console.log('คลิก') }
btn.addEventListener('click', handleClick)
btn.removeEventListener('click', handleClick)  // ต้องใช้ Reference เดิม
```

---

### Event Delegation: จัดการ Event อย่างมีประสิทธิภาพ

แทนที่จะเพิ่ม Listener ให้ทุก Element ให้เพิ่มที่ Parent Element เดียว — ลด Memory และรองรับ Element ที่เพิ่มมาทีหลัง:

```javascript
// ❌ ไม่ดี — เพิ่ม Listener ทุก Task (ถ้ามี 100 Task = 100 Listener)
document.querySelectorAll('.task-item').forEach(item => {
  item.addEventListener('click', handleTaskClick)
  item.querySelector('.delete-btn').addEventListener('click', handleDelete)
})

// ✅ ดีกว่า — เพิ่ม Listener ที่ List เดียว
const taskList = document.querySelector('#task-list')

taskList.addEventListener('click', (event) => {
  // หา Element ที่ใกล้ที่สุดที่ตรง Selector
  const taskItem = event.target.closest('.task-item')
  if (!taskItem) return  // คลิกที่อื่นที่ไม่ใช่ Task

  const taskId = taskItem.dataset.id

  // ตรวจสอบว่ากดปุ่มอะไร
  if (event.target.matches('.delete-btn')) {
    deleteTask(taskId)
  } else if (event.target.matches('.complete-btn')) {
    toggleTask(taskId)
  } else if (event.target.matches('.edit-btn')) {
    openEditModal(taskId)
  }
})

// ตัวอย่างจริง — Task Manager List
function renderTasks(tasks) {
  const list = document.querySelector('#task-list')
  list.innerHTML = tasks.map(task => `
    <li class="task-item" data-id="${task.id}">
      <span class="task-title ${task.done ? 'line-through' : ''}">
        ${escapeHTML(task.title)}
      </span>
      <div class="task-actions">
        <button class="complete-btn">${task.done ? '↩️' : '✅'}</button>
        <button class="edit-btn">✏️</button>
        <button class="delete-btn">🗑️</button>
      </div>
    </li>
  `).join('')
}

// ป้องกัน XSS — ใช้ escapeHTML ก่อนแสดงข้อความจากผู้ใช้
function escapeHTML(str) {
  return str
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
}
```

---

## 3.4 ES Modules และ Tooling

### ES Modules: จัดโครงสร้างโค้ด

**ES Modules** ทำให้แบ่งโค้ดออกเป็นไฟล์ย่อย ๆ แต่ละไฟล์มีหน้าที่ชัดเจน ง่ายต่อการ Maintain:

```javascript
// utils/api.js — ฟังก์ชัน HTTP ทั้งหมด
export async function api(url, options = {}) {
  const response = await fetch(url, options)
  if (!response.ok) throw new Error(`HTTP ${response.status}`)
  return response.json()
}

export async function getTasks() {
  return api('/api/tasks')
}

export async function createTask(data) {
  return api('/api/tasks', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
  })
}

// utils/storage.js — ฟังก์ชัน localStorage ทั้งหมด
export function saveToStorage(key, value) {
  localStorage.setItem(key, JSON.stringify(value))
}

export function loadFromStorage(key, defaultValue = null) {
  try {
    const item = localStorage.getItem(key)
    return item ? JSON.parse(item) : defaultValue
  } catch {
    return defaultValue
  }
}

// utils/dom.js — Helper Functions สำหรับ DOM
export function createElement(tag, attrs = {}, children = []) {
  const el = document.createElement(tag)
  Object.entries(attrs).forEach(([key, val]) => el.setAttribute(key, val))
  children.forEach(child => el.append(child))
  return el
}

export function escapeHTML(str) {
  return str.replace(/[&<>"]/g, c => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;' })[c])
}

// constants.js — ค่าคงที่ของ App
export const STORAGE_KEYS = {
  TASKS: 'task-manager-tasks',
  FILTER: 'task-manager-filter',
}

export const TASK_STATUS = {
  ALL: 'all',
  PENDING: 'pending',
  DONE: 'done',
}

// main.js — Entry Point รวมทุกอย่าง
import { getTasks, createTask } from './utils/api.js'
import { saveToStorage, loadFromStorage } from './utils/storage.js'
import { STORAGE_KEYS, TASK_STATUS } from './constants.js'

// ใช้ Named Import ได้หลายตัวพร้อมกัน
import { renderTasks, renderFilter } from './components/taskList.js'
```

**Default vs Named Export:**

```javascript
// Named Export — ส่งออกหลายอย่างจากไฟล์เดียว
export function add(a, b) { return a + b }
export function subtract(a, b) { return a - b }
export const PI = 3.14159

// Named Import — ต้องตรงชื่อ
import { add, PI } from './math.js'
import { add as sum } from './math.js'  // เปลี่ยนชื่อได้

// Default Export — ส่งออกหลักของไฟล์ (1 ต่อไฟล์)
export default class TaskManager { ... }

// Default Import — ตั้งชื่ออะไรก็ได้
import TaskManager from './TaskManager.js'
import MyManager from './TaskManager.js'  // ชื่อไหนก็ได้
```

---

### ESLint + Prettier: มาตรฐานโค้ดในทีม

**ESLint** ตรวจจับ Bug และโค้ดที่เขียนไม่ดี **Prettier** จัดรูปแบบโค้ดอัตโนมัติ — ทั้งคู่ทำงานร่วมกัน:

```bash
# ติดตั้ง
pnpm add -D eslint prettier eslint-config-prettier @eslint/js

# สร้างไฟล์ Config
pnpm dlx @eslint/create-config
```

**`eslint.config.js`:**

```javascript
import js from '@eslint/js'

export default [
  js.configs.recommended,
  {
    rules: {
      // ห้ามใช้ var
      'no-var': 'error',
      // ต้องใช้ === ไม่ใช่ ==
      'eqeqeq': 'error',
      // ห้ามมี console.log ใน Production (warn ใน Dev ได้)
      'no-console': 'warn',
      // ต้องประกาศตัวแปรก่อนใช้
      'no-use-before-define': 'error',
      // ห้ามมีตัวแปรที่ประกาศแล้วไม่ได้ใช้
      'no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    },
  },
]
```

**`.prettierrc`:**

```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "arrowParens": "avoid"
}
```

**`package.json` — เพิ่ม Scripts:**

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint src/**/*.js",
    "lint:fix": "eslint src/**/*.js --fix",
    "format": "prettier --write src/**/*.{js,ts,css}"
  }
}
```

---

## 3.5 TypeScript

### TypeScript คืออะไร

**TypeScript** (ไทป์สคริปต์) คือ JavaScript ที่เพิ่มระบบ Type เข้ามา — บังคับให้ระบุว่าตัวแปรแต่ละตัวเก็บค่าประเภทอะไร Browser ไม่เข้าใจ TypeScript โดยตรง จึงต้อง Compile เป็น JavaScript ก่อน

```
TypeScript (.ts)  →  Compiler (tsc)  →  JavaScript (.js)  →  Browser
```

**ทำไมทุกทีมใช้ TypeScript ในปี 2026:**

```
ปัญหาของ JavaScript ล้วน ๆ:

function calculateTotal(price, quantity) {
  return price * quantity
}

calculateTotal('100', 5)   // '100' × 5 = '100100100100100'  ← Bug!
calculateTotal(100)        // 100 × undefined = NaN          ← Bug!
```

```typescript
// TypeScript ป้องกัน Bug เหล่านี้ตั้งแต่ก่อน Run

function calculateTotal(price: number, quantity: number): number {
  return price * quantity
}

calculateTotal('100', 5)  // ❌ Error ทันที: Argument of type 'string' is not assignable to parameter of type 'number'
calculateTotal(100)       // ❌ Error ทันที: Expected 2 arguments, but got 1
calculateTotal(100, 5)    // ✅ 500
```

---

### ติดตั้ง TypeScript กับ Vite

```bash
# สร้างโปรเจกต์ใหม่ด้วย TypeScript Template
pnpm create vite@latest task-manager --template vanilla-ts
cd task-manager
pnpm install
pnpm dev
```

ไฟล์ที่ได้:
```
task-manager/
├── src/
│   ├── main.ts        ← ไฟล์ .ts ไม่ใช่ .js
│   └── vite-env.d.ts  ← Type Declarations สำหรับ Vite
├── tsconfig.json      ← การตั้งค่า TypeScript
└── package.json
```

**`tsconfig.json` ที่แนะนำ:**

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

> **💡 สำคัญ:** เปิด `"strict": true` เสมอ — มันเปิด Type Checking ที่เข้มงวดที่สุด ป้องกัน Bug ได้มากที่สุด

---

### Type Annotations พื้นฐาน

```typescript
// Primitive Types
const siteName: string = 'Task Manager'
const maxTasks: number = 100
const isDarkMode: boolean = false

// TypeScript อนุมาน Type อัตโนมัติ (Type Inference)
const count = 0        // TypeScript รู้ว่าเป็น number
const name = 'สมชาย'  // TypeScript รู้ว่าเป็น string

// Array
const tags: string[] = ['work', 'personal', 'urgent']
const scores: number[] = [85, 92, 78]
const flags: boolean[] = [true, false, true]

// Array แบบ Generic Syntax (เทียบเท่ากัน)
const tags2: Array<string> = ['work', 'personal']

// Tuple — Array ที่กำหนดจำนวนและ Type ของแต่ละตำแหน่ง
const coordinate: [number, number] = [13.7563, 100.5018]
const entry: [string, number] = ['คะแนน', 95]

// Union Type — รับได้หลาย Type
let id: string | number = 1
id = 'abc-123'  // ✅ ได้ทั้งสองแบบ

// Literal Type — รับได้เฉพาะค่าที่กำหนด
let status: 'pending' | 'done' | 'cancelled' = 'pending'
status = 'done'       // ✅
status = 'unknown'    // ❌ Error

// Function Type Annotations
function greet(name: string): string {
  return `สวัสดี ${name}!`
}

const add = (a: number, b: number): number => a + b

// Optional Parameter — มีหรือไม่มีก็ได้
function createUser(name: string, age?: number): string {
  return age ? `${name} อายุ ${age}` : name
}

// Default Parameter
function paginate(page: number = 1, limit: number = 10) {
  return { page, limit, offset: (page - 1) * limit }
}

// Return void — ไม่มีค่า Return
function logMessage(msg: string): void {
  console.log(msg)
}

// Return never — ไม่มีทางสิ้นสุด (throw Error เสมอ)
function throwError(message: string): never {
  throw new Error(message)
}
```

---

### Interface และ Type Alias

**Interface — กำหนดโครงสร้าง Object:**

```typescript
// กำหนดโครงสร้างของ Task
interface Task {
  id: number
  title: string
  done: boolean
  priority: 'low' | 'medium' | 'high'
  createdAt: Date
  tags: string[]
  dueDate?: Date        // Optional — มีหรือไม่มีก็ได้
  assignedTo?: string
}

// ใช้ Interface
const task: Task = {
  id: 1,
  title: 'เรียน TypeScript',
  done: false,
  priority: 'high',
  createdAt: new Date(),
  tags: ['learning', 'frontend'],
}

// Interface สำหรับ Function
interface FilterOptions {
  status: 'all' | 'pending' | 'done'
  priority?: 'low' | 'medium' | 'high'
  searchQuery?: string
}

function filterTasks(tasks: Task[], options: FilterOptions): Task[] {
  return tasks.filter(task => {
    if (options.status === 'done' && !task.done) return false
    if (options.status === 'pending' && task.done) return false
    if (options.priority && task.priority !== options.priority) return false
    if (options.searchQuery) {
      const q = options.searchQuery.toLowerCase()
      if (!task.title.toLowerCase().includes(q)) return false
    }
    return true
  })
}

// Extend Interface — สืบทอดและเพิ่มเติม
interface TaskWithUser extends Task {
  userName: string
  userAvatar: string
}

// Interface สำหรับ API Response
interface ApiResponse<T> {
  data: T
  message: string
  success: boolean
  timestamp: string
}

// ใช้งาน
type TaskListResponse = ApiResponse<Task[]>
type SingleTaskResponse = ApiResponse<Task>
```

**Type Alias — ชื่อแทนให้ Type:**

```typescript
// Type Alias สำหรับ Primitive
type TaskId = number
type UserId = string
type Priority = 'low' | 'medium' | 'high'
type Status = 'pending' | 'done' | 'cancelled'

// Type Alias สำหรับ Object (คล้าย Interface)
type Coordinates = {
  lat: number
  lng: number
}

// Type Alias สำหรับ Union ที่ซับซ้อน
type InputEvent = MouseEvent | KeyboardEvent | TouchEvent
type Nullable<T> = T | null
type Optional<T> = T | undefined

// Intersection Type — รวมทุก Property จากหลาย Type
type AdminUser = User & {
  adminLevel: number
  permissions: string[]
}
```

**Interface vs Type — เมื่อไหร่ใช้อะไร:**

```
ใช้ Interface เมื่อ:
- กำหนดโครงสร้าง Object หรือ Class
- ต้องการ Extend หรือ Implement ได้
- เป็น Public API ของ Library

ใช้ Type เมื่อ:
- Union Type: string | number
- Tuple: [string, number]
- Mapped Type หรือ Conditional Type
- ต้องการตั้งชื่อ Primitive หรือ Complex Type
```

---

### Generics เบื้องต้น

**Generics** ทำให้ฟังก์ชันยืดหยุ่นโดยไม่เสียการตรวจสอบ Type:

```typescript
// ปัญหาที่ Generics แก้
function getFirst(arr: any[]): any {  // ❌ ใช้ any — ไม่ปลอดภัย
  return arr[0]
}

const first = getFirst([1, 2, 3])  // first: any — ไม่รู้ว่าเป็น number

// แก้ด้วย Generics
function getFirst<T>(arr: T[]): T {  // ✅ ปลอดภัย
  return arr[0]
}

const num = getFirst([1, 2, 3])          // num: number ✅
const str = getFirst(['a', 'b', 'c'])    // str: string ✅
const task = getFirst(tasks)             // task: Task ✅

// Generics หลายตัว
function pair<K, V>(key: K, value: V): { key: K; value: V } {
  return { key, value }
}

pair('name', 'สมชาย')   // { key: string; value: string }
pair('age', 28)          // { key: string; value: number }
pair(1, true)            // { key: number; value: boolean }

// Generics กับ Interface
interface Repository<T> {
  findById(id: number): Promise<T | null>
  findAll(): Promise<T[]>
  create(data: Omit<T, 'id'>): Promise<T>
  update(id: number, data: Partial<T>): Promise<T>
  delete(id: number): Promise<void>
}

// Implement Repository สำหรับ Task
class TaskRepository implements Repository<Task> {
  async findById(id: number): Promise<Task | null> {
    const tasks = loadFromStorage<Task[]>('tasks', [])
    return tasks.find(t => t.id === id) ?? null
  }

  async findAll(): Promise<Task[]> {
    return loadFromStorage<Task[]>('tasks', [])
  }

  async create(data: Omit<Task, 'id'>): Promise<Task> {
    const tasks = await this.findAll()
    const newTask: Task = { ...data, id: Date.now() }
    saveToStorage('tasks', [...tasks, newTask])
    return newTask
  }

  async update(id: number, data: Partial<Task>): Promise<Task> {
    const tasks = await this.findAll()
    const index = tasks.findIndex(t => t.id === id)
    if (index === -1) throw new Error(`Task ${id} ไม่พบ`)
    tasks[index] = { ...tasks[index], ...data }
    saveToStorage('tasks', tasks)
    return tasks[index]
  }

  async delete(id: number): Promise<void> {
    const tasks = await this.findAll()
    saveToStorage('tasks', tasks.filter(t => t.id !== id))
  }
}
```

**Utility Types ที่ใช้บ่อย:**

```typescript
interface Task {
  id: number
  title: string
  done: boolean
  priority: 'low' | 'medium' | 'high'
  dueDate?: Date
}

// Partial<T> — ทุก Property เป็น Optional
type TaskUpdate = Partial<Task>
// { id?: number; title?: string; done?: boolean; ... }

// Required<T> — ทุก Property เป็น Required
type FullTask = Required<Task>
// { id: number; title: string; done: boolean; dueDate: Date; ... }

// Pick<T, K> — เลือกเฉพาะบาง Property
type TaskPreview = Pick<Task, 'id' | 'title' | 'done'>
// { id: number; title: string; done: boolean }

// Omit<T, K> — ตัดบาง Property ออก
type NewTask = Omit<Task, 'id'>
// { title: string; done: boolean; priority: ...; dueDate?: Date }

// Record<K, V> — Object ที่มี Key และ Value ตาม Type ที่กำหนด
type TasksByPriority = Record<'low' | 'medium' | 'high', Task[]>
const grouped: TasksByPriority = {
  low: [],
  medium: [],
  high: [],
}

// ReturnType<T> — ได้ Type ที่ Function Return
function createTask() { return { id: 1, title: 'test' } }
type CreatedTask = ReturnType<typeof createTask>
// { id: number; title: string }
```

---

## โปรเจกต์ปฏิบัติ

> **สร้างแอป "Task Manager"**
>
> ด้วย Vanilla JavaScript + TypeScript: เพิ่ม/ลบ/แก้ไข Task, กรองตามสถานะ, บันทึกลง `localStorage`, มี Type ครบทุก Function

### โครงสร้างไฟล์

```
task-manager/
├── src/
│   ├── main.ts              ← Entry Point
│   ├── types/
│   │   └── task.ts          ← Type และ Interface ทั้งหมด
│   ├── utils/
│   │   ├── storage.ts       ← localStorage helpers
│   │   └── dom.ts           ← DOM helpers
│   ├── services/
│   │   └── taskService.ts   ← Business Logic ของ Task
│   └── components/
│       ├── taskForm.ts      ← Form เพิ่ม/แก้ไข Task
│       ├── taskList.ts      ← รายการ Task
│       └── filterBar.ts     ← Filter และ Search
├── index.html
├── tsconfig.json
└── package.json
```

### `src/types/task.ts` — Types ทั้งหมด

```typescript
export type Priority = 'low' | 'medium' | 'high'
export type TaskStatus = 'all' | 'pending' | 'done'

export interface Task {
  id: number
  title: string
  description?: string
  done: boolean
  priority: Priority
  createdAt: string  // ISO 8601 string เพราะ Date ไม่ serialize ได้ใน JSON
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
    if (a.done !== b.done) return a.done ? 1 : -1  // ยังไม่เสร็จขึ้นก่อน
    return priorityOrder[a.priority] - priorityOrder[b.priority]  // high ก่อน
  })
}
```

### `src/main.ts` — Entry Point

```typescript
import './style.css'
import type { AppState, Task, FilterState } from './types/task.js'
import { saveToStorage, loadFromStorage } from './utils/storage.js'
import { createTask, toggleTask, deleteTask, filterTasks, sortTasks } from './services/taskService.js'

const STORAGE_KEY = 'task-manager-state'

// State เริ่มต้น
const state: AppState = loadFromStorage(STORAGE_KEY, {
  tasks: [] as Task[],
  filter: { status: 'all', searchQuery: '' } as FilterState,
  editingTaskId: null,
})

// --- Event Handlers ---

function handleAddTask(title: string, priority: string): void {
  if (!title.trim()) return
  const task = createTask(title, priority as Task['priority'])
  state.tasks.push(task)
  persist()
  render()
}

function handleToggle(id: number): void {
  state.tasks = toggleTask(state.tasks, id)
  persist()
  render()
}

function handleDelete(id: number): void {
  if (!confirm('ลบ Task นี้ใช่ไหม?')) return
  state.tasks = deleteTask(state.tasks, id)
  persist()
  render()
}

function handleFilter(updates: Partial<FilterState>): void {
  state.filter = { ...state.filter, ...updates }
  render()
}

// --- Persistence ---
function persist(): void {
  saveToStorage(STORAGE_KEY, state)
}

// --- Render ---
function render(): void {
  const filtered = filterTasks(state.tasks, state.filter)
  const sorted = sortTasks(filtered)
  renderTaskList(sorted)
  renderSummary(state.tasks)
}

function renderTaskList(tasks: Task[]): void {
  const list = document.querySelector<HTMLUListElement>('#task-list')!
  if (tasks.length === 0) {
    list.innerHTML = '<li class="empty">ไม่มี Task ที่ตรงกับเงื่อนไข</li>'
    return
  }
  list.innerHTML = tasks.map(task => `
    <li class="task-item ${task.done ? 'done' : ''}" data-id="${task.id}">
      <button class="toggle-btn">${task.done ? '✅' : '⬜'}</button>
      <span class="task-title">${task.title}</span>
      <span class="badge badge-${task.priority}">${task.priority}</span>
      <button class="delete-btn">🗑️</button>
    </li>
  `).join('')
}

function renderSummary(tasks: Task[]): void {
  const total = tasks.length
  const done = tasks.filter(t => t.done).length
  const summary = document.querySelector('#task-summary')
  if (summary) summary.textContent = `${done}/${total} เสร็จแล้ว`
}

// --- Setup Events ---
function setupEvents(): void {
  // Form ส่ง Task ใหม่
  document.querySelector<HTMLFormElement>('#task-form')!
    .addEventListener('submit', e => {
      e.preventDefault()
      const form = e.target as HTMLFormElement
      const title = (form.elements.namedItem('title') as HTMLInputElement).value
      const priority = (form.elements.namedItem('priority') as HTMLSelectElement).value
      handleAddTask(title, priority)
      form.reset()
    })

  // Event Delegation สำหรับ List
  document.querySelector('#task-list')!
    .addEventListener('click', e => {
      const target = e.target as HTMLElement
      const item = target.closest<HTMLElement>('.task-item')
      if (!item) return
      const id = Number(item.dataset.id)

      if (target.matches('.toggle-btn')) handleToggle(id)
      if (target.matches('.delete-btn')) handleDelete(id)
    })

  // Filter
  document.querySelector('#filter-status')!
    .addEventListener('change', e => {
      handleFilter({ status: (e.target as HTMLSelectElement).value as FilterState['status'] })
    })

  // Search
  document.querySelector<HTMLInputElement>('#search-input')!
    .addEventListener('input', e => {
      handleFilter({ searchQuery: (e.target as HTMLInputElement).value })
    })
}

// --- Init ---
setupEvents()
render()
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
✅ ผ่าน ESLint โดยไม่มี Error
✅ ผ่าน TypeScript Compiler โดยไม่มี Error
✅ Push ขึ้น GitHub พร้อม README

Bonus:
⭐ แก้ไขชื่อ Task ได้ (Inline Edit)
⭐ จัด Sort ตาม Priority อัตโนมัติ
⭐ แสดงสรุป x/y เสร็จแล้ว
⭐ Dark Mode
```

---

## สรุปหน่วยที่ 3

```
สิ่งที่เรียนรู้ในหน่วยนี้
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ const/let เท่านั้น, ห้ามใช้ var
   Array Methods: map, filter, reduce, find ทำงานโดยไม่แก้ Array เดิม

✅ Destructuring และ Spread (...) ทำให้โค้ดกระชับและ Immutable
   Optional Chaining (?.) และ Nullish Coalescing (??) ป้องกัน Bug

✅ async/await ทำให้เขียน Async Code อ่านเหมือน Sync
   try/catch/finally จัดการ Error อย่างถูกต้อง

✅ fetch() ส่ง HTTP Request ครบทุก Method: GET POST PUT PATCH DELETE
   ต้องตรวจสอบ response.ok ก่อน parse JSON เสมอ

✅ Event Delegation ดีกว่าเพิ่ม Listener ทุก Element
   ป้องกัน XSS ด้วย escapeHTML ก่อนแสดงข้อความจากผู้ใช้

✅ ES Modules แบ่งโค้ดเป็นไฟล์ตามหน้าที่
   ESLint + Prettier มาตรฐานโค้ดที่ทีมทุกที่ใช้

✅ TypeScript เพิ่ม Type Safety ทำให้ Bug หายก่อน Runtime
   Interface กำหนดโครงสร้าง, Generics ทำให้ Function ยืดหยุ่น
   Utility Types: Partial, Pick, Omit, Record ใช้บ่อยมากในทีม
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
