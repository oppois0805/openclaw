# 🎯 HTML Code Review 完整審查提示模版

請作為專業前端開發者，依照以下詳細準則對 HTML 代碼進行全面審查並提出改進建議：

---

## 📋 審查總覽

### 🔍 審查範圍

- [ ] HTML5 語義化與結構
- [ ] 可訪問性 (A11y) 合規
- [ ] SEO 優化與元數據
- [ ] 性能與載入優化
- [ ] 跨瀏覽器相容性
- [ ] 安全性考量
- [ ] 代碼品質與維護性

---

## 🏗️ **1. HTML 結構與語義化 (Structure & Semantics)**

### 📄 文檔結構

- **DOCTYPE 聲明**：是否使用 HTML5 DOCTYPE？

  ```html
  <!DOCTYPE html>
  ```

- **基本結構**：是否包含完整的基本結構？

  ```html
  <!DOCTYPE html>
  <html lang="zh-TW">
    <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>頁面標題</title>
    </head>
    <body>
      <!-- 內容 -->
    </body>
  </html>
  ```

- **語言屬性**：是否正確設定 `lang` 屬性？
- **字符編碼**：是否聲明 UTF-8 編碼？

### 🏷️ 語義化標籤

- **結構標籤**：是否適當使用 HTML5 語義化標籤？

  ```html
  <!-- ✅ 良好的語義化結構 -->
  <header>
    <nav>
      <ul>
        <li><a href="/">首頁</a></li>
        <li><a href="/about">關於我們</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <article>
      <header>
        <h1>文章標題</h1>
        <time datetime="2024-01-01">2024年1月1日</time>
      </header>
      <section>
        <h2>段落標題</h2>
        <p>內容...</p>
      </section>
    </article>

    <aside>
      <section>
        <h3>相關文章</h3>
        <ul>
          ...
        </ul>
      </section>
    </aside>
  </main>

  <footer>
    <p>&copy; 2024 公司名稱</p>
  </footer>
  ```

- **內容標籤**：是否正確使用內容相關標籤？

  ```html
  <!-- 文本語義 -->
  <strong>重要內容</strong>
  <em>強調內容</em>
  <mark>標記內容</mark>
  <abbr title="HyperText Markup Language">HTML</abbr>
  <cite>引用來源</cite>
  <code>程式碼</code>
  <kbd>鍵盤輸入</kbd>
  <samp>範例輸出</samp>

  <!-- 列表與定義 -->
  <dl>
    <dt>術語</dt>
    <dd>定義</dd>
  </dl>
  ```

### 📊 標題層級

- **h1-h6 使用**：是否正確使用標題層級？

  ```html
  <!-- ✅ 正確的標題層級 -->
  <h1>主標題</h1>
  <h2>章節標題</h2>
  <h3>子段落標題</h3>
  <h4>詳細段落</h4>

  <!-- ❌ 跳躍層級 -->
  <h1>主標題</h1>
  <h3>直接跳到 h3</h3>
  <!-- 錯誤 -->
  ```

- **標題唯一性**：每頁是否只有一個 `<h1>`？
- **內容層次**：標題是否正確反映內容層次？

---

## ♿ **2. 可訪問性 (Accessibility)**

### 🏷️ ARIA 與標籤

- **ARIA 標籤**：是否適當使用 ARIA 屬性？

  ```html
  <!-- 區域標籤 -->
  <nav aria-label="主要導航">...</nav>
  <section aria-labelledby="news-heading">
    <h2 id="news-heading">最新消息</h2>
  </section>

  <!-- 狀態與屬性 -->
  <button aria-expanded="false" aria-controls="menu">選單</button>
  <div id="menu" aria-hidden="true">...</div>

  <!-- 表單標籤 -->
  <label for="email">電子郵件地址</label>
  <input type="email" id="email" required aria-describedby="email-help" />
  <div id="email-help">請輸入有效的電子郵件地址</div>
  ```

- **role 屬性**：是否正確使用 role 屬性？
  ```html
  <div role="tablist">
    <button role="tab" aria-selected="true">標籤 1</button>
    <button role="tab" aria-selected="false">標籤 2</button>
  </div>
  <div role="tabpanel">內容面板</div>
  ```

### 🖼️ 圖片與媒體

- **alt 屬性**：是否為所有圖片提供適當的 alt 文字？

  ```html
  <!-- ✅ 內容圖片 -->
  <img src="chart.png" alt="2024年銷售成長趨勢圖，顯示持續上升趨勢" />

  <!-- ✅ 裝飾性圖片 -->
  <img src="decoration.png" alt="" role="presentation" />

  <!-- ✅ 功能性圖片 -->
  <button>
    <img src="search.svg" alt="搜尋" />
  </button>
  ```

- **圖片標題**：是否使用 `<figure>` 和 `<figcaption>`？
  ```html
  <figure>
    <img src="diagram.png" alt="系統架構圖" />
    <figcaption>圖 1: 微服務架構概覽</figcaption>
  </figure>
  ```

### ⌨️ 鍵盤導航

- **tabindex**：是否正確設定 tabindex？

  ```html
  <!-- 正常導航順序 -->
  <button>按鈕 1</button>
  <button>按鈕 2</button>

  <!-- 跳過導航 -->
  <a href="#main-content" class="skip-link">跳至主要內容</a>

  <!-- 自定義組件 -->
  <div role="button" tabindex="0">自定義按鈕</div>
  ```

- **focus 管理**：是否考慮鍵盤使用者的導航體驗？

### 🎯 表單可訪問性

- **標籤關聯**：是否正確關聯 label 與表單控件？

  ```html
  <!-- 明確關聯 -->
  <label for="username">使用者名稱</label>
  <input type="text" id="username" name="username" />

  <!-- 隱式關聯 -->
  <label>
    密碼
    <input type="password" name="password" />
  </label>

  <!-- 群組標籤 -->
  <fieldset>
    <legend>聯絡方式</legend>
    <label><input type="radio" name="contact" value="email" />電子郵件</label>
    <label><input type="radio" name="contact" value="phone" />電話</label>
  </fieldset>
  ```

- **錯誤處理**：是否提供清晰的錯誤訊息？
  ```html
  <label for="email">電子郵件</label>
  <input
    type="email"
    id="email"
    aria-invalid="true"
    aria-describedby="email-error"
  />
  <div id="email-error" role="alert">請輸入有效的電子郵件地址</div>
  ```

---

## 🔍 **3. SEO 優化 (Search Engine Optimization)**

### 📊 Meta 標籤

- **基本 meta**：是否包含完整的基本 meta 標籤？
  ```html
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>具體、描述性的頁面標題 - 品牌名稱</title>
    <meta name="description" content="150-160字符的頁面描述，包含關鍵字" />
    <meta name="keywords" content="關鍵字1, 關鍵字2, 關鍵字3" />
    <meta name="author" content="作者或公司名稱" />
    <link rel="canonical" href="https://example.com/current-page" />
  </head>
  ```

### 🌐 Open Graph 與社群媒體

- **Open Graph**：是否設定 Open Graph 標籤？

  ```html
  <!-- Open Graph -->
  <meta property="og:title" content="頁面標題" />
  <meta property="og:description" content="頁面描述" />
  <meta property="og:image" content="https://example.com/image.jpg" />
  <meta property="og:url" content="https://example.com/current-page" />
  <meta property="og:type" content="website" />
  <meta property="og:site_name" content="網站名稱" />

  <!-- Twitter Cards -->
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content="頁面標題" />
  <meta name="twitter:description" content="頁面描述" />
  <meta name="twitter:image" content="https://example.com/image.jpg" />
  ```

### 🔗 連結與導航

- **內部連結**：是否使用描述性的連結文字？

  ```html
  <!-- ❌ 不好 -->
  <a href="/products">點擊這裡</a>
  <a href="/contact">更多</a>

  <!-- ✅ 良好 -->
  <a href="/products">查看我們的產品目錄</a>
  <a href="/contact">聯絡我們的客服團隊</a>
  ```

- **麵包屑**：是否提供結構化的麵包屑導航？
  ```html
  <nav aria-label="麵包屑導航">
    <ol itemscope itemtype="https://schema.org/BreadcrumbList">
      <li
        itemprop="itemListElement"
        itemscope
        itemtype="https://schema.org/ListItem"
      >
        <a itemprop="item" href="/"><span itemprop="name">首頁</span></a>
        <meta itemprop="position" content="1" />
      </li>
      <li
        itemprop="itemListElement"
        itemscope
        itemtype="https://schema.org/ListItem"
      >
        <a itemprop="item" href="/products"
          ><span itemprop="name">產品</span></a
        >
        <meta itemprop="position" content="2" />
      </li>
      <li aria-current="page">當前頁面</li>
    </ol>
  </nav>
  ```

### 📋 結構化資料

- **Schema.org**：是否使用結構化資料標記？

  ```html
  <!-- 文章標記 -->
  <article itemscope itemtype="https://schema.org/Article">
    <h1 itemprop="headline">文章標題</h1>
    <meta itemprop="author" content="作者名稱" />
    <time itemprop="datePublished" datetime="2024-01-01">2024年1月1日</time>
    <div itemprop="articleBody">文章內容...</div>
  </article>

  <!-- 組織標記 -->
  <div itemscope itemtype="https://schema.org/Organization">
    <span itemprop="name">公司名稱</span>
    <span
      itemprop="address"
      itemscope
      itemtype="https://schema.org/PostalAddress"
    >
      <span itemprop="streetAddress">地址</span>
      <span itemprop="addressLocality">城市</span>
    </span>
  </div>
  ```

---

## ⚡ **4. 性能優化 (Performance)**

### 🖼️ 圖片優化

- **圖片格式**：是否使用適當的圖片格式？

  ```html
  <!-- 現代圖片格式 -->
  <picture>
    <source srcset="image.avif" type="image/avif" />
    <source srcset="image.webp" type="image/webp" />
    <img src="image.jpg" alt="描述" loading="lazy" />
  </picture>

  <!-- 響應式圖片 -->
  <img
    srcset="small.jpg 480w, medium.jpg 800w, large.jpg 1200w"
    sizes="(max-width: 480px) 100vw, (max-width: 800px) 50vw, 25vw"
    src="medium.jpg"
    alt="描述"
    loading="lazy"
  />
  ```

- **懶載入**：是否使用 `loading="lazy"`？
- **尺寸屬性**：是否設定 `width` 和 `height`？

### 📦 資源載入

- **Critical Resources**：是否優化關鍵資源載入？

  ```html
  <!-- DNS 預解析 -->
  <link rel="dns-prefetch" href="//fonts.googleapis.com" />

  <!-- 資源預載 -->
  <link rel="preload" href="critical.css" as="style" />
  <link rel="preload" href="hero-image.jpg" as="image" />

  <!-- 字體預載 -->
  <link
    rel="preload"
    href="font.woff2"
    as="font"
    type="font/woff2"
    crossorigin
  />

  <!-- 模組預載 -->
  <link rel="modulepreload" href="app.js" />
  ```

- **非同步載入**：是否適當使用 `async` 和 `defer`？

  ```html
  <!-- 非阻塞腳本 -->
  <script src="analytics.js" async></script>
  <script src="app.js" defer></script>

  <!-- 內聯關鍵 CSS -->
  <style>
    /* Critical CSS here */
  </style>
  <link
    rel="preload"
    href="non-critical.css"
    as="style"
    onload="this.onload=null;this.rel='stylesheet'"
  />
  ```

### 🔗 第三方服務

- **外部資源**：是否最小化第三方依賴？
- **CDN 使用**：是否使用 CDN 載入資源？
- **版本控制**：是否鎖定第三方資源版本？

---

## 🛡️ **5. 安全性 (Security)**

### 🔒 內容安全政策

- **CSP Header**：是否設定適當的 CSP？
  ```html
  <meta
    http-equiv="Content-Security-Policy"
    content="default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';"
  />
  ```

### 🔗 連結安全

- **外部連結**：是否使用適當的 rel 屬性？

  ```html
  <!-- 外部連結 -->
  <a href="https://external-site.com" rel="noopener noreferrer" target="_blank">
    外部網站
  </a>

  <!-- UGC 連結 -->
  <a href="https://user-generated-link.com" rel="nofollow noopener noreferrer">
    使用者提供的連結
  </a>
  ```

### 📝 表單安全

- **輸入驗證**：是否使用適當的輸入類型和驗證？
  ```html
  <form>
    <input
      type="email"
      required
      pattern="[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$"
    />
    <input type="tel" pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}" />
    <input type="url" required />
    <input type="number" min="1" max="100" />
    <textarea maxlength="500"></textarea>
  </form>
  ```

---

## 🎨 **6. 代碼品質與維護性 (Code Quality)**

### 📝 代碼格式

- **縮排**：是否使用一致的縮排（2 或 4 空格）？
- **換行**：是否適當使用換行提升可讀性？
  ```html
  <!-- ✅ 良好格式 -->
  <article
    class="post"
    data-id="123"
    itemscope
    itemtype="https://schema.org/Article"
  >
    <header class="post-header">
      <h1 class="post-title" itemprop="headline">標題</h1>
    </header>
  </article>
  ```

### 🏷️ 屬性順序

- **屬性排序**：是否使用一致的屬性排序？
  ```html
  <!-- 建議順序：class, id, name, data-*, src/href, title, alt, role, aria-* -->
  <img
    class="hero-image"
    id="main-image"
    data-lightbox="gallery"
    src="image.jpg"
    alt="描述"
    role="img"
    aria-describedby="image-caption"
  />
  ```

### 💬 註解與文檔

- **註解策略**：是否適當使用註解？

  ```html
  <!-- =============================================================================
       頁首組件 (Header Component)
       ============================================================================= -->
  <header class="site-header">
    <!-- 主要導航 -->
    <nav class="main-nav" aria-label="主要導航">
      <!-- ... -->
    </nav>

    <!-- 搜尋功能 -->
    <div class="search-container">
      <!-- TODO: 實作進階搜尋功能 -->
      <form class="search-form">
        <!-- ... -->
      </form>
    </div>
  </header>

  <!-- =============================================================================
       主要內容區域 (Main Content)
       ============================================================================= -->
  ```

### 🧹 代碼清理

- **未使用元素**：是否移除未使用的 HTML 元素？
- **冗餘標籤**：是否簡化不必要的嵌套？
- **過時屬性**：是否移除過時的 HTML 屬性？

---

## 🌍 **7. 跨瀏覽器相容性 (Cross-browser Compatibility)**

### 📱 響應式設計

- **Viewport Meta**：是否設定正確的 viewport？

  ```html
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  ```

- **媒體查詢**：是否考慮不同裝置的顯示？

### 🔧 Polyfills

- **功能檢測**：是否為舊瀏覽器提供 polyfills？
  ```html
  <!-- 條件載入 -->
  <script>
    if (!window.fetch) {
      document.write('<script src="fetch-polyfill.js"><\/script>');
    }
  </script>
  ```

### 🧪 測試範圍

- **主流瀏覽器**：Chrome、Firefox、Safari、Edge
- **行動瀏覽器**：iOS Safari、Android Chrome
- **舊版瀏覽器**：IE11（如需支援）

---

## 🧰 **8. 工具與驗證 (Tools & Validation)**

### ✅ HTML 驗證

- **W3C Validator**：是否通過 HTML 驗證？
- **語法檢查**：是否有語法錯誤？
- **標籤閉合**：是否正確閉合所有標籤？

### 🔍 可訪問性測試

- **axe-core**：是否通過自動化 a11y 測試？
- **WAVE**：是否通過 WAVE 檢測？
- **螢幕閱讀器**：是否測試螢幕閱讀器相容性？

### 📊 性能測試

- **Lighthouse**：是否通過 Lighthouse 審計？
- **PageSpeed Insights**：是否優化載入速度？
- **Core Web Vitals**：是否滿足 Web 核心指標？

---

## 🧠 **審查結果格式**

### 📋 問題分類

請將發現的問題按以下優先級分類：

#### 🔴 **Critical (關鍵)**

- 語法錯誤導致頁面無法正常顯示
- 嚴重的可訪問性問題
- 安全漏洞

#### 🟡 **Major (重要)**

- SEO 優化問題
- 性能影響
- 語義化不當

#### 🔵 **Minor (次要)**

- 代碼風格問題
- 輕微優化建議
- 最佳實踐建議

### 🔧 **建議格式**

```html
<!-- 🔴 Critical: 可訪問性問題 -->
<!-- 📝 問題：圖片缺少 alt 屬性 -->
<!-- 🔧 建議：為所有內容圖片添加描述性 alt 文字 -->
<img src="chart.png" alt="2024年第一季銷售成長20%的趨勢圖" />

<!-- 🟡 Major: SEO 問題 -->
<!-- 📝 問題：缺少 meta description -->
<!-- 🔧 建議：添加150-160字符的頁面描述 -->
<meta
  name="description"
  content="我們提供專業的網頁設計服務，幫助企業建立優質的線上形象..."
/>

<!-- 🔵 Minor: 語義化改進 -->
<!-- 📝 問題：使用 div 作為按鈕 -->
<!-- 🔧 建議：使用 button 元素提升語義和可訪問性 -->
<button type="button" onclick="toggleMenu()">開啟選單</button>
```

### 📊 **總結報告**

請提供：

1. **整體評分**：A-F 等級評估
2. **主要優點**：HTML 代碼的優秀之處
3. **改進重點**：最需要關注的 3-5 個領域
4. **行動計劃**：具體的改進步驟建議

---

## 🎯 **特殊場景考慮**

### 📱 **PWA 應用**

- 是否包含 PWA 必要的 meta 標籤？
- 是否設定 manifest.json 連結？
- 是否支援離線功能標記？

### 🛒 **電商網站**

- 是否包含結構化商品資料？
- 是否設定正確的購物車標記？
- 是否優化商品列表的可訪問性？

### 📰 **內容網站**

- 是否正確標記文章結構？
- 是否包含作者和發布時間資訊？
- 是否設定適當的內容分類？

### 🏢 **企業網站**

- 是否包含聯絡資訊的結構化資料？
- 是否設定正確的組織標記？
- 是否優化本地 SEO 標記？

---

**請基於以上全面的檢查清單，對提供的 HTML 代碼進行詳細審查，並按照指定格式提供具體的改進建議。特別關注語義化、可訪問性、SEO 優化和性能表現。**
