# RULE 01 — Tech stack, cấu trúc thư mục, quy ước code

> Đọc file này **trước khi tạo bất kỳ file code nào**. Không tự đổi stack. Muốn thêm thư viện ngoài danh sách → nêu lý do và chờ người dùng đồng ý.

---

## 1. STACK CỐ ĐỊNH

| Lớp | Công nghệ |
|---|---|
| Ngôn ngữ | TypeScript (`strict: true`) |
| Framework | React 18+ (function component + hooks) |
| Build | Vite |
| CSS | Tailwind CSS (dùng **một** phiên bản theo template ban đầu: v4 với `@tailwindcss/vite`, hoặc v3 với PostCSS; **không trộn**) |
| Router | `react-router-dom` (BrowserRouter) |
| Backend | Firebase: Authentication (**Google**), Cloud Firestore, Hosting |
| Ảnh | Cloudinary (unsigned upload preset); lưu **URL** trong Firestore (đổi theo `brief.md` mục F1) |
| Thanh toán | **Chỉ COD** |

### Thư viện được phép (mặc định)
| Mục đích | Thư viện |
|---|---|
| Dữ liệu & cache | `@tanstack/react-query` (giảm số lần đọc Firestore, có sẵn loading/error) |
| State cục bộ (giỏ hàng, UI) | `zustand` |
| Form + kiểm tra | `react-hook-form` + `zod` + `@hookform/resolvers` |
| Animation | `motion` (xem `skills/frontend-design.md` mục 7) |
| Icon | `lucide-react` |
| Meta/SEO | `react-helmet-async` |
| Tiện ích class | `clsx` + `tailwind-merge` (hàm `cn`) |
| Biểu đồ (chỉ admin, tải lười) | `recharts` hoặc tự vẽ bằng CSS |

### Không dùng (trừ khi người dùng yêu cầu)
Bộ UI nặng (MUI, Ant Design, Chakra), `moment`, `lodash` nguyên khối, Redux, Axios (dùng `fetch`), CSS-in-JS, thư viện thanh toán, Firebase Storage (nếu đã chọn Cloudinary).

---

## 2. CẤU TRÚC THƯ MỤC

```
project-root/
├── PROJECT_CONFIG.md  GEN_PROGRESS.md  brief.md
├── rules/  skills/
├── firebase.json  firestore.rules  firestore.indexes.json  .firebaserc
├── .env  .env.example  .gitignore
├── index.html  vite.config.ts  tsconfig.json  tailwind.config.*
└── src/
    ├── main.tsx  App.tsx  index.css          # index.css chứa design tokens (CSS variables)
    ├── routes/
    │   ├── ClientRoutes.tsx  AdminRoutes.tsx
    │   ├── RequireAuth.tsx   RequireAdmin.tsx   # route guard
    ├── components/
    │   ├── ui/            # Button, Input, Select, Modal, Drawer, Toast, Skeleton, Badge, Tabs, Accordion, EmptyState, PriceTag, QuantityStepper
    │   ├── layout/        # ClientLayout, AdminLayout, Header, Footer, MobileMenu
    │   └── shared/        # ProductCard, CategoryCard, OrderStatusBadge, ImageUploader, SEO
    ├── features/
    │   ├── auth/  catalog/  cart/  checkout/  orders/  account/  admin/
    │   │   └── (mỗi feature: components/, hooks/, schemas.ts)
    ├── pages/
    │   ├── client/        # HomePage, ProductListPage, ProductDetailPage, CartPage, CheckoutPage, OrderSuccessPage, AccountPage, ...
    │   └── admin/         # DashboardPage, ProductsPage, ProductFormPage, CategoriesPage, OrdersPage, OrderDetailPage, BannersPage, SettingsPage, AdminsPage
    ├── services/          # MỌI truy cập Firebase/Cloudinary nằm ở đây
    │   ├── firebase.ts    # khởi tạo app, auth, db
    │   ├── authService.ts productService.ts categoryService.ts bannerService.ts
    │   ├── cartService.ts orderService.ts userService.ts settingsService.ts
    │   ├── adminService.ts uploadService.ts
    ├── hooks/             # useAuth, useDebounce, useMediaQuery, useDocumentTitle...
    ├── store/             # cartStore.ts, uiStore.ts
    ├── lib/               # format.ts, constants.ts, slug.ts, search.ts, motion.ts, cn.ts, errors.ts
    ├── types/             # models.ts (interface theo rule 03)
    └── seed/              # dữ liệu mẫu + script nạp (xóa khi go-live)
```

Alias import: `@/` trỏ tới `src/` (cấu hình trong `vite.config.ts` và `tsconfig.json`).

---

## 3. BIẾN MÔI TRƯỜNG

`.env.example` (commit), `.env` (KHÔNG commit):
```
VITE_FIREBASE_API_KEY=
VITE_FIREBASE_AUTH_DOMAIN=
VITE_FIREBASE_PROJECT_ID=
VITE_FIREBASE_STORAGE_BUCKET=
VITE_FIREBASE_MESSAGING_SENDER_ID=
VITE_FIREBASE_APP_ID=
VITE_OWNER_EMAIL=            # chỉ để UI gợi ý; quyền thật do firestore.rules quyết định
VITE_CLOUDINARY_CLOUD_NAME=
VITE_CLOUDINARY_UPLOAD_PRESET=
```
- Cấu hình web của Firebase và upload preset **được phép lộ công khai**; **bảo mật nằm ở Firestore Rules** (rule 04) và danh sách Authorized domains.
- Tuyệt đối không đưa: service account, private key, API secret, token quản trị.
- Khai báo kiểu cho `import.meta.env` trong `src/vite-env.d.ts`.
- Nếu AI Studio không dùng file `.env`, dùng cơ chế cấu hình mà AI Studio hỗ trợ, **không** dán khóa bí mật vào mã nguồn.

---

## 4. QUY ƯỚC CODE

### Đặt tên & file
- Component/trang: `PascalCase.tsx`, **mỗi file một component chính**, `export function Name` (named export; trang dùng default export để lazy-load).
- Hook: `useXxx.ts`; service: `xxxService.ts`; hằng số `UPPER_SNAKE_CASE`.
- Tên collection và trạng thái đơn lấy từ `lib/constants.ts`, **không gõ chuỗi trực tiếp** trong code:
```ts
export const COL = { USERS:'users', ADMINS:'admins', CATEGORIES:'categories', PRODUCTS:'products',
  CARTS:'carts', ORDERS:'orders', BANNERS:'banners', SETTINGS:'settings' } as const;
export const ORDER_STATUS = ['pending','confirmed','shipping','completed','cancelled'] as const;
```

### TypeScript
- Không dùng `any`; dùng `unknown` rồi thu hẹp kiểu. Kiểu dữ liệu Firestore đặt ở `types/models.ts` (rule 03).
- Hàm service có kiểu trả về rõ ràng, chuyển `Timestamp` → `Date` ở tầng service, component không phải biết về Firestore.

### Truy cập dữ liệu
- Component → hook (React Query) → service → Firestore. **Component không import `firebase/firestore`.**
- Chỉ dùng `onSnapshot` (realtime) cho: danh sách đơn của admin, chi tiết đơn đang xem. Còn lại dùng `getDocs/getDoc` + React Query (`staleTime` 5 phút; danh mục & cài đặt shop 30 phút).
- Mọi truy vấn danh sách có `limit()` (mặc định 12 sản phẩm/trang, 20 đơn/trang) và phân trang bằng `startAfter`. **Cấm đọc cả collection** trừ khi đã chốt ≤ 200 sản phẩm (rule 03 mục tìm kiếm).
- Truy vấn của khách luôn có điều kiện khớp rule: `where('isActive','==',true)` cho catalog, `where('userId','==',uid)` cho đơn.
- Tiền là **số nguyên VND**. Không dùng số thực cho tiền.
- Thời gian ghi dữ liệu dùng `serverTimestamp()`.

### Xử lý lỗi & trạng thái
- Mọi trang có đủ **loading (Skeleton) / lỗi (nút "Thử lại") / rỗng (EmptyState)**.
- Hàm `toUserMessage(error)` trong `lib/errors.ts` đổi mã lỗi Firebase sang câu tiếng Việt (xem `skills/copywriting-vi.md`): `permission-denied`, `unavailable`, `not-found`, `auth/popup-closed-by-user`, `auth/network-request-failed`...
- Không `console.log` dữ liệu người dùng; lỗi bất ngờ ghi `console.error` và hiện toast thân thiện.

### Form
- Dùng `react-hook-form` + `zod`; schema đặt cạnh feature (`schemas.ts`). Thông báo lỗi tiếng Việt.
- Regex SĐT Việt Nam: `^(0|\+84)\d{9}$` (chuẩn hóa về dạng `0xxxxxxxxx` trước khi lưu).

### Route & hiệu năng
- Mọi trang `React.lazy` + `Suspense`. **Bundle admin tách riêng** (chỉ tải khi vào `/admin`).
- Ảnh: `loading="lazy"`, đặt `width/height`; ảnh Cloudinary thêm tham số tối ưu (`f_auto,q_auto,w_…`).
- Font: tải subset `latin` + `vietnamese`, `font-display: swap`.

### Giao diện
- Màu, bo góc, bóng lấy từ **design tokens** (CSS variables trong `index.css`, ánh xạ vào Tailwind). Không rải mã hex trong component.
- Tuân theo `skills/frontend-design.md`, `ux-conversion.md`, `copywriting-vi.md`.

### Commit/ghi chú
- Mỗi bước trong `GEN_PROGRESS.md` = một nhóm thay đổi gọn. Cuối lượt báo cáo theo mẫu ở `00-master-rule.md` mục 9.

---

## 5. ROUTES

**Client**
| Đường dẫn | Trang | Yêu cầu đăng nhập |
|---|---|---|
| `/` | Trang chủ | Không |
| `/san-pham` | Danh sách (lọc, sắp xếp, tìm kiếm `?q=`) | Không |
| `/danh-muc/:slug` | Sản phẩm theo danh mục | Không |
| `/san-pham/:slug` | Chi tiết sản phẩm | Không |
| `/gio-hang` | Giỏ hàng | Không (giỏ tạm trên máy) |
| `/thanh-toan` | Checkout COD | **Có** |
| `/dat-hang-thanh-cong/:orderId` | Cảm ơn | Có (chỉ chủ đơn) |
| `/tai-khoan`, `/tai-khoan/dia-chi` | Tài khoản, địa chỉ | Có |
| `/tai-khoan/don-hang`, `/tai-khoan/don-hang/:id` | Đơn của tôi | Có |
| `/gioi-thieu`, `/lien-he`, `/chinh-sach/:slug` | Trang tĩnh | Không |
| `/dang-nhap` | Đăng nhập Google | Không |
| `*` | 404 | Không |

**Admin** (tất cả cần quyền admin)
`/admin` · `/admin/san-pham` · `/admin/san-pham/moi` · `/admin/san-pham/:id` · `/admin/danh-muc` · `/admin/don-hang` · `/admin/don-hang/:id` · `/admin/banner` · `/admin/cai-dat` · `/admin/quan-tri-vien` (chỉ owner).

---

## 6. FIREBASE HOSTING (`firebase.json`)

```json
{
  "firestore": { "rules": "firestore.rules", "indexes": "firestore.indexes.json" },
  "hosting": {
    "public": "dist",
    "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
    "rewrites": [{ "source": "**", "destination": "/index.html" }],
    "headers": [
      { "source": "/assets/**", "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }] },
      { "source": "/index.html", "headers": [{ "key": "Cache-Control", "value": "no-cache" }] }
    ]
  }
}
```

---

## 7. DẤU HIỆU VI PHẠM RULE NÀY (Gemini tự soát)

- [ ] Component import trực tiếp `firebase/firestore`.
- [ ] Có chuỗi `'orders'`, `'pending'`... gõ tay thay vì dùng hằng số.
- [ ] Có `any`, `console.log` thừa, hoặc mã hex rải rác.
- [ ] Truy vấn không có `limit`.
- [ ] Trang thiếu loading/lỗi/rỗng.
- [ ] Bundle client chứa code admin.
