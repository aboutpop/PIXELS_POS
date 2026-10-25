💡 คำแนะนำเพิ่มเติมหากต้องการสั่งให้ AI เขียนแอปนี้จริงเนื่องจากเนื้องานของระบบมีขนาดใหญ่ (แอปพลิเคชันระดับ Production) หากสั่งให้ AI สร้างไฟล์ทั้งหมดทีเดียวใน Prompt เดียว AI อาจเกิดอาการ context ตกหล่นหรือเขียนโค้ดสั้นลงเพื่อประหยัด Tokenวิธีที่ดีที่สุดคือการสั่งทำเป็นเฟส (Phased Generation):Phase 1: ให้ AI สร้างโครงสร้างโปรเจกต์, index.html, js/config.js, viewLoader.js และสคริปต์ SQL Migration สำหรับ Supabase   Phase 2: ให้ AI สร้าง dbHelper.js, barcodeHelper.js, productMatcher.js และ Helper Utilities ทั้งหมด   Phase 3: ให้ AI สร้างหน้าขาย POS และ Payment (views/pos.html, pos.js, views/payment.html, posPayment.js)   Phase 4: ให้ AI สร้างหน้า Inventory, Stock-In AI, Dashboard และ Settings   เพียงเท่านี้ คุณจะได้เว็บแอป Pixel POS & AI Inventory ที่ทำงานได้ตรงตามสเปกเดิมทุกประการครับ! 🕹️

 2. สิ่งที่ AI ต้อง "สร้างขึ้นมาทดแทน" (Minor Gaps)เนื่องจากไฟล์เอกสารทั้ง 3 ฉบับเน้นอธิบาย สเปก ตรรกะ และโครงสร้าง แต่ไม่ได้ฝังไฟล์มีเดีย Binary มาด้วย AI จึงจะต้องสร้างส่วนนี้ขึ้นมาใหม่:ไฟล์เสียงพูดภาษาไทย 25 ไฟล์ (.wav):เอกสารบอกชื่อไฟล์และลำดับการต่อประโยคไว้อย่างแม่นยำ แต่ไม่มีไฟล์ .wav จริง AI อาจเขียนโค้ด Web Audio Engine แบบสังเคราะห์เสียง หรือใช้ Web Speech Synthesis API มาทำหน้าที่แทน   ไฟล์ Web Font & Pixel Icons:ไฟล์ Font Awesome (fa-solid-900.woff2) และภาพ SVG พิกเซล (pixel-coin.svg, pixel-calc.svg) AI จะสร้าง SVG มาแทนที่ให้อัตโนมัติ   Google Apps Script (Code.gs):เอกสารระบุสเปกการทำงานไว้ชัดเจน (รับ Base64 ➔ บันทึกลง Google Drive โฟลเดอร์ POS-Images ➔ คืนค่า Direct CDN URL lh3.googleusercontent.com) AI จะเขียนโค้ด Code.gs ตัวนี้ขึ้นมาให้ผู้ใช้นำไปแปะใน Google Apps Script ได้ทันที
    
# [👤 Human System Encyclopedia & Setup Guide | ห้าม AI โหลดตอนเริ่มแชทใหม่]

> ⚠️ **คำสั่งสำคัญสำหรับ AI Agents (Strict AI Cold-Start Prohibition):**  
> **ห้ามอ่านไฟล์นี้ทั้งหมดเมื่อเริ่มต้นเซสชันใหม่** เพื่อประหยัด Token โควตาบริบท ให้อ่านเฉพาะ [`.agents/WRAPUP.md`](file:///e:/xampp/htdocs/myproject/about_pos/.agents/WRAPUP.md) (ลำดับที่ 1) และ [`AGENTS.md`](file:///e:/xampp/htdocs/myproject/about_pos/AGENTS.md) (ลำดับที่ 2) เท่านั้น  
> *(อนุญาตให้ AI ใช้เครื่องมือค้นหาเฉพาะหัวข้อใน `README.md` เมื่อต้องแก้ไขส่วนที่เกี่ยวข้องกับเอกสารคู่มือโดยตรงเท่านั้น)*

---

# 🕹️ Pixel POS & AI Inventory (Retro Arcade Point of Sale)

> **ระบบบริหารงานขายหน้าร้านและจัดการคลังสินค้าอัจฉริยะ (Retro Arcade Style POS & AI Stock-In Terminal)**  
> พัฒนาด้วยสถาปัตยกรรม **Modern Client-Side Modular Architecture (Native ES6+ Modules)** ผสานฐานข้อมูลคลาวด์ **Supabase (PostgreSQL 15+)**, แคชออฟไลน์ในเครื่อง **IndexedDB (Dexie.js)**, และปัญญาประดิษฐ์ **Google Gemini Vision API** สำหรับสแกนใบเสร็จ/บิลส่งของเข้าสต็อกอัตโนมัติ ออกแบบส่วนติดต่อผู้ใช้สไตล์ Retro 8-bit/16-bit Arcade พร้อมการรองรับหน้าจอสัมผัส แท็บเล็ต และ PWA เต็มรูปแบบ

---

## 📑 สารบัญ (Table of Contents)
1. [ภาพรวมและปรัชญาการออกแบบ (System Overview)](#1-ภาพรวมและปรัชญาการออกแบบ-system-overview)
2. [ภาษาและเทคโนโลยีหลักที่ใช้ (Tech Stack & Core Languages)](#2-ภาษาและเทคโนโลยีหลักที่ใช้-tech-stack--core-languages)
3. [สถาปัตยกรรมระบบและการไหลของข้อมูล (System Architecture)](#3-สถาปัตยกรรมระบบและการไหลของข้อมูล-system-architecture)
4. [โครงสร้างไดเรกทอรีของโปรเจกต์ (Project Structure)](#4-โครงสร้างไดเรกทอรีของโปรเจกต์-project-structure)
5. [โครงสร้างฐานข้อมูลและสคริปต์ SQL (Database Schemas & SQL)](#5-โครงสร้างฐานข้อมูลและสคริปต์-sql-database-schemas--sql)
6. [เจาะลึกทุกหน้าจอและส่วนติดต่อผู้ใช้ (Views & UI Deep Dive)](#6-เจาะลึกทุกหน้าจอและส่วนติดต่อผู้ใช้-views--ui-deep-dive)
7. [ศูนย์รวมหน้าต่างป๊อปอัปและโมดัล (Modals Registry)](#7-ศูนย์รวมหน้าต่างป๊อปอัปและโมดัล-modals-registry)
8. [เจาะลึกการทำงานของทุกโมดูล (Core Application Modules)](#8-เจาะลึกการทำงานของทุกโมดูล-core-application-modules)
9. [ชุดเครื่องมือและฟังก์ชันช่วยเหลือ (Utilities & Helper Libraries)](#9-ชุดเครื่องมือและฟังก์ชันช่วยเหลือ-utilities--helper-libraries)
10. [ตรรกะและ Business Logic สำคัญของระบบ](#10-ตรรกะและ-business-logic-สำคัญของระบบ)
11. [ระบบ AI Stock-In สแกนใบเสร็จด้วย Gemini Vision](#11-ระบบ-ai-stock-in-สแกนใบเสร็จด้วย-gemini-vision)
12. [ระบบเชื่อมต่อภาพ Google Drive ผ่าน Google Apps Script (GAS)](#12-ระบบเชื่อมต่อภาพ-google-drive-ผ่าน-google-apps-script-gas)
13. [ระบบคีย์บอร์ดและฮาร์ดแวร์สแกนบาร์โค้ด (Barcode Hardware & Thai Fix)](#13-ระบบคีย์บอร์ดและฮาร์ดแวร์สแกนบาร์โค้ด-barcode-hardware--thai-fix)
14. [การปรับปรุงความเสถียร รองรับแท็บเล็ตและจอสัมผัส (Touch & Tablet Hardening)](#14-การปรับปรุงความเสถียร-รองรับแท็บเล็ตและจอสัมผัส-touch--tablet-hardening)
15. [ระบบเสียงพูดแจ้งเตือนและคิดเงินภาษาไทย (Thai Voice Checkout Announcement)](#15-ระบบเสียงพูดแจ้งเตือนและคิดเงินภาษาไทย-thai-voice-checkout-announcement)
16. [สรุปกฎและข้อบังคับสำคัญของระบบ (Critical System Directives Summary)](#16-กฎและข้อบังคับสำคัญสำหรับผู้พัฒนาและ-ai-critical-rules--directives)
17. [คู่มือการติดตั้งและการรันระบบ (Setup & Deployment Guide)](#17-คู่มือการติดตั้งและการรันระบบ-setup--deployment-guide)

---

## 1. ภาพรวมและปรัชญาการออกแบบ (System Overview)

Pixel POS & AI Inventory ได้รับการออกแบบภายใต้แนวคิด **"Retro Arcade Aesthetics with Modern Cloud Resilience"**:
- **Retro Arcade 8-Bit/16-Bit Interface**: ดีไซน์ย้อนยุคสไตล์ตู้เกมอาร์เคด โทนสีเหลืองอำพัน-ดำตัดขอบคมชัด หน้าจอ LED แบบมีเส้นสแกนไลฟ์สไตล์ CRT พร้อมระบบเสียงสังเคราะห์ Chiptune SFX ในตัวผ่าน Web Audio API โดยไม่ต้องพึ่งพาไฟล์เสียงภายนอก พร้อมปุ่มเปิด/ปิดเสียง (Sound Mute) ที่จำสถานะข้ามเซสชัน
- **Single Page Application (SPA) with In-Memory View Cache & Module Lifecycle**: โครงสร้างไฟล์ HTML แยกย่อยตามหน้าในโฟลเดอร์ `views/*.html` ถูกโหลดแบบอะซิงโครนัสเข้าสู่คอนเทนเนอร์หลัก `#app-viewport` ใน `index.html` พร้อมระบบ **In-Memory View Cache (Map)** สลับหน้าได้เร็วระดับ sub-millisecond โดย **ไม่เปลืองโควตา 5MB ของ `localStorage`** (ตัดปัญหา QuotaExceededError 100%), มีระบบ **Service Worker (Cache-First)** พร้อม `{ ignoreSearch: true }` สำหรับการทำงานออฟไลน์, ระบบ **Module Lifecycle (`init()` / `destroy()`)** กำจัดปัญหา Memory Leak จาก Event Listeners สะสมบนแท็บเล็ตแคชเชียร์, และมี Navigation Guard ล็อกการเปลี่ยนหน้าขณะระบบกำลังดาวน์โหลดข้อมูลสินค้าเริ่มต้น
- **Offline-First & Dual-Client Fallback**: ทำงานต่อเนื่องได้แม้สัญญาณอินเทอร์เน็ตขาดหายด้วย IndexedDB (`Dexie.js`) และ Service Worker (PWA) เมื่อส่งคำสั่งฐานข้อมูล ระบบมีตัวช่วย `dbHelper.js` ที่สลับการทำงานระหว่าง Authenticated Client และ Anon Public Client ของ Supabase โดยอัตโนมัติเมื่อเกิดปัญหา Session หลุดหรือติดสิทธิ์ RLS
- **AI-Powered Inventory Staging**: ใช้วิสัยทัศน์คอมพิวเตอร์ผ่าน Google Gemini Flash API ถอดข้อมูลจากบิล/ใบเสร็จรับเงินค้าส่ง แปลงเป็นข้อมูลที่มีโครงสร้าง (Structured JSON) เข้าสู่ Staging Table ซึ่งรองรับทั้ง **มุมมองการ์ด (Cards View)** สำหรับแท็บเล็ตจอสัมผัส และ **มุมมองตาราง (Table View)** สำหรับคอมพิวเตอร์จอกว้าง
- **Touch & Tablet Ergonomics**: ปรับขนาดปุ่มกดสำหรับนิ้วมือ (Touch Targets อย่างน้อย 44×44px), แป้นพิมพ์ตัวเลขเสมือนจริงบนหน้าจอ (On-Screen Pixel Numpad) สำหรับระบุจำนวนสินค้าและรับเงินสด, ป้องกันการคลุมข้อความโดยไม่ตั้งใจบนอุปกรณ์สัมผัส, และปรับระยะบรรทัดภาษาไทยให้สระและวรรณยุกต์ไม่แหว่ง

---

## 2. ภาษาและเทคโนโลยีหลักที่ใช้ (Tech Stack & Core Languages)

| ส่วนประกอบ (Component) | เทคโนโลยี / ภาษาที่ใช้ | คำอธิบายและบทบาทหน้าที่ |
| :--- | :--- | :--- |
| **Core Frontend Language** | **JavaScript (Native ES6+ Modules)** | ใช้ Native ES Modules (`import`/`export`) เต็มระบบ ไม่ต้องพึ่งพา Bundler (Vite/Webpack) ทำให้ดีบักง่าย โค้ดโปร่งใส และโหลดไว |
| **Structure & Templating** | **HTML5 Semantic Partial Views** | หน้าจอถูกแยกเป็นโมดูลาร์ในไดเรกทอรี `views/*.html` เพื่อให้ `viewLoader.js` โหลดเฉพาะส่วนที่ต้องการ จัดเก็บใน In-Memory Cache (Map) โดยไม่กินพื้นที่ `localStorage` และมี Versioning ควบคุมผ่าน `APP_VERSION` |
| **Styling & Retro Theme** | **Tailwind CSS v3.4 + Custom Pixel CSS** | ยูทิลิตี้คลาสของ Tailwind CSS ผสานกับสไตล์ตู้เกมพิกเซลและ Scanlines ใน `css/retro-pixel.css` |
| **Local Web Server** | **Node.js (Native HTTP Server)** | เซิร์ฟเวอร์ `server.js` ขนาดกะทัดรัด ให้บริการไฟล์สถิต มีระบบกันชนกรณี Port ซ้ำ (`EADDRINUSE`) และรองรับ SPA Fallback |
| **Primary Cloud Database** | **Supabase (PostgreSQL 15+)** | เก็บข้อมูลหลัก: สินค้า, หมวดหมู่, ประวัติการขาย, รายการขาย, ความเคลื่อนไหวสต็อก และการตั้งค่าร้านค้า |
| **Offline DB & Local Cache** | **Dexie.js (IndexedDB Wrapper)** | ฐานข้อมูล `RetroArcadePosDB` แคชสินค้าและหมวดหมู่ พร้อมตารางคิวบิลออฟไลน์ `offline_orders` |
| **Vision AI Engine** | **Google Gemini Flash API** | ถอดข้อความใบเสร็จรับเงิน/ใบส่งสินค้า รองรับ Failover Pool (`gemini-2.5-flash`, `gemini-2.0-flash`, `gemini-1.5-flash`) |
| **Camera Barcode & QR Reader** | **Html5-Qrcode SDK** | สแกนบาร์โค้ด 1D (EAN-13, Code 128) และ 2D (QR Code) ผ่านกล้องแท็บเล็ต/สมาร์ทโฟน |
| **Data Analytics & Charts** | **Chart.js v4.4** | กราฟเส้นแนวโน้มยอดขายและกำไร, กราฟแท่งสินค้าขายดี 10 อันดับแรก, และโดนัทสัดส่วนการชำระเงิน |
| **Progressive Web App (PWA)** | **Service Worker (`sw.js`) + Manifest** | รองรับการติดตั้งแบบ Standalone Application บน iOS, iPadOS, Android, Windows, macOS พร้อมไอคอน Maskable และการแคชแบบ Cache-First ที่รองรับ `{ ignoreSearch: true }` |
| **Image Storage Pipeline** | **Google Apps Script (GAS) + Google Drive** | Middleware รับภาพ Base64 นำไปบันทึกลง Google Drive แล้วแปลงเป็น High-Speed Direct Google CDN Link |
| **PromptPay Payment QR** | **Native JavaScript EMVCo QR Generator** | สร้าง Dynamic PromptPay QR Code ตามมาตรฐาน EMVCo พร้อมคำนวณ Checksum CRC16-CCITT อัตโนมัติ |
| **Receipt Printing** | **Thermal Print Engine (ESC/POS CSS)** | จัดรูปแบบสลิปใบเสร็จมาตรฐานกระดาษความร้อน 58 มม. และ 80 มม. พิมพ์ผ่านเบราว์เซอร์หรือเครื่องพิมพ์บลูทูธ/USB |
| **Audio Synthesizer** | **Web Audio API Chiptune Engine** | สังเคราะห์เสียง 8-bit แบบเรียลไทม์ (Beep, Coin, Hit, Laser, Powerup, Jump, Error) ไม่ต้องใช้ไฟล์ `.mp3` |
| **Thai Voice Announcement** | **Web Audio API + Pristine Gemini TTS** | ระบบเสียงประกาศยอดเงินและเงินทอนภาษาไทย 25 แทร็ก (Voice: Aoede) รองรับตัวเลขหลักหน่วยถึงหลักแสน ตัดเสียงซ่าท้ายคำด้วย Linear Fade-Out สู่ 0 อย่างสมบูรณ์ |
| **Unified Icon System** | **Font Awesome 6 Free + Pixel SVG** | ไอคอนระบบมาตรฐานคมชัด กึ่งกลางสมดุล (`assets/fontawesome/`) แคชออฟไลน์ 100% ผสานเวกเตอร์พิกเซลเรโทร (`pixel-coin.svg`, `pixel-calc.svg`) |

---

## 3. สถาปัตยกรรมระบบและการไหลของข้อมูล (System Architecture)

> 🗺️ **แผนภาพสถาปัตยกรรมเชิงโต้ตอบฉบับสมบูรณ์ (Interactive Architecture Diagram):**  
> สามารถเปิดดูแผนภาพสถาปัตยกรรมระบบ 4 ลำดับชั้น (Tier 1–4: Shell, Views, Modals & Bento Sub-views, Middleware, Database & External Cloud) แบบ Visual Interactive พร้อมระบบค้นหา, โหมด Dark/Light, และ Trace Motion ได้โดยตรงที่:  
> 🔗 **[pixel-pos-system.html](file:///e:/xampp/htdocs/myproject/about_pos/.archify/pixel-pos-system.html)**  
> *(สร้างและคอมไพล์สถาปัตยกรรมผ่านคำสั่ง `/archify` หรือ `npm run archify` จากสเปก [.archify/pixel-pos.architecture.json](file:///e:/xampp/htdocs/myproject/about_pos/.archify/pixel-pos.architecture.json))*

```text
┌───────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                    CLIENT BROWSER ENVIRONMENT (SPA)                               │
│                                                                                                   │
│   index.html (Core Shell)                                                                         │
│   ├── Header: Sound Toggle, Offline/Online Status Badge, Sync Indicator, Navigation Bar          │
│   ├── ViewContainer (#app-viewport): Dynamic Injection via js/core/viewLoader.js                 │
│   │   ├── In-Memory View Cache (Map): Sub-millisecond swap, 0% localStorage storage pressure     │
│   │   ├── Module Lifecycle Manager (setActiveModule): Auto .destroy() teardown on view swap      │
│   │   ├── views/pos.html           (POS Terminal & Cart)                                          │
│   │   ├── views/payment.html       (Checkout & On-Screen Numpad)                                  │
│   │   ├── views/products.html      (Product Catalog & Stock Management)                           │
│   │   ├── views/categories.html    (Category Sorting & Counts)                                    │
│   │   ├── views/orders.html        (Sales History & Void Logic)                                   │
│   │   ├── views/stockin.html       (AI Stock-In: Staging Cards / Table)                           │
│   │   ├── views/dashboard.html     (Reports & Chart.js Analytics)                                 │
│   │   ├── views/settings.html      (Store Config, VAT, Printers, API Keys)                        │
│   │   └── views/modals.html        (21 Universal Modals Registry)                                 │
│   └── Event Bus (appBus in dbHelper.js) ──► Scoped Disposable unsubscriptions                     │
└────────────────────────────────────────┬──────────────────────────────────┬───────────────────────┘
                                         │                                  │
                  Local Caching & Queue  │                                  │ Cloud REST API / SDK
                                         ▼                                  ▼
      ┌──────────────────────────────────────────────┐        ┌────────────────────────────────────┐
      │          INDEXEDDB (RetroArcadePosDB)        │        │       SUPABASE (POSTGRESQL 15+)    │
      │                                              │        │                                    │
      │  - products (Local catalog cache)            │        │  - products       - orders         │
      │  - categories (Local category cache)         │◄──────►│  - categories     - sale_items     │
      │  - offline_orders (Pending sync queue)       │  Sync  │  - app_settings   - movements      │
      │  - settings (Local store profile)            │        │  - Dual-Client Fallback (dbHelper) │
      └──────────────────────────────────────────────┘        └────────────────────────────────────┘
                                                                               │
                                                                               ├─► Google Gemini Vision API
                                                                               │   (Receipt OCR / AI Staging)
                                                                               │
                                                                               └─► Google Apps Script (GAS)
                                                                                   (Image Upload to Google Drive)
```

---

## 4. โครงสร้างไดเรกทอรีของโปรเจกต์ (Project Structure)

```text
about_pos/
├── assets/
│   ├── fontawesome/            # Font Awesome 6 Free (all.min.css และ fa-solid-900.woff2) แคชใน sw.js สำหรับทำงานออฟไลน์ 100%
│   ├── icons/                  # ไอคอน PWA, pixel-coin.svg (เหรียญทอง), pixel-calc.svg (เครื่องคิดเลข 2.5D Isometric)
│   ├── images/                 # ภาพสำรอง (placeholder.svg, no-image)
│   └── sfx/                    # โฟลเดอร์สำรองสำหรับไฟล์เสียง
├── css/
│   ├── retro-pixel.css         # แกนหลักสไตล์ Retro Arcade, LED Scanlines, ปุ่ม Numpad, การ์ด และ Tablet Tweaks
│   ├── tailwind-input.css      # ไฟล์ต้นทางสำหรับการ Compile Tailwind CSS
│   ├── tailwind.css            # ไฟล์ Tailwind CSS ที่ Compile พร้อมใช้งานจริง (Minified)
│   └── thermal-print.css       # สไตล์ชีตสำหรับจัดรูปแบบการพิมพ์สลิปกระดาษความร้อน 58mm และ 80mm
├── google-apps-script/
│   └── Code.gs                 # สคริปต์ Google Apps Script Web App สำหรับรับอัปโหลดภาพไปบันทึกบน Google Drive
├── js/
│   ├── config.js               # การตั้งค่า Supabase และ Single Source of Truth สำหรับเลขเวอร์ชัน (APP_VERSION)
│   ├── db.js                   # จัดการฐานข้อมูล IndexedDB ฝั่งเบราว์เซอร์ผ่าน Dexie.js (RetroArcadePosDB)
│   ├── pwa-install.js          # ตรวจจับ event beforeinstallprompt และควบคุมปุ่มติดตั้ง PWA
│   ├── sync.js                 # ซิงค์ข้อมูลอัตโนมัติ (ตรวจจับเน็ตหลุด/ต่อเน็ต และส่งคิวบิลออฟไลน์ขึ้น Supabase)
│   ├── core/
│   │   └── viewLoader.js       # โหลด views/*.html แบบ Asynchronous, In-Memory Cache (Map), จัดการ Lifecycle (setActiveModule), ล้างแคชเก่า (purgeLegacyViewCache) และ Versioning
│   ├── modules/
│   │   ├── categories.js       # โมดูลจัดการหมวดหมู่สินค้า (Lifecycle: init / destroy, จัดลำดับ sort_order)
│   │   ├── orders.js           # โมดูลประวัติการขาย (Lifecycle: init / destroy, กรองวันที่, พิมพ์ซ้ำ, ยกเลิกบิล)
│   │   ├── pos.js              # โมดูลขายหน้าร้าน (Lifecycle: init / destroy, ตะกร้าสินค้า, สแกนบาร์โค้ด, พักบิล, LED Qty Numpad)
│   │   ├── products.js         # โมดูลคลังสินค้า (Lifecycle: init / destroy, แคตตาล็อก, เพิ่ม/แก้ไขสินค้า, Voice-to-Name, CSV)
│   │   ├── reports.js          # โมดูลรายงานและสถิติ (Lifecycle: init / destroy, KPIs ยอดขาย, กำไร, Chart.js 4 รูปแบบ)
│   │   ├── settings.js         # โมดูลตั้งค่าร้านค้า (Lifecycle: init / destroy, ภาษี VAT, เครื่องพิมพ์, Gemini Key, GAS URL)
│   │   ├── stock-in.js         # โมดูลรับเข้าสต็อก AI (Lifecycle: init / destroy, Gemini Vision, Staging Cards/Table, Stepper)
│   │   └── stockAiAudit.js     # โมดูลตรวจสอบคุณภาพข้อมูลสินค้า & ค้นหาชื่อด้วยบาร์โค้ด AI (Stock AI Audit & Hygiene Hub)
│   └── utils/
│       ├── audio.js            # ระบบสังเคราะห์เสียง Chiptune 8-bit (Web Audio API) พร้อมระบบเปิด/ปิดเสียง Mute และ Volume Control
│       ├── barcodeHelper.js    # แก้ไขปัญหาแป้นพิมพ์ภาษาไทย (Thai-English Layout Auto-Correction: เกษมณี & ปัตตโชติ)
│       ├── cameraScanner.js    # ตัวจัดการกล้องสแกนบาร์โค้ด 1D/2D และถ่ายภาพสินค้าความละเอียด 900x900
│       ├── clipboard.js        # ฟังก์ชันคัดลอกข้อความลง Clipboard พร้อม Toast แจ้งเตือน
│       ├── dbHelper.js         # Resilient DB Layer (สลับ Client, แก้ Schema Error ด้วย while loop) และ Event Bus (appBus)
│       ├── gdrive.js           # ตัวแปลงลิงก์ Google Drive ID ไปเป็น Direct Google UserContent CDN URL
│       ├── geminiApi.js        # เชื่อมต่อ Google Gemini API พร้อม Multi-Model 3.x Auto-Discovery, Fallback Cascade และ Timeout
│       ├── iconHelper.js       # ระบบไอคอน Font Awesome 6 Free & Pixel SVG
│       ├── imageUploader.js    # บีบอัดรูปภาพหน้าจอ/ไฟล์, แปลงเป็น Base64 แล้วส่งไปยัง Google Apps Script
│       ├── productHygieneAuditor.js # เครื่องยนต์ตรวจจับชื่อ/ราคา/บาร์โค้ดผิดปกติระดับ 0ms และ Gemini Batch AI Audit
│       ├── productMatcher.js   # เครื่องยนต์จับคู่สินค้าอัจฉริยะ (Space-Agnostic, Brand Dominance, Color Conflict)
│       ├── promptpay.js        # สร้าง Payload ข้อความ PromptPay EMVCo QR พร้อมคำนวณ CRC16-CCITT Checksum
│       ├── storageHelper.js    # จัดการ LocalStorage / SessionStorage อย่างปลอดภัยป้องกัน QuotaExceededError
│       ├── thaiSpeechHelper.js # ระบบเสียงพูดแจ้งเตือนยอดเงินและเงินทอนภาษาไทย 25 แทร็ก (Web Audio Gapless Chaining)
│       ├── themeHelper.js      # จัดการสลับธีมคู่ขนาน ('warm-arcade' และ 'cyber-emerald') บันทึก LocalStorage และยิง Event
│       ├── thermal-printer.js  # จัดรูปแบบข้อความใบเสร็จรับเงิน ESC/POS และส่งคำสั่งพิมพ์ลงกระดาษความร้อน
│       ├── uiHelper.js         # Micro-Loader Overlay แบบ Retro Arcade พร้อมฟังก์ชัน showLoader / hideLoader / withLoader
│       ├── voiceSearch.js      # เครื่องยนต์สั่งงานด้วยเสียงภาษาไทย (Voice-to-Cart 3-Tier & Quick-Add Voice-to-Name Dictation)
│       └── wakeLock.js         # ป้องกันหน้าจอแท็บเล็ตดับขณะเปิดหน้าขาย POS (Screen Wake Lock API)
├── tools/
│   └── bump-version.js         # สคริปต์ Node.js ปรับเวอร์ชันอัตโนมัติ (Atomic Bump: js/config.js + sw.js) ผ่าน npm run bump
├── views/
│   ├── categories.html         # ส่วนติดต่อผู้ใช้หน้าจัดการหมวดหมู่สินค้า
│   ├── dashboard.html          # ส่วนติดต่อผู้ใช้หน้าแดชบอร์ดรายงานและกราฟวิเคราะห์ยอดขาย
│   ├── modals.html             # ศูนย์รวมโมดัลหลักของระบบ (Product CRUD, Re-new Barcode, Camera, ฯลฯ)
│   ├── orders.html             # ส่วนติดต่อผู้ใช้หน้าประวัติการขายและบิลย้อนหลัง
│   ├── payment.html            # ส่วนติดต่อผู้ใช้หน้าต่างชำระเงิน (เงินสด, PromptPay, QR ร้านค้า, Cash Numpad)
│   ├── pos.html                # ส่วนติดต่อผู้ใช้หน้าขายหน้าร้าน (Product Grid + ตะกร้าสินค้า + Qty Numpad)
│   ├── products.html           # ส่วนติดต่อผู้ใช้หน้าแคตตาล็อกสินค้าและสต็อกคงเหลือ
│   ├── settings.html           # ส่วนติดต่อผู้ใช้หน้าตั้งค่าระบบและการเชื่อมต่อ
│   └── stockin.html            # ส่วนติดต่อผู้ใช้หน้า AI Stock-In (Upload, Staging Cards/Table, Confirm/Success)
├── AGENTS.md                   # คู่มือกฎระเบียบและแนวทางการเขียนโค้ดสำหรับผู้พัฒนาและ AI Agent
├── index.html                  # ไฟล์ Shell หลักของเว็บแอปพลิเคชัน (Header, View Container, Scripts)
├── manifest.json               # การกำหนดค่า Web App Manifest สำหรับติดตั้ง PWA
├── metadata.json               # ข้อมูล Metadata ของโปรเจกต์
├── package.json                # ข้อมูล Dependencies และ Scripts (dev, start, build:css, bump)
├── README.md                   # เอกสารคู่มือระบบฉบับสมบูรณ์ (ไฟล์นี้)
├── server.js                   # Node.js HTTP Web Server ในตัวพร้อมระบบตรวจจับพอร์ตชน
├── sw.js                       # Service Worker แคชไฟล์สำหรับทำงานออฟไลน์
├── tailwind.config.js          # การตั้งค่าคอนฟิกของ Tailwind CSS (Theme, Colors, Fonts, Content paths)
└── unused_files/               # โฟลเดอร์เก็บไฟล์ที่ไม่ได้ใช้งานแล้ว (Local Only - ระบุใน .gitignore) พร้อม CATALOG.txt
```

---

## 5. โครงสร้างฐานข้อมูลและสคริปต์ SQL (Database Schemas & SQL)

ระบบเชื่อมต่อกับฐานข้อมูล **PostgreSQL 15+** บน **Supabase** ร่วมกับแคชในเครื่องผ่าน **Dexie.js (IndexedDB)** มีโครงสร้างหลักประกอบด้วย 6 ตาราง, 1 View ความปลอดภัย Zero-Secret, และ 3 Atomic Stored Procedures ดังนี้:

### 5.1 ตารางฐานข้อมูลหลัก (6 Core Tables)

| ตาราง (Table) | หน้าที่หลัก | คอลัมน์สำคัญ | ข้อจำกัด & ดัชนี (Constraints & Indexes) |
|---|---|---|---|
| `categories` | หมวดหมู่สินค้า | `id`, `name`, `sort_order`, `created_at` | Primary Key `id`, Index `sort_order ASC` |
| `products` | แคตตาล็อกสินค้าและสต็อก | `id`, `name`, `barcode`, `cost`, `price`, `stock`, `unit`, `category_id`, `image_url`, `is_quick_item`, `is_active`, `track_stock`, `updated_at` | Primary Key `id`, Unique `barcode`, Index `category_id`, Index `is_active` |
| `orders` | บิลและคำสั่งซื้อ | `id`, `order_no`, `status`, `payment_type`, `total_amount`, `discount`, `net_amount`, `total_cost`, `profit`, `cash_received`, `change_amount`, `transfer_amount`, `void_reason`, `created_at` | Primary Key `id`, Unique `order_no`, Index `created_at DESC` |
| `sale_items` | รายการสินค้าในแต่ละบิล | `id`, `order_id`, `product_id`, `product_name`, `quantity`, `unit_price`, `unit_cost`, `subtotal` | Primary Key `id`, FK `order_id` (CASCADE), FK `product_id` (SET NULL) |
| `stock_movements` | บันทึกประวัติเคลื่อนไหวสต็อก | `id`, `product_id`, `type`, `quantity`, `unit_cost`, `note`, `created_at` | Primary Key `id`, FK `product_id` (CASCADE), Index `created_at DESC` *(ไม่มี `ref_invoice_no`)* |
| `app_settings` | การตั้งค่าร้านและฮาร์ดแวร์ | `id`, `store_name`, `store_address`, `store_tel`, `tax_id`, `promptpay_number`, `vat_rate`, `enable_receipt_vat`, `qr_type`, `custom_qr_url`, `enable_thermal_printer`, `printer_paper_size`, `auto_print_receipt`, `pos_grid_cols`, `gemini_api_key`, `google_apps_script_url` | Single Row PK `id = 1`, RLS Read-only Public, Write Authenticated |

### 5.2 มุมมองความปลอดภัย Zero-Secret (1 Database View)

| มุมมอง (View) | หน้าที่และนโยบายความปลอดภัย | สิทธิ์เข้าถึง |
|---|---|---|
| `public_app_settings` | คัดกรองเฉพาะข้อมูลแบรนด์ร้านค้า พารามิเตอร์พิมพ์สลิป และ PromptPay โดย **ตัดคีย์ลับ (Gemini API Key, GAS URL, รหัสผ่าน) ออก 100%** สำหรับเรียกใช้หน้าร้าน | `GRANT SELECT TO anon, authenticated` |

### 5.3 ฟังก์ชันจัดการธุรกรรมระดับอะตอมิก (3 Atomic Stored Procedures)

| ฟังก์ชัน RPC (Stored Procedure) | วัตถุประสงค์ (ACID Guarantee) | พารามิเตอร์ |
|---|---|---|
| `adjust_product_stock_atomic` | ปรับยอดสต็อกสินค้า พร้อมบันทึกประวัติความเคลื่อนไหวลง `stock_movements` ในธุรกรรมเดียวด้วย Row-Level Locking (`FOR UPDATE`) ป้องกันสต็อกเคลื่อน | `p_product_id`, `p_delta`, `p_movement_type`, `p_unit_cost`, `p_note` |
| `void_order_atomic` | ยกเลิกบิล (`VOIDED`) พร้อมคำนวณและคืนสต็อกสินค้าทุกรายการในบิลกลับเข้าคลังอัตโนมัติในธุรกรรมเดียว | `p_order_id`, `p_reason`, `p_timestamp` |
| `sync_offline_order_atomic` | ซิงค์บิลออฟไลน์แบบครบวงจร (สร้างบิล + บันทึกรายการ + ตัดสต็อก + บันทึก movement) รองรับ Idempotency ป้องกันบิลซ้ำ | `p_order JSONB` |

> 💡 **ไฟล์สคริปต์ SQL ฉบับเต็ม:** สำหรับการติดตั้งฐานข้อมูลใหม่หรือ Migration ทั้งหมด สามารถดูและรันคำสั่ง DDL พร้อม RLS และ Triggers ได้โดยตรงจากไฟล์ [sql/migration_atomic_stock_and_transactions.sql](file:///e:/xampp/htdocs/myproject/about_pos/sql/migration_atomic_stock_and_transactions.sql)

---

## 6. เจาะลึกทุกหน้าจอและส่วนติดต่อผู้ใช้ (Views & UI Deep Dive)

### 6.1 หน้าจอขายหน้าร้าน (POS Terminal) - `views/pos.html`
- **Catalog Panel (ฝั่งซ้าย)**:
  - **ปุ่มสั่งงานด้วยเสียง 2 แถวฝั่งซ้ายสุด (2-Row Tall Voice-to-Cart Microphone [F3 / แตะไมค์])**:
    - กินพื้นที่ 2 แถว (แถวบาร์โค้ด + แถวหมวดหมู่) ทางฝั่งซ้ายสุด กว้าง 50-56px กดง่ายบนจอสัมผัส
    - สั่งเพิ่มสินค้าด้วยเสียงพูดภาษาไทย (Web Speech API) แปลงชื่อสินค้าและจำนวนเข้าสู่ตะกร้าขายอัตโนมัติ
    - สัญญาณเสียงไกด์ **"ติ๊ด!"** ทันทีที่ไมค์เปิดทำงาน
    - ระบบ Visualizer สองระดับ: แอนิเมชันคลื่นเสียง Equalizer 4 แท่ง (`.voice-sound-waves`) บนปุ่มสีแดง CRT Pulse และแบนเนอร์ด้านบน (`#pos-voice-banner`) สตรีมข้อความสดเรียลไทม์ขณะกำลังพูด
    - ตรรกะตรวจจับคำระบุราคา เช่น *"โค้ก 20 บาท"* เพื่อเทียบเคียงหาสินค้าที่ตรงระดับราคา (เช่น โค้ก 590 มล. ราคา 19 บาท) โดยตั้งค่าเริ่มต้นเป็น 1 หน่วยทันที
    - **ระบบแนะนำตัวเลือกตระกูลสินค้าและคำใกล้เคียงอัตโนมัติ (Top Match Auto-Add with $\le 10$ Variation Cards)**:
      - เพิ่มสินค้าตัวที่ดีที่สุด (Top Match) เข้าตะกร้าทันที และไฮไลต์ด้วยกรอบสีเขียว Emerald Pulse (`.voice-card-auto-added`) พร้อมป้าย `✓ เพิ่มแล้ว`
      - สกัดแบรนด์/ตระกูลสินค้า (`getProductBrandKeywords`), แยกคำประสม (`extractVoiceRelatedKeywords`), รองรับเสียงใกล้เคียง (Sound-Alikes เช่น "ร๊อก" <-> "ร็อค") และส่วนขยายคำ (เช่น พูด *"หงส์"* จะเพิ่ม *"หงส์ แบน"* เข้าตะกร้า และดึงสินค้าที่มีคำว่า *"หงส์..."* เช่น *"หงส์ทอง"* มาร่วมในตัวเลือกด้วย หรือพูด *"เลย์ร๊อก"* จะนำ *"เลย์ร็อค..."* เข้าตะกร้า และดึงสินค้าที่มีคำว่า *"เลย์"* หรือ *"ร๊อก/ร็อค"* มาเป็นตัวเลือกด้วย)
      - นำสินค้าตัวเลือกใกล้เคียง/Variations มาแสดงใน Grid (`pos-card-content`) รวมสูงสุด **10 การ์ด** พร้อมป้าย `+ เลือกตัวนี้` ให้แคชเชียร์แตะเลือกสลับได้ทันทีในคลิกเดียว
    - มีระบบขอสิทธิ์ไมค์ล่วงหน้า (`ensureMicPermission`) รองรับสภาพแวดล้อม PWA / HTTPS ได้อย่างลื่นไหล
  - ช่องกรอกค้นหาชื่อสินค้า/ยิงบาร์โค้ดฮาร์ดแวร์ (`#pos-barcode-input`) พร้อมปุ่มล้างคำค้นหา และปุ่มเปิดกล้องสแกนบาร์โค้ด
  - **แถบหมวดหมู่สินค้าและปุ่มลูกศรเพรียวบาง (Slim Category Arrows & 48px Tabs)**:
    - เพิ่มความสูงแถบหมวดหมู่เป็น 48px เพื่อความสะดวกในการแตะสัมผัสบนแท็บเล็ต
    - ปุ่มลูกศรเลื่อนซ้าย-ขวาความกว้าง 18px (`.slim-cat-arrow`) ไม่เบียดบังพื้นที่แสดงผล
    - ตกแต่งสไตล์ Scrollbar ให้เข้ากับโทนสีเรโทรของแต่ละธีม
  - แถบเลื่อนเลือกหมวดหมู่สินค้าแนวนอน (`.pos-cat-tab`) พร้อมปุ่มตัวกรองพิเศษ `⚡ รายการด่วน (Quick Items)`
  - ตัวเลือกปรับจำนวนคอลัมน์การแสดงผลสินค้า (3, 4, 5, 6 คอลัมน์) ตามขนาดหน้าจอ
  - การ์ดสินค้าพิกเซล: แสดงรูปสินค้า (มีรูป Placeholder สำรอง), ชื่อสินค้าพร้อมตัดคำ 2 บรรทัด (ไม่ทับสระภาษาไทย), ป้ายราคาเด่นชัด, และ Badge สต็อกคงเหลือ (สีเขียว = มีสินค้า, สีแดง = สินค้าหมด/ติดลบ)
- **Cart & Order Panel (ฝั่งขวา)**:
  - แสดงรายการสินค้าในตะกร้าแบบเรียลไทม์ พร้อมเส้นขอบคมชัดสไตล์เกม
  - **การ์ดรายการสินค้าในตะกร้าสไตล์โมเดิร์นเรโทร (Modern Retro Arcade Cart Card)**:
    - **รูปภาพ Thumbnail สินค้าชิดซ้ายสุดเต็มความสูง (Edge-to-Edge Flush Thumbnail)**: แสดงภาพสินค้าจริงจาก Google Drive / CDN พร้อมระบบ Fallback
    - **ชื่อสินค้าและราคาต่อหน่วยแบบคลีน (Clean Currency Display)**: ตัดเลข `.00` ออกโดยอัตโนมัติหากสินค้าไม่มีเศษสตางค์ (เช่น `@10 / ขวด`, `฿10`) และแสดงทศนิยม 2 ตำแหน่งเฉพาะเมื่อมีเศษสตางค์จริง (เช่น `@10.50 / ขวด`)
    - **ชุดปรับจำนวนลูกศรสามเหลี่ยมเรโทร (Arcade Stepper Controls)**:
      - ปุ่มลูกศรแดง **`◀`** ปรับลดจำนวน
      - กล่องตัวเลขกรอบประสีทอง **`[1]`**: แตะเพื่อเปิดโมดัล Quick Quantity Numpad ป้อนตัวเลขได้โดยตรง
      - ปุ่มลูกศรเขียว **`▶`** ปรับเพิ่มจำนวน
    - **ยอดรวมย่อย (Subtotal)**: แสดงตัวเลขฟอนต์พิกเซลสีเขียวเรโทรชัดเจน
    - **แถบปุ่มลบสีแดงแนวตั้งประกบขวาสุดเต็มความสูง (Integrated Right End-Cap Delete Button `✕`)**: ดีไซน์กลืนกับตัวการ์ด ไม่ล้นขอบจอ (Zero Overflow) ปลอดภัยต่อการเลื่อนตะกร้าบน Android PWA
    - **Zero-Padding Protection**: ป้องกัน Media Query Tablet/Mobile ฉีด Padding แทรกกลางการ์ด ทำให้รูปและปุ่มลบแนบสนิทกับขอบการ์ด 100% ในทุกขนาดหน้าจอ
  - ส่วนคำนวณเงินท้ายบิล: รวมเงิน (Subtotal), สวิตช์ส่วนลด (เลือกระบุเป็น `฿` บาท หรือ `%` เปอร์เซ็นต์ พร้อมแสดงผลคำนวณส่วนลดย่อย), ภาษีมูลค่าเพิ่ม (VAT), ยอดสุทธิ (Net Amount), และการวิเคราะห์กำไรเบื้องต้น
  - แถบปุ่มลัด: `🗑️ ล้างตะกร้า`, `⏸️ พักบิล / 📂 เรียกบิล`, `⚡ จ่ายเงินสดพอดี`, และ `💳 ชำระเงิน (Checkout)`
- **โมดัลคีย์ตัวเลขด่วน (Quick Quantity Numpad Modal)**:
  - อยู่ภายใน `views/pos.html` (`#modal-quick-quantity`)
  - ดีไซน์กะทัดรัด (Compact Zero-Scroll Architecture ความสูงรวม ~360px): รับประกันปุ่มยกเลิก `[Esc]` และยืนยัน `[Enter]` ไม่ตกขอบจอด้านล่างบนแท็บเล็ต 1024x640 แนวนอน
  - แสดงชื่อสินค้า, ราคาต่อหน่วย, และหน้าจอ LED Scanline สีเขียวแสดงจำนวนสินค้าที่พิมพ์
  - ปุ่มลัดตามแพ็ค/โหล: `+1 ชิ้น`, `6 (ครึ่งโหล)`, `12 (1 โหล)`, `24 (2 โหล)`
  - แผงแป้นตัวเลข 0-9 แบบ Slim Grid พร้อมปุ่ม `C (ล้าง)` และ `⌫ (Backspace)`
  - รองรับคีย์ลัดคีย์บอร์ด: `Enter` ยืนยัน, `Esc` ยกเลิก, และพิมพ์ตัวเลขจากแป้นพิมพ์คอมพิวเตอร์ได้ทันที

### 6.2 หน้าจอชำระเงิน (Payment & Checkout View) - `views/payment.html`
- เปลี่ยนผ่านจากหน้า POS เป็นแบบ 2 คอลัมน์เต็มหน้าจอเพื่อความสะดวกในการจัดการคำสั่งซื้อ:
  - **คอลัมน์ซ้าย (Receipt Summary)**: แสดงสรุปรายการสินค้าในบิลทั้งหมด, ยอดรวม, ส่วนลดท้ายบิล, ภาษี VAT, และยอดสุทธิที่ต้องชำระ
  - **คอลัมน์ขวา (Payment Method & Interaction - Dynamic Attention Engine)**:
    - **ระบบกรอบสีเน้นสถานะการรับเงิน (Dynamic Border Attention)**: กรอบของคอนเทนเนอร์ `#pos-checkout-col-right` เปลี่ยนสี 3 สีตามประเภทการรับเงินเพื่อดึงดูดสายตาและป้องกันการสับสน:
      - **เงินสด (Cash):** กรอบสีส้มสดใสเรโทร (`#f97316`) พร้อมปุ่มแท็บ Active สีส้ม
      - **QR / โอนเงิน (PromptPay):** กรอบสีม่วงเด่นชัด (`#9333ea`) พร้อมปุ่มแท็บ Active สีม่วง
      - **ร่วมจ่าย (Split สด+โอน):** กรอบสีชมพูสะดุดตา (`#ec4899`) พร้อมปุ่มแท็บ Active สีชมพู
    - **แท็บเงินสด (Cash Payment with On-Screen Numpad)**:
      - ปุ่มลัดธนบัตรสำเร็จรูป: `฿20`, `฿50`, `฿100`, `฿500`, `฿1,000`, และ `พอดี`
      - ช่องกรอกเงินสดที่รับมา (`#pos-cash-received`) ปรับสีกรอบและพื้นหลังตามโหมด
      - **แผงคีย์ตัวเลขรับเงินสด (Cash Numpad Grid)**: ปุ่มตัวเลข 0-9, `00`, จุดทศนิยม `.`, ปุ่มล้าง `C`, ปุ่มลบ `⌫`, และปุ่ม `พอดี`
      - จอ LED แสดงเงินทอนสีเขียวเรืองแสง (`#pos-change-display`) ขนาดใหญ่ ชัดเจน อ่านง่าย
    - **แท็บพร้อมเพย์ (Dynamic PromptPay)**: เจนเนอเรท QR Code พร้อมเพย์ตามยอดบิลโดยตรง ผ่านไลบรารี `promptpay.js` พร้อม Slide-Up QR Drawer
    - **แท็บร่วมจ่าย (Split Payment)**: ช่องกรอกคู่สำหรับยอดโอนและเงินสด
    - ตัวเลือกเปิด/ปิดการพิมพ์ใบเสร็จความร้อนอัตโนมัติ
    - ปุ่ม `💾 บันทึกการขาย (Complete Sale)` และปุ่มย้อนกลับไปหน้าขาย

### 6.3 หน้าจอสินค้าและคลังสินค้า (Products & Inventory) - `views/products.html`
- แถบสถิติด้านบน (Inventory KPIs): สินค้าทั้งหมด, สินค้าพร้อมขาย, สินค้าสต็อกต่ำ/หมด, มูลค่าสต็อกรวมตามทุน (คำนวณเฉพาะสินค้าเปิดขาย และสินค้าปิดการขายจะถูกรีสต็อกเป็น 0 ชิ้นอัตโนมัติ)
- ตัวกรองสถานะสต็อก 11 หมวดหมู่ (เรียงตามลำดับความคล่องตัว และตั้งค่าเริ่มต้นที่ **"เปิดขาย"**): `เปิดขาย`, `พร้อมขาย`, `ใกล้หมด`, `หมดสต็อก`, `🚨 ติดลบ`, `♾️ ไม่นับสต็อก`, `🆕 สินค้าเพิ่มใหม่`, `🔄 เปลี่ยนแปลงล่าสุด`, `⚡ ขายด่วน`, `ปิดขาย`, `ทั้งหมด`
- **ระบบค้นหาสินค้าด้วยเสียงพูด (Voice Search Engine):** ปุ่มไมโครโฟนทรงสูงสไตล์ Arcade ด้านซ้ายสุด สั่งค้นหาและกรองรายการสินค้าด้วยเสียงภาษาไทยแบบ 3-Tier Multi-Level Priority
- ช่องค้นหาชื่อสินค้า/บาร์โค้ด, ตัวกรองตามหมวดหมู่
- ปุ่มฟังก์ชัน: `+ เพิ่มสินค้าใหม่` (เปิด CRUD Modal), `นำเข้า CSV (Import)`, `ส่งออก CSV (Export)`, และ `พิมพ์บาร์โค้ด`
- ตารางสินค้า (Touch-Friendly & Compact):
  - **ระบบตัดหน้าตารางสินค้า (Inventory Pagination):** ตัดแสดงผลหน้าละ **25 รายการ** (`pageSize = 25`) พร้อมแถบควบคุมสลับหน้า (`◀ ย้อนกลับ`, ปุ่มเลขหน้าอัจฉริยะ, และ `ถัดไป ▶`) โดยจะซ่อนแถบตัดหน้าอัตโนมัติหากสินค้ามี $\le 25$ รายการ และรีเซ็ตกลับหน้า 1 เสมอเมื่อมีการค้นหาหรือกรองข้อมูล
  - **ระบบท่อส่งรูปภาพและ De-duplication (Google Drive Pipeline v2.2):**
    - แตะที่รูปภาพสินค้าเพื่อเปิดกล้องถ่ายภาพด่วน (Quick Photo 900×900 px) บันทึกและอัปเดตทันที
    - **Instant Local Cache (`_localImageCache`):** แสดงรูปที่เพิ่งถ่ายได้ทันที 100% Zero-Latency บนตารางโดยไม่ต้องรอ Google Drive CDN Replicate
    - **Smart Retry & Fallbacks:** สลับ URL ทางเลือกเมื่อเกิด CDN Propagation Delay ชั่วคราว ป้องกันรูปหลุดเป็นกล่องเปล่า
    - **Auto-Cleanup (De-duplication):** ส่ง `oldFileId` ให้ Google Apps Script ย้ายรูปเดิมลงถังขยะอัตโนมัติ ไม่เกิดไฟล์ซ้ำซ้อนสะสมใน Google Drive
    - **Audit Agent & Diagnostics:** มี Agent ประจำตัว [.agents/skills/image-pipeline-auditor/](file:///e:/xampp/htdocs/myproject/about_pos/.agents/skills/image-pipeline-auditor/) และชุดทดสอบ `tests/test_image_upload_diagnostics.js`
  - แตะที่ตัวเลขบาร์โค้ดหรือไอคอน `🔄` เพื่อเปิดหน้าต่าง Re-new Barcode จับคู่สินค้าเดิมได้ทันที และมีปุ่มคัดลอกบาร์โค้ดใน Modal
  - ปุ่มจัดการกะทัดรัด (`✏️ แก้` และ `🗑️`) ประหยัดพื้นที่ตาราง ไม่ดันล้นจอแท็บเล็ต 1024×640
  - รองรับการปรับตัวเลขสต็อก ราคา และต้นทุนผ่าน Numpad Card ที่มีคอนทราสต์ชัดเจนในทุกธีม (ทั้ง Arcade Classic และ Cyber Emerald)

### 6.4 หน้าจอจัดการหมวดหมู่สินค้า (Categories) - `views/categories.html`
- แสดงรายชื่อหมวดหมู่สินค้าทั้งหมด พร้อมลำดับการจัดเรียง (`sort_order`)
- ป้ายแสดงจำนวนสินค้าที่ผูกอยู่กับแต่ละหมวดหมู่อย่างถูกต้อง
- ปุ่มสลับลำดับหมวดหมู่ขึ้น (`▲`) และลง (`▼`) เพื่อจัดตำแหน่งแถบในหน้าขาย
- ปุ่ม `+ เพิ่มหมวดหมู่`, ปุ่มแก้ไขชื่อหมวดหมู่ และปุ่มลบหมวดหมู่ (พร้อมตรวจสอบป้องกันการลบหมวดหมู่ที่มีสินค้าผูกอยู่)

### 6.5 หน้าจอประวัติการขายและสรุปบิล (Orders History) - `views/orders.html`
- **ชุด Bento Summary Cards (Hero Scoreboard 4 กล่องยอดสรุปด้านบนสุด)**:
  1. 💵 **ยอดขายสุทธิ (`#orders-summary-sales`)**: แสดงยอดขายรวมของทุกบิลที่ผ่านการกรอง พร้อมป้ายเปรียบเทียบ % เทียบกับช่วงเวลาก่อนหน้า (Previous Period Comparison)
  2. 📈 **กำไรสุทธิ (`#orders-summary-profit`)**: คำนวณกำไรสุทธิรวม, ป้าย Margin % และยอดต้นทุนรวม
  3. 🧾 **จำนวนบิลที่สำเร็จ (`#orders-summary-count`)**: แสดงจำนวนบิลสำเร็จทั้งหมด และยอดขายเฉลี่ยต่อบิล (AOV)
  4. 💳 **ช่องทางการรับเงิน (`#orders-summary-payment`)**: แสดงยอดเงินสด vs โอนเงิน/QR พร้อมแถบ Dual Progress Bar 2 สีคำนวณสัดส่วนแบบเรียลไทม์
  - *การคำนวณทั้ง 4 กล่องจะคำนวณจากยอดรวมของทุกบิลที่ผ่านการกรอง (Total Filtered Orders) เสมอ ไม่ใช่เฉพาะ 25 รายการในหน้าที่กำลังเปิดอยู่*
- **ชุดปุ่มตัวกรองช่วงเวลา (Retro Arcade Touch-Friendly Filter Bar)**:
  - ปุ่มตัวเลือกมาตรฐาน: `วันนี้ [Active]` | `เมื่อวานนี้` | `สัปดาห์นี้` | `สัปดาห์ที่แล้ว` | `เดือนนี้` | `กำหนดเอง`
  - คำนวณขอบเขตเวลาและช่วงเวลาก่อนหน้าด้วย Local Timezone (`getOrderPrevDateRangeBounds`) ป้องกันปัญหา UTC Offset หลุดวัน
- **ระบบตัดแบ่งหน้า (Orders Pagination 25 รายการ/หน้า)**:
  - แถบควบคุม `#orders-pagination` ใต้ตาราง: `[ ◀ ย้อนกลับ ]`, ตัวบอกสถานะ `หน้า X จาก Y (รวม Z บิล)`, และ `[ ถัดไป ▶ ]`
  - ซ่อนแถบ Pagination อัตโนมัติหากบิลที่ผ่านการกรองมี $\le 25$ รายการ และรีเซ็ตกลับหน้า 1 เสมอเมื่อเปลี่ยนตัวกรอง
- **ตารางแสดงประวัติบิล**: เลขที่บิล (`INV-xxxxxxxx`), วันที่และเวลา, วิธีชำระเงิน, ยอดรวม, ส่วนลด, ยอดสุทธิ, กำไรสุทธิของบิล, และสถานะ (`COMPLETED` หรือ `VOIDED`)
- **การดูรายละเอียดและการยกเลิกบิล (Inline Void Confirmation & Restock)**:
  - หน้าต่างรายละเอียดบิลและสลิปใบเสร็จรับเงิน พร้อมปุ่มพิมพ์ใบเสร็จความร้อนซ้ำ
  - แผงยืนยันการยกเลิกบิลแบบ Inline ในตัว ป้องกันการยิงซ้ำ (`isVoiding`) และคืนสต็อกสินค้าลง `stock_movements` ถูกต้องตามมาตรฐาน

### 6.6 หน้าจอรับเข้าสต็อก AI (AI Stock-In Terminal) - `views/stockin.html`
- **Input Panel**:
  - อัปโหลดรูปภาพใบเสร็จ/บิลส่งของ หรือเปิดกล้องถ่ายภาพ (รองรับบิลทั้งแนวตั้งและแนวนอน)
  - **ระบบหมุนภาพและสลับมุมมอง (Offscreen Canvas 90° Rotation & Expand View)**:
    - ปุ่ม `หมุนซ้าย 90°` และ `หมุนขวา 90°` หมุนพิกเซลจริงผ่าน Canvas ลงใน Base64 ก่อนส่งให้ Gemini Vision อ่านข้อความได้ถูกต้อง
    - ปุ่ม `ขยายเต็มจอ / พอดีกรอบ` (`#btn-stockin-toggle-expand`) และป้ายบอกทิศทางภาพอัตโนมัติ (`#stockin-orientation-badge`)
  - แถบแจ้งเตือนการบีบอัดรูปภาพฝั่งไคลเอนต์ (`#stockin-compression-badge`) แสดงผลลัพธ์การย่อภาพก่อนส่ง AI
  - ช่องวางข้อความดิบหรือ JSON จากคู่ค้า
  - ปุ่ม `🧠 วิเคราะห์ด้วย AI (Gemini Vision)`
- **Staging Controls & Switcher**:
  - **ตัวสลับมุมมอง (View Switcher)**:
    - `🗂️ การ์ด (Cards View)`: โครงสร้างการ์ดแนวนอนจัดระเบียบ 2 คอลัมน์ (รูปภาพพร้อมปุ่มถ่ายรูปด่วนด้านซ้าย, ข้อมูลสินค้าและ Stepper 44px ด้านขวา)
    - `📋 ตาราง (Table View)`: เหมาะสำหรับจอคอมพิวเตอร์แนวนอน แสดงคอลัมน์ครบถ้วน
  - **ระบบกล้องถ่ายรูปสินค้าด่วนแบบรวมศูนย์ (Unified Quick Photo Camera):** เชื่อมโยงเข้ากับ Retro Live Camera Viewfinder Modal (`#prod-camera-modal`) ของหน้า Inventory ถ่ายและตัดขอบสี่เหลี่ยมจัตุรัส 900×900 px พร้อมอัปโหลดขึ้น Google Drive และซิงค์ลงฐานข้อมูล Supabase / Dexie ทันทีเมื่อสินค้านั้นผูกกับสินค้าในระบบ
  - ปุ่ม `➕ เพิ่มรายการ`: สำหรับพิมพ์เพิ่มสินค้าเข้าสต็อกด้วยตนเองโดยไม่ต้องผ่าน AI
  - ป้ายแสดงจำนวนรายการที่พบในบิล
- **Staging Interaction (การตรวจสอบและปรับข้อมูล - มาตรฐานหน่วยค้าปลีก)**:
  - ระบุชื่อผู้ขาย (Supplier) และเลขที่เอกสาร (Invoice No.)
  - จับคู่สินค้ากับสต็อกเดิมอัตโนมัติ (ขึ้นป้าย `🔗 สินค้าเดิม` หรือ `✨ สินค้าใหม่`)
  - **ปุ่มนำบาร์โค้ดไปใช้เป็นสินค้าใหม่ (New Item Barcode Flow)**: เมื่อสแกนหาบาร์โค้ดใน Modal ค้นหาแล้วไม่พบ จะมีปุ่ม `➕ นำบาร์โค้ดนี้ไปใช้เป็นสินค้าใหม่` คัดลอกเลขบาร์โค้ดลงในการ์ดรับสินค้าทันที
  - **การจัดเรียง 5 ช่องตามลำดับคณิตศาสตร์ค้าปลีก**:
    `จำนวนกล่อง` ➔ `กล่องละ (฿)` ➔ `จำนวนหน่วยในกล่อง` ➔ `ทุน/หน่วย (฿)` ➔ `ราคาขาย/หน่วย (฿)`
  - คำนวณจำนวนหน่วยรับเข้าจริง และราคาทุนต่อหน่วยอัตโนมัติ พร้อมแสดงผลรวมรับเข้าไว้ด้านขวาสุดของแถว
  - แจ้งเตือนเมื่อราคาทุนใหม่ต่างจากทุนเดิมในระบบ (`⚠️ ปรับราคาทุนใหม่`)
- **Action Buttons**:
  - ปุ่ม `🗑️ ล้างรายการ`
  - ปุ่ม `💾 ยืนยันบันทึกรับเข้าสต็อก (Confirm Stock-in)` เพื่อเปิดโมดัลยืนยันแบบสัมผัส

### 6.7 ศูนย์วิเคราะห์กลยุทธ์ร้านค้าด้วย AI (AI-Analytics & Retail Strategy Center) - `views/dashboard.html`
- **1. ส่วนบน: AI Strategic Command Card (วิเคราะห์สภาวะร้านสดด้วย Gemini Flash)**:
  - กล่อง CRT Scanline สไตล์ Arcade พร้อมปุ่มกด `[ 🧠 แตะเพื่อวิเคราะห์สภาวะร้านด้วย AI ]`
  - ประมวลผลสถิติร้านค้าตามช่วงเวลาที่เลือก และส่งต่อไปยัง Google Gemini Flash API ผ่านระบบ Failover Pool (`gemini-2.5-flash` ➔ `gemini-2.0-flash` ➔ `gemini-1.5-flash`)
  - แสดงผลลัพธ์ 3 มิติเชิงกลยุทธ์:
    - 🟢 **จุดเด่น / สินค้าขับเคลื่อน (Growth Highlights)**: สรุปสินค้าดาวรุ่งและสิ่งที่ร้านทำได้ดี
    - 🟡 **ข้อควรระวัง & แนวโน้มที่ชะลอตัว (Watchouts & Stagnant Items)**: แจ้งเตือนหมวดหมู่ที่ยอดตกหรือสินค้าที่เริ่มนิ่ง
    - 🔵 **AI Re-order Advisor**: คำแนะนำการสั่งของเข้าสต็อกล่วงหน้าก่อนของขาด พร้อมตารางรายการแนะนำ (`#ai-reorder-tbody`)
  - ระบบ **Client-side Analytics Cache**: จดจำผลลัพธ์การวิเคราะห์ตามช่วงเวลา ไม่ยิง API ซ้ำซ้อนเมื่อสลับแท็บไปมา
- **2. ส่วนกลาง: Retail Science & Matrix (2 คอลัมน์)**:
  - **Peak Hours & Sales Timeline**: วิเคราะห์ช่วงเวลาที่ขายดีที่สุดของวัน (Best Slot Banner) และคำนวณการกระจายตัวของยอดขาย 4 ช่วงเวลาของวัน (🌅 เช้า 06-12 น., ☀️ บ่าย 12-17 น., 🌆 เย็น 17-21 น., 🌙 ดึก 21-24 น.)
  - **Margin vs Volume Matrix**: จัดเกรดสินค้าในร้าน 3 ระดับตามโมเดลค้าปลีก:
    - 🌟 *ดาวเด่น (High Margin + High Volume)*: สินค้าตัวทำกำไรหลัก
    - ⚡ *ตัวดึงลูกค้า (Low Margin + High Volume)*: สินค้าหมุนเงินไว
    - 📦 *ของดองทุน (High Margin + Low Volume)*: สินค้าที่ควรจัดโปรโมชั่นคู่
- **3. ส่วนล่าง: Inventory Health & Deadstock Predictor (2 คอลัมน์)**:
  - **มาตรวัดเงินจมสต็อก (Deadstock & Capital Trap)**: คำนวณมูลค่าต้นทุนรวมของสินค้าที่มีสต็อกแต่ไม่มีการขายเลยในรอบเวลานั้น (`stock > 0` และ 0 sales) พร้อมแสดง 5 อันดับแรกที่มีมูลค่าเงินจมสูงสุด
  - **สต็อกติดลบ & วิกฤตใกล้หมด (Critical & Negative Stock Table)**: รวมสินค้าที่สต็อกติดลบ (`stock < 0`) และสินค้าใกล้หมด พร้อม **Quick Edit Modal Link** ที่สามารถแตะชื่อสินค้าหรือปุ่มจัดการเพื่อเปิด `#prod-crud-modal` แก้ไขสินค้าของหน้า Inventory ได้ทันทีแบบข้ามโมดูล
- **4. ส่วนท้าย: กราฟวิเคราะห์ Chart.js 5 รูปแบบ (Main Display Tab Switcher)**:
  1. `[ ยอดขาย & แนวโน้ม ]`: กราฟแท่งยอดขายสุทธิและกำไรสุทธิรายชั่วโมงหรือรายวัน (`#dash-chart-trend`)
  2. `[ สินค้ายอดขายสูงสุด ]`: ตาราง Leaderboard และกราฟแท่งแนวนอน Top 5 ยอดขาย
  3. `[ สินค้ากำไรสูงสุด ]`: ตาราง Leaderboard และกราฟแท่งแนวนอน Top 5 กำไรสุทธิ
  4. `[ สินค้าจำนวนสูงสุด ]`: ตาราง Leaderboard และกราฟแท่งแนวนอน Top 5 จำนวนชิ้น
  5. `[ สัดส่วนหมวดหมู่สินค้า ]`: ตารางสัดส่วนยอดขายตามหมวดหมู่ และกราฟโดนัท (`#dash-chart-categories`)
  - ควบคุมการสร้างและทำลายอินสแตนซ์ Chart.js สะอาดหมดจดใน `destroy()` ป้องกัน Canvas Memory Leak 100%

### 6.8 หน้าจอตั้งค่าระบบ (Settings) - `views/settings.html`
- **สถาปัตยกรรมแถบ Sub-Tabs กะทัดรัด (`gap-[0.2rem]`)**: ออกแบบปุ่ม Sub-Tabs ทั้ง 6 ปุ่มด้วยขนาดกะทัดรัด (`px-2.5 sm:px-3 py-1.5 text-xs`) พร้อมไอคอน Font Awesome คมชัด ทำให้ทั้ง 6 แท็บเรียงเป็นแถวเดียวพอดีบนหน้าจอแท็บเล็ต 1024x640 โดยไม่ตกบรรทัด
- **1. ข้อมูลร้าน & เครื่องพิมพ์ (`tab-pane-store`)**: ข้อมูลโปรไฟล์ร้านค้า (ชื่อร้าน, ที่อยู่, เบอร์โทร, เลขภาษี, ข้อความท้ายบิล), กล่องภาษีมูลค่าเพิ่ม (VAT Rate & สวิตช์แสดงในสลิป), และเครื่องพิมพ์ใบเสร็จความร้อน (58mm/80mm, Auto-print, ปุ่มทดสอบพิมพ์)
- **2. หน้าขาย POS & ธีมสี (`tab-pane-pos`)**:
  - **Quick Display & Hardware Controls**: สวิตช์เปิด/ปิด **Screen Wake Lock** ควบคุมการล็อกหน้าจอไม่ให้หลับระหว่างประจำการหน้าขาย POS พร้อมไฟสถานะสด และกำจัดกล่องคอนโทรลซ้ำซ้อน
  - **Theme Picker Bento Cards**: สลับธีมคู่ขนานระหว่าง *Warm Arcade* (คลาสสิกเรโทร สีครีม-ทอง-ดำ) และ *Cyber Emerald* (นีออนไซเบอร์ ดำ-เขียวมรกต)
  - **Grid Density Selector**: เลือกระดับความหนาแน่นของคอลัมน์สินค้าหน้าร้าน (3, 4, 5, 6 คอลัมน์)
  - **Sound & Voice Controls**: สวิตช์เปิด/ปิดเสียง SFX 8-bit และระบบเสียงพูดภาษาไทยแจ้งยอดเงินและเงินทอน
- **3. ที่เก็บรูป & Google Drive (`tab-pane-storage`)**: ตั้งค่า Google Apps Script Web App Deployment URL สำหรับจัดเก็บภาพสินค้าลง Google Drive พร้อมปุ่มเปิดคู่มือการติดตั้งและคำแนะนำ
- **4. Gemini AI & สแกนเนอร์ (`tab-pane-ai`)**: ช่องระบุ Gemini API Key (พร้อม Security Masking), ปุ่มทดสอบเชื่อมต่อ API, และปุ่มเปิดกล้องสแกนเนอร์ทดสอบ
- **5. ติดตั้งแอป PWA (`tab-pane-pwa`)**: ตรวจสอบสถานะการติดตั้งแอป Progressive Web App, ตรวจสอบโควตาพื้นที่เก็บข้อมูลถาวร (Persistent Storage Quota), และปุ่มติดตั้ง Standalone App
- **6. ฐานข้อมูล (`tab-pane-schema`)**:
  - **Database Health & Diagnostics Bento Grid**: แสดงสถานะทั้ง 6 ตารางหลัก (`categories`, `products`, `orders`, `sale_items`, `stock_movements`, `app_settings`) พร้อม Schema Field Tags, Status Badges (`🟢 พร้อมใช้งาน`), และสถิติจำนวนข้อมูลสด (Live Row Counts) ดึงข้อมูลอัตโนมัติทันทีที่สลับเข้าแท็บ
  - **Cross-Module Synchronicity Matrix**: แสดงความเชื่อมโยงระดับระบบ (หมวดหมู่ ➔ POS, สินค้าขายด่วน ➔ แท็บ ⭐ ขายดี, สินค้าบริการ/ปรุงสดไม่ตัดสต็อก, AI Stock-In ➔ สินค้า & สต็อกเข้า)
  - **Verified SQL Migration Script**: รวมคำสั่ง SQL พร้อมใช้งานใน Supabase Studio > SQL Editor สอดคล้องตามกฎข้อที่ 6 (ความถูกต้องของตาราง `stock_movements` โดยไม่ส่งฟิลด์แปลกปลอม) พร้อมปุ่มคัดลอก SQL ในตัว
  - **Local Database Snapshot Export (Dexie JSON Backup)**: ปุ่มสีม่วง *"สำรองข้อมูล JSON"* สำหรับดาวน์โหลดข้อมูล IndexedDB ทั้ง 6 ตารางและ Local Configurations ออกมาเป็นไฟล์ JSON กู้คืนได้ทันทีโดยไม่พึ่งพาระบบคลาวด์

---

## 7. ศูนย์รวมหน้าต่างป๊อปอัปและโมดัล (Modals Registry)

หน้าต่างป๊อปอัปทั้งหมดของระบบถูกรวมศูนย์ไว้ใน `views/modals.html` (และโมดัลคีย์แพดใน `views/pos.html`) รวมทั้งสิ้น 21 โมดัล:

| รหัสโมดัล (Modal ID) | ชื่อโมดัล / บทบาทหน้าที่ | รายละเอียดและการทำงานสำคัญ |
| :--- | :--- | :--- |
| `pos-hold-modal` | **รายการบิลที่พักไว้ (Hold Bills)** | แสดงรายการบิลที่ถูกพักไว้ พร้อมเวลาและยอดเงิน สามารถกดเรียกคืนกลับมาขายต่อ หรือลบทิ้ง |
| `pos-success-modal` | **สำเร็จการขาย (Stage Clear)** | แสดงเมื่อชำระเงินสำเร็จ สรุปเลขที่บิล, ยอดเงิน, ช่องทางชำระ, เงินทอนขนาดใหญ่ และปุ่มลูกค้ารายต่อไป |
| `barcode-not-found-modal` | **แจ้งเตือนไม่พบบาร์โค้ด / สแกนผิดพลาด** | แจ้งเตือนเมื่อยิงบาร์โค้ดไม่พบ หรือสแกนผิดพลาด/ไม่สมบูรณ์ มี 3 ทางเลือก: สแกนผิดพลาด/ยกเลิก `[N]`, Re-new Barcode `[R]`, หรือสร้างสินค้าใหม่ `[Y]` |
| `prod-crud-modal` | **เพิ่ม/แก้ไขสินค้า (Product CRUD)** | ฟอร์มจัดการสินค้าเต็มรูปแบบ: อัปโหลดภาพ/ถ่ายรูป 900x900, สุ่มรหัสบาร์โค้ด, คำนวณ Margin %, สลับรายการด่วน, เปิด/ปิดระบบตัดสต็อก |
| `modal-barcode-match` | **จับคู่บาร์โค้ดใหม่ (Re-new Barcode)** | นำบาร์โค้ดที่เพิ่งยิง มาจับคู่ผูกเข้ากับสินค้าเดิมที่มีอยู่ในระบบได้อย่างรวดเร็ว |
| `modal-quick-quantity` | **แป้นตัวเลขปรับจำนวนสินค้าด่วน** | อยู่ใน `views/pos.html` มีจอ LED Scanline, ปุ่มลัดแพ็ค/โหล (+1, 6, 12, 24) และคีย์แพด 0-9 |
| `modal-stockin-product-picker` | **เลือกจับคู่สินค้าใน Stock-In** | ค้นหาและจับคู่สินค้าเดิมในระบบด้วย 2 ระบบหลัก: 🎙️ สั่งการด้วยเสียง (Web Speech API) และ 📷/⚡ สแกนบาร์โค้ด (กล้องและปืนยิง) พร้อมค้นหาชื่อไทยตรงตัวไม่แปลงสระเพี้ยน |
| `modal-stockin-cat-unit` | **กำหนดหมวดหมู่และหน่วยนับ** | ป๊อปอัปเลือกหมวดหมู่และหน่วยนับสำหรับรายการสินค้าใหม่ใน Staging Table |
| `modal-cost-diff-detail` | **แจ้งเตือนผลต่างราคาทุน (Cost Variance)** | แสดงการเปรียบเทียบระหว่างทุนเดิมในระบบ กับทุนใหม่จากใบเสร็จ เพื่อพิจารณาปรับราคาขายปลีก |
| `modal-stockin-confirm-save` | **ยืนยันการบันทึกสต็อก AI** | สรุปจำนวนรายการ, จำนวนชิ้นรวม, มูลค่าทุนรวม และปุ่มกดยืนยันขนาดใหญ่สำหรับจอสัมผัส |
| `modal-stockin-success` | **แจ้งผลบันทึกสต็อกสำเร็จ** | แสดงจำนวนรายการที่สำเร็จ และแสดงรายการที่ผิดพลาด (ถ้ามี) ก่อนปิดและส่งโฟกัสกลับช่องบาร์โค้ด |
| `camera-scanner-modal` | **กล้องสแกนบาร์โค้ด (Camera Barcode)** | เปิดกล้องหลังเพื่อสแกนบาร์โค้ด 1D/2D ด้วยไลบรารี Html5-Qrcode |
| `prod-camera-modal` | **กล้องถ่ายรูปสินค้า (Product Camera)** | สตรีมวิดีโอจากกล้องเพื่อถ่ายภาพสินค้า จัดเฟรมสี่เหลี่ยมจัตุรัส แล้วส่งเข้า Image Pipeline |
| `order-detail-modal` | **รายละเอียดบิลขายย้อนหลัง** | แสดงรายการสินค้า, ช่องทางชำระ, ส่วนลด, VAT และมีปุ่มพิมพ์สลิปซ้ำ (Reprint) หรือยกเลิกบิล (Void Order) |
| `cat-add-modal` | **เพิ่มหมวดหมู่ด่วน** | ป๊อปอัปขนาดกะทัดรัดสำหรับเพิ่มหมวดหมู่สินค้าใหม่อย่างรวดเร็วขณะทำงาน |
| `category-form-modal` | **จัดการหมวดหมู่สินค้า** | ฟอร์มสร้าง/แก้ไขหมวดหมู่สินค้าและกำหนดลำดับการแสดงผล (`sort_order`) |
| `prod-csv-modal` | **นำเข้าสินค้าผ่านไฟล์ CSV** | อัปโหลดไฟล์ CSV สินค้าเพื่อนำเข้าข้อมูลพร้อมกันเป็นชุด (Batch Import) |
| `prod-export-modal` | **ส่งออกข้อมูลสินค้า (Export Products)** | ส่งออกแคตตาล็อกสินค้าในระบบเป็นไฟล์ CSV หรือรายงานสรุป |
| `prod-rls-guide-modal` | **คู่มือการตั้งค่า Supabase RLS** | แนะนำคำสั่ง SQL และการกำหนดนโยบาย Row Level Security บน Supabase |
| `gdrive-guide-modal` | **คู่มือการติดตั้ง Google Apps Script** | แสดงขั้นตอนการสร้าง Web App บน script.google.com สำหรับเก็บรูปภาพลง Google Drive |
| `modal-ios-pwa-guide` | **คู่มือการติดตั้ง PWA บน iOS/iPadOS** | แนะนำวิธีเพิ่มเว็บแอปลงในหน้าจอโฮม (Add to Home Screen) บน Safari |

---

## 8. เจาะลึกการทำงานของทุกโมดูล (Core Application Modules)

> **มาตรฐานวงจรชีวิตโมดูล (Standardized Module Lifecycle):** ทุกโมดูลหน้าจอทั้ง 7 โมดูลได้รับการออกแบบตามสถาปัตยกรรมจัดการหน่วยความจำแบบเดียวกัน:
> 1. `this.disposables = []`: สำหรับเก็บ Callback ฟังก์ชัน Unsubscribe จาก `appBus` และ DOM Event Listeners
> 2. `async init()` หรือ `init()`: เมธอดเริ่มต้นสำหรับดึงข้อมูล ผูกเหตุการณ์ และสั่งเรนเดอร์ UI เมื่อสลับเข้ามายังหน้านั้น
> 3. `destroy()`: เมธอดปลดเปลื้องทรัพยากร วนลูปสั่งทำงานทุก Callback ใน `this.disposables` และเคลียร์ค่า Pointer ใน DOM เพื่อป้องกัน Memory Leak 100% ควบคุมการทำงานอัตโนมัติผ่าน `setActiveModule()` ใน `viewLoader.js`

### 8.1 `js/modules/pos.js` (`PosModule`)
- **Lifecycle & Zero Memory Leaks**: รองรับ `async init()` และ `destroy()` อย่างสมบูรณ์แบบ ทำการถอดถอนคีย์บอร์ดลิสเนอร์, Event Bus, ล้าง timers (`_searchDebounceTimer`, `_scanFocusTimer`) และ **ตัดการเชื่อมต่อตัวแปร DOM references ทั้งหมดกว่า 25 ตัวเป็น `null`** เพื่อให้ Garbage Collection ทำงานได้ทันที กำจัด Detached DOM Tree leaks 100%
- **Clean Audio Architecture & Zero AudioContext Leaks**: รวมการเล่นเสียงตอบรับผ่าน `playAudio` ใน `audio.js` (Singleton AudioContext + Compressor) ลบเมธอดซ้ำซ้อนและตัดโค้ดเก่าที่สร้าง `new AudioCtx()` ใหม่ ป้องกันการรั่วไหลของหน่วยความจำเสียง
- **Category Tabs Event Delegation**: ผูก event listener `click` เข้ากับคอนเทนเนอร์ `#pos-category-tabs` เพียงจุดเดียวใน `bindDOMEvents()` โดยใน `renderCategories()` เหลือเพียงการอัปเดต `innerHTML` ขจัด DOM event churn จากการวนลูปผูกทุกปุ่มในทุกรอบเรนเดอร์
- **Smart Debounced Search (150ms)**: เพิ่มการหน่วงเวลา 150ms ในช่องค้นหา `#pos-search-input` ขณะผู้ใช้กำลังพิมพ์ เพื่อป้องกันการคำนวณ Sorting/Filtering ซ้ำซ้อน และสั่งเรนเดอร์ทันทีเมื่อกด `Enter`, `Escape` หรือกดล้างคำค้น
- **Zero-Latency Touch Ergonomics**: กำหนด `touch-action: manipulation` บนปุ่ม `.pixel-btn-sm`, `.pos-cat-tab`, `.pos-cart-step-btn` และ `.pos-cart-del-btn` ตัดความหน่วง 300ms ของเบราว์เซอร์สัมผัสบนแท็บเล็ต 1024x640
- **High-Performance In-Memory Lookups ($O(1)$)**: มีดัชนีคู่ `_productById` และ `_productByBarcode` (Hash Maps) รองรับทั้ง String และ `safeNumberId` ดึงข้อมูลสินค้าทันที < 0.05ms เมื่อคลิกการ์ดสินค้า (`addToCartById`), วาดรายการตะกร้า (`updateCartUI`), หรือยิงบาร์โค้ด (`handleBarcodeScan`)
- **Single-Pass Category Aggregation ($O(N)$)**: ปรับปรุง `renderCategories()` นับจำนวนสินค้าและยอดขายดีในลูปเดียว ขจัดปัญหา CPU Spikes และความหน่วงจากการสลับหมวดหมู่
- **Pre-computed Sales Stats & Persistent Thai Collator**: เรียงลำดับสินค้าขายดีเร็วขึ้น 4–8 เท่าด้วย `thaiCollator = new Intl.Collator('th')` และการแนบสถิติยอดขายล่วงหน้า
- **Ultra-Fast Modal Check**: ใช้ `ALL_MODAL_SELECTORS` รวบการค้นหา DOM ใน `isAnyModalOpen()` ช่วยให้ปืนสแกนเนอร์ส่งข้อมูลได้ต่อเนื่อง ไร้อาการตกหล่น
- จัดการ State ของตะกร้าสินค้า (`this.cart`)
- การคำนวณเงินสด: เมธอด `getTotals()` คืนค่า `subtotal`, `discount`, `netAmount`, `totalCost`, `profit`
- เมธอด `updateQuantity(productId, delta)`: ปรับเพิ่ม/ลดจำนวนสินค้า
- เมธอด `openQuantityNumpad(productId)` / `confirmQuantityNumpad()`: จัดการแป้นพิมพ์ LED Numpad บนหน้าจอ
- การพักบิลและเรียกบิล: `holdCurrentCart()` และ `restoreHeldCart(holdId)` เก็บใน Local Memory และเปิด Modal จัดการ
- การจบการขาย (`finalizeCheckout()`):
  - เขียนข้อมูลลง `orders` และ `sale_items` ผ่าน `resilientInsert`
  - หักลบสต็อกสินค้าใน `products` ผ่าน `resilientUpdate`
  - บันทึกประวัติลง `stock_movements` (ประเภท `SALE`)
  - อัปเดตแคชใน Dexie IndexedDB
  - สั่งพิมพ์ใบเสร็จความร้อน (หากเปิดตัวเลือกไว้)
  - เล่นเสียงสังเคราะห์ `stage_clear` และเปิด `pos-success-modal`

### 8.2 `js/modules/products.js` (`ProductsModule`)
- **Lifecycle Implementation**: รองรับ `async init()` (ดึงสินค้า, อัปเดตสถิติ Inventory KPIs, เรนเดอร์ตาราง และดักฟัง `products:changed`) และ `destroy()` (เคลียร์ Disposables และ Table References)
- โหลดแคตตาล็อกสินค้าจาก Supabase และซิงค์ลง Dexie IndexedDB
- เรนเดอร์ตารางสินค้า รองรับการค้นหา Full-text (ชื่อ, บาร์โค้ด) และการกรองตามหมวดหมู่/สถานะ
- จัดการฟอร์มบันทึกสินค้า (รองรับทั้งการสร้างใหม่และการแก้ไข) โดยมีระบบปิด Browser Autocomplete
- ฟังก์ชันส่งออกข้อมูลสินค้าเป็นไฟล์ CSV (`exportToCSV()`) และนำเข้าสินค้าจากไฟล์ CSV (`importFromCSV()`)
- ระบบสร้างป้ายบาร์โค้ดสำหรับนำไปสั่งพิมพ์สติกเกอร์บาร์โค้ดติดสินค้า
- ฟังก์ชันปรับสต็อกสินค้าด่วน (`adjustStock()`) พร้อมบันทึกประวัติความเคลื่อนไหว

### 8.3 `js/modules/categories.js` (`CategoriesModule`)
- **Lifecycle Implementation**: รองรับ `async init()` (โหลดหมวดหมู่, นับจำนวนสินค้า, ดักฟัง `categories:changed` และ `products:changed`) และ `destroy()` (ถอดถอน Disposables)
- จัดการหมวดหมู่สินค้า (CRUD)
- คำนวณจำนวนสินค้าที่สังกัดอยู่ในแต่ละหมวดหมู่แบบไดนามิก
- เลื่อนลำดับการแสดงผลขึ้น/ลง (`moveOrder()`) และบันทึกฟิลด์ `sort_order` ใหม่ลงฐานข้อมูล

### 8.4 `js/modules/orders.js` (`OrdersModule`)
- **Lifecycle Implementation**: รองรับ `async init()` (โหลดบิลประวัติการขาย, ผูกปุ่มตัวกรองช่วงเวลา, เรนเดอร์ตาราง) และ `destroy()` (ถอดถอน Disposables)
- **ระบบตัดแบ่งหน้า (Pagination Engine)**: แสดงผลหน้าละ 25 รายการ (`pageSize = 25`) เช่นเดียวกับ `products.js` พร้อมปุ่ม ย้อนกลับ / ถัดไป และปุ่มตัวเลขหน้า ซ่อนแถบ Pagination อัตโนมัติหากรายการบิล $\le 25$ รายการ ขจัด DOM Lag จากประวัติบิลสะสม
- **ตัวกรองช่วงเวลาด่วน (Touch-Friendly Date Presets)**:
  - กำหนดค่าเริ่มต้นเป็น **"วันนี้ (Today)"** เสมอเมื่อเปิดเข้าสู่หน้า Orders
  - ชุดปุ่มกดสัมผัสขนาด $\ge 36$px: วันนี้, เมื่อวานนี้, สัปดาห์นี้, สัปดาห์ที่แล้ว, เดือนนี้ และกำหนดเอง (Custom Range)
  - คำนวณช่วงเวลาอิง Local Timezone แม่นยำ ปราศจากปัญหา UTC Date-Shift
- **แถบสรุปภาพรวมคำนวณจากยอดรวมทั้งหมด (Accurate Summary KPIs Bar)**:
  - คำนวณยอดขายรวมสุทธิ ต้นทุนรวม และกำไรสุทธิรวม จาก **บิลทั้งหมดที่ผ่านการกรอง (Total Filtered Orders)** ไม่ใช่คำนวณเฉพาะ 25 รายการในหน้าที่กำลังแสดงผล
  - แสดงจำนวนบิลทั้งหมด บิลสำเร็จ และบิลยกเลิกอย่างชัดเจน
- แสดงรายละเอียดสลิปใบเสร็จ และสั่งพิมพ์ซ้ำผ่านเครื่องพิมพ์ความร้อน
- ระบบยกเลิกบิล (`voidOrder()`):
  1. ปรับสถานะบิลใน `orders` เป็น `VOIDED`
  2. วนลูปคืนยอดจำนวนสินค้าใน `sale_items` กลับเข้าฟิลด์ `stock` ในตาราง `products`
  3. บันทึกความเคลื่อนไหวใน `stock_movements` (ประเภท `VOID`)
  4. อัปเดตแคชใน Dexie และกระจายสัญญาณผ่าน `appBus`
  5. คงการแสดงผลที่หน้าเดิมของผู้ใช้แบบ In-Place โดยไม่กระโดดกลับไปหน้าแรกโดยไม่จำเป็น

### 8.5 `js/modules/stock-in.js` (`StockInModule` - AI Stock-In & Inventory Restock)
- **Lifecycle & Event Guard Implementation**: รองรับ `async init()` (โหลดแคตตาล็อกสินค้าสำหรับ Auto-match, ผูก Event คอนโทรล Staging) และ `destroy()` (เคลียร์ Disposables, หยุด Voice Engine และกล้องสแกน) แยก `bindDOMEvents()` พร้อมการ์ด `_domEventsBound` ป้องกัน Event Listener สะสมซ้อนทับ และแยก `bindLifecycleEvents()` ลงใน `this.disposables` ตามกฎข้อที่ 9, 11 & 12
- **In-Memory Product Matching Map**: มี `_productMap = new Map()` ช่วยจับคู่สินค้าเดิมในตารางรับเข้าสต็อกระดับ sub-millisecond ขจัดปัญหา $O(M \times N)$ สแกนซ้ำซ้อน
- ประมวลผลภาพถ่ายใบเสร็จผ่าน `geminiApi.js` แล้วนำข้อมูลเข้าสู่ Staging
- **Dual View & Ergonomic Staging Review (Mathematical Formula Chunking & Zebra Striping)**:
  - **ระบบสลับสีพื้นหลังแยกรายการ (Alternating Zebra Striping)**: ทั้งการ์ดและตารางสลับสีพื้นหลังระหว่างสีขาว (`bg-white`) และสีนวลกระดาษเรโทร (`bg-[#FAF7F0]`) ช่วยให้สายตาแยกแยะรายการที่อยู่ติดกันได้อย่างรวดเร็ว ชัดเจน ไม่สับสน
  - **มุมมองการ์ด (Card View - `.stockin-card`)**: ปรับปรุงโครงสร้างตามหลักสรีรศาสตร์การตรวจรับจริง:
    - **ส่วนบน:** ป้าย `#ลำดับ` สีเหลืองเรโทร (`bg-amber-400`), ป้ายสถานะ (`✨ สินค้าใหม่` / `🔗 เดิม:`), ช่องบาร์โค้ด `🏷️`, รูปสินค้าสี่เหลี่ยมขอบประพร้อมปุ่มถ่ายรูปกล้องด่วน `📷`, และปุ่มลบ `✕` สีแดง
    - **ส่วนกลาง:** ช่องชื่อสินค้ากว้างเด่นชัด และปุ่มลัด `📁 หมวดหมู่ | 📦 หน่วย`, `🔗 จับคู่สินค้าเดิม`, `🧮 คำนวณ`
    - **กลุ่มสูตรคณิตศาสตร์ (Grouped Math Equations):**
      - `กล่อง×หน่วย`: `[-] [กล่อง] [+]` $\times$ `[-] [หน่วย/กล่อง] [+]` $=$ `[รวมชิ้น]`
      - `ราคา/หน่วย`: `[กล่องละ ฿]` $/$ `[หน่วย/กล่อง]` $=$ `[ทุน/หน่วย ฿]`
      - `ราคาขาย(กำไร)`: ราคาขายตัวเลขขนาดใหญ่ `[ราคาขาย] ฿` พร้อมกำไรชัดเจน `[+กำไร (Margin%)]`
    - **แถบล่าง (Footer Bar):** `รวมรับเข้า: [รวมชิ้น] หน่วย | คงเหลือใหม่: [สต็อกใหม่] หน่วย`
  - **มุมมองตาราง (Table View)**: เรียงคอลัมน์ตามขั้นตอนการคำนวณจริงจากซ้ายไปขวา (`จำนวนกล่อง` $\times$ `หน่วย/กล่อง` $=$ `รวมรับเข้า` | `กล่องละ ฿` $\rightarrow$ `ทุน/หน่วย ฿` | `ราคาขาย ฿` พร้อมป้ายกำไร) พร้อมสลับสีแถว Zebra Striping
- **Dual-System Product Picker Modal (`#modal-stockin-product-picker`)**:
  - **🎙️ ค้นหาด้วยเสียง [Voice Search]**: สั่งการด้วยเสียงภาษาไทยผ่าน Web Speech API มี Pulse Animation แบนเนอร์แสดงข้อความเสียงสด
  - **📷/⚡ สแกนบาร์โค้ด**: รองรับปืนสแกนเนอร์และกล้องถ่ายรูปสแกนบาร์โค้ด 1D/2D
  - **ค้นหาภาษาไทยตรงตัว (Thai Keyword Preservation)**: แก้ไขไม่ให้คำค้นหาภาษาไทยถูกบิดเบือนด้วย `normalizeThaiBarcode` ค้นพบชื่อสินค้าภาษาไทยได้ทันที
- ฟังก์ชัน `addManualItem()`: เพิ่มรายการสินค้าเข้า Staging ด้วยตนเอง
- เมธอด `syncStagingFromDOM()`: ดึงค่าล่าสุดจาก Input บนหน้าจอก่อนกดบันทึก ป้องกันข้อมูลสูญหาย
- บันทึกสต็อกเข้าสู่ระบบ: อัปเดตสินค้าเดิม หรือเพิ่มสินค้าใหม่ พร้อมบันทึก `stock_movements` (ประเภท `STOCK_IN`)
- รองรับการคำนวณกรณีสินค้าเดิมมียอดสต็อกติดลบ (เช่น เดิม -2 ชิ้น รับเข้า 10 ชิ้น ยอดใหม่จะเป็น 8 ชิ้น พร้อมบันทึกหมายเหตุ)
- มีโมดัลยืนยัน (`modal-stockin-confirm`) และโมดัลแจ้งผลสำเร็จ (`modal-stockin-success`)

### 8.6 `js/modules/reports.js` (`ReportsModule` - AI-Analytics & Strategy Center)
- **Lifecycle & Memory Management**: รองรับ `async init()` (คำนวณ KPIs, สร้างอินสแตนซ์ Chart.js 5 รูปแบบ) และ `destroy()` (ทำลาย Chart.js Instances เก่าทิ้ง และเคลียร์ Disposables ป้องกัน Memory Leak ใน Canvas)
- **สถาปัตยกรรม Bento Overview Hub + Dedicated Detail Panels**:
  - **Bento Overview Hub (`#dash-bento-overview`)**: สรุปภาพรวมระดับผู้บริหารด้วย 5 การ์ด Bento กะทัดรัด (AI Strategic Insights, Peak Hours & Timeline, Margin vs Volume Matrix, ตรวจสุขภาพคลังสินค้า & สต็อกวิกฤต, และอันดับสินค้า & หมวดหมู่)
  - **Dedicated Detail Panels (`#dash-detail-container`)**: หน้ารายละเอียดเจาะลึก 4 ส่วน (AI Command, ยอดขายและกราฟแนวโน้ม, เมทริกซ์จัดเกรดสินค้า, และตารางตรวจสต็อกติดลบ/สินค้าเงินจม) พร้อม **ปุ่มตรึงด้านบน `[ 🏠 กลับหน้าหลัก (Home) ]`** เพื่อสลับกลับสู่หน้าภาพรวม Bento ได้ทันที
- **High-Contrast Dual-Theme Support (`.dash-ai-card`)**: การ์ด AI สไตล์ Command Center สีเข้ม (`#0F172A` / `#061E16`) ให้ตัวหนังสือทอง, เขียว และฟ้า คมชัด อ่านง่าย 100% ทั้งในธีม Classic (Warm Arcade) และ Cyber Emerald
- **Single-Point Safe Product Edit**: ตารางสต็อกวิกฤตมีปุ่ม `[ ✏️ แก้ไข ]` เพียงจุดเดียวในคอลัมน์จัดการ (ตัดไอคอนดินสอท้ายชื่อสินค้าออก) พร้อมกลไก Multi-Tier Fallback ส่งข้อมูลสินค้าเข้าสู่ `#prod-crud-modal` แม้หน้าแคตตาล็อกยังไม่เคยถูกโหลด
- **Multi-Color Category Distribution Doughnut Chart**: กราฟสัดส่วนหมวดหมู่สินค้าใช้ `categoryPalette` หลากสีเรโทรเรืองแสง แจกจ่ายสีสดใสให้แต่ละหมวดหมู่แตกต่างกันอย่างชัดเจน ตรงกันระหว่างชิ้นพายบนกราฟและจุดหัวข้อในตาราง
- **Gemini Flash Failover Pool**: วิเคราะห์กลยุทธ์ยอดขาย สินค้าที่เป็นหัวหอก และรายการสั่งของเข้าสต็อกอัตโนมัติผ่าน Google Gemini Flash API พร้อมระบบ In-Memory Cache ตามช่วงเวลา

### 8.7 `js/modules/settings.js` (`SettingsModule`)
- **Lifecycle Implementation**: รองรับ `async init()` (ดึงค่า `app_settings` ล่าสุด, ตั้งค่า Form Fields, ผูก Event คอนโทรล) และ `destroy()` (ถอดถอน Disposables)
- บันทึกการตั้งค่าร้านค้าลง Supabase ตาราง `app_settings` และแคชใน Dexie
- จัดการข้อมูลพร้อมเพย์ และระบบ QR Code ชำระเงิน
- จัดการเครื่องพิมพ์ความร้อน (58mm/80mm) และปุ่มทดสอบพิมพ์
- ทดสอบการเชื่อมต่อ Google Gemini API และ Google Apps Script Web App

---

## 9. ชุดเครื่องมือและฟังก์ชันช่วยเหลือ (Utilities & Helper Libraries)

| ไฟล์ (File Path) | บทบาทและฟังก์ชันการทำงานหลัก |
| :--- | :--- |
| `js/utils/dbHelper.js` | **Resilient Database Layer & Scoped Event Bus**: ฟังก์ชัน `resilientUpdate()`, `resilientInsert()`, `resilientDelete()` รองรับการสลับยิงผ่าน Public Anon Client อัตโนมัติเมื่อ Session หลุดหรือติด RLS, ตรวจจับและตัดฟิลด์ที่ไม่มีในสกีมาทิ้งแบบ Recursive ด้วย `while` loop แล้ว Retry ทันที, รองรับลำดับอาร์กิวเมนต์แบบยืดหยุ่น และมี `appBus.on()` ที่คืนฟังก์ชัน Unsubscribe สำหรับเก็บใน `this.disposables` เพื่อป้องกัน Memory Leak เมื่อโมดูลถูกทำลาย |
| `js/utils/barcodeHelper.js` | **Thai Barcode Layout Fixer**: ฟังก์ชัน `normalizeThaiBarcode()` และ `getCharFromPhysicalEvent()` แปลงบาร์โค้ดจากปืนสแกนเนอร์ภาษาไทย (ผังปัตตโชติบน Alldocube OS) กลับเป็นเลขอารบิก `0-9` ด้วยการแปลงตรง 1-to-1 (`THAI_PATTACHOTE_REF_MAP`) แก้ปัญหาเครื่องสแกนพ่น `=` แทนเลข `1` และ `ู` แทนเลข `6` รวดเร็ว แม่นยำ และไม่ใช้เงื่อนไขซับซ้อน |
| `js/utils/audio.js` | **Chiptune Audio Synthesizer & Shared AudioContext Provider**: สังเคราะห์เสียง 8-bit ด้วย Web Audio API เช่น `beep`, `hit`, `coin`, `powerup`, `laser`, `jump`, `error`, `stage_clear`, `plus`, `minus`, `select` พร้อมระบบปรับระดับเสียง (Master Volume Bus & Dynamics Compressor), จำสถานะเปิด/ปิดเสียง Mute ใน LocalStorage (`toggleAudioMute()`, `isAudioMuted()`) และทำหน้าที่เป็น **Shared Singleton AudioContext Provider** (`getAudioContext()`) ใช้งานร่วมกับ `thaiSpeechHelper.js` ป้องกันปัญหาแย่ง Audio Hardware Channel บนแท็บเล็ต Android |
| `js/utils/themeHelper.js` | **Dual-Theme Manager**: จัดการสลับธีมคู่ขนานระหว่าง **`warm-arcade`** (คลาสสิกเรโทร สีครีม-ทอง-ดำ) และ **`cyber-emerald`** (นีออนไซเบอร์ ดำ-เขียวมรกต คอนทราสต์สูง) จัดเก็บการตั้งค่าลง LocalStorage ซิงค์สีแถบเบราว์เซอร์ผ่าน `<meta name="theme-color">` และกระจายเหตุการณ์ `theme:changed` เข้าสู่ Event Bus |
| `js/utils/uiHelper.js` | **Retro Micro-Loader Overlay**: ยูทิลิตี้แสดงผลหน้าจอโหลดขนาดเล็กแบบ Hardware-Accelerated สไตล์เรโทร (`showLoader()`, `hideLoader()`, `withLoader()`) ป้องกันการกดซ้ำซ้อน และเคลียร์สถานะปลอดภัยใน `finally` block เสมอ |
| `js/utils/geminiApi.js` | **Gemini Vision Integration**: เชื่อมต่อ Gemini API พร้อมระบบ **Multi-Model Failover Pool** (`gemini-2.5-flash` ➔ `gemini-2.0-flash` ➔ `gemini-1.5-flash`), ควบคุม Timeout ด้วย `AbortController` (15s สำหรับทดสอบ, 45s สำหรับสแกนบิล), และถอด Markdown Code Fences อัตโนมัติ |
| `js/utils/promptpay.js` | **PromptPay EMVCo QR Generator**: สร้าง String ตามมาตรฐาน EMVCo รองรับเบอร์โทรศัพท์ 10 หลัก และเลขผู้เสียภาษี 13 หลัก พร้อมคำนวณค่า Checksum ตามอัลกอริทึม **CRC16-CCITT (0xFFFF)** อย่างถูกต้อง |
| `js/utils/thermal-printer.js` | **Thermal Print Formatter**: จัดหน้าสลิปใบเสร็จรับเงินสำหรับกระดาษความร้อน 58 มม. และ 80 มม. มีข้อมูลหัวบิล, ตารางรายการสินค้า, ส่วนลด, VAT, ยอดสุทธิ, เงินสด, เงินทอน, และบาร์โค้ดท้ายบิล |
| `js/utils/imageUploader.js` | **Camera & Drive Uploader**: บีบอัดภาพถ่ายจากกล้องให้มีขนาดเหมาะสม แปลงเป็น Base64 แล้วส่ง POST ไปยัง Google Apps Script Web App เพื่อจัดเก็บบน Google Drive |
| `js/utils/gdrive.js` | **Google Drive CDN Linker**: แปลง URL ของไฟล์ใน Google Drive ไปเป็น High-Performance Google CDN Direct Image URL (`https://lh3.googleusercontent.com/d/{FILE_ID}`) ช่วยให้เบราว์เซอร์และ Service Worker (`sw.js`) แคชภาพลง Cache Storage (`pixel-pos-images-v1`) ได้รวดเร็ว โหลดภาพได้ทันที 100% แม้ในโหมดออฟไลน์ |
| `js/sync.js` | **Resilient Background Sync & Queue Manager**: บริหารจัดการคิวบิลออฟไลน์ (`offline_orders`) ใน Dexie.js เพื่อรองรับ Local-First Optimistic Checkout (< 50ms), ตรวจจับสัญญาณเน็ต/Heartbeat, ส่งข้อมูลขึ้น Supabase ในเบื้องหลังผ่าน `resilientInsert`/`resilientUpdate` ควบคู่กับการควบคุม Notification Badge, Animated Floating Toast และลงทะเบียน `reg.sync.register('sync-offline-orders')` กับ Service Worker |
| `js/utils/clipboard.js` | **Clipboard Copy Helper**: ฟังก์ชันช่วยคัดลอกข้อความลง Clipboard พร้อมแสดงกล่องข้อความ Toast แจ้งเตือน ใช้งานเป็นมาตรฐานเดี่ยวทั่วทั้งระบบ |
| `js/utils/productMatcher.js` | **Intelligent Product Matcher & Bilingual Engine**: เอนจินจับคู่และค้นหาสินค้าส่วนกลางสำหรับ Voice-to-Cart, POS/Products Search และ AI Stock-In OCR รองรับการแปลงคำทับศัพท์ EN <-> TH Cartesian expansion (ให้ความสำคัญกับคำไทยก่อน), Space-Agnostic Substring matching, ระบบป้องกันความขัดแย้งของสี (Color Mismatch Hard Penalty), ระบบความโดดเด่นของแบรนด์หลัก (Brand Dominance) และการตัดคำขยะในบิล (Receipt Noise & Pack Stripper) |
| `js/utils/wakeLock.js` | **Screen Wake Lock Engine**: จัดการการล็อกหน้าจอไม่ให้ดับ (Screen Wake Lock API) บนแท็บเล็ตแคชเชียร์ขณะใช้งานหน้าขาย POS อัตโนมัติ ป้องกันบลูทูธของปืนสแกนเนอร์หลับ คืนสถานะเมื่อสลับหน้า พร้อมสวิตช์เปิด/ปิด (ON/OFF) และไฟสถานะในหน้า Settings แท็บ *"หน้าขาย POS & ธีมสี"* |
| `js/utils/storageHelper.js` | **Persistent Storage Provider**: ขอสิทธิ์จัดเก็บข้อมูลถาวร (`navigator.storage.persist()`) เพื่อป้องกันไม่ให้เบราว์เซอร์ล้าง IndexedDB (Dexie) ทิ้งโดยพลการเมื่อพื้นที่เครื่องใกล้เต็ม พร้อมตรวจสอบโควตาการใช้งานพื้นที่ (Storage Quota) ในแท็บ PWA |
| `js/utils/cameraScanner.js` | **Hardware-Accelerated Camera Barcode Scanner**: ถอดรหัสบาร์โค้ดผ่านกล้องด้วย `window.BarcodeDetector` ระดับฮาร์ดแวร์ของ Chrome Android รวดเร็วและกิน CPU น้อยกว่าไลบรารี JS เดิมกว่า 60% พร้อมระบบ Fallback สู่ `Html5Qrcode` อัตโนมัติ |

---

## 10. ตรรกะและ Business Logic สำคัญของระบบ

### 10.1 ตรรกะการคำนวณเงินหน้าร้าน (POS Calculations)
- **Subtotal (ยอดรวมก่อนส่วนลด)**: ผลรวมของ `(unit_price * quantity)` ของทุกรายการในตะกร้า
- **Discount (ส่วนลดท้ายบิล)**:
  - หากระบุเป็น `%`: `discountAmount = Math.round(((subtotal * percent) / 100) * 100) / 100`
  - หากระบุเป็น `บาท`: `discountAmount = Math.min(rawDiscount, subtotal)`
- **Net Amount (ยอดสุทธิ)**: `netAmount = Math.max(0, subtotal - discountAmount)`
- **Cost & Profit (ต้นทุนและกำไร)**:
  - `totalCost = sum(unit_cost * quantity)`
  - `grossProfit = netAmount - totalCost`
- **VAT (ภาษีมูลค่าเพิ่ม)**:
  - หากเปิดโหมดแสดง VAT: คำนวณยอดก่อนภาษีและภาษีตามอัตราที่กำหนด (ค่าเริ่มต้น 7%) แสดงในสลิปใบเสร็จ

### 10.2 ตรรกะการแปลงแพ็คและปัดเศษต้นทุน AI (Pack to Unit Decomposition)
- **จำนวนชิ้นรับเข้าจริง**: `totalUnits = pack_qty * units_per_pack`
- **ต้นทุนต่อชิ้น**:
  ```javascript
  costPerPiece = Math.ceil((cost_per_pack / units_per_pack) * 1000) / 1000;
  ```
  *(ปัดเศษขึ้นที่ทศนิยม 3 ตำแหน่งเสมอ เพื่อป้องกันปัญหากำไรขาดหายจากการปัดเศษลงเมื่อแยกจำหน่ายเป็นชิ้น)*

### 10.3 ตรรกะการแก้ปัญหาสต็อกเดิมติดลบ (Negative Stock Resolution)
- หากสินค้าเดิมในระบบมียอดติดลบ เช่น `-2 ชิ้น` (จากการขายหน้าร้านล่วงหน้าก่อนบันทึกสต็อกเข้า)
- เมื่อรับเข้า `10 ชิ้น`: ยอดสต็อกคงเหลือใหม่จะเป็น `-2 + 10 = 8 ชิ้น`
- บันทึกหมายเหตุชัดเจนลงใน `stock_movements` ว่าได้หักลบยอดติดลบเดิมเรียบร้อยแล้ว

### 10.4 ตรรกะการยกเลิกบิล (Order Void Logic)
- เมื่อสั่งยกเลิกบิลที่หน้าประวัติการขาย (`orders.js`):
  1. ปรับสถานะบิลในตาราง `orders` เป็น `VOIDED` พร้อมบันทึกเหตุผลใน `void_reason`
  2. วนลูปสินค้าทุกรายการใน `sale_items` ของบิลนั้น
  3. คืนยอดจำนวนสินค้ากลับเข้าฟิลด์ `stock` ในตาราง `products`
  4. บันทึกประวัติลง `stock_movements` ประเภท `VOID` เพื่อความโปร่งใสในการตรวจสอบบัญชี

### 10.5 ตรรกะการสร้าง PromptPay EMVCo QR Code
- สร้าง String ตามมาตรฐาน EMVCo Tag-Length-Value (TLV):
  - Tag 00: Version (`01`)
  - Tag 01: QR Type (`12` สำหรับ Dynamic QR ที่ระบุจำนวนเงิน)
  - Tag 29: Merchant Account Information (รองรับเบอร์โทรศัพท์มือถือ 10 หลัก แปลงเป็น Format `0066...` หรือ เลขประจำตัวผู้เสียภาษี 13 หลัก)
  - Tag 53: Currency Code (`764` - สกุลเงินบาท THB)
  - Tag 54: Transaction Amount (ระบุทศนิยม 2 ตำแหน่ง)
  - Tag 58: Country Code (`TH`)
  - Tag 63: Checksum (คำนวณด้วยอัลกอริทึม **CRC16-CCITT** ค่าเริ่มต้น `0xFFFF` ผลลัพธ์เป็นเลขฐานสิบหก 4 หลักพิมพ์ใหญ่)

### 10.6 ตรรกะการจับคู่สินค้าอัจฉริยะแบบสองภาษาและการตรวจจับข้อขัดแย้ง (Intelligent Bilingual Product Matcher)
- **การใช้งานร่วมกันทั้งระบบ (Universal Matcher Engine)**: ตรรกะใน `js/utils/productMatcher.js` ถูกใช้งานเป็นหัวใจหลักใน 3 ส่วน:
  1. **Voice-to-Cart (สั่งซื้อด้วยเสียง)**: แปลงเสียงพูดภาษาไทย คำสั่งจำนวน หน่วยนับ และช่วงราคา
  2. **ช่องค้นหาสินค้า (POS & Products Search)**: ค้นหาคำกลางคำ/ท้ายคำ และคำทับศัพท์
  3. **AI Stock-In (Gemini Vision OCR)**: นำข้อความที่อ่านได้จากบิลส่งของจริงมาจับคู่กับสินค้าในคลัง
- **Bilingual & Sound-Alikes Cartesian Expansion**: แปลงคำทับศัพท์ภาษาอังกฤษ (เช่น `ready gojiberry`, `sms`, `3in1`) ให้เป็นคำภาษาไทยก่อน (เช่น `เรดดี้ โกจิเบอร์รี่`, `เอสเอ็มเอส`, `ทรีอินวัน`) สอดคล้องกับคลังสินค้าที่กว่า 90% บันทึกด้วยภาษาไทย
- **Space-Agnostic Substring Matching**: ค้นหาเจอแม้พิมพ์หรือบันทึกแบบติดกันหรือเว้นวรรคไม่ตรงกัน
- **Color Conflict Hard Penalty**: ตรวจจับสี (แดง, เขียว, ฟ้า, ฯลฯ) หากบิลระบุสีหนึ่งแต่สินค้าเป็นอีกสีหนึ่ง จะถูกหักคะแนน -50 ทันที ป้องกันการจับคู่สลับสีข้ามสินค้า
- **Brand Dominance & Conflict Protection**: หากบิลระบุแบรนด์เด่นชัด (เช่น เบอร์ดี้, โค้ก, LM) สินค้าที่มีแบรนด์ตรงกันจะได้โบนัส +18 และสินค้าแบรนด์คู่แข่ง (เช่น เนสกาแฟ, เป๊ปซี่) จะถูกหักคะแนน -50 ทันที
- **Receipt Noise & Pack Stripper**: ตัดคำขยะในบิลค้าส่ง เช่น ขนาดบรรจุ (0.360L, 100มล.), ขนาดแพ็ค (ลัง 5แพ็คx10ข., หีบ 10 ซอง), ราคาในบิล (10 บ., 3 บ.), ดอกจัน และเว้นวรรคคำอัตโนมัติ

---

## 11. ระบบ AI Stock-In สแกนใบเสร็จด้วย Gemini Vision

ระบบรับเข้าสต็อก AI ทำงานผ่าน Pipeline ดังนี้:

```text
[อัปโหลดรูปภาพใบเสร็จ หรือถ่ายรูปด้วยกล้อง]
                   │
                   ▼
[ย่อขนาดและบีบอัดภาพฝั่งไคลเอนต์ (Canvas JPEG Compression)]
                   │ (แสดง Badge: ⚡ บีบอัดภาพเพื่อความเร็ว AI เรียบร้อย)
                   ▼
[ส่ง Prompt + ภาพ Base64 ไปยัง Gemini Vision API]
                   │ Multi-Model Failover Pool:
                   │ 1. gemini-2.5-flash (Primary)
                   │ 2. gemini-2.0-flash (Secondary)
                   │ 3. gemini-1.5-flash (Tertiary)
                   │ (Timeout Protection 45s ผ่าน AbortController)
                   ▼
[รับผลลัพธ์ Structured JSON (ผู้ขาย, วันที่, เลขที่บิล, รายการสินค้า)]
                   │
                   ▼
[จับคู่กับแคตตาล็อกเดิมในระบบ (Fuzzy Matching บาร์โค้ดและชื่อ)]
                   │
                   ▼
[แสดงผลใน Staging Interface เพื่อให้ผู้ใช้ตรวจสอบ]
   ├── สลับมุมมอง: 🗂️ Cards View (แท็บเล็ต) หรือ 📋 Table View (จอกว้าง)
   ├── ปรับจำนวนแพ็ค / ชิ้นต่อแพ็ค (มีปุ่ม Stepper 44px)
   ├── คำนวณราคาทุนต่อชิ้นอัตโนมัติ (ปัดเศษขึ้น 3 ตำแหน่ง)
   ├── แจ้งเตือนเมื่อราคาทุนใหม่เปลี่ยนไปจากเดิม
   └── เพิ่มรายการด้วยตนเอง (Add Manual Item) ได้ทันที
                   │
                   ▼
[กดปุ่มยืนยันบันทึกสต็อก ➔ เปิด Modal ยืนยันสรุปยอด (Confirm Modal)]
                   │
                   ▼
[บันทึกเข้า Supabase & Dexie (resilientUpdate / resilientInsert)]
                   │
                   ▼
[เปิด Modal แจ้งผลสำเร็จ (Success Modal) ➔ โฟกัสกลับช่องบาร์โค้ด]
```

---

## 12. ระบบเชื่อมต่อภาพ Google Drive ผ่าน Google Apps Script (GAS)

เพื่อช่วยให้ร้านค้าสามารถถ่ายภาพสินค้าจากกล้องแท็บเล็ต/มือถือและจัดเก็บได้ฟรีโดยไม่ต้องเช่า Cloud Storage ราคาแพง ระบบมีไปป์ไลน์เชื่อมต่อกับ Google Drive ผ่าน Google Apps Script:

1. ไฟล์สคริปต์อยู่ที่ `google-apps-script/Code.gs`
2. ผู้ใช้งานสร้างโปรเจกต์ใหม่บน [script.google.com](https://script.google.com) แล้ววางโค้ดจาก `Code.gs`
3. สั่ง Deploy เป็น **Web App** กำหนดสิทธิ์การเข้าถึงเป็น `Anyone` (ทุกคน)
4. นำ Web App URL (ลงท้ายด้วย `/exec`) ไปบันทึกในหน้าตั้งค่าของระบบ POS
5. เมื่อถ่ายรูปสินค้า ระบบจะบีบอัด แปลงเป็น Base64 และยิง POST ไปยัง Web App
6. สคริปต์จะบันทึกไฟล์ภาพลงโฟลเดอร์ `POS-Images` ใน Google Drive และส่ง Direct CDN URL (`https://lh3.googleusercontent.com/d/{FILE_ID}`) กลับมาบันทึกลงในฐานข้อมูลสินค้าทันที

---

## 13. ระบบคีย์บอร์ดและฮาร์ดแวร์สแกนบาร์โค้ด (Barcode Hardware, Adaptive Cadence & Thai Fix)

- **การจำลองแป้นพิมพ์ของปืนสแกน (Keyboard Wedge) & In-Memory Capture**:
  - ปืนยิงบาร์โค้ด USB/Bluetooth จะส่งข้อมูลเสมือนการพิมพ์แป้นพิมพ์อย่างรวดเร็วและปิดท้ายด้วยปุ่ม `Enter`
  - ดักจับเหตุการณ์ในระดับ **Capture Phase (`keydown`)** โดยสกัดตัวเลขจาก Physical Key Codes (`Digit0-9`, `Numpad0-9`) โดยตรง ไม่ขึ้นกับภาษาแป้นพิมพ์ของ OS
  - สั่ง `e.preventDefault()` ทันทีเมื่อโฟกัสอยู่ที่ช่อง `#pos-barcode-input` เพื่อป้องกันไม่ให้อักขระไทยของ Alldocube OS (ปัตตโชติ) หรือเกษมณีหลุดเข้าไปใน DOM แล้วนำค่าจาก Buffer อัปเดตลง `.value` โดยตรง ป้องกัน DOM Repaint Lag ได้ 100%
- **การแก้ปัญหา Bluetooth Standby & Low Transfer Rate Mode (Adaptive Cadence Dual-Threshold)**:
  - หัวอ่านไร้สาย Bluetooth เมื่อหลับ (Standby) หลังไม่ได้ใช้งาน เมื่อหยิบขึ้นมายิงบาร์โค้ด 1–3 ตัวแรกจะมี Latency Spike (80–150ms)
  - นอกจากนี้ สแกนเนอร์ที่ตั้งค่าเป็น Low Transfer Rate หรือการยิงบาร์โค้ดยาว 12–14+ หลัก ต้องการช่วง Cadence ที่กว้างขึ้นเพื่อป้องกันบัฟเฟอร์หลุดทิ้งกลางคัน
  - ระบบใช้เกณฑ์ความเร็วแบบปรับตัวได้ (Adaptive Cadence Dual-Threshold):
    - `CADENCE_WAKEUP = 200ms` (180ms – 220ms): ขยาย Grace Period ให้ 1–3 ตัวแรก เพื่อรองรับ Latency Spike จากการเชื่อมต่อบลูทูธ ป้องกันตัวเลขนำหน้าตกหล่น
    - `CADENCE_FAST = 120ms` (100ms – 120ms): ปรับสู่ความเร็วระดับฮาร์ดแวร์สำหรับตัวถัดไป รองรับโหมด Low Rate และตัดการพิมพ์ด้วยมือของมนุษย์ (> 250ms) ทิ้งอย่างเด็ดขาด
- **การประมวลผล Enter Handling ที่แม่นยำ**:
  - เมื่อพบ `Enter` และความยาว Buffer $\ge 8$ หลัก จะส่งเข้า `handleBarcodeScan(scannedCode)` ทันที
  - หากพบ `Enter` แต่ Buffer มีความยาว $< 8$ หลัก ระบบจะล้างบัฟเฟอร์ทิ้งทันที เพื่อป้องกันข้อมูลไม่สมบูรณ์ค้างในหน่วยความจำ
- **การแก้ปัญหาแป้นพิมพ์ภาษาไทย (Thai Barcode Direct Ref Mapping & Physical Keys)**:
  - ใช้ 2 เงื่อนไขหลักอย่างเรียบง่ายและเด็ดขาด ไม่ใช้เงื่อนไขซับซ้อน:
    1. **Physical Key Codes**: ดักจับจาก `e.code` (`Digit0-9`, `Numpad0-9`, `Equal` ➔ `'1'`) หรือ fallback `e.key` เพื่อตัดปัญหาภาษาแป้นพิมพ์ของระบบปฏิบัติการ
    2. **Thai Pattachote-ENG Reference Mapping**: แปลงอักขระตรง 1-to-1 จากข้อมูลจริงของเครื่องอ่านบาร์โค้ด (`THAI_PATTACHOTE_REF_MAP`):
       - `=` ➔ `1` (แก้ไขปัญหาเครื่องสแกนพ่น `=` ออกมาแทนเลข 1 บน Android/Alldocube OS เช่น `558=883=000020` ➔ `55818831000020`)
       - `ู` ➔ `6` (แถว Shift+6 ผังปัตตโชติ)
       - `๐-๙` ➔ `0-9` (ตัวเลขไทย)
       - พร้อม Fallback ปัตตโชติ unshifted และเกษมณี
  - หากสแกนผ่านซอฟต์แวร์อื่น หรือพิมพ์/วาง (Paste) ลงในช่องกรอกหรือช่องค้นหาสินค้า ฟังก์ชัน `normalizeThaiBarcode()` ใน `js/utils/barcodeHelper.js` จะแทนที่อักขระตาม Ref Map ทันทีโดยไม่ต้องประเมิน Signature Score ที่ซับซ้อน
- **ป๊อปอัปจัดการบาร์โค้ดไม่พบ / สแกนผิดพลาด (`barcode-not-found-modal`)**:
  - **ตรวจจับบาร์โค้ดสั้นผิดปกติ**: หากบาร์โค้ดมีความยาว $< 8$ หลัก ระบบจะแสดงแถบเตือนสีแดงพร้อมข้อความระบุว่าอาจเกิดจากสัญญาณ Bluetooth สะดุด เพื่อให้แคชเชียร์รู้ทันที
  - **ปุ่ม `❌ สแกนผิดพลาด / ยกเลิก [N]` (หรือกด `Esc`)**: ระบบจะรับรู้ว่าเป็นการสแกนที่ผิดพลาด ล้าง Buffer ตกค้างทั้งหมด และคืนโฟกัสกลับไปที่ช่องยิงบาร์โค้ดทันทีตามกฎข้อที่ 1
  - **ปุ่ม `🔄 Re-new Barcode [R]`**: เปิดโมดัล Re-new Barcode เพื่อนำบาร์โค้ดนั้นไปจับคู่กับสินค้าเดิมในระบบ
  - **ปุ่ม `➕ สร้างสินค้า [Y]` (หรือกด `Enter`)**: เปิดฟอร์มสร้างสินค้าใหม่โดยกรอกบาร์โค้ดนั้นให้อัตโนมัติ

---

## 14. การปรับปรุงความเสถียร รองรับแท็บเล็ตและจอสัมผัส (Touch & Tablet Hardening)

จากการอัปเดตและปรับปรุงระบบตลอดช่วงที่ผ่านมา แอปพลิเคชันได้รับการยกระดับด้าน Ergonomics และความเสถียรสูงสุด:

1. **การป้องกันปัญหาการสัมผัสบน PWA (Touch Interaction Tuning)**:
   - ปิดการคลุมข้อความ (Text Selection) และ Callout เมนูของเบราว์เซอร์ทั่วทั้งแอปพลิเคชัน เพื่อให้ความรู้สึกเหมือนเนทีฟแอป
   - เปิดให้คลุมข้อความเฉพาะในช่อง Input และ Textarea เพื่อให้ยังพิมพ์และแก้ไขข้อความได้ตามปกติ
   - กำหนด `touch-action: manipulation` บนปุ่มกด เพื่อกำจัดความหน่วง 300ms ของเบราว์เซอร์บนจอสัมผัส
2. **การปรับแต่ง Typography ภาษาไทย**:
   - ปรับระยะบรรทัด `line-height: 1.38` และเพิ่ม padding ด้านล่างสำหรับการตัดคำ 2 บรรทัด (`.line-clamp-2`) ป้องกันปัญหาสระบนและวรรณยุกต์ไทยแหว่งหรือตกขอบ
3. **ระบบแป้นพิมพ์ตัวเลขเสมือนจริงบนหน้าจอ (On-Screen Pixel Numpads)**:
   - **Quick Quantity Numpad**: แตะที่ตัวเลขจำนวนสินค้าในตะกร้าหน้า POS เพื่อพิมพ์ระบุจำนวนได้ทันที พร้อมปุ่มลัดแพ็ค/โหล
   - **Cash Payment Numpad**: แป้นตัวเลขขนาดใหญ่บนหน้าชำระเงิน พร้อมปุ่มลัดธนบัตร 20, 50, 100, 500, 1,000 และปุ่มรับเงินพอดี
4. **Staging Cards View & 44px Steppers ใน AI Stock-In**:
   - ออกแบบมุมมองการ์ดสัมผัสสำหรับแท็บเล็ต พร้อมปุ่ม Stepper `[-]` และ `[+]` ขนาด 44×44px ตามมาตรฐานสุขลักษณะการสัมผัส (Touch Target Guidelines)
   - เพิ่มโมดัลยืนยันการบันทึกสต็อก (`modal-stockin-confirm`) และโมดัลแจ้งผลสำเร็จ (`modal-stockin-success`) แทนการใช้ `alert()` แบบดั้งเดิม
5. **ระบบป้องกันการเปลี่ยนหน้าขณะดาวน์โหลด (Navigation Loading Lock)**:
   - ใส่คลาส `nav-loading-locked` และตัวแปรควบคุม `window.setAppLoading()` ล็อกแถบเมนูนำทางขณะระบบกำลังโหลดแคตตาล็อกสินค้าเริ่มต้น ป้องกันข้อผิดพลาดจากการแย่งทรัพยากร DOM
6. **ระบบเสียงสังเคราะห์ Chiptune พร้อมสวิตช์เปิด/ปิดเสียง (Sound Mute Toggle)**:
   - เพิ่มปุ่มควบคุมเสียงที่แถบ Header ด้านบน (`#btn-toggle-sound`)
   - บันทึกสถานะเปิด/ปิดเสียงลงใน `localStorage` (`pixel_pos_audio_muted`) ทำให้สถานะคงอยู่แม้รีเฟรชหน้าเว็บ
7. **ระบบความปลอดภัยของฟอร์ม (Form Security & Autofill Prevention)**:
   - ปิด Browser Autocomplete และ Password Suggestions ในฟอร์มสินค้า (`autocomplete="off"`, `data-lpignore="true"`) ป้องกันไม่ให้เบราว์เซอร์เดาหรือแสดงแถบกรอกรหัสผ่านทับหน้าจอ
   - เคลียร์ช่องรหัสผ่านใน Login Modal ทันทีเมื่อปิดหน้าต่าง
   - ซ่อนข้อมูล API Key ด้วย Security Masking
8. **ความเสถียรของเซิร์ฟเวอร์ Local Dev (`server.js`)**:
   - เพิ่มระบบดักจับข้อผิดพลาด `EADDRINUSE` แจ้งเตือนผู้ใช้อย่างสุภาพกรณีรันเซิร์ฟเวอร์ซ้ำซ้อนบนพอร์ตเดียวกัน
9. **ระบบจัดการหน่วยความจำและวงจรชีวิตโมดูล (Module Lifecycle & Zero Memory Leaks)**:
   - แก้ไขปัญหา Vanilla JS SPA ที่สะสม Event Listeners เมื่อแคชเชียร์เปิดเครื่องทิ้งไว้ตลอดวันจนหน่วยความจำ (RAM) บวม
   - ย้ายแคช View จากการเก็บสตริง HTML ดิบลง `localStorage` ซึ่งเสี่ยงชนโควตา 5MB สู่ **In-Memory Cache (Map)** ใน RAM ร่วมกับ **Service Worker (sw.js)** แบบ Cache-First
   - มีฟังก์ชัน `purgeLegacyViewCache()` ล้างสตริงแคชเก่าออกจาก `localStorage` ทันทีที่บูตระบบ คืนพื้นที่ให้เบราว์เซอร์
   - กำหนดให้ทุกโมดูลหน้าจอมี `this.disposables = []`, `async init()`, และ `destroy()` โดยมี `viewLoader.js` (`setActiveModule`) สั่ง `.destroy()` โมดูลเดิมอัตโนมัติก่อนเปิดโมดูลใหม่เสมอ
   - ฟังก์ชัน `appBus.on()` คืนค่า Unsubscribe Callback เพื่อเก็บเข้า `this.disposables` ถอดถอนได้สะอาดยกชุดเมื่อสลับหน้า

---

---

## 15. ระบบเสียงพูดแจ้งเตือนและคิดเงินภาษาไทย (Thai Voice Checkout Announcement)

ระบบเสียงพูดแจ้งเตือนหน้าร้าน (Thai Voice Engine) ได้รับการออกแบบเพื่อสร้างประสบการณ์คิดเงินที่มืออาชีพและลดข้อผิดพลาดในการรับ-ทอนเงิน โดยอ่านยอดเงิน ยอดรับชำระ และเงินทอนเป็นภาษาไทยอัตโนมัติทันทีที่ปิดบิล

### จุดเด่นของระบบเสียงประกาศ
1. **คลังเสียงมาตรฐาน 25 ไฟล์ (Pristine Voice Assets):**
   - ใช้เสียง **Voice: Aoede** โทนผู้หญิงอายุ 25-30 ปี สดใส สุภาพ และน่าเชื่อถือ
   - สังเคราะห์ไฟล์เสียงทั้งหมดใน **Take เดียว (Single Batch Request)** โดยใช้แท็ก SSML ` <break time="1.5s"/> ` คั่นระหว่างคำ เพื่อรักษาความคงที่ของระดับเสียง (Pitch), ความดัง (Volume), และจังหวะพูด (Cadence) ให้เป็นเนื้อเดียวกัน 100%
   - ผ่านกระบวนการ **Pristine Tail Trimming & C2PA Stripping**: ตัดก้อนขยะ Binary Provenance Metadata ท้ายไฟล์ดิบออกสนิท พร้อมเกลี่ยสัญญาณด้วย Linear Fade-In/Out 6ms สู่แอมพลิจูด 0 ปราศจากเสียงซ่าและเสียงคลิกดิจิทัลท้ายคำทุกไฟล์ (โดยเฉพาะคำว่า "แสน" `unit_100000.wav`)
2. **การแปลงตัวเลขและไวยากรณ์ภาษาไทย (`js/utils/thaiSpeechHelper.js`):**
   - รองรับตัวเลขตั้งแต่หลักหน่วยจนถึงหลักแสน (>= 100,000) ต่อท้ายด้วย `unit_100000.wav` และกระจายเศษส่งต่อหลักหมื่นและพันอย่างถูกต้อง
   - กฎการอ่านคำว่า **"เอ็ด"** (`num_11.wav`), **"ยี่สิบ"** (`num_20.wav`), และ **"สิบ"** (`num_10.wav`) ตามหลักภาษาไทยที่ถูกต้อง
   - รองรับการอ่านเศษสตางค์ (`unit_satang.wav`) เมื่อยอดเงินมีเศษทศนิยม
3. **Web Audio Gapless Engine:**
   - Preload และแคชไฟล์เสียงทั้ง 25 ไฟล์ลงใน `Map<string, AudioBuffer>` พร้อม Versioning Query `?v=${APP_VERSION}` ใน `sw.js` ป้องกันปัญหาแคชค้างและตัดปัญหาความหน่วงของเครือข่าย
   - ทำการร้อยเรียงเสียงด้วย Web Audio API แบบ Micro-crossfade ต่อเนื่องไร้รอยต่อ
   - หยุดคิวเสียงทันทีเมื่อมีการเปิดบิลใหม่ (`stopVoice()`) และเคารพการตั้งค่าปิดเสียง (Sound Mute / Voice Toggle)
4. **เครื่องมือทดสอบและเปรียบเทียบเสียง (`tests/voice_comparator.html`):**
   - หน้าทดสอบเฉพาะทางสำหรับกดฟังเสียงทีละคำทั้ง 25 คำ
   - ทดสอบจำลองการคิดเงินจริง (Exact Cash, Cash with Change, Split QR + Cash, เศษสตางค์)

## 16. กฎและข้อบังคับสำคัญของระบบ (Critical System Rules Summary)

> ⚠️ **Single Source of Truth สำหรับ AI Agents และผู้พัฒนา:** รายละเอียด พารามิเตอร์ สคริปต์ และโค้ดตัวอย่างของกฎบังคับทั้ง 19 ข้อ ถูกรวบรวมไว้ที่ไฟล์ [AGENTS.md](file:///e:/xampp/htdocs/myproject/about_pos/AGENTS.md) เพื่อป้องกันความขัดแย้งของข้อมูล โดยมีสาระสำคัญสรุปย่อดังนี้:

| กฎข้อที่ | ชื่อกฎ (Rule Title) | สาระสำคัญและข้อกำหนดบังคับ (Key Directive) |
|:---:|---|---|
| **1** | **Barcode Input Focus Rule** | คืนโฟกัสกลับช่องบาร์โค้ดเสมอ (`#pos-barcode-input` / `#prod-search-input`) หลังจบ Modal/Action ทุกชนิด |
| **2** | **Resilient Database Rule** | ห้ามเขียน Supabase ตรงๆ ต้องผ่าน `dbHelper.js` (`resilientUpdate`, `atomicAdjustStock`, ฯลฯ) พร้อม Fallback 5 ชั้น |
| **3** | **Parameter & ID Safety Rule** | ส่ง `id` ก่อน `payload` ใน `resilientUpdate` เสมอ และเปรียบเทียบ ID ด้วย `safeIdCompare(a, b)` |
| **4** | **Hardware Barcode Engine & Thai Auto-Correction** | จับ Physical Key (`e.code`), แก้ไขแป้นพิมพ์ไทยปัตตโชติ/เกษมณี, และใช้ Dual Cadence (200ms / 120ms) |
| **5** | **Non-Blocking UI Before Alert Rule** | ต้องปลดสถานะปุ่มกด (`disabled = false`) คืนข้อความปกติก่อนเรียก `alert()` หรือ `confirm()` เสมอ |
| **6** | **Stock Movements Schema Rule** | ตาราง `stock_movements` มีเฉพาะคอลัมน์มาตรฐาน ห้ามส่ง `ref_invoice_no` หรือฟิลด์นอกสกีมาเด็ดขาด |
| **7** | **Flexible POS & Payment Layout Mandate** | ปรับ Layout หน้า POS/Payment ได้ยืดหยุ่นเพื่อ UX/UI รองรับ 2 Profile อุปกรณ์ ห้าม `overflow-hidden` ในหน้าอื่น |
| **8** | **Unified On-Screen Numpad & Zero-Replace** | รองรับการสลับเป้าหมาย cash/discount, ป้อนเลข 1-9 แทนที่ 0 ทันที, และใส่ `inputmode="none"` บล็อก Virtual Keyboard |
| **9** | **Centralized Cache Invalidation & Lifecycle** | ห้ามแก้เลขเวอร์ชันด้วยมือ รัน `npm run bump` ทุกครั้งที่มีการแก้โค้ด และจัดการ `destroy()` ป้องกัน Memory Leak |
| **10** | **Strict No-Secret Exposing Rule** | ห้ามใส่ API Key, Service Role Key หรือ Token ในโค้ดหรือ Markdown เก็บใน `app_settings` เท่านั้น |
| **11** | **Method Overwrite Prevention & Event Guard** | ห้ามประกาศเมธอดชื่อซ้ำในคลาสเดียวกัน และแยก Static DOM Events ออกจาก Lifecycle Events |
| **12** | **Thai Voice Announcement & Audio Chaining** | อ่านยอดเงิน/เงินทอนภาษาไทยอัตโนมัติแบบ Gapless Web Audio, เรียงไวยากรณ์ถูกต้อง, ไม่ชนกับเสียง 8-bit SFX |
| **13** | **Thai Voice-to-Cart Engine & PWA Permission** | รับคำสั่งเสียงภาษาไทยเพิ่มสินค้าลงตะกร้าพร้อม Visualizer สองระดับ, จับคู่ 3-Tier Priority, แสดง variations สูงสุด 10 การ์ด |
| **14** | **Inactive Inventory Auto-Zero & Filter Rules** | เมื่อปิดการขายสินค้าให้รีเซ็ตสต็อกเป็น 0 อัตโนมัติ และหน้าแคตตาล็อกเริ่มต้นที่ตัวกรอง "เปิดขาย" เสมอ |
| **15** | **Unified Icon Standard & Unused Files** | ใช้ Font Awesome 6 Free (Offline 100%) ร่วมกับ Custom Pixel Art และย้ายไฟล์เก่าเข้า `unused_files/` |
| **16** | **Stock-In Staging & Payment Ergonomics** | หมุนภาพสลิปบน Canvas พิกเซลจริงก่อนส่ง AI, แสดง Accordion และ Zebra Striping, พร้อมกรอบสีเน้นโหมดชำระเงิน |
| **17** | **Intelligent Bilingual Product Matcher** | ค้นหาแบบ Space-Agnostic, ให้คะแนนแบรนด์ตรงกัน, หักแต้มสีขัดแย้ง -50 คะแนน, และตัดคำขยะในบิล |
| **18** | **Store Barcode Absolute Priority Mandate** | บาร์โค้ดเดิมในร้านคือ Priority สูงสุด ห้ามไม่ให้ OCR/AI หรือ Article No. ห้าง เขียนทับบาร์โค้ดเดิมเด็ดขาด |
| **19** | **Dual-Device Ergonomics & Kiosk Keyboard Lock** | รองรับ Tablet 1024x640 และ Mobile 1260x2800, ใช้ปุ่ม Toggle เดี่ยว 🔫 / ⌨️ ล็อกและปลด Virtual Keyboard |

> 📖 อ่านรายละเอียดฉบับเต็มของกฎทั้งหมดได้ที่เอกสาร: **[AGENTS.md (Section 2) — Critical Rules & Directives](file:///e:/xampp/htdocs/myproject/about_pos/AGENTS.md#2-กฎและคำสั่งบังคับระดับวิกฤต-critical-rules--directives)**

---

## 17. คู่มือการติดตั้งและการรันระบบ (Setup & Deployment Guide)

### ความต้องการของระบบ (Prerequisites)
- **Node.js**: เวอร์ชัน 18.0.0 หรือสูงกว่า
- **เว็บเบราว์เซอร์**: Google Chrome, Microsoft Edge, Safari หรือ Firefox (รองรับ IndexedDB, Web Audio API, Camera API)

### 1. การรันบนเครื่อง Local Development
```bash
# 1. ติดตั้ง Dependencies (Tailwind CSS, Autoprefixer, PostCSS)
npm install

# 2. เริ่มต้นเซิร์ฟเวอร์ Local Dev Server (รันบนพอร์ต 3000)
npm run dev

# 3. เปิดเว็บเบราว์เซอร์ที่
http://localhost:3000
```

### 2. การคอมไพล์ Tailwind CSS (หากมีการปรับแต่งสไตล์)
```bash
npm run build:css
```

### 3. การเชื่อมต่อกับฐานข้อมูล Supabase
1. เข้าสู่แดชบอร์ดของ [Supabase](https://supabase.com)
2. ไปที่เมนู **SQL Editor** แล้วนำคำสั่ง SQL จาก [หัวข้อที่ 5](#5-โครงสร้างฐานข้อมูลและสคริปต์-sql-database-schemas--sql) ไปรันเพื่อสร้างตารางทั้งหมด
3. เปิดไฟล์ `js/config.js` และระบุ URL กับ Anon Key ของโปรเจกต์คุณ:
   ```javascript
   export const SUPABASE_CONFIG = {
     URL: 'https://YOUR_PROJECT_ID.supabase.co',
     ANON_KEY: 'YOUR_SUPABASE_ANON_KEY'
   };
   ```

### 4. การติดตั้งและเปิดใช้งาน PWA (Progressive Web App)
- เปิดเว็บเบราว์เซอร์บนมือถือหรือคอมพิวเตอร์ไปยัง URL ของระบบ
- กดปุ่ม **"ติดตั้งแอป (Install App)"** ที่แถบ Header หรือผ่านแท็บ *"ติดตั้งแอป PWA"* ในหน้า Settings
- แอปจะถูกติดตั้งเป็น Standalone Application ใช้งานได้เสมือนโปรแกรม Desktop หรือ Mobile App เต็มรูปแบบ
- **ฟีเจอร์ระดับ Native PWA บน Android Chrome:**
  - **Screen Wake Lock:** ป้องกันหน้าจอดับสนิทขณะประจำการหน้าขาย POS อัตโนมัติ (ปรับแต่ง On/Off ได้ในแท็บ *"หน้าขาย POS & ธีมสี"*) ป้องกันหัวอ่านบลูทูธตัดการเชื่อมต่อ
  - **Persistent Storage:** ป้องกันระบบปฏิบัติการแอบล้างข้อมูลแคช IndexedDB (Dexie) ทิ้งเมื่อความจุเครื่องเหลือน้อย
  - **Native Hardware BarcodeDetector:** ถอดรหัสบาร์โค้ดผ่านกล้องด้วยชิปเร่งความเร็วฮาร์ดแวร์ใน Chrome Android โดยตรง ประหยัดแบตเตอรี่และกิน CPU ลดลงกว่า 60%
  - **Background Sync:** ผลักบิลที่ค้างในคิวออฟไลน์ขึ้น Supabase เบื้องหลังอัตโนมัติทันทีที่สัญญาณเน็ตฟื้นตัว
  - **Orientation Support:** กำหนด `"orientation": "any"` ใน `manifest.json` รองรับทั้งแท็บเล็ตแคชเชียร์แนวนอน (1024×640 px) และสมาร์ตโฟนแนวตั้ง (1260×2800 px) ตามกฎข้อที่ 19

### 5. การปรับเวอร์ชันและทำลายแคชอัตโนมัติ (Version Bump & Cache Busting)
เมื่อมีการแก้ไขไฟล์โค้ดของระบบ ต้องรันคำสั่ง bump เวอร์ชันเพื่อให้อัปเดตทั้ง `APP_VERSION` ใน `js/config.js`, `CACHE_NAME` ใน `sw.js`, module query `?v=` ใน `index.html`, และ Badge เวอร์ชันใน Footer ของ `views/settings.html` พร้อมกันแบบ Atomic:
```bash
# อัปเดตเลขเวอร์ชันตามเวลาปัจจุบันอัตโนมัติ (เช่น v1.2.100110)
npm run bump

# หรือระบุเลขเวอร์ชันที่ต้องการเจาะจง:
npm run bump -- 1.2.100110
```

### 6. การสร้างและตรวจสอบแผนภาพสถาปัตยกรรมระบบ (Interactive Architecture Diagram)
ระบบรองรับการแสดงแผนภาพสถาปัตยกรรมแบบ Visual Interactive HTML 4 ลำดับชั้น (Tier 1–4) ผ่านเครื่องมือ Archify:
- **ไฟล์แผนภาพผลลัพธ์:** [pixel-pos-system.html](file:///e:/xampp/htdocs/myproject/about_pos/.archify/pixel-pos-system.html)
- **เรียกใช้งานผ่าน AI Slash Command:** พิมพ์คำสั่ง `/archify` ในหน้าแชทเพื่อเรียกใช้งานสกิล
- **คำสั่งจัดการผ่าน Terminal:**
  ```bash
  # ตรวจสอบความสมบูรณ์ของแผนภาพสถาปัตยกรรม
  npm run archify -- check .archify/pixel-pos-system.html

  # เรนเดอร์แผนภาพสถาปัตยกรรมใหม่จากไฟล์สเปก JSON
  npm run archify -- render architecture .archify/pixel-pos.architecture.json .archify/pixel-pos-system.html --quality showcase
  ```

---

**Pixel POS & AI Inventory** — *Crafted with retro arcade passion & modern cloud architecture.* 🕹️📦✨
