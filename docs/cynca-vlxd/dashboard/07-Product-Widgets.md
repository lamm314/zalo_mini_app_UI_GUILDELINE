# Cynca VLXD Admin Dashboard — Product Widgets Specification

> Phiên bản: 1.0  
> Phạm vi: Các widget sản phẩm, danh mục và tồn kho trên Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`, `02-Layout.md`, `05-KPI-Cards.md`, `06-Charts.md`

---

## 1. Vai trò của product widgets

Product widgets giúp người quản trị theo dõi nhanh tình trạng kinh doanh và vận hành của danh mục vật liệu xây dựng.

Các widget phải trả lời được những câu hỏi sau:

- Sản phẩm nào đang bán tốt nhất?
- Sản phẩm nào sắp hết hàng?
- Sản phẩm nào hết hàng hoàn toàn?
- Danh mục nào đóng góp doanh thu lớn?
- Sản phẩm nào bán chậm hoặc không phát sinh đơn?
- Có sản phẩm nào thiếu giá, ảnh hoặc thông tin kỹ thuật?
- SKU nào cần nhập thêm?
- Sản phẩm nào đang bị ẩn hoặc ngừng kinh doanh?

Widget sản phẩm phải ưu tiên hành động vận hành, không chỉ hiển thị số liệu tĩnh.

---

## 2. Bộ widget đề xuất

Dashboard tổng quan có thể sử dụng các widget sau:

1. Sản phẩm bán chạy.
2. Cảnh báo tồn kho.
3. Danh mục nổi bật.
4. Sản phẩm bán chậm.
5. Chất lượng dữ liệu sản phẩm.
6. Sản phẩm mới thêm.
7. Sản phẩm đang khuyến mãi.

Màn hình mặc định chỉ nên hiển thị 2–3 widget sản phẩm quan trọng nhất.

Ưu tiên:

- Sản phẩm bán chạy.
- Cảnh báo tồn kho.
- Danh mục nổi bật.

---

## 3. Layout tổng thể

Bố cục đề xuất:

```text
┌──────────────────────────────┬───────────────────────────────┐
│ Sản phẩm bán chạy — 6 cols   │ Cảnh báo tồn kho — 6 cols    │
└──────────────────────────────┴───────────────────────────────┘
```

Hoặc:

```text
┌──────────────────────────────────────────────────────────────┐
│ Sản phẩm bán chạy — 12 columns                               │
├──────────────────────────────┬───────────────────────────────┤
│ Danh mục nổi bật — 6 cols    │ Cảnh báo tồn kho — 6 cols     │
└──────────────────────────────┴───────────────────────────────┘
```

Tại viewport 1024–1279px, các widget ưu tiên xếp một cột.

---

## 4. Card container chuẩn

```css
.product-widget {
  min-width: 0;
  min-height: 360px;
  background: #FFFFFF;
  border: 1px solid #E7ECF3;
  border-radius: 20px;
  box-shadow: 0 1px 2px rgba(16,24,40,0.04);
}
```

Card header:

```css
padding: 22px 24px 16px;
```

Card body:

```css
padding: 0 24px 20px;
```

Nếu widget chứa table-like list, có thể dùng padding ngang 0 cho body và đặt padding ở từng row.

---

## 5. Widget header

Cấu trúc:

```text
Tiêu đề
Mô tả ngắn hoặc summary                         [Bộ lọc ▾] [Xem tất cả]
```

Title:

```css
font-size: 18px;
font-weight: 600;
color: #182230;
```

Description:

```css
font-size: 13px;
color: #7B8797;
margin-top: 4px;
```

Hành động bên phải:

- Chọn khoảng thời gian.
- Chọn chi nhánh hoặc kho.
- Xem tất cả.
- Menu tùy chọn.

Không đặt quá 2 hành động hiển thị trực tiếp.

---

## 6. Widget sản phẩm bán chạy

### 6.1 Mục tiêu

Hiển thị sản phẩm có hiệu suất bán hàng cao nhất trong khoảng thời gian đã chọn.

### 6.2 Tiêu chí xếp hạng

Cho phép chọn:

- Theo doanh thu.
- Theo số lượng bán.
- Theo lợi nhuận gộp.

Mặc định:

```text
Theo doanh thu
```

### 6.3 Số lượng hiển thị

```text
5 sản phẩm
```

Tối đa 10 sản phẩm trong widget. Nếu cần nhiều hơn, điều hướng đến trang báo cáo.

### 6.4 Cấu trúc item

```text
[01] [Ảnh] Tên sản phẩm
           SKU · Danh mục                 128 đã bán
                                          245.000.000 ₫
```

Mỗi row gồm:

- Xếp hạng.
- Thumbnail.
- Tên sản phẩm.
- SKU.
- Danh mục.
- Số lượng bán.
- Doanh thu.
- Optional trend.

### 6.5 Row dimensions

```css
min-height: 72px;
padding-block: 12px;
border-bottom: 1px solid #EEF1F5;
```

Row cuối không có border dưới.

### 6.6 Ranking

```css
width: 28px;
font-size: 13px;
font-weight: 700;
color: #7B8797;
```

Top 3 có thể dùng màu nhấn nhẹ:

- Hạng 1: xanh dương chính.
- Hạng 2 và 3: text đậm hơn.

Không dùng huy chương hoặc hiệu ứng vàng quá nổi.

---

## 7. Thumbnail sản phẩm

### 7.1 Kích thước

```css
width: 52px;
height: 52px;
border-radius: 12px;
object-fit: cover;
background: #F2F4F7;
```

### 7.2 Nội dung hình ảnh

Ảnh phải:

- Nền sạch.
- Tỷ lệ nhất quán.
- Không biến dạng.
- Phản ánh đúng sản phẩm.

### 7.3 Fallback

Nếu không có ảnh:

- Dùng icon `Package`.
- Nền xám nhạt.
- Có tooltip `Sản phẩm chưa có ảnh` nếu cần.

Không dùng ảnh placeholder ngẫu nhiên.

---

## 8. Product identity block

Tên sản phẩm:

```css
font-size: 14px;
font-weight: 600;
line-height: 20px;
color: #182230;
```

Giới hạn:

```css
display: -webkit-box;
-webkit-line-clamp: 1;
-webkit-box-orient: vertical;
overflow: hidden;
```

Metadata:

```css
font-size: 12px;
color: #7B8797;
margin-top: 3px;
```

Ví dụ:

```text
XM-HC-PCB40 · Xi măng
```

Không hiển thị quá nhiều metadata trong một row.

---

## 9. Sales metric block

Số lượng bán:

```css
font-size: 12px;
color: #667085;
```

Doanh thu:

```css
font-size: 14px;
font-weight: 600;
color: #182230;
margin-top: 4px;
```

Ở màn hình hẹp, có thể ẩn số lượng bán và chỉ giữ doanh thu.

---

## 10. Progress visualization

Có thể dùng progress bar nhỏ để so sánh tương đối.

```css
height: 6px;
border-radius: 999px;
background: #EAF1FF;
```

Fill:

```css
background: #0B57F0;
```

Progress được tính so với sản phẩm đứng đầu.

Không dùng progress bar nếu gây chật row hoặc dữ liệu đã rõ bằng số.

---

## 11. Widget cảnh báo tồn kho

### 11.1 Mục tiêu

Giúp nhân viên phát hiện SKU cần nhập thêm hoặc đang hết hàng.

### 11.2 Nhóm cảnh báo

- Hết hàng.
- Sắp hết hàng.
- Tồn dưới mức tối thiểu.
- Hàng tồn lâu.
- Tồn âm do lỗi đồng bộ.

### 11.3 Thứ tự ưu tiên

1. Tồn âm.
2. Hết hàng.
3. Sắp hết hàng.
4. Hàng tồn lâu.

### 11.4 Cấu trúc item

```text
[Ảnh] Thép Việt Nhật D16
      SKU: TVN-D16
      Kho Hà Nội                  Còn 4 cây
                                  Mức tối thiểu: 20
                                  [Nhập hàng]
```

Mỗi item gồm:

- Thumbnail hoặc icon.
- Tên sản phẩm.
- SKU.
- Kho.
- Tồn hiện tại.
- Ngưỡng tối thiểu.
- Badge trạng thái.
- Hành động.

---

## 12. Inventory status badges

### Hết hàng

```css
background: #FEF3F2;
color: #B42318;
```

Nhãn:

```text
Hết hàng
```

### Sắp hết

```css
background: #FFFAEB;
color: #B54708;
```

Nhãn:

```text
Sắp hết
```

### Tồn ổn định

```css
background: #ECFDF3;
color: #027A48;
```

Không cần hiển thị badge `Ổn định` trong widget cảnh báo nếu không có giá trị hành động.

---

## 13. Inventory progress

Có thể hiển thị thanh tồn kho so với ngưỡng tối thiểu.

Ví dụ:

```text
4 / 20
```

Progress:

```css
height: 8px;
border-radius: 999px;
background: #F2F4F7;
```

Fill theo semantic:

- Đỏ: dưới 20% ngưỡng.
- Vàng: từ 20% đến dưới 100% ngưỡng.
- Xanh: đạt hoặc vượt ngưỡng.

Không chỉ dùng màu; luôn hiển thị số.

---

## 14. Quick actions tồn kho

Các hành động phù hợp:

- Tạo phiếu nhập.
- Điều chuyển kho.
- Cập nhật tồn.
- Xem lịch sử kho.
- Liên hệ nhà cung cấp.

Nút chính trong row chỉ nên là một hành động nổi bật, ví dụ:

```text
[ Nhập hàng ]
```

Các hành động còn lại đặt trong menu ba chấm.

---

## 15. Widget danh mục nổi bật

### 15.1 Mục tiêu

Hiển thị hiệu suất các nhóm vật liệu chính.

### 15.2 Danh mục đề xuất

- Xi măng.
- Thép xây dựng.
- Gạch.
- Cát và đá.
- Sơn.
- Tôn và mái.
- Chống thấm.
- Thiết bị phụ trợ.

### 15.3 Layout

Có thể dùng grid 2 cột bên trong card:

```css
.category-widget-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 12px;
}
```

### 15.4 Category item

```text
[Icon/Ảnh] Xi măng
           18 sản phẩm
           420 triệu ₫
```

Kích thước:

```css
min-height: 104px;
padding: 16px;
border: 1px solid #EEF1F5;
border-radius: 16px;
```

Hover nếu click được:

```css
border-color: #C9D7F8;
background: #F8FAFF;
```

---

## 16. Category visual

Icon hoặc ảnh:

```css
width: 40px;
height: 40px;
border-radius: 12px;
background: #EAF1FF;
color: #0B57F0;
```

Tên danh mục:

```css
font-size: 14px;
font-weight: 600;
color: #182230;
```

Số sản phẩm:

```css
font-size: 12px;
color: #7B8797;
```

Doanh thu:

```css
font-size: 13px;
font-weight: 600;
color: #344054;
```

---

## 17. Widget sản phẩm bán chậm

### 17.1 Định nghĩa

Sản phẩm bán chậm cần có quy tắc nghiệp vụ rõ ràng.

Ví dụ:

```text
Không phát sinh đơn trong 30 ngày
hoặc
Tốc độ bán thấp hơn ngưỡng danh mục
```

Không gắn nhãn bán chậm chỉ dựa trên cảm tính.

### 17.2 Nội dung item

- Tên sản phẩm.
- SKU.
- Số ngày không phát sinh bán.
- Tồn hiện tại.
- Giá trị tồn.
- Hành động gợi ý.

### 17.3 Hành động

- Tạo khuyến mãi.
- Điều chỉnh giá.
- Ẩn sản phẩm.
- Điều chuyển kho.
- Xem lịch sử bán.

---

## 18. Widget chất lượng dữ liệu sản phẩm

### 18.1 Mục tiêu

Phát hiện sản phẩm chưa hoàn thiện dữ liệu để hiển thị trên Mini App.

### 18.2 Các lỗi dữ liệu

- Thiếu ảnh.
- Thiếu giá.
- Thiếu mô tả.
- Thiếu thông số kỹ thuật.
- Thiếu danh mục.
- Thiếu đơn vị tính.
- SKU trùng.
- Sản phẩm không có tồn kho.

### 18.3 Summary

```text
42 / 50 sản phẩm hoàn chỉnh
```

Progress:

```css
height: 10px;
border-radius: 999px;
background: #E7ECF3;
```

Fill:

```css
background: #0B57F0;
```

### 18.4 Issue list

```text
8 sản phẩm thiếu ảnh
3 sản phẩm thiếu thông số
2 sản phẩm chưa có giá
```

Mỗi dòng click được để mở danh sách đã lọc.

---

## 19. Widget sản phẩm mới thêm

Hiển thị tối đa 5 sản phẩm mới nhất.

Thông tin:

- Thumbnail.
- Tên sản phẩm.
- SKU.
- Người tạo.
- Thời gian tạo.
- Trạng thái hiển thị.

Status:

- Đang bán.
- Bản nháp.
- Đang ẩn.
- Hết hàng.

---

## 20. Widget sản phẩm khuyến mãi

Thông tin:

- Tên sản phẩm.
- Giá gốc.
- Giá khuyến mãi.
- Phần trăm giảm.
- Thời gian còn lại.
- Số lượng bán trong chiến dịch.

Không hiển thị countdown chạy từng giây nếu không cần thiết. Có thể dùng:

```text
Còn 3 ngày
```

---

## 21. Sorting và filters

Widget sản phẩm có thể hỗ trợ:

- Theo doanh thu.
- Theo số lượng bán.
- Theo lợi nhuận.
- Theo kho.
- Theo danh mục.
- Theo thời gian.

Filter phải đồng bộ với phạm vi Dashboard.

Nếu widget có filter riêng, cần thể hiện rõ filter đó không áp dụng cho toàn trang.

---

## 22. Row interaction

Row click mở:

- Trang chi tiết sản phẩm.
- Drawer xem nhanh.
- Trang tồn kho đã lọc theo SKU.

Nếu row click được:

```css
cursor: pointer;
```

Hover:

```css
background: #F8FAFC;
```

Focus:

```css
outline: 3px solid rgba(11,87,240,0.18);
outline-offset: -2px;
```

Không đặt quá nhiều nút trong một row.

---

## 23. Loading state

Widget loading:

- Skeleton header.
- 5 skeleton rows.
- Giữ nguyên min-height.

Skeleton thumbnail:

```css
width: 52px;
height: 52px;
border-radius: 12px;
```

Không hiển thị danh sách trống trong lúc tải.

---

## 24. Empty state

### Best sellers empty

```text
Chưa có dữ liệu bán hàng
Các sản phẩm bán chạy sẽ xuất hiện khi có đơn hàng hoàn thành.
```

### Inventory alerts empty

```text
Tồn kho đang ổn định
Không có sản phẩm nào dưới ngưỡng cảnh báo.
```

### Data quality empty

```text
Dữ liệu sản phẩm đã hoàn chỉnh
Không phát hiện sản phẩm thiếu thông tin bắt buộc.
```

Empty state phải phù hợp ngữ cảnh, không dùng một câu chung cho mọi widget.

---

## 25. Error state

```text
Không thể tải dữ liệu sản phẩm
[ Thử lại ]
```

Giữ nguyên chiều cao card.

Nếu chỉ ảnh sản phẩm lỗi, dùng fallback ảnh; không chuyển cả widget sang error state.

---

## 26. Stale data state

Hiển thị:

```text
Cập nhật lần cuối 16:30, 06/08/2026
```

Nếu dữ liệu tồn kho quá cũ:

```text
Tồn kho có thể chưa được đồng bộ
```

Cảnh báo tồn kho cần ưu tiên dữ liệu gần thời gian thực hơn widget doanh thu sản phẩm.

---

## 27. Responsive behavior

### Viewport ≥ 1440px

- Hai widget 6/6 cột.
- Hiển thị đầy đủ thumbnail, metadata và metrics.

### Viewport 1280–1439px

- Có thể giữ 6/6 nếu đủ rộng.
- Giảm metadata phụ.

### Viewport 1024–1279px

- Widget xếp 12 cột.
- Giữ row tối thiểu 68px.

### Viewport < 1024px

- Một cột.
- Ẩn metric phụ.
- Nút hành động có thể chuyển vào menu.
- Category grid chuyển thành 1 cột nếu cần.

---

## 28. Accessibility

Yêu cầu:

- Thumbnail có alt mô tả sản phẩm hoặc alt rỗng nếu chỉ trang trí.
- Status không chỉ dựa vào màu.
- Row click được phải dùng link hoặc button semantic.
- Progress bar có `aria-valuenow`, `aria-valuemin`, `aria-valuemax`.
- Các action icon-only có aria-label.
- Tên sản phẩm không bị cắt khỏi accessible name.

Ví dụ:

```html
<a aria-label="Xem sản phẩm Xi măng Hoàng Thạch PCB40, doanh thu 245 triệu đồng">
```

---

## 29. Data contract đề xuất

```ts
export type ProductPerformanceItem = {
  productId: string;
  name: string;
  sku: string;
  categoryName: string;
  imageUrl?: string;
  quantitySold: number;
  revenue: number;
  grossProfit?: number;
  trendPercent?: number;
};

export type InventoryAlertItem = {
  productId: string;
  warehouseId: string;
  name: string;
  sku: string;
  imageUrl?: string;
  warehouseName: string;
  availableQuantity: number;
  minimumQuantity: number;
  status: "negative" | "out_of_stock" | "low_stock" | "slow_moving";
};
```

---

## 30. Component tree đề xuất

```text
ProductWidgetCard
├── WidgetHeader
│   ├── WidgetTitle
│   ├── WidgetDescription
│   └── WidgetActions
├── WidgetBody
│   ├── ProductPerformanceList
│   │   └── ProductPerformanceRow
│   ├── InventoryAlertList
│   │   └── InventoryAlertRow
│   └── CategoryGrid
│       └── CategoryCard
└── WidgetFooter
    └── ViewAllLink
```

---

## 31. CSS tham chiếu

```css
.product-widget {
  min-width: 0;
  min-height: 360px;
  background: #FFFFFF;
  border: 1px solid #E7ECF3;
  border-radius: 20px;
  box-shadow: 0 1px 2px rgba(16,24,40,0.04);
}

.product-row {
  display: grid;
  grid-template-columns: auto 52px minmax(0, 1fr) auto;
  align-items: center;
  gap: 12px;
  min-height: 72px;
  padding: 10px 24px;
  border-bottom: 1px solid #EEF1F5;
}

.product-row:hover {
  background: #F8FAFC;
}

.product-thumbnail {
  width: 52px;
  height: 52px;
  border-radius: 12px;
  object-fit: cover;
  background: #F2F4F7;
}
```

---

## 32. Acceptance checklist

- [ ] Widget chỉ hiển thị dữ liệu có ý nghĩa quản trị.
- [ ] Best sellers có tiêu chí xếp hạng rõ ràng.
- [ ] Inventory alert ưu tiên đúng mức độ nghiêm trọng.
- [ ] Thumbnail đúng tỷ lệ và có fallback.
- [ ] Tên sản phẩm không xuống quá nhiều dòng.
- [ ] SKU và danh mục dễ nhận biết.
- [ ] Số tiền đúng định dạng Việt Nam.
- [ ] Status badge đồng nhất với hệ thống.
- [ ] Row có hover và focus khi click được.
- [ ] Loading không làm thay đổi chiều cao card.
- [ ] Empty state riêng cho từng loại widget.
- [ ] Error ảnh không làm lỗi toàn widget.
- [ ] Dữ liệu tồn kho cũ có cảnh báo.
- [ ] Widget responsive tốt ở 1024px.
- [ ] Phân quyền hành động nhập kho và chỉnh sửa đúng.
- [ ] Progress bar có nhãn số và accessibility.
- [ ] Không quá 10 item trong một widget.

---

## 33. Những điều không được làm

- Không dùng ảnh sản phẩm sai hoặc ảnh ngẫu nhiên.
- Không xếp hạng khi chưa xác định metric.
- Không chỉ dùng màu để báo tồn kho.
- Không đặt nhiều hơn 2 nút trong một row.
- Không hiển thị quá nhiều metadata.
- Không dùng biểu đồ phức tạp cho danh sách ngắn.
- Không gắn nhãn bán chậm nếu thiếu quy tắc nghiệp vụ.
- Không hiển thị số tồn kho cũ mà không cảnh báo.
- Không dùng card màu sặc sỡ cho từng danh mục.
- Không để widget tự cuộn dọc dài nếu có thể dùng trang `Xem tất cả`.

---

## 34. Kết luận

Product widgets của Cynca VLXD phải giúp người quản trị theo dõi hiệu suất sản phẩm, chất lượng dữ liệu và tình trạng tồn kho một cách nhanh chóng. Các widget cần trực quan, có khả năng hành động và liên kết chặt chẽ với trang sản phẩm, kho hàng và báo cáo chi tiết.

File tiếp theo:

```text
08-Orders-Table.md
```