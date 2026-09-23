# HookCheck

[English](#english) · [Tiếng Việt](#tieng-viet)

<a id="english"></a>
## English

A webhook debugging workspace for developers and QA engineers. Create an inbox, send a webhook, and see exactly what arrived and why it passed or failed your expectations.

**Deployment:** The full application runs in a restricted Google Cloud QA environment. Public access is not available yet; the screenshots below show the working product with synthetic data. This repository is the public showcase; the application source is maintained privately.

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
- **Deployed and exercised:** A containerized application, database, HTTPS ingress, and automated release checks run in Google Cloud QA. An isolated Kubernetes lab has also passed real HTTP and browser journeys, including persistence after a VM restart.
- **Recovery tested:** Automated checks cover rollback to an earlier application image and encrypted database restore with retained synthetic requests.

**Stack:** TypeScript, Node.js, Fastify, PostgreSQL, Nunjucks, Docker, Playwright, GitHub Actions, Google Cloud, Kubernetes, Terraform.

---

<a id="tieng-viet"></a>
## Tiếng Việt

HookCheck là công cụ gỡ lỗi webhook cho lập trình viên và kỹ sư QA. Tạo hộp thư nhận webhook, gửi sự kiện, rồi xem chính xác dữ liệu đã đến và lý do từng điều kiện kiểm tra đạt hoặc không đạt.

**Triển khai:** Ứng dụng đầy đủ đang chạy trong môi trường QA giới hạn quyền truy cập trên Google Cloud. Chưa có bản dùng thử công khai; ảnh bên dưới chụp sản phẩm hoạt động với dữ liệu giả lập. Repo này giới thiệu sản phẩm công khai, còn mã nguồn ứng dụng được duy trì riêng tư.

### Giao diện

Ảnh chụp từ phiên chạy local với các sự kiện giả lập. Token truy cập đã được che.

#### Trang chủ

![Trang chủ HookCheck](assets/home.png)

#### Thiết lập điều kiện kiểm tra

![Thiết lập yêu cầu trường JSON /order/id](assets/expectations.png)

#### Kiểm tra request

| Thiếu trường bắt buộc | Request hợp lệ |
| --- | --- |
| ![Điều kiện không đạt vì thiếu mã đơn hàng](assets/request-fail.png) | ![Điều kiện đạt và cả hai request vẫn có trong lịch sử](assets/request-pass.png) |

### Bạn có thể làm gì

- Tạo hộp thư nhận webhook tạm thời mà không cần tài khoản
- Xem body gốc và metadata của request, kể cả JSON lỗi định dạng và dữ liệu nhị phân
- Đặt điều kiện kiểm tra HTTP method, header và trường JSON
- Xem lịch sử request cùng bộ điều kiện và kết quả tại thời điểm nhận
- Phân quyền riêng giữa gửi webhook và xem hoặc quản lý hộp thư
- Tự hết hạn, xóa dữ liệu và giới hạn số request cùng dung lượng lưu trữ

### Một phiên gỡ lỗi điển hình

1. Tạo hộp thư và lưu liên kết quản lý bí mật.
2. Đặt điều kiện mong đợi rồi gửi webhook đến địa chỉ nhận.
3. Xem request và kết quả từng điều kiện trên giao diện web.
4. Sửa bên gửi và so sánh sự kiện mới với lần gửi lỗi trước đó.

Thay đổi điều kiện chỉ ảnh hưởng đến các request nhận sau đó; kết quả cũ vẫn được giữ nguyên.

### Không chỉ là bản mẫu

- **Giữ đúng dữ liệu nhận:** Lưu nguyên byte gốc qua bảy HTTP method, kể cả JSON lỗi định dạng và payload nhị phân. Mỗi request giữ bộ điều kiện và kết quả lúc được nhận, nên việc sửa điều kiện sau đó không làm thay đổi lịch sử.
- **Quyền truy cập tách biệt:** Địa chỉ nhận webhook không cho phép xem dữ liệu. Một liên kết bí mật riêng kiểm soát quyền truy cập; nội dung nhận được chỉ hiển thị dưới dạng văn bản trơ hoặc base64.
- **Dữ liệu nhất quán khi có tải:** Giao dịch PostgreSQL ghi request, kết quả kiểm tra và quota cùng nhau. Dịch vụ chỉ xác nhận đã nhận sau khi giao dịch được commit.
- **Đã triển khai và kiểm tra:** Ứng dụng đóng gói container, cơ sở dữ liệu, HTTPS ingress và các bước kiểm tra phát hành tự động chạy trong môi trường Google Cloud QA. Một lab Kubernetes riêng đã vượt qua các luồng kiểm tra HTTP và trình duyệt thực, gồm cả việc giữ dữ liệu sau khi khởi động lại VM.
- **Đã kiểm tra phục hồi:** Các bài kiểm tra tự động bao gồm quay về image ứng dụng cũ và khôi phục cơ sở dữ liệu từ bản sao lưu mã hóa, đồng thời giữ nguyên các request giả lập.

**Công nghệ:** TypeScript, Node.js, Fastify, PostgreSQL, Nunjucks, Docker, Playwright, GitHub Actions, Google Cloud, Kubernetes, Terraform.

