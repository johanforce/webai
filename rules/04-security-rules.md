# RULE 04 — Firestore Security Rules & nguyên tắc bảo mật

> Web này **không có server riêng**: trình duyệt gọi thẳng Firestore. Vì vậy **Security Rules là lớp bảo vệ duy nhất** của dữ liệu. Mọi quyền phải được rule cưỡng chế, không dựa vào việc ẩn nút trên giao diện.
> Mọi thay đổi schema (rule 03) → cập nhật file này và deploy lại rules **trong cùng lượt**.

---

## 1. NGUYÊN TẮC

1. **Mặc định từ chối.** Collection nào không có `match` thì không ai truy cập được.
2. **Tối thiểu quyền:** khách chỉ chạm dữ liệu của chính mình; catalog chỉ đọc; chỉ admin ghi catalog/đơn/cài đặt.
3. **Admin xác định ở server** bằng email đã xác thực (`email_verified`), không bằng cờ do client gửi lên.
4. **Owner** là email cố định trong rules; admin phụ nằm ở `admins/{email}` và chỉ owner được ghi.
5. **Kiểm tra dữ liệu ghi vào** (khóa cho phép, kiểu, giới hạn) để không bị nhét trường lạ hoặc giá trị rác.
6. **Không bao giờ** dùng `allow ... : if true` cho thao tác ghi, hoặc `if request.auth != null` làm điều kiện duy nhất cho dữ liệu nhạy cảm.
7. **Truy vấn phải khớp rule:** Firestore từ chối cả truy vấn nếu nó *có thể* trả về document không được phép đọc. Vì vậy client luôn thêm điều kiện lọc (`isActive == true`, `userId == uid`).

---

## 2. FILE `firestore.rules` (bản mẫu hoàn chỉnh)

> Thay `OWNER_EMAIL_HERE` bằng Gmail owner (chữ thường), ví dụ `shopowner@gmail.com`. Giá trị này phải khớp `VITE_OWNER_EMAIL`.

```
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {

    // ========== HÀM DÙNG CHUNG ==========
    function ownerEmail() { return 'OWNER_EMAIL_HERE'; }

    function signedIn() { return request.auth != null; }

    function verified() {
      return signedIn() && request.auth.token.email_verified == true;
    }

    function isOwner() {
      return verified() && request.auth.token.email == ownerEmail();
    }

    function isAdmin() {
      return isOwner() || (
        verified() &&
        exists(/databases/$(database)/documents/admins/$(request.auth.token.email))
      );
    }

    function isSelf(uid) { return signedIn() && request.auth.uid == uid; }

    // Các khóa được phép thay đổi trong một lần cập nhật
    function onlyChanges(keys) {
      return request.resource.data.diff(resource.data).affectedKeys().hasOnly(keys);
    }

    // ========== USERS ==========
    function validUser(d) {
      return d.keys().hasOnly(['email','displayName','photoURL','phone','addresses',
                               'createdAt','updatedAt','lastLoginAt'])
        && d.email == request.auth.token.email
        && (!('addresses' in d) || (d.addresses is list && d.addresses.size() <= 10))
        && (!('phone' in d) || (d.phone is string && d.phone.size() <= 15));
    }

    match /users/{uid} {
      allow read: if isSelf(uid) || isAdmin();
      allow create: if isSelf(uid) && verified() && validUser(request.resource.data);
      allow update: if isSelf(uid) && validUser(request.resource.data)
                    && request.resource.data.createdAt == resource.data.createdAt;
      allow delete: if false;
    }

    // ========== ADMINS (chỉ owner ghi) ==========
    function validAdminDoc(email, d) {
      return d.keys().hasOnly(['email','role','addedBy','createdAt'])
        && d.email == email
        && (d.role == 'staff' || (d.role == 'owner' && email == ownerEmail()));
    }

    match /admins/{email} {
      allow get: if verified() && (request.auth.token.email == email || isAdmin());
      allow list: if isOwner();
      allow create, update: if isOwner() && validAdminDoc(email, request.resource.data);
      allow delete: if isOwner() && email != request.auth.token.email;
    }

    // ========== CATALOG (đọc công khai phần đang bán, admin ghi) ==========
    match /categories/{id} {
      allow read: if resource.data.isActive == true || isAdmin();
      allow create, update, delete: if isAdmin();
    }

    match /products/{id} {
      allow read: if resource.data.isActive == true || isAdmin();
      allow create, update: if isAdmin() && validProduct(request.resource.data);
      allow delete: if isAdmin();
    }

    function validProduct(d) {
      return d.name is string && d.name.size() > 0 && d.name.size() <= 200
        && d.price is int && d.price >= 0
        && d.stock is int && d.stock >= 0
        && d.isActive is bool
        && (!('salePrice' in d) || d.salePrice == null || (d.salePrice is int && d.salePrice < d.price))
        && (!('images' in d) || (d.images is list && d.images.size() <= 10))
        && (!('variants' in d) || (d.variants is list && d.variants.size() <= 50));
    }

    match /banners/{id} {
      allow read: if resource.data.isActive == true || isAdmin();
      allow create, update, delete: if isAdmin();
    }

    match /settings/{docId} {
      allow read: if true;                 // thông tin shop công khai, KHÔNG lưu bí mật ở đây
      allow create, update, delete: if isAdmin();
    }

    // ========== CARTS (giỏ của chính mình) ==========
    match /carts/{uid} {
      allow get: if isSelf(uid);
      allow create, update: if isSelf(uid) && verified()
        && request.resource.data.keys().hasOnly(['items','updatedAt'])
        && request.resource.data.items is list
        && request.resource.data.items.size() <= 50;
      allow delete: if isSelf(uid);
    }

    // ========== ORDERS ==========
    function validNewOrder(d) {
      return d.keys().hasOnly(['code','userId','userEmail','items','customer','note',
                               'subtotal','shippingFee','total','paymentMethod',
                               'status','stockDeducted','createdAt'])
        && d.keys().hasAll(['code','userId','userEmail','items','customer',
                            'subtotal','shippingFee','total','paymentMethod',
                            'status','stockDeducted','createdAt'])
        && d.userId == request.auth.uid
        && d.userEmail == request.auth.token.email
        && d.status == 'pending'
        && d.paymentMethod == 'COD'
        && d.stockDeducted == false
        && d.code is string && d.code.size() <= 30
        && d.items is list && d.items.size() > 0 && d.items.size() <= 50
        && d.subtotal is int && d.subtotal >= 0
        && d.shippingFee is int && d.shippingFee >= 0
        && d.total is int && d.total == d.subtotal + d.shippingFee
        && d.customer is map
        && d.customer.keys().hasAll(['name','phone','province','district','ward','detail'])
        && d.customer.name is string && d.customer.name.size() > 0 && d.customer.name.size() <= 100
        && d.customer.phone is string && d.customer.phone.matches('^0[0-9]{9}$')
        && d.customer.detail is string && d.customer.detail.size() > 0 && d.customer.detail.size() <= 300
        && (!('note' in d) || (d.note is string && d.note.size() <= 500))
        && d.createdAt == request.time;
    }

    match /orders/{orderId} {
      allow read: if signedIn() && (resource.data.userId == request.auth.uid || isAdmin());

      allow create: if verified() && validNewOrder(request.resource.data);

      allow update: if isAdmin()
        || (
          verified()
          && resource.data.userId == request.auth.uid
          && resource.data.status == 'pending'
          && request.resource.data.status == 'cancelled'
          && onlyChanges(['status','updatedAt','cancelReason','cancelledBy'])
        );

      allow delete: if false;              // không xóa đơn; hủy bằng status = 'cancelled'
    }
  }
}
```

---

## 3. GIỚI HẠN ĐÃ BIẾT (nói thẳng với người dùng)

Vì không có server, có những điều rules **không** làm được. Đây là rủi ro còn lại và cách giảm thiểu:

| Giới hạn | Hệ quả | Giảm thiểu trong v1 |
|---|---|---|
| Rules không thể lặp qua từng phần tử `items` để đối chiếu giá với `products` | Khách có kỹ thuật có thể gửi `unitPrice` thấp hơn giá thật (tổng vẫn khớp `subtotal + shippingFee`) | Trang chi tiết đơn của admin **tự so sánh** `unitPrice` với giá hiện tại và kiểm tra `subtotal == Σ lineTotal`, **hiện cảnh báo đỏ** nếu lệch. Đơn COD luôn được shop gọi xác nhận trước khi giao |
| Rules không kiểm tra tồn kho lúc tạo đơn | Có thể đặt vượt tồn | Chỉ trừ kho khi admin xác nhận (transaction báo lỗi nếu không đủ); client kiểm tra tồn trước khi đặt |
| Không giới hạn tần suất tạo đơn | Có thể bị spam đơn | Bắt buộc đăng nhập Google; admin lọc/hủy đơn rác; cân nhắc bật Firebase **App Check** ở giai đoạn sau |
| Rules không tính phí ship từ cài đặt shop | `shippingFee` do client gửi | Admin đối chiếu với `settings/shop` trong trang chi tiết đơn |
| Ảnh Cloudinary tải lên bằng unsigned preset | Ai có tên preset có thể tải ảnh lên cloud của bạn | Trong Cloudinary: giới hạn định dạng, dung lượng, thư mục của preset; theo dõi dung lượng; xóa ảnh không dùng thủ công |

Nếu sau này cần chống gian lận chặt (phí, giá, tồn kho tự động), nâng cấp lên **Cloud Functions** (gói Blaze) để tạo đơn ở server. Ghi vào `PROJECT_CONFIG.md` mục 7 (mở rộng).

---

## 4. YÊU CẦU PHÍA CLIENT ĐỂ KHỚP RULES

- Catalog: `query(..., where('isActive','==',true))`. Admin truy vấn không cần lọc.
- Đơn của khách: `query(collection('orders'), where('userId','==',uid), orderBy('createdAt','desc'))`.
- Tạo đơn: dùng đúng **tập khóa** trong `validNewOrder`, `createdAt: serverTimestamp()`, `status:'pending'`, `paymentMethod:'COD'`, `stockDeducted:false`. Thêm khóa lạ → bị từ chối.
- Khách hủy đơn: chỉ gửi `{ status:'cancelled', cancelledBy:'customer', cancelReason, updatedAt: serverTimestamp() }`.
- Giá trị tiền gửi lên phải là **số nguyên**.
- Không lưu bất kỳ bí mật nào trong `settings/shop` hoặc các collection đọc công khai.

---

## 5. TRIỂN KHAI & THỬ NGHIỆM

```bash
# thử cục bộ với Emulator (không đụng dữ liệu thật)
firebase emulators:start --only firestore,auth

# triển khai rules và index
firebase deploy --only firestore:rules,firestore:indexes
```
- Dùng **Rules Playground** (Firebase Console) hoặc test với Emulator cho đủ 13 tình huống ở `skills/qa-checklist.md` mục 1.1.
- Sau mỗi lần sửa rules: deploy → chạy lại các tình huống bị ảnh hưởng.
- Trong **Authentication → Settings → Authorized domains**: chỉ giữ tên miền thật (và `localhost` khi dev).
- Đặt cảnh báo ngân sách/quota trong Google Cloud để tránh chi phí ngoài ý muốn.
- Nếu về sau dùng Firebase Storage: viết thêm `storage.rules` (chỉ admin ghi, giới hạn loại và dung lượng tệp). Hiện tại không dùng.

---

## 6. CÁC LỖI THƯỜNG GẶP KHI VIẾT RULES (tránh)

- ❌ Để rule "mở" khi thử rồi quên đóng (`allow read, write: if true`).
- ❌ Quên `email_verified`, khiến email chưa xác thực có thể đi qua.
- ❌ So sánh email có chữ hoa: email trong token là chữ thường; `admins/{email}` cũng phải chữ thường.
- ❌ Truy vấn danh sách không khớp điều kiện rule → báo `permission-denied` (sửa truy vấn, không nới rule).
- ❌ Cho phép `update` toàn bộ document đơn cho khách (chỉ được đổi các khóa trong `onlyChanges`).
- ❌ Lưu quyền vào `users` rồi dùng nó để cấp quyền.

---

## 7. TIÊU CHÍ CHẤP NHẬN

- [ ] `firestore.rules` đã deploy, khớp bản mẫu (đã thay email owner), không còn rule thử nghiệm.
- [ ] 13 tình huống ở `qa-checklist.md` mục 1.1 cho kết quả đúng mong đợi.
- [ ] Mọi truy vấn trong `services/` chạy được với rules (không `permission-denied` ở luồng bình thường).
- [ ] Trang chi tiết đơn của admin có cảnh báo khi giá/ship/tổng không khớp.
- [ ] Authorized domains chỉ gồm tên miền thật.
