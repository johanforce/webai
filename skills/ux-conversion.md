# SKILL: ux-conversion — Tối ưu chuyển đổi, mobile-first

> Đặt tại `skills/ux-conversion.md`. Dùng cùng `frontend-design.md` khi làm các bước C4.x (client) và C5.3.
> Mục tiêu: **khách vào → tìm thấy món → tin tưởng → đặt hàng COD thành công, càng ít bước và ít do dự càng tốt.**
> Bối cảnh: khách Việt Nam, phần lớn dùng điện thoại, mạng có thể chậm, chưa quen tin shop online lạ, thanh toán khi nhận (COD).

---

## 1. NGUYÊN TẮC GỐC

1. **Mỗi màn hình có đúng 1 hành động chính.** Hành động chính dùng màu CTA nổi nhất; hành động phụ dùng nút phụ/ghost.
2. **Giảm ma sát:** bỏ mọi trường, bước, popup không cần thiết cho việc mua.
3. **Giảm lo lắng:** khách mua online thường sợ: hàng không đúng mô tả, bị lừa, không đổi trả được. Giải quyết ngay tại nơi họ do dự, không dồn xuống cuối trang.
4. **Phản hồi tức thì:** mọi thao tác phải có phản hồi thấy được trong < 100ms (đổi trạng thái nút, toast, skeleton).
5. **Dữ liệu thật, không dối khách:** không đồng hồ đếm ngược giả, không "chỉ còn 2 sản phẩm" khi sai thật. Khan hiếm chỉ hiện khi `stock` thật thấp.

## 2. MOBILE-FIRST

- Thiết kế và kiểm tra ở **360–390px trước**. Thao tác chính nằm trong tầm ngón cái (nửa dưới màn hình).
- Vùng chạm **≥ 44×44px**, khoảng cách giữa nút ≥ 8px.
- **Thanh hành động dính đáy** ở trang chi tiết (Thêm vào giỏ / Mua ngay) và checkout (Đặt hàng + tổng tiền).
- Bộ lọc, sắp xếp, chọn biến thể mở bằng **drawer đáy** (bottom sheet), không dùng dropdown nhỏ.
- Bàn phím phù hợp: `inputmode="tel"` cho SĐT, `autocomplete` đúng (`name`, `tel`, `street-address`) để trình duyệt tự điền.
- Nút gọi nhanh: `tel:` cho hotline; nút Zalo/Messenger nếu shop có, đặt cố định góc dưới, **không che nút mua**.
- Ảnh: webp, kích thước theo màn hình, `loading="lazy"`; mục tiêu tải trang đầu < 3s trên 4G.
- Tránh: hover-only (không có trên điện thoại), popup chiếm toàn màn hình ngay khi vào, carousel tự chạy che nội dung.

## 3. HÀNH TRÌNH MUA HÀNG

```
Trang chủ → Danh sách → Chi tiết → Giỏ → Checkout → Cảm ơn → Theo dõi đơn
```
Mỗi bước có một việc phải làm tốt:

### 3.1 Trang chủ: "mình ở đúng chỗ rồi"
- Trong **3 giây đầu** khách phải biết: shop bán gì, cho ai, vì sao nên mua ở đây.
- Hero có 1 CTA rõ ("Xem bộ sưu tập", "Mua ngay") dẫn thẳng tới sản phẩm.
- Danh mục dễ chạm (ảnh + tên). Sản phẩm nổi bật/mới nằm ngay dưới hero.
- Khối **tin cậy** ngắn: "Thanh toán khi nhận hàng (COD)", "Đổi trả trong X ngày", "Giao toàn quốc", hotline thật.

### 3.2 Danh sách sản phẩm: "tìm nhanh"
- Hiện **số kết quả**, lọc theo danh mục/giá/biến thể chính, sắp xếp (Mới nhất, Giá tăng/giảm).
- Thẻ sản phẩm gồm: ảnh, tên (tối đa 2 dòng), giá (giá sale + giá gốc gạch + % giảm), nhãn (Mới/Sale/Hết hàng). Không nhồi quá nhiều thông tin.
- Tìm kiếm có gợi ý, **chịu được gõ không dấu** (chuẩn hóa tiếng Việt: "ao thun" khớp "áo thun").
- Không có kết quả → gợi ý bỏ bớt bộ lọc hoặc xem danh mục khác, kèm nút.
- Giữ nguyên vị trí cuộn và bộ lọc khi khách bấm vào sản phẩm rồi quay lại.

### 3.3 Chi tiết sản phẩm: "đủ thông tin để quyết định"
Thứ tự từ trên xuống: gallery → tên → giá → biến thể → số lượng → CTA → tin cậy → mô tả/thông số → sản phẩm liên quan.
- **Gallery:** vuốt ngang, bấm phóng to, ít nhất 3 ảnh nếu có; ảnh đổi theo màu chọn.
- **Biến thể:** hiển thị dạng chip, **chọn mặc định** biến thể còn hàng; biến thể hết hàng bị làm mờ nhưng vẫn thấy, không biến mất.
- Nếu chưa chọn biến thể mà bấm mua → **cuộn tới và nhấn mạnh phần chọn** kèm câu "Chọn size để tiếp tục", không báo lỗi chung chung.
- **Tin cậy ngay cạnh CTA:** "Thanh toán khi nhận hàng", "Được kiểm tra hàng" (nếu shop cho), "Đổi trả X ngày", thời gian giao dự kiến.
- Tình trạng còn hàng rõ ràng; hết hàng thì CTA đổi thành trạng thái vô hiệu kèm lý do, không ẩn.
- Mô tả chia khối ngắn (accordion): Mô tả · Thông số · Hướng dẫn chọn size/sử dụng · Đổi trả & giao hàng. Đưa lợi ích lên trước thông số kỹ thuật.

### 3.4 Giỏ hàng: "xem lại, không lạc"
- **Thêm vào giỏ không đẩy khách sang trang khác.** Hiện mini-cart/toast với 2 nút: "Xem giỏ hàng" và "Tiếp tục mua".
- Mỗi dòng: ảnh, tên, biến thể, số lượng (bước +/−), giá, xoá. Xoá có **hoàn tác** ngắn thay vì hộp xác nhận.
- Hiện **tạm tính, phí ship, tổng** ngay trong giỏ; không để khách bất ngờ ở bước cuối.
- Thanh tiến độ miễn phí ship: "Mua thêm 120.000 ₫ để được miễn phí ship" (chỉ khi shop có ngưỡng).
- Kiểm tra tồn kho và cảnh báo sớm nếu số lượng vượt tồn.
- Giỏ lưu theo tài khoản (`carts/{uid}`); khi đăng nhập xong **gộp** giỏ tạm vào giỏ tài khoản.

### 3.5 Checkout COD: "ít nhất, nhanh nhất"
- **Tối đa 2 bước** (hoặc 1 trang dài trên mobile): (1) Thông tin nhận hàng → (2) Xem lại và Đặt hàng.
- Thanh tiến trình đơn giản; tóm tắt đơn (có thể thu gọn) luôn truy cập được.
- Đăng nhập Google **trước khi vào checkout** hoặc ngay tại checkout với 1 nút lớn, giải thích lý do ngắn: "Đăng nhập để theo dõi đơn hàng của bạn". Sau đăng nhập, quay lại đúng chỗ đang dở.
- Tự điền từ tài khoản: tên (từ Google), SĐT và địa chỉ đã lưu lần trước. Cho **chọn địa chỉ đã lưu**.
- Chỉ hỏi những gì cần để giao hàng: Họ tên, SĐT, Tỉnh/TP, Quận/Huyện, Phường/Xã, Địa chỉ chi tiết, Ghi chú (không bắt buộc). Không hỏi ngày sinh, giới tính, v.v.
- **Kiểm tra từng ô khi rời ô (on blur)**, lỗi hiện ngay dưới ô, bằng ngôn ngữ dễ hiểu. SĐT: 10 số, đầu 0 (hoặc +84).
- Phương thức thanh toán chỉ có **COD**, hiển thị như một lựa chọn đã chọn sẵn, mô tả rõ: "Thanh toán bằng tiền mặt khi nhận hàng".
- Nút cuối: **"Đặt hàng"** kèm tổng tiền. Có trạng thái đang xử lý, vô hiệu hóa nút để **chống bấm đúp tạo 2 đơn**.
- Nếu lỗi mạng/lỗi ghi dữ liệu: giữ nguyên dữ liệu đã nhập, báo rõ và cho thử lại. Không bắt nhập lại từ đầu.
- Trước khi tạo đơn, **kiểm tra lại giá và tồn kho từ Firestore**; nếu có thay đổi, báo khách rõ ràng rồi cho xác nhận lại.

### 3.6 Sau đặt hàng: "yên tâm và quay lại"
- Trang cảm ơn: mã đơn, tóm tắt, địa chỉ, tổng tiền COD cần chuẩn bị, thời gian giao dự kiến, bước tiếp theo ("Shop sẽ gọi xác nhận trong vòng …").
- Nút: "Xem đơn hàng", "Tiếp tục mua sắm". Có thể gợi ý sản phẩm liên quan.
- Trang đơn hàng có **dòng thời gian trạng thái** (Chờ xác nhận → Đã xác nhận → Đang giao → Hoàn thành / Đã hủy) và thông tin liên hệ shop.
- Cho khách **hủy đơn** khi đơn còn `pending`.

## 4. NIỀM TIN (ĐẶC BIỆT QUAN TRỌNG VỚI COD VÀ SHOP MỚI)

- Footer và trang liên hệ có **thông tin thật**: tên shop, địa chỉ, hotline, giờ làm việc, link mạng xã hội.
- Chính sách đổi trả, giao hàng, bảo mật dễ tìm (liên kết ở footer và gần CTA).
- Ảnh sản phẩm **thật**, nhiều góc; có ảnh thực tế/đóng gói nếu được.
- Bằng chứng xã hội chỉ dùng khi **có thật** (số đơn đã bán, nhận xét thật). Chưa có thì **không bịa**.
- Hiển thị rõ **tổng chi phí trước khi đặt**: giá, ship, không phụ phí ẩn.
- Tên miền/biểu tượng ổ khóa (HTTPS) do Firebase Hosting tự có.

## 5. XỬ LÝ TRẠNG THÁI ĐẶC BIỆT

| Tình huống | Cách xử lý |
|---|---|
| Sản phẩm hết hàng | Hiển thị "Hết hàng", vô hiệu CTA, gợi ý sản phẩm tương tự, (tuỳ chọn) nút "Báo khi có hàng" ở phiên bản sau |
| Giỏ trống | Empty state có nút dẫn tới sản phẩm nổi bật |
| Chưa đăng nhập vào trang cần đăng nhập | Chuyển tới đăng nhập, sau đó **quay lại đúng trang** |
| Mất mạng | Banner "Bạn đang offline", giữ dữ liệu form, tự thử lại khi có mạng |
| Đăng nhập Google thất bại/đóng popup | Báo ngắn gọn, giữ nguyên trang, cho thử lại |
| Tải chậm | Skeleton đúng khung; không để màn hình trắng |
| Link sản phẩm đã xoá/ẩn | Trang "Sản phẩm không còn", gợi ý danh mục liên quan |

## 6. HIỆU NĂNG LÀ MỘT PHẦN CỦA CHUYỂN ĐỔI

- Chia nhỏ code theo route (`React.lazy` + `Suspense`), admin tách khỏi bundle client.
- Phân trang hoặc "xem thêm" khi lấy sản phẩm từ Firestore (dùng `limit` + `startAfter`), **không tải toàn bộ collection**.
- Lưu cache danh mục/cài đặt shop (ít thay đổi) để giảm số lần đọc Firestore.
- Ảnh hero tải ưu tiên; các ảnh khác lazy.
- Font: tải subset cần dùng, `font-display: swap`.

## 7. ĐO LƯỜNG ĐƠN GIẢN (TÙY CHỌN, SAU KHI CHẠY ỔN)

Theo dõi các mốc: xem sản phẩm → thêm giỏ → vào checkout → đặt thành công. Chỗ rơi nhiều nhất là chỗ cần cải thiện đầu tiên. Có thể dùng Firebase Analytics (GA4) cho bản base.

## 8. CHECKLIST DUYỆT NHANH (UX)

- [ ] Mỗi màn hình có 1 CTA chính rõ ràng.
- [ ] Từ trang chủ tới đặt hàng thành công ≤ 5 lần chạm trong trường hợp tốt nhất.
- [ ] Giá, ship, tổng tiền rõ trước khi đặt; không phụ phí ẩn.
- [ ] Biến thể chưa chọn → hướng dẫn, không báo lỗi mơ hồ.
- [ ] Chống bấm đúp khi đặt hàng; lỗi không làm mất dữ liệu đã nhập.
- [ ] Trang cảm ơn có mã đơn và bước tiếp theo.
- [ ] Mobile 360px: thao tác chính trong tầm ngón cái, không cuộn ngang.
- [ ] Có thông tin liên hệ và chính sách thật, dễ tìm.
