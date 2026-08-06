# Cynca VLXD Admin Dashboard — Backup & Recovery Center Specification

> Phiên bản: 1.0  
> Phạm vi: Giám sát sao lưu, phục hồi dữ liệu, kiểm thử khôi phục và kế hoạch khắc phục thảm họa  
> Đối tượng sử dụng: Product, Design, Backend, DevOps, SRE, Security, Database, QA và AI coding agent  
> Tài liệu liên quan: `23-Security-Privacy.md`, `24-Observability.md`, `25-Runbooks.md`, `39-Audit-Activity-Center.md`, `47-System-Health-Incident-Center.md`

---

## 1. Mục tiêu

Backup & Recovery Center giúp Cynca VLXD biết dữ liệu nào đang được bảo vệ, bản sao lưu gần nhất có hợp lệ hay không và hệ thống có thể phục hồi trong bao lâu khi xảy ra sự cố.

Hệ thống phải trả lời nhanh:

- Database, object storage và cấu hình quan trọng đã được sao lưu chưa?
- Backup gần nhất thành công khi nào?
- Có bản sao nào lỗi, quá cũ hoặc sắp hết hạn lưu trữ?
- RPO và RTO mục tiêu có đang được đáp ứng?
- Bản sao đã được kiểm thử phục hồi thực tế chưa?
- Có thể phục hồi toàn hệ thống, theo thời điểm hay theo phạm vi dữ liệu nào?
- Ai có quyền yêu cầu và phê duyệt phục hồi?
- Sau khi restore, dữ liệu được xác minh bằng cách nào?

Backup Center không phải giao diện cho phép tải trực tiếp bản sao database hoặc secret về máy cá nhân.

---

## 2. Tài sản cần bảo vệ

```ts
export type BackupAssetType =
  | "database"
  | "object_storage"
  | "configuration"
  | "audit_log"
  | "search_index"
  | "queue_state"
  | "secrets_metadata"
  | "application_artifact";
```

Ưu tiên:

- PostgreSQL production.
- File sản phẩm, chứng từ và attachment.
- Dashboard configuration.
- Audit events.
- Report/import metadata.
- Integration configuration không chứa secret thô.

---

## 3. Permission model

```text
backup.center.view
backup.assets.view
backup.jobs.view
backup.jobs.run
backup.restore.request
backup.restore.approve
backup.restore.execute
backup.restore.verify
backup.retention.view
backup.retention.manage
backup.dr_tests.view
backup.dr_tests.manage
backup.audit.view
```

`backup.restore.execute`, `backup.restore.approve` và `backup.retention.manage` là quyền critical.

---

## 4. Information architecture

```text
Backup & Recovery Center
├── Tổng quan
├── Tài sản được bảo vệ
├── Backup Jobs
├── Recovery Points
├── Restore Requests
├── Restore Tests
├── Disaster Recovery
├── Retention Policies
└── Audit History
```

---

## 5. Backup status model

```ts
export type BackupStatus =
  | "scheduled"
  | "running"
  | "completed"
  | "partially_completed"
  | "failed"
  | "cancelled"
  | "expired"
  | "verification_failed";
```

Không đánh dấu `completed` nếu upload xong nhưng integrity check chưa đạt.

---

## 6. Backup job contract

```ts
export type BackupJob = {
  id: string;
  assetId: string;
  type: "full" | "incremental" | "snapshot" | "transaction_log";
  status: BackupStatus;
  startedAt?: string;
  completedAt?: string;
  sizeBytes?: number;
  storageRegion?: string;
  encryptionStatus: "encrypted" | "unknown";
  checksumStatus: "pending" | "valid" | "invalid";
  retentionUntil?: string;
  initiatedBy: "schedule" | "user" | "system";
  errorCode?: string;
};
```

---

## 7. Dashboard tổng quan

KPI đề xuất:

1. Assets protected.
2. Backup jobs thành công 24 giờ.
3. Failed backups.
4. Recovery points hợp lệ.
5. Assets vượt RPO.
6. Restore tests quá hạn.

Widgets:

- Backup health matrix.
- RPO/RTO compliance.
- Failed jobs.
- Recovery-point age.
- Upcoming retention expiry.
- Recent restore tests.

---

## 8. RPO và RTO

### RPO

Mức dữ liệu tối đa có thể mất.

### RTO

Thời gian tối đa để khôi phục dịch vụ.

```ts
export type RecoveryObjective = {
  assetId: string;
  rpoMinutes: number;
  rtoMinutes: number;
  actualRecoveryPointAgeMinutes?: number;
  lastRestoreDurationMinutes?: number;
  compliant: boolean;
};
```

UI phải hiển thị target, actual và trạng thái vượt ngưỡng.

---

## 9. Backup asset card

Mỗi asset gồm:

- Tên tài sản.
- Environment.
- Owner team.
- Criticality.
- Backup frequency.
- Last successful backup.
- Recovery-point age.
- RPO/RTO.
- Encryption.
- Last restore test.
- Status.

Không chỉ dùng màu để báo compliance.

---

## 10. Recovery point

```ts
export type RecoveryPoint = {
  id: string;
  assetId: string;
  createdAt: string;
  type: "full" | "incremental" | "snapshot" | "point_in_time";
  status: "available" | "verifying" | "corrupted" | "expired";
  sizeBytes?: number;
  checksum?: string;
  encrypted: boolean;
  restorableScopes: string[];
  retentionUntil?: string;
};
```

Không hiển thị raw storage path cho user thông thường.

---

## 11. Point-in-time recovery

Nếu hỗ trợ PITR:

- Chọn timestamp.
- Hiển thị timezone rõ.
- Kiểm tra transaction-log coverage.
- Cảnh báo khoảng thời gian không thể phục hồi.
- Preview target environment.
- Không restore đè production trực tiếp thiếu approval.

---

## 12. Restore request workflow

```text
Chọn asset và recovery point
→ Chọn phạm vi restore
→ Chọn target environment
→ Mô tả lý do
→ Impact preview
→ Approval
→ Dry validation
→ Execute
→ Verify
→ Close
```

Production restore phải có separation of duties nếu tổ chức yêu cầu.

---

## 13. Restore request contract

```ts
export type RestoreRequest = {
  id: string;
  assetId: string;
  recoveryPointId: string;
  scope: "full" | "database" | "schema" | "table" | "object_prefix" | "configuration";
  targetEnvironment: "isolated_test" | "staging" | "production";
  status: "draft" | "awaiting_approval" | "approved" | "running" | "verifying" | "completed" | "failed" | "rejected" | "cancelled";
  reason: string;
  requestedBy: string;
  approvedBy?: string;
  startedAt?: string;
  completedAt?: string;
};
```

---

## 14. Impact preview

Trước restore cần hiển thị:

- Target environment.
- Dữ liệu có thể bị ghi đè.
- Downtime dự kiến.
- Phạm vi user/module bị ảnh hưởng.
- Backup mới nhất trước restore.
- Rollback plan.
- Dependencies cần dừng.
- Verification checklist.

Không chỉ hiển thị nút `Restore` và một confirm dialog đơn giản.

---

## 15. Restore strategies

```text
Restore to isolated environment
Restore to staging
Selective restore
Point-in-time recovery
Full disaster recovery
```

Ưu tiên restore vào isolated environment để kiểm tra trước khi đưa vào production.

---

## 16. Verification

Sau restore phải kiểm tra:

- Checksum/integrity.
- Database connectivity.
- Schema version.
- Record counts hoặc reconciliation.
- Business transaction sample.
- Authentication.
- Orders, inventory và finance read checks.
- Audit continuity.
- File accessibility.

Không coi restore thành công chỉ vì process exit code bằng 0.

---

## 17. Restore test

Restore test định kỳ gồm:

- Asset.
- Recovery point.
- Test environment.
- Restore duration.
- Verification result.
- RTO compliance.
- Issues found.
- Corrective actions.

```ts
export type RestoreTest = {
  id: string;
  assetId: string;
  recoveryPointId: string;
  status: "scheduled" | "running" | "passed" | "failed" | "cancelled";
  startedAt?: string;
  completedAt?: string;
  restoreDurationMinutes?: number;
  verificationSummary?: string;
  issues?: string[];
};
```

---

## 18. Disaster Recovery plan

DR view hiển thị:

- Primary region/site.
- Recovery region/site.
- Replication status.
- Failover readiness.
- DNS/network dependencies.
- Last DR test.
- Runbook version.
- Owners.
- Communication plan.

Không tuyên bố DR-ready nếu chưa có restore/failover test gần đây.

---

## 19. DR exercise

Kịch bản:

- Database unavailable.
- Primary region outage.
- Object storage failure.
- Credential compromise.
- Accidental deletion.
- Ransomware-like data corruption.

Mỗi exercise có scope, giả định, timeline, kết quả và action items.

---

## 20. Retention policy

```ts
export type BackupRetentionPolicy = {
  id: string;
  assetType: BackupAssetType;
  fullBackupRetentionDays: number;
  incrementalRetentionDays?: number;
  monthlyRetentionMonths?: number;
  yearlyRetentionYears?: number;
  immutablePeriodDays?: number;
  regionReplication?: boolean;
};
```

Thay đổi retention cần impact preview, approval và audit.

---

## 21. Immutability và encryption

Yêu cầu:

- Encryption at rest và in transit.
- Backup storage private.
- Immutable/WORM period nếu cần.
- Key-management tách biệt.
- Không lưu secret thô trong backup cấu hình nếu có thể tránh.
- Credential truy cập backup phải rotate.

---

## 22. Failure handling

Các lỗi:

- Source unavailable.
- Storage quota.
- Checksum invalid.
- Encryption failure.
- Retention policy conflict.
- Restore incompatibility.
- Verification failed.

Retry chỉ áp dụng lỗi tạm thời; corruption hoặc schema mismatch cần điều tra.

---

## 23. Notifications

Thông báo khi:

- Backup failed.
- Asset vượt RPO.
- Verification failed.
- Recovery point sắp hết hạn.
- Restore request cần approval.
- Restore completed/failed.
- DR test quá hạn.

Không gửi storage path, secret hoặc payload nhạy cảm trong notification.

---

## 24. Audit events

```text
backup.job.started
backup.job.completed
backup.job.failed
backup.restore.requested
backup.restore.approved
backup.restore.started
backup.restore.completed
backup.restore.failed
backup.retention.updated
backup.dr_test.completed
```

Audit gồm actor, asset, recovery point, target, result, request ID và timestamp.

---

## 25. API endpoints đề xuất

```text
GET  /api/v1/backups/summary
GET  /api/v1/backups/assets
GET  /api/v1/backups/jobs
GET  /api/v1/backups/recovery-points
POST /api/v1/backups/jobs
POST /api/v1/restores/requests
POST /api/v1/restores/requests/:id/approve
POST /api/v1/restores/requests/:id/execute
POST /api/v1/restores/requests/:id/verify
GET  /api/v1/restores/tests
POST /api/v1/restores/tests
GET  /api/v1/backups/retention-policies
PATCH /api/v1/backups/retention-policies/:id
```

---

## 26. Frontend component inventory

```text
BackupRecoveryCenterPage
├── BackupSummaryCards
├── BackupAssetGrid
├── BackupJobTable
├── RecoveryPointTable
├── RpoRtoCompliancePanel
├── RestoreRequestWizard
├── RestoreImpactPreview
├── RestoreProgressPanel
├── RestoreVerificationChecklist
├── RestoreTestTable
├── DrReadinessPanel
├── RetentionPolicyEditor
└── BackupAuditTimeline
```

---

## 27. Loading, empty và error states

- Không hiển thị asset `Protected` khi health service lỗi.
- Empty state phân biệt chưa cấu hình backup với không có lỗi.
- Restore error giữ nguyên evidence và request ID.
- Recovery point corrupted phải bị disable khỏi lựa chọn restore.
- Không hiển thị raw command, secret hoặc storage path.

---

## 28. Responsive và accessibility

- Desktop ưu tiên bảng và wizard rộng.
- Tablet hiển thị card list và drawer.
- Mobile chỉ ưu tiên trạng thái, approval và incident summary; production restore nên yêu cầu desktop.
- Status không chỉ dùng màu.
- Wizard có step labels.
- Progress và verification có text.
- Dialog quản lý focus.

---

## 29. Security requirements

- Backend enforce environment và asset scope.
- Production restore yêu cầu re-authentication.
- Không cho tải raw backup file từ UI.
- Không hiển thị encryption key hoặc secret.
- Restore action dùng allowlist/runbook, không shell tùy ý.
- Mọi restore, retention và DR action phải audit.
- Security incident có thể khóa chức năng restore theo policy.

---

## 30. Testing

### Unit

- RPO/RTO compliance.
- Recovery-point age.
- Retention expiry.
- Restore-state transitions.
- Permission mapping.

### Integration

- Backup completed chỉ sau verification.
- Production restore cần approval.
- Corrupted recovery point không được chọn.
- Restore verification failure không báo completed.
- Cross-environment scope bị chặn.

### E2E

- Xem asset vượt RPO.
- Tạo restore test vào isolated environment.
- Verify kết quả.
- Tạo production restore request.
- Reviewer approve.
- Theo dõi restore và close.
- Cập nhật retention policy có impact preview.

---

## 31. AI coding agent requirements

AI coding agent phải:

- Không cho tải raw backup file hoặc secret.
- Không đánh dấu backup thành công trước checksum/integrity verification.
- Không restore production thiếu approval, re-authentication và impact preview.
- Không coi process exit code là verification đầy đủ.
- Không tuyên bố DR-ready nếu chưa có test.
- Bổ sung test cho corrupted backup, RPO breach, restore verification và cross-environment access.

---

## 32. Acceptance checklist

- [ ] Có Backup Asset Inventory.
- [ ] Có backup job và recovery point model.
- [ ] Có RPO/RTO compliance.
- [ ] Có restore workflow nhiều bước.
- [ ] Có impact preview và approval.
- [ ] Có point-in-time và selective restore nếu backend hỗ trợ.
- [ ] Có verification checklist.
- [ ] Có restore test và DR exercise.
- [ ] Có retention, encryption và immutability.
- [ ] Có notifications và audit.
- [ ] Có loading, empty, error, responsive và accessibility states.
- [ ] Có security, testing và Visual QA đầy đủ.

---

## 33. Những điều không được làm

- Không cho tải raw backup file từ Dashboard.
- Không restore đè production bằng một click.
- Không hiển thị completed trước integrity check.
- Không dùng recovery point corrupted.
- Không thay retention thiếu approval và impact preview.
- Không lưu secret thô trong backup nếu tránh được.
- Không tuyên bố DR-ready chỉ vì backup job chạy thành công.
- Không chạy shell tùy ý từ giao diện.

---

## 34. Kết luận

Backup & Recovery Center của Cynca VLXD phải chứng minh được rằng dữ liệu không chỉ được sao lưu mà còn có thể phục hồi đúng RPO/RTO, đúng phạm vi và được xác minh bằng nghiệp vụ thực tế. Mọi restore production phải được kiểm soát, audit, phê duyệt và có kế hoạch rollback rõ ràng.

File tiếp theo:

```text
49-Data-Governance-Quality-Center.md
```