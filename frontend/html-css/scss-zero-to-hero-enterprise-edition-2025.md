# SCSS - Từ Zero đến Hero 🚀

## 📚 Mục Lục

1. [SCSS là gì?](#scss-là-gì)
2. [Cấu trúc thư mục cơ bản](#cấu-trúc-thư-mục-cơ-bản)
3. [Variables - Biến](#variables)
4. [Mixins - Hàm tái sử dụng](#mixins)
5. [Utilities - Công cụ tiện ích](#utilities)
6. [clsx/cn có cần thiết không?](#clsx-cn)
7. [Best Practices](#best-practices)
8. [Ví dụ thực tế](#ví-dụ-thực-tế)
9. [Cấu trúc thư mục cấp doanh nghiệp](#cấu-trúc-thư-mục-cấp-doanh-nghiệp)


## SCSS là gì?

**SCSS** (Sassy CSS) là một **CSS preprocessor** - nghĩa là bạn viết code theo cú pháp SCSS, sau đó nó sẽ được **biên dịch** (compile) thành CSS thuần.

### Tại sao dùng SCSS?

```scss
// ❌ CSS thuần - lặp lại nhiều
.button-primary {
  background: #3b82f6;
  padding: 12px 24px;
  border-radius: 8px;
}

.button-secondary {
  background: #6b7280;
  padding: 12px 24px;
  border-radius: 8px;
}

// ✅ SCSS - DRY (Don't Repeat Yourself)
$primary-color: #3b82f6;
$secondary-color: #6b7280;

@mixin button-base {
  padding: 12px 24px;
  border-radius: 8px;
}

.button-primary {
  @include button-base;
  background: $primary-color;
}

.button-secondary {
  @include button-base;
  background: $secondary-color;
}
```

---

## Cấu trúc thư mục cơ bản

```
src/
│
└── styles/
  ├── base.scss
  ├── variables.scss
  ├── mixins.scss
  ├── utilities/
  │   ├── display.scss
  │   ├── position.scss
  │   ├── flex.scss
  │   ├── spacing.scss
  │   ├── object.scss        # object-fit
  │   ├── rounded.scss       # border radius
  │   ├── effects.scss       # blur, opacity...
  │   ├── animation.scss     # skeleton
  │   ├── layout.scss
  │   └── index.scss
  └── index.scss
```

### `variables.scss` - Kho chứa biến

Đây là **tim** của dự án SCSS. Lưu trữ tất cả giá trị có thể tái sử dụng.

```scss
// 🎨 COLORS
$primary: #3b82f6;
$secondary: #6b7280;
$success: #10b981;
$danger: #ef4444;
$warning: #f59e0b;

$text-primary: #1f2937;
$text-secondary: #6b7280;
$text-muted: #9ca3af;

$bg-primary: #ffffff;
$bg-secondary: #f3f4f6;
$bg-dark: #111827;

// 📏 SPACING (theo hệ thống 8px)
$spacing-1: 0.25rem; // 4px
$spacing-2: 0.5rem; // 8px
$spacing-3: 0.75rem; // 12px
$spacing-4: 1rem; // 16px
$spacing-5: 1.25rem; // 20px
$spacing-6: 1.5rem; // 24px
$spacing-8: 2rem; // 32px
$spacing-10: 2.5rem; // 40px
$spacing-12: 3rem; // 48px

// 🔤 TYPOGRAPHY
$font-family-base: "Inter", system-ui, sans-serif;
$font-family-heading: "Poppins", sans-serif;
$font-family-mono: "Fira Code", monospace;

$font-size-xs: 0.75rem; // 12px
$font-size-sm: 0.875rem; // 14px
$font-size-base: 1rem; // 16px
$font-size-lg: 1.125rem; // 18px
$font-size-xl: 1.25rem; // 20px
$font-size-2xl: 1.5rem; // 24px
$font-size-3xl: 1.875rem; // 30px
$font-size-4xl: 2.25rem; // 36px

$font-weight-light: 300;
$font-weight-normal: 400;
$font-weight-medium: 500;
$font-weight-semibold: 600;
$font-weight-bold: 700;

// 🔲 BORDERS & RADIUS
$border-width: 1px;
$border-color: #e5e7eb;

$radius-sm: 0.25rem; // 4px
$radius-md: 0.5rem; // 8px
$radius-lg: 0.75rem; // 12px
$radius-xl: 1rem; // 16px
$radius-2xl: 1.5rem; // 24px
$radius-full: 9999px; // Hình tròn

// 🎬 TRANSITIONS
$transition-fast: 150ms ease-in-out;
$transition-base: 250ms ease-in-out;
$transition-slow: 350ms ease-in-out;

// 🌑 SHADOWS
$shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
$shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
$shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
$shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1);

// 📱 BREAKPOINTS
$breakpoint-sm: 640px;
$breakpoint-md: 768px;
$breakpoint-lg: 1024px;
$breakpoint-xl: 1280px;
$breakpoint-2xl: 1536px;

// 🎯 Z-INDEX
$z-dropdown: 1000;
$z-sticky: 1020;
$z-fixed: 1030;
$z-modal-backdrop: 1040;
$z-modal: 1050;
$z-popover: 1060;
$z-tooltip: 1070;
```

---

### `mixins.scss` - Hàm tái sử dụng

Mixins như **functions** trong lập trình. Giúp tạo ra các đoạn code có thể tái sử dụng.

```scss
// 📱 RESPONSIVE BREAKPOINTS
@mixin mobile {
  @media (max-width: #{$breakpoint-sm - 1px}) {
    @content;
  }
}

@mixin tablet {
  @media (min-width: #{$breakpoint-md}) {
    @content;
  }
}

@mixin desktop {
  @media (min-width: #{$breakpoint-lg}) {
    @content;
  }
}

@mixin xl-desktop {
  @media (min-width: #{$breakpoint-xl}) {
    @content;
  }
}
```

#### `#{}` = _nhúng giá trị hoặc phép toán vào chuỗi_ → bắt SCSS tính rồi xuất ra đúng giá trị.

```scss
@media (max-width: #{$breakpoint-sm - 1px}) { ... }
```

**→ `#{}` giúp SCSS tính toán biểu thức và chèn kết quả vào chuỗi.**

Nếu không có `#{}`, SCSS xem toàn phần `(...)` là **string**, không tính được phép trừ.

---

#### Ví dụ

- `$breakpoint-sm = 768px`
- `$breakpoint-sm - 1px` → **767px**
- Kết quả CSS:

```css
@media (max-width: 767px) {
  ...;
}
```

---

```scss
// 🎯 FLEXBOX
@mixin flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

@mixin flex-between {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

@mixin flex-column {
  display: flex;
  flex-direction: column;
}

// 📏 SIZING
@mixin size($width, $height: $width) {
  width: $width;
  height: $height;
}

@mixin square($size) {
  @include size($size);
}

// ✂️ TEXT TRUNCATE
@mixin text-truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

@mixin line-clamp($lines) {
  display: -webkit-box;
  -webkit-line-clamp: $lines;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

// 🎨 GRADIENT
@mixin gradient($start, $end, $direction: to right) {
  background: linear-gradient($direction, $start, $end);
}

// 🌑 GLASS MORPHISM
@mixin glass {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
}
```

```scss
// 🎬 TRANSITION
@mixin transition($properties...) {
  transition: $properties $transition-base;
}
```

#### ✔️ Ý nghĩa dấu `...` trong SCSS

`$properties...` = **variadic arguments** → cho phép mixin nhận **một hoặc nhiều giá trị**.

---

#### ✔️ Ví dụ

```scss
@mixin transition($properties...) {
  transition: $properties $transition-base;
  // $transition-base được khai báo bên trên
  // Kết hợp với các tham số $properties truyền vào (optional)
}
```

Bạn có thể gọi mixin `transition` truyền vào các variables :

```scss
@include transition(opacity);
@include transition(opacity, transform, color);
```

---

#### ✔️ Tóm lại

- `...` cho phép truyền **nhiều tham số**.
- Dùng được cho mixin và function.
- Giúp mixin linh hoạt hơn.

---

```scss
// 🎭 HOVER LIFT
@mixin hover-lift {
  transition: transform $transition-base, box-shadow $transition-base;

  &:hover {
    transform: translateY(-4px);
    box-shadow: $shadow-lg;
  }
}

// 📦 CONTAINER
@mixin container($max-width: $breakpoint-xl) {
  width: 100%;
  max-width: $max-width;
  margin-left: auto;
  margin-right: auto;
  padding-left: $spacing-4;
  padding-right: $spacing-4;
}

// 🎯 ABSOLUTE CENTER
@mixin absolute-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

// 🔘 BUTTON RESET
@mixin button-reset {
  border: none;
  background: none;
  padding: 0;
  margin: 0;
  cursor: pointer;
  font: inherit;
  color: inherit;

  &:focus {
    outline: none;
  }
}

// 📝 SCROLLBAR
@mixin custom-scrollbar($width: 8px, $track: #f1f1f1, $thumb: #888) {
  &::-webkit-scrollbar {
    width: $width;
  }

  &::-webkit-scrollbar-track {
    background: $track;
  }

  &::-webkit-scrollbar-thumb {
    background: $thumb;
    border-radius: $radius-full;

    &:hover {
      background: darken($thumb, 10%);
    }
  }
}
```

---

#### `base.scss` - CSS Reset & Base Styles

```scss
// CSS RESET
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  font-size: 16px;
  scroll-behavior: smooth;
}

body {
  font-family: $font-family-base;
  font-size: $font-size-base;
  line-height: 1.6;
  color: $text-primary;
  background: $bg-primary;

  // Dành cho trình duyệt dùng engine WebKit / Blink (Chrome, Safari…)
  // Mục đích: làm chữ hiển thị mịn hơn, giảm “răng cưa”. Trên font nhỏ, chữ sẽ ít bị nhòe, sắc nét hơn.
  // Cơ chế: chuyển từ rendering dựa trên subpixel sang grayscale anti-aliasing → chữ trông mượt hơn.
  -webkit-font-smoothing: antialiased;

  // Dành cho Firefox trên macOS.
  // Tương tự, chuyển chữ sang grayscale anti-aliasing thay vì subpixel rendering mặc định của macOS.
  // Kết quả: chữ mịn hơn và đồng bộ với các trình duyệt khác.
  -moz-osx-font-smoothing: grayscale;
}

// HEADINGS
h1,
h2,
h3,
h4,
h5,
h6 {
  font-family: $font-family-heading;
  font-weight: $font-weight-bold;
  line-height: 1.2;
  margin-bottom: $spacing-4;
}

h1 {
  font-size: $font-size-4xl;
}
h2 {
  font-size: $font-size-3xl;
}
h3 {
  font-size: $font-size-2xl;
}
h4 {
  font-size: $font-size-xl;
}
h5 {
  font-size: $font-size-lg;
}
h6 {
  font-size: $font-size-base;
}

// LINKS
a {
  color: $primary;
  text-decoration: none;
  @include transition(color);

  &:hover {
    color: darken($primary, 10%);
  }
}

// IMAGES
img {
  max-width: 100%;
  height: auto;
  display: block;
}

// BUTTONS
button {
  @include button-reset;
}

// LISTS
ul,
ol {
  list-style: none;
}

// CODE
code {
  font-family: $font-family-mono;
  background: $bg-secondary;
  padding: 2px 6px;
  border-radius: $radius-sm;
  font-size: 0.9em;
}
```

---

## Utilities - Công cụ tiện ích

### `display.scss`

```scss
// DISPLAY
.d-block {
  display: block;
}
.d-inline {
  display: inline;
}
.d-inline-block {
  display: inline-block;
}
.d-flex {
  display: flex;
}
.d-inline-flex {
  display: inline-flex;
}
.d-grid {
  display: grid;
}
.d-none {
  display: none;
}

// VISIBILITY
.visible {
  visibility: visible;
}
.invisible {
  visibility: hidden;
}
```

### `flex.scss`

```scss
// FLEX DIRECTION
.flex-row {
  flex-direction: row;
}
.flex-row-reverse {
  flex-direction: row-reverse;
}
.flex-col {
  flex-direction: column;
}
.flex-col-reverse {
  flex-direction: column-reverse;
}

// JUSTIFY CONTENT
.justify-start {
  justify-content: flex-start;
}
.justify-end {
  justify-content: flex-end;
}
.justify-center {
  justify-content: center;
}
.justify-between {
  justify-content: space-between;
}
.justify-around {
  justify-content: space-around;
}
.justify-evenly {
  justify-content: space-evenly;
}

// ALIGN ITEMS
.items-start {
  align-items: flex-start;
}
.items-end {
  align-items: flex-end;
}
.items-center {
  align-items: center;
}
.items-baseline {
  align-items: baseline;
}
.items-stretch {
  align-items: stretch;
}

// FLEX WRAP
.flex-wrap {
  flex-wrap: wrap;
}
.flex-nowrap {
  flex-wrap: nowrap;
}

// FLEX GROW/SHRINK
.flex-1 {
  flex: 1;
}
.flex-auto {
  flex: auto;
}
.flex-none {
  flex: none;
}

// GAP
@for $i from 1 through 12 {
  .gap-#{$i} {
    gap: $spacing-#{$i};
  }
}
```

### `spacing.scss`

```scss
// MARGIN
@each $size in (1, 2, 3, 4, 5, 6, 8, 10, 12) {
  .m-#{$size} {
    margin: #{$size * 0.25}rem;
  }
  .mt-#{$size} {
    margin-top: #{$size * 0.25}rem;
  }
  .mr-#{$size} {
    margin-right: #{$size * 0.25}rem;
  }
  .mb-#{$size} {
    margin-bottom: #{$size * 0.25}rem;
  }
  .ml-#{$size} {
    margin-left: #{$size * 0.25}rem;
  }
  .mx-#{$size} {
    margin-left: #{$size * 0.25}rem;
    margin-right: #{$size * 0.25}rem;
  }
  .my-#{$size} {
    margin-top: #{$size * 0.25}rem;
    margin-bottom: #{$size * 0.25}rem;
  }
}

// PADDING (tương tự margin)
@each $size in (1, 2, 3, 4, 5, 6, 8, 10, 12) {
  .p-#{$size} {
    padding: #{$size * 0.25}rem;
  }
  .pt-#{$size} {
    padding-top: #{$size * 0.25}rem;
  }
  // ... tương tự
}

// AUTO
.m-auto {
  margin: auto;
}
.mx-auto {
  margin-left: auto;
  margin-right: auto;
}
```

### `rounded.scss`

```scss
.rounded-none {
  border-radius: 0;
}
.rounded-sm {
  border-radius: $radius-sm;
}
.rounded {
  border-radius: $radius-md;
}
.rounded-lg {
  border-radius: $radius-lg;
}
.rounded-xl {
  border-radius: $radius-xl;
}
.rounded-2xl {
  border-radius: $radius-2xl;
}
.rounded-full {
  border-radius: $radius-full;
}

// ROUNDED SIDES
.rounded-t-lg {
  border-top-left-radius: $radius-lg;
  border-top-right-radius: $radius-lg;
}
.rounded-r-lg {
  border-top-right-radius: $radius-lg;
  border-bottom-right-radius: $radius-lg;
}
.rounded-b-lg {
  border-bottom-left-radius: $radius-lg;
  border-bottom-right-radius: $radius-lg;
}
.rounded-l-lg {
  border-top-left-radius: $radius-lg;
  border-bottom-left-radius: $radius-lg;
}
```

### `object.scss`

```scss
.object-contain {
  object-fit: contain;
}
.object-cover {
  object-fit: cover;
}
.object-fill {
  object-fit: fill;
}
.object-none {
  object-fit: none;
}
.object-scale-down {
  object-fit: scale-down;
}

// OBJECT POSITION
.object-center {
  object-position: center;
}
.object-top {
  object-position: top;
}
.object-right {
  object-position: right;
}
.object-bottom {
  object-position: bottom;
}
.object-left {
  object-position: left;
}
```

### `effects.scss`

```scss
// OPACITY
@for $i from 0 through 10 {
  .opacity-#{$i * 10} {
    opacity: $i * 0.1;
  }
}

// BLUR
.blur-none {
  filter: blur(0);
}
.blur-sm {
  filter: blur(4px);
}
.blur {
  filter: blur(8px);
}
.blur-lg {
  filter: blur(16px);
}
.blur-xl {
  filter: blur(24px);
}

// SHADOW
.shadow-none {
  box-shadow: none;
}
.shadow-sm {
  box-shadow: $shadow-sm;
}
.shadow {
  box-shadow: $shadow-md;
}
.shadow-lg {
  box-shadow: $shadow-lg;
}
.shadow-xl {
  box-shadow: $shadow-xl;
}

// GRAYSCALE
.grayscale {
  filter: grayscale(100%);
}
.grayscale-0 {
  filter: grayscale(0);
}
```

### `animation.scss`

```scss
// SKELETON LOADING
@keyframes skeleton-loading {
  0% {
    background-position: 200% 0;
  }
  100% {
    background-position: -200% 0;
  }
}

.skeleton {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: skeleton-loading 1.5s infinite;
  border-radius: $radius-md;
}

// FADE IN
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.animate-fade-in {
  animation: fadeIn $transition-base;
}

// SLIDE UP
@keyframes slideUp {
  from {
    transform: translateY(20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

.animate-slide-up {
  animation: slideUp $transition-base;
}

// SPIN
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.animate-spin {
  animation: spin 1s linear infinite;
}

// PULSE
@keyframes pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}

.animate-pulse {
  animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}
```

---

## Kết hợp thư viện clsx, twMerge?

`clsx` là một thư viện nhỏ giúp ghép className động trong React.

### Khi nào SCSS đã đủ? ✅

```tsx
// Component đơn giản
function Button({ variant = "primary" }) {
  return <button className={`btn btn-${variant}`}>Click me</button>;
}
```

```scss
.btn {
  padding: $spacing-3 $spacing-6;
  border-radius: $radius-lg;
  font-weight: $font-weight-semibold;
  @include transition(all);

  &-primary {
    background: $primary;
    color: white;
  }

  &-secondary {
    background: $secondary;
    color: white;
  }
}
```

### Khi nào cần clsx? ⚠️

**1. Conditional Classes phức tạp**

```tsx
// ❌ Khó đọc
<div className={`card ${isActive ? 'active' : ''} ${isLoading ? 'loading' : ''} ${size}`}>

// ✅ Dễ đọc hơn với clsx
import clsx from 'clsx';

<div className={clsx(
  'card',
  isActive && 'active',
  isLoading && 'loading',
  size
)}>
```

**2. Kết hợp nhiều điều kiện**

```tsx
import clsx from "clsx";

function Card({ size, variant, disabled, loading }) {
   // thêm 'card-disabled' nếu disabled = true
   // thêm 'card-loading' nếu loading = true
   // thêm 'card-interactive' nếu không disabled và không loading
  return (
    <div
      className={clsx("card", card-${size}, card-${variant},
      {
        "card-disabled": disabled,
        "card-loading": loading,
        "card-interactive": !disabled && !loading,
      })}
    >
      Content
    </div>
  );
}
```

#### Ví dụ minh họa:

```js
<Card size="small" variant="primary" disabled={false} loading={true} />
```

- `"card"` → luôn có
- `"card-small"` → từ size
- `"card-primary"` → từ variant
- `"card-disabled"` → `disabled=false` → bỏ
- `"card-loading"` → `loading=true` → thêm
- `"card-interactive"` → `!disabled && !loading = !false && !true = false` → bỏ

→ Kết quả:

```css
class="card card-small card-primary card-loading"
```

---

**3. Utility-first approach (như Tailwind)**

Nếu bạn dùng SCSS theo kiểu utility classes:

```ts
// lib/utils
import { clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: Parameters<typeof clsx>) {
  return twMerge(clsx(...inputs));
}
```

- `clsx`: giúp **ghép class conditionally**
- `twMerge`: từ Tailwind CSS, giúp **gộp class trùng lặp hoặc class xung đột** (ví dụ `bg-red-500 bg-blue-500` → chỉ giữ `bg-blue-500`)

- `cn` = `clsx + twMerge` → **vừa ghép class, vừa tự động xử lý class xung đột**.

```tsx
import { cn } from '@/lib/utils'; // cn = clsx + twMerge

<div className={cn(
  'd-flex',
  'items-center',
  'gap-4',
  'p-6',
  'rounded-lg',
  'shadow-md',
  isActive && 'bg-primary text-white',
  !isActive && 'bg-secondary'
)}>

```

```tsx
const buttonClass = cn(
  "px-4 py-2 rounded",
  isPrimary ? "bg-blue-500 text-white" : "bg-gray-200 text-black",
  size === "large" && "text-xl"
);
```

### Kết luận

| Cách tiếp cận            | Khi nào dùng                    | Cần clsx/cn? |
| ------------------------ | ------------------------------- | ------------ |
| **Component-based SCSS** | Component có style cố định      | ❌ Không cần |
| **BEM Methodology**      | Dự án vừa/lớn, team nhiều người | ❌ Không cần |
| **Conditional classes**  | Nhiều điều kiện động            | ✅ Nên dùng  |
| **Utility-first**        | Giống Tailwind                  | ✅ Nên dùng  |

**Khuyến nghị**:

- Nếu bạn viết SCSS theo kiểu **component-based** (mỗi component có file .scss riêng), **KHÔNG CẦN** clsx/cn
- Nếu bạn hay dùng **utility classes** và có nhiều **conditional rendering**, thì **NÊN DÙNG** clsx/cn

---

## Best Practices

### 1. Đặt tên theo BEM

```scss
// Block
.card {
  padding: $spacing-4;

  // Element
  &__header {
    margin-bottom: $spacing-3;
  }

  &__title {
    font-size: $font-size-xl;
  }

  &__body {
    color: $text-secondary;
  }

  // Modifier
  &--featured {
    border: 2px solid $primary;
  }

  &--large {
    padding: $spacing-8;
  }
}
```

### 2. Nesting không quá 3 cấp

```scss
// ❌ BAD - Quá sâu
.header {
  .nav {
    .menu {
      .item {
        .link {
          color: blue;
        }
      }
    }
  }
}

// ✅ GOOD
.header {
  // ...
}

.nav {
  // ...
}

.menu-item {
  // ...

  &__link {
    color: blue;
  }
}
```

### 3. Tách utilities ra file riêng

```scss
// utilities/index.scss
@forward "display";
@forward "flex";
@forward "spacing";
@forward "rounded";
@forward "effects";
@forward "animation";
```

### 4. Sử dụng @use thay vì @import

```scss
// ❌ OLD WAY
@import "variables";
@import "mixins";

// ✅ NEW WAY (Dart Sass)
@use "variables" as *;
@use "mixins" as *;
```

---

## Ví dụ thực tế

### Button Component

```scss
// components/_button.scss
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: $spacing-2;

  padding: $spacing-3 $spacing-6;
  border-radius: $radius-lg;

  font-weight: $font-weight-semibold;
  font-size: $font-size-base;

  cursor: pointer;
  border: none;

  @include transition(all);

  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  // VARIANTS
  &--primary {
    background: $primary;
    color: white;

    &:hover:not(:disabled) {
      background: darken($primary, 10%);
      transform: translateY(-2px);
      box-shadow: $shadow-lg;
    }
  }

  &--secondary {
    background: $secondary;
    color: white;

    &:hover:not(:disabled) {
      background: darken($secondary, 10%);
    }
  }

  &--outline {
    background: transparent;
    border: 2px solid $primary;
    color: $primary;

    &:hover:not(:disabled) {
      background: $primary;
      color: white;
    }
  }

  &--ghost {
    background: transparent;
    color: $primary;

    &:hover:not(:disabled) {
      background: rgba($primary, 0.1);
    }
  }

  // SIZES
  &--sm {
    padding: $spacing-2 $spacing-4;
    font-size: $font-size-sm;
  }

  &--lg {
    padding: $spacing-4 $spacing-8;
    font-size: $font-size-lg;
  }

  &--icon {
    @include square(40px);
    padding: 0;
  }

  // LOADING STATE
  &--loading {
    position: relative;
    color: transparent;
    pointer-events: none;

    &::after {
      content: "";
      @include absolute-center;
      @include square(16px);
      border: 2px solid currentColor;
      border-right-color: transparent;
      border-radius: $radius-full;
      animation: spin 0.6s linear infinite;
    }
  }
}
```

### Card Component

```scss
// components/_card.scss
.card {
  background: $bg-primary;
  border-radius: $radius-xl;
  box-shadow: $shadow-md;
  overflow: hidden;
  @include transition(all);

  &:hover {
    @include hover-lift;
  }

  &__image {
    width: 100%;
    height: 200px;
    object-fit: cover;
  }

  &__content {
    padding: $spacing-6;
  }

  &__header {
    @include flex-between;
    margin-bottom: $spacing-4;
  }

  &__title {
    font-size: $font-size-xl;
    font-weight: $font-weight-bold;
    color: $text-primary;
    margin: 0;
  }

  &__badge {
    padding: $spacing-1 $spacing-3;
    background: $primary;
    color: white;
    border-radius: $radius-full;
    font-size: $font-size-xs;
    font-weight: $font-weight-semibold;
  }

  &__description {
    color: $text-secondary;
    line-height: 1.6;
    margin-bottom: $spacing-4;
    @include line-clamp(3);
  }

  &__footer {
    @include flex-between;
    padding-top: $spacing-4;
    border-top: 1px solid $border-color;
  }

  // VARIANTS
  &--featured {
    border: 2px solid $primary;

    .card__badge {
      background: $warning;
    }
  }

  &--glass {
    @include glass;
  }

  // RESPONSIVE
  @include mobile {
    .card__content {
      padding: $spacing-4;
    }
  }
}
```

### Responsive Grid Layout

```scss
// layouts/_grid.scss
.grid {
  display: grid;
  gap: $spacing-6;

  &--cols-1 {
    grid-template-columns: repeat(1, 1fr);
  }
  &--cols-2 {
    grid-template-columns: repeat(2, 1fr);
  }
  &--cols-3 {
    grid-template-columns: repeat(3, 1fr);
  }
  &--cols-4 {
    grid-template-columns: repeat(4, 1fr);
  }

  // AUTO FIT
  &--auto-fit {
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  }

  // RESPONSIVE
  @include mobile {
    &--cols-2,
    &--cols-3,
    &--cols-4 {
      grid-template-columns: 1fr;
    }
  }

  @include tablet {
    &--cols-3,
    &--cols-4 {
      grid-template-columns: repeat(2, 1fr);
    }
  }
}
```

---

## 🎯 Tổng kết

### SCSS mạnh ở:

- ✅ Tổ chức code theo module
- ✅ Variables & mixins
- ✅ Nesting logic
- ✅ Functions & calculations
- ✅ Component-based styling

### clsx/cn mạnh ở:

- ✅ Conditional classes
- ✅ Dynamic class names
- ✅ Merge utility classes
- ✅ Clean JSX

### Lời khuyên cuối:

1. **Bắt đầu với SCSS thuần** - Học kỹ variables, mixins, nesting
2. **Tổ chức theo BEM** - Code sẽ dễ maintain
3. **Thêm clsx** khi cần - Chỉ khi có nhiều conditional logic
4. **Không cần Tailwind** nếu đã có SCSS tốt

**SCSS đủ mạnh để build bất kỳ UI nào!** 🚀

---

## 🏗️ Cấu Trúc Thư Mục SCSS – Mức Doanh Nghiệp

### 📁 Cấu trúc Hiện Đại & Scalable

```
src/
├── styles/
│   │
│   ├── 📦 foundation/                    # Nền tảng - Layer 1 (Không sinh CSS)
│   │   ├── _tokens.scss                 # Design tokens (màu, spacing, font...)
│   │   ├── _variables.scss              # Biến được tính từ tokens
│   │   ├── _functions.scss              # Pure functions (tính toán)
│   │   ├── _mixins.scss                 # Mixins tái sử dụng
│   │   └── _index.scss                  # Export foundation
│   │
│   ├── 🎨 primitives/                    # Primitives - Layer 2 (Base CSS)
│   │   ├── _reset.scss                  # CSS reset/normalize
│   │   ├── _root.scss                   # :root CSS variables
│   │   ├── _typography.scss             # Font-face, body, headings
│   │   ├── _keyframes.scss              # @keyframes animations
│   │   └── _index.scss
│   │
│   ├── 🧩 utilities/                     # Utilities - Layer 3 (Atomic classes)
│   │   ├── _layout.scss                 # display, position, z-index
│   │   ├── _flexbox.scss                # flex utilities
│   │   ├── _grid.scss                   # grid utilities
│   │   ├── _spacing.scss                # margin, padding
│   │   ├── _sizing.scss                 # width, height, min/max
│   │   ├── _typography.scss             # text-align, font-size, weight...
│   │   ├── _colors.scss                 # bg, text, border colors
│   │   ├── _borders.scss                # border, radius
│   │   ├── _effects.scss                # shadow, opacity, blur
│   │   ├── _interactions.scss           # cursor, pointer-events, user-select
│   │   └── _index.scss
│   │
│   └── main.scss                        # Global entry point
│
├── components/
│   │
│   ├── 🎯 ui/                            # UI Components - Isolated & Reusable
│   │   │
│   │   ├── Button/
│   │   │   ├── Button.tsx               # React component
│   │   │   ├── Button.scss              # Component styles
│   │   │   ├── Button.types.ts          # TypeScript types
│   │   │   ├── Button.stories.tsx       # Storybook stories
│   │   │   ├── Button.test.tsx          # Unit tests
│   │   │   └── index.ts                 # Public API
│   │   │
│   │   ├── Card/
│   │   │   ├── Card.tsx
│   │   │   ├── Card.scss
│   │   │   ├── CardHeader.tsx           # Sub-components
│   │   │   ├── CardBody.tsx
│   │   │   ├── CardFooter.tsx
│   │   │   └── index.ts
│   │   │
│   │   ├── Input/
│   │   │   ├── Input.tsx
│   │   │   ├── Input.scss
│   │   │   ├── Input.types.ts
│   │   │   └── index.ts
│   │   │
│   │   ├── Select/
│   │   ├── Checkbox/
│   │   ├── Radio/
│   │   ├── Switch/
│   │   ├── Badge/
│   │   ├── Avatar/
│   │   ├── Tooltip/
│   │   ├── Modal/
│   │   ├── Dialog/
│   │   ├── Dropdown/
│   │   ├── Tabs/
│   │   ├── Accordion/
│   │   ├── Alert/
│   │   ├── Toast/
│   │   ├── Skeleton/
│   │   ├── Spinner/
│   │   └── index.ts                     # Export all UI components
│   │
│   ├── 🧱 layout/                        # Layout Components
│   │   ├── Container/
│   │   ├── Grid/
│   │   ├── Stack/
│   │   ├── Flex/
│   │   ├── Spacer/
│   │   └── index.ts
│   │
│   ├── 🎭 patterns/                      # Composite Components (UI + Logic)
│   │   ├── Header/
│   │   ├── Sidebar/
│   │   ├── Footer/
│   │   ├── Navbar/
│   │   ├── Breadcrumb/
│   │   ├── Pagination/
│   │   ├── DataTable/
│   │   ├── Form/
│   │   └── index.ts
│   │
│   └── 🚀 features/                      # Feature-Specific Components
│       ├── auth/
│       │   ├── LoginForm/
│       │   └── RegisterForm/
│       │
│       ├── dashboard/
│       │   ├── StatCard/
│       │   └── ChartWidget/
│       │
│       └── product/
│           ├── ProductCard/
│           └── ProductDetail/
│
└── lib/
    └── utils/
        ├── cn.ts                         # clsx + merge utility
        └── styles.ts                     # Style helpers
```

---

### 🎯 Giải Thích Chi Tiết

### 1️⃣ **foundation/** - Nền Tảng (Layer 1)

**Mục đích**: Chứa tất cả config, không sinh ra CSS trực tiếp

#### `_tokens.scss` - Design Tokens

- **Single Source of Truth** cho toàn bộ design system
- Định nghĩa raw values: màu hex, pixel values, font names
- Theo chuẩn Design Tokens (tương tự Figma variables)
- **Không bao giờ sử dụng trực tiếp** trong component

```scss
// ❌ KHÔNG làm trong component
color: #3b82f6;

// ✅ LÀM như này
color: $color-primary-500;
```

**Ví dụ 1: Color Tokens**

```scss
// styles/foundation/_tokens.scss

// Primitive colors - raw values
$color-blue-50: #eff6ff;
$color-blue-100: #dbeafe;
$color-blue-500: #3b82f6;
$color-blue-900: #1e3a8a;

$color-gray-50: #f9fafb;
$color-gray-500: #6b7280;
$color-gray-900: #111827;

// Spacing scale - 8px system
$space-0: 0;
$space-1: 0.25rem; // 4px
$space-2: 0.5rem; // 8px
$space-4: 1rem; // 16px
$space-6: 1.5rem; // 24px
$space-8: 2rem; // 32px

// Font tokens
$font-sans: "Inter", -apple-system, sans-serif;
$font-mono: "Fira Code", monospace;

$font-size-12: 0.75rem;
$font-size-14: 0.875rem;
$font-size-16: 1rem;
$font-size-20: 1.25rem;
```

**Ví dụ 2: Radius & Shadow Tokens**

```scss
// Border radius tokens
$radius-none: 0;
$radius-sm: 0.25rem; // 4px
$radius-md: 0.5rem; // 8px
$radius-lg: 0.75rem; // 12px
$radius-xl: 1rem; // 16px
$radius-full: 9999px;

// Shadow tokens - raw values
$shadow-xs: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
$shadow-sm: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
$shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
$shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
$shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1);

// Duration tokens
$duration-fast: 150ms;
$duration-base: 250ms;
$duration-slow: 350ms;
```

---

#### `_variables.scss` - Semantic Variables

- **Biến được tính toán** từ tokens
- Có ý nghĩa ngữ cảnh (semantic): `$button-bg`, `$input-border`
- Dễ thay đổi theme (light/dark)

**Ví dụ 1: Semantic Color Variables**

```scss
// styles/foundation/_variables.scss
@use "tokens" as *;

// Semantic colors - mapped from tokens
$primary: $color-blue-500;
$secondary: $color-gray-500;
$success: #10b981;
$danger: #ef4444;
$warning: #f59e0b;

// Text colors - contextual meaning
$text-primary: $color-gray-900;
$text-secondary: $color-gray-500;
$text-disabled: $color-gray-400;
$text-inverse: #ffffff;

// Background colors
$bg-primary: #ffffff;
$bg-secondary: $color-gray-50;
$bg-tertiary: $color-gray-100;
$bg-overlay: rgba(0, 0, 0, 0.5);

// Border colors
$border-default: $color-gray-200;
$border-focus: $primary;
$border-error: $danger;
```

**Ví dụ 2: Component-Specific Variables**

```scss
// Button variables
$button-padding-sm: $space-2 $space-4;
$button-padding-md: $space-3 $space-6;
$button-padding-lg: $space-4 $space-8;
$button-radius: $radius-lg;
$button-font-weight: 600;

// Input variables
$input-height: 2.5rem; // 40px
$input-padding: $space-3 $space-4;
$input-border: $border-default;
$input-border-focus: $border-focus;
$input-radius: $radius-md;
$input-bg: $bg-primary;
$input-bg-disabled: $bg-secondary;

// Card variables
$card-padding: $space-6;
$card-radius: $radius-xl;
$card-shadow: $shadow-md;
$card-border: $border-default;
```

---

#### `_functions.scss` - Pure Functions

- Functions tính toán không có side effect
- VD: `rem()`, `em()`, `lighten()`, `darken()`

**Ví dụ 1: Unit Conversion Functions**

```scss
// styles/foundation/_functions.scss

// Convert px to rem
@function rem($px, $base: 16) {
  @return #{$px / $base}rem;
}

// Convert px to em
@function em($px, $base: 16) {
  @return #{$px / $base}em;
}

// Usage
.text {
  font-size: rem(18); // 1.125rem
  margin-bottom: em(24); // 1.5em
}
```

**Ví dụ 2: Color Manipulation Functions**

```scss
// Get contrast text color (black or white)
@function contrast-color($bg-color) {
  @if (lightness($bg-color) > 50%) {
    @return #000000;
  } @else {
    @return #ffffff;
  }
}

// Tint - mix color with white
@function tint($color, $percentage) {
  @return mix(white, $color, $percentage);
}

// Shade - mix color with black
@function shade($color, $percentage) {
  @return mix(black, $color, $percentage);
}

// Usage
.button {
  background: $primary;
  color: contrast-color($primary); // Auto white or black

  &:hover {
    background: shade($primary, 10%); // Darken 10%
  }
}
```

---

#### `_mixins.scss` - Reusable Mixins

- Code snippets tái sử dụng
- VD: `@mixin flex-center`, `@mixin hover-lift`

**Ví dụ 1: Layout Mixins**

```scss
// styles/foundation/_mixins.scss

// Flexbox center
@mixin flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

// Flexbox between
@mixin flex-between {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

// Absolute center
@mixin absolute-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

// Full cover
@mixin cover {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

// Usage
.modal {
  @include absolute-center;
  width: 500px;
}

.overlay {
  @include cover;
  background: rgba(0, 0, 0, 0.5);
}
```

**Ví dụ 2: Text & Effect Mixins**

```scss
// Text truncate with ellipsis
@mixin truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

// Multi-line clamp
@mixin line-clamp($lines: 2) {
  display: -webkit-box;
  -webkit-line-clamp: $lines;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

// Smooth scroll
@mixin smooth-scroll {
  scroll-behavior: smooth;
  -webkit-overflow-scrolling: touch;
}

// Custom scrollbar
@mixin scrollbar($width: 8px, $thumb: #888, $track: #f1f1f1) {
  &::-webkit-scrollbar {
    width: $width;
    height: $width;
  }

  &::-webkit-scrollbar-track {
    background: $track;
  }

  &::-webkit-scrollbar-thumb {
    background: $thumb;
    border-radius: 4px;

    &:hover {
      background: darken($thumb, 10%);
    }
  }
}

// Usage
.product-title {
  @include truncate;
}

.description {
  @include line-clamp(3);
}

.sidebar {
  @include scrollbar(6px, $primary, $bg-secondary);
}
```

---

### 2️⃣ **primitives/** - Primitives (Layer 2)

**Mục đích**: Base CSS ảnh hưởng toàn cục, chỉ chạy 1 lần

#### `_reset.scss`

- CSS reset để normalize styles cross-browser
- Loại bỏ default browser styles

**Ví dụ 1: Box Model Reset**

```scss
// styles/primitives/_reset.scss

// Universal reset
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

// Root setup
html {
  font-size: 16px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-rendering: optimizeLegibility;
}

body {
  margin: 0;
  padding: 0;
  min-height: 100vh;
  line-height: 1.5;
}
```

**Ví dụ 2: Element Reset**

```scss
// Remove default list styles
ul,
ol {
  list-style: none;
  margin: 0;
  padding: 0;
}

// Reset button styles
button {
  border: none;
  background: none;
  padding: 0;
  margin: 0;
  font: inherit;
  color: inherit;
  cursor: pointer;

  &:focus {
    outline: none;
  }
}

// Reset link styles
a {
  color: inherit;
  text-decoration: none;
}

// Reset form elements
input,
textarea,
select {
  font: inherit;
  color: inherit;
  border: none;
  background: none;

  &:focus {
    outline: none;
  }
}

// Image defaults
img,
picture,
video,
canvas,
svg {
  display: block;
  max-width: 100%;
  height: auto;
}
```

---

#### `_root.scss`

- Khai báo CSS custom properties (`:root`)
- Cho phép runtime theming

**Ví dụ 1: CSS Variables for Light Theme**

```scss
// styles/primitives/_root.scss
@use "../foundation/tokens" as *;

:root {
  // Colors
  --color-primary: #{$color-blue-500};
  --color-secondary: #{$color-gray-500};
  --color-success: #10b981;
  --color-danger: #ef4444;

  // Text colors
  --text-primary: #{$color-gray-900};
  --text-secondary: #{$color-gray-500};
  --text-inverse: #ffffff;

  // Backgrounds
  --bg-primary: #ffffff;
  --bg-secondary: #{$color-gray-50};
  --bg-overlay: rgba(0, 0, 0, 0.5);

  // Borders
  --border-color: #{$color-gray-200};
  --border-radius: #{$radius-md};

  // Spacing
  --spacing-sm: #{$space-2};
  --spacing-md: #{$space-4};
  --spacing-lg: #{$space-6};
}
```

**Ví dụ 2: Dark Theme Override**

```scss
// Dark theme with CSS variables
[data-theme="dark"] {
  // Colors remain the same
  --color-primary: #{$color-blue-400}; // Lighter in dark mode
  --color-secondary: #{$color-gray-400};

  // Text colors inverted
  --text-primary: #ffffff;
  --text-secondary: #{$color-gray-400};
  --text-inverse: #{$color-gray-900};

  // Backgrounds inverted
  --bg-primary: #{$color-gray-900};
  --bg-secondary: #{$color-gray-800};
  --bg-overlay: rgba(0, 0, 0, 0.8);

  // Borders
  --border-color: #{$color-gray-700};
}

// Usage in components
.card {
  background: var(--bg-primary);
  color: var(--text-primary);
  border: 1px solid var(--border-color);

  // Auto adapts to light/dark theme!
}
```

---

#### `_typography.scss`

- `@font-face` declarations
- Default body, headings styles
- Line-height, letter-spacing

**Ví dụ 1: Font Face Declarations**

```scss
// styles/primitives/_typography.scss

// Import web fonts
@font-face {
  font-family: "Inter";
  src: url("/fonts/Inter-Regular.woff2") format("woff2");
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: "Inter";
  src: url("/fonts/Inter-SemiBold.woff2") format("woff2");
  font-weight: 600;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: "Inter";
  src: url("/fonts/Inter-Bold.woff2") format("woff2");
  font-weight: 700;
  font-style: normal;
  font-display: swap;
}

// Body defaults
body {
  font-family: $font-sans;
  font-size: $font-size-16;
  font-weight: 400;
  line-height: 1.6;
  color: var(--text-primary);
}
```

**Ví dụ 2: Heading Styles**

```scss
// Heading hierarchy
h1,
h2,
h3,
h4,
h5,
h6 {
  margin: 0 0 1rem 0;
  font-weight: 700;
  line-height: 1.2;
  color: var(--text-primary);
}

h1 {
  font-size: 2.5rem; // 40px
  letter-spacing: -0.02em;

  @media (max-width: 768px) {
    font-size: 2rem; // 32px
  }
}

h2 {
  font-size: 2rem; // 32px
  letter-spacing: -0.01em;
}

h3 {
  font-size: 1.5rem; // 24px
}

h4 {
  font-size: 1.25rem; // 20px
}

h5,
h6 {
  font-size: 1rem; // 16px
}

// Paragraph
p {
  margin: 0 0 1rem 0;
  line-height: 1.6;
}

// Inline text elements
strong,
b {
  font-weight: 700;
}

em,
i {
  font-style: italic;
}

code {
  font-family: $font-mono;
  font-size: 0.875em;
  padding: 0.125rem 0.25rem;
  background: var(--bg-secondary);
  border-radius: 0.25rem;
}
```

---

#### `_keyframes.scss`

- Tất cả `@keyframes` animations
- Tách riêng để tránh duplicate

**Ví dụ 1: Common Animations**

```scss
// styles/primitives/_keyframes.scss

// Fade in
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

// Slide up
@keyframes slideUp {
  from {
    transform: translateY(20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

// Scale in
@keyframes scaleIn {
  from {
    transform: scale(0.9);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}

// Spin (for loaders)
@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

// Usage
.modal {
  animation: fadeIn 0.3s ease-out;
}

.toast {
  animation: slideUp 0.3s ease-out;
}
```

**Ví dụ 2: Skeleton & Shimmer Effects**

```scss
// Skeleton loading shimmer
@keyframes shimmer {
  0% {
    background-position: -1000px 0;
  }
  100% {
    background-position: 1000px 0;
  }
}

// Pulse animation
@keyframes pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}

// Bounce
@keyframes bounce {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}

// Shake (for errors)
@keyframes shake {
  0%,
  100% {
    transform: translateX(0);
  }
  25% {
    transform: translateX(-10px);
  }
  75% {
    transform: translateX(10px);
  }
}

// Usage
.skeleton {
  background: linear-gradient(90deg, #f0f0f0 0px, #e0e0e0 40px, #f0f0f0 80px);
  background-size: 1000px;
  animation: shimmer 1.5s infinite;
}

.error-input {
  animation: shake 0.3s ease-in-out;
}
```

---

### 3️⃣ **utilities/** - Utilities (Layer 3)

**Mục đích**: Atomic utility classes - tái sử dụng nhanh

**Triết lý**:

- Mỗi class làm **1 việc duy nhất**
- Có thể compose nhiều classes
- Không nên override

```html
<!-- Compose utilities -->
<div class="d-flex items-center gap-4 p-6 rounded-lg shadow-md"></div>
```

**Tổ chức theo nhóm chức năng**:

- Layout: display, position, z-index, overflow
- Flexbox: flex-direction, justify, align
- Grid: grid-template, gap
- Spacing: margin, padding
- Sizing: width, height
- Typography: font, text-align, color
- Colors: background, text, border
- Effects: shadow, opacity, blur

**Ví dụ 1: Display & Position Utilities**

```scss
// styles/utilities/_layout.scss

// Display
.d-block { display: block; }
.d-inline { display: inline; }
.d-inline-block { display: inline-block; }
.d-flex { display: flex; }
.d-inline-flex { display: inline-flex; }
.d-grid { display: grid; }
.d-none { display: none; }

// Position
.position-static { position: static; }
.position-relative { position: relative; }
.position-absolute { position: absolute; }
.position-fixed { position: fixed; }
.position-sticky { position: sticky; }

// Z-index scale
.z-0 { z-index: 0; }
.z-10 { z-index: 10; }
.z-20 { z-index: 20; }
.z-30 { z-index: 30; }
.z-40 { z-index: 40; }
.z-50 { z-index: 50; }

// Overflow
.overflow-auto { overflow: auto; }
.overflow-hidden { overflow: hidden; }
.overflow-visible { overflow: visible; }
.overflow-scroll { overflow: scroll; }

// Usage
<div class="position-relative z-10">
  <div class="position-absolute d-flex">
    Content
  </div>
</div>
```

**Ví dụ 2: Spacing Utilities (8px System)**

```scss
// styles/utilities/_spacing.scss
@use '../foundation/tokens' as *;

// Margin utilities
.m-0 { margin: 0; }
.m-1 { margin: $space-1; }   // 4px
.m-2 { margin: $space-2; }   // 8px
.m-3 { margin: 0.75rem; }    // 12px
.m-4 { margin: $space-4; }   // 16px
.m-6 { margin: $space-6; }   // 24px
.m-8 { margin: $space-8; }   // 32px
.m-auto { margin: auto; }

// Margin top
.mt-0 { margin-top: 0; }
.mt-2 { margin-top: $space-2; }
.mt-4 { margin-top: $space-4; }
.mt-6 { margin-top: $space-6; }

// Margin horizontal
.mx-auto {
  margin-left: auto;
  margin-right: auto;
}

// Padding utilities
.p-0 { padding: 0; }
.p-2 { padding: $space-2; }
.p-4 { padding: $space-4; }
.p-6 { padding: $space-6; }
.p-8 { padding: $space-8; }

// Gap utilities (for flex/grid)
.gap-2 { gap: $space-2; }
.gap-4 { gap: $space-4; }
.gap-6 { gap: $space-6; }

// Usage
<div class="p-6 mt-4 gap-4">
  <div class="mx-auto">Centered</div>
</div>
```

### 4️⃣ **components/ui/** - UI Components

**Mục đích**: Isolated, reusable, dumb components

**Đặc điểm**:

- ✅ **Không có business logic**
- ✅ **Không gọi API**
- ✅ **Không dùng global state**
- ✅ **Chỉ nhận props và render UI**
- ✅ **Có thể dùng ở bất kỳ đâu**

**Cấu trúc mỗi component**:

```
Button/
├── Button.tsx           # Main component
├── Button.scss          # Styles (BEM naming)
├── Button.types.ts      # TypeScript interfaces
├── Button.stories.tsx   # Storybook (nếu có)
├── Button.test.tsx      # Unit tests
└── index.ts             # Public exports
```

**Quy tắc viết SCSS**:

- Dùng **BEM naming**: `.btn`, `.btn__icon`, `.btn--primary`
- **Scope toàn bộ styles** trong class cha
- **Không dùng global tag selectors** (trừ `&` nesting)

```scss
// ✅ GOOD - Scoped
.btn {
  &__icon { ... }
  &--primary { ... }
}

// ❌ BAD - Global pollution
button { ... }
```

---

### 5️⃣ **components/layout/** - Layout Components

**Mục đích**: Components quản lý bố cục, spacing, positioning

**Ví dụ**:

- `Container`: Max-width wrapper với responsive padding
- `Grid`: CSS Grid wrapper với các variants
- `Stack`: Vertical/horizontal spacing wrapper
- `Flex`: Flexbox wrapper với props tiện lợi

**Tại sao tách riêng**?

- Layout logic khác với UI logic
- Dễ maintain spacing system
- Tránh duplicate layout code

---

### 6️⃣ **components/patterns/** - Composite Components

**Mục đích**: Kết hợp nhiều UI components + một chút logic

**Đặc điểm**:

- Được xây dựng từ `ui/` components
- Có thể có local state
- Xử lý user interactions cơ bản
- Vẫn reusable nhưng phức tạp hơn

**Ví dụ**:

- `Header`: Compose `Container` + `Navbar` + `Avatar` + `Dropdown`
- `DataTable`: Compose `Table` + `Pagination` + `Search` + sorting logic
- `Form`: Compose `Input` + `Select` + validation logic

---

### 7️⃣ **components/features/** - Feature Components

**Mục đích**: Business-specific components, không reusable

**Đặc điểm**:

- ✅ **Có business logic**
- ✅ **Gọi API**
- ✅ **Dùng global state**
- ✅ **Tích hợp với backend**
- ❌ **KHÔNG reusable** giữa các features

**Tổ chức theo feature**:

```
features/
├── auth/              # Authentication feature
├── dashboard/         # Dashboard feature
├── product/           # Product management
└── user/              # User management
```

**Quy tắc**:

- Mỗi feature có thể có sub-components riêng
- Có thể có SCSS riêng nếu cần
- Ưu tiên compose từ `ui/` và `patterns/`

---

## 🎨 Import Strategy - Tối Ưu Performance

### Global SCSS (`styles/main.scss`)

```scss
// Layer 1: Foundation (no CSS output)
@use "foundation" as *;

// Layer 2: Primitives (base styles)
@use "primitives";

// Layer 3: Utilities (atomic classes)
@use "utilities";
```

### Component SCSS (`Button.scss`)

```scss
// Chỉ import foundation (variables, mixins)
@use "@/styles/foundation" as *;

.btn {
  padding: $spacing-3 $spacing-6;
  border-radius: $radius-lg;
  @include transition(all);

  &--primary {
    background: $color-primary-500;
  }
}
```

**Tại sao không import utilities?**

- Utilities đã load global trong `main.scss`
- Tránh duplicate CSS
- Component chỉ cần variables & mixins

---

## 📐 Naming Conventions

### BEM (Block Element Modifier)

```scss
.block {
} // .card
.block__element {
} // .card__header
.block--modifier {
} // .card--featured
.block__element--modifier {
} // .card__title--large
```

### File Naming

```
PascalCase.tsx         # React components
PascalCase.scss        # Component styles
kebab-case.scss        # SCSS partials (_reset.scss)
camelCase.ts           # Utilities, helpers
```

### CSS Class Naming

```scss
// UI Components
.btn                   // Block
.btn__icon            // Element
.btn--primary         // Modifier

// Utilities
.d-flex               // Short, clear
.items-center         // Readable
.p-4                  // Numeric scale

// States
.is-active            // State prefix
.has-error            // Condition prefix
```

---

## 🚀 Scalability Principles

### 1. **Separation of Concerns**

- **Foundation**: Config only
- **Primitives**: Global base
- **Utilities**: Atomic helpers
- **Components**: Isolated UI

### 2. **Progressive Enhancement**

```
Primitives → Utilities → UI → Patterns → Features
   ↓            ↓         ↓       ↓          ↓
 Base CSS → Helpers → Atoms → Molecules → Organisms
```

### 3. **Single Responsibility**

- Mỗi file có 1 mục đích duy nhất
- Mỗi component làm 1 việc tốt
- Dễ test, dễ maintain

### 4. **Open/Closed Principle**

- Open for extension (thêm variants)
- Closed for modification (không sửa core)

```scss
// ✅ Thêm variant mới
.btn--gradient { ... }

// ❌ Sửa base
.btn {
  // Không sửa code này
}
```

### 5. **Composition over Inheritance**

```tsx
// ✅ Compose
<Card>
  <CardHeader>
    <Avatar />
    <Badge />
  </CardHeader>
</Card>

// ❌ Inherit
<ExtendedCard /> // Kế thừa tất cả props
```

---

## 🎯 Best Practices

### ✅ DO

- Dùng `@use` thay vì `@import` (Dart Sass)
- Scope tất cả styles trong component class
- Dùng semantic naming cho variables
- Tách tokens ra file riêng
- Viết utilities atomic và immutable
- Test component trong isolation (Storybook)

### ❌ DON'T

- Không style global tags (`button`, `div`)
- Không nest quá 3 levels
- Không hard-code values (dùng variables)
- Không duplicate utilities
- Không mix business logic vào UI components
- Không import utilities vào component SCSS

---

## 📊 So Sánh Với Các Phương Pháp Khác

| Approach          | Khi Nào Dùng                     | Trade-offs                                    |
| ----------------- | -------------------------------- | --------------------------------------------- |
| **SCSS Modules**  | Dự án vừa/lớn, cần design system | ✅ Reusable ✅ Maintainable ⚠️ Setup phức tạp |
| **Tailwind CSS**  | Prototype nhanh, team nhỏ        | ✅ Fast ⚠️ HTML dài ❌ Khó custom             |
| **CSS-in-JS**     | React-heavy, runtime theming     | ✅ Dynamic ❌ Performance ❌ SSR              |
| **Inline Styles** | Quick fix, one-off styles        | ✅ Simple ❌ Không reuse ❌ Không responsive  |

---

## 🏆 Kết Luận

Cấu trúc này được sử dụng ở:

- **Airbnb** (Design System)
- **Shopify** (Polaris)
- **Atlassian** (Design System)
- **IBM** (Carbon Design System)

**Ưu điểm**:

- ✅ Scalable: Dễ thêm components mới
- ✅ Maintainable: Dễ tìm và sửa
- ✅ Testable: Component isolation
- ✅ Reusable: DRY principle
- ✅ Performant: Optimal CSS output
- ✅ Team-friendly: Clear conventions

**Đầu tư ban đầu**: Cao (setup structure)  
**ROI lâu dài**: Rất cao (save time, reduce bugs)