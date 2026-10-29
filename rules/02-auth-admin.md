# RULE 02 — Đăng nhập Google & phân quyền Admin/Client

> Nguyên tắc vàng: **giao diện chỉ phản ánh quyền, không tạo ra quyền.** Quyền thật do `firestore.rules` (rule 04) quyết định. Route guard ở client chỉ để trải nghiệm tốt hơn.

---

## 1. VAI TRÒ

| Vai trò | Cách xác định | Làm được gì |
|---|---|---|
| **Khách vãng lai** | Chưa đăng nhập | Xem catalog, dùng giỏ hàng tạm trên máy. **Không** đặt hàng |
| **Khách hàng** | Đăng nhập Google, không có trong `admins` | Đặt hàng COD, xem/hủy đơn của mình, quản lý hồ sơ & địa chỉ |
| **Admin phụ (`staff`)** | Có document `admins/{email}` role `staff` | Quản lý sản phẩm, danh mục, đơn, banner, cài đặt |
| **Owner (`owner`)** | Gmail trùng email cấu hình trong `firestore.rules` (Gmail tạo Firebase/Firestore) | Mọi quyền của staff + thêm/xóa admin phụ |

- Chỉ **một owner**. Email owner được ghi trong `firestore.rules` (hằng số) và `VITE_OWNER_EMAIL` (chỉ để UI).
- Email lưu và so sánh **chữ thường**.

---

## 2. LUỒNG ĐĂNG NHẬP GOOGLE

**Dùng:** `GoogleAuthProvider` + `signInWithPopup`. Nếu popup bị chặn hoặc chạy trên thiết bị di động khó dùng popup → `signInWithRedirect` + xử lý kết quả khi tải lại.

```
Bấm "Đăng nhập bằng Google"
  → Google trả về user (email đã xác thực)
  → authService.ensureUserDoc(user): tạo/cập nhật users/{uid}
  → authService.resolveRole(email): đọc admins/{email} → 'owner' | 'staff' | 'customer'
      · Nếu email == VITE_OWNER_EMAIL và chưa có admins/{email}:
        tự tạo admins/{email} {role:'owner'}  (rule chỉ cho phép đúng owner làm việc này)
  → Quay lại trang đang dở (returnTo), hoặc về trang chủ
```

### Trình duyệt trong ứng dụng (Zalo, Facebook, Messenger, Instagram, TikTok)
Google **chặn đăng nhập OAuth trong WebView** (lỗi `disallowed_useragent`). Đây là tình huống rất hay gặp ở khách Việt Nam.
- Nhận diện qua `navigator.userAgent` (chứa `FBAN|FBAV|Zalo|Instagram|Line|TikTok|Messenger`).
- Khi phát hiện: hiện hộp hướng dẫn **"Mở bằng Chrome/Safari để đăng nhập"** + nút sao chép liên kết, **không** gọi đăng nhập.
- Khách xem hàng và dùng giỏ tạm vẫn bình thường trong WebView.

### Trạng thái cần xử lý
| Tình huống | Hành vi |
|---|---|
| Đang kiểm tra đăng nhập khi tải trang | Hiện loading toàn trang/skeleton; **không** nháy giao diện "chưa đăng nhập" |
| Đóng popup (`auth/popup-closed-by-user`) | Im lặng hoặc toast nhẹ, cho thử lại |
| Popup bị chặn (`auth/popup-blocked`) | Chuyển sang redirect |
| Mất mạng | Thông báo theo `copywriting-vi.md` |
| Email chưa xác thực | Không cấp quyền admin; tài khoản Google thường đã xác thực sẵn |

---

## 3. AUTH CONTEXT (`features/auth`)

```ts
type Role = 'guest' | 'customer' | 'staff' | 'owner';
interface AuthState {
  status: 'loading' | 'ready';
  user: { uid: string; email: string; displayName: string; photoURL?: string } | null;
  role: Role;                 // 'guest' khi chưa đăng nhập
  isAdmin: boolean;           // role === 'staff' || role === 'owner'
  signIn(returnTo?: string): Promise<void>;
  signOut(): Promise<void>;
}
```
- Bọc `AuthProvider` ở `App.tsx`. Dùng `onAuthStateChanged`; sau khi có user mới gọi `resolveRole`.
- `status` chỉ là `'ready'` khi đã biết chắc role.
- Component dùng hook `useAuth()`, không gọi `firebase/auth` trực tiếp.
- Đăng xuất: xóa cache React Query, đặt lại cart store (giữ giỏ tạm của khách là tùy chọn; mặc định **xóa dữ liệu theo tài khoản**).

### `users/{uid}` khi đăng nhập
- Lần đầu: tạo `{ email, displayName, photoURL, phone:'', addresses:[], createdAt, updatedAt, lastLoginAt }`.
- Các lần sau: chỉ cập nhật `displayName`, `photoURL`, `lastLoginAt`, `updatedAt`. **Không ghi đè** `phone`, `addresses`.
- Document `users` **không chứa trường quyền** (không có `role`/`isAdmin`) để không ai tự nâng quyền.

---

## 4. ROUTE GUARD

```tsx
<RequireAuth>     // cần đăng nhập
  status==='loading' → <PageLoader/>
  !user → <Navigate to="/dang-nhap" state={{ returnTo: location.pathname + location.search }} />
<RequireAdmin>    // cần quyền admin
  status==='loading' → <PageLoader/>
  !user → đăng nhập (có returnTo)
  !isAdmin → trang "Không có quyền truy cập" (HTTP-like 403), nút về trang chủ
<RequireOwner>    // chỉ owner (route /admin/quan-tri-vien)
  role!=='owner' → trang 403
```

### Ma trận truy cập
| Trang | Guest | Khách | Staff | Owner |
|---|:-:|:-:|:-:|:-:|
| Catalog, giỏ, trang tĩnh | ✅ | ✅ | ✅ | ✅ |
| `/thanh-toan`, `/tai-khoan/*` | ➡️ đăng nhập | ✅ | ✅ | ✅ |
| `/admin/*` | ➡️ đăng nhập | 🚫 403 | ✅ | ✅ |
| `/admin/quan-tri-vien` | ➡️ đăng nhập | 🚫 | 🚫 | ✅ |

- Gõ trực tiếp URL admin cũng phải bị chặn (guard bọc **toàn bộ** nhánh `/admin`).
- Link vào admin chỉ hiện trong menu tài khoản khi `isAdmin`.
- Admin vẫn có thể mua hàng như khách (dùng cùng tài khoản).

---

## 5. QUẢN LÝ ADMIN PHỤ (chỉ owner)

- Collection `admins/{email}` (id là email chữ thường): `{ email, role:'staff', addedBy, createdAt }`.
- Owner thêm admin phụ bằng cách nhập Gmail → tạo document. Người đó đăng nhập lần sau sẽ thấy quyền admin.
- Owner xóa được admin phụ; **không xóa được chính mình**.
- Admin phụ không đọc được danh sách admin khác ngoài chính mình (rule 04); trang `/admin/quan-tri-vien` chỉ owner vào được.
- Khi thu hồi quyền: người đó mất quyền ở lần đọc rule tiếp theo; UI cập nhật khi tải lại trang/đăng nhập lại.

---

## 6. CHUYỂN GIỎ HÀNG TẠM SANG TÀI KHOẢN

- Khách chưa đăng nhập: giỏ lưu trong `localStorage` (bọc `try/catch`, hỏng thì dùng bộ nhớ tạm).
- Sau đăng nhập: **gộp** giỏ tạm vào `carts/{uid}` (cộng dồn số lượng cùng sản phẩm/biến thể, giới hạn theo tồn kho), rồi xóa giỏ tạm.
- Đăng nhập xong phải quay lại đúng trang người dùng đang làm (checkout, trang sản phẩm...).

---

## 7. CẤM

- ❌ Dùng email/mật khẩu hoặc nhà cung cấp đăng nhập khác (chỉ Google).
- ❌ Lưu quyền admin vào `localStorage`, `users` hoặc cờ phía client rồi tin vào đó.
- ❌ Hardcode danh sách email admin phụ trong mã nguồn (chỉ owner nằm trong `firestore.rules`).
- ❌ Chỉ ẩn nút ở UI mà không có rule tương ứng.
- ❌ Hiển thị trang admin trong lúc `status === 'loading'`.

## 8. TIÊU CHÍ CHẤP NHẬN

- [ ] Đăng nhập/đăng xuất Google chạy được; tải lại trang vẫn giữ đăng nhập, không nháy sai trạng thái.
- [ ] Đăng nhập xong quay lại đúng trang (`returnTo`).
- [ ] Gmail thường vào `/admin` → thấy trang 403; chưa đăng nhập → chuyển tới đăng nhập.
- [ ] Owner đăng nhập lần đầu tự có `admins/{email}`; menu hiện "Quản trị".
- [ ] Owner thêm một Gmail làm staff → người đó vào được `/admin` nhưng **không** vào được `/admin/quan-tri-vien`.
- [ ] Trình duyệt Zalo/Facebook hiện hướng dẫn mở bằng trình duyệt ngoài thay vì lỗi trắng.
- [ ] Giỏ tạm của khách được gộp vào tài khoản sau đăng nhập.
