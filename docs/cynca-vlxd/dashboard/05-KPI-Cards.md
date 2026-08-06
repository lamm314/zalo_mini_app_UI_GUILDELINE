# Cynca VLXD Admin Dashboard — KPI Cards Specification

> Phiên bản: 1.0  
> Phạm vi: Hệ thống thẻ chỉ số chính trên Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`, `02-Layout.md`, `04-Header.md`

---

## 1. Vai trò của KPI cards

KPI cards là nhóm thông tin quan trọng nhất ở phần đầu Dashboard. Người dùng phải hiểu được tình trạng kinh doanh trong vài giây đầu thông qua các chỉ số này.

KPI cards phải giúp người dùng:

- Nhìn thấy giá trị hiện tại.
- So sánh với kỳ trước.
- Nhận biết xu hướng tăng hoặc giảm.
- Hiểu phạm vi thời gian đang áp dụng.
- Truy cập nhanh báo cáo chi tiết.
- Phát hiện bất thường cần xử lý.

KPI card không phải thành phần trang trí. Mỗi card phải có ý nghĩa quản trị cụ thể và công thức dữ liệu rõ ràng.

---

## 2. Bộ KPI mặc định

Dashboard tổng quan mặc định hiển thị 4 KPI:

1. Doanh thu thuần.
2. Đơn hàng.
3. Khách hàng.
4. Lợi nhuận gộp.

Tùy vai trò, KPI thứ tư có thể thay bằng:

- Giá trị tồn kho.
- Công nợ phải thu.
- Sản phẩm sắp hết hàng.
- Tỷ lệ hoàn thành đơn.
- Giá trị đơn hàng trung bình.

Không hiển thị quá 6 KPI trong khu vực đầu trang.

---

## 3. Layout KPI

### 3.1 Desktop lớn

```css
.kpi-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 24px;
}
```

### 3.2 Laptop

```css
@media (max-width: 1279px) {
  .kpi-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}
```

### 3.3 Tablet mode

```css
@media (max-width: 1023px) {
  .kpi-grid {
    grid-template-columns: 1fr;
  }
}
```

KPI card không dùng chiều rộng cố định.

---

## 4. Kích thước card

```css
min-width: 240px;
min-height: 156px;
padding: 22px 24px;
border-radius: 20px;
```

Card phải đủ cao để chứa:

- Label.
- Value.
- Trend.
- Supporting text hoặc sparkline.

Không ép nội dung xuống dưới 140px chiều cao.

---

## 5. Cấu trúc card

```text
KPI Card
├── Header row
│   ├── KPI label
│   ├── Tooltip trigger
│   └── Icon container
├── Value row
│   └── KPI value
├── Comparison row
│   ├── Trend badge
│   └── Comparison label
└── Optional footer
    ├── Sparkline
    └── Deep link
```

Wireframe:

```text
┌───────────────────────────────────┐
│ Doanh thu thuần              [₫] │
│                                   │
│ 1,28 tỷ ₫                         │
│                                   │
│ ↑ 12,5%  so với tháng trước       │
│ ───── sparkline ─────────────      │
└───────────────────────────────────┘
```

---

## 6. Visual style

Card mặc định:

```css
background: #FFFFFF;
border: 1px solid #E7ECF3;
box-shadow: 0 1px 2px rgba(16,24,40,0.04);
```

Hover khi card có thể click:

```css
border-color: #CFD7E4;
box-shadow: 0 8px 24px rgba(16,24,40,0.07);
transform: translateY(-2px);
```

Transition:

```css
transition: border-color 160ms ease,
            box-shadow 160ms ease,
            transform 160ms ease;
```

Không dùng gradient toàn card. Có thể dùng màu nền rất nhẹ cho icon hoặc badge.

---

## 7. Header row

```css
.kpi-card-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 16px;
}
```

Label:

```css
font-size: 14px;
font-weight: 500;
line-height: 20px;
color: #667085;
```

Tooltip trigger:

```css
width: 20px;
height: 20px;
color: #98A2B3;
```

Icon container:

```css
width: 44px;
height: 44px;
border-radius: 14px;
display: grid;
place-items: center;
background: #EAF1FF;
color: #0B57F0;
```

Icon size:

```css
width: 22px;
height: 22px;
stroke-width: 1.9;
```

---

## 8. KPI value

KPI value là yếu tố nổi bật nhất.

```css
font-size: clamp(28px, 2vw, 36px);
font-weight: 700;
line-height: 1.15;
letter-spacing: -0.02em;
color: #182230;
```

Khoảng cách:

```css
margin-top: 18px;
```

### 8.1 Quy tắc hiển thị tiền

Ưu tiên dạng rút gọn trong card:

```text
1,28 tỷ ₫
845 triệu ₫
24,6 triệu ₫
```

Tooltip hiển thị số đầy đủ:

```text
1.280.450.000 ₫
```

Không làm tròn ở báo cáo chi tiết.

### 8.2 Quy tắc số lượng

```text
1.248 đơn
3.540 khách hàng
28 sản phẩm
```

Dùng dấu chấm phân tách hàng nghìn theo định dạng Việt Nam.

### 8.3 Quy tắc phần trăm

```text
92,4%
```

Tối đa 1 chữ số thập phân trừ khi nghiệp vụ yêu cầu chính xác hơn.

---

## 9. Trend badge

Trend badge cho biết thay đổi so với kỳ tham chiếu.

### 9.1 Positive

```css
background: #ECFDF3;
color: #027A48;
```

Nhãn:

```text
↑ 12,5%
```

### 9.2 Negative

```css
background: #FEF3F2;
color: #B42318;
```

Nhãn:

```text
↓ 4,2%
```

### 9.3 Neutral

```css
background: #F2F4F7;
color: #475467;
```

Nhãn:

```text
→ 0,0%
```

### 9.4 Kích thước

```css
height: 24px;
padding-inline: 8px;
border-radius: 999px;
font-size: 12px;
font-weight: 600;
```

### 9.5 Quy tắc nghiệp vụ

Không phải KPI nào tăng cũng tốt.

Ví dụ:

- Doanh thu tăng → positive.
- Đơn hủy tăng → negative.
- Công nợ quá hạn tăng → negative.
- Tỷ lệ hoàn thành tăng → positive.

Màu phải dựa trên ý nghĩa kinh doanh, không chỉ dựa trên dấu cộng hoặc trừ.

---

## 10. Comparison label

```css
font-size: 12px;
font-weight: 400;
color: #7B8797;
```

Ví dụ:

```text
so với tháng trước
so với 30 ngày liền trước
so với cùng kỳ năm trước
```

Phạm vi so sánh phải nhất quán với date range hiện tại.

---

## 11. Sparkline

Sparkline là tùy chọn, chỉ dùng khi giúp người dùng hiểu xu hướng.

### 11.1 Kích thước

```css
width: 100%;
height: 36px;
margin-top: 14px;
```

### 11.2 Style

- Đường xanh dương chính.
- Stroke 2px.
- Không hiển thị axis.
- Không hiển thị nhiều series.
- Có area fill rất nhẹ.

```css
stroke: #0B57F0;
fill: rgba(11,87,240,0.08);
```

### 11.3 Accessibility

Sparkline cần mô tả text:

```text
Xu hướng doanh thu tăng dần trong 7 ngày gần nhất.
```

Không dùng sparkline làm nguồn thông tin duy nhất.

---

## 12. KPI definitions

### 12.1 Doanh thu thuần

Định nghĩa gợi ý:

```text
Tổng doanh thu đơn hàng hoàn thành
− chiết khấu
− hoàn tiền
− giá trị hàng trả lại
```

Không bao gồm đơn hủy.

Tooltip:

```text
Doanh thu thuần được tính từ các đơn hoàn thành trong khoảng thời gian đã chọn, sau khi trừ chiết khấu và hoàn tiền.
```

### 12.2 Đơn hàng

Mặc định:

```text
Tổng số đơn được tạo trong khoảng thời gian đã chọn.
```

Có thể bổ sung breakdown:

- Chờ xác nhận.
- Đang xử lý.
- Đang giao.
- Hoàn thành.
- Đã hủy.

### 12.3 Khách hàng

Cần xác định rõ:

- Tổng khách hàng hoạt động.
- Khách hàng mới.
- Khách đã mua hàng.

KPI mặc định đề xuất:

```text
Khách hàng có ít nhất một đơn hàng trong khoảng thời gian đã chọn.
```

### 12.4 Lợi nhuận gộp

Định nghĩa:

```text
Doanh thu thuần − giá vốn hàng bán
```

Chỉ hiển thị cho vai trò được phép xem dữ liệu tài chính.

### 12.5 Giá trị tồn kho

Định nghĩa:

```text
Tổng số lượng tồn khả dụng × giá vốn hiện tại
```

Cần ghi rõ thời điểm cập nhật.

### 12.6 Công nợ phải thu

Định nghĩa:

```text
Tổng giá trị hóa đơn hoặc đơn hàng đến hạn nhưng chưa thanh toán đầy đủ.
```

Nên có cảnh báo quá hạn.

---

## 13. KPI by role

### 13.1 Admin

- Doanh thu thuần.
- Đơn hàng.
- Khách hàng.
- Lợi nhuận gộp.

### 13.2 Quản lý kinh doanh

- Doanh thu.
- Đơn hàng.
- Giá trị đơn trung bình.
- Khách hàng mới.

### 13.3 Nhân viên kho

- Đơn chờ xuất.
- Sản phẩm sắp hết.
- Giá trị tồn kho.
- Phiếu nhập hôm nay.

### 13.4 Kế toán

- Doanh thu.
- Đã thu.
- Công nợ phải thu.
- Công nợ quá hạn.

### 13.5 Ban lãnh đạo

- Doanh thu.
- Lợi nhuận gộp.
- Tăng trưởng.
- Giá trị tồn kho.

---

## 14. KPI warning state

KPI warning dùng khi chỉ số vượt ngưỡng cần chú ý.

Ví dụ:

```text
18 sản phẩm sắp hết hàng
```

Style:

```css
border-color: #FEC84B;
background: #FFFCF5;
```

Danger state:

```css
border-color: #FDA29B;
background: #FFFBFA;
```

Không đổi toàn bộ card sang màu đỏ đậm.

---

## 15. Click behavior

KPI card có thể click khi có trang chi tiết tương ứng.

Ví dụ:

- Doanh thu → `/reports/revenue`.
- Đơn hàng → `/orders`.
- Khách hàng → `/customers`.
- Tồn kho thấp → `/inventory?status=low-stock`.

Nếu card click được:

- Dùng con trỏ pointer.
- Có hover state.
- Có focus state.
- Toàn card là link hoặc button semantic.

Không đặt nhiều link nhỏ bên trong card click toàn bộ để tránh xung đột tương tác.

---

## 16. Tooltip

Tooltip có thể giải thích:

- Công thức KPI.
- Phạm vi dữ liệu.
- Lần cập nhật cuối.
- Cách tính so sánh.

Ví dụ:

```text
Dữ liệu cập nhật lúc 16:30, ngày 06/08/2026.
```

Tooltip không chứa hành động bắt buộc.

---

## 17. Loading state

Khi tải dữ liệu:

- Giữ nguyên chiều cao card.
- Dùng skeleton cho label, value và trend.
- Icon container có thể hiển thị ngay.

```css
.kpi-skeleton-value {
  width: 58%;
  height: 34px;
  border-radius: 8px;
}
```

Không hiển thị `0` trong lúc chưa có dữ liệu vì dễ gây hiểu nhầm.

---

## 18. Empty state

Nếu thực sự không có dữ liệu:

```text
Chưa có dữ liệu
```

Supporting text:

```text
Không có đơn hàng trong khoảng thời gian đã chọn.
```

Không dùng dấu gạch ngang đơn độc nếu người dùng có thể hiểu sai.

---

## 19. Error state

Ví dụ:

```text
Không thể tải doanh thu
[ Thử lại ]
```

Giữ nguyên kích thước card.

Style nhẹ:

```css
background: #FFFBFA;
border-color: #FECDCA;
```

Không hiển thị stack trace hoặc mã lỗi nội bộ.

---

## 20. Stale data state

Khi dữ liệu cũ hơn ngưỡng cho phép:

```text
Dữ liệu có thể chưa cập nhật
Cập nhật lần cuối 45 phút trước
```

Có thể dùng icon warning nhỏ màu vàng.

Không hiển thị dữ liệu cũ như dữ liệu hiện tại mà không cảnh báo.

---

## 21. Focus state

```css
.kpi-card:focus-visible {
  outline: 3px solid rgba(11,87,240,0.22);
  outline-offset: 3px;
}
```

Focus phải rõ hơn hover.

---

## 22. Responsive typography

```css
.kpi-value {
  font-size: 34px;
}

@media (max-width: 1279px) {
  .kpi-value {
    font-size: 30px;
  }
}
```

Không giảm dưới 26px trên tablet mode.

---

## 23. Number formatting helper

Quy tắc hiển thị đề xuất:

```ts
formatCompactCurrency(1280450000) // "1,28 tỷ ₫"
formatNumber(1248)               // "1.248"
formatPercent(12.45)             // "12,5%"
```

Giá trị âm:

```text
−12,4 triệu ₫
```

Dùng ký tự minus thực sự hoặc format nhất quán.

---

## 24. Color mapping

| KPI | Icon background | Icon color |
|---|---|---|
| Doanh thu | `#EAF1FF` | `#0B57F0` |
| Đơn hàng | `#EFF8FF` | `#1570EF` |
| Khách hàng | `#F4F3FF` | `#6938EF` |
| Lợi nhuận | `#ECFDF3` | `#039855` |
| Tồn kho | `#FFF6ED` | `#EC4A0A` |
| Công nợ | `#FEF3F2` | `#D92D20` |

Không biến mỗi card thành một màu nền khác nhau. Chỉ icon container hoặc badge được dùng màu phân loại.

---

## 25. Icon mapping đề xuất

Dùng Lucide Icons:

| KPI | Icon |
|---|---|
| Doanh thu | `Banknote` |
| Đơn hàng | `ShoppingCart` |
| Khách hàng | `Users` |
| Lợi nhuận | `TrendingUp` |
| Tồn kho | `Warehouse` |
| Công nợ | `ReceiptText` |
| Đơn chờ xử lý | `Clock3` |
| Sản phẩm sắp hết | `TriangleAlert` |

---

## 26. CSS tham chiếu

```css
.kpi-card {
  min-width: 0;
  min-height: 156px;
  padding: 22px 24px;
  background: #FFFFFF;
  border: 1px solid #E7ECF3;
  border-radius: 20px;
  box-shadow: 0 1px 2px rgba(16,24,40,0.04);
}

.kpi-card--interactive:hover {
  border-color: #CFD7E4;
  box-shadow: 0 8px 24px rgba(16,24,40,0.07);
  transform: translateY(-2px);
}

.kpi-value {
  margin-top: 18px;
  font-size: 34px;
  font-weight: 700;
  line-height: 1.15;
  letter-spacing: -0.02em;
  color: #182230;
}

.kpi-comparison {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-top: 12px;
}
```

---

## 27. Component tree đề xuất

```text
KPIGrid
└── KPICard
    ├── KPICardHeader
    │   ├── KPILabel
    │   ├── KPIInfoTooltip
    │   └── KPIIcon
    ├── KPIValue
    ├── KPIComparison
    │   ├── TrendBadge
    │   └── ComparisonLabel
    └── KPIFooter
        ├── Sparkline
        └── OptionalLink
```

---

## 28. TypeScript props tham khảo

```ts
export type KPITrend = "positive" | "negative" | "neutral";

export type KPICardProps = {
  id: string;
  label: string;
  value: number | string;
  formattedValue: string;
  trend?: KPITrend;
  trendValue?: number;
  comparisonLabel?: string;
  icon: React.ComponentType;
  href?: string;
  tooltip?: string;
  sparklineData?: number[];
  loading?: boolean;
  error?: string;
  staleAt?: Date;
};
```

---

## 29. Data contract đề xuất

```json
{
  "id": "net-revenue",
  "label": "Doanh thu thuần",
  "value": 1280450000,
  "currency": "VND",
  "formattedValue": "1,28 tỷ ₫",
  "trend": "positive",
  "trendValue": 12.5,
  "comparisonLabel": "so với tháng trước",
  "updatedAt": "2026-08-06T16:30:00+07:00"
}
```

Frontend không nên tự đoán ý nghĩa positive hoặc negative nếu backend có thể cung cấp semantic trend.

---

## 30. Accessibility

Yêu cầu:

- Label và value phải đọc được bằng screen reader.
- Trend không chỉ biểu thị bằng màu.
- Icon trang trí dùng `aria-hidden="true"`.
- Card link phải có accessible name đầy đủ.
- Tooltip trigger có nhãn rõ ràng.
- Sparkline có mô tả text thay thế.

Ví dụ:

```html
<a aria-label="Doanh thu thuần 1,28 tỷ đồng, tăng 12,5 phần trăm so với tháng trước">
```

---

## 31. Acceptance checklist

- [ ] KPI grid hiển thị 4 card ở desktop lớn.
- [ ] Card có min-height 156px.
- [ ] Value là yếu tố thị giác nổi bật nhất.
- [ ] Trend dùng đúng semantic kinh doanh.
- [ ] Comparison label nêu rõ kỳ tham chiếu.
- [ ] Tooltip giải thích công thức KPI.
- [ ] Số tiền và số lượng đúng định dạng Việt Nam.
- [ ] Card click được có hover và focus rõ ràng.
- [ ] Loading không hiển thị giá trị 0 giả.
- [ ] Empty state phân biệt với loading.
- [ ] Error state không làm co card.
- [ ] Stale data có cảnh báo.
- [ ] KPI theo đúng phân quyền người dùng.
- [ ] Sparkline không là nguồn thông tin duy nhất.
- [ ] Responsive không làm vỡ value.
- [ ] Không quá 6 KPI ở khu vực đầu trang.

---

## 32. Những điều không được làm

- Không dùng quá nhiều màu nền card.
- Không hiển thị KPI không có định nghĩa dữ liệu rõ ràng.
- Không dùng dấu tăng/giảm mà thiếu kỳ so sánh.
- Không mặc định mọi số tăng đều là positive.
- Không hiển thị số quá dài làm vỡ layout.
- Không dùng chart phức tạp trong KPI card.
- Không để icon lớn hơn value.
- Không dùng animation số quá lâu.
- Không hiển thị dữ liệu tài chính cho vai trò không được phép.
- Không dùng `0` thay cho trạng thái chưa tải dữ liệu.

---

## 33. Kết luận

KPI cards của Cynca VLXD phải giúp người quản trị hiểu nhanh tình trạng kinh doanh, nhận biết xu hướng và truy cập báo cáo chi tiết. Mỗi card phải có dữ liệu đáng tin cậy, định nghĩa rõ ràng, trạng thái đầy đủ và phân cấp thị giác nhất quán.

File tiếp theo:

```text
06-Charts.md
```