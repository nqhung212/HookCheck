# HookCheck

[English](#english) · [Tiếng Việt](#tieng-viet)

<a id="english"></a>
## English

A webhook debugging workspace for developers. Create an inbox, send a webhook, and see exactly what arrived and why it passed or failed your expectations.

**Deployment:** The full application is deployed on Google Cloud with HTTPS, PostgreSQL, monitoring, backups, and a tested recovery path. Access is currently restricted, so there is no public demo yet. This repository showcases the product; its application source is maintained privately.

### Interface

Screenshots from a local session using synthetic events. Access tokens are hidden.

#### Home

![HookCheck home page](assets/home.png)

#### Validation rules

![Rule editor requiring the JSON field /order/id](assets/expectations.png)

#### Request inspection

| Missing required field | Valid request |
| --- | --- |
| ![Failed check for a missing order ID](assets/request-fail.png) | ![Passing check with both requests preserved in history](assets/request-pass.png) |

### What you can do

- Temporary inboxes without account setup
- Original request bodies and metadata, including malformed JSON and binary content
- Configurable checks for HTTP methods, headers, and JSON fields
- Request history with the rules and results recorded at capture time
- Separate capture and management access
- Inbox expiry, deletion, and request/storage limits

### A typical debugging session

1. Create an inbox and save its management link.
2. Configure expectations and send a webhook to the capture endpoint.
3. Inspect the request and individual check results in the browser dashboard.
4. Correct the sender and compare the new event with the earlier failure.

Updating rules affects future requests; earlier receipts retain their original results.

### Built beyond the prototype

- **Faithful capture:** Preserves original bytes across seven HTTP methods, including malformed JSON and binary payloads. Each receipt keeps the rules and results from when it arrived, so later edits cannot rewrite history.
- **Private by design:** The endpoint that receives webhooks cannot inspect them. A separate secret link controls access; captured content is displayed as inert text or base64.
- **Consistent under load:** PostgreSQL transactions keep captured requests, rule results, and quotas together. The service acknowledges a capture only after it is committed.
- **Cloud deployment:** The containerized application runs behind HTTPS with a persistent database and monitoring. A separate Kubernetes deployment demonstrates the same webhook workflow and keeps captured data through a VM restart.
- **Recoverable data:** Rollback to an earlier application image and restoration from an encrypted database backup have been exercised with retained synthetic requests.

**Stack:** TypeScript, Node.js, Fastify, PostgreSQL, Nunjucks, Docker, Playwright, GitHub Actions, Google Cloud, Kubernetes, Terraform.

---

<a id="tieng-viet"></a>
## Tiếng Việt

HookCheck giúp lập trình viên tiếp nhận và kiểm tra webhook. Bạn có thể tạo một hộp thư tạm, gửi yêu cầu đến địa chỉ riêng của hộp thư, rồi xem nội dung thực nhận cùng kết quả kiểm tra cho từng quy tắc.

**Triển khai:** HookCheck đang chạy trên Google Cloud với HTTPS, PostgreSQL, hệ thống giám sát và sao lưu. Quy trình phục hồi đã được kiểm chứng. Môi trường hiện giới hạn quyền truy cập nên chưa có bản dùng thử công khai. Kho lưu trữ này giới thiệu sản phẩm; mã nguồn ứng dụng được quản lý riêng.

### Giao diện

Ảnh chụp từ môi trường cục bộ với dữ liệu mẫu. Các token truy cập đã được che.

#### Trang chủ

![Trang chủ HookCheck](assets/home.png)

#### Thiết lập quy tắc kiểm tra

![Quy tắc yêu cầu trường JSON /order/id](assets/expectations.png)

#### Chi tiết webhook đã nhận

| Thiếu trường bắt buộc | Đáp ứng quy tắc |
| --- | --- |
| ![Quy tắc không đạt vì thiếu mã đơn hàng](assets/request-fail.png) | ![Quy tắc đạt; cả hai lần gửi vẫn có trong lịch sử](assets/request-pass.png) |

### Bạn có thể làm gì

- Tạo hộp thư nhận webhook tạm thời mà không cần đăng ký tài khoản
- Xem nguyên nội dung và thông tin của yêu cầu, kể cả JSON không hợp lệ hoặc dữ liệu nhị phân
- Thiết lập quy tắc cho phương thức HTTP, header và trường JSON
- Xem lại từng yêu cầu cùng quy tắc và kết quả được ghi nhận tại thời điểm nhận
- Dùng hai địa chỉ riêng cho việc gửi webhook và quản lý hộp thư
- Chủ động xóa dữ liệu; hộp thư tự hết hạn và có giới hạn về số yêu cầu, dung lượng

### Cách sử dụng

1. Tạo hộp thư và lưu liên kết quản lý riêng tư.
2. Đặt quy tắc mong đợi, sau đó gửi webhook đến địa chỉ nhận.
3. Xem nội dung yêu cầu và kết quả từng quy tắc trên giao diện web.
4. Sửa hệ thống gửi webhook rồi so sánh lần gửi mới với lần lỗi trước đó.

Khi bạn thay đổi quy tắc, các lần gửi trước vẫn giữ nguyên kết quả ban đầu.

### Điểm nổi bật về kỹ thuật

- **Giữ nguyên dữ liệu gốc:** HookCheck lưu chính xác các byte nhận được qua bảy phương thức HTTP, kể cả JSON không hợp lệ và dữ liệu nhị phân. Quy tắc và kết quả của mỗi lần gửi được lưu cùng yêu cầu, nên lịch sử không thay đổi khi bạn chỉnh sửa quy tắc.
- **Tách biệt quyền truy cập:** Địa chỉ nhận webhook không thể dùng để xem dữ liệu. Việc xem và quản lý hộp thư cần một liên kết bí mật riêng. Hệ thống chỉ hiển thị nội dung dưới dạng văn bản hoặc base64, không thực thi nội dung đó.
- **Ghi nhận nhất quán:** PostgreSQL ghi yêu cầu, kết quả kiểm tra và số lượt đã dùng trong hạn mức bằng cùng một giao dịch. HookCheck chỉ xác nhận đã nhận sau khi dữ liệu được lưu thành công.
- **Triển khai trên cloud:** Ứng dụng chạy trong container qua HTTPS, cùng PostgreSQL và hệ thống giám sát. Một môi trường Kubernetes độc lập cũng đã chạy thành công luồng webhook và giữ nguyên dữ liệu sau khi khởi động lại máy chủ.
- **Khả năng phục hồi:** Việc trở về phiên bản ứng dụng trước và khôi phục cơ sở dữ liệu từ bản sao lưu mã hóa đã được kiểm chứng bằng dữ liệu mẫu.

**Công nghệ:** TypeScript, Node.js, Fastify, PostgreSQL, Nunjucks, Docker, Playwright, GitHub Actions, Google Cloud, Kubernetes, Terraform.

