# Cynca VLXD Admin Dashboard — Branch Manager Dashboard Specification

> Phiên bản: 1.0  
> Phạm vi: Dashboard điều hành chi nhánh dành cho quản lý chi nhánh và người được ủy quyền  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Sales, Warehouse, Finance, Customer Service, QA và AI coding agent  
> Tài liệu liên quan: `27-Role-Based-Dashboard.md`, `32-Executive-Dashboard.md`, `33-Sales-Dashboard.md`, `34-Warehouse-Dashboard.md`, `35-Finance-Dashboard.md`, `36-Customer-Service-Dashboard.md`

---

## 1. Mục tiêu

Branch Manager Dashboard giúp quản lý một chi nhánh Cynca VLXD theo dõi đồng thời tình hình kinh doanh, đơn hàng, tồn kho, công nợ, nhân sự vận hành và chất lượng dịch vụ trong phạm vi chi nhánh được phân công.

Dashboard phải trả lời nhanh:

- Chi nhánh đang đạt bao nhiêu phần trăm kế hoạch?
- Doanh thu, đơn hàng và lợi nhuận đang tăng hay giảm?
- Đơn nào đang chậm xử lý hoặc có nguy cơ giao thất bại?
- SKU nào sắp hết, tồn âm hoặc ảnh hưởng trực tiếp tới bán hàng?
- Công nợ và thanh toán của chi nhánh có vấn đề gì?
- Nhóm bán hàng, kho và CSKH có đang quá tải không?
- Rủi ro nào cần quản lý chi nhánh can thiệp ngay?
- Việc gì cần giao người phụ trách trong ngày?

Dashboard không thay thế các module Sales, Warehouse, Finance hoặc Customer Service. Đây là lớp điều hành hợp nhất theo phạm vi chi nhánh.

---

## 2. Đối tượng sử dụng

### Branch Manager

- Xem toàn bộ dữ liệu chi nhánh trong phạm vi được cấp.
- Theo dõi mục tiêu, doanh thu và vận hành.
- Phân công hoặc điều phối đầu việc nếu có quyền.
- Drill-down sang các module chuyên môn.

### Deputy Branch Manager

- Có thể được cấp một phần quyền, ví dụ chỉ Sales và Operations.

### Branch Viewer

- Chỉ xem báo cáo tổng hợp.
- Không có mutation controls.

Không suy quyền chỉ từ tên vai trò. Permission và scope backend mới là nguồn quyết định cuối cùng.

---

## 3. Nguyên tắc thiết kế

1. Luôn hiển thị rõ tên chi nhánh và phạm vi dữ liệu.
2. Không cho đổi sang chi nhánh ngoài scope.
3. Ưu tiên cảnh báo cần hành động hơn chart thứ cấp.
4. Tối đa 5–6 KPI chính ở vùng đầu.
5. Không trộn dữ liệu toàn hệ thống vào số liệu chi nhánh.
6. Mọi KPI phải có kỳ so sánh và thời gian cập nhật.
7. Không hiển thị số `0` khi dữ liệu chưa tải hoặc API lỗi.
8. Widget lỗi độc lập, không làm sập toàn trang.
9. Drill-down phải giữ nguyên branch và date scope.
10. Không hiển thị dữ liệu nhạy cảm ngoài permission.

---

## 4. Layout tổng thể

Desktop 1440px:

```text
Page Toolbar
Branch Health Summary
KPI Grid 4–6 cards
Revenue & Orders Trend            8 columns
Priority Attention                4 columns
Sales Performance                 6 columns
Warehouse & Fulfillment           6 columns
Finance & Debt                    6 columns
Customer Service                  6 columns
Team Operations                   7 columns
Recent Branch Activity            5 columns
```

Grid sử dụng 12 cột và spacing theo token hiện có.

---

## 5. Page toolbar

### Bên trái

- Tên chi nhánh.
- Mã chi nhánh nếu cần.
- Trạng thái hoạt động.
- Thời điểm cập nhật cuối.

### Bên phải

- Date range.
- Compare mode.
- Warehouse filter nếu chi nhánh có nhiều kho.
- Team filter nếu có quyền.
- Export branch report.
- Refresh.

Không hiển thị branch selector nếu user chỉ có đúng một branch.

---

## 6. Date presets

```text
Hôm nay
7 ngày
30 ngày
Tháng này
Quý này
Năm nay
Tùy chọn
```

Compare mode:

```text
Kỳ trước
Cùng kỳ năm trước
Không so sánh
```

Mọi widget mặc định phải cập nhật cùng filter, trừ dữ liệu snapshot có ghi chú riêng.

---

## 7. Branch Health Summary

Tóm tắt theo sáu nhóm:

```text
Kinh doanh
Đơn hàng
Kho
Tài chính
Chăm sóc khách hàng
Vận hành đội ngũ
```

Mỗi nhóm dùng trạng thái:

```text
Tốt
Cần chú ý
Rủi ro
Chưa đủ dữ liệu
```

Mỗi trạng thái phải có lý do ngắn và link drill-down.

---

## 8. KPI chính

KPI mặc định:

1. Doanh thu chi nhánh.
2. Tổng đơn hàng.
3. Tỷ lệ hoàn thành mục tiêu.
4. Đơn quá SLA.
5. SKU có rủi ro tồn kho.
6. Công nợ quá hạn hoặc ticket quá SLA tùy cấu hình.

KPI tùy chọn theo quyền:

- Lợi nhuận gộp.
- Biên lợi nhuận gộp.
- Giá trị tồn kho.
- Tỷ lệ giao thành công.
- CSAT.
- Tỷ lệ thu tiền.

---

## 9. KPI semantic rules

Semantic phải dựa trên nghiệp vụ:

- Doanh thu tăng: thường positive.
- Công nợ quá hạn tăng: negative.
- Tồn kho tăng: phụ thuộc loại sản phẩm và target.
- Ticket quá SLA giảm: positive.
- Đơn hủy tăng: negative.

Backend nên trả `semanticTrend` thay vì frontend tự suy từ dấu số.

---

## 10. Revenue & Orders Trend

Chart chính gồm:

- Doanh thu.
- Số đơn.
- Mục tiêu lũy kế tùy chọn.
- Kỳ so sánh.

Controls:

- Day/week/month.
- Revenue/orders toggle.
- Compare mode.

Tooltip:

- Giá trị đầy đủ.
- Số đơn.
- Target.
- Chênh lệch.
- Data status.

---

## 11. Branch Target Progress

Component:

```text
BranchTargetProgress
├── ActualRevenue
├── TargetRevenue
├── CompletionPercentage
├── TimeProgressPercentage
├── RemainingGap
└── Forecast nếu có
```

Phải hiển thị cả tiến độ thời gian để quản lý hiểu đúng mức hoàn thành.

Không tạo forecast nếu backend chưa có logic đáng tin cậy.

---

## 12. Priority Attention Center

Các rủi ro cần ưu tiên:

- Doanh thu giảm mạnh.
- Không đạt target theo run-rate.
- Đơn quá SLA.
- Tồn âm hoặc hết hàng ảnh hưởng đơn.
- Công nợ quá hạn tăng.
- Thanh toán thất bại.
- Ticket khiếu nại nghiêm trọng.
- Đồng bộ kho lỗi.
- Nhóm vận hành quá tải.

Mỗi item gồm severity, tác động, owner, thời điểm phát hiện và action.

---

## 13. Priority sorting

Thứ tự gợi ý:

```text
Critical unresolved
High overdue
High near due
Medium by impact
Low by recency
```

Không để cảnh báo thông tin thấp đẩy cảnh báo critical xuống dưới.

---

## 14. Sales Performance

Widget gồm:

- Doanh thu theo nhân viên hoặc nhóm.
- Target completion.
- Đơn hàng.
- Giá trị đơn trung bình.
- Đơn quá hạn.
- Khách cần follow-up.

Manager chỉ thấy team thuộc branch scope.

Không dùng doanh thu tuyệt đối làm thước đo duy nhất nếu quy mô danh mục khách hàng khác nhau.

---

## 15. Sales team table

Cột đề xuất:

- Nhân viên.
- Doanh thu.
- Mục tiêu.
- Completion %.
- Số đơn.
- Khách follow-up.
- Đơn quá hạn.
- Trạng thái tải công việc.

Click row mở drawer chi tiết, không điều hướng mất filter.

---

## 16. Warehouse & Fulfillment

Tổng hợp:

- Đơn chờ lấy hàng.
- Đơn chờ đóng gói.
- Đơn chờ bàn giao.
- SKU sắp hết.
- Tồn âm.
- Đồng bộ dữ liệu kho.
- Tác vụ kho quá SLA.

Không hiển thị toàn bộ SKU; chỉ hiển thị exceptions và link chi tiết.

---

## 17. Fulfillment SLA

Metrics:

- Median confirmation time.
- Median picking time.
- Median packing time.
- Tỷ lệ giao đúng SLA.
- Số đơn quá SLA.

SLA phải được cấu hình theo branch hoặc task type nếu cần.

Frontend không hardcode rule SLA trong component.

---

## 18. Inventory risk summary

Nhóm rủi ro:

```text
Negative stock
Out of stock
Low stock
Overstock
Slow moving
Stale data
```

Mỗi nhóm hiển thị số SKU, mức tác động và dữ liệu cập nhật cuối.

Không hiển thị `Còn hàng` nếu inventory API lỗi.

---

## 19. Finance & Debt

Theo quyền, widget có thể gồm:

- Doanh thu ghi nhận.
- Tiền đã thu.
- Phải thu.
- Công nợ quá hạn.
- Tỷ lệ thu tiền.
- Thanh toán thất bại.
- Hoàn tiền đang chờ.

Nếu user không có `finance.view`, widget phải ẩn hoàn toàn hoặc hiển thị restricted state theo policy.

---

## 20. Debt aging summary

Buckets:

```text
Chưa đến hạn
1–30 ngày
31–60 ngày
61–90 ngày
Trên 90 ngày
```

Executive branch view chỉ hiển thị summary và top exceptions.

Không đưa chi tiết khách hàng hoặc chứng từ ngoài quyền.

---

## 21. Customer Service Overview

Metrics:

- Ticket mới.
- Ticket đang mở.
- Ticket quá SLA.
- Failed delivery cases.
- Callback pending.
- CSAT nếu có đủ dữ liệu.

Widget ưu tiên các trường hợp cần quản lý can thiệp.

---

## 22. Complaint and escalation summary

Hiển thị:

- Top complaint topics.
- Khiếu nại nghiêm trọng.
- Ticket escalated.
- Reopened tickets.
- Vấn đề lặp theo sản phẩm hoặc đơn vị giao vận.

Nếu phát hiện xu hướng bằng mô hình, phải có confidence và nguồn dữ liệu rõ.

---

## 23. Team Operations

Tổng hợp workload theo các nhóm:

```text
Sales
Warehouse
Customer Service
Finance Operations
```

Mỗi nhóm có:

- Open tasks.
- Overdue tasks.
- Oldest task age.
- Available staff.
- Workload status.

Không dùng một con số workload duy nhất để đánh giá cá nhân thiếu ngữ cảnh.

---

## 24. Branch task center

Task types có thể gồm:

```text
review_sales_gap
resolve_overdue_order
handle_inventory_risk
review_overdue_debt
resolve_customer_escalation
approve_transfer
follow_up_sync_failure
```

Mỗi task gồm priority, owner, dueAt, entity, status và available actions.

---

## 25. Assignment workflow

Manager có thể giao người phụ trách khi có quyền.

Yêu cầu:

- Chỉ chọn user trong branch/team được phép.
- Backend confirmation.
- Conflict/version handling.
- Notification tới người nhận.
- Audit log.

Không optimistic update nếu assignment ảnh hưởng workflow quan trọng.

---

## 26. Recent Branch Activity

Activity types:

```text
order_confirmed
order_cancelled
inventory_adjusted
payment_received
refund_approved
ticket_escalated
task_assigned
report_exported
```

Mỗi item gồm entity, actor, timestamp, result và link phù hợp quyền.

Không hiển thị nội dung nhạy cảm hoặc PII dư thừa.

---

## 27. Branch comparison

Branch Manager mặc định chỉ xem một branch.

Nếu user có quyền vùng hoặc nhiều branch:

- Cho phép so sánh branch được cấp.
- Luôn hiển thị rõ scope.
- Không trộn số liệu tổng hệ thống nếu không có quyền.

Comparison metrics:

- Revenue.
- Target completion.
- Order SLA.
- Inventory risk.
- Debt overdue.
- CSAT.

---

## 28. Branch profile summary

Có thể hiển thị nhẹ:

- Branch name.
- Address rút gọn.
- Manager.
- Warehouse count.
- Active staff count.
- Opening status.

Không đưa thông tin cá nhân nhạy cảm lên vùng tổng quan.

---

## 29. Data contract

```ts
export type BranchManagerDashboardData = {
  scope: DashboardScope;
  branch: BranchSummary;
  health: BranchHealthSummary;
  target: BranchTargetSummary;
  kpis: DashboardKPI[];
  revenueOrdersTrend: RevenueOrdersTrend;
  risks: BranchRiskItem[];
  sales: BranchSalesSummary;
  warehouse: BranchWarehouseSummary;
  finance?: BranchFinanceSummary;
  customerService: BranchCustomerServiceSummary;
  teamOperations: BranchTeamOperations;
  tasks: BranchManagementTask[];
  recentActivity: BranchActivity[];
  updatedAt: string;
};
```

---

## 30. Branch summary contract

```ts
export type BranchSummary = {
  id: string;
  code: string;
  name: string;
  status: "active" | "temporarily_closed" | "inactive";
  managerUserId?: string;
  warehouseCount: number;
  activeStaffCount?: number;
  timezone: string;
};
```

---

## 31. Branch risk contract

```ts
export type BranchRiskItem = {
  id: string;
  category: "sales" | "orders" | "inventory" | "finance" | "support" | "system";
  severity: "critical" | "high" | "medium" | "low";
  title: string;
  summary: string;
  impactValue?: number;
  impactType?: "currency" | "percentage" | "count";
  ownerUserId?: string;
  dueAt?: string;
  detectedAt: string;
  href?: string;
  availableActions: string[];
};
```

---

## 32. Branch management task contract

```ts
export type BranchManagementTask = {
  id: string;
  type: string;
  title: string;
  priority: "critical" | "high" | "medium" | "low";
  branchId: string;
  assignedUserId?: string;
  entityType?: string;
  entityId?: string;
  status: "open" | "in_progress" | "completed" | "dismissed";
  dueAt?: string;
  availableActions: string[];
};
```

---

## 33. Permissions

Đề xuất:

```text
branch.dashboard.view
branch.performance.view
branch.staff.view
branch.tasks.assign
sales.branch.view
warehouse.branch.view
finance.branch.view
support.branch.view
reports.branch.export
branches.compare
```

Các permission domain chi tiết vẫn phải được kiểm tra riêng.

---

## 34. Scope behavior

Scope mặc định:

```text
branch
```

Backend phải:

- Xác định branch IDs được phép.
- Lọc toàn bộ API theo scope.
- Không tin branchId do client gửi.
- Áp object-level authorization.

User filter, warehouse filter và team filter không được mở rộng scope.

---

## 35. Data freshness

Ngưỡng tham khảo:

| Dataset | Stale threshold |
|---|---:|
| Orders | 2–5 phút |
| Warehouse tasks | 2–5 phút |
| Inventory | 5–15 phút |
| Revenue KPI | 15–30 phút |
| Finance debt | 15–60 phút |
| Support SLA | 1–3 phút |
| Team workload | 5–15 phút |

Không trộn dữ liệu có độ mới khác nhau mà thiếu timestamp hoặc stale notice.

---

## 36. Loading states

- Branch header skeleton.
- Health summary skeleton.
- KPI skeleton.
- Trend chart placeholder.
- Domain widget skeletons.
- Task list skeleton.

Không hiển thị `0` khi chưa tải.

---

## 37. Empty states

### Chưa có dữ liệu

```text
Chưa có đủ dữ liệu để tạo tổng quan cho chi nhánh này.
```

### Không có rủi ro

```text
Không phát hiện vấn đề đáng chú ý trong phạm vi đã chọn.
```

### Không có task

```text
Không có đầu việc quản lý nào đang chờ xử lý.
```

### Không có công nợ quá hạn

```text
Chi nhánh không có công nợ quá hạn trong kỳ này.
```

---

## 38. Error states

- Widget lỗi độc lập.
- Finance widget lỗi không làm mất Sales hoặc Warehouse.
- Có retry và request ID.
- Không thay dữ liệu lỗi bằng zero hoặc empty giả.
- Không hiển thị branch health là `Tốt` khi nguồn dữ liệu lỗi.

---

## 39. Responsive behavior

### ≥1440px

- KPI 4–6 cột.
- Trend/Risk 8/4.
- Domain widgets 6/6.

### 1024–1439px

- KPI 2–3 cột.
- Widgets xếp dọc khi cần.
- Tables scroll trong wrapper.

### 768–1023px

- Sidebar drawer.
- KPI 2 cột.
- Domain widget một cột.

### <768px

Mobile fallback ưu tiên:

1. Branch Health.
2. KPI.
3. Priority Attention.
4. Orders/Warehouse exceptions.
5. Tasks.

---

## 40. Accessibility

- KPI có accessible summary.
- Health/risk không chỉ dùng màu.
- Chart có text summary hoặc bảng thay thế.
- Tables có caption và headers.
- Drawer/dialog quản lý focus.
- Touch target tối thiểu 44px.
- Zoom 200% không mất action chính.
- Live updates dùng `aria-live=polite` phù hợp.

---

## 41. Search integration

Branch Search ưu tiên:

- Orders trong branch.
- Products/SKU trong kho của branch.
- Customers thuộc branch scope.
- Tickets.
- Staff/navigation commands theo quyền.

Không log raw query.

Không trả entity ngoài branch rồi mới ẩn client-side.

---

## 42. Notification integration

Notification categories:

- Sales target risk.
- Order overdue.
- Inventory risk.
- Debt overdue.
- Payment failure.
- Ticket escalation.
- Sync failure.
- Staff workload alert.

Không toast mọi sự kiện; dùng Notification Center và Priority Attention.

---

## 43. Analytics events

```text
branch.dashboard.viewed
branch.filter.applied
branch.risk.opened
branch.sales_member.opened
branch.warehouse_alert.opened
branch.finance_exception.opened
branch.task.assigned
branch.report.export_requested
```

Không gửi PII, nội dung ticket hoặc số tiền chi tiết vào analytics.

---

## 44. Observability

Theo dõi:

- Branch Dashboard load success.
- Domain widget error rates.
- Data freshness theo domain.
- Assignment mutation failure.
- Export failure.
- Contract validation errors.
- Permission denied anomalies.
- Cross-branch cache anomalies.

---

## 45. Security và privacy

- Backend enforce branch scope trên mọi endpoint.
- Không trả dữ liệu toàn hệ thống rồi lọc client.
- Finance, PII và staff data theo permission riêng.
- Export có signed URL và expiry.
- Assignment/action quan trọng có audit.
- Cache key gồm user, permission hash, branch và filters.
- Clear cache nhạy cảm khi logout hoặc đổi tài khoản.

---

## 46. Audit requirements

Nên audit:

```text
branch.task.assigned
branch.task.reassigned
branch.export.generated
branch.scope.changed_by_admin
branch.risk.dismissed
branch.note.created
```

Audit gồm actor, branch, entity, action, reason, result, request ID và timestamp.

---

## 47. Testing

### Unit

- Branch target completion.
- Health status mapping.
- Risk sorting.
- Scope serialization.
- Stale calculation.
- Permission-based widget mapping.

### Component

- Branch Health Summary.
- KPI states.
- Priority Attention.
- Sales Performance.
- Warehouse Summary.
- Finance Summary.
- Customer Service Summary.
- Task Center.

### Integration

- User chỉ thấy branch được cấp.
- Warehouse filter giữ branch scope.
- Finance widget ẩn đúng quyền.
- Assignment conflict handling.
- Drill-down giữ filter.
- Export đúng branch.

### E2E

- Branch Manager mở Dashboard.
- Chọn tháng này.
- Mở đơn quá SLA.
- Mở tồn âm.
- Xem công nợ quá hạn.
- Giao task cho nhân viên.
- Export branch report.
- Viewer không thấy mutation actions.

---

## 48. Visual QA

Baseline:

```text
Branch Manager — 1440 × 900
Branch Viewer — 1440 × 900
Branch Manager — 1024 × 768
Mobile Fallback
Healthy Branch
Critical Risk
Finance Permission Limited
No Tasks
Partial Widget Error
Stale Inventory
Large Currency Values
```

Kiểm tra:

- Branch scope visibility.
- Information hierarchy.
- KPI alignment.
- Risk priority.
- Domain widget balance.
- Long Vietnamese text.
- Focus states.

---

## 49. Performance budgets

Mục tiêu tham khảo:

```text
Dashboard LCP p75 < 2,5s
Branch overview API p95 < 2s
Filter UI response < 200ms
Task assignment feedback < 1,5s
Search p95 < 1s
```

Có thể lazy-load domain widget dưới fold và cache an toàn theo branch.

---

## 50. AI-generated branch summary requirements

Nếu dùng AI tạo bản tóm tắt:

- Chỉ dùng dữ liệu đã lọc theo branch và permission.
- Không gửi PII hoặc tài chính nhạy cảm tới provider chưa phê duyệt.
- Không tự tạo số liệu.
- Mọi claim phải map tới KPI/risk source.
- Hiển thị `Được tạo tự động` và timestamp.
- Có fallback rule-based.
- Không dùng AI output để tự động giao task hoặc mutation.

---

## 51. AI coding agent requirements

AI coding agent phải:

- Không mở rộng branch scope từ filter client.
- Không hardcode KPI, SLA hoặc risk rules thiếu nguồn.
- Không hiển thị finance/PII/staff data ngoài permission.
- Không optimistic update assignment quan trọng thiếu conflict handling.
- Không đánh dấu branch health là tốt khi nguồn dữ liệu lỗi.
- Bổ sung test cho cross-branch access, stale domain data và partial errors.
- Báo rõ API, permissions, scope và backend assumptions.

---

## 52. Acceptance checklist

- [ ] Hiển thị rõ branch scope.
- [ ] Có Branch Health Summary.
- [ ] Có tối đa 6 KPI chính.
- [ ] Có Revenue & Orders Trend.
- [ ] Có Target Progress.
- [ ] Có Priority Attention Center.
- [ ] Có Sales Performance.
- [ ] Có Warehouse & Fulfillment.
- [ ] Có Finance & Debt theo quyền.
- [ ] Có Customer Service Overview.
- [ ] Có Team Operations và Task Center.
- [ ] Có Recent Branch Activity.
- [ ] Permission và branch scope được backend enforce.
- [ ] Loading, empty, error và stale states đầy đủ.
- [ ] Responsive và accessibility hoàn chỉnh.
- [ ] Search, Notification, analytics, observability và audit được xác định.
- [ ] Unit, component, integration, E2E và Visual QA đầy đủ.

---

## 53. Những điều không được làm

- Không cho đổi sang branch ngoài quyền.
- Không trả dữ liệu toàn hệ thống rồi lọc client.
- Không trộn số liệu branch với toàn công ty.
- Không dùng số `0` khi API lỗi.
- Không hiển thị finance, PII hoặc staff data thiếu quyền.
- Không hardcode SLA, target hoặc risk rules.
- Không optimistic update assignment quan trọng thiếu xác nhận.
- Không hiển thị branch health là `Tốt` khi dữ liệu thiếu.
- Không dùng AI để bịa tóm tắt hoặc tự động ra quyết định.
- Không thay các module nghiệp vụ bằng Dashboard tổng hợp.

---

## 54. Kết luận

Branch Manager Dashboard của Cynca VLXD phải cung cấp một góc nhìn hợp nhất về kinh doanh, đơn hàng, kho, tài chính, dịch vụ khách hàng và tải công việc trong đúng phạm vi chi nhánh. Giao diện cần ưu tiên rủi ro và hành động, đồng thời giữ chặt branch scope, permission, độ mới dữ liệu và khả năng drill-down sang module chuyên môn.

File tiếp theo đề xuất:

```text
README.md
38-Reports-Export-Center.md
```