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

> ⚠️ **คำเตือนสำหรับ AI Agents และผู้พัฒนาทุกคน:** กฎทั้ง 12 ข้อด้านล่างนี้เป็นข้อบังคับเด็ดขาด (Mandatory Rules) ห้ามละเมิดเด็ดขาดไม่ว่าในกรณีใดๆ

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
  3. ตรวจจับคอลัมน์ที่ไม่ตรงกับ Schema ของฐานข้อมูล (`PGRST204` / `extractMissingColumn`) ด้วย `while` loop แล้วตัดฟิลด์นั้นทิ้งพร้อมสั่ง Retry อัตโนมัติทันทีจนกว่าจะสำเร็จ (รองรับกรณีคอลัมน์เกินหลายตัวพร้อมกัน)
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
> **Mandate:** ปืนยิงบาร์โค้ดฮาร์ดแวร์ทำงานเสมือนการพิมพ์แป้นพิมพ์ความเร็วสูง (Keyboard Wedge) ระบบต้องดักจับข้อมูลอย่างแม่นยำ ป้องกันปัญหาภาษาไทยทั้งผังเกษมณีและผังปัตตโชติ (โดยเฉพาะแท็บเล็ต Alldocube OS ที่บังคับผังปัตตโชติ) ป้องกันอาการกระตุกของบลูทูธ (Bluetooth Jitter) และตัดการพิมพ์ด้วยมือของมนุษย์:
1. **In-Memory Capture ผ่าน Physical Keys (`e.code`) (เงื่อนไขที่ 1):**
   - ดักจับเหตุการณ์ที่ Capture Phase (`window.addEventListener('keydown', ..., true)`)
   - สกัดตัวเลขจาก `e.code.startsWith('Digit')` และ `e.code.startsWith('Numpad')` หรือใช้ `getCharFromPhysicalEvent(e)`
   - รองรับ Physical Key `Equal` (`e.code === 'Equal'`) หรือ `e.key === '='` แปลงเป็นตัวเลข `'1'` ทันที เพื่อแก้ปัญหาฮาร์ดแวร์สแกนเนอร์พ่นเครื่องหมาย `=` แทนเลข 1
   - เมื่อโฟกัสอยู่ที่ช่องบาร์โค้ด (`#pos-barcode-input`) ให้สั่ง `e.preventDefault()` ทันที เพื่อป้องกันไม่ให้อักขระไทยของระบบปฏิบัติการเข้าไปใน DOM แล้วนำค่าตัวเลขจาก `barcodeBuffer` ไปอัปเดตลง `.value` โดยตรง ป้องกัน DOM Repaint Lag
2. **ระบบความเร็วปรับตัวได้ (Adaptive Cadence Dual-Threshold) (คงเงื่อนไขฮาร์ดแวร์ไว้):**
   - หัวอ่านบาร์โค้ดบลูทูธไร้สายเมื่อหลับ (Standby/Sleep) แล้วตื่นขึ้นมายิง 1–3 ตัวแรก จะเกิด Latency Spike จากการ Reconnect Handshake (อาจหน่วง 80–150ms)
   - ปืนสแกนเนอร์ที่ตั้งค่าเป็น Low Transfer Rate หรือการยิงบาร์โค้ดยาว 12–14+ หลัก ต้องขยายช่วง Cadence เพื่อป้องกันการล้างบัฟเฟอร์ทิ้งกลางคัน
   - **ข้อบังคับ:** ใช้ Dual-Threshold สำหรับโหมดฮาร์ดแวร์สแกนเนอร์:
     ```javascript
     const CADENCE_FAST = 120;     // เกณฑ์ความเร็วส่งข้อมูลโหมด Low Transfer Rate (100ms - 120ms)
     const CADENCE_WAKEUP = 200;   // Grace Period สำหรับ 1-3 ตัวแรกช่วงตื่น (180ms - 220ms)
     const allowedCadence = (barcodeBuffer.length <= 3) ? CADENCE_WAKEUP : CADENCE_FAST;
     if (timeDiff > allowedCadence) barcodeBuffer = '';
     ```
   - ป้องกันอาการเลขตัวหน้านำหลุดหาย (Dropped Leading Digits) และป้องกันบัฟเฟอร์หลุดกลางคันเมื่อยิงบาร์โค้ดยาว 12–14 หลัก พร้อมตัดการพิมพ์มือของมนุษย์ (> 250ms) ทิ้งอย่างเด็ดขาด
3. **การประมวลผลเมื่อกด Enter และการรับมือสแกนผิดพลาด:**
   - เมื่อพบ `Enter` และความยาว Buffer $\ge 8$ หลัก ให้สั่ง `preventDefault()`, `stopPropagation()` (และ `stopImmediatePropagation()`) ล้างตัวแปรบัฟเฟอร์ และส่งเข้า `handleBarcodeScan(scannedCode)` ทันที
   - หากพบ `Enter` แต่ Buffer มีความยาว $< 8$ หลัก ให้ล้างบัฟเฟอร์ทิ้งทันทีเพื่อป้องกันข้อมูลไม่สมบูรณ์ตกค้างในรอบถัดไป
   - หากไม่พบสินค้า ให้เปิด `barcode-not-found-modal` ซึ่งมีป้ายตรวจจับความยาวบาร์โค้ด ($< 8$ หลัก) แจ้งเตือนกรณีสัญญาณหลุดสแกนไม่ครบ
   - ปุ่ม **`❌ สแกนผิดพลาด / ยกเลิก [N]`** ให้ระบบเข้าใจว่าเป็นการสแกนที่ผิดพลาด ล้าง buffer ในระบบ และคืนโฟกัสกลับไปที่ `#pos-barcode-input` ทันทีตามกฎข้อที่ 1
4. **การเปลี่ยนคีย์ตัวอักษรไทย ปัตตโชติ-ENG โดยอ้างอิงตรงจาก Reference 4 ชุด (เงื่อนไขที่ 2):**
   - เลิกการใช้เงื่อนไขซับซ้อน (ตัด Signature Scoring, Layout Guessing ออกทั้งหมด)
   - แปลงแบบตรงตัว 1-to-1 (`THAI_PATTACHOTE_REF_MAP`) ตามตัวอย่างจริงจากเครื่องสแกน:
     - `=` ➔ `1` (ทุกจุดที่พ่นเครื่องหมาย `=` ออกมาแทนเลข 1)
     - `ู` ➔ `6` (แถว Shift+6 ผังปัตตโชติ)
     - `๐-๙` ➔ `0-9`
     - พร้อม Fallback ปัตตโชติ unshifted (`ุ` ➔ 1, `ี` ➔ 4, `ึ` ➔ 5, `ั` ➔ 6, `่` ➔ 7, `า` ➔ 8, `ส` ➔ 9, `ว` ➔ 0) และเกษมณี
   - ทุกจุดที่รับ Input บาร์โค้ดแบบ Manual หรือ Paste รวมถึงช่องค้นหาสินค้า ต้องผ่านฟังก์ชัน:
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

### 📌 กฎข้อที่ 7: กฎการออกแบบและปรับแต่ง Layout หน้าขาย POS & Payment ยืดหยุ่นเพื่อ UX/UI (Flexible POS & Payment Layout Mandate)
> **Mandate:** โครงสร้างและ Layout ของหน้าขายหน้าร้าน (`views/pos.html`) และหน้าสรุปบิลชำระเงิน (`views/payment.html`) **อนุญาตและรองรับการปรับเปลี่ยน Layout ได้อย่างยืดหยุ่นเมื่อต้องการปรับปรุง UX/UI หรือรองรับฟีเจอร์ใหม่** (ยกเลิกข้อจำกัดห้ามเปลี่ยน Layout เด็ดขาดเดิม) เพื่อให้ตอบโจทย์การใช้งานจริงและรองรับการพัฒนาต่อยอดได้อย่างคล่องตัว:
- **หลักการปรับเปลี่ยน Layout เพื่อ UX/UI (UX/UI Adaptive Principle):**
  1. **อนุญาตปรับ Layout และขนาดเพื่อความสะดวกในการใช้งาน:** สามารถปรับเปลี่ยนโครงสร้าง, ความสูง, ขนาดปุ่มสัมผัส (เช่น เพิ่มความสูงแถบหมวดหมู่ `#pos-category-tabs-container` 80% ให้กดง่ายขึ้นบนจอสัมผัส), ระยะห่าง, การจัดวางคอลัมน์ หรือ Scrollbar เพื่อให้แคชเชียร์กดใช้งานได้สะดวกสบายยิ่งขึ้น (Touch-Friendly)
  2. **รองรับฟีเจอร์ใหม่ในอนาคต:** Layout หน้า POS และ Payment ต้องเปิดกว้างต่อการต่อยอด ปรับขนาดพื้นที่ หรือเพิ่ม Component ใหม่ๆ ได้อย่างราบรื่น ไม่ถูกล็อกโครงสร้างแบบตายตัว
  3. **คงความสะดวกและความรวดเร็วในการคิดเงิน (Fast Checkout):** ปุ่มสำคัญ เช่น ปุ่มคิดเงิน/ปิดบิล ยังคงต้องเข้าถึงและมองเห็นได้ง่าย และองค์ประกอบหลักทำงานร่วมกันได้อย่างสมดุลทั้งบนแท็บเล็ตและอุปกรณ์อื่นๆ
- **ข้อกำหนดสำหรับหน้าจออื่นๆ ทั้งหมด (Dashboard, Orders, Products, Categories, Stock-In, Settings):**
  - **ห้ามใส่ `overflow-hidden` ที่ตัดข้อมูลทิ้งเด็ดขาด:** ทุกหน้าจอที่ไม่ใช่ POS/Payment (เช่น `views/dashboard.html`, `views/orders.html`, `views/products.html`) **จะต้องรองรับการ Scroll ระดับหน้าจอ (`overflow-y-auto` พร้อม padding ล่าง `pb-16`)** เพื่อให้ผู้ใช้สามารถเลื่อนดูข้อมูล กราฟสถิติ และการตั้งค่าต่างๆ ได้ครบถ้วนทั้งบนแท็บเล็ต แล็ปท็อป และสมาร์ตโฟน
  - **การจัดการข้อมูลที่ยาวเป็นพิเศษ (Dedicated Internal Scrollbox):** หากมีตารางหรือลิสต์ข้อมูลที่มีความยาวมาก (เช่น ตารางสินค้าในแคตตาล็อก, ตาราง Leaderboard อันดับสินค้า, ตารางแจ้งเตือนสต็อก) ให้สร้างกล่อง Scrollbox ภายใน (`max-h-[...] overflow-y-auto`) พร้อมแถวหัวตารางตรึงอยู่กับที่ (`sticky top-0 z-10`) ควบคู่กับการเลื่อนหน้าหลัก เพื่อให้อ่านข้อมูลได้อย่างเป็นระเบียบและไม่ดันองค์ประกอบอื่นจนบิดเบี้ยว

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
- **ข้อบังคับการปรับเวอร์ชัน (Cache Invalidation):** ห้ามแก้ไขเลขเวอร์ชันใน `js/config.js` หรือ `sw.js` ด้วยมือโดยเด็ดขาด ทุกครั้งที่มีการแก้ไขไฟล์ HTML, CSS, หรือ JS แม้เพียงตัวอักษรเดียว **ต้องรันคำสั่ง `npm run bump` ผ่าน Terminal ทุกครั้ง** (สคริปต์จะทำการอัปเดตทั้ง `APP_VERSION` ใน `js/config.js` และ `CACHE_NAME` ใน `sw.js` ให้ตรงกันแบบ Atomic เพื่อทำลาย Cache เก่าอย่างสมบูรณ์แบบ)
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

### 📌 กฎข้อที่ 11: กฎการป้องกันการประกาศเมธอดซ้ำซ้อนและการแยกการผูก Event (Method Overwrite Prevention & Static DOM Event Guard Rule)
> **Mandate:** ใน JavaScript ES6 Classes การประกาศเมธอดชื่อซ้ำกันภายในคลาสเดียวกัน (เช่น `async init()`) จะทำให้ตัวที่อยู่ลำดับหลังเขียนทับ (Overwrite) ตัวก่อนหน้าบน Prototype โดยสมบูรณ์โดยไม่มี Error แจ้งเตือน และการผูก DOM Event ซ้ำๆ ใน SPA จะทำให้เกิด Memory Leak และฟังก์ชันทำงานเบิ้ลหลายรอบ
1. **ห้ามมีเมธอดชื่อซ้ำในคลาสเดียวกันเด็ดขาด (Strict Single Method Declaration):**
   - ก่อนบันทึกไฟล์หรือ refactor โค้ด ต้องตรวจสอบว่าไม่มีการประกาศเมธอดเดิมซ้ำสองจุด เช่น การทิ้ง `async init() { await this.loadInitialData(); }` ไว้หลังฟังก์ชัน `init()` หลัก ซึ่งจะทำให้ `bindEvents()`, `initElements()` และการทำงานเริ่มต้นทั้งหมดถูกลบล้างทิ้ง
2. **การแยกประเภท Event ระหว่าง Static DOM และ Dynamic Lifecycle (Dual-Tier Event Binding):**
   - **Static DOM Events:** ปุ่มกด, ฟอร์ม submit, input, dropzone, หัวตาราง sort ที่อยู่ใน HTML ของวิว ให้แยกไว้ในเมธอด `bindDOMEvents()` และครอบด้วยตัวแปรป้องกัน `if (this._domEventsBound) return; this._domEventsBound = true;` เพื่อให้ผูก Event เพียงครั้งเดียวตลอดอายุแอป ไม่เกิดการผูกซ้อนเมื่อผู้ใช้สลับหน้าแท็บไปมา
   - **Lifecycle Events:** การดักฟัง `appBus.on(...)` หรือ `window.addEventListener('keydown', ...)` ให้แยกไว้ใน `bindLifecycleEvents()` และต้องเก็บฟังก์ชัน Unsubscribe ลงใน `this.disposables` เสมอ เพื่อให้เมธอด `destroy()` สามารถปลดออกได้อย่างสะอาดหมดจดเมื่อสลับไปหน้าอื่น

---

### 📌 กฎข้อที่ 12: กฎระบบเสียงพูดภาษาไทยแจ้งยอดเงินและทอนเงินอัตโนมัติ (Thai Voice Announcement & Web Audio Chaining Rule)
> **Mandate:** ระบบเสียงพูดภาษาไทยต้องทำงานแบบ Non-blocking ไร้รอยต่อ (Gapless Playback) ถูกต้องตามหลักไวยากรณ์ภาษาไทย และไม่กระทบการทำงานหลักของแคชเชียร์:
1. **การทำงานร่วมกับเสียง SFX เดิม (Co-existence):**
   - เมื่อชำระเงินสำเร็จ ให้เล่นเสียงสังเคราะห์ 8-bit `stage_clear` สั้นๆ ก่อน แล้วหน่วงเวลา ~0.2 วินาที (200ms) จึงเริ่มเล่นเสียงพูดภาษาไทย เพื่อคงเสน่ห์เกมเรโทรควบคู่กับความสะดวกในการฟัง
2. **การแปลงตัวเลขและไวยากรณ์ภาษาไทย (`numberToThaiAudioList`):**
   - **คำว่า "เอ็ด" (`num_11.wav`):** ต้องใช้เมื่อเลข 1 อยู่ในหลักหน่วยและมีหลักที่มากกว่าสิบอยู่ข้างหน้า (เช่น 11, 21, 31, 101, 1001, 20001) แต่หากเป็นเลข 1 เดี่ยวๆ ให้ใช้ "หนึ่ง" (`num_1.wav`)
   - **คำว่า "ยี่สิบ" (`num_20.wav`) และ "สิบ" (`num_10.wav`):** ต้องแปลงตัวเลข 20 เป็น `num_20.wav` และ 10 เป็น `num_10.wav` โดยไม่นำ `num_1` หรือ `num_2` มาต่อหน้าคำว่าสิบ
   - **เศษสตางค์ (`unit_satang.wav`):** หากยอดเงินมีเศษทศนิยมมากกว่า 0 (เช่น 25.50 บาท) ให้อ่านส่วนสตางค์ตามจริงต่อท้ายส่วนบาทเสมอ เช่น `[25] + unit_baht.wav + [50] + unit_satang.wav`
3. **โครงสร้างคิวประโยคตามประเภทการชำระเงิน (`speakCheckout`):**
   - **เงินสดพอดี (Exact Cash):** `['prefix_all.wav', ...totalList, 'unit_baht.wav', ...satangIfAny, 'pay_cash_exact.wav', 'thank_you.wav']`
   - **QR Code / พร้อมเพย์ / โอนเงิน:** `['prefix_all.wav', ...totalList, 'unit_baht.wav', ...satangIfAny, 'pay_qr.wav', 'thank_you.wav']`
   - **เงินสดมีเงินทอน (Cash with Change):** `['prefix_all.wav', ...totalList, 'unit_baht.wav', ...satangIfAny, 'pay_cash.wav', ...cashList, 'unit_baht.wav', 'change.wav', ...changeList, 'unit_baht.wav', ...changeSatangIfAny, 'thank_you.wav']`
   - **ร่วมจ่าย (Split: สด + QR):** `['prefix_all.wav', ...totalList, 'unit_baht.wav', ...satangIfAny, 'pay_cash.wav', ...cashList, 'unit_baht.wav', 'pay_qr.wav', 'thank_you.wav']`
4. **ความต่อเนื่องของเสียงและการหยุดคิว (Gapless Web Audio Engine & Cancellation):**
   - ต้อง Preload และแคชไฟล์เสียงลงใน `Map<string, AudioBuffer>` เพื่อหลีกเลี่ยงความหน่วงของเครือข่าย
   - เชื่อมต่อเสียงด้วย `AudioContext.currentTime` แบบ Micro-crossfade ต่อเนื่องไร้รอยต่อ ป้องกันเสียงขาดช่วง (Audio Jitter)
   - หากมีการเปิดบิลใหม่ หรือมีเสียงพูดใหม่แทรกเข้ามา ต้องเรียก `stopVoice()` หยุดคิวเดิมทันที
   - เคารพสถานะ Mute จาก `isAudioMuted()` และการเปิด/ปิดเสียงพูดจาก `isVoiceEnabled()` (`pixel_pos_voice_enabled`)

---

---

### 📌 กฎข้อที่ 13: กฎระบบสั่งเพิ่มสินค้าด้วยเสียงพูดภาษาไทย (Thai Voice-to-Cart Engine & PWA Permission Rule)
> **Mandate:** ระบบสั่งเพิ่มสินค้าลงตะกร้าด้วยเสียงพูดภาษาไทย (Web Speech API) ต้องมีความทนทาน ทำงานร่วมกับสภาพแวดล้อม PWA/HTTPS อย่างถูกต้อง มีสัญญาณเสียงไกด์ สัญญาณภาพที่ชัดเจน และมีตรรกะการจับคู่สินค้าที่ชาญฉลาด:
1. **การขอสิทธิ์ไมโครโฟนบน PWA / Secure Context:**
   - Web Speech API บน Android/Chromium ต้องทำงานบน Secure Context (`https://` หรือ `http://localhost`) เสมอ
   - ก่อนเรียก `recognition.start()` ต้องเรียก `ensureMicPermission()` ผ่าน `navigator.mediaDevices.getUserMedia({ audio: true })` เพื่อกระตุ้นให้ Android แสดง System Dialog ขออนุญาตไมโครโฟน และคืนทรัพยากรทันทีพร้อมหน่วงเวลา ~80ms ให้ฮาร์ดแวร์เสียงของแท็บเล็ตพร้อมใช้งาน ป้องกัน `audio-capture error`
2. **สัญญาณเสียงไกด์ "ติ๊ด!" (Acoustic Cue Beep):**
   - เมื่อไมโครโฟนเริ่มรับเสียงจริง (`recognition.onstart`) ให้ส่งเสียงสังเคราะห์ 8-bit `playVoiceGuideBeep()` (1046.5Hz ➔ 1174.6Hz) ทันที เพื่อให้แคชเชียร์ทราบจังหวะเริ่มพูดอย่างแม่นยำ
3. **ระบบการแสดงผลสองระดับ (Two-Tier Visualizer & Sound Wave Animation):**
   - ปุ่มไมค์ 2 แถวด้านซ้าย (`.pixel-btn-mic-tall`) ต้องเปลี่ยนเป็นสีแดงสดเรโทรพร้อมประกาย CRT Glow (`retro-mic-pulse`), แสดงแถบคลื่นเสียง Equalizer 4 แท่ง (`.voice-sound-waves`), และข้อความ `● กำลังฟัง`
   - แบนเนอร์ด้านบนแคตตาล็อกสินค้า (`#pos-voice-banner`) ต้องแสดงสถานะพร้อมไฟกะพริบ (`animate-ping`) และสตรีมข้อความสด (Interim Transcript) ขณะกำลังพูดแบบเรียลไทม์ พร้อมปุ่ม `✕ ยกเลิก` (และรองรับการกดยกเลิกผ่านปุ่ม `Esc` หรือ `F3`)
4. **ตรรกะการวิเคราะห์คำสั่งเสียงและราคาสินค้า (`parseThaiVoiceCommand`):**
   - หากพูดชื่อสินค้าอย่างเดียว หรือพูดชื่อสินค้าพร้อมราคา (เช่น *"โค้ก 20 บาท"*) ให้ถือว่าจำนวนสินค้าคือ **1 หน่วย** เสมอ
   - แยกแยะคำระบุราคา (เช่น *"20 บาท"*, *"ยี่สิบบาท"*) ออกจากจำนวน และใช้ราคานี้เป็นตัวจับคู่สินค้าที่มีระดับราคาใกล้เคียงที่สุด (เช่น *"โค้ก 20 บาท"* จับคู่กับ *"โค้ก 590 มล."* ราคา 19 บาท)
5. **การคืนโฟกัสตามกฎข้อที่ 1:**
   - เมื่อเสร็จสิ้นกระบวนการสั่งด้วยเสียง (ทั้งกรณีสำเร็จ, ยกเลิก หรือ Error) จะต้องส่งโฟกัสกลับไปที่ `#pos-barcode-input` ด้วย `this.focusBarcodeInput(100)` เสมอ

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
│   │   themeHelper.js    uiHelper.js          sync.js          clipboard.js   │   │
│   └────────────────────────┬──────────────────────────────────┬──────────────┘   │
│                            │                                  │                  │
│             IndexedDB Sync │                                  │ REST API / SDK   │
│                            ▼                                  │                  │
│               Dexie.js (RetroArcadePosDB)                     │                  │
│               - products / categories                         │                  │
│               - offline_orders / settings                     │                  │
│   └────────────────────────┼──────────────────────────────────┼──────────────────┘
│                            │                                  │
│                            ▼                                  ▼
│     ┌───────────────────────────────┐           ┌────────────────────────────┐
│     │     SUPABASE (POSTGRESQL)     │           │   EXTERNAL CLOUD BACKEND   │
│     │                               │           │                            │
│     │  - products     - orders      │           │  - Google Gemini API       │
│     │  - categories   - sale_items  │           │    (Vision OCR Staging)    │
│     │  - app_settings - movements   │           │  - Google Apps Script      │
│     │  - Row Level Security (RLS)   │           │    (Drive Image Pipeline)  │
│     └───────────────────────────────┘           └────────────────────────────┘
```

### 3.1 สถาปัตยกรรม Front-end View Loader, In-Memory Caching & Versioning
- **Single Page Application (SPA):** ไฟล์ `index.html` ทำหน้าที่เป็น Core Shell โหลดไลบรารีพื้นฐาน แสดง Header และควบคุมการสลับหน้าในคอนเทนเนอร์ `#app-viewport`
- **Dynamic View Partial Loading (`viewLoader.js`):** เมื่อมีการสลับหน้า ระบบจะ fetch ไฟล์ HTML จากโฟลเดอร์ `views/` มาฝังลงใน `#app-viewport`
- **In-Memory View Cache (Map):** เก็บข้อมูล Partial HTML views ในหน่วยความจำ RAM ของเบราว์เซอร์ (`const viewCache = new Map()`) ทำให้การสลับหน้าเร็วระดับ sub-millisecond ในช่วง Runtime โดย **ไม่เปลืองโควตา 5MB ของ `localStorage`** (ตัดปัญหา QuotaExceededError 100%)
- **Service Worker Offline Persistence (`sw.js`):** แคชไฟล์ HTML แบบ Cache-First ผ่าน Service Worker โดยใช้ `{ ignoreSearch: true }` ทำให้ทำงานออฟไลน์ได้สมบูรณ์และตัดปัญหา Cache-busting query parameter ไม่ตรงกับแคช
- **Legacy Cache Auto-Purge (`purgeLegacyViewCache()`):** ทำการสแกนและลบคีย์แคชเก่า `pixel_view_cache_*` ออกจาก `localStorage` ทันทีที่ระบบบูต เพื่อคืนพื้นที่ให้เบราว์เซอร์
- **Centralized Versioning (`APP_VERSION`):** กำหนดเลขเวอร์ชันที่ศูนย์กลางเดียวใน `js/config.js` (`export const APP_VERSION = '1.2.091918';`) โดย `viewLoader.js` จะดึงค่านี้ไปแนบ query parameter `?v=${APP_VERSION}` อัตโนมัติ ป้องกันปัญหาเบราว์เซอร์แคชไฟล์เก่าค้าง (อัปเดตอัตโนมัติผ่านคำสั่ง `npm run bump`)
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
| **Thai Barcode Keyboard Layout (Kedmanee & Pattachote)** | สแกนเนอร์บาร์โค้ดยิงเป็นภาษาไทย เช่น `๘๘๕๐...`, เกษมณี (`คคถ...`), หรือปัตตโชติ (`าาึ...` บน Alldocube OS) | กรอง Input ด้วย `normalizeThaiBarcode()` ที่มี dual-layout detection ก่อนค้นหาและบันทึกทุกครั้ง |
| **ID Type Mismatch** | ID ที่ได้จาก Supabase เป็น BigInt แต่ใน Local memory อาจเป็น String ทำให้ `find()` หรือ `===` ไม่ตรงกัน | ใช้ `safeIdCompare(a, b)` หรือ `safeNumberId(id)` จาก `dbHelper.js` เสมอ |
| **Zero-Padding on Number Inputs** | เมื่อเปิดช่องกรอกที่มีค่าเริ่มต้น `0` การพิมพ์เลข 5 เข้าไปจะกลายเป็น `50` หรือ `05` | ใช้ตรรกะ **กฎข้อที่ 8**: ตรวจจับหากค่าเดิมเป็น `0` การกดเลข 1-9 จะเข้าแทนที่เลข `0` ทันที |
| **Memory Leaks & Stale View Cache** | สลับหน้าไปมาแล้ว Listener บน window/appBus สะสมทับซ้อน (RAM บวม) หรือวิวแคชค้าง | ปฏิบัติตาม **กฎข้อที่ 9**: ทุกโมดูลต้องมี `init()` / `destroy()` และเก็บการยกเลิกใน `this.disposables` พร้อมสลับผ่าน `setActiveModule()`; แคชวิวใช้ In-Memory Map + SW และอัปเดต `APP_VERSION` ใน `js/config.js` จุดเดียว |
| **Tablet Viewport Height Overflow** | การใส่ padding หรือความสูงองค์ประกอบคงที่มากเกินไป ทำให้ปุ่มยืนยันหลุดขอบจอ 640px | ปฏิบัติตาม **กฎข้อที่ 7**: ใช้โครงสร้าง Zero-Scroll Layout และควบคุมความสูงให้ไม่เกิน 540px |
| **Virtual Keyboard Obscuring Layout** | การแตะช่อง Input บนแท็บเล็ตทำให้คีย์บอร์ดของ Android เด้งขึ้นมาบดบังหน้าจอ | ใส่ `inputmode="none"` บนช่องรับเงินสดและส่วนลด เพื่อบังคับใช้ Numpad ของแอป |
| **PromptPay Checksum Failure** | คำนวณ CRC16 ผิดสูตรทำให้แอปธนาคารสแกนจ่ายเงินไม่ผ่าน | ใช้ CRC16-CCITT (0xFFFF) 4 หลักพิมพ์ใหญ่ ตามมาตรฐาน EMVCo QR เสมอ |
| **Hardware Scanner Keystroke Flood** | ปืนสแกนยิงตัวอักษรเข้ามาเร็วมาก หากดักจับ `input` event ทีละตัวจะทำให้หน้าเว็บกระตุก | ดักจับ Capture-Phase `keydown` ลง In-Memory Buffer พร้อมใส่ `e.preventDefault()` ป้องกัน DOM Repaint Lag และอัปเดต `.value` ตรง |
| **Bluetooth Standby & Low Rate Jitter** | หัวอ่านไร้สายหลับ (Standby) พอตื่นมายิง 1-3 ตัวแรกเกิด Latency Spike หรือโหมด Low Rate ยิงยาว 12-14 หลักแล้วบัฟเฟอร์หลุด | ใช้ **Adaptive Cadence (Dual-Threshold)**: ให้ Grace Period 200ms (180-220ms) ช่วง 1-3 ตัวแรก และ 120ms (100-120ms) สำหรับตัวถัดไป |
| **Erroneous / Partial Barcode Scan** | สแกนเนอร์อ่านบาร์โค้ดหลุด (ส่งมาไม่ครบ 8-13 หลัก) หรือสแกนโดนรหัสผิด | โมดัล `barcode-not-found-modal` แสดงแถบเตือนบาร์โค้ดสั้นผิดปกติ พร้อมปุ่ม `❌ สแกนผิดพลาด / ยกเลิก [N]` เพื่อล้าง Buffer และคืนโฟกัสทันที |
| **Navigation Race Condition** | ผู้ใช้กดเปลี่ยนหน้าขณะที่ระบบกำลัง fetch ข้อมูลแคตตาล็อกเริ่มต้น | ล็อกแถบเมนูด้วย `nav-loading-locked` และตัวแปร `window.setAppLoading()` จนกว่าจะโหลดเสร็จ |
| **Method Overwrite & Inactive Listeners in Classes** | มีการประกาศเมธอดชื่อซ้ำกันในคลาส (เช่น `async init()`) ทำให้เมธอดหลังทับเมธอดแรกบน Prototype เงียบๆ ส่งผลให้ปุ่มและตัวกรองไม่ทำงาน หรือการผูก DOM Event ซ้ำหลายรอบเมื่อสลับหน้า | ปฏิบัติตาม **กฎข้อที่ 11**: ห้ามประกาศเมธอดชื่อซ้ำในคลาสเดียวกันเด็ดขาด, แยก Static DOM Events และครอบด้วย `this._domEventsBound`, และแยก Lifecycle Events ลง `this.disposables` เพื่อล้างใน `destroy()` |
| **Media Query Padding Injection on Pixel-Box Cards** | `@media (max-width: 1024px)` และ `(max-width: 640px)` กำหนด `padding: 0.625rem / 0.5rem` ให้ `.pixel-box` ทำให้การ์ดที่มีโครงสร้าง Edge-to-Edge (เช่น `.pos-cart-card` ที่มีรูปชิดซ้ายและปุ่มลบชิดขวา) เกิดช่องว่างสีขาวลอยรอบตัวบน Android PWA/Chrome หรือเวลาย่อหน้าต่าง PC | กำหนด `padding: 0 !important;` บน `.pos-cart-card` และ `#pos-cart-items .pixel-box` ครอบคลุมทั้งใน Base CSS, ภายใน Media Query ทุกระดับ และใส่ inline style `style="padding: 0 !important;"` บนมาร์กอัป |
| **Clean Satang-less Currency Display** | สินค้าส่วนใหญ่ไม่มีเศษสตางค์ การแสดง `.00` ตลอดเวลาทำให้เปลืองพื้นที่แนวนอนบนหน้าจอแท็บเล็ตและตัวเลขดูรกรุงรัง | ใช้ฟังก์ชัน `formatCleanPrice(num)`: หากไม่มีเศษสตางค์ (`val % 1 === 0`) ให้แสดงเฉพาะจำนวนเต็ม เช่น `@10 / ขวด` และ `฿10` หากมีเศษสตางค์จริงจึงแสดงทศนิยม 2 ตำแหน่ง เช่น `@10.50 / ขวด` และ `฿10.50` |
| **POS Quick-Quantity Modal Height Overflow** | โมดัลปรับจำนวนด่วนมีความสูงมากเกินไป ทำให้ปุ่มยืนยัน [Enter] และยกเลิก [Esc] ด้านล่างหลุดขอบจอบนแท็บเล็ต 1024x640 แนวนอน | บีบขนาดความสูงรวมให้ไม่เกิน ~360px: ย่อไฟ LED (`text-2xl sm:text-3xl`), ลด Padding รวมเป็น `p-2.5`, ปรับความสูงแป้น Numpad จาก `py-2.5` เหลือ `py-1.5` และใส่ `max-h-[96vh] overflow-y-auto` ป้องกันการล้นจอ |
| **In-Memory Lookup & Barcode Bottleneck ($O(N)$ vs $O(1)$)** | การค้นหาสินค้าด้วย `Array.find()` ในทุกครั้งที่คลิกการ์ด, วาดตะกร้า หรือยิงบาร์โค้ด ทำให้แท็บเล็ตสะดุดเมื่อมีสินค้าหลักพันรายการ | สร้างดัชนีคู่ In-Memory Hash Maps (`_productById`, `_productByBarcode`, `_productMap`) ให้ค้นหาแบบ $O(1)$ ทันทีในเวลา < 0.05ms พร้อมเก็บ `Array.find` เป็น Fallback |
| **Multi-Category Filter Loop Churn ($O(K \times N)$)** | การสั่ง `products.filter()` แยกนับจำนวนสินค้าทีละหมวดหมู่ใน `renderCategories()` ทำให้เกิดลูปเปรียบเทียบซ้ำซ้อนหลักหมื่นรอบ | ปรับเป็น Single-Pass Loop นับจำนวนสินค้าทุกหมวดหมู่และยอดขายดีในรอบเดียว ($O(N)$) แล้วเก็บลง Map สำหรับดึงแสดงผลทันที |
| **Intl.Collator & Array.sort GC Churn** | การสร้าง Object `localeCompare` ใหม่ และดึงสถิติยอดขายซ้ำๆ หลายพันครั้งระหว่าง Array Sorting ทำให้เกิด GC Stutter บนแท็บเล็ต | ประกาศ `thaiCollator = new Intl.Collator('th')` ตัวเดียวที่ระดับโมดูล และคำนวณแนบสถิติยอดขาย (`times`, `quantity`) ไว้บนอ็อบเจกต์ชั่วคราวก่อนเริ่มเรียงลำดับ |
| **PWA Web Speech Insecure Context Block** | หากเข้าถึงผ่าน IP ในวง LAN (เช่น `http://192.168.x.x:3000`) Chrome จะปิดกั้น Web Speech API และ `getUserMedia` โดยไม่แสดง Dialog ขอสิทธิ์ | ต้องรันบน Secure Context (`https://` บน Vercel หรือ `localhost`) หรือเปิด `chrome://flags/#unsafely-treat-insecure-origin-as-secure` บนแท็บเล็ต |
| **Android Audio HAL Hardware Mic Locking** | เมื่อเรียก `getUserMedia` ขอสิทธิ์และสั่ง `stop()` ชิปเสียงแท็บเล็ตต้องการเวลาคลายตัว หากเรียก `recognition.start()` ทันทีจะติด `audio-capture error` | ใส่ Grace Period หน่วงเวลา 80ms (`await new Promise(r => setTimeout(r, 80))`) ให้ฮาร์ดแวร์คลายตัวก่อนเริ่มจับเสียง |
| **Voice Spoken Price vs Quantity Ambiguity** | ผู้ใช้พูด *"โค้ก 20 บาท"* อาจถูกระบบเข้าใจผิดว่าสั่ง 20 ชิ้น | กรองคำระบุราคา (`THAI_PRICE_WORDS` + regex บาท/.-/฿) แยกเป็น `targetPrice` เพื่อเทียบเคียงระดับราคาสินค้า และกำหนดค่าเริ่มต้นจำนวนเป็น 1 หน่วยหากไม่ได้ระบุจำนวน |


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
   - ใช้ `playAudio('stage_clear')` เมื่อบันทึกการขายสำเร็จในหน้า Stage Clear Modal
   - ใช้ `playAudio('beep')` เมื่อยิงบาร์โค้ดสำเร็จ หรือกดปุ่มแป้นตัวเลข
   - ใช้ `playAudio('error')` เมื่อเกิดข้อผิดพลาดในการทำงาน หรือรับเงินสดมาไม่พอ
   - ใช้ `playAudio('hit')` เมื่อลบสินค้าออกจากตะกร้า หรือล้างรายการ
   - ใช้ `playAudio('plus')` / `playAudio('minus')` เมื่อเพิ่มหรือลดจำนวนสินค้า
   - ใช้ `playAudio('select')` เมื่อกดปุ่มสลับธีม หรือเลือกตัวเลือก
   - ใช้ `playAudio('powerup')` เมื่อนำเข้าสินค้าหรือรับสต็อกสำเร็จ
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
