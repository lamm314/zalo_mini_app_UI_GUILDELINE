# Cynca VLXD Admin Dashboard — System Settings Specification

> Phiên bản: 1.0  
> Phạm vi: Giao diện cấu hình hệ thống, tổ chức, nghiệp vụ, thông báo và tích hợp  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, DevOps, Security, QA và quản trị hệ thống  
> Tài liệu liên quan: `23-Security-Privacy.md`, `27-Role-Based-Dashboard.md`, `29-Dashboard-Admin-Configurator.md`, `39-Audit-Activity-Center.md`, `40-User-Role-Management.md`

---

## 1. Mục tiêu

System Settings là khu vực tập trung để quản trị viên cấu hình các hành vi dùng chung của Cynca VLXD mà không phải sửa trực tiếp mã nguồn.

Hệ thống phải hỗ trợ:

- Cấu hình thông tin tổ chức và chi nhánh.
- Thiết lập định dạng ngày giờ, tiền tệ và múi giờ.
- Cấu hình quy tắc đơn hàng, kho, tài chính và chăm sóc khách hàng.
- Cấu hình notification, email và Zalo OA.
- Quản lý tích hợp và trạng thái đồng bộ.
- Quản lý feature flags và rollout có kiểm soát.
- Xem trước thay đổi, validate, publish và rollback.
- Audit toàn bộ thay đổi quan trọng.
- Không cho nhập mã tùy ý, secret hoặc endpoint không được kiểm soát.

System Settings không phải là trình chỉnh sửa database hoặc file cấu hình thô.

---

## 2. Nguyên tắc cốt lõi

1. Mọi setting phải có key, type, phạm vi và owner rõ ràng.
2. Giá trị nhạy cảm không được hiển thị lại sau khi lưu.
3. Backend là nguồn quyết định cuối cùng.
4. Setting phải có validation và default an toàn.
5. Thay đổi quan trọng cần preview tác động.
6. Có versioning, audit và rollback.
7. Không dùng raw JSON làm giao diện quản trị duy nhất.
8. Không cho setting mở rộng permission hoặc data scope.
9. Không áp dụng thay đổi production trước khi backend xác nhận.
10. Không để một setting lỗi làm hỏng toàn hệ thống.

---

## 3. Information architecture

```text
System Settings
├── General
├── Organization
├── Branches & Warehouses
├── Localization
├── Orders
├── Inventory & Warehouse
├── Finance
├── Customer Service
├── Notifications
├── Integrations
├── Feature Flags
├── Data & Retention
├── Security
└── Change History
```

Mỗi khu vực chỉ hiển thị khi người dùng có permission tương ứng.

---

## 4. Permission model

```text
settings.view
settings.general.update
settings.organization.update
settings.branches.update
settings.orders.update
settings.inventory.update
settings.finance.update
settings.support.update
settings.notifications.update
settings.integrations.view
settings.integrations.update
settings.feature_flags.view
settings.feature_flags.update
settings.security.view
settings.security.update
settings.publish
settings.rollback
settings.audit.view
```

Các quyền `settings.security.update`, `settings.integrations.update`, `settings.publish` và `settings.rollback` phải được xem là quyền nhạy cảm.

---

## 5. Setting contract

```ts
export type SystemSetting<T = unknown> = {
  key: string;
  name: string;
  description?: string;
  category: string;
  value: T;
  defaultValue: T;
  valueType: "string" | "number" | "boolean" | "select" | "multi_select" | "duration" | "json_object";
  scope: "system" | "branch" | "warehouse";
  sensitivity: "normal" | "sensitive" | "secret";
  editable: boolean;
  restartRequired?: boolean;
  validation?: SettingValidationRule[];
  updatedAt?: string;
  updatedBy?: string;
};
```

---

## 6. Validation rule

```ts
export type SettingValidationRule = {
  type: "required" | "min" | "max" | "pattern" | "enum" | "dependency" | "conflict";
  value?: unknown;
  message: string;
};
```

Validation phải chạy cả frontend và backend.

Frontend chỉ giúp phản hồi sớm; backend vẫn phải từ chối giá trị không hợp lệ.

---

## 7. General Settings

Các trường đề xuất:

- Tên hệ thống.
- Logo quản trị.
- Tên thương hiệu `Cynca VLXD`.
- Email hỗ trợ.
- Hotline.
- Website.
- Trang mặc định sau đăng nhập.
- Múi giờ mặc định.
- Ngôn ngữ mặc định.
- Số dòng mặc định trong bảng.
- Thời gian tự refresh Dashboard.

Không cho tải logo hoặc file thiếu kiểm tra loại, dung lượng và bảo mật.

---

## 8. Organization Settings

Thông tin:

- Tên pháp lý.
- Mã số doanh nghiệp.
- Địa chỉ trụ sở.
- Tên giao dịch.
- Người đại diện.
- Đơn vị tiền tệ mặc định.
- Năm tài chính.
- Chính sách hiển thị dữ liệu tổ chức.

Các trường pháp lý nên có owner và quy trình duyệt riêng nếu ảnh hưởng chứng từ hoặc báo cáo.

---

## 9. Branch & Warehouse Settings

### Branch

- Mã chi nhánh.
- Tên.
- Trạng thái.
- Múi giờ.
- Địa chỉ.
- Quản lý chi nhánh.
- Kho mặc định.
- SLA mặc định.

### Warehouse

- Mã kho.
- Tên kho.
- Chi nhánh.
- Trạng thái.
- Đơn vị tính mặc định.
- Cấu hình tồn âm.
- Ngưỡng dữ liệu stale.
- Chính sách quét mã.

Không cho xóa chi nhánh hoặc kho đang được dữ liệu nghiệp vụ tham chiếu; ưu tiên archive hoặc disable.

---

## 10. Localization Settings

Các cấu hình:

```text
Locale: vi-VN
Timezone: Asia/Ho_Chi_Minh
Currency: VND
Date format: DD/MM/YYYY
Time format: 24-hour
Number separator
First day of week
```

Quy tắc:

- Backend lưu timestamp UTC.
- UI hiển thị theo timezone đã chọn.
- Không thay đổi mã đơn, SKU hoặc identifier theo locale.
- Tiền tệ phải có định dạng đầy đủ và compact thống nhất.

---

## 11. Order Settings

Các nhóm:

### Lifecycle

- Trạng thái được phép.
- Transition rules.
- Trạng thái mặc định.
- Hủy và hoàn trả.

### SLA

- Thời gian xác nhận.
- Thời gian xử lý.
- Thời gian chờ giao.
- Quy tắc quá hạn.

### Assignment

- Tự động phân công.
- Branch/team assignment.
- Manual override.

### Numbering

- Prefix mã đơn.
- Sequence format.
- Reset theo năm/tháng nếu nghiệp vụ cho phép.

Không cho đổi numbering rule làm trùng mã đã tồn tại.

---

## 12. Order transition configuration

```ts
export type OrderTransitionRule = {
  fromStatus: string;
  toStatus: string;
  requiredPermissions: string[];
  confirmationRequired: boolean;
  reasonRequired?: boolean;
  sideEffects?: string[];
};
```

UI cần hiển thị flow trực quan và cảnh báo transition bị cô lập hoặc vòng lặp bất hợp lý.

Không cho admin nhập JavaScript handler.

---

## 13. Inventory Settings

Các cấu hình:

- Cho phép hoặc chặn tồn âm.
- Reorder point mặc định.
- Low-stock threshold.
- Slow-moving window.
- Overstock rule.
- Cycle count frequency.
- Adjustment approval threshold.
- Đồng bộ tồn kho.
- Reservation policy.

Các rule phải có tooltip định nghĩa và owner nghiệp vụ.

Không dùng một threshold tuyệt đối cho mọi SKU nếu hệ thống hỗ trợ rule theo sản phẩm.

---

## 14. Finance Settings

Các cấu hình:

- Kỳ tài chính.
- Trạng thái provisional/closed.
- Điều khoản thanh toán mặc định.
- Debt aging buckets.
- Refund approval threshold.
- Reconciliation tolerance.
- Currency rounding.
- Tax/invoice integration behavior.
- Data close schedule.

Thay đổi Finance Settings phải có review và audit chặt chẽ.

Không cho thay đổi công thức KPI tài chính trong UI chung nếu chưa có quy trình Finance phê duyệt.

---

## 15. Customer Service Settings

Các cấu hình:

- Ticket priorities.
- SLA theo priority và channel.
- Complaint taxonomy.
- Escalation routes.
- Auto-assignment.
- Reopen policy.
- Callback reminder.
- Survey timing.
- CSAT minimum sample warning.

SLA và escalation phải được validate để không tạo rule thiếu owner hoặc deadline.

---

## 16. Notification Settings

Các nhóm:

- Notification categories được bật.
- Severity routing.
- In-app notifications.
- Email notifications.
- Zalo OA notifications.
- Digest schedule.
- Quiet hours.
- Deduplication window.
- Retry policy.

Không cho tắt hoàn toàn security-critical notifications nếu policy bắt buộc.

---

## 17. Notification template editor

Template fields:

- Template key.
- Channel.
- Subject/title.
- Body.
- Variables được phép.
- Preview.
- Fallback language.

Ví dụ biến:

```text
{{order_code}}
{{customer_name_masked}}
{{due_time}}
{{branch_name}}
```

Yêu cầu:

- Chỉ dùng variable whitelist.
- Không hỗ trợ script.
- Không cho chèn secret hoặc raw HTML nguy hiểm.
- Preview với dữ liệu mẫu đã mask.

---

## 18. Integration Settings

Các tích hợp có thể gồm:

- Zalo OA.
- Zalo Mini App backend.
- Payment gateway.
- Shipping provider.
- Email provider.
- Accounting system.
- Inventory/WMS.
- Object storage.
- Analytics/monitoring.

Mỗi integration hiển thị:

- Tên.
- Trạng thái.
- Environment.
- Last successful sync.
- Last error.
- Owner.
- Actions.

---

## 19. Secret handling

Secret fields:

- API key.
- Client secret.
- Signing secret.
- Webhook secret.
- Private token.

Quy tắc:

- Chỉ hiển thị masked value.
- Không trả secret cũ về client.
- Khi cập nhật phải nhập giá trị mới.
- Có rotate action.
- Có test connection.
- Có audit event nhưng không ghi secret.
- Không lưu secret trong frontend state lâu hơn cần thiết.

---

## 20. Integration test connection

Luồng:

```text
Nhập/cập nhật cấu hình
→ Lưu draft
→ Test connection
→ Xem kết quả
→ Publish
```

Kết quả test:

```text
Success
Authentication failed
Endpoint unavailable
Permission insufficient
Configuration invalid
Timeout
```

Không hiển thị raw response chứa secret hoặc internal stack trace.

---

## 21. Webhook settings

Nếu hỗ trợ webhook:

- Event subscriptions.
- Destination URL whitelist hoặc validation.
- Signing secret.
- Retry policy.
- Timeout.
- Delivery logs.
- Disable switch.

Yêu cầu:

- Chặn private/internal destinations nếu policy yêu cầu.
- Chống SSRF.
- Không hiển thị signing secret sau khi lưu.
- Có rotate và test delivery.

---

## 22. Feature Flags

```ts
export type FeatureFlag = {
  key: string;
  name: string;
  description?: string;
  status: "on" | "off" | "scheduled";
  environment: "development" | "staging" | "production";
  targeting?: {
    roleIds?: string[];
    branchIds?: string[];
    userIds?: string[];
    percentage?: number;
  };
  startsAt?: string;
  endsAt?: string;
  owner: string;
  updatedAt: string;
};
```

Không dùng feature flag như authorization.

---

## 23. Feature flag editor

Hỗ trợ:

- Bật/tắt.
- Rollout theo phần trăm.
- Theo role.
- Theo branch.
- Theo user thử nghiệm.
- Schedule.
- Preview audience.
- Kill switch.

Yêu cầu:

- Cảnh báo khi target production lớn.
- Có owner và expiry review.
- Không cho flag tồn tại vĩnh viễn thiếu owner.
- Có audit và rollback.

---

## 24. Data & Retention Settings

Các nhóm:

- Audit retention.
- Notification retention.
- Export file expiry.
- Session retention.
- Analytics retention.
- Soft-delete retention.
- Backup policy summary.

Frontend chỉ quản lý giá trị policy được backend cho phép.

Không cho giảm retention nhạy cảm thiếu cảnh báo tác động và approval.

---

## 25. Security Settings

Có thể gồm:

- Session timeout.
- Idle timeout.
- Maximum concurrent sessions.
- MFA requirement theo role.
- Password policy nếu hệ thống tự quản lý mật khẩu.
- Login attempt limit.
- Lockout duration.
- Allowed domains.
- IP allowlist nếu có.
- Re-authentication cho action nhạy cảm.

Không cho tắt authentication, audit hoặc MFA bắt buộc qua một toggle đơn giản thiếu approval.

---

## 26. Setting scope và override

Thứ tự ưu tiên:

```text
System default
→ Organization policy
→ Branch override
→ Warehouse override
```

Không phải setting nào cũng hỗ trợ override.

UI phải hiển thị:

- Giá trị hiện tại.
- Giá trị kế thừa.
- Nguồn giá trị.
- Override đang hoạt động.
- Nút reset về inherited value.

---

## 27. Draft, validation và publish

Lifecycle:

```text
Draft
→ Validated
→ Ready for Review
→ Approved
→ Published
→ Superseded
```

Không áp dụng ngay mọi thay đổi khi người dùng đang nhập.

Các setting ít rủi ro có thể auto-save draft nhưng vẫn cần explicit publish nếu ảnh hưởng toàn hệ thống.

---

## 28. Change set

Nhiều setting nên được gom thành một change set:

```ts
export type SettingsChangeSet = {
  id: string;
  title: string;
  status: "draft" | "validation_failed" | "ready_for_review" | "approved" | "published" | "rolled_back";
  changes: SettingChange[];
  requestedBy: string;
  approvedBy?: string;
  createdAt: string;
  publishedAt?: string;
};
```

---

## 29. Impact preview

Trước publish hiển thị:

- Setting thay đổi.
- Giá trị trước/sau.
- Scope ảnh hưởng.
- Số branch/warehouse bị ảnh hưởng.
- Module phụ thuộc.
- Có cần restart hay không.
- Rủi ro và warning.
- Rollback target.

Không chỉ hiển thị raw JSON diff.

---

## 30. Approval workflow

Setting cần approval:

- Security policy.
- Finance policy.
- Permission-sensitive integration.
- Feature flag production diện rộng.
- Retention policy.
- Webhook hoặc payment integration.

Luồng:

```text
Editor tạo change set
→ Validate
→ Gửi review
→ Reviewer approve/reject
→ Publisher publish
```

---

## 31. Scheduled changes

Có thể hỗ trợ publish theo lịch:

- Thời điểm cụ thể.
- Timezone rõ ràng.
- Maintenance window.
- Expiry hoặc auto-revert cho thử nghiệm.

Không schedule thay đổi nếu dependency chưa sẵn sàng.

---

## 32. Rollback

Rollback phải:

- Tạo version mới dựa trên version cũ.
- Không xóa lịch sử.
- Validate lại với schema hiện tại.
- Kiểm tra integration/feature không còn tồn tại.
- Hiển thị impact.
- Có audit.

Không rollback mù nếu data migration hoặc external dependency đã thay đổi.

---

## 33. Conflict handling

Khi nhiều admin cùng sửa:

- Dùng version hoặc ETag.
- Backend trả 409.
- UI cho reload, compare hoặc duplicate change set.
- Không ghi đè âm thầm.

---

## 34. Change History

Mỗi version hiển thị:

- Version.
- Change set.
- Người tạo.
- Người duyệt.
- Người publish.
- Thời gian.
- Scope.
- Diff summary.
- Rollback status.
- Incident link nếu có.

---

## 35. Audit events

Bắt buộc ghi:

```text
settings.change_set.created
settings.change_set.updated
settings.change_set.validated
settings.change_set.approved
settings.change_set.published
settings.change_set.rolled_back
settings.integration.secret_rotated
settings.feature_flag.updated
settings.security_policy.updated
```

Audit không được chứa secret value.

---

## 36. API endpoints đề xuất

```text
GET    /api/v1/settings
GET    /api/v1/settings/schema
GET    /api/v1/settings/:key
POST   /api/v1/settings/change-sets
PATCH  /api/v1/settings/change-sets/:id
POST   /api/v1/settings/change-sets/:id/validate
POST   /api/v1/settings/change-sets/:id/review
POST   /api/v1/settings/change-sets/:id/approve
POST   /api/v1/settings/change-sets/:id/publish
POST   /api/v1/settings/change-sets/:id/rollback
GET    /api/v1/settings/history
POST   /api/v1/integrations/:id/test
POST   /api/v1/integrations/:id/rotate-secret
GET    /api/v1/feature-flags
PATCH  /api/v1/feature-flags/:key
```

---

## 37. Frontend component inventory

```text
SystemSettingsPage
├── SettingsSidebar
├── SettingsSection
├── SettingField
├── InheritedValueBadge
├── ScopeOverrideEditor
├── IntegrationCard
├── SecretField
├── ConnectionTestPanel
├── NotificationTemplateEditor
├── FeatureFlagTable
├── FeatureFlagEditor
├── SettingsValidationPanel
├── ImpactPreviewDialog
├── ChangeSetReview
└── SettingsHistoryTable
```

---

## 38. Loading, empty và error states

### Loading

- Navigation skeleton.
- Settings form skeleton.
- Integration cards skeleton.
- History table skeleton.

### Empty

```text
Không có cấu hình nào trong nhóm này.
Không có thay đổi nào đang chờ duyệt.
Chưa có lịch sử thay đổi.
```

### Error

- Giữ nguyên giá trị draft local nếu save lỗi.
- Permission/schema service lỗi phải fail closed.
- Test connection lỗi hiển thị mã lỗi chuẩn hóa.
- Publish lỗi không thay trạng thái local thành Published.
- Có request ID cho lỗi quan trọng.

---

## 39. Responsive behavior

### Desktop

- Sidebar settings cố định.
- Form content 720–900px.
- Preview/diff ở panel phải hoặc dialog rộng.

### Tablet

- Sidebar thành drawer.
- Form một cột.
- Tables scroll trong wrapper.

### Mobile

- Chỉ hỗ trợ xem và chỉnh setting đơn giản.
- Integration, security và feature flag phức tạp nên hiển thị khuyến nghị dùng desktop.
- Dialog thành full-screen sheet.

---

## 40. Accessibility

- Mỗi field có label và description.
- Error liên kết bằng `aria-describedby`.
- Toggle có trạng thái rõ.
- Không chỉ dùng màu để báo inherited/override.
- Keyboard có thể chỉnh, validate và review.
- Dialog quản lý focus.
- Diff có nhãn trước/sau.
- Touch target tối thiểu 44px.
- Zoom 200% vẫn hoàn tất luồng chính.

---

## 41. Security requirements

- Backend enforce permission và setting scope.
- Không trả secret cũ về client.
- Không cho arbitrary JavaScript, HTML, CSS hoặc SQL.
- Không cho raw endpoint thiếu validation.
- Chống SSRF với webhook/integration URL.
- Sanitize template content.
- Publish và rollback phải audit.
- Cache key gồm permission và scope.
- Không lưu secret vào analytics, logs hoặc browser storage.

---

## 42. Analytics và observability

Analytics có thể ghi:

```text
settings.page.viewed
settings.section.opened
settings.change_set.created
settings.validation.completed
settings.review.requested
settings.publish.completed
settings.publish.failed
settings.feature_flag.updated
```

Không gửi setting values, secret, endpoint hoặc template body vào analytics.

Observability theo dõi:

- Settings API latency.
- Validation failures.
- Publish failures.
- Integration test failures.
- Feature flag propagation delay.
- Configuration cache invalidation.
- Rollback failures.
- Permission denied anomalies.

---

## 43. Testing

### Unit

- Validation rules.
- Inheritance/override resolution.
- Change-set diff.
- Feature flag targeting.
- Secret masking.
- Conflict detection.

### Component

- Setting fields.
- Scope override.
- Integration card.
- Secret rotation dialog.
- Notification template editor.
- Feature flag editor.
- Impact preview.

### Integration

- Branch override không ảnh hưởng branch khác.
- Secret cũ không được trả về client.
- Publish chờ backend confirmation.
- Sensitive setting cần approval.
- Feature flag rollout đúng target.
- Conflict 409 không ghi đè.
- Rollback tạo version mới.

### E2E

- Cập nhật General Settings.
- Tạo branch override.
- Thay SLA đơn hàng.
- Test integration.
- Rotate secret.
- Tạo notification template.
- Rollout feature flag cho một branch.
- Publish change set.
- Rollback version.

---

## 44. Visual QA

Baseline:

```text
System Settings — 1440 × 900
General Settings
Branch Override
Order SLA Settings
Finance Sensitive Warning
Integration Connected
Integration Error
Secret Rotation
Notification Template Editor
Feature Flag Rollout
Validation Errors
Impact Preview
Change History
Mobile Settings
```

---

## 45. AI coding agent requirements

AI coding agent phải:

- Không biến System Settings thành raw JSON editor duy nhất.
- Không cho nhập arbitrary code hoặc SQL.
- Không trả hoặc log secret.
- Không publish trước backend confirmation.
- Không cho branch override vượt organization policy.
- Không dùng feature flag như authorization.
- Không rollback thiếu validation.
- Bổ sung test cho secret masking, SSRF protection, inheritance, conflict và approval.
- Báo rõ schema, permissions, scope và backend assumptions.

---

## 46. Acceptance checklist

- [ ] Có Information Architecture rõ ràng.
- [ ] Có permission cho từng nhóm setting.
- [ ] Có setting schema và validation.
- [ ] Có General, Organization, Branch và Localization Settings.
- [ ] Có Order, Inventory, Finance và Customer Service Settings.
- [ ] Có Notification Settings và Template Editor.
- [ ] Có Integration Settings và secret handling.
- [ ] Có Webhook protection.
- [ ] Có Feature Flags và rollout controls.
- [ ] Có Data Retention và Security Settings.
- [ ] Có inheritance và scope override.
- [ ] Có draft, validation, review, publish và rollback.
- [ ] Có Change Set và Impact Preview.
- [ ] Có conflict handling và history.
- [ ] Có audit đầy đủ.
- [ ] Loading, empty, error và responsive states hoàn chỉnh.
- [ ] Accessibility, security, analytics, observability và testing đầy đủ.

---

## 47. Những điều không được làm

- Không cho nhập JavaScript, SQL hoặc CSS tùy ý.
- Không hiển thị secret cũ sau khi lưu.
- Không dùng feature flag thay permission.
- Không cho branch override vượt policy hệ thống.
- Không publish setting chưa validate.
- Không áp dụng thay đổi nhạy cảm thiếu approval.
- Không rollback mù khi dependency đã thay đổi.
- Không ghi secret, endpoint nhạy cảm hoặc template body vào analytics.
- Không xóa lịch sử version khi rollback.
- Không fallback sang cấu hình rộng hoặc kém an toàn khi service lỗi.

---

## 48. Kết luận

System Settings của Cynca VLXD phải cung cấp một nơi cấu hình tập trung, rõ ràng và an toàn cho toàn bộ hệ thống. Mọi giá trị phải dựa trên schema, permission và scope; mọi thay đổi quan trọng phải được validate, preview, review, version hóa, audit và có khả năng rollback có kiểm soát.

File tiếp theo đề xuất:

```text
README.md
42-Design-System-Playground.md
```