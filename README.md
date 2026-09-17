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
- **Single Page Application (SPA) with Dynamic View Loader**: โครงสร้างไฟล์ HTML แยกย่อยตามหน้าในโฟลเดอร์ `views/*.html` ถูกโหลดแบบอะซิงโครนัสเข้าสู่คอนเทนเนอร์หลักใน `index.html` พร้อมระบบแคช View ในตัว และมี Navigation Guard ล็อกการเปลี่ยนหน้าขณะระบบกำลังดาวน์โหลดข้อมูลสินค้าเริ่มต้น
- **Offline-First & Dual-Client Fallback**: ทำงานต่อเนื่องได้แม้สัญญาณอินเทอร์เน็ตขาดหายด้วย IndexedDB (`Dexie.js`) และ Service Worker (PWA) เมื่อส่งคำสั่งฐานข้อมูล ระบบมีตัวช่วย `dbHelper.js` ที่สลับการทำงานระหว่าง Authenticated Client และ Anon Public Client ของ Supabase โดยอัตโนมัติเมื่อเกิดปัญหา Session หลุดหรือติดสิทธิ์ RLS
- **AI-Powered Inventory Staging**: ใช้วิสัยทัศน์คอมพิวเตอร์ผ่าน Google Gemini Flash API ถอดข้อมูลจากบิล/ใบเสร็จรับเงินค้าส่ง แปลงเป็นข้อมูลที่มีโครงสร้าง (Structured JSON) เข้าสู่ Staging Table ซึ่งรองรับทั้ง **มุมมองการ์ด (Cards View)** สำหรับแท็บเล็ตจอสัมผัส และ **มุมมองตาราง (Table View)** สำหรับคอมพิวเตอร์จอกว้าง
- **Touch & Tablet Ergonomics**: ปรับขนาดปุ่มกดสำหรับนิ้วมือ (Touch Targets อย่างน้อย 44×44px), แป้นพิมพ์ตัวเลขเสมือนจริงบนหน้าจอ (On-Screen Pixel Numpad) สำหรับระบุจำนวนสินค้าและรับเงินสด, ป้องกันการคลุมข้อความโดยไม่ตั้งใจบนอุปกรณ์สัมผัส, และปรับระยะบรรทัดภาษาไทยให้สระและวรรณยุกต์ไม่แหว่ง

---

## 2. ภาษาและเทคโนโลยีหลักที่ใช้ (Tech Stack & Core Languages)

| ส่วนประกอบ (Component) | เทคโนโลยี / ภาษาที่ใช้ | คำอธิบายและบทบาทหน้าที่ |
| :--- | :--- | :--- |
| **Core Frontend Language** | **JavaScript (Native ES6+ Modules)** | ใช้ Native ES Modules (`import`/`export`) เต็มระบบ ไม่ต้องพึ่งพา Bundler (Vite/Webpack) ทำให้ดีบักง่าย โค้ดโปร่งใส และโหลดไว |
| **Structure & Templating** | **HTML5 Semantic Partial Views** | หน้าจอถูกแยกเป็นโมดูลาร์ในไดเรกทอรี `views/*.html` เพื่อให้ `viewLoader.js` โหลดเฉพาะส่วนที่ต้องการ |
| **Styling & Retro Theme** | **Tailwind CSS v3.4 + Custom Pixel CSS** | ยูทิลิตี้คลาสของ Tailwind CSS ผสานกับสไตล์ตู้เกมพิกเซลและ Scanlines ใน `css/retro-pixel.css` |
| **Local Web Server** | **Node.js (Native HTTP Server)** | เซิร์ฟเวอร์ `server.js` ขนาดกะทัดรัด ให้บริการไฟล์สถิต มีระบบกันชนกรณี Port ซ้ำ (`EADDRINUSE`) และรองรับ SPA Fallback |
| **Primary Cloud Database** | **Supabase (PostgreSQL 15+)** | เก็บข้อมูลหลัก: สินค้า, หมวดหมู่, ประวัติการขาย, รายการขาย, ความเคลื่อนไหวสต็อก และการตั้งค่าร้านค้า |
| **Offline DB & Local Cache** | **Dexie.js (IndexedDB Wrapper)** | ฐานข้อมูล `RetroArcadePosDB` แคชสินค้าและหมวดหมู่ พร้อมตารางคิวบิลออฟไลน์ `offline_orders` |
| **Vision AI Engine** | **Google Gemini Flash API** | ถอดข้อความใบเสร็จรับเงิน/ใบส่งสินค้า รองรับ Failover Pool (`gemini-2.5-flash`, `gemini-2.0-flash`, `gemini-1.5-flash`) |
| **Camera Barcode & QR Reader** | **Html5-Qrcode SDK** | สแกนบาร์โค้ด 1D (EAN-13, Code 128) และ 2D (QR Code) ผ่านกล้องแท็บเล็ต/สมาร์ทโฟน |
| **Data Analytics & Charts** | **Chart.js v4.4** | กราฟเส้นแนวโน้มยอดขายและกำไร, กราฟแท่งสินค้าขายดี 10 อันดับแรก, และโดนัทสัดส่วนการชำระเงิน |
| **Progressive Web App (PWA)** | **Service Worker (`sw.js`) + Manifest** | รองรับการติดตั้งแบบ Standalone Application บน iOS, iPadOS, Android, Windows, macOS พร้อมไอคอน Maskable |
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
│   │   ├── views/pos.html           (POS Terminal & Cart)                                          │
│   │   ├── views/payment.html       (Checkout & On-Screen Numpad)                                  │
│   │   ├── views/products.html      (Product Catalog & Stock Management)                           │
│   │   ├── views/categories.html    (Category Sorting & Counts)                                    │
│   │   ├── views/orders.html        (Sales History & Void Logic)                                   │
│   │   ├── views/stockin.html       (AI Stock-In: Staging Cards / Table)                           │
│   │   ├── views/dashboard.html     (Reports & Chart.js Analytics)                                 │
│   │   ├── views/settings.html      (Store Config, VAT, Printers, API Keys)                        │
│   │   └── views/modals.html        (14 Universal Modals Registry)                                 │
│   └── Event Bus (appBus in dbHelper.js) ──► Cross-module instant updates                          │
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
│   ├── config.js               # การตั้งค่า Supabase URL และ Public Anon Key
│   ├── db.js                   # จัดการฐานข้อมูล IndexedDB ฝั่งเบราว์เซอร์ผ่าน Dexie.js (RetroArcadePosDB)
│   ├── pwa-install.js          # ตรวจจับ event beforeinstallprompt และควบคุมปุ่มติดตั้ง PWA
│   ├── sync.js                 # ซิงค์ข้อมูลอัตโนมัติ (ตรวจจับเน็ตหลุด/ต่อเน็ต และส่งคิวบิลออฟไลน์ขึ้น Supabase)
│   ├── core/
│   │   └── viewLoader.js       # จัดการโหลดไฟล์ HTML ใน views/ แบบ Asynchronous พร้อมแคชและ Versioning
│   ├── modules/
│   │   ├── categories.js       # โมดูลจัดการหมวดหมู่สินค้า (เพิ่ม, แก้ไข, ลบ, จัดลำดับ sort_order)
│   │   ├── orders.js           # โมดูลประวัติการขาย (ค้นหาบิล, กรองวันที่, พิมพ์สลิปซ้ำ, ยกเลิกบิลและคืนสต็อก)
│   │   ├── pos.js              # โมดูลขายหน้าร้าน (จัดการตะกร้า, สแกนบาร์โค้ด, พักบิล/เรียกบิล, LED Qty Numpad)
│   │   ├── products.js         # โมดูลคลังสินค้า (แคตตาล็อก, เพิ่ม/แก้ไขสินค้า, ปรับสต็อก, นำเข้า/ส่งออก CSV)
│   │   ├── reports.js          # โมดูลรายงานและสถิติ (KPIs ยอดขาย, กำไร, กราฟ Chart.js 4 รูปแบบ)
│   │   ├── settings.js         # โมดูลตั้งค่าร้านค้า (ภาษี VAT, ข้อมูลร้าน, เครื่องพิมพ์, Gemini Key, GAS URL)
│   │   └── stock-in.js         # โมดูลรับเข้าสต็อก AI (Gemini Vision, Staging Cards/Table, Stepper, ตรวจสอบทุน)
│   └── utils/
│       ├── audio.js            # ระบบสังเคราะห์เสียง Chiptune 8-bit (Web Audio API) พร้อมระบบเปิด/ปิดเสียง Mute
│       ├── barcodeHelper.js    # แก้ไขปัญหาแป้นพิมพ์ภาษาไทย (Thai-English Layout Auto-Correction)
│       ├── clipboard.js        # ฟังก์ชันคัดลอกข้อความลง Clipboard พร้อม Toast แจ้งเตือน
│       ├── dbHelper.js         # Resilient DB Layer (สลับ Client, แก้ Schema Error) และ Event Bus (appBus)
│       ├── gdrive.js           # ตัวแปลงลิงก์ Google Drive ID ไปเป็น Direct Google UserContent CDN URL
│       ├── geminiApi.js        # เชื่อมต่อ Google Gemini Flash API พร้อม Failover Pool และ Timeout Protection
│       ├── imageUploader.js    # บีบอัดรูปภาพหน้าจอ/ไฟล์, แปลงเป็น Base64 แล้วส่งไปยัง Google Apps Script
│       ├── promptpay.js        # สร้าง Payload ข้อความ PromptPay EMVCo QR พร้อมคำนวณ CRC16-CCITT Checksum
│       └── thermal-printer.js  # จัดรูปแบบข้อความใบเสร็จรับเงิน ESC/POS และส่งคำสั่งพิมพ์ลงกระดาษความร้อน
├── views/
│   ├── categories.html         # ส่วนติดต่อผู้ใช้หน้าจัดการหมวดหมู่สินค้า
│   ├── dashboard.html          # ส่วนติดต่อผู้ใช้หน้าแดชบอร์ดรายงานและกราฟวิเคราะห์ยอดขาย
│   ├── modals.html             # ศูนย์รวมโมดัลทั้งหมด 14 โมดัล (Product CRUD, Re-new Barcode, Camera, ฯลฯ)
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
├── package.json                # ข้อมูล Dependencies และ Scripts (dev, start, build:css)
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
  - ช่องกรอกค้นหาชื่อสินค้า/ยิงบาร์โค้ดฮาร์ดแวร์ (`#barcode-input`) พร้อมปุ่มล้างคำค้นหา และปุ่มเปิดกล้องสแกนบาร์โค้ด
  - แถบเลื่อนเลือกหมวดหมู่สินค้าแนวนอน (`.pos-cat-tab`) พร้อมปุ่มตัวกรองพิเศษ `⚡ รายการด่วน (Quick Items)`
  - ตัวเลือกปรับจำนวนคอลัมน์การแสดงผลสินค้า (3, 4, 5, 6 คอลัมน์) ตามขนาดหน้าจอ
  - การ์ดสินค้าพิกเซล: แสดงรูปสินค้า (มีรูป Placeholder สำรอง), ชื่อสินค้าพร้อมตัดคำ 2 บรรทัด (ไม่ทับสระภาษาไทย), ป้ายราคาเด่นชัด, และ Badge สต็อกคงเหลือ (สีเขียว = มีสินค้า, สีแดง = สินค้าหมด/ติดลบ)
- **Cart & Order Panel (ฝั่งขวา)**:
  - แสดงรายการสินค้าในตะกร้าแบบเรียลไทม์ พร้อมเส้นขอบคมชัดสไตล์เกม
  - **แถวควบคุมจำนวนสินค้า (Redesigned Touch Controls)**:
    - ปุ่ม `[-]` ปรับลดจำนวน (กว้าง 36-40px สัมผัสง่าย)
    - **ปุ่มตัวเลขระบุจำนวน (Clickable Quantity Button)**: แตะเพื่อเปิดโมดัล On-Screen Retro LED Numpad สำหรับพิมพ์ตัวเลขจำนวนสินค้าโดยตรง
    - ปุ่ม `[+]` ปรับเพิ่มจำนวน
    - แสดงยอดรวมย่อย (Subtotal) ขนาดใหญ่ชัดเจน
    - ปุ่มลบรายการ `[✕ ลบ]` ย้ายไปอยู่ขวาสุด ป้องกันการกดพลาด
  - ส่วนคำนวณเงินท้ายบิล: รวมเงิน (Subtotal), สวิตช์ส่วนลด (เลือกระบุเป็น `฿` บาท หรือ `%` เปอร์เซ็นต์ พร้อมแสดงผลคำนวณส่วนลดย่อย), ภาษีมูลค่าเพิ่ม (VAT), ยอดสุทธิ (Net Amount), และการวิเคราะห์กำไรเบื้องต้น
  - แถบปุ่มลัด: `🗑️ ล้างตะกร้า`, `⏸️ พักบิล / 📂 เรียกบิล`, `⚡ จ่ายเงินสดพอดี`, และ `💳 ชำระเงิน (Checkout)`
- **โมดัลคีย์ตัวเลขด่วน (Quick Quantity Numpad Modal)**:
  - อยู่ภายใน `views/pos.html` (`#modal-quick-quantity`)
  - แสดงชื่อสินค้า, ราคาต่อหน่วย, และหน้าจอ LED Scanline สีเขียวแสดงจำนวนสินค้าที่พิมพ์
  - ปุ่มลัดตามแพ็ค/โหล: `+1 ชิ้น`, `6 (ครึ่งโหล)`, `12 (1 โหล)`, `24 (2 โหล)`
  - แผงแป้นตัวเลข 0-9 พร้อมปุ่ม `C (ล้าง)` และ `⌫ (Backspace)`
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

หน้าต่างป๊อปอัปทั้งหมดของระบบถูกรวมศูนย์ไว้ใน `views/modals.html` ประกอบด้วย 14 โมดัลหลัก:

| รหัสโมดัล (Modal ID) | ชื่อโมดัล / บทบาทหน้าที่ | รายละเอียดและการทำงานสำคัญ |
| :--- | :--- | :--- |
| `pos-hold-modal` | **รายการบิลที่พักไว้ (Hold Bills)** | แสดงรายการบิลที่ถูกพักไว้ พร้อมเวลาและยอดเงิน สามารถกดเรียกคืนกลับมาขายต่อ หรือลบทิ้ง |
| `pos-success-modal` | **สำเร็จการขาย (Stage Clear)** | แสดงเมื่อชำระเงินสำเร็จ สรุปเลขที่บิล, ยอดเงิน, ช่องทางชำระ, เงินทอนขนาดใหญ่ และปุ่มลูกค้ารายต่อไป |
| `barcode-not-found-modal` | **แจ้งเตือนไม่พบบาร์โค้ด** | แจ้งเตือนเมื่อยิงบาร์โค้ดไม่พบ มี 3 ทางเลือก: ยกเลิก `[N]`, Re-new Barcode `[R]`, หรือสร้างสินค้าใหม่ `[Y]` |
| `prod-crud-modal` | **เพิ่ม/แก้ไขสินค้า (Product CRUD)** | ฟอร์มจัดการสินค้าเต็มรูปแบบ: อัปโหลดภาพ/ถ่ายรูป 900x900, สุ่มรหัสบาร์โค้ด, คำนวณ Margin %, สลับรายการด่วน |
| `modal-barcode-match` | **จับคู่บาร์โค้ดใหม่ (Re-new Barcode)** | นำบาร์โค้ดที่เพิ่งยิง มาจับคู่ผูกเข้ากับสินค้าเดิมที่มีอยู่ในระบบได้อย่างรวดเร็ว |
| `stock-adjust-modal` | **ปรับปรุงจำนวนสต็อกด่วน** | ปรับปรุงยอดสต็อกสินค้าทันที (รับเข้า, ของเสียหาย, นับสต็อก) พร้อมบันทึกลง `stock_movements` |
| `camera-scanner-modal` | **กล้องสแกนบาร์โค้ด** | เปิดกล้องหลังเพื่อสแกนบาร์โค้ด 1D/2D ด้วยไลบรารี Html5-Qrcode |
| `camera-photo-modal` | **กล้องถ่ายรูปสินค้า** | สตรีมวิดีโอจากกล้องเพื่อถ่ายภาพสินค้า จัดเฟรมสี่เหลี่ยมจัตุรัส แล้วส่งเข้า Image Pipeline |
| `modal-quick-quantity` | **แป้นตัวเลขปรับจำนวนสินค้าด่วน** | อยู่ใน `views/pos.html` มีจอ LED Scanline, ปุ่มลัดแพ็ค/โหล (+1, 6, 12, 24) และคีย์แพด 0-9 |
| `modal-stockin-confirm` | **ยืนยันการบันทึกสต็อก AI** | สรุปจำนวนรายการ, จำนวนชิ้นรวม, มูลค่าทุนรวม และปุ่มกดยืนยันขนาดใหญ่สำหรับจอสัมผัส |
| `modal-stockin-success` | **แจ้งผลบันทึกสต็อกสำเร็จ** | แสดงจำนวนรายการที่สำเร็จ และแสดงรายการที่ผิดพลาด (ถ้ามี) ก่อนปิดและส่งโฟกัสกลับช่องบาร์โค้ด |
| `match-picker-modal` | **เลือกจับคู่สินค้าใน Stock-In** | ค้นหาและเลือกสินค้าเดิมในระบบเพื่อผูกกับรายการในใบเสร็จรับเงิน |
| `cat-unit-modal` | **กำหนดหมวดหมู่และหน่วยนับ** | ป๊อปอัปเลือกหมวดหมู่และหน่วยนับสำหรับรายการสินค้าใหม่ใน Staging Table |
| `cost-diff-modal` | **แจ้งเตือนผลต่างราคาทุน** | แสดงการเปรียบเทียบระหว่างทุนเดิมในระบบ กับทุนใหม่จากใบเสร็จ เพื่อพิจารณาปรับราคาขายปลีก |
| `gdrive-guide-modal` | **คู่มือการติดตั้ง Google Apps Script** | ป๊อปอัปแสดงขั้นตอนการสร้าง Web App บน script.google.com สำหรับเก็บรูปภาพลง Google Drive |

---

## 8. เจาะลึกการทำงานของทุกโมดูล (Core Application Modules)

### 8.1 `js/modules/pos.js` (`PosModule`)
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
- โหลดแคตตาล็อกสินค้าจาก Supabase และซิงค์ลง Dexie IndexedDB
- เรนเดอร์ตารางสินค้า รองรับการค้นหา Full-text (ชื่อ, บาร์โค้ด) และการกรองตามหมวดหมู่/สถานะ
- จัดการฟอร์มบันทึกสินค้า (รองรับทั้งการสร้างใหม่และการแก้ไข) โดยมีระบบปิด Browser Autocomplete
- ฟังก์ชันส่งออกข้อมูลสินค้าเป็นไฟล์ CSV (`exportToCSV()`) และนำเข้าสินค้าจากไฟล์ CSV (`importFromCSV()`)
- ระบบสร้างป้ายบาร์โค้ดสำหรับนำไปสั่งพิมพ์สติกเกอร์บาร์โค้ดติดสินค้า
- ฟังก์ชันปรับสต็อกสินค้าด่วน (`adjustStock()`) พร้อมบันทึกประวัติความเคลื่อนไหว

### 8.3 `js/modules/categories.js` (`CategoriesModule`)
- จัดการหมวดหมู่สินค้า (CRUD)
- คำนวณจำนวนสินค้าที่สังกัดอยู่ในแต่ละหมวดหมู่แบบไดนามิก
- เลื่อนลำดับการแสดงผลขึ้น/ลง (`moveOrder()`) และบันทึกฟิลด์ `sort_order` ใหม่ลงฐานข้อมูล

### 8.4 `js/modules/orders.js` (`OrdersModule`)
- ดึงรายการประวัติการขาย พร้อมตัวกรองตามวันที่และประเภทการชำระเงิน
- คำนวณยอดขายรวมและกำไรสุทธิรวมตามช่วงเวลาที่เลือก
- แสดงรายละเอียดสลิปใบเสร็จ และสั่งพิมพ์ซ้ำผ่านเครื่องพิมพ์ความร้อน
- ระบบยกเลิกบิล (`voidOrder()`):
  1. ปรับสถานะบิลใน `orders` เป็น `VOIDED`
  2. วนลูปคืนยอดจำนวนสินค้าใน `sale_items` กลับเข้าฟิลด์ `stock` ในตาราง `products`
  3. บันทึกความเคลื่อนไหวใน `stock_movements` (ประเภท `VOID`)
  4. อัปเดตแคชใน Dexie และกระจายสัญญาณผ่าน `appBus`

### 8.5 `js/modules/stock-in.js` (`StockInModule`)
- ประมวลผลภาพถ่ายใบเสร็จผ่าน `geminiApi.js` แล้วนำข้อมูลเข้าสู่ Staging
- **รองรับ 2 มุมมอง (Dual View)**: `renderCards()` สำหรับแท็บเล็ตจอสัมผัส และ `renderTable()` สำหรับจอกว้าง
- ฟังก์ชัน `addManualItem()`: เพิ่มรายการสินค้าเข้า Staging ด้วยตนเอง
- เมธอด `syncStagingFromDOM()`: ดึงค่าล่าสุดจาก Input บนหน้าจอก่อนกดบันทึก ป้องกันข้อมูลสูญหาย
- บันทึกสต็อกเข้าสู่ระบบ: อัปเดตสินค้าเดิม หรือเพิ่มสินค้าใหม่ พร้อมบันทึก `stock_movements` (ประเภท `STOCK_IN`)
- รองรับการคำนวณกรณีสินค้าเดิมมียอดสต็อกติดลบ (เช่น เดิม -2 ชิ้น รับเข้า 10 ชิ้น ยอดใหม่จะเป็น 8 ชิ้น พร้อมบันทึกหมายเหตุ)
- มีโมดัลยืนยัน (`modal-stockin-confirm`) และโมดัลแจ้งผลสำเร็จ (`modal-stockin-success`)

### 8.6 `js/modules/reports.js` (`ReportsModule`)
- ประมวลผลสถิติและตัวชี้วัดทางการเงิน (ยอดขาย, ทุน, กำไร, Margin, AOV, จำนวนบิล)
- สร้างและอัปเดตกราฟ Chart.js: กราฟแนวโน้มยอดขาย, กราฟช่องทางชำระเงิน, กราฟ 10 สินค้าขายดี, กราฟยอดขายตามหมวดหมู่
- ดึงรายการสินค้าสต็อกต่ำและสินค้าสต็อกติดลบมาแสดงเตือน

### 8.7 `js/modules/settings.js` (`SettingsModule`)
- บันทึกการตั้งค่าร้านค้าลง Supabase ตาราง `app_settings` และแคชใน Dexie
- จัดการข้อมูลพร้อมเพย์ และระบบ QR Code ชำระเงิน
- จัดการเครื่องพิมพ์ความร้อน (58mm/80mm) และปุ่มทดสอบพิมพ์
- ทดสอบการเชื่อมต่อ Google Gemini API และ Google Apps Script Web App

---

## 9. ชุดเครื่องมือและฟังก์ชันช่วยเหลือ (Utilities & Helper Libraries)

| ไฟล์ (File Path) | บทบาทและฟังก์ชันการทำงานหลัก |
| :--- | :--- |
| `js/utils/dbHelper.js` | **Resilient Database Layer & Event Bus**: ฟังก์ชัน `resilientUpdate()`, `resilientInsert()`, `resilientDelete()` รองรับการสลับยิงผ่าน Public Anon Client อัตโนมัติเมื่อ Session หลุดหรือติด RLS, ตรวจจับและตัดฟิลด์ที่ไม่มีในสกีมาทิ้งแล้ว Retry ทันที, รองรับลำดับอาร์กิวเมนต์แบบยืดหยุ่น และมี `appBus` สำหรับส่ง Event ข้ามโมดูล |
| `js/utils/barcodeHelper.js` | **Thai Barcode Layout Fixer**: ฟังก์ชัน `normalizeThaiBarcode()` แปลงอักขระภาษาไทย (เช่น `ขชๆพะ` หรือตัวเลขไทย) กลับเป็นตัวเลขอารบิก `0-9` และอักษรภาษาอังกฤษ `a-z` ทันทีเมื่อผู้ใช้ลืมสลับภาษาของแป้นพิมพ์ขณะยิงบาร์โค้ด |
| `js/utils/audio.js` | **Chiptune Audio Synthesizer**: สังเคราะห์เสียง 8-bit ด้วย Web Audio API เช่น `beep`, `hit`, `coin`, `powerup`, `laser`, `jump`, `error`, `stage_clear` พร้อมระบบจำสถานะเปิด/ปิดเสียง Mute ใน LocalStorage (`toggleAudioMute()`, `isAudioMuted()`) |
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

## 13. ระบบคีย์บอร์ดและฮาร์ดแวร์สแกนบาร์โค้ด (Barcode Hardware & Thai Fix)

- **การจำลองแป้นพิมพ์ของปืนสแกน (Keyboard Wedge)**: ปืนยิงบาร์โค้ด USB/Bluetooth จะส่งข้อมูลเสมือนการพิมพ์แป้นพิมพ์อย่างรวดเร็วและปิดท้ายด้วยปุ่ม `Enter`
- **การแก้ปัญหาแป้นพิมพ์ภาษาไทย (Thai Barcode Auto-Correction)**:
  - หากระบบปฏิบัติการเปิดค้างอยู่ที่ภาษาไทย ตัวเลข `8850123` จะกลายเป็น `๕๕๔๐๑๒๓` หรือตัวอักษรเกษมณี `ขชๆพะ`
  - ฟังก์ชัน `normalizeThaiBarcode()` ใน `js/utils/barcodeHelper.js` จะดักจับและแปลงตัวอักษรภาษาไทยทั้งหมดกลับเป็นตัวเลขอารบิก `0-9` และอักษรภาษาอังกฤษ `a-z` ทันที
- **ป๊อปอัปจัดการบาร์โค้ดไม่พบ (`barcode-not-found-modal`)**:
  - กดปุ่ม `[Y]` หรือ `Enter`: เปิดฟอร์มสร้างสินค้าใหม่โดยกรอกบาร์โค้ดนั้นให้อัตโนมัติ
  - กดปุ่ม `[R]`: เปิดโมดัล **Re-new Barcode** เพื่อนำบาร์โค้ดนั้นไปจับคู่กับสินค้าเดิมในระบบ
  - กดปุ่ม `[N]` หรือ `Esc`: ยกเลิก และคืนโฟกัสกลับไปที่ช่องยิงบาร์โค้ด

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

---

## 15. กฎและข้อบังคับสำคัญสำหรับผู้พัฒนาและ AI (Critical Rules & Directives)

> **คำเตือนสำหรับ AI Agents และผู้พัฒนาซอฟต์แวร์ทุกคน:** กฎเหล่านี้เป็นข้อบังคับเด็ดขาดที่ต้องปฏิบัติตามอย่างเคร่งครัด

### 📌 กฎข้อที่ 1: กฎการคืนโฟกัสช่องกรอก Barcode (Barcode Input Focus Rule)
- หน้าจอที่มีการสแกนบาร์โค้ด (`barcode-input` หรือ `prod-search-input`) **จะต้องส่งโฟกัสกลับไปที่ช่องกรอกบาร์โค้ดทุกครั้ง** เมื่อเสร็จสิ้นกระบวนการทำงานใดๆ บน Pop-up / Modal / Button:
  - เมื่อจบ flow การ **Re-new Barcode** (จับคู่บาร์โค้ดใหม่)
  - เมื่อจบ flow การ **+ เพิ่มสินค้าใหม่** (Add New Product CRUD) ทั้งกรณีบันทึกสำเร็จหรือกดยกเลิก
  - เมื่อปิด Modal แจ้งเตือนบาร์โค้ดไม่พบ (`barcode-not-found-modal`)
  - เมื่อสแกนและเพิ่มสินค้าลงตะกร้าในหน้าขาย (POS)
  - เมื่อจบการขาย ชำระเงิน หรือล้างตะกร้า
- **รูปแบบคำสั่ง:** ต้องหน่วงเวลาเล็กน้อยเพื่อให้ DOM render และ Modal transition เสร็จสมบูรณ์:
  ```javascript
  setTimeout(() => {
    const input = document.getElementById('barcode-input') || document.getElementById('prod-search-input');
    if (input) input.focus();
  }, 100);
  ```

### 📌 กฎข้อที่ 2: กฎความเสถียรฐานข้อมูล Dual-Client Fallback (Resilient Database Rule)
- **ห้าม** ยิงคำสั่ง `supabase.from('...').update/insert/delete` ตรงๆ โดยไม่มีระบบ Fallback
- **ข้อบังคับ:** ต้องเรียกผ่านฟังก์ชันผู้ช่วยส่วนกลางใน `js/utils/dbHelper.js` เสมอ:
  - `resilientUpdate(supabase, table, id, payload)`
  - `resilientInsert(supabase, table, payload)`
  - `resilientDelete(supabase, table, id)`
- ตัวช่วยเหล่านี้จะสลับไปยิงผ่าน `window.publicSupabase` (Anon Client) อัตโนมัติหากพบปัญหา Session หลุด และตรวจจับคอลัมน์ที่ไม่ตรงกับสกีมาเพื่อตัดทิ้งและ Retry ทันที

### 📌 กฎข้อที่ 3: กฎความปลอดภัยของลำดับพารามิเตอร์และการเปรียบเทียบ ID (Parameter & ID Safety Rule)
1. **ลำดับพารามิเตอร์ของ `resilientUpdate`:**
   - มาตรฐานหลัก: `resilientUpdate(supabase, table, id, payload)`
   - ผู้พัฒนาต้องส่ง `id` ก่อน `payload` เสมอ
2. **การเปรียบเทียบ ID ข้ามโมดูล:**
   - ห้ามใช้ `a === b` ตรงๆ กับ ID ที่มาจาก Supabase และ IndexedDB เพราะชนิดข้อมูลอาจเป็น `String`, `Number`, หรือ `BigInt`
   - **ข้อบังคับ:** ต้องใช้ `safeIdCompare(a, b)` หรือแปลงด้วย `safeNumberId(id)` เสมอ

### 📌 กฎข้อที่ 4: กฎการคืนสถานะปุ่มก่อนแสดง Dialog (Non-Blocking UI Before Alert Rule)
- คำสั่ง `alert()` หรือ `confirm()` ของเบราว์เซอร์เป็น Synchronous Blocking Call ที่จะหยุดการทำงานของ JavaScript ทันที
- **ข้อบังคับ:** ก่อนเรียก `alert(...)` แจ้งเตือนความสำเร็จหรือล้มเหลว **ต้องคืนสถานะปุ่มกด (Disabled = false และเปลี่ยนข้อความปุ่มกลับเป็นปกติ) ก่อนเสมอ**

### 📌 กฎข้อที่ 5: กฎความสอดคล้องของตาราง Stock Movements (Stock Movements Schema Rule)
- สกีมาของตาราง `stock_movements` ในฐานข้อมูล Supabase ประกอบด้วยคอลัมน์:
  `id`, `product_id`, `type`, `quantity`, `unit_cost`, `note`, `created_at`
- **ห้าม** ส่งฟิลด์ที่ไม่มีอยู่ในตาราง เช่น `ref_invoice_no` เข้าไปเป็นคอลัมน์เดี่ยวเด็ดขาด
- หากมีข้อมูลเลขอ้างอิงเอกสารหรือใบกำกับภาษี ให้ผนวกเข้าในฟิลด์ `note` เช่น:
  ```javascript
  note: `รับเข้า ${inQty} ชิ้น จาก ${supplier} [เลขที่เอกสาร: ${invoiceNo}]`
  ```

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

---

**Pixel POS & AI Inventory** — *Crafted with retro arcade passion & modern cloud architecture.* 🕹️📦✨
