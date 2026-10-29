# PROJECT_CONFIG.md — Cấu hình bộ base Web Bán Hàng (Vibe Code)

> File này là **nguồn sự thật duy nhất** (single source of truth) của dự án.
> Mọi file rule/skill được sinh ra phải bám theo file này.
> Trạng thái: `[CHỐT]` = đã quyết định, `[MẶC ĐỊNH]` = AI tự giả định, cần bạn xác nhận, `[TRỐNG]` = điền theo từng dự án.

---

## 1. Mục tiêu

Một bộ khung (base) cho phép: **nhập ý tưởng bán hàng → trả lời bộ câu hỏi business → sinh ra bộ file `.md` (rule + skill) → dùng bộ file đó để vibe code ra website bán hàng hoàn chỉnh.**

Công cụ vibe code: **Google AI Studio (Build mode, Gemini)**.
Vì AI Studio không đọc `CLAUDE.md` hay `.cursor/rules`, bộ file md được dùng bằng cách:
1. Dán nội dung `rules/00-master-rule.md` vào **System instructions** của app.
2. Dán/upload từng file skill khi tới bước tương ứng trong `GEN_PROGRESS.md`.
3. Giữ `PROJECT_CONFIG.md` + `GEN_PROGRESS.md` trong thư mục gốc dự án để Gemini luôn đọc lại.

---

## 2. Quyết định đã chốt `[CHỐT]`

| Hạng mục | Quyết định |
|---|---|
| Công cụ | Google AI Studio (Gemini) |
| Frontend | React + Vite + Tailwind CSS |
| Database | Firebase Firestore |
| Đăng nhập | Firebase Auth — **Google (Gmail) only** |
| Admin | Gmail đã tạo Firebase project/Firestore = tài khoản admin gốc (owner) |
| Thanh toán | **Chỉ COD** (thanh toán khi nhận hàng) |
| Ngôn ngữ UI | Tiếng Việt |
| Tiền tệ | VND (định dạng `1.250.000 ₫`) |

---

## 3. Giả định mặc định `[MẶC ĐỊNH]` — hãy xác nhận hoặc sửa

| Hạng mục | Giả định | Lý do |
|---|---|---|
| Guest checkout | **Không.** Phải đăng nhập Gmail mới đặt hàng | Gắn đơn với `uid`, rule bảo mật đơn giản |
| Số admin | 1 owner + có thể thêm admin phụ qua collection `admins` (do owner thêm) | Dễ mở rộng, không hardcode nhiều email |
| Ảnh sản phẩm | Upload qua **Cloudinary (unsigned preset)**, lưu URL vào Firestore | Firebase Storage cần gói Blaze; Cloudinary có gói free |
| Địa chỉ giao hàng | Nhập tay: Họ tên, SĐT, Tỉnh/TP, Quận/Huyện, Phường/Xã, Địa chỉ chi tiết | Chưa cần API địa giới ở bản base |
| Phí ship | Cố định, cấu hình trong `settings/shop` (có ngưỡng miễn phí ship) | Đủ cho COD bản đầu |
| Biến thể | Có (size/màu/...) — bật/tắt theo ngành hàng | Tùy ngành |
| Tồn kho | Có theo sản phẩm/biến thể, trừ kho khi admin xác nhận đơn | Tránh bán lố |
| Mã giảm giá | Chưa có ở v1 (đưa vào Phase mở rộng) | Giữ base gọn |
| Thông báo đơn mới | Admin xem realtime trên dashboard (`onSnapshot`) + chuông/badge | Chưa cần email/Zalo |
| SEO | Có `<title>`, meta, Open Graph cơ bản (SPA) | Vite SPA, SEO nâng cao để sau |
| Deploy | Firebase Hosting | Cùng hệ Firebase |

---

## 4. Input theo từng dự án `[TRỐNG]` — bộ câu hỏi business

> Điền bảng này cho **mỗi website mới**. Đây là phần "input" của bộ base.
> Quy trình: bạn đưa 1 câu ý tưởng → AI hỏi các mục dưới → điền vào đây → sinh rule/skill.

### A. Ý tưởng & thị trường
- [ ] A1. Bán loại hàng gì? (1 câu): `...`
- [ ] A2. Khách hàng mục tiêu (tuổi, giới, thu nhập, nơi ở): `...`
- [ ] A3. Điểm khác biệt so với đối thủ: `...`
- [ ] A4. 2–3 website tham khảo (link): `...`

### B. Sản phẩm
- [ ] B1. Số lượng sản phẩm dự kiến (<20 / 20–200 / >200): `...`
- [ ] B2. Danh mục chính (liệt kê): `...`
- [ ] B3. Có biến thể không? (size/màu/dung tích/vị...): `...`
- [ ] B4. Thuộc tính đặc thù ngành (VD: chất liệu, hạn sử dụng, thành phần, bảo hành): `...`
- [ ] B5. Giá: một giá hay có giá gốc + giá khuyến mãi?: `...`
- [ ] B6. Cần quản lý tồn kho không?: `...`

### C. Thương hiệu & giao diện
- [ ] C1. Tên shop / slogan: `...`
- [ ] C2. Tông cảm xúc (sang trọng / trẻ trung / gần gũi / tối giản / năng động...): `...`
- [ ] C3. Màu chủ đạo (hoặc "để AI đề xuất"): `...`
- [ ] C4. Đã có logo chưa?: `...`
- [ ] C5. Có hỗ trợ dark mode không?: `...`

### D. Vận hành
- [ ] D1. Phí ship (cố định / theo vùng) và ngưỡng miễn phí: `...`
- [ ] D2. Khu vực giao hàng (toàn quốc / nội thành): `...`
- [ ] D3. Chính sách đổi trả, bảo hành (tóm tắt): `...`
- [ ] D4. Kênh liên hệ (Hotline, Zalo, Facebook, địa chỉ cửa hàng): `...`
- [ ] D5. Gmail admin gốc: `...@gmail.com`

### E. Trang nội dung
- [ ] E1. Cần các trang: Giới thiệu / Chính sách / Liên hệ / Blog? : `...`
- [ ] E2. Có banner/slider khuyến mãi ở trang chủ không?: `...`

---

## 5. Cấu trúc file sẽ được sinh ra

```
project-root/
├── PROJECT_CONFIG.md          # file này
├── GEN_PROGRESS.md            # tiến trình
├── brief.md                   # câu trả lời Mục 4 đã chốt (sinh ở Phase 1)
├── rules/
│   ├── 00-master-rule.md      # dán vào System instructions AI Studio
│   ├── 01-tech-stack.md       # stack, cấu trúc thư mục, quy ước code
│   ├── 02-auth-admin.md       # Google login, phân quyền admin/client
│   ├── 03-firestore-schema.md # collections, field, index
│   ├── 04-security-rules.md   # firestore.rules + nguyên tắc bảo mật
│   ├── 05-client-features.md  # chức năng trang khách
│   ├── 06-admin-features.md   # chức năng trang admin
│   └── 07-business-context.md # nội dung ngành hàng (sinh từ brief.md)
└── skills/
    ├── frontend-design.md     # thẩm mỹ, bố cục, component
    ├── ux-conversion.md       # tối ưu chuyển đổi, mobile-first
    ├── copywriting-vi.md      # viết nội dung tiếng Việt cho bán hàng
    └── qa-checklist.md        # kiểm thử trước khi giao
```

---

## 6. Nguyên tắc cốt lõi cho rule (tóm tắt)

### 6.1 Phân quyền
- **Client**: đọc sản phẩm/danh mục công khai; chỉ đọc/tạo **đơn hàng của chính mình** (`userId == auth.uid`).
- **Admin**: toàn quyền quản lý sản phẩm, danh mục, đơn hàng, cài đặt, banner.
- Xác định admin bằng **Firestore Rules** (kiểm tra `request.auth.token.email` thuộc owner hoặc tồn tại trong `admins/{email}`), **không** chỉ ẩn nút ở UI.
- Chỉ chấp nhận `email_verified == true`.
- Route `/admin/*` có route guard ở client **và** rule ở server.

### 6.2 Firestore schema (khung)

| Collection | Field chính |
|---|---|
| `users/{uid}` | `email, displayName, photoURL, phone, addresses[], createdAt` |
| `admins/{email}` | `email, role ('owner'\|'staff'), addedBy, createdAt` |
| `categories/{id}` | `name, slug, image, order, isActive` |
| `products/{id}` | `name, slug, description, categoryId, images[], price, salePrice, stock, variants[], attributes{}, isActive, isFeatured, createdAt` |
| `carts/{uid}` | `items[{productId, variantId, qty}], updatedAt` |
| `orders/{id}` | `userId, items[snapshot], customer{name, phone, address}, subtotal, shippingFee, total, paymentMethod:'COD', status, note, createdAt, updatedAt` |
| `banners/{id}` | `image, link, order, isActive` |
| `settings/shop` | `name, logo, hotline, zalo, address, shippingFee, freeShipThreshold, policies{}` |

Trạng thái đơn: `pending → confirmed → shipping → completed` hoặc `cancelled`.
Đơn hàng lưu **snapshot** tên/giá sản phẩm tại thời điểm đặt (không tham chiếu giá động).

### 6.3 Chức năng Client (cơ bản)
Trang chủ (banner, danh mục, nổi bật, mới) · Danh sách sản phẩm (lọc, sắp xếp, tìm kiếm, phân trang) · Chi tiết sản phẩm (gallery, biến thể, số lượng) · Giỏ hàng · Checkout COD · Tài khoản (thông tin, địa chỉ) · Lịch sử & chi tiết đơn · Trang tĩnh (giới thiệu, chính sách, liên hệ) · Đăng nhập Google · 404.

### 6.4 Chức năng Admin
Dashboard (doanh thu, số đơn theo trạng thái, đơn mới) · CRUD sản phẩm (upload ảnh, biến thể, tồn kho) · CRUD danh mục · Quản lý đơn (lọc, đổi trạng thái, xem chi tiết, in đơn) · Quản lý banner · Cài đặt shop · Quản lý admin phụ (chỉ owner).

### 6.5 Nguyên tắc frontend (skill)
- **Mobile-first**, hầu hết khách vào bằng điện thoại.
- Đẹp nhưng không rối: hệ thống design token (màu, bo góc, bóng, spacing) thống nhất; có hover/transition tinh tế; skeleton loading; empty state rõ ràng.
- Không dùng giao diện "mẫu mặc định": chọn font cặp (heading + body) có cá tính, bảng màu theo tông thương hiệu, ảnh sản phẩm là trung tâm.
- Nút CTA nổi bật, giá và trạng thái còn hàng rõ ràng, checkout tối đa 2 bước.
- Truy cập được: contrast đủ, focus state, alt ảnh, vùng bấm ≥ 44px.

### 6.6 Quy ước code
- Thư mục: `src/{components,pages,features,hooks,lib,services,store,types}`; `pages/admin/*` tách riêng.
- Mọi truy cập Firestore qua `src/services/*`, **không** gọi trực tiếp trong component.
- Cấu hình Firebase ở `.env` (`VITE_FIREBASE_*`), không hardcode key quan trọng khác.
- Mỗi trang xử lý đủ 3 trạng thái: loading / lỗi / rỗng.
- Dữ liệu mẫu (seed) tách file riêng để dễ xoá khi go-live.

---

## 7. Phạm vi mở rộng sau v1 (chưa làm)
Mã giảm giá · Đánh giá sản phẩm · Wishlist · Thông báo email/Telegram/Zalo · Thanh toán QR/VNPay/MoMo · Phí ship theo vùng · SEO nâng cao (SSR/prerender) · Đa ngôn ngữ.

---

## 8. Nhật ký quyết định
| Ngày | Quyết định | Ghi chú |
|---|---|---|
| 2026-10-29 | Chốt: AI Studio, React+Vite+Tailwind, COD only | Khởi tạo dự án |