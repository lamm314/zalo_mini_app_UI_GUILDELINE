# Cynca VLXD Admin Dashboard — Automation & Workflow Center Specification

> Phiên bản: 1.0  
> Phạm vi: Thiết kế, quản lý, giám sát và kiểm soát các workflow tự động trong hệ thống  
> Đối tượng sử dụng: Product, Operations, Sales Operations, Warehouse, Customer Service, Finance, Backend, QA, Security và AI coding agent

---

## 1. Mục tiêu

Automation & Workflow Center giúp Cynca VLXD tạo và quản lý các quy trình tự động có kiểm soát, ví dụ phân công đơn, nhắc khách hàng, cảnh báo tồn kho, gửi thông báo hoặc khởi tạo tác vụ nội bộ.

Hệ thống phải trả lời:

- Workflow nào đang bật, tạm dừng hoặc lỗi?
- Trigger, điều kiện và action của workflow là gì?
- Có bao nhiêu lần chạy thành công, thất bại hoặc bị bỏ qua?
- Workflow nào đang gây gửi trùng, vòng lặp hoặc quá tải?
- Action nhạy cảm nào cần approval hoặc human review?
- Có thể test workflow trước khi publish không?
- Khi workflow lỗi, có thể retry hoặc rollback an toàn không?

---

## 2. Information architecture

```text
Automation & Workflow Center
├── Tổng quan
├── Workflow Library
├── Workflow Builder
├── Trigger Catalog
├── Action Catalog
├── Test & Simulation
├── Run History
├── Error & Retry Queue
├── Approval Tasks
├── Version History
└── Audit Log
```

---

## 3. Permission model

```text
automations.center.view
automations.workflows.view
automations.workflows.create
automations.workflows.update
automations.workflows.publish
automations.workflows.pause
automations.workflows.delete
automations.runs.view
automations.runs.retry
automations.approvals.manage
automations.catalog.manage
automations.audit.view
```

Publish, retry action nhạy cảm và catalog management là quyền đặc biệt.

---

## 4. Workflow contract

```ts
export type AutomationWorkflow = {
  id: string;
  key: string;
  name: string;
  description?: string;
  domain: "orders" | "sales" | "inventory" | "warehouse" | "finance" | "support" | "notifications" | "system";
  status: "draft" | "testing" | "review" | "published" | "paused" | "deprecated" | "archived";
  trigger: WorkflowTrigger;
  conditions: WorkflowCondition[];
  steps: WorkflowStep[];
  scope: WorkflowScope;
  ownerUserId: string;
  version: number;
  createdAt: string;
  updatedAt: string;
};
```

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Workflows published.
2. Runs trong 24 giờ.
3. Failed runs.
4. Approval tasks đang chờ.
5. Workflow bị pause.
6. Duplicate/loop warnings.

Widgets:

- Workflow health.
- Run success trend.
- Top failed workflows.
- Retry queue.
- Approval queue.
- Recent version changes.

---

## 6. Trigger catalog

Trigger types:

```text
Event-based
Scheduled
Manual
Threshold-based
State transition
Webhook received
Data freshness breach
```

Ví dụ:

- Order created.
- Payment completed.
- Inventory below threshold.
- Ticket near SLA.
- Customer order completed 7 days ago.
- Report job failed.

Trigger phải map tới catalog backend, không dùng raw event name tùy ý.

---

## 7. Trigger contract

```ts
export type WorkflowTrigger = {
  type: "event" | "schedule" | "manual" | "threshold" | "state_transition" | "webhook";
  key: string;
  configuration: Record<string, string | number | boolean | string[]>;
};
```

Configuration phải validate theo schema riêng của trigger.

---

## 8. Condition builder

Condition hỗ trợ:

- Equals / not equals.
- Greater/less than.
- Contains.
- In list.
- Date/time window.
- Branch/warehouse scope.
- Status transition.
- Permission-safe attribute.

Không cho nhập JavaScript, SQL hoặc biểu thức tùy ý.

---

## 9. Condition contract

```ts
export type WorkflowCondition = {
  id: string;
  fieldKey: string;
  operator: "eq" | "neq" | "gt" | "gte" | "lt" | "lte" | "contains" | "in" | "not_in";
  value: string | number | boolean | string[];
  groupId?: string;
  logicalOperator?: "and" | "or";
};
```

---

## 10. Action catalog

Action types:

```text
Create task
Assign owner
Send in-app notification
Send Zalo OA message
Send email
Update status
Add internal note
Create escalation
Generate report
Call registered webhook
Pause workflow branch
Request approval
```

Action phải map tới registry backend và có permission/scope riêng.

---

## 11. Action contract

```ts
export type WorkflowStep = {
  id: string;
  type: "action" | "delay" | "branch" | "approval" | "end";
  actionKey?: string;
  configuration?: Record<string, unknown>;
  nextStepIds?: string[];
  retryPolicy?: WorkflowRetryPolicy;
  timeoutSeconds?: number;
};
```

---

## 12. Workflow builder UI

Bố cục desktop:

```text
Trigger & Variables Panel
Visual Workflow Canvas
Properties Panel
Validation & Test Panel
Version/Publish Bar
```

Yêu cầu:

- Node rõ trạng thái.
- Có zoom và minimap nếu flow lớn.
- Có list alternative cho accessibility.
- Có undo/redo trong draft.
- Không cho publish khi còn node không kết nối hoặc validation failed.

---

## 13. Workflow scope

```ts
export type WorkflowScope = {
  mode: "system" | "branch" | "warehouse" | "team";
  resourceIds?: string[];
};
```

Workflow không được xử lý entity ngoài scope dù trigger nhận event rộng hơn.

---

## 14. Delay và scheduling

Delay có thể dùng:

- Sau N phút/giờ/ngày.
- Đến một thời điểm cụ thể.
- Theo business hours.
- Theo timezone branch.

Ví dụ:

- Gửi yêu cầu đánh giá sau 7 ngày.
- Nhắc callback trước deadline 30 phút.
- Gửi báo cáo lúc 08:00 thứ Hai.

Không dùng client timer cho workflow dài hạn.

---

## 15. Idempotency và deduplication

Mỗi run/action cần:

- Workflow ID/version.
- Trigger event ID.
- Entity ID.
- Idempotency key.
- Deduplication window.

Không gửi trùng notification hoặc cập nhật trạng thái nhiều lần khi cùng event được nhận lại.

---

## 16. Loop prevention

Hệ thống phải phát hiện:

- Workflow tự kích hoạt lại chính nó.
- Hai workflow kích hoạt vòng lặp.
- Status update qua lại.
- Retry vô hạn.
- Webhook echo.

Publish phải bị chặn nếu phát hiện vòng lặp rõ ràng.

---

## 17. Approval step

Dùng cho:

- Refund hoặc finance action.
- Stock adjustment.
- Order cancellation diện rộng.
- External message nhạy cảm.
- Permission hoặc configuration change.

Approval step gồm approver rule, dueAt, escalation và expiry.

Không tự tiếp tục khi approval timeout trừ khi policy quy định rõ fail-safe action.

---

## 18. Human-in-the-loop

Các bước có thể yêu cầu người dùng:

- Review nội dung tin nhắn.
- Xác nhận record match.
- Duyệt exception.
- Chọn phương án xử lý.
- Bổ sung evidence.

Workflow phải chờ trạng thái backend xác nhận thay vì giả định người dùng đã hoàn tất.

---

## 19. Test & simulation

Test mode phải hỗ trợ:

- Dữ liệu fixture.
- Trigger sample.
- Step-by-step execution.
- Condition result.
- Action preview.
- Variables preview.
- Warnings.
- Không mutation production.

Hiển thị rõ:

```text
Đây là bản chạy thử. Không có dữ liệu thật nào được thay đổi.
```

---

## 20. Dry run

Dry run có thể kiểm tra:

- Scope.
- Permissions.
- Template variables.
- Recipient count.
- Estimated action count.
- Loop risk.
- Rate limit.
- External dependency availability.

Không publish workflow nhạy cảm nếu dry run chưa đạt theo policy.

---

## 21. Versioning và publish

Lifecycle:

```text
Draft
→ Validate
→ Test
→ Review
→ Publish
→ Monitor
→ Pause/Deprecate
```

Publish tạo version bất biến.

Edit workflow published phải tạo draft version mới.

---

## 22. Impact preview

Trước publish hiển thị:

- Trigger volume ước tính.
- Entities/branches bị ảnh hưởng.
- Messages/tasks dự kiến.
- External integrations.
- Sensitive actions.
- Rate-limit risk.
- Required approvals.
- Rollback/pause strategy.

---

## 23. Run status model

```ts
export type WorkflowRunStatus =
  | "queued"
  | "running"
  | "waiting"
  | "awaiting_approval"
  | "completed"
  | "partially_completed"
  | "failed"
  | "cancelled"
  | "expired";
```

---

## 24. Workflow run contract

```ts
export type WorkflowRun = {
  id: string;
  workflowId: string;
  workflowVersion: number;
  triggerEventId?: string;
  entityType?: string;
  entityId?: string;
  status: WorkflowRunStatus;
  currentStepId?: string;
  startedAt: string;
  completedAt?: string;
  failedStepId?: string;
  errorCode?: string;
  attemptCount: number;
};
```

---

## 25. Run detail timeline

Timeline gồm:

- Trigger received.
- Conditions evaluated.
- Step started/completed.
- Delay scheduled.
- Approval requested/completed.
- Retry scheduled.
- Run completed/failed.

Payload và variables phải redact dữ liệu nhạy cảm.

---

## 26. Retry policy

```ts
export type WorkflowRetryPolicy = {
  maxAttempts: number;
  strategy: "fixed" | "exponential";
  delaySeconds: number;
  retryableErrorCodes: string[];
};
```

Không retry tự động cho:

- Permission denied.
- Invalid configuration.
- Rejected approval.
- Business conflict.
- Non-idempotent sensitive action.

---

## 27. Manual retry

Yêu cầu:

- Chỉ retry failed step đủ điều kiện.
- Hiển thị impact.
- Dùng idempotency.
- Cho chọn retry step hoặc restart run nếu backend hỗ trợ.
- Audit action.

Không đổi run cũ thành completed; tạo attempt mới hoặc child run.

---

## 28. Pause và emergency stop

Pause workflow:

- Không nhận trigger mới.
- Runs hiện tại theo policy: tiếp tục, dừng an toàn hoặc chờ.

Emergency stop:

- Dành cho gửi nhầm hàng loạt, vòng lặp hoặc rủi ro dữ liệu.
- Cần quyền và reason.
- Có audit và notification tới owner.

---

## 29. Notification automation examples

### Xác nhận đơn hàng

```text
Order created
→ Validate customer channel
→ Send confirmation
→ Record delivery result
→ Create task if failed
```

### Nhắc đánh giá sau 7 ngày

```text
Order completed
→ Wait 7 days
→ Check no refund/open complaint
→ Send review request
→ Record result
```

### Nhắc mua lại

```text
Product repurchase window reached
→ Check customer preference/consent
→ Generate recommendation
→ Human/policy check
→ Send approved message
```

---

## 30. Rate limiting và quiet hours

Workflow gửi tin nhắn phải tuân:

- Per-channel rate limit.
- Per-recipient frequency cap.
- Quiet hours.
- Consent/preferences.
- Deduplication window.
- Provider quota.

Không gửi hàng loạt thiếu suppression và opt-out rules.

---

## 31. API endpoints đề xuất

```text
GET  /api/v1/automations/summary
GET  /api/v1/automations/workflows
POST /api/v1/automations/workflows
GET  /api/v1/automations/workflows/:id
PATCH /api/v1/automations/workflows/:id
POST /api/v1/automations/workflows/:id/validate
POST /api/v1/automations/workflows/:id/simulate
POST /api/v1/automations/workflows/:id/publish
POST /api/v1/automations/workflows/:id/pause
GET  /api/v1/automations/runs
GET  /api/v1/automations/runs/:id
POST /api/v1/automations/runs/:id/retry
GET  /api/v1/automations/approvals
POST /api/v1/automations/approvals/:id/decision
```

---

## 32. Frontend component inventory

```text
AutomationWorkflowCenterPage
├── AutomationSummaryCards
├── WorkflowLibrary
├── WorkflowBuilder
├── TriggerSelector
├── ConditionBuilder
├── ActionSelector
├── WorkflowCanvas
├── WorkflowListAlternative
├── ValidationPanel
├── SimulationPanel
├── ImpactPreviewDialog
├── RunHistoryTable
├── RunTimeline
├── ApprovalQueue
└── EmergencyStopDialog
```

---

## 33. Loading, empty và error states

- Workflow engine lỗi phải hiển thị Unknown, không Healthy.
- Simulation lỗi không được coi là pass.
- Publish lỗi không đổi trạng thái local thành Published.
- Run failure phải giữ step, error code và request ID.
- Không hiển thị secret hoặc full payload.

---

## 34. Security và privacy

- Backend enforce action permission và scope.
- Không trả secret của integration/template.
- Variables nhạy cảm phải mask.
- Không cho workflow tự cấp permission.
- Không cho raw code, SQL hoặc arbitrary webhook destination.
- External action phải dùng registered integration.
- Audit mọi publish, pause, retry và approval.

---

## 35. Audit events

```text
automation.workflow.created
automation.workflow.validated
automation.workflow.published
automation.workflow.paused
automation.run.started
automation.run.completed
automation.run.failed
automation.run.retried
automation.approval.decided
automation.emergency_stop.activated
```

---

## 36. Testing

### Unit

- Condition evaluation.
- Scope matching.
- Idempotency key.
- Loop detection.
- Retry eligibility.
- Quiet-hours calculation.

### Integration

- Duplicate event không gửi trùng.
- Approval timeout không tự continue sai policy.
- Simulation không mutation production.
- Workflow ngoài branch scope bị chặn.
- Emergency stop ngăn trigger mới.

### E2E

- Tạo workflow xác nhận đơn.
- Test bằng fixture.
- Publish.
- Theo dõi run.
- Tạo workflow đánh giá sau 7 ngày.
- Xử lý approval step.
- Retry action tạm lỗi.
- Emergency stop workflow lỗi vòng lặp.

---

## 37. AI-assisted workflow requirements

Nếu dùng AI gợi ý workflow:

- Chỉ tạo draft hoặc suggestion.
- Không tự publish.
- Không tạo raw code hoặc endpoint.
- Phải dùng trigger/action catalog hiện có.
- Phân biệt assumption và rule đã xác nhận.
- Người dùng phải review scope, recipient và sensitive actions.

---

## 38. AI coding agent requirements

AI coding agent phải:

- Không cho raw JavaScript, SQL hoặc arbitrary code trong builder.
- Không publish khi validation/simulation failed hoặc unknown.
- Không retry non-idempotent action mặc định.
- Không bỏ loop detection, quiet hours hoặc recipient frequency cap.
- Không tự tiếp tục approval step khi timeout.
- Bổ sung test cho deduplication, scope, loop prevention, retry và emergency stop.

---

## 39. Acceptance checklist

- [ ] Có Workflow Library và Builder.
- [ ] Có Trigger, Condition và Action Catalog.
- [ ] Có visual canvas và accessible list alternative.
- [ ] Có scope và permission enforcement.
- [ ] Có delay, scheduling và business hours.
- [ ] Có idempotency, deduplication và loop prevention.
- [ ] Có approval và human-in-the-loop.
- [ ] Có simulation, dry run và impact preview.
- [ ] Có versioning và publish workflow.
- [ ] Có run history, timeline và retry.
- [ ] Có pause và emergency stop.
- [ ] Có rate limit, quiet hours và consent controls.
- [ ] Có audit, responsive, accessibility và testing.

---

## 40. Những điều không được làm

- Không cho nhập raw JavaScript, SQL hoặc arbitrary endpoint.
- Không publish workflow chưa validate/test.
- Không gửi trùng do thiếu idempotency.
- Không retry action nhạy cảm không idempotent.
- Không tự duyệt approval step.
- Không bỏ quiet hours, consent hoặc opt-out.
- Không để workflow tự mở rộng permission/scope.
- Không hiển thị secret hoặc full payload.

---

## 41. Kết luận

Automation & Workflow Center của Cynca VLXD phải cho phép tự động hóa quy trình mà vẫn giữ được khả năng kiểm soát, thử nghiệm, audit và dừng khẩn cấp. Mọi workflow phải dựa trên catalog an toàn, có scope, idempotency, loop prevention và human review cho hành động nhạy cảm.

File tiếp theo đề xuất:

```text
README.md
57-AI-Assistant-Governance-Center.md
```