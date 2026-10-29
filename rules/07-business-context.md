# RULE 07 — Bối cảnh business theo ngành hàng

> **File này khác các rule còn lại: nó được SINH RA cho từng dự án** từ `brief.md` đã duyệt (bước B5–B6 trong `GEN_PROGRESS.md`). Bản dưới đây là **mẫu** với các `{{...}}` cần điền.
> Quy tắc điền: lấy từ `brief.md`; thiếu thì **hỏi**, không bịa. Điền xong **không còn `{{` nào**.
> Ưu tiên khi mâu thuẫn: `PROJECT_CONFIG.md` > `brief.md` > file này > các rule khác về nội dung/ngành (rule kỹ thuật 01–04 không bị file này thay đổi).

---

## 1. TỔNG QUAN

| Mục | Giá trị |
|---|---|
| Tên shop | {{SHOP_NAME}} |
| Slogan | {{SLOGAN}} |
| Ý tưởng kinh doanh | {{BUSINESS_IDEA}} |
| Ngành hàng | {{INDUSTRY}} |
| Điểm khác biệt (USP) | {{USP}} |
| Gmail admin gốc | {{ADMIN_EMAIL}} |
| Quy mô sản phẩm dự kiến | {{PRODUCT_COUNT}} (quyết định cách tìm kiếm ở rule 03 mục 6) |

## 2. KHÁCH HÀNG MỤC TIÊU

- Nhóm chính: {{TARGET_CUSTOMER}} (tuổi, giới, thu nhập, nơi ở)
- Thiết bị chủ yếu: điện thoại (mặc định) — {{DEVICE_NOTE}}
- Họ quan tâm điều gì khi mua: {{BUYING_FACTORS}} (giá, chất lượng, nguồn gốc, tốc độ giao, thương hiệu…)
- Họ lo lắng điều gì: {{CONCERNS}} (hàng không đúng mô tả, khó đổi trả, không tin shop mới…) → giải quyết ở các khối tin cậy, theo `skills/ux-conversion.md` mục 4.

## 3. DANH MỤC SẢN PHẨM

| # | Tên danh mục | Slug | Mô tả ngắn |
|---|---|---|---|
| 1 | {{CAT_1}} | {{slug-1}} | … |
| 2 | {{CAT_2}} | {{slug-2}} | … |
| 3 | {{CAT_3}} | {{slug-3}} | … |

## 4. THUỘC TÍNH ĐẶC THÙ NGÀNH (`attributes`)

`ProductDoc.attributes` là `Record<string,string>`. Liệt kê **khóa cố định** (không dấu, camelCase), nhãn hiển thị, và hiển thị ở đâu. Thứ tự dưới đây là thứ tự hiển thị ở mục "Thông số" trang chi tiết.

| Khóa (`key`) | Nhãn hiển thị | Bắt buộc | Ví dụ |
|---|---|---|---|
| {{attr_key_1}} | {{Nhãn 1}} | Có/Không | … |
| {{attr_key_2}} | {{Nhãn 2}} | Có/Không | … |
| {{attr_key_3}} | {{Nhãn 3}} | Có/Không | … |

Cấu hình này đặt trong `src/lib/industry.ts` (một nguồn duy nhất, dùng cho cả form admin và trang chi tiết):
```ts
export const ATTRIBUTE_FIELDS = [
  { key: '{{attr_key_1}}', label: '{{Nhãn 1}}', required: false, placeholder: '…' },
] as const;
```

## 5. BIẾN THỂ

| Mục | Giá trị |
|---|---|
| Dùng biến thể? (`hasVariants`) | {{Có/Không}} |
| Loại tùy chọn (`optionTypes`) | {{size, color, …}} |
| Giá trị gợi ý cho từng loại | {{size: S, M, L, XL; color: …}} |
| Giá riêng theo biến thể? | {{Có/Không}} |
| Tồn kho theo biến thể? | {{Có/Không}} |
| Hướng dẫn chọn size/dung tích… | {{Có (nội dung ở policies hoặc accordion) / Không}} |

## 6. GIÁ, KHO, ĐƠN VỊ

- Cách ra giá: {{một giá / giá gốc + khuyến mãi}}; khoảng giá thực tế: {{từ … đến … ₫}}
- Đơn vị bán (`unit`): {{cái / hộp / kg / bộ …}}
- Quản lý tồn kho: {{Có/Không}} (nếu **Không**, đặt `stock` lớn mặc định và ẩn cảnh báo tồn kho trên giao diện nhưng vẫn giữ trường dữ liệu)
- Số lượng tối đa mỗi mặt hàng (`maxQtyPerItem`): {{20}}

## 7. VẬN HÀNH & CHÍNH SÁCH (điền nội dung THẬT từ brief)

| Mục | Giá trị |
|---|---|
| Phí giao hàng | {{… ₫ cố định}} · Miễn phí từ {{… ₫ / không có}} |
| Khu vực giao | {{toàn quốc / nội thành …}} |
| Thời gian giao dự kiến | {{2–4 ngày làm việc}} |
| Đổi trả | {{điều kiện, thời hạn}} |
| Bảo hành | {{có/không, thời hạn}} |
| Hotline / Zalo | {{…}} / {{…}} |
| Địa chỉ, giờ làm việc | {{…}} |
| Mạng xã hội | {{…}} |

→ Chuyển thành nội dung `settings/shop.policies` (`doi-tra`, `giao-hang`, `bao-mat`) và `about`. **Không tự bịa điều khoản.** Thiếu thông tin → hỏi người dùng hoặc để trống và ẩn mục đó.

## 8. THƯƠNG HIỆU & GIỌNG ĐIỆU

| Mục | Giá trị |
|---|---|
| Tông cảm xúc | {{BRAND_TONE}} |
| Màu chủ đạo / bảng màu | {{BRAND_COLOR}} → chi tiết token ở `skills/frontend-design.md` mục 4 |
| Font cặp (hỗ trợ tiếng Việt) | {{heading}} + {{body}} |
| Điểm nhớ của shop | {{1 câu}} |
| Xưng hô | {{bạn – mình / quý khách – chúng tôi / …}} |
| Mức animation | {{vừa phải / nổi bật}} |
| Từ nên dùng | {{…}} |
| Từ tránh | {{…}} |

## 9. NỘI DUNG KHỞI TẠO

- **Hero trang chủ:** tiêu đề {{…}} · dòng phụ {{…}} · nút chính {{…}}
- **Điểm tin cậy (3–4 mục thật):** {{…}}
- **Dữ liệu mẫu (seed):** 3–5 danh mục, 8–12 sản phẩm đúng ngành, giá thật thị trường VN, ảnh placeholder, 1–2 banner. Đặt tiền tố slug `demo-` để xóa ở bước D3.
- **Bằng chứng xã hội thật có thể dùng:** {{số đơn đã bán / năm hoạt động / nhận xét thật}} hoặc "chưa có" → **không hiển thị khối này**.

## 10. SEO

- Từ khóa chính: {{…}} · Từ khóa phụ: {{…}}
- Mẫu `title`: `{{Tên sản phẩm}} | {{SHOP_NAME}}` (≤ 60 ký tự)
- Mẫu `description`: lợi ích chính + lời mời hành động (≤ 155 ký tự)

## 11. ĐIỀU CHƯA CHẮC (để hỏi lại)
- {{…}}

---

# PHỤ LỤC — GỢI Ý SẴN THEO NGÀNH (để Gemini đề xuất ở bước B5/B6, người dùng quyết)

> Đây chỉ là điểm xuất phát. Luôn tinh chỉnh theo `brief.md`.

### A. Thời trang (quần áo, giày, túi)
- **Biến thể:** Có — `size`, `color`; giá chung hoặc riêng; tồn theo biến thể. Cần **bảng hướng dẫn chọn size**.
- **attributes:** `material` (Chất liệu), `fit` (Form dáng), `origin` (Xuất xứ), `care` (Hướng dẫn giặt), `gender` (Đối tượng).
- **Ảnh:** nhiều góc, ảnh mặc thật; tỉ lệ 4:5.
- **Lưu ý:** đổi trả size là mối lo lớn nhất → làm nổi bật chính sách đổi size.

### B. Mỹ phẩm, chăm sóc cá nhân
- **Biến thể:** Tùy sản phẩm — `volume` (dung tích) hoặc `shade` (tông màu).
- **attributes:** `brand`, `skinType`, `ingredients`, `usage`, `origin`, `expiry` (Hạn sử dụng/ngày sản xuất), `volume`.
- **Lưu ý:** **không tuyên bố công dụng y tế** ("trị mụn", "chữa") nếu không có căn cứ; thể hiện rõ hạn dùng, nguồn gốc, thành phần.

### C. Thực phẩm, đồ uống
- **Biến thể:** `weight` (khối lượng) hoặc `flavor` (vị).
- **attributes:** `ingredients`, `origin`, `expiry`, `storage` (Bảo quản), `certification` (nếu có thật, ví dụ VietGAP).
- **Vận hành:** hàng tươi/dễ hỏng cần ghi rõ khu vực giao và thời gian giao; có thể giới hạn khu vực ở `settings`.
- **Lưu ý:** hiển thị hạn sử dụng, bảo quản; ảnh món/sản phẩm là trung tâm.

### D. Đồ handmade, quà tặng, nến thơm, trang trí
- **Biến thể:** `scent`/`color`/`size` tùy loại; tồn kho thấp → hiển thị "Chỉ còn x" khi **thật sự** thấp.
- **attributes:** `material`, `burnTime`/`dimensions`, `madeBy`, `giftWrap` (Có gói quà), `leadTime` (Thời gian làm).
- **Lưu ý:** kể câu chuyện thủ công; ảnh chi tiết, ảnh đóng gói; cân nhắc ghi chú lời nhắn tặng ở bước checkout (tùy chọn, dùng trường `note`).

### E. Điện tử, phụ kiện công nghệ
- **Biến thể:** `color`, `capacity`/`model`.
- **attributes:** `brand`, `model`, `warranty` (Bảo hành), `specs` (Thông số chính), `compatibility`, `origin`.
- **Lưu ý:** làm rõ **bảo hành** và nguồn gốc; thông số dạng bảng dễ so sánh; chống hàng giả bằng chính sách minh bạch.

### F. Sách, văn phòng phẩm
- **Biến thể:** thường Không (hoặc `format`: bìa mềm/bìa cứng).
- **attributes:** `author`, `publisher`, `year`, `pages`, `language`, `isbn`.
- **Lưu ý:** ảnh bìa rõ, trích đoạn/mô tả nội dung ngắn (không sao chép bản quyền).

### G. Đồ gia dụng, nội thất nhỏ
- **Biến thể:** `color`/`size`.
- **attributes:** `material`, `dimensions`, `weight`, `warranty`, `origin`, `assembly` (Cần lắp đặt).
- **Lưu ý:** sản phẩm cồng kềnh → ghi chú phí/giao hàng riêng trong chính sách nếu có.

### H. Sản phẩm số / dịch vụ
- **Không phù hợp** với bản base (cần giao tệp, tài khoản, thanh toán online). Báo người dùng và đề xuất hướng khác thay vì cố làm.

---

## QUY TRÌNH ĐIỀN FILE NÀY (cho Gemini)

1. Đọc `brief.md` (đã duyệt). Nếu chưa duyệt → quay về Chế độ A trong `00-master-rule.md`.
2. Chọn ngành gần nhất ở Phụ lục, **đề xuất** attributes/biến thể cho người dùng duyệt (nêu ngắn gọn lý do).
3. Điền mục 1–11, xóa toàn bộ `{{...}}`; mục nào brief không có thì ghi "chưa có" và ghi vào mục 11.
4. Cập nhật `src/lib/industry.ts` (mục 4) và báo lại các điểm của `03-firestore-schema.md` bị ảnh hưởng (`attributes`, `hasVariants`, `unit`, quy mô tìm kiếm).
5. Báo cáo theo mẫu ở `00-master-rule.md` mục 9, chờ người dùng xác nhận trước khi sang bước tiếp theo.
