# Cynca VLXD Admin Dashboard — System Health & Incident Center Specification

> Phiên bản: 1.0  
> Phạm vi: Giám sát sức khỏe hệ thống, quản lý sự cố, trạng thái dịch vụ và quy trình ứng phó  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, DevOps, SRE, Security, Operations, QA và AI coding agent  
> Tài liệu liên quan: `22-Release-Operations.md`, `24-Observability.md`, `25-Runbooks.md`, `39-Audit-Activity-Center.md`, `46-Integration-Monitoring-Center.md`

---

## 1. Mục tiêu

System Health & Incident Center giúp Cynca VLXD theo dõi trạng thái toàn bộ nền tảng, phát hiện sự cố, xác định phạm vi ảnh hưởng và điều phối quá trình xử lý từ lúc phát hiện đến khi đóng hậu kiểm.

Hệ thống phải trả lời nhanh:

- Dịch vụ nào đang khỏe, suy giảm hoặc ngừng hoạt động?
- Người dùng, chi nhánh và quy trình nghiệp vụ nào bị ảnh hưởng?
- Sự cố bắt đầu từ khi nào và mức độ nghiêm trọng ra sao?
- Ai đang phụ trách xử lý?
- Runbook hoặc biện pháp tạm thời nào đang được áp dụng?
- Các chỉ số đã hồi phục hoàn toàn chưa?
- Có cần cập nhật thông báo nội bộ hoặc trang trạng thái không?
- Sau sự cố cần hành động khắc phục nào?

System Health & Incident Center không thay thế APM, metrics platform, log platform hoặc on-call tool. Đây là giao diện điều phối và tổng hợp phục vụ vận hành.

---

## 2. Phạm vi giám sát

```ts
export type SystemComponentCategory =
  | "frontend"
  | "api"
  | "database"
  | "cache"
  | "queue"
  | "storage"
  | "authentication"
  | "notification"
  | "reporting"
  | "import_export"
  | "integration"
  | "infrastructure";
```

Ví dụ component:

- Admin Web.
- Zalo Mini App API.
- Orders API.
- Inventory API.
- Finance API.
- PostgreSQL.
- Redis.
- Message Queue.
- Object Storage.
- Authentication service.
- Notification worker.
- Report worker.
- Import worker.

---

## 3. Permission model

```text
system_health.view
system_health.view_sensitive
system_health.metrics.view
system_health.logs.link
incidents.view
incidents.create
incidents.update
incidents.assign
incidents.resolve
incidents.close
incidents.postmortem.view
incidents.postmortem.manage
maintenance.view
maintenance.manage
status_page.manage
runbooks.view
runbooks.execute
```

Không dựa duy nhất vào role name.

`runbooks.execute`, `incidents.resolve`, `maintenance.manage` và `status_page.manage` phải là quyền nhạy cảm.

---

## 4. Information architecture

```text
System Health & Incident Center
├── System Overview
├── Services & Components
├── Active Incidents
├── Alert Stream
├── Dependencies
├── Maintenance Windows
├── Status Communications
├── Runbooks
├── Postmortems
└── Incident History
```

---

## 5. Service status model

```ts
export type ServiceHealthStatus =
  | "operational"
  | "degraded"
  | "partial_outage"
  | "major_outage"
  | "maintenance"
  | "unknown";
```

| Trạng thái | Ý nghĩa |
|---|---|
| Operational | Hoạt động bình thường |
| Degraded | Hiệu năng hoặc tỷ lệ lỗi xấu đi |
| Partial outage | Một phần chức năng không dùng được |
| Major outage | Chức năng chính ngừng hoạt động |
| Maintenance | Trong cửa sổ bảo trì |
| Unknown | Chưa đủ tín hiệu để xác định |

Không hiển thị `Operational` khi telemetry bị gián đoạn.

---

## 6. System component contract

```ts
export type SystemComponentHealth = {
  id: string;
  key: string;
  name: string;
  category: SystemComponentCategory;
  environment: "development" | "staging" | "production";
  status: ServiceHealthStatus;
  ownerTeam: string;
  availability24h?: number;
  errorRatePercent?: number;
  p95LatencyMs?: number;
  saturationPercent?: number;
  lastHeartbeatAt?: string;
  activeIncidentIds?: string[];
  dependencies?: string[];
  affectedFeatures?: string[];
};
```

---

## 7. System Overview

KPI đề xuất:

1. Components operational.
2. Components degraded/outage.
3. Active incidents.
4. Critical alerts.
5. Availability 24 giờ.
6. Error budget remaining.

Widgets:

- Service status grid.
- Active incident summary.
- Error and latency trend.
- Infrastructure saturation.
- User impact summary.
- Recent deployments.
- Maintenance schedule.

---

## 8. Service status grid

Mỗi card gồm:

- Service name.
- Category.
- Environment.
- Status.
- Availability.
- p95 latency.
- Error rate.
- Last heartbeat.
- Incident badge.
- Owner team.

Không dùng màu làm tín hiệu duy nhất.

---

## 9. Health signals

Tín hiệu có thể gồm:

- Request success rate.
- Error rate.
- p50/p95/p99 latency.
- Throughput.
- Queue depth.
- CPU/memory/disk saturation.
- Database connections.
- Cache hit rate.
- Worker lag.
- Synthetic checks.
- Business transaction success.

Không kết luận sức khỏe chỉ từ một metric kỹ thuật.

---

## 10. Golden signals

Khuyến nghị theo dõi:

```text
Latency
Traffic
Errors
Saturation
```

Với từng service, UI cần hiển thị threshold, actual, trend và window.

Business-critical service có thể bổ sung:

- Order creation success.
- Inventory update delay.
- Payment completion.
- Message delivery success.

---

## 11. SLO và error budget

```ts
export type ServiceSLO = {
  serviceId: string;
  indicatorKey: string;
  target: number;
  window: "7d" | "28d" | "30d" | "90d";
  actual: number;
  errorBudgetRemainingPercent: number;
  burnRateShortWindow?: number;
  burnRateLongWindow?: number;
};
```

UI phải phân biệt:

- SLO breach.
- Fast burn.
- Slow burn.
- Telemetry unavailable.

---

## 12. Dependency map

Hiển thị quan hệ:

```text
Admin Web
→ API Gateway
→ Orders API
→ Database
→ Queue
→ Notification Worker
```

Yêu cầu:

- Tối giản và có filter.
- Highlight dependency lỗi.
- Hiển thị upstream/downstream impact.
- Có list alternative cho accessibility.

Không tạo sơ đồ quá phức tạp không thể đọc.

---

## 13. Alert stream

Alert row gồm:

- Alert name.
- Severity.
- Service.
- State.
- Started at.
- Duration.
- Metric/rule.
- Current value.
- Owner.
- Incident link.

States:

```text
Firing
Acknowledged
Silenced
Resolved
Suppressed by Maintenance
```

---

## 14. Alert severity

```ts
export type AlertSeverity = "critical" | "high" | "medium" | "low" | "info";
```

Gợi ý:

- Critical: mất chức năng chính hoặc rủi ro dữ liệu.
- High: suy giảm lớn, nhiều người dùng bị ảnh hưởng.
- Medium: ảnh hưởng hạn chế hoặc cần điều tra.
- Low: cảnh báo sớm.
- Info: thay đổi trạng thái hoặc phục hồi.

Không để mọi alert đều là critical.

---

## 15. Alert deduplication

Deduplicate theo:

- Alert rule.
- Service.
- Scope.
- Time window.
- Incident correlation.

Không tạo hàng trăm alert riêng cho cùng một nguyên nhân gốc.

Có thể group theo incident hoặc dependency failure.

---

## 16. Incident severity model

```ts
export type IncidentSeverity = "SEV-1" | "SEV-2" | "SEV-3" | "SEV-4";
```

| Severity | Mô tả |
|---|---|
| SEV-1 | Ngừng hoạt động diện rộng, rủi ro dữ liệu hoặc bảo mật nghiêm trọng |
| SEV-2 | Suy giảm lớn, nhiều chức năng hoặc người dùng bị ảnh hưởng |
| SEV-3 | Ảnh hưởng hạn chế, có workaround |
| SEV-4 | Sự cố nhỏ, không ảnh hưởng lớn đến vận hành |

Severity phải dựa trên impact và urgency, không chỉ theo metric.

---

## 17. Incident status model

```ts
export type IncidentStatus =
  | "investigating"
  | "identified"
  | "mitigating"
  | "monitoring"
  | "resolved"
  | "postmortem_pending"
  | "closed";
```

Không đóng incident ngay khi metric vừa hồi phục trong vài giây; cần monitoring period phù hợp.

---

## 18. Incident contract

```ts
export type SystemIncident = {
  id: string;
  title: string;
  severity: IncidentSeverity;
  status: IncidentStatus;
  environment: "staging" | "production";
  affectedComponentIds: string[];
  affectedFeatureKeys: string[];
  affectedBranchIds?: string[];
  startedAt: string;
  detectedAt: string;
  acknowledgedAt?: string;
  mitigatedAt?: string;
  resolvedAt?: string;
  incidentCommanderUserId?: string;
  communicationsOwnerUserId?: string;
  summary: string;
  currentImpact?: string;
  workaround?: string;
  rootCauseCategory?: string;
  requestIds?: string[];
  traceIds?: string[];
};
```

---

## 19. Active incident card

Hiển thị:

- Severity.
- Title.
- Status.
- Duration.
- Affected features.
- User/business impact.
- Incident commander.
- Last update.
- Next update time.
- Primary action.

SEV-1 và SEV-2 phải nằm trên vùng đầu của trang.

---

## 20. Incident creation flow

```text
Chọn alert/service
→ Xác định severity
→ Mô tả impact
→ Chọn affected components/features
→ Gán Incident Commander
→ Chọn communication owner
→ Tạo incident
→ Mở timeline và runbook
```

Có thể tạo tự động từ alert nhưng phải cho người có quyền xác nhận và cập nhật severity.

---

## 21. Incident timeline

Timeline events:

```text
incident.created
incident.acknowledged
incident.severity_changed
incident.status_changed
incident.owner_assigned
runbook.step_executed
mitigation.applied
communication.published
incident.resolved
postmortem.created
```

Mỗi item gồm actor, timestamp, summary và liên kết liên quan.

Không cho sửa lịch sử gốc; correction phải là event mới.

---

## 22. Incident roles

### Incident Commander

- Điều phối tổng thể.
- Quyết định severity và trạng thái.
- Phân công workstream.

### Technical Lead

- Điều tra và thực hiện mitigation.

### Communications Owner

- Cập nhật nội bộ và status page.

### Scribe

- Ghi timeline và quyết định.

Một người có thể giữ nhiều vai trò với incident nhỏ, nhưng SEV-1 nên tách vai trò nếu có nguồn lực.

---

## 23. User and business impact

Impact summary có thể gồm:

- Số user/session lỗi.
- Branch bị ảnh hưởng.
- Tỷ lệ đơn tạo thất bại.
- Số task kho chậm.
- Payment failures.
- Message delivery failures.
- Data freshness delay.

Không hiển thị PII hoặc dữ liệu tài chính chi tiết không cần thiết.

---

## 24. Incident workstreams

Có thể chia:

- Application.
- Database.
- Infrastructure.
- Integration.
- Security.
- Customer communication.
- Data recovery.

Mỗi workstream có owner, status, tasks và updates.

Không biến Incident Center thành hệ thống project management đầy đủ.

---

## 25. Runbook integration

Runbook card gồm:

- Tên.
- Service.
- Trigger conditions.
- Last reviewed.
- Owner.
- Automation level.
- Required permission.

Runbook types:

```text
Diagnostic
Mitigation
Failover
Recovery
Verification
Communication
```

---

## 26. Runbook execution

Các bước phải phân loại:

- Manual read-only check.
- Manual mutation.
- Automated safe action.
- High-risk action.

High-risk action cần:

- Re-authentication.
- Confirmation.
- Dry run nếu hỗ trợ.
- Approval nếu policy yêu cầu.
- Audit.

Không cho chạy shell command tùy ý từ giao diện.

---

## 27. Common mitigation actions

Có thể gồm:

- Restart worker được kiểm soát.
- Scale worker pool.
- Pause consumer.
- Enable fallback mode.
- Disable feature flag.
- Open circuit breaker.
- Switch read-only mode.
- Failover service.

Mỗi action phải có precondition, rollback và owner rõ.

---

## 28. Read-only and degraded modes

Khi sự cố nghiêm trọng, hệ thống có thể chuyển một số module sang:

```text
Read-only
Queue writes
Feature disabled
Fallback data
Maintenance page
```

UI phải giải thích rõ:

- Chức năng nào bị hạn chế.
- Dữ liệu có thể stale hay không.
- Action nào đang được queue.
- Khi nào cập nhật tiếp theo.

---

## 29. Status communication

Kênh:

- In-app banner.
- Notification Center.
- Email nội bộ.
- Zalo OA nội bộ nếu được phê duyệt.
- External status page nếu có.

Thông báo gồm:

- Tình trạng hiện tại.
- Chức năng bị ảnh hưởng.
- Workaround.
- Thời gian cập nhật tiếp theo.

Không công bố nguyên nhân chưa xác nhận như sự thật.

---

## 30. Status update templates

```text
Investigating
Identified
Mitigation in progress
Monitoring recovery
Resolved
```

Mỗi template có nội dung mẫu nhưng cần chỉnh theo incident.

Không đưa stack trace, secret hoặc chi tiết bảo mật nhạy cảm vào thông báo.

---

## 31. Maintenance windows

```ts
export type MaintenanceWindow = {
  id: string;
  title: string;
  affectedComponentIds: string[];
  startsAt: string;
  endsAt: string;
  timezone: string;
  expectedImpact: string;
  ownerUserId: string;
  status: "scheduled" | "in_progress" | "completed" | "cancelled";
  communicationPlan?: string;
};
```

Yêu cầu:

- Cảnh báo trước.
- Scope rõ.
- Có rollback plan.
- Không suppress alert ngoài component và thời gian đã khai báo.

---

## 32. Deployment correlation

Incident detail nên hiển thị:

- Deployment gần thời điểm lỗi.
- Version/commit.
- Feature flags thay đổi.
- Settings change sets.
- Database migration.
- Infrastructure changes.

Correlation là gợi ý điều tra, không tự kết luận deployment là nguyên nhân.

---

## 33. Incident resolution criteria

Trước khi resolve:

- Metric chính trở lại ngưỡng.
- Business transaction thành công.
- Queue backlog giảm về mức chấp nhận.
- Không còn alert critical liên quan.
- Dữ liệu được kiểm tra nhất quán.
- Workaround và communication đã cập nhật.
- Có monitoring period phù hợp.

---

## 34. Postmortem

Postmortem gồm:

- Executive summary.
- Customer/business impact.
- Timeline.
- Detection.
- Root cause.
- Contributing factors.
- What went well.
- What went poorly.
- Where we got lucky.
- Corrective actions.
- Owners và due dates.

Mục tiêu là cải tiến hệ thống, không đổ lỗi cá nhân.

---

## 35. Root cause categories

```text
Application defect
Configuration error
Capacity
Dependency failure
Database
Network
Deployment
Security
Data quality
Process gap
Unknown
```

Không ép chọn root cause trước khi điều tra hoàn tất.

---

## 36. Corrective action tracking

```ts
export type IncidentActionItem = {
  id: string;
  incidentId: string;
  title: string;
  category: "prevent" | "detect" | "mitigate" | "recover" | "process";
  ownerUserId: string;
  dueAt: string;
  status: "open" | "in_progress" | "blocked" | "completed" | "cancelled";
  verificationEvidence?: string;
};
```

Không đóng action item chỉ bằng ghi chú; cần evidence hoặc review khi phù hợp.

---

## 37. Incident metrics

Theo dõi:

- MTTD: Mean Time to Detect.
- MTTA: Mean Time to Acknowledge.
- MTTR: Mean Time to Restore/Resolve.
- Incident count theo severity.
- Repeat incident rate.
- Alert-to-incident ratio.
- Postmortem completion rate.
- Corrective action overdue rate.

Không dùng metric để đánh giá hoặc phạt cá nhân thiếu ngữ cảnh.

---

## 38. Search và filters

Filters:

- Environment.
- Component.
- Status.
- Severity.
- Owner team.
- Date range.
- Affected module.
- Branch impact.
- Postmortem state.

Search:

- Incident ID.
- Alert ID.
- Request ID.
- Trace ID.
- Service name.
- Error code.

---

## 39. API endpoints đề xuất

```text
GET    /api/v1/system-health/summary
GET    /api/v1/system-health/components
GET    /api/v1/system-health/components/:id
GET    /api/v1/system-health/alerts
GET    /api/v1/incidents
POST   /api/v1/incidents
GET    /api/v1/incidents/:id
PATCH  /api/v1/incidents/:id
POST   /api/v1/incidents/:id/assign
POST   /api/v1/incidents/:id/status
POST   /api/v1/incidents/:id/timeline
POST   /api/v1/incidents/:id/resolve
POST   /api/v1/incidents/:id/close
GET    /api/v1/incidents/:id/postmortem
POST   /api/v1/incidents/:id/postmortem
GET    /api/v1/maintenance-windows
POST   /api/v1/maintenance-windows
GET    /api/v1/runbooks
POST   /api/v1/runbooks/:id/execute
```

---

## 40. Frontend component inventory

```text
SystemHealthIncidentCenterPage
├── SystemHealthSummaryCards
├── ServiceStatusGrid
├── ServiceHealthCard
├── GoldenSignalsPanel
├── DependencyMap
├── AlertStreamTable
├── ActiveIncidentList
├── IncidentDetailPage
├── IncidentTimeline
├── IncidentRolePanel
├── ImpactSummary
├── RunbookPanel
├── MitigationActionDialog
├── StatusCommunicationEditor
├── MaintenanceWindowList
├── PostmortemEditor
└── CorrectiveActionTable
```

---

## 41. Loading, empty và error states

### Loading

- Service cards skeleton.
- Alert rows skeleton.
- Incident timeline skeleton.
- Dependency map placeholder.

### Empty

```text
Không có incident đang hoạt động.
Không có alert critical trong phạm vi hiện tại.
Không có maintenance window sắp tới.
```

### Error

- Telemetry lỗi không hiển thị Operational.
- Incident update lỗi giữ trạng thái cũ.
- Runbook action lỗi không báo thành công.
- Có request ID.
- Không hiển thị stack trace hoặc secret.

---

## 42. Responsive behavior

### Desktop

- Service grid 3–4 cột.
- Incident detail nhiều panel.
- Timeline và impact song song.

### Tablet

- Grid 2 cột.
- Dependency map chuyển list nếu hẹp.
- Detail dùng tabs hoặc drawers.

### Mobile

- Chỉ ưu tiên active incidents, status và communication.
- High-risk runbook actions có thể yêu cầu desktop.
- Timeline dạng card list.

---

## 43. Accessibility

- Service status không chỉ dùng màu.
- Metrics có text và đơn vị rõ.
- Dependency map có danh sách thay thế.
- Timeline dùng semantic list.
- Dialog quản lý focus.
- Alert table có caption và headers.
- Keyboard có thể mở incident và cập nhật trạng thái.
- Touch target tối thiểu 44px.

---

## 44. Security và privacy

- Backend enforce permission và environment scope.
- Không hiển thị secret, token, internal credential hoặc raw stack trace.
- Request/trace ID có thể hiển thị theo quyền.
- Security incident có restricted visibility.
- Runbook action dùng allowlist, không shell tùy ý.
- Re-authentication cho action nhạy cảm.
- Status communication phải sanitize.
- Audit mọi severity/status/owner/runbook change.

---

## 45. Audit events

```text
incident.created
incident.severity.updated
incident.status.updated
incident.owner.assigned
incident.timeline.updated
incident.runbook.executed
incident.communication.published
incident.resolved
incident.closed
incident.postmortem.published
maintenance.created
maintenance.cancelled
```

---

## 46. Analytics và observability

Analytics an toàn:

```text
system_health.center.viewed
system_health.component.opened
incident.opened
incident.status_changed
incident.runbook.opened
incident.postmortem.opened
```

Không gửi incident summary, request ID, trace ID hoặc metric values chi tiết vào analytics.

Observability của chính Incident Center:

- Health API latency.
- Incident mutation failure.
- Alert ingestion lag.
- Communication publishing failure.
- Runbook execution failure.
- Audit write failure.

---

## 47. Testing

### Unit

- Status mapping.
- Severity mapping.
- SLO burn calculation.
- Incident duration.
- Resolution criteria.
- Impact aggregation.

### Component

- Service card.
- Alert stream.
- Incident card.
- Timeline.
- Runbook panel.
- Status editor.
- Postmortem form.

### Integration

- Alert tạo incident.
- Incident status update có audit.
- Maintenance suppression đúng scope.
- Runbook permission enforcement.
- Telemetry unavailable state.
- Status communication publish failure.

### E2E

- Mở SEV-2 incident.
- Gán Incident Commander.
- Thêm timeline update.
- Chạy diagnostic runbook.
- Publish status update.
- Chuyển sang Monitoring.
- Resolve incident.
- Tạo postmortem và corrective actions.
- Viewer không thấy security incident restricted.

---

## 48. Visual QA

```text
System Health — All Operational
Service Degraded
Partial Outage
Major Outage
Active SEV-1 Incident
Incident Timeline
Dependency Failure
Telemetry Unknown
Runbook Confirmation
Status Communication
Maintenance Window
Postmortem Editor
Mobile Incident View
```

---

## 49. AI-assisted incident summary requirements

Nếu dùng AI hỗ trợ:

- Chỉ dùng dữ liệu đã permission-filtered và redact.
- Không gửi secret, raw logs hoặc PII tới provider chưa phê duyệt.
- Không tự xác định root cause như sự thật.
- Phân biệt fact, hypothesis và recommendation.
- Mọi claim phải map tới metric, alert hoặc timeline source.
- Không tự chạy mitigation hoặc đóng incident.
- Có human review trước khi publish communication/postmortem.

---

## 50. AI coding agent requirements

AI coding agent phải:

- Không coi telemetry missing là Operational.
- Không cho chạy shell tùy ý từ runbook UI.
- Không tự resolve incident chỉ vì alert hết firing.
- Không công bố root cause chưa xác nhận.
- Không suppress alert ngoài maintenance scope.
- Không hiển thị security incident cho user thiếu quyền.
- Bổ sung test cho telemetry outage, runbook authorization, status communication và incident audit.
- Báo rõ metrics source, incident workflow và backend assumptions.

---

## 51. Acceptance checklist

- [ ] Có System Health Overview.
- [ ] Có component status và golden signals.
- [ ] Có SLO/error budget.
- [ ] Có dependency map và accessible alternative.
- [ ] Có Alert Stream và deduplication.
- [ ] Có incident severity/status model.
- [ ] Có Active Incident Card và detail page.
- [ ] Có timeline, roles và impact summary.
- [ ] Có runbook integration và action controls.
- [ ] Có degraded/read-only modes.
- [ ] Có status communication.
- [ ] Có maintenance windows.
- [ ] Có deployment correlation.
- [ ] Có resolution criteria.
- [ ] Có postmortem và corrective actions.
- [ ] Permission, security và audit đầy đủ.
- [ ] Loading, empty, error và responsive states hoàn chỉnh.
- [ ] Accessibility, analytics, observability và testing đầy đủ.

---

## 52. Những điều không được làm

- Không hiển thị Operational khi telemetry lỗi.
- Không dùng một metric duy nhất để kết luận hệ thống khỏe.
- Không chạy shell command tùy ý từ UI.
- Không tự đóng incident khi alert tắt.
- Không sửa timeline event gốc.
- Không công bố nguyên nhân chưa xác nhận.
- Không dùng maintenance để che alert sai phạm vi.
- Không hiển thị secret, raw stack trace hoặc security incident ngoài quyền.
- Không dùng incident metrics để đánh giá cá nhân thiếu ngữ cảnh.
- Không thay thế APM, logs hoặc on-call platform bằng Dashboard tổng hợp.

---

## 53. Kết luận

System Health & Incident Center của Cynca VLXD phải cung cấp bức tranh đáng tin cậy về trạng thái dịch vụ, tác động nghiệp vụ và tiến trình xử lý sự cố. Giao diện cần hỗ trợ phát hiện, điều phối, mitigation, communication, resolution và hậu kiểm mà vẫn giữ chặt permission, audit, an toàn runbook và tính trung thực của telemetry.

File tiếp theo đề xuất:

```text
README.md
48-Backup-Recovery-Center.md
```