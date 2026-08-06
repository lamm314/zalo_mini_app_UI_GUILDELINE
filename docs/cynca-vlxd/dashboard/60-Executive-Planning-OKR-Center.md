# Cynca VLXD Admin Dashboard — Executive Planning & OKR Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý mục tiêu chiến lược, OKR, kế hoạch kinh doanh, sáng kiến và theo dõi thực thi  
> Đối tượng sử dụng: Ban điều hành, Branch Manager, Finance, Operations, Product, PMO, QA và AI coding agent

---

## 1. Mục tiêu

Executive Planning & OKR Center giúp Cynca VLXD chuyển định hướng chiến lược thành mục tiêu, kết quả then chốt, sáng kiến và hành động có thể đo lường.

Hệ thống phải trả lời:

- Mục tiêu chiến lược nào đang được ưu tiên?
- Kết quả then chốt đang đạt bao nhiêu phần trăm?
- Chỉ số nào đang chậm, lệch kế hoạch hoặc thiếu dữ liệu?
- Sáng kiến nào đóng góp cho mục tiêu nào?
- Branch, team và owner nào đang chịu trách nhiệm?
- Có dependency, blocker hoặc rủi ro nào cần can thiệp?
- Kế hoạch quý/năm có nhất quán với ngân sách và năng lực vận hành không?

---

## 2. Information architecture

```text
Executive Planning & OKR Center
├── Tổng quan chiến lược
├── Objectives
├── Key Results
├── Initiatives
├── Planning Calendar
├── Portfolio View
├── Risks & Dependencies
├── Check-ins
├── Reviews
└── Audit History
```

---

## 3. Permission model

```text
planning.center.view
planning.objectives.view
planning.objectives.create
planning.objectives.update
planning.objectives.approve
planning.key_results.manage
planning.initiatives.manage
planning.checkins.manage
planning.portfolio.view
planning.risks.manage
planning.reviews.manage
planning.audit.view
```

Mục tiêu toàn công ty, thay đổi target và đóng kỳ cần quyền riêng.

---

## 4. Objective contract

```ts
export type ExecutiveObjective = {
  id: string;
  title: string;
  description?: string;
  level: "company" | "region" | "branch" | "department" | "team";
  scopeIds?: string[];
  periodId: string;
  ownerUserId: string;
  status: "draft" | "review" | "approved" | "active" | "at_risk" | "completed" | "cancelled";
  priority: "critical" | "high" | "medium" | "low";
  parentObjectiveId?: string;
  progressPercent?: number;
  confidence?: "high" | "medium" | "low";
  createdAt: string;
  updatedAt: string;
};
```

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Objectives active.
2. Objectives on track.
3. Objectives at risk.
4. Key Results thiếu check-in.
5. Initiatives blocked.
6. Progress toàn kỳ.

Widgets:

- Strategic priorities.
- Progress by branch/domain.
- At-risk objectives.
- Upcoming reviews.
- Blocked initiatives.
- Data-confidence warnings.

---

## 6. Objective hierarchy

Cấu trúc tham chiếu:

```text
Company Objective
→ Regional/Branch Objective
→ Department/Team Objective
→ Key Results
→ Initiatives
```

Không bắt buộc mọi objective phải cascade nếu chiến lược không phù hợp, nhưng relationship phải rõ.

---

## 7. Key Result contract

```ts
export type KeyResult = {
  id: string;
  objectiveId: string;
  title: string;
  metricKey?: string;
  measurementType: "number" | "currency" | "percentage" | "milestone" | "binary";
  baseline?: number;
  target?: number;
  currentValue?: number;
  unit?: string;
  direction: "increase" | "decrease" | "maintain" | "complete";
  ownerUserId: string;
  status: "not_started" | "on_track" | "at_risk" | "off_track" | "completed";
  dataSource?: "manual" | "dashboard_metric" | "report" | "integration";
  updatedAt?: string;
};
```

---

## 8. Progress calculation

- Number/percentage/currency: tính từ baseline, target và current value.
- Decrease metric: tiến độ tăng khi giá trị giảm đúng hướng.
- Milestone: dùng weighted milestones.
- Binary: 0 hoặc 100%, chỉ dùng khi phù hợp.

Không dùng công thức tăng đơn giản cho mọi loại Key Result.

---

## 9. Confidence và data status

Mỗi Key Result cần phân biệt:

```text
Data verified
Data provisional
Data stale
Manual update
Missing data
```

Objective progress không được hiển thị chính xác giả tạo khi nhiều KR thiếu dữ liệu.

---

## 10. Initiative contract

```ts
export type StrategicInitiative = {
  id: string;
  title: string;
  objectiveIds: string[];
  keyResultIds?: string[];
  ownerUserId: string;
  status: "proposed" | "approved" | "in_progress" | "blocked" | "completed" | "cancelled";
  startAt?: string;
  dueAt?: string;
  budgetAmount?: number;
  branchIds?: string[];
  dependencies?: string[];
  riskLevel?: "low" | "medium" | "high" | "critical";
};
```

---

## 11. Strategic planning workflow

```text
Define themes
→ Draft objectives
→ Define measurable KRs
→ Review alignment
→ Approve targets
→ Assign initiatives
→ Run check-ins
→ Mid-period review
→ Close and score period
```

Không kích hoạt objective thiếu owner, period hoặc Key Result phù hợp.

---

## 12. Planning periods

```ts
export type PlanningPeriod = {
  id: string;
  name: string;
  type: "month" | "quarter" | "half_year" | "year";
  startsAt: string;
  endsAt: string;
  status: "draft" | "planning" | "active" | "review" | "closed";
  checkinFrequency: "weekly" | "biweekly" | "monthly";
};
```

Kỳ đã closed không được sửa target thiếu reopening workflow và audit.

---

## 13. Check-in workflow

Mỗi check-in gồm:

- Current value.
- Status.
- Confidence.
- Progress note.
- Blockers.
- Next actions.
- Evidence/source.
- Updated by/time.

Không chỉ cập nhật phần trăm mà thiếu giải thích khi trạng thái at risk/off track.

---

## 14. Check-in contract

```ts
export type OKRCheckIn = {
  id: string;
  keyResultId: string;
  value?: number;
  status: "on_track" | "at_risk" | "off_track" | "completed";
  confidence: "high" | "medium" | "low";
  note: string;
  blockers?: string[];
  nextActions?: string[];
  createdBy: string;
  createdAt: string;
};
```

---

## 15. Alignment view

Hiển thị:

- Parent/child objectives.
- Shared Key Results.
- Supporting initiatives.
- Branch/team ownership.
- Missing alignment.
- Conflicting targets.

Có tree/list alternative cho accessibility.

---

## 16. Portfolio view

Portfolio tổng hợp sáng kiến theo:

- Strategic theme.
- Objective.
- Branch.
- Owner.
- Status.
- Budget.
- Risk.
- Expected impact.

Không biến thành hệ thống project management chi tiết; chỉ giữ mức điều hành.

---

## 17. Risk và dependency management

Risk gồm:

- Title.
- Probability.
- Impact.
- Owner.
- Mitigation.
- Due date.
- Status.

Dependency gồm:

- Upstream/downstream initiative.
- Required date.
- Owner.
- Status.
- Impact if delayed.

---

## 18. Budget alignment

Nếu có quyền Finance, hiển thị:

- Planned budget.
- Approved budget.
- Actual spend.
- Forecast.
- Variance.
- Funding status.

Không hiển thị số tài chính ngoài permission hoặc coi ngân sách là doanh thu.

---

## 19. Branch planning

Branch Manager chỉ xem và quản lý objective trong scope.

Có thể hiển thị:

- Revenue target.
- Order SLA.
- Inventory health.
- Debt collection.
- CSAT.
- Staff readiness.

Target branch phải được phê duyệt theo policy và không tự mở rộng sang branch khác.

---

## 20. Review meetings

Review view gồm:

- Agenda.
- Objectives at risk.
- Missing check-ins.
- Decisions required.
- Actions từ kỳ trước.
- Notes và decisions.
- Owners/due dates.

Không thay thế lịch/calendar, nhưng có thể liên kết meeting và export review pack.

---

## 21. Period scoring

Scoring có thể theo:

- Weighted KRs.
- Objective status.
- Confidence/data quality.
- Manual review note.

Không dùng điểm OKR làm thước đo duy nhất để đánh giá cá nhân hoặc thưởng phạt thiếu bối cảnh.

---

## 22. Objective change control

Thay đổi sau khi active:

- Target.
- Owner.
- Scope.
- Weight.
- Due date.
- Key Result definition.

Cần reason, impact preview, approval tùy mức thay đổi và audit.

---

## 23. API endpoints đề xuất

```text
GET  /api/v1/planning/summary
GET  /api/v1/planning/periods
POST /api/v1/planning/periods
GET  /api/v1/planning/objectives
POST /api/v1/planning/objectives
PATCH /api/v1/planning/objectives/:id
POST /api/v1/planning/objectives/:id/approve
GET  /api/v1/planning/key-results
POST /api/v1/planning/key-results
POST /api/v1/planning/key-results/:id/check-ins
GET  /api/v1/planning/initiatives
POST /api/v1/planning/initiatives
GET  /api/v1/planning/portfolio
GET  /api/v1/planning/reviews
```

---

## 24. Frontend component inventory

```text
ExecutivePlanningOKRCenterPage
├── PlanningSummaryCards
├── StrategicPriorityPanel
├── ObjectiveTree
├── ObjectiveListAlternative
├── ObjectiveDetailPage
├── KeyResultProgressTable
├── CheckInDrawer
├── AlignmentView
├── InitiativePortfolio
├── RiskDependencyPanel
├── BudgetAlignmentPanel
├── ReviewMeetingPack
└── PeriodClosingWizard
```

---

## 25. Loading, empty và error states

- Metric source lỗi phải hiển thị Unknown/Stale, không tự dùng 0.
- Objective chưa có KR cần setup state.
- Check-in save lỗi giữ draft.
- Portfolio API lỗi không đánh dấu không có initiatives.
- Closed period conflict phải chặn update và hiển thị reopening requirement.

---

## 26. Security và privacy

- Backend enforce company/branch/team scope.
- Finance data, staff notes và review notes theo permission riêng.
- Không trả dữ liệu toàn công ty rồi lọc client-side.
- Objective changes và period close/reopen phải audit.
- Export review pack dùng signed URL nếu chứa dữ liệu nhạy cảm.

---

## 27. Audit events

```text
planning.period.created
planning.period.activated
planning.period.closed
planning.objective.created
planning.objective.approved
planning.objective.updated
planning.key_result.updated
planning.checkin.created
planning.initiative.created
planning.review.completed
```

---

## 28. Testing

### Unit

- Progress theo direction.
- Weighted milestone.
- Objective aggregation.
- Missing-data confidence.
- Period-state transitions.

### Integration

- Branch user chỉ thấy branch scope.
- Closed period chặn sửa.
- Dashboard metric stale hiển thị warning.
- Target change cần approval.
- Budget panel ẩn đúng permission.

### E2E

- Tạo quarterly period.
- Tạo objective và Key Results.
- Approve target.
- Gắn initiatives.
- Thực hiện check-in.
- Review objective at risk.
- Close period và xuất review pack.

---

## 29. AI-assisted planning requirements

Nếu dùng AI:

- Chỉ gợi ý objective, KR, summary hoặc risk hypothesis.
- Không tự đặt target hoặc approve plan.
- Không bịa metric/current value.
- Phải dẫn nguồn khi tóm tắt dữ liệu.
- Human review bắt buộc trước khi lưu hoặc publish.

---

## 30. AI coding agent requirements

AI coding agent phải:

- Không dùng một công thức progress cho mọi measurement type.
- Không coi missing/stale data là 0.
- Không sửa closed period thiếu workflow.
- Không dùng OKR score làm KPI cá nhân mặc định.
- Không hiển thị Finance data ngoài permission.
- Bổ sung test cho branch scope, target change, progress direction và missing data.

---

## 31. Acceptance checklist

- [ ] Có planning periods.
- [ ] Có Objective hierarchy.
- [ ] Có measurable Key Results.
- [ ] Có progress calculation theo loại metric.
- [ ] Có confidence và data status.
- [ ] Có initiatives và portfolio view.
- [ ] Có check-in workflow.
- [ ] Có alignment, risk và dependencies.
- [ ] Có budget alignment theo permission.
- [ ] Có review meeting và period closing.
- [ ] Có change control và audit.
- [ ] Có responsive, accessibility và testing đầy đủ.

---

## 32. Những điều không được làm

- Không tạo objective active thiếu owner hoặc KR.
- Không hiển thị 0 khi metric chưa tải/lỗi.
- Không dùng progress formula sai direction.
- Không sửa target hoặc period đã đóng thiếu approval.
- Không dùng OKR score làm căn cứ duy nhất đánh giá cá nhân.
- Không hiển thị dữ liệu tài chính ngoài quyền.
- Không để AI tự đặt hoặc duyệt mục tiêu.

---

## 33. Kết luận

Executive Planning & OKR Center của Cynca VLXD phải kết nối chiến lược với chỉ số, sáng kiến, owner và nhịp review rõ ràng. Tiến độ cần dựa trên dữ liệu đáng tin cậy, có confidence, scope và change control; mục tiêu phải hỗ trợ quyết định điều hành thay vì chỉ tạo thêm báo cáo hình thức.

File tiếp theo đề xuất:

```text
README.md
61-Notification-Campaign-Center.md
```