# Cynca VLXD Admin Dashboard — Business Continuity Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý kế hoạch duy trì hoạt động, kịch bản gián đoạn, khả năng vận hành thay thế và diễn tập liên tục kinh doanh  
> Đối tượng sử dụng: Ban điều hành, Operations, IT, Security, Finance, Warehouse, Customer Service, QA và AI coding agent

---

## 1. Mục tiêu

Business Continuity Center giúp Cynca VLXD duy trì các hoạt động quan trọng khi xảy ra mất điện, mất mạng, gián đoạn nhà cung cấp, sự cố hệ thống, thiếu nhân sự, sự cố kho hoặc thiên tai.

Hệ thống phải trả lời:

- Quy trình nào là critical và có thể gián đoạn trong bao lâu?
- Có phương án vận hành thay thế nào?
- Chi nhánh, kho, đội ngũ và nhà cung cấp nào bị ảnh hưởng?
- Ai có quyền kích hoạt continuity plan?
- Có thể chuyển sang chế độ thủ công, read-only hoặc queue offline không?
- Khi nào được quay lại vận hành bình thường?
- Lần diễn tập gần nhất có đạt yêu cầu không?

---

## 2. Information architecture

```text
Business Continuity Center
├── Tổng quan
├── Critical Business Services
├── Continuity Plans
├── Disruption Scenarios
├── Alternate Operations
├── Contact Trees
├── Exercise & Simulation
├── Active Continuity Events
├── Recovery Tracking
└── Audit & Evidence
```

---

## 3. Permission model

```text
continuity.center.view
continuity.services.view
continuity.plans.view
continuity.plans.manage
continuity.events.activate
continuity.events.manage
continuity.contacts.view
continuity.exercises.manage
continuity.recovery.verify
continuity.evidence.export
```

Kích hoạt continuity plan là hành động nhạy cảm và phải có audit.

---

## 4. Critical service model

```ts
export type CriticalBusinessService = {
  id: string;
  name: string;
  ownerUserId: string;
  criticality: "critical" | "high" | "medium" | "low";
  maximumTolerableDowntimeMinutes: number;
  minimumServiceLevelPercent: number;
  dependentSystems: string[];
  dependentTeams: string[];
  dependentSuppliers?: string[];
  alternateProcessAvailable: boolean;
};
```

Ví dụ:

- Tiếp nhận và xác nhận đơn hàng.
- Xuất kho và bàn giao vận chuyển.
- Thu tiền và đối soát.
- Chăm sóc khách hàng.
- Quản lý tồn kho.

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Critical services có plan hợp lệ.
2. Plans sắp hết hạn review.
3. Active continuity events.
4. Services vượt MTD.
5. Exercise actions quá hạn.
6. Alternate sites/channels sẵn sàng.

Widgets:

- Continuity readiness.
- Critical service status.
- Active disruption map.
- Contact-tree readiness.
- Recent exercises.
- Recovery blockers.

---

## 6. Continuity plan contract

```ts
export type ContinuityPlan = {
  id: string;
  name: string;
  serviceIds: string[];
  scenarioIds: string[];
  status: "draft" | "review" | "approved" | "active" | "expired" | "retired";
  ownerUserId: string;
  activationCriteria: string[];
  alternateProcesses: string[];
  communicationPlanId?: string;
  recoveryPriorities: string[];
  lastReviewedAt?: string;
  nextReviewAt?: string;
  version: number;
};
```

---

## 7. Disruption scenarios

```text
Data center/service outage
Internet outage
Power outage
Warehouse unavailable
Supplier interruption
Shipping disruption
Staff shortage
Cyber incident
Data corruption
Natural disaster
```

Mỗi scenario cần:

- Trigger.
- Expected impact.
- Maximum duration.
- Affected services.
- Alternative process.
- Required contacts.
- Exit criteria.

---

## 8. Business Impact Analysis

BIA cần ghi nhận:

- Financial impact.
- Customer impact.
- Operational impact.
- Legal/compliance impact.
- Reputation impact.
- Dependency impact.
- Maximum tolerable downtime.
- Minimum acceptable service level.

Không dùng một risk score tổng hợp thiếu giải thích.

---

## 9. Alternate operation modes

Có thể gồm:

```text
Manual order capture
Read-only dashboard
Offline task queue
Alternate warehouse
Alternate carrier
Manual reconciliation
Temporary hotline routing
Limited product catalog
```

UI phải ghi rõ hạn chế, dữ liệu có thể stale và quy trình nhập bù sau khi phục hồi.

---

## 10. Activation workflow

```text
Detect disruption
→ Assess impact
→ Select plan
→ Confirm authority
→ Activate
→ Notify teams
→ Start alternate operations
→ Track service level
→ Recover
→ Verify
→ Stand down
```

Không kích hoạt toàn hệ thống khi chỉ một chi nhánh bị ảnh hưởng.

---

## 11. Continuity event contract

```ts
export type ContinuityEvent = {
  id: string;
  title: string;
  scenarioId: string;
  status: "assessing" | "activated" | "operating_alternate" | "recovering" | "verifying" | "closed";
  affectedServiceIds: string[];
  affectedBranchIds?: string[];
  activatedPlanId?: string;
  activatedBy?: string;
  startedAt: string;
  targetRecoveryAt?: string;
  closedAt?: string;
};
```

---

## 12. Contact tree

Mỗi contact tree gồm:

- Primary contact.
- Backup contact.
- Role.
- Channel.
- Escalation delay.
- Acknowledgement state.

Không hiển thị thông tin cá nhân rộng hơn phạm vi cần thiết.

---

## 13. Communication plan

Kênh:

- In-app banner.
- Email nội bộ.
- Zalo OA nội bộ.
- Hotline.
- External notice nếu cần.

Nội dung gồm:

- Tình trạng.
- Phạm vi ảnh hưởng.
- Quy trình thay thế.
- Hành động người dùng cần làm.
- Thời điểm cập nhật tiếp theo.

---

## 14. Exercise & simulation

Các loại:

```text
Tabletop exercise
Walkthrough
Partial failover test
Full continuity drill
Supplier disruption drill
Warehouse relocation drill
```

Mỗi exercise gồm scope, assumptions, participants, outcome, gaps và action items.

---

## 15. Exercise contract

```ts
export type ContinuityExercise = {
  id: string;
  name: string;
  type: string;
  planId: string;
  scheduledAt: string;
  status: "scheduled" | "running" | "completed" | "cancelled";
  participants: string[];
  objectives: string[];
  result?: "passed" | "partially_passed" | "failed";
  findings?: string[];
};
```

---

## 16. Recovery tracking

Theo dõi:

- Service restored.
- Data reconciled.
- Offline transactions imported.
- Inventory verified.
- Customer communication completed.
- Alternate process stopped.
- Backlog cleared.
- Lessons captured.

Không đóng continuity event trước khi dữ liệu và backlog được kiểm tra.

---

## 17. API endpoints đề xuất

```text
GET  /api/v1/continuity/summary
GET  /api/v1/continuity/services
GET  /api/v1/continuity/plans
POST /api/v1/continuity/plans
PATCH /api/v1/continuity/plans/:id
GET  /api/v1/continuity/events
POST /api/v1/continuity/events
POST /api/v1/continuity/events/:id/activate
PATCH /api/v1/continuity/events/:id
GET  /api/v1/continuity/exercises
POST /api/v1/continuity/exercises
```

---

## 18. Frontend component inventory

```text
BusinessContinuityCenterPage
├── ContinuitySummaryCards
├── CriticalServiceTable
├── ReadinessMatrix
├── ContinuityPlanEditor
├── ScenarioLibrary
├── ActivationWizard
├── ActiveEventPanel
├── ContactTreePanel
├── RecoveryChecklist
├── ExerciseCalendar
└── FindingsActionTable
```

---

## 19. Security và audit

- Backend enforce branch, service và role scope.
- Không hiển thị full contact data ngoài quyền.
- Kích hoạt/đóng plan phải re-authentication nếu policy yêu cầu.
- Không tự queue action tài chính nhạy cảm trong alternate mode.
- Audit plan version, activation, communications và recovery verification.

Audit events:

```text
continuity.plan.created
continuity.plan.approved
continuity.event.activated
continuity.mode.changed
continuity.communication.sent
continuity.recovery.verified
continuity.exercise.completed
```

---

## 20. Testing

### Unit

- MTD calculation.
- Plan expiry.
- Activation eligibility.
- Service-level status.

### Integration

- Branch event không ảnh hưởng branch khác.
- Contact-tree acknowledgement.
- Alternate mode hiển thị đúng hạn chế.
- Recovery chỉ complete sau verification.

### E2E

- Kích hoạt mất mạng tại một chi nhánh.
- Chuyển sang manual order capture.
- Gửi communication.
- Theo dõi backlog.
- Khôi phục và nhập bù.
- Đóng event sau verification.

---

## 21. Acceptance checklist

- [ ] Có Critical Business Services.
- [ ] Có Business Impact Analysis.
- [ ] Có Continuity Plans và scenarios.
- [ ] Có alternate operation modes.
- [ ] Có activation workflow.
- [ ] Có contact tree và communication plan.
- [ ] Có exercise management.
- [ ] Có recovery tracking.
- [ ] Có permission, audit, responsive và accessibility.
- [ ] Có testing và Visual QA đầy đủ.

---

## 22. Những điều không được làm

- Không kích hoạt plan toàn hệ thống thiếu scope.
- Không coi alternate mode là vận hành bình thường.
- Không giấu dữ liệu stale hoặc pending reconciliation.
- Không đóng event trước verification.
- Không hiển thị contact data ngoài quyền.
- Không dùng continuity plan thay thế backup, incident hoặc security plan.

---

## 23. Kết luận

Business Continuity Center của Cynca VLXD phải giúp doanh nghiệp duy trì các dịch vụ thiết yếu khi xảy ra gián đoạn, với phương án thay thế, quyền kích hoạt, communication và recovery checklist rõ ràng. Mỗi kế hoạch phải được review, diễn tập và chứng minh bằng kết quả thực tế.

File tiếp theo:

```text
55-Vendor-Third-Party-Management-Center.md
```