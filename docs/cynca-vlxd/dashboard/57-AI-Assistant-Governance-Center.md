# Cynca VLXD Admin Dashboard — AI Assistant Governance Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý AI Assistant, nguồn dữ liệu, quyền truy cập, prompt, đánh giá chất lượng và kiểm soát rủi ro  
> Đối tượng sử dụng: Product, Data, Security, Compliance, Operations, Engineering, QA và AI coding agent

---

## 1. Mục tiêu

AI Assistant Governance Center giúp Cynca VLXD kiểm soát cách AI được sử dụng trong Dashboard, bao gồm trợ lý tìm kiếm, tóm tắt, gợi ý xử lý, soạn phản hồi và hỗ trợ phân tích.

Hệ thống phải trả lời:

- AI Assistant nào đang được bật và phục vụ vai trò nào?
- AI được phép truy cập nguồn dữ liệu nào?
- Prompt, model, tool và policy nào đang áp dụng?
- Có nội dung nào bị chặn, từ chối hoặc cần human review?
- Chất lượng câu trả lời có đạt yêu cầu không?
- Có rò rỉ dữ liệu, hallucination hoặc hành động vượt quyền không?
- Phiên bản nào đang chạy và có thể rollback không?

---

## 2. Information architecture

```text
AI Assistant Governance Center
├── Tổng quan
├── Assistants
├── Data Sources
├── Prompt & Policy Registry
├── Tool Permissions
├── Evaluations
├── Conversation Reviews
├── Safety Incidents
├── Cost & Usage
├── Version History
└── Audit Log
```

---

## 3. Permission model

```text
ai.center.view
ai.assistants.view
ai.assistants.manage
ai.prompts.view
ai.prompts.manage
ai.data_sources.view
ai.data_sources.manage
ai.tools.manage
ai.evaluations.view
ai.evaluations.manage
ai.conversations.review
ai.safety_incidents.manage
ai.usage.view
ai.versions.publish
ai.versions.rollback
```

Publish, tool permission, data-source permission và rollback là quyền nhạy cảm.

---

## 4. AI assistant contract

```ts
export type AIAssistantDefinition = {
  id: string;
  key: string;
  name: string;
  description?: string;
  status: "draft" | "testing" | "review" | "published" | "paused" | "deprecated";
  supportedRoles: string[];
  allowedScopes: Array<"system" | "branch" | "warehouse" | "team" | "assigned" | "self">;
  modelPolicyId: string;
  promptVersionId: string;
  dataSourceIds: string[];
  toolIds: string[];
  humanReviewMode: "none" | "recommended" | "required";
  ownerUserId: string;
  version: number;
};
```

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Assistants published.
2. Sessions 24 giờ.
3. Human-review rate.
4. Blocked responses.
5. Safety incidents mở.
6. Evaluation pass rate.

Widgets:

- Assistant health.
- Quality trend.
- Top refusal categories.
- Tool-call failure.
- Data-source access warnings.
- Cost/usage trend.

---

## 6. Use cases được phép

Ví dụ:

- Tóm tắt Dashboard theo branch scope.
- Tóm tắt ticket và đề xuất bước tiếp theo.
- Soạn draft phản hồi khách hàng.
- Giải thích KPI và xu hướng.
- Tìm tài liệu hướng dẫn nội bộ.
- Gợi ý mapping import.
- Gợi ý workflow draft.

AI không được mặc định:

- Phê duyệt refund.
- Điều chỉnh tồn kho.
- Gán permission.
- Publish workflow.
- Deploy release.
- Xóa dữ liệu.

---

## 7. Data source registry

```ts
export type AIDataSource = {
  id: string;
  key: string;
  name: string;
  type: "database_view" | "search_index" | "document_collection" | "api" | "knowledge_base";
  classification: "public" | "internal" | "confidential" | "restricted";
  allowedRoles: string[];
  allowedScopes: string[];
  freshnessSlaMinutes?: number;
  ownerUserId: string;
  status: "active" | "paused" | "deprecated";
};
```

AI chỉ được truy cập dữ liệu đã permission-filtered từ backend.

---

## 8. Prompt registry

Mỗi prompt gồm:

- Prompt key.
- Version.
- Purpose.
- System instructions.
- Allowed tools.
- Data policy.
- Output schema.
- Refusal rules.
- Owner.
- Evaluation suite.

Không chỉnh prompt production trực tiếp thiếu version, review và test.

---

## 9. Prompt version contract

```ts
export type AIPromptVersion = {
  id: string;
  assistantId: string;
  version: number;
  status: "draft" | "testing" | "approved" | "published" | "retired";
  promptTemplate: string;
  outputSchemaId?: string;
  createdBy: string;
  approvedBy?: string;
  createdAt: string;
  publishedAt?: string;
};
```

Prompt chứa secret hoặc credential bị cấm.

---

## 10. Tool permissions

Mỗi tool cần khai báo:

- Tool key.
- Read hoặc write.
- Required permission.
- Scope rules.
- Confirmation requirement.
- Human approval requirement.
- Idempotency.
- Audit behavior.

Tool write-action nhạy cảm phải mặc định bị chặn hoặc yêu cầu human approval.

---

## 11. Tool-call lifecycle

```text
AI proposes tool call
→ Backend validates permission and scope
→ User confirmation nếu cần
→ Approval nếu nhạy cảm
→ Execute
→ Verify result
→ Audit
→ Return structured response
```

Không tin tool arguments do model sinh ra mà thiếu schema validation.

---

## 12. Human review

Human review bắt buộc với:

- Nội dung gửi ra ngoài có rủi ro.
- Thay đổi trạng thái nghiệp vụ quan trọng.
- Dữ liệu tài chính hoặc tồn kho.
- Quyền truy cập.
- Nội dung pháp lý hoặc tuân thủ.
- Safety escalation.

Review UI hiển thị source, draft, confidence, warnings và action cuối cùng.

---

## 13. Grounding và citations

AI response dựa trên dữ liệu nội bộ cần:

- Chỉ dùng source được phép.
- Hiển thị nguồn hoặc entity reference phù hợp.
- Phân biệt fact và inference.
- Ghi data freshness.
- Không bịa nguồn.

Nếu thiếu nguồn, AI phải nói rõ chưa đủ dữ liệu.

---

## 14. Conversation review

Review queue gồm:

- Session ID.
- Assistant/version.
- Role/scope đã mask.
- User feedback.
- Safety flags.
- Tool calls.
- Reviewer status.

Không cho reviewer xem conversation ngoài permission hoặc purpose đã duyệt.

---

## 15. Evaluation framework

Đánh giá:

```text
Groundedness
Accuracy
Completeness
Permission compliance
Scope compliance
Safety
Helpfulness
Format compliance
Tool-call correctness
Latency
```

Không publish version nếu evaluation critical còn failed hoặc unknown.

---

## 16. Evaluation case contract

```ts
export type AIEvaluationCase = {
  id: string;
  suiteId: string;
  inputFixture: string;
  expectedBehavior: string;
  requiredSources?: string[];
  prohibitedActions?: string[];
  tags: string[];
  sensitivity: "normal" | "restricted";
};
```

Fixtures không dùng dữ liệu production thật.

---

## 17. Safety incidents

Ví dụ:

- Rò rỉ dữ liệu ngoài scope.
- Bịa số liệu tài chính.
- Tool call vượt permission.
- Nội dung gửi khách hàng sai nghiêm trọng.
- Prompt injection thành công.
- Trích xuất secret.

Workflow:

```text
Detected
→ Triage
→ Pause assistant/version nếu cần
→ Contain
→ Investigate
→ Fix prompt/tool/data policy
→ Re-evaluate
→ Republish
```

---

## 18. Prompt injection controls

- Tách system instructions khỏi content nguồn.
- Không cho tài liệu nguồn thay đổi policy.
- Tool allowlist.
- Schema validation.
- Output sanitization.
- Secret isolation.
- Content classification.
- Suspicious instruction detection.

Không cho AI thực thi câu lệnh từ nội dung tài liệu hoặc ticket.

---

## 19. Cost & usage

Theo dõi:

- Requests.
- Input/output tokens.
- Cost bucket.
- Latency.
- Tool calls.
- Cache hit.
- Failure/refusal rate.
- Usage theo assistant/role/module.

Không gửi prompt hoặc conversation content vào analytics thông thường.

---

## 20. Versioning và rollout

Lifecycle:

```text
Draft
→ Offline evaluation
→ Staging test
→ Human review
→ Limited rollout
→ Full rollout
→ Monitor
→ Rollback/retire
```

Rollout theo role, branch hoặc tỷ lệ nhưng không dùng làm authorization.

---

## 21. API endpoints đề xuất

```text
GET  /api/v1/ai-governance/summary
GET  /api/v1/ai-governance/assistants
POST /api/v1/ai-governance/assistants
PATCH /api/v1/ai-governance/assistants/:id
GET  /api/v1/ai-governance/prompts
POST /api/v1/ai-governance/prompts
POST /api/v1/ai-governance/prompts/:id/evaluate
POST /api/v1/ai-governance/prompts/:id/publish
POST /api/v1/ai-governance/assistants/:id/pause
GET  /api/v1/ai-governance/evaluations
GET  /api/v1/ai-governance/reviews
GET  /api/v1/ai-governance/incidents
POST /api/v1/ai-governance/versions/:id/rollback
```

---

## 22. Frontend component inventory

```text
AIAssistantGovernanceCenterPage
├── AISummaryCards
├── AssistantDirectory
├── AssistantDetailPage
├── DataSourceAccessPanel
├── PromptVersionEditor
├── ToolPermissionMatrix
├── EvaluationSuiteTable
├── ConversationReviewQueue
├── SafetyIncidentPanel
├── UsageCostPanel
├── RolloutPanel
└── VersionHistory
```

---

## 23. Security và privacy

- Backend enforce role, scope và field permission trước khi AI nhận dữ liệu.
- Không gửi secret, token hoặc raw restricted data tới provider chưa phê duyệt.
- Conversation retention theo policy.
- Tool write-action cần confirmation/approval.
- Không dùng AI output làm source of truth duy nhất.
- Audit prompt publish, tool changes, sensitive reviews và rollback.

---

## 24. Audit events

```text
ai.assistant.created
ai.assistant.published
ai.assistant.paused
ai.prompt.updated
ai.prompt.evaluated
ai.tool_permission.updated
ai.review.completed
ai.safety_incident.created
ai.version.rolled_back
```

---

## 25. Testing

### Unit

- Scope filtering.
- Tool eligibility.
- Output-schema validation.
- Citation/source mapping.
- Redaction.

### Integration

- Cross-branch query bị chặn.
- Tool write cần confirmation.
- Prompt injection không đổi policy.
- Evaluation failed chặn publish.
- Rollback khôi phục version trước.

### E2E

- Tạo assistant draft.
- Gán data sources và tools.
- Chạy evaluation suite.
- Limited rollout.
- Review conversation bị flag.
- Pause assistant.
- Fix prompt và republish.

---

## 26. AI coding agent requirements

AI coding agent phải:

- Không cho model truy cập dữ liệu trước backend authorization.
- Không cho tool write chạy trực tiếp thiếu confirmation/approval.
- Không publish khi evaluation critical failed hoặc unknown.
- Không lưu secret trong prompt.
- Không dùng conversation production làm fixture thiếu kiểm soát.
- Bổ sung test cho prompt injection, scope leakage, hallucination handling và rollback.

---

## 27. Acceptance checklist

- [ ] Có Assistant Directory.
- [ ] Có Data Source Registry.
- [ ] Có Prompt & Policy Registry.
- [ ] Có Tool Permission Matrix.
- [ ] Có grounding và citations.
- [ ] Có human review.
- [ ] Có evaluation framework.
- [ ] Có conversation review và safety incidents.
- [ ] Có prompt-injection controls.
- [ ] Có usage/cost và rollout/versioning.
- [ ] Có permission, audit, responsive và accessibility.
- [ ] Có testing và Visual QA đầy đủ.

---

## 28. Những điều không được làm

- Không cho AI tự approve, deploy, refund, adjust stock hoặc grant permission.
- Không gửi restricted data hoặc secret tới provider chưa phê duyệt.
- Không để tài liệu nguồn ghi đè system policy.
- Không publish prompt thiếu evaluation.
- Không coi AI output là dữ liệu chính thức thiếu verification.
- Không dùng feature rollout thay authorization.
- Không ẩn việc câu trả lời được AI tạo.

---

## 29. Kết luận

AI Assistant Governance Center của Cynca VLXD phải bảo đảm AI hữu ích nhưng luôn hoạt động trong permission, scope, data policy và human-control rõ ràng. Mọi assistant, prompt, tool và model version phải được đánh giá, version hóa, giám sát và có thể pause hoặc rollback khi phát hiện rủi ro.

File tiếp theo:

```text
58-Search-Knowledge-Management-Center.md
```