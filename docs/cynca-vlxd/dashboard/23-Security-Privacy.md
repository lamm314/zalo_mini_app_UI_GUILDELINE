# Cynca VLXD Admin Dashboard — Security & Privacy

> Phiên bản: 1.0  
> Phạm vi: Yêu cầu bảo mật, quyền riêng tư và bảo vệ dữ liệu cho Dashboard quản trị  
> Stack mục tiêu: Next.js, React, TypeScript, REST/BFF  
> Đối tượng sử dụng: Frontend, Backend, DevOps, QA, Product Owner, Security, AI coding agent  
> Tài liệu liên quan: toàn bộ thư mục `docs/cynca-vlxd/dashboard/`

---

## 1. Mục tiêu

Dashboard Cynca VLXD xử lý dữ liệu kinh doanh, đơn hàng, khách hàng, sản phẩm, tồn kho, thanh toán và tài khoản nhân sự. Vì vậy, bảo mật và quyền riêng tư phải được thiết kế ngay từ đầu, không bổ sung sau khi hệ thống đã hoàn thiện.

Mục tiêu chính:

- Chỉ người được phép mới truy cập đúng dữ liệu và chức năng.
- Không để lộ dữ liệu giữa các chi nhánh, kho hoặc vai trò.
- Bảo vệ thông tin khách hàng và dữ liệu tài chính.
- Ngăn thao tác trái phép, giả mạo request và chiếm phiên đăng nhập.
- Giảm dữ liệu nhạy cảm xuất hiện trên giao diện, log và export.
- Có dấu vết kiểm tra cho thao tác quan trọng.
- Có quy trình phản ứng khi xảy ra sự cố bảo mật.

---

## 2. Nguyên tắc cốt lõi

### 2.1 Least privilege

Mỗi người dùng chỉ được cấp quyền tối thiểu cần cho công việc.

### 2.2 Defense in depth

Không phụ thuộc vào một lớp bảo vệ duy nhất. Frontend, backend, database, session, network và audit đều phải cùng kiểm soát.

### 2.3 Backend là nguồn quyết định cuối cùng

Ẩn menu hoặc button ở frontend chỉ là UX. Backend phải kiểm tra lại quyền trên mọi request.

### 2.4 Data minimization

Chỉ thu thập, trả về và hiển thị dữ liệu thực sự cần thiết.

### 2.5 Secure by default

Mặc định không có quyền, không public dữ liệu và không bật tính năng nhạy cảm nếu chưa cấu hình rõ.

---

## 3. Phân loại dữ liệu

### Public

- Tên danh mục công khai.
- Nội dung marketing công khai.
- Ảnh sản phẩm công khai.

### Internal

- KPI vận hành.
- Danh sách sản phẩm nội bộ.
- Dữ liệu tồn kho.
- Thông báo hệ thống.

### Confidential

- Doanh thu.
- Lợi nhuận.
- Giá vốn.
- Công nợ.
- Báo cáo tài chính.
- Thông tin nhà cung cấp.

### Restricted / Personal Data

- Họ tên khách hàng.
- Số điện thoại.
- Email.
- Địa chỉ giao hàng.
- Mã số thuế cá nhân hoặc doanh nghiệp nếu có.
- Dữ liệu tài khoản nhân viên.
- Session, token và thông tin xác thực.

Dữ liệu phải được xử lý theo mức phân loại cao nhất có trong cùng payload.

---

## 4. Authentication

Hệ thống phải hỗ trợ:

- Đăng nhập bằng tài khoản riêng.
- Mật khẩu được xử lý an toàn ở backend.
- Session hết hạn.
- Đăng xuất khỏi thiết bị hiện tại.
- Vô hiệu hóa tài khoản.
- Reset mật khẩu an toàn.

Khuyến nghị:

- Session lưu trong cookie `HttpOnly`.
- Cookie có `Secure` trên HTTPS.
- `SameSite=Lax` hoặc chặt hơn tùy kiến trúc.
- Không lưu access token nhạy cảm trong `localStorage` nếu có lựa chọn an toàn hơn.

---

## 5. Multi-factor authentication

MFA nên bắt buộc cho:

- Super Admin.
- Người quản lý phân quyền.
- Người xem dữ liệu tài chính nhạy cảm.
- Người thực hiện hoàn tiền hoặc thao tác có giá trị cao.
- Người truy cập từ môi trường quản trị đặc biệt.

MFA có thể dùng:

- TOTP.
- Passkey/WebAuthn.
- Hardware security key.

Không ưu tiên SMS làm yếu tố duy nhất nếu có phương án mạnh hơn.

---

## 6. Password policy

Nếu hệ thống dùng mật khẩu:

- Tối thiểu 12 ký tự hoặc policy tương đương.
- Cho phép password manager và paste.
- Không ép đổi mật khẩu định kỳ vô lý nếu không có dấu hiệu rò rỉ.
- Kiểm tra mật khẩu phổ biến hoặc đã rò rỉ nếu hạ tầng hỗ trợ.
- Không gửi mật khẩu qua email hoặc Zalo.
- Không lưu mật khẩu dạng plain text.

Toggle hiển thị mật khẩu phải có accessible name.

---

## 7. Session security

Session phải có:

- Thời gian hết hạn.
- Idle timeout phù hợp.
- Absolute timeout.
- Khả năng revoke.
- Rotation sau đăng nhập hoặc nâng quyền.
- Phát hiện session bất thường nếu có.

Khi session sắp hết hạn:

- Cảnh báo người dùng.
- Cho phép gia hạn nếu hợp lệ.
- Không làm mất dữ liệu form chưa lưu nếu có thể.

---

## 8. Authorization model

Khuyến nghị dùng RBAC kết hợp scope.

Ví dụ permission:

```text
dashboard.view
finance.view
orders.view
orders.update
orders.cancel
orders.refund
inventory.view
inventory.update
products.view
products.update
reports.export
users.manage
roles.manage
settings.manage
```

Scope có thể gồm:

- Toàn hệ thống.
- Chi nhánh.
- Kho.
- Nhân viên được phân công.

Frontend không được suy quyền từ tên vai trò như `admin` hoặc `warehouse` nếu backend đã có permission list.

---

## 9. Object-level authorization

Backend phải kiểm tra quyền trên từng entity.

Ví dụ người dùng có `orders.view` nhưng chỉ được xem đơn thuộc chi nhánh Hà Nội thì:

- Không được xem đơn chi nhánh khác bằng cách sửa URL.
- Search không trả đơn ngoài phạm vi.
- Export không chứa đơn ngoài phạm vi.
- Notification không dẫn đến đơn ngoài phạm vi.

Đây là yêu cầu chống IDOR/BOLA.

---

## 10. Permission UI behavior

Khi không có quyền:

- Ẩn menu không liên quan.
- Ẩn KPI nhạy cảm.
- Ẩn action bị cấm.
- Không để khoảng trống layout bất thường.

Nếu cần hiển thị disabled:

```text
Bạn không có quyền xuất báo cáo tài chính.
```

Không chỉ giảm opacity mà thiếu giải thích.

---

## 11. Route protection

Mỗi route Dashboard phải có:

- Authentication guard.
- Permission guard.
- Scope validation.

Không chỉ kiểm tra ở Sidebar.

Ví dụ:

```text
/reports/finance
```

phải trả 403 hoặc trạng thái phù hợp nếu người dùng mở URL trực tiếp mà không có `finance.view`.

---

## 12. API security

Mọi API phải:

- Xác thực session/token.
- Kiểm tra quyền.
- Kiểm tra scope.
- Validate input.
- Giới hạn payload.
- Có rate limit phù hợp.
- Trả error không lộ chi tiết nội bộ.
- Gắn request ID để điều tra.

Không tin dữ liệu từ frontend như:

- `branchId`.
- `warehouseId`.
- `userId`.
- `availableActions`.
- Tổng tiền do client gửi.

---

## 13. CSRF protection

Nếu dùng cookie-based session, mutation phải có cơ chế chống CSRF phù hợp:

- SameSite cookie.
- CSRF token.
- Origin/Referer validation.
- Framework protection tương đương.

Các request như:

- Hủy đơn.
- Hoàn tiền.
- Cập nhật tồn kho.
- Phân quyền.

phải được bảo vệ nghiêm ngặt.

---

## 14. XSS prevention

Quy tắc:

- Không render HTML từ API bằng `dangerouslySetInnerHTML` nếu chưa sanitize.
- React escaping mặc định phải được giữ.
- Rich text phải qua sanitizer được kiểm chứng.
- Không tạo URL JavaScript từ dữ liệu người dùng.
- Không chèn dữ liệu không tin cậy vào script hoặc style.

Test payload ví dụ:

```html
<img src=x onerror=alert(1)>
```

phải hiển thị dạng text hoặc bị loại bỏ an toàn.

---

## 15. Content Security Policy

Khuyến nghị triển khai CSP.

Ví dụ định hướng:

```text
default-src 'self';
script-src 'self' 'nonce-...';
style-src 'self' 'unsafe-inline';
img-src 'self' https: data:;
connect-src 'self' https://api.example.com;
frame-ancestors 'none';
base-uri 'self';
form-action 'self';
```

Cấu hình thực tế phải phù hợp chart, CDN, analytics và image host.

Ưu tiên nonce/hash thay vì mở rộng `unsafe-inline` cho script.

---

## 16. Security headers

Khuyến nghị:

```text
Strict-Transport-Security
Content-Security-Policy
X-Content-Type-Options: nosniff
Referrer-Policy
Permissions-Policy
Cross-Origin-Opener-Policy
Cross-Origin-Resource-Policy
```

Không phụ thuộc hoàn toàn vào header cũ như `X-XSS-Protection`.

---

## 17. HTTPS

- Production bắt buộc HTTPS.
- Không mixed content.
- Cookie nhạy cảm dùng `Secure`.
- HSTS được bật sau khi xác nhận toàn bộ domain hỗ trợ HTTPS.
- Internal API cũng nên dùng TLS nếu đi qua mạng không tin cậy.

---

## 18. CORS

CORS phải whitelist origin cụ thể.

Không dùng:

```text
Access-Control-Allow-Origin: *
```

cho endpoint có credential hoặc dữ liệu nhạy cảm.

Chỉ cho phép method và header cần thiết.

---

## 19. Input validation

Frontend validation chỉ hỗ trợ UX. Backend phải validate lại.

Kiểm tra:

- Kiểu dữ liệu.
- Độ dài.
- Enum.
- Giá trị số.
- Date range.
- Branch scope.
- File upload.
- Sort field whitelist.
- Filter field whitelist.

Không nối trực tiếp input vào SQL, command hoặc URL nội bộ.

---

## 20. File upload security

Nếu Dashboard cho upload ảnh, tài liệu hoặc file import:

- Kiểm tra MIME type và extension.
- Giới hạn dung lượng.
- Đổi tên file an toàn.
- Không dùng tên file người dùng làm path trực tiếp.
- Quét malware nếu hạ tầng hỗ trợ.
- Tách storage khỏi application server khi phù hợp.
- Không cho upload HTML hoặc executable nếu không cần.
- Ảnh nên re-encode phía server.

File private phải dùng signed URL có thời hạn.

---

## 21. Export security

Export là điểm rủi ro cao.

Yêu cầu:

- Kiểm tra quyền lại tại thời điểm tạo export.
- Áp dụng đúng branch/date/filter scope.
- Không tin column list từ client nếu vượt quyền.
- File download có thời hạn.
- Signed URL không public lâu dài.
- Tên file không chứa dữ liệu nhạy cảm dư thừa.
- Ghi audit log cho export nhạy cảm.

CSV cần xử lý formula injection bằng cách neutralize giá trị bắt đầu với:

```text
=
+
-
@
```

khi dữ liệu có thể do người dùng nhập.

---

## 22. Search privacy

Global Search chỉ được trả dữ liệu trong scope.

Không nên hiển thị quá nhiều PII trong dropdown.

Ví dụ:

- Mask số điện thoại khi không cần đầy đủ.
- Không hiển thị địa chỉ chi tiết.
- Không tìm kiếm theo trường nhạy cảm nếu người dùng không có quyền.
- Giới hạn số kết quả.
- Rate limit để tránh enumeration.

---

## 23. PII masking

Tùy vai trò, có thể mask:

```text
0987 *** 321
ng***@example.com
```

Nguyên tắc:

- Người xử lý đơn có thể cần số điện thoại đầy đủ.
- Người chỉ xem báo cáo tổng hợp không cần PII đầy đủ.
- Export phải tuân theo cùng permission masking.

Không mask theo frontend בלבד; backend nên trả dữ liệu phù hợp quyền.

---

## 24. Logging policy

Không log:

- Password.
- OTP.
- Access token.
- Session cookie.
- Full payment details.
- Địa chỉ khách hàng đầy đủ.
- Số điện thoại đầy đủ nếu không cần.
- Nội dung file private.

Log nên có:

- Request ID.
- User ID nội bộ.
- Role/scope không nhạy cảm.
- Action.
- Entity ID.
- Result.
- Error code.
- Timestamp.

---

## 25. Audit log

Bắt buộc hoặc khuyến nghị mạnh cho:

- Đăng nhập thất bại nhiều lần.
- Thay đổi quyền.
- Tạo hoặc vô hiệu hóa tài khoản.
- Xem hoặc export báo cáo nhạy cảm.
- Hủy đơn.
- Hoàn tiền.
- Cập nhật tồn kho thủ công.
- Thay đổi giá.
- Thay đổi cấu hình hệ thống.

Audit log cần chống sửa đổi trái phép và có retention phù hợp.

---

## 26. Audit event contract

```ts
export type AuditEvent = {
  id: string;
  actorUserId: string;
  actorRole?: string;
  action: string;
  entityType?: string;
  entityId?: string;
  branchId?: string;
  result: "success" | "failure";
  reason?: string;
  requestId?: string;
  ipHash?: string;
  userAgent?: string;
  occurredAt: string;
};
```

Không lưu dữ liệu nhạy cảm dư thừa trong payload audit.

---

## 27. Rate limiting

Áp dụng cho:

- Login.
- Password reset.
- OTP/MFA.
- Search.
- Export.
- Bulk mutation.
- Notification mark-all.
- File upload.

Khi bị giới hạn, trả error có thể hiểu được:

```text
Bạn đã thực hiện quá nhiều yêu cầu. Vui lòng thử lại sau.
```

Không tiết lộ chi tiết nội bộ về thuật toán rate limit.

---

## 28. Brute-force protection

Login cần:

- Rate limiting theo tài khoản và IP/risk signal.
- Progressive delay.
- Alert khi thất bại bất thường.
- Không tiết lộ tài khoản có tồn tại hay không.

Message phù hợp:

```text
Thông tin đăng nhập không chính xác.
```

---

## 29. Account lifecycle

Quy trình phải có:

- Tạo tài khoản.
- Gán vai trò.
- Thay đổi quyền.
- Tạm khóa.
- Vô hiệu hóa.
- Thu hồi session.
- Xóa hoặc anonymize theo chính sách.

Nhân viên nghỉ việc phải bị thu hồi quyền kịp thời.

---

## 30. Privileged actions

Các action rủi ro cao cần thêm kiểm soát:

- Confirm dialog.
- Re-authentication hoặc MFA nếu cần.
- Reason/note bắt buộc.
- Audit log.
- Version/concurrency check.

Ví dụ:

- Hoàn tiền.
- Hủy đơn giá trị lớn.
- Sửa giá vốn.
- Điều chỉnh tồn kho thủ công.
- Thay đổi vai trò quản trị.

---

## 31. Sensitive data in UI

Không hiển thị mặc định:

- Full address trong bảng.
- Thông tin thanh toán chi tiết.
- Token hoặc ID kỹ thuật nhạy cảm.
- Giá vốn cho người không có quyền.
- Audit metadata cho người không liên quan.

Dữ liệu chi tiết chỉ mở khi người dùng có quyền và thực sự cần.

---

## 32. Clipboard security

Nếu có nút copy:

- Chỉ copy dữ liệu cần thiết.
- Thông báo rõ đã copy gì.
- Không copy token hoặc secret.
- Có thể mask trên UI nhưng copy full value chỉ khi permission cho phép.

Không tự động đọc clipboard.

---

## 33. Notification privacy

Notification không nên chứa PII dư thừa.

Ví dụ tốt:

```text
Đơn #CYN-240806-0187 đang chờ xác nhận.
```

Thay vì:

```text
Nguyễn Văn Minh, số điện thoại 0987..., địa chỉ đầy đủ...
```

Nếu notification gửi qua email hoặc Zalo OA, cần xem đó là kênh bên ngoài và giảm dữ liệu nhạy cảm hơn nữa.

---

## 34. Browser storage

Không lưu trong `localStorage`:

- Token nhạy cảm.
- Dữ liệu khách hàng.
- Báo cáo tài chính.
- Nội dung đơn hàng đầy đủ.

Có thể lưu:

- Sidebar collapsed preference.
- Theme preference.
- Filter không nhạy cảm.

Dữ liệu cache phải được làm sạch khi logout nếu có nguy cơ lộ thông tin giữa tài khoản.

---

## 35. Client cache isolation

Cache key phải có:

- User identity hoặc session boundary.
- Branch scope.
- Date range.
- Permission-sensitive context.

Khi logout hoặc đổi tài khoản:

- Clear query cache nhạy cảm.
- Clear persisted state liên quan.
- Không để dữ liệu tài khoản trước hiển thị thoáng qua.

---

## 36. Clickjacking protection

Dashboard quản trị không nên bị nhúng trong iframe ngoài ý muốn.

Dùng:

```text
Content-Security-Policy: frame-ancestors 'none'
```

hoặc whitelist cụ thể nếu có nhu cầu nhúng hợp lệ.

---

## 37. Open redirect prevention

Các tham số như:

```text
returnUrl
redirect
next
```

phải được whitelist path nội bộ hoặc domain tin cậy.

Không redirect trực tiếp đến URL tùy ý do người dùng cung cấp.

---

## 38. SSRF và backend proxy

Nếu BFF cho phép tải URL ảnh hoặc tài liệu:

- Chỉ cho domain whitelist.
- Chặn localhost, metadata IP và private network nếu không cần.
- Validate protocol.
- Giới hạn response size và timeout.

Frontend không nên cung cấp endpoint proxy URL tùy ý.

---

## 39. Dependency security

- Dùng lockfile.
- Quét dependency vulnerability.
- Review package mới.
- Tránh package ít bảo trì cho authentication, crypto hoặc sanitizer.
- Cập nhật security patch kịp thời.
- Không chạy script install không rõ nguồn gốc.

Các package chart, editor và file parser cần chú ý XSS và prototype pollution.

---

## 40. Secret management

Secret phải nằm trong:

- Secret manager.
- CI/CD protected variables.
- Deployment platform secrets.

Không nằm trong:

- Git.
- `.env.example` với giá trị thật.
- Screenshot.
- Issue công khai.
- Frontend bundle.
- Log CI.

Nếu secret bị lộ, phải rotate ngay; xóa commit không đủ.

---

## 41. Privacy notice và purpose limitation

Hệ thống cần xác định rõ mục đích sử dụng dữ liệu khách hàng:

- Xử lý đơn hàng.
- Giao hàng.
- Chăm sóc khách hàng.
- Báo cáo vận hành.
- Marketing nếu có căn cứ phù hợp.

Không tái sử dụng dữ liệu cho mục đích khác mà thiếu thông báo hoặc căn cứ cần thiết.

---

## 42. Data retention

Cần policy cho:

- Đơn hàng.
- Customer profile.
- Audit logs.
- Notification history.
- Export files.
- Session logs.
- Uploaded files.

Ví dụ định hướng:

- Export temporary: vài giờ hoặc vài ngày.
- Notification UI: giữ theo nhu cầu vận hành.
- Audit log: lâu hơn theo yêu cầu kiểm soát.

Thời hạn thực tế phải do pháp lý và nghiệp vụ phê duyệt.

---

## 43. Data deletion và anonymization

Khi cần xóa dữ liệu cá nhân:

- Xác minh yêu cầu.
- Kiểm tra nghĩa vụ lưu trữ pháp lý.
- Xóa hoặc anonymize khỏi hệ thống hoạt động.
- Xử lý bản sao và export trong phạm vi hợp lý.
- Ghi audit việc xử lý yêu cầu.

Không xóa dữ liệu kế toán hoặc hóa đơn trái nghĩa vụ lưu trữ.

---

## 44. Third-party services

Khi dùng:

- Analytics.
- Error monitoring.
- Email.
- Zalo OA.
- CDN.
- Storage.
- Chat/support.

cần đánh giá:

- Dữ liệu nào được gửi.
- Vị trí lưu trữ.
- Retention.
- Quyền truy cập.
- DPA hoặc điều khoản phù hợp.
- Khả năng tắt hoặc giảm tracking.

Không gửi PII đầy đủ vào analytics hoặc error monitoring nếu không cần.

---

## 45. Analytics privacy

Analytics chỉ nên thu:

- Route.
- Feature usage.
- Performance.
- Error category.
- Role hoặc branch dạng không nhạy cảm nếu thật sự cần.

Không thu:

- Tên khách hàng.
- Số điện thoại.
- Địa chỉ.
- Nội dung search nhạy cảm.
- Giá trị tài chính chi tiết gắn với cá nhân.

---

## 46. Error reporting privacy

Trước khi gửi error context:

- Redact URL query nhạy cảm.
- Redact request body.
- Mask user input.
- Không attach screenshot chứa PII nếu chưa kiểm soát.
- Không record session toàn bộ trên màn hình dữ liệu nhạy cảm.

---

## 47. Security testing

Bắt buộc kiểm tra:

- Authentication bypass.
- IDOR/BOLA.
- Permission escalation.
- CSRF.
- XSS.
- Open redirect.
- CSV injection.
- File upload bypass.
- Rate limit.
- Session fixation.
- Logout cache clearing.
- Branch scope bypass.
- Export scope bypass.

Có thể dùng automated scanning, nhưng phải có manual test cho business authorization.

---

## 48. Threat model tối thiểu

Tài sản cần bảo vệ:

- Customer PII.
- Financial KPI.
- Inventory data.
- Order workflow.
- User accounts.
- Permissions.
- Export files.

Tác nhân đe dọa:

- Người ngoài.
- Tài khoản bị chiếm.
- Nhân viên vượt quyền.
- Script tự động.
- Third-party compromise.

Attack surfaces:

- Login.
- Search.
- API.
- Export.
- Upload.
- Notification links.
- Admin settings.

---

## 49. Incident response cho dữ liệu

Khi nghi ngờ lộ dữ liệu:

1. Cô lập sự cố.
2. Thu hồi session/token liên quan.
3. Dừng export hoặc tính năng gây rủi ro.
4. Xác định phạm vi dữ liệu.
5. Bảo toàn log và bằng chứng.
6. Khôi phục kiểm soát.
7. Thông báo bên liên quan theo quy trình pháp lý.
8. Thực hiện postmortem và action items.

Không xóa log điều tra vội vàng.

---

## 50. Security ownership

| Khu vực | Owner chính | Owner phối hợp |
|---|---|---|
| Authentication | Backend/Security | Frontend |
| Authorization | Backend | Product/Frontend |
| Session | Backend/DevOps | Security |
| UI privacy | Frontend/Product | Legal/Security |
| Audit logs | Backend | Security/Operations |
| Secrets | DevOps | Engineering |
| Dependency security | Engineering | Security |
| Incident response | Security/Engineering Lead | DevOps/Product |

---

## 51. AI coding agent requirements

AI phải:

- Không hardcode secret.
- Không tắt permission check để sửa lỗi UI.
- Không thêm `dangerouslySetInnerHTML` thiếu sanitizer.
- Không lưu token vào localStorage mặc định.
- Không tạo CORS wildcard cho endpoint credentialed.
- Không ghi PII vào log.
- Không khẳng định hệ thống an toàn chỉ vì build pass.
- Báo rõ rủi ro khi thêm dependency, upload, export hoặc auth flow.
- Không tự thay đổi policy dữ liệu hoặc retention.

---

## 52. Security checklist trước release

- [ ] Authentication hoạt động đúng.
- [ ] MFA áp dụng cho tài khoản đặc quyền nếu yêu cầu.
- [ ] Cookie/session flags đúng.
- [ ] Permission kiểm tra ở backend.
- [ ] Object-level authorization đã test.
- [ ] Branch và warehouse scope không thể bypass.
- [ ] CSRF protection có cho mutation.
- [ ] Không có XSS nghiêm trọng.
- [ ] CSP và security headers được cấu hình.
- [ ] CORS không mở rộng quá mức.
- [ ] Input được validate ở server.
- [ ] Upload được kiểm soát.
- [ ] Export kiểm tra scope và CSV injection.
- [ ] Search không lộ dữ liệu ngoài quyền.
- [ ] Log không chứa secret hoặc PII dư thừa.
- [ ] Audit log có cho action nhạy cảm.
- [ ] Rate limiting áp dụng cho endpoint rủi ro.
- [ ] Cache được clear khi logout.
- [ ] Dependency scan không còn lỗi critical chưa xử lý.
- [ ] Secret không xuất hiện trong bundle hoặc repository.
- [ ] Third-party data sharing đã được review.
- [ ] Incident contact và rollback plan sẵn sàng.

---

## 53. Những điều không được làm

- Không tin quyền do frontend gửi lên.
- Không chỉ ẩn button mà bỏ backend authorization.
- Không trả dữ liệu toàn hệ thống rồi lọc ở frontend.
- Không lưu token nhạy cảm trong localStorage nếu có thể tránh.
- Không log password, token hoặc PII đầy đủ.
- Không tạo signed URL không hết hạn.
- Không export dữ liệu ngoài scope.
- Không dùng CORS wildcard với credential.
- Không render HTML chưa sanitize.
- Không public source map hoặc secret ngoài ý muốn.
- Không giữ tài khoản nhân viên nghỉ việc hoạt động.
- Không dùng dữ liệu khách hàng cho mục đích mới thiếu kiểm soát.
- Không tuyên bố tuân thủ pháp lý nếu chưa được bộ phận pháp lý xác nhận.

---

## 54. Kết luận

Security và Privacy của Cynca VLXD Dashboard phải được thực hiện đồng thời ở giao diện, API, session, permission, export, search, logging và vận hành. Frontend chỉ là một lớp trong hệ thống phòng thủ; quyền truy cập và phạm vi dữ liệu phải được backend kiểm soát tuyệt đối. Mọi tính năng mới liên quan đến dữ liệu khách hàng, tài chính, upload, export hoặc phân quyền phải được review bảo mật trước khi phát hành.

File tiếp theo đề xuất:

```text
README.md
24-Observability.md
```