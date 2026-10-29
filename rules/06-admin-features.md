# RULE 06 — Chức năng trang Admin

> Chỉ vào được khi có quyền admin (rule 02): guard ở client **và** rules ở server (rule 04).
> Giao diện theo `skills/frontend-design.md` mục 11 (bản điềm tĩnh): rõ ràng, nhanh, ít phân tâm, **dùng tốt trên điện thoại** vì chủ shop hay duyệt đơn bằng điện thoại.
> Bundle admin tách riêng, chỉ tải khi vào `/admin`.

---

## 0. QUY TẮC CHUNG

- Layout: sidebar (thu thành menu dưới/ngăn kéo trên mobile), header có tên shop, tài khoản, nút về cửa hàng.
- Mọi bảng: tiêu đề dính, tìm kiếm/lọc, phân trang (20 dòng/lần, `limit` + `startAfter`), loading/lỗi/rỗng.
- Mọi form: kiểm tra bằng zod, báo lỗi theo ô, nút lưu có trạng thái đang xử lý, **cảnh báo khi rời trang chưa lưu**, toast thành công/thất bại theo `copywriting-vi.md` mục 9.
- Thao tác nguy hiểm (xóa, hủy đơn) có hộp xác nhận nêu rõ hậu quả.
- Mọi ghi dữ liệu qua `services/`; ghi `updatedAt: serverTimestamp()`.
- Nút/menu chỉ dành cho owner (Quản trị viên) **ẩn với staff**; rules vẫn là lớp bảo vệ thật.

---

## 1. DASHBOARD `/admin`

Hiển thị: số đơn theo trạng thái (đặc biệt **Chờ xác nhận**), doanh thu (đơn `completed`) hôm nay / 7 ngày / 30 ngày, danh sách 5 đơn mới nhất, sản phẩm sắp hết hàng (`stock ≤ 5`), số sản phẩm đang bán.
- Dữ liệu đơn lấy trong khoảng 30 ngày gần nhất với `limit(500)`; tính tổng ở client. Nếu vượt giới hạn thì ghi chú "dữ liệu hiển thị tối đa 500 đơn gần nhất".
- **Đơn mới realtime:** `onSnapshot` trên đơn `status == 'pending'`; hiện badge ở menu "Đơn hàng", cập nhật số trong `document.title` (ví dụ `(3) Quản trị`), tùy chọn âm báo (mặc định tắt, bật bằng cài đặt trình duyệt, cần khách tương tác trước).
- Biểu đồ đơn giản (cột theo ngày) — tải lười, có thể vẽ bằng CSS.

**AC**
- [ ] Số liệu khớp dữ liệu thật; hạn chế đọc (không đọc cả collection).
- [ ] Có đơn mới → badge và tiêu đề cập nhật không cần tải lại.

---

## 2. SẢN PHẨM `/admin/san-pham`, `/moi`, `/:id`

**Danh sách:** ảnh nhỏ, tên, danh mục, giá, tồn, trạng thái (đang bán/ẩn), nút Sửa/Ẩn-Hiện/Xóa. Lọc theo danh mục/trạng thái, tìm theo tên (bỏ dấu), sắp xếp.

**Form sản phẩm** (chia nhóm):
1. **Thông tin:** tên, `slug` (tự sinh từ tên, sửa được, kiểm tra trùng → thêm hậu tố), danh mục, mô tả ngắn, mô tả đầy đủ, đơn vị.
2. **Ảnh:** tải nhiều ảnh (`ImageUploader` → Cloudinary), kéo/chọn đổi thứ tự, ảnh đầu là ảnh chính, tối đa 10, xóa khỏi danh sách (không xóa trên Cloudinary). Giới hạn ≤ 5MB, jpg/png/webp. Hiện tiến độ tải và lỗi từng ảnh.
3. **Giá & kho:** giá gốc, giá khuyến mãi (để trống nếu không giảm; phải `< giá gốc`), tồn kho. Số nguyên VND, hiển thị có dấu phân cách khi gõ.
4. **Biến thể** (nếu ngành bật — xem `07-business-context.md`): chọn loại tùy chọn (size/màu/…), nhập các giá trị, **tự sinh các tổ hợp**, mỗi tổ hợp có SKU, giá riêng (tùy chọn), tồn kho, ảnh (tùy chọn). Tối đa 50.
5. **Thuộc tính đặc thù ngành** (`attributes`): các trường theo `07-business-context.md`.
6. **Hiển thị:** đang bán (`isActive`), nổi bật (`isFeatured`).

Hành vi:
- Tự cập nhật `nameSearch` (đã bỏ dấu, chữ thường) mỗi khi lưu; nếu quy mô > 200 sản phẩm thì cập nhật cả `searchKeywords`.
- **Sửa sản phẩm không đổi đơn cũ** (đơn là snapshot).
- Xóa sản phẩm: hộp xác nhận; khuyến nghị **Ẩn** thay vì xóa; chỉ owner/staff có quyền xóa theo rules, đơn cũ vẫn giữ nguyên.
- Nút "Xem trên cửa hàng" mở trang chi tiết.

**AC**
- [ ] Tạo được sản phẩm đủ ảnh, biến thể, kho; hiển thị đúng ở client.
- [ ] Giá khuyến mãi ≥ giá gốc hoặc tồn kho âm → bị chặn với thông báo rõ.
- [ ] Slug không trùng; ẩn sản phẩm → khách không thấy.
- [ ] Rời form khi chưa lưu → có cảnh báo.

---

## 3. DANH MỤC `/admin/danh-muc`

- Thêm/sửa: tên, slug (tự sinh), ảnh, thứ tự (`order`), bật/tắt.
- Đổi thứ tự bằng kéo thả hoặc nút lên/xuống (cập nhật `order`).
- Hiển thị số sản phẩm trong mỗi danh mục (đếm bằng truy vấn `count()` hoặc đọc có giới hạn).
- **Không xóa danh mục còn sản phẩm:** kiểm tra `products where categoryId == id limit 1`; còn thì báo "Danh mục đang có sản phẩm. Hãy chuyển sản phẩm sang danh mục khác trước." Có thể **ẩn** thay vì xóa.

**AC**
- [ ] CRUD danh mục chạy đúng; thứ tự phản ánh ở client; xóa danh mục có sản phẩm bị chặn.

---

## 4. ĐƠN HÀNG `/admin/don-hang`, `/:id`

**Danh sách:** tab/lọc theo trạng thái (mặc định **Chờ xác nhận**), tìm theo mã đơn/SĐT/tên, hiển thị: mã, thời gian, khách, tổng tiền, trạng thái, **realtime** (`onSnapshot`) cho tab đang mở; phân trang.

**Chi tiết đơn:** thông tin khách và địa chỉ (SĐT bấm để gọi `tel:`, nút sao chép), các dòng hàng (snapshot), tạm tính, phí ship, tổng COD cần thu, ghi chú, `statusHistory`, nút **In đơn** (bản in gọn: mã đơn, người nhận, hàng, tổng thu COD, ghi chú).

**Kiểm tra đối chiếu (bắt buộc, theo rule 04 mục 3) — hiển thị cảnh báo đỏ khi:**
- `subtotal ≠ Σ lineTotal` hoặc `lineTotal ≠ unitPrice × qty`;
- `unitPrice` thấp hơn giá hiện tại của sản phẩm (hiển thị chênh lệch; chỉ thông tin nếu shop có đổi giá hợp lệ);
- `shippingFee` khác giá trị tính từ `settings/shop`.
Cảnh báo nhắc admin xác nhận lại với khách qua điện thoại trước khi duyệt.

**Chuyển trạng thái (chỉ theo luồng hợp lệ):**
| Từ | Sang | Việc cần làm |
|---|---|---|
| `pending` | `confirmed` | **Trừ kho bằng `runTransaction`**: đọc tồn từng dòng → nếu thiếu thì dừng, báo mặt hàng nào thiếu → trừ → `stockDeducted=true` |
| `confirmed` | `shipping` | Ghi lịch sử |
| `shipping` | `completed` | Ghi lịch sử; (tùy chọn) tăng `soldCount` |
| `pending`/`confirmed`/`shipping` | `cancelled` | Nhập lý do; nếu `stockDeducted=true` thì **cộng lại kho** (transaction) và đặt `stockDeducted=false`; `cancelledBy:'admin'` |
- `completed` là trạng thái cuối, không chuyển tiếp.
- Mỗi lần đổi: thêm `{status, at, by: email admin}` vào `statusHistory`, cập nhật `updatedAt`.
- Nếu khách đã hủy đơn (`cancelledBy:'customer'`) thì hiển thị rõ lý do.

**AC**
- [ ] Đơn mới của khách xuất hiện realtime ở tab "Chờ xác nhận".
- [ ] Xác nhận đơn trừ đúng kho; thiếu hàng thì không xác nhận được và báo rõ mặt hàng nào.
- [ ] Hủy đơn đã trừ kho hoàn lại đúng kho; tồn kho không bao giờ âm.
- [ ] Không chuyển trạng thái sai luồng được (nút bị ẩn/vô hiệu).
- [ ] Đơn có số liệu bất thường hiển thị cảnh báo đỏ.
- [ ] In đơn hiển thị đủ thông tin giao nhận và tổng thu COD.

---

## 5. BANNER `/admin/banner`

- Thêm/sửa: ảnh (tải Cloudinary; gợi ý tỉ lệ cho desktop/mobile), tiêu đề, phụ đề, liên kết nội bộ (chọn từ danh mục/sản phẩm hoặc nhập đường dẫn bắt đầu bằng `/`), thứ tự, bật/tắt.
- Xem trước dạng thu nhỏ. Không cho nhập liên kết ngoài (tránh chuyển hướng độc hại).

**AC**
- [ ] Banner bật hiển thị đúng thứ tự ở trang chủ; tắt thì biến mất.

---

## 6. CÀI ĐẶT SHOP `/admin/cai-dat`

Các nhóm (lưu vào `settings/shop`):
- **Thông tin shop:** tên, slogan, logo (tải ảnh), hotline, Zalo, email, Facebook, địa chỉ, giờ làm việc.
- **Giao hàng:** phí giao hàng cố định, ngưỡng miễn phí (có thể tắt), thời gian giao dự kiến, số lượng tối đa mỗi mặt hàng.
- **Nội dung trang:** giới thiệu (`about`), các chính sách (`policies`: đổi trả, giao hàng, bảo mật; thêm bớt mục). Soạn bằng ô văn bản nhiều dòng, **lưu dạng text thuần**.
- **Dữ liệu mẫu:** nút "Nạp dữ liệu mẫu" / "Xóa dữ liệu mẫu" (chỉ hiện khi môi trường dev hoặc owner; xóa theo dấu hiệu seed ở rule 03 mục 8).

**AC**
- [ ] Lưu xong, trang client cập nhật theo (sau khi làm mới cache; React Query `staleTime` danh mục/cài đặt 30 phút — có nút/hành động làm mới hoặc tự `invalidate` sau khi lưu).
- [ ] Không có bí mật nào nằm trong `settings/shop`.
- [ ] Phí ship và ngưỡng miễn phí áp dụng đúng ở checkout.

---

## 7. QUẢN TRỊ VIÊN `/admin/quan-tri-vien` (chỉ owner)

- Danh sách admin phụ (`admins`), thêm bằng Gmail (kiểm tra định dạng, chuyển chữ thường, chống trùng), xóa (hộp xác nhận). Không xóa được chính owner.
- Ghi chú hiển thị: "Người này cần đăng nhập bằng đúng Gmail đã nhập để có quyền quản trị."
- Staff truy cập đường dẫn này → trang 403.

**AC**
- [ ] Owner thêm một Gmail → người đó vào được `/admin` nhưng không vào được trang này.
- [ ] Xóa admin phụ → người đó mất quyền ở lần tải lại.

---

## 8. HIỆU NĂNG & AN TOÀN ADMIN

- Không tải cả collection: mọi danh sách có `limit`; `onSnapshot` chỉ dùng cho danh sách đơn đang xem, badge đơn chờ và chi tiết đơn.
- Dọn listener khi rời trang (`unsubscribe`).
- Không `dangerouslySetInnerHTML` với nội dung do người dùng/admin nhập.
- Ghi dữ liệu kiểm tra kiểu: tiền và tồn kho là **số nguyên**; không để `NaN`.
- Lỗi `permission-denied` hiển thị thông báo quyền rõ ràng, không treo màn hình.

## 9. TIÊU CHÍ HOÀN THÀNH CẢ NHÓM (đối chiếu `GEN_PROGRESS.md` C3.1–C3.4, C5.1–C5.2)

- [ ] Tạo ≥ 5 sản phẩm thật qua giao diện admin, hiển thị đúng ở client.
- [ ] Xử lý trọn một đơn: `pending → confirmed → shipping → completed` (kho trừ đúng) và một đơn bị hủy (kho hoàn đúng).
- [ ] Owner thêm/xóa được admin phụ; staff không vào được trang quản trị viên.
- [ ] Dùng được thoải mái trên điện thoại 360px.
- [ ] Mọi trang đủ loading/lỗi/rỗng; có cảnh báo khi rời form chưa lưu.
