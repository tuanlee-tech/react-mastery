# Tài liệu Mobile-First Front-End: Performance, SEO & Accessibility

> **Mục tiêu:** Hướng dẫn dev Front-end xây dựng website tối ưu cho mobile, tập trung vào tốc độ, SEO và khả năng truy cập (accessibility).

---

## 1. Triết lý Mobile-First

1.1. Mobile-First là gì?

**Mobile-First** = Thiết kế và code cho màn hình nhỏ nhất trước, sau đó mở rộng lên tablet/desktop.

**Bảng breakpoint CSS chuẩn, đầy đủ và phổ biến nhất hiện nay**, theo **mobile-first**.
Bao gồm mobile, tablet, desktop, HD, 2K, và 4K.
| Thiết bị / Mục đích | Min-width (px) | Ghi chú |
| -------------------- | -------------- | ------------------------------------ |
| Mobile nhỏ (default) | — | Không cần media query, CSS mặc định |
| Mobile lớn / Phablet | 480 | iPhone lớn, Android phổ biến |
| Tablet nhỏ | 600 | 7–8 inch tablet, portrait |
| Tablet lớn | 768 | 9–10 inch tablet, portrait/landscape |
| Laptop / Desktop nhỏ | 1024 | 1024px → 1366px screens |
| Desktop trung bình | 1200 | Full HD / 1920px screens |
| Desktop lớn / 2K | 1536 | 2K màn hình rộng |
| Desktop 4K | 1920 | 4K UHD |

---

**Ví dụ :**

```scss
/* ✅ Mobile-First: Default cho mobile */
.container {
  padding: 16px;
  font-size: 16px;
}

/* Mobile lớn / Phablet */
@media (min-width: 480px) {
  .container {
    padding: 18px;
    font-size: 16px;
  }
}

/* Tablet nhỏ */
@media (min-width: 600px) {
  .container {
    padding: 20px;
    font-size: 17px;
  }
}

/* Tablet lớn */
@media (min-width: 768px) {
  .container {
    padding: 24px;
    font-size: 18px;
  }
}

/* Laptop / Desktop nhỏ */
@media (min-width: 1024px) {
  .container {
    padding: 28px;
    max-width: 1024px;
    margin: 0 auto;
  }
}

/* Desktop trung bình */
@media (min-width: 1200px) {
  .container {
    padding: 32px;
    max-width: 1200px;
  }
}

/* Desktop lớn / 2K */
@media (min-width: 1536px) {
  .container {
    padding: 36px;
    max-width: 1400px;
  }
}

/* Desktop 4K */
@media (min-width: 1920px) {
  .container {
    padding: 40px;
    max-width: 1600px;
  }
}
```

### 1.2. Tại sao Mobile-First?

| Lý do                            | Giải thích                                    | Nếu không dùng Mobile-First         |
| -------------------------------- | --------------------------------------------- | ----------------------------------- |
| **60%+ traffic từ mobile**       | Phần lớn người dùng vào web bằng điện thoại   | Website chậm, UX tệ cho đa số user  |
| **Google Mobile-First Indexing** | Google ưu tiên version mobile để xếp hạng SEO | Rank thấp hơn trên Google           |
| **CSS sạch hơn**                 | Ít override, dễ maintain                      | Nhiều `!important`, code rối        |
| **Performance tốt hơn**          | Load ít CSS/assets hơn trên mobile            | Mobile phải tải code desktop → chậm |

**Ví dụ thực tế:**

- **Amazon, Facebook, Shopee** đều dùng Mobile-First.
- Một trang e-commerce không tối ưu mobile mất ~40% conversion rate (tỉ lệ chuyển đổi khách xem -> khách đặt hàng ).

### 1.3. BEM & SCSS

**BEM (Block-Element-Modifier):**

- **Block**: thành phần chính, độc lập.
  Ví dụ:

  ```scss
  .product-card { ... }       // Card sản phẩm
  .button { ... }             // Nút bấm
  .navbar { ... }             // Thanh menu
  .form { ... }               // Form đăng nhập/đăng ký
  ```

- **Element**: phần tử con của block, không tồn tại độc lập.
  Ví dụ:

  ```scss
  .product-card__image { ... }   // Ảnh sản phẩm
  .product-card__name { ... }    // Tên sản phẩm
  .product-card__price { ... }   // Giá sản phẩm

  .button__icon { ... }          // Icon trong button
  .navbar__link { ... }          // Link trong navbar
  .form__input { ... }           // Input field
  .form__label { ... }           // Label cho input
  ```

- **Modifier**: trạng thái hoặc biến thể của block/element.
  Ví dụ:

  ```scss
  .product-card--in-stock { ... }       // Card sản phẩm còn hàng
  .product-card--out-of-stock { ... }   // Card sản phẩm hết hàng
  .product-card__price--discount { ... } // Giá giảm giá

  .button--primary { ... }   // Button chính
  .button--disabled { ... }  // Button disabled
  .navbar__link--active { ... } // Link active
  .form__input--error { ... }   // Input báo lỗi
  ```

## 📌 Mini Cheat Sheet BEM – Block / Element / Modifier

| Block (thành phần chính) | Element (phần tử con)                               | Modifier (trạng thái/biến thể)                      | Ví dụ CSS/SCSS                                        |
| ------------------------ | --------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------- |
| `product-card`           | `__image`, `__name`, `__price`, `__button`          | `--in-stock`, `--out-of-stock`, `__price--discount` | `.product-card__price--discount { color: red; }`      |
| `button`                 | `__icon`, `__text`                                  | `--primary`, `--secondary`, `--disabled`            | `.button--primary { background: blue; }`              |
| `navbar`                 | `__link`, `__logo`, `__item`                        | `__link--active`                                    | `.navbar__link--active { font-weight: bold; }`        |
| `form`                   | `__input`, `__label`, `__error-message`, `__button` | `__input--error`, `__button--disabled`              | `.form__input--error { border-color: red; }`          |
| `modal`                  | `__header`, `__body`, `__footer`, `__close-button`  | `--open`, `--fullscreen`                            | `.modal--open { display: block; }`                    |
| `article-card`           | `__title`, `__summary`, `__image`, `__author`       | `--featured`, `__title--highlight`                  | `.article-card--featured { border: 2px solid gold; }` |
| `dropdown`               | `__toggle`, `__menu`, `__item`                      | `__item--selected`, `--open`                        | `.dropdown__item--selected { background: #eee; }`     |
| `tab`                    | `__link`, `__content`                               | `__link--active`, `--vertical`                      | `.tab__link--active { font-weight: bold; }`           |
| `card`                   | `__header`, `__body`, `__footer`                    | `--highlighted`, `__header--small`                  | `.card--highlighted { border-color: green; }`         |
| `tooltip`                | `__text`, `__arrow`                                 | `--top`, `--bottom`, `--error`                      | `.tooltip--error { background: red; }`                |

---

### Tips khi dùng BEM

1. **Tách biệt rõ ràng:** Block chịu layout chính, Element chỉ style con, Modifier cho trạng thái.
2. **Đặt tên semantic:** Nhìn class là hiểu component + chức năng.
3. **Kết hợp SCSS nested:** Viết dễ đọc, mobile-first, dễ mở rộng.
4. **Tránh xung đột:** Không dùng chung tên như `.box`, `.item`.

**Nguyên tắc đặt tên:**

- Chỉ dùng `a-z`, `0-9`, `-`, `_`.
- Dùng `__` cho element, `--` cho modifier.
- Tên semantic, rõ ràng, tránh tên chung chung như `.box` hay `.item`.

---

**Ví dụ BEM + SCSS Mobile-First:**

```scss
.product-card {
  // tương đương .product-card
  padding: 16px;
  background: #fff;

  &__image {
    // tương đương .product-card__image
    width: 100%;
    border-radius: 6px;
  }

  &__name {
    // tương đương .product-card__name
    font-size: 16px;
    font-weight: 600;
  }

  &__price {
    // tương đương .product-card__price
    font-size: 16px;
    color: #e60023;
  }

  &--in-stock {
    // tương đương .product-card--in-stock
    border: 1px solid green;
  }
}

/* Tablet và Desktop */
@media (min-width: 768px) {
  .product-card {
    padding: 24px;

    &__name {
      font-size: 18px;
    }

    &__price {
      font-size: 18px;
    }
  }
}
```

**Lợi ích:**

- Tách biệt rõ ràng **structure + style + state**
- Mobile-first CSS → dễ maintain và mở rộng

---

## 2. Meta Tags - SEO & Social Media

### 2.1. Meta Tags cơ bản

```html
<!DOCTYPE html>
<html lang="vi">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <!-- SEO cơ bản -->
    <title>Tiêu đề trang (50-60 ký tự) - Brand Name</title>
    <meta
      name="description"
      content="Mô tả 150-160 ký tự, có keyword chính, call-to-action"
    />
    <meta name="keywords" content="keyword1, keyword2, keyword3" />
    <link rel="canonical" href="https://example.com/page" />

    <!-- Robots -->
    <meta name="robots" content="index, follow, max-image-preview:large" />
    <meta name="googlebot" content="index, follow" />
  </head>
</html>
```

#### **Giải thích:**

| Meta Tag      | Công dụng                            | Nếu không dùng                                |
| ------------- | ------------------------------------ | --------------------------------------------- |
| `viewport`    | Responsive trên mobile               | Web hiện như desktop, zoom out                |
| `title`       | Hiện trên tab, Google search         | Google tự tạo title → không tối ưu            |
| `description` | Đoạn mô tả trên Google               | Google tự lấy text → không hấp dẫn            |
| `canonical`   | Chỉ URL chính thức (tránh duplicate) | Google index nhiều URL giống nhau → rank giảm |
| `robots`      | Cho phép/chặn Google crawl           | Default: index, follow                        |

**Robots values:**

- `index`: Cho phép index
- `noindex`: Chặn index (trang login, admin)
- `follow`: Theo links trong trang
- `nofollow`: Không theo links
- `max-image-preview:large`: Hiện ảnh lớn trên Google

### 2.2. Open Graph (Facebook, LinkedIn)

```html
<!-- Open Graph Protocol -->
<meta property="og:type" content="website" />
<meta property="og:url" content="https://example.com/page" />
<meta property="og:title" content="Tiêu đề khi share lên Facebook" />
<meta property="og:description" content="Mô tả khi share, 200-300 ký tự" />
<meta property="og:image" content="https://example.com/share-image.jpg" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content="Mô tả ảnh share" />
<meta property="og:site_name" content="Tên website" />
<meta property="og:locale" content="vi_VN" />
```

#### **Kích thước ảnh chuẩn:**

- **Facebook/LinkedIn:** 1200x630px (tỷ lệ 1.91:1)
- **Nếu không dùng:** Facebook lấy ảnh random, title/description tự sinh → không đẹp, CTR thấp

**Ví dụ thực tế:**

- **The Verge, TechCrunch:** Luôn có OG tags → share đẹp, CTR tăng 30%.

### 2.3. Twitter Cards

```html
<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:site" content="@username" />
<meta name="twitter:creator" content="@author" />
<meta name="twitter:title" content="Tiêu đề khi share Twitter" />
<meta name="twitter:description" content="Mô tả Twitter, 200 ký tự" />
<meta name="twitter:image" content="https://example.com/twitter-image.jpg" />
<meta name="twitter:image:alt" content="Mô tả ảnh" />
```

**Card types:**

- `summary`: Ảnh nhỏ, bên trái
- `summary_large_image`: Ảnh lớn, chiếm toàn bộ
- `app`: Ứng dụng mobile
- `player`: Video/audio embed

### 2.4. Favicon & App Icons

```html
<!-- Favicon -->
<link rel="icon" type="image/x-icon" href="/favicon.ico" />
<link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png" />
<link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png" />

<!-- Apple Touch Icon -->
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png" />

<!-- Android -->
<link rel="manifest" href="/site.webmanifest" />
<meta name="theme-color" content="#ffffff" />
```

---

## 3. JSON-LD Structured Data

### 3.1. Tại sao cần JSON-LD?

**JSON-LD** giúp Google hiểu nội dung trang → hiện **Rich Snippets** (đoạn trích nổi bật):

- ⭐ Rating sao
- 💰 Giá sản phẩm
- 👤 Tác giả, ngày đăng
- 🍞 Breadcrumbs (đường dẫn)

**Ví dụ thực tế:**

- **Amazon:** Product schema → hiện giá, rating trên Google → CTR tăng 20-30%.
- **Recipe sites:** Recipe schema → hiện rating, thời gian nấu → CTR tăng 40%.

### 3.2. Organization Schema

**Xem chi tiết tài liệu về** `Organization Schema` [tại đây](https://developers.google.com/search/docs/appearance/structured-data/organization)

**Test JSON-LD tại :** https://search.google.com/test/rich-results

```html
<script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "Organization",
    "name": "Tên công ty",
    "url": "https://example.com",
    "logo": "https://example.com/logo.png",
    "sameAs": [
      "https://facebook.com/page",
      "https://twitter.com/account",
      "https://linkedin.com/company"
    ],
    "contactPoint": {
      "@type": "ContactPoint",
      "telephone": "+84-xxx-xxx-xxx",
      "contactType": "Customer Service"
    }
  }
</script>
```

### 3.3. Article Schema (Blog post)

```html
<script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "Article",
    "headline": "Tiêu đề bài viết",
    "image": "https://example.com/article-image.jpg",
    "author": {
      "@type": "Person",
      "name": "Tên tác giả"
    },
    "publisher": {
      "@type": "Organization",
      "name": "Tên website",
      "logo": {
        "@type": "ImageObject",
        "url": "https://example.com/logo.png"
      }
    },
    "datePublished": "2024-01-15",
    "dateModified": "2024-01-20"
  }
</script>
```

### 3.4. Product Schema (E-commerce)

```html
<script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "Product",
    "name": "Tai nghe Sony WH-1000XM5",
    "image": "https://example.com/product.jpg",
    "description": "Mô tả sản phẩm",
    "brand": {
      "@type": "Brand",
      "name": "Sony"
    },
    "offers": {
      "@type": "Offer",
      "price": "7990000",
      "priceCurrency": "VND",
      "availability": "https://schema.org/InStock",
      "url": "https://example.com/product/123"
    },
    "aggregateRating": {
      "@type": "AggregateRating",
      "ratingValue": "4.8",
      "reviewCount": "256"
    }
  }
</script>
```

### 3.5. Breadcrumb Schema

```html
<script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "BreadcrumbList",
    "itemListElement": [
      {
        "@type": "ListItem",
        "position": 1,
        "name": "Trang chủ",
        "item": "https://example.com"
      },
      {
        "@type": "ListItem",
        "position": 2,
        "name": "Tai nghe",
        "item": "https://example.com/tai-nghe"
      },
      {
        "@type": "ListItem",
        "position": 3,
        "name": "Sony WH-1000XM5"
      }
    ]
  }
</script>
```

---

## 4. Robots.txt & Sitemap

### 4.1. Robots.txt

```txt
# /robots.txt
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /private/
Disallow: /api/
Disallow: /*?*sort=*  # Chặn URL có query params

# Sitemap
Sitemap: https://example.com/sitemap.xml

# Crawl delay (nếu cần)
Crawl-delay: 10

# Chặn bot cụ thể
User-agent: BadBot
Disallow: /
```

**Công dụng:**

- `Allow`: Cho phép crawl
- `Disallow`: Chặn crawl (admin, private pages)
- `Sitemap`: Chỉ đường đến sitemap
- `Crawl-delay`: Giới hạn tốc độ crawl (tránh quá tải server)

### 4.2. Sitemap.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2024-01-20</lastmod>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://example.com/products</loc>
    <lastmod>2024-01-19</lastmod>
    <changefreq>weekly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

**Giải thích:**

- `loc`: URL đầy đủ
- `lastmod`: Ngày sửa lần cuối
- `changefreq`: Tần suất thay đổi (always, hourly, daily, weekly, monthly, yearly, never)
- `priority`: Độ ưu tiên (0.0 - 1.0)

**Submit sitemap:** Google Search Console → Sitemaps → Add sitemap URL

---

## 5. HTML Semantic & Tối ưu SEO

### 5.1. Cấu trúc Semantic

```html
<!DOCTYPE html>
<html lang="vi">
  <head>
    <!-- Meta tags ở section 2 -->
  </head>
  <body>
    <header>
      <nav aria-label="Menu chính">
        <!-- Navigation -->
      </nav>
    </header>

    <main>
      <article>
        <h1>Heading chính - chỉ 1 h1/trang</h1>
        <section>
          <h2>Heading phụ</h2>
          <p>Nội dung...</p>
        </section>
      </article>
    </main>

    <aside>
      <!-- Sidebar/Related content -->
    </aside>

    <footer>
      <!-- Footer -->
    </footer>
  </body>
</html>
```

### 5.2. Tại sao dùng Semantic HTML?

| Thẻ         | Công dụng                    | SEO/Accessibility             | Nếu dùng `<div>` thay thế              |
| ----------- | ---------------------------- | ----------------------------- | -------------------------------------- |
| `<header>`  | Đầu trang/section            | Google hiểu cấu trúc trang    | Screen reader không biết đâu là header |
| `<nav>`     | Menu điều hướng              | Google index links quan trọng | Links bị coi là content thường         |
| `<main>`    | Nội dung chính (1 lần/trang) | Google ưu tiên index          | Không biết đâu là nội dung chính       |
| `<article>` | Bài viết độc lập             | Rich snippets trên Google     | Mất cơ hội hiện đoạn trích             |
| `<section>` | Phân đoạn nội dung           | Cấu trúc rõ ràng              | Khó crawl, khó hiểu                    |
| `<aside>`   | Nội dung phụ                 | Google biết không quan trọng  | Content phụ được index như chính       |
| `<footer>`  | Chân trang                   | Thông tin bổ sung             | Screen reader đọc như content chính    |

---

## 6. Heading Hierarchy (H1-H6)

### 6.1. Quy tắc Heading

```html
<h1>Tiêu đề chính - CHỈ 1 H1/trang</h1>

<section>
  <h2>Phần 1</h2>
  <p>Nội dung...</p>

  <h3>Mục 1.1</h3>
  <p>Chi tiết...</p>

  <h3>Mục 1.2</h3>
  <p>Chi tiết...</p>
</section>

<section>
  <h2>Phần 2</h2>
  <p>Nội dung...</p>
</section>
```

### 6.2. Tại sao quan trọng?

| Yếu tố            | Tác động                              | Nếu dùng sai                            |
| ----------------- | ------------------------------------- | --------------------------------------- |
| **SEO**           | Google hiểu cấu trúc content          | Rank thấp, không hiện featured snippets |
| **Accessibility** | Screen reader điều hướng bằng heading | User mù không điều hướng được           |
| **UX**            | User scan nội dung nhanh              | Khó đọc, tỷ lệ bounce cao               |

---

## 7. Ảnh - Responsive Images

### 7.1. Loading Strategy: lazy vs eager

```html
<!-- ✅ Ảnh ABOVE THE FOLD (hero): loading="eager" hoặc không có -->
<img
  src="hero.jpg"
  srcset="hero-800.jpg 800w, hero-1600.jpg 1600w"
  sizes="100vw"
  alt="Hero banner"
  width="1600"
  height="600"
  fetchpriority="high"
/>

<!-- ✅ Ảnh BELOW THE FOLD: loading="lazy" -->
<img
  src="product.jpg"
  srcset="product-400.jpg 400w, product-800.jpg 800w"
  sizes="(max-width: 600px) 100vw, 50vw"
  alt="Sản phẩm"
  width="800"
  height="600"
  loading="lazy"
/>
```

#### **Giải thích:**

| Attribute              | Khi nào dùng                  | Công dụng              | Nếu dùng sai                       |
| ---------------------- | ----------------------------- | ---------------------- | ---------------------------------- |
| `loading="eager"`      | Ảnh hero, logo, ảnh đầu trang | Tải ngay lập tức       | Default behavior                   |
| `loading="lazy"`       | Ảnh dưới fold, gallery        | Chỉ tải khi scroll gần | Ảnh hero lazy → hiện chậm, LCP cao |
| `fetchpriority="high"` | LCP image (hero)              | Ưu tiên tải trước      | Ảnh hero load chậm                 |

**Ví dụ thực tế:**

- **Medium:** Hero image `fetchpriority="high"` + eager → LCP giảm 40%.
- **Instagram:** Feed images `loading="lazy"` → tiết kiệm 60% băng thông.

### 7.2. Thuộc tính đầy đủ

```html
<img
  src="fallback.jpg"
  srcset="small.jpg 400w, medium.jpg 800w, large.jpg 1200w"
  sizes="(max-width: 600px) 100vw, (max-width: 1024px) 50vw, 33vw"
  alt="Mô tả chi tiết cho SEO và screen reader"
  width="800"
  height="600"
  loading="lazy"
  decoding="async"
  fetchpriority="low"
/>
```

#### **Giải thích chi tiết:**

| Attribute          | Công dụng                        | Nếu không dùng                                  |
| ------------------ | -------------------------------- | ----------------------------------------------- |
| `srcset`           | Nhiều version ảnh                | Mobile tải ảnh desktop → chậm 10x               |
| `sizes`            | Kích thước hiển thị trong layout | Trình duyệt chọn ảnh sai                        |
| `alt`              | SEO + accessibility              | Google không biết ảnh gì, screen reader im lặng |
| `width/height`     | Tránh layout shift (CLS)         | Trang nhảy khi load ảnh → UX tệ                 |
| `loading="lazy"`   | Tải khi scroll gần               | Load hết ngay → chậm                            |
| `decoding="async"` | Decode ảnh không block rendering | Ảnh lớn block UI                                |
| `fetchpriority`    | Độ ưu tiên tải (high/low/auto)   | Ảnh không quan trọng tải trước ảnh hero         |

### 7.3. Định dạng ảnh hiện đại

```html
<picture>
  <source srcset="image.avif" type="image/avif" />
  <source srcset="image.webp" type="image/webp" />
  <img src="image.jpg" alt="Fallback" width="800" height="600" />
</picture>
```

**So sánh dung lượng:**

- JPG: 100KB (baseline)
- WebP: 70KB (-30%)
- AVIF: 50KB (-50%)

---

## 8. Video & Iframe

### 8.1. Video Responsive

```html
<video
  width="100%"
  height="auto"
  controls
  preload="metadata"
  poster="thumbnail.jpg"
  loading="lazy"
>
  <source src="video.webm" type="video/webm" />
  <source src="video.mp4" type="video/mp4" />
  <track
    kind="subtitles"
    src="subtitles-vi.vtt"
    srclang="vi"
    label="Tiếng Việt"
  />
  Trình duyệt không hỗ trợ video.
</video>
```

#### **Giải thích:**

| Attribute            | Công dụng                         | Nếu không dùng                   |
| -------------------- | --------------------------------- | -------------------------------- |
| `preload="metadata"` | Chỉ tải metadata, không tải video | `auto`: tải cả video → chậm      |
| `poster`             | Ảnh thumbnail trước khi play      | Màn hình đen → UX tệ             |
| `loading="lazy"`     | Tải khi scroll gần                | Tải hết video ngay → chậm        |
| `<track>`            | Phụ đề (accessibility)            | Người khiếm thính không xem được |

### 8.2. Iframe Responsive (YouTube)

```html
<div style="position: relative; padding-bottom: 56.25%; height: 0;">
  <iframe
    style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"
    src="https://www.youtube.com/embed/VIDEO_ID"
    title="Tiêu đề video"
    loading="lazy"
    allow="accelerometer; autoplay; encrypted-media; gyroscope"
    allowfullscreen
  ></iframe>
</div>
```

**Facade Pattern (tối ưu hơn):**

```html
<!-- Chỉ hiện ảnh thumbnail, click mới load iframe -->
<div class="youtube-facade" data-video-id="VIDEO_ID">
  <img
    src="https://img.youtube.com/vi/VIDEO_ID/maxresdefault.jpg"
    alt="Video thumbnail"
  />
  <button aria-label="Play video">▶</button>
</div>

<script>
  // Click → load iframe thật
  document.querySelectorAll(".youtube-facade").forEach((el) => {
    el.addEventListener("click", () => {
      const iframe = document.createElement("iframe");
      iframe.src = `https://www.youtube.com/embed/${el.dataset.videoId}?autoplay=1`;
      el.replaceWith(iframe);
    });
  });
</script>
```

**Lợi ích:**

- Tiết kiệm ~500KB/video chưa xem
- FCP, LCP giảm 50%

---

## 9. Links & Buttons - Accessibility

### 9.1. Link (`<a>`)

```html
<!-- ✅ Link thường -->
<a href="/products" aria-label="Xem tất cả sản phẩm">Sản phẩm</a>

<!-- ✅ Link mở tab mới -->
<a
  href="https://external.com"
  target="_blank"
  rel="noopener noreferrer"
  aria-label="Trang ngoài (mở tab mới)"
>
  External Link
</a>

<!-- ✅ Link download -->
<a href="/file.pdf" download="filename.pdf" aria-label="Tải xuống file PDF">
  Download
</a>

<!-- ✅ Link skip to main content (accessibility) -->
<a href="#main-content" class="skip-link"> Nhảy đến nội dung chính </a>
```

#### **Giải thích:**

| Attribute          | Công dụng                                         | Nếu không dùng                        |
| ------------------ | ------------------------------------------------- | ------------------------------------- |
| `href`             | URL đích                                          | Không hoạt động như link              |
| `target="_blank"`  | Mở tab mới                                        | Mở cùng tab (mất context)             |
| `rel="noopener"`   | Bảo mật: trang mới không truy cập `window.opener` | Lỗ hổng bảo mật                       |
| `rel="noreferrer"` | Không gửi referrer                                | Trang đích biết bạn đến từ đâu        |
| `download`         | Force download thay vì mở file                    | File mở trong browser                 |
| `aria-label`       | Mô tả cho screen reader                           | Screen reader chỉ đọc text trong link |

**Skip link (accessibility):**

```css
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  z-index: 100;
}

.skip-link:focus {
  top: 0; /* Hiện khi Tab focus */
}
```

### 9.2. Button (`<button>`)

```html
<!-- ✅ Button submit form -->
<button type="submit">Gửi</button>

<!-- ✅ Button thường -->
<button type="button" onclick="doSomething()">Click me</button>

<!-- ✅ Icon button -->
<button type="button" aria-label="Đóng modal">
  <svg>...</svg>
</button>

<!-- ✅ Button disabled -->
<button type="button" disabled aria-disabled="true">Loading...</button>
```

#### **Kích thước Touch Target:**

```css
button,
a {
  min-width: 44px;
  min-height: 44px; /* Google/Apple khuyến nghị */
  padding: 12px 24px;
  cursor: pointer;
}

/* Focus visible cho keyboard navigation */
button:focus-visible {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}
```

**Tại sao 44px?**

- Ngón tay trung bình ~44-48px
- Nếu <44px: bấm nhầm, frustration
- **WCAG 2.1 Level AAA:** 44x44px minimum

---

## 10. Form - Accessibility & UX

### 10.1. Input đầy đủ attributes

```html
<form action="/submit" method="POST" novalidate>
  <!-- Text input -->
  <label for="name"> Họ tên <span aria-label="bắt buộc">*</span> </label>
  <input
    type="text"
    id="name"
    name="name"
    autocomplete="name"
    required
    aria-required="true"
    aria-invalid="false"
    aria-describedby="name-error"
    placeholder="Nguyễn Văn A"
    style="font-size: 16px;"
  />
  <span id="name-error" class="error" role="alert" aria-live="polite"></span>

  <!-- Email -->
  <label for="email">Email</label>
  <input
    type="email"
    id="email"
    name="email"
    autocomplete="email"
    inputmode="email"
    required
    aria-required="true"
  />

  <!-- Phone -->
  <label for="phone">Số điện thoại</label>
  <input
    type="tel"
    id="phone"
    name="phone"
    autocomplete="tel"
    inputmode="tel"
    pattern="[0-9]{10}"
    title="Nhập 10 chữ số"
  />

  <!-- Password -->
  <label for="password">Mật khẩu</label>
  <input
    type="password"
    id="password"
    name="password"
    autocomplete="new-password"
    minlength="8"
    required
    aria-describedby="password-requirements"
  />
  <p id="password-requirements">Tối thiểu 8 ký tự, có chữ hoa và số</p>

  <!-- Select -->
  <label for="country">Quốc gia</label>
  <select id="country" name="country" autocomplete="country" required>
    <option value="">Chọn quốc gia</option>
    <option value="vn">Việt Nam</option>
    <option value="us">United States</option>
  </select>

  <!-- Checkbox -->
  <label>
    <input type="checkbox" name="terms" required aria-required="true" />
    Tôi đồng ý với <a href="/terms">điều khoản</a>
  </label>

  <!-- Submit -->
  <button type="submit">Gửi</button>
</form>
```

### 10.2. Giải thích Attributes

| Attribute              | Công dụng                                    | Nếu không dùng                     |
| ---------------------- | -------------------------------------------- | ---------------------------------- |
| `<label for="id">`     | Click label = focus input                    | Khó click trên mobile              |
| `autocomplete`         | Tự động điền từ browser                      | User phải gõ lại → frustration     |
| `inputmode`            | Bàn phím phù hợp (text, email, tel, numeric) | Bàn phím sai → gõ lâu              |
| `aria-required="true"` | Screen reader đọc "bắt buộc"                 | User khiếm thị không biết required |
| `aria-invalid`         | Thông báo lỗi cho screen reader              | Không biết field lỗi               |
| `aria-describedby`     | Liên kết input với text hướng dẫn/lỗi        | Screen reader không đọc lỗi        |
| `role="alert"`         | Thông báo lỗi quan trọng                     | Lỗi không được đọc ngay            |
| `aria-live="polite"`   | Screen reader đọc khi có thay đổi            | Không biết có lỗi mới              |
| `font-size: 16px`      | Tránh iOS zoom tự động                       | iOS zoom input <16px → UX tệ       |
| `novalidate`           | Tắt HTML5 validation (dùng custom)           | Validation default xấu             |

### 10.3. Autocomplete values

```html
<!-- Personal info -->
<input autocomplete="name" />
<!-- Họ tên đầy đủ -->
<input autocomplete="given-name" />
<!-- Tên -->
<input autocomplete="family-name" />
<!-- Họ -->
<input autocomplete="email" />
<input autocomplete="tel" />
<input autocomplete="tel-national" />
<!-- SĐT không mã quốc gia -->

<!-- Address -->
<input autocomplete="street-address" />
<input autocomplete="address-line1" />
<input autocomplete="address-line2" />
<input autocomplete="country" />
<input autocomplete="postal-code" />

<!-- Payment -->
<input autocomplete="cc-name" />
<!-- Tên trên thẻ -->
<input autocomplete="cc-number" />
<!-- Số thẻ -->
<input autocomplete="cc-exp" />
<!-- Ngày hết hạn -->
<input autocomplete="cc-csc" />
<!-- CVV -->

<!-- Auth -->
<input autocomplete="username" />
<input autocomplete="current-password" />
<!-- Login -->
<input autocomplete="new-password" />
<!-- Đăng ký/đổi MK -->
```

**Lợi ích:**

- User điền form nhanh 30-50%
- Conversion rate tăng 10-20%
- Mobile UX tốt hơn (ít gõ)

### 10.4. Hiển thị lỗi Accessible

```html
<!-- Inline error -->
<div class="form-group" :class="{ 'has-error': errors.email }">
  <label for="email">Email</label>
  <input
    type="email"
    id="email"
    :aria-invalid="errors.email ? 'true' : 'false'"
    aria-describedby="email-error"
  />
  <span
    id="email-error"
    class="error-message"
    role="alert"
    aria-live="polite"
    v-if="errors.email"
  >
    {{ errors.email }}
  </span>
</div>

<!-- Summary errors (đầu form) -->
<div role="alert" aria-live="assertive" v-if="hasErrors">
  <h2>Có {{ errorCount }} lỗi cần sửa:</h2>
  <ul>
    <li><a href="#email">Email không hợp lệ</a></li>
    <li><a href="#phone">Số điện thoại thiếu số</a></li>
  </ul>
</div>
```

**Quy tắc lỗi:**

- Hiện lỗi **ngay** khi blur (rời khỏi field)
- Màu đỏ + icon + text (không chỉ màu)
- Link lỗi → field tương ứng
- Screen reader đọc ngay (aria-live)

---

## 11. Performance Optimization - Advanced

### 11.1. Resource Hints

```html
<head>
  <!-- DNS Prefetch: Resolve DNS sớm -->
  <link rel="dns-prefetch" href="https://fonts.googleapis.com" />
  <link rel="dns-prefetch" href="https://www.google-analytics.com" />

  <!-- Preconnect: Kết nối sớm (DNS + TCP + TLS) -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />

  <!-- Preload: Tải tài nguyên quan trọng sớm -->
  <link
    rel="preload"
    href="/fonts/main.woff2"
    as="font"
    type="font/woff2"
    crossorigin
  />
  <link rel="preload" href="/hero.jpg" as="image" />
  <link rel="preload" href="/critical.css" as="style" />

  <!-- Prefetch: Tải trước tài nguyên page tiếp theo -->
  <link rel="prefetch" href="/next-page.html" />

  <!-- Prerender: Render trước page tiếp theo (cẩn thận) -->
  <link rel="prerender" href="/likely-next-page" />
</head>
```

#### **Giải thích:**

| Hint           | Khi nào dùng                            | Lợi ích                                | Cẩn thận                              |
| -------------- | --------------------------------------- | -------------------------------------- | ------------------------------------- |
| `dns-prefetch` | External domains (fonts, analytics)     | Giảm 20-120ms DNS lookup               | Dùng cho ≤6 domains                   |
| `preconnect`   | Critical external resources             | Giảm 100-500ms (DNS+TCP+TLS)           | Dùng cho ≤3 origins                   |
| `preload`      | Critical resources (fonts, hero images) | Load song song HTML, giảm render delay | Chỉ dùng cho tài nguyên chắc chắn cần |
| `prefetch`     | Trang user có thể vào tiếp theo         | Instant navigation                     | Lãng phí nếu user không vào           |

**Ví dụ thực tế:**

- **Google Fonts:** `preconnect` giảm 300ms load font.
- **Amazon:** `preload` hero image → LCP giảm 40%.

### 11.2. Critical CSS Inline

```html
<head>
  <!-- Critical CSS inline trong <style> -->
  <style>
    /* Above-the-fold styles */
    body {
      margin: 0;
      font-family: sans-serif;
    }
    .hero {
      min-height: 100vh;
      background: #000;
    }
    .header {
      height: 60px;
      position: sticky;
      top: 0;
    }
  </style>

  <!-- Non-critical CSS load async -->
  <link
    rel="preload"
    href="/main.css"
    as="style"
    onload="this.onload=null;this.rel='stylesheet'"
  />
  <noscript><link rel="stylesheet" href="/main.css" /></noscript>
</head>
```

**Cách tách Critical CSS:**

1. Dùng tool: Critical, Critters, PurgeCSS
2. Inline CSS cho above-the-fold
3. Load CSS còn lại async

**Lợi ích:**

- FCP giảm 50%
- Eliminate render-blocking CSS

### 11.3. Defer/Async JavaScript

```html
<!-- ❌ Blocking: Download + Execute block HTML parsing -->
<script src="app.js"></script>

<!-- ✅ Async: Download parallel, execute ngay (không đảm bảo thứ tự) -->
<script src="analytics.js" async></script>

<!-- ✅ Defer: Download parallel, execute sau khi HTML parsed (đảm bảo thứ tự) -->
<script src="main.js" defer></script>

<!-- ✅ Module: Default defer -->
<script type="module" src="app.mjs"></script>
```

**Khi nào dùng:**

- **Defer:** Scripts phụ thuộc DOM (main app)
- **Async:** Scripts độc lập (analytics, ads)
- **Blocking:** Chỉ dùng cho critical scripts (hiếm)

### 11.4. Lazy Load Third-Party Scripts

```html
<!-- Analytics chỉ load khi user scroll/click -->
<script>
  // Chờ user tương tác
  let loaded = false;
  const loadAnalytics = () => {
    if (loaded) return;
    loaded = true;

    const script = document.createElement("script");
    script.src = "https://www.google-analytics.com/analytics.js";
    script.async = true;
    document.head.appendChild(script);
  };

  // Load khi scroll hoặc click
  ["scroll", "click", "touchstart"].forEach((event) => {
    window.addEventListener(event, loadAnalytics, {
      once: true,
      passive: true,
    });
  });

  // Hoặc timeout 3s
  setTimeout(loadAnalytics, 3000);
</script>
```

**Lợi ích:**

- FCP, LCP giảm 30-50%
- Main thread tự do hơn
- TTI (Time to Interactive) giảm 40%

### 11.5. Font Optimization

```html
<head>
  <!-- Preconnect Google Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />

  <!-- Load font với display=swap -->
  <link
    href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap"
    rel="stylesheet"
  />
</head>
```

```css
/* Self-hosted font với font-display */
@font-face {
  font-family: "Custom Font";
  src: url("/fonts/custom.woff2") format("woff2");
  font-weight: 400;
  font-style: normal;
  font-display: swap; /* Hiện fallback font trước, swap khi custom font load xong */
}
```

**Font-display values:**

- `swap`: Hiện fallback ngay, swap khi font load (khuyến nghị)
- `fallback`: Đợi 100ms, nếu chưa load dùng fallback, swap trong 3s
- `optional`: Chỉ dùng font custom nếu load <100ms, không thì bỏ qua
- `block`: Block 3s, đợi font (KHÔNG nên dùng)

**Lợi ích:**

- `swap` tránh FOIT (Flash of Invisible Text)
- CLS thấp hơn
- FCP nhanh hơn 50%

### 11.6. Unused CSS/JS Removal

```javascript
// Code splitting (Webpack/Vite)
// Thay vì:
import HeavyLibrary from "heavy-library";

// Dùng dynamic import:
button.addEventListener("click", async () => {
  const { HeavyLibrary } = await import("heavy-library");
  new HeavyLibrary();
});
```

**Tools:**

- **PurgeCSS:** Xóa CSS không dùng
- **Tree-shaking:** Webpack/Rollup tự động xóa code không dùng
- **Code splitting:** Chia bundle nhỏ, chỉ load khi cần

**Lợi ích:**

- Bundle size giảm 50-70%
- Initial load nhanh 40%

### 11.7. Print-Only CSS

```html
<!-- CSS cho print không block render -->
<link rel="stylesheet" href="print.css" media="print" />
```

```css
/* Hoặc trong CSS file */
@media print {
  /* Print styles */
  .no-print {
    display: none;
  }
}
```

### 11.8. JS Execute Flow & Optimization

**HTML Parsing Flow:**

```
1. HTML parsing bắt đầu
   ↓
2. Gặp <script>
   - Blocking: DỪNG parse HTML → Download + Execute → Tiếp tục parse
   - Async: Parse tiếp → Download parallel → Execute ngay khi xong
   - Defer: Parse tiếp → Download parallel → Execute sau khi parse xong
   ↓
3. HTML parsed xong → DOMContentLoaded event
   ↓
4. Images/CSS load xong → Load event
```

**Chiến lược tối ưu:**

```html
<head>
  <!-- Critical inline JS (hiếm) -->
  <script>
    // Feature detection, polyfill
  </script>

  <!-- Preload critical chunks -->
  <link rel="modulepreload" href="/critical.js" />
</head>

<body>
  <!-- Content -->

  <!-- Main app: defer -->
  <script src="/main.js" defer></script>

  <!-- Analytics: async -->
  <script src="https://analytics.com/script.js" async></script>

  <!-- Non-critical: lazy load -->
  <script>
    // Load khi idle
    requestIdleCallback(() => {
      import("./heavy-feature.js");
    });
  </script>
</body>
```

---

## 12. Typography - Chữ dễ đọc Mobile

```css
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  font-size: 16px;
  line-height: 1.5;
}

h1 {
  font-size: 28px;
  line-height: 1.2;
}
h2 {
  font-size: 24px;
  line-height: 1.3;
}
h3 {
  font-size: 20px;
  line-height: 1.4;
}
p {
  font-size: 16px;
  line-height: 1.5;
}

@media (min-width: 768px) {
  h1 {
    font-size: 36px;
  }
  p {
    font-size: 18px;
  }
}
```

**Quy tắc:**

- Font-size ≥16px: Dễ đọc mobile
- Line-height 1.5: Không gian thoải mái
- System fonts: Load ngay, không chờ

---

## 13. Accessibility - Keyboard Navigation

### 13.1. Focus Management

```css
/* Focus visible cho keyboard users */
*:focus-visible {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}

/* Remove outline cho mouse users */
*:focus:not(:focus-visible) {
  outline: none;
}

/* Custom focus */
button:focus-visible {
  box-shadow: 0 0 0 3px rgba(0, 102, 204, 0.3);
}
```

### 13.2. Skip Navigation

```html
<a href="#main-content" class="skip-link">Nhảy đến nội dung chính</a>

<nav>...</nav>

<main id="main-content">
  <!-- Content -->
</main>
```

```css
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  background: #000;
  color: #fff;
  padding: 8px;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
```

### 13.3. ARIA Roles & States

```html
<!-- Custom dropdown -->
<button
  aria-haspopup="true"
  aria-expanded="false"
  aria-controls="dropdown-menu"
>
  Menu
</button>

<ul id="dropdown-menu" role="menu" hidden>
  <li role="menuitem"><a href="#">Item 1</a></li>
  <li role="menuitem"><a href="#">Item 2</a></li>
</ul>

<!-- Tab interface -->
<div role="tablist">
  <button role="tab" aria-selected="true" aria-controls="panel1">Tab 1</button>
  <button role="tab" aria-selected="false" aria-controls="panel2">Tab 2</button>
</div>

<div role="tabpanel" id="panel1">Content 1</div>
<div role="tabpanel" id="panel2" hidden>Content 2</div>

<!-- Live region (notifications) -->
<div role="status" aria-live="polite" aria-atomic="true">
  <!-- Dynamic content -->
</div>
```

---

## 14. Performance Metrics & Tools

### 14.1. Core Web Vitals

| Metric                             | Mục tiêu | Cách đo            | Cải thiện                       |
| ---------------------------------- | -------- | ------------------ | ------------------------------- |
| **LCP** (Largest Contentful Paint) | <2.5s    | PageSpeed Insights | Optimize ảnh, preload, CDN      |
| **FID** (First Input Delay)        | <100ms   | RUM tools          | Giảm JS, defer/async            |
| **CLS** (Cumulative Layout Shift)  | <0.1     | PageSpeed Insights | Set width/height, reserve space |

### 14.2. Tools kiểm tra

| Tool                         | Mục đích                        | URL                     |
| ---------------------------- | ------------------------------- | ----------------------- |
| **Lighthouse**               | Performance, SEO, Accessibility | Chrome DevTools         |
| **PageSpeed Insights**       | Core Web Vitals, suggestions    | pagespeed.web.dev       |
| **WebPageTest**              | Detailed waterfall              | webpagetest.org         |
| **Chrome DevTools Coverage** | Unused CSS/JS                   | DevTools → Coverage tab |
| **WAVE**                     | Accessibility checker           | wave.webaim.org         |

---

## 15. Checklist

### HTML

- ✅ `<meta name="viewport">` đã thêm
- ✅ Một thẻ `<h1>` duy nhất, headings đúng thứ tự
- ✅ Semantic tags: `<header>`, `<nav>`, `<main>`, `<article>`, `<footer>`
- ✅ Open Graph + Twitter Cards
- ✅ JSON-LD structured data
- ✅ Robots.txt + sitemap.xml
- ✅ Canonical URLs

### Images

- ✅ Ảnh hero: `fetchpriority="high"`, eager/no lazy
- ✅ Ảnh below fold: `loading="lazy"`
- ✅ Tất cả ảnh: `alt`, `width`, `height`
- ✅ `srcset` + `sizes` cho responsive
- ✅ WebP/AVIF với fallback
- ✅ `decoding="async"` cho ảnh lớn

### Forms

- ✅ Input ≥16px, có `<label>`
- ✅ `autocomplete` đầy đủ
- ✅ `inputmode` phù hợp
- ✅ `aria-required`, `aria-invalid`, `aria-describedby`
- ✅ Lỗi có `role="alert"`, `aria-live="polite"`

### Links & Buttons

- ✅ Min 44x44px touch target
- ✅ `aria-label` cho icon buttons
- ✅ Links mở tab mới: `rel="noopener noreferrer"`
- ✅ Focus visible cho keyboard
- ✅ Skip navigation link

### Performance

- ✅ Critical CSS inline
- ✅ CSS/JS defer/async
- ✅ Preconnect fonts
- ✅ `font-display: swap`
- ✅ Lazy load third-party scripts
- ✅ LCP <2.5s, CLS <0.1, FID <100ms
- ✅ Remove unused CSS/JS

### Accessibility

- ✅ Keyboard navigation hoạt động
- ✅ Focus visible
- ✅ ARIA roles/states khi cần
- ✅ Color contrast ≥4.5:1
- ✅ Screen reader friendly

---

## 16. Kết luận

**Mobile-First = Chuẩn mực hiện đại.**

> "Optimize for the 60% (mobile), then enhance for desktop."

**3 trụ cột:**

1. **Performance:** Resource hints, lazy load, code splitting
2. **SEO:** Semantic HTML, meta tags, JSON-LD, sitemap
3. **Accessibility:** ARIA, keyboard nav, screen reader support