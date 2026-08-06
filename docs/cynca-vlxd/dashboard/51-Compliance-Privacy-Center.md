# Cynca VLXD Admin Dashboard — Compliance & Privacy Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý nghĩa vụ tuân thủ, quyền riêng tư, yêu cầu của chủ thể dữ liệu và kiểm soát dữ liệu nhạy cảm  
> Đối tượng sử dụng: Product, Legal, Compliance, Security, Data, Operations, QA và AI coding agent

---

## 1. Mục tiêu

Compliance & Privacy Center giúp Cynca VLXD quản lý chính sách dữ liệu, bằng chứng tuân thủ, yêu cầu truy cập/xóa dữ liệu và các rủi ro liên quan đến quyền riêng tư.

Hệ thống phải trả lời:

- Dữ liệu cá nhân nào đang được thu thập và dùng cho mục đích gì?
- Cơ sở xử lý và thời hạn lưu trữ là gì?
- Có yêu cầu truy cập, chỉnh sửa, xóa hoặc hạn chế xử lý nào đang chờ?
- Có hệ thống, export hoặc integration nào làm lộ dữ liệu vượt quyền?
- Chính sách nào sắp hết hạn review?
- Sự cố dữ liệu nào cần đánh giá hoặc thông báo?

---

## 2. Information architecture

```text
Compliance & Privacy Center
├── Tổng quan
├── Data Processing Register
├── Data Subject Requests
├── Consent & Preferences
├── Retention & Deletion
├── Privacy Risk Assessments
├── Third-party Processors
├── Policy & Evidence
├── Data Incidents
└── Audit History
```

---

## 3. Permission model

```text
compliance.center.view
privacy.processing_register.view
privacy.processing_register.manage
privacy.requests.view
privacy.requests.manage
privacy.consent.view
privacy.retention.manage
privacy.risk_assessments.manage
privacy.third_parties.view
privacy.third_parties.manage
privacy.incidents.view
privacy.incidents.manage
privacy.evidence.export
```

Không dựa duy nhất vào role name.

---

## 4. Processing activity contract

```ts
export type ProcessingActivity = {
  id: string;
  name: string;
  purpose: string;
  lawfulBasis?: string;
  dataCategories: string[];
  subjectCategories: string[];
  systems: string[];
  recipients?: string[];
  retentionPolicyId?: string;
  ownerUserId: string;
  status: "draft" | "active" | "under_review" | "retired";
  lastReviewedAt?: string;
  nextReviewAt?: string;
};
```

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Processing activities active.
2. Data subject requests đang mở.
3. Requests gần/quá hạn.
4. Retention jobs lỗi.
5. Privacy risks high/critical.
6. Third-party reviews sắp hết hạn.

Widgets:

- Request queue.
- High-risk processing activities.
- Policy review calendar.
- Retention exceptions.
- Recent privacy incidents.

---

## 6. Data Subject Request workflow

Các loại yêu cầu:

```text
Access
Correction
Deletion
Restriction
Objection
Data portability
Consent withdrawal
```

Luồng:

```text
Request received
→ Identity verification
→ Scope discovery
→ Legal/operational review
→ Fulfillment
→ Quality check
→ Response sent
→ Closed
```

Không thực hiện xóa dữ liệu trước khi xác minh danh tính và đánh giá nghĩa vụ lưu trữ.

---

## 7. Request contract

```ts
export type PrivacyRequest = {
  id: string;
  type: "access" | "correction" | "deletion" | "restriction" | "objection" | "portability" | "withdrawal";
  status: "received" | "verification" | "in_review" | "fulfilling" | "ready_for_response" | "completed" | "rejected" | "cancelled";
  subjectReference: string;
  receivedAt: string;
  dueAt: string;
  assignedUserId?: string;
  verificationStatus: "pending" | "verified" | "failed";
  affectedSystems?: string[];
  legalHold?: boolean;
};
```

---

## 8. Identity verification

- Chỉ thu thập thông tin cần thiết.
- Không hiển thị giấy tờ đầy đủ cho người thiếu quyền.
- Verification evidence có expiry và retention riêng.
- Không gửi OTP, giấy tờ hoặc dữ liệu xác minh vào analytics.

---

## 9. Consent & Preferences

Theo dõi:

- Consent purpose.
- Channel.
- Version.
- Captured at.
- Source.
- Withdrawal status.
- Evidence reference.

Không dùng consent để hợp thức hóa xử lý không phù hợp hoặc không cần consent.

---

## 10. Retention & deletion

Mỗi policy gồm:

- Dataset/category.
- Retention period.
- Trigger event.
- Legal hold behavior.
- Deletion/anonymization method.
- Owner.
- Review date.

Deletion jobs phải có preview, scope, dry run và verification.

---

## 11. Legal hold

Legal hold phải:

- Chặn deletion trong đúng phạm vi.
- Có reason, owner, start/end.
- Có review định kỳ.
- Có audit.

Không dùng legal hold vô thời hạn thiếu owner hoặc review date.

---

## 12. Privacy risk assessment

Đánh giá:

- Loại dữ liệu.
- Quy mô.
- Mục đích.
- Đối tượng bị ảnh hưởng.
- Third-party sharing.
- Automated decision-making.
- Security controls.
- Residual risk.

Statuses:

```text
Draft
Review
Approved
Mitigation Required
Expired
```

---

## 13. Third-party processors

Mỗi đối tác gồm:

- Tên.
- Dịch vụ.
- Data categories.
- Processing location.
- Contract/DPA status.
- Security review.
- Subprocessors.
- Last review.
- Next review.
- Exit plan.

Không hiển thị contract hoặc security evidence ngoài quyền.

---

## 14. Data incident workflow

```text
Detected
→ Triage
→ Containment
→ Impact assessment
→ Legal review
→ Notification decision
→ Remediation
→ Closure
```

Phân biệt security incident và reportable privacy incident.

---

## 15. Evidence management

Evidence có thể gồm:

- Policy versions.
- Approval records.
- Training completion.
- Processor reviews.
- Request fulfillment evidence.
- Retention/deletion logs.
- Audit exports.

Files phải private, có expiry và permission riêng.

---

## 16. API endpoints đề xuất

```text
GET  /api/v1/compliance/summary
GET  /api/v1/privacy/processing-activities
POST /api/v1/privacy/processing-activities
GET  /api/v1/privacy/requests
POST /api/v1/privacy/requests
PATCH /api/v1/privacy/requests/:id
GET  /api/v1/privacy/consents
GET  /api/v1/privacy/retention-policies
PATCH /api/v1/privacy/retention-policies/:id
GET  /api/v1/privacy/risk-assessments
POST /api/v1/privacy/risk-assessments
GET  /api/v1/privacy/processors
GET  /api/v1/privacy/incidents
```

---

## 17. Frontend component inventory

```text
CompliancePrivacyCenterPage
├── ComplianceSummaryCards
├── ProcessingRegisterTable
├── PrivacyRequestQueue
├── RequestDetailDrawer
├── ConsentTimeline
├── RetentionPolicyTable
├── DeletionJobReview
├── PrivacyRiskAssessmentForm
├── ThirdPartyProcessorTable
├── PrivacyIncidentPanel
└── EvidenceLibrary
```

---

## 18. Security và privacy

- Backend enforce field-level permission.
- PII masking mặc định.
- Không trả evidence nhạy cảm rồi ẩn ở client.
- Export dùng signed URL.
- Deletion và request fulfillment cần audit.
- Không ghi request details hoặc subject identity vào analytics.

---

## 19. Audit events

```text
privacy.request.created
privacy.request.verified
privacy.request.completed
privacy.request.rejected
privacy.retention.updated
privacy.deletion.started
privacy.deletion.completed
privacy.legal_hold.created
privacy.risk_assessment.approved
privacy.processor.reviewed
privacy.incident.created
```

---

## 20. Testing

### Unit

- Due-date calculation.
- Legal-hold blocking.
- Retention resolution.
- Permission masking.

### Integration

- Deletion bị chặn bởi legal hold.
- Request chỉ hoàn tất sau verification.
- Export không vượt field permission.
- Processor review expiry warning.

### E2E

- Tạo request access.
- Verify identity.
- Collect data từ nhiều system.
- Review response.
- Complete request.
- Tạo deletion request có legal hold.
- Mở privacy incident.

---

## 21. AI coding agent requirements

AI coding agent phải:

- Không tự động xóa dữ liệu thiếu verification và legal review.
- Không hiển thị PII hoặc evidence ngoài quyền.
- Không dùng consent như default cho mọi processing activity.
- Không gửi request content tới analytics hoặc provider chưa phê duyệt.
- Bổ sung test cho legal hold, masking, deadlines và export scope.

---

## 22. Acceptance checklist

- [ ] Có Processing Register.
- [ ] Có Data Subject Request workflow.
- [ ] Có identity verification.
- [ ] Có consent evidence và withdrawal.
- [ ] Có retention, deletion và legal hold.
- [ ] Có privacy risk assessment.
- [ ] Có third-party processor management.
- [ ] Có privacy incident workflow.
- [ ] Có evidence library và audit.
- [ ] Có loading, empty, error, responsive và accessibility states.

---

## 23. Những điều không được làm

- Không xóa dữ liệu bằng một click.
- Không bỏ qua verification.
- Không vô hiệu legal hold thiếu approval.
- Không hiển thị PII/evidence ngoài quyền.
- Không gửi dữ liệu chủ thể vào analytics.
- Không coi security incident nào cũng là privacy breach hoặc ngược lại.

---

## 24. Kết luận

Compliance & Privacy Center của Cynca VLXD phải biến nghĩa vụ quyền riêng tư thành workflow có thể theo dõi, kiểm chứng và audit. Mọi yêu cầu, retention, deletion và đánh giá rủi ro phải có owner, deadline, permission và bằng chứng rõ ràng.

File tiếp theo:

```text
52-Security-Operations-Center.md
```