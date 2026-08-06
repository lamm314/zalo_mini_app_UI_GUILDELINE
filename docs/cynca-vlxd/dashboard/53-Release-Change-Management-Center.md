# Cynca VLXD Admin Dashboard — Release & Change Management Center

> Phiên bản: 1.0  
> Phạm vi: Quản lý đề xuất thay đổi, lịch phát hành, phê duyệt, triển khai và đánh giá sau phát hành

---

## 1. Mục tiêu

Release & Change Management Center giúp đội ngũ Cynca VLXD quản lý thay đổi hệ thống theo quy trình rõ ràng từ khi đề xuất đến khi hoàn tất đánh giá sau triển khai.

Màn hình phải giúp người dùng biết:

- Thay đổi nào đang chờ xem xét hoặc phê duyệt.
- Phiên bản nào sắp phát hành.
- Module, chi nhánh hoặc nhóm người dùng nào bị ảnh hưởng.
- Các bước kiểm thử bắt buộc đã hoàn tất chưa.
- Đợt triển khai đang ở giai đoạn nào.
- Có cần tạm dừng, tiếp tục hoặc quay về phiên bản ổn định trước đó không.
- Có phát sinh lỗi hoặc suy giảm hiệu năng sau phát hành không.

---

## 2. Cấu trúc trang

```text
Release & Change Management
├── Tổng quan
├── Yêu cầu thay đổi
├── Lịch phát hành
├── Phiên bản chờ phát hành
├── Phê duyệt
├── Tiến độ triển khai
├── Phát hành theo giai đoạn
├── Thay đổi cơ sở dữ liệu
├── Khôi phục phiên bản
├── Đánh giá sau phát hành
└── Lịch sử thay đổi
```

---

## 3. Nhóm quyền

```text
changes.center.view
changes.create
changes.update
changes.review
changes.approve
changes.schedule
releases.view
releases.create
releases.deploy
releases.pause
releases.rollback
feature_rollouts.manage
migrations.view
migrations.approve
post_release_reviews.manage
```

Quyền triển khai, quay về phiên bản trước và phê duyệt thay đổi dữ liệu phải được kiểm soát riêng.

---

## 4. Loại thay đổi

```ts
export type ChangeType =
  | "standard"
  | "normal"
  | "emergency"
  | "configuration"
  | "infrastructure"
  | "database"
  | "security"
  | "feature_flag";
```

- Standard: thay đổi lặp lại, rủi ro thấp, đã có quy trình chuẩn.
- Normal: thay đổi thông thường cần review và phê duyệt.
- Emergency: thay đổi khẩn cấp, quy trình rút gọn nhưng phải có đánh giá sau đó.

---

## 5. Change Request contract

```ts
export type ChangeRequest = {
  id: string;
  title: string;
  type: ChangeType;
  status: "draft" | "review" | "awaiting_approval" | "approved" | "scheduled" | "implementing" | "verifying" | "completed" | "failed" | "rolled_back" | "cancelled";
  environment: "development" | "staging" | "production";
  affectedModules: string[];
  riskLevel: "low" | "medium" | "high" | "critical";
  ownerUserId: string;
  requestedAt: string;
  scheduledStartAt?: string;
  scheduledEndAt?: string;
  rollbackPlan?: string;
  verificationPlan?: string;
};
```

---

## 6. Tổng quan

KPI đề xuất:

1. Thay đổi đang chờ phê duyệt.
2. Phiên bản dự kiến phát hành trong 7 ngày.
3. Thay đổi rủi ro cao.
4. Đợt triển khai đang chạy.
5. Thay đổi thất bại trong 30 ngày.
6. Tỷ lệ thay đổi thành công.

Widgets:

- Lịch phát hành gần nhất.
- Hàng đợi phê duyệt.
- Đợt triển khai đang hoạt động.
- Cảnh báo trùng lịch.
- Thay đổi thất bại hoặc đã quay lại phiên bản cũ.
- Đánh giá sau phát hành sắp đến hạn.

---

## 7. Quy trình thay đổi

```text
Bản nháp
→ Review kỹ thuật
→ Đánh giá rủi ro
→ Kiểm thử
→ Phê duyệt
→ Xếp lịch
→ Triển khai
→ Xác minh
→ Hoàn tất
```

Thay đổi khẩn cấp:

```text
Xác nhận tình huống khẩn cấp
→ Phê duyệt tối thiểu
→ Triển khai
→ Xác minh
→ Đánh giá bắt buộc sau triển khai
```

---

## 8. Đánh giá rủi ro

Các yếu tố:

- Ảnh hưởng người dùng.
- Ảnh hưởng dữ liệu.
- Thời gian gián đoạn.
- Ảnh hưởng bảo mật.
- Độ khó khi quay lại phiên bản cũ.
- Phụ thuộc hệ thống.
- Rủi ro thay đổi cấu trúc dữ liệu.
- Thời điểm kinh doanh cao điểm.

Không dùng một điểm tổng hợp duy nhất mà thiếu giải thích.

---

## 9. Các kiểm tra bắt buộc

Có thể gồm:

- Review mã nguồn.
- Unit test.
- Integration test.
- E2E test.
- Visual regression.
- Accessibility test.
- Security review.
- Performance comparison.
- Kiểm tra migration.
- Diễn tập khôi phục phiên bản.

Phiên bản không được đánh dấu sẵn sàng nếu bước bắt buộc còn lỗi hoặc chưa có kết quả.

---

## 10. Release Candidate

```ts
export type ReleaseCandidate = {
  id: string;
  version: string;
  commitSha?: string;
  artifactIds: string[];
  changeRequestIds: string[];
  environment: "staging" | "production";
  status: "building" | "testing" | "ready" | "awaiting_approval" | "scheduled" | "deploying" | "deployed" | "failed" | "rolled_back";
  createdAt: string;
  createdBy: string;
};
```

---

## 11. Lịch phát hành

Mỗi lịch gồm:

- Tên release hoặc change.
- Environment.
- Owner.
- Mức rủi ro.
- Thời gian bắt đầu và kết thúc.
- Module bị ảnh hưởng.
- Cửa sổ bảo trì.
- Khoảng thời gian không nên triển khai.

Cảnh báo khi nhiều thay đổi cùng tác động một module hoặc hệ thống dữ liệu.

---

## 12. Khoảng thời gian hạn chế triển khai

Ví dụ:

- Chương trình bán hàng lớn.
- Thời gian chốt kỳ tài chính.
- Đợt kiểm kê kho.
- Sự kiện quan trọng.
- Thời gian thiếu nhân sự hỗ trợ.

Việc vượt qua giới hạn phải có lý do, phê duyệt và lịch sử rõ ràng.

---

## 13. Ma trận phê duyệt

Phê duyệt phụ thuộc:

- Môi trường triển khai.
- Mức rủi ro.
- Loại thay đổi.
- Ảnh hưởng dữ liệu.
- Ảnh hưởng bảo mật.
- Thời gian gián đoạn.

Người tạo thay đổi không nên tự phê duyệt thay đổi rủi ro cao nếu chính sách yêu cầu tách nhiệm vụ.

---

## 14. Chiến lược triển khai

```text
Rolling
Blue/Green
Canary
Feature Flag Rollout
Manual Controlled Rollout
```

UI phải hiển thị:

- Chiến lược.
- Các giai đoạn.
- Điều kiện sức khỏe.
- Tiêu chí tạm dừng.
- Phương án quay lại.

---

## 15. Phát hành theo giai đoạn

Ví dụ:

```text
Người dùng nội bộ
5%
25%
50%
100%
```

Mỗi giai đoạn có:

- Nhóm người dùng.
- Thời gian theo dõi.
- Tiêu chí thành công.
- Metrics.
- Nút tạm dừng, tiếp tục hoặc quay lại.

Không tự chuyển sang giai đoạn tiếp theo khi trạng thái kiểm tra chưa rõ.

---

## 16. Tiến độ triển khai

Hiển thị:

- Giai đoạn hiện tại.
- Số instance đã cập nhật.
- Health checks.
- Error rate.
- Latency.
- Kiểm tra nghiệp vụ chính.
- Thời điểm bắt đầu.
- Người thực hiện.

Không tạo tiến độ giả khi hệ thống triển khai chưa trả dữ liệu.

---

## 17. Health Gates

Ví dụ:

- Error rate không vượt baseline.
- API p95 trong ngưỡng.
- Tạo đơn hoạt động ổn định.
- Inventory sync không bị stale.
- Không có cảnh báo nghiêm trọng.
- Migration không lỗi.

Mỗi gate có target, actual, time window và trạng thái.

---

## 18. Feature Rollout

Hỗ trợ theo:

- Người dùng nội bộ.
- Role.
- Chi nhánh.
- Tỷ lệ phần trăm.
- Thời gian định trước.

Feature flag không được dùng thay cho permission.

Mỗi rollout có owner, ngày review và cơ chế tắt nhanh.

---

## 19. Database Migration

Mỗi migration cần:

- Mã migration.
- Schema version.
- Tính tương thích.
- Thời gian ước tính.
- Rủi ro khóa dữ liệu.
- Kế hoạch backfill.
- Kế hoạch phục hồi.
- Kiểm tra sau migration.

Giao diện chỉ gọi quy trình migration đã được đăng ký và phê duyệt.

---

## 20. An toàn migration

- Thử nghiệm tại staging.
- Có recovery point trước migration.
- Kiểm tra tương thích với phiên bản ứng dụng trước và sau.
- Có giới hạn thời gian và lock.
- Có checkpoint.
- Có tiêu chí dừng.
- Có xác minh sau hoàn tất.

---

## 21. Quy trình quay lại phiên bản trước

```text
Phát hiện vấn đề
→ Tạm dừng rollout
→ Đánh giá ảnh hưởng
→ Phê duyệt nếu cần
→ Quay lại phiên bản trước
→ Xác minh dịch vụ và dữ liệu
→ Thông báo
→ Mở đánh giá sau phát hành
```

Không quay lại tự động khi dữ liệu không còn tương thích với phiên bản trước.

---

## 22. Rollback Request

```ts
export type RollbackRequest = {
  id: string;
  releaseId: string;
  reason: string;
  targetVersion: string;
  status: "draft" | "approved" | "running" | "verifying" | "completed" | "failed";
  requestedBy: string;
  approvedBy?: string;
  startedAt?: string;
  completedAt?: string;
};
```

---

## 23. Đánh giá sau phát hành

Nội dung:

- Mục tiêu thay đổi.
- Kết quả thực tế.
- Incident hoặc regression.
- Ảnh hưởng người dùng.
- Metrics trước và sau.
- Việc quay lại phiên bản cũ nếu có.
- Bài học.
- Action items.

Bắt buộc với thay đổi khẩn cấp, thất bại hoặc phải quay lại phiên bản trước.

---

## 24. Chỉ số quản lý thay đổi

- Tần suất triển khai.
- Lead time for changes.
- Change failure rate.
- Mean time to restore.
- Tỷ lệ thay đổi khẩn cấp.
- Tỷ lệ rollback.
- Thời gian chờ phê duyệt.

Không dùng các chỉ số này để đánh giá cá nhân thiếu ngữ cảnh.

---

## 25. API endpoints đề xuất

```text
GET  /api/v1/changes/summary
GET  /api/v1/changes
POST /api/v1/changes
PATCH /api/v1/changes/:id
POST /api/v1/changes/:id/submit-review
POST /api/v1/changes/:id/approve
POST /api/v1/changes/:id/schedule
GET  /api/v1/releases
POST /api/v1/releases
POST /api/v1/releases/:id/deploy
POST /api/v1/releases/:id/pause
POST /api/v1/releases/:id/continue
POST /api/v1/releases/:id/rollback
GET  /api/v1/release-calendar
GET  /api/v1/post-release-reviews
```

---

## 26. Frontend component inventory

```text
ReleaseChangeManagementCenterPage
├── ChangeSummaryCards
├── ChangeRequestTable
├── ChangeRequestWizard
├── RiskAssessmentPanel
├── ApprovalMatrix
├── ReleaseCalendar
├── ReleaseCandidatePanel
├── DeploymentProgress
├── HealthGatePanel
├── CanaryRolloutPanel
├── MigrationReviewPanel
├── RollbackDialog
└── PostReleaseReviewForm
```

---

## 27. Loading, empty và error states

- Trạng thái pipeline lỗi phải hiển thị `Unknown`, không phải `Ready`.
- Triển khai lỗi không được báo thành công.
- Khi chưa kiểm tra được conflict lịch, phải cảnh báo rõ.
- Rollback lỗi giữ lại request ID và liên kết incident.
- Không hiển thị thông tin xác thực triển khai trong UI.

---

## 28. Security và Audit

- Backend kiểm tra môi trường và quyền hành động.
- Production deploy hoặc rollback có thể yêu cầu xác thực lại.
- Artifact cần metadata về nguồn và tính toàn vẹn.
- Audit mọi phê duyệt, xếp lịch, triển khai, tạm dừng, tiếp tục và rollback.

Events:

```text
change.created
change.approved
change.scheduled
release.created
release.deployment.started
release.deployment.paused
release.deployed
release.rollback.started
release.rollback.completed
post_release_review.completed
```

---

## 29. Testing

### Unit

- Risk mapping.
- Approval routing.
- Calendar conflict detection.
- Health-gate evaluation.
- Rollout-stage transitions.

### Integration

- Required check lỗi chặn release.
- Blackout override cần approval.
- Canary có gate unknown không tự tiếp tục.
- Rollback chỉ hoàn tất sau verification.
- Migration không tương thích hiển thị cảnh báo nghiêm trọng.

### E2E

- Tạo normal change.
- Review và phê duyệt.
- Xếp lịch production release.
- Rollout nhóm nội bộ rồi 5%.
- Tạm dừng khi có regression.
- Rollback.
- Tạo post-release review.
- Hoàn tất retrospective cho emergency change.

---

## 30. AI coding agent requirements

AI coding agent phải:

- Không coi trạng thái kiểm thử chưa rõ là sẵn sàng.
- Không bỏ qua approval hoặc blackout period.
- Không tự tiếp tục rollout khi health gate chưa đạt.
- Không thực hiện migration ngoài registry được phê duyệt.
- Không rollback thiếu kiểm tra tương thích dữ liệu.
- Bổ sung test cho required checks, approval, rollout pause và rollback.

---

## 31. Acceptance checklist

- [ ] Có Change Request workflow.
- [ ] Có risk assessment và required checks.
- [ ] Có Release Candidate và Release Calendar.
- [ ] Có blackout periods và conflict detection.
- [ ] Có approval matrix và separation of duties.
- [ ] Có deployment strategies và staged rollout.
- [ ] Có health gates.
- [ ] Có feature rollout và cơ chế tắt nhanh.
- [ ] Có migration safety.
- [ ] Có rollback workflow.
- [ ] Có post-release review và metrics.
- [ ] Có permission, audit, responsive và accessibility.
- [ ] Có testing và Visual QA đầy đủ.

---

## 32. Những điều không được làm

- Không triển khai khi kiểm tra bắt buộc thất bại hoặc chưa rõ.
- Không bỏ qua phê duyệt thiếu audit.
- Không tự tiếp tục rollout khi health gate chưa đạt.
- Không thực hiện migration tùy ý ngoài quy trình.
- Không rollback khi chưa kiểm tra tương thích dữ liệu.
- Không dùng feature flag thay authorization.
- Không bỏ post-release review cho emergency hoặc failed change.

---

## 33. Kết luận

Release & Change Management Center của Cynca VLXD phải giúp mọi thay đổi production có owner, risk assessment, kiểm thử, phê duyệt, lịch triển khai, health gate và phương án quay lại rõ ràng. Hệ thống cần cân bằng giữa kiểm soát rủi ro và tốc độ phát hành.

File tiếp theo đề xuất:

```text
54-Business-Continuity-Center.md
```