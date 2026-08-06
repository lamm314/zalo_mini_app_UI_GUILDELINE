# Cynca VLXD Admin Dashboard — Observability

> Phiên bản: 1.0  
> Phạm vi: Logging, metrics, tracing, alerting và vận hành quan sát cho Dashboard quản trị  
> Stack mục tiêu: Next.js, React, TypeScript, REST/BFF  
> Đối tượng sử dụng: Frontend, Backend, DevOps, QA, Product Owner, Security, AI coding agent  
> Tài liệu liên quan: `20-Frontend-Architecture.md`, `21-Test-Strategy.md`, `22-Release-Operations.md`, `23-Security-Privacy.md`

---

## 1. Mục tiêu

Observability giúp đội vận hành hiểu hệ thống đang hoạt động như thế nào, người dùng đang gặp lỗi ở đâu và thay đổi nào gây regression.

Hệ thống quan sát phải trả lời được:

- Dashboard có tải được không?
- Widget nào đang lỗi?
- API nào chậm hoặc thất bại?
- Release nào làm tăng lỗi?
- Người dùng vai trò nào bị ảnh hưởng?
- Chi nhánh nào đang gặp vấn đề dữ liệu?
- Search, export và notification có hoạt động ổn định không?
- Lỗi là frontend, backend, mạng hay dữ liệu?
- Có dấu hiệu vi phạm quyền hoặc truy cập bất thường không?

Observability không chỉ là ghi log. Cần kết hợp logs, metrics, traces, events và alerting.

---

## 2. Ba trụ cột chính

### Logs

Ghi lại sự kiện có ngữ cảnh chi tiết.

### Metrics

Đo xu hướng và tỷ lệ theo thời gian.

### Traces

Theo dõi một request hoặc workflow xuyên qua frontend, BFF và backend.

Ngoài ra cần:

- Release metadata.
- User journey events.
- Audit events.
- Synthetic monitoring.
- Real User Monitoring.

---

## 3. Nguyên tắc cốt lõi

### 3.1 Có ngữ cảnh nhưng không lộ dữ liệu

Mỗi sự kiện phải đủ thông tin để điều tra nhưng không chứa secret hoặc PII dư thừa.

### 3.2 Correlation xuyên hệ thống

Frontend, BFF và backend phải dùng chung request hoặc trace identifier khi có thể.

### 3.3 Theo dõi theo release

Mọi error và performance event phải gắn:

- Release version.
- Commit SHA.
- Environment.

### 3.4 Alert theo tác động người dùng

Không alert chỉ vì một log xuất hiện. Ưu tiên tỷ lệ lỗi, số người dùng bị ảnh hưởng và workflow quan trọng.

### 3.5 Không tạo noise

Alert quá nhiều làm đội vận hành bỏ qua cảnh báo quan trọng.

---

## 4. Observability architecture

```text
Browser
├── RUM SDK
├── Error Reporter
├── Web Vitals
├── User Journey Events
└── Trace Headers
        ↓
Next.js / BFF
├── Structured Logs
├── Metrics
├── Distributed Tracing
└── Request Correlation
        ↓
Backend Services
├── API Logs
├── Database Metrics
├── Job Metrics
└── Audit Events
        ↓
Observability Platform
├── Dashboards
├── Alerts
├── Search
├── Traces
└── Incident Integration
```

---

## 5. Environment naming

Dùng tên nhất quán:

```text
local
development
staging
production
```

Mọi event phải có:

```ts
export type EnvironmentName =
  | "local"
  | "development"
  | "staging"
  | "production";
```

Không trộn dữ liệu staging và production trong cùng dashboard nếu không có filter rõ ràng.

---

## 6. Release metadata

Mỗi build cần nhúng metadata:

```ts
export type ReleaseMetadata = {
  version: string;
  commitSha: string;
  buildId: string;
  builtAt: string;
  environment: EnvironmentName;
};
```

Có thể cung cấp qua environment variables:

```text
NEXT_PUBLIC_APP_VERSION
NEXT_PUBLIC_COMMIT_SHA
NEXT_PUBLIC_BUILD_ID
```

Không đưa secret vào public metadata.

---

## 7. Structured logging

Logs phải dùng JSON hoặc cấu trúc tương đương.

Ví dụ:

```json
{
  "level": "error",
  "event": "dashboard.widget.load_failed",
  "widget": "revenue_chart",
  "route": "/dashboard",
  "requestId": "req_123",
  "traceId": "trace_456",
  "release": "1.4.0",
  "environment": "production",
  "errorCode": "UPSTREAM_UNAVAILABLE",
  "retryable": true,
  "timestamp": "2026-08-06T17:30:00+07:00"
}
```

Không ghi log dạng câu tự do nếu cần query máy móc.

---

## 8. Log levels

### Debug

- Chỉ development hoặc sampling thấp.
- Không bật rộng ở production.

### Info

- Workflow hoàn tất.
- Background job started/completed.
- Release startup.

### Warn

- Retry thành công sau lỗi.
- Dữ liệu stale.
- Permission anomaly chưa gây sự cố.

### Error

- Widget load failure.
- Mutation failure.
- Contract parse failure.

### Fatal

- Dashboard shell không render.
- Authentication service không khả dụng diện rộng.
- Lỗi khiến ứng dụng không dùng được.

Không dùng `error` cho hành vi bình thường như empty state.

---

## 9. Frontend error events

Bắt buộc ghi nhận:

- Unhandled exception.
- Unhandled promise rejection.
- React render error.
- Widget error boundary.
- API response parse error.
- Dynamic import failure.
- Chunk load error.
- Export workflow failure.
- Real-time connection failure.

Event name đề xuất:

```text
frontend.unhandled_error
frontend.render_error
dashboard.widget.load_failed
api.contract_validation_failed
realtime.connection_failed
export.job_failed
```

---

## 10. Error fingerprinting

Nhóm lỗi theo:

- Error type.
- Stack trace.
- Component hoặc route.
- API endpoint.
- Error code.

Không fingerprint theo message chứa dữ liệu động như ID đơn hàng vì sẽ tạo quá nhiều issue riêng.

---

## 11. Error context

Context hữu ích:

- Route.
- Widget.
- Release.
- Browser.
- OS.
- Viewport.
- User role.
- Branch ID dạng nội bộ nếu được phép.
- Request ID.
- Trace ID.
- Last action.

Không gửi:

- Password.
- Token.
- Full phone.
- Full address.
- Nội dung tài chính chi tiết gắn với khách hàng.

---

## 12. Request ID

Mỗi request nên có:

```text
X-Request-ID
```

Nếu client không gửi, edge hoặc backend tạo.

Frontend cần đọc request ID từ response lỗi và hiển thị trong thông tin hỗ trợ khi phù hợp:

```text
Mã hỗ trợ: req_01J...
```

Không hiển thị trace nội bộ dài hoặc stack trace cho người dùng.

---

## 13. Distributed tracing

Khuyến nghị dùng chuẩn W3C Trace Context:

```text
traceparent
tracestate
```

Trace có thể bao gồm:

```text
Browser interaction
→ Next.js route
→ BFF
→ Orders API
→ Database
```

Trace cần sampling để kiểm soát chi phí.

---

## 14. Trace spans đề xuất

```text
dashboard.page.load
dashboard.kpis.fetch
dashboard.revenue.fetch
dashboard.orders.fetch
search.global
order.quick_view.fetch
order.status.update
inventory.alerts.fetch
report.export.create
notification.list.fetch
```

Span attributes:

- Endpoint.
- HTTP method.
- Status code.
- Duration.
- Cache status.
- Branch scope dạng không nhạy cảm.
- Result count.
- Error code.

---

## 15. Metrics taxonomy

Nhóm metrics:

1. Availability.
2. Errors.
3. Performance.
4. Business workflow.
5. Data freshness.
6. Security signals.
7. Background jobs.
8. Real-time connectivity.

---

## 16. Availability metrics

Theo dõi:

```text
dashboard_page_load_success_rate
dashboard_overview_api_success_rate
search_api_success_rate
notification_api_success_rate
export_job_success_rate
```

Không chỉ theo dõi HTTP 200; cần biết response có dữ liệu hợp lệ hay không.

---

## 17. Error metrics

```text
frontend_error_count
frontend_error_user_rate
widget_error_count
api_error_rate
contract_validation_failure_count
mutation_failure_rate
permission_denied_rate
```

`frontend_error_user_rate` quan trọng hơn raw count khi một user tạo nhiều lỗi lặp.

---

## 18. Performance metrics

Theo dõi:

```text
LCP
CLS
INP
TTFB
FCP
route_transition_duration
widget_load_duration
api_latency
search_response_duration
export_job_duration
```

Phân tích p50, p75, p95 và p99 khi phù hợp.

---

## 19. Core Web Vitals targets

Mục tiêu tham khảo:

```text
LCP p75 < 2,5s
CLS p75 < 0,1
INP p75 < 200ms
```

Phân tách theo:

- Route.
- Browser.
- Device class.
- Connection type.
- Release.

Không chỉ dùng average vì dễ che giấu nhóm người dùng bị chậm.

---

## 20. Widget performance

Mỗi widget quan trọng cần metric:

```text
widget_load_duration_seconds{widget="kpi"}
widget_load_duration_seconds{widget="revenue_chart"}
widget_load_duration_seconds{widget="orders_table"}
widget_load_duration_seconds{widget="inventory_alerts"}
```

Ghi nhận thêm:

- Cache hit/miss.
- Data source.
- Retry count.
- Empty result.
- Stale result.

---

## 21. Business workflow metrics

Theo dõi ở mức tổng hợp, không chứa PII:

```text
order_quick_view_open_count
order_status_update_success_rate
inventory_action_success_rate
report_export_request_count
report_export_success_rate
global_search_success_rate
notification_mark_read_success_rate
```

Các metrics này giúp phát hiện chức năng kỹ thuật hoạt động nhưng người dùng không hoàn thành được tác vụ.

---

## 22. Search metrics

Theo dõi:

- Search request count.
- Empty result rate.
- Error rate.
- Response duration.
- Result selected rate.
- Query abandonment rate.

Không log nguyên văn query nếu có thể chứa số điện thoại, tên khách hàng hoặc dữ liệu nhạy cảm.

Có thể log:

- Query length.
- Result type.
- Result count.
- Hashed category nếu được review.

---

## 23. Export metrics

```text
export_requests_total
export_jobs_completed_total
export_jobs_failed_total
export_job_duration_seconds
export_file_size_bytes
export_download_success_rate
```

Dimensions hợp lý:

- Report type.
- Format.
- Status.
- Size bucket.

Không dùng user ID hoặc branch ID có cardinality cao làm metric label nếu platform không phù hợp.

---

## 24. Notification metrics

Theo dõi:

- Unread count load success.
- Dropdown open rate.
- Mark-read success rate.
- Real-time event delay.
- Duplicate notification rate.
- Toast rate.

Cảnh báo nếu toast rate tăng đột biến vì có thể gây spam UI.

---

## 25. Data freshness metrics

```text
data_age_seconds{dataset="inventory"}
data_age_seconds{dataset="orders"}
data_age_seconds{dataset="revenue_kpi"}
data_age_seconds{dataset="notifications"}
```

Alert theo ngưỡng riêng:

- Inventory: ngắn.
- Orders: ngắn.
- Revenue aggregate: dài hơn.

Không dùng cùng một stale threshold cho mọi dataset.

---

## 26. Cache observability

Theo dõi:

- Cache hit rate.
- Cache miss rate.
- Revalidation duration.
- Stale response served count.
- Cache key scope anomalies.

Đặc biệt cần phát hiện cache key thiếu user hoặc branch scope vì có rủi ro lộ dữ liệu.

---

## 27. Real-time observability

Metrics:

```text
realtime_connection_state
realtime_reconnect_count
realtime_event_delay_seconds
realtime_duplicate_event_count
realtime_event_processing_failure_count
```

Log khi:

- Connection disconnected.
- Reconnect nhiều lần.
- Event parse fail.
- Event bị bỏ do duplicate.

Không alert mọi reconnect ngắn hạn; alert khi ảnh hưởng kéo dài.

---

## 28. Background job observability

Áp dụng cho:

- Export.
- Import.
- Sync inventory.
- Data aggregation.

Theo dõi:

- Queue depth.
- Queue wait time.
- Processing time.
- Success/failure.
- Retry count.
- Dead-letter count.

Job event phải có `jobId`, `type`, `status` và `requestId`.

---

## 29. Security signals

Theo dõi:

- Login failure spikes.
- Permission denied spikes.
- Branch scope violation attempts.
- Export denied count.
- Rate limit triggered count.
- Suspicious search volume.
- CSRF validation failure.
- Session anomaly.

Security metrics cần được chuyển tới kênh phù hợp, không trộn với mọi UI alert thông thường.

---

## 30. Audit vs observability logs

Audit log và observability log có mục tiêu khác nhau.

### Audit log

- Ai làm gì.
- Trên entity nào.
- Kết quả ra sao.
- Dùng cho kiểm soát và điều tra.

### Observability log

- Hệ thống chạy thế nào.
- API nào chậm.
- Component nào lỗi.

Không dùng observability log thay thế audit log cho thao tác nhạy cảm.

---

## 31. Frontend event schema

```ts
export type FrontendEvent = {
  eventName: string;
  occurredAt: string;
  route: string;
  release: string;
  environment: EnvironmentName;
  sessionId?: string;
  anonymousUserId?: string;
  userRole?: string;
  branchScope?: string;
  requestId?: string;
  traceId?: string;
  properties?: Record<string, string | number | boolean | null>;
};
```

`sessionId` và `anonymousUserId` phải tuân thủ privacy policy.

---

## 32. Event naming convention

Dùng dạng:

```text
domain.object.action
```

Ví dụ:

```text
dashboard.page.loaded
dashboard.widget.failed
orders.quick_view.opened
orders.status_update.failed
inventory.alert.opened
search.result.selected
reports.export.completed
notifications.item.read
```

Không dùng tên chung như `click`, `error1`, `event_test`.

---

## 33. Event properties

Properties nên:

- Có schema ổn định.
- Giới hạn cardinality.
- Không chứa PII.
- Dùng enum khi có thể.

Ví dụ tốt:

```json
{
  "widget": "orders_table",
  "state": "error",
  "errorCode": "UPSTREAM_UNAVAILABLE",
  "retryable": true
}
```

---

## 34. Cardinality control

Không dùng các giá trị có cardinality cao làm metric labels:

- User ID.
- Order ID.
- Full URL query.
- Error message động.
- Product name.

Các giá trị này có thể nằm trong logs/traces với kiểm soát privacy phù hợp.

---

## 35. Sampling

### Errors

- Fatal/critical: 100%.
- Common repeated error: có thể sample sau khi đủ bằng chứng.

### Traces

- Baseline: sampling thấp.
- Error traces: ưu tiên giữ.
- Slow traces: ưu tiên giữ.
- Release mới: tăng sampling tạm thời.

### Performance events

- Sample đủ đại diện theo browser và device.

Không sample audit events nhạy cảm cần lưu đầy đủ theo policy.

---

## 36. Data redaction

Trước khi gửi event:

- Xóa token.
- Xóa cookie.
- Mask phone/email.
- Xóa request body nhạy cảm.
- Loại query params có PII.
- Không attach HTML toàn trang.

Nên có hàm tập trung:

```ts
redactSensitiveData(payload)
```

---

## 37. Session replay policy

Nếu dùng session replay:

- Mask input mặc định.
- Mask PII và tài chính.
- Không ghi password hoặc OTP.
- Không record file upload content.
- Cho phép tắt theo policy.
- Giới hạn retention.
- Chỉ bật sau khi privacy review.

Không bật session replay toàn bộ production theo mặc định mà thiếu kiểm soát.

---

## 38. RUM instrumentation

Real User Monitoring nên thu:

- Page load.
- Route transition.
- Core Web Vitals.
- JS errors.
- API timing.
- Browser/device.
- Release.

Không dùng RUM để thu dữ liệu nghiệp vụ nhạy cảm chi tiết.

---

## 39. Synthetic monitoring

Synthetic checks đề xuất:

- Login page reachable.
- Dashboard route reachable.
- Overview API healthy.
- Search endpoint responsive.
- Notification endpoint responsive.
- Export endpoint queue accepted.

Tần suất:

- Critical availability: 1–5 phút.
- Full workflow synthetic: 15–60 phút.

Không chạy destructive mutation trên production tenant thật.

---

## 40. Health endpoints

Có thể tách:

```text
/health/live
/health/ready
```

### Liveness

Ứng dụng đang chạy.

### Readiness

Các dependency bắt buộc đủ để phục vụ request.

Health endpoint không trả secret hoặc topology nội bộ chi tiết.

---

## 41. Dashboard vận hành đề xuất

### Frontend Health Dashboard

- Error rate.
- Affected users.
- LCP/CLS/INP.
- Route load.
- Release comparison.

### API Dashboard

- Request rate.
- Error rate.
- Latency p95.
- Endpoint breakdown.

### Business Operations Dashboard

- Order action success.
- Inventory action success.
- Search success.
- Export success.

### Data Freshness Dashboard

- Inventory age.
- Orders age.
- Revenue aggregate age.

### Security Signals Dashboard

- Login failures.
- Permission denials.
- Rate limits.
- Scope violation attempts.

---

## 42. Alert design

Mỗi alert phải có:

- Tên rõ.
- Điều kiện.
- Severity.
- Owner.
- Runbook link.
- Dashboard link.
- Tác động dự kiến.
- Cách xác minh.

Ví dụ:

```text
Tên: Dashboard Overview Error Rate High
Điều kiện: error rate > 5% trong 5 phút
Severity: SEV-2
Owner: Frontend + Backend
Runbook: /runbooks/dashboard-overview-errors
```

---

## 43. Alert severity

### Critical

- Dashboard không tải diện rộng.
- Authentication failure diện rộng.
- Permission hoặc dữ liệu scope sai.

### High

- API lỗi trên 5%.
- Order mutation lỗi diện rộng.
- Inventory data stale nghiêm trọng.

### Medium

- Một widget lỗi tăng.
- Export job failure tăng.
- Web Vitals regression.

### Low

- Minor browser-specific issue.
- Warning trend chưa ảnh hưởng lớn.

---

## 44. Alert deduplication

- Gom alert cùng root cause.
- Không tạo một alert cho từng widget nếu backend dependency chung đang lỗi.
- Dùng suppression trong maintenance window.
- Tự đóng khi metric phục hồi đủ thời gian.

Không để một incident tạo hàng chục cảnh báo giống nhau.

---

## 45. Alert thresholds tham khảo

| Alert | Ngưỡng khởi đầu |
|---|---:|
| Dashboard overview error rate | >5% trong 5 phút |
| Frontend fatal affected users | >1% trong 10 phút |
| API p95 latency | >2 giây trong 10 phút |
| LCP p75 | >4 giây |
| Export failure | >10% trong 15 phút |
| Inventory data age | >15 phút |
| Order data age | >5 phút |
| Real-time disconnected | >10 phút |
| Permission denial anomaly | >3× baseline |

Ngưỡng phải được điều chỉnh theo baseline thật.

---

## 46. SLO đề xuất

### Dashboard availability

```text
99,9% theo tháng
```

### Dashboard overview success

```text
99,5% request thành công
```

### Order mutation success

```text
99,9% với request hợp lệ
```

### Search latency

```text
p95 < 1 giây
```

### Inventory freshness

```text
95% dữ liệu dưới 10 phút tuổi
```

SLO phải được Product và Engineering thống nhất.

---

## 47. Error budget

Error budget giúp quyết định:

- Có tiếp tục rollout không.
- Có cần ưu tiên reliability không.
- Có nên hoãn feature mới không.

Nếu burn rate cao:

- Dừng rollout.
- Ưu tiên fix reliability.
- Tăng monitoring.

Không dùng SLO chỉ để báo cáo mà không ảnh hưởng quyết định.

---

## 48. Release comparison

Sau deploy, so sánh release mới với cũ:

- Error rate.
- Affected users.
- LCP/INP.
- API latency.
- Export failure.
- Search success.
- Widget load failure.

Dashboard monitoring cần filter theo release version.

---

## 49. Deployment markers

Mỗi deploy phải tạo marker trên timeline observability:

```text
release v1.4.0 deployed
feature flag new_dashboard enabled 25%
rollback to v1.3.2
```

Điều này giúp liên hệ regression với thay đổi.

---

## 50. Runbooks

Runbook tối thiểu:

```text
runbooks/
├── dashboard-not-loading.md
├── overview-api-errors.md
├── order-mutation-failures.md
├── inventory-data-stale.md
├── export-job-failures.md
├── realtime-disconnected.md
└── permission-anomaly.md
```

Runbook gồm:

- Dấu hiệu.
- Dashboard/log query.
- Cách xác minh.
- Mitigation.
- Rollback/flag.
- Owner.
- Escalation.

---

## 51. Incident workflow integration

Alert quan trọng nên tích hợp với:

- On-call system.
- Chat/notification channel.
- Incident management tool.
- Status communication.

Alert message phải chứa đủ ngữ cảnh để người trực bắt đầu điều tra ngay.

---

## 52. Retention policy

Gợi ý cần được phê duyệt:

- High-volume debug logs: ngắn.
- Application errors: trung bình.
- Security/audit logs: dài hơn.
- Traces: sampling và retention phù hợp chi phí.
- Performance aggregates: dài để so xu hướng.

Retention thực tế phải cân bằng:

- Điều tra.
- Chi phí.
- Privacy.
- Yêu cầu pháp lý.

---

## 53. Cost control

- Sampling traces.
- Giới hạn event properties.
- Tránh cardinality cao.
- Giảm debug logs production.
- Aggregate metrics.
- Retention theo mức giá trị.
- Deduplicate repeated errors.

Không giảm chi phí bằng cách bỏ hoàn toàn lỗi critical hoặc audit event.

---

## 54. Local và staging observability

### Local

- Console structured logs.
- Dev overlay.
- Mock traces khi cần.

### Staging

- Gần production.
- Error reporting riêng project/environment.
- Performance test.
- Synthetic workflows.

Không để staging event làm nhiễu production alerts.

---

## 55. CI observability checks

CI có thể kiểm tra:

- Source map upload.
- Release metadata tồn tại.
- Monitoring SDK initialization.
- Sensitive field redaction tests.
- Event schema validation.
- Performance budget.
- Health endpoint.

Không block toàn bộ release chỉ vì optional analytics event thiếu nếu không ảnh hưởng vận hành.

---

## 56. Testing observability

Test cases:

- Error boundary gửi event đúng.
- API error có request ID.
- PII được redact.
- Release metadata gắn đúng.
- Metric không dùng label cardinality cao.
- Failed export tạo event.
- Contract parse error được ghi nhận.
- Logout clear user context.
- Permission denied không lộ entity data.

---

## 57. Logout và user context

Khi logout:

- Clear monitoring user context.
- Clear session tags.
- Clear query cache nhạy cảm.
- Không gắn event của user mới vào user cũ.

Không dùng email hoặc phone làm monitoring user identifier nếu không cần.

---

## 58. Privacy-safe identifiers

Có thể dùng:

- Internal user ID.
- One-way hash.
- Anonymous session ID.

Không dùng:

- Full email.
- Full phone.
- Customer name.

Identifier phải ổn định đủ để đếm affected users nhưng không lộ PII không cần thiết.

---

## 59. AI coding agent requirements

AI phải:

- Không thêm logging chứa request body nhạy cảm.
- Không gửi token, cookie hoặc PII vào error reporter.
- Gắn release metadata nếu sửa monitoring setup.
- Báo rõ event hoặc metric mới được thêm.
- Không tạo metric label cardinality cao.
- Không tuyên bố observability hoàn chỉnh nếu chưa có alert/runbook.
- Không tắt error reporting để che lỗi.
- Không làm fail-open với permission chỉ vì monitoring lỗi.

---

## 60. Acceptance checklist

- [ ] Release, commit và environment được gắn vào event.
- [ ] Frontend errors được thu thập.
- [ ] Widget errors có context.
- [ ] Request ID xuyên qua frontend và backend.
- [ ] Tracing dùng chuẩn phù hợp.
- [ ] Core Web Vitals được theo dõi.
- [ ] Widget load duration được đo.
- [ ] Search, export, order và inventory workflows có metrics.
- [ ] Data freshness có metric riêng.
- [ ] Real-time và background jobs có observability.
- [ ] Security signals được tách phù hợp.
- [ ] Logs không chứa secret hoặc PII dư thừa.
- [ ] Event naming và schema nhất quán.
- [ ] Cardinality được kiểm soát.
- [ ] Sampling policy rõ ràng.
- [ ] Synthetic monitoring có cho luồng chính.
- [ ] Dashboards vận hành đã xác định.
- [ ] Alerts có owner và runbook.
- [ ] Deployment markers hoạt động.
- [ ] Logout clear monitoring context.
- [ ] Retention và cost control được phê duyệt.

---

## 61. Những điều không được làm

- Không chỉ ghi console log và gọi đó là observability.
- Không log password, token, cookie hoặc PII đầy đủ.
- Không dùng user ID, order ID hoặc URL động làm metric label.
- Không alert theo từng lỗi đơn lẻ thiếu ngữ cảnh.
- Không trộn staging và production data.
- Không bỏ release metadata.
- Không dùng average thay cho percentile duy nhất.
- Không tạo alert thiếu owner hoặc runbook.
- Không bật session replay thiếu privacy review.
- Không dùng observability log thay audit log.
- Không tắt monitoring trong release mới để giảm noise.
- Không giữ debug logging production vô thời hạn.

---

## 62. Kết luận

Observability của Cynca VLXD Dashboard phải giúp đội kỹ thuật phát hiện, hiểu và khắc phục sự cố nhanh chóng. Hệ thống cần kết nối trải nghiệm người dùng, hiệu năng frontend, API, dữ liệu, workflow nghiệp vụ, release và bảo mật trong cùng một chuỗi quan sát. Mọi event phải có giá trị vận hành, có ngữ cảnh và tuân thủ quyền riêng tư.

File tiếp theo đề xuất:

```text
README.md
25-Runbooks.md
```