# Cynca VLXD Admin Dashboard — Customer Identity & Profile Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý hồ sơ khách hàng, định danh, hợp nhất dữ liệu, consent, segmentation và lịch sử tương tác  
> Đối tượng sử dụng: Sales, Customer Service, Marketing Operations, Data, Security, Compliance, QA và AI coding agent

---

## 1. Mục tiêu

Customer Identity & Profile Center giúp Cynca VLXD duy trì một hồ sơ khách hàng đáng tin cậy, thống nhất và có thể sử dụng an toàn trong bán hàng, chăm sóc khách hàng, báo cáo và tự động hóa.

Hệ thống phải trả lời:

- Đây có phải cùng một khách hàng xuất hiện ở nhiều kênh không?
- Hồ sơ nào là bản ghi chính thức?
- Có thông tin nào trùng, thiếu, sai hoặc xung đột?
- Khách hàng đã đồng ý nhận thông tin qua kênh nào?
- Ai đang phụ trách khách hàng và thuộc chi nhánh nào?
- Lịch sử đơn hàng, ticket, thanh toán và tương tác ra sao?
- Có thể merge, split hoặc cập nhật hồ sơ mà vẫn giữ audit không?

---

## 2. Information architecture

```text
Customer Identity & Profile Center
├── Tổng quan
├── Customer Directory
├── Unified Profiles
├── Identity Resolution
├── Duplicate Review Queue
├── Consent & Preferences
├── Segments
├── Interaction Timeline
├── Data Quality Issues
└── Audit History
```

---

## 3. Permission model

```text
customers.center.view
customers.directory.view
customers.profile.view
customers.profile.update
customers.pii.view
customers.pii.update
customers.identity_resolution.view
customers.identity_resolution.manage
customers.merge
customers.split
customers.consent.view
customers.consent.manage
customers.segments.view
customers.segments.manage
customers.audit.view
```

Merge, split và cập nhật PII là quyền nhạy cảm.

---

## 4. Customer profile contract

```ts
export type UnifiedCustomerProfile = {
  id: string;
  customerCode: string;
  customerType: "individual" | "business";
  displayName: string;
  companyName?: string;
  taxCode?: string;
  emailMasked?: string;
  phoneMasked?: string;
  branchId?: string;
  assignedUserId?: string;
  lifecycleStatus: "prospect" | "active" | "inactive" | "blocked";
  identityConfidence?: number;
  sourceRecordIds: string[];
  consentSummary: CustomerConsentSummary;
  qualityStatus: "good" | "needs_review" | "conflict" | "unknown";
  createdAt: string;
  updatedAt: string;
};
```

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Unified profiles.
2. Duplicate candidates.
3. Profiles thiếu contact hợp lệ.
4. Consent conflicts.
5. Identity matches chờ review.
6. Customer-quality issues mở.

Widgets:

- Duplicate trend.
- Identity-resolution queue.
- Consent status.
- Profiles by lifecycle.
- Data-quality issues.
- Recently merged profiles.

---

## 6. Customer Directory

Cột đề xuất:

- Customer code.
- Tên/công ty.
- Loại khách hàng.
- Branch.
- Assigned owner.
- Contact đã mask.
- Lifecycle status.
- Quality status.
- Last interaction.
- Actions.

Filter:

- Customer type.
- Branch.
- Owner.
- Lifecycle.
- Consent status.
- Quality status.
- Duplicate candidate.

---

## 7. Unified Profile page

Tabs:

```text
Overview
Identity & Sources
Orders
Payments
Tickets
Interactions
Consent
Segments
Data Quality
Audit
```

Header:

- Tên khách hàng.
- Customer code.
- Status.
- Branch/owner.
- Identity confidence.
- Quality warning.
- Primary actions.

---

## 8. Source records

Nguồn có thể gồm:

- Zalo Mini App.
- Website.
- Sales nhập tay.
- Import CSV/XLSX.
- Order system.
- Customer Service.
- Payment system.
- External CRM.

Mỗi source record phải có source, external ID, createdAt, updatedAt, trust level và sync status.

---

## 9. Identity resolution

Các tín hiệu match:

- Customer code.
- Tax code.
- Verified phone.
- Verified email.
- Zalo user identifier.
- Shipping address chuẩn hóa.
- Company name + address.
- Historical link.

Không dùng tên hiển thị đơn lẻ làm tín hiệu merge đủ mạnh.

---

## 10. Identity match contract

```ts
export type CustomerIdentityMatch = {
  id: string;
  sourceProfileId: string;
  candidateProfileId: string;
  confidence: number;
  matchedSignals: string[];
  conflictingSignals?: string[];
  status: "suggested" | "reviewing" | "approved" | "rejected" | "expired";
  createdAt: string;
  reviewedBy?: string;
};
```

---

## 11. Duplicate Review Queue

Mỗi candidate hiển thị:

- Hai hoặc nhiều hồ sơ.
- Matching signals.
- Conflicts.
- Confidence.
- Orders/tickets liên quan.
- Consent differences.
- Suggested master profile.
- Available actions.

Không auto-merge hồ sơ business hoặc có dữ liệu tài chính thiếu review.

---

## 12. Merge workflow

```text
Select candidates
→ Compare fields
→ Select master values
→ Review consent and ownership
→ Preview affected records
→ Confirm
→ Execute merge
→ Verify references
→ Audit
```

Merge phải giữ source records và lịch sử thay đổi.

---

## 13. Merge contract

```ts
export type CustomerMergeRequest = {
  id: string;
  masterProfileId: string;
  mergedProfileIds: string[];
  fieldResolution: Record<string, string | null>;
  status: "draft" | "review" | "approved" | "running" | "verifying" | "completed" | "failed";
  requestedBy: string;
  approvedBy?: string;
  reason: string;
};
```

---

## 14. Split workflow

Dùng khi hồ sơ đã merge sai hoặc nhiều người dùng chung contact.

Yêu cầu:

- Chọn source records cần tách.
- Chọn orders/tickets/interactions liên quan.
- Review consent.
- Preview references.
- Approval nếu có tài chính hoặc dispute.
- Verification sau split.

Không split chỉ bằng cách xóa liên kết thiếu audit.

---

## 15. Field conflict resolution

Conflict examples:

- Hai số điện thoại khác nhau.
- Tên doanh nghiệp khác nhau.
- Tax code không khớp.
- Branch owner khác nhau.
- Consent status khác nhau.

UI phải hiển thị source, updatedAt, verification status và trust level cho từng giá trị.

---

## 16. Consent & preferences

```ts
export type CustomerConsentSummary = {
  marketingEmail: "granted" | "denied" | "unknown";
  marketingZalo: "granted" | "denied" | "unknown";
  serviceMessages: "allowed" | "restricted";
  profiling?: "granted" | "denied" | "unknown";
  updatedAt?: string;
};
```

Consent không được suy từ việc khách từng mua hàng.

---

## 17. Communication preferences

Theo dõi:

- Preferred channel.
- Quiet hours.
- Language.
- Branch contact preference.
- Opt-out reason.
- Frequency cap.

Automation phải kiểm tra preferences trước khi gửi.

---

## 18. Interaction timeline

Events:

- Order created/completed/cancelled.
- Ticket opened/resolved.
- Call/message.
- Quote sent.
- Payment received.
- Review request.
- Consent updated.
- Profile merged/split.

Timeline phải lọc theo permission và không hiển thị nội dung nhạy cảm dư thừa.

---

## 19. Customer segments

Segments có thể theo:

- Lifecycle.
- Purchase frequency.
- Product category.
- Branch.
- Outstanding debt.
- Service issue history.
- Consent/preferences.

Không dùng sensitive attributes hoặc inferred characteristics thiếu policy hợp lệ.

---

## 20. Segment contract

```ts
export type CustomerSegment = {
  id: string;
  name: string;
  description?: string;
  ruleSetId: string;
  status: "draft" | "active" | "paused" | "archived";
  estimatedSize?: number;
  ownerUserId: string;
  allowedUses: string[];
  updatedAt: string;
};
```

---

## 21. Profile quality rules

Ví dụ:

- Customer code unique.
- Business customer cần company name.
- Verified contact format hợp lệ.
- Tax code không trùng bất hợp lý.
- Branch/owner tồn tại.
- Consent evidence đầy đủ.
- Không có source conflict critical.

Quality service lỗi phải hiển thị Unknown, không Good.

---

## 22. PII masking

- Phone: chỉ hiện một phần.
- Email: mask local part.
- Address: rút gọn theo role.
- Tax code: theo permission.
- Notes: không hiển thị ngoài purpose.

Reveal PII nếu hỗ trợ phải có permission, reason và audit.

---

## 23. API endpoints đề xuất

```text
GET  /api/v1/customers/summary
GET  /api/v1/customers
GET  /api/v1/customers/:id
PATCH /api/v1/customers/:id
GET  /api/v1/customers/identity-matches
POST /api/v1/customers/merge-requests
POST /api/v1/customers/merge-requests/:id/approve
POST /api/v1/customers/merge-requests/:id/execute
POST /api/v1/customers/:id/split
GET  /api/v1/customers/:id/interactions
GET  /api/v1/customer-segments
POST /api/v1/customer-segments
```

---

## 24. Frontend component inventory

```text
CustomerIdentityProfileCenterPage
├── CustomerSummaryCards
├── CustomerDirectoryTable
├── UnifiedProfilePage
├── SourceRecordPanel
├── IdentityMatchQueue
├── ProfileComparisonView
├── MergeWizard
├── SplitWizard
├── ConsentPreferencePanel
├── InteractionTimeline
├── SegmentBuilder
└── ProfileQualityPanel
```

---

## 25. Loading, empty và error states

- Identity engine lỗi không hiển thị `Không có duplicate` như kết luận chắc chắn.
- Merge failure giữ request ID và trạng thái trước merge.
- Consent service lỗi không mặc định granted.
- Profile detail lỗi không hiển thị cache của khách khác.
- Không hiển thị raw PII trong error logs.

---

## 26. Security và privacy

- Backend enforce branch, owner, purpose và field permission.
- Không trả toàn bộ PII rồi mask bằng CSS.
- Merge/split cần idempotency và audit.
- Consent history append-only hoặc versioned.
- Segment export theo permission và allowed use.
- Không dùng customer profile để suy diễn thuộc tính nhạy cảm thiếu policy.

---

## 27. Audit events

```text
customer.profile.created
customer.profile.updated
customer.pii.revealed
customer.identity_match.reviewed
customer.merge.requested
customer.merge.completed
customer.split.completed
customer.consent.updated
customer.segment.created
```

---

## 28. Testing

### Unit

- Identity confidence mapping.
- Field conflict resolution.
- Consent precedence.
- PII masking.
- Segment rule validation.

### Integration

- Cross-branch profile bị chặn.
- Merge giữ source references.
- Consent denied không bị ghi đè bởi unknown.
- Split phân bổ đúng orders/tickets.
- Reveal PII có audit.

### E2E

- Tìm duplicate candidate.
- So sánh hồ sơ.
- Tạo merge request.
- Approve và execute.
- Verify timeline và references.
- Cập nhật consent.
- Tạo segment theo branch.

---

## 29. AI-assisted identity requirements

Nếu dùng AI:

- Chỉ gợi ý duplicate hoặc field normalization.
- Không tự merge/split.
- Không gửi PII tới provider chưa phê duyệt.
- Hiển thị confidence và signals.
- Human review bắt buộc.

---

## 30. AI coding agent requirements

AI coding agent phải:

- Không auto-merge dựa trên tên hoặc một contact chưa verified.
- Không mặc định consent là granted.
- Không trả PII rộng rồi mask client-side.
- Không split/merge thiếu idempotency, impact preview và audit.
- Bổ sung test cho conflict fields, cross-branch access, consent precedence và rollback failure.

---

## 31. Acceptance checklist

- [ ] Có Customer Directory và Unified Profiles.
- [ ] Có source records và identity resolution.
- [ ] Có Duplicate Review Queue.
- [ ] Có merge/split workflow.
- [ ] Có field-conflict resolution.
- [ ] Có consent và communication preferences.
- [ ] Có interaction timeline.
- [ ] Có segment management.
- [ ] Có data-quality rules và PII masking.
- [ ] Có permission, audit, responsive và accessibility.
- [ ] Có testing và Visual QA đầy đủ.

---

## 32. Những điều không được làm

- Không auto-merge hồ sơ thiếu confidence và review.
- Không dùng tên làm khóa định danh duy nhất.
- Không mặc định consent từ lịch sử mua hàng.
- Không hiển thị PII ngoài quyền.
- Không merge/split thiếu impact preview.
- Không xóa source record hoặc lịch sử merge.
- Không dùng segment cho mục đích ngoài allowed use.

---

## 33. Kết luận

Customer Identity & Profile Center của Cynca VLXD phải tạo ra hồ sơ khách hàng thống nhất nhưng vẫn giữ source, consent, ownership và audit rõ ràng. Identity resolution cần dựa trên tín hiệu đáng tin cậy, human review và quy trình merge/split có thể xác minh.

File tiếp theo:

```text
60-Executive-Planning-OKR-Center.md
```