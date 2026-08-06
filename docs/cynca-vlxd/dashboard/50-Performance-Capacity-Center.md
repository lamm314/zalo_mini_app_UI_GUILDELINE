# Cynca VLXD Admin Dashboard — Performance & Capacity Center Specification

> Phiên bản: 1.0  
> Phạm vi: Theo dõi hiệu năng, tải hệ thống, dung lượng, xu hướng tăng trưởng và kế hoạch mở rộng  
> Đối tượng sử dụng: Product, Frontend, Backend, DevOps, SRE, Database, Operations, QA và AI coding agent  
> Tài liệu liên quan: `20-Frontend-Architecture.md`, `22-Release-Operations.md`, `24-Observability.md`, `46-Integration-Monitoring-Center.md`, `47-System-Health-Incident-Center.md`

---

## 1. Mục tiêu

Performance & Capacity Center giúp Cynca VLXD theo dõi tốc độ phản hồi, tải tài nguyên, độ trễ nghiệp vụ và khả năng đáp ứng tăng trưởng trước khi hệ thống bị quá tải.

Hệ thống phải trả lời nhanh:

- Trang và API nào đang chậm?
- Người dùng hoặc chi nhánh nào bị ảnh hưởng?
- CPU, memory, database, queue và storage đang sử dụng bao nhiêu?
- Có bottleneck ở frontend, backend, database hay integration?
- Tốc độ tăng trưởng hiện tại sẽ chạm giới hạn khi nào?
- Có cần scale, tối ưu query, tăng worker hay dọn storage?
- Release gần đây có làm hiệu năng xấu đi không?
- Performance budget có đang bị vi phạm?

---

## 2. Permission model

```text
performance.center.view
performance.frontend.view
performance.api.view
performance.database.view
performance.infrastructure.view
performance.capacity.view
performance.forecast.view
performance.recommendations.view
performance.tests.run
performance.baselines.manage
performance.audit.view
```

Không dựa duy nhất vào role name.

---

## 3. Information architecture

```text
Performance & Capacity Center
├── Tổng quan
├── Frontend Performance
├── API Performance
├── Database Performance
├── Queues & Workers
├── Infrastructure Capacity
├── Storage Growth
├── Performance Regressions
├── Capacity Forecast
├── Load Tests
└── Optimization History
```

---

## 4. Performance status model

```ts
export type PerformanceStatus =
  | "good"
  | "needs_attention"
  | "poor"
  | "critical"
  | "unknown";
```

Không hiển thị `good` khi telemetry không đủ.

---

## 5. Trang tổng quan

KPI đề xuất:

1. Frontend LCP p75.
2. API p95 latency.
3. Error rate.
4. Database saturation.
5. Queue backlog age.
6. Capacity headroom.

Widgets:

- User-experience trend.
- Slowest endpoints.
- Database bottlenecks.
- Queue and worker pressure.
- Infrastructure saturation.
- Capacity forecast.
- Recent regressions.

---

## 6. Core performance contract

```ts
export type PerformanceMetric = {
  key: string;
  label: string;
  value: number | null;
  unit: "ms" | "s" | "percent" | "count" | "bytes" | "rps";
  percentile?: "p50" | "p75" | "p90" | "p95" | "p99";
  status: PerformanceStatus;
  target?: number;
  comparisonValue?: number;
  updatedAt: string;
};
```

---

## 7. Frontend performance

Theo dõi:

- LCP.
- INP.
- CLS.
- TTFB.
- Route transition time.
- JavaScript error rate.
- Bundle size.
- API waterfall.

Phân tách theo:

- Route.
- Device category.
- Network type bucket.
- Browser.
- Region/branch nếu phù hợp.

Không thu thập URL hoặc metadata chứa PII.

---

## 8. Frontend performance budgets

Ví dụ:

```text
LCP p75 < 2,5 giây
INP p75 < 200 ms
CLS p75 < 0,1
Initial JS bundle theo route trong budget đã định
```

Budget phải được version hóa và gắn owner.

---

## 9. Route performance table

Cột:

- Route.
- LCP p75.
- INP p75.
- TTFB p75.
- Error rate.
- Sample size.
- Trend.
- Budget status.
- Last release.

Không kết luận route tốt khi sample quá nhỏ mà thiếu cảnh báo.

---

## 10. API performance

Theo dõi:

- Request rate.
- Success rate.
- p50/p95/p99 latency.
- Timeout rate.
- Error codes.
- Payload-size bucket.
- Cache hit rate.
- Dependency latency.

Phân tách theo endpoint, method, service, environment và release.

---

## 11. Slow endpoint table

Cột:

- Endpoint template đã sanitize.
- Service.
- p95 latency.
- Error rate.
- RPS.
- Sample size.
- Dependency contribution.
- Regression state.
- Owner.

Không hiển thị raw URL chứa entity ID hoặc query PII.

---

## 12. Database performance

Theo dõi:

- Query latency.
- Slow query count.
- Connection pool usage.
- Lock wait.
- Deadlocks.
- Cache hit ratio.
- IOPS.
- Replica lag.
- Table/index growth.

Không hiển thị raw query với dữ liệu người dùng. Dùng query fingerprint.

---

## 13. Query fingerprint contract

```ts
export type QueryPerformanceSummary = {
  fingerprint: string;
  operation: "select" | "insert" | "update" | "delete" | "other";
  database: string;
  p95DurationMs: number;
  calls: number;
  rowsProcessed?: number;
  lockWaitMs?: number;
  ownerService?: string;
  regressionPercent?: number;
};
```

---

## 14. Queue và worker performance

Theo dõi:

- Queue depth.
- Oldest message age.
- Processing rate.
- Retry rate.
- DLQ rate.
- Worker utilization.
- Concurrency.
- Job duration.

Queue cần phân biệt backlog bình thường với backlog gây breach SLA.

---

## 15. Infrastructure capacity

Tài nguyên:

- CPU.
- Memory.
- Disk.
- Network.
- Container/pod count.
- Node capacity.
- Database connections.
- Cache memory.
- Object-storage usage.

Mỗi resource hiển thị current, peak, target, headroom và forecast.

---

## 16. Capacity headroom

```ts
export type CapacityHeadroom = {
  resourceKey: string;
  currentUsagePercent: number;
  safeLimitPercent: number;
  headroomPercent: number;
  peakUsagePercent?: number;
  forecastBreachAt?: string;
  status: PerformanceStatus;
};
```

Không dùng 100% làm safe limit mặc định.

---

## 17. Storage growth

Theo dõi:

- Database size.
- Object storage.
- Audit logs.
- Reports/import files.
- Backup storage.
- Log retention.

Hiển thị:

- Current size.
- Growth 7/30/90 ngày.
- Retention impact.
- Forecast full date.
- Largest contributors.

---

## 18. Capacity forecast

Forecast dựa trên:

- Historical trend.
- Seasonal pattern.
- Planned campaigns.
- New branches.
- Product/import growth.
- Known releases.

Phải hiển thị confidence và assumptions.

Không trình bày forecast như ngày chắc chắn hệ thống sẽ đầy.

---

## 19. Performance regression

Regression có thể phát hiện theo:

- Trước/sau deployment.
- Trước/sau feature flag.
- So với baseline.
- So với cùng giờ/ngày.
- Theo route hoặc endpoint.

```ts
export type PerformanceRegression = {
  id: string;
  metricKey: string;
  scopeType: "route" | "endpoint" | "query" | "worker" | "service";
  scopeKey: string;
  baselineValue: number;
  currentValue: number;
  changePercent: number;
  confidence: "high" | "medium" | "low";
  detectedAt: string;
  relatedRelease?: string;
  status: "open" | "investigating" | "accepted" | "resolved";
};
```

---

## 20. Release correlation

Hiển thị:

- Release version.
- Commit/deployment time.
- Feature flags changed.
- Config changes.
- Metrics before/after.
- Affected routes/endpoints.

Correlation là tín hiệu điều tra, không tự kết luận release là nguyên nhân.

---

## 21. Business performance signals

Theo dõi thêm:

- Thời gian tạo đơn.
- Thời gian tìm sản phẩm.
- Thời gian load tồn kho.
- Thời gian xác nhận task kho.
- Thời gian tạo báo cáo.
- Thời gian gửi thông báo.

Business latency giúp ưu tiên tối ưu đúng trải nghiệm người dùng.

---

## 22. Performance recommendations

Recommendation types:

- Add index.
- Optimize query.
- Increase worker concurrency.
- Reduce payload.
- Enable cache.
- Split report job.
- Archive old data.
- Scale service.
- Optimize bundle.

Mỗi recommendation cần evidence, expected impact, risk và owner.

Không tự áp dụng recommendation lên production.

---

## 23. Load test center

Hỗ trợ:

- Test scenario registry.
- Target environment.
- Virtual users/RPS.
- Duration.
- Ramp-up.
- Success criteria.
- Result comparison.

Không chạy load test vào production thiếu approval và maintenance plan.

---

## 24. Load test contract

```ts
export type LoadTestRun = {
  id: string;
  scenarioKey: string;
  environment: "development" | "staging" | "production";
  status: "draft" | "awaiting_approval" | "queued" | "running" | "completed" | "failed" | "cancelled";
  targetRps?: number;
  durationSeconds: number;
  startedAt?: string;
  completedAt?: string;
  p95LatencyMs?: number;
  errorRatePercent?: number;
  passed?: boolean;
};
```

---

## 25. Load-test safety

- Scenario allowlist.
- Environment guard.
- Rate ceiling.
- Approval cho production.
- Kill switch.
- Test-data isolation.
- Không gửi notification thật hoặc thanh toán thật.
- Audit đầy đủ.

---

## 26. Baselines

Baseline có thể theo:

- Release ổn định gần nhất.
- 7/30 ngày.
- Theo giờ cao điểm.
- Theo device/network.
- Theo branch scale.

Baseline phải version hóa và không tự thay đổi âm thầm.

---

## 27. Alerting

Cảnh báo khi:

- LCP/INP vượt budget.
- API p95 tăng mạnh.
- Error rate spike.
- Database connection gần đầy.
- Queue oldest age vượt SLA.
- Disk/storage gần ngưỡng.
- Capacity forecast sắp breach.
- Regression sau release.

Không alert mọi biến động nhỏ; cần duration và sample threshold.

---

## 28. API endpoints đề xuất

```text
GET  /api/v1/performance/summary
GET  /api/v1/performance/frontend
GET  /api/v1/performance/routes
GET  /api/v1/performance/apis
GET  /api/v1/performance/queries
GET  /api/v1/performance/queues
GET  /api/v1/performance/capacity
GET  /api/v1/performance/storage
GET  /api/v1/performance/regressions
PATCH /api/v1/performance/regressions/:id
GET  /api/v1/performance/load-tests
POST /api/v1/performance/load-tests
POST /api/v1/performance/load-tests/:id/cancel
GET  /api/v1/performance/baselines
PATCH /api/v1/performance/baselines/:id
```

---

## 29. Frontend component inventory

```text
PerformanceCapacityCenterPage
├── PerformanceSummaryCards
├── FrontendVitalsPanel
├── RoutePerformanceTable
├── ApiPerformanceTable
├── QueryFingerprintTable
├── QueuePressurePanel
├── InfrastructureCapacityGrid
├── CapacityHeadroomCard
├── StorageGrowthChart
├── CapacityForecastPanel
├── RegressionList
├── ReleaseComparisonDrawer
├── RecommendationPanel
└── LoadTestRunWizard
```

---

## 30. Loading, empty và error states

- Telemetry lỗi phải hiển thị Unknown, không Good.
- Empty regression list chỉ có ý nghĩa khi detector đang hoạt động.
- Sample size thấp phải có warning.
- Forecast lỗi không làm mất current usage.
- Không hiển thị raw query, token hoặc payload.

---

## 31. Responsive và accessibility

- Desktop ưu tiên bảng, chart và comparison panels.
- Tablet giảm cột và dùng drawer.
- Mobile chỉ ưu tiên summary, critical regression và capacity warning.
- Charts có text summary hoặc table alternative.
- Status không chỉ dùng màu.
- Tables có caption và headers.
- Dialog load test quản lý focus.

---

## 32. Security và privacy

- Backend enforce environment và service scope.
- Không hiển thị raw URL/query chứa PII.
- Không cho chạy arbitrary benchmark script.
- Load test production cần approval và re-authentication.
- Không gửi payment/message thật trong test.
- Không gửi metric dimensions nhạy cảm vào analytics.
- Audit baseline, test và capacity-policy changes.

---

## 33. Audit events

```text
performance.baseline.updated
performance.regression.acknowledged
performance.regression.resolved
performance.load_test.requested
performance.load_test.approved
performance.load_test.started
performance.load_test.completed
performance.capacity_threshold.updated
```

---

## 34. Analytics và observability

Analytics an toàn:

```text
performance.center.viewed
performance.route.opened
performance.endpoint.opened
performance.regression.opened
performance.load_test.opened
```

Không gửi raw endpoint, query fingerprint nội bộ, metric values chi tiết hoặc infrastructure identifiers vào analytics.

Observability của chính Center:

- Metrics-query latency.
- Dashboard render errors.
- Forecast-service failure.
- Regression-detector lag.
- Load-test orchestration failure.

---

## 35. Testing

### Unit

- Status thresholds.
- Percentile formatting.
- Headroom calculation.
- Forecast-breach mapping.
- Regression comparison.

### Integration

- Telemetry unavailable → Unknown.
- Release comparison đúng time window.
- Sample thấp có warning.
- Production load test cần approval.
- Query/URL redaction.

### E2E

- Mở route chậm.
- So sánh trước/sau release.
- Xem database bottleneck.
- Xem storage forecast.
- Tạo staging load test.
- Theo dõi kết quả.
- Viewer thiếu quyền không thấy infrastructure detail.

---

## 36. AI-assisted recommendation requirements

Nếu dùng AI:

- Chỉ tạo recommendation dựa trên metrics và evidence đã redact.
- Không tự chạy query, scale service hoặc thay cấu hình.
- Phân biệt observation, hypothesis và recommendation.
- Không gửi raw query/logs tới provider chưa phê duyệt.
- Human review bắt buộc trước mọi thay đổi production.

---

## 37. AI coding agent requirements

AI coding agent phải:

- Không coi missing telemetry là Good.
- Không dùng average thay percentile cho mọi trường hợp.
- Không hiển thị raw query/URL chứa dữ liệu nhạy cảm.
- Không chạy load test production thiếu approval và kill switch.
- Không tự áp dụng optimization recommendation.
- Bổ sung test cho sample size, redaction, regression correlation và capacity forecast.

---

## 38. Acceptance checklist

- [ ] Có Frontend, API, Database và Queue performance.
- [ ] Có Core Web Vitals và performance budgets.
- [ ] Có percentile, sample size và trend.
- [ ] Có infrastructure capacity và headroom.
- [ ] Có storage growth và forecast.
- [ ] Có regression detection và release correlation.
- [ ] Có business-performance signals.
- [ ] Có recommendation với evidence.
- [ ] Có Load Test Center an toàn.
- [ ] Có baselines, alerting và audit.
- [ ] Có loading, empty, error, responsive và accessibility states.
- [ ] Có security, analytics, observability và testing đầy đủ.

---

## 39. Những điều không được làm

- Không coi Unknown là Good.
- Không dùng average duy nhất thay percentile.
- Không hiển thị raw SQL, raw URL hoặc PII dimensions.
- Không kết luận release là nguyên nhân chỉ vì tương quan thời gian.
- Không tự scale hoặc chỉnh cấu hình từ recommendation.
- Không chạy load test production thiếu approval.
- Không dùng dữ liệu thật để tạo thanh toán hoặc thông báo trong load test.
- Không dùng 100% làm safe capacity limit.

---

## 40. Kết luận

Performance & Capacity Center của Cynca VLXD phải kết hợp trải nghiệm người dùng, hiệu năng API, database, queue và tài nguyên hạ tầng để phát hiện bottleneck trước khi ảnh hưởng vận hành. Mọi đánh giá cần dựa trên percentile, sample size, baseline và telemetry đáng tin cậy; mọi load test hoặc thay đổi capacity phải có kiểm soát và audit.

File tiếp theo đề xuất:

```text
README.md
51-Compliance-Privacy-Center.md
```