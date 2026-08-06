# Cynca VLXD Admin Dashboard — Security Operations Center Specification

> Phiên bản: 1.0  
> Phạm vi: Giám sát sự kiện bảo mật, điều tra cảnh báo, phản ứng sự cố và quản lý rủi ro truy cập  
> Đối tượng sử dụng: Security, DevOps, SRE, System Admin, Compliance, QA và AI coding agent

---

## 1. Mục tiêu

Security Operations Center giúp Cynca VLXD phát hiện, ưu tiên và xử lý các tín hiệu bảo mật liên quan đến tài khoản, permission, API, integration, dữ liệu và hạ tầng.

Hệ thống phải trả lời:

- Cảnh báo nào là nghiêm trọng và đang ảnh hưởng tới đâu?
- Có hành vi đăng nhập, export hoặc thay đổi quyền bất thường không?
- User, service account hoặc API client nào liên quan?
- Có cần khóa session, rotate credential hoặc cô lập integration không?
- Incident đã được containment và verification chưa?
- Evidence và timeline có đầy đủ để điều tra không?

---

## 2. Information architecture

```text
Security Operations Center
├── Security Overview
├── Alert Queue
├── Investigations
├── Identity & Access Risks
├── API & Integration Risks
├── Data Exposure Signals
├── Vulnerabilities
├── Security Incidents
├── Response Actions
└── Evidence & Audit
```

---

## 3. Permission model

```text
security.center.view
security.alerts.view
security.alerts.manage
security.investigations.manage
security.identity_risks.view
security.sessions.revoke
security.accounts.lock
security.credentials.rotate
security.integrations.isolate
security.incidents.manage
security.evidence.view
security.evidence.export
```

Response actions là quyền sensitive hoặc critical.

---

## 4. Alert model

```ts
export type SecurityAlert = {
  id: string;
  title: string;
  category: "identity" | "authorization" | "api" | "data" | "integration" | "infrastructure" | "malware" | "vulnerability";
  severity: "critical" | "high" | "medium" | "low" | "info";
  status: "new" | "triaged" | "investigating" | "contained" | "resolved" | "false_positive";
  detectedAt: string;
  affectedUsers?: string[];
  affectedServices?: string[];
  affectedBranches?: string[];
  confidence?: number;
  ruleKey: string;
  ownerUserId?: string;
};
```

---

## 5. Security overview

KPI đề xuất:

1. Critical/high alerts mở.
2. Active investigations.
3. Accounts locked.
4. Suspicious exports.
5. Credentials sắp hết hạn.
6. Vulnerabilities quá SLA.

Widgets:

- Critical alert queue.
- Identity-risk trend.
- Permission escalation events.
- Data-exposure signals.
- Security incident timeline.
- Response actions gần đây.

---

## 6. Alert triage

Mỗi alert cần:

- Severity.
- Confidence.
- Rule/source.
- Affected scope.
- Related events.
- Suggested investigation steps.
- Available actions.

Không tự kết luận vi phạm chỉ từ một anomaly signal.

---

## 7. Investigation workflow

```text
Alert received
→ Validate signal
→ Determine scope
→ Collect evidence
→ Assign owner
→ Contain if needed
→ Eradicate cause
→ Recover
→ Verify
→ Close or mark false positive
```

---

## 8. Investigation contract

```ts
export type SecurityInvestigation = {
  id: string;
  title: string;
  severity: "critical" | "high" | "medium" | "low";
  status: "open" | "collecting_evidence" | "contained" | "recovering" | "monitoring" | "closed";
  alertIds: string[];
  ownerUserId: string;
  startedAt: string;
  containedAt?: string;
  closedAt?: string;
  hypothesis?: string;
  confirmedFindings?: string[];
};
```

Phân biệt hypothesis và confirmed finding trong UI.

---

## 9. Identity & access risks

Theo dõi:

- Failed login spike.
- Impossible travel hoặc location anomaly nếu được phép.
- MFA failures.
- Dormant privileged account.
- Permission escalation.
- Cross-branch access attempts.
- Session reuse bất thường.
- Service account không owner.

Không hiển thị raw IP hoặc location chi tiết ngoài permission.

---

## 10. Permission escalation view

Hiển thị:

- User/role.
- Permission trước/sau.
- Actor.
- Approval reference.
- Scope change.
- Time.
- Risk status.

Gán quyền critical thiếu approval phải tạo alert high/critical theo policy.

---

## 11. API & integration risks

Theo dõi:

- API key sử dụng bất thường.
- Signature verification failure.
- Webhook destination đáng ngờ.
- Rate-limit abuse.
- Excessive permission.
- Credential expiry.
- Integration replay bất thường.

---

## 12. Data exposure signals

Ví dụ:

- Export volume bất thường.
- Download dữ liệu restricted.
- Search PII với tần suất cao.
- Cross-scope query bị từ chối.
- Report chứa sensitive columns.
- Public file/object misconfiguration.

Signal cần context và baseline; không tự coi mọi export lớn là vi phạm.

---

## 13. Vulnerability management

Mỗi finding gồm:

- Component/package/image.
- Severity.
- CVE/reference.
- Exposure.
- Exploitability.
- Fix version.
- Owner.
- Due date.
- Exception status.

Không hiển thị exploit detail nhạy cảm cho user không chuyên trách.

---

## 14. Response actions

Các action có thể gồm:

- Revoke sessions.
- Lock account.
- Disable API client.
- Rotate credential.
- Pause integration.
- Disable feature flag.
- Restrict export.
- Block suspicious destination.
- Force password/MFA reset.

Mỗi action cần permission, confirmation, reason và audit.

---

## 15. High-risk action safeguards

- Re-authentication.
- Four-eyes approval nếu cần.
- Dry run/impact preview.
- Scope rõ.
- Rollback hoặc recovery plan.
- Backend confirmation.
- Không optimistic update.

Không cho chạy shell command tùy ý từ UI.

---

## 16. Security incident model

```ts
export type SecurityIncident = {
  id: string;
  title: string;
  severity: "SEV-1" | "SEV-2" | "SEV-3" | "SEV-4";
  status: "investigating" | "contained" | "eradicating" | "recovering" | "monitoring" | "closed";
  affectedAssets: string[];
  dataExposureSuspected: boolean;
  incidentCommanderUserId?: string;
  startedAt: string;
  containedAt?: string;
  closedAt?: string;
};
```

---

## 17. Evidence handling

Evidence có thể gồm:

- Audit events.
- Redacted logs.
- Session metadata.
- Configuration snapshots.
- File hashes.
- Alert timeline.
- Approval records.

Evidence phải immutable hoặc có integrity controls, private và có chain-of-custody metadata.

---

## 18. Detection health

Theo dõi:

- Last event received.
- Rule execution delay.
- Dropped events.
- Failed parsers.
- Disabled rules.
- Coverage gaps.

Không coi không có alert là an toàn khi detection pipeline lỗi.

---

## 19. API endpoints đề xuất

```text
GET  /api/v1/security/summary
GET  /api/v1/security/alerts
GET  /api/v1/security/alerts/:id
PATCH /api/v1/security/alerts/:id
GET  /api/v1/security/investigations
POST /api/v1/security/investigations
PATCH /api/v1/security/investigations/:id
POST /api/v1/security/actions/revoke-sessions
POST /api/v1/security/actions/lock-account
POST /api/v1/security/actions/rotate-credential
POST /api/v1/security/actions/isolate-integration
GET  /api/v1/security/vulnerabilities
GET  /api/v1/security/incidents
```

---

## 20. Frontend component inventory

```text
SecurityOperationsCenterPage
├── SecuritySummaryCards
├── SecurityAlertQueue
├── AlertDetailDrawer
├── InvestigationWorkspace
├── IdentityRiskPanel
├── PermissionEscalationTable
├── DataExposurePanel
├── VulnerabilityTable
├── ResponseActionDialog
├── SecurityIncidentTimeline
└── EvidencePanel
```

---

## 21. Loading, empty và error states

- Detection pipeline lỗi phải hiển thị warning critical.
- Empty alert queue không đồng nghĩa an toàn khi telemetry unknown.
- Response action lỗi không báo thành công.
- Không hiển thị raw secret, token hoặc stack trace.
- Có request ID cho lỗi điều tra.

---

## 22. Security và privacy

- Backend enforce environment, branch và field scope.
- Evidence chỉ trả cho người được phép.
- PII, IP và location được mask theo policy.
- Không ghi alert/evidence content vào analytics.
- Response action và evidence export phải audit.
- Session/credential secret không bao giờ trả về client.

---

## 23. Audit events

```text
security.alert.triaged
security.investigation.created
security.account.locked
security.sessions.revoked
security.credential.rotated
security.integration.isolated
security.incident.created
security.incident.contained
security.evidence.exported
```

---

## 24. Testing

### Unit

- Severity mapping.
- Confidence display.
- Permission-scope masking.
- Response-action eligibility.

### Integration

- Lock account thu hồi session.
- Credential rotation không trả secret cũ.
- Detection outage hiển thị unknown.
- Evidence export đúng scope.

### E2E

- Mở alert permission escalation.
- Tạo investigation.
- Revoke sessions.
- Isolate integration.
- Chuyển incident sang contained.
- Viewer thiếu quyền không thấy evidence.

---

## 25. AI-assisted investigation requirements

Nếu dùng AI:

- Chỉ tóm tắt evidence đã redact.
- Phân biệt fact, hypothesis và recommendation.
- Không tự khóa tài khoản hoặc rotate credential.
- Không gửi raw log, PII hoặc secret tới provider chưa phê duyệt.
- Human review bắt buộc.

---

## 26. AI coding agent requirements

AI coding agent phải:

- Không coi không có alert là an toàn khi detector lỗi.
- Không hiển thị secret hoặc raw evidence ngoài quyền.
- Không tự chạy response action.
- Không optimistic update action nhạy cảm.
- Bổ sung test cho privilege escalation, evidence masking, detector outage và session revoke.

---

## 27. Acceptance checklist

- [ ] Có Alert Queue và triage.
- [ ] Có Investigation workflow.
- [ ] Có Identity, API, integration và data-exposure risks.
- [ ] Có vulnerability management.
- [ ] Có response actions với safeguards.
- [ ] Có security incident và evidence handling.
- [ ] Có detection health.
- [ ] Có permission, audit, responsive và accessibility.
- [ ] Có testing và Visual QA đầy đủ.

---

## 28. Những điều không được làm

- Không kết luận vi phạm chỉ từ anomaly.
- Không hiển thị secret, raw token hoặc evidence ngoài quyền.
- Không chạy shell tùy ý.
- Không tự khóa tài khoản hoặc isolate service thiếu confirmation.
- Không coi empty alert queue là an toàn khi pipeline lỗi.
- Không gửi evidence vào analytics.

---

## 29. Kết luận

Security Operations Center của Cynca VLXD phải hỗ trợ phát hiện, điều tra, containment và recovery với dữ liệu đã được kiểm soát. Mọi phản ứng nhạy cảm phải có permission, impact preview, confirmation, audit và khả năng xác minh kết quả.

File tiếp theo:

```text
53-Release-Change-Management-Center.md
```