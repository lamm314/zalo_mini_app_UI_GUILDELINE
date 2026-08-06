# Cynca VLXD Admin Dashboard — Vendor & Third-Party Management Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý nhà cung cấp, đối tác dịch vụ, đánh giá rủi ro, hợp đồng, SLA và hiệu suất  
> Đối tượng sử dụng: Procurement, Operations, Finance, Legal, Security, Compliance, Warehouse, QA và AI coding agent

---

## 1. Mục tiêu

Vendor & Third-Party Management Center giúp Cynca VLXD quản lý toàn bộ vòng đời nhà cung cấp và đối tác, từ tiếp nhận, đánh giá, phê duyệt, ký kết, vận hành đến gia hạn hoặc chấm dứt.

Hệ thống phải trả lời:

- Đối tác nào đang cung cấp dịch vụ gì?
- Hợp đồng và chứng từ nào sắp hết hạn?
- SLA, chất lượng và tỷ lệ lỗi của từng đối tác ra sao?
- Đối tác nào có rủi ro bảo mật, tài chính hoặc vận hành cao?
- Có phụ thuộc tập trung vào một nhà cung cấp không?
- Khi chấm dứt hợp tác cần thu hồi quyền truy cập và dữ liệu như thế nào?

---

## 2. Information architecture

```text
Vendor & Third-Party Management
├── Tổng quan
├── Vendor Directory
├── Onboarding & Due Diligence
├── Contracts & Documents
├── SLA & Performance
├── Risk Assessments
├── Access & Integrations
├── Issues & Corrective Actions
├── Renewal & Exit Planning
└── Audit History
```

---

## 3. Permission model

```text
vendors.center.view
vendors.view
vendors.create
vendors.update
vendors.approve
vendors.contracts.view
vendors.contracts.manage
vendors.risks.view
vendors.risks.manage
vendors.performance.view
vendors.access.manage
vendors.exit.manage
vendors.evidence.export
```

---

## 4. Vendor contract

```ts
export type Vendor = {
  id: string;
  code: string;
  name: string;
  category: "supplier" | "shipping" | "payment" | "technology" | "consulting" | "facility" | "other";
  status: "prospect" | "due_diligence" | "approved" | "active" | "suspended" | "terminating" | "terminated";
  criticality: "critical" | "high" | "medium" | "low";
  ownerUserId: string;
  services: string[];
  branchIds?: string[];
  integrationIds?: string[];
  contractIds?: string[];
  riskLevel?: "low" | "medium" | "high" | "critical";
  nextReviewAt?: string;
};
```

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Active vendors.
2. Critical vendors.
3. Contracts sắp hết hạn.
4. SLA breaches 30 ngày.
5. High-risk vendors.
6. Reviews quá hạn.

Widgets:

- Critical vendor health.
- Renewal calendar.
- SLA trend.
- Open corrective actions.
- Concentration risk.
- Recent incidents.

---

## 6. Vendor onboarding workflow

```text
Create vendor profile
→ Collect documents
→ Business review
→ Security/privacy review
→ Financial review
→ Contract review
→ Approval
→ Activate
```

Không kích hoạt vendor critical khi required review còn failed hoặc unknown.

---

## 7. Due diligence checklist

Có thể gồm:

- Legal registration.
- Tax information.
- Bank verification.
- Insurance.
- Security questionnaire.
- Privacy/DPA review.
- Service capability.
- References.
- Business continuity.
- Subcontractor disclosure.

---

## 8. Contract management

Mỗi hợp đồng gồm:

- Contract code.
- Effective date.
- Expiry date.
- Auto-renewal.
- Notice period.
- Value/currency theo quyền.
- SLA attachment.
- Data-processing terms.
- Termination conditions.
- Owner.

Files phải private và có version.

---

## 9. Contract contract

```ts
export type VendorContract = {
  id: string;
  vendorId: string;
  code: string;
  status: "draft" | "review" | "active" | "expired" | "terminated";
  effectiveFrom: string;
  effectiveTo?: string;
  autoRenew: boolean;
  noticeDays?: number;
  ownerUserId: string;
  documentVersion?: number;
};
```

---

## 10. SLA model

```ts
export type VendorSLA = {
  id: string;
  vendorId: string;
  metricKey: string;
  target: number;
  unit: "percent" | "minutes" | "hours" | "days" | "count";
  evaluationWindow: "daily" | "weekly" | "monthly";
  actual?: number;
  status: "met" | "at_risk" | "breached" | "unknown";
  serviceCreditRule?: string;
};
```

Ví dụ:

- Tỷ lệ giao đúng hẹn.
- Payment success rate.
- API availability.
- Response time.
- Defect rate.
- Resolution time.

---

## 11. Performance scorecard

Hiển thị:

- SLA compliance.
- Quality issues.
- Delivery timeliness.
- Incident count.
- Corrective actions.
- User feedback.
- Trend.

Không dùng một điểm tổng hợp duy nhất thiếu drill-down.

---

## 12. Risk assessment

Các nhóm:

```text
Operational
Financial
Security
Privacy
Legal
Compliance
Concentration
Geographic
Business continuity
```

Mỗi risk cần inherent risk, controls, residual risk, owner và review date.

---

## 13. Concentration risk

Theo dõi:

- Tỷ trọng đơn hàng hoặc dịch vụ theo vendor.
- Số quy trình phụ thuộc duy nhất.
- Vendor không có phương án thay thế.
- Region hoặc infrastructure dùng chung.

Không chỉ đo theo giá trị hợp đồng.

---

## 14. Access & integrations

Vendor có thể được cấp:

- Portal access.
- API credential.
- File exchange.
- Webhook.
- Support access.
- Temporary account.

Yêu cầu:

- Least privilege.
- Owner.
- Expiry.
- Review định kỳ.
- Credential rotation.
- Thu hồi khi chấm dứt.

---

## 15. Vendor issue workflow

```text
Issue detected
→ Assign owner
→ Assess severity
→ Vendor response
→ Corrective action
→ Verify
→ Close
```

Issue types:

- SLA breach.
- Quality defect.
- Delivery failure.
- Security incident.
- Documentation lapse.
- Billing discrepancy.

---

## 16. Corrective action contract

```ts
export type VendorCorrectiveAction = {
  id: string;
  vendorId: string;
  title: string;
  severity: "critical" | "high" | "medium" | "low";
  ownerUserId: string;
  vendorOwner?: string;
  dueAt: string;
  status: "open" | "in_progress" | "verifying" | "completed" | "overdue";
  evidenceRequired: boolean;
};
```

---

## 17. Renewal workflow

```text
Review performance
→ Review risk
→ Confirm business need
→ Compare alternatives
→ Review pricing/terms
→ Approve renewal
→ Sign/update records
```

Cảnh báo theo notice period, không chỉ expiry date.

---

## 18. Exit planning

Exit plan gồm:

- Contract termination steps.
- Data return/deletion.
- Credential revocation.
- Integration shutdown.
- Asset return.
- Knowledge transfer.
- Replacement vendor.
- Final reconciliation.
- Evidence of completion.

Không đánh dấu terminated trước khi access và dữ liệu được xử lý.

---

## 19. API endpoints đề xuất

```text
GET  /api/v1/vendors/summary
GET  /api/v1/vendors
POST /api/v1/vendors
GET  /api/v1/vendors/:id
PATCH /api/v1/vendors/:id
POST /api/v1/vendors/:id/approve
GET  /api/v1/vendors/:id/contracts
POST /api/v1/vendors/:id/contracts
GET  /api/v1/vendors/:id/slas
GET  /api/v1/vendors/:id/risks
POST /api/v1/vendors/:id/issues
POST /api/v1/vendors/:id/exit
```

---

## 20. Frontend component inventory

```text
VendorThirdPartyManagementPage
├── VendorSummaryCards
├── VendorDirectoryTable
├── VendorDetailPage
├── OnboardingChecklist
├── DueDiligencePanel
├── ContractTimeline
├── RenewalCalendar
├── SLAScorecard
├── RiskAssessmentPanel
├── AccessInventory
├── VendorIssueQueue
└── ExitPlanChecklist
```

---

## 21. Security và privacy

- Backend enforce vendor, contract và field permissions.
- Bank, contract và security evidence phải được mask/restricted.
- Không trả API credential cũ về client.
- Vendor portal không được truy cập dữ liệu ngoài phạm vi.
- Exit phải thu hồi session và credential.
- Audit mọi approval, contract, access và termination action.

---

## 22. Audit events

```text
vendor.created
vendor.approved
vendor.contract.activated
vendor.sla.breached
vendor.risk.updated
vendor.access.granted
vendor.access.revoked
vendor.issue.created
vendor.corrective_action.completed
vendor.terminated
```

---

## 23. Testing

### Unit

- Renewal dates.
- Notice-period warnings.
- SLA status.
- Risk mapping.

### Integration

- Vendor critical không active khi review chưa đạt.
- Contract expiry warning.
- Access bị thu hồi khi terminate.
- Sensitive evidence bị mask.

### E2E

- Tạo vendor mới.
- Hoàn tất due diligence.
- Kích hoạt hợp đồng.
- Ghi nhận SLA breach.
- Tạo corrective action.
- Gia hạn hoặc chấm dứt vendor.

---

## 24. AI coding agent requirements

AI coding agent phải:

- Không kích hoạt vendor khi required review unknown/failed.
- Không hiển thị credential hoặc contract evidence ngoài quyền.
- Không đánh dấu termination hoàn tất trước access/data cleanup.
- Không dùng một vendor score thiếu drill-down.
- Bổ sung test cho approval, notice period, access revocation và evidence masking.

---

## 25. Acceptance checklist

- [ ] Có Vendor Directory.
- [ ] Có onboarding và due diligence.
- [ ] Có contract, document và renewal management.
- [ ] Có SLA scorecards.
- [ ] Có risk assessment và concentration risk.
- [ ] Có access/integration inventory.
- [ ] Có issue và corrective-action workflow.
- [ ] Có exit planning.
- [ ] Có permission, audit, responsive và accessibility.
- [ ] Có testing và Visual QA đầy đủ.

---

## 26. Những điều không được làm

- Không active vendor critical thiếu review.
- Không hiển thị bank, credential hoặc contract evidence ngoài quyền.
- Không tự gia hạn hợp đồng thiếu review.
- Không dùng expiry date mà bỏ notice period.
- Không terminate vendor mà quên thu hồi access và dữ liệu.
- Không coi SLA unknown là met.

---

## 27. Kết luận

Vendor & Third-Party Management Center của Cynca VLXD phải giúp doanh nghiệp kiểm soát rủi ro, hiệu suất, hợp đồng và quyền truy cập của mọi đối tác. Vòng đời vendor chỉ hoàn tất khi business, security, legal, SLA và exit obligations đều được xử lý và audit đầy đủ.

File tiếp theo:

```text
56-Automation-Workflow-Center.md
```