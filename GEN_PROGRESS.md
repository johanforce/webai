# GEN_PROGRESS.md — Tiến trình tạo bộ base

> Cách dùng: làm tuần tự từng bước, đánh dấu `[x]` khi xong. Mỗi bước nhỏ có **"Xong khi"** để biết đã đạt chưa.
> Nguyên tắc: **mỗi lượt chat với Gemini chỉ làm 1 bước.** Xong bước nào, test/duyệt rồi mới sang bước kế.
> Cấu hình chung xem `PROJECT_CONFIG.md`.

**Tiến độ tổng:** Phần A `0/5` · Phần B `0/7` · Phần C `0/14` · Phần D `0/4`

---

# PHẦN A — Hoàn thiện bộ khung (làm 1 lần duy nhất)

Mục tiêu: có đủ file rule/skill mẫu dùng lại cho mọi dự án.

### A1. Chốt các giả định mặc định
- [ ] Đọc Mục 3 trong `PROJECT_CONFIG.md`, sửa những gì không đúng ý (guest checkout, Cloudinary, số admin...)
- **Xong khi:** Không còn dòng `[MẶC ĐỊNH]` nào bạn chưa đồng ý.

### A2. Viết `rules/00-master-rule.md`
- [ ] Gồm: vai trò AI, quy tắc làm việc (mỗi lần 1 bước, hỏi lại khi thiếu thông tin), danh sách file phải đọc trước khi code, điều **cấm** (không bỏ qua security rules, không gọi Firestore trong component...)
- **Xong khi:** Dán vào System instructions AI Studio, Gemini nhắc lại đúng được stack và quy tắc.

### A3. Viết nhóm rule kỹ thuật `01` → `04`
- [ ] 01 tech-stack · [ ] 02 auth-admin · [ ] 03 firestore-schema · [ ] 04 security-rules (kèm file `firestore.rules` mẫu)
- **Xong khi:** Mỗi file ≤ 1 trang, không mâu thuẫn nhau, bám Mục 6 của config.

### A4. Viết nhóm rule chức năng `05` → `07`
- [ ] 05 client-features · [ ] 06 admin-features · [ ] 07 business-context (mẫu có chỗ trống `{{...}}`)
- **Xong khi:** Mỗi chức năng có tiêu chí chấp nhận (acceptance criteria) kiểm tra được.

### A5. Viết bộ skill `skills/*`
- [ ] frontend-design · [ ] ux-conversion · [ ] copywriting-vi · [ ] qa-checklist
- **Xong khi:** Skill frontend có sẵn: danh sách font cặp gợi ý, 3–4 bảng màu theo tông thương hiệu, quy ước component (Button, Card, Modal, Toast, Skeleton).

---

# PHẦN B — Chạy bộ sinh cho 1 dự án mới (làm mỗi lần có ý tưởng mới)

### B1. Nhập ý tưởng
- [ ] Viết 1–2 câu: bán gì, cho ai.
- **Xong khi:** Có câu ý tưởng ghi vào đầu `brief.md`.

### B2. AI hỏi bộ câu hỏi business
- [ ] Gemini hỏi lần lượt nhóm A→E (Mục 4 config), mỗi lần 1 nhóm.
- **Xong khi:** Tất cả mục có câu trả lời hoặc ghi rõ "để AI đề xuất".

### B3. Chốt `brief.md`
- [ ] Tổng hợp câu trả lời, bạn đọc lại và duyệt.
- **Xong khi:** Bạn nhắn "duyệt brief".

### B4. Đề xuất nhận diện thương hiệu
- [ ] AI đề xuất 2 phương án (tên màu + font cặp + tông giọng), bạn chọn 1.
- **Xong khi:** Phương án được ghi vào `brief.md` mục Brand.

### B5. Sinh `rules/07-business-context.md`
- [ ] Điền `{{...}}` từ brief: ngành, danh mục, thuộc tính đặc thù, chính sách.
- **Xong khi:** Không còn placeholder `{{` nào.

### B6. Tinh chỉnh schema theo ngành
- [ ] Cập nhật `03-firestore-schema.md` (thêm `attributes` đặc thù, bật/tắt biến thể, tồn kho).
- **Xong khi:** Schema khớp brief và khớp `firestore.rules`.

### B7. Đóng gói bộ file
- [ ] Kiểm tra đủ file, đồng bộ nội dung, dán `00-master-rule.md` vào System instructions.
- **Xong khi:** Gemini trả lời đúng khi hỏi "tóm tắt dự án này".

---

# PHẦN C — Vibe code web bằng bộ file (mỗi bước 1 lượt chat)

### Giai đoạn 0 — Chuẩn bị Firebase (làm tay)
- [ ] **C0.1** Tạo Firebase project bằng Gmail admin
- [ ] **C0.2** Bật Authentication → Sign-in method → **Google**
- [ ] **C0.3** Tạo Firestore Database (production mode, chọn region gần VN, ví dụ `asia-southeast1`)
- [ ] **C0.4** Lấy cấu hình web app (apiKey, projectId...) → điền `.env`
- [ ] **C0.5** Tạo tài khoản Cloudinary + unsigned upload preset (nếu dùng)
- **Xong khi:** Có đủ biến môi trường trong `.env`.

### Giai đoạn 1 — Nền móng
- [ ] **C1.1** Khởi tạo Vite + React + Tailwind, cấu trúc thư mục theo rule 01. *Xong khi: chạy `npm run dev` thấy trang trống không lỗi.*
- [ ] **C1.2** Thiết lập design tokens (màu, font, bo góc) + component nền: Button, Input, Card, Modal, Toast, Skeleton. *Xong khi: có 1 trang demo hiển thị đủ component.*
- [ ] **C1.3** Kết nối Firebase (`lib/firebase.ts`) + `services/` rỗng. *Xong khi: đọc thử được 1 document.*

### Giai đoạn 2 — Đăng nhập & phân quyền
- [ ] **C2.1** Đăng nhập Google, lưu/cập nhật `users/{uid}`. *Xong khi: đăng nhập/đăng xuất được, thấy user trong Firestore.*
- [ ] **C2.2** Xác định admin (owner email + collection `admins`), route guard `/admin/*`. *Xong khi: tài khoản thường vào /admin bị chặn, admin vào được.*
- [ ] **C2.3** Viết & deploy `firestore.rules` theo rule 04. *Xong khi: thử ghi `products` bằng tài khoản thường bị từ chối.*

### Giai đoạn 3 — Admin (làm trước để có dữ liệu)
- [ ] **C3.1** Layout admin (sidebar, header, responsive).
- [ ] **C3.2** CRUD danh mục.
- [ ] **C3.3** CRUD sản phẩm + upload ảnh + biến thể + tồn kho.
- [ ] **C3.4** Cài đặt shop (`settings/shop`) + banner.
- **Xong khi (cả giai đoạn):** Tạo được ≥ 5 sản phẩm thật qua giao diện admin.

### Giai đoạn 4 — Client
- [ ] **C4.1** Layout client (header, menu mobile, footer) + trang chủ.
- [ ] **C4.2** Danh sách sản phẩm: lọc, sắp xếp, tìm kiếm.
- [ ] **C4.3** Chi tiết sản phẩm: gallery, chọn biến thể, thêm giỏ.
- [ ] **C4.4** Giỏ hàng (lưu theo `carts/{uid}`).
- [ ] **C4.5** Checkout COD + tạo đơn (snapshot giá, tính ship). *Xong khi: đặt đơn thành công và thấy trong Firestore.*
- [ ] **C4.6** Tài khoản: thông tin, địa chỉ, lịch sử đơn, chi tiết đơn.
- [ ] **C4.7** Trang tĩnh: giới thiệu, chính sách, liên hệ, 404.

### Giai đoạn 5 — Quản lý đơn & hoàn thiện
- [ ] **C5.1** Admin quản lý đơn: lọc, đổi trạng thái, trừ kho khi xác nhận, in đơn.
- [ ] **C5.2** Dashboard admin: doanh thu, đơn theo trạng thái, badge đơn mới realtime.
- [ ] **C5.3** Đánh bóng giao diện theo skill `frontend-design` (animation, empty state, loading).
- [ ] **C5.4** SEO cơ bản + ảnh tối ưu + kiểm thử theo `qa-checklist.md`.
- [ ] **C5.5** Deploy Firebase Hosting.

---

# PHẦN D — Nghiệm thu

- [ ] **D1.** Chạy `qa-checklist.md`: khách đặt hàng trọn luồng trên điện thoại.
- [ ] **D2.** Kiểm tra bảo mật: khách không đọc được đơn người khác, không ghi được sản phẩm.
- [ ] **D3.** Xoá dữ liệu seed/test, đổi cấu hình sang dữ liệu thật.
- [ ] **D4.** Cập nhật Mục 8 (nhật ký quyết định) trong `PROJECT_CONFIG.md`.

---

## Nhật ký tiến trình
| Ngày | Bước | Kết quả / ghi chú |
|---|---|---|
| 2026-10-29 | Khởi tạo | Tạo `PROJECT_CONFIG.md` và `GEN_PROGRESS.md` |