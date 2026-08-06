# Cynca VLXD Admin Dashboard — Orders Table Specification

> Phiên bản: 1.0  
> Phạm vi: Bảng đơn hàng gần nhất trên Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`, `02-Layout.md`, `04-Header.md`, `06-Charts.md`

---

## 1. Vai trò của bảng đơn hàng

Bảng đơn hàng là khu vực vận hành chính của Dashboard. Thành phần này phải giúp người dùng:

- Theo dõi đơn hàng mới nhất.
- Nhận biết đơn đang chờ xử lý.
- Kiểm tra trạng thái thanh toán và giao hàng.
- Phát hiện đơn có rủi ro chậm xử lý.
- Truy cập nhanh chi tiết đơn hàng.
- Thực hiện các hành động phổ biến mà không rời Dashboard.

Bảng trên Dashboard chỉ hiển thị tập dữ liệu rút gọn, ưu tiên 8–10 đơn gần nhất hoặc đơn cần chú ý. Trang quản lý đơn hàng đầy đủ nằm ở module riêng.

---

## 2. Layout tổng thể

Khuyến nghị:

```text
┌──────────────────────────────────────────────────────────────┐
│ Đơn hàng gần đây                         [Bộ lọc] [Xem tất cả]│
├──────────────────────────────────────────────────────────────┤
│ Mã đơn │ Khách hàng │ Thanh toán │ Trạng thái │ Tổng │ Thời gian │
├──────────────────────────────────────────────────────────────┤
│ ...                                                          │
└──────────────────────────────────────────────────────────────┘
```

Card:

```css
min-height: 420px;
background: #FFFFFF;
border: 1px solid #E7ECF3;
border-radius: 20px;
box-shadow: 0 1px 2px rgba(16,24,40,0.04);
overflow: hidden;
```

---

## 3. Table card header

Header card gồm:

- Tiêu đề `Đơn hàng gần đây`.
- Mô tả ngắn hoặc số đơn cần xử lý.
- Bộ lọc nhanh.
- Link `Xem tất cả`.

Typography:

```css
font-size: 18px;
font-weight: 600;
color: #182230;
```

Description:

```css
font-size: 13px;
color: #7B8797;
```

Ví dụ:

```text
12 đơn đang chờ xác nhận
```

---

## 4. Bộ cột mặc định

| Cột | Nội dung | Ưu tiên |
|---|---|---|
| Mã đơn | Order ID | Bắt buộc |
| Khách hàng | Tên + số điện thoại | Bắt buộc |
| Thanh toán | Phương thức + trạng thái | Bắt buộc |
| Trạng thái | Trạng thái xử lý đơn | Bắt buộc |
| Tổng tiền | Giá trị đơn | Bắt buộc |
| Thời gian | Thời điểm tạo | Bắt buộc |
| Hành động | Xem / xử lý nhanh | Bắt buộc |

Có thể thêm cột `Chi nhánh`, `Nhân viên phụ trách` hoặc `Kênh bán` ở trang danh sách đầy đủ, không bắt buộc trong widget Dashboard.

---

## 5. Kích thước bảng

```css
.table-wrapper {
  width: 100%;
  overflow-x: auto;
}

.orders-table {
  width: 100%;
  min-width: 920px;
  border-collapse: separate;
  border-spacing: 0;
}
```

Chiều cao row:

```css
min-height: 64px;
```

Header row:

```css
height: 44px;
background: #F8FAFC;
```

---

## 6. Table header

```css
.orders-table th {
  padding: 12px 16px;
  font-size: 12px;
  font-weight: 600;
  color: #667085;
  text-align: left;
  white-space: nowrap;
  border-bottom: 1px solid #E7ECF3;
}
```

Cột số tiền căn phải.

Cột hành động căn giữa hoặc phải.

Không viết hoa toàn bộ nhãn cột.

---

## 7. Table row

```css
.orders-table td {
  padding: 14px 16px;
  font-size: 13px;
  color: #344054;
  border-bottom: 1px solid #EEF1F5;
  vertical-align: middle;
}
```

Hover:

```css
background: #F8FAFC;
```

Row cuối không có border dưới.

Nếu row có thể click, toàn row phải là link semantic hoặc có cơ chế focus rõ ràng.

---

## 8. Cột mã đơn

Hiển thị:

```text
#CYN-240806-0187
```

Typography:

```css
font-family: ui-monospace, SFMono-Regular, Menlo, monospace;
font-size: 12px;
font-weight: 600;
color: #0B57F0;
```

Click vào mã đơn mở trang chi tiết hoặc drawer xem nhanh.

Không hiển thị ID nội bộ khó đọc nếu đã có mã đơn nghiệp vụ.

---

## 9. Cột khách hàng

Cấu trúc:

```text
Nguyễn Văn Minh
0987 654 321
```

Tên:

```css
font-size: 13px;
font-weight: 600;
color: #182230;
```

Số điện thoại:

```css
font-size: 12px;
color: #7B8797;
margin-top: 2px;
```

Nếu là khách doanh nghiệp có thể hiển thị:

```text
Công ty TNHH ABC
MST: 0101234567
```

Không hiển thị đầy đủ địa chỉ trong bảng Dashboard.

---

## 10. Cột thanh toán

Hiển thị hai lớp thông tin:

```text
Chuyển khoản
Đã thanh toán
```

Phương thức thanh toán:

- COD.
- Chuyển khoản.
- Ví điện tử.
- Công nợ.
- Thanh toán tại cửa hàng.

Trạng thái thanh toán:

- Chưa thanh toán.
- Đã thanh toán.
- Thanh toán một phần.
- Hoàn tiền một phần.
- Đã hoàn tiền.

Màu trạng thái phải đồng nhất toàn hệ thống.

---

## 11. Trạng thái đơn hàng

Các trạng thái chính:

- Chờ xác nhận.
- Đã xác nhận.
- Đang chuẩn bị.
- Chờ giao.
- Đang giao.
- Hoàn thành.
- Đã hủy.
- Trả hàng.

Badge mẫu:

```css
height: 26px;
padding-inline: 10px;
border-radius: 999px;
font-size: 12px;
font-weight: 600;
```

Màu đề xuất:

```css
--status-pending-bg: #FFFAEB;
--status-pending-text: #B54708;
--status-confirmed-bg: #EFF8FF;
--status-confirmed-text: #175CD3;
--status-processing-bg: #F4F3FF;
--status-processing-text: #5925DC;
--status-shipping-bg: #EEF4FF;
--status-shipping-text: #3538CD;
--status-completed-bg: #ECFDF3;
--status-completed-text: #027A48;
--status-cancelled-bg: #FEF3F2;
--status-cancelled-text: #B42318;
--status-returned-bg: #FFF6ED;
--status-returned-text: #C4320A;
```

Không dùng chỉ màu dot mà thiếu nhãn.

---

## 12. Cột tổng tiền

```css
font-size: 13px;
font-weight: 600;
color: #182230;
text-align: right;
white-space: nowrap;
```

Định dạng:

```text
12.450.000 ₫
```

Nếu đơn có giảm giá hoặc hoàn tiền, tooltip có thể hiển thị breakdown.

---

## 13. Cột thời gian

Hiển thị tương đối ở dòng chính:

```text
12 phút trước
```

Dòng phụ:

```text
06/08/2026 · 16:18
```

Không chỉ hiển thị thời gian tương đối vì sẽ mất ngữ cảnh khi xem lại.

Đơn quá hạn xử lý có thể hiển thị cảnh báo:

```text
Quá hạn 25 phút
```

---

## 14. Cột hành động

Hành động trực tiếp đề xuất:

- Xem chi tiết.
- Xác nhận đơn.
- Cập nhật trạng thái.
- In đơn.
- Hủy đơn.

Trong bảng Dashboard chỉ nên hiển thị:

- Một icon xem nhanh.
- Một menu ba chấm.

Menu ba chấm chứa các hành động còn lại.

Icon-only button:

```css
width: 36px;
height: 36px;
border-radius: 10px;
```

Phải có `aria-label` và tooltip.

---

## 15. Row priority state

### Đơn mới

Có thể dùng dot xanh nhỏ hoặc nền rất nhẹ:

```css
background: #F8FAFF;
```

### Đơn quá hạn xử lý

```css
box-shadow: inset 3px 0 0 #F79009;
```

### Đơn có lỗi thanh toán

```css
box-shadow: inset 3px 0 0 #F04438;
```

Không đổi toàn bộ row sang đỏ đậm.

---

## 16. Filter nhanh

Filter đề xuất:

- Tất cả.
- Chờ xác nhận.
- Đang giao.
- Hoàn thành.
- Đã hủy.

Có thể dùng segmented control hoặc select.

Không hiển thị quá 5 tab trạng thái trong card nhỏ.

Filter phải đồng bộ với dữ liệu và summary ở header card.

---

## 17. Sorting

Dashboard mặc định sắp xếp:

```text
Mới nhất trước
```

Cho phép sort theo:

- Thời gian tạo.
- Tổng tiền.
- Trạng thái.

Không cần sort mọi cột trong widget Dashboard.

---

## 18. Selection và bulk action

Widget Dashboard mặc định không cần checkbox chọn nhiều.

Bulk action chỉ nên có trong trang quản lý đơn hàng đầy đủ.

Nếu Dashboard có chế độ xử lý nhanh theo lô, phải có:

- Checkbox header.
- Thanh action cố định.
- Xác nhận trước thao tác nguy hiểm.

---

## 19. Quick view drawer

Click row có thể mở drawer bên phải.

Drawer hiển thị:

- Mã đơn.
- Khách hàng.
- Danh sách sản phẩm.
- Thanh toán.
- Giao hàng.
- Timeline trạng thái.
- Hành động chính.

Chiều rộng:

```css
width: 640px;
max-width: calc(100vw - 40px);
```

Drawer không thay thế trang chi tiết đầy đủ.

---

## 20. Loading state

Hiển thị 6–8 skeleton rows.

Skeleton phải giữ nguyên cấu trúc cột.

```css
height: 20px;
border-radius: 6px;
background: #F2F4F7;
```

Không hiển thị spinner giữa bảng nếu có thể dùng skeleton.

---

## 21. Empty state

Ví dụ:

```text
Chưa có đơn hàng nào
Các đơn hàng mới sẽ xuất hiện tại đây.
[ Tạo đơn hàng ]
```

Nếu filter không có kết quả:

```text
Không có đơn hàng phù hợp
Hãy thử thay đổi bộ lọc.
```

Empty state phải nằm trong body card và giữ min-height.

---

## 22. Error state

```text
Không thể tải danh sách đơn hàng
Kết nối đến máy chủ đang gián đoạn.
[ Thử lại ]
```

Không làm lỗi toàn Dashboard.

Nếu một hành động row thất bại, hiển thị toast và giữ trạng thái row cũ.

---

## 23. Optimistic update

Có thể dùng optimistic update cho thao tác ít rủi ro như:

- Đánh dấu đã xem.
- Gán nhân viên.

Không dùng optimistic update cho:

- Xác nhận thanh toán.
- Hủy đơn.
- Hoàn tiền.
- Chuyển trạng thái hoàn thành.

Các thao tác quan trọng phải chờ phản hồi backend.

---

## 24. Responsive behavior

### Viewport ≥ 1440px

Hiển thị đầy đủ tất cả cột mặc định.

### Viewport 1280–1439px

Có thể rút gọn:

- Ẩn dòng phụ thời gian.
- Ẩn phương thức thanh toán, giữ trạng thái thanh toán.

### Viewport 1024–1279px

- Bảng chiếm 12 cột.
- Cho phép cuộn ngang.
- Giữ mã đơn, khách hàng, trạng thái, tổng tiền và hành động.

### Viewport < 1024px

Có thể chuyển sang list card:

```text
#CYN-...
Nguyễn Văn Minh
12.450.000 ₫
[Đang giao]
06/08/2026 · 16:18
```

Dashboard desktop không cần tối ưu sâu cho mobile.

---

## 25. Accessibility

Yêu cầu:

- Dùng `<table>`, `<thead>`, `<tbody>`, `<th scope="col">`.
- Row action có accessible name.
- Status có nhãn text.
- Sortable header có `aria-sort`.
- Drawer giữ focus trap.
- Sau khi đóng drawer, focus quay lại row trigger.
- Không dùng click row mà thiếu keyboard support.

Ví dụ:

```html
<th scope="col" aria-sort="descending">Thời gian</th>
```

---

## 26. Data contract đề xuất

```ts
export type OrderStatus =
  | "pending"
  | "confirmed"
  | "processing"
  | "ready_to_ship"
  | "shipping"
  | "completed"
  | "cancelled"
  | "returned";

export type PaymentStatus =
  | "unpaid"
  | "partial"
  | "paid"
  | "partially_refunded"
  | "refunded";

export type DashboardOrderRow = {
  id: string;
  code: string;
  customerName: string;
  customerPhone?: string;
  customerCompany?: string;
  paymentMethod: string;
  paymentStatus: PaymentStatus;
  orderStatus: OrderStatus;
  totalAmount: number;
  currency: "VND";
  createdAt: string;
  overdueMinutes?: number;
  unread?: boolean;
};
```

---

## 27. Component tree đề xuất

```text
OrdersTableCard
├── OrdersTableHeader
│   ├── TitleGroup
│   ├── StatusFilter
│   └── ViewAllLink
├── OrdersTableWrapper
│   └── OrdersTable
│       ├── OrdersTableHead
│       └── OrdersTableBody
│           └── OrderRow
│               ├── OrderCodeCell
│               ├── CustomerCell
│               ├── PaymentCell
│               ├── OrderStatusCell
│               ├── TotalCell
│               ├── TimeCell
│               └── RowActions
└── OrderQuickViewDrawer
```

---

## 28. CSS tham chiếu

```css
.orders-card {
  min-width: 0;
  min-height: 420px;
  background: #FFFFFF;
  border: 1px solid #E7ECF3;
  border-radius: 20px;
  overflow: hidden;
}

.orders-table {
  width: 100%;
  min-width: 920px;
  border-collapse: separate;
  border-spacing: 0;
}

.orders-table th {
  height: 44px;
  padding: 12px 16px;
  background: #F8FAFC;
  border-bottom: 1px solid #E7ECF3;
  font-size: 12px;
  font-weight: 600;
  color: #667085;
}

.orders-table td {
  padding: 14px 16px;
  border-bottom: 1px solid #EEF1F5;
  font-size: 13px;
  color: #344054;
}

.orders-table tbody tr:hover {
  background: #F8FAFC;
}
```

---

## 29. Acceptance checklist

- [ ] Bảng hiển thị tối đa 8–10 đơn trên Dashboard.
- [ ] Mã đơn dễ nhận biết và click được.
- [ ] Khách hàng có tên và thông tin phụ hợp lý.
- [ ] Trạng thái thanh toán và đơn hàng tách biệt.
- [ ] Badge trạng thái đồng nhất toàn hệ thống.
- [ ] Tổng tiền đúng định dạng Việt Nam.
- [ ] Thời gian có cả dạng tương đối và tuyệt đối.
- [ ] Row quá hạn được cảnh báo nhẹ.
- [ ] Hành động nguy hiểm không chạy optimistic.
- [ ] Loading dùng skeleton rows.
- [ ] Empty state phân biệt với filter không có kết quả.
- [ ] Error state không làm lỗi toàn Dashboard.
- [ ] Bảng cuộn ngang an toàn ở 1024px.
- [ ] Keyboard navigation hoạt động.
- [ ] Drawer trả focus về row khi đóng.
- [ ] Sort và filter phản ánh đúng dữ liệu.
- [ ] Không có bulk action không cần thiết trong widget Dashboard.

---

## 30. Những điều không được làm

- Không nhồi quá nhiều cột vào bảng Dashboard.
- Không dùng màu đỏ đậm cho toàn row.
- Không chỉ dùng icon mà thiếu tooltip.
- Không trộn trạng thái thanh toán và trạng thái đơn.
- Không hiển thị địa chỉ đầy đủ trong bảng.
- Không dùng checkbox chọn nhiều nếu không có bulk action thực tế.
- Không dùng thời gian tương đối mà thiếu ngày giờ cụ thể.
- Không thay đổi trạng thái quan trọng mà không xác nhận hoặc phản hồi backend.
- Không làm row quá thấp gây khó đọc.

---

## 31. Kết luận

Orders Table của Cynca VLXD phải giúp người quản trị xử lý nhanh đơn mới, phát hiện đơn bất thường và truy cập chi tiết mà không làm Dashboard trở nên nặng nề. Bảng phải rõ ràng, có trạng thái đầy đủ, hỗ trợ keyboard và thích ứng tốt với màn hình laptop.

File tiếp theo:

```text
09-Notification.md
```