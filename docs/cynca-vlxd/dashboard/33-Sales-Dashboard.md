# Cynca VLXD Admin Dashboard — Sales Dashboard Specification

> Phiên bản: 1.0  
> Phạm vi: Dashboard bán hàng dành cho quản lý kinh doanh và nhân viên kinh doanh  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Sales Operations, QA và AI coding agent  
> Tài liệu liên quan: `05-KPI-Cards.md`, `06-Charts.md`, `08-Orders-Table.md`, `16-Data-Contracts.md`, `27-Role-Based-Dashboard.md`, `31-Search-Command-Center.md`

---

## 1. Mục tiêu

Sales Dashboard giúp đội kinh doanh Cynca VLXD theo dõi doanh số, đơn hàng, khách hàng và các đầu việc cần xử lý trong ngày.

Dashboard phải trả lời nhanh:

- Doanh số hiện tại đang đạt bao nhiêu so với mục tiêu?
- Có bao nhiêu đơn mới, đơn chờ xác nhận và đơn quá hạn?
- Nhân viên hoặc chi nhánh nào đang đạt hoặc chưa đạt kế hoạch?
- Sản phẩm nào bán tốt, sản phẩm nào đang thiếu hàng?
- Khách hàng nào cần liên hệ lại?
- Nguồn doanh thu và tỷ lệ chuyển đổi đang thay đổi ra sao?
- Việc gì cần ưu tiên xử lý ngay?

Sales Dashboard không thay thế CRM, trang quản lý đơn hàng hoặc báo cáo tài chính chi tiết. Đây là màn hình tổng hợp để hành động nhanh.

---

## 2. Hai biến thể chính

### Sales Manager Dashboard

Dành cho quản lý kinh doanh.

Trọng tâm:

- Doanh số toàn nhóm.
- So sánh nhân viên.
- Tiến độ mục tiêu.
- Đơn hàng theo trạng thái.
- Hiệu suất sản phẩm và khách hàng.
- Cảnh báo doanh số, đơn quá hạn và thiếu hàng.

### Sales Staff Dashboard

Dành cho nhân viên kinh doanh.

Trọng tâm:

- Doanh số cá nhân.
- Đơn được phân công.
- Khách cần chăm sóc.
- Tác vụ hôm nay.
- Sản phẩm bán chạy và tồn kho liên quan.
- Thông báo trực tiếp.

Hai biến thể phải dùng cùng Design System và component registry, chỉ khác cấu hình, permission và scope.

---

## 3. Nguyên tắc thiết kế

1. Ưu tiên hành động trước biểu đồ thứ cấp.
2. Không hiển thị dữ liệu nhân viên khác cho sales staff nếu không có quyền.
3. KPI luôn có phạm vi và kỳ so sánh.
4. Đơn quá hạn và khách cần liên hệ phải nổi bật.
5. Không dùng màu xanh cho mọi chỉ số tăng; semantic phụ thuộc nghiệp vụ.
6. Không để một widget lỗi làm sập toàn Dashboard.
7. Không hardcode doanh số, mục tiêu hoặc trạng thái trong component.
8. Không hiển thị số 0 giả khi dữ liệu chưa tải.
9. Mọi drill-down giữ nguyên branch, user và date scope.
10. Dashboard phải dùng tốt trên laptop 1366px và tablet.

---

## 4. Layout — Sales Manager

Desktop 1440px:

```text
Page Toolbar
Sales Summary / Target Progress
KPI Grid 4–6 cards
Revenue Trend                    8 columns
Order Status                     4 columns
Team Performance                 7 columns
Priority Actions                 5 columns
Best Products                    6 columns
Customer & Pipeline              6 columns
Recent Orders                   12 columns
```

---

## 5. Layout — Sales Staff

Desktop 1440px:

```text
Page Toolbar
Personal Target Progress
KPI Grid 4 cards
My Priority Tasks                5 columns
My Orders                        7 columns
Personal Revenue Trend           7 columns
Products & Stock                 5 columns
Customers to Follow Up           6 columns
Recent Activity                  6 columns
```

Sales Staff cần ít chart hơn, nhiều danh sách hành động hơn.

---

## 6. Page toolbar

### Sales Manager

- Date range.
- Branch filter.
- Salesperson filter.
- Compare mode.
- Export sales report.
- Create order nếu có quyền.

### Sales Staff

- Date range.
- Scope mặc định `Của tôi`.
- Create order.
- Global search.
- Refresh.

Không hiển thị user filter cho nhân viên không có quyền xem đội nhóm.

---

## 7. Date presets

```text
Hôm nay
7 ngày
30 ngày
Tháng này
Quý này
Tùy chọn
```

Compare:

```text
Kỳ trước
Cùng kỳ năm trước
Không so sánh
```

Sales Staff mặc định có thể dùng `Tháng này`; quản lý có thể mặc định `30 ngày` hoặc `Tháng này` theo cấu hình.

---

## 8. KPI — Sales Manager

KPI đề xuất:

1. Doanh thu thuần.
2. Đơn hàng.
3. Giá trị đơn trung bình.
4. Khách hàng mới.
5. Tỷ lệ hoàn thành mục tiêu.
6. Đơn quá hạn xử lý.

KPI tùy chọn nếu có dữ liệu:

- Tỷ lệ chuyển đổi.
- Tỷ lệ hủy.
- Tỷ lệ khách quay lại.
- Lợi nhuận gộp nếu có permission.

---

## 9. KPI — Sales Staff

KPI đề xuất:

1. Doanh số cá nhân.
2. Đơn được phân công.
3. Đơn chờ xử lý.
4. Khách cần liên hệ.

Tùy chọn:

- Mục tiêu cá nhân.
- Giá trị đơn trung bình.
- Tỷ lệ chốt đơn.

Không hiển thị doanh thu toàn công ty hoặc dữ liệu nhân viên khác mặc định.

---

## 10. Target Progress

Component:

```text
SalesTargetProgress
├── ActualRevenue
├── TargetRevenue
├── CompletionPercentage
├── RemainingGap
├── TimeProgress
└── Forecast
```

Ví dụ:

```text
Đã đạt 78% mục tiêu tháng
11,7 tỷ / 15 tỷ ₫
Còn 8 ngày
```

Cần hiển thị cả tiến độ thời gian để tránh hiểu sai 78% là tốt khi kỳ gần kết thúc.

---

## 11. Forecast doanh số

Chỉ hiển thị khi có logic rõ ràng.

Có thể dùng:

- Run rate.
- Weighted pipeline.
- Mô hình dự báo nội bộ.

Ví dụ:

```text
Dự báo cuối tháng: 14,6–15,3 tỷ ₫
```

Phải có label `Dự báo`, timestamp và confidence nếu phù hợp.

Không trình bày forecast như doanh thu chắc chắn.

---

## 12. Revenue Trend

Chart line/area.

Series:

- Doanh thu thực tế.
- Mục tiêu lũy kế.
- Kỳ trước tùy chọn.

Controls:

- Day/week/month.
- Revenue/orders toggle.
- Compare mode.

Tooltip:

- Giá trị đầy đủ.
- Mục tiêu.
- Chênh lệch.
- Số đơn.

---

## 13. Order Status

Donut hoặc horizontal stacked bar.

Statuses:

```text
Chờ xác nhận
Đã xác nhận
Đang xử lý
Chờ giao
Đang giao
Hoàn thành
Hủy
Trả hàng
```

Ưu tiên làm nổi bật:

- Chờ xác nhận.
- Quá hạn.
- Hủy.
- Giao thất bại.

Không chỉ hiển thị tỷ lệ; cần số lượng tuyệt đối.

---

## 14. Priority Actions

Widget quan trọng, gồm các việc cần xử lý:

- Đơn mới chưa xác nhận.
- Đơn quá SLA.
- Khách cần gọi lại.
- Báo giá sắp hết hạn.
- Thanh toán lỗi liên quan đơn.
- Sản phẩm thiếu hàng ảnh hưởng đơn.

Mỗi item gồm:

- Priority.
- Nội dung.
- Entity code.
- Deadline hoặc age.
- Owner.
- Primary action.

---

## 15. Task priority rules

Gợi ý:

```text
Critical: ảnh hưởng đơn giá trị cao hoặc khách hàng quan trọng
High: quá SLA hoặc cần xử lý trong ngày
Medium: follow-up trong 1–3 ngày
Low: thông tin hoặc nhắc việc không khẩn cấp
```

Business rule phải nằm ở backend hoặc service layer, không hardcode phân loại trong JSX.

---

## 16. Team Performance

Dành cho Sales Manager.

Bảng hoặc horizontal bar gồm:

- Nhân viên.
- Doanh số.
- Mục tiêu.
- Completion %.
- Số đơn.
- Giá trị đơn trung bình.
- Tỷ lệ chuyển đổi nếu có.
- Đơn quá hạn.

Không chỉ xếp hạng theo doanh số tuyệt đối; cho phép xem hiệu suất so với mục tiêu.

---

## 17. Team performance privacy

- Chỉ quản lý có permission mới xem toàn đội.
- Nhân viên chỉ xem dữ liệu cá nhân trừ khi policy cho phép.
- Không hiển thị thông tin lương, hoa hồng hoặc PII trong bảng mặc định.
- Export phải tuân cùng scope.

---

## 18. Salesperson detail drawer

Khi click nhân viên:

- KPI cá nhân.
- Target progress.
- Revenue trend.
- Orders by status.
- Overdue tasks.
- Top products.
- Customer follow-ups.

Drawer giữ scope và không mở dữ liệu ngoài quyền.

---

## 19. Pipeline summary

Chỉ dùng nếu hệ thống có lead/opportunity chuẩn.

Stages ví dụ:

```text
Lead mới
Đang tư vấn
Đã gửi báo giá
Đang đàm phán
Đã chốt
Thất bại
```

Metrics:

- Số cơ hội.
- Giá trị pipeline.
- Weighted value.
- Conversion rate.
- Average age.

Nếu chưa có dữ liệu pipeline chuẩn, không suy pipeline từ đơn hàng.

---

## 20. Customer Follow-up

Danh sách khách cần chăm sóc:

- Khách chưa phản hồi báo giá.
- Khách có đơn bị giao thất bại.
- Khách mua định kỳ đến chu kỳ mua lại.
- Khách có khiếu nại chưa giải quyết.
- Khách VIP không phát sinh đơn trong thời gian bất thường.

Mỗi item:

- Customer name hoặc company.
- Reason.
- Last interaction.
- Due time.
- Assigned salesperson.
- Action gọi/nhắn/xem hồ sơ nếu được phép.

---

## 21. Customer privacy

- Chỉ hiển thị số điện thoại đầy đủ khi role cần xử lý.
- Không hiển thị địa chỉ đầy đủ trong widget.
- Không gửi PII vào analytics.
- Backend áp masking trước khi trả dữ liệu.

---

## 22. Best Products

Hiển thị top 5–10 sản phẩm theo:

- Doanh thu.
- Số lượng.
- Lợi nhuận nếu có quyền.
- Tăng trưởng.

Mỗi row:

- Thumbnail.
- Product name.
- SKU.
- Quantity.
- Revenue.
- Trend.
- Stock indicator nhẹ.

---

## 23. Product availability for sales

Sales cần biết khả năng bán, không cần toàn bộ dữ liệu kho.

Trạng thái đề xuất:

```text
Còn hàng
Sắp hết
Hết hàng
Cần kiểm tra
Dữ liệu tồn cũ
```

Không hiển thị số tồn chi tiết nếu role không có permission.

---

## 24. Lost sales risk

Widget tùy chọn:

- Sản phẩm bán chạy nhưng sắp hết.
- Đơn bị chậm vì thiếu hàng.
- Doanh thu tiềm năng bị ảnh hưởng.

Nếu có impact value, phải ghi rõ đây là ước tính.

---

## 25. Recent Orders

Bảng gồm:

- Mã đơn.
- Khách hàng.
- Nhân viên phụ trách.
- Trạng thái.
- Thanh toán.
- Tổng tiền.
- Tuổi đơn.
- Actions.

Sales Staff chỉ thấy đơn assigned hoặc self scope.

Sales Manager có thể xem team/branch scope theo quyền.

---

## 26. Order quick actions

Có thể gồm:

- Xem.
- Xác nhận.
- Gán người phụ trách.
- Liên hệ khách.
- In báo giá/đơn.

Không cho hủy hoặc hoàn tiền thiếu confirm và permission.

Backend trả `availableActions`.

---

## 27. Overdue order rules

Đơn quá hạn cần indicator dựa trên SLA.

Ví dụ:

```text
Đơn mới chưa xác nhận sau 30 phút
Đơn đã xác nhận chưa xử lý sau 4 giờ
Đơn chờ giao quá 24 giờ
```

SLA phải cấu hình được theo loại đơn hoặc chi nhánh nếu nghiệp vụ cần.

---

## 28. Lead source / Sales channel

Nếu có dữ liệu:

- Website.
- Zalo Mini App.
- Zalo OA.
- Điện thoại.
- Cửa hàng.
- Giới thiệu.
- Khác.

Chart horizontal bar hoặc stacked bar.

Không hiển thị nguồn nếu tracking chưa đáng tin cậy.

---

## 29. Conversion metrics

Các định nghĩa phải rõ:

```text
Lead → Opportunity
Opportunity → Quote
Quote → Order
Order → Completed
```

Không dùng một tỷ lệ `conversion` mơ hồ.

Tooltip phải giải thích numerator và denominator.

---

## 30. Cancellation analysis

Widget tùy chọn:

- Tỷ lệ hủy.
- Lý do hủy.
- Giá trị đơn hủy.
- Nhân tố theo sản phẩm/chi nhánh/kênh.

Lý do hủy phải dùng taxonomy ổn định, không chỉ free text.

---

## 31. Sales alerts

Thông báo quan trọng:

- Đơn mới assigned.
- Đơn quá hạn.
- Khách cần follow-up.
- Mục tiêu có nguy cơ không đạt.
- Sản phẩm bán chạy sắp hết.
- Báo giá sắp hết hạn.
- Thanh toán thất bại.

Không toast mọi event; dùng Notification Center và Priority Actions.

---

## 32. Sales staff task center

Task types:

```text
confirm_order
follow_up_customer
send_quote
collect_payment_info
resolve_delivery_issue
update_customer_record
```

Task contract:

```ts
export type SalesTask = {
  id: string;
  type: string;
  title: string;
  entityType?: "order" | "customer" | "quote";
  entityId?: string;
  priority: "critical" | "high" | "medium" | "low";
  dueAt?: string;
  assignedUserId: string;
  status: "open" | "in_progress" | "completed" | "dismissed";
  availableActions: string[];
};
```

---

## 33. Sales summary contract

```ts
export type SalesDashboardData = {
  scope: DashboardScope;
  target: SalesTargetSummary;
  kpis: DashboardKPI[];
  revenueTrend: RevenueChartData;
  orderStatus: OrderStatusSummary;
  priorityActions: SalesTask[];
  teamPerformance?: SalespersonPerformance[];
  bestProducts: ProductPerformanceItem[];
  customerFollowUps: CustomerFollowUpItem[];
  recentOrders: DashboardOrderRow[];
  updatedAt: string;
};
```

---

## 34. Sales target contract

```ts
export type SalesTargetSummary = {
  targetId: string;
  periodType: "month" | "quarter" | "year";
  actualRevenue: number;
  targetRevenue: number;
  completionPercentage: number;
  timeProgressPercentage: number;
  remainingAmount: number;
  forecastRevenue?: number;
  forecastConfidence?: "high" | "medium" | "low";
  updatedAt: string;
};
```

---

## 35. Salesperson performance contract

```ts
export type SalespersonPerformance = {
  userId: string;
  displayName: string;
  avatarUrl?: string;
  revenue: number;
  targetRevenue?: number;
  targetCompletion?: number;
  orders: number;
  averageOrderValue: number;
  conversionRate?: number;
  overdueTasks: number;
  trendPercent?: number;
};
```

---

## 36. Customer follow-up contract

```ts
export type CustomerFollowUpItem = {
  id: string;
  customerId: string;
  customerName: string;
  companyName?: string;
  maskedPhone?: string;
  reason: string;
  priority: "critical" | "high" | "medium" | "low";
  lastInteractionAt?: string;
  dueAt?: string;
  assignedUserId: string;
  href: string;
  availableActions: string[];
};
```

---

## 37. Permissions

Đề xuất:

```text
sales.dashboard.view
sales.team.view
sales.targets.view
sales.targets.manage
orders.view
orders.view_all
orders.update
orders.assign
customers.view
customers.view_pii
reports.sales.export
products.view
inventory.sales_availability.view
```

Không kiểm tra bằng role name duy nhất.

---

## 38. Scope behavior

### Sales Manager

Có thể có:

```text
branch
team
all assigned salespeople
```

### Sales Staff

Mặc định:

```text
assigned hoặc self
```

Filter không được mở rộng ngoài backend scope.

---

## 39. Loading states

- KPI skeleton.
- Target progress skeleton.
- Revenue chart placeholder.
- Task list skeleton.
- Orders rows skeleton.

Không hiển thị mục tiêu 0 hoặc doanh thu 0 khi chưa tải.

---

## 40. Empty states

### Sales Staff chưa có đơn

```text
Bạn chưa có đơn hàng được phân công.
```

### Không có task

```text
Không có công việc ưu tiên đang chờ xử lý.
```

### Chưa có target

```text
Chưa thiết lập mục tiêu bán hàng cho kỳ này.
```

### Không có follow-up

```text
Không có khách hàng nào cần liên hệ trong thời điểm này.
```

---

## 41. Error states

- Widget lỗi độc lập.
- Team Performance lỗi không làm mất My Orders.
- Target API lỗi hiển thị `Chưa thể tải mục tiêu`.
- Có retry và request ID.
- Không thay dữ liệu lỗi bằng dữ liệu kỳ trước thiếu label.

---

## 42. Stale data

Ngưỡng tham khảo:

| Dataset | Stale threshold |
|---|---:|
| Orders | 2–5 phút |
| Tasks | 2–5 phút |
| Product availability | 5–15 phút |
| Revenue KPI | 15–30 phút |
| Target progress | 15–60 phút |

Product availability stale phải hiển thị rõ vì có thể ảnh hưởng tư vấn khách.

---

## 43. Responsive behavior

### ≥1440px

- KPI 4–6 cột.
- Revenue/Status 8/4.
- Team/Priority 7/5.

### 1024–1439px

- KPI 2–3 cột.
- Charts xếp dọc khi cần.
- Team table scroll riêng.

### 768–1023px

- Sidebar drawer.
- KPI 2 cột.
- Widget một cột.
- Toolbar wrap.

### <768px

Sales Staff mobile fallback ưu tiên:

1. Target.
2. KPI.
3. Priority tasks.
4. My orders.
5. Customer follow-up.

---

## 44. Accessibility

- KPI có accessible summary.
- Target progress có text ngoài progress bar.
- Chart có summary hoặc table thay thế.
- Orders table dùng semantic table.
- Task priority không chỉ dùng màu.
- Icon-only actions có label.
- Drawer và dropdown quản lý focus.
- Touch target tối thiểu 44px.
- Zoom 200% không mất action chính.

---

## 45. Search integration

Global Search ưu tiên theo role:

### Sales Manager

- Orders.
- Customers.
- Salespeople.
- Products.

### Sales Staff

- My orders.
- Assigned customers.
- Products.
- Create order command.

Search phải tuân scope và không log raw query.

---

## 46. Notification integration

Notification Center filter theo Sales:

- Đơn hàng.
- Khách hàng.
- Thanh toán liên quan.
- Sản phẩm thiếu hàng.

Unread notification không tự resolve khi mở Dashboard.

Priority Actions có thể lấy từ notification actionable hoặc task service, nhưng cần tránh duplicate.

---

## 47. Analytics events

```text
sales.dashboard.viewed
sales.filter.applied
sales.target.opened
sales.task.opened
sales.task.completed
sales.team_member.opened
sales.order.quick_view_opened
sales.customer.follow_up_started
sales.report.export_requested
```

Không gửi customer name, phone, order ID hoặc giá trị tài chính chi tiết vào analytics.

---

## 48. Observability

Theo dõi:

- Sales Dashboard load success.
- Target API latency.
- Orders freshness.
- Task completion mutation failure.
- Team Performance contract errors.
- Search success by role.
- Export failure.
- Permission denied anomalies.

---

## 49. Security và privacy

- Backend enforce assigned/team/branch scope.
- Không trả dữ liệu toàn đội rồi lọc client.
- PII masking theo role.
- Export kiểm tra quyền lại.
- Team performance chỉ cho người có permission.
- Cache key gồm user, team, branch và date range.
- Không lưu customer data đầy đủ trong browser storage.

---

## 50. Testing

### Unit

- Target completion.
- Time progress.
- Forecast display rules.
- Priority sorting.
- SLA overdue calculation.
- Sales scope serialization.

### Component

- KPI states.
- Target Progress.
- Team Performance.
- Priority Actions.
- Customer Follow-up.
- Recent Orders.

### Integration

- Manager filter team member.
- Staff chỉ thấy assigned orders.
- Target và revenue cập nhật cùng filter.
- Task completion refresh list.
- Product stale state.
- Export đúng scope.

### E2E

- Sales Manager mở Dashboard.
- Chọn nhân viên.
- Mở đơn quá hạn.
- Xem team performance.
- Export report.
- Sales Staff hoàn thành task follow-up.
- Tạo đơn từ command center.

---

## 51. Visual QA

Baseline:

```text
Sales Manager — 1440 × 900
Sales Staff — 1440 × 900
Sales Manager — 1024 × 768
Sales Staff Mobile Fallback
Target Not Configured
No Priority Tasks
Partial Widget Error
Stale Product Availability
Long Customer Names
Large Revenue Values
```

Kiểm tra:

- Target hierarchy.
- KPI alignment.
- Team table readability.
- Task priority.
- Orders overflow.
- Long Vietnamese text.
- Focus states.

---

## 52. Performance budgets

Mục tiêu tham khảo:

```text
Dashboard LCP p75 < 2,5s
Sales overview API p95 < 1,5s
Task mutation feedback < 1s trong điều kiện bình thường
Search p95 < 1s
Filter UI response < 200ms
```

Lazy-load Team Performance hoặc chart dưới fold nếu cần.

---

## 53. AI coding agent requirements

AI coding agent phải:

- Phân biệt Sales Manager và Sales Staff scope.
- Không hardcode mục tiêu hoặc KPI formula.
- Không hiển thị dữ liệu team cho staff thiếu quyền.
- Không tạo pipeline nếu backend chưa có dữ liệu lead/opportunity chuẩn.
- Không tạo forecast giả.
- Không đánh dấu task hoàn tất trước backend confirmation nếu action quan trọng.
- Bổ sung test cho assigned scope, stale stock và overdue SLA.
- Báo rõ API, permissions, scope và phần backend còn thiếu.

---

## 54. Acceptance checklist

- [ ] Có biến thể Sales Manager và Sales Staff.
- [ ] Toolbar đúng theo quyền.
- [ ] KPI và target progress rõ ràng.
- [ ] Revenue Trend và Order Status đầy đủ.
- [ ] Có Priority Actions.
- [ ] Có Team Performance cho manager.
- [ ] Có My Orders cho staff.
- [ ] Có Customer Follow-up.
- [ ] Có Best Products và product availability.
- [ ] Recent Orders tuân scope.
- [ ] Pipeline chỉ hiển thị khi có dữ liệu chuẩn.
- [ ] SLA và overdue rules được định nghĩa.
- [ ] Permissions và assigned/team/branch scope được backend enforce.
- [ ] Loading, empty, error và stale states đầy đủ.
- [ ] Responsive và accessibility hoàn chỉnh.
- [ ] Analytics, observability, security và privacy được xác định.
- [ ] Unit, component, integration, E2E và Visual QA đầy đủ.

---

## 55. Những điều không được làm

- Không hiển thị toàn đội cho sales staff thiếu quyền.
- Không dùng doanh số tuyệt đối làm thước đo duy nhất.
- Không tạo forecast hoặc pipeline giả.
- Không hiển thị số 0 khi API chưa tải.
- Không toast mọi đơn hoặc task mới.
- Không hiển thị PII dư thừa trong widget.
- Không tự suy available actions chỉ từ status.
- Không để filter mở rộng scope.
- Không dùng dữ liệu tồn kho stale mà thiếu cảnh báo.
- Không thay CRM hoặc trang Orders bằng Dashboard tổng hợp.

---

## 56. Kết luận

Sales Dashboard của Cynca VLXD phải giúp quản lý nhìn thấy hiệu suất đội ngũ và giúp nhân viên bán hàng biết ngay việc cần làm tiếp theo. Thiết kế cần cân bằng giữa mục tiêu, doanh số, đơn hàng, khách hàng và khả năng cung ứng sản phẩm, đồng thời giữ chặt permission, scope và độ mới của dữ liệu.

File tiếp theo đề xuất:

```text
README.md
34-Warehouse-Dashboard.md
```