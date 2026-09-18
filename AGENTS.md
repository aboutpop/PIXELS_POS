# Pixel POS & AI Inventory - Development Guidelines & Rules

> **คู่มือกฎเกณฑ์, เงื่อนไขตรรกะ (Business Logic), ข้อควรระวัง (Gotchas) และสถาปัตยกรรมทำงานร่วมกันระหว่าง Front-end และ Back-end สำหรับผู้พัฒนาและ AI Agent**  
> *เอกสารฉบับสมบูรณ์ รวบรวมจากการวิเคราะห์ พัฒนา และปรับปรุงระบบจริงตลอดทั้งโครงการ*

---

## 📑 สารบัญ (Table of Contents)
1. [ภาพรวมระบบและปรัชญาการพัฒนา (System Overview)](#1-ภาพรวมระบบและปรัชญาการพัฒนา-system-overview)
2. [กฎและคำสั่งบังคับระดับวิกฤต (Critical Rules & Directives)](#2-กฎและคำสั่งบังคับระดับวิกฤต-critical-rules--directives)
3. [สถาปัตยกรรมระบบและการทำงานร่วมกันระหว่าง Front-end และ Back-end](#3-สถาปัตยกรรมระบบและการทำงานร่วมกันระหว่าง-front-end-และ-back-end)
4. [ตรรกะและ Business Logic สำคัญของระบบ (Core Business Logic)](#4-ตรรกะและ-business-logic-สำคัญของระบบ-core-business-logic)
5. [ข้อควรระวังและกับดักที่พบบ่อย (Critical Gotchas & Pitfalls Table)](#5-ข้อควรระวังและกับดักที่พบบ่อย-critical-gotchas--pitfalls-table)
6. [แนวทางการเขียนโค้ดและขยายโมดูล (Coding Conventions & Best Practices)](#6-แนวทางการเขียนโค้ดและขยายโมดูล-coding-conventions--best-practices)

---

## 1. ภาพรวมระบบและปรัชญาการพัฒนา (System Overview)

**Pixel POS & AI Inventory** เป็นระบบบริหารงานขายหน้าร้านและจัดการสต็อกสินค้าอัจฉริยะ ออกแบบด้วยแนวคิด **"Retro Arcade Aesthetics with Modern Cloud Resilience"**:
- **ส่วนต่อประสานผู้ใช้ (UI):** สไตล์ Retro 8-bit/16-bit Arcade โทนสีคลาสสิกเรโทร พร้อมเอฟเฟกต์ CRT Scanline และเสียงสังเคราะห์ Chiptune SFX ผ่าน Web Audio API
- **ความเข้ากันได้ของอุปกรณ์ (Target Device):** รองรับแท็บเล็ตจอสัมผัสขนาด **1024 x 640 px (Landscape)** เป็นหน้าจออ้างอิงหลัก และทำงานได้แบบ PWA (Progressive Web App) ติดตั้งเป็น Standalone App ได้
- **สถาปัตยกรรมโค้ด (Tech Foundation):** ใช้ **Native ES6+ Modules** ล้วนโดยไม่ผ่าน Bundler ที่ซับซ้อน ทำให้ดีบักง่าย รวดเร็ว และเป็นระเบียบ
- **ความทนทานต่อออฟไลน์ (Offline-First):** ทำงานร่วมกันระหว่างแคชในเครื่อง **IndexedDB (Dexie.js)** และฐานข้อมูลคลาวด์ **Supabase (PostgreSQL 15+)**
- **ปัญญาประดิษฐ์ (AI Integration):** ใช้ **Google Gemini Vision API** สแกนใบเสร็จ/บิลรับของเพื่อนำเข้าสต็อกอัตโนมัติ

---

## 2. กฎและคำสั่งบังคับระดับวิกฤต (Critical Rules & Directives)

> ⚠️ **คำเตือนสำหรับ AI Agents และผู้พัฒนาทุกคน:** กฎทั้ง 10 ข้อด้านล่างนี้เป็นข้อบังคับเด็ดขาด (Mandatory Rules) ห้ามละเมิดเด็ดขาดไม่ว่าในกรณีใดๆ

---

### 📌 กฎข้อที่ 1: กฎการคืนโฟกัสช่องกรอก Barcode (Barcode Input Focus Rule)
> **Mandate:** ทุกหน้าจอที่มีการสแกนบาร์โค้ด (เช่น POS `#pos-barcode-input`, แคตตาล็อก `#prod-search-input`, หรือหน้า Stock-In) **จะต้องส่งโฟกัสกลับไปที่ช่องกรอกบาร์โค้ดทุกครั้ง** เมื่อสิ้นสุด flow การทำงานบน Pop-up / Modal / Button เช่น:
- เมื่อจบ flow การ **Re-new Barcode** (จับคู่บาร์โค้ดใหม่ให้กับสินค้าเดิม)
- เมื่อจบ flow การ **+ เพิ่มสินค้าใหม่** (Product CRUD Modal) ทั้งกรณีกดยืนยันสำเร็จ หรือกดยกเลิก/ปิด Modal
- เมื่อปิด Modal แจ้งเตือนบาร์โค้ดไม่พบ (`barcode-not-found-modal`) หรือ Modal ยืนยันใดๆ
- เมื่อสแกนและเพิ่มสินค้าลงตะกร้าในหน้าขาย (POS)
- เมื่อเสร็จสิ้นการชำระเงิน หรือเมื่อกดล้างตะกร้าสินค้า
- เมื่อกดย้อนกลับจากหน้าชำระเงิน (`views/payment.html`) กลับมายังหน้าขายหลัก (`views/pos.html`)
- **รูปแบบการคืนโฟกัสที่ถูกต้อง:** ต้องหน่วงเวลาเล็กน้อยเพื่อให้เบราว์เซอร์จัดการ Animation และเรนเดอร์ DOM เสร็จสมบูรณ์:
  ```javascript
  setTimeout(() => {
    const input = document.getElementById('pos-barcode-input') || document.getElementById('prod-search-input');
    if (input) input.focus();
  }, 100);
  ```

---

### 📌 กฎข้อที่ 2: กฎความเสถียรฐานข้อมูล Dual-Client Fallback (Resilient Database Rule)
> **Mandate:** ห้ามยิงคำสั่ง `supabase.from('...').update/insert/delete` ตรงๆ โดยไม่มีระบบ Fallback สำรองเด็ดขาด
- แคชเชียร์หน้าร้านอาจทำงานภายใต้ Anonymous Session หรือ Token หมดอายุ (ทำให้เกิดข้อผิดพลาด 401 Unauthorized, 403 Forbidden หรือ RLS Restricted)
- **ข้อบังคับ:** ทุกการเขียนฐานข้อมูลต้องเรียกผ่าน Helper กลางใน `js/utils/dbHelper.js` เสมอ:
  - `resilientUpdate(supabase, table, id, payload)`
  - `resilientInsert(supabase, table, payload)`
  - `resilientDelete(supabase, table, id)`
- **กลไกความเสถียร 4 ชั้นใน dbHelper:**
  1. ยิงผ่าน Primary Supabase Client ก่อน
  2. สลับไปยิงผ่าน `window.publicSupabase` (Anon Client) อัตโนมัติหากติด RLS หรือ Session หลุด
  3. ตรวจจับคอลัมน์ที่ไม่ตรงกับ Schema ของฐานข้อมูล (`PGRST204` / `extractMissingColumn`) แล้วตัดฟิลด์นั้นทิ้งพร้อมสั่ง Retry อัตโนมัติทันที
  4. ทำการบันทึกแบบไม่พ่วง `.select()` หากสิทธิ์ SELECT ของตารางถูกจำกัดภายใต้นโยบาย RLS

---

### 📌 กฎข้อที่ 3: กฎลำดับพารามิเตอร์และการเปรียบเทียบ ID (Parameter & ID Safety Rule)
> **Mandate:** การส่งอาร์กิวเมนต์และการเปรียบเทียบ Primary Key / Foreign Key ต้องเป็นไปตามมาตรฐานความปลอดภัย:
1. **ลำดับอาร์กิวเมนต์ของ `resilientUpdate`:**
   - มาตรฐานหลัก: `resilientUpdate(supabase, table, id, payload)` (ส่ง `id` ก่อน `payload` เสมอ)
   - แม้ `dbHelper.js` จะมีกลไก Polymorphic สลับลำดับให้อัตโนมัติ แต่ผู้พัฒนาต้องยึดมาตรฐานนี้เพื่อความสะอาดของโค้ด
2. **การเปรียบเทียบ ID ข้ามโมดูล:**
   - **ห้าม** ใช้ `a === b` ตรงๆ กับ ID ที่มาจาก Supabase และ IndexedDB เพราะชนิดข้อมูลอาจเป็น `String`, `Number`, หรือ `BigInt` (เช่น `'1'` กับ `1`)
   - **ข้อบังคับ:** ต้องใช้ฟังก์ชัน `safeIdCompare(a, b)` หรือแปลงด้วย `safeNumberId(id)` จาก `dbHelper.js` เสมอ

---

### 📌 กฎข้อที่ 4: กฎระบบดักจับบาร์โค้ดฮาร์ดแวร์และแก้ปัญหาผังแป้นพิมพ์ไทย (Hardware Barcode Engine & Thai Auto-Correction)
> **Mandate:** ปืนยิงบาร์โค้ดฮาร์ดแวร์ทำงานเสมือนการพิมพ์แป้นพิมพ์ความเร็วสูง (Keyboard Wedge) ระบบต้องดักจับข้อมูลอย่างแม่นยำ ป้องกันปัญหาภาษาไทย ป้องกันอาการกระตุกของบลูทูธ (Bluetooth Jitter) และตัดการพิมพ์ด้วยมือของมนุษย์:
1. **In-Memory Capture ผ่าน Physical Keys (`e.code`):**
   - ดักจับเหตุการณ์ที่ Capture Phase (`window.addEventListener('keydown', ..., true)`)
   - สกัดตัวเลขจาก `e.code.startsWith('Digit')` และ `e.code.startsWith('Numpad')` หรือใช้ `getCharFromPhysicalEvent(e)` เพื่อไม่ให้อิงกับภาษาแป้นพิมพ์ของระบบปฏิบัติการ
   - เมื่อโฟกัสอยู่ที่ช่องบาร์โค้ด (`#pos-barcode-input`) ให้สั่ง `e.preventDefault()` ป้องกัน DOM Repaint Lag และซิงก์ค่าตรงสู่ `.value` เพื่อไม่ให้อักษรไทยหลุดเข้าไปใน DOM
2. **ระบบความเร็วปรับตัวได้ (Adaptive Cadence Dual-Threshold):**
   - หัวอ่านบาร์โค้ดบลูทูธไร้สายเมื่อหลับ (Standby/Sleep) แล้วตื่นขึ้นมายิง 1–3 ตัวแรก จะเกิด Latency Spike จากการ Reconnect Handshake (อาจหน่วง 80–150ms)
   - **ข้อบังคับ:** ใช้ Dual-Threshold แทนค่าคงที่เดี่ยว:
     ```javascript
     const CADENCE_FAST = 55;      // ความเร็วปกติของหัวอ่านฮาร์ดแวร์ (< 55ms)
     const CADENCE_WAKEUP = 180;   // Grace Period สำหรับ 1-3 ตัวแรกช่วงตื่น (< 180ms)
     const allowedCadence = (barcodeBuffer.length <= 3) ? CADENCE_WAKEUP : CADENCE_FAST;
     if (timeDiff > allowedCadence) barcodeBuffer = '';
     ```
   - ป้องกันอาการเลขตัวหน้านำหลุดหาย (Dropped Leading Digits) ได้ 100% พร้อมตัดการพิมพ์มือของมนุษย์ (> 200ms) ทิ้งอย่างเด็ดขาด
3. **การประมวลผลเมื่อกด Enter และการรับมือสแกนผิดพลาด:**
   - เมื่อพบ `Enter` และความยาว Buffer $\ge 3$ ตัวอักษร ให้สั่ง `preventDefault()`, `stopPropagation()` และส่งเข้า `handleBarcodeScan(scannedCode)` ทันที
   - หากไม่พบสินค้า ให้เปิด `barcode-not-found-modal` ซึ่งมีป้ายตรวจจับความยาวบาร์โค้ด ($< 8$ หลัก) แจ้งเตือนกรณีสัญญาณหลุดสแกนไม่ครบ
   - ปุ่ม **`❌ สแกนผิดพลาด / ยกเลิก [N]`** ให้ระบบเข้าใจว่าเป็นการสแกนที่ผิดพลาด ล้าง buffer ในระบบ และคืนโฟกัสกลับไปที่ `#pos-barcode-input` ทันทีตามกฎข้อที่ 1
4. **Fallback Thai Normalization:**
   - ทุกจุดที่รับ Input บาร์โค้ดแบบ Manual หรือ Paste ต้องผ่านฟังก์ชัน:
     ```javascript
     import { normalizeThaiBarcode } from '../utils/barcodeHelper.js';
     const cleanBarcode = normalizeThaiBarcode(rawInput);
     ```

---

### 📌 กฎข้อที่ 5: กฎการคืนสถานะปุ่มก่อนแสดง Dialog (Non-Blocking UI Before Alert Rule)
> **Mandate:** คำสั่ง `alert()` หรือ `confirm()` ของเบราว์เซอร์เป็น Synchronous Blocking Call ที่จะหยุดการทำงานของ JavaScript ในหน้านั้นทันที
- **ข้อบังคับ:** ก่อนเรียก `alert(...)` หรือ `confirm(...)` แจ้งเตือนความสำเร็จหรือข้อผิดพลาด **ต้องคืนสถานะปุ่มกด (Disabled = false และเปลี่ยนข้อความปุ่มกลับเป็นปกติ) ก่อนเสมอ**
- ห้ามวางคำสั่งคืนสถานะปุ่มไว้หลัง `alert()` เพราะจะทำให้ปุ่มค้างสถานะ `กำลังบันทึก...` ในขณะที่หน้าต่างแจ้งเตือนแสดงอยู่

---

### 📌 กฎข้อที่ 6: กฎความสอดคล้องของตาราง Stock Movements (Stock Movements Schema Rule)
> **Mandate:** สกีมาของตาราง `stock_movements` ในฐานข้อมูล Supabase มีเฉพาะคอลัมน์ต่อไปนี้:
`id`, `product_id`, `type`, `quantity`, `unit_cost`, `note`, `created_at`
- **ห้าม** ส่งฟิลด์ที่ไม่มีอยู่ในตาราง เช่น `ref_invoice_no` หรือ `supplier` เข้าไปเป็นคอลัมน์เดี่ยวเด็ดขาด (จะทำให้เกิด Error PostgREST 400 ทันที)
- หากมีข้อมูลเลขอ้างอิงเอกสารหรือชื่อผู้ขาย ให้ผนวกเข้าในฟิลด์ `note` เช่น:
  ```javascript
  note: `รับเข้า ${inQty} ชิ้น จาก ${supplier} [เลขที่เอกสาร: ${invoiceNo}]`
  ```

---

### 📌 กฎข้อที่ 7: กฎการออกแบบหน้าจอ Zero-Scroll สำหรับแท็บเล็ต (Tablet 1024x640 Landscape Reference)
> **Mandate:** หน้าจอแท็บเล็ตที่ใช้งานจริงมีขนาด `1024 x 640 px` ในแนวนอน
- **พื้นที่แสดงผลจริง (Usable Viewport):** เมื่อหักลบแถบสถานะของ Android (~28px), แถบ Gesture Bar ด้านล่าง (~18px) และ Header ของระบบ (~44px) จะเหลือพื้นที่แนวตั้งในการทำงานประมาณ **540px**
- **ข้อบังคับ Zero-Scroll (Zero-Scroll Mandate):**
  1. หน้าสรุปบิลและชำระเงิน (`views/payment.html`) ต้องแสดงผลครบถ้วนในหน้าจอเดียว **ห้ามมี Outer Scrollbar**
  2. ปุ่ม **"รับเงินสดพอดี & ตัดสต็อก [Enter]"** จะต้องปรากฏอยู่บนจอเสมอ 100% โดยที่แคชเชียร์ไม่ต้องเลื่อนจอลงไปกด (รองรับ 1-Click Instant Checkout เมื่อแตะปุ่ม `⚡ พอดี (ปิดบิล)` หรือกด `[Enter]` ทันทีหากได้รับเงินสดพอดี ไม่ต้องเสียเวลากรอกตัวเลข)
  3. อนุญาตให้เกิด Scrollbar เฉพาะภายในกล่องรายการสินค้า (`#modal-receipt-items`) เท่านั้นเมื่อมีสินค้าจำนวนมากเกินความสูง
  4. หน้าต่างย่อย แถบข้อมูลสรุปบิล และ Numpad ต้องถูกออกแบบให้มีขนาดกะทัดรัด (Compact) เพื่อไม่ให้ดันส่วนอื่นๆ ล้นจอ

---

### 📌 กฎข้อที่ 8: กฎระบบแป้นตัวเลขรวมศูนย์ (Unified On-Screen Numpad & Zero-Replace Protection)
> **Mandate:** บนแท็บเล็ตจอสัมผัส ระบบต้องใช้แป้นตัวเลขบนหน้าจอ (On-Screen Numpad) ที่รองรับการป้อนข้อมูลอเนกประสงค์
- **การสลับเป้าหมาย (Active Target Routing):**
  - แผง Numpad ฝั่งขวาในหน้าชำระเงิน ต้องรองรับทั้งการกรอก `cash` (เงินสดที่รับมา) และ `discount` (ส่วนลดท้ายบิล) ผ่านตัวแปรสถานะ `this.activeNumpadTarget`
- **การแก้ปัญหาเลข 0 ค้าง (Zero-Replace Protection):**
  - หากค่าในช่องกรอกเป็น `0` หรือเป็นค่าว่าง การกดเลข 1-9 (ไม่ว่าจะกดจาก Numpad บนจอ หรือแป้นพิมพ์ฮาร์ดแวร์) **จะต้องเข้าแทนที่เลข 0 ทันที** ป้องกันข้อผิดพลาด เช่น ต้องการลด `5` บาท แต่กลายเป็น `50` บาท หรือ `05` บาท
- **การบล็อกคีย์บอร์ดเสมือนของระบบ (Soft-Keyboard Suppression):**
  - ช่องกรอกที่ต้องใช้ปืนสแกนเนอร์หรือ Numpad ของแอป (เช่น `#pos-barcode-input`, `#pos-cash-received`, `#modal-discount-input`) ต้องใส่ `inputmode="none"` เสมอ เพื่อป้องกันไม่ให้ Virtual Keyboard ของ Android เด้งขึ้นมาบดบังหน้าจอ

---

### 📌 กฎข้อที่ 9: กฎการป้องกันเบราว์เซอร์แคชและวงจรชีวิตโมดูล (Centralized Cache Invalidation & Module Lifecycle Mandate)
> **Mandate:** ระบบใช้สถาปัตยกรรม In-Memory View Cache ร่วมกับ Service Worker (Cache-First) เพื่อความเร็วสูงสุดและไม่กินพื้นที่ `localStorage` โดยมีศูนย์กลางเลขเวอร์ชัน (Single Source of Truth) อยู่ที่ `js/config.js` (`APP_VERSION`) และควบคุมหน่วยความจำด้วยวงจรชีวิตโมดูลมาตรฐาน
- **ข้อบังคับการปรับเวอร์ชัน (Cache Invalidation):** ทุกครั้งที่มีการแก้ไขไฟล์หรือ Release ระบบ (`views/*.html`, `js/modules/*.js`, `js/utils/*.js`, `css/*.css`):
  1. อัปเดตค่า `APP_VERSION` ใน `js/config.js` (เช่น `export const APP_VERSION = '1.2.091826';`) ซึ่ง `viewLoader.js` จะดึงไปแนบท้าย query parameter ของวิวทุกไฟล์ (`?v=${APP_VERSION}`) อัตโนมัติ
  2. อัปเดต `CACHE_NAME` ใน `sw.js` เพื่อให้ Service Worker เคลียร์และแคชไฟล์ใหม่
  3. อัปเดต query parameter ท้ายไฟล์ใน `index.html` หากมีการแก้ไขสคริปต์หลัก
- **ข้อบังคับวงจรชีวิตโมดูล (Lifecycle & Disposables):**
  - ทุกโมดูลหน้าจอต้องมี `this.disposables = []`, เมธอด `async init()`, และเมธอด `destroy()`
  - ทุกการดักฟัง `appBus.on(...)` หรือ DOM Events ต้องเก็บฟังก์ชัน Unsubscribe ลงใน `this.disposables` เสมอ และต้องสลับหน้าผ่าน `setActiveModule()` เพื่อสั่งทำลายโมดูลเดิม ป้องกันการรั่วไหลของหน่วยความจำ (Memory Leak) บนแท็บเล็ตแคชเชียร์
- หากละเลยขั้นตอนนี้ เครื่องแท็บเล็ตของผู้ใช้จะยังคงโหลดโค้ดเก่าจากแคชเครื่อง หรือเกิดอาการหน่วง/แครชจากการสะสมของ Event Listener

---

### 📌 กฎข้อที่ 10: กฎความปลอดภัยและการจัดการข้อมูลความลับ (Strict No-Secret Exposing Rule)
> **Mandate:** ความปลอดภัยของระบบและความเป็นส่วนตัวของผู้ใช้เป็นสิ่งสำคัญสูงสุด
- **ห้าม** ใส่ข้อมูลความลับ เช่น API Keys (เช่น Gemini API Key), Database Password, Supabase Service Role Key, หรือ Token ลงในซอร์สโค้ดฝั่ง Client (`index.html`, `js/*`) หรือเอกสาร Markdown (`AGENTS.md`, `README.md`) เด็ดขาด
- การตั้งค่า Gemini API Key, Google Apps Script Web App URL หรือบัญชี PromptPay จะต้องถูกบันทึกลงในตาราง `app_settings` บน Supabase / IndexedDB ผ่านหน้าจอตั้งค่าระบบ (`views/settings.html`) พร้อมการทำ Security Masking เสมอ

---

## 3. สถาปัตยกรรมระบบและการทำงานร่วมกันระหว่าง Front-end และ Back-end

```text
┌──────────────────────────────────────────────────────────────────────────────────┐
│                             BROWSER FRONT-END (SPA)                              │
│                                                                                  │
│   index.html (Core Shell) ──► js/core/viewLoader.js ──► views/*.html (Partials)  │
│   ├── Header (Network Status, Sound Toggle, Theme Switcher, Navigation)         │
│   └── Viewport (#app-viewport: pos, payment, products, stockin, orders, etc.)    │
│                                                                                  │
│   ┌──────────────────────────────────────────────────────────────────────────┐   │
│   │        Core Modules (Standardized Lifecycle: init / destroy)             │   │
│   │   pos.js        products.js       categories.js      orders.js           │   │
│   │   stock-in.js   reports.js        settings.js        pwa-install.js      │   │
│   └────────────────────────────────────┬─────────────────────────────────────┘   │
│                                        │                                         │
│                      Central Event Bus │ (appBus: 'products:changed', etc.)      │
│                      Scoped Disposals  │ (returns unsubscribe callback)          │
│                                        ▼                                         │
│   ┌──────────────────────────────────────────────────────────────────────────┐   │
│   │                             Helper Services                              │   │
│   │   dbHelper.js       barcodeHelper.js     promptpay.js     audio.js       │   │
│   │   gdrive.js         thermal-printer.js   imageUploader.js geminiApi.js   │   │
│   │   themeHelper.js    sync.js              clipboard.js                    │   │
│   └────────────────────────┬──────────────────────────────────┬──────────────┘   │
│                            │                                  │                  │
│             IndexedDB Sync │                                  │ REST API / SDK   │
│                            ▼                                  │                  │
│               Dexie.js (RetroArcadePosDB)                     │                  │
│               - products / categories                         │                  │
│               - offline_orders / settings                     │                  │
└────────────────────────────┼──────────────────────────────────┼──────────────────┘
                             │                                  │
                             ▼                                  ▼
      ┌───────────────────────────────┐           ┌────────────────────────────┐
      │     SUPABASE (POSTGRESQL)     │           │   EXTERNAL CLOUD BACKEND   │
      │                               │           │                            │
      │  - products     - orders      │           │  - Google Gemini API       │
      │  - categories   - sale_items  │           │    (Vision OCR Staging)    │
      │  - app_settings - movements   │           │  - Google Apps Script      │
      │  - Row Level Security (RLS)   │           │    (Drive Image Pipeline)  │
      └───────────────────────────────┘           └────────────────────────────┘
```

### 3.1 สถาปัตยกรรม Front-end View Loader, In-Memory Caching & Versioning
- **Single Page Application (SPA):** ไฟล์ `index.html` ทำหน้าที่เป็น Core Shell โหลดไลบรารีพื้นฐาน แสดง Header และควบคุมการสลับหน้าในคอนเทนเนอร์ `#app-viewport`
- **Dynamic View Partial Loading (`viewLoader.js`):** เมื่อมีการสลับหน้า ระบบจะ fetch ไฟล์ HTML จากโฟลเดอร์ `views/` มาฝังลงใน `#app-viewport`
- **In-Memory View Cache (Map):** เก็บข้อมูล Partial HTML views ในหน่วยความจำ RAM ของเบราว์เซอร์ (`const viewCache = new Map()`) ทำให้การสลับหน้าเร็วระดับ sub-millisecond ในช่วง Runtime โดย **ไม่เปลืองโควตา 5MB ของ `localStorage`** (ตัดปัญหา QuotaExceededError 100%)
- **Service Worker Offline Persistence (`sw.js`):** แคชไฟล์ HTML แบบ Cache-First ผ่าน Service Worker โดยใช้ `{ ignoreSearch: true }` ทำให้ทำงานออฟไลน์ได้สมบูรณ์และตัดปัญหา Cache-busting query parameter ไม่ตรงกับแคช
- **Legacy Cache Auto-Purge (`purgeLegacyViewCache()`):** ทำการสแกนและลบคีย์แคชเก่า `pixel_view_cache_*` ออกจาก `localStorage` ทันทีที่ระบบบูต เพื่อคืนพื้นที่ให้เบราว์เซอร์
- **Centralized Versioning (`APP_VERSION`):** กำหนดเลขเวอร์ชันที่ศูนย์กลางเดียวใน `js/config.js` (`export const APP_VERSION = '1.2.091826';`) โดย `viewLoader.js` จะดึงค่านี้ไปแนบ query parameter `?v=${APP_VERSION}` อัตโนมัติ ป้องกันปัญหาเบราว์เซอร์แคชไฟล์เก่าค้าง
- **Navigation Guard:** ควบคุมด้วยคลาส `nav-loading-locked` และ `window.setAppLoading()` ล็อกการกดสลับหน้าซ้ำขณะระบบกำลังดาวน์โหลดข้อมูลแคตตาล็อกสินค้าเริ่มต้น

### 3.2 ระบบ Event Bus รวมศูนย์ (`appBus`) และวงจรชีวิตโมดูล (Module Lifecycle Architecture)
- เพื่อลดการผูกมัดของโค้ด (Decoupled Architecture) โมดูลต่างๆ จะสื่อสารกันผ่าน `appBus` (ตั้งอยู่ใน `js/utils/dbHelper.js`):
  - `appBus.emit('products:changed', { action: 'save' })`: แจ้งเตือนทุกหน้า (POS, Products, Dashboard) ให้รีเฟรชแคตตาล็อกสินค้าพร้อมกันแบบ Real-time
  - `appBus.emit('stock:updated', { productId, newStock })`: แจ้งเตือนเมื่อมีการตัดสต็อกหรือรับเข้าสต็อก
  - `appBus.emit('categories:changed')`: อัปเดตรายการหมวดหมู่และป้ายนับจำนวนสินค้า
  - `appBus.emit('theme:changed', { theme })`: ปรับแต่งส่วนประกอบ UI เมื่อมีการสลับธีม
- **Disposable Event Subscriptions:** เมธอด `appBus.on(event, handler)` ได้รับการปรับปรุงให้คืนค่าเป็นฟังก์ชัน Unsubscribe สำหรับยกเลิกการดักฟัง:
  ```javascript
  const unregister = appBus.on('products:changed', () => this.refresh());
  this.disposables.push(unregister);
  ```
- **Module Lifecycle Interface & Zero Memory Leaks:**
  - บนแท็บเล็ตแคชเชียร์ที่เปิดใช้งานตลอดกะ 8–12 ชั่วโมง การสลับหน้าไปมาซ้ำๆ โดยไม่มีการถอดถอน Event Listener จะทำให้เกิด Memory Leak สะสมจนเบราว์เซอร์หน่วงหรือแครช
  - **ข้อบังคับ:** โมดูลแสดงผลหลักทั้ง 7 โมดูล (`pos.js`, `products.js`, `categories.js`, `orders.js`, `stock-in.js`, `reports.js`, `settings.js`) ต้องมีอินเทอร์เฟซมาตรฐานเดียวกัน:
    1. `this.disposables = []`: เก็บฟังก์ชัน Unsubscribe ของ Event Bus และ DOM Listeners
    2. `async init()` หรือ `init()`: ทำการ bind events และ render หน้าจอ
    3. `destroy()`: วนลูปเรียกฟังก์ชันใน `this.disposables` ทั้งหมดเพื่อถอดถอน listener และเคลียร์ DOM Reference
- **Lifecycle Coordination via `viewLoader.js` (`setActiveModule`):**
  - ใน `js/core/viewLoader.js` มีฟังก์ชัน `setActiveModule(targetModule)`
  - เมื่อผู้ใช้สลับหน้า (ผ่าน `switchView()` ใน `index.html`) ระบบจะเรียก `setActiveModule(targetModule)` ซึ่งจะสั่ง `.destroy()` โมดูลเดิมอัตโนมัติก่อนลงทะเบียนโมดูลใหม่เสมอ ตัดโอกาสเกิด Memory Leak และ Event Trigger ซ้ำซ้อน 100%

### 3.3 ฐานข้อมูลแคชในเครื่องและการทำงานออฟไลน์ (`Dexie.js` & `sync.js`)
- ฐานข้อมูล IndexedDB ชื่อ `RetroArcadePosDB` ประกอบด้วย 4 ตาราง: `products`, `categories`, `offline_orders`, `settings`
- **Stale-While-Revalidate Pattern:** เมื่อเปิดระบบ POS จะดึงสินค้าจาก Dexie IndexedDB มาแสดงผลทันทีภายในเสี้ยววินาที จากนั้นจึงยิง Supabase เพื่ออัปเดตข้อมูลล่าสุดแบบเบื้องหลัง
- **Offline Resilience:** กรณีอินเทอร์เน็ตหลุด คำสั่งซื้อจะถูกเขียนลงตาราง `offline_orders` ในเครื่อง และมี Service `js/sync.js` คอยตรวจจับสถานะออนไลน์เพื่อส่งคิวคำสั่งซื้อขึ้น Supabase อัตโนมัติเมื่อสัญญาณเน็ตกลับมา

### 3.4 โครงสร้างฐานข้อมูลบนคลาวด์ (`Supabase PostgreSQL 15+`)
ฐานข้อมูลประกอบด้วย 6 ตารางหลักที่เชื่อมโยงกันอย่างเป็นระบบ:
1. `categories`: หมวดหมู่สินค้า พร้อมฟิลด์ `sort_order` สำหรับจัดเรียง
2. `products`: แคตตาล็อกสินค้า, บาร์โค้ด, ราคาทุน (`NUMERIC(12,3)`), ราคาขาย, ยอดคงเหลือ, หน่วยนับ และหมวดหมู่
3. `orders`: บิลขาย, เลขที่บิล (`order_no`), สถานะ (`COMPLETED` / `VOIDED`), วิธีชำระเงิน, ยอดรวม, ส่วนลด, กำไร และเงินทอน
4. `sale_items`: รายการสินค้าในแต่ละบิล พร้อม `ON DELETE CASCADE` เชื่อมกับ `orders`
5. `stock_movements`: ประวัติสต็อก (`STOCK_IN`, `SALE`, `ADJUST`, `VOID`) พร้อมฟิลด์ `quantity`, `unit_cost`, `note`
6. `app_settings`: การตั้งค่าร้านค้า, หมายเลขพร้อมเพย์, ภาษี VAT, การตั้งค่าเครื่องพิมพ์ความร้อน และคีย์เชื่อมต่อระบบ

### 3.5 ระบบ AI Vision สำหรับรับเข้าสต็อก (`geminiApi.js`)
- **Multi-Model Failover Pool:** ติดต่อกับ Google Gemini API โดยมีระบบสลับโมเดลสำรองอัตโนมัติเมื่อโมเดลหลักติดโควตาหรือขัดข้อง:
  1. `gemini-2.5-flash` (โมเดลหลัก ความเร็วและความแม่นยำสูงสุด)
  2. `gemini-2.0-flash` (โมเดลสำรองลำดับที่ 1)
  3. `gemini-1.5-flash` (โมเดลสำรองลำดับที่ 2)
- **Timeout Protection:** ควบคุมด้วย `AbortController` (15 วินาทีสำหรับทดสอบ และ 45 วินาทีสำหรับการวิเคราะห์ใบเสร็จ)
- **JSON Normalization:** รองรับการถอด Markdown Code Fences (เช่น ```` ```json ````) อัตโนมัติ เพื่อให้ได้ JSON บริสุทธิ์

### 3.6 ไปป์ไลน์จัดเก็บรูปภาพผ่าน Google Apps Script (`Code.gs` & `gdrive.js`)
- เบราว์เซอร์บีบอัดรูปภาพด้วย HTML5 Canvas เป็น JPEG คุณภาพสูง
- แปลงเป็น Base64 แล้วส่ง POST ไปยัง Google Apps Script Web App (`Code.gs`)
- Apps Script จะนำไฟล์ไปบันทึกลง Google Drive ของเจ้าของร้านในโฟลเดอร์ `POS-Images`
- ส่ง ID ของรูปภาพกลับมา แล้วแปลงเป็น Direct CDN URL ผ่าน `gdrive.js`:
  `https://lh3.googleusercontent.com/d/{FILE_ID}`
  เพื่อการโหลดภาพที่รวดเร็ว แคชได้ในเบราว์เซอร์ และไม่ติดปัญหาข้อจำกัด CORS

### 3.7 ระบบธีมคู่ขนาน (Dual-Theme System)
- สลับธีมผ่าน attribute `data-theme` บน `<html>` และ `<body>`:
  - **`warm-arcade`:** ธีมเรโทรคลาสสิก โทนสีครีม-ทอง-น้ำตาล เหมือนตู้เกมยุค 80s/90s
  - **`cyber-emerald`:** ธีมมรกตไซเบอร์นีออน ดำ-เขียว คอนทราสต์สูงพิเศษ สบายตาและคมชัดบนจอแท็บเล็ต
- ซิงค์ค่าสีของ Android Status Bar ผ่าน `<meta name="theme-color">` อัตโนมัติ

### 3.8 ระบบการพิมพ์สลิปใบเสร็จความร้อน (Thermal Printing Engine)
- รองรับกระดาษความร้อนทั้งขนาด **58mm** และ **80mm**
- **Browser Print Engine:** ควบคุมด้วย `css/thermal-print.css` ผ่าน `@media print` ซ่อนองค์ประกอบ UI อื่นๆ ทั้งหมด เหลือเฉพาะตัวสลิปใบเสร็จสีดำสนิท พร้อมระยะขอบ 0mm
- **ESC/POS Bluetooth Service (`thermal-printer.js`):** ส่งชุดคำสั่งมาตรฐาน ESC/POS ตรงไปยังเครื่องพิมพ์ใบเสร็จบลูทูธไร้สายได้ทันที

---

## 4. ตรรกะและ Business Logic สำคัญของระบบ (Core Business Logic)

### 4.1 ตรรกะการคำนวณเงินหน้าร้าน (POS Financial Calculations)
- **Subtotal (ยอดรวมก่อนส่วนลด):**
  $$\text{Subtotal} = \sum (\text{unit\_price} \times \text{quantity})$$
- **Discount (ส่วนลดท้ายบิล):**
  - กรณีคิดเป็นเปอร์เซ็นต์ (`%`): $\text{discountAmount} = \text{round}\left(\frac{\text{subtotal} \times \text{percent}}{100}, 2\right)$
  - กรณีคิดเป็นจำนวนเงิน (`บาท`): $\text{discountAmount} = \min(\text{rawDiscount}, \text{subtotal})$
- **Net Amount (ยอดสุทธิ):**
  $$\text{netAmount} = \max(0, \text{subtotal} - \text{discountAmount})$$
- **Cost & Profit (ต้นทุนและกำไร):**
  $$\text{totalCost} = \sum (\text{unit\_cost} \times \text{quantity})$$
  $$\text{grossProfit} = \text{netAmount} - \text{totalCost}$$
- **VAT (ภาษีมูลค่าเพิ่ม 7% หรือตามที่ตั้งค่า):**
  - กรณีแสดงในใบเสร็จ: คำนวณยอดก่อนภาษี $(\text{netAmount} \times \frac{100}{107})$ และภาษี $(\text{netAmount} - \text{ยอดก่อนภาษี})$

---

### 4.2 ตรรกะการรับเข้าสต็อก AI (AI Stock-In Staging Logic)
1. **การแปลงแพ็คเป็นชิ้นเดี่ยว (Pack to Unit Decomposition):**
   - จำนวนชิ้นรับเข้าจริง: $\text{totalUnits} = \text{pack\_qty} \times \text{units\_per\_pack}$
   - ต้นทุนต่อชิ้น: ปัดเศษขึ้นที่ทศนิยม 3 ตำแหน่งเสมอ เพื่อป้องกันปัญหากำไรหายจากการปัดเศษลง
     ```javascript
     costPerPiece = Math.ceil((cost_per_pack / units_per_pack) * 1000) / 1000;
     ```
2. **การแก้ปัญหาสต็อกเดิมติดลบ (Negative Stock Resolution):**
   - หากสินค้าเดิมมียอดติดลบ เช่น `-2 ชิ้น` (จากการขายหน้าร้านล่วงหน้าก่อนบันทึกสต็อกเข้า)
   - เมื่อรับเข้า `10 ชิ้น`: ยอดคงเหลือใหม่จะเป็น `-2 + 10 = 8 ชิ้น`
   - บันทึกหมายเหตุชัดเจนลงใน `stock_movements` ว่าได้หักลบยอดติดลบเดิมแล้ว
3. **การตรวจสอบผลต่างราคาทุน (Cost Variance Detection):**
   - หากราคาทุนใหม่ต่างจากราคาทุนเดิม ระบบจะแสดงป้ายเตือน `⚠️ ปรับราคาทุนใหม่เป็น ฿XX.XXX` และไฮไลต์ให้พิจารณาปรับราคาขายปลีก
4. **การซิงค์ข้อมูลจากหน้าจอก่อนบันทึก (DOM Sync Guarantee):**
   - ต้องเรียก `syncStagingFromDOM()` วนลูปอ่านค่าจากทุก Input บนหน้าจอก่อนยืนยันบันทึก ป้องกันข้อมูลสูญหายกรณีผู้ใช้พิมพ์แก้ไขแล้วกดยืนยันทันที

---

### 4.3 ตรรกะการพักบิลและเรียกคืนบิล (Hold Bills & Resume Logic)
- บันทึกบิลที่พักไว้ลงใน `localStorage` (`pixel_pos_hold_bills`) เพื่อความรวดเร็วและป้องกันข้อมูลหายเมื่อรีเฟรช
- แสดงจำนวนบิลที่พักอยู่เป็น Badge สีแดงบนปุ่ม `📑 พักไว้`
- เมื่อกดเรียกคืนบิล (Resume):
  - หากในตะกร้าปัจจุบันมีสินค้าค้างอยู่ ระบบจะแสดงกล่องยืนยันว่าจะรวมสินค้า (Merge) หรือแทนที่บิลเดิม (Replace)
  - คืนค่ารายการสินค้า, จำนวน, และส่วนลดเดิมกลับเข้าสู่หน้าขายทันที

---

### 4.4 ตรรกะการร่วมจ่าย 2 ช่องทาง (Split Payment: Cash + PromptPay QR)
- แคชเชียร์สามารถรับเงินสดบางส่วน และให้ลูกค้าสแกน QR จ่ายส่วนที่เหลือได้:
  $$\text{transferAmount} = \max(0, \text{netAmount} - \text{cashReceived})$$
- สร้าง PromptPay QR Code ตามยอด `transferAmount` ทันทีแบบ Real-time
- หากรับเงินสดมามากกว่ายอดสุทธิ ระบบจะปรับ `transferAmount = 0` และคำนวณเงินทอนส่วนต่างให้ถูกต้อง

---

### 4.5 ตรรกะการจับคู่บาร์โค้ดใหม่ให้กับสินค้าเดิม (Re-new Barcode)
- เมื่อยิงบาร์โค้ดที่ไม่พบในระบบ สามารถกดปุ่ม `🔄 Re-new Barcode` เพื่อค้นหาสินค้าเดิมในสต็อก
- ตรวจสอบความปลอดภัย: ตรวจสอบว่าบาร์โค้ดใหม่ไม่ซ้ำซ้อนกับสินค้าอื่นในระบบ (`checkDuplicateBarcode`)
- อัปเดตบาร์โค้ดใหม่ลงฐานข้อมูล Supabase และแคช Dexie IndexedDB พร้อมส่งโฟกัสกลับช่องบาร์โค้ดหน้าขายทันที

---

### 4.6 ตรรกะการยกเลิกบิล (Order Void Logic)
- เมื่อกดยกเลิกบิลที่หน้าประวัติการขาย (`orders.js`):
  1. ปรับสถานะบิลใน `orders` เป็น `VOIDED` พร้อมบันทึกเหตุผลใน `void_reason`
  2. วนลูปสินค้าทุกรายการใน `sale_items` ของบิลนั้น
  3. นำจำนวนที่เคยขาย คืนกลับเข้าฟิลด์ `stock` ในตาราง `products`
  4. บันทึกประวัติความเคลื่อนไหวใน `stock_movements` ประเภท `VOID` เพื่อความถูกต้องในการตรวจสอบบัญชี

---

### 4.7 ตรรกะ PromptPay EMVCo QR Code
- สร้าง String ตามมาตรฐาน EMVCo Tag-Length-Value (TLV):
  - Tag 00: Version (`01`)
  - Tag 01: QR Type (`12` สำหรับ Dynamic QR มีระบุจำนวนเงิน)
  - Tag 29: Merchant Account Information (เบอร์โทรศัพท์มือถือ 10 หลัก แปลงเป็น Format `0066...` หรือ เลขประจำตัวผู้เสียภาษี 13 หลัก)
  - Tag 53: Currency Code (`764` - สกุลเงินบาท THB)
  - Tag 54: Transaction Amount (ระบุทศนิยม 2 ตำแหน่ง)
  - Tag 58: Country Code (`TH`)
  - Tag 63: Checksum คำนวณด้วยอัลกอริทึม **CRC16-CCITT (0xFFFF)** ผลลัพธ์ 4 หลักพิมพ์ใหญ่

---

### 4.8 ตรรกะระบบสแกนบาร์โค้ดฮาร์ดแวร์และการจัดการ Bluetooth Latency Jitter (Adaptive Cadence Scanner Engine)
- **สถาปัตยกรรม In-Memory Buffer:**
  - สแกนเนอร์บาร์โค้ดยิงข้อมูลเข้ามาเป็น Keystrokes ถี่ๆ ป้องกันการเด้งของ Virtual Keyboard ด้วยการใช้ `inputmode="none"` บน `#pos-barcode-input`
  - ตรวจจับผ่าน Capture Phase Keydown ก่อนที่ Event จะถึง DOM Elements ป้องกันไม่ให้เบราว์เซอร์กระตุกจากการ Render ทีละตัวอักษร
- **การจัดการความหน่วง Bluetooth Standby (Reconnection Jitter):**
  - เมื่อสแกนเนอร์พักการใช้งานเกิน 2-3 นาที บลูทูธจะเข้าโหมดประหยัดพลังงาน
  - เมื่อสแกนบาร์โค้ด EAN-13 ครั้งแรกหลังตื่น ตัวเลขตัวที่ 1 ถึง 3 จะถูกส่งด้วยระยะห่าง 80-140ms (หากใช้ Threshold คงที่ 50-60ms จะทำให้ Buffer ถูกล้างทิ้งกลางคันและตัวเลขนำหน้าตกหล่น)
  - ระบบจึงใช้ **Adaptive Cadence (Dual-Threshold)**: ให้ Grace Period 180ms ในช่วง 1-3 ตัวแรก และปรับเป็น 55ms เมื่อผ่านตัวที่ 3 เป็นต้นไป
- **การจัดการกรณีสแกนผิดพลาด (Erroneous / Incomplete Scan Handling):**
  - หากสแกนโดนบาร์โค้ดผิด หรือข้อมูลมาไม่ครบถ้วน โมดัล `#barcode-not-found-modal` จะแสดงข้อมูลความยาวหลักพร้อมคำแนะนำแจ้งเตือนสัญญาณหลุด
  - แคชเชียร์สามารถกด `❌ สแกนผิดพลาด / ยกเลิก [N]` หรือปุ่ม `[Esc]`
  - ระบบจะ Dismiss คำขอนี้ ล้าง Buffer และตัวอักษรตกค้าง พร้อมคืนโฟกัสที่ช่องสแกนเพื่อให้ยิงซ้ำได้ทันทีตามกฎข้อที่ 1

---

## 5. ข้อควรระวังและกับดักที่พบบ่อย (Critical Gotchas & Pitfalls Table)

| หัวข้อ (Issue) | สาเหตุที่อาจทำให้ระบบพัง (Root Cause) | วิธีป้องกันและแก้ไขที่ต้องปฏิบัติ (Strict Prevention) |
| :--- | :--- | :--- |
| **Schema Mismatch on Stock Movements** | ตาราง `stock_movements` ไม่มีคอลัมน์ `ref_invoice_no` หากส่งไป PostgREST จะส่ง Error PGRST204 ทันที | ผนวกเลขที่เอกสารเข้าในคอลัมน์ `note` แทน และใช้ `resilientInsert` ซึ่งมี auto-strip missing column |
| **ResilientUpdate Argument Inversion** | ผู้พัฒนาอาจเผลอเรียก `resilientUpdate(supabase, table, payload, id)` สลับที่กับ `id` | ส่ง `id` ก่อน `payload` เสมอตามมาตรฐาน และมีตัวช่วย Polymorphic ใน `dbHelper.js` สำรอง |
| **Alert Freezing UI State** | การสั่ง `alert()` หรือ `confirm()` จะระงับเบราว์เซอร์ทันที ทำให้ปุ่มค้างสถานะกำลังโหลด | คืนสถานะปุ่ม `disabled = false` และเปลี่ยนข้อความกลับเป็นปกติ ก่อนเรียกคำสั่ง `alert()` เสมอ |
| **Staging DOM Input Loss** | ผู้ใช้พิมพ์แก้ไขชื่อสินค้าหรือราคาในตาราง Stock-in แล้วกดยืนยันทันทีก่อน trigger event `change` | ต้องมีฟังก์ชัน `syncStagingFromDOM()` วนลูปอ่านค่าล่าสุดจากทุก Input ใน DOM เข้า Array ก่อนบันทึก |
| **Lost Barcode Focus** | แคชเชียร์สแกนบาร์โค้ดยิงสินค้า แต่เคอร์เซอร์ไม่ได้อยู่ที่ช่อง input ทำให้ตัวเลขหาย | ปฏิบัติตาม **กฎข้อที่ 1 (Barcode Focus Rule)** เสมอ โดยคืนโฟกัสด้วย `setTimeout(..., 100)` |
| **Google Drive CORS Blocker** | URL รูปภาพของ Google Drive ในรูปแบบ `drive.google.com/file/d/...` ถูกบล็อกด้วยนโยบาย CORS | แปลง URL ผ่านฟังก์ชัน `formatGoogleDriveUrl()` ใน `gdrive.js` ให้อยู่ในรูป Google CDN Direct Link เสมอ |
| **Thai Barcode Keyboard Layout** | สแกนเนอร์บาร์โค้ดยิงเป็นภาษาไทย เช่น `๘๘๕๐...` ทำให้ค้นหาสินค้าไม่พบ | กรอง Input ด้วย `normalizeThaiBarcode()` ก่อนค้นหาและบันทึกทุกครั้ง |
| **ID Type Mismatch** | ID ที่ได้จาก Supabase เป็น BigInt แต่ใน Local memory อาจเป็น String ทำให้ `find()` หรือ `===` ไม่ตรงกัน | ใช้ `safeIdCompare(a, b)` หรือ `safeNumberId(id)` จาก `dbHelper.js` เสมอ |
| **Zero-Padding on Number Inputs** | เมื่อเปิดช่องกรอกที่มีค่าเริ่มต้น `0` การพิมพ์เลข 5 เข้าไปจะกลายเป็น `50` หรือ `05` | ใช้ตรรกะ **กฎข้อที่ 8**: ตรวจจับหากค่าเดิมเป็น `0` การกดเลข 1-9 จะเข้าแทนที่เลข `0` ทันที |
| **Memory Leaks & Stale View Cache** | สลับหน้าไปมาแล้ว Listener บน window/appBus สะสมทับซ้อน (RAM บวม) หรือวิวแคชค้าง | ปฏิบัติตาม **กฎข้อที่ 9**: ทุกโมดูลต้องมี `init()` / `destroy()` และเก็บการยกเลิกใน `this.disposables` พร้อมสลับผ่าน `setActiveModule()`; แคชวิวใช้ In-Memory Map + SW และอัปเดต `APP_VERSION` ใน `js/config.js` จุดเดียว |
| **Tablet Viewport Height Overflow** | การใส่ padding หรือความสูงองค์ประกอบคงที่มากเกินไป ทำให้ปุ่มยืนยันหลุดขอบจอ 640px | ปฏิบัติตาม **กฎข้อที่ 7**: ใช้โครงสร้าง Zero-Scroll Layout และควบคุมความสูงให้ไม่เกิน 540px |
| **Virtual Keyboard Obscuring Layout** | การแตะช่อง Input บนแท็บเล็ตทำให้คีย์บอร์ดของ Android เด้งขึ้นมาบดบังหน้าจอ | ใส่ `inputmode="none"` บนช่องรับเงินสดและส่วนลด เพื่อบังคับใช้ Numpad ของแอป |
| **PromptPay Checksum Failure** | คำนวณ CRC16 ผิดสูตรทำให้แอปธนาคารสแกนจ่ายเงินไม่ผ่าน | ใช้ CRC16-CCITT (0xFFFF) 4 หลักพิมพ์ใหญ่ ตามมาตรฐาน EMVCo QR เสมอ |
| **Hardware Scanner Keystroke Flood** | ปืนสแกนยิงตัวอักษรเข้ามาเร็วมาก หากดักจับ `input` event ทีละตัวจะทำให้หน้าเว็บกระตุก | ดักจับ Capture-Phase `keydown` ลง In-Memory Buffer พร้อมใส่ `e.preventDefault()` ป้องกัน DOM Repaint Lag |
| **Bluetooth Standby Reconnection Jitter** | หัวอ่านไร้สายหลับ (Standby) พอตื่นมายิง 1-3 ตัวแรกเกิด Latency Spike (> 60ms) ทำให้อักษรนำหน้าหาย | ใช้ **Adaptive Cadence (Dual-Threshold)**: ให้ Grace Period 180ms ช่วง 1-3 ตัวแรก และ 55ms สำหรับตัวถัดไป |
| **Erroneous / Partial Barcode Scan** | สแกนเนอร์อ่านบาร์โค้ดหลุด (ส่งมาไม่ครบ 8-13 หลัก) หรือสแกนโดนรหัสผิด | โมดัล `barcode-not-found-modal` แสดงแถบเตือนบาร์โค้ดสั้นผิดปกติ พร้อมปุ่ม `❌ สแกนผิดพลาด / ยกเลิก [N]` เพื่อล้าง Buffer และคืนโฟกัสทันที |
| **Navigation Race Condition** | ผู้ใช้กดเปลี่ยนหน้าขณะที่ระบบกำลัง fetch ข้อมูลแคตตาล็อกเริ่มต้น | ล็อกแถบเมนูด้วย `nav-loading-locked` และตัวแปร `window.setAppLoading()` จนกว่าจะโหลดเสร็จ |

---

## 6. แนวทางการเขียนโค้ดและขยายโมดูล (Coding Conventions & Best Practices)

1. **สถาปัตยกรรมแบบแยกส่วนและวงจรชีวิต (Modular Architecture & Lifecycle):**
   - ส่วนติดต่อผู้ใช้อยู่ใน `views/*.html`
   - โมดูลตรรกะหลักอยู่ใน `js/modules/*.js` (Export เป็น Class เช่น `PosModule`, `ProductsModule`)
   - เครื่องมือช่วยเหลือทั่วไปอยู่ใน `js/utils/*.js`
   - ใช้ Standard ES Modules (`import` / `export`) หลีกเลี่ยงตัวแปร Global นอกเหนือจากตัวแปรจำเป็นอย่าง `window.supabase`
   - **ข้อบังคับวงจรชีวิตโมดูล:** ทุกโมดูลหน้าจอต้องมี `this.disposables = []`, `async init()`, และ `destroy()` เพื่อปลดเปลื้อง Event Listener และ Resource เมื่อผู้ใช้สลับหน้า ป้องกัน Memory Leak 100%
2. **ระบบเสียงตอบสนอง (Audio Feedback):**
   - ใช้ `playAudio('coin')` เมื่อจบการขายสำเร็จ หรือบันทึกข้อมูลเรียบร้อย
   - ใช้ `playAudio('beep')` เมื่อยิงบาร์โค้ดสำเร็จ หรือกดปุ่มแป้นตัวเลข
   - ใช้ `playAudio('error')` เมื่อเกิดข้อผิดพลาดในการทำงาน หรือรับเงินสดมาไม่พอ
   - ใช้ `playAudio('hit')` เมื่อลบสินค้าออกจากตะกร้า
3. **การออกแบบและ Touch Ergonomics สไตล์ Retro Arcade:**
   - คงเอกลักษณ์ดีไซน์พิกเซล ขอบมนแบบ Retro 8-bit/16-bit
   - ขนาด Touch Target ของปุ่มกดบนแท็บเล็ตต้องไม่ต่ำกว่า **44 × 44 px**
   - กำหนด `touch-action: manipulation` บนปุ่มเพื่อตัดความหน่วง 300ms ของเบราว์เซอร์สัมผัส
   - ป้องกันการคลุมข้อความโดยไม่ตั้งใจด้วย `user-select: none` ยกเว้นในช่อง Input
4. **ความปลอดภัยและสิ่งแวดล้อม (Security & Environments):**
   - ระบบรันบนพอร์ต **3000** เป็นมาตรฐาน (`http://localhost:3000`)
   - **ห้าม** เผยแพร่ API Key, Token หรือ Service Role Key ลงในโค้ดหรือเอกสาร Markdown (ปฏิบัติตามกฎข้อที่ 10)
   - การบันทึกและแก้ไขข้อมูลร้านค้าต้องผ่านหน้าจอ Settings และจัดเก็บลงตาราง `app_settings` พร้อมทำ Security Masking เสมอ

---

*เอกสารฉบับนี้จัดทำขึ้นเพื่อเป็นมาตรฐานสูงสุดสำหรับการพัฒนา พัฒนาต่อยอด และการทำงานร่วมกันระหว่างผู้พัฒนาและ AI Agents ในโปรเจกต์ Pixel POS & AI Inventory* 🕹️🚀
