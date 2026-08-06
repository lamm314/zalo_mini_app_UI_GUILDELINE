# Cynca VLXD Admin Dashboard — Charts Specification

> Phiên bản: 1.0  
> Phạm vi: Biểu đồ phân tích trong Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`, `02-Layout.md`, `05-KPI-Cards.md`

---

## 1. Vai trò của biểu đồ

Biểu đồ giúp người quản trị hiểu xu hướng, cơ cấu và biến động của hoạt động kinh doanh theo thời gian.

Mỗi biểu đồ phải trả lời một câu hỏi quản trị rõ ràng, ví dụ:

- Doanh thu đang tăng hay giảm?
- Giai đoạn nào có doanh thu cao nhất?
- Đơn hàng đang ở trạng thái nào?
- Nhóm sản phẩm nào đóng góp nhiều nhất?
- Chi nhánh nào hoạt động tốt hơn?
- Tồn kho có đang giảm bất thường không?

Không dùng biểu đồ chỉ để làm Dashboard trông nhiều dữ liệu hơn.

---

## 2. Bộ biểu đồ mặc định

Dashboard tổng quan sử dụng tối đa 2 biểu đồ chính ở vùng đầu:

1. Biểu đồ doanh thu theo thời gian.
2. Biểu đồ trạng thái đơn hàng.

Các biểu đồ phụ có thể xuất hiện ở khu vực dưới:

- Doanh thu theo danh mục.
- Top sản phẩm.
- Doanh thu theo chi nhánh.
- Tỷ lệ khách hàng mới và quay lại.
- Biến động tồn kho.
- Phương thức thanh toán.

Không hiển thị quá 4 biểu đồ trên màn hình tổng quan mặc định.

---

## 3. Layout biểu đồ

Bố cục đề xuất:

```text
┌──────────────────────────────────────┬───────────────────────┐
│ Revenue chart — 8 columns            │ Order status — 4 cols │
└──────────────────────────────────────┴───────────────────────┘
```

Kích thước card:

```css
min-height: 380px;
border-radius: 20px;
padding: 24px;
```

Hai card cùng hàng phải có chiều cao tương đương.

---

## 4. Cấu trúc chart card

```text
Chart Card
├── Header
│   ├── Title
│   ├── Description / summary
│   └── Controls
├── Metric summary — optional
├── Chart canvas
├── Legend — optional
└── Footer / source / update time
```

Wireframe:

```text
┌──────────────────────────────────────────────┐
│ Doanh thu                                    │
│ 1,28 tỷ ₫ · tăng 12,5%      [Theo tháng ▾]  │
│                                              │
│        ╭───╮                   ╭────╮         │
│   ╭────╯   ╰────╮       ╭──────╯    ╰──      │
│───╯             ╰───────╯                    │
│                                              │
│ T1  T2  T3  T4  T5  T6  T7  T8  T9 ...      │
└──────────────────────────────────────────────┘
```

---

## 5. Chart header

Title:

```css
font-size: 18px;
font-weight: 600;
color: #182230;
```

Description:

```css
font-size: 13px;
font-weight: 400;
color: #7B8797;
```

Controls có thể gồm:

- Khoảng thời gian.
- Đơn vị hiển thị.
- Chọn series.
- So sánh kỳ trước.
- Menu tùy chọn.

Không đặt quá 3 control trong chart header.

---

## 6. Biểu đồ doanh thu

### 6.1 Loại biểu đồ

Dùng line chart hoặc area chart.

Khuyến nghị:

- Một series: line + area fill nhẹ.
- Hai series: hai đường, không dùng area fill chồng lấn quá mạnh.

Không dùng bar chart nếu mục tiêu chính là xem xu hướng liên tục theo thời gian.

### 6.2 Series mặc định

```text
Doanh thu thuần
```

Series so sánh tùy chọn:

```text
Kỳ trước
```

### 6.3 Màu

```css
--chart-primary: #0B57F0;
--chart-primary-fill: rgba(11,87,240,0.10);
--chart-compare: #98A2B3;
```

### 6.4 Đường

```css
stroke-width: 2.5px;
```

Không dùng stroke dày hơn 3px.

### 6.5 Area fill

Gradient rất nhẹ:

```css
linear-gradient(
  180deg,
  rgba(11,87,240,0.18) 0%,
  rgba(11,87,240,0.02) 100%
)
```

### 6.6 Dữ liệu theo thời gian

Khoảng thời gian và độ chi tiết:

| Khoảng thời gian | Granularity đề xuất |
|---|---|
| Hôm nay | Theo giờ |
| 7 ngày | Theo ngày |
| 30 ngày | Theo ngày |
| 3 tháng | Theo tuần |
| 12 tháng | Theo tháng |
| Nhiều năm | Theo quý hoặc năm |

Không hiển thị quá nhiều điểm dữ liệu làm chart khó đọc.

---

## 7. Biểu đồ trạng thái đơn hàng

### 7.1 Loại biểu đồ

Dùng donut chart.

Không dùng pie chart 3D.

### 7.2 Nhóm trạng thái

- Chờ xác nhận.
- Đang xử lý.
- Đang giao.
- Hoàn thành.
- Đã hủy.

Nếu có quá nhiều trạng thái nghiệp vụ, gom vào nhóm lớn ở Dashboard và để chi tiết trong trang báo cáo.

### 7.3 Màu trạng thái

```css
--order-pending: #F79009;
--order-processing: #2E90FA;
--order-shipping: #7A5AF8;
--order-completed: #12B76A;
--order-cancelled: #F04438;
```

Màu phải nhất quán với badge trạng thái trong bảng đơn hàng.

### 7.4 Trung tâm donut

Hiển thị:

```text
1.248
Tổng đơn
```

Value:

```css
font-size: 28px;
font-weight: 700;
color: #182230;
```

Label:

```css
font-size: 12px;
color: #7B8797;
```

### 7.5 Legend

Mỗi item gồm:

- Dot màu.
- Tên trạng thái.
- Số lượng.
- Tỷ lệ phần trăm.

Ví dụ:

```text
● Hoàn thành     824   66,0%
```

---

## 8. Biểu đồ doanh thu theo danh mục

Dùng horizontal bar chart.

Danh mục có thể gồm:

- Xi măng.
- Thép xây dựng.
- Gạch.
- Cát và đá.
- Sơn.
- Tôn và mái.
- Thiết bị phụ trợ.

Sắp xếp giảm dần theo doanh thu.

Không hiển thị quá 8 danh mục trong Dashboard. Phần còn lại gom vào `Khác` hoặc chuyển sang trang báo cáo.

Bar chart:

```css
bar-height: 12px;
border-radius: 999px;
```

---

## 9. Biểu đồ top sản phẩm

Có thể dùng:

- Horizontal bar chart.
- Ranked list có progress bar.

Trên Dashboard, ranked list thường dễ đọc hơn chart đầy đủ.

Mỗi item gồm:

- Xếp hạng.
- Thumbnail.
- Tên sản phẩm.
- Số lượng bán.
- Doanh thu.
- Progress bar tương đối.

Không dùng màu khác nhau cho từng sản phẩm nếu không mang ý nghĩa phân loại.

---

## 10. Biểu đồ doanh thu theo chi nhánh

Dùng grouped bar chart hoặc simple bar chart.

Nếu so sánh kỳ hiện tại với kỳ trước:

- Series 1: kỳ hiện tại, xanh dương.
- Series 2: kỳ trước, xám.

Không dùng hơn 2 series trong chart Dashboard.

Chi nhánh cần được sắp xếp theo doanh thu hoặc giữ thứ tự nghiệp vụ nhất quán.

---

## 11. Biểu đồ khách hàng mới và quay lại

Dùng stacked bar chart hoặc two-line chart.

Series:

- Khách hàng mới.
- Khách hàng quay lại.

Màu:

```css
--customer-new: #0B57F0;
--customer-returning: #7A5AF8;
```

Tooltip phải giải thích rõ cách phân loại khách quay lại.

---

## 12. Biểu đồ tồn kho

Dùng line chart hoặc area chart theo thời gian.

Có thể hiển thị:

- Tổng giá trị tồn kho.
- Số lượng tồn.
- Số SKU dưới ngưỡng.

Không trộn giá trị tiền và số lượng trên cùng một trục nếu không có dual-axis rõ ràng. Ưu tiên tách thành hai chart.

---

## 13. Axes

### 13.1 X-axis

```css
font-size: 11px;
color: #7B8797;
```

Không hiển thị mọi label nếu bị chồng lấn.

### 13.2 Y-axis

Format rút gọn:

```text
0
250 triệu
500 triệu
750 triệu
1 tỷ
```

### 13.3 Axis line

Có thể ẩn axis line nếu gridline đã đủ rõ.

### 13.4 Gridline

```css
stroke: #E7ECF3;
stroke-width: 1px;
stroke-dasharray: 3 3;
```

Gridline phải nhẹ, không cạnh tranh với dữ liệu.

---

## 14. Tooltip

Tooltip phải hiển thị:

- Thời điểm hoặc danh mục.
- Giá trị đầy đủ.
- Series.
- So sánh nếu có.

Ví dụ:

```text
Tháng 7/2026
Doanh thu thuần: 1.280.450.000 ₫
Tăng 12,5% so với tháng trước
```

Styling:

```css
padding: 12px 14px;
border-radius: 12px;
background: #101828;
color: #FFFFFF;
box-shadow: 0 8px 24px rgba(16,24,40,0.18);
```

Tooltip không vượt ngoài viewport.

---

## 15. Legend

Legend chỉ hiển thị khi có từ 2 series hoặc nhiều nhóm trạng thái.

```css
font-size: 12px;
color: #526071;
```

Legend item:

```text
[dot] Label
```

Không dùng legend tách rời quá xa chart.

---

## 16. Data labels

Chỉ hiển thị data label trực tiếp khi:

- Số điểm ít.
- Giá trị cần đọc ngay.
- Donut chart cần tỷ lệ.

Không hiển thị data label cho line chart có nhiều điểm.

---

## 17. Filter và interaction

Các interaction hợp lệ:

- Hover để xem tooltip.
- Click legend để ẩn/hiện series.
- Chọn khoảng thời gian.
- Chọn đơn vị.
- Click điểm dữ liệu để drill-down nếu có.

Khi click drill-down:

- Điều hướng đến báo cáo chi tiết.
- Giữ filter hiện tại qua query params.

Ví dụ:

```text
/reports/revenue?from=2026-07-01&to=2026-07-31
```

---

## 18. Animation

### 18.1 Initial render

```css
duration: 500–700ms;
easing: cubic-bezier(0.2, 0, 0, 1);
```

### 18.2 Data update

```css
duration: 250–400ms;
```

### 18.3 Reduced motion

Khi người dùng bật `prefers-reduced-motion`, giảm hoặc tắt animation.

Không animate liên tục.

---

## 19. Loading state

Chart card loading gồm:

- Skeleton title.
- Skeleton control.
- Placeholder chart area.

```css
.chart-skeleton {
  min-height: 300px;
  border-radius: 12px;
  background: linear-gradient(
    90deg,
    #F2F4F7 25%,
    #EAECF0 37%,
    #F2F4F7 63%
  );
}
```

Không render chart rỗng với axis trước khi có dữ liệu.

---

## 20. Empty state

Ví dụ:

```text
Chưa có dữ liệu doanh thu
Không có đơn hàng trong khoảng thời gian đã chọn.
```

Có thể có action:

```text
[ Chọn khoảng thời gian khác ]
```

Giữ nguyên chiều cao card.

---

## 21. Error state

Ví dụ:

```text
Không thể tải biểu đồ doanh thu
Kết nối đến máy chủ đang gián đoạn.
[ Thử lại ]
```

Chỉ thay nội dung chart card bị lỗi, không làm lỗi toàn Dashboard.

---

## 22. Stale data state

Hiển thị chú thích:

```text
Dữ liệu cập nhật lần cuối lúc 16:30, ngày 06/08/2026
```

Nếu quá ngưỡng:

```text
Dữ liệu có thể chưa cập nhật
```

---

## 23. Responsive behavior

### Viewport ≥ 1440px

- Revenue chart 8 cột.
- Order status 4 cột.
- Chiều cao tối thiểu 380px.

### Viewport 1280–1439px

- Giữ 8/4 nếu đủ rộng.
- Giảm padding card xuống 20px nếu cần.

### Viewport 1024–1279px

- Revenue chart 12 cột.
- Order status 12 cột hoặc 6 cột tùy layout.
- Legend có thể chuyển xuống dưới chart.

### Viewport < 1024px

- Chart xếp một cột.
- Giảm số label trục.
- Không giảm chiều cao chart dưới 280px.

---

## 24. Accessibility

Mỗi chart phải có:

- Tiêu đề rõ ràng.
- Mô tả ngắn bằng text.
- Bảng hoặc summary dữ liệu thay thế khi cần.
- Không chỉ dùng màu để phân biệt series.
- Tooltip có thể truy cập bằng bàn phím nếu chart interactive.

Ví dụ summary:

```text
Doanh thu tăng từ 820 triệu đồng trong tháng 1 lên 1,28 tỷ đồng trong tháng 7. Tháng 5 là tháng có mức giảm duy nhất.
```

SVG chart nên có:

```html
role="img"
aria-labelledby="revenue-chart-title revenue-chart-desc"
```

---

## 25. Color palette cho charts

Series chính:

```css
--chart-series-1: #0B57F0;
--chart-series-2: #7A5AF8;
--chart-series-3: #12B76A;
--chart-series-4: #F79009;
--chart-series-5: #F04438;
--chart-series-6: #2E90FA;
```

Chỉ sử dụng số màu cần thiết cho dữ liệu thực tế.

Không dùng màu decorative không có ý nghĩa.

---

## 26. Format dữ liệu

### Tiền tệ

```text
1,28 tỷ ₫
845 triệu ₫
```

Tooltip hiển thị đầy đủ:

```text
1.280.450.000 ₫
```

### Số lượng

```text
1.248 đơn
```

### Phần trăm

```text
66,0%
```

### Ngày tháng

```text
06/08
Tháng 8
Q3/2026
```

---

## 27. Data contract đề xuất

```ts
export type TimeSeriesPoint = {
  timestamp: string;
  value: number;
};

export type ChartSeries = {
  id: string;
  label: string;
  values: TimeSeriesPoint[];
  semanticColor?: "primary" | "success" | "warning" | "danger" | "neutral";
};

export type RevenueChartData = {
  currency: "VND";
  granularity: "hour" | "day" | "week" | "month" | "quarter" | "year";
  series: ChartSeries[];
  updatedAt: string;
};
```

---

## 28. Component tree đề xuất

```text
ChartCard
├── ChartCardHeader
│   ├── ChartTitleGroup
│   └── ChartControls
├── ChartSummary
├── ChartViewport
│   ├── Axes
│   ├── Gridlines
│   ├── Series
│   └── Tooltip
├── ChartLegend
└── ChartFooter
```

---

## 29. Thư viện đề xuất

Có thể sử dụng:

- Recharts.
- Apache ECharts.
- Nivo.
- Visx.

Tiêu chí chọn:

- Responsive tốt.
- Hỗ trợ accessibility.
- Tooltip tùy biến.
- Hiệu năng ổn định.
- Hỗ trợ SSR hoặc client rendering phù hợp kiến trúc.

Không trộn nhiều thư viện chart trong cùng Dashboard.

---

## 30. Acceptance checklist

- [ ] Mỗi chart trả lời một câu hỏi quản trị rõ ràng.
- [ ] Revenue chart dùng line hoặc area chart.
- [ ] Order status dùng donut chart.
- [ ] Màu trạng thái nhất quán với bảng đơn hàng.
- [ ] Axis và gridline dễ đọc nhưng không quá nổi.
- [ ] Tooltip hiển thị giá trị đầy đủ.
- [ ] Chart có loading, empty, error và stale state.
- [ ] Dữ liệu đúng định dạng Việt Nam.
- [ ] Responsive không làm chart quá thấp.
- [ ] Không quá 2 series trong chart Dashboard mặc định.
- [ ] Có summary text hỗ trợ accessibility.
- [ ] Animation hỗ trợ reduced motion.
- [ ] Legend chỉ xuất hiện khi cần.
- [ ] Drill-down giữ nguyên filter.
- [ ] Không có biểu đồ 3D.
- [ ] Không hiển thị quá 4 chart trên Dashboard tổng quan.

---

## 31. Những điều không được làm

- Không dùng biểu đồ 3D.
- Không dùng quá nhiều màu.
- Không dùng dual-axis nếu không thật sự cần.
- Không bỏ đơn vị khỏi axis và tooltip.
- Không hiển thị legend không liên quan.
- Không để label chồng lên nhau.
- Không dùng chart để thay thế bảng khi người dùng cần số liệu chính xác.
- Không animate liên tục.
- Không dùng donut chart với quá nhiều nhóm nhỏ.
- Không trộn tiền, số lượng và phần trăm trên cùng một trục.

---

## 32. Kết luận

Biểu đồ của Cynca VLXD phải rõ ràng, có mục đích và hỗ trợ ra quyết định. Thiết kế cần ưu tiên khả năng đọc dữ liệu, tính nhất quán, trạng thái đầy đủ và khả năng drill-down thay vì hiệu ứng trang trí.

File tiếp theo:

```text
07-Product-Widgets.md
```