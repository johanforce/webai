# SKILL: qa-checklist — Kiểm thử trước khi giao

> Đặt tại `skills/qa-checklist.md`. Dùng ở bước **C5.4, D1, D2, D3** trong `GEN_PROGRESS.md`.
> Cách dùng: Gemini đi qua từng mục, **tự kiểm tra những gì xem được trong code**, và **liệt kê rõ những mục người dùng phải tự thử trên trình duyệt/điện thoại**. Không được đánh dấu ✅ cho thứ chưa kiểm tra thật.
> Ký hiệu: 🔴 = chặn giao hàng nếu lỗi · 🟡 = nên sửa · 🟢 = nâng cao.

**Cách báo cáo kết quả:** cuối mỗi nhóm, ghi `Đạt x/y`, liệt kê mục lỗi kèm file liên quan và đề xuất sửa. Chỉ sửa khi được người dùng đồng ý, trừ lỗi bảo mật 🔴.

---

## 0. CHUẨN BỊ

- [ ] `npm run build` chạy xong **không lỗi** và không cảnh báo TypeScript nghiêm trọng. 🔴
- [ ] `npm run lint` (nếu có) sạch lỗi. 🟡
- [ ] Không còn `console.log` thừa, `TODO` quan trọng, code chết. 🟡
- [ ] `.env` không bị đưa vào repo; có `.env.example` liệt kê các biến cần thiết (không chứa giá trị thật). 🔴
- [ ] Đã bật Firebase Emulator hoặc dùng project test để thử trước khi dùng dữ liệu thật. 🟡

---

## 1. BẢO MẬT & PHÂN QUYỀN 🔴 (quan trọng nhất)

> Thử bằng **3 vai trò**: (a) chưa đăng nhập, (b) khách đã đăng nhập (Gmail thường), (c) admin.

### 1.1 Firestore Security Rules
| # | Kiểm tra | Mong đợi |
|---|---|---|
| 1 | (a) đọc `products`, `categories`, `banners`, `settings/shop` | Được đọc (công khai) |
| 2 | (a) đọc `orders`, `users`, `carts`, `admins` | **Bị từ chối** |
| 3 | (a) ghi bất kỳ collection nào | **Bị từ chối** |
| 4 | (b) ghi `products`/`categories`/`banners`/`settings` | **Bị từ chối** |
| 5 | (b) đọc đơn của **người khác** | **Bị từ chối** |
| 6 | (b) đọc/ghi `users/{uid khác}`, `carts/{uid khác}` | **Bị từ chối** |
| 7 | (b) tạo đơn với `userId` của người khác | **Bị từ chối** |
| 8 | (b) tạo đơn với `status` khác `pending` hoặc tự đặt `total` sai | **Bị từ chối** hoặc bị ghi đè đúng giá trị |
| 9 | (b) sửa đơn đã tạo để đổi trạng thái sang `completed` | **Bị từ chối** (khách chỉ được hủy khi `pending`) |
| 10 | (b) tự thêm mình vào `admins` | **Bị từ chối** |
| 11 | (c) CRUD sản phẩm, danh mục, banner, settings, đơn | Được phép |
| 12 | (c) thêm/xóa admin phụ | Chỉ **owner** được phép |
| 13 | Tài khoản `email_verified == false` | Không được hưởng quyền admin |

- [ ] Rule không dùng `allow read, write: if true` ở bất kỳ đâu. 🔴
- [ ] Rule không dùng `if request.auth != null` làm điều kiện duy nhất cho dữ liệu nhạy cảm. 🔴
- [ ] Đã deploy `firestore.rules` mới nhất và chạy thử bằng **Rules Playground/Emulator**. 🔴
- [ ] Rule kiểm tra **kiểu dữ liệu và trường bắt buộc** khi tạo đơn (số lượng là số nguyên dương, giá ≥ 0, `paymentMethod == 'COD'`). 🟡

### 1.2 Ứng dụng
- [ ] Truy cập `/admin` khi chưa đăng nhập → chuyển tới đăng nhập. 🔴
- [ ] Truy cập `/admin` bằng Gmail thường → bị chặn, thông báo rõ. 🔴
- [ ] Gõ trực tiếp URL admin con (`/admin/orders`) cũng bị chặn. 🔴
- [ ] Quản trị viên gốc đúng là Gmail đã cấu hình (`{{ADMIN_EMAIL}}`); đổi tài khoản khác thì mất quyền. 🔴
- [ ] Không có khóa bí mật (API secret, private key, token) trong mã nguồn phía client. (Cấu hình web của Firebase và Cloudinary unsigned preset **được phép** công khai.) 🔴
- [ ] Không dùng `dangerouslySetInnerHTML` với dữ liệu người dùng nhập; nếu cần hiển thị nội dung từ admin thì phải làm sạch (sanitize). 🔴
- [ ] Upload ảnh: giới hạn loại tệp và dung lượng (ví dụ ≤ 5MB, jpg/png/webp). 🟡
- [ ] Firebase Authentication: đã thêm **Authorized domains** đúng tên miền thật. 🟡

---

## 2. CHỨC NĂNG CLIENT

### 2.1 Đăng nhập
- [ ] Đăng nhập Google thành công; tạo/cập nhật `users/{uid}`. 🔴
- [ ] Đóng popup đăng nhập giữa chừng → không lỗi, có thể thử lại. 🟡
- [ ] Đăng xuất → giỏ/đơn không còn truy cập được; quay lại trạng thái khách. 🔴
- [ ] Tải lại trang vẫn giữ đăng nhập; không nhấp nháy giao diện sai trạng thái (có loading khi chưa biết). 🟡

### 2.2 Duyệt sản phẩm
- [ ] Trang chủ hiển thị banner, danh mục, nổi bật, mới từ dữ liệu thật. 🔴
- [ ] Lọc, sắp xếp, tìm kiếm cho kết quả đúng; tìm **không dấu** vẫn ra. 🟡
- [ ] Phân trang/“xem thêm” hoạt động, không tải toàn bộ collection. 🟡
- [ ] Danh mục/sản phẩm bị ẩn (`isActive=false`) **không hiện** với khách. 🔴
- [ ] Trang chi tiết: gallery, chọn biến thể, giá đổi theo biến thể, hết hàng bị vô hiệu đúng. 🔴
- [ ] Mở link sản phẩm không tồn tại → trang thông báo thân thiện, không trắng/lỗi. 🟡

### 2.3 Giỏ hàng
- [ ] Thêm, tăng/giảm, xóa sản phẩm; tổng tạm tính đúng. 🔴
- [ ] Không vượt quá tồn kho; thông báo rõ khi vượt. 🔴
- [ ] Giỏ giữ nguyên sau khi tải lại trang và sau khi đăng nhập (gộp giỏ). 🟡
- [ ] Sản phẩm bị xóa/ẩn/hết hàng sau khi đã vào giỏ được xử lý và báo cho khách. 🟡

### 2.4 Checkout COD & đơn hàng
- [ ] Chưa đăng nhập bấm đặt hàng → được dẫn đăng nhập rồi quay lại đúng chỗ. 🔴
- [ ] Kiểm tra form: thiếu tên, SĐT sai định dạng, thiếu địa chỉ → báo lỗi đúng ô. 🔴
- [ ] Phí ship và tổng tiền đúng (kể cả ngưỡng miễn phí ship). 🔴
- [ ] Đặt hàng thành công → tạo `orders/{id}` với **snapshot** tên/giá, `status = pending`, `paymentMethod = COD`. 🔴
- [ ] **Bấm đặt hàng liên tiếp/bấm đúp chỉ tạo 1 đơn.** 🔴
- [ ] Giá/tồn kho thay đổi giữa lúc thêm giỏ và lúc đặt → được kiểm tra lại và báo khách. 🔴
- [ ] Lỗi mạng khi đặt → giữ dữ liệu form, báo lỗi, cho thử lại. 🟡
- [ ] Sau đặt thành công, giỏ được làm trống; trang cảm ơn hiện mã đơn. 🔴
- [ ] “Đơn hàng của tôi” chỉ hiện đơn của chính mình; chi tiết đúng; khách hủy được khi `pending`. 🔴

### 2.5 Tài khoản & trang tĩnh
- [ ] Sửa thông tin, thêm/sửa/xóa địa chỉ, chọn địa chỉ mặc định. 🟡
- [ ] Trang giới thiệu, chính sách, liên hệ hiển thị nội dung thật; liên kết footer không chết. 🟡
- [ ] Trang 404 thân thiện, có nút về trang chủ. 🟡

---

## 3. CHỨC NĂNG ADMIN

- [ ] Dashboard: số đơn theo trạng thái, doanh thu đúng với dữ liệu; đơn mới hiện realtime/badge. 🟡
- [ ] CRUD danh mục: thêm, sửa, ẩn/hiện, sắp xếp; xóa danh mục đang có sản phẩm được xử lý an toàn. 🔴
- [ ] CRUD sản phẩm: upload nhiều ảnh, biến thể, giá, giá khuyến mãi ≤ giá gốc, tồn kho ≥ 0, slug không trùng. 🔴
- [ ] Sửa sản phẩm **không làm thay đổi** các đơn cũ (nhờ snapshot). 🔴
- [ ] Quản lý đơn: lọc theo trạng thái, xem chi tiết, đổi trạng thái theo đúng luồng (`pending → confirmed → shipping → completed` hoặc `cancelled`). 🔴
- [ ] Trừ kho khi xác nhận đơn; hoàn kho khi hủy (nếu đã trừ); không cho tồn kho âm. 🔴
- [ ] In đơn hiển thị đủ thông tin giao nhận và tổng tiền COD. 🟡
- [ ] Cài đặt shop (tên, logo, hotline, phí ship, ngưỡng miễn phí) lưu xong và **client cập nhật theo**. 🔴
- [ ] Banner thêm/sắp xếp/ẩn; hiển thị đúng trên trang chủ. 🟡
- [ ] Chỉ owner thấy và dùng được mục quản lý admin phụ. 🔴
- [ ] Giao diện admin dùng được trên điện thoại. 🟡

---

## 4. GIAO DIỆN & UX

- [ ] Hiển thị tốt ở **360px, 390px, 768px, 1024px, 1440px**; không cuộn ngang. 🔴
- [ ] Vùng chạm ≥ 44px; nút chính nổi bật, màu CTA nhất quán. 🟡
- [ ] Mọi trang có đủ **loading / lỗi / rỗng**. 🔴
- [ ] Skeleton giữ đúng khung, không gây nhảy bố cục (CLS thấp). 🟡
- [ ] Thanh hành động dính đáy ở chi tiết sản phẩm và checkout (mobile) không che nội dung cuối trang. 🟡
- [ ] Animation mượt trên điện thoại tầm trung; chỉ dùng `transform/opacity`. 🟡
- [ ] Bật “Giảm chuyển động” (reduced motion) của hệ điều hành → animation giảm/tắt đúng. 🟡
- [ ] Không dính các dấu hiệu “template” trong `frontend-design.md` mục 3. 🟢
- [ ] Nội dung theo `copywriting-vi.md`: không còn chữ tiếng Anh mặc định, thuật ngữ nhất quán. 🟡

## 5. TRUY CẬP (ACCESSIBILITY)

- [ ] Dùng bàn phím: Tab qua được mọi phần tử tương tác, focus **nhìn thấy**, thứ tự hợp lý. 🟡
- [ ] Modal/Drawer: giữ focus bên trong, đóng bằng phím ESC, trả focus khi đóng. 🟡
- [ ] Ảnh có `alt`; nút chỉ có icon có `aria-label`; ô nhập có `label`. 🟡
- [ ] Tương phản chữ/nền ≥ 4.5:1. 🟡
- [ ] Không chỉ dùng màu để báo trạng thái (kèm chữ hoặc icon). 🟢

## 6. HIỆU NĂNG & SEO

- [ ] Chạy Lighthouse (mobile) cho trang chủ, danh sách, chi tiết: mục tiêu **Performance ≥ 80**, Accessibility ≥ 90, SEO ≥ 90. 🟡
- [ ] Ảnh dùng định dạng hiện đại, có kích thước phù hợp, `loading="lazy"` (trừ ảnh hero). 🟡
- [ ] Code chia theo route; admin không nằm trong bundle client. 🟡
- [ ] Số lượt đọc Firestore hợp lý (không đọc cả collection ở mỗi lần vào trang). 🟡
- [ ] Mỗi trang có `<title>`, `meta description`, Open Graph (tên, ảnh, mô tả) riêng. 🟡
- [ ] Có `favicon`, `robots.txt`; ngôn ngữ trang `lang="vi"`. 🟢

## 7. TRƯỚC KHI GO-LIVE (D3)

- [ ] **Xóa dữ liệu seed/test** (sản phẩm mẫu, đơn thử, tài khoản thử). 🔴
- [ ] Đã nhập dữ liệu thật: sản phẩm, danh mục, banner, `settings/shop` (hotline, địa chỉ, phí ship, chính sách). 🔴
- [ ] Thông tin liên hệ, chính sách đổi trả/giao hàng thật và đúng. 🔴
- [ ] Firestore rules production đã deploy; **không còn rule “mở” dùng để test**. 🔴
- [ ] Thêm tên miền chính thức vào Firebase Auth → Authorized domains. 🔴
- [ ] Đặt cảnh báo ngân sách/quota trong Firebase để tránh phát sinh chi phí ngoài ý muốn. 🟡
- [ ] Đã tạo bản sao lưu dữ liệu Firestore (export) trước khi mở bán. 🟡
- [ ] Đặt **1 đơn thật từ điện thoại** cho đến khi admin xử lý xong luồng `pending → completed`. 🔴
- [ ] Deploy Firebase Hosting; kiểm tra lại trên tên miền thật (HTTPS). 🔴

---

## 8. MẪU BÁO CÁO QA (Gemini điền)

```
## BÁO CÁO QA — {{ngày}} — bước {{mã bước}}
Tổng quan: 🔴 đạt x/y · 🟡 đạt x/y · 🟢 đạt x/y

### Đã tự kiểm tra bằng đọc code
- <mục> → ✅/❌ (file: …)

### Người dùng cần tự thử (kèm cách thử)
1. <mục> — Cách thử: …

### Lỗi phát hiện
| Mức | Mô tả | File | Đề xuất sửa |
|---|---|---|---|

### Kết luận
- [ ] Có thể giao  - [ ] Cần sửa 🔴 trước khi giao
```

> Quy tắc: còn bất kỳ mục 🔴 nào chưa đạt → **không kết luận “có thể giao”**.
