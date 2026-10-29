# SKILL: copywriting-vi — Viết nội dung tiếng Việt cho web bán hàng

> Đặt tại `skills/copywriting-vi.md`. Dùng cho mọi chữ xuất hiện trong giao diện: nút, nhãn, thông báo, mô tả sản phẩm, trang tĩnh.
> Giọng điệu cụ thể lấy từ `brief.md` (mục Brand). File này là **quy tắc chung**, giọng thương hiệu quyết định mức độ trang trọng hay thân mật.

---

## 1. NGUYÊN TẮC GỐC

1. **Chữ để giúp khách hiểu và hành động**, không để trang trí. Trước khi viết, hỏi: "khách cần biết gì, làm gì tiếp theo?"
2. **Viết từ góc nhìn khách hàng.** Gọi tên sự vật bằng thứ khách hiểu, không bằng cách hệ thống vận hành ("Đơn hàng của bạn", không phải "Order record").
3. **Cụ thể hơn khéo léo.** "Giao trong 2–3 ngày" tốt hơn "Giao hàng siêu tốc".
4. **Mỗi dòng chữ làm đúng 1 việc.** Một nút một hành động, một thông báo một thông tin.
5. **Không bịa.** Không số liệu giả, không đánh giá giả, không khẳng định y tế/công dụng chưa kiểm chứng, không so sánh sai sự thật.
6. **Nhất quán thuật ngữ** trên toàn site (xem bảng mục 3).

## 2. GIỌNG ĐIỆU

- Chọn **một** xưng hô và giữ suốt site:
  - Thân thiện, trẻ: "bạn" – "mình / shop"
  - Lịch sự, cao cấp: "quý khách" – "chúng tôi"
  - Gần gũi gia đình/đồ ăn: "bạn" – "tụi mình / nhà mình"
- Câu ngắn, động từ rõ, **viết hoa kiểu câu thường** (chỉ viết hoa chữ đầu câu và tên riêng), không viết HOA TOÀN BỘ để nhấn.
- Hạn chế: dấu chấm than liên tục, emoji dày đặc, từ sáo rỗng ("tuyệt vời nhất", "chất lượng số 1", "cực kỳ siêu"), tiếng lóng làm khó hiểu. Emoji chỉ dùng khi hợp tông thương hiệu, tối đa 1 emoji mỗi cụm.
- Không xin lỗi dài dòng ở thông báo lỗi; nói rõ chuyện gì và cách sửa.

## 3. BẢNG THUẬT NGỮ NHẤT QUÁN

| Khái niệm | Dùng | Không dùng lẫn với |
|---|---|---|
| Cart | **Giỏ hàng** | Túi, xe đẩy |
| Add to cart | **Thêm vào giỏ** | Bỏ vào giỏ, Cho vào giỏ |
| Buy now | **Mua ngay** | Đặt liền |
| Place order | **Đặt hàng** | Gửi, Xác nhận mua |
| Order | **Đơn hàng** | Hóa đơn (chỉ dùng khi thật sự là hóa đơn) |
| Checkout | **Thanh toán** (tiêu đề trang) / **Đặt hàng** (nút) | |
| COD | **Thanh toán khi nhận hàng (COD)** | Trả tiền mặt, Ship COD |
| Shipping fee | **Phí giao hàng** (hoặc "Phí ship" nếu giọng trẻ) | |
| Free shipping | **Miễn phí giao hàng** | |
| Out of stock | **Hết hàng** | Tạm hết, Sold out |
| Sale price | **Giá khuyến mãi** / nhãn **Giảm 20%** | |
| Account | **Tài khoản** | Hồ sơ |
| Log in | **Đăng nhập bằng Google** | Login |
| Log out | **Đăng xuất** | |
| Return policy | **Chính sách đổi trả** | |

> Chốt lựa chọn trong bảng này **một lần**, sau đó dùng đúng như vậy ở mọi nơi (nút, toast, email, trang tĩnh).

## 4. NÚT (CTA)

- Nút nói **đúng điều sẽ xảy ra**: động từ + đối tượng.
- Tốt: "Thêm vào giỏ", "Đặt hàng", "Lưu địa chỉ", "Xem đơn hàng", "Tiếp tục mua sắm", "Hủy đơn hàng", "Đăng nhập bằng Google".
- Tránh: "Gửi", "OK", "Click vào đây", "Xác nhận" (khi không rõ xác nhận gì).
- Nút nguy hiểm nói rõ hậu quả: "Xóa sản phẩm", không chỉ "Xóa".
- Tên hành động **giữ nguyên xuyên suốt luồng**: nút "Đặt hàng" → trạng thái "Đang đặt hàng…" → thông báo "Đã đặt hàng thành công".
- Trạng thái đang xử lý: động từ + "…" ("Đang thêm…", "Đang lưu…").

## 5. THÔNG BÁO, LỖI, TRẠNG THÁI RỖNG

### Thông báo thành công (toast, ngắn)
- "Đã thêm vào giỏ hàng."
- "Đã lưu địa chỉ."
- "Đặt hàng thành công. Mã đơn của bạn là #{{orderCode}}."
- "Đã hủy đơn hàng."

### Lỗi: nêu rõ chuyện gì + cách sửa
| Tình huống | Câu chữ |
|---|---|
| Thiếu họ tên | "Vui lòng nhập họ và tên người nhận." |
| SĐT sai | "Số điện thoại cần đủ 10 số, bắt đầu bằng số 0." |
| Chưa chọn size | "Chọn size để thêm vào giỏ." |
| Vượt tồn kho | "Chỉ còn {{n}} sản phẩm. Hãy giảm số lượng nhé." |
| Mất mạng | "Không có kết nối mạng. Dữ liệu của bạn vẫn được giữ, hãy thử lại khi có mạng." |
| Lỗi hệ thống khi đặt | "Chưa đặt được đơn hàng. Thông tin của bạn vẫn được giữ, hãy thử lại sau ít phút hoặc gọi {{hotline}}." |
| Đăng nhập thất bại | "Chưa đăng nhập được. Hãy thử lại hoặc dùng tài khoản Google khác." |
| Giá/tồn kho thay đổi | "Giá của một số sản phẩm đã thay đổi. Vui lòng xem lại đơn hàng trước khi đặt." |
| Không có quyền (admin) | "Tài khoản này không có quyền truy cập trang quản trị." |

### Trạng thái rỗng (lời mời hành động)
- Giỏ trống: "Giỏ hàng đang trống." + nút "Xem sản phẩm mới".
- Chưa có đơn: "Bạn chưa có đơn hàng nào." + nút "Bắt đầu mua sắm".
- Tìm không thấy: "Không tìm thấy sản phẩm cho “{{keyword}}”." + "Thử từ khóa khác hoặc xem tất cả danh mục."
- Danh mục chưa có hàng: "Danh mục này sắp có sản phẩm mới." + nút về trang chủ.

## 6. MÔ TẢ SẢN PHẨM

Cấu trúc gợi ý (ngắn, dễ quét):
1. **Dòng mở đầu (1–2 câu):** sản phẩm này là gì và hợp với ai/dịp nào.
2. **3–5 lợi ích** dạng gạch đầu dòng, mỗi dòng bắt đầu bằng điều khách được hưởng, sau đó mới là đặc điểm.
3. **Thông số:** chất liệu, kích thước, trọng lượng, thành phần, xuất xứ, hạn dùng... (theo ngành, lấy từ `attributes`).
4. **Hướng dẫn:** chọn size, bảo quản, sử dụng.
5. **Đổi trả & giao hàng:** liên kết tới chính sách.

Quy tắc:
- Lợi ích trước, thông số sau. Ví dụ: "Thoáng mát cả ngày nhờ vải cotton 100%" hơn là "Chất liệu: cotton 100%".
- Dùng con số thật, đơn vị Việt Nam (cm, kg, ml, g).
- Tên sản phẩm: loại + đặc điểm nhận diện + (mã/màu nếu cần). Ngắn gọn, không nhồi từ khóa.
- Không khẳng định công dụng y tế, "trị", "chữa", "cam kết 100%" nếu không có căn cứ.
- Mỗi mô tả viết khác nhau; không sao chép nguyên một đoạn cho mọi sản phẩm.

## 7. GIÁ, SỐ, NGÀY GIỜ

- Tiền: `1.250.000 ₫` (dấu chấm ngăn nghìn, ký hiệu ₫ sau số). Dùng `Intl.NumberFormat('vi-VN', { style:'currency', currency:'VND' })`.
- Giảm giá: hiện giá mới nổi bật, giá cũ gạch ngang, nhãn "-20%". Tính % từ giá thật.
- Ngày giờ: `29/10/2026`, `14:30`. Thời gian tương đối chỉ cho thông báo gần ("2 giờ trước").
- Số điện thoại hiển thị có khoảng cách: `0901 234 567`.
- Thời gian giao dự kiến nói thành khoảng: "Dự kiến giao trong 2–4 ngày làm việc".

## 8. CÂU CHỮ CHO TRANG QUAN TRỌNG

### Hero trang chủ
- Tiêu đề: nói **khách được gì** hoặc **shop khác biệt thế nào** trong một câu, không khẩu hiệu chung chung.
- Dòng phụ (tùy chọn): 1 câu bổ sung chi tiết cụ thể (chất liệu, nguồn gốc, thời gian giao).
- 1 nút chính + (tùy chọn) 1 nút phụ.
- Không dùng chữ nhồi tính từ; không dùng "Chào mừng đến với {{shop}}" làm tiêu đề.

### Điểm tin cậy (3–4 mục, ngắn)
- "Thanh toán khi nhận hàng (COD)"
- "Đổi trả trong {{n}} ngày"
- "Giao hàng toàn quốc" / "Giao nội thành trong ngày"
- "Hỗ trợ {{giờ làm việc}}: {{hotline}}"
(Chỉ ghi đúng với chính sách thật của shop trong `brief.md`.)

### Chi tiết trạng thái đơn
| Trạng thái | Nhãn khách thấy | Mô tả ngắn |
|---|---|---|
| `pending` | Chờ xác nhận | "Shop sẽ gọi xác nhận đơn của bạn sớm nhất." |
| `confirmed` | Đã xác nhận | "Đơn đang được chuẩn bị." |
| `shipping` | Đang giao | "Đơn đang trên đường đến bạn. Hãy chuẩn bị {{total}} để thanh toán khi nhận." |
| `completed` | Hoàn thành | "Cảm ơn bạn đã mua hàng." |
| `cancelled` | Đã hủy | "Đơn hàng này đã được hủy." |

### Trang tĩnh
- **Giới thiệu:** câu chuyện ngắn (vì sao có shop, giá trị, cam kết thật), 3–5 đoạn ngắn, có ảnh thật.
- **Chính sách đổi trả / giao hàng / bảo mật:** chia mục có tiêu đề rõ, câu ngắn, ví dụ cụ thể, ghi ngày cập nhật. Điền theo `brief.md`, **không tự bịa điều khoản**; nếu thiếu thông tin thì hỏi.
- **Liên hệ:** hotline, Zalo, email, địa chỉ, giờ làm việc, bản đồ (nếu có).

### Thẻ SEO
- `<title>`: "{{Tên sản phẩm / trang}} | {{Tên shop}}" (≤ 60 ký tự).
- `meta description`: 1–2 câu có lợi ích + lời mời hành động (≤ 155 ký tự).
- Mỗi trang có title và description **khác nhau**.

## 9. ADMIN

- Giọng trung tính, ngắn: "Thêm sản phẩm", "Lưu thay đổi", "Đã cập nhật trạng thái đơn".
- Xác nhận thao tác nguy hiểm: "Xóa sản phẩm “{{tên}}”? Thao tác này không thể hoàn tác."
- Nhãn trường rõ ràng, có gợi ý (placeholder) ví dụ thật: "Ví dụ: Áo thun cotton cổ tròn".
- Giải thích ngắn khi trường dễ nhầm: "Giá khuyến mãi (để trống nếu không giảm)".

## 10. KIỂM TRA TRƯỚC KHI GIAO

- [ ] Mọi chữ trên giao diện là tiếng Việt chuẩn, có dấu, đúng chính tả; không còn chữ tiếng Anh mặc định (Submit, Loading, Error...).
- [ ] Một xưng hô duy nhất xuyên suốt.
- [ ] Thuật ngữ khớp bảng mục 3.
- [ ] Nút là động từ + đối tượng; tên hành động nhất quán trong cả luồng.
- [ ] Mọi lỗi có nêu cách sửa; mọi trạng thái rỗng có nút hành động.
- [ ] Không có "Lorem ipsum", số liệu giả, đánh giá giả, khẳng định chưa kiểm chứng.
- [ ] Giá, ngày, số điện thoại đúng định dạng Việt Nam.
- [ ] Mỗi trang có `title` và `meta description` riêng.
