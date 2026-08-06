# Cynca VLXD Admin Dashboard — Integration Monitoring Center Specification

> Phiên bản: 1.0  
> Phạm vi: Giám sát tích hợp, đồng bộ dữ liệu, webhook, API đối tác và hàng đợi xử lý  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, DevOps, Operations, Security, QA và AI coding agent  
> Tài liệu liên quan: `24-Observability.md`, `25-Runbooks.md`, `30-Notification-Center.md`, `39-Audit-Activity-Center.md`, `41-System-Settings.md`

---

## 1. Mục tiêu

Integration Monitoring Center giúp Cynca VLXD theo dõi trạng thái kết nối giữa Dashboard với Zalo OA, Zalo Mini App, cổng thanh toán, đơn vị vận chuyển, hệ thống kho, kế toán, email và các dịch vụ bên ngoài.

Hệ thống phải trả lời nhanh:

- Tích hợp nào đang hoạt động, suy giảm hoặc mất kết nối?
- Lần đồng bộ thành công gần nhất là khi nào?
- Có bao nhiêu event đang chờ, lỗi hoặc bị đưa vào dead-letter queue?
- Dữ liệu nào bị ảnh hưởng và phạm vi ảnh hưởng ra sao?
- Có thể retry an toàn hay cần điều tra thủ công?
- Secret, chứng thư hoặc token nào sắp hết hạn?
- SLA và error budget của từng tích hợp có đang bị vi phạm?

Integration Monitoring Center không thay thế log kỹ thuật chuyên sâu, APM hoặc SIEM. Đây là lớp quản trị vận hành có ngữ cảnh nghiệp vụ.

---

## 2. Nhóm tích hợp

```ts
export type IntegrationCategory =
  | "zalo"
  | "payment"
  | "shipping"
  | "inventory"
  | "accounting"
  | "email"
  | "storage"
  | "analytics"
  | "identity"
  | "custom";
```

Ví dụ:

- Zalo OA Messaging.
- Zalo Mini App backend.
- Payment gateway.
- Shipping carrier API.
- WMS hoặc inventory sync.
- Accounting connector.
- Email provider.
- Object storage.
- SSO hoặc identity provider.

---

## 3. Permission model

```text
integrations.monitor.view
integrations.monitor.view_sensitive
integrations.logs.view
integrations.events.view
integrations.events.retry
integrations.events.cancel
integrations.dlq.view
integrations.dlq.replay
integrations.connection.test
integrations.configuration.view
integrations.configuration.update
integrations.credentials.rotate
integrations.incidents.manage
integrations.audit.view
```

Không dựa duy nhất vào role name.

Các quyền retry, replay, rotate credential và update configuration phải được xem là nhạy cảm.

---

## 4. Information architecture

```text
Integration Monitoring Center
├── Tổng quan
├── Danh sách tích hợp
├── Event Stream
├── Sync Jobs
├── Webhook Deliveries
├── Error & Retry Queue
├── Dead-letter Queue
├── Credentials & Expiry
├── Incidents
├── Maintenance Windows
└── Audit History
```

---

## 5. Integration status model

```ts
export type IntegrationStatus =
  | "healthy"
  | "degraded"
  | "down"
  | "paused"
  | "maintenance"
  | "misconfigured"
  | "unknown";
```

| Trạng thái | Ý nghĩa |
|---|---|
| Healthy | Hoạt động bình thường |
| Degraded | Có lỗi hoặc độ trễ tăng nhưng chưa ngừng hoàn toàn |
| Down | Không thể sử dụng |
| Paused | Chủ động tạm dừng |
| Maintenance | Trong cửa sổ bảo trì |
| Misconfigured | Sai cấu hình hoặc credential |
| Unknown | Chưa đủ dữ liệu để xác định |

Không hiển thị `Healthy` khi health-check service lỗi.

---

## 6. Integration contract

```ts
export type IntegrationSummary = {
  id: string;
  key: string;
  name: string;
  category: IntegrationCategory;
  environment: "development" | "staging" | "production";
  status: IntegrationStatus;
  ownerTeam: string;
  lastSuccessAt?: string;
  lastFailureAt?: string;
  lastHealthCheckAt?: string;
  successRate24h?: number;
  p95LatencyMs?: number;
  pendingEvents?: number;
  failedEvents?: number;
  dlqEvents?: number;
  credentialExpiresAt?: string;
  dataFreshnessSeconds?: number;
  affectedModules?: string[];
};
```

---

## 7. Trang tổng quan

KPI đề xuất:

1. Tích hợp Healthy.
2. Tích hợp Degraded/Down.
3. Failed events 24 giờ.
4. Pending events.
5. DLQ events.
6. Credential sắp hết hạn.

Widgets:

- Critical integrations.
- Error trend.
- Queue backlog.
- Data freshness.
- Recent incidents.
- Upcoming maintenance.

Không dùng chart trang trí nếu không giúp xử lý sự cố.

---

## 8. Integration card

Mỗi card gồm:

- Tên và category.
- Environment.
- Status.
- Owner.
- Last success.
- Success rate.
- p95 latency.
- Pending/failed count.
- Credential expiry warning.
- Primary action.

Primary action theo trạng thái:

- Healthy: `Xem chi tiết`.
- Degraded: `Xem lỗi`.
- Down: `Mở incident`.
- Misconfigured: `Kiểm tra cấu hình`.

---

## 9. Integration detail page

Tabs:

```text
Overview
Events
Sync Jobs
Errors
Webhooks
Credentials
Configuration
Incidents
Audit
```

Header:

- Status.
- Environment.
- Owner.
- Last health check.
- Data freshness.
- Test connection.
- Pause/resume nếu có quyền.

---

## 10. Health indicators

Theo dõi:

- Availability.
- Success rate.
- Latency.
- Error rate.
- Throughput.
- Queue age.
- Data freshness.
- Credential validity.

Mỗi indicator phải có threshold và owner rõ.

Không dùng một health score tổng hợp duy nhất mà thiếu khả năng giải thích.

---

## 11. SLO và error budget

```ts
export type IntegrationSLO = {
  availabilityTarget?: number;
  successRateTarget?: number;
  p95LatencyTargetMs?: number;
  freshnessTargetSeconds?: number;
  evaluationWindow: "1h" | "24h" | "7d" | "30d";
  errorBudgetRemainingPercent?: number;
};
```

UI phải hiển thị:

- Target.
- Actual.
- Window.
- Error budget còn lại.
- Breach state.

Không dùng SLO thiếu nguồn dữ liệu ổn định.

---

## 12. Event stream

Event row gồm:

- Event ID.
- Integration.
- Event type.
- Entity reference đã mask.
- Direction inbound/outbound.
- Status.
- Attempt count.
- Created time.
- Last attempt.
- Latency.
- Actions.

Không hiển thị payload đầy đủ mặc định.

---

## 13. Event status model

```ts
export type IntegrationEventStatus =
  | "received"
  | "validated"
  | "queued"
  | "processing"
  | "succeeded"
  | "failed"
  | "retry_scheduled"
  | "dead_lettered"
  | "cancelled";
```

Phân biệt lỗi validation với lỗi provider hoặc lỗi timeout.

---

## 14. Event detail drawer

Nội dung:

- Event summary.
- Integration/source.
- Event type.
- Correlation ID.
- Request ID/trace ID.
- Attempt timeline.
- Error code.
- Redacted request/response metadata.
- Related entity.
- Retry eligibility.
- Available actions.

Không hiển thị token, signature, cookie hoặc full payment data.

---

## 15. Payload viewing

Nếu role được phép:

- Payload phải redact secret và PII.
- Có giới hạn kích thước.
- Không render HTML.
- Có tab structured JSON read-only.
- Có copy action chỉ cho dữ liệu đã redact.
- Có audit khi xem payload nhạy cảm nếu policy yêu cầu.

Không cho sửa payload trực tiếp rồi replay thiếu workflow.

---

## 16. Sync jobs

```ts
export type IntegrationSyncJob = {
  id: string;
  integrationId: string;
  type: "full" | "incremental" | "reconciliation" | "backfill";
  status: "queued" | "running" | "completed" | "partially_completed" | "failed" | "cancelled";
  startedAt?: string;
  completedAt?: string;
  processedRecords?: number;
  successRecords?: number;
  failedRecords?: number;
  checkpoint?: string;
  initiatedBy: "schedule" | "user" | "system";
  availableActions: string[];
};
```

---

## 17. Sync job progress

Hiển thị:

- Current phase.
- Processed/total nếu biết.
- Rows per second.
- Oldest pending age.
- Estimated completion nếu đáng tin cậy.
- Failure count.

Không giả percent khi backend không có progress thật.

---

## 18. Retry policy

Retry phù hợp khi:

- Timeout.
- Rate limit.
- Provider temporary unavailable.
- Network failure.

Không retry tự động vô hạn khi:

- Invalid credential.
- Schema mismatch.
- Permission denied.
- Invalid payload.
- Business conflict.

Policy cần có:

- Max attempts.
- Backoff.
- Jitter.
- Retryable error codes.
- DLQ threshold.

---

## 19. Manual retry

Yêu cầu:

- Chỉ hiển thị khi event retryable.
- Backend kiểm tra lại.
- Dùng idempotency key.
- Có confirmation với event nhạy cảm.
- Hiển thị attempt mới.
- Audit action.

Không thay event cũ thành succeeded; tạo attempt hoặc replay record mới.

---

## 20. Dead-letter Queue

DLQ row gồm:

- Event ID.
- Integration.
- Event type.
- First failure.
- Last failure.
- Attempts.
- Error category.
- Age.
- Owner.
- Review state.

Actions:

- Open detail.
- Assign owner.
- Mark investigated.
- Replay.
- Discard theo policy.

---

## 21. DLQ replay workflow

```text
Chọn event
→ Xem payload đã redact
→ Xác định nguyên nhân
→ Chọn replay strategy
→ Dry validation
→ Confirm
→ Tạo replay event
→ Theo dõi kết quả
```

Không replay hàng loạt thiếu rate limit và impact preview.

---

## 22. Webhook deliveries

Theo dõi:

- Delivery ID.
- Destination đã mask.
- Event type.
- HTTP status.
- Duration.
- Attempt.
- Signature status.
- Next retry.
- Result.

Không hiển thị signing secret.

---

## 23. Webhook security indicators

- Signature generated/verified.
- Destination allowed.
- TLS valid.
- Redirect count.
- SSRF protection result.
- Response size limit.

Không cho test webhook tới private/internal destination nếu policy chặn.

---

## 24. Rate limiting

Hiển thị:

- Current request rate.
- Provider limit.
- Remaining quota.
- Reset time.
- Throttled count.

Khi gần giới hạn:

- Giảm concurrency.
- Queue event.
- Cảnh báo degraded.
- Không retry dồn dập.

---

## 25. Circuit breaker

Trạng thái:

```text
Closed
Open
Half-open
Disabled
```

UI hiển thị:

- Reason opened.
- Opened at.
- Failure threshold.
- Next probe.
- Affected features.

Manual reset phải có permission và confirmation.

---

## 26. Credential expiry

Theo dõi:

- Token expiry.
- Certificate expiry.
- API key rotation date.
- OAuth refresh failure.
- Webhook signing secret rotation.

Cảnh báo tham khảo:

```text
30 ngày
14 ngày
7 ngày
24 giờ
Đã hết hạn
```

Không hiển thị credential value.

---

## 27. Credential rotation workflow

```text
Bắt đầu rotate
→ Nhập credential mới
→ Test connection
→ Chọn thời điểm kích hoạt
→ Publish
→ Theo dõi lỗi
→ Thu hồi credential cũ
```

Có thể hỗ trợ overlap window để tránh downtime.

Mọi bước phải audit nhưng không ghi secret.

---

## 28. Data freshness

Widget hiển thị độ trễ dữ liệu theo module:

- Orders.
- Inventory.
- Payments.
- Shipping statuses.
- Accounting.
- Zalo messages.

Freshness states:

```text
Fresh
Near threshold
Stale
Unknown
```

Không coi `không có event mới` là stale nếu nguồn thực sự không có thay đổi; cần heartbeat hoặc checkpoint phù hợp.

---

## 29. Data reconciliation

Có thể hiển thị:

- Source count.
- Destination count.
- Missing records.
- Duplicate records.
- Mismatched status.
- Last reconciliation.

Reconciliation không được tự sửa dữ liệu nếu chưa có workflow phê duyệt.

---

## 30. Incident management

Integration incident gồm:

- Incident ID.
- Severity.
- Integration.
- Impacted modules.
- Started at.
- Current status.
- Incident commander.
- Timeline.
- Workaround.
- Resolution.

Statuses:

```text
Investigating
Identified
Mitigating
Monitoring
Resolved
Postmortem Pending
Closed
```

---

## 31. Maintenance windows

Cho phép khai báo:

- Integration.
- Start/end.
- Timezone.
- Expected impact.
- Owner.
- Notification audience.
- Suppression rules.

Không dùng maintenance để che lỗi ngoài phạm vi/thời gian đã khai báo.

---

## 32. Alert routing

Alert theo:

- Severity.
- Integration owner.
- Business owner.
- Branch bị ảnh hưởng.
- On-call schedule nếu có.

Channels:

- In-app.
- Email.
- Zalo OA nội bộ nếu được phê duyệt.
- Incident tool nếu có.

Không gửi payload nhạy cảm trong alert.

---

## 33. Search và filters

Filters:

- Integration.
- Category.
- Environment.
- Status.
- Error category.
- Date range.
- Event type.
- Owner.
- Branch/module affected.

Search:

- Event ID.
- Request ID.
- Trace ID.
- Correlation ID.
- Error code.

Không tìm raw payload toàn văn mặc định.

---

## 34. API endpoints đề xuất

```text
GET    /api/v1/integration-monitoring/summary
GET    /api/v1/integration-monitoring/integrations
GET    /api/v1/integration-monitoring/integrations/:id
GET    /api/v1/integration-monitoring/events
GET    /api/v1/integration-monitoring/events/:id
POST   /api/v1/integration-monitoring/events/:id/retry
GET    /api/v1/integration-monitoring/dlq
POST   /api/v1/integration-monitoring/dlq/:id/replay
GET    /api/v1/integration-monitoring/sync-jobs
POST   /api/v1/integration-monitoring/sync-jobs/:id/cancel
GET    /api/v1/integration-monitoring/webhooks
POST   /api/v1/integration-monitoring/integrations/:id/test
POST   /api/v1/integration-monitoring/integrations/:id/rotate-credential
GET    /api/v1/integration-monitoring/incidents
POST   /api/v1/integration-monitoring/incidents
```

---

## 35. Frontend component inventory

```text
IntegrationMonitoringCenterPage
├── IntegrationSummaryCards
├── IntegrationHealthGrid
├── IntegrationCard
├── IntegrationDetailPage
├── HealthIndicatorPanel
├── EventStreamTable
├── EventDetailDrawer
├── SyncJobTable
├── QueueBacklogChart
├── WebhookDeliveryTable
├── DLQTable
├── RetryDialog
├── ReplayReviewDialog
├── CredentialExpiryPanel
├── IncidentTimeline
└── MaintenanceWindowList
```

---

## 36. Loading, empty và error states

### Loading

- Health card skeleton.
- Event rows skeleton.
- Queue chart placeholder.
- Incident timeline skeleton.

### Empty

```text
Không có event lỗi trong khoảng thời gian đã chọn.
Không có event trong dead-letter queue.
Không có incident đang mở.
```

### Error

- Health service lỗi không hiển thị Healthy.
- Event detail lỗi giữ list hiện tại.
- Retry lỗi không đổi event thành succeeded.
- Có request ID.
- Không hiển thị stack trace hoặc raw provider response.

---

## 37. Responsive behavior

### Desktop

- Health grid 3–4 cột.
- Event table đầy đủ.
- Detail drawer 560–680px.

### Tablet

- Health grid 2 cột.
- Giảm cột event table.
- Detail full-height drawer.

### Mobile

- Health card list.
- Chỉ xem status, incident và event summary.
- Retry/replay nhạy cảm có thể yêu cầu desktop.
- Detail full-screen.

---

## 38. Accessibility

- Status không chỉ dùng màu.
- Chart có text summary.
- Table có caption và headers.
- Timeline dùng semantic list.
- Retry/replay dialog quản lý focus.
- Error và warning có text rõ.
- Keyboard có thể filter và mở detail.
- Touch target tối thiểu 44px.

---

## 39. Security và privacy

- Backend enforce permission và environment scope.
- Secret, token, signature và cookie không trả về client.
- Payload được redact.
- Webhook URL và response metadata được mask phù hợp.
- Replay/retry dùng idempotency.
- Chống SSRF cho test connection/webhook.
- Không gửi payload vào analytics hoặc error monitoring.
- Audit mọi credential rotation, replay và manual reset.

---

## 40. Audit events

```text
integrations.connection.tested
integrations.event.retried
integrations.dlq.replayed
integrations.sync.cancelled
integrations.credential.rotation_started
integrations.credential.rotated
integrations.circuit_breaker.reset
integrations.incident.created
integrations.incident.resolved
integrations.maintenance.created
```

---

## 41. Analytics và observability

Analytics an toàn:

```text
integrations.center.viewed
integrations.detail.opened
integrations.event.opened
integrations.retry.requested
integrations.dlq.replay_requested
integrations.incident.opened
```

Không gửi event ID, payload, endpoint, credential metadata hoặc entity ID vào analytics.

Observability theo dõi:

- Health-check latency.
- Event processing latency.
- Queue depth.
- Retry rate.
- DLQ growth.
- Reconciliation mismatch.
- Credential expiry.
- Alert delivery failure.

---

## 42. Testing

### Unit

- Status mapping.
- Retry eligibility.
- Freshness calculation.
- Error classification.
- Credential expiry thresholds.
- SLO evaluation.

### Component

- Integration card.
- Event table.
- Event detail drawer.
- Sync progress.
- DLQ replay dialog.
- Incident timeline.
- Empty/error states.

### Integration

- Retry tạo attempt mới.
- DLQ replay dùng idempotency.
- Payload masking.
- Credential rotation không trả secret cũ.
- Maintenance suppression đúng scope.
- Permission-limited views.

### E2E

- Mở tích hợp degraded.
- Xem event lỗi.
- Retry event hợp lệ.
- Replay DLQ sau dry validation.
- Test connection.
- Rotate credential.
- Mở incident và cập nhật timeline.
- Viewer không thấy payload nhạy cảm.

---

## 43. Visual QA

```text
Integration Center — 1440 × 900
All Healthy
Degraded Integration
Down Integration
Event Stream
Event Detail Redacted
DLQ with Errors
Retry Dialog
Credential Expiry Warning
Incident Timeline
Maintenance Window
Mobile Health List
```

---

## 44. AI coding agent requirements

AI coding agent phải:

- Không hiển thị secret hoặc raw payload thiếu redact.
- Không retry mọi lỗi mặc định.
- Không replay DLQ thiếu validation và idempotency.
- Không coi health-check lỗi là Healthy.
- Không dùng maintenance để che incident sai phạm vi.
- Không reset circuit breaker thiếu permission và confirmation.
- Bổ sung test cho masking, retry policy, DLQ replay, SSRF và credential expiry.
- Báo rõ event model, queue system, provider limits và backend assumptions.

---

## 45. Acceptance checklist

- [ ] Có Integration Health Overview.
- [ ] Có status model và health indicators.
- [ ] Có SLO và error budget.
- [ ] Có Event Stream và Event Detail.
- [ ] Có Sync Jobs và progress.
- [ ] Có retry policy và manual retry an toàn.
- [ ] Có Dead-letter Queue và replay workflow.
- [ ] Có Webhook Deliveries.
- [ ] Có rate limit và circuit breaker status.
- [ ] Có credential expiry và rotation workflow.
- [ ] Có data freshness và reconciliation.
- [ ] Có incident và maintenance management.
- [ ] Permission, masking, audit và idempotency đầy đủ.
- [ ] Loading, empty, error và responsive states hoàn chỉnh.
- [ ] Accessibility, analytics, observability và testing đầy đủ.

---

## 46. Những điều không được làm

- Không hiển thị token, secret hoặc signature.
- Không coi unknown là healthy.
- Không retry lỗi validation hoặc permission denied vô hạn.
- Không replay event thiếu idempotency.
- Không sửa payload trực tiếp để replay thiếu workflow.
- Không cho test webhook tới destination nguy hiểm.
- Không gửi payload hoặc endpoint nhạy cảm vào analytics.
- Không dùng một health score không giải thích được.
- Không xóa DLQ event thiếu audit và policy.
- Không thay observability platform bằng màn hình tổng hợp này.

---

## 47. Kết luận

Integration Monitoring Center của Cynca VLXD phải cung cấp góc nhìn rõ ràng về sức khỏe kết nối, luồng event, hàng đợi, độ mới dữ liệu và các sự cố ảnh hưởng nghiệp vụ. Mọi thao tác retry, replay, rotate credential và reset phải được kiểm soát bằng permission, validation, idempotency, masking và audit đầy đủ.

File tiếp theo:

```text
47-System-Health-Incident-Center.md
```