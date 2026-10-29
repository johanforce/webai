# SKILL: frontend-design — Giao diện web bán hàng bắt mắt, có animation

> Đặt file này tại `skills/frontend-design.md`. Dán/đính kèm khi làm các bước giao diện (C1.2, C4.x, C5.3).
> Áp dụng chính cho **trang client**. Trang admin dùng bản "điềm tĩnh" ở mục 11.
> Stack: React + Vite + TypeScript + Tailwind CSS + thư viện animation `motion`.

---

## 1. TƯ DUY THIẾT KẾ

Hãy làm như **design lead của một studio nổi tiếng vì mỗi khách hàng có một bản sắc riêng, không bị nhầm với ai khác**. Khách hàng này đã từ chối những bản thiết kế sáo rỗng, giống template. Họ trả tiền cho một góc nhìn riêng.

- Mọi lựa chọn (màu, font, bố cục, chuyển động) phải **xuất phát từ ngành hàng và khách mục tiêu** trong `brief.md`, không phải từ thói quen.
- Web bán hàng thành công khi: nhìn là muốn xem tiếp, tìm thấy món cần mua nhanh, tin tưởng, và đặt hàng không vướng. **Đẹp phải phục vụ bán hàng.**
- Chỉ **dồn sự táo bạo vào một chỗ** (thường là hero hoặc ảnh sản phẩm). Mọi thứ xung quanh giữ gọn gàng, kỷ luật. Trước khi chốt, tự hỏi: "có thứ trang trí nào không phục vụ mục tiêu bán hàng không?" rồi bỏ nó đi.

## 2. QUY TRÌNH BẮT BUỘC: LẬP KẾ HOẠCH → SOI → CODE → TỰ PHÊ BÌNH

### Bước 1. Viết kế hoạch thiết kế ngắn (trước khi code)
Trình bày cho người dùng, gồm:
- **Màu:** 4–6 màu có tên + mã hex (nền, nền phụ, chữ, màu thương hiệu, màu nhấn CTA, màu trạng thái).
- **Font:** font nào, vai trò gì (tiêu đề / nội dung / giá).
- **Bố cục:** mô tả bố cục trang chủ bằng 1–2 câu + **wireframe ASCII** cho mobile. Nêu rõ canh lề (trái/giữa).
- **Chuyển động:** liệt kê "khoảnh khắc chính" (mục 7) và các micro-interaction sẽ dùng.
- **Điểm nhớ:** 1 câu nói thứ gì làm shop này khác biệt.

### Bước 2. Soi kế hoạch với brief
Tự hỏi: "Nếu nhận một brief ngành hàng khác, mình có ra đúng kế hoạch này không?" Nếu có → nó đang là mặc định, **sửa lại** và nói rõ đã đổi gì, vì sao.

### Bước 3. Code theo kế hoạch đã duyệt.
### Bước 4. Tự phê bình: nhìn lại từng màn hình, xoá bớt một "phụ kiện" thừa (kiểu Chanel: ra khỏi nhà, soi gương, bỏ một món).

## 3. TRÁNH DẤU HIỆU "AI TẠO RA" (TEMPLATE TELLS)

Các kiểu sau là mặc định phổ biến. Chỉ dùng khi **brief yêu cầu đúng như vậy**, không dùng vì tiện:

1. Nền kem ấm (~`#F4F1EA`) + serif tương phản cao + nhấn màu đất nung (~`#D97757`).
2. Nền gần đen + một màu nhấn xanh acid hoặc đỏ cam.
3. Kiểu báo giấy: đường kẻ mảnh, bo góc 0, cột dày đặc.
4. **Bộ "thẻ SaaS"**: nội dung băm thành các card y hệt nhau, cùng một độ bo, cùng một bóng xám nhạt, thêm gradient trang trí.
5. Chi tiết "đồ dựng sẵn": nhãn IN HOA giãn chữ phía trên mỗi tiêu đề, chuỗi chấm giữa `A · B · C`, nhãn kiểu `TỪ — mảnh`, mũi tên `→` gắn sau mọi nút, font mono cho nhãn nhỏ, đen pha (`#0B0B0B`, `#111`).
6. Tiêu đề chỉ nhấn **một từ** bằng in nghiêng/đổi màu; đánh số `01 / 02 / 03` khi nội dung không thật sự là chuỗi bước.
7. Hero kiểu "một con số to + nhãn nhỏ + gradient".

Cấu trúc hiển thị (viền, đường kẻ, nhãn, số) là **thông tin**, không phải trang trí. Dùng khi nó giúp người dùng hiểu nội dung.

## 4. MÀU SẮC

- Dùng **biến CSS + token Tailwind**, không rải mã hex trong component.
- Cấu trúc token:

```css
/* src/index.css */
:root {
  --c-bg: …;          /* nền trang */
  --c-surface: …;     /* nền card/khối */
  --c-ink: …;         /* chữ chính (không dùng #000 thuần) */
  --c-ink-soft: …;    /* chữ phụ */
  --c-brand: …;       /* màu thương hiệu */
  --c-accent: …;      /* màu CTA "Thêm vào giỏ / Đặt hàng" */
  --c-sale: …;        /* giá giảm, nhãn sale */
  --c-success: …;  --c-warning: …;  --c-danger: …;
  --radius-sm: 8px; --radius-md: 14px; --radius-lg: 24px;
  --shadow-soft: 0 6px 24px -12px rgb(0 0 0 / .18);
  --shadow-lift: 0 18px 40px -16px rgb(0 0 0 / .28);
  --ease-out: cubic-bezier(.22,1,.36,1);
}
```
```js
// tailwind.config.js → theme.extend.colors
brand: 'var(--c-brand)', accent: 'var(--c-accent)', ink: 'var(--c-ink)', …
```

- Màu CTA phải **nổi nhất trang** và nhất quán (cùng một màu cho mọi hành động mua).
- Độ tương phản chữ/nền ≥ 4.5:1.
- **Gợi ý hướng màu theo tông thương hiệu** (chỉ là điểm xuất phát, phải tinh chỉnh theo brief, không copy nguyên):

| Tông | Hướng màu | Ghi chú |
|---|---|---|
| Sang trọng, tinh tế | Nền ngà lạnh/xám khói, chữ xanh mực, nhấn vàng đồng trầm | Nhiều khoảng trắng, ảnh lớn |
| Trẻ trung, năng động | Nền trắng sáng, nhấn xanh điện + cam san hô | Bo góc lớn, chuyển động nhanh |
| Gần gũi, tự nhiên | Nền xanh lá xám nhạt, chữ xanh rừng, nhấn cam đào | Ảnh ấm, bo mềm |
| Tối giản, hiện đại | Nền trắng, chữ xám than, nhấn một màu duy nhất đậm | Lưới chặt, chữ làm nhân vật chính |
| Ăn uống, tươi vui | Nền kem chanh nhạt, nhấn đỏ cà chua, xanh bơ | Ảnh món ăn là trung tâm |

## 5. CHỮ (TYPOGRAPHY)

- Chữ làm nên cá tính. Dùng **1 hoặc 2 họ font**, nếu 2 thì phải khác nhau rõ rệt (heading vs body).
- **Bắt buộc font hỗ trợ tiếng Việt** (tải subset `vietnamese`). Gợi ý đã hỗ trợ: *Be Vietnam Pro, Plus Jakarta Sans, Lexend, Montserrat, Raleway, Nunito, Quicksand, Archivo, Space Grotesk, Oswald, Playfair Display, Cormorant, Lora, Baloo 2*.
- Gợi ý cặp: *Playfair Display + Be Vietnam Pro* (sang trọng) · *Lexend + Nunito* (thân thiện) · *Archivo + Plus Jakarta Sans* (hiện đại) · *Baloo 2 + Nunito* (vui nhộn). Chọn theo brief, đừng chọn cặp mặc định cho mọi shop.
- Thang cỡ chữ rõ ràng (ví dụ mobile): 12 / 14 / 16 / 20 / 28 / 40. Tiêu đề hero dùng chữ như một yếu tố thiết kế chủ động.
- Dòng chữ **< 80 ký tự**. Serif cần line-height lớn hơn sans một chút. Giá dùng chữ số đều nhau (`font-variant-numeric: tabular-nums`).

## 6. BỐ CỤC & COMPONENT

**Nguyên tắc chung**
- Mobile-first: thiết kế khung 360–390px trước; breakpoint `sm 640 / md 768 / lg 1024 / xl 1280`.
- Nhịp khoảng cách thống nhất (bội số của 4/8px). Container tối đa ~1200px.
- **Phân cấp độ bo:** không dùng một độ bo cho mọi thứ. Nút/ô nhập `sm`, card `md`, khối lớn/hero `lg`.
- Ảnh sản phẩm là nhân vật chính: tỉ lệ cố định (ví dụ 4:5), `object-cover`, `loading="lazy"`, có `alt`.

**Bộ component nền tảng** (tạo ở `src/components/ui/`)
`Button` (primary / secondary / ghost / loading) · `Input`, `Select`, `Textarea` (có nhãn, lỗi) · `Badge` (Sale, Mới, Hết hàng) · `Card` · `Modal`/`Drawer` · `Toast` · `Skeleton` · `Tabs` · `Accordion` · `QuantityStepper` · `PriceTag` (giá gốc gạch + giá sale + % giảm) · `EmptyState` · `Rating` (để sẵn, chưa bật).

**Bố cục các trang chính**

| Trang | Cấu trúc gợi ý |
|---|---|
| Header | Thanh khuyến mãi mỏng (có thể chạy chữ) → logo, tìm kiếm, tài khoản, giỏ (badge số lượng). Mobile: menu trượt, thanh tìm kiếm mở rộng. Dính trên cùng, thu nhỏ khi cuộn. |
| Trang chủ | Hero (khoảnh khắc chính) → danh mục nổi bật → sản phẩm nổi bật/mới → điểm tin cậy (giao hàng, đổi trả, COD) → nội dung thương hiệu ngắn → footer đủ liên hệ. |
| Danh sách SP | Bộ lọc (mobile: drawer đáy), sắp xếp, lưới 2 cột mobile / 3–4 cột desktop, hiển thị số kết quả, phân trang hoặc "xem thêm". |
| Chi tiết SP | Gallery vuốt/zoom, tên, giá, chọn biến thể, số lượng, **nút "Thêm vào giỏ" dính đáy trên mobile**, mô tả/thông số dạng accordion, sản phẩm liên quan. |
| Giỏ hàng | Drawer bên phải (desktop) hoặc trang riêng (mobile), tổng tiền + tiến độ "mua thêm X để miễn phí ship". |
| Checkout (COD) | Tối đa 2 bước: (1) thông tin giao nhận, (2) xem lại + **Đặt hàng (thanh toán khi nhận)**. Tóm tắt đơn luôn thấy được. |
| Đặt hàng xong | Trang cảm ơn: mã đơn, tóm tắt, bước tiếp theo, một khoảnh khắc ăn mừng nhẹ. |

## 7. ANIMATION (PHẦN QUAN TRỌNG)

Khách muốn trang **bắt mắt, có chuyển động**. Quy tắc để chuyển động tạo giá trị chứ không rối: **một vài khoảnh khắc được dàn dựng kỹ + phản hồi tức thì cho hành động của người dùng.** Không phủ hiệu ứng lên mọi thứ.

### 7.1 Thư viện
`npm i motion` → `import { motion, AnimatePresence, useReducedMotion } from "motion/react"`. (Nếu lỗi, dùng `framer-motion` với cú pháp tương đương.) Với hiệu ứng đơn giản dùng CSS/Tailwind transition.

### 7.2 Khoảnh khắc chính (chọn 1–2, làm thật kỹ)
Chọn theo ngành hàng, không dùng chung một kiểu:
- **Hero dàn dựng khi tải trang:** tiêu đề hiện từng dòng/từng cụm (mask reveal), ảnh sản phẩm trượt/scale vào, CTA hiện cuối. Tổng ≤ 1.2s, chạy **một lần**.
- **Hero có chiều sâu:** ảnh sản phẩm di chuyển nhẹ theo con trỏ (desktop) hoặc theo cuộn (parallax nhẹ).
- **Băng chuyền sản phẩm/marquee** cho thương hiệu, chạy chậm, dừng khi hover.
- **Chuyển cảnh theo danh mục:** đổi tab danh mục thì lưới sản phẩm đổi mượt (layout animation).

### 7.3 Phản hồi cho hành động (nên có, nhẹ và nhanh 150–350ms)
| Hành động | Hiệu ứng |
|---|---|
| Hover/nhấn card sản phẩm | Ảnh phóng nhẹ (1.04), hiện ảnh thứ 2 nếu có, nút "Thêm nhanh" trượt lên. Mobile: hiệu ứng nhấn `scale(.98)`. |
| Thêm vào giỏ | Nút đổi trạng thái (đang thêm → ✓ Đã thêm), **badge giỏ nảy**, mở mini-cart hoặc toast; tuỳ chọn ảnh bay vào icon giỏ. |
| Mở giỏ / bộ lọc / menu | Drawer trượt vào + lớp phủ mờ dần; đóng bằng vuốt/ESC. |
| Tăng giảm số lượng | Con số đổi bằng trượt dọc ngắn. |
| Chọn biến thể | Viền/nền chip chuyển mượt; ảnh gallery đổi theo màu chọn. |
| Xoá khỏi giỏ | Dòng thu gọn chiều cao rồi biến mất (`AnimatePresence`). |
| Gửi form lỗi | Ô lỗi rung nhẹ + thông báo rõ. |
| Đặt hàng thành công | Dấu ✓ vẽ nét (SVG path) + pháo giấy/hiệu ứng nhẹ, một lần. |
| Chuyển trang | Fade 150–200ms, **không** trượt cả trang. |
| Đang tải | Skeleton có shimmer, giữ đúng khung của nội dung thật (không nhảy bố cục). |

### 7.4 Cuộn trang
- Hạn chế "hiện dần khi cuộn" cho **mọi** section (đó là dấu hiệu template). Chỉ dùng cho: lưới sản phẩm (stagger nhẹ, chạy một lần, tối đa 8 phần tử đầu) và một vài khối nhấn.
- Header co lại/đổi nền khi cuộn; nút "Thêm vào giỏ" dính đáy ở trang chi tiết.

### 7.5 Preset dùng lại (tạo `src/lib/motion.ts`)
```ts
export const ease = [0.22, 1, 0.36, 1] as const;
export const fadeUp = { hidden: { opacity: 0, y: 16 }, show: { opacity: 1, y: 0, transition: { duration: 0.45, ease } } };
export const stagger = { show: { transition: { staggerChildren: 0.06 } } };
export const pop = { whileTap: { scale: 0.96 }, transition: { type: "spring", stiffness: 500, damping: 30 } };
export const drawerRight = { initial: { x: "100%" }, animate: { x: 0 }, exit: { x: "100%" }, transition: { duration: 0.35, ease } };
```
Dùng `viewport={{ once: true, amount: 0.2 }}` cho mọi hiệu ứng theo cuộn.

### 7.6 Giới hạn kỹ thuật (bắt buộc)
- Chỉ animate **`transform` và `opacity`** (không animate width/height/top/left, trừ layout animation của thư viện).
- Tôn trọng `prefers-reduced-motion`: dùng `useReducedMotion()` hoặc `@media (prefers-reduced-motion: reduce)` để tắt/giảm hiệu ứng.
- Không có hiệu ứng tự chạy lặp vô hạn ngoài marquee và skeleton.
- Không để animation chặn thao tác (không bắt người dùng chờ để bấm được).
- Giữ 60fps trên điện thoại tầm trung: không blur/backdrop-filter lớn chồng nhau, không ảnh nặng không tối ưu.

## 8. NỘI DUNG & CÂU CHỮ (TIẾNG VIỆT)

Chữ trong giao diện tồn tại để người dùng dễ hiểu và dễ dùng, không phải để trang trí.

- Viết từ góc nhìn khách hàng, dùng từ đơn giản. Cụ thể, dễ hiểu quan trọng hơn khéo léo.
- Dùng câu chủ động. **Nút nói đúng điều sẽ xảy ra:** "Thêm vào giỏ", "Đặt hàng (thanh toán khi nhận)", "Lưu địa chỉ", không dùng "Gửi" chung chung.
- **Giữ nhất quán một tên cho một hành động** xuyên suốt: nút "Đặt hàng" → toast "Đã đặt hàng".
- Lỗi: nói rõ chuyện gì xảy ra và cách sửa, không xin lỗi dài dòng. Ví dụ: "Số điện thoại cần đủ 10 số."
- Trạng thái rỗng là lời mời hành động: "Giỏ hàng đang trống. Xem sản phẩm mới →" (một nút rõ ràng).
- Giọng điệu theo thương hiệu (`brief.md`), chữ thường chuẩn câu (sentence case), không viết hoa toàn bộ để làm nhãn.
- Không dùng nội dung giả kiểu "Lorem ipsum". Viết nội dung mẫu thật, đúng ngành, đúng giá tiền VN.

## 9. TRUY CẬP & CHẤT LƯỢNG (SÀN TỐI THIỂU, KHÔNG CẦN KHOE)

- Hiển thị tốt từ 360px; không cuộn ngang.
- Vùng chạm ≥ 44×44px; khoảng cách giữa nút đủ để bấm.
- Focus state **nhìn thấy được** khi dùng bàn phím; thứ tự Tab hợp lý; Modal/Drawer giữ focus và đóng bằng ESC.
- `alt` cho ảnh, `aria-label` cho nút icon, form có `label`.
- Tương phản đạt chuẩn; không chỉ dùng màu để báo trạng thái.
- Ảnh: định dạng hiện đại (webp), đặt `width/height` để tránh nhảy bố cục, `loading="lazy"` (trừ ảnh hero).
- Dùng CSS/Tailwind cẩn thận với độ ưu tiên selector để các class không triệt tiêu nhau (đặc biệt padding/margin giữa các section).

## 10. QUY TRÌNH DUYỆT TRƯỚC KHI GIAO MỘT BƯỚC GIAO DIỆN

Gemini tự kiểm tra và báo cáo ngắn:
- [ ] Đã dùng design tokens, không rải mã màu/px tuỳ tiện.
- [ ] Có đủ loading / lỗi / rỗng.
- [ ] Mobile 360px đẹp và dùng được, desktop không bị kéo giãn xấu.
- [ ] Animation: chỉ `transform/opacity`, có xử lý reduced motion, không quá một "khoảnh khắc chính" tranh nhau.
- [ ] CTA nổi bật và nhất quán, giá và tình trạng còn hàng rõ.
- [ ] Không dính các dấu hiệu template ở mục 3.
- [ ] Đã xoá ít nhất một chi tiết thừa.

## 11. TRANG ADMIN: BẢN ĐIỀM TĨNH

Admin ưu tiên **rõ ràng, nhanh, ít phân tâm**:
- Cùng token màu/font với client nhưng nền trung tính, mật độ thông tin cao hơn.
- Animation tối thiểu: chuyển trạng thái nút, drawer, toast, skeleton. **Không** có hiệu ứng trang trí.
- Bảng dữ liệu: header dính, lọc/tìm kiếm, trạng thái đơn bằng `Badge` màu nhất quán, thao tác hàng loạt khi cần.
- Form dài chia nhóm, lưu nháp/ cảnh báo khi thoát chưa lưu, thông báo thành công/thất bại rõ.
- Dùng được tốt trên điện thoại (chủ shop hay duyệt đơn bằng điện thoại).