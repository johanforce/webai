# RULE 05 — Chức năng trang khách (Client)

> Mỗi tính năng có **Tiêu chí chấp nhận (AC)** để kiểm tra được. Xong tính năng nào thì tự đối chiếu AC rồi mới báo cáo.
> Giao diện theo `skills/frontend-design.md`; luồng và câu chữ theo `skills/ux-conversion.md` và `skills/copywriting-vi.md`. Dữ liệu theo rule 03, quyền theo rule 02 và 04.
> Phạm vi v1: **chỉ COD, bắt buộc đăng nhập Google để đặt hàng.** Không thêm mã giảm giá, đánh giá, wishlist, thanh toán online (thuộc mở rộng sau v1).

---

## 0. QUY TẮC CHUNG CHO MỌI TRANG CLIENT

- Mỗi trang có: **loading (Skeleton) / lỗi (nút "Thử lại") / rỗng (EmptyState có nút hành động)**.
- Mỗi trang đặt `<title>` và `meta description` riêng bằng `react-helmet-async`.
- Dữ liệu lấy qua hook React Query → `services/`. Không gọi Firestore trong component.
- Mobile-first; kiểm tra ở 360px.
- Văn bản hiển thị là tiếng Việt, tiền theo định dạng `1.250.000 ₫`.
- Bundle client **không** chứa code admin.

---

## 1. LAYOUT CHUNG

**Header:** thanh khuyến mãi mỏng (lấy từ `settings/shop`, có thể tắt) → logo, ô tìm kiếm, menu danh mục, tài khoản, giỏ (badge số lượng). Mobile: menu trượt, tìm kiếm mở rộng. Dính trên cùng, co lại khi cuộn.
**Footer:** thông tin shop (tên, địa chỉ, hotline, Zalo, email, giờ làm việc), liên kết chính sách (từ `settings.policies`), liên kết trang giới thiệu/liên hệ, mạng xã hội.
**Nút liên hệ nhanh** (gọi/Zalo) cố định góc dưới, **không che nút mua**.

**AC**
- [ ] Header/footer hiển thị đúng dữ liệu `settings/shop`; thiếu trường nào thì ẩn phần đó, không để trống xấu.
- [ ] Badge giỏ cập nhật tức thì khi thêm/xóa.
- [ ] Menu mobile mở/đóng mượt, đóng bằng ESC và bấm ra ngoài.

---

## 2. TRANG CHỦ `/`

Nội dung (thứ tự): Banner/hero → danh mục nổi bật → sản phẩm nổi bật (`isFeatured`) → sản phẩm mới → điểm tin cậy (COD, đổi trả, giao hàng, hotline) → giới thiệu ngắn → footer.
- Banner lấy từ `banners` (`isActive`, sắp theo `order`). Không có banner → hero mặc định theo thương hiệu.
- Danh mục: `isActive`, sắp theo `order`.
- Giới hạn: 8 sản phẩm nổi bật, 8 sản phẩm mới.

**AC**
- [ ] Thấy rõ shop bán gì và nút hành động chính trong 3 giây đầu trên mobile.
- [ ] Khối rỗng (chưa có sản phẩm/danh mục/banner) được ẩn hoặc có EmptyState hợp lý.
- [ ] Chỉ có **một** khoảnh khắc animation chính (theo skill); ảnh hero tải ưu tiên.

---

## 3. DANH SÁCH SẢN PHẨM `/san-pham`, `/danh-muc/:slug`

- **Bộ lọc:** danh mục, khoảng giá, biến thể chính (nếu ngành có), "đang giảm giá", "còn hàng".
- **Sắp xếp:** Mới nhất (mặc định), Giá thấp → cao, Giá cao → thấp.
- **Tìm kiếm:** `?q=` — chuẩn hóa bỏ dấu (rule 03 mục 6), gõ "ao thun" vẫn ra "Áo thun". Gợi ý khi gõ (tối đa 5 mục, `useDebounce` 250ms).
- Hiển thị **số kết quả**. Lưới 2 cột mobile / 3–4 cột desktop.
- Phân trang kiểu **"Xem thêm"** (12 mục/lần) bằng `limit` + `startAfter`.
- Trạng thái bộ lọc/sắp xếp/trang nằm trên **URL** (chia sẻ và quay lại được). Giữ vị trí cuộn khi quay lại từ trang chi tiết.
- Mobile: bộ lọc mở bằng **drawer đáy**.
- `ProductCard`: ảnh (tỉ lệ cố định), tên (tối đa 2 dòng), `PriceTag` (giá khuyến mãi + giá gốc gạch + % giảm), nhãn (Mới, Giảm x%, Hết hàng). Khách không đăng nhập vẫn thêm vào giỏ được.
- Danh mục có `slug` không tồn tại hoặc `isActive=false` → trang "Không tìm thấy" thân thiện.

**AC**
- [ ] Lọc/sắp xếp/tìm kiếm cho kết quả đúng; đổi bộ lọc không tải lại toàn trang.
- [ ] Không có kết quả → EmptyState gợi ý bỏ bớt bộ lọc.
- [ ] Sản phẩm `isActive=false` không bao giờ xuất hiện.
- [ ] Sản phẩm hết hàng hiển thị rõ, không ẩn mất.
- [ ] Mỗi lần "Xem thêm" chỉ đọc thêm 12 document.

---

## 4. CHI TIẾT SẢN PHẨM `/san-pham/:slug`

Thứ tự: gallery → tên → giá → biến thể → số lượng → CTA → tin cậy → mô tả/thông số (accordion) → sản phẩm liên quan (cùng danh mục, tối đa 4).
- **Gallery:** vuốt ngang, bấm phóng to; đổi ảnh theo biến thể có ảnh.
- **Biến thể (`hasVariants`):** chip theo từng `optionTypes`; giá và tồn kho đổi theo biến thể chọn; tổ hợp hết hàng làm mờ nhưng vẫn thấy.
- Chưa chọn đủ biến thể mà bấm mua → cuộn tới phần chọn và nhắc theo `copywriting-vi.md` ("Chọn size để thêm vào giỏ").
- **Số lượng:** 1 đến `min(stock, maxQtyPerItem)`; vượt thì báo rõ.
- **CTA:** "Thêm vào giỏ" và "Mua ngay" (thêm giỏ rồi vào thanh toán). Mobile: thanh hành động **dính đáy**.
- Hết hàng → CTA vô hiệu kèm lý do, gợi ý sản phẩm liên quan.
- Khối tin cậy cạnh CTA: COD, thời gian giao (`estimatedDelivery`), đổi trả.
- Thông số hiển thị từ `attributes` theo thứ tự ngành (`07-business-context.md`).
- SEO: `title` = tên + tên shop; `description` từ `shortDescription`; Open Graph có ảnh đầu tiên.
- Slug không tồn tại/đã ẩn → trang "Sản phẩm không còn" + gợi ý.

**AC**
- [ ] Chọn biến thể → giá/tồn/ảnh đổi đúng.
- [ ] Thêm vào giỏ không chuyển trang; có phản hồi (mini-cart/toast + badge nảy).
- [ ] Không thêm vượt tồn kho.
- [ ] Thanh CTA dính đáy không che nội dung cuối trang.

---

## 5. GIỎ HÀNG `/gio-hang` (+ mini-cart)

**Kho dữ liệu:** `store/cartStore.ts` (zustand) — mục giỏ chỉ lưu `{productId, variantId, qty}`; **tên, giá, ảnh, tồn lấy mới từ Firestore** khi hiển thị (React Query) để luôn đúng.
- Khách chưa đăng nhập: lưu `localStorage` (bọc `try/catch`). Đã đăng nhập: đồng bộ `carts/{uid}`; khi đăng nhập thì **gộp** giỏ tạm vào giỏ tài khoản (rule 02 mục 6).
- Mỗi dòng: ảnh, tên, biến thể, `QuantityStepper`, giá, xóa (có **Hoàn tác** ngắn, không hộp xác nhận).
- Tóm tắt: tạm tính, phí giao hàng, tổng. Thanh tiến độ "mua thêm X ₫ để miễn phí giao hàng" khi `freeShipThreshold` có giá trị.
- Xử lý sản phẩm đã ẩn/xóa/hết hàng/giá đổi: đánh dấu dòng, báo cho khách, không cho đặt dòng hết hàng.
- Nút "Tiến hành đặt hàng": chưa đăng nhập → đăng nhập Google rồi quay lại `/thanh-toan`.

**AC**
- [ ] Thêm/tăng/giảm/xóa chạy đúng, tổng tiền đúng, không vượt tồn.
- [ ] Tải lại trang hoặc đăng nhập xong, giỏ vẫn còn và được gộp đúng.
- [ ] Giỏ trống → EmptyState có nút "Xem sản phẩm mới".

---

## 6. THANH TOÁN COD `/thanh-toan` (cần đăng nhập)

**Tối đa 2 bước:** (1) Thông tin nhận hàng → (2) Xem lại & Đặt hàng. Tóm tắt đơn luôn xem được (có thể thu gọn).

**Thông tin nhận hàng:** Họ tên, SĐT, Tỉnh/TP, Quận/Huyện, Phường/Xã, Địa chỉ chi tiết, Ghi chú (tùy chọn). Tự điền từ tài khoản; cho **chọn địa chỉ đã lưu** hoặc nhập mới và tick "Lưu địa chỉ này". Kiểm tra bằng zod khi rời ô (SĐT `^(0|\+84)\d{9}$`, chuẩn hóa về `0xxxxxxxxx`).

**Thanh toán:** chỉ **COD**, hiển thị đã chọn sẵn: "Thanh toán bằng tiền mặt khi nhận hàng".

**Thuật toán tạo đơn (`orderService.createOrder`) — làm đúng thứ tự:**
1. Đọc **mới** từng sản phẩm trong giỏ từ Firestore (không tin dữ liệu cache cũ).
2. Kiểm tra: còn `isActive`, còn đủ tồn (theo biến thể), tính lại giá hiệu lực. Nếu có thay đổi so với giỏ → **dừng, báo rõ, cho khách xem lại** rồi mới đặt.
3. Tạo `items` dạng snapshot (`name`, `variantLabel`, `image`, `unitPrice`, `qty`, `lineTotal`).
4. Tính `subtotal`; `shippingFee` = 0 nếu `subtotal >= freeShipThreshold` (khi có), ngược lại `settings.shippingFee`; `total = subtotal + shippingFee`. Tất cả là **số nguyên**.
5. Sinh `code` (rule 03 mục 5), ghi `orders` với đúng tập khóa trong rule 04: `status:'pending'`, `paymentMethod:'COD'`, `stockDeducted:false`, `createdAt: serverTimestamp()`.
6. Thành công → xóa giỏ (store + `carts/{uid}`), lưu địa chỉ nếu khách tick, chuyển tới `/dat-hang-thanh-cong/:orderId`.
7. Lỗi → **giữ nguyên** dữ liệu đã nhập, hiện thông báo và cho thử lại.

**Chống bấm đúp:** khóa nút "Đặt hàng" ngay khi bấm (trạng thái "Đang đặt hàng…"), dùng khóa chống gửi lặp trong hàm tạo đơn.

**AC**
- [ ] Chưa đăng nhập vào `/thanh-toan` → đăng nhập rồi quay lại đúng trang.
- [ ] Bấm đặt hàng nhiều lần liên tiếp chỉ tạo **một** đơn.
- [ ] Đơn trong Firestore có đủ snapshot, `total = subtotal + shippingFee`, đúng ngưỡng miễn phí giao hàng.
- [ ] Giá/tồn đổi giữa lúc thêm giỏ và lúc đặt → khách được báo và xem lại.
- [ ] Lỗi mạng khi đặt → không mất dữ liệu form.

---

## 7. ĐẶT HÀNG THÀNH CÔNG `/dat-hang-thanh-cong/:orderId`

Hiển thị: mã đơn, tóm tắt, địa chỉ, **số tiền cần chuẩn bị khi nhận hàng**, thời gian giao dự kiến, bước tiếp theo ("Shop sẽ gọi xác nhận…"). Nút: "Xem đơn hàng", "Tiếp tục mua sắm". Một khoảnh khắc ăn mừng nhẹ (một lần).
- Chỉ chủ đơn xem được (rule bắt buộc); người khác thấy trang 404/403.

**AC**
- [ ] Mã đơn khớp với đơn trong Firestore; tải lại trang vẫn xem được.

---

## 8. TÀI KHOẢN `/tai-khoan/*` (cần đăng nhập)

- **Hồ sơ:** xem/sửa tên hiển thị, SĐT; hiện email Google (không sửa).
- **Địa chỉ:** thêm/sửa/xóa (≤ 10), đặt mặc định.
- **Đơn của tôi:** danh sách (mới nhất trước, phân trang 10 đơn/lần), lọc theo trạng thái. Truy vấn luôn có `where('userId','==',uid)`.
- **Chi tiết đơn:** thông tin giao nhận, các dòng hàng (snapshot), tiền, **dòng thời gian trạng thái**, liên hệ shop; nút **Hủy đơn** chỉ khi `pending` (hỏi xác nhận + lý do tùy chọn), gửi đúng tập khóa cho phép ở rule 04. Theo dõi realtime (`onSnapshot`) khi đang xem một đơn.
- Đăng xuất.

**AC**
- [ ] Khách chỉ thấy đơn của mình; mở URL đơn người khác → không truy cập được.
- [ ] Hủy đơn `pending` thành công, đơn đã `confirmed` trở đi không còn nút hủy.
- [ ] Thêm/sửa/xóa địa chỉ cập nhật `users/{uid}` đúng, địa chỉ mặc định hoạt động.

---

## 9. TRANG TĨNH & KHÁC

- `/gioi-thieu` (từ `settings.about`), `/chinh-sach/:slug` (từ `settings.policies[slug]`: `doi-tra`, `giao-hang`, `bao-mat`), `/lien-he` (thông tin liên hệ thật + liên kết gọi/Zalo/bản đồ; **không có form gửi thư** ở v1).
- **Hiển thị văn bản do admin nhập dưới dạng text an toàn** (tách đoạn theo dòng trống). **Không** dùng `dangerouslySetInnerHTML`.
- `/dang-nhap`: nút lớn "Đăng nhập bằng Google"; xử lý trình duyệt trong ứng dụng (rule 02 mục 2); có `returnTo`.
- `*`: 404 thân thiện, nút về trang chủ.
- SEO: `lang="vi"`, favicon, `robots.txt`, Open Graph mặc định.

**AC**
- [ ] Nội dung trang tĩnh lấy từ cài đặt shop; chưa có nội dung → ẩn liên kết ở footer, không hiện trang rỗng.
- [ ] Không còn liên kết chết trong header/footer.

---

## 10. KHÔNG LÀM TRONG V1

Mã giảm giá · đánh giá/nhận xét · wishlist · thanh toán online (QR/VNPay/MoMo) · tính phí ship theo vùng · thông báo email/Zalo · đa ngôn ngữ · SSR. Có ý tưởng hay thì **đề xuất**, chờ người dùng đồng ý.

## 11. TIÊU CHÍ HOÀN THÀNH CẢ NHÓM (đối chiếu `GEN_PROGRESS.md` C4.1–C4.7)

- [ ] Khách đi trọn luồng: trang chủ → sản phẩm → giỏ → đăng nhập → đặt COD → xem đơn, trên điện thoại 360px.
- [ ] Đủ loading/lỗi/rỗng ở mọi trang.
- [ ] Không component nào import `firebase/firestore`; mọi truy vấn có `limit` và khớp rules.
