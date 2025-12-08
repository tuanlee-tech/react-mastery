# 🚀 SCSS, Web Components & Build Tools — Production Guide

> **Mục tiêu**: Tài liệu này giúp bạn hiểu **sâu** và **đúng** cách dùng SCSS Mixin/Placeholder, Web Components, Webpack/Vite trong sản phẩm thực tế. Phù hợp cho **fresher học**, **senior review**, **team lead mentor**.



## 📋 Mục lục

1. [SCSS: Mixin & Placeholder](#scss-mixin--placeholder)
2. [Web Components: Production-Ready](#web-components-production-ready)
3. [Webpack vs Vite: Khi nào dùng gì](#webpack-vs-vite)
4. [Best Practices Tổng Hợp](#best-practices-tổng-hợp)
5. [Common Pitfalls & Solutions](#common-pitfalls--solutions)

---

# 📐 SCSS: Mixin & Placeholder

## 🎯 Tại sao cần SCSS trong production?

Khi code CSS thuần, bạn sẽ gặp:
- **Code lặp lại** → khó maintain
- **CSS file lớn** → chậm load
- **Khó scale** khi team đông, feature nhiều

SCSS giải quyết bằng: **Mixin** (logic động) + **Placeholder** (style tĩnh).

---

## 🔥 1. Mixin — Dynamic Logic with Parameters

### 📌 Bản chất

```scss
@mixin tên-mixin($param1, $param2) {
  // CSS code với tham số
}

.class {
  @include tên-mixin(value1, value2);
}
```

**Mỗi lần `@include` → SCSS generate đoạn CSS mới.**

### ✅ Khi nào dùng Mixin?

| Tình huống            | Lý do                                    |
| --------------------- | ---------------------------------------- |
| Responsive breakpoint | Cần truyền `min-width`, `max-width`      |
| Spacing system        | Cần truyền margin/padding dynamic        |
| Typography scale      | Cần tính toán `font-size`, `line-height` |
| Animation variants    | Cần truyền duration, delay               |

### ✅ Best Practices

#### 1. **Giữ mixin đơn giản, tập trung logic**

```scss
// ❌ AVOID: Mixin quá phức tạp
@mixin button($size, $color, $border, $radius, $shadow) {
  // Quá nhiều tham số → khó nhớ, khó dùng
}

// ✅ GOOD: Tách logic rõ ràng
@mixin button-size($size) {
  @if $size == 'sm' {
    padding: 4px 8px;
    font-size: 12px;
  } @else if $size == 'md' {
    padding: 8px 16px;
    font-size: 14px;
  } @else {
    padding: 12px 24px;
    font-size: 16px;
  }
}
```

#### 2. **Dùng default value để giảm boilerplate**

```scss
@mixin flex($direction: row, $align: center, $justify: flex-start, $gap: null) {
  display: flex;
  flex-direction: $direction;
  align-items: $align;
  justify-content: $justify;
  
  @if $gap != null {
    gap: $gap;
  }
}

// Sử dụng
.navbar {
  @include flex($justify: space-between, $gap: 16px);
  // direction & align dùng default
}
```

#### 3. **Responsive mixin pattern**

```scss
// Define breakpoints
$breakpoints: (
  'sm': 640px,
  'md': 768px,
  'lg': 1024px,
  'xl': 1280px,
);

@mixin respond($breakpoint) {
  @if map-has-key($breakpoints, $breakpoint) {
    @media (min-width: map-get($breakpoints, $breakpoint)) {
      @content;
    }
  }
}

// Usage
.container {
  padding: 16px;
  
  @include respond('md') {
    padding: 24px;
  }
  
  @include respond('lg') {
    padding: 32px;
  }
}
```

### ⚠️ Pitfalls của Mixin

#### ❌ Problem 1: CSS Bloat

```scss
@mixin card-style {
  border-radius: 8px;
  padding: 16px;
  background: white;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

// Dùng ở 20 nơi
.product-card { @include card-style; }
.user-card { @include card-style; }
.post-card { @include card-style; }
// ... 17 nơi nữa

// ❌ Result: CSS lặp lại 20 lần!
```

**Solution**: Dùng Placeholder thay vì Mixin cho style tĩnh.

---

## 🔥 2. Placeholder — Zero-Cost CSS Sharing

### 📌 Bản chất

```scss
%placeholder-name {
  // CSS code
}

.class {
  @extend %placeholder-name;
}
```

**Placeholder không xuất hiện trong CSS, chỉ merge selector.**

### ✅ Khi nào dùng Placeholder?

| Tình huống        | Lý do                              |
| ----------------- | ---------------------------------- |
| Button base style | Style cố định, lặp lại nhiều       |
| Card foundation   | Border, padding, shadow giống nhau |
| Icon base         | Size, display cố định              |
| Reset/normalize   | Style base không thay đổi          |

### ✅ Best Practices

#### 1. **Foundation layer pattern**

```scss
// Base styles
%btn-base {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s;
  
  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
}

// Variants
.btn-primary {
  @extend %btn-base;
  background: #3b82f6;
  color: white;
}

.btn-secondary {
  @extend %btn-base;
  background: #64748b;
  color: white;
}

.btn-outline {
  @extend %btn-base;
  background: transparent;
  border: 1px solid #e2e8f0;
}
```

**Output CSS** (tối ưu):

```css
.btn-primary, .btn-secondary, .btn-outline {
  display: inline-flex;
  align-items: center;
  /* ... base styles ... */
}

.btn-primary { background: #3b82f6; }
.btn-secondary { background: #64748b; }
.btn-outline { background: transparent; }
```

#### 2. **Grid system với placeholder**

```scss
%grid-base {
  display: grid;
  gap: 16px;
}

.product-grid {
  @extend %grid-base;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
}

.dashboard-grid {
  @extend %grid-base;
  grid-template-columns: repeat(12, 1fr);
}
```

### ⚠️ Pitfalls của Placeholder

#### ❌ Problem 1: Selector Explosion

```scss
// ❌ AVOID: Extend trong nested
.card {
  &__header {
    @extend %flex-center; // Tạo selector phức tạp
  }
}
```

#### ❌ Problem 2: Cross-media Query Extend

```scss
// ❌ ERROR: Không extend qua media query
%mobile-layout { /* ... */ }

@media (max-width: 768px) {
  .container {
    @extend %mobile-layout; // SCSS sẽ báo lỗi!
  }
}
```

**Solution**: Dùng mixin cho responsive:

```scss
@mixin mobile-layout {
  /* ... */
}

@media (max-width: 768px) {
  .container {
    @include mobile-layout; // ✅ OK
  }
}
```

---

## 🧠 Mixin vs Placeholder Decision Tree

```
Cần truyền tham số?
  ├─ YES → Mixin
  └─ NO → Style lặp lại nhiều nơi?
      ├─ YES → Placeholder
      └─ NO → CSS thuần
```

### 📊 So sánh thực tế

```scss
// Scenario: Button system với 3 sizes, 4 colors

// ❌ ANTI-PATTERN: Mixin cho tất cả
@mixin button($size, $color) {
  // Base + size + color logic
  // → 12 nơi dùng = 12 đoạn CSS lặp lại
}

// ✅ OPTIMAL: Kết hợp Placeholder + Mixin
%btn-base {
  /* Base styles - dùng chung */
}

@mixin btn-size($size) {
  /* Size logic - có tham số */
}

@mixin btn-color($color) {
  /* Color logic - có tham số */
}

.btn-primary-lg {
  @extend %btn-base;
  @include btn-size('lg');
  @include btn-color('primary');
}
```

**Kết quả**:
- Base styles: merge 1 lần (placeholder)
- Size/color: generate theo nhu cầu (mixin)
- CSS output: tối ưu nhất

---

# 🧩 Web Components: Production-Ready

## 🎯 Tại sao dùng Web Components?

| Vấn đề                            | Giải pháp Web Components           |
| --------------------------------- | ---------------------------------- |
| CSS conflict giữa components      | Shadow DOM isolated                |
| Component bị phụ thuộc framework  | Custom Elements framework-agnostic |
| Khó reuse component giữa projects | Đóng gói thành npm package         |
| Global CSS làm hỏng UI            | Shadow DOM không leak CSS          |

---

## 🔥 1. Custom Elements — Foundation

### 📌 Lifecycle Methods

```javascript
class MyComponent extends HTMLElement {
  // 1. Constructor - khởi tạo state
  constructor() {
    super();
    this.state = { count: 0 };
  }
  
  // 2. connectedCallback - component vào DOM
  connectedCallback() {
    this.render();
    this.attachEventListeners();
  }
  
  // 3. disconnectedCallback - component rời DOM
  disconnectedCallback() {
    this.cleanup();
  }
  
  // 4. attributeChangedCallback - props thay đổi
  attributeChangedCallback(name, oldValue, newValue) {
    if (oldValue !== newValue) {
      this.render();
    }
  }
  
  // 5. Khai báo props theo dõi
  static get observedAttributes() {
    return ['variant', 'disabled'];
  }
}
```

### ✅ Best Practices

#### 1. **Props Management Pattern**

```javascript
class AppButton extends HTMLElement {
  static get observedAttributes() {
    return ['variant', 'size', 'disabled'];
  }
  
  // Getter cho props
  get variant() {
    return this.getAttribute('variant') || 'primary';
  }
  
  get size() {
    return this.getAttribute('size') || 'md';
  }
  
  get disabled() {
    return this.hasAttribute('disabled');
  }
  
  // Setter cho props (optional)
  set variant(value) {
    this.setAttribute('variant', value);
  }
  
  attributeChangedCallback(name, oldValue, newValue) {
    if (oldValue === newValue) return;
    
    switch(name) {
      case 'variant':
        this.updateVariant();
        break;
      case 'disabled':
        this.updateDisabledState();
        break;
    }
  }
}
```

#### 2. **State Management**

```javascript
class Counter extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this._count = 0; // Private state
  }
  
  // Getter/Setter cho state
  get count() {
    return this._count;
  }
  
  set count(value) {
    this._count = value;
    this.render(); // Re-render khi state thay đổi
  }
  
  increment() {
    this.count += 1;
    // Dispatch custom event
    this.dispatchEvent(new CustomEvent('countchange', {
      detail: { count: this.count }
    }));
  }
}
```

---

## 🔥 2. Shadow DOM — Encapsulation

### 📌 Tại sao bắt buộc dùng Shadow DOM?

```html
<!-- Không có Shadow DOM -->
<style>
  .button { background: red; } /* Global CSS */
</style>

<my-button>
  <button class="button">Click</button>
  <!-- ❌ Bị ảnh hưởng bởi global CSS -->
</my-button>

<!-- Có Shadow DOM -->
<my-button>
  #shadow-root
    <style>
      .button { background: blue; }
    </style>
    <button class="button">Click</button>
    <!-- ✅ Hoàn toàn tách biệt -->
</my-button>
```

### ✅ Best Practices

#### 1. **CSS Custom Properties Pattern**

```javascript
class ThemedCard extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
  }
  
  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <style>
        :host {
          /* Host là custom element itself */
          display: block;
          
          /* CSS Variables - có thể override từ bên ngoài */
          --card-bg: var(--card-bg, white);
          --card-padding: var(--card-padding, 16px);
          --card-radius: var(--card-radius, 8px);
        }
        
        .card {
          background: var(--card-bg);
          padding: var(--card-padding);
          border-radius: var(--card-radius);
          box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }
      </style>
      
      <div class="card">
        <slot></slot>
      </div>
    `;
  }
}

customElements.define('themed-card', ThemedCard);
```

**Usage với custom properties**:

```html
<style>
  themed-card {
    --card-bg: #f3f4f6;
    --card-padding: 24px;
  }
</style>

<themed-card>
  <h3>Custom Theme</h3>
  <p>Content here</p>
</themed-card>
```

#### 2. **Parts API (Modern Approach)**

```javascript
class ModernButton extends HTMLElement {
  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <style>
        .button { /* internal styles */ }
        .icon { /* internal styles */ }
      </style>
      
      <button part="button" class="button">
        <span part="icon" class="icon"></span>
        <span part="label"><slot></slot></span>
      </button>
    `;
  }
}
```

**External styling với ::part()**:

```css
modern-button::part(button) {
  background: linear-gradient(45deg, blue, purple);
}

modern-button::part(icon) {
  color: gold;
}
```

---

## 🔥 3. Template & Slot — Flexible Layouts

### 📌 Slot Patterns

#### 1. **Default Slot**

```javascript
class SimpleCard extends HTMLElement {
  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <div class="card">
        <slot></slot> <!-- Default slot nhận tất cả content -->
      </div>
    `;
  }
}
```

```html
<simple-card>
  <h3>Title</h3>
  <p>Content</p>
  <!-- Tất cả vào default slot -->
</simple-card>
```

#### 2. **Named Slots**

```javascript
class AdvancedCard extends HTMLElement {
  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <style>
        .card { /* ... */ }
        .header { /* ... */ }
        .body { /* ... */ }
        .footer { /* ... */ }
      </style>
      
      <div class="card">
        <div class="header">
          <slot name="header"></slot>
        </div>
        <div class="body">
          <slot></slot> <!-- Default slot -->
        </div>
        <div class="footer">
          <slot name="footer"></slot>
        </div>
      </div>
    `;
  }
}
```

```html
<advanced-card>
  <h3 slot="header">Card Title</h3>
  
  <p>Main content goes here</p>
  <p>More content</p>
  
  <button slot="footer">Action</button>
</advanced-card>
```

#### 3. **Fallback Content**

```javascript
this.shadowRoot.innerHTML = `
  <slot name="icon">
    <!-- Fallback nếu không có content -->
    <svg><!-- default icon --></svg>
  </slot>
`;
```

### ✅ Template Element Pattern

```javascript
class ProductCard extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    
    // Clone template thay vì innerHTML
    const template = document.getElementById('product-card-template');
    this.shadowRoot.appendChild(template.content.cloneNode(true));
  }
}
```

```html
<template id="product-card-template">
  <style>
    /* Scoped styles */
  </style>
  <div class="card">
    <slot name="image"></slot>
    <slot name="title"></slot>
    <slot name="price"></slot>
  </div>
</template>
```

---

## 🔥 4. Production-Ready Component Example

```javascript
/**
 * <app-button variant="primary" size="md" disabled>
 *   Click me
 * </app-button>
 */
class AppButton extends HTMLElement {
  static get observedAttributes() {
    return ['variant', 'size', 'disabled', 'loading'];
  }
  
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this._handleClick = this._handleClick.bind(this);
  }
  
  connectedCallback() {
    this.render();
    this.shadowRoot.querySelector('button').addEventListener('click', this._handleClick);
  }
  
  disconnectedCallback() {
    this.shadowRoot.querySelector('button').removeEventListener('click', this._handleClick);
  }
  
  attributeChangedCallback(name, oldValue, newValue) {
    if (oldValue !== newValue) {
      this.render();
    }
  }
  
  _handleClick(e) {
    if (this.hasAttribute('disabled') || this.hasAttribute('loading')) {
      e.preventDefault();
      e.stopPropagation();
      return;
    }
    
    // Dispatch custom event
    this.dispatchEvent(new CustomEvent('app-button-click', {
      bubbles: true,
      composed: true, // Bubble qua shadow boundary
      detail: { variant: this.variant }
    }));
  }
  
  get variant() {
    return this.getAttribute('variant') || 'primary';
  }
  
  get size() {
    return this.getAttribute('size') || 'md';
  }
  
  render() {
    const variant = this.variant;
    const size = this.size;
    const disabled = this.hasAttribute('disabled');
    const loading = this.hasAttribute('loading');
    
    this.shadowRoot.innerHTML = `
      <style>
        :host {
          display: inline-block;
        }
        
        button {
          font-family: inherit;
          border: none;
          border-radius: 6px;
          cursor: pointer;
          transition: all 0.2s;
          font-weight: 500;
          display: inline-flex;
          align-items: center;
          justify-content: center;
          gap: 8px;
        }
        
        /* Sizes */
        button.sm { padding: 4px 12px; font-size: 12px; }
        button.md { padding: 8px 16px; font-size: 14px; }
        button.lg { padding: 12px 24px; font-size: 16px; }
        
        /* Variants */
        button.primary {
          background: #3b82f6;
          color: white;
        }
        button.primary:hover:not(:disabled) {
          background: #2563eb;
        }
        
        button.secondary {
          background: #64748b;
          color: white;
        }
        
        button.outline {
          background: transparent;
          border: 1px solid #e2e8f0;
          color: #334155;
        }
        
        button:disabled {
          opacity: 0.5;
          cursor: not-allowed;
        }
        
        .spinner {
          display: inline-block;
          width: 14px;
          height: 14px;
          border: 2px solid currentColor;
          border-right-color: transparent;
          border-radius: 50%;
          animation: spin 0.6s linear infinite;
        }
        
        @keyframes spin {
          to { transform: rotate(360deg); }
        }
      </style>
      
      <button 
        class="${variant} ${size}"
        ${disabled ? 'disabled' : ''}
      >
        ${loading ? '<span class="spinner"></span>' : ''}
        <slot></slot>
      </button>
    `;
  }
}

customElements.define('app-button', AppButton);
```

**Usage**:

```html
<app-button variant="primary" size="lg">
  Submit
</app-button>

<app-button variant="outline" disabled>
  Disabled
</app-button>

<app-button variant="secondary" loading>
  Loading...
</app-button>

<script>
  document.querySelector('app-button').addEventListener('app-button-click', (e) => {
    console.log('Clicked:', e.detail);
  });
</script>
```

---

## ⚠️ Web Components Pitfalls

### ❌ Problem 1: Form Association

```javascript
// ❌ Custom input không hoạt động với form
<form>
  <custom-input name="email"></custom-input>
</form>
```

**Solution**: Form-Associated Custom Elements

```javascript
class CustomInput extends HTMLElement {
  static formAssociated = true; // Enable form association
  
  constructor() {
    super();
    this._internals = this.attachInternals(); // Form API
  }
  
  connectedCallback() {
    this.shadowRoot.querySelector('input').addEventListener('input', (e) => {
      this._internals.setFormValue(e.target.value);
    });
  }
}
```

### ❌ Problem 2: SEO & SSR

Web Components render ở client → crawler không thấy nội dung.

**Solutions**:
1. **Declarative Shadow DOM** (mới):
```html
<custom-element>
  <template shadowrootmode="open">
    <style>/* styles */</style>
    <slot></slot>
  </template>
  <!-- Light DOM content for SEO -->
  <h1>Visible to crawlers</h1>
</custom-element>
```

2. **SSR với libraries**: Lit SSR, WebC (11ty)

### ❌ Problem 3: Memory Leaks

```javascript
// ❌ Không cleanup
connectedCallback() {
  this.interval = setInterval(() => {}, 1000);
  window.addEventListener('resize', this.handleResize);
}
```

**Solution**:

```javascript
disconnectedCallback() {
  clearInterval(this.interval);
  window.removeEventListener('resize', this.handleResize);
}
```

---

# ⚙️ Webpack vs Vite

## 🎯 Tại sao cần bundler?

| Vấn đề                        | Bundler giải quyết        |
| ----------------------------- | ------------------------- |
| Browser không hiểu TypeScript | Transform TS → JS         |
| CSS/SCSS không native         | Process → CSS             |
| Images cần optimize           | Compress, WebP conversion |
| Code cần minify               | Tree-shaking, uglify      |
| Dev experience chậm           | HMR, Fast Refresh         |

---

## 🔥 1. Webpack — The Industry Standard

### 📌 Core Concepts

```javascript
// webpack.config.js
module.exports = {
  entry: './src/index.js',           // Điểm vào
  output: {                          // Đầu ra
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  module: {                          // Loaders
    rules: [
      { test: /\.tsx?$/, use: 'ts-loader' },
      { test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }
    ]
  },
  plugins: [                         // Plugins
    new HtmlWebpackPlugin(),
    new MiniCssExtractPlugin()
  ]
};
```

### ✅ Webpack Best Practices

#### 1. **Code Splitting Pattern**

```javascript
// webpack.config.js
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: 10
        },
        common: {
          minChunks: 2,
          priority: 5,
          reuseExistingChunk: true
        }
      }
    }
  }
};
```

#### 2. **Production Optimization**

```javascript
const TerserPlugin = require('terser-webpack-plugin');
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

module.exports = {
  mode: 'production',
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          compress: {
            drop_console: true, // Remove console.log
          }
        }
      }),
      new CssMinimizerPlugin()
    ],
    moduleIds: 'deterministic', // Stable module IDs for caching
    runtimeChunk: 'single'      // Separate runtime chunk
  }
};
```

#### 3. **Environment Variables**

```javascript
const webpack = require('webpack');

module.exports = {
  plugins: [
    new webpack.DefinePlugin({
      'process.env.API_URL': JSON.stringify(process.env.API_URL),
      'process.env.NODE_ENV': JSON.stringify('production')
    })
  ]
};
```

### 📊 Webpack Performance

```javascript
// webpack.config.js - Tối ưu build time
module.exports = {
  cache: {
    type: 'filesystem', // Cache để build nhanh hơn
  },
  resolve: {
    extensions: ['.js', '.jsx', '.ts', '.tsx'],
    modules: ['node_modules'], // Giảm thời gian resolve
  }
};
```

---

## 🔥 2. Vite — Next-Gen Tooling

### 📌 Core Philosophy

**Dev**: ESM native → không bundle
**Build**: Rollup → bundle tối ưu

### ✅ Vite Best Practices

#### 1. **Basic Config**

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  
  resolve: {
    alias: {
      '@': '/src',
      '@components': '/src/components'
    }
  },
  
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          ui: ['@mui/material']
        }
      }
    }
  },
  
  server: {
    port: 3000,
    open: true
  }
});
```

#### 2. **Environment Variables**

```javascript
// .env.production
VITE_API_URL=https://api.example.com

// Usage in code
const apiUrl = import.meta.env.VITE_API_URL;
```

#### 3. **Asset Handling**

```javascript
// Import as URL
import imageUrl from './image.png';

// Import as raw string
import svgRaw from './icon.svg?raw';

// Import as Web Worker
import Worker from './worker?worker';
```

---

## 📊 Webpack vs Vite: Decision Matrix

| Tiêu chí               | Webpack                 | Vite          | Winner  |
| ---------------------- | ----------------------- | ------------- | ------- |
| **Dev Server Start**   | 10-60s                  | 0.5-2s        | Vite ⚡  |
| **HMR Speed**          | 1-3s                    | <100ms        | Vite ⚡  |
| **Build Production**   | Mạnh                    | Mạnh (Rollup) | Draw    |
| **Config Complexity**  | Cao                     | Thấp          | Vite 👍  |
| **Learning Curve**     | Steep                   | Gentle        | Vite 👍  |
| **Enterprise Support** | Mature                  | Growing       | Webpack |
| **Custom Loaders**     | Nhiều                   | Ít hơn        | Webpack |
| **Micro-frontend**     | Tốt (Module Federation) | OK            | Webpack |
| **Legacy Browser**     | Tốt                     | Cần config    | Webpack |

### 🎯 Khi nào dùng Webpack?

✅ **Dùng Webpack khi**:
- Enterprise app lớn, phức tạp
- Cần Module Federation (micro-frontend)
- Nhiều custom loaders/plugins đặc thù
- Team đã quen Webpack, migration cost cao
- Cần support IE11 hoặc legacy browsers

### 🎯 Khi nào dùng Vite?

✅ **Dùng Vite khi**:
- SPA modern (React, Vue, Svelte)
- Startup, product mới (2023+)
- Dev experience quan trọng
- Team nhỏ, cần ship nhanh
- Evergreen browsers (Chrome, Firefox, Safari mới)

---

# 📚 Best Practices Tổng Hợp

## 🎯 SCSS Architecture

### 1. **File Structure Pattern**

```scss
// styles/
// ├── abstracts/
// │   ├── _variables.scss    // Colors, spacing, breakpoints
// │   ├── _mixins.scss        // Reusable mixins
// │   ├── _placeholders.scss  // Reusable placeholders
// │   └── _functions.scss     // SCSS functions
// ├── base/
// │   ├── _reset.scss         // CSS reset
// │   ├── _typography.scss    // Font styles
// │   └── _utilities.scss     // Utility classes
// ├── components/
// │   ├── _buttons.scss
// │   ├── _cards.scss
// │   └── _forms.scss
// ├── layout/
// │   ├── _header.scss
// │   ├── _footer.scss
// │   └── _grid.scss
// └── main.scss               // Import all
```

### 2. **Variables Organization**

```scss
// abstracts/_variables.scss

// Colors - Semantic naming
$color-primary: #3b82f6;
$color-secondary: #64748b;
$color-success: #10b981;
$color-danger: #ef4444;
$color-warning: #f59e0b;

// Grays - Tint scale
$gray-50: #f9fafb;
$gray-100: #f3f4f6;
$gray-200: #e5e7eb;
$gray-300: #d1d5db;
$gray-400: #9ca3af;
$gray-500: #6b7280;
$gray-600: #4b5563;
$gray-700: #374151;
$gray-800: #1f2937;
$gray-900: #111827;

// Spacing scale (8px base)
$spacing-1: 4px;   // 0.25rem
$spacing-2: 8px;   // 0.5rem
$spacing-3: 12px;  // 0.75rem
$spacing-4: 16px;  // 1rem
$spacing-5: 20px;  // 1.25rem
$spacing-6: 24px;  // 1.5rem
$spacing-8: 32px;  // 2rem
$spacing-10: 40px; // 2.5rem
$spacing-12: 48px; // 3rem
$spacing-16: 64px; // 4rem

// Breakpoints
$breakpoint-sm: 640px;
$breakpoint-md: 768px;
$breakpoint-lg: 1024px;
$breakpoint-xl: 1280px;
$breakpoint-2xl: 1536px;

// Z-index scale
$z-dropdown: 1000;
$z-sticky: 1020;
$z-fixed: 1030;
$z-modal-backdrop: 1040;
$z-modal: 1050;
$z-popover: 1060;
$z-tooltip: 1070;
```

### 3. **Mixin Library Pattern**

```scss
// abstracts/_mixins.scss

// ============================================
// LAYOUT MIXINS
// ============================================

@mixin flex($direction: row, $align: stretch, $justify: flex-start, $gap: null) {
  display: flex;
  flex-direction: $direction;
  align-items: $align;
  justify-content: $justify;
  
  @if $gap {
    gap: $gap;
  }
}

@mixin grid($columns: 12, $gap: 16px) {
  display: grid;
  grid-template-columns: repeat($columns, 1fr);
  gap: $gap;
}

@mixin center-absolute {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

// ============================================
// RESPONSIVE MIXINS
// ============================================

@mixin respond-above($breakpoint) {
  @media (min-width: $breakpoint) {
    @content;
  }
}

@mixin respond-below($breakpoint) {
  @media (max-width: $breakpoint - 1px) {
    @content;
  }
}

@mixin respond-between($min, $max) {
  @media (min-width: $min) and (max-width: $max - 1px) {
    @content;
  }
}

// ============================================
// TYPOGRAPHY MIXINS
// ============================================

@mixin text-truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

@mixin text-clamp($lines: 2) {
  display: -webkit-box;
  -webkit-line-clamp: $lines;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

@mixin font-size($size, $line-height: null) {
  font-size: $size;
  
  @if $line-height {
    line-height: $line-height;
  }
}

// ============================================
// VISUAL MIXINS
// ============================================

@mixin box-shadow($level: 1) {
  @if $level == 1 {
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  } @else if $level == 2 {
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  } @else if $level == 3 {
    box-shadow: 0 10px 15px rgba(0, 0, 0, 0.1);
  } @else if $level == 4 {
    box-shadow: 0 20px 25px rgba(0, 0, 0, 0.15);
  }
}

@mixin transition($properties: all, $duration: 0.3s, $timing: ease) {
  transition: $properties $duration $timing;
}

@mixin hover-lift {
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  
  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
}

// ============================================
// PSEUDO ELEMENTS
// ============================================

@mixin pseudo($display: block, $pos: absolute, $content: '') {
  content: $content;
  display: $display;
  position: $pos;
}

// ============================================
// ASPECT RATIO (Legacy support)
// ============================================

@mixin aspect-ratio($width, $height) {
  position: relative;
  
  &::before {
    content: '';
    display: block;
    padding-top: ($height / $width) * 100%;
  }
  
  > * {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
}
```

### 4. **Placeholder Library Pattern**

```scss
// abstracts/_placeholders.scss

// ============================================
// BUTTON BASE
// ============================================

%btn-base {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 500;
  line-height: 1.5;
  text-decoration: none;
  cursor: pointer;
  transition: all 0.2s ease;
  
  &:focus {
    outline: 2px solid transparent;
    outline-offset: 2px;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.5);
  }
  
  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
}

// ============================================
// CARD BASE
// ============================================

%card-base {
  background: white;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

%card-interactive {
  @extend %card-base;
  cursor: pointer;
  transition: all 0.2s ease;
  
  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
}

// ============================================
// FORM BASE
// ============================================

%input-base {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #e5e7eb;
  border-radius: 6px;
  font-size: 14px;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
  
  &:focus {
    outline: none;
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
  }
  
  &::placeholder {
    color: #9ca3af;
  }
  
  &:disabled {
    background-color: #f9fafb;
    cursor: not-allowed;
  }
}

// ============================================
// LAYOUT BASE
// ============================================

%container-base {
  width: 100%;
  max-width: 1280px;
  margin-left: auto;
  margin-right: auto;
  padding-left: 16px;
  padding-right: 16px;
}

%section-base {
  padding-top: 48px;
  padding-bottom: 48px;
  
  @media (min-width: 768px) {
    padding-top: 64px;
    padding-bottom: 64px;
  }
}

// ============================================
// UTILITY PLACEHOLDERS
// ============================================

%clearfix {
  &::after {
    content: '';
    display: table;
    clear: both;
  }
}

%visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

### 5. **Component Pattern Example**

```scss
// components/_buttons.scss

// Import base
@use '../abstracts/variables' as *;
@use '../abstracts/mixins' as *;
@use '../abstracts/placeholders' as *;

// ============================================
// BUTTON VARIANTS
// ============================================

.btn {
  @extend %btn-base;
  
  // Primary
  &--primary {
    background: $color-primary;
    color: white;
    
    &:hover:not(:disabled) {
      background: darken($color-primary, 8%);
    }
    
    &:active:not(:disabled) {
      background: darken($color-primary, 12%);
    }
  }
  
  // Secondary
  &--secondary {
    background: $color-secondary;
    color: white;
    
    &:hover:not(:disabled) {
      background: darken($color-secondary, 8%);
    }
  }
  
  // Outline
  &--outline {
    background: transparent;
    border: 1px solid $color-primary;
    color: $color-primary;
    
    &:hover:not(:disabled) {
      background: rgba($color-primary, 0.1);
    }
  }
  
  // Ghost
  &--ghost {
    background: transparent;
    color: $color-primary;
    
    &:hover:not(:disabled) {
      background: rgba($color-primary, 0.1);
    }
  }
  
  // ============================================
  // SIZES
  // ============================================
  
  &--sm {
    padding: 4px 12px;
    font-size: 12px;
  }
  
  &--md {
    // Default size from %btn-base
  }
  
  &--lg {
    padding: 12px 24px;
    font-size: 16px;
  }
  
  &--xl {
    padding: 16px 32px;
    font-size: 18px;
  }
  
  // ============================================
  // MODIFIERS
  // ============================================
  
  &--full-width {
    width: 100%;
  }
  
  &--icon-only {
    padding: 8px;
    
    &.btn--sm {
      padding: 4px;
    }
    
    &.btn--lg {
      padding: 12px;
    }
  }
  
  &--loading {
    position: relative;
    color: transparent !important;
    pointer-events: none;
    
    &::after {
      @include pseudo;
      @include center-absolute;
      width: 16px;
      height: 16px;
      border: 2px solid currentColor;
      border-right-color: transparent;
      border-radius: 50%;
      animation: spin 0.6s linear infinite;
      color: white;
    }
  }
}

// ============================================
// BUTTON GROUP
// ============================================

.btn-group {
  @include flex(row, center, flex-start, 0);
  
  .btn {
    border-radius: 0;
    
    &:first-child {
      border-top-left-radius: 6px;
      border-bottom-left-radius: 6px;
    }
    
    &:last-child {
      border-top-right-radius: 6px;
      border-bottom-right-radius: 6px;
    }
    
    &:not(:last-child) {
      border-right: 1px solid rgba(255, 255, 255, 0.2);
    }
  }
}

// ============================================
// ANIMATIONS
// ============================================

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
```

---

## 🧩 Web Components Design System

### 1. **Base Component Class**

```javascript
// base-component.js

export class BaseComponent extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this._initialized = false;
  }
  
  connectedCallback() {
    if (!this._initialized) {
      this.init();
      this._initialized = true;
    }
    this.render();
  }
  
  disconnectedCallback() {
    this.cleanup();
  }
  
  attributeChangedCallback(name, oldValue, newValue) {
    if (oldValue !== newValue && this._initialized) {
      this.render();
    }
  }
  
  // Override in child classes
  init() {}
  cleanup() {}
  render() {}
  
  // Utility methods
  emit(eventName, detail = {}) {
    this.dispatchEvent(new CustomEvent(eventName, {
      bubbles: true,
      composed: true,
      detail
    }));
  }
  
  query(selector) {
    return this.shadowRoot.querySelector(selector);
  }
  
  queryAll(selector) {
    return this.shadowRoot.querySelectorAll(selector);
  }
}
```

### 2. **Shared Styles Pattern**

```javascript
// styles/shared-styles.js

export const sharedStyles = `
  :host {
    /* CSS Variables */
    --color-primary: #3b82f6;
    --color-secondary: #64748b;
    --spacing-sm: 8px;
    --spacing-md: 16px;
    --spacing-lg: 24px;
    --radius: 8px;
    --shadow: 0 2px 8px rgba(0,0,0,0.1);
    --transition: all 0.2s ease;
  }
  
  * {
    box-sizing: border-box;
  }
`;

// Usage in components
import { BaseComponent } from './base-component.js';
import { sharedStyles } from './styles/shared-styles.js';

class MyComponent extends BaseComponent {
  render() {
    this.shadowRoot.innerHTML = `
      <style>
        ${sharedStyles}
        
        .component {
          padding: var(--spacing-md);
          border-radius: var(--radius);
        }
      </style>
      
      <div class="component">
        <slot></slot>
      </div>
    `;
  }
}
```

### 3. **Component Communication Pattern**

```javascript
// ============================================
// PARENT → CHILD: Props
// ============================================

class ParentComponent extends BaseComponent {
  render() {
    this.shadowRoot.innerHTML = `
      <child-component 
        title="Hello" 
        count="5"
      ></child-component>
    `;
  }
}

class ChildComponent extends BaseComponent {
  static get observedAttributes() {
    return ['title', 'count'];
  }
  
  get title() {
    return this.getAttribute('title') || '';
  }
  
  get count() {
    return parseInt(this.getAttribute('count')) || 0;
  }
}

// ============================================
// CHILD → PARENT: Custom Events
// ============================================

class ChildComponent extends BaseComponent {
  handleClick() {
    this.emit('item-selected', {
      id: this.id,
      timestamp: Date.now()
    });
  }
}

class ParentComponent extends BaseComponent {
  init() {
    this.shadowRoot.addEventListener('item-selected', (e) => {
      console.log('Child emitted:', e.detail);
    });
  }
}

// ============================================
// SIBLING → SIBLING: Event Bus
// ============================================

// event-bus.js
class EventBus {
  constructor() {
    this.listeners = new Map();
  }
  
  on(event, callback) {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, []);
    }
    this.listeners.get(event).push(callback);
  }
  
  off(event, callback) {
    if (!this.listeners.has(event)) return;
    const callbacks = this.listeners.get(event);
    const index = callbacks.indexOf(callback);
    if (index > -1) {
      callbacks.splice(index, 1);
    }
  }
  
  emit(event, data) {
    if (!this.listeners.has(event)) return;
    this.listeners.get(event).forEach(callback => callback(data));
  }
}

export const eventBus = new EventBus();

// Usage
import { eventBus } from './event-bus.js';

class Component1 extends BaseComponent {
  handleAction() {
    eventBus.emit('user-action', { userId: 123 });
  }
}

class Component2 extends BaseComponent {
  init() {
    this.handleUserAction = (data) => {
      console.log('Received:', data);
    };
    eventBus.on('user-action', this.handleUserAction);
  }
  
  cleanup() {
    eventBus.off('user-action', this.handleUserAction);
  }
}
```

### 4. **State Management Pattern**

```javascript
// state-manager.js

class StateManager {
  constructor(initialState = {}) {
    this.state = initialState;
    this.subscribers = new Set();
  }
  
  getState() {
    return { ...this.state };
  }
  
  setState(updates) {
    const prevState = this.getState();
    this.state = { ...this.state, ...updates };
    this.notify(this.state, prevState);
  }
  
  subscribe(callback) {
    this.subscribers.add(callback);
    return () => this.subscribers.delete(callback);
  }
  
  notify(newState, prevState) {
    this.subscribers.forEach(callback => {
      callback(newState, prevState);
    });
  }
}

// Create store
export const appStore = new StateManager({
  user: null,
  theme: 'light',
  cart: []
});

// Usage in component
class ShoppingCart extends BaseComponent {
  init() {
    this.unsubscribe = appStore.subscribe((state, prevState) => {
      if (state.cart !== prevState.cart) {
        this.render();
      }
    });
  }
  
  cleanup() {
    this.unsubscribe();
  }
  
  render() {
    const { cart } = appStore.getState();
    
    this.shadowRoot.innerHTML = `
      <style>${sharedStyles}</style>
      <div class="cart">
        <h3>Cart (${cart.length})</h3>
        ${cart.map(item => `
          <div class="cart-item">${item.name}</div>
        `).join('')}
      </div>
    `;
  }
  
  addItem(item) {
    const { cart } = appStore.getState();
    appStore.setState({ cart: [...cart, item] });
  }
}
```

### 5. **Lazy Loading Pattern**

```javascript
// lazy-loader.js

export class LazyLoader {
  constructor() {
    this.loaded = new Set();
    this.loading = new Map();
  }
  
  async loadComponent(tagName, url) {
    // Already loaded
    if (this.loaded.has(tagName)) {
      return true;
    }
    
    // Currently loading
    if (this.loading.has(tagName)) {
      return this.loading.get(tagName);
    }
    
    // Start loading
    const promise = import(url).then(() => {
      this.loaded.add(tagName);
      this.loading.delete(tagName);
      return true;
    });
    
    this.loading.set(tagName, promise);
    return promise;
  }
}

export const lazyLoader = new LazyLoader();

// Usage
class AppShell extends BaseComponent {
  async loadPage(page) {
    const loader = this.query('.loader');
    loader.style.display = 'block';
    
    try {
      await lazyLoader.loadComponent(
        `page-${page}`,
        `./pages/${page}.js`
      );
      
      this.shadowRoot.innerHTML += `<page-${page}></page-${page}>`;
    } catch (error) {
      console.error('Failed to load page:', error);
    } finally {
      loader.style.display = 'none';
    }
  }
}
```

---

## ⚙️ Build Tool Configuration

### 1. **Webpack Production Config**

```javascript
// webpack.prod.js

const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
const TerserPlugin = require('terser-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const CompressionPlugin = require('compression-webpack-plugin');

module.exports = {
  mode: 'production',
  
  entry: {
    main: './src/index.js',
    vendor: './src/vendor.js',
  },
  
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'js/[name].[contenthash:8].js',
    chunkFilename: 'js/[name].[contenthash:8].chunk.js',
    publicPath: '/',
    clean: true,
  },
  
  module: {
    rules: [
      // JavaScript/TypeScript
      {
        test: /\.(js|jsx|ts|tsx)$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [
              ['@babel/preset-env', {
                targets: '> 0.25%, not dead',
                useBuiltIns: 'usage',
                corejs: 3,
              }],
              '@babel/preset-typescript',
            ],
            cacheDirectory: true,
          },
        },
      },
      
      // SCSS/CSS
      {
        test: /\.(scss|css)$/,
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: 'css-loader',
            options: {
              sourceMap: true,
              modules: {
                auto: true,
                localIdentName: '[hash:base64:8]',
              },
            },
          },
          {
            loader: 'postcss-loader',
            options: {
              postcssOptions: {
                plugins: [
                  'autoprefixer',
                  'cssnano',
                ],
              },
            },
          },
          'sass-loader',
        ],
      },
      
      // Images
      {
        test: /\.(png|jpe?g|gif|svg|webp)$/i,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 8 * 1024, // 8kb
          },
        },
        generator: {
          filename: 'images/[name].[hash:8][ext]',
        },
      },
      
      // Fonts
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/i,
        type: 'asset/resource',
        generator: {
          filename: 'fonts/[name].[hash:8][ext]',
        },
      },
    ],
  },
  
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          compress: {
            drop_console: true,
            drop_debugger: true,
          },
          format: {
            comments: false,
          },
        },
        extractComments: false,
      }),
      new CssMinimizerPlugin(),
    ],
    
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: 10,
          reuseExistingChunk: true,
        },
        common: {
          minChunks: 2,
          priority: 5,
          reuseExistingChunk: true,
          name: 'common',
        },
      },
    },
    
    runtimeChunk: 'single',
    
    moduleIds: 'deterministic',
  },
  
  plugins: [
    new CleanWebpackPlugin(),
    
    new HtmlWebpackPlugin({
      template: './src/index.html',
      minify: {
        removeComments: true,
        collapseWhitespace: true,
        removeAttributeQuotes: true,
      },
    }),
    
    new MiniCssExtractPlugin({
      filename: 'css/[name].[contenthash:8].css',
      chunkFilename: 'css/[name].[contenthash:8].chunk.css',
    }),
    
    new CompressionPlugin({
      algorithm: 'gzip',
      test: /\.(js|css|html|svg)$/,
      threshold: 10240, // 10kb
      minRatio: 0.8,
    }),
  ],
  
  resolve: {
    extensions: ['.js', '.jsx', '.ts', '.tsx', '.json'],
    alias: {
      '@': path.resolve(__dirname, 'src'),
      '@components': path.resolve(__dirname, 'src/components'),
      '@utils': path.resolve(__dirname, 'src/utils'),
    },
  },
  
  performance: {
    hints: 'warning',
    maxEntrypointSize: 512000, // 500kb
    maxAssetSize: 512000,
  },
};
```

### 2. **Vite Production Config**

```javascript
// vite.config.js

import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { visualizer } from 'rollup-plugin-visualizer';
import viteCompression from 'vite-plugin-compression';
import path from 'path';

export default defineConfig({
  plugins: [
    react(),
    
    // Gzip compression
    viteCompression({
      algorithm: 'gzip',
      ext: '.gz',
      threshold: 10240, // 10kb
    }),
    
    // Brotli compression
    viteCompression({
      algorithm: 'brotliCompress',
      ext: '.br',
      threshold: 10240,
    }),
    
    // Bundle analyzer
    visualizer({
      filename: './dist/stats.html',
      open: false,
      gzipSize: true,
      brotliSize: true,
    }),
  ],
  
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils'),
    },
  },
  
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
    sourcemap: true,
    
    // Browser targets
    target: 'es2015',
    
    rollupOptions: {
      output: {
        // Manual chunks
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'ui-vendor': ['@mui/material', '@emotion/react'],
          'utils-vendor': ['lodash-es', 'date-fns'],
        },
        
        // Naming pattern
        chunkFileNames: 'js/[name]-[hash].js',
        entryFileNames: 'js/[name]-[hash].js',
        assetFileNames: ({ name }) => {
          if (/\.(gif|jpe?g|png|svg|webp)$/.test(name ?? '')) {
            return 'images/[name]-[hash][extname]';
          }
          if (/\.css$/.test(name ?? '')) {
            return 'css/[name]-[hash][extname]';
          }
          return 'assets/[name]-[hash][extname]';
        },
      },
    },
    
    // Chunk size warnings
    chunkSizeWarningLimit: 500, // 500kb
    
    // Minify options
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true,
      },
    },
  },
  
  // Dev server
  server: {
    port: 3000,
    open: true,
    cors: true,
  },
  
  // Preview server
  preview: {
    port: 4173,
  },
});
```

---

# ⚠️ Common Pitfalls & Solutions

## 1. SCSS Pitfalls

### ❌ Pitfall: Over-nesting

```scss
// ❌ BAD: Too deep nesting
.header {
  .nav {
    .menu {
      .item {
        .link {
          color: blue; // .header .nav .menu .item .link
        }
      }
    }
  }
}
```

**✅ Solution**: Max 3 levels nesting

```scss
// ✅ GOOD
.nav-link {
  color: blue;
}

//
```scss
// ✅ GOOD: BEM methodology
.header {
  &__nav {
    // ...
  }
  
  &__menu-item {
    // ...
  }
  
  &__link {
    color: blue;
  }
}
```

### ❌ Pitfall: Mixin Overuse

```scss
// ❌ BAD: Using mixin for static styles
@mixin card-style {
  background: white;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.product-card { @include card-style; }
.user-card { @include card-style; }
.blog-card { @include card-style; }
// CSS duplicated 3 times!
```

**✅ Solution**: Use placeholder for static styles

```scss
// ✅ GOOD: Placeholder merges selectors
%card-style {
  background: white;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.product-card { @extend %card-style; }
.user-card { @extend %card-style; }
.blog-card { @extend %card-style; }

// Output: .product-card, .user-card, .blog-card { ... }
```

### ❌ Pitfall: Color Hardcoding

```scss
// ❌ BAD: Magic numbers everywhere
.button {
  background: #3b82f6;
  color: #ffffff;
  border: 1px solid #2563eb;
}

.alert {
  background: #3b82f6; // Same color but no connection
}
```

**✅ Solution**: Use semantic variables

```scss
// ✅ GOOD: Variables system
$color-primary: #3b82f6;
$color-primary-dark: #2563eb;
$color-white: #ffffff;

.button {
  background: $color-primary;
  color: $color-white;
  border: 1px solid $color-primary-dark;
}

.alert {
  background: $color-primary;
}
```

### ❌ Pitfall: Important Overuse

```scss
// ❌ BAD: Fighting specificity with !important
.button {
  color: blue !important;
  padding: 10px !important;
  margin: 5px !important;
}
```

**✅ Solution**: Fix specificity properly

```scss
// ✅ GOOD: Proper specificity
// If you need to override library styles:
.custom-button.custom-button {
  color: blue; // Double class = higher specificity
}

// Or use :where() for zero specificity
:where(.button) {
  color: blue; // Easy to override
}
```

---

## 2. Web Components Pitfalls

### ❌ Pitfall: Memory Leaks

```javascript
// ❌ BAD: Not cleaning up
class BadComponent extends HTMLElement {
  connectedCallback() {
    this.interval = setInterval(() => {
      console.log('Running...');
    }, 1000);
    
    window.addEventListener('resize', this.handleResize);
    
    this.observer = new IntersectionObserver(() => {});
    this.observer.observe(this);
  }
  
  // No cleanup! 🔥 Memory leak
}
```

**✅ Solution**: Always cleanup

```javascript
// ✅ GOOD: Proper cleanup
class GoodComponent extends HTMLElement {
  connectedCallback() {
    // Bind methods
    this.handleResize = this.handleResize.bind(this);
    
    // Setup
    this.interval = setInterval(() => {
      console.log('Running...');
    }, 1000);
    
    window.addEventListener('resize', this.handleResize);
    
    this.observer = new IntersectionObserver(() => {});
    this.observer.observe(this);
  }
  
  disconnectedCallback() {
    // Cleanup everything
    clearInterval(this.interval);
    window.removeEventListener('resize', this.handleResize);
    
    if (this.observer) {
      this.observer.disconnect();
    }
  }
  
  handleResize() {
    // Handler logic
  }
}
```

### ❌ Pitfall: Forgetting observedAttributes

```javascript
// ❌ BAD: attributeChangedCallback won't fire
class BadButton extends HTMLElement {
  attributeChangedCallback(name, oldValue, newValue) {
    console.log('This never runs!');
    this.render();
  }
  
  // Missing observedAttributes!
}
```

**✅ Solution**: Always declare observedAttributes

```javascript
// ✅ GOOD
class GoodButton extends HTMLElement {
  static get observedAttributes() {
    return ['variant', 'disabled', 'size'];
  }
  
  attributeChangedCallback(name, oldValue, newValue) {
    if (oldValue === newValue) return;
    this.render();
  }
}
```

### ❌ Pitfall: Direct innerHTML in connectedCallback

```javascript
// ❌ BAD: Re-renders on every connect
class BadComponent extends HTMLElement {
  connectedCallback() {
    // This runs EVERY time element is added to DOM
    this.innerHTML = '<div>Content</div>';
    // Lost state, event listeners, etc.
  }
}

// Example of the problem:
const el = document.querySelector('bad-component');
document.body.removeChild(el); // Removed
document.body.appendChild(el);  // Re-added → innerHTML runs again!
```

**✅ Solution**: Check initialization state

```javascript
// ✅ GOOD: Initialize once
class GoodComponent extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this._initialized = false;
  }
  
  connectedCallback() {
    if (!this._initialized) {
      this.render();
      this._initialized = true;
    }
  }
}
```

### ❌ Pitfall: Event Bubbling Issues

```javascript
// ❌ BAD: Custom events don't cross shadow boundary
class BadComponent extends HTMLElement {
  handleClick() {
    this.dispatchEvent(new CustomEvent('myevent'));
    // Won't bubble outside shadow DOM!
  }
}

// Parent can't listen:
document.addEventListener('myevent', () => {
  console.log('Never fires!');
});
```

**✅ Solution**: Use `composed: true`

```javascript
// ✅ GOOD: Events cross shadow boundary
class GoodComponent extends HTMLElement {
  handleClick() {
    this.dispatchEvent(new CustomEvent('myevent', {
      bubbles: true,
      composed: true, // Cross shadow boundary
      detail: { data: 'some data' }
    }));
  }
}

// Now parent can listen:
document.addEventListener('myevent', (e) => {
  console.log('Works!', e.detail);
});
```

### ❌ Pitfall: Styling Slotted Content

```javascript
// ❌ BAD: Can't style slotted content directly
class BadCard extends HTMLElement {
  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <style>
        .content {
          /* This won't work on slotted content */
          color: red;
        }
      </style>
      <div class="content">
        <slot></slot>
      </div>
    `;
  }
}
```

**✅ Solution**: Use ::slotted()

```javascript
// ✅ GOOD: Proper slotted styling
class GoodCard extends HTMLElement {
  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <style>
        /* Style the slot wrapper */
        .content {
          padding: 16px;
        }
        
        /* Style slotted elements */
        ::slotted(*) {
          margin: 0;
        }
        
        ::slotted(h1) {
          font-size: 24px;
        }
        
        ::slotted(p) {
          color: #666;
        }
      </style>
      <div class="content">
        <slot></slot>
      </div>
    `;
  }
}
```

### ❌ Pitfall: Form Integration

```javascript
// ❌ BAD: Custom input doesn't work with forms
class BadInput extends HTMLElement {
  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <input type="text">
    `;
  }
}

// Won't submit with form:
// <form>
//   <bad-input name="email"></bad-input>
// </form>
```

**✅ Solution**: Form-Associated Custom Elements

```javascript
// ✅ GOOD: Form-associated
class GoodInput extends HTMLElement {
  static formAssociated = true;
  
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this._internals = this.attachInternals();
  }
  
  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <input type="text">
    `;
    
    const input = this.shadowRoot.querySelector('input');
    input.addEventListener('input', (e) => {
      // Update form value
      this._internals.setFormValue(e.target.value);
    });
  }
  
  // Form validation
  get value() {
    return this.shadowRoot.querySelector('input').value;
  }
  
  // Required for validation
  checkValidity() {
    const input = this.shadowRoot.querySelector('input');
    return input.checkValidity();
  }
}

customElements.define('good-input', GoodInput);
```

---

## 3. Build Tool Pitfalls

### ❌ Pitfall: Webpack - Not Optimizing Images

```javascript
// ❌ BAD: Large images in bundle
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg)$/,
        type: 'asset/resource'
        // No optimization!
      }
    ]
  }
};
```

**✅ Solution**: Use image-webpack-loader

```javascript
// ✅ GOOD: Optimize images
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|jpeg|gif)$/i,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 8 * 1024 // Inline < 8kb
          }
        },
        use: [
          {
            loader: 'image-webpack-loader',
            options: {
              mozjpeg: {
                progressive: true,
                quality: 65
              },
              optipng: {
                enabled: true
              },
              pngquant: {
                quality: [0.65, 0.90],
                speed: 4
              },
              gifsicle: {
                interlaced: false
              },
              webp: {
                quality: 75
              }
            }
          }
        ]
      }
    ]
  }
};
```

### ❌ Pitfall: Webpack - Slow Dev Builds

```javascript
// ❌ BAD: No caching, slow rebuilds
module.exports = {
  mode: 'development',
  // No cache configuration
};
```

**✅ Solution**: Enable caching

```javascript
// ✅ GOOD: Fast rebuilds with cache
module.exports = {
  mode: 'development',
  
  cache: {
    type: 'filesystem',
    cacheDirectory: path.resolve(__dirname, '.webpack_cache'),
  },
  
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            cacheDirectory: true, // Babel cache
          }
        }
      }
    ]
  },
  
  // Faster source maps in dev
  devtool: 'eval-cheap-module-source-map',
};
```

### ❌ Pitfall: Vite - Large Vendor Chunks

```javascript
// ❌ BAD: One huge vendor chunk
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'lodash', 'moment', 'axios']
          // 500kb+ vendor chunk!
        }
      }
    }
  }
});
```

**✅ Solution**: Split into smaller chunks

```javascript
// ✅ GOOD: Strategic chunking
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          // Core framework
          'react-vendor': ['react', 'react-dom', 'react-router-dom'],
          
          // UI library
          'ui-vendor': ['@mui/material', '@emotion/react', '@emotion/styled'],
          
          // Utilities
          'utils-vendor': ['lodash-es', 'date-fns', 'axios'],
          
          // Charts (lazy load)
          'charts-vendor': ['recharts', 'd3'],
        }
      }
    },
    
    // Warn if chunk > 500kb
    chunkSizeWarningLimit: 500
  }
});
```

### ❌ Pitfall: Not Using Environment Variables Securely

```javascript
// ❌ BAD: Exposing secrets
// .env
API_KEY=secret_key_12345
DATABASE_URL=postgres://...

// main.js
console.log(process.env.API_KEY); // Exposed in bundle!
```

**✅ Solution**: Webpack - Use DefinePlugin properly

```javascript
// ✅ GOOD: Only expose public vars
// .env.local (gitignored)
REACT_APP_API_URL=https://api.example.com
REACT_APP_PUBLIC_KEY=pk_live_123

// webpack.config.js
const webpack = require('webpack');
const dotenv = require('dotenv');

const env = dotenv.config().parsed || {};

// Only pass REACT_APP_* variables
const envKeys = Object.keys(env)
  .filter(key => key.startsWith('REACT_APP_'))
  .reduce((acc, key) => {
    acc[`process.env.${key}`] = JSON.stringify(env[key]);
    return acc;
  }, {});

module.exports = {
  plugins: [
    new webpack.DefinePlugin(envKeys)
  ]
};
```

**✅ Solution**: Vite - Use VITE_ prefix

```javascript
// ✅ GOOD: Vite auto-exposes VITE_* vars
// .env
VITE_API_URL=https://api.example.com
DATABASE_URL=secret  // Not exposed (no VITE_ prefix)

// main.js
const apiUrl = import.meta.env.VITE_API_URL; // ✅ OK
const dbUrl = import.meta.env.DATABASE_URL;  // ❌ undefined
```

### ❌ Pitfall: Not Analyzing Bundle Size

```javascript
// ❌ BAD: No visibility into bundle size
// Ship 2MB bundle to production
```

**✅ Solution**: Add bundle analyzer

```javascript
// ✅ GOOD: Webpack Bundle Analyzer
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      openAnalyzer: false,
      reportFilename: 'bundle-report.html'
    })
  ]
};

// ✅ GOOD: Vite visualizer
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [
    visualizer({
      filename: './dist/stats.html',
      open: true,
      gzipSize: true,
      brotliSize: true,
    })
  ]
});
```

---

## 4. Performance Pitfalls

### ❌ Pitfall: Render Blocking CSS

```html
<!-- ❌ BAD: Blocks initial render -->
<link rel="stylesheet" href="styles.css">
<link rel="stylesheet" href="vendor.css">
<link rel="stylesheet" href="icons.css">
```

**✅ Solution**: Critical CSS inline + async non-critical

```html
<!-- ✅ GOOD: Inline critical CSS -->
<style>
  /* Critical above-the-fold CSS */
  body { margin: 0; font-family: sans-serif; }
  .header { height: 60px; background: #fff; }
</style>

<!-- Async load non-critical -->
<link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="styles.css"></noscript>
```

### ❌ Pitfall: Blocking JavaScript

```html
<!-- ❌ BAD: Blocks HTML parsing -->
<script src="vendor.js"></script>
<script src="app.js"></script>
<body>
  <!-- Content -->
</body>
```

**✅ Solution**: defer or async

```html
<!-- ✅ GOOD: Non-blocking -->
<head>
  <script defer src="vendor.js"></script>
  <script defer src="app.js"></script>
</head>
<body>
  <!-- Content parsed immediately -->
</body>
```

### ❌ Pitfall: No Code Splitting

```javascript
// ❌ BAD: Import everything upfront
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Dashboard from './pages/Dashboard';
import Profile from './pages/Profile';
import Settings from './pages/Settings';
import Reports from './pages/Reports';
// 500kb+ bundle!

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
        <Route path="/reports" element={<Reports />} />
      </Routes>
    </BrowserRouter>
  );
}
```

**✅ Solution**: React.lazy + Suspense

```javascript
// ✅ GOOD: Lazy load routes
import React, { Suspense, lazy } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

// Lazy load pages
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Profile = lazy(() => import('./pages/Profile'));
const Settings = lazy(() => import('./pages/Settings'));
const Reports = lazy(() => import('./pages/Reports'));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/profile" element={<Profile />} />
          <Route path="/settings" element={<Settings />} />
          <Route path="/reports" element={<Reports />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

---

### Production Checklist

### SCSS Checklist

- [ ] Tổ chức biến hợp lý trong `_variables.scss`  
- [ ] Mixin chỉ dùng cho logic động (có tham số)  
- [ ] Placeholder (`%`) dùng cho các style tĩnh lặp lại  
- [ ] Nesting tối đa 3 cấp  
- [ ] Áp dụng BEM hoặc quy ước đặt tên thống nhất  
- [ ] Không hardcode màu / khoảng cách / font  
- [ ] Có mixin responsive đầy đủ  
- [ ] Không dùng `!important` (trừ utility class)  
- [ ] Cấu trúc thư mục rõ ràng, dễ mở rộng  

### Web Components Checklist

- [ ] Bật Shadow DOM để đóng gói (encapsulation)  
- [ ] Khai báo `observedAttributes` đầy đủ  
- [ ] Quản lý lifecycle đúng chuẩn  
- [ ] Dọn dẹp event listener trong `disconnectedCallback`  
- [ ] Custom event dùng `composed: true` khi cần xuyên Shadow DOM  
- [ ] Dùng CSS Custom Properties để hỗ trợ theme  
- [ ] Dùng `<slot>` linh hoạt bố cục  
- [ ] Triển khai Form-Associated Custom Elements nếu cần  
- [ ] Đầy đủ ARIA roles/states cho Accessibility  
- [ ] Xử lý lỗi (try/catch, fallback)  

### Build Tool Checklist

- [ ] Source map bật cả dev + prod  
- [ ] Code splitting đã cấu hình  
- [ ] CSS được extract ra file riêng  
- [ ] Ảnh đã được tối ưu (WebP/AVIF, đúng size)  
- [ ] Nén gzip / brotli bật trên server  
- [ ] Environment variables xử lý an toàn  
- [ ] Đã chạy bundle analyzer  
- [ ] Cache headers (immutable, long-term) đã config  
- [ ] Tree-shaking hoạt động tốt  
- [ ] Build production đã test kỹ  

### Performance Checklist

- [ ] Lighthouse score > 90 (mobile)  
- [ ] First Contentful Paint < 1.8s  
- [ ] Time to Interactive < 3.8s  
- [ ] Largest Contentful Paint < 2.5s  
- [ ] Cumulative Layout Shift < 0.1  
- [ ] Bundle chính (main chunk) < 200 KB (gzipped)  
- [ ] Ảnh below-the-fold dùng `loading="lazy"`  
- [ ] Font dùng WOFF2 + subset + `font-display: swap`  
- [ ] Critical CSS được inline  
- [ ] CSS không critical load async/defer  

---

### Lộ Trình Học cho Fresher

### Tuần 1–2: Nền tảng SCSS

**Ngày 1–3**: Biến, Nesting, Import/@use  
- Thực hành: Xây hệ màu (color system)  
- Thực hành: Tạo hệ spacing 8px  
- Thực hành: Tổ chức cấu trúc thư mục  

**Ngày 4–7**: Mixins & Functions  
- Thực hành: Hệ mixin responsive  
- Thực hành: Mixin typography scale  
- Thực hành: Thư viện function tiện ích  

**Ngày 8–10**: Placeholder & @extend  
- Thực hành: Hệ button hoàn chỉnh  
- Thực hành: Thư viện card  
- So sánh: Mixin vs Placeholder trong CSS output  

**Ngày 11–14**: Dự án thực tế  
- Xây dựng: Design system hoàn chỉnh  
- Bao gồm: Components + utilities + layouts  
- Tối ưu: Kiểm tra kích thước CSS sau compile  

### Tuần 3–4: Web Components

**Ngày 1–5**: Custom Elements cơ bản  
- Thực hành: Counter component  
- Thực hành: Tab component  
- Thực hành: Tooltip component  
- Học: Toàn bộ lifecycle callbacks  

**Ngày 6–10**: Shadow DOM & Styling  
- Thực hành: Button có theme  
- Thực hành: Card dùng CSS Custom Properties  
- Thực hành: Form components với `::part`  

**Ngày 11–15**: Pattern nâng cao  
- Thực hành: Quản lý state nội bộ  
- Thực hành: Giao tiếp qua custom events  
- Thực hành: Lazy register component  

**Ngày 16–20**: Dự án thực tế  
- Xây dựng: Thư viện component production-ready (10+ components)  
- Test: Chạy được trên React, Vue, vanilla JS  

### Tuần 5–6: Build Tools

**Ngày 1–3**: Webpack cơ bản  
- Setup: Config webpack từ đầu  
- Thực hành: Babel, SCSS loader, image loader  
- Thực hành: HTMLWebpackPlugin + MiniCssExtractPlugin  

**Ngày 4–7**: Webpack nâng cao  
- Thực hành: Code splitting + dynamic import  
- Thực hành: Tối ưu production (Terser, SplitChunks)  
- Thực hành: Multi-page config  

**Ngày 8–10**: Vite  
- Setup: Dự án Vite từ zero  
- So sánh: Tốc độ dev vs Webpack  
- Thực hành: Plugin, build optimization  

**Ngày 11–15**: Dự án thực tế  
- Xây dựng: App hoàn chỉnh với build tối ưu  
- Đo lường: Bundle size + Lighthouse score  

---

### Câu Hỏi Phỏng Vấn Senior

### Câu hỏi SCSS

**Q1**: Khi nào dùng mixin, khi nào dùng placeholder?

**Trả lời chuẩn**:  
Dùng mixin khi cần tham số hoặc logic điều kiện (ví dụ responsive, gradient…).  
Dùng placeholder (`%`) cho các đoạn style tĩnh lặp lại để giảm kích thước CSS output (vì `@extend` sẽ gộp chung selector).  
Ví dụ: base button dùng `%btn-base`, còn responsive dùng `@mixin tablet {}`.

**Q2**: Làm sao tránh CSS phình to trong dự án lớn?

**Trả lời chuẩn**:  
Dùng placeholder cho style tĩnh, giới hạn mixin chỉ cho logic động, nesting ≤ 3 cấp, dùng BEM để tránh specificity cao, thường xuyên audit CSS bằng PurgeCSS hoặc Chrome Coverage, cân nhắc CSS Modules / CSS-in-JS cho component thật sự scoped.

### Câu hỏi Web Components

**Q3**: Sự khác biệt giữa open và closed Shadow DOM?

**Trả lời chuẩn**:  
Open cho phép truy cập `element.shadowRoot` từ bên ngoài → tiện debug/test. Closed trả về `null`. Trong thực tế production gần như luôn dùng open vì closed không thực sự bảo mật và gây khó khăn khi debug.

**Q4**: Làm sao tích hợp Web Component vào form HTML?

**Trả lời chuẩn:  
Dùng Form-Associated Custom Elements (FACE):  
```js
static formAssociated = true;
internals = this.attachInternals();
```
Sau đó dùng `internals.setFormValue(value)`, `internals.checkValidity()`, `formDisabledCallback()`, v.v… để đồng bộ với form native.

**Q5**: Tránh memory leak trong Web Component như thế nào?

**Trả lời chuẩn**:  
Trong `disconnectedCallback` phải:  
- Remove tất cả event listener (`removeEventListener`)  
- Clear `setInterval` / `setTimeout`  
- Disconnect `MutationObserver`, `ResizeObserver`, `IntersectionObserver`  
- Hủy `AbortController`, fetch, promise đang chạy  

### Câu hỏi Build Tools

**Q6**: Khi nào chọn Webpack thay vì Vite?

**Trả lời chuẩn**:  
Chọn Webpack khi:  
- Dự án enterprise có yêu cầu build phức tạp  
- Dùng Module Federation (micro-frontend)  
- Cần hỗ trợ legacy browser rất cũ  
- Cần loader/plugin đặc thù chưa có trên Vite  
Còn lại (SPA hiện đại) → Vite nhanh hơn hẳn về dev experience.

**Q7**: Tối ưu bundle size production ra sao?

**Trả lời chuẩn**:  
Code splitting + dynamic import, tree-shaking, Terser minify, gzip/brotli, lazy load route/component, dùng webpack-bundle-analyzer, chuyển sang lodash-es, dùng PurgeCSS/UncSS, sideEffects false trong package.json, preload critical chunks.

---

### Mẹo Hay từ Production

1. **SCSS**: Luôn kiểm tra CSS output sau compile. Một hệ SCSS tốt = CSS cuối cùng gọn nhẹ.  
2. **Web Components**: Shadow DOM là bắt buộc nếu component được chia sẻ giữa các team/framework.  
3. **Build Tools**: Tốc độ dev ảnh hưởng trực tiếp đến năng suất team → đừng tiếc vài giờ config Vite.  
4. **Performance**: Người dùng thật không dùng MacBook Pro + 1 Gbps. Luôn test trên 3G + CPU throttle.  
5. **Documentation**: Component tốt là component tự giải thích được qua props/API.  
6. **Testing**: Web Component dễ test hơn React/Vue component vì chỉ là DOM thuần.  
7. **Migration**: Không rewrite hết một lần. Từng bước áp dụng pattern mới.  
8. **Team**: Quy tắc chỉ hiệu quả khi mọi người hiểu “tại sao”, chứ không chỉ “làm thế nào”.

---


# 📚 Resources for Deep Dive

## SCSS
- Official Docs: https://sass-lang.com/documentation
- CSS Guidelines: https://cssguidelin.es/
- BEM Methodology: http://getbem.com/

## Web Components
- MDN Web Components: https://developer.mozilla.org/en-US/docs/Web/Web_Components
- web.dev Guide: https://web.dev/custom-elements-v1/
- Lit (Web Components library): https://lit.dev/

## Build Tools
- Webpack Docs: https://webpack.js.org/
- Vite Docs: https://vitejs.dev/
- web.dev Build Performance: https://web.dev/optimize-css/

---

**Kết luận**: Tài liệu này tập trung vào **tư duy production** — không chỉ biết cú pháp mà hiểu **khi nào, tại sao, và cách tối ưu**. Áp dụng đúng sẽ giúp bạn build products scale tốt, maintain dễ, và performance cao.