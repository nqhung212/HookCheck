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

**HookCheck cho bạn thấy chính xác webhook mà hệ thống nhận được.** Tạo một inbox tạm thời, gửi webhook tới URL riêng rồi xem request và kết quả đối chiếu với các quy tắc đã đặt. Tất cả trên trình duyệt, không cần tạo tài khoản.

HookCheck đã được triển khai trên Google Cloud với HTTPS, cơ sở dữ liệu, giám sát và sao lưu. Hệ thống chỉ mở trong các phiên trình diễn có kiểm soát; máy chủ được tắt giữa các phiên. Repo này giới thiệu sản phẩm và thiết kế kỹ thuật, còn mã nguồn ứng dụng được quản lý riêng.

### Cách sử dụng

1. **Tạo inbox** để lấy URL nhận webhook và lưu liên kết quản lý.
2. **Đặt quy tắc** cho phương thức HTTP, header hoặc trường JSON cần kiểm tra.
3. **Gửi thử webhook** từ hệ thống của bạn, một công cụ HTTP hoặc lệnh mẫu có sẵn.
4. **Xem kết quả**, chỉnh hệ thống gửi rồi thử lại. Cả hai lần gửi vẫn nằm trong lịch sử để bạn đối chiếu.

Khi quy tắc thay đổi, HookCheck chỉ dùng quy tắc mới cho những webhook đến sau. Kết quả đã ghi nhận trước đó được giữ nguyên.

### Giao diện

Ảnh dưới đây sử dụng dữ liệu mẫu và không hiển thị liên kết quản lý.

| Trang chủ | Thiết lập quy tắc |
| --- | --- |
| ![Trang chủ HookCheck](assets/home.png) | ![Quy tắc kiểm tra trường JSON /order/id](assets/expectations.png) |

| Thiếu mã đơn hàng | Sau khi bổ sung mã đơn hàng |
| --- | --- |
| ![Kiểm tra không đạt vì thiếu mã đơn hàng](assets/request-fail.png) | ![Kiểm tra đạt; cả hai lần gửi vẫn có trong lịch sử](assets/request-pass.png) |

### Tính năng chính

- **Xem request thực nhận:** Phương thức HTTP, header, query string, IP người gửi và body đều có trong phần chi tiết. HookCheck vẫn lưu được body khi JSON sai định dạng hoặc nội dung là dữ liệu nhị phân.
- **Kiểm tra theo quy tắc:** Có năm loại quy tắc: so khớp phương thức HTTP; kiểm tra header có tồn tại hoặc đúng giá trị; kiểm tra trường JSON có tồn tại hoặc đúng giá trị và kiểu dữ liệu. Từng quy tắc có kết quả riêng, kèm kết quả chung cho request.
- **Theo dõi lịch sử:** Mỗi lần hệ thống gửi lại webhook tạo một bản ghi mới. Danh sách tự cập nhật; bạn có thể xem các trang cũ mà không bị xáo trộn khi có request mới.
- **Quản lý inbox:** Xóa từng request hoặc cả inbox. Inbox tự hết hạn và có giới hạn về kích thước body, số request, dung lượng lưu trữ và tốc độ gửi.
- **Tách quyền truy cập:** URL nhận webhook chỉ dùng để gửi. Muốn xem dữ liệu hoặc sửa quy tắc phải có liên kết quản lý riêng.
- **Thao tác thuận tiện:** Sao chép URL nhận và lệnh gửi mẫu, chỉnh quy tắc trên trình duyệt, xem body dưới dạng văn bản hoặc base64.

### Thiết kế kỹ thuật

- **Giữ nguyên payload:** HookCheck đọc trực tiếp luồng HTTP và lưu chính xác từng byte của body qua cả bảy phương thức GET, HEAD, POST, PUT, PATCH, DELETE và OPTIONS. Dữ liệu nhị phân và JSON sai định dạng không bị biến đổi trong quá trình lưu.
- **Kết quả không bị viết lại:** Mỗi request lưu kèm phiên bản quy tắc và kết quả tại thời điểm nhận. Khi bạn sửa quy tắc, lịch sử cũ vẫn phản ánh đúng lần kiểm tra ban đầu. Phân trang bằng cursor giữ thứ tự ổn định ngay cả khi có request mới.
- **Ghi dữ liệu nhất quán:** PostgreSQL lưu request, kết quả kiểm tra và số lượt đã dùng trong hạn mức trong cùng một giao dịch. HookCheck chỉ trả lời thành công sau khi giao dịch hoàn tất, kể cả khi nhiều request cùng chạm giới hạn.
- **Bảo vệ dữ liệu:** Quyền gửi và quyền quản lý dùng hai token độc lập; cơ sở dữ liệu chỉ lưu giá trị băm. Nội dung webhook được hiển thị như dữ liệu, không được thực thi. Ứng dụng và tác vụ cập nhật cơ sở dữ liệu cũng dùng các quyền riêng.
- **Giới hạn rõ ràng:** HookCheck kiểm soát kích thước body, metadata, số request, dung lượng và tốc độ gửi. Inbox hết hạn sẽ ngừng hoạt động ngay, không phải chờ tác vụ dọn dẹp.

### Triển khai và vận hành

| Thành phần | Công nghệ và cách triển khai |
| --- | --- |
| Ứng dụng | TypeScript, Node.js, Fastify, Nunjucks và JavaScript trên trình duyệt |
| Dữ liệu | PostgreSQL, migration theo phiên bản, vùng lưu trữ riêng và hạn mức được cập nhật bằng giao dịch |
| Hạ tầng | Container, GitHub Actions, Terraform và Google Cloud; bản triển khai bằng Docker Compose dùng Caddy cho HTTPS |
| Kubernetes | Một môi trường k3s độc lập dùng Traefik, PostgreSQL StatefulSet/PVC, service account và network policy |
| Giám sát | Prometheus, Grafana và các quy tắc Alertmanager theo dõi ứng dụng, cơ sở dữ liệu và máy chủ trong mạng riêng |
| Phục hồi | Sao lưu mã hóa trên cloud, khôi phục ở môi trường độc lập và quay về phiên bản ứng dụng trước mà vẫn giữ dữ liệu |

Bộ kiểm tra dùng HTTP thực để đối chiếu byte đã gửi với byte được lưu ở cả bảy phương thức. Các luồng trình duyệt, giao dịch PostgreSQL, cách ly container, dữ liệu sau khi khởi động lại máy chủ, rollback và khôi phục cũng đã được kiểm chứng bằng dữ liệu mẫu. HookCheck dành cho dữ liệu thử nghiệm; không gửi bí mật sản xuất hoặc dữ liệu cá nhân vào inbox tạm thời.
