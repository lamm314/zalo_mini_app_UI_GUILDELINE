# Cynca VLXD Admin Dashboard — Executive Dashboard Specification

> Phiên bản: 1.0  
> Phạm vi: Dashboard điều hành dành cho Ban lãnh đạo, Tổng giám đốc và quản lý cấp cao  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Data, Finance, QA và AI coding agent  
> Tài liệu liên quan: `05-KPI-Cards.md`, `06-Charts.md`, `16-Data-Contracts.md`, `27-Role-Based-Dashboard.md`, `28-Dashboard-Configuration-Schema.md`

---

## 1. Mục tiêu

Executive Dashboard giúp Ban lãnh đạo Cynca VLXD nắm được tình hình kinh doanh, tài chính và vận hành trong thời gian ngắn mà không phải đọc nhiều báo cáo chi tiết.

Dashboard phải trả lời nhanh các câu hỏi:

- Doanh thu hiện tại đang tăng hay giảm?
- Lợi nhuận gộp có đạt mục tiêu không?
- Đơn hàng, khách hàng và tồn kho có dấu hiệu bất thường không?
- Chi nhánh nào hoạt động tốt hoặc cần can thiệp?
- Nhóm sản phẩm nào tạo doanh thu và lợi nhuận chính?
- Công nợ, dòng tiền và tồn kho đang tạo rủi ro gì?
- Những vấn đề nào cần Ban lãnh đạo ra quyết định ngay?

Executive Dashboard không thay thế báo cáo tài chính, báo cáo kế toán hoặc module vận hành chi tiết. Đây là lớp tổng hợp phục vụ quyết định.

---

## 2. Đối tượng sử dụng

### Ban Tổng giám đốc

- Xem toàn hệ thống.
- Theo dõi mục tiêu và xu hướng.
- Xem cảnh báo cấp cao.
- Drill-down tới chi nhánh hoặc nhóm sản phẩm.

### Giám đốc kinh doanh

- Theo dõi doanh thu, đơn hàng và hiệu suất bán hàng.
- So sánh chi nhánh và nhóm sản phẩm.

### Giám đốc tài chính

- Theo dõi lợi nhuận, công nợ, dòng tiền và chất lượng doanh thu.

### Giám đốc vận hành

- Theo dõi tồn kho, tiến độ xử lý đơn và hiệu suất chi nhánh.

Mỗi người dùng vẫn chỉ thấy dữ liệu trong permission và scope được cấp.

---

## 3. Nguyên tắc thiết kế

1. Ưu tiên quyết định, không ưu tiên số lượng widget.
2. Tối đa 5–6 KPI chính trong vùng đầu.
3. Mỗi KPI phải có định nghĩa và kỳ so sánh.
4. Cảnh báo quan trọng phải xuất hiện trước báo cáo thứ cấp.
5. Không dùng quá nhiều màu.
6. Không dùng chart 3D, gauge trang trí hoặc infographic khó đọc.
7. Không hiển thị số liệu chưa xác thực như dữ liệu chính thức.
8. Mọi chỉ số tài chính phải có timestamp và scope.
9. Drill-down phải giữ nguyên filter.
10. Không để số compact che mất giá trị đầy đủ.

---

## 4. Information hierarchy

Thứ tự ưu tiên:

```text
1. Tình trạng tổng thể
2. KPI tài chính và kinh doanh
3. Xu hướng và so sánh
4. Rủi ro cần quyết định
5. Hiệu suất chi nhánh và sản phẩm
6. Tồn kho, công nợ và vận hành
7. Báo cáo chi tiết
```

Vùng nhìn thấy đầu tiên phải cho phép người dùng hiểu trạng thái toàn công ty trong khoảng 10 giây.

---

## 5. Layout tổng thể

Desktop 1440px:

```text
Page Toolbar
Executive Summary Banner
KPI Grid 4–6 cards
Revenue & Profit Trend         8 columns
Business Health / Targets      4 columns
Branch Performance             7 columns
Risk & Attention Center        5 columns
Product & Category Mix         6 columns
Cash / Debt / Inventory        6 columns
Executive Notes / Reports     12 columns
```

Dashboard dùng grid 12 cột và gap theo token hiện có.

---

## 6. Page toolbar

### Bên trái

- Tiêu đề `Tổng quan điều hành`.
- Mô tả scope hiện tại.
- Thời điểm cập nhật cuối.

### Bên phải

- Date range.
- Branch scope.
- Compare mode.
- Currency nếu tương lai hỗ trợ đa tiền tệ.
- Export.
- Refresh.

Không hiển thị các action vận hành nhỏ như tạo đơn hoặc nhập kho trong Executive Toolbar.

---

## 7. Date range presets

Preset đề xuất:

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

Khi thay date range, toàn bộ KPI và chart phải cập nhật cùng scope, trừ widget có ghi rõ dữ liệu snapshot khác.

---

## 8. Executive Summary Banner

Banner tóm tắt bằng ngôn ngữ tự nhiên, tối đa 3–4 ý.

Ví dụ:

```text
Doanh thu tháng này tăng 12,5% so với tháng trước, chủ yếu nhờ nhóm thép xây dựng. Tuy nhiên biên lợi nhuận gộp giảm 1,8 điểm phần trăm và công nợ quá hạn tăng tại hai chi nhánh.
```

Yêu cầu:

- Dựa trên dữ liệu có nguồn rõ ràng.
- Không tự suy diễn thiếu bằng chứng.
- Tách fact và recommendation.
- Có timestamp.
- Có link tới chỉ số liên quan.

Nếu dùng AI để tạo summary, phải có guardrail, nguồn dữ liệu và trạng thái `Được tạo tự động` rõ ràng.

---

## 9. KPI chính

KPI mặc định:

1. Doanh thu thuần.
2. Lợi nhuận gộp.
3. Biên lợi nhuận gộp.
4. Tổng đơn hàng.
5. Giá trị đơn trung bình.
6. Công nợ quá hạn hoặc dòng tiền thuần tùy phạm vi dữ liệu.

Không hiển thị quá 6 KPI ở vùng đầu.

---

## 10. KPI card anatomy

Mỗi card gồm:

- Label.
- Value.
- Trend.
- Kỳ so sánh.
- Target progress nếu có.
- Tooltip định nghĩa.
- Updated time hoặc stale notice.
- Drill-down link.

Ví dụ:

```text
Doanh thu thuần
12,84 tỷ ₫
+12,5% so với tháng trước
86% kế hoạch tháng
```

---

## 11. KPI semantic rules

Không suy trend chỉ từ dấu cộng hoặc trừ.

Ví dụ:

- Doanh thu tăng: thường positive.
- Công nợ quá hạn tăng: negative.
- Tồn kho tăng: neutral hoặc negative tùy mục tiêu.
- Tỷ lệ hủy giảm: positive.

Backend nên trả semantic trend hoặc business rule được chuẩn hóa.

---

## 12. Revenue & Profit Trend

Chart chính dùng line hoặc area chart.

Series đề xuất:

- Doanh thu thuần.
- Lợi nhuận gộp.
- Kỳ so sánh tùy chọn.

Controls:

- Daily/weekly/monthly granularity.
- Revenue/profit toggle nếu không đủ không gian.
- Compare mode.

Tooltip hiển thị:

- Thời gian.
- Giá trị đầy đủ.
- Chênh lệch tuyệt đối.
- Chênh lệch phần trăm.

---

## 13. Target tracking

Có thể hiển thị:

- Kế hoạch tháng.
- Kế hoạch quý.
- Kế hoạch năm.

Component đề xuất:

```text
TargetProgressCard
├── Actual
├── Target
├── CompletionPercentage
├── Forecast
└── Gap
```

Không dùng gauge tròn lớn nếu progress bar hoặc bullet chart đọc nhanh hơn.

---

## 14. Forecast

Forecast chỉ hiển thị khi:

- Có mô hình hoặc logic rõ ràng.
- Có khoảng tin cậy hoặc mức độ chắc chắn nếu phù hợp.
- Có timestamp và nguồn.
- Có thể giải thích yếu tố chính.

Không trình bày forecast như số chắc chắn.

Ví dụ:

```text
Dự báo doanh thu tháng: 15,2–16,0 tỷ ₫
```

---

## 15. Business Health Card

Tóm tắt trạng thái theo 4–6 khía cạnh:

- Doanh thu.
- Lợi nhuận.
- Đơn hàng.
- Công nợ.
- Tồn kho.
- Vận hành.

Mỗi mục dùng trạng thái:

```text
Tốt
Cần chú ý
Rủi ro
Chưa đủ dữ liệu
```

Không chỉ dùng màu; phải có label và lý do ngắn.

---

## 16. Branch Performance

Hiển thị bảng hoặc bar chart xếp hạng chi nhánh.

Metrics có thể chọn:

- Doanh thu.
- Lợi nhuận.
- Tăng trưởng.
- Đơn hàng.
- Giá trị đơn trung bình.
- Công nợ quá hạn.

Mỗi dòng:

- Tên chi nhánh.
- Giá trị hiện tại.
- Trend.
- Target progress.
- Rank.
- Risk indicator.

Không tạo leaderboard gây hiểu sai nếu quy mô chi nhánh khác nhau; cho phép xem cả giá trị tuyệt đối và tỷ lệ tăng trưởng.

---

## 17. Branch comparison matrix

Có thể dùng heatmap có kiểm soát:

| Chi nhánh | Doanh thu | Lợi nhuận | Đơn hàng | Công nợ | Tồn kho |
|---|---:|---:|---:|---:|---:|
| Hà Nội |  |  |  |  |  |
| Hưng Yên |  |  |  |  |  |

Yêu cầu:

- Có số liệu bên cạnh màu.
- Có legend.
- Hỗ trợ keyboard và screen-reader summary.
- Không dùng quá nhiều gradient khó phân biệt.

---

## 18. Risk & Attention Center

Widget quan trọng dành cho quyết định.

Nhóm rủi ro:

- Doanh thu giảm mạnh.
- Biên lợi nhuận giảm.
- Công nợ quá hạn tăng.
- Tồn kho âm hoặc tồn quá cao.
- Đơn hủy tăng.
- Chi nhánh không đạt kế hoạch.
- Đồng bộ dữ liệu lỗi.

Mỗi risk item gồm:

- Severity.
- Tiêu đề.
- Tác động.
- Scope.
- Thời điểm phát hiện.
- Action hoặc drill-down.

---

## 19. Risk prioritization

Điểm ưu tiên có thể dựa trên:

```text
Severity × Financial Impact × Urgency × Confidence
```

Không hiển thị công thức phức tạp cho lãnh đạo nếu không cần, nhưng tooltip phải giải thích logic xếp hạng.

Rủi ro có dữ liệu không chắc chắn phải được đánh dấu confidence thấp.

---

## 20. Product & Category Mix

Mục tiêu:

- Nhóm sản phẩm tạo doanh thu chính.
- Nhóm tạo lợi nhuận tốt.
- Sản phẩm tăng trưởng nhanh.
- Mức độ phụ thuộc vào một vài nhóm hàng.

Chart đề xuất:

- Horizontal bar.
- Treemap có label rõ nếu số nhóm vừa phải.
- Pareto chart.

Không dùng pie chart với quá nhiều category.

---

## 21. Product concentration risk

Có thể hiển thị:

```text
Top 5 sản phẩm chiếm 63% doanh thu.
```

Giúp nhận biết rủi ro phụ thuộc.

Drill-down:

- Theo doanh thu.
- Theo lợi nhuận.
- Theo số lượng.
- Theo chi nhánh.

---

## 22. Customer concentration

Nếu có dữ liệu và quyền:

- Top customer revenue share.
- Customer retention.
- New vs returning customers.
- Overdue debt concentration.

Không hiển thị PII chi tiết ở Executive Dashboard mặc định.

Có thể hiển thị khách hàng theo mã hoặc tên doanh nghiệp nếu quyền cho phép.

---

## 23. Cash and Debt Overview

KPI/Widgets đề xuất:

- Tổng phải thu.
- Đã thu trong kỳ.
- Công nợ đến hạn.
- Công nợ quá hạn.
- DSO nếu có dữ liệu.
- Hoàn tiền hoặc payment failures.

Chart:

- Debt aging buckets.
- Cash collection trend.

Dữ liệu kế toán phải có định nghĩa và kỳ chốt rõ.

---

## 24. Debt aging

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
- Số khách hàng hoặc đơn liên quan.

Không cho người không có `finance.debt.view` truy cập.

---

## 25. Inventory Overview

Thông tin cấp điều hành:

- Tổng giá trị tồn kho.
- Tồn kho chậm luân chuyển.
- Hết hàng/sắp hết.
- Tồn âm.
- Tỷ lệ quay vòng hàng tồn kho nếu có.
- Kho có dữ liệu stale.

Không hiển thị quá nhiều SKU trong Executive Dashboard; chỉ hiển thị summary và exceptions.

---

## 26. Inventory balance indicator

Có thể chia:

```text
Thiếu hàng
Cân bằng
Tồn cao
Chậm luân chuyển
```

Phải dựa trên rule hoặc forecast có tài liệu.

Không gắn label `tồn cao` chỉ dựa trên số lượng tuyệt đối nếu sản phẩm có quy mô khác nhau.

---

## 27. Order Operations Summary

Metrics:

- Đơn mới.
- Đơn hoàn thành.
- Đơn hủy.
- Đơn quá hạn xử lý.
- Tỷ lệ giao thành công.
- Thời gian xử lý trung vị.

Executive view chỉ hiển thị exceptions và trend, không thay thế bảng đơn hàng vận hành.

---

## 28. Sales Funnel

Nếu hệ thống có lead hoặc opportunity:

```text
Lead
→ Báo giá
→ Đơn xác nhận
→ Đơn hoàn thành
```

Metrics:

- Conversion rate.
- Drop-off.
- Average time per stage.
- Value per stage.

Nếu chưa có dữ liệu lead chuẩn, không dựng funnel giả từ đơn hàng.

---

## 29. Operational Efficiency

Chỉ số tham khảo:

- Thời gian xác nhận đơn.
- Thời gian chuẩn bị hàng.
- Thời gian giao hàng.
- Tỷ lệ đơn quá SLA.
- Tỷ lệ lỗi nhập/xuất.
- Tỷ lệ đồng bộ dữ liệu thành công.

So sánh theo chi nhánh hoặc kho khi có ý nghĩa.

---

## 30. Executive Notes

Cho phép người có quyền ghi chú ngắn:

- Quyết định.
- Vấn đề cần theo dõi.
- Cam kết hành động.
- Người phụ trách.
- Hạn xử lý.

Không biến ghi chú thành hệ thống quản lý dự án đầy đủ.

Ghi chú nhạy cảm cần permission và audit log.

---

## 31. Decision action center

Có thể hiển thị các action cấp lãnh đạo:

- Xem báo cáo chi tiết.
- Giao người phụ trách.
- Tạo follow-up task.
- Xuất báo cáo họp.
- Ghi chú quyết định.

Không đưa action vận hành như xác nhận đơn, điều chỉnh tồn hoặc hoàn tiền vào vùng executive mặc định.

---

## 32. Scheduled reports

Danh sách:

- Báo cáo tuần.
- Báo cáo tháng.
- Báo cáo tài chính vận hành.
- Báo cáo chi nhánh.

Mỗi item:

- Tên báo cáo.
- Kỳ dữ liệu.
- Trạng thái.
- Người tạo.
- Download.
- Expiry.

Download phải tuân thủ quyền và signed URL có thời hạn.

---

## 33. Executive export

Format:

```text
PDF
XLSX
CSV cho dữ liệu chi tiết
```

PDF nên gồm:

- Scope.
- Thời gian dữ liệu.
- KPI.
- Charts.
- Risk summary.
- Notes tùy chọn.

Không đưa dữ liệu chi tiết ngoài quyền vào file export.

---

## 34. Presentation mode

Có thể có chế độ trình chiếu cho cuộc họp:

- Ẩn Sidebar.
- Tăng vùng nội dung.
- Giữ Header tối giản.
- Chuyển section bằng keyboard.
- Hiển thị data timestamp.

Presentation mode không được ẩn cảnh báo stale hoặc scope.

---

## 35. Data freshness

Mỗi loại dữ liệu có freshness riêng:

| Dataset | Ngưỡng tham khảo |
|---|---:|
| Orders | 2–5 phút |
| Inventory | 5–15 phút |
| Revenue KPI | 15–60 phút |
| Finance close data | Theo kỳ chốt |
| Forecast | Theo thời gian chạy mô hình |

Khi stale:

- Giữ số cũ nếu an toàn.
- Hiển thị thời gian cập nhật.
- Không trộn dữ liệu mới và cũ mà không cảnh báo.

---

## 36. Data confidence

Một số chỉ số có thể có confidence:

```text
High
Medium
Low
```

Ví dụ forecast hoặc dữ liệu đang đối soát.

Không dùng confidence để hợp thức hóa dữ liệu sai; chỉ dùng khi bản chất chỉ số là ước tính.

---

## 37. Executive Dashboard data contract

```ts
export type ExecutiveDashboardData = {
  scope: DashboardScope;
  summary: ExecutiveSummary;
  kpis: ExecutiveKPI[];
  revenueProfitTrend: RevenueProfitTrend;
  targetProgress: TargetProgress[];
  branchPerformance: BranchPerformanceItem[];
  risks: ExecutiveRiskItem[];
  productMix: ProductMixItem[];
  debtOverview?: DebtOverview;
  inventoryOverview: InventoryOverview;
  operationsSummary: OperationsSummary;
  updatedAt: string;
};
```

---

## 38. Executive KPI contract

```ts
export type ExecutiveKPI = {
  id: string;
  label: string;
  value: number | null;
  valueType: "currency" | "number" | "percentage";
  currency?: "VND";
  trend?: "positive" | "negative" | "neutral";
  trendValue?: number;
  comparisonLabel?: string;
  targetValue?: number;
  targetCompletion?: number;
  forecastValue?: number;
  availability: "available" | "not_calculated" | "not_applicable" | "permission_denied";
  updatedAt: string;
};
```

---

## 39. Executive risk contract

```ts
export type ExecutiveRiskItem = {
  id: string;
  category: "revenue" | "profit" | "debt" | "inventory" | "orders" | "branch" | "system";
  severity: "critical" | "high" | "medium" | "low";
  title: string;
  summary: string;
  impactValue?: number;
  impactType?: "currency" | "percentage" | "count";
  scopeLabel?: string;
  confidence?: "high" | "medium" | "low";
  detectedAt: string;
  href?: string;
  actionLabel?: string;
};
```

---

## 40. Permissions

Đề xuất:

```text
executive.dashboard.view
finance.view
finance.gross_profit.view
finance.debt.view
inventory.executive.view
branches.compare
reports.executive.export
executive.notes.view
executive.notes.update
```

Không dựa duy nhất vào role name `director` hoặc `admin`.

---

## 41. Role variations

### CEO preset

- Toàn cảnh.
- Revenue/profit.
- Branch performance.
- Risk center.
- Strategic notes.

### CFO preset

- Revenue quality.
- Gross profit.
- Debt aging.
- Cash collection.
- Financial risks.

### COO preset

- Inventory.
- Order SLA.
- Warehouse/branch efficiency.
- Operational risks.

### Sales Director preset

- Revenue.
- Orders.
- Customer/product mix.
- Branch/team performance.

Dùng cùng component registry, khác cấu hình và quyền.

---

## 42. Responsive behavior

### ≥1440px

- KPI 4–6 cột.
- Chart 8/4.
- Branch/Risk 7/5.

### 1024–1439px

- KPI 2–3 cột.
- Chart xếp 12 cột nếu cần.
- Bảng chi nhánh scroll trong wrapper.

### 768–1023px

- Sidebar drawer.
- KPI 2 cột.
- Widget một cột.

### <768px

- Mobile fallback chỉ giữ summary, KPI, risk và chart chính.
- Không cố hiển thị toàn bộ matrix phức tạp.

---

## 43. Accessibility

- KPI có accessible summary.
- Chart có text summary hoặc data table thay thế.
- Risk severity không chỉ dùng màu.
- Tables có caption và headers.
- Presentation mode vẫn dùng keyboard.
- Focus order theo hierarchy.
- Compact number có full value trong accessible text hoặc tooltip.
- Zoom 200% vẫn đọc được KPI và risk.

---

## 44. Loading states

- Summary banner skeleton.
- KPI skeleton giữ chiều cao.
- Chart placeholder cố định.
- Risk card skeleton.
- Branch table skeleton.

Không hiển thị `0` cho chỉ số chưa tải.

---

## 45. Empty states

### Chưa có dữ liệu

```text
Chưa có đủ dữ liệu để tạo tổng quan điều hành cho phạm vi này.
```

### Không có rủi ro

```text
Không phát hiện rủi ro đáng chú ý trong phạm vi đã chọn.
```

### Không có target

```text
Chưa thiết lập kế hoạch cho kỳ này.
```

Không hiển thị chart giả khi chưa có dữ liệu.

---

## 46. Error handling

- Widget lỗi độc lập.
- Summary lỗi không làm sập KPI.
- Finance API lỗi phải hiển thị rõ số liệu chưa khả dụng.
- Có retry.
- Có request ID.
- Không thay số lỗi bằng số kỳ trước mà thiếu label.

---

## 47. Analytics events

```text
executive.dashboard.viewed
executive.filter.applied
executive.kpi.opened
executive.risk.opened
executive.branch.drilldown_opened
executive.report.export_requested
executive.presentation_mode.opened
executive.note.created
```

Không gửi giá trị tài chính chi tiết hoặc nội dung ghi chú vào analytics.

---

## 48. Observability

Theo dõi:

- Executive Dashboard load success.
- KPI data freshness.
- Finance endpoint latency.
- Summary generation failures.
- Risk detection age.
- Export success.
- Permission denied anomalies.
- Contract validation failures.

---

## 49. Security và privacy

- Backend enforce permission và branch scope.
- Không trả finance data rồi mới ẩn ở frontend.
- Không hiển thị PII mặc định.
- Notes và export có audit log.
- AI summary không được gửi dữ liệu nhạy cảm tới provider chưa được phê duyệt.
- Signed URL có thời hạn.
- Cache key gồm permission và scope.

---

## 50. Testing

### Unit

- KPI semantic trend.
- Target progress.
- Risk sorting.
- Compact value formatting.
- Freshness calculation.

### Component

- KPI states.
- Revenue/profit chart.
- Branch ranking.
- Risk center.
- Target progress.
- Summary banner.

### Integration

- Filter cập nhật toàn Dashboard.
- Role variation.
- Finance permission.
- Drill-down giữ scope.
- Stale data handling.
- Export đúng phạm vi.

### E2E

- CEO mở Dashboard.
- Chọn quý này.
- So sánh cùng kỳ.
- Mở chi nhánh có rủi ro.
- Export PDF.
- Mở presentation mode.

---

## 51. Visual QA

Baseline:

```text
CEO Dashboard — 1440 × 900
CFO Dashboard
COO Dashboard
Sales Director Dashboard
Executive Dashboard — 1024 × 768
Loading
Partial Error
Stale Finance Data
No Risks
Presentation Mode
Large Currency Values
```

Kiểm tra:

- Information hierarchy.
- KPI alignment.
- Risk priority.
- Branch table readability.
- Chart labels.
- Data timestamp.
- Export and presentation controls.

---

## 52. Performance budgets

Mục tiêu tham khảo:

```text
Shell render: dưới 1 giây trên mạng tốt
LCP p75: dưới 2,5 giây
Executive overview API p95: dưới 2 giây
Filter update: phản hồi UI dưới 200ms
Chart interaction: dưới 100ms
```

Có thể lazy-load widget dưới fold.

---

## 53. AI-generated summary requirements

Nếu triển khai summary bằng AI:

- Chỉ dùng dữ liệu đã được permission-filtered.
- Không gửi PII không cần thiết.
- Không tự tạo số liệu.
- Mọi claim phải map được về KPI hoặc risk source.
- Hiển thị thời điểm tạo.
- Có fallback summary rule-based.
- Không dùng AI output để tự động thực hiện mutation.
- Có observability và quality review.

---

## 54. AI coding agent requirements

AI coding agent phải:

- Không tự chọn KPI thiếu định nghĩa.
- Không hardcode số liệu.
- Không dùng chart trang trí khó đọc.
- Không bỏ data freshness và permission states.
- Không tạo forecast giả.
- Không gửi financial data vào analytics.
- Báo rõ API, contracts, permissions và assumptions.
- Bổ sung test cho stale finance data, large values và role variations.
- Không tuyên bố Executive Dashboard hoàn chỉnh nếu backend chưa cung cấp dữ liệu tài chính tin cậy.

---

## 55. Acceptance checklist

- [ ] Executive hierarchy rõ ràng.
- [ ] Có summary banner.
- [ ] Có tối đa 6 KPI chính.
- [ ] Revenue/profit trend đầy đủ.
- [ ] Target progress và forecast được định nghĩa an toàn.
- [ ] Có Business Health Card.
- [ ] Có Branch Performance.
- [ ] Có Risk & Attention Center.
- [ ] Có Product/Category Mix.
- [ ] Có Debt và Inventory Overview theo quyền.
- [ ] Có Operations Summary.
- [ ] Có Executive Notes và scheduled reports nếu được bật.
- [ ] Export và presentation mode đúng.
- [ ] Data freshness và confidence rõ ràng.
- [ ] Permission, scope và privacy được backend enforce.
- [ ] Loading, empty, error và stale states đầy đủ.
- [ ] Accessibility, responsive và performance đạt yêu cầu.
- [ ] Có test và Visual QA theo CEO, CFO, COO và Sales Director.

---

## 56. Những điều không được làm

- Không dùng quá nhiều KPI hoặc chart.
- Không dùng gauge, chart 3D hoặc màu trang trí quá mức.
- Không hiển thị số chưa xác thực như dữ liệu chính thức.
- Không cho người không có quyền xem lợi nhuận hoặc công nợ.
- Không tạo forecast thiếu nguồn hoặc confidence.
- Không dùng AI summary để bịa số liệu.
- Không trộn dữ liệu stale và fresh thiếu cảnh báo.
- Không đưa PII chi tiết vào Executive Dashboard.
- Không xếp hạng chi nhánh thiếu ngữ cảnh quy mô.
- Không thay báo cáo kế toán chính thức bằng Dashboard tổng hợp.

---

## 57. Kết luận

Executive Dashboard của Cynca VLXD phải biến dữ liệu kinh doanh, tài chính và vận hành thành một bức tranh điều hành rõ ràng, có ưu tiên và có khả năng drill-down. Giao diện cần giúp lãnh đạo nhận biết xu hướng, rủi ro và khoảng cách so với mục tiêu mà không làm mất tính chính xác, phạm vi quyền và độ mới của dữ liệu.

File tiếp theo đề xuất:

```text
README.md
33-Sales-Dashboard.md
```