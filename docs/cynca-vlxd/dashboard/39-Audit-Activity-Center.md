# Cynca VLXD Admin Dashboard — Audit & Activity Center Specification

> Phiên bản: 1.0  
> Phạm vi: Trung tâm nhật ký kiểm toán, lịch sử thao tác và hoạt động hệ thống  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Security, Compliance, Operations, QA và AI coding agent  
> Tài liệu liên quan: `23-Security-Privacy.md`, `24-Observability.md`, `25-Runbooks.md`, `27-Role-Based-Dashboard.md`, `38-Reports-Export-Center.md`

---

## 1. Mục tiêu

Audit & Activity Center giúp Cynca VLXD theo dõi ai đã thực hiện hành động gì, trên đối tượng nào, vào thời điểm nào và kết quả ra sao.

Hệ thống phải hỗ trợ:

- Tra cứu thao tác theo người dùng, module, entity, thời gian và kết quả.
- Phân biệt audit log, activity feed và technical log.
- Theo dõi các hành động nhạy cảm như phân quyền, hoàn tiền, điều chỉnh tồn kho và xuất báo cáo.
- Xem chi tiết thay đổi trước/sau ở mức an toàn.
- Phát hiện hành vi bất thường hoặc vượt quyền.
- Xuất lịch sử theo đúng permission và scope.
- Giữ log chống sửa đổi trái phép.
- Hỗ trợ điều tra incident và truy vết request.
- Không làm lộ secret, token, PII hoặc dữ liệu tài chính dư thừa.

Audit Center không thay thế hệ thống log kỹ thuật, SIEM hoặc observability platform. Đây là giao diện nghiệp vụ và kiểm soát dành cho người được phép.

---

## 2. Phân biệt Audit, Activity và Technical Logs

### Audit Event

Trả lời:

```text
Ai đã làm gì, trên đối tượng nào, kết quả ra sao?
```

Ví dụ:

- Admin thay đổi quyền user.
- Kế toán duyệt hoàn tiền.
- Quản lý kho điều chỉnh tồn.
- Người dùng tải báo cáo tài chính.

### Activity Event

Phục vụ theo dõi hoạt động nghiệp vụ thông thường.

Ví dụ:

- Đơn được xác nhận.
- Ticket được reassigned.
- Báo cáo hoàn tất.

### Technical Log

Phục vụ kỹ thuật:

- Stack trace.
- API latency.
- Database error.
- Retry details.

Technical logs không nên hiển thị trực tiếp đầy đủ trong Audit Center.

---

## 3. Vai trò sử dụng

### Security / Compliance

- Xem toàn bộ audit event được cấp.
- Theo dõi hành động nhạy cảm.
- Điều tra anomaly.
- Xuất lịch sử kiểm toán.

### System Admin

- Xem thay đổi user, role, configuration và system settings.

### Branch Manager

- Xem activity trong branch được phân công.
- Không xem audit ngoài scope.

### Finance Manager

- Xem audit của refund, reconciliation, export và finance actions.

### Operations Manager

- Xem inventory adjustment, transfer, task assignment và order workflow.

### Read-only Auditor

- Chỉ xem và export theo quyền.
- Không có mutation controls.

---

## 4. Permission model

```text
audit.center.view
audit.events.view
audit.events.view_sensitive
audit.events.export
audit.events.annotate
audit.events.flag
audit.events.view_system
audit.events.view_security
audit.events.view_finance
audit.events.view_inventory
audit.events.view_user_management
audit.retention.view
audit.retention.manage
```

Không dựa duy nhất vào role name.

Backend phải áp cả permission và data scope.

---

## 5. Event categories

```ts
export type AuditEventCategory =
  | "authentication"
  | "authorization"
  | "user_management"
  | "orders"
  | "inventory"
  | "finance"
  | "reports"
  | "customers"
  | "support"
  | "configuration"
  | "security"
  | "system";
```

---

## 6. Event severity

```ts
export type AuditSeverity =
  | "critical"
  | "high"
  | "medium"
  | "low"
  | "info";
```

Ví dụ:

- Critical: permission escalation bất thường, cross-branch access, secret exposure.
- High: refund giá trị lớn, inventory adjustment lớn, failed login spike.
- Medium: user role changed, report export sensitive.
- Low: configuration previewed, notification dismissed.
- Info: login success, report completed.

Severity phải được backend hoặc policy engine xác định.

---

## 7. Event result model

```ts
export type AuditResult =
  | "success"
  | "failure"
  | "denied"
  | "partial"
  | "cancelled";
```

Phân biệt:

- `failure`: hệ thống cố thực hiện nhưng lỗi.
- `denied`: bị chặn do permission hoặc policy.
- `partial`: một phần thao tác thành công.
- `cancelled`: người dùng hoặc hệ thống hủy.

---

## 8. Audit event contract

```ts
export type AuditEvent = {
  id: string;
  occurredAt: string;
  category: AuditEventCategory;
  action: string;
  severity: AuditSeverity;
  result: AuditResult;
  actor: {
    userId?: string;
    displayName?: string;
    roleId?: string;
    actorType: "user" | "service" | "system" | "api_client";
  };
  target?: {
    entityType?: string;
    entityId?: string;
    label?: string;
  };
  scope?: {
    branchId?: string;
    warehouseId?: string;
    organizationId?: string;
  };
  source?: {
    ipHash?: string;
    userAgent?: string;
    sessionId?: string;
    apiClientId?: string;
  };
  requestId?: string;
  traceId?: string;
  reasonCode?: string;
  summary: string;
  changes?: AuditChange[];
  metadata?: Record<string, string | number | boolean | null>;
};
```

---

## 9. Change contract

```ts
export type AuditChange = {
  field: string;
  label: string;
  before?: string | number | boolean | null;
  after?: string | number | boolean | null;
  sensitive?: boolean;
  masked?: boolean;
};
```

Quy tắc:

- Chỉ lưu field cần thiết.
- Không lưu password, token, OTP hoặc secret.
- PII phải mask theo policy.
- Dữ liệu lớn nên lưu diff summary, không lưu full object.

---

## 10. Event naming convention

Dùng dạng:

```text
domain.object.action
```

Ví dụ:

```text
auth.session.created
auth.login.failed
users.role.updated
orders.status.updated
inventory.stock.adjusted
finance.refund.approved
reports.export.downloaded
dashboard.configuration.published
support.ticket.escalated
```

Không dùng tên chung như `update`, `action1`, `done`.

---

## 11. Các audit event bắt buộc

### Authentication

```text
auth.login.succeeded
auth.login.failed
auth.logout.completed
auth.session.revoked
auth.mfa.challenge.failed
```

### User & Permission

```text
users.account.created
users.account.disabled
users.role.updated
roles.permission.updated
scope.assignment.updated
```

### Orders

```text
orders.status.updated
orders.cancelled
orders.assigned
orders.refund.requested
```

### Inventory

```text
inventory.stock.adjusted
inventory.transfer.approved
inventory.count.submitted
warehouse.sync.retried
```

### Finance

```text
finance.refund.approved
finance.refund.rejected
finance.reconciliation.updated
finance.period.reopened
```

### Reports

```text
reports.export.requested
reports.export.completed
reports.export.downloaded
reports.schedule.updated
```

### Configuration

```text
dashboard.configuration.published
dashboard.configuration.rolled_back
settings.updated
```

---

## 12. Information architecture

```text
Audit & Activity Center
├── Tổng quan
├── Audit Events
├── Security Events
├── User & Permission Changes
├── Financial Actions
├── Inventory Actions
├── Reports & Exports
├── Activity Feed
├── Saved Views
└── Retention & Policy
```

Không hiển thị các khu vực người dùng không có quyền.

---

## 13. Trang tổng quan

KPI nhẹ:

- Audit events trong 24 giờ.
- Failed/denied actions.
- High/critical events.
- Permission changes.
- Sensitive exports.
- Unresolved flags.

Widgets:

- Critical events.
- Recent permission changes.
- Suspicious activity summary.
- Top affected modules.
- Audit ingestion status.

Không dùng KPI audit để đánh giá năng suất nhân viên.

---

## 14. Audit Event Table

Cột đề xuất:

- Thời gian.
- Actor.
- Action.
- Target.
- Category.
- Scope.
- Result.
- Severity.
- Request ID.
- Actions.

Bảng phải hỗ trợ:

- Sort theo thời gian và severity.
- Filter nâng cao.
- Cursor pagination.
- Column selection theo permission.
- Sticky header.

---

## 15. Filters

```ts
export type AuditEventFilter = {
  from?: string;
  to?: string;
  categories?: AuditEventCategory[];
  actions?: string[];
  severities?: AuditSeverity[];
  results?: AuditResult[];
  actorUserIds?: string[];
  entityTypes?: string[];
  entityId?: string;
  branchIds?: string[];
  warehouseIds?: string[];
  requestId?: string;
  traceId?: string;
};
```

Filter phải tuân scope người dùng.

Không cho tìm dữ liệu ngoài quyền chỉ bằng entity ID.

---

## 16. Search behavior

Search có thể hỗ trợ:

- User name hoặc internal user ID.
- Entity code.
- Request ID.
- Trace ID.
- Action key.

Không log raw search query nếu chứa PII hoặc internal identifiers nhạy cảm.

Phone/email search chỉ dành cho role được phép và cần minimum query length.

---

## 17. Event detail drawer

Nội dung:

- Event summary.
- Actor.
- Action.
- Target.
- Timestamp.
- Result và reason code.
- Scope.
- Request ID và trace ID.
- Before/after changes.
- Related events.
- Annotation và flag nếu có quyền.

Không hiển thị stack trace kỹ thuật đầy đủ trừ role chuyên trách và source đã được redacted.

---

## 18. Before/after diff

Hiển thị dạng bảng:

| Field | Trước | Sau |
|---|---|---|
| Status | Pending | Approved |
| Role | Sales Staff | Sales Manager |

Quy tắc:

- Highlight nhẹ field thay đổi.
- Sensitive fields phải mask.
- Không hiển thị full JSON mặc định.
- Có tab `Raw Metadata` chỉ cho role được phép.

---

## 19. Related events

Liên kết theo:

- Request ID.
- Trace ID.
- Entity ID.
- Session ID.
- Actor.
- Time window.

Ví dụ workflow refund:

```text
refund.requested
→ refund.review_started
→ refund.approved
→ payment.refund_processing
→ payment.refund_completed
```

Related events giúp điều tra mà không phải search lại thủ công.

---

## 20. Activity Feed

Activity Feed là bản rút gọn, dễ đọc hơn Audit Events.

Ví dụ:

```text
Nguyễn Văn A đã xác nhận đơn #CYN-240806-0187 lúc 18:20.
```

Không hiển thị:

- IP hash.
- Trace ID.
- Metadata kỹ thuật.
- Thay đổi nhạy cảm chi tiết.

Activity Feed có thể dùng trong Branch Manager hoặc Executive Dashboard.

---

## 21. Security Events view

Hiển thị:

- Login failures.
- Session revocation.
- MFA failures.
- Permission denied spikes.
- Cross-scope access attempts.
- Suspicious export volume.
- Unusual API client activity.

Có severity, status điều tra và owner.

Không để người dùng thông thường xem security telemetry chi tiết.

---

## 22. Permission change view

Bảng gồm:

- User/role bị thay đổi.
- Permission trước/sau.
- Người thực hiện.
- Scope.
- Reason.
- Approval reference nếu có.
- Timestamp.

Permission escalation phải được đánh dấu high hoặc critical theo policy.

---

## 23. Financial action view

Theo dõi:

- Refund approval/rejection.
- Reconciliation changes.
- Debt adjustments.
- Closed period reopen.
- Finance exports.

Amount có thể hiển thị theo permission.

Không hiển thị bank/card details.

---

## 24. Inventory action view

Theo dõi:

- Stock adjustment.
- Inventory count submission.
- Transfer approval.
- Sync retry.
- Manual override.

Diff có thể gồm quantity before/after, reason code và warehouse scope.

---

## 25. Report export audit view

Theo dõi:

- Template.
- Format.
- Scope.
- Requested by.
- Completed/downloaded time.
- Sensitive columns included.
- File expiry.

Không lưu signed URL trong audit log.

---

## 26. Flags và annotations

Người có quyền có thể:

- Flag event để điều tra.
- Gán owner.
- Thêm annotation.
- Đặt trạng thái review.

```ts
export type AuditReviewState =
  | "unreviewed"
  | "reviewing"
  | "resolved"
  | "false_positive";
```

Annotation không được chỉnh sửa event gốc.

---

## 27. Saved views

Cho phép lưu:

- Filter.
- Columns.
- Sort.
- Time range preset.

Ví dụ:

```text
Critical security events
Finance actions over threshold
Permission changes this week
Inventory adjustments by branch
```

Saved view không được mở rộng permission hoặc scope.

---

## 28. Export audit data

Định dạng:

```text
CSV
XLSX
PDF summary
```

Yêu cầu:

- Permission riêng.
- Scope giữ nguyên.
- CSV injection protection.
- Signed URL có expiry.
- Audit event cho chính thao tác export audit.
- Không cho export raw sensitive metadata nếu thiếu quyền.

---

## 29. Pagination

Ưu tiên cursor pagination:

```text
?cursor=...&limit=50
```

- Mặc định 50 events.
- Cho 25/50/100 nếu cần.
- Không tải hàng nghìn event một lần.
- Full export dùng background job.

---

## 30. Retention policy

Retention theo loại event:

- Authentication: theo security policy.
- Permission changes: dài hạn hơn.
- Finance actions: theo yêu cầu kiểm soát và pháp lý.
- Report downloads: theo policy dữ liệu.
- Low-value activity: ngắn hơn.

Retention phải được phê duyệt bởi Security/Legal/Operations.

Không hardcode retention khác nhau trong frontend.

---

## 31. Immutability

Audit events phải:

- Append-only.
- Không sửa nội dung gốc qua UI.
- Không xóa thủ công tùy tiện.
- Có integrity controls phù hợp.
- Tách annotation khỏi event gốc.

Nếu cần xóa theo pháp lý, phải dùng quy trình đặc biệt có audit riêng.

---

## 32. Clock và timezone

- Backend lưu UTC.
- UI hiển thị timezone người dùng hoặc branch.
- Timestamp đầy đủ trong detail.
- Relative time chỉ là bổ sung.

Ví dụ:

```text
06/08/2026 18:20:31 GMT+7
```

Không chỉ hiển thị `5 phút trước` trong audit detail.

---

## 33. Actor identity

Actor có thể là:

- User.
- System.
- Service account.
- API client.
- Scheduled job.

UI phải phân biệt rõ bằng icon và label.

Nếu user đã bị xóa, vẫn giữ internal ID và snapshot display name phù hợp policy.

---

## 34. Source metadata

Có thể hiển thị theo quyền:

- IP hash hoặc masked IP.
- User agent.
- Session ID rút gọn.
- API client.
- Request ID.
- Trace ID.

Không hiển thị raw token, cookie hoặc authorization header.

---

## 35. Anomaly signals

Có thể đánh dấu:

- Nhiều failed logins.
- Nhiều denied actions.
- Export volume bất thường.
- Permission change ngoài giờ.
- Inventory adjustment lớn.
- Refund spike.
- Cross-branch attempts.

Anomaly là tín hiệu điều tra, không phải kết luận vi phạm.

Phải có confidence và rule/source rõ ràng.

---

## 36. Audit ingestion health

Theo dõi:

- Last event received.
- Ingestion delay.
- Dropped event count.
- Schema validation failures.
- Storage write failures.

Nếu audit pipeline lỗi:

- Hiển thị warning critical cho admin phù hợp.
- Không im lặng coi như không có sự kiện.

---

## 37. API endpoints đề xuất

```text
GET    /api/v1/audit/events
GET    /api/v1/audit/events/:id
GET    /api/v1/audit/events/:id/related
POST   /api/v1/audit/events/:id/flag
POST   /api/v1/audit/events/:id/annotations
PATCH  /api/v1/audit/events/:id/review-state
GET    /api/v1/audit/saved-views
POST   /api/v1/audit/saved-views
PATCH  /api/v1/audit/saved-views/:id
DELETE /api/v1/audit/saved-views/:id
POST   /api/v1/audit/exports
GET    /api/v1/audit/health
```

Mutation chỉ áp dụng annotation/review state, không sửa event gốc.

---

## 38. Frontend component inventory

```text
AuditActivityCenterPage
├── AuditSummaryCards
├── AuditFilterBar
├── AuditEventTable
├── AuditEventRow
├── AuditSeverityBadge
├── AuditResultBadge
├── AuditEventDrawer
├── AuditChangeDiff
├── RelatedEventTimeline
├── SecurityEventPanel
├── AuditSavedViews
├── AuditAnnotationPanel
└── AuditExportDialog
```

---

## 39. Loading states

- Summary skeleton.
- Filter options loading.
- Event rows skeleton.
- Detail drawer skeleton.
- Related timeline skeleton.

Không hiển thị `0 sự kiện` trước khi request hoàn tất.

---

## 40. Empty states

### Không có sự kiện

```text
Không có sự kiện phù hợp trong khoảng thời gian đã chọn.
```

### Không có security event

```text
Không phát hiện sự kiện bảo mật đáng chú ý trong phạm vi hiện tại.
```

### Chưa có annotation

```text
Chưa có ghi chú điều tra cho sự kiện này.
```

Không dùng empty state như bằng chứng rằng hệ thống chắc chắn không có hoạt động bất thường nếu audit pipeline đang lỗi.

---

## 41. Error states

- List lỗi không xóa filter hiện tại.
- Detail lỗi hiển thị request ID và retry.
- Related events lỗi không làm mất event chính.
- Export lỗi không đổi trạng thái thành completed.
- Ingestion health lỗi phải hiển thị riêng.

Không hiển thị raw stack trace.

---

## 42. Responsive behavior

### Desktop

- Table đầy đủ.
- Detail drawer 520–640px.

### Tablet

- Giảm cột.
- Chuyển metadata phụ vào drawer.

### Mobile

- Card list.
- Filter full-screen sheet.
- Detail full-screen.
- Không hiển thị diff bảng quá rộng; chuyển dạng stacked.

Audit Center ưu tiên desktop nhưng vẫn phải đọc được trên mobile.

---

## 43. Accessibility

- Table có caption và headers.
- Severity/result không chỉ dùng màu.
- Drawer quản lý focus.
- Diff có label `Trước` và `Sau` rõ.
- Timeline dùng danh sách semantic.
- Filter errors liên kết field.
- Keyboard có thể mở, filter và xem detail.
- Touch target tối thiểu 44px.

---

## 44. Security và privacy

- Backend enforce permission và scope.
- Sensitive metadata chỉ trả cho role được phép.
- Không log hoặc hiển thị password, token, OTP, cookie.
- PII và finance values được mask theo policy.
- Export audit data dùng signed URL.
- Annotation được sanitize.
- Raw metadata không chứa arbitrary HTML.
- Cache key gồm user, permission hash, scope và filters.

---

## 45. Analytics events

```text
audit.center.viewed
audit.filter.applied
audit.event.opened
audit.event.flagged
audit.annotation.created
audit.saved_view.created
audit.export.requested
```

Không gửi event summary, actor name, entity ID, IP hoặc diff values vào analytics.

---

## 46. Observability

Theo dõi:

- Audit query latency.
- Event ingestion delay.
- Schema validation failures.
- Export failure.
- Detail load failure.
- Permission denied anomalies.
- Dropped event count.
- Annotation mutation failure.

---

## 47. Testing

### Unit

- Severity mapping.
- Result mapping.
- Diff masking.
- Filter serialization.
- Timezone formatting.
- Saved view permission merge.

### Component

- Audit table.
- Filter bar.
- Event drawer.
- Diff table.
- Related timeline.
- Annotation panel.
- Empty/error/loading states.

### Integration

- Branch manager chỉ thấy branch scope.
- Finance auditor thấy finance events theo quyền.
- Sensitive metadata bị mask.
- Related events theo request ID.
- Export đúng scope.
- Annotation không thay đổi event gốc.

### E2E

- Mở Audit Center.
- Filter permission changes.
- Mở event detail.
- Xem before/after diff.
- Flag event.
- Thêm annotation.
- Tạo saved view.
- Export lịch sử.
- Viewer thiếu quyền không thấy raw metadata.

---

## 48. Visual QA

Baseline:

```text
Audit Center — 1440 × 900
Critical Security Event
Permission Change Event
Finance Refund Event
Inventory Adjustment Event
Event Detail Drawer
Masked Sensitive Fields
No Results
Ingestion Warning
Mobile Card List
```

Kiểm tra:

- Severity visibility.
- Table density.
- Long action names.
- Diff readability.
- Scope labels.
- Focus states.
- Masking consistency.

---

## 49. Performance budgets

Mục tiêu tham khảo:

```text
Audit Center LCP p75 < 2,5s
Audit list API p95 < 1,5s
Event detail p95 < 1s
Filter UI response < 200ms
Export authorization < 1s
```

Dùng cursor pagination và không tải diff lớn trước khi mở detail.

---

## 50. AI coding agent requirements

AI coding agent phải:

- Không cho sửa hoặc xóa event gốc qua UI.
- Không hiển thị secret hoặc sensitive metadata ngoài quyền.
- Không trộn audit log với technical log thiếu kiểm soát.
- Không tạo anomaly conclusion thiếu rule và confidence.
- Không export vượt permission/scope.
- Không gửi actor/entity/diff values vào analytics.
- Bổ sung test cho masking, immutability, scope và related events.
- Báo rõ retention, storage và backend assumptions.

---

## 51. Acceptance checklist

- [ ] Phân biệt Audit, Activity và Technical Logs.
- [ ] Có category, severity và result model.
- [ ] Có event contract và change diff.
- [ ] Có Audit Event Table và filter nâng cao.
- [ ] Có Event Detail Drawer.
- [ ] Có before/after diff và related timeline.
- [ ] Có Security, Permission, Finance, Inventory và Export views.
- [ ] Có flag, annotation và review state.
- [ ] Có saved views.
- [ ] Có export an toàn.
- [ ] Có retention, immutability và timezone rules.
- [ ] Có ingestion health và anomaly signals.
- [ ] Permission và scope được backend enforce.
- [ ] Loading, empty, error và responsive states đầy đủ.
- [ ] Accessibility, security, analytics và observability hoàn chỉnh.
- [ ] Unit, component, integration, E2E và Visual QA đầy đủ.

---

## 52. Những điều không được làm

- Không cho chỉnh sửa event gốc.
- Không xóa audit event tùy tiện.
- Không lưu password, token, OTP hoặc cookie.
- Không hiển thị raw PII hoặc finance data thiếu quyền.
- Không trả dữ liệu toàn hệ thống rồi lọc client-side.
- Không dùng activity feed thay audit log.
- Không dùng anomaly signal như kết luận vi phạm.
- Không export raw metadata vượt quyền.
- Không coi không có event là an toàn khi audit pipeline lỗi.
- Không ghi audit content vào analytics.

---

## 53. Kết luận

Audit & Activity Center của Cynca VLXD phải cung cấp khả năng truy vết rõ ràng, an toàn và có thể kiểm chứng cho các thao tác người dùng và hệ thống. Event gốc phải bất biến, dữ liệu nhạy cảm phải được kiểm soát, mọi truy vấn phải tuân permission và scope, đồng thời hệ thống phải hỗ trợ điều tra incident bằng request ID, trace ID, diff và related-event timeline.

File tiếp theo đề xuất:

```text
README.md
40-User-Role-Management.md
```