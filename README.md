# HookCheck

[English](#english) · [Tiếng Việt](#tieng-viet)

<a id="english"></a>
## English

**See every webhook as it arrived. Check it against your expectations.** HookCheck gives developers a temporary inbox for inspecting incoming HTTP requests and validating them in the same browser workspace. Create an inbox without an account, send an event, and immediately see the original request and a clear result for every rule.

The application has been deployed on Google Cloud with HTTPS, persistent storage, monitoring and recovery workflows. Access is controlled and the cloud runtime is stopped between demonstration sessions. This public repository presents the product and its engineering; application source is maintained separately.

### Product walkthrough

1. **Create an inbox.** Get a unique capture endpoint and save the private management link.
2. **Define expectations.** Check the HTTP method, headers or JSON fields that matter to your integration.
3. **Send a webhook.** Use your existing sender, an HTTP client or the sample command in the workspace.
4. **Inspect and compare.** Read the request, see each PASS/FAIL result, correct the sender and compare the next event with the earlier one.

Changing rules affects new requests only. Each earlier receipt retains the rule revision and results recorded when it arrived.

### Interface

The screenshots use sample events. Private management links are not displayed.

| Home | Rule editor |
| --- | --- |
| ![HookCheck home page](assets/home.png) | ![Rule editor checking JSON field /order/id](assets/expectations.png) |

| Missing order ID | Corrected event |
| --- | --- |
| ![Failed JSON field check](assets/request-fail.png) | ![Passing check with both events in history](assets/request-pass.png) |

### What HookCheck does

| Area | Capability |
| --- | --- |
| Capture | Receive GET, HEAD, POST, PUT, PATCH, DELETE and OPTIONS while preserving the original body bytes. Inspect headers, query parameters, source IP and content type. Malformed JSON and binary bodies remain available. |
| Expectations | Five rule types: method equals, header exists, header equals, JSON field exists and typed JSON field equals. See individual PASS/FAIL reasons and the overall result. |
| History | Browse separate receipts for sender retries, discover new events automatically and page through older requests without losing your place. Stored results never change when rules are edited. |
| Inbox lifecycle | Delete a request or an entire inbox. Temporary expiry, request limits, body limits, storage quotas and rate limits keep the service bounded. |
| Access | Sending and management use separate random capabilities. The capture endpoint cannot read receipts; the private management link is required to inspect or change an inbox. |
| Browser experience | Copy the capture endpoint and sample command, edit rules, inspect text or base64 bodies, and navigate clear empty, loading and error states. |

### Engineering behind the workflow

- **Exact bytes:** A bounded raw-stream reader stores the bytes delivered by HTTP transport without parsing and rebuilding the body. This matters for binary content, malformed JSON and methods whose bodies are often overlooked.
- **Atomic receipts:** PostgreSQL transactions store the request, captured rule revision, check results and quota usage together. HookCheck acknowledges a capture only after commit, including at quota boundaries under concurrent traffic.
- **Historical truth:** Rule snapshots make PASS/FAIL results immutable for each receipt. Cursor pagination preserves order as new webhooks arrive or older receipts are deleted.
- **Capability security:** Capture and management secrets are independent; only their hashes are stored. Browser content is rendered inertly, with text and base64 clearly distinguished.
- **Bounded operation:** Upload, metadata, request-count, storage and rate limits protect a temporary inbox from unbounded traffic. Expiry is enforced during access as well as cleanup.
- **Least privilege:** Application and migration database roles have separate permissions. Containers run with constrained filesystems and network access; the Kubernetes deployment adds service accounts and default-deny policies.
- **Reviewable releases:** Versioned specifications and OpenAPI define behavior, forward migrations evolve storage, and CI checks the build with real PostgreSQL, Chromium and HTTP traffic before a release is exercised.

### Cloud deployment and recovery

| Layer | Implementation |
| --- | --- |
| Application | TypeScript, Node.js, Fastify, server-rendered Nunjucks and lightweight browser JavaScript |
| Storage | PostgreSQL with versioned forward migrations, transactional quotas and persistent volumes |
| Delivery | Container images, GitHub Actions and Terraform-managed Google Cloud infrastructure; HTTPS ingress through Caddy in the container deployment |
| Kubernetes | A separate k3s deployment uses Traefik ingress, a PostgreSQL StatefulSet/PVC, service accounts and network policies |
| Observability | Private Prometheus metrics, Grafana dashboards and Alertmanager rules for application, database and host signals |
| Recovery | Encrypted cloud backups, an independent restore environment and rehearsed rollback between application images while retaining captured data |

Real HTTP checks have verified exact-byte capture across all seven methods. Browser journeys, database transactions, container isolation, restart persistence, rollback and restore have been exercised with synthetic data. The service is intended for test events; do not send production secrets or personal data to a temporary inbox.

---

<a id="tieng-viet"></a>
## Tiếng Việt

**Xem chính xác webhook đã đến và kiểm tra theo quy tắc của bạn.** HookCheck cung cấp hộp thư tạm thời để lập trình viên tiếp nhận, xem và kiểm tra các yêu cầu HTTP ngay trên trình duyệt. Bạn có thể tạo hộp thư mà không cần tài khoản, gửi sự kiện và xem nội dung gốc cùng kết quả của từng quy tắc.

Ứng dụng đã được triển khai trên Google Cloud với HTTPS, lưu trữ dữ liệu, giám sát và quy trình phục hồi. Môi trường chỉ mở cho các phiên trình diễn được kiểm soát và máy chủ được tắt giữa các phiên. Kho lưu trữ công khai này giới thiệu sản phẩm và những giải pháp kỹ thuật; mã nguồn ứng dụng được quản lý riêng.

### Cách HookCheck hoạt động

1. **Tạo hộp thư.** Nhận địa chỉ gửi webhook riêng và lưu liên kết quản lý bí mật.
2. **Đặt quy tắc.** Chọn phương thức HTTP, header hoặc trường JSON cần kiểm tra.
3. **Gửi webhook.** Dùng hệ thống gửi hiện có, công cụ HTTP hoặc lệnh mẫu trong giao diện.
4. **Xem và đối chiếu.** Đọc yêu cầu đã nhận, xem kết quả ĐẠT/KHÔNG ĐẠT, sửa bên gửi rồi so sánh lần gửi mới với lần trước.

Quy tắc mới chỉ áp dụng cho các yêu cầu đến sau. Mỗi yêu cầu cũ giữ nguyên phiên bản quy tắc và kết quả tại thời điểm được nhận.

### Giao diện

Ảnh giao diện sử dụng dữ liệu mẫu; liên kết quản lý riêng tư không được hiển thị.

| Trang chủ | Trình chỉnh sửa quy tắc |
| --- | --- |
| ![Trang chủ HookCheck](assets/home.png) | ![Quy tắc kiểm tra trường JSON /order/id](assets/expectations.png) |

| Thiếu mã đơn hàng | Sự kiện đã được sửa |
| --- | --- |
| ![Quy tắc JSON không đạt](assets/request-fail.png) | ![Quy tắc đạt; cả hai sự kiện vẫn có trong lịch sử](assets/request-pass.png) |

### HookCheck làm được gì

| Nhóm tính năng | Khả năng |
| --- | --- |
| Tiếp nhận | Nhận GET, HEAD, POST, PUT, PATCH, DELETE và OPTIONS, đồng thời giữ nguyên byte gốc của nội dung. Xem header, tham số truy vấn, IP nguồn và loại nội dung. JSON không hợp lệ hoặc dữ liệu nhị phân vẫn xem được. |
| Quy tắc | Năm kiểu kiểm tra: phương thức HTTP bằng giá trị mong đợi; header tồn tại hoặc bằng giá trị mong đợi; trường JSON tồn tại hoặc bằng giá trị có kiểu xác định. Mỗi quy tắc có kết quả và lý do riêng. |
| Lịch sử | Mỗi lần gửi lại là một bản ghi riêng. Yêu cầu mới tự xuất hiện; bạn vẫn có thể duyệt các trang cũ mà không mất vị trí. Sửa quy tắc không làm thay đổi kết quả đã lưu. |
| Vòng đời hộp thư | Xóa từng yêu cầu hoặc cả hộp thư. Thời hạn sử dụng cùng các giới hạn về kích thước, số yêu cầu, dung lượng và tốc độ gửi giúp kiểm soát tài nguyên. |
| Quyền truy cập | Quyền gửi webhook và quyền quản lý được tách biệt. Địa chỉ nhận không thể đọc dữ liệu; cần liên kết quản lý riêng tư để xem hoặc thay đổi hộp thư. |
| Giao diện | Sao chép địa chỉ nhận và lệnh mẫu, chỉnh sửa quy tắc, xem nội dung dạng văn bản hoặc base64, cùng các trạng thái trống, đang tải và lỗi rõ ràng. |

### Kỹ thuật phía sau

- **Giữ đúng dữ liệu gốc:** Bộ đọc luồng có giới hạn lưu các byte được truyền qua HTTP mà không phân tích rồi dựng lại nội dung. Cách này giữ được dữ liệu nhị phân, JSON không hợp lệ và nội dung của cả GET lẫn HEAD.
- **Ghi dữ liệu nhất quán:** Giao dịch PostgreSQL lưu yêu cầu, phiên bản quy tắc, kết quả kiểm tra và mức sử dụng hạn mức cùng nhau. HookCheck chỉ xác nhận đã nhận sau khi giao dịch hoàn tất, kể cả khi nhiều yêu cầu cùng chạm giới hạn.
- **Lịch sử không thay đổi:** Bản chụp quy tắc giữ nguyên kết quả của từng yêu cầu. Phân trang bằng cursor duy trì thứ tự khi có webhook mới hoặc bản ghi cũ bị xóa.
- **Bảo vệ quyền truy cập:** Hai mã bí mật dùng cho gửi và quản lý độc lập với nhau; cơ sở dữ liệu chỉ lưu giá trị băm. Nội dung webhook được hiển thị như dữ liệu, không được thực thi; văn bản và base64 được phân biệt rõ.
- **Tài nguyên có giới hạn:** Kích thước nội dung, metadata, số yêu cầu, dung lượng lưu trữ và tốc độ gửi đều được kiểm soát. Hộp thư hết hạn sẽ ngừng hoạt động ngay cả trước khi tác vụ dọn dẹp chạy.
- **Phân quyền tối thiểu:** Ứng dụng và tác vụ migration dùng các vai trò cơ sở dữ liệu khác nhau. Container được giới hạn quyền ghi và truy cập mạng; bản triển khai Kubernetes bổ sung service account và chính sách chặn mặc định.
- **Phát hành có thể kiểm chứng:** Đặc tả phiên bản và OpenAPI xác định hành vi; migration theo phiên bản cập nhật lược đồ; CI kiểm tra bản build bằng PostgreSQL, Chromium và lưu lượng HTTP thực trước khi triển khai.

### Triển khai và phục hồi trên cloud

| Thành phần | Cách triển khai |
| --- | --- |
| Ứng dụng | TypeScript, Node.js, Fastify, giao diện dựng bằng Nunjucks và JavaScript gọn nhẹ trên trình duyệt |
| Dữ liệu | PostgreSQL với migration theo phiên bản, hạn mức theo giao dịch và vùng lưu trữ bền vững |
| Phát hành | Image container, GitHub Actions và hạ tầng Google Cloud quản lý bằng Terraform; bản triển khai container dùng Caddy cho HTTPS |
| Kubernetes | Môi trường k3s riêng dùng Traefik, PostgreSQL StatefulSet/PVC, service account và network policy |
| Giám sát | Metrics Prometheus, dashboard Grafana và quy tắc Alertmanager cho ứng dụng, cơ sở dữ liệu và máy chủ trong mạng riêng |
| Phục hồi | Bản sao lưu mã hóa trên cloud, môi trường khôi phục độc lập và quy trình trở về image ứng dụng trước mà vẫn giữ dữ liệu đã nhận |

Các bài kiểm tra HTTP thực đã xác nhận dữ liệu được giữ nguyên qua cả bảy phương thức. Luồng trình duyệt, giao dịch cơ sở dữ liệu, cách ly container, dữ liệu sau khi khởi động lại, rollback và khôi phục đều đã được kiểm chứng bằng dữ liệu mẫu. Hộp thư tạm thời dành cho sự kiện thử nghiệm; không gửi bí mật sản xuất hoặc dữ liệu cá nhân vào đây.
