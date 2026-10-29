# RULE 03 — Firestore schema

> Mọi thay đổi schema phải cập nhật **đồng thời**: `types/models.ts`, `firestore.rules` (rule 04), `firestore.indexes.json` và (nếu có) `seed/`.
> Phần `attributes` của sản phẩm và việc bật/tắt biến thể do `07-business-context.md` quyết định theo ngành.

---

## 1. QUY ƯỚC CHUNG

- Tiền: **số nguyên VND** (không số thực). Phần trăm giảm tính ở client từ `price` và `salePrice`.
- Thời gian: `Timestamp` (ghi bằng `serverTimestamp()`); chuyển sang `Date` ở tầng service.
- `id` document lấy từ `doc.id`, không lưu lặp trong dữ liệu (trừ chỗ ghi rõ).
- Tên trường `camelCase`. Boolean đặt tên `isXxx`.
- Email lưu **chữ thường**.
- Không lồng quá 2 cấp; không dùng collection con ở v1 (đơn giản, dễ viết rule).
- Giới hạn kích thước: document < 1MB; mảng ảnh ≤ 10, biến thể ≤ 50, địa chỉ ≤ 10, mục trong giỏ ≤ 50.

---

## 2. COLLECTIONS

| Collection | Document id | Ai đọc | Ai ghi |
|---|---|---|---|
| `users` | `uid` | chính chủ, admin | chính chủ |
| `admins` | email (chữ thường) | chính chủ (đọc doc của mình), admin | chỉ owner |
| `categories` | tự sinh | công khai (chỉ `isActive`) | admin |
| `products` | tự sinh | công khai (chỉ `isActive`) | admin |
| `carts` | `uid` | chính chủ | chính chủ |
| `orders` | tự sinh | chủ đơn, admin | chủ đơn (tạo; hủy khi `pending`), admin |
| `banners` | tự sinh | công khai (chỉ `isActive`) | admin |
| `settings` | `shop` (một doc) | công khai | admin |

---

## 3. KIỂU DỮ LIỆU (`src/types/models.ts`)

```ts
import type { Timestamp } from 'firebase/firestore';

export type OrderStatus = 'pending' | 'confirmed' | 'shipping' | 'completed' | 'cancelled';

export interface Address {
  id: string;            // uuid tạo ở client
  name: string;          // người nhận
  phone: string;         // 0xxxxxxxxx
  province: string;      // Tỉnh/TP
  district: string;      // Quận/Huyện
  ward: string;          // Phường/Xã
  detail: string;        // Số nhà, đường
  isDefault: boolean;
}

export interface UserDoc {              // users/{uid}
  email: string;
  displayName: string;
  photoURL?: string;
  phone?: string;
  addresses: Address[];                 // ≤ 10
  createdAt: Timestamp;
  updatedAt: Timestamp;
  lastLoginAt: Timestamp;
}

export interface AdminDoc {             // admins/{email}
  email: string;
  role: 'owner' | 'staff';
  addedBy: string;
  createdAt: Timestamp;
}

export interface CategoryDoc {          // categories/{id}
  name: string;
  slug: string;                         // duy nhất, không dấu: "ao-thun"
  image?: string;
  order: number;                        // thứ tự hiển thị
  isActive: boolean;
  createdAt: Timestamp;
  updatedAt: Timestamp;
}

export interface ProductVariant {
  id: string;                           // uuid
  sku?: string;
  options: Record<string, string>;      // {size:'M', color:'Đen'}
  price?: number;                       // bỏ trống = dùng giá của sản phẩm
  salePrice?: number | null;
  stock: number;                        // ≥ 0
  image?: string;                       // ảnh đại diện biến thể (tùy chọn)
}

export interface ProductDoc {           // products/{id}
  name: string;
  slug: string;                         // duy nhất
  nameSearch: string;                   // tên đã bỏ dấu, chữ thường (phục vụ tìm kiếm)
  searchKeywords?: string[];            // chỉ dùng khi > 200 sản phẩm (mục 6)
  description: string;                  // văn bản thuần, xuống dòng bằng \n
  shortDescription?: string;
  categoryId: string;
  images: string[];                     // URL Cloudinary, phần tử đầu là ảnh chính (≤ 10)
  price: number;                        // giá gốc (VND)
  salePrice?: number | null;            // giá khuyến mãi, phải < price
  unit?: string;                        // cái, hộp, kg...
  stock: number;                        // dùng khi hasVariants = false
  hasVariants: boolean;
  optionTypes?: string[];               // ['size','color'] khi hasVariants = true
  variants?: ProductVariant[];          // ≤ 50
  attributes: Record<string, string>;   // thuộc tính đặc thù ngành (07-business-context)
  isActive: boolean;
  isFeatured: boolean;
  soldCount: number;                    // admin cập nhật khi đơn hoàn thành (tùy chọn)
  createdAt: Timestamp;
  updatedAt: Timestamp;
}

export interface CartItem {
  productId: string;
  variantId?: string | null;
  qty: number;                          // 1..99
}
export interface CartDoc {              // carts/{uid}
  items: CartItem[];                    // ≤ 50
  updatedAt: Timestamp;
}

export interface OrderItem {            // SNAPSHOT tại thời điểm đặt
  productId: string;
  variantId?: string | null;
  name: string;
  variantLabel?: string;                // "Size M / Đen"
  image?: string;
  unitPrice: number;                    // giá đã áp dụng (salePrice nếu có)
  qty: number;
  lineTotal: number;                    // unitPrice * qty
}
export interface OrderDoc {             // orders/{id}
  code: string;                         // "DH260101-K7QX" (xem mục 5)
  userId: string;
  userEmail: string;
  items: OrderItem[];                   // 1..50
  customer: { name: string; phone: string; province: string; district: string; ward: string; detail: string };
  note?: string;
  subtotal: number;
  shippingFee: number;
  total: number;                        // = subtotal + shippingFee
  paymentMethod: 'COD';
  status: OrderStatus;
  stockDeducted: boolean;               // đã trừ kho chưa
  statusHistory?: { status: OrderStatus; at: Timestamp; by: string }[];   // admin thêm
  cancelReason?: string;
  cancelledBy?: 'customer' | 'admin';
  createdAt: Timestamp;
  updatedAt?: Timestamp;
}

export interface BannerDoc {            // banners/{id}
  image: string;
  title?: string;
  subtitle?: string;
  link?: string;                        // đường dẫn nội bộ: "/danh-muc/ao-thun"
  order: number;
  isActive: boolean;
}

export interface ShopSettings {         // settings/shop
  name: string;
  slogan?: string;
  logo?: string;
  hotline: string;
  zalo?: string;
  email?: string;
  facebook?: string;
  address?: string;
  openingHours?: string;
  shippingFee: number;                  // phí ship cố định
  freeShipThreshold: number | null;     // null = không miễn phí ship
  estimatedDelivery?: string;           // "2–4 ngày làm việc"
  maxQtyPerItem?: number;               // mặc định 20
  policies: Record<string, { title: string; content: string }>;
                                        // key = slug: 'doi-tra', 'giao-hang', 'bao-mat'
  about?: string;                       // nội dung trang giới thiệu
  updatedAt: Timestamp;
}
```

---

## 4. QUAN HỆ & ĐỘ NHẤT QUÁN

- **Giá và tồn kho hiệu lực:** nếu `hasVariants = true` → lấy từ biến thể (`variant.price ?? product.price`, `variant.salePrice ?? product.salePrice`, `variant.stock`); ngược lại lấy ở cấp sản phẩm.
- **Giá hiển thị** = `salePrice` nếu có và `< price`, ngược lại `price`.
- **Đơn hàng là snapshot:** sửa/xóa sản phẩm sau này **không** ảnh hưởng đơn cũ. Không join ngược từ đơn sang sản phẩm để lấy tên/giá.
- **Danh mục ↔ sản phẩm:** `categoryId` tham chiếu `categories/{id}`. Không xóa danh mục còn sản phẩm (kiểm tra ở admin).
- **Slug duy nhất:** kiểm tra bằng truy vấn trước khi lưu; nếu trùng thì thêm hậu tố `-2`, `-3`.
- **Trừ kho:** chỉ admin trừ kho **khi chuyển đơn sang `confirmed`**, bằng `runTransaction` (đọc tồn → kiểm tra đủ → trừ → đặt `stockDeducted=true`). Hủy đơn đã trừ kho → cộng lại và đặt `stockDeducted=false`.
- **Luồng trạng thái hợp lệ:**
```
pending ──► confirmed ──► shipping ──► completed
   │            │            │
   └────────────┴────────────┴──► cancelled     (completed là trạng thái cuối)
```
Khách chỉ được đổi `pending → cancelled`.

---

## 5. MÃ ĐƠN

`code = "DH" + yyMMdd + "-" + 4 ký tự ngẫu nhiên (A–Z, 2–9, bỏ ký tự dễ nhầm như O/0/I/1)`. Ví dụ `DH261029-K7QX`.
- Sinh ở client lúc tạo đơn; khả năng trùng rất thấp. Admin tìm đơn theo `code`.
- Tên bản ghi (`doc.id`) vẫn là id tự sinh của Firestore.

---

## 6. TÌM KIẾM (Firestore không có full-text)

Chọn theo số lượng sản phẩm trong `brief.md` (B1):

| Quy mô | Cách làm |
|---|---|
| **≤ 200 sản phẩm** (mặc định) | Tải danh sách sản phẩm `isActive` một lần, cache bằng React Query, **lọc phía client** trên `nameSearch` (đã bỏ dấu, chữ thường) |
| **> 200 sản phẩm** | Lưu `searchKeywords` (các tiền tố/từ đã bỏ dấu của tên), truy vấn `array-contains` một từ khóa, lọc tiếp phía client; hoặc đề xuất dịch vụ tìm kiếm ngoài ở giai đoạn sau |

Hàm chuẩn hóa dùng chung (`lib/search.ts`): chuyển chữ thường, `normalize('NFD')`, bỏ dấu, đổi `đ → d`, bỏ ký tự đặc biệt, gộp khoảng trắng.

---

## 7. INDEX (`firestore.indexes.json`)

Các truy vấn dự kiến cần index tổng hợp:

```json
{
  "indexes": [
    { "collectionGroup": "products", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "isActive", "order": "ASCENDING" },
      { "fieldPath": "createdAt", "order": "DESCENDING" } ] },
    { "collectionGroup": "products", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "isActive", "order": "ASCENDING" },
      { "fieldPath": "categoryId", "order": "ASCENDING" },
      { "fieldPath": "createdAt", "order": "DESCENDING" } ] },
    { "collectionGroup": "products", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "isActive", "order": "ASCENDING" },
      { "fieldPath": "isFeatured", "order": "ASCENDING" },
      { "fieldPath": "createdAt", "order": "DESCENDING" } ] },
    { "collectionGroup": "products", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "isActive", "order": "ASCENDING" },
      { "fieldPath": "price", "order": "ASCENDING" } ] },
    { "collectionGroup": "products", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "isActive", "order": "ASCENDING" },
      { "fieldPath": "price", "order": "DESCENDING" } ] },
    { "collectionGroup": "orders", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "userId", "order": "ASCENDING" },
      { "fieldPath": "createdAt", "order": "DESCENDING" } ] },
    { "collectionGroup": "orders", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "status", "order": "ASCENDING" },
      { "fieldPath": "createdAt", "order": "DESCENDING" } ] },
    { "collectionGroup": "categories", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "isActive", "order": "ASCENDING" },
      { "fieldPath": "order", "order": "ASCENDING" } ] },
    { "collectionGroup": "banners", "queryScope": "COLLECTION", "fields": [
      { "fieldPath": "isActive", "order": "ASCENDING" },
      { "fieldPath": "order", "order": "ASCENDING" } ] }
  ],
  "fieldOverrides": []
}
```
- Khi Firestore báo thiếu index, console sẽ in đường dẫn tạo index; **thêm vào file này** để khỏi mất khi triển khai lại.
- Truy vấn một trường (ví dụ tìm theo `slug`) dùng index tự động, không cần khai báo.

---

## 8. SEED (dữ liệu mẫu)

- Đặt trong `src/seed/` (JSON + hàm nạp chỉ chạy khi admin bấm nút ở trang cài đặt, hoặc script riêng).
- Mỗi dự án: 3–5 danh mục, 8–12 sản phẩm **đúng ngành**, giá thật theo thị trường VN, ảnh placeholder rõ ràng (không ảnh bản quyền), 1–2 banner, `settings/shop` đủ trường.
- Đánh dấu mọi bản ghi seed bằng `attributes.__seed = 'true'` hoặc tiền tố slug `demo-` để xóa sạch ở bước D3.

---

## 9. TIÊU CHÍ CHẤP NHẬN

- [ ] Mọi collection/field dùng trong code đều có trong `types/models.ts` và mục 3.
- [ ] Mọi truy vấn trong `services/` có index tương ứng trong `firestore.indexes.json`.
- [ ] Tạo đơn lưu đủ snapshot; sửa sản phẩm không làm đổi đơn cũ.
- [ ] Trừ/hoàn kho đúng bằng transaction; tồn kho không âm.
- [ ] Mọi thay đổi schema đã đồng bộ với `firestore.rules`.
