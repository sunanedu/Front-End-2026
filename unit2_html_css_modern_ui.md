# หน่วยที่ 2: HTML, CSS และการออกแบบ UI สมัยใหม่

> **หลักสูตร:** หลักการพัฒนาซอฟต์แวร์ด้วยเทคโนโลยี Front-End
> **ระดับ:** ผู้เริ่มต้น (Beginner)
> **ระยะเวลา:** 3 สัปดาห์
> **ปรับปรุงล่าสุด:** พฤษภาคม 2026

---

## วัตถุประสงค์การเรียนรู้

เมื่อเรียนจบหน่วยนี้ ผู้เรียนจะสามารถ:

1. เขียน HTML ที่มีโครงสร้าง Semantic ถูกต้องและเข้าถึงได้ (Accessible)
2. ใช้ CSS สมัยใหม่ทั้ง Flexbox, Grid, Custom Properties และ Container Queries ได้
3. สร้างหน้าเว็บ Responsive ด้วย Mobile-First approach ได้
4. ติดตั้งและใช้ Tailwind CSS v4 ในโปรเจกต์จริงได้
5. ใช้ shadcn/ui Component Library ปรับแต่งให้เข้ากับ Design ของโปรเจกต์ได้
6. อ่าน Design จาก Figma และแปลงเป็น Code ได้อย่างถูกต้อง

---

## 2.1 HTML5 Semantic และ Accessibility

### HTML คืออะไร

**HTML** (เอชทีเอ็มแอล — HyperText Markup Language) คือภาษาที่ใช้กำหนดโครงสร้างและความหมายของเนื้อหาในหน้าเว็บ เปรียบเหมือน "โครงกระดูก" ที่ทำให้เบราว์เซอร์รู้ว่าแต่ละส่วนคืออะไร

HTML ทำงานด้วย **Tag** (แท็ก) ซึ่งเป็นคำสั่งที่ครอบเนื้อหา:

```html
<p>นี่คือย่อหน้า (paragraph)</p>
<h1>นี่คือหัวข้อใหญ่สุด</h1>
<a href="https://example.com">นี่คือลิงก์</a>
```

---

### Semantic HTML: เขียน HTML ให้มีความหมาย

**Semantic HTML** (ซีแมนติก เอชทีเอ็มแอล) หมายถึงการเลือกใช้ Tag ที่บอก "ความหมาย" ของเนื้อหา ไม่ใช่แค่รูปร่างที่แสดงผล

**เปรียบเทียบให้เห็นชัด**

```html
<!-- ❌ ไม่ใช่ Semantic — ใช้ div ทุกอย่าง เบราว์เซอร์ไม่รู้ว่าแต่ละส่วนคืออะไร -->
<div id="header">
  <div id="nav">...</div>
</div>
<div id="content">
  <div class="article">...</div>
  <div class="sidebar">...</div>
</div>
<div id="footer">...</div>

<!-- ✅ Semantic — เบราว์เซอร์ และ Search Engine รู้ทันทีว่าแต่ละส่วนคืออะไร -->
<header>
  <nav>...</nav>
</header>
<main>
  <article>...</article>
  <aside>...</aside>
</main>
<footer>...</footer>
```

---

### Tag Semantic ที่ต้องรู้จัก

```
┌─────────────────────────────────────────────────────────────┐
│  <header>                                                   │
│    ส่วนหัว: โลโก้, ชื่อเว็บ, Navigation หลัก                       │
│  ─────────────────────────────────────────────────────────  │
│  <nav>                                                      │
│    เมนู Navigation: ลิงก์ไปยังส่วนต่าง ๆ ของเว็บ                    │
│  ─────────────────────────────────────────────────────────  │
│  <main>                                                     │
│    เนื้อหาหลักของหน้า — มีได้แค่ 1 <main> ต่อหน้า                  │
│    ┌───────────────────┐  ┌──────────────────────────────┐  │
│    │  <article>        │  │  <aside>                     │  │
│    │  เนื้อหาที่ยืน        │  │  เนื้อหาเสริม: Widget,           │  │
│    │  ได้ด้วยตัวเอง       │  │  โฆษณา, บทความที่เกี่ยวข้อง      │  │
│    │  เช่น บทความ      │  └──────────────────────────────┘  │
│    │  โพสต์บล็อก        │                                    │
│    └───────────────────┘                                    │
│    ┌───────────────────────────────────────────────────┐    │
│    │  <section>                                        │    │
│    │  กลุ่มเนื้อหาที่มีธีมเดียวกัน มักมี Heading                  │    │
│    └───────────────────────────────────────────────────┘    │
│  ─────────────────────────────────────────────────────────  │
│  <footer>                                                   │
│    ส่วนท้าย: ลิขสิทธิ์, ลิงก์, ข้อมูลติดต่อ                           │
└─────────────────────────────────────────────────────────────┘
```

**ตัวอย่างโครงสร้าง HTML ที่ถูกต้อง**

```html
<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ชื่อเว็บไซต์ | คำอธิบายสั้น</title>
  <meta name="description" content="คำอธิบายหน้าสำหรับ Search Engine">
</head>
<body>

  <header>
    <a href="/" aria-label="กลับหน้าแรก">
      <img src="/logo.svg" alt="โลโก้บริษัท" width="120" height="40">
    </a>
    <nav aria-label="เมนูหลัก">
      <ul>
        <li><a href="/products">สินค้า</a></li>
        <li><a href="/about">เกี่ยวกับเรา</a></li>
        <li><a href="/contact">ติดต่อ</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <section aria-labelledby="hero-heading">
      <h1 id="hero-heading">ยินดีต้อนรับสู่ร้านของเรา</h1>
      <p>เราจำหน่ายสินค้าคุณภาพสูงในราคาที่เข้าถึงได้</p>
      <a href="/products" class="btn-primary">ดูสินค้าทั้งหมด</a>
    </section>

    <section aria-labelledby="featured-heading">
      <h2 id="featured-heading">สินค้าแนะนำ</h2>
      <ul>
        <li>
          <article>
            <img src="/product-1.jpg" alt="ชื่อสินค้า 1">
            <h3>ชื่อสินค้า 1</h3>
            <p>฿ 990</p>
          </article>
        </li>
      </ul>
    </section>
  </main>

  <footer>
    <p>&copy; 2026 บริษัทตัวอย่าง. สงวนลิขสิทธิ์.</p>
  </footer>

</body>
</html>
```

---

### ทำไม Semantic HTML ถึงสำคัญ

**1. SEO (Search Engine Optimization)**

Search Engine อย่าง Google อ่าน HTML และให้ความสำคัญกับโครงสร้างที่ถูกต้อง `<h1>` บอกว่านี่คือหัวข้อหลักของหน้า `<article>` บอกว่าเนื้อหานี้สำคัญ — เว็บที่เขียน Semantic ดีจะอยู่ในอันดับที่สูงกว่า

**2. Accessibility (การเข้าถึง)**

**Accessibility** (แอกเซสซิบิลิตี — a11y) คือการทำให้เว็บใช้งานได้โดยทุกคน รวมถึงผู้ที่ใช้ **Screen Reader** (โปรแกรมอ่านหน้าจอสำหรับผู้พิการทางสายตา) Screen Reader อ่านโครงสร้าง HTML เพื่อบอกผู้ใช้ว่ากำลังอยู่ในส่วนไหนของหน้า

**3. Maintainability**

โค้ดที่ Semantic อ่านง่าย บำรุงรักษาง่าย ทีมสามารถเข้าใจโครงสร้างได้ทันทีโดยไม่ต้องอ่าน Class หรือ ID

---

### ARIA Attributes เบื้องต้น

**ARIA** (แอเรีย — Accessible Rich Internet Applications) คือ Attribute พิเศษที่เพิ่มความหมายให้กับ Element สำหรับ Assistive Technology

```html
<!-- aria-label: ให้ชื่อที่อธิบายชัดขึ้น เมื่อข้อความที่มองเห็นไม่เพียงพอ -->
<button aria-label="ปิดหน้าต่างนี้">✕</button>

<!-- aria-labelledby: ชี้ไปยัง Element อื่นที่เป็น Label -->
<section aria-labelledby="section-title">
  <h2 id="section-title">สินค้ายอดนิยม</h2>
  ...
</section>

<!-- role: บอกบทบาทของ Element -->
<div role="alert">เกิดข้อผิดพลาด กรุณาลองใหม่อีกครั้ง</div>

<!-- aria-expanded: บอกสถานะ เปิด/ปิด ของ Dropdown หรือ Accordion -->
<button aria-expanded="false" aria-controls="menu">เมนู</button>
<ul id="menu" hidden>...</ul>

<!-- aria-hidden: ซ่อนจาก Screen Reader (สำหรับ Element ที่เป็นแค่ Visual) -->
<span aria-hidden="true">🎉</span>
<span>ยินดีด้วย!</span>
```

> **💡 หลักการ:** ใช้ Semantic HTML Tag ก่อนเสมอ ใช้ ARIA เฉพาะเมื่อไม่มี Tag ที่เหมาะสม — `<button>` ดีกว่า `<div role="button">` เสมอ

---

### HTML Forms ที่ถูกต้อง

Form เป็นส่วนที่ผู้ใช้โต้ตอบมากที่สุด การเขียน Form ที่ถูกต้องส่งผลต่อทั้ง Accessibility และ UX

```html
<form action="/submit" method="POST" novalidate>

  <!-- fieldset จัดกลุ่ม Input ที่เกี่ยวข้อง -->
  <fieldset>
    <legend>ข้อมูลส่วนตัว</legend>

    <!-- label ต้องเชื่อมกับ input เสมอ ผ่าน for/id -->
    <div class="form-group">
      <label for="full-name">ชื่อ-นามสกุล <span aria-label="จำเป็น">*</span></label>
      <input
        type="text"
        id="full-name"
        name="fullName"
        autocomplete="name"
        required
        aria-describedby="name-hint"
      >
      <span id="name-hint" class="hint">กรอกชื่อจริงตามบัตรประชาชน</span>
    </div>

    <div class="form-group">
      <label for="email">อีเมล <span aria-label="จำเป็น">*</span></label>
      <input
        type="email"
        id="email"
        name="email"
        autocomplete="email"
        required
        aria-describedby="email-error"
      >
      <span id="email-error" role="alert" class="error" hidden>
        รูปแบบอีเมลไม่ถูกต้อง
      </span>
    </div>

    <div class="form-group">
      <label for="message">ข้อความ</label>
      <textarea
        id="message"
        name="message"
        rows="4"
        maxlength="500"
      ></textarea>
    </div>

  </fieldset>

  <button type="submit">ส่งข้อมูล</button>

</form>
```

**กฎสำคัญของ Form**

| กฎ | เหตุผล |
|----|--------|
| ทุก `<input>` ต้องมี `<label>` ที่เชื่อมกัน | Screen Reader อ่านได้ถูกต้อง |
| ใช้ `type` ที่เหมาะสม (`email`, `tel`, `number`) | Mobile แสดง Keyboard ที่ถูกต้อง |
| ใช้ `autocomplete` ที่เหมาะสม | ผู้ใช้กรอกเร็วขึ้น ลด Friction |
| ใช้ `required` บน Field ที่จำเป็น | Browser Validation ขั้นพื้นฐาน |
| แสดง Error ด้วย `role="alert"` | Screen Reader แจ้งผู้ใช้ทันที |

---

## 2.2 CSS สมัยใหม่

### CSS คืออะไร

**CSS** (ซีเอสเอส — Cascading Style Sheets) คือภาษาที่ใช้กำหนดรูปลักษณ์และ Layout ของหน้าเว็บ ถ้า HTML คือโครงกระดูก CSS คือเสื้อผ้าและรูปร่างภายนอก

CSS ทำงานด้วย **Selector** และ **Property**

```css
/* selector { property: value; } */

h1 {
  color: #1a1a2e;       /* สีข้อความ */
  font-size: 2rem;      /* ขนาดตัวอักษร */
  margin-bottom: 1rem;  /* ระยะห่างด้านล่าง */
}

.card {
  background-color: white;
  border-radius: 8px;
  padding: 1.5rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}
```

---

### CSS Custom Properties (CSS Variables)

**CSS Custom Properties** หรือที่เรียกว่า CSS Variables คือตัวแปรที่เก็บค่าซ้ำ ๆ เช่น สี, spacing ไว้ใน ที่เดียว เมื่อต้องการเปลี่ยน Design ทั้งเว็บ แก้แค่จุดเดียว

```css
/* กำหนด Custom Properties บน :root (ทั้งหน้า) */
:root {
  /* Colors */
  --color-primary: #6366f1;
  --color-primary-dark: #4f46e5;
  --color-secondary: #f59e0b;
  --color-text: #1f2937;
  --color-text-muted: #6b7280;
  --color-background: #ffffff;
  --color-surface: #f9fafb;
  --color-border: #e5e7eb;

  /* Typography */
  --font-sans: 'Inter', system-ui, sans-serif;
  --font-size-sm: 0.875rem;   /* 14px */
  --font-size-base: 1rem;     /* 16px */
  --font-size-lg: 1.125rem;   /* 18px */
  --font-size-xl: 1.25rem;    /* 20px */
  --font-size-2xl: 1.5rem;    /* 24px */
  --font-size-3xl: 1.875rem;  /* 30px */

  /* Spacing */
  --spacing-xs: 0.25rem;   /* 4px */
  --spacing-sm: 0.5rem;    /* 8px */
  --spacing-md: 1rem;      /* 16px */
  --spacing-lg: 1.5rem;    /* 24px */
  --spacing-xl: 2rem;      /* 32px */
  --spacing-2xl: 3rem;     /* 48px */

  /* Border Radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.1);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
}

/* ใช้งาน */
.button-primary {
  background-color: var(--color-primary);
  color: white;
  padding: var(--spacing-sm) var(--spacing-lg);
  border-radius: var(--radius-md);
  font-family: var(--font-sans);
}

.button-primary:hover {
  background-color: var(--color-primary-dark);
}
```

**Dark Mode ด้วย Custom Properties**

```css
/* Light Mode (default) */
:root {
  --color-background: #ffffff;
  --color-text: #1f2937;
  --color-surface: #f9fafb;
}

/* Dark Mode */
@media (prefers-color-scheme: dark) {
  :root {
    --color-background: #111827;
    --color-text: #f9fafb;
    --color-surface: #1f2937;
  }
}

/* เปลี่ยนแค่ตรงนี้ ทั้งหน้าปรับตามทันที */
body {
  background-color: var(--color-background);
  color: var(--color-text);
}
```

---

### Flexbox: Layout แบบแถว/คอลัมน์

**Flexbox** (เฟล็กซ์บ็อกซ์) คือระบบ Layout ที่จัดวาง Element เป็นแถวหรือคอลัมน์ เหมาะสำหรับ Component ขนาดเล็กถึงกลาง เช่น Navbar, Card Content, Button Group

```css
/* Container หลัก */
.flex-container {
  display: flex;

  /* ทิศทาง */
  flex-direction: row;          /* แถว (default) */
  /* flex-direction: column;   คอลัมน์ */

  /* การจัดวางแนวนอน (Main Axis) */
  justify-content: flex-start;  /* ชิดซ้าย (default) */
  justify-content: center;      /* กึ่งกลาง */
  justify-content: flex-end;    /* ชิดขวา */
  justify-content: space-between; /* กระจายขอบ */
  justify-content: space-around;  /* กระจายมีระยะรอบ */

  /* การจัดวางแนวตั้ง (Cross Axis) */
  align-items: stretch;         /* ยืดเต็มความสูง (default) */
  align-items: center;          /* กึ่งกลางแนวตั้ง */
  align-items: flex-start;      /* ชิดบน */
  align-items: flex-end;        /* ชิดล่าง */

  /* ตัดบรรทัดเมื่อเต็ม */
  flex-wrap: nowrap;   /* ไม่ตัด (default) */
  flex-wrap: wrap;     /* ตัดบรรทัดอัตโนมัติ */

  /* ระยะห่างระหว่าง Item */
  gap: 1rem;
}

/* Item ภายใน Container */
.flex-item {
  flex: 1;           /* แบ่งพื้นที่เท่า ๆ กัน */
  flex: 0 0 200px;   /* ขนาดคงที่ 200px */
  flex-grow: 1;      /* ขยายเต็มพื้นที่ที่เหลือ */
}
```

**ตัวอย่าง Navbar ด้วย Flexbox**

```css
.navbar {
  display: flex;
  justify-content: space-between;  /* โลโก้ซ้าย, เมนูขวา */
  align-items: center;              /* กึ่งกลางแนวตั้ง */
  padding: var(--spacing-md) var(--spacing-xl);
  background-color: var(--color-background);
  border-bottom: 1px solid var(--color-border);
}

.navbar__logo {
  flex-shrink: 0;  /* ไม่ให้โลโก้หด */
}

.navbar__menu {
  display: flex;
  gap: var(--spacing-lg);
  list-style: none;
}
```

---

### CSS Grid: Layout แบบตาราง

**CSS Grid** คือระบบ Layout แบบ 2 มิติ (แถวและคอลัมน์พร้อมกัน) เหมาะสำหรับ Layout ขนาดใหญ่ เช่น หน้าเว็บทั้งหน้า, Product Grid, Dashboard

```css
/* Grid Container */
.grid-container {
  display: grid;

  /* กำหนดคอลัมน์ */
  grid-template-columns: 1fr 1fr 1fr;          /* 3 คอลัมน์เท่ากัน */
  grid-template-columns: repeat(3, 1fr);        /* เหมือนบรรทัดบน */
  grid-template-columns: 200px 1fr 200px;       /* ซ้ายขวาคงที่, กลางยืดหยุ่น */
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); /* Auto-responsive */

  /* กำหนดแถว */
  grid-template-rows: auto;           /* ความสูงอัตโนมัติ */
  grid-template-rows: 80px 1fr 60px;  /* Header, Content, Footer */

  /* ระยะห่าง */
  gap: 1.5rem;             /* ระหว่างทั้งแถวและคอลัมน์ */
  column-gap: 2rem;        /* ระหว่างคอลัมน์ */
  row-gap: 1rem;           /* ระหว่างแถว */
}

/* Grid Item */
.grid-item {
  grid-column: span 2;    /* ครอบคลุม 2 คอลัมน์ */
  grid-row: span 2;       /* ครอบคลุม 2 แถว */
}
```

**ตัวอย่าง Page Layout ด้วย Grid**

```css
/* Layout 3 ส่วน: Header, Sidebar + Content, Footer */
.page-layout {
  display: grid;
  grid-template-areas:
    "header  header"
    "sidebar content"
    "footer  footer";
  grid-template-columns: 260px 1fr;
  grid-template-rows: 64px 1fr 60px;
  min-height: 100vh;
}

.page-header  { grid-area: header; }
.page-sidebar { grid-area: sidebar; }
.page-content { grid-area: content; }
.page-footer  { grid-area: footer; }
```

**ตัวอย่าง Product Grid ที่ Responsive อัตโนมัติ**

```css
.product-grid {
  display: grid;
  /* auto-fill: สร้างคอลัมน์ให้มากที่สุดเท่าที่พื้นที่อนุญาต */
  /* minmax(280px, 1fr): แต่ละคอลัมน์กว้างอย่างน้อย 280px */
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: var(--spacing-lg);
}
```

---

### เมื่อไหร่ควรใช้ Flexbox หรือ Grid

```
ใช้ Flexbox เมื่อ:
├── จัดวาง Element ใน 1 แกน (แถวหรือคอลัมน์)
├── จัด Alignment ภายใน Component เล็ก ๆ
├── Navbar, Button Group, Card Header
└── เนื้อหาที่ขนาดไม่แน่นอน

ใช้ Grid เมื่อ:
├── จัด Layout ที่ต้องควบคุมทั้งแถวและคอลัมน์พร้อมกัน
├── Page Layout หลัก (Header + Sidebar + Content + Footer)
├── Product Grid, Image Gallery, Dashboard Cards
└── ต้องการ Layout ที่ซับซ้อนและแม่นยำ
```

> **💡 ในทางปฏิบัติ:** ใช้ทั้งสองร่วมกัน — Grid สำหรับ Layout ภาพรวม Flexbox สำหรับ Component ย่อยภายใน

---

### CSS Nesting

ตั้งแต่ปี 2024 เบราว์เซอร์รองรับ **CSS Nesting** โดยตรง ทำให้เขียน CSS ซ้อนกันได้โดยไม่ต้องใช้ SASS หรือ Preprocessor อื่น

```css
/* แบบเก่า — ต้องเขียนซ้ำชื่อ Class */
.card { background: white; border-radius: 8px; }
.card .card__title { font-size: 1.25rem; font-weight: 600; }
.card .card__body { padding: 1rem; }
.card:hover { box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
.card .button { background: blue; }
.card .button:hover { background: darkblue; }

/* แบบใหม่ — CSS Nesting */
.card {
  background: white;
  border-radius: 8px;

  &:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  }

  .card__title {
    font-size: 1.25rem;
    font-weight: 600;
  }

  .card__body {
    padding: 1rem;
  }

  .button {
    background: blue;

    &:hover {
      background: darkblue;
    }
  }
}
```

---

### Container Queries: Responsive ตาม Container

**Media Queries** เดิมทำให้ Responsive ตาม Viewport (ขนาดหน้าจอ) แต่ปัญหาคือ Component เดียวกันอาจอยู่ในพื้นที่แคบหรือกว้างขึ้นอยู่กับตำแหน่ง

**Container Queries** แก้ปัญหานี้ — ทำให้ Component Responsive ตามขนาดของ Container ที่ครอบอยู่

```css
/* กำหนดให้ .card-container เป็น Container ที่ลูกสามารถ Query ได้ */
.card-container {
  container-type: inline-size;
  container-name: card;  /* ตั้งชื่อเพื่อ Query เจาะจง */
}

/* Card จะปรับ Layout ตามขนาดของ .card-container */
.card {
  display: flex;
  flex-direction: column;  /* มือถือ: Stack แนวตั้ง */
  gap: 1rem;
}

/* เมื่อ Container กว้างกว่า 480px */
@container card (min-width: 480px) {
  .card {
    flex-direction: row;  /* จอใหญ่: เรียงแนวนอน */
  }

  .card__image {
    width: 200px;
    flex-shrink: 0;
  }
}
```

---

### Mobile-First Responsive Design

**Mobile-First** คือแนวทางที่เขียน CSS สำหรับมือถือก่อน แล้วค่อย Override สำหรับจอใหญ่ขึ้น ด้วย `min-width` Media Query

```css
/* เหตุผลที่ใช้ Mobile-First:
   1. ผู้ใช้งานเว็บ 60%+ มาจากมือถือ
   2. Code ที่ได้ กระชับและมีประสิทธิภาพกว่า
   3. เริ่มจาก Constraint ก่อน ขยายทีหลัง — ดีกว่าเริ่มกว้างแล้วหด */

/* === Base Styles (Mobile) === */
.hero {
  padding: var(--spacing-xl) var(--spacing-md);
  text-align: center;
}

.hero__title {
  font-size: var(--font-size-2xl);  /* เล็กกว่าสำหรับมือถือ */
}

.hero__layout {
  display: flex;
  flex-direction: column;           /* Stack แนวตั้ง */
  gap: var(--spacing-lg);
}

/* === Tablet (≥ 768px) === */
@media (min-width: 768px) {
  .hero {
    padding: var(--spacing-2xl) var(--spacing-xl);
    text-align: left;
  }

  .hero__title {
    font-size: var(--font-size-3xl);
  }
}

/* === Desktop (≥ 1024px) === */
@media (min-width: 1024px) {
  .hero__layout {
    flex-direction: row;             /* เรียงแนวนอน */
    align-items: center;
  }

  .hero__title {
    font-size: 3.5rem;
  }
}

/* === Large Desktop (≥ 1280px) === */
@media (min-width: 1280px) {
  .container {
    max-width: 1200px;
    margin-inline: auto;
  }
}
```

**Breakpoint มาตรฐานที่ใช้**

| ชื่อ | ขนาด | อุปกรณ์ |
|------|------|---------|
| xs (default) | < 480px | มือถือเล็ก |
| sm | ≥ 480px | มือถือใหญ่ |
| md | ≥ 768px | Tablet |
| lg | ≥ 1024px | Laptop |
| xl | ≥ 1280px | Desktop |
| 2xl | ≥ 1536px | จอใหญ่ |

---

### CSS Transitions และ Animations

**Transitions** ทำให้การเปลี่ยนแปลง Property เกิดขึ้นอย่างนุ่มนวล

```css
.button {
  background-color: var(--color-primary);
  color: white;
  padding: 0.75rem 1.5rem;
  border-radius: var(--radius-md);
  border: none;
  cursor: pointer;

  /* transition: property duration easing delay */
  transition: background-color 200ms ease, transform 150ms ease, box-shadow 200ms ease;
}

.button:hover {
  background-color: var(--color-primary-dark);
  transform: translateY(-2px);           /* ยกขึ้นเล็กน้อย */
  box-shadow: var(--shadow-md);
}

.button:active {
  transform: translateY(0);             /* กดลง */
  box-shadow: var(--shadow-sm);
}
```

**Animations ด้วย @keyframes**

```css
/* กำหนด Animation */
@keyframes fade-in {
  from { opacity: 0; transform: translateY(16px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50%       { opacity: 0.5; }
}

/* ใช้งาน */
.card {
  /* animation: name duration easing delay iteration direction */
  animation: fade-in 300ms ease both;
}

.loading-spinner {
  animation: spin 1s linear infinite;
}

.skeleton {
  animation: pulse 2s ease-in-out infinite;
}

/* Respect ผู้ใช้ที่ตั้งค่า Reduce Motion */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 2.3 Tailwind CSS v4

### Tailwind CSS คืออะไร

**Tailwind CSS** (เทลวินด์ ซีเอสเอส) คือ CSS Framework แบบ **Utility-First** — แทนที่จะเขียน CSS ด้วยตัวเอง คุณใช้ Class ที่สำเร็จรูปมาประกอบในไฟล์ HTML โดยตรง

```html
<!-- แบบเขียน CSS เอง -->
<style>
.button {
  background-color: #6366f1;
  color: white;
  padding: 0.75rem 1.5rem;
  border-radius: 8px;
  font-weight: 600;
  transition: background-color 200ms;
}
.button:hover {
  background-color: #4f46e5;
}
</style>
<button class="button">คลิกฉัน</button>

<!-- แบบ Tailwind CSS -->
<button class="bg-indigo-500 text-white px-6 py-3 rounded-lg font-semibold hover:bg-indigo-600 transition-colors">
  คลิกฉัน
</button>
```

**ทำไม Tailwind CSS ถึงได้รับความนิยมสูง**

- **เร็ว:** ไม่ต้องคิดชื่อ Class ไม่ต้องสลับไฟล์ CSS
- **สม่ำเสมอ:** ทุก Spacing, สี, ขนาดมาจาก Scale เดียวกัน
- **ขนาดไฟล์เล็ก:** Tailwind ลบ Class ที่ไม่ใช้ออกอัตโนมัติ (Purge)
- **Responsive ง่าย:** `md:flex lg:grid` — เพิ่ม Breakpoint Prefix ได้เลย
- **เป็นมาตรฐาน:** ทีม Frontend ส่วนใหญ่ใช้ในปัจจุบัน

---

### ติดตั้ง Tailwind CSS v4 กับ Vite

Tailwind CSS v4 เปลี่ยนวิธีการตั้งค่าใหม่ — ง่ายขึ้นมาก ไม่ต้องใช้ไฟล์ `tailwind.config.js` แยก

```bash
# สร้างโปรเจกต์ด้วย Vite (ถ้ายังไม่มี)
pnpm create vite@latest my-app --template vanilla
cd my-app

# ติดตั้ง Tailwind CSS v4
pnpm add -D tailwindcss @tailwindcss/vite
```

**แก้ไข `vite.config.js`**

```javascript
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [
    tailwindcss(),
  ],
})
```

**สร้างไฟล์ CSS หลัก (`src/style.css`)**

```css
@import "tailwindcss";

/* Custom CSS ของคุณเพิ่มได้ที่นี่ */
```

**Import ใน `src/main.js`**

```javascript
import './style.css'
```

---

### Class ที่ใช้บ่อย — คู่มืออ้างอิง

**Layout**

```html
<!-- Flexbox -->
<div class="flex items-center justify-between gap-4">

<!-- Grid -->
<div class="grid grid-cols-3 gap-6">
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">

<!-- Container -->
<div class="container mx-auto px-4">
```

**Spacing (Padding & Margin)**

```html
<!-- p = padding, m = margin -->
<!-- t=top, r=right, b=bottom, l=left, x=horizontal, y=vertical -->
<div class="p-4">          <!-- padding: 1rem (16px) ทุกด้าน -->
<div class="px-6 py-3">   <!-- padding: 0 1.5rem / 0.75rem -->
<div class="mt-4 mb-8">   <!-- margin-top: 1rem, margin-bottom: 2rem -->
<div class="mx-auto">     <!-- margin: 0 auto (จัดกลาง) -->
```

**Typography**

```html
<h1 class="text-3xl font-bold text-gray-900">หัวข้อใหญ่</h1>
<p class="text-base text-gray-600 leading-relaxed">ข้อความปกติ</p>
<span class="text-sm font-medium text-indigo-600">ข้อความเล็กสี</span>
```

**Colors**

```html
<!-- รูปแบบ: {property}-{color}-{shade} -->
<div class="bg-white text-gray-900">         <!-- พื้นหลังขาว ข้อความดำ -->
<div class="bg-indigo-500 text-white">       <!-- พื้นหลังม่วง ข้อความขาว -->
<div class="bg-gray-100 border border-gray-200"> <!-- Surface พร้อม Border -->
```

**Border & Radius**

```html
<div class="border border-gray-200 rounded-lg">          <!-- border + border-radius -->
<div class="border-2 border-indigo-500 rounded-full">   <!-- border หนา + วงกลม -->
```

**Shadows**

```html
<div class="shadow-sm">   <!-- เงาเล็กน้อย -->
<div class="shadow-md">   <!-- เงาปานกลาง -->
<div class="shadow-lg">   <!-- เงาชัด -->
<div class="shadow-xl">   <!-- เงาใหญ่ -->
```

**Responsive Breakpoints**

```html
<!-- prefix: md: lg: xl: 2xl: -->
<!-- Mobile-First: ใส่ prefix เมื่อต้องการ Override ที่ขนาดใหญ่ขึ้น -->

<div class="flex-col md:flex-row">         <!-- มือถือ: Stack, Tablet+: แถว -->
<div class="text-lg md:text-xl lg:text-2xl"> <!-- ขนาดตัวอักษรเพิ่มตามจอ -->
<div class="hidden md:block">               <!-- ซ่อนในมือถือ แสดงใน Tablet+ -->
<div class="block md:hidden">               <!-- แสดงในมือถือ ซ่อนใน Tablet+ -->
<div class="grid-cols-1 md:grid-cols-2 lg:grid-cols-3"> <!-- Grid Responsive -->
```

**State Variants**

```html
<!-- hover:, focus:, active:, disabled: -->
<button class="bg-indigo-500 hover:bg-indigo-600 active:bg-indigo-700
               focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2
               disabled:opacity-50 disabled:cursor-not-allowed
               transition-colors">
  ปุ่ม
</button>
```

---

### Dark Mode ด้วย Tailwind

```html
<!-- เพิ่ม dark: prefix — ทำงานเมื่อ OS หรือ HTML มี class "dark" -->
<div class="bg-white dark:bg-gray-900 text-gray-900 dark:text-gray-100">
  <h1 class="text-2xl font-bold text-indigo-600 dark:text-indigo-400">
    หัวข้อ
  </h1>
  <p class="text-gray-600 dark:text-gray-400">ข้อความ</p>
</div>
```

```css
/* ใน style.css — ตั้งค่า dark mode strategy */
@import "tailwindcss";

@variant dark (&:where(.dark, .dark *));
```

---

### จัดการ Custom Design System ใน Tailwind v4

ใน Tailwind v4 กำหนดค่าใน CSS โดยตรง ไม่ต้องใช้ไฟล์ Config แยก

```css
@import "tailwindcss";

/* กำหนด Custom Colors, Fonts, Spacing */
@theme {
  --color-brand-50: #eef2ff;
  --color-brand-100: #e0e7ff;
  --color-brand-500: #6366f1;
  --color-brand-600: #4f46e5;
  --color-brand-900: #312e81;

  --font-display: 'Prompt', sans-serif;
  --font-body: 'Sarabun', sans-serif;

  --spacing-18: 4.5rem;
  --spacing-88: 22rem;

  --radius-xl: 1rem;
  --radius-2xl: 1.5rem;
}
```

จากนั้นใช้ Class ที่สร้างขึ้นมาได้ทันที

```html
<div class="bg-brand-500 font-display text-white rounded-2xl p-18">
  Custom Design System
</div>
```

---

## 2.4 shadcn/ui — UI Component Library

### แนวคิด Headless Component

ปัญหาของ Component Library แบบเดิม (Bootstrap, Material UI) คือ Style ถูก Lock ไว้ ปรับแต่งยาก และต้อง Override CSS จำนวนมาก

**shadcn/ui** (แชดซีเอ็น ยูไอ) แก้ปัญหานี้ด้วยแนวคิด **"Copy เข้าโปรเจกต์"** — แทนที่จะ Install Package แล้วใช้ คุณ Copy Source Code ของ Component ลงในโปรเจกต์โดยตรง ทำให้:

- ปรับแต่งได้ 100% เพราะ Code อยู่ในมือคุณ
- ไม่มี Black Box — อ่าน Code ทำความเข้าใจได้เสมอ
- ใช้ Tailwind CSS + Radix UI (Headless Primitives ที่ Accessible)

```
Radix UI
(Logic + Accessibility ครบ แต่ไม่มี Style)
        +
Tailwind CSS
(Styling ที่ยืดหยุ่น)
        =
shadcn/ui
(Component สวย, Accessible, ปรับแต่งได้)
```

---

### ติดตั้ง shadcn/ui กับ Next.js

```bash
# สร้างโปรเจกต์ Next.js ใหม่
pnpm create next-app@latest my-app --typescript --tailwind --eslint --app
cd my-app

# ติดตั้ง shadcn/ui (จะถามคำถามการตั้งค่า)
pnpx shadcn@latest init
```

**เมนูตั้งค่า shadcn/ui**

```
Which style would you like to use? › Default
Which color would you like to use as base color? › Slate
Where is your global CSS file? › app/globals.css
Do you want to use CSS variables for colors? › yes
Where is your tailwind.config located? › tailwind.config.ts
Configure the import alias for components: › @/components
Configure the import alias for utils: › @/lib/utils
```

**เพิ่ม Component ที่ต้องการ**

```bash
# เพิ่ม Component แยกทีละตัว
pnpx shadcn@latest add button
pnpx shadcn@latest add input
pnpx shadcn@latest add dialog
pnpx shadcn@latest add card
pnpx shadcn@latest add table
pnpx shadcn@latest add toast

# หรือเพิ่มหลายตัวพร้อมกัน
pnpx shadcn@latest add button input dialog card
```

---

### Component ที่ใช้บ่อย

**Button**

```tsx
import { Button } from "@/components/ui/button"

export default function Example() {
  return (
    <div className="flex gap-3">
      {/* Variants */}
      <Button variant="default">ปุ่มหลัก</Button>
      <Button variant="secondary">ปุ่มรอง</Button>
      <Button variant="outline">ปุ่ม Outline</Button>
      <Button variant="ghost">ปุ่ม Ghost</Button>
      <Button variant="destructive">ลบข้อมูล</Button>

      {/* Sizes */}
      <Button size="sm">เล็ก</Button>
      <Button size="default">ปกติ</Button>
      <Button size="lg">ใหญ่</Button>

      {/* States */}
      <Button disabled>ปิดการใช้งาน</Button>
      <Button>
        <span className="mr-2">💾</span>
        บันทึก
      </Button>
    </div>
  )
}
```

**Input และ Form**

```tsx
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { Button } from "@/components/ui/button"

export default function LoginForm() {
  return (
    <div className="space-y-4">
      <div className="space-y-2">
        <Label htmlFor="email">อีเมล</Label>
        <Input
          id="email"
          type="email"
          placeholder="your@email.com"
        />
      </div>

      <div className="space-y-2">
        <Label htmlFor="password">รหัสผ่าน</Label>
        <Input
          id="password"
          type="password"
          placeholder="••••••••"
        />
      </div>

      <Button className="w-full">เข้าสู่ระบบ</Button>
    </div>
  )
}
```

**Card**

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card"
import { Button } from "@/components/ui/button"

export default function ProductCard() {
  return (
    <Card className="w-[360px]">
      <CardHeader>
        <CardTitle>ชื่อสินค้า</CardTitle>
        <CardDescription>คำอธิบายสั้น ๆ ของสินค้า</CardDescription>
      </CardHeader>
      <CardContent>
        <img
          src="/product.jpg"
          alt="รูปสินค้า"
          className="w-full h-48 object-cover rounded-md"
        />
        <p className="mt-4 text-2xl font-bold">฿ 1,290</p>
      </CardContent>
      <CardFooter className="flex gap-2">
        <Button className="flex-1">ใส่ตะกร้า</Button>
        <Button variant="outline">❤️</Button>
      </CardFooter>
    </Card>
  )
}
```

**Dialog (Modal)**

```tsx
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog"
import { Button } from "@/components/ui/button"

export default function ConfirmDialog() {
  return (
    <Dialog>
      <DialogTrigger asChild>
        <Button variant="destructive">ลบบัญชี</Button>
      </DialogTrigger>

      <DialogContent>
        <DialogHeader>
          <DialogTitle>ยืนยันการลบบัญชี</DialogTitle>
          <DialogDescription>
            การดำเนินการนี้ไม่สามารถย้อนกลับได้ ข้อมูลทั้งหมดของคุณจะถูกลบออกจากระบบถาวร
          </DialogDescription>
        </DialogHeader>

        <DialogFooter>
          <Button variant="outline">ยกเลิก</Button>
          <Button variant="destructive">ยืนยันการลบ</Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  )
}
```

---

### ปรับแต่ง Component ให้เข้ากับ Design

เนื่องจาก Code ของ shadcn/ui อยู่ในโปรเจกต์ ปรับแต่งได้เต็มที่

```tsx
// components/ui/button.tsx — แก้ไขตรงนี้ได้โดยตรง

const buttonVariants = cva(
  // Base class ของ Button ทุกตัว
  "inline-flex items-center justify-center font-medium transition-all focus-visible:outline-none focus-visible:ring-2 disabled:opacity-50 disabled:pointer-events-none",
  {
    variants: {
      variant: {
        default: "bg-indigo-600 text-white hover:bg-indigo-700 shadow-sm",
        secondary: "bg-gray-100 text-gray-900 hover:bg-gray-200",
        outline: "border border-gray-300 bg-white hover:bg-gray-50 text-gray-700",
        ghost: "hover:bg-gray-100 text-gray-700",
        destructive: "bg-red-600 text-white hover:bg-red-700",
        // เพิ่ม Variant ใหม่ของคุณเอง
        brand: "bg-gradient-to-r from-indigo-500 to-purple-600 text-white hover:opacity-90",
      },
      size: {
        sm: "h-8 px-3 text-sm rounded-md",
        default: "h-10 px-4 text-sm rounded-lg",
        lg: "h-12 px-6 text-base rounded-xl",
        icon: "h-10 w-10 rounded-lg",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)
```

---

## 2.5 Figma และ Design Handoff

### Figma คืออะไร

**Figma** (ฟิกม่า) คือโปรแกรมออกแบบ UI/UX ที่ทีม Designer ใช้สร้าง Mockup และ Prototype ก่อนส่งให้ Developer เขียนโค้ด ทำงานบนเบราว์เซอร์ ใช้ร่วมกันได้ Real-time

```
Design Process:
Designer → Figma → Design Spec → Developer → Code → Product
```

ในฐานะ Developer คุณไม่จำเป็นต้องออกแบบเอง แต่ต้องอ่าน Figma ให้ได้และแปลงเป็น Code อย่างถูกต้อง

---

### Figma Dev Mode — อ่าน Spec อย่างมืออาชีพ

**Dev Mode** (เดฟ โหมด) คือมุมมองพิเศษใน Figma สำหรับ Developer โดยเฉพาะ เข้าถึงได้ที่ปุ่ม `<>` มุมขวาบน

**สิ่งที่ Dev Mode แสดง**

```
ข้อมูลที่ได้จาก Dev Mode
├── Dimensions: width, height ของ Element
├── Spacing: padding, margin, gap ระหว่าง Element
├── Typography: font-family, font-size, font-weight, line-height, letter-spacing
├── Colors: ค่าสี HEX, RGB, HSL
├── Border: border-width, border-color, border-radius
├── Effects: box-shadow, opacity, blur
└── Assets: Export รูปภาพ, Icon เป็น SVG, PNG, WebP
```

**วิธีอ่านข้อมูลจาก Figma:**

1. **คลิกที่ Element** ที่ต้องการ
2. ดูแถบด้านขวา — Figma จะแสดง CSS Properties
3. สำหรับ Spacing — ชี้ Mouse ไปที่ช่องว่างระหว่าง Element แล้วกด Option/Alt เพื่อดูระยะห่าง
4. คลิก **"Copy as CSS"** เพื่อ Copy Property ที่ต้องการ

---

### แปลง Figma Design เป็น Code

**ตัวอย่าง: อ่านจาก Figma แล้วเขียนเป็น Tailwind**

Design ที่ได้รับจาก Figma
```
Card Component:
- Width: 360px
- Background: #FFFFFF
- Border: 1px solid #E5E7EB
- Border Radius: 12px
- Padding: 24px
- Drop Shadow: 0px 4px 6px rgba(0, 0, 0, 0.07)

Title Text:
- Font: Inter
- Size: 20px / 700 weight
- Color: #111827
- Margin Bottom: 8px

Description Text:
- Size: 14px / 400 weight
- Color: #6B7280
- Line Height: 1.5
```

แปลงเป็น Tailwind CSS
```html
<div class="w-[360px] bg-white border border-gray-200 rounded-xl p-6 shadow-md">
  <h3 class="text-xl font-bold text-gray-900 mb-2">ชื่อการ์ด</h3>
  <p class="text-sm text-gray-500 leading-relaxed">คำอธิบาย</p>
</div>
```

---

### Export Assets จาก Figma

```
วิธี Export Icon เป็น SVG:
1. คลิกที่ Icon ใน Figma
2. แถบขวา → Sections "Export"
3. เลือก Format: SVG
4. คลิก Export

วิธี Export รูปภาพ:
1. คลิกที่ Image/Frame
2. Export → PNG 2x (สำหรับ Retina Display)
   หรือ WebP (ไฟล์เล็กกว่า, Browser รองรับแล้ว)
3. ตั้งชื่อให้ชัดเจน: hero-image.webp, product-card.png
```

---

### การสื่อสารระหว่าง Designer และ Developer

การทำงานร่วมกันที่ดีช่วยให้โปรเจกต์เดินหน้าได้เร็วและลด Rework

```
✅ สิ่งที่ Developer ควรทำ:
- ถาม Designer เมื่อ Spec ไม่ชัดเจน อย่าเดาเอง
- แจ้ง Designer เมื่อ Design ทำไม่ได้ทางเทคนิค เพื่อหาทางออกร่วมกัน
- ใช้ Figma Comment เพื่อถามคำถามตรงจุด
- แจ้ง Edge Case: "ถ้า text ยาวมากจะเกิดอะไรขึ้น?"

✅ คำถามที่ควรถาม Designer:
- Hover State และ Active State เป็นอย่างไร?
- เมื่อ Error เกิดขึ้น แสดงผลอย่างไร?
- Empty State (ไม่มีข้อมูล) ดูอย่างไร?
- Loading State เป็นอย่างไร?
- มือถือหน้าตาเป็นอย่างไร?
```

---

## โปรเจกต์ปฏิบัติ

> **สร้าง Landing Page สินค้า/บริการ 1 หน้า**
>
> Clone Design จาก Figma ที่กำหนดให้ สร้างด้วย HTML + Tailwind CSS v4 + shadcn/ui โดยต้องรองรับ Mobile ทุกขนาด และ Push ขึ้น GitHub

**ข้อกำหนดของโปรเจกต์**

```
โครงสร้าง HTML:
✅ ใช้ Semantic HTML ครบถ้วน (<header>, <main>, <section>, <footer>)
✅ มี <meta> tags ที่ถูกต้อง
✅ รูปภาพทุกรูปมี alt text ที่สื่อความหมาย
✅ Navigation ใช้ <nav> พร้อม aria-label

Design & Layout:
✅ Responsive ทำงานได้บนจอ 375px (iPhone SE) ถึง 1440px (Desktop)
✅ ใช้ Mobile-First approach
✅ Breakpoint ครบ: mobile → tablet → desktop

Component ที่ต้องมี:
✅ Navbar พร้อม Logo และ Menu
✅ Hero Section มี Headline, Subtext, CTA Button
✅ Feature/Product Section อย่างน้อย 3 Card
✅ Call-to-Action Section
✅ Footer

Tailwind CSS:
✅ ใช้ Utility Classes จาก Tailwind v4
✅ มี Dark Mode (ใช้ prefers-color-scheme)

shadcn/ui:
✅ ใช้อย่างน้อย 2 Component จาก shadcn/ui

GitHub:
✅ Push ขึ้น GitHub Repository
✅ มี README.md อธิบายโปรเจกต์
```

**ขั้นตอนการทำ**

```bash
# 1. สร้างโปรเจกต์
pnpm create vite@latest landing-page --template vanilla
cd landing-page

# 2. ติดตั้ง Tailwind CSS v4
pnpm add -D tailwindcss @tailwindcss/vite

# 3. เปิด Figma Design ที่กำหนด วิเคราะห์โครงสร้าง
#    แบ่ง Design ออกเป็น Section ก่อนเขียน Code

# 4. เขียน HTML โครงสร้าง Semantic ก่อน
#    จากนั้นค่อยเพิ่ม Tailwind Classes

# 5. ทดสอบ Responsive ในทุก Breakpoint
#    ใช้ Chrome DevTools → Toggle Device Toolbar (Ctrl+Shift+M)

# 6. Push ขึ้น GitHub
git init
git add .
git commit -m "feat: initial landing page"
git push origin main
```

---

## สรุปหน่วยที่ 2

```
สิ่งที่เรียนรู้ในหน่วยนี้
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Semantic HTML ทำให้เว็บ SEO ดีขึ้นและ Accessible
   ใช้ <header>, <nav>, <main>, <article>, <section>, <footer>

✅ CSS Custom Properties รวมค่าซ้ำ ๆ ไว้ที่เดียว
   เปลี่ยน Design ทั้งเว็บได้ด้วยการแก้จุดเดียว

✅ Flexbox = Layout 1 แกน (แถว หรือ คอลัมน์)
   Grid = Layout 2 แกน (แถว และ คอลัมน์ พร้อมกัน)

✅ Mobile-First: เขียน CSS มือถือก่อน ขยายด้วย min-width
   Container Queries: Responsive ตาม Container ไม่ใช่แค่ Viewport

✅ Tailwind CSS v4: Utility-First, เร็ว, ขนาดไฟล์เล็ก
   shadcn/ui: Component ที่ Accessible, ปรับแต่งได้ 100%

✅ อ่าน Figma Dev Mode เพื่อดู Spec, สี, Spacing, Font ได้
   Export Assets และสื่อสารกับ Designer อย่างมีประสิทธิภาพ
```

---

## คำศัพท์ที่ควรรู้ (Glossary)

| คำศัพท์ | คำอ่าน | ความหมาย |
|---------|--------|---------|
| Semantic HTML | ซีแมนติก | HTML ที่เลือก Tag ตามความหมายของเนื้อหา |
| Accessibility (a11y) | แอกเซสซิบิลิตี | การทำให้เว็บใช้งานได้โดยทุกคน |
| Screen Reader | สกรีน รีดเดอร์ | โปรแกรมอ่านหน้าจอสำหรับผู้พิการทางสายตา |
| ARIA | แอเรีย | Attribute เพิ่มความหมายสำหรับ Assistive Technology |
| CSS Custom Property | - | ตัวแปรใน CSS สำหรับเก็บค่าซ้ำ ๆ |
| Flexbox | เฟล็กซ์บ็อกซ์ | ระบบ Layout CSS แบบ 1 แกน |
| CSS Grid | ซีเอสเอส กริด | ระบบ Layout CSS แบบ 2 แกน |
| Media Query | มีเดีย คิวรี | CSS Rule ที่ทำงานตามขนาดหน้าจอ |
| Container Query | คอนเทนเนอร์ คิวรี | CSS Rule ที่ทำงานตามขนาด Container |
| Mobile-First | โมบาย เฟิร์สต์ | เขียน CSS มือถือก่อน ขยายไปจอใหญ่ |
| Responsive Design | รีสปอนซีฟ ดีไซน์ | Design ที่ปรับตัวตามขนาดหน้าจอ |
| Breakpoint | เบรกพอยต์ | ขนาดหน้าจอที่ Layout เปลี่ยนแปลง |
| Utility-First | ยูทิลิตี เฟิร์สต์ | แนวทาง CSS ที่ใช้ Class เล็ก ๆ ประกอบกัน |
| Tailwind CSS | เทลวินด์ ซีเอสเอส | CSS Framework แบบ Utility-First |
| shadcn/ui | แชดซีเอ็น ยูไอ | Component Library ที่ Copy Code เข้าโปรเจกต์ |
| Headless Component | เฮดเลส คอมโพเนนต์ | Component ที่มี Logic แต่ไม่มี Style ในตัว |
| Figma | ฟิกม่า | โปรแกรมออกแบบ UI/UX บนเว็บ |
| Design Handoff | ดีไซน์ แฮนด์ออฟ | กระบวนการส่งต่อ Design จาก Designer → Developer |
| Dev Mode | เดฟ โหมด | มุมมองใน Figma สำหรับ Developer |
| Transition | ทรานซิชัน | การเปลี่ยนแปลง CSS อย่างนุ่มนวล |
| Animation | แอนิเมชัน | การเคลื่อนไหวที่กำหนดด้วย @keyframes |
| Viewport | วิวพอร์ต | พื้นที่แสดงผลของเบราว์เซอร์ |

---

## แหล่งเรียนรู้เพิ่มเติม

- **developer.mozilla.org/en-US/docs/Web/HTML** — เอกสาร HTML อ้างอิงจาก MDN
- **developer.mozilla.org/en-US/docs/Web/CSS** — เอกสาร CSS อ้างอิงจาก MDN
- **css-tricks.com/snippets/css/a-guide-to-flexbox** — คู่มือ Flexbox ภาพประกอบครบ
- **css-tricks.com/snippets/css/complete-guide-grid** — คู่มือ Grid ภาพประกอบครบ
- **tailwindcss.com/docs** — เอกสาร Tailwind CSS v4 อย่างเป็นทางการ
- **ui.shadcn.com** — เอกสาร shadcn/ui พร้อม Component ตัวอย่าง
- **web.dev/learn/accessibility** — หลักสูตร Accessibility จาก Google
- **figma.com/best-practices/tips-on-developer-handoff** — คู่มือ Design Handoff

---

*หน่วยถัดไป → หน่วยที่ 3: JavaScript สมัยใหม่และ TypeScript*
