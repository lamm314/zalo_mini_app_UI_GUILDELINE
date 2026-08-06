# Cynca VLXD Admin Dashboard — Finance Dashboard Specification

> Phiên bản: 1.0  
> Phạm vi: Dashboard tài chính vận hành dành cho kế toán, quản lý tài chính và Ban lãnh đạo có quyền  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Finance, Accounting, QA, Security và AI coding agent  
> Tài liệu liên quan: `05-KPI-Cards.md`, `06-Charts.md`, `16-Data-Contracts.md`, `23-Security-Privacy.md`, `27-Role-Based-Dashboard.md`, `32-Executive-Dashboard.md`

---

## 1. Mục tiêu

Finance Dashboard giúp đội tài chính Cynca VLXD theo dõi doanh thu, thu tiền, công nợ, hoàn tiền, nghĩa vụ thanh toán và các ngoại lệ cần xử lý.

Dashboard phải trả lời nhanh:

- Doanh thu ghi nhận trong kỳ là bao nhiêu?
- Bao nhiêu tiền đã thu, chưa thu và quá hạn?
- Công nợ tập trung ở khách hàng hoặc chi nhánh nào?
- Thanh toán thất bại, hoàn tiền và đối soát đang ở trạng thái nào?
- Dòng tiền thu dự kiến trong ngắn hạn ra sao?
- Có chênh lệch nào giữa đơn hàng, thanh toán, hóa đơn và báo cáo nguồn?
- Dữ liệu tài chính có đủ mới và đã chốt hay chưa?

Finance Dashboard là màn hình tổng hợp vận hành. Nó không thay thế sổ kế toán, báo cáo tài chính pháp định, phần mềm kế toán hoặc quy trình phê duyệt thanh toán.

---

## 2. Các biến thể vai trò

### Finance Manager / CFO View

Trọng tâm:

- Doanh thu và lợi nhuận.
- Thu tiền và công nợ.
- Dòng tiền dự kiến.
- Rủi ro tài chính.
- So sánh chi nhánh.
- Báo cáo và đối soát.

### Accountant View

Trọng tâm:

- Thanh toán cần đối soát.
- Công nợ đến hạn.
- Thanh toán thất bại.
- Hoàn tiền chờ xử lý.
- Hóa đơn hoặc chứng từ thiếu.
- Tác vụ kế toán trong ngày.

### Read-only Finance Viewer

- Xem KPI, chart và bảng tổng hợp.
- Không có mutation actions.
- Export chỉ khi có quyền riêng.

Các biến thể dùng chung component registry, khác permission, scope và thứ tự widget.

---

## 3. Nguyên tắc thiết kế

1. Mọi chỉ số phải có định nghĩa nghiệp vụ rõ.
2. Phân biệt doanh thu, tiền đã thu và dòng tiền.
3. Không dùng số đơn hàng thay cho số liệu kế toán chính thức.
4. Mọi số tiền phải có currency và kỳ dữ liệu.
5. Dữ liệu chưa chốt phải có trạng thái rõ.
6. Không hiển thị số `0` khi API lỗi hoặc chưa tải.
7. Không tự động xác nhận thanh toán hoặc hoàn tiền từ Dashboard thiếu workflow.
8. Không cho người thiếu quyền xem giá vốn, lợi nhuận hoặc công nợ chi tiết.
9. Mọi export phải giữ nguyên scope và permission.
10. Không để một widget lỗi làm sập toàn Dashboard.

---

## 4. Layout — Finance Manager

Desktop 1440px:

```text
Page Toolbar
Financial Status Summary
KPI Grid 4–6 cards
Revenue & Collection Trend       8 columns
Cash / Debt Health               4 columns
Debt Aging                       7 columns
Exceptions & Risks               5 columns
Branch Financial Performance     7 columns
Payment Reconciliation           5 columns
Refunds / Failed Payments        6 columns
Upcoming Cash Inflows            6 columns
Recent Financial Activity       12 columns
```

---

## 5. Layout — Accountant

Desktop 1440px:

```text
Page Toolbar
Daily Finance Task Summary
KPI Grid 4 cards
Reconciliation Queue             7 columns
Debt Due / Overdue               5 columns
Failed Payments                  6 columns
Refund Requests                  6 columns
Missing Documents                6 columns
Recent Transactions              6 columns
```

Accountant View ưu tiên queue và exceptions hơn chart tổng hợp.

---

## 6. Page toolbar

### Finance Manager

- Date range.
- Branch filter.
- Payment method.
- Currency nếu tương lai đa tiền tệ.
- Compare mode.
- Data status: provisional/closed.
- Export finance report.
- Refresh.

### Accountant

- Date range.
- Branch hoặc business unit.
- Payment status.
- Reconciliation status.
- Due status.
- Export operational list nếu có quyền.

Không hiển thị action tạo hoặc chỉnh bút toán nếu Dashboard không phải nơi thực hiện nghiệp vụ đó.

---

## 7. Data status model

```ts
export type FinanceDataStatus =
  | "provisional"
  | "reconciled"
  | "closed"
  | "restated";
```

### Provisional

Dữ liệu tạm tính, chưa đối soát đầy đủ.

### Reconciled

Đã đối soát với nguồn thanh toán hoặc hệ thống liên quan.

### Closed

Kỳ đã chốt theo quy trình nội bộ.

### Restated

Số liệu đã điều chỉnh sau khi chốt.

Dashboard phải hiển thị trạng thái này gần date range hoặc KPI tài chính.

---

## 8. KPI chính

KPI đề xuất:

1. Doanh thu ghi nhận.
2. Tiền đã thu.
3. Phải thu.
4. Công nợ quá hạn.
5. Tỷ lệ thu tiền.
6. Hoàn tiền trong kỳ.

KPI tùy chọn theo quyền:

- Lợi nhuận gộp.
- Biên lợi nhuận gộp.
- DSO.
- Payment failure rate.
- Giá trị chưa đối soát.
- Dòng tiền thuần vận hành nếu backend có nguồn đáng tin cậy.

---

## 9. Financial KPI anatomy

Mỗi KPI gồm:

- Label.
- Giá trị đầy đủ.
- Compact value nếu cần.
- Trend semantic.
- Kỳ so sánh.
- Data status.
- Updated time.
- Tooltip định nghĩa.
- Drill-down.

Ví dụ:

```text
Công nợ quá hạn
1,84 tỷ ₫
+8,2% so với kỳ trước
Dữ liệu đã đối soát đến 17:00 06/08/2026
```

Công nợ tăng phải được xem là negative semantic dù số tăng dương.

---

## 10. KPI definitions

### Doanh thu ghi nhận

Phải xác định rõ:

- Theo đơn hoàn thành, hóa đơn, hoặc nguyên tắc ghi nhận nào.
- Có bao gồm thuế hay không.
- Có trừ hủy, trả hàng, chiết khấu hay không.

### Tiền đã thu

- Chỉ gồm payment đã xác nhận.
- Không coi payment pending là đã thu.

### Phải thu

- Số còn phải thu tại thời điểm snapshot.

### Tỷ lệ thu tiền

```text
Collected Amount / Amount Due
```

Công thức thực tế phải được Finance phê duyệt.

---

## 11. Revenue & Collection Trend

Chart chính gồm:

- Doanh thu ghi nhận.
- Tiền đã thu.
- Amount due tùy chọn.
- Kỳ so sánh.

Controls:

- Day/week/month.
- Revenue/collection toggle.
- Branch.
- Payment method.

Tooltip:

- Giá trị đầy đủ.
- Chênh lệch.
- Data status.
- Số giao dịch nếu cần.

---

## 12. Cash & Debt Health

Tóm tắt trạng thái:

```text
Thu tiền
Công nợ đến hạn
Công nợ quá hạn
Thanh toán thất bại
Hoàn tiền
Đối soát
```

Mỗi mục có:

- Label trạng thái.
- Giá trị.
- Severity.
- Lý do.
- Drill-down.

Không chỉ dùng màu xanh/vàng/đỏ.

---

## 13. Debt aging

Buckets đề xuất:

```text
Chưa đến hạn
1–30 ngày
31–60 ngày
61–90 ngày
Trên 90 ngày
```

Mỗi bucket hiển thị:

- Giá trị.
- Tỷ trọng.
- Số khách hàng.
- Số chứng từ hoặc đơn liên quan.
- Trend.

Chart đề xuất:

- Stacked horizontal bar.
- Column chart.
- Summary table.

Không dùng pie chart khi cần so sánh nhiều kỳ.

---

## 14. DSO

Nếu triển khai Days Sales Outstanding:

- Phải có công thức và dữ liệu đầu vào rõ.
- Ghi rõ đây là số bình quân.
- Cho phép xem theo kỳ và chi nhánh.
- Không so sánh thiếu ngữ cảnh ngành hoặc chính sách tín dụng.

Không hiển thị DSO nếu dữ liệu doanh thu hoặc phải thu chưa đủ tin cậy.

---

## 15. Customer debt concentration

Widget cho Finance Manager:

- Top debtors.
- Tỷ trọng top 5/top 10.
- Overdue amount.
- Oldest due date.
- Assigned owner.
- Risk status.

Privacy:

- Chỉ hiển thị tên doanh nghiệp hoặc customer code khi có quyền.
- Không hiển thị phone/address trong summary.
- Export phải kiểm tra `finance.debt.view` và `customers.view`.

---

## 16. Branch Financial Performance

Bảng hoặc bar chart gồm:

- Branch.
- Revenue.
- Collected amount.
- Receivables.
- Overdue debt.
- Collection rate.
- Gross margin nếu có quyền.
- Data status.

Không xếp hạng chỉ theo doanh thu; cho phép xem hiệu quả thu tiền và nợ quá hạn.

---

## 17. Reconciliation Queue

Các loại đối soát:

```text
Payment gateway
Bank transfer
COD settlement
Marketplace settlement
Internal ledger
Refund settlement
```

Mỗi item:

- Reconciliation batch/code.
- Source.
- Period.
- Expected amount.
- Matched amount.
- Difference.
- Status.
- Owner.
- Due time.
- Available actions.

---

## 18. Reconciliation status

```ts
export type ReconciliationStatus =
  | "pending"
  | "processing"
  | "matched"
  | "partially_matched"
  | "mismatched"
  | "failed"
  | "approved";
```

Backend trả status và available actions.

Frontend không tự đổi `mismatched` thành `matched` chỉ vì người dùng chỉnh UI.

---

## 19. Reconciliation exception

Ngoại lệ gồm:

- Thiếu giao dịch.
- Trùng giao dịch.
- Sai số tiền.
- Sai reference.
- Chậm settlement.
- Currency mismatch.
- Payment không liên kết được order.

Mỗi exception cần:

- Reason code.
- Impact amount.
- Source reference đã mask phù hợp.
- Age.
- Owner.
- Resolution action.

---

## 20. Failed Payments

Bảng gồm:

- Payment reference.
- Order code.
- Customer summary đã mask.
- Amount.
- Method.
- Failure reason.
- Retry eligibility.
- Created time.
- Owner.

Không lưu hoặc hiển thị thông tin thẻ nhạy cảm.

Retry phải do payment provider và backend kiểm soát.

---

## 21. Refund Requests

Statuses:

```text
Requested
Under Review
Approved
Processing
Completed
Rejected
Failed
```

Mỗi item:

- Refund code.
- Order code.
- Amount.
- Reason.
- Requested by.
- Approval status.
- Payment method.
- Age.
- Actions.

Refund là action nhạy cảm, không optimistic update.

---

## 22. Refund approval workflow

Yêu cầu:

- Permission riêng.
- Approval threshold.
- Reason và evidence.
- Separation of duties nếu policy yêu cầu.
- MFA hoặc re-authentication với giá trị lớn nếu được áp dụng.
- Backend confirmation.
- Audit log.
- Conflict/version check.

Không cho một người tự tạo và tự duyệt nếu policy cấm.

---

## 23. Upcoming Cash Inflows

Dự báo dòng tiền thu theo:

- Due date.
- Payment terms.
- Customer payment history.
- Confirmed settlement schedules.

Buckets:

```text
7 ngày tới
8–14 ngày
15–30 ngày
Trên 30 ngày
```

Phải ghi rõ:

- `Expected` hoặc `Forecast`.
- Confidence.
- Source.
- Updated time.

Không trình bày như tiền chắc chắn sẽ thu.

---

## 24. Payment method breakdown

Các phương thức:

- Chuyển khoản.
- COD.
- Cổng thanh toán.
- Công nợ.
- Tiền mặt nếu có.
- Khác.

Hiển thị:

- Collected amount.
- Transaction count.
- Failure rate.
- Settlement delay.

Không dùng chart quá nhiều segment.

---

## 25. Missing Documents

Widget cho Accountant:

- Thiếu hóa đơn.
- Thiếu chứng từ thanh toán.
- Thiếu biên bản đối soát.
- Thiếu lý do hoàn tiền.
- Thiếu thông tin thuế.

Mỗi item:

- Entity code.
- Document type.
- Age.
- Owner.
- Due date.
- Action upload/request.

Upload phải tuân file security policy.

---

## 26. Tax and invoice status

Nếu hệ thống có tích hợp hóa đơn:

Statuses:

```text
Not Required
Pending
Issued
Failed
Adjusted
Cancelled
```

Dashboard chỉ hiển thị summary và exceptions.

Không thay thế phần mềm hóa đơn điện tử hoặc quy trình kê khai thuế.

---

## 27. Recent Financial Activity

Bảng gồm:

- Activity code.
- Type.
- Related entity.
- Amount.
- Status.
- Actor.
- Timestamp.
- Source.

Activity types:

```text
payment_received
payment_failed
refund_requested
refund_completed
reconciliation_completed
debt_adjusted
invoice_issued
export_generated
```

Không hiển thị secret hoặc full payment details.

---

## 28. Financial exceptions center

Nhóm rủi ro:

- Overdue debt tăng mạnh.
- Reconciliation mismatch.
- Refund spike.
- Payment failure spike.
- Revenue and payment discrepancy.
- Missing finance documents.
- Stale finance data.
- Closed period changed.

Mỗi risk item có severity, impact amount, scope, owner và action.

---

## 29. Period closing status

Có thể hiển thị:

```text
Open
In Review
Ready to Close
Closed
Reopened
```

Mỗi kỳ:

- Period.
- Completion percentage.
- Pending checks.
- Owner.
- Last update.

Không cho đóng kỳ trực tiếp từ Dashboard nếu chưa có workflow phê duyệt đầy đủ.

---

## 30. Finance task center

Task types:

```text
reconcile_payment
review_overdue_debt
approve_refund
collect_missing_document
review_invoice_failure
confirm_settlement
investigate_difference
```

Task gồm priority, dueAt, owner, related entity và available actions.

---

## 31. Finance dashboard contract

```ts
export type FinanceDashboardData = {
  scope: DashboardScope;
  dataStatus: FinanceDataStatus;
  kpis: FinanceKPI[];
  revenueCollectionTrend: FinanceTrendData;
  debtAging: DebtAgingBucket[];
  branchPerformance?: BranchFinancePerformance[];
  reconciliationQueue: ReconciliationItem[];
  failedPayments: FailedPaymentItem[];
  refunds: RefundItem[];
  upcomingCashInflows?: CashInflowForecast[];
  missingDocuments: MissingFinanceDocument[];
  exceptions: FinanceException[];
  recentActivity: FinanceActivity[];
  updatedAt: string;
};
```

---

## 32. Finance KPI contract

```ts
export type FinanceKPI = {
  id: string;
  label: string;
  value: number | null;
  valueType: "currency" | "number" | "percentage" | "days";
  currency?: "VND";
  semanticTrend?: "positive" | "negative" | "neutral";
  trendValue?: number;
  comparisonLabel?: string;
  availability: "available" | "not_calculated" | "not_applicable" | "permission_denied";
  dataStatus: FinanceDataStatus;
  updatedAt: string;
};
```

---

## 33. Debt aging contract

```ts
export type DebtAgingBucket = {
  key: "not_due" | "1_30" | "31_60" | "61_90" | "over_90";
  label: string;
  amount: number;
  sharePercent: number;
  customerCount: number;
  documentCount: number;
  trendPercent?: number;
};
```

---

## 34. Reconciliation item contract

```ts
export type ReconciliationItem = {
  id: string;
  code: string;
  source: string;
  periodFrom: string;
  periodTo: string;
  expectedAmount: number;
  matchedAmount: number;
  differenceAmount: number;
  status: ReconciliationStatus;
  ownerUserId?: string;
  dueAt?: string;
  updatedAt: string;
  availableActions: string[];
};
```

---

## 35. Permissions

Đề xuất:

```text
finance.dashboard.view
finance.revenue.view
finance.collection.view
finance.gross_profit.view
finance.debt.view
finance.reconciliation.view
finance.reconciliation.update
finance.refunds.view
finance.refunds.approve
finance.invoices.view
finance.period_close.view
reports.finance.export
customers.view
customers.view_pii
```

Không dựa duy nhất vào role name `accountant`, `cfo` hoặc `admin`.

---

## 36. Scope behavior

Có thể gồm:

```text
system
branch
business unit
assigned reconciliation batches
```

Finance user chỉ được chọn scope backend cho phép.

Filter branch không được làm lộ số liệu ngoài phạm vi.

---

## 37. Data freshness

Ngưỡng tham khảo:

| Dataset | Stale threshold |
|---|---:|
| Payment events | 2–5 phút |
| Reconciliation status | 5–15 phút |
| Receivables | 15–60 phút |
| Revenue KPI | 15–60 phút |
| Closed period data | Theo lần chốt |
| Cash forecast | Theo lần chạy mô hình |

Dữ liệu closed period có thể ít thay đổi nhưng mọi restatement phải hiển thị rõ.

---

## 38. Loading states

- KPI skeleton.
- Trend chart placeholder.
- Debt aging skeleton.
- Reconciliation rows skeleton.
- Refund and failed-payment skeleton.

Không hiển thị `0 ₫` khi chưa tải.

---

## 39. Empty states

### Không có công nợ quá hạn

```text
Không có công nợ quá hạn trong phạm vi đã chọn.
```

### Không có đối soát chờ xử lý

```text
Không có phiên đối soát nào đang chờ xử lý.
```

### Không có hoàn tiền

```text
Không có yêu cầu hoàn tiền trong kỳ này.
```

### Chưa có dữ liệu tài chính

```text
Chưa có đủ dữ liệu tài chính cho phạm vi này.
```

---

## 40. Error states

- Widget lỗi độc lập.
- Payment API lỗi không làm mất Debt Aging đã cache.
- Data contract lỗi phải hiển thị `Số liệu chưa khả dụng`.
- Có retry và request ID.
- Không dùng số kỳ trước thay số hiện tại thiếu label.
- Không đánh dấu `closed` khi service closing status lỗi.

---

## 41. Stale and provisional states

Stale và provisional là hai trạng thái khác nhau:

- `Stale`: dữ liệu không đủ mới.
- `Provisional`: dữ liệu mới nhưng chưa chốt/đối soát.

UI phải hiển thị đúng cả hai khi cùng tồn tại.

Ví dụ:

```text
Dữ liệu tạm tính · Cập nhật lần cuối 45 phút trước
```

---

## 42. Responsive behavior

### ≥1440px

- KPI 4–6 cột.
- Trend/Health 8/4.
- Debt/Exceptions 7/5.

### 1024–1439px

- KPI 2–3 cột.
- Charts xếp dọc khi cần.
- Tables scroll trong wrapper.

### 768–1023px

- Sidebar drawer.
- KPI 2 cột.
- Widget một cột.
- Toolbar wrap.

### <768px

Mobile fallback ưu tiên:

1. Data status.
2. KPI.
3. Overdue debt.
4. Reconciliation queue.
5. Failed payments/refunds.

Không cố hiển thị toàn bộ financial matrix trên mobile.

---

## 43. Accessibility

- Số compact có full value trong accessible text.
- Debt Aging chart có data table thay thế.
- Status không chỉ dùng màu.
- Tables có caption, headers và `aria-sort`.
- Dialog phê duyệt refund quản lý focus.
- Error message liên kết action retry.
- Zoom 200% vẫn xem được KPI và task quan trọng.
- Touch target tối thiểu 44px trên tablet/mobile.

---

## 44. Security và privacy

- Backend enforce permission và branch scope.
- Không trả finance data rồi mới ẩn ở frontend.
- Không lưu payment secrets hoặc bank credentials trong client.
- Không hiển thị full card/bank account data.
- Export có signed URL và expiry.
- Refund, reconciliation update và closing-related actions có audit.
- Cache key gồm user, permission hash, scope, period và data status.
- Error reporter phải redact financial payload nhạy cảm.

---

## 45. Audit requirements

Bắt buộc audit:

```text
finance.reconciliation.updated
finance.reconciliation.approved
finance.refund.approved
finance.refund.rejected
finance.debt.adjusted
finance.period.reopened
finance.report.exported
```

Audit gồm actor, action, entity, amount nếu policy cho phép, before/after, reason, result, request ID và timestamp.

---

## 46. Analytics events

```text
finance.dashboard.viewed
finance.filter.applied
finance.kpi.opened
finance.debt_bucket.opened
finance.reconciliation.opened
finance.refund.review_started
finance.report.export_requested
```

Không gửi số tiền cụ thể, customer name, bank reference hoặc nội dung chứng từ vào analytics.

---

## 47. Observability

Theo dõi:

- Finance Dashboard load success.
- Revenue/payment API latency.
- Reconciliation failure rate.
- Debt data freshness.
- Refund mutation failure.
- Export failure.
- Contract validation errors.
- Closed-period restatement events.
- Permission denied anomalies.

---

## 48. Search integration

Finance Search ưu tiên:

- Order code.
- Payment reference đã mask.
- Reconciliation batch.
- Customer/company theo quyền.
- Invoice code.
- Refund code.

Không cho tìm phone/email nếu role không cần.

Không log raw query.

---

## 49. Notification integration

Finance notifications:

- Payment failed.
- Debt due/overdue.
- Reconciliation mismatch.
- Refund waiting approval.
- Invoice issuance failed.
- Period closing issue.
- Finance data stale.

Không toast mọi giao dịch mới.

---

## 50. Testing

### Unit

- KPI semantic trend.
- Collection rate.
- Debt aging bucket mapping.
- DSO display rule.
- Data status mapping.
- Stale/provisional combination.

### Component

- Financial KPI states.
- Debt Aging.
- Reconciliation Queue.
- Failed Payments.
- Refund Requests.
- Data Status Banner.

### Integration

- Branch filter cập nhật toàn Dashboard.
- Accountant không thấy gross profit thiếu quyền.
- Refund approval chờ backend confirmation.
- Reconciliation conflict handling.
- Closed/provisional data states.
- Export đúng scope.

### E2E

- Finance Manager mở Dashboard.
- Chọn kỳ và branch.
- Mở debt bucket.
- Accountant xử lý reconciliation exception.
- Review refund request.
- Export finance report.
- Read-only viewer không thấy mutation controls.

---

## 51. Visual QA

Baseline:

```text
Finance Manager — 1440 × 900
Accountant — 1440 × 900
Finance Read-only
Finance Dashboard — 1024 × 768
Provisional Data
Closed Period
Stale Data
No Overdue Debt
Reconciliation Mismatch
Refund Approval Dialog
Large Currency Values
Permission Limited
```

Kiểm tra:

- Data status visibility.
- Currency alignment.
- Debt severity.
- Table readability.
- Long references.
- Focus states.
- Permission masking.

---

## 52. Performance budgets

Mục tiêu tham khảo:

```text
Dashboard LCP p75 < 2,5s
Finance overview API p95 < 2s
Filter UI response < 200ms
Reconciliation mutation feedback < 1,5s trong điều kiện bình thường
Search p95 < 1s
```

Lazy-load detail tables dưới fold và dùng cursor pagination.

---

## 53. AI-generated finance summary requirements

Nếu dùng AI tạo bản tóm tắt:

- Chỉ dùng dữ liệu đã permission-filtered.
- Không gửi PII, bank reference hoặc chứng từ đầy đủ tới provider chưa phê duyệt.
- Không tự tạo số liệu hoặc kết luận kế toán.
- Claim phải map tới KPI/risk source.
- Hiển thị `Được tạo tự động`.
- Có timestamp và fallback rule-based.
- Không dùng AI output để tự động phê duyệt refund hoặc điều chỉnh số liệu.

---

## 54. AI coding agent requirements

AI coding agent phải:

- Phân biệt revenue, collected amount, receivables và cash flow.
- Không hardcode công thức tài chính chưa được phê duyệt.
- Không tạo DSO hoặc forecast giả khi dữ liệu chưa đủ.
- Không optimistic update refund, reconciliation hoặc period-close actions.
- Không hiển thị finance data ngoài permission/scope.
- Không gửi số tiền chi tiết vào analytics.
- Bổ sung test cho provisional, closed, stale và restated data.
- Báo rõ API, formulas, permissions và backend assumptions.

---

## 55. Acceptance checklist

- [ ] Có biến thể Finance Manager, Accountant và Read-only.
- [ ] Toolbar có period, branch, status và export phù hợp.
- [ ] Có Data Status model.
- [ ] KPI phân biệt revenue, collection, receivables và overdue debt.
- [ ] Có Revenue & Collection Trend.
- [ ] Có Cash & Debt Health.
- [ ] Có Debt Aging và customer concentration theo quyền.
- [ ] Có Branch Financial Performance.
- [ ] Có Reconciliation Queue và exceptions.
- [ ] Có Failed Payments và Refund Requests.
- [ ] Refund approval có workflow an toàn.
- [ ] Có Upcoming Cash Inflows với confidence.
- [ ] Có Missing Documents và invoice status.
- [ ] Có Financial Exceptions Center và Task Center.
- [ ] Permissions và scope được backend enforce.
- [ ] Loading, empty, error, stale, provisional và closed states đầy đủ.
- [ ] Accessibility, responsive, security và audit hoàn chỉnh.
- [ ] Analytics, observability và testing đầy đủ.

---

## 56. Những điều không được làm

- Không đồng nhất doanh thu với tiền đã thu.
- Không dùng dữ liệu đơn hàng thay báo cáo kế toán chính thức mà thiếu định nghĩa.
- Không hiển thị số `0` khi API lỗi.
- Không cho người thiếu quyền xem giá vốn, lợi nhuận hoặc công nợ chi tiết.
- Không optimistic update refund hoặc reconciliation.
- Không tự tạo DSO, forecast hoặc accounting conclusion thiếu nguồn.
- Không gửi financial payload nhạy cảm vào analytics/logs.
- Không cho export vượt scope.
- Không đánh dấu dữ liệu provisional là closed.
- Không thay phần mềm kế toán bằng Dashboard tổng hợp.

---

## 57. Kết luận

Finance Dashboard của Cynca VLXD phải giúp đội tài chính nhìn rõ doanh thu, thu tiền, công nợ, đối soát và các ngoại lệ cần xử lý mà vẫn giữ đúng định nghĩa kế toán, trạng thái dữ liệu, phân quyền và audit. Giao diện cần ưu tiên tính chính xác, khả năng đối chiếu và hành động an toàn hơn hiệu ứng trực quan.

File tiếp theo đề xuất:

```text
README.md
36-Customer-Service-Dashboard.md
```