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
15. [กฎและข้อบังคับสำคัญสำหรับผู้พัฒนาและ AI (Critical Rules & Directives)](#15-กฎและข้อบังคับสำคัญสำหรับผู้พัฒนาและ-ai-critical-rules--directives)
16. [คู่มือการติดตั้งและการรันระบบ (Setup & Deployment Guide)](#16-คู่มือการติดตั้งและการรันระบบ-setup--deployment-guide)

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

---

## 3. สถาปัตยกรรมระบบและการไหลของข้อมูล (System Architecture)

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
│   │   └── views/modals.html        (14 Universal Modals Registry)                                 │
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
│   ├── icons/                  # ไอคอน PWA (192x192, 512x512, Maskable, Apple Touch Icon)
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
│   │   ├── products.js         # โมดูลคลังสินค้า (Lifecycle: init / destroy, แคตตาล็อก, เพิ่ม/แก้ไขสินค้า, CSV)
│   │   ├── reports.js          # โมดูลรายงานและสถิติ (Lifecycle: init / destroy, KPIs ยอดขาย, กำไร, Chart.js 4 รูปแบบ)
│   │   ├── settings.js         # โมดูลตั้งค่าร้านค้า (Lifecycle: init / destroy, ภาษี VAT, เครื่องพิมพ์, Gemini Key, GAS URL)
│   │   └── stock-in.js         # โมดูลรับเข้าสต็อก AI (Lifecycle: init / destroy, Gemini Vision, Staging Cards/Table, Stepper)
│   └── utils/
│       ├── audio.js            # ระบบสังเคราะห์เสียง Chiptune 8-bit (Web Audio API) พร้อมระบบเปิด/ปิดเสียง Mute และ Volume Control
│       ├── barcodeHelper.js    # แก้ไขปัญหาแป้นพิมพ์ภาษาไทย (Thai-English Layout Auto-Correction: เกษมณี & ปัตตโชติ)
│       ├── clipboard.js        # ฟังก์ชันคัดลอกข้อความลง Clipboard พร้อม Toast แจ้งเตือน
│       ├── dbHelper.js         # Resilient DB Layer (สลับ Client, แก้ Schema Error ด้วย while loop) และ Event Bus (appBus)
│       ├── gdrive.js           # ตัวแปลงลิงก์ Google Drive ID ไปเป็น Direct Google UserContent CDN URL
│       ├── geminiApi.js        # เชื่อมต่อ Google Gemini Flash API พร้อม Failover Pool และ Timeout Protection
│       ├── imageUploader.js    # บีบอัดรูปภาพหน้าจอ/ไฟล์, แปลงเป็น Base64 แล้วส่งไปยัง Google Apps Script
│       ├── promptpay.js        # สร้าง Payload ข้อความ PromptPay EMVCo QR พร้อมคำนวณ CRC16-CCITT Checksum
│       ├── themeHelper.js      # จัดการสลับธีมคู่ขนาน ('warm-arcade' และ 'cyber-emerald') บันทึก LocalStorage และยิง Event
│       ├── thermal-printer.js  # จัดรูปแบบข้อความใบเสร็จรับเงิน ESC/POS และส่งคำสั่งพิมพ์ลงกระดาษความร้อน
│       └── uiHelper.js         # Micro-Loader Overlay แบบ Retro Arcade พร้อมฟังก์ชัน showLoader / hideLoader / withLoader
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
└── tailwind.config.js          # การตั้งค่าคอนฟิกของ Tailwind CSS (Theme, Colors, Fonts, Content paths)
```

---

## 5. โครงสร้างฐานข้อมูลและสคริปต์ SQL (Database Schemas & SQL)

ระบบเชื่อมต่อกับฐานข้อมูล **PostgreSQL 15+** บน **Supabase** ประกอบด้วย 6 ตารางหลัก โดยสามารถนำสคริปต์ SQL ด้านล่างนี้ไปรันในช่อง **SQL Editor** ของ Supabase เพื่อสร้างตารางทั้งหมดพร้อม Index, Foreign Keys, Default Records และ Row Level Security (RLS) Policies:

```sql
-- ==============================================================================
-- 1. ตารางหมวดหมู่สินค้า (categories)
-- ==============================================================================
CREATE TABLE IF NOT EXISTS public.categories (
    id BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    name TEXT NOT NULL,
    sort_order INT DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

CREATE INDEX IF NOT EXISTS idx_categories_sort_order ON public.categories (sort_order ASC);

-- ==============================================================================
-- 2. ตารางแคตตาล็อกสินค้าและสต็อก (products)
-- ==============================================================================
CREATE TABLE IF NOT EXISTS public.products (
    id BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    name TEXT NOT NULL,
    barcode TEXT UNIQUE,
    cost NUMERIC(12, 3) DEFAULT 0.000 NOT NULL,
    price NUMERIC(12, 2) DEFAULT 0.00 NOT NULL,
    stock NUMERIC(12, 2) DEFAULT 0 NOT NULL,
    unit TEXT DEFAULT 'ชิ้น' NOT NULL,
    category_id BIGINT REFERENCES public.categories(id) ON DELETE SET NULL,
    image_url TEXT,
    is_quick_item BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    track_stock BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMPTZ DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
    updated_at TIMESTAMPTZ DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

CREATE INDEX IF NOT EXISTS idx_products_barcode ON public.products (barcode);
CREATE INDEX IF NOT EXISTS idx_products_category ON public.products (category_id);
CREATE INDEX IF NOT EXISTS idx_products_active ON public.products (is_active);

-- ==============================================================================
-- 3. ตารางบันทึกคำสั่งซื้อ / ใบเสร็จรับเงิน (orders)
-- ==============================================================================
CREATE TABLE IF NOT EXISTS public.orders (
    id BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    order_no TEXT NOT NULL UNIQUE,
    status TEXT DEFAULT 'COMPLETED' NOT NULL, -- 'COMPLETED', 'VOIDED'
    payment_type TEXT DEFAULT 'CASH' NOT NULL, -- 'CASH', 'PROMPTPAY', 'CUSTOM_QR', 'TRANSFER'
    total_amount NUMERIC(12, 2) DEFAULT 0.00 NOT NULL,
    discount NUMERIC(12, 2) DEFAULT 0.00 NOT NULL,
    net_amount NUMERIC(12, 2) DEFAULT 0.00 NOT NULL,
    total_cost NUMERIC(12, 3) DEFAULT 0.000 NOT NULL,
    profit NUMERIC(12, 2) DEFAULT 0.00 NOT NULL,
    cash_received NUMERIC(12, 2) DEFAULT 0.00 NOT NULL,
    change_amount NUMERIC(12, 2) DEFAULT 0.00 NOT NULL,
    void_reason TEXT,
    created_at TIMESTAMPTZ DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

CREATE INDEX IF NOT EXISTS idx_orders_created_at ON public.orders (created_at DESC);
CREATE INDEX IF NOT EXISTS idx_orders_order_no ON public.orders (order_no);

-- ==============================================================================
-- 4. ตารางรายการสินค้าในแต่ละบิล (sale_items)
-- ==============================================================================
CREATE TABLE IF NOT EXISTS public.sale_items (
    id BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    order_id BIGINT REFERENCES public.orders(id) ON DELETE CASCADE NOT NULL,
    product_id BIGINT REFERENCES public.products(id) ON DELETE SET NULL,
    product_name TEXT NOT NULL,
    quantity NUMERIC(12, 2) DEFAULT 1 NOT NULL,
    unit_price NUMERIC(12, 2) NOT NULL,
    unit_cost NUMERIC(12, 3) DEFAULT 0.000 NOT NULL,
    subtotal NUMERIC(12, 2) NOT NULL,
    created_at TIMESTAMPTZ DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

CREATE INDEX IF NOT EXISTS idx_sale_items_order_id ON public.sale_items (order_id);
CREATE INDEX IF NOT EXISTS idx_sale_items_product_id ON public.sale_items (product_id);

-- ==============================================================================
-- 5. ตารางประวัติความเคลื่อนไหวสต็อก (stock_movements)
-- ==============================================================================
CREATE TABLE IF NOT EXISTS public.stock_movements (
    id BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    product_id BIGINT REFERENCES public.products(id) ON DELETE CASCADE NOT NULL,
    type TEXT NOT NULL, -- 'STOCK_IN', 'SALE', 'ADJUST', 'RETURN', 'DAMAGE', 'VOID'
    quantity NUMERIC(12, 2) NOT NULL,
    unit_cost NUMERIC(12, 3) DEFAULT 0.000,
    note TEXT,
    created_at TIMESTAMPTZ DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

CREATE INDEX IF NOT EXISTS idx_stock_movements_product_id ON public.stock_movements (product_id);
CREATE INDEX IF NOT EXISTS idx_stock_movements_created_at ON public.stock_movements (created_at DESC);

-- ==============================================================================
-- 6. ตารางการตั้งค่าร้านค้าและระบบ (app_settings)
-- ==============================================================================
CREATE TABLE IF NOT EXISTS public.app_settings (
    id INT PRIMARY KEY DEFAULT 1,
    store_name TEXT DEFAULT 'ร้านค้าพิกเซล POS',
    store_address TEXT,
    store_tel TEXT,
    tax_id TEXT,
    receipt_header TEXT,
    receipt_footer TEXT,
    promptpay_number TEXT,
    vat_rate NUMERIC(5, 2) DEFAULT 7.00,
    enable_receipt_vat BOOLEAN DEFAULT FALSE,
    qr_type TEXT DEFAULT 'promptpay', -- 'promptpay' หรือ 'custom'
    custom_qr_url TEXT,
    custom_qr_note TEXT,
    enable_thermal_printer BOOLEAN DEFAULT FALSE,
    printer_paper_size TEXT DEFAULT '58mm', -- '58mm' หรือ '80mm'
    auto_print_receipt BOOLEAN DEFAULT FALSE,
    pos_grid_cols INT DEFAULT 4,
    gemini_api_key TEXT,
    google_apps_script_url TEXT,
    updated_at TIMESTAMPTZ DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

-- สร้างเรคคอร์ดเริ่มต้นสำหรับการตั้งค่าร้าน
INSERT INTO public.app_settings (id, store_name)
VALUES (1, 'ร้านค้าพิกเซล POS')
ON CONFLICT (id) DO NOTHING;

-- ==============================================================================
-- 7. กำหนดนโยบายความปลอดภัย Row Level Security (RLS)
-- ==============================================================================
ALTER TABLE public.categories ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.products ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.sale_items ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.stock_movements ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.app_settings ENABLE ROW LEVEL SECURITY;

-- อนุญาตให้ทั้ง Authenticated User และ Anon Public Key ใช้งานได้เต็มสิทธิ์สำหรับการขายหน้าร้าน
DO $$
DECLARE
  tbl TEXT;
BEGIN
  FOREACH tbl IN ARRAY ARRAY['categories', 'products', 'orders', 'sale_items', 'stock_movements', 'app_settings']
  LOOP
    EXECUTE format('DROP POLICY IF EXISTS "Public access on %s" ON public.%s;', tbl, tbl);
    EXECUTE format('CREATE POLICY "Public access on %s" ON public.%s FOR ALL USING (true) WITH CHECK (true);', tbl, tbl);
  END LOOP;
END $$;
```

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
  - **คอลัมน์ขวา (Payment Method & Interaction)**:
    - แถบเลือกวิธีชำระเงิน: **💵 เงินสด (Cash)**, **📱 พร้อมเพย์ (PromptPay)**, และ **🏦 บัญชีร้านค้า (Shop QR)**
    - **แท็บเงินสด (Cash Payment with On-Screen Numpad)**:
      - ปุ่มลัดธนบัตรสำเร็จรูป: `฿20`, `฿50`, `฿100`, `฿500`, `฿1,000`, และ `พอดี`
      - ช่องกรอกเงินสดที่รับมา (`#pos-cash-received`)
      - **แผงคีย์ตัวเลขรับเงินสด (Cash Numpad Grid)**: ปุ่มตัวเลข 0-9, `00`, จุดทศนิยม `.`, ปุ่มล้าง `C`, ปุ่มลบ `⌫`, และปุ่ม `พอดี`
      - จอ LED แสดงเงินทอนสีเขียวเรืองแสง (`#pos-change-display`) ขนาดใหญ่ ชัดเจน อ่านง่าย
    - **แท็บพร้อมเพย์ (Dynamic PromptPay)**: เจนเนอเรท QR Code พร้อมเพย์ตามยอดบิลโดยตรง ผ่านไลบรารี `promptpay.js`
    - **แท็บ QR บัญชีร้านค้า (Custom Shop QR)**: แสดงภาพ QR Code ธนาคารของร้านที่อัปโหลดไว้
    - ตัวเลือกเปิด/ปิดการพิมพ์ใบเสร็จความร้อนอัตโนมัติ
    - ปุ่ม `💾 บันทึกการขาย (Complete Sale)` และปุ่มย้อนกลับไปหน้าขาย

### 6.3 หน้าจอสินค้าและคลังสินค้า (Products & Inventory) - `views/products.html`
- แถบสถิติด้านบน (Inventory KPIs): สินค้าทั้งหมด, สินค้าพร้อมขาย, สินค้าสต็อกต่ำ/หมด, มูลค่าสต็อกรวมตามทุน
- ช่องค้นหาชื่อสินค้า/บาร์โค้ด, ตัวกรองตามหมวดหมู่, ตัวกรองสถานะสต็อก (ทั้งหมด, มีสินค้า, สินค้าหมด)
- ปุ่มฟังก์ชัน: `+ เพิ่มสินค้าใหม่` (เปิด CRUD Modal), `นำเข้า CSV (Import)`, `ส่งออก CSV (Export)`, และ `พิมพ์บาร์โค้ด`
- ตารางสินค้า: รูปภาพขนาดย่อ, ชื่อสินค้า, บาร์โค้ด, หมวดหมู่, สต็อกคงเหลือ, ราคาทุน, ราคาขาย, Margin กำไร (%), ปุ่มสลับรายการด่วน (Quick Item Toggle), ปุ่มเปิด/ปิดการขาย, ปุ่มปรับสต็อกด่วน, ปุ่มแก้ไข, และปุ่มลบสินค้า

### 6.4 หน้าจอจัดการหมวดหมู่สินค้า (Categories) - `views/categories.html`
- แสดงรายชื่อหมวดหมู่สินค้าทั้งหมด พร้อมลำดับการจัดเรียง (`sort_order`)
- ป้ายแสดงจำนวนสินค้าที่ผูกอยู่กับแต่ละหมวดหมู่อย่างถูกต้อง
- ปุ่มสลับลำดับหมวดหมู่ขึ้น (`▲`) และลง (`▼`) เพื่อจัดตำแหน่งแถบในหน้าขาย
- ปุ่ม `+ เพิ่มหมวดหมู่`, ปุ่มแก้ไขชื่อหมวดหมู่ และปุ่มลบหมวดหมู่ (พร้อมตรวจสอบป้องกันการลบหมวดหมู่ที่มีสินค้าผูกอยู่)

### 6.5 หน้าจอประวัติการขาย (Orders History) - `views/orders.html`
- ตัวกรองช่วงเวลา: วันนี้, เมื่อวาน, 7 วันย้อนหลัง, เดือนนี้, หรือระบุช่วงวันที่เอง
- ตัวกรองตามช่องทางการชำระเงิน (เงินสด, พร้อมเพย์, บัญชีร้านค้า, โอนเงิน)
- สรุปตัวเลขยอดขายรวมและกำไรสุทธิรวมของช่วงเวลาที่เลือก
- ตารางแสดงประวัติบิล: เลขที่บิล (`INV-xxxxxxxx`), วันที่และเวลา, วิธีชำระเงิน, ยอดรวม, ส่วนลด, ยอดสุทธิ, กำไรสุทธิของบิล, และสถานะ (`COMPLETED` หรือ `VOIDED`)
- การดูรายละเอียดบิล: เปิดป๊อปอัปแสดงใบเสร็จรับเงิน, ปุ่มสั่งพิมพ์ใบเสร็จความร้อนซ้ำ, และปุ่ม **ยกเลิกบิล (Void Bill)** เพื่อคืนสินค้ากลับเข้าสต็อก

### 6.6 หน้าจอรับเข้าสต็อก AI (AI Stock-In Terminal) - `views/stockin.html`
- **Input Panel**:
  - อัปโหลดรูปภาพใบเสร็จ/บิลส่งของ หรือเปิดกล้องถ่ายภาพ
  - แถบแจ้งเตือนการบีบอัดรูปภาพฝั่งไคลเอนต์ (`#stockin-compression-badge`) แสดงผลลัพธ์การย่อภาพก่อนส่ง AI
  - ช่องวางข้อความดิบหรือ JSON จากคู่ค้า
  - ปุ่ม `🧠 วิเคราะห์ด้วย AI (Gemini Vision)`
- **Staging Controls & Switcher**:
  - **ตัวสลับมุมมอง (View Switcher)**:
    - `🗂️ การ์ด (Cards View)`: เหมาะสำหรับแท็บเล็ตและหน้าจอสัมผัส แสดงข้อมูลสินค้าแบบการ์ดแยกชิ้น มี Stepper ปุ่มกดขนาดใหญ่ 44px
    - `📋 ตาราง (Table View)`: เหมาะสำหรับจอคอมพิวเตอร์แนวนอน แสดงคอลัมน์ครบถ้วน
  - ปุ่ม `➕ เพิ่มรายการ`: สำหรับพิมพ์เพิ่มสินค้าเข้าสต็อกด้วยตนเองโดยไม่ต้องผ่าน AI
  - ป้ายแสดงจำนวนรายการที่พบในบิล
- **Staging Interaction (การตรวจสอบและปรับข้อมูล)**:
  - ระบุชื่อผู้ขาย (Supplier) และเลขที่เอกสาร (Invoice No.)
  - จับคู่สินค้ากับสต็อกเดิมอัตโนมัติ (ขึ้นป้าย `🔗 สินค้าเดิม` หรือ `✨ สินค้าใหม่`)
  - ปรับจำนวนแพ็ค, จำนวนชิ้นต่อแพ็ค, ราคาทุนต่อแพ็ค, และราคาขายปลีก
  - คำนวณจำนวนชิ้นรับเข้าจริง และราคาทุนต่อชิ้นอัตโนมัติ (ปัดเศษขึ้น 3 ตำแหน่ง)
  - แจ้งเตือนเมื่อราคาทุนใหม่ต่างจากทุนเดิมในระบบ (`⚠️ ปรับราคาทุนใหม่`)
- **Action Buttons**:
  - ปุ่ม `🗑️ ล้างรายการ`
  - ปุ่ม `💾 ยืนยันบันทึกรับเข้าสต็อก (Confirm Stock-in)` เพื่อเปิดโมดัลยืนยันแบบสัมผัส

### 6.7 หน้าจอแดชบอร์ดรายงานและสถิติ (Dashboard & Reports) - `views/dashboard.html`
- แถบตัวเลือกช่วงเวลา: วันนี้, เมื่อวาน, 7 วัน, 30 วัน, เดือนนี้, หรือระบุวันที่
- การ์ดสรุปตัวชี้วัด (KPIs): ยอดขายรวม (Total Sales), ต้นทุนรวม (Total Cost), กำไรสุทธิ (Net Profit), อัตรากำไรขั้นต้น (Margin %), จำนวนคำสั่งซื้อ (Total Orders), และยอดขายเฉลี่ยต่อบิล (AOV)
- **กราฟวิเคราะห์ Chart.js 4 รูปแบบ**:
  1. กราฟเส้นแนวโน้มยอดขายและกำไร (Sales & Profit Timeline)
  2. กราฟโดนัทสัดส่วนช่องทางการชำระเงิน (Payment Breakdown)
  3. กราฟแท่งจัดอันดับ 10 สินค้าขายดีที่สุด (Top 10 Bestselling Products)
  4. กราฟโดนัทสัดส่วนยอดขายตามหมวดหมู่สินค้า (Sales by Category)
- รายการแจ้งเตือนสินค้าสต็อกใกล้หมด และรายการสินค้าสต็อกติดลบ

### 6.8 หน้าจอตั้งค่าระบบ (Settings) - `views/settings.html`
- **ข้อมูลร้านค้า**: ชื่อร้าน, ที่อยู่, เบอร์โทรศัพท์, เลขประจำตัวผู้เสียภาษี, ข้อความหัวบิล และท้ายบิล
- **ภาษีมูลค่าเพิ่ม (VAT)**: อัตราภาษี (%), สวิตช์เปิด/ปิดการคำนวณและแสดงภาษีในใบเสร็จ
- **การรับชำระเงิน**: หมายเลขพร้อมเพย์ (เบอร์มือถือ หรือ เลขผู้เสียภาษี), เลือกรูปแบบ QR Code, อัปโหลดรูปภาพ QR บัญชีร้านค้า
- **เครื่องพิมพ์ใบเสร็จความร้อน**: สวิตช์เปิดใช้งาน, ขนาดหน้ากระดาษ (58 มม. หรือ 80 มม.), สวิตช์สั่งพิมพ์อัตโนมัติหลังรับเงิน, ปุ่มทดสอบพิมพ์
- **การเชื่อมต่อ AI & Cloud**: ช่องระบุ Google Gemini API Key (มีระบบซ่อน Masking ป้องกันเบราว์เซอร์จำรหัสผ่าน), ช่องระบุ Google Apps Script Web App URL, ปุ่มทดสอบการเชื่อมต่อ Gemini API
- **เลย์เอาต์หน้าขาย**: เลือกจำนวนคอลัมน์การแสดงผลสินค้าในหน้า POS (3, 4, 5, 6 คอลัมน์)
- **บัญชีผู้ใช้งาน**: แสดงอีเมลผู้ใช้งานปัจจุบัน และปุ่มออกจากระบบ (Sign Out)

---

## 7. ศูนย์รวมหน้าต่างป๊อปอัปและโมดัล (Modals Registry)

หน้าต่างป๊อปอัปทั้งหมดของระบบถูกรวมศูนย์ไว้ใน `views/modals.html` (และโมดัลคีย์แพดใน `views/pos.html`) รวมทั้งสิ้น 20 โมดัล:

| รหัสโมดัล (Modal ID) | ชื่อโมดัล / บทบาทหน้าที่ | รายละเอียดและการทำงานสำคัญ |
| :--- | :--- | :--- |
| `pos-hold-modal` | **รายการบิลที่พักไว้ (Hold Bills)** | แสดงรายการบิลที่ถูกพักไว้ พร้อมเวลาและยอดเงิน สามารถกดเรียกคืนกลับมาขายต่อ หรือลบทิ้ง |
| `pos-success-modal` | **สำเร็จการขาย (Stage Clear)** | แสดงเมื่อชำระเงินสำเร็จ สรุปเลขที่บิล, ยอดเงิน, ช่องทางชำระ, เงินทอนขนาดใหญ่ และปุ่มลูกค้ารายต่อไป |
| `barcode-not-found-modal` | **แจ้งเตือนไม่พบบาร์โค้ด / สแกนผิดพลาด** | แจ้งเตือนเมื่อยิงบาร์โค้ดไม่พบ หรือสแกนผิดพลาด/ไม่สมบูรณ์ มี 3 ทางเลือก: สแกนผิดพลาด/ยกเลิก `[N]`, Re-new Barcode `[R]`, หรือสร้างสินค้าใหม่ `[Y]` |
| `prod-crud-modal` | **เพิ่ม/แก้ไขสินค้า (Product CRUD)** | ฟอร์มจัดการสินค้าเต็มรูปแบบ: อัปโหลดภาพ/ถ่ายรูป 900x900, สุ่มรหัสบาร์โค้ด, คำนวณ Margin %, สลับรายการด่วน, เปิด/ปิดระบบตัดสต็อก |
| `modal-barcode-match` | **จับคู่บาร์โค้ดใหม่ (Re-new Barcode)** | นำบาร์โค้ดที่เพิ่งยิง มาจับคู่ผูกเข้ากับสินค้าเดิมที่มีอยู่ในระบบได้อย่างรวดเร็ว |
| `modal-quick-quantity` | **แป้นตัวเลขปรับจำนวนสินค้าด่วน** | อยู่ใน `views/pos.html` มีจอ LED Scanline, ปุ่มลัดแพ็ค/โหล (+1, 6, 12, 24) และคีย์แพด 0-9 |
| `modal-stockin-product-picker` | **เลือกจับคู่สินค้าใน Stock-In** | ค้นหาและเลือกสินค้าเดิมในระบบเพื่อผูกกับรายการในใบเสร็จรับเงิน พร้อมตัวเลือกสแกนบาร์โค้ดผ่านกล้อง |
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
- **Lifecycle Implementation**: รองรับ `async init()` (โหลดสินค้า/หมวดหมู่, ผูกคีย์ลัด Numpad, ดักฟัง `products:changed` และ `categories:changed`) และ `destroy()` (ถอดถอนคีย์บอร์ดลิสเนอร์และ Event Bus เมื่อสลับหน้า)
- **High-Performance In-Memory Lookups ($O(1)$)**: มีดัชนีคู่ `_productById` และ `_productByBarcode` (Hash Maps) ดึงข้อมูลสินค้าทันที < 0.05ms เมื่อคลิกการ์ดสินค้า (`addToCartById`), วาดรายการตะกร้า (`updateCartUI`), หรือยิงบาร์โค้ด (`handleBarcodeScan`)
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
- ดึงรายการประวัติการขาย พร้อมตัวกรองตามวันที่และประเภทการชำระเงิน
- คำนวณยอดขายรวมและกำไรสุทธิรวมตามช่วงเวลาที่เลือก
- แสดงรายละเอียดสลิปใบเสร็จ และสั่งพิมพ์ซ้ำผ่านเครื่องพิมพ์ความร้อน
- ระบบยกเลิกบิล (`voidOrder()`):
  1. ปรับสถานะบิลใน `orders` เป็น `VOIDED`
  2. วนลูปคืนยอดจำนวนสินค้าใน `sale_items` กลับเข้าฟิลด์ `stock` ในตาราง `products`
  3. บันทึกความเคลื่อนไหวใน `stock_movements` (ประเภท `VOID`)
  4. อัปเดตแคชใน Dexie และกระจายสัญญาณผ่าน `appBus`

### 8.5 `js/modules/stock-in.js` (`StockInModule`)
- **Lifecycle & Event Guard Implementation**: รองรับ `async init()` (โหลดแคตตาล็อกสินค้าสำหรับ Auto-match, ผูก Event คอนโทรล Staging) และ `destroy()` (เคลียร์ Disposables และ Staging Elements) แยก `bindDOMEvents()` พร้อมการ์ด `_domEventsBound` ป้องกัน Event Listener สะสมซ้อนทับ และแยก `bindLifecycleEvents()` ลงใน `this.disposables` ตามกฎข้อที่ 9, 11 & 12
- **In-Memory Product Matching Map**: มี `_productMap = new Map()` ช่วยจับคู่สินค้าเดิมในตารางรับเข้าสต็อกระดับ sub-millisecond ขจัดปัญหา $O(M \times N)$ สแกนซ้ำซ้อน
- ประมวลผลภาพถ่ายใบเสร็จผ่าน `geminiApi.js` แล้วนำข้อมูลเข้าสู่ Staging
- **รองรับ 2 มุมมอง (Dual View)**: `renderCards()` สำหรับแท็บเล็ตจอสัมผัส และ `renderTable()` สำหรับจอกว้าง
- ฟังก์ชัน `addManualItem()`: เพิ่มรายการสินค้าเข้า Staging ด้วยตนเอง
- เมธอด `syncStagingFromDOM()`: ดึงค่าล่าสุดจาก Input บนหน้าจอก่อนกดบันทึก ป้องกันข้อมูลสูญหาย
- บันทึกสต็อกเข้าสู่ระบบ: อัปเดตสินค้าเดิม หรือเพิ่มสินค้าใหม่ พร้อมบันทึก `stock_movements` (ประเภท `STOCK_IN`)
- รองรับการคำนวณกรณีสินค้าเดิมมียอดสต็อกติดลบ (เช่น เดิม -2 ชิ้น รับเข้า 10 ชิ้น ยอดใหม่จะเป็น 8 ชิ้น พร้อมบันทึกหมายเหตุ)
- มีโมดัลยืนยัน (`modal-stockin-confirm`) และโมดัลแจ้งผลสำเร็จ (`modal-stockin-success`)

### 8.6 `js/modules/reports.js` (`ReportsModule`)
- **Lifecycle Implementation**: รองรับ `async init()` (คำนวณ KPIs, สร้างอินสแตนซ์ Chart.js 4 รูปแบบ) และ `destroy()` (ทำลาย Chart.js Instances เก่าทิ้ง และเคลียร์ Disposables เพื่อป้องกัน Memory Leak ใน Canvas)
- ประมวลผลสถิติและตัวชี้วัดทางการเงิน (ยอดขาย, ทุน, กำไร, Margin, AOV, จำนวนบิล)
- สร้างและอัปเดตกราฟ Chart.js: กราฟแนวโน้มยอดขาย, กราฟช่องทางชำระเงิน, กราฟ 10 สินค้าขายดี, กราฟยอดขายตามหมวดหมู่
- ดึงรายการสินค้าสต็อกต่ำและสินค้าสต็อกติดลบมาแสดงเตือน

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
| `js/utils/audio.js` | **Chiptune Audio Synthesizer**: สังเคราะห์เสียง 8-bit ด้วย Web Audio API เช่น `beep`, `hit`, `coin`, `powerup`, `laser`, `jump`, `error`, `stage_clear`, `plus`, `minus`, `select` พร้อมระบบปรับระดับเสียง (Master Volume Bus & Dynamics Compressor) และจำสถานะเปิด/ปิดเสียง Mute ใน LocalStorage (`toggleAudioMute()`, `isAudioMuted()`) |
| `js/utils/themeHelper.js` | **Dual-Theme Manager**: จัดการสลับธีมคู่ขนานระหว่าง **`warm-arcade`** (คลาสสิกเรโทร สีครีม-ทอง-ดำ) และ **`cyber-emerald`** (นีออนไซเบอร์ ดำ-เขียวมรกต คอนทราสต์สูง) จัดเก็บการตั้งค่าลง LocalStorage ซิงค์สีแถบเบราว์เซอร์ผ่าน `<meta name="theme-color">` และกระจายเหตุการณ์ `theme:changed` เข้าสู่ Event Bus |
| `js/utils/uiHelper.js` | **Retro Micro-Loader Overlay**: ยูทิลิตี้แสดงผลหน้าจอโหลดขนาดเล็กแบบ Hardware-Accelerated สไตล์เรโทร (`showLoader()`, `hideLoader()`, `withLoader()`) ป้องกันการกดซ้ำซ้อน และเคลียร์สถานะปลอดภัยใน `finally` block เสมอ |
| `js/utils/geminiApi.js` | **Gemini Vision Integration**: เชื่อมต่อ Gemini API พร้อมระบบ **Multi-Model Failover Pool** (`gemini-2.5-flash` ➔ `gemini-2.0-flash` ➔ `gemini-1.5-flash`), ควบคุม Timeout ด้วย `AbortController` (15s สำหรับทดสอบ, 45s สำหรับสแกนบิล), และถอด Markdown Code Fences อัตโนมัติ |
| `js/utils/promptpay.js` | **PromptPay EMVCo QR Generator**: สร้าง String ตามมาตรฐาน EMVCo รองรับเบอร์โทรศัพท์ 10 หลัก และเลขผู้เสียภาษี 13 หลัก พร้อมคำนวณค่า Checksum ตามอัลกอริทึม **CRC16-CCITT (0xFFFF)** อย่างถูกต้อง |
| `js/utils/thermal-printer.js` | **Thermal Print Formatter**: จัดหน้าสลิปใบเสร็จรับเงินสำหรับกระดาษความร้อน 58 มม. และ 80 มม. มีข้อมูลหัวบิล, ตารางรายการสินค้า, ส่วนลด, VAT, ยอดสุทธิ, เงินสด, เงินทอน, และบาร์โค้ดท้ายบิล |
| `js/utils/imageUploader.js` | **Camera & Drive Uploader**: บีบอัดภาพถ่ายจากกล้องให้มีขนาดเหมาะสม แปลงเป็น Base64 แล้วส่ง POST ไปยัง Google Apps Script Web App เพื่อจัดเก็บบน Google Drive |
| `js/utils/gdrive.js` | **Google Drive CDN Linker**: แปลง URL ของไฟล์ใน Google Drive ไปเป็น High-Performance Google CDN Direct Image URL (`https://lh3.googleusercontent.com/d/{FILE_ID}`) ช่วยให้เบราว์เซอร์แคชภาพและโหลดได้รวดเร็วโดยไม่ติด CORS |
| `js/utils/clipboard.js` | **Clipboard Copy Helper**: ฟังก์ชันช่วยคัดลอกข้อความลง Clipboard พร้อมแสดงกล่องข้อความ Toast แจ้งเตือน |

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

## 15. กฎและข้อบังคับสำคัญสำหรับผู้พัฒนาและ AI (Critical Rules & Directives)

> ⚠️ **คำเตือนสำหรับ AI Agents และผู้พัฒนาซอฟต์แวร์ทุกคน:** กฎทั้ง 10 ข้อด้านล่างนี้เป็นข้อบังคับเด็ดขาด (Mandatory Rules) ห้ามละเมิดเด็ดขาดไม่ว่าในกรณีใดๆ

### 📌 กฎข้อที่ 1: กฎการคืนโฟกัสช่องกรอก Barcode (Barcode Input Focus Rule)
- หน้าจอที่มีการสแกนบาร์โค้ด (`#pos-barcode-input`, `#prod-search-input`, หรือหน้า Stock-In) **จะต้องส่งโฟกัสกลับไปที่ช่องกรอกบาร์โค้ดทุกครั้ง** เมื่อสิ้นสุด flow การทำงานบน Pop-up / Modal / Button:
  - เมื่อจบ flow การ **Re-new Barcode** (จับคู่บาร์โค้ดใหม่ให้กับสินค้าเดิม)
  - เมื่อจบ flow การ **+ เพิ่มสินค้าใหม่** (Product CRUD Modal) ทั้งกรณีกดยืนยันสำเร็จ หรือกดยกเลิก/ปิด Modal
  - เมื่อปิด Modal แจ้งเตือนบาร์โค้ดไม่พบ (`barcode-not-found-modal`) หรือ Modal ยืนยันใดๆ
  - เมื่อสแกนและเพิ่มสินค้าลงตะกร้าในหน้าขาย (POS)
  - เมื่อเสร็จสิ้นการชำระเงิน หรือเมื่อกดล้างตะกร้าสินค้า
  - เมื่อกดย้อนกลับจากหน้าชำระเงิน (`views/payment.html`) กลับมายังหน้าขายหลัก (`views/pos.html`)
- **รูปแบบคำสั่ง:** ต้องหน่วงเวลาเล็กน้อยเพื่อให้ DOM render และ Animation เสร็จสมบูรณ์:
  ```javascript
  setTimeout(() => {
    const input = document.getElementById('pos-barcode-input') || document.getElementById('prod-search-input');
    if (input) input.focus();
  }, 100);
  ```

### 📌 กฎข้อที่ 2: กฎความเสถียรฐานข้อมูล Dual-Client Fallback (Resilient Database Rule)
- **ห้าม** ยิงคำสั่ง `supabase.from('...').update/insert/delete` ตรงๆ โดยไม่มีระบบ Fallback สำรองเด็ดขาด
- **ข้อบังคับ:** ทุกการเขียนฐานข้อมูลต้องเรียกผ่าน Helper กลางใน `js/utils/dbHelper.js` เสมอ:
  - `resilientUpdate(supabase, table, id, payload)`
  - `resilientInsert(supabase, table, payload)`
  - `resilientDelete(supabase, table, id)`
- ตัวช่วยเหล่านี้จะสลับไปยิงผ่าน `window.publicSupabase` (Anon Client) อัตโนมัติหากติด RLS หรือ Session หลุด และตรวจจับคอลัมน์ที่ไม่ตรงกับสกีมา (`PGRST204`) ด้วย `while` loop เพื่อตัดทิ้งแล้ว Retry ทันทีจนกว่าจะสำเร็จ (รองรับกรณีมีคอลัมน์เกินหลายตัวพร้อมกัน)

### 📌 กฎข้อที่ 3: กฎลำดับพารามิเตอร์และการเปรียบเทียบ ID (Parameter & ID Safety Rule)
1. **ลำดับพารามิเตอร์ของ `resilientUpdate`:**
   - มาตรฐานหลัก: `resilientUpdate(supabase, table, id, payload)` (ส่ง `id` ก่อน `payload` เสมอ)
2. **การเปรียบเทียบ ID ข้ามโมดูล:**
   - **ห้าม** ใช้ `a === b` ตรงๆ กับ ID ที่มาจาก Supabase และ IndexedDB เพราะชนิดข้อมูลอาจเป็น `String`, `Number`, หรือ `BigInt`
   - **ข้อบังคับ:** ต้องใช้ `safeIdCompare(a, b)` หรือแปลงด้วย `safeNumberId(id)` จาก `dbHelper.js` เสมอ

### 📌 กฎข้อที่ 4: กฎระบบดักจับบาร์โค้ดฮาร์ดแวร์และแก้ปัญหาผังแป้นพิมพ์ไทย (Hardware Barcode Engine & Thai Auto-Correction)
1. **In-Memory Capture ผ่าน Physical Keys (`e.code`):**
   - ดักจับเหตุการณ์ที่ Capture Phase (`window.addEventListener('keydown', ..., true)`)
   - สกัดตัวเลขจาก `e.code.startsWith('Digit')` และ `e.code.startsWith('Numpad')` ไม่ให้อิงกับภาษาแป้นพิมพ์ของระบบปฏิบัติการ
   - เมื่อโฟกัสอยู่ที่ `#pos-barcode-input` ให้สั่ง `e.preventDefault()` ป้องกัน DOM Repaint Lag และอักขระไทยหลุดเข้า DOM
2. **ระบบความเร็วปรับตัวได้ (Adaptive Cadence Dual-Threshold):**
   - ใช้ `CADENCE_WAKEUP = 200ms` (180ms – 220ms) สำหรับ 1-3 ตัวแรกช่วงหัวอ่าน Bluetooth ตื่นจากโหมด Standby และ `CADENCE_FAST = 120ms` (100ms – 120ms) สำหรับตัวถัดไป เพื่อรองรับโหมด Low Rate และป้องกันบัฟเฟอร์หลุดเมื่อยิงบาร์โค้ดยาว 12–14 หลัก
3. **Fallback Thai Normalization (Dual-Layout):**
   - ทุกจุดที่รับ Input บาร์โค้ดแบบ Manual หรือ Paste รวมถึงช่องค้นหาสินค้า ต้องผ่าน `normalizeThaiBarcode(rawInput)` ซึ่งรองรับทั้งผังปัตตโชติ (Alldocube OS) และผังเกษมณีเสมอ

### 📌 กฎข้อที่ 5: กฎการคืนสถานะปุ่มก่อนแสดง Dialog (Non-Blocking UI Before Alert Rule)
- คำสั่ง `alert()` หรือ `confirm()` ของเบราว์เซอร์เป็น Synchronous Blocking Call ที่จะหยุดการทำงานของ JavaScript ทันที
- **ข้อบังคับ:** ก่อนเรียก `alert(...)` หรือ `confirm(...)` แจ้งเตือนความสำเร็จหรือล้มเหลว **ต้องคืนสถานะปุ่มกด (Disabled = false และเปลี่ยนข้อความปุ่มกลับเป็นปกติ) ก่อนเสมอ**

### 📌 กฎข้อที่ 6: กฎความสอดคล้องของตาราง Stock Movements (Stock Movements Schema Rule)
- สกีมาของตาราง `stock_movements` ในฐานข้อมูล Supabase ประกอบด้วยคอลัมน์:
  `id`, `product_id`, `type`, `quantity`, `unit_cost`, `note`, `created_at`
- **ห้าม** ส่งฟิลด์ที่ไม่มีอยู่ในตาราง เช่น `ref_invoice_no` เข้าไปเป็นคอลัมน์เดี่ยวเด็ดขาด
- หากมีข้อมูลเลขอ้างอิงเอกสารหรือชื่อผู้ขาย ให้ผนวกเข้าในฟิลด์ `note` เช่น:
  ```javascript
  note: `รับเข้า ${inQty} ชิ้น จาก ${supplier} [เลขที่เอกสาร: ${invoiceNo}]`
  ```

### 📌 กฎข้อที่ 7: กฎการออกแบบและปรับแต่ง Layout หน้าขาย POS & Payment ยืดหยุ่นเพื่อ UX/UI (Flexible POS & Payment Layout Mandate)
- **การปรับเปลี่ยน Layout เพื่อ UX/UI:** โครงสร้าง Layout หน้า POS และ Payment รองรับการปรับเปลี่ยนได้อย่างยืดหยุ่นเมื่อต้องการยกระดับ UX/UI หรือรองรับฟีเจอร์ใหม่
- **Touch-Friendly & Usability:** สามารถปรับเพิ่มความสูง ขนาดปุ่มสัมผัส (เช่น แถบหมวดหมู่สินค้า `#pos-category-tabs-container` เพิ่มความสูง 80% ให้กดง่ายขึ้น) และจัดแต่งสไตล์ Scrollbar ให้กลมกลืนกับ Theme
- **Fast Checkout Focus:** คงความสะดวกรวดเร็วในการปิดบิล และการเข้าถึงปุ่มสำคัญได้อย่างแม่นยำ

### 📌 กฎข้อที่ 8: กฎระบบแป้นตัวเลขรวมศูนย์ (Unified On-Screen Numpad & Zero-Replace Protection)
- **การสลับเป้าหมาย (Active Target Routing):** แผง Numpad ในหน้าชำระเงินรองรับทั้งการกรอกเงินสด (`cash`) และส่วนลด (`discount`)
- **การแก้ปัญหาเลข 0 ค้าง (Zero-Replace Protection):** หากค่าในช่องกรอกเป็น `0` หรือเป็นค่าว่าง การกดเลข 1-9 จะต้องเข้าแทนที่เลข `0` ทันที
- **การบล็อกคีย์บอร์ดเสมือนของระบบ (Soft-Keyboard Suppression):** ช่องกรอกที่ใช้ปืนสแกนหรือ Numpad ของแอป ต้องใส่ `inputmode="none"` เสมอ เพื่อป้องกัน Virtual Keyboard ของ Android เด้งขึ้นมาบดบังหน้าจอ

### 📌 กฎข้อที่ 9: กฎการป้องกันเบราว์เซอร์แคชและวงจรชีวิตโมดูล (Centralized Cache Invalidation & Module Lifecycle Mandate)
- **ข้อบังคับการปรับเวอร์ชัน (Cache Invalidation):** ห้ามแก้ไขเลขเวอร์ชันใน `js/config.js` หรือ `sw.js` ด้วยมือโดยเด็ดขาด ทุกครั้งที่มีการแก้ไขไฟล์ HTML, CSS, หรือ JS แม้เพียงตัวอักษรเดียว **ต้องรันคำสั่ง `npm run bump` ผ่าน Terminal ทุกครั้ง** (สคริปต์จะทำการอัปเดตทั้ง `APP_VERSION` ใน `js/config.js` และ `CACHE_NAME` ใน `sw.js` ให้ตรงกันแบบ Atomic เพื่อทำลาย Cache เก่าอย่างสมบูรณ์แบบ)
- **ข้อบังคับวงจรชีวิตโมดูล (Lifecycle & Disposables):**
  - ทุกโมดูลหน้าจอต้องมี `this.disposables = []`, เมธอด `async init()`, และเมธอด `destroy()`
  - ทุกการดักฟัง `appBus.on(...)` หรือ DOM Events ต้องเก็บฟังก์ชัน Unsubscribe ลงใน `this.disposables` เสมอ และต้องสลับหน้าผ่าน `setActiveModule()` เพื่อสั่งทำลายโมดูลเดิม ป้องกันการรั่วไหลของหน่วยความจำ (Memory Leak) บนแท็บเล็ตแคชเชียร์

### 📌 กฎข้อที่ 10: กฎความปลอดภัยและการจัดการข้อมูลความลับ (Strict No-Secret Exposing Rule)
- **ห้าม** ใส่ข้อมูลความลับ เช่น API Keys (เช่น Gemini API Key), Database Password, Supabase Service Role Key, หรือ Token ลงในซอร์สโค้ดฝั่ง Client (`index.html`, `js/*`) หรือเอกสาร Markdown (`AGENTS.md`, `README.md`) เด็ดขาด
- การตั้งค่า Gemini API Key, Google Apps Script Web App URL หรือบัญชี PromptPay จะต้องถูกบันทึกลงในตาราง `app_settings` บน Supabase / IndexedDB ผ่านหน้าจอตั้งค่าระบบ (`views/settings.html`) พร้อมการทำ Security Masking เสมอ

### 📌 กฎข้อที่ 11: กฎการป้องกันการประกาศเมธอดซ้ำซ้อนและการแยกการผูก Event (Method Overwrite Prevention & Static DOM Event Guard Rule)
1. **ห้ามมีเมธอดชื่อซ้ำในคลาสเดียวกันเด็ดขาด (Strict Single Method Declaration):**
   - ก่อนบันทึกไฟล์หรือ refactor โค้ด ต้องตรวจสอบว่าไม่มีการประกาศเมธอดเดิมซ้ำสองจุด ซึ่งจะทำให้ Prototype เมธอดหลังเขียนทับเมธอดแรกเงียบๆ
2. **การแยกประเภท Event ระหว่าง Static DOM และ Dynamic Lifecycle (Dual-Tier Event Binding):**
   - **Static DOM Events:** ปุ่มกด, ฟอร์ม submit, input ที่อยู่ใน HTML ของวิว ให้แยกไว้ในเมธอด `bindDOMEvents()` และครอบด้วยตัวแปรป้องกัน `if (this._domEventsBound) return; this._domEventsBound = true;` ผูกเพียงครั้งเดียวตลอดอายุแอป
   - **Lifecycle Events:** การดักฟัง `appBus.on(...)` หรือ `window.addEventListener('keydown', ...)` ให้แยกไว้ใน `bindLifecycleEvents()` และต้องเก็บฟังก์ชัน Unsubscribe ลงใน `this.disposables` เสมอ เพื่อให้เมธอด `destroy()` ปลดออกได้อย่างสะอาดหมดจดเมื่อสลับไปหน้าอื่น

---

## 16. คู่มือการติดตั้งและการรันระบบ (Setup & Deployment Guide)

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

### 4. การติดตั้งและเปิดใช้งาน PWA
- เปิดเว็บเบราว์เซอร์บนมือถือหรือคอมพิวเตอร์ไปยัง URL ของระบบ
- กดปุ่ม **"ติดตั้งแอป (Install App)"** ที่แถบ Header หรือผ่านเมนูของเบราว์เซอร์
- แอปจะถูกติดตั้งเป็น Standalone Application ใช้งานได้เสมือนโปรแกรม Desktop หรือ Mobile App เต็มรูปแบบ

### 5. การปรับเวอร์ชันและทำลายแคชอัตโนมัติ (Version Bump & Cache Busting)
เมื่อมีการแก้ไขไฟล์โค้ดของระบบ ต้องรันคำสั่ง bump เวอร์ชันเพื่อให้อัปเดตทั้ง `APP_VERSION` ใน `js/config.js` และ `CACHE_NAME` ใน `sw.js` พร้อมกันแบบ Atomic:
```bash
# อัปเดตเลขเวอร์ชันตามเวลาปัจจุบันอัตโนมัติ (เช่น v1.2.091918)
npm run bump

# หรือระบุเลขเวอร์ชันที่ต้องการเจาะจง:
npm run bump -- 1.2.091918
```

---

**Pixel POS & AI Inventory** — *Crafted with retro arcade passion & modern cloud architecture.* 🕹️📦✨
