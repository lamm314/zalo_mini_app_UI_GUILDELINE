# Cynca VLXD Admin Dashboard — Data Governance & Quality Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản trị dữ liệu, chất lượng dữ liệu, ownership, lineage và xử lý vấn đề dữ liệu  
> Đối tượng sử dụng: Product, Data, Backend, Finance, Operations, Security, Compliance, QA và AI coding agent  
> Tài liệu liên quan: `16-Data-Contracts.md`, `23-Security-Privacy.md`, `39-Audit-Activity-Center.md`, `45-Data-Import-Center.md`, `48-Backup-Recovery-Center.md`

---

## 1. Mục tiêu

Data Governance & Quality Center giúp Cynca VLXD xác định dữ liệu nào là nguồn chính thức, ai chịu trách nhiệm, dữ liệu đang tốt hay lỗi và vấn đề dữ liệu ảnh hưởng tới module nào.

Hệ thống phải trả lời nhanh:

- Dataset nào là source of truth?
- Ai là Data Owner và Data Steward?
- Chất lượng dữ liệu theo từng domain đang ở mức nào?
- Rule nào đang vi phạm và bao nhiêu bản ghi bị ảnh hưởng?
- Dữ liệu đến từ đâu, qua những bước biến đổi nào?
- PII, dữ liệu tài chính và dữ liệu nhạy cảm đang được phân loại ra sao?
- Vấn đề nào cần sửa nguồn, sửa mapping hay chạy backfill?
- Sau khi sửa, chất lượng đã được xác minh chưa?

---

## 2. Data domains

```ts
export type DataDomain =
  | "products"
  | "orders"
  | "customers"
  | "inventory"
  | "warehouse"
  | "finance"
  | "support"
  | "users"
  | "reports"
  | "integrations";
```

Mỗi domain cần owner, steward, schema, quality rules và retention policy.

---

## 3. Permission model

```text
data_governance.view
data_catalog.view
data_catalog.manage
data_quality.view
data_quality.rules.manage
data_quality.issues.manage
data_lineage.view
data_classification.view
data_classification.manage
data_stewardship.assign
data_backfill.request
data_backfill.approve
data_audit.view
```

Không dựa duy nhất vào role name.

---

## 4. Information architecture

```text
Data Governance & Quality Center
├── Tổng quan
├── Data Catalog
├── Quality Scorecards
├── Quality Rules
├── Data Issues
├── Lineage
├── Classification
├── Ownership & Stewardship
├── Backfill / Remediation
└── Audit History
```

---

## 5. Dataset contract

```ts
export type GovernedDataset = {
  id: string;
  key: string;
  name: string;
  domain: DataDomain;
  description?: string;
  sourceOfTruth: boolean;
  ownerUserId?: string;
  stewardUserId?: string;
  systemOfRecord?: string;
  sensitivity: "public" | "internal" | "confidential" | "restricted";
  freshnessSlaMinutes?: number;
  retentionPolicyId?: string;
  schemaVersion?: number;
  qualityScore?: number;
  status: "active" | "deprecated" | "archived";
};
```

---

## 6. Trang tổng quan

KPI đề xuất:

1. Datasets active.
2. Quality rules passing.
3. Critical data issues.
4. Datasets stale.
5. Unassigned owners.
6. Restricted datasets.

Widgets:

- Quality by domain.
- Critical issues.
- Freshness breaches.
- Schema changes.
- Ownership gaps.
- Recent remediation.

---

## 7. Data Catalog

Mỗi dataset card hoặc row gồm:

- Tên dataset.
- Domain.
- Source of truth.
- Owner/steward.
- Sensitivity.
- Freshness SLA.
- Quality score.
- Schema version.
- Last updated.
- Status.

Search theo dataset, field, domain và owner.

---

## 8. Field catalog

```ts
export type DataFieldDefinition = {
  key: string;
  name: string;
  dataType: string;
  nullable: boolean;
  description?: string;
  classification?: "none" | "pii" | "financial" | "credential" | "business_sensitive";
  maskingPolicy?: string;
  allowedValues?: string[];
  sourceField?: string;
  deprecated?: boolean;
};
```

Không hiển thị sample value nhạy cảm mặc định.

---

## 9. Data quality dimensions

```text
Completeness
Accuracy
Validity
Uniqueness
Consistency
Timeliness
Referential Integrity
```

Mỗi score phải có công thức, phạm vi và thời điểm đo.

Không dùng một quality score tổng hợp mà thiếu drill-down.

---

## 10. Quality rule contract

```ts
export type DataQualityRule = {
  id: string;
  datasetId: string;
  key: string;
  name: string;
  dimension: "completeness" | "accuracy" | "validity" | "uniqueness" | "consistency" | "timeliness" | "referential_integrity";
  severity: "critical" | "high" | "medium" | "low";
  ruleType: "not_null" | "unique" | "range" | "enum" | "regex" | "reference" | "freshness" | "custom_registered";
  threshold: number;
  enabled: boolean;
  ownerUserId?: string;
  lastRunAt?: string;
  lastResult?: "passed" | "warning" | "failed" | "unknown";
};
```

Không cho nhập SQL hoặc code tùy ý từ UI. Custom rule phải map tới registry backend.

---

## 11. Quality scorecard

Hiển thị:

- Overall score.
- Score theo dimension.
- Trend.
- Failed rules.
- Affected records.
- Freshness.
- Last run.
- Owner.

Scorecard phải phân biệt `unknown` với `passed`.

---

## 12. Rule examples

### Products

- SKU không trống và unique.
- Category tồn tại.
- Unit hợp lệ.
- Giá không âm.

### Orders

- Status transition hợp lệ.
- Tổng tiền bằng tổng dòng hàng theo rule.
- Branch tồn tại.
- Customer reference hợp lệ.

### Inventory

- Warehouse thuộc branch.
- Available quantity nhất quán với reservation policy.
- Không tồn âm nếu policy cấm.

### Finance

- Payment reference unique.
- Reconciliation difference trong tolerance.
- Closed period không thay đổi thiếu restatement.

---

## 13. Data issue model

```ts
export type DataQualityIssue = {
  id: string;
  datasetId: string;
  ruleId?: string;
  title: string;
  severity: "critical" | "high" | "medium" | "low";
  status: "open" | "investigating" | "fix_in_progress" | "verifying" | "resolved" | "accepted_risk";
  affectedRecordCount?: number;
  affectedModules?: string[];
  ownerUserId?: string;
  detectedAt: string;
  dueAt?: string;
  rootCauseCategory?: string;
  remediationType?: string;
};
```

---

## 14. Data issue workflow

```text
Detected
→ Triage
→ Assign owner
→ Identify root cause
→ Choose remediation
→ Fix source/mapping/data
→ Re-run validation
→ Verify downstream
→ Resolve
```

Không đóng issue chỉ vì số lỗi tạm thời giảm mà chưa xác minh nguyên nhân.

---

## 15. Root cause categories

```text
Source system
Manual input
Import mapping
Integration mapping
Schema change
Transformation bug
Reference data
Duplicate processing
Late-arriving data
Unknown
```

---

## 16. Remediation strategies

- Fix at source.
- Update validation rule.
- Correct mapping.
- Backfill data.
- Deduplicate.
- Reconcile.
- Quarantine records.
- Accept risk có thời hạn.

Ưu tiên sửa tại nguồn thay vì sửa lặp ở từng báo cáo.

---

## 17. Data lineage

Lineage hiển thị:

```text
Source system
→ Ingestion
→ Transformation
→ Operational database
→ Reporting dataset
→ Dashboard widget/export
```

Mỗi node có owner, schema version, freshness và status.

Có list alternative cho accessibility.

---

## 18. Lineage impact analysis

Khi schema/rule thay đổi, hiển thị:

- Downstream datasets.
- APIs.
- Reports.
- Dashboard widgets.
- Imports/exports.
- Integrations.
- Owners cần thông báo.

Không publish breaking schema change thiếu impact review.

---

## 19. Schema change management

Statuses:

```text
Draft
Review
Approved
Scheduled
Applied
Rolled Back
```

Yêu cầu:

- Versioning.
- Compatibility classification.
- Migration plan.
- Backfill plan.
- Validation.
- Downstream notification.
- Audit.

---

## 20. Data classification

```text
Public
Internal
Confidential
Restricted
```

Field tags:

```text
PII
Financial
Credential
Legal
Operational sensitive
```

Classification ảnh hưởng masking, export, retention và audit.

---

## 21. Masking policy

Ví dụ:

- Phone: chỉ hiện 4 số cuối.
- Email: mask phần local.
- Address: rút gọn.
- Payment reference: mask.
- Secret: không trả về client.

Masking phải được backend enforce, không chỉ CSS/frontend.

---

## 22. Ownership và stewardship

### Data Owner

- Chịu trách nhiệm định nghĩa và quyền sử dụng.

### Data Steward

- Theo dõi chất lượng, metadata và issue hằng ngày.

### Technical Owner

- Chịu trách nhiệm pipeline, schema và vận hành.

Dataset critical không nên thiếu owner.

---

## 23. Reference data management

Reference data gồm:

- Order statuses.
- Units.
- Categories.
- Branch/warehouse codes.
- Payment methods.
- Complaint taxonomy.

Thay đổi cần version, effective date và impact preview.

---

## 24. Freshness monitoring

Freshness states:

```text
Fresh
Near SLA
Breached
Unknown
```

Không coi không có thay đổi nghiệp vụ là stale; dùng heartbeat/checkpoint phù hợp.

---

## 25. Duplicate management

Hiển thị:

- Duplicate key.
- Candidate records.
- Confidence.
- Source.
- Merge policy.
- Owner.

Không tự merge customer hoặc finance record thiếu review.

---

## 26. Backfill request

```ts
export type DataBackfillRequest = {
  id: string;
  datasetId: string;
  dateFrom?: string;
  dateTo?: string;
  filterSummary?: string[];
  estimatedRecords?: number;
  reason: string;
  status: "draft" | "awaiting_approval" | "approved" | "queued" | "running" | "verifying" | "completed" | "failed";
  requestedBy: string;
  approvedBy?: string;
};
```

Backfill phải có idempotency, impact preview và verification.

---

## 27. Quarantine

Record vi phạm critical rule có thể được đưa vào quarantine khi nghiệp vụ cho phép.

UI hiển thị:

- Record reference đã mask.
- Reason.
- Source.
- Detected time.
- Downstream blocked state.
- Resolution action.

Không quarantine âm thầm dữ liệu ảnh hưởng đơn hàng mà thiếu alert.

---

## 28. Audit events

```text
data.dataset.created
data.dataset.owner_updated
data.quality_rule.created
data.quality_rule.updated
data.issue.assigned
data.issue.resolved
data.schema_change.approved
data.classification.updated
data.backfill.requested
data.backfill.completed
```

Không ghi full record content trong audit.

---

## 29. API endpoints đề xuất

```text
GET  /api/v1/data-governance/summary
GET  /api/v1/data-governance/datasets
GET  /api/v1/data-governance/datasets/:id
GET  /api/v1/data-quality/scorecards
GET  /api/v1/data-quality/rules
POST /api/v1/data-quality/rules
PATCH /api/v1/data-quality/rules/:id
GET  /api/v1/data-quality/issues
PATCH /api/v1/data-quality/issues/:id
GET  /api/v1/data-lineage/:datasetId
POST /api/v1/data-backfills
POST /api/v1/data-backfills/:id/approve
```

---

## 30. Frontend component inventory

```text
DataGovernanceQualityCenterPage
├── GovernanceSummaryCards
├── DatasetCatalogTable
├── DatasetDetailPage
├── QualityScorecard
├── QualityRuleTable
├── DataIssueQueue
├── DataIssueDrawer
├── LineageGraph
├── LineageListAlternative
├── ClassificationPanel
├── OwnershipEditor
├── BackfillRequestWizard
└── SchemaChangeImpactPanel
```

---

## 31. Loading, empty và error states

- Quality service lỗi phải hiển thị Unknown, không Passed.
- Dataset thiếu owner phải có warning.
- Lineage lỗi không làm mất dataset metadata.
- Empty issue list chỉ được coi tích cực khi rule engine đang hoạt động.
- Không hiển thị sample PII trong error state.

---

## 32. Responsive và accessibility

- Desktop dùng bảng, scorecard và lineage graph.
- Tablet chuyển graph thành list khi cần.
- Mobile ưu tiên issues, score và owner; quản lý rule/schema phức tạp nên dùng desktop.
- Status không chỉ dùng màu.
- Graph có semantic alternative.
- Tables có caption và headers.

---

## 33. Security và privacy

- Backend enforce domain, field và classification permission.
- Không trả sample restricted data cho user thiếu quyền.
- Không cho custom SQL/code trong rule editor.
- Backfill và merge nhạy cảm cần approval.
- Export metadata không được chứa PII values.
- Audit mọi classification, owner và schema change.

---

## 34. Testing

### Unit

- Quality-score calculation.
- Freshness status.
- Rule threshold.
- Classification mapping.
- Impact traversal.

### Integration

- Failed rule tạo issue.
- Verification pass mới resolve issue.
- Schema change hiển thị downstream impact.
- Backfill cần approval và idempotency.
- Masking theo permission.

### E2E

- Mở dataset Orders.
- Xem failed rules.
- Assign issue.
- Tạo remediation/backfill.
- Verify kết quả.
- Xem lineage tới Dashboard report.
- Viewer thiếu quyền không thấy restricted fields.

---

## 35. AI-assisted quality requirements

Nếu dùng AI:

- Chỉ gợi ý mapping, root-cause hypothesis hoặc rule draft.
- Không tự sửa dữ liệu.
- Không gửi restricted sample data tới provider chưa phê duyệt.
- Phân biệt fact và hypothesis.
- Người dùng phải review trước khi tạo rule/backfill.

---

## 36. AI coding agent requirements

AI coding agent phải:

- Không dùng một quality score thiếu drill-down.
- Không coi rule engine lỗi là dữ liệu tốt.
- Không cho custom SQL hoặc script tùy ý.
- Không tự merge hoặc backfill dữ liệu nhạy cảm.
- Không hiển thị PII sample ngoài permission.
- Bổ sung test cho masking, lineage impact, freshness và verification.

---

## 37. Acceptance checklist

- [ ] Có Data Catalog và source-of-truth flag.
- [ ] Có owner, steward và technical owner.
- [ ] Có quality dimensions, rules và scorecards.
- [ ] Có Data Issue workflow.
- [ ] Có lineage và impact analysis.
- [ ] Có schema-change management.
- [ ] Có classification và masking.
- [ ] Có reference-data management.
- [ ] Có freshness, duplicate và quarantine handling.
- [ ] Có backfill workflow.
- [ ] Có permission, audit, responsive và accessibility.
- [ ] Có testing và Visual QA đầy đủ.

---

## 38. Những điều không được làm

- Không coi Unknown là Passed.
- Không nhập SQL hoặc code tùy ý làm quality rule.
- Không sửa dữ liệu downstream thay vì sửa nguồn thiếu lý do.
- Không merge customer/finance record tự động thiếu review.
- Không backfill thiếu approval, idempotency và verification.
- Không hiển thị PII sample ngoài quyền.
- Không publish breaking schema change thiếu impact analysis.
- Không đóng issue trước khi re-run validation.

---

## 39. Kết luận

Data Governance & Quality Center của Cynca VLXD phải giúp tổ chức biết dữ liệu nào đáng tin cậy, ai chịu trách nhiệm và lỗi cần được sửa tại đâu. Chất lượng dữ liệu phải được đo bằng rule minh bạch, có lineage, ownership, remediation và verification thay vì chỉ hiển thị một điểm số tổng hợp.

File tiếp theo:

```text
50-Performance-Capacity-Center.md
```