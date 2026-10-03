# RSR — Báo cáo Tóm tắt Yêu cầu: Module Đăng nhập

| Mục | Giá trị |
|---|---|
| **Hệ thống** | Perfex CRM — Anh Tester Demo |
| **URL** | https://crm.anhtester.com/admin/authentication |
| **Module** | Đăng nhập / Quên mật khẩu |
| **Ngày khảo sát** | 03-10-2026 |
| **Phương pháp** | Khảo sát UI + DOM + tầng network, **chỉ đọc, khi chưa đăng nhập** |
| **Phạm vi đã khảo sát** | Trang Đăng nhập · Trang Quên mật khẩu · Chuyển hướng URL nội bộ khi chưa đăng nhập |
| **Chưa khảo sát** | Mọi thứ sau đăng nhập (Dashboard, menu, đăng xuất có phiên, cookie `autologin`, hết phiên) — xem mục 6 |

> ⚠️ **Giới hạn khảo sát.** Khảo sát không gửi biểu mẫu nào (không thử đăng nhập đúng/sai, không gửi Quên mật khẩu) và không đăng nhập. Mọi hành vi phía máy chủ (thông báo lỗi, validate, khoá tài khoản…) **chưa được kiểm chứng** ở đây. Các mục đó ghi là "chưa kiểm chứng", không suy đoán là đã biết.
> Thông tin đăng nhập không được ghi vào tài liệu này.

---

## 1. Danh sách màn hình

| Màn hình | Route | Tiêu đề tab | `body.className` |
|---|---|---|---|
| Đăng nhập | `/admin/authentication` | `Perfex CRM \| Anh Tester Demo - Login` | `tw-bg-neutral-100 login_admin` |
| Quên mật khẩu | `/admin/authentication/forgot_password` | `Perfex CRM \| Anh Tester Demo - Login` (giống trang đăng nhập) | `tw-bg-neutral-100 authentication forgot-password` |

Hai route còn lại thuộc module nhưng **chưa khảo sát**: `/admin/authentication/reset_password/<id>/<key>` (cần liên kết gửi qua email) và `/admin/authentication/logout`.

---

## 2. Yêu cầu chức năng quan sát được

### 2.1. Trang Đăng nhập

| Mã | Yêu cầu / Hành vi quan sát | Cách xác nhận | Trạng thái |
|---|---|---|---|
| REQ-LOGIN-01 | Trang hiển thị logo, tiêu đề `Login`, ô **Email Address**, ô **Password**, checkbox **Remember me**, nút **Login**, liên kết **Forgot Password?** | Ảnh chụp + DOM | 🟢 Đã kiểm chứng |
| REQ-LOGIN-02 | Biểu mẫu gửi bằng **POST** tới `/admin/authentication` | DOM `form.action`, `form.method` | 🟢 |
| REQ-LOGIN-03 | Ô Email: `type="email"`, `name="email"`, `id="email"`. **Không** có thuộc tính `required`, không `placeholder`, không `maxlength`, không `autocomplete` | DOM | 🟢 |
| REQ-LOGIN-04 | Ô Mật khẩu: `type="password"` (che ký tự), `name="password"`, `id="password"`. Không `required`, không `maxlength` | DOM | 🟢 |
| REQ-LOGIN-05 | Checkbox Remember me: `name="remember"`, `id="remember"`, mặc định **không tích**, `value="estimate"` | DOM | 🟢 |
| REQ-LOGIN-06 | Biểu mẫu có input ẩn `csrf_token_name` (giá trị 32 ký tự) — cơ chế chống CSRF | DOM (không ghi giá trị) | 🟢 |
| REQ-LOGIN-07 | Nhãn có liên kết `for` đúng với từng ô (`email`, `password`, `remember`) | DOM | 🟢 |
| REQ-LOGIN-08 | Trang **không có** CAPTCHA | Ảnh chụp + DOM | 🟢 |
| REQ-LOGIN-09 | Trang **không có** đăng nhập mạng xã hội / SSO | Ảnh chụp + DOM | 🟢 |
| REQ-LOGIN-10 | Trang nạp **0 thẻ script** — không có JavaScript | DOM `document.scripts` | 🟢 |
| REQ-LOGIN-11 | Trang phục vụ qua **HTTPS** | `location.protocol` | 🟢 (chuyển hướng HTTP→HTTPS: ⚪ chưa kiểm chứng) |
| REQ-LOGIN-12 | Liên kết **Forgot Password?** trỏ tới `/admin/authentication/forgot_password` | DOM | 🟢 |
| REQ-LOGIN-13 | Logo là liên kết trỏ về `https://crm.anhtester.com/` (trang chủ cổng khách hàng) | DOM | 🟢 |
| REQ-LOGIN-14 | Khi chưa đăng nhập, mở URL nội bộ (thử `/admin/clients`) bị chuyển về `/admin/authentication`, trang cuối trả `200` | `fetch` theo chuyển hướng | 🟢 |
| REQ-LOGIN-15 | Trang có `meta viewport` (`width=device-width, initial-scale=1`) nhưng chặn phóng to (`user-scalable=no`, `maximum-scale=1`) | DOM | 🟢 — liên quan khả năng truy cập |
| REQ-LOGIN-16 | Ngôn ngữ trang: `lang="en"` | DOM | 🟢 |

### 2.2. Trang Quên mật khẩu

| Mã | Yêu cầu / Hành vi quan sát | Cách xác nhận | Trạng thái |
|---|---|---|---|
| REQ-LOGIN-17 | Trang hiển thị logo, tiêu đề `Forgot Password`, ô **Email Address**, nút **Confirm** | Ảnh chụp + DOM | 🟢 |
| REQ-LOGIN-18 | Biểu mẫu gửi bằng **POST** tới `/admin/authentication/forgot_password`, có input ẩn CSRF | DOM | 🟢 |
| REQ-LOGIN-19 | Ô Email: `type="email"`, `name="email"`, **không** có `required` | DOM | 🟢 |
| REQ-LOGIN-20 | Trang **không có** liên kết quay lại trang đăng nhập (chỉ có liên kết logo về trang chủ cổng khách hàng) | DOM | 🟢 |
| REQ-LOGIN-21 | Trang nạp 0 thẻ script | DOM | 🟢 |

### 2.3. Hành vi phía máy chủ — chưa kiểm chứng

Các yêu cầu dưới đây chỉ biết từ tài liệu `REQUIREMENTS_LOGIN_SUMMARY.md` của dự án trước, **chưa được xác nhận** trong lượt khảo sát này.

| Hành vi | Nguồn | Trạng thái |
|---|---|---|
| Đăng nhập đúng → vào Dashboard | Tài liệu cũ | ⚪ |
| Sai thông tin → `Invalid email or password` | Tài liệu cũ | ⚪ |
| Email bị xoá sau khi đăng nhập lỗi (được xác nhận là lỗi) | Tài liệu cũ | ⚪ |
| Không khoá tài khoản khi sai nhiều lần | Tài liệu cũ | ⚪ |
| Quên mật khẩu: email không tồn tại → `Email not found`; bỏ trống cũng báo `Email not found` (là lỗi) | Tài liệu cũ | ⚪ |
| Mã CSRF sai → nội dung `419 Page Expired!` | Tài liệu cũ | ⚪ |
| Phiên hết hạn sau 1 giờ không hoạt động | Tài liệu cũ | ⚪ |
| HTTP không chuyển hướng sang HTTPS, không HSTS (lỗi đang mở) | Tài liệu cũ | ⚪ |

---

## 3. Đặc tả trường

| Màn hình | Trường | Loại | `name` / `id` | Bắt buộc (HTML) | Giới hạn | Ghi chú |
|---|---|---|---|---|---|---|
| Đăng nhập | Email Address | Email | `email` / `email` | Không | Không `maxlength` | Validate định dạng của trình duyệt (`type=email`) |
| Đăng nhập | Password | Password | `password` / `password` | Không | Không `maxlength` | Che ký tự |
| Đăng nhập | Remember me | Checkbox | `remember` / `remember` | Không | — | Mặc định bỏ tích; `value="estimate"` |
| Đăng nhập | (ẩn) CSRF | Hidden | `csrf_token_name` | — | 32 ký tự | Đổi theo phiên |
| Quên mật khẩu | Email Address | Email | `email` / `email` | Không | Không `maxlength` | |
| Quên mật khẩu | (ẩn) CSRF | Hidden | `csrf_token_name` | — | — | |

---

## 4. Ma trận phân quyền

Hai trang trong phạm vi mở cho mọi người chưa đăng nhập. Ma trận phân quyền theo vai trò (Admin / Project Manager / Customer) và hành vi khi đã đăng nhập **chưa khảo sát**.

| Hành động | Khách (chưa đăng nhập) |
|---|---|
| Mở trang đăng nhập | ✅ |
| Mở trang Quên mật khẩu | ✅ |
| Mở URL nội bộ (`/admin/clients`) | ❌ — chuyển về `/admin/authentication` |

---

## 5. Điểm mơ hồ & rủi ro

### 5.1. Điểm mơ hồ cần hỏi BA

| Mã | Câu hỏi | Mức độ |
|---|---|---|
| AMB-LOGIN-01 | Ô Email và Mật khẩu **không có `required`** và trang không có JavaScript. Khi bỏ trống, kiểm tra chỉ dựa vào máy chủ — thông báo cụ thể là gì? Có phải thiếu validate phía client không? | 🟡 |
| AMB-LOGIN-02 | Checkbox Remember me mang `value="estimate"` — có ý nghĩa gì không? | 🟢 |
| AMB-LOGIN-03 | Remember me có thật sự hoạt động (tự đăng nhập khi phiên hết hạn)? Hạn cookie là bao lâu? | 🔴 |
| AMB-LOGIN-04 | Trang Quên mật khẩu không có liên kết quay lại đăng nhập — cố ý hay thiếu? | 🟡 |
| AMB-LOGIN-05 | Tiêu đề tab của trang Quên mật khẩu ghi `... - Login` — cố ý hay thiếu tiêu đề riêng? | 🟢 |
| AMB-LOGIN-06 | Trang chặn phóng to (`user-scalable=no`) — có yêu cầu về khả năng truy cập (WCAG) không? | 🟡 |
| AMB-LOGIN-07 | Có bắt buộc chuyển HTTP → HTTPS kèm HSTS không? | 🟡 |
| AMB-LOGIN-08 | Có khoá tài khoản / giới hạn tần suất sau N lần sai không? | 🔴 |
| AMB-LOGIN-09 | Chính sách mật khẩu (độ dài tối thiểu, ký tự bắt buộc) ở đâu? | 🟡 |
| AMB-LOGIN-10 | Phiên sống bao lâu và tính theo thời gian không hoạt động hay tổng thời gian? Cho phép nhiều phiên song song không? | 🟡 |
| AMB-LOGIN-11 | Hệ thống có mấy vai trò, vai trò nào vào được `/admin`? Customer đăng nhập ở cổng nào? | 🔴 |
| AMB-LOGIN-12 | Sau khi đăng nhập có quay lại URL đích ban đầu không? | 🟡 |
| AMB-LOGIN-13 | Quên mật khẩu với email có thật trả thông báo gì — có lộ email nào đã đăng ký không? | 🔴 |

### 5.2. Rủi ro

| Mã | Rủi ro | Mô tả |
|---|---|---|
| RISK-LOGIN-01 | Không có lớp chống dò mật khẩu quan sát được | Không CAPTCHA (xác nhận); chưa rõ giới hạn tần suất / khoá tài khoản |
| RISK-LOGIN-02 | Không validate phía client | Không `required`, không JavaScript; mọi kiểm tra dựa vào máy chủ |
| RISK-LOGIN-03 | Remember me có thể không hoạt động | Người dùng tích vào và tin rằng không phải đăng nhập lại |
| RISK-LOGIN-04 | Khả năng truy cập | Chặn phóng to trên thiết bị di động |
| RISK-LOGIN-05 | Module là cổng vào của toàn hệ thống | Hỏng module này chặn kiểm thử mọi module khác |
| RISK-LOGIN-06 | Khảo sát chưa phủ sau đăng nhập | Phân quyền, đăng xuất, phiên, cookie chưa được kiểm chứng |

---

## 6. Phần chưa khảo sát (cần làm tiếp)

1. Gửi biểu mẫu đăng nhập: đúng, sai, bỏ trống, sai định dạng email — để xác nhận thông báo lỗi thật. *(Cần đồng ý rõ ràng trước khi gửi.)*
2. Mọi hành vi sau đăng nhập: Dashboard, menu theo vai trò, đăng xuất, cookie `autologin`, hết phiên, chuyển hướng khi đã đăng nhập mở lại trang đăng nhập.
3. Gửi biểu mẫu Quên mật khẩu với email không tồn tại.
4. Liên kết `reset_password` với mã khoá sai.
5. Chuyển hướng HTTP → HTTPS và header HSTS (kiểm bằng `curl -I`).
6. Viewport mobile.

---

## 7. Nhật ký

| Ngày | Nguồn | Tóm tắt |
|---|---|---|
| 03-10-2026 | Khảo sát UI chưa đăng nhập (chỉ đọc) | Khởi tạo RSR cho trang Đăng nhập và Quên mật khẩu: 21 REQ quan sát được, 13 điểm mơ hồ, 6 rủi ro |


test thay dổi thông tin