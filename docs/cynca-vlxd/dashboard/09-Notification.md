# Cynca VLXD Admin Dashboard — Notification Specification

> Phiên bản: 1.0  
> Phạm vi: Hệ thống thông báo trên Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`, `04-Header.md`, `07-Product-Widgets.md`, `08-Orders-Table.md`

---

## 1. Vai trò của hệ thống thông báo

Hệ thống thông báo giúp người quản trị nhận biết các sự kiện cần chú ý mà không phải kiểm tra thủ công từng module.

Thông báo phải hỗ trợ người dùng:

- Phát hiện đơn hàng mới.
- Nhận cảnh báo tồn kho.
- Theo dõi thanh toán và công nợ.
- Nhận biết lỗi đồng bộ hoặc lỗi hệ thống.
- Theo dõi thay đổi quan trọng về sản phẩm.
- Nhận thông báo liên quan đến khách hàng hoặc chiến dịch.
- Điều hướng nhanh đến đối tượng liên quan.
- Phân biệt mức độ khẩn cấp.

Thông báo không được biến thành luồng tin gây nhiễu. Chỉ gửi những sự kiện có giá trị hành động hoặc giá trị nhận biết rõ ràng.

---

## 2. Các điểm hiển thị thông báo

Thông báo có thể xuất hiện ở 4 vị trí:

1. Notification button trong header.
2. Notification dropdown.
3. Notification widget trong Dashboard.
4. Toast cho sự kiện vừa xảy ra hoặc phản hồi thao tác.

Các vị trí này phải dùng chung dữ liệu, trạng thái đã đọc và semantic severity.

---

## 3. Phân loại thông báo

### 3.1 Đơn hàng

- Có đơn hàng mới.
- Đơn chờ xác nhận quá lâu.
- Đơn giao thất bại.
- Đơn bị hủy.
- Đơn yêu cầu hoàn tiền.

### 3.2 Tồn kho

- Sản phẩm sắp hết hàng.
- Sản phẩm hết hàng.
- Tồn kho âm.
- SKU tồn quá lâu.
- Đồng bộ tồn kho thất bại.

### 3.3 Thanh toán và công nợ

- Thanh toán thành công.
- Thanh toán thất bại.
- Công nợ đến hạn.
- Công nợ quá hạn.
- Hoàn tiền hoàn tất.

### 3.4 Sản phẩm

- Sản phẩm thiếu ảnh.
- Sản phẩm thiếu giá.
- Sản phẩm bị ẩn.
- Sản phẩm cập nhật thất bại.
- Sản phẩm mới được tạo.

### 3.5 Khách hàng

- Khách hàng gửi yêu cầu hỗ trợ.
- Khách doanh nghiệp mới.
- Khách hàng khiếu nại.

### 3.6 Hệ thống

- Mất kết nối dịch vụ.
- Đồng bộ thất bại.
- Tác vụ nền hoàn thành.
- Có bản cập nhật hệ thống.
- Quyền truy cập thay đổi.

---

## 4. Mức độ ưu tiên

### Critical

Dùng khi cần xử lý ngay:

- Tồn kho âm.
- Thanh toán lỗi nghiêm trọng.
- Lỗi hệ thống ảnh hưởng vận hành.
- Đơn bị treo hoặc mất dữ liệu.

### High

- Đơn chờ xác nhận quá thời hạn.
- Công nợ quá hạn.
- Hết hàng sản phẩm bán chạy.

### Medium

- Sắp hết hàng.
- Đơn mới.
- Sản phẩm thiếu thông tin.

### Low

- Báo cáo đã tạo xong.
- Đồng bộ hoàn thành.
- Có sản phẩm mới được thêm.

Mức độ ưu tiên không đồng nghĩa hoàn toàn với màu. Nội dung và hành động mới là yếu tố chính.

---

## 5. Notification button trong header

Kích thước:

```css
width: 40px;
height: 40px;
border-radius: 12px;
```

Icon:

```text
Bell
```

Badge số:

```css
position: absolute;
top: 6px;
right: 6px;
min-width: 17px;
height: 17px;
padding-inline: 4px;
border-radius: 999px;
background: #F04438;
color: #FFFFFF;
font-size: 10px;
font-weight: 700;
```

Quy tắc:

- Tối đa hiển thị `99+`.
- Nếu chỉ cần biểu thị có thông báo mới, dùng dot 8px.
- Badge phải phản ánh số thông báo chưa đọc có ý nghĩa, không phải tổng lịch sử.

Accessibility:

```html
aria-label="Thông báo, 4 thông báo chưa đọc"
```

---

## 6. Notification dropdown

Kích thước:

```css
width: 380px;
max-height: 520px;
background: #FFFFFF;
border: 1px solid #E7ECF3;
border-radius: 16px;
box-shadow: 0 16px 40px rgba(16,24,40,0.16);
```

Vị trí:

- Căn phải với notification button.
- Cách trigger 8px.
- Không vượt khỏi viewport.

Cấu trúc:

```text
NotificationDropdown
├── Header
├── Filter tabs
├── Notification list
└── Footer
```

---

## 7. Dropdown header

Nội dung:

```text
Thông báo                     Đánh dấu tất cả đã đọc
```

Title:

```css
font-size: 16px;
font-weight: 600;
color: #182230;
```

Action:

```css
font-size: 12px;
font-weight: 600;
color: #0B57F0;
```

Không hiển thị action nếu không có thông báo chưa đọc.

---

## 8. Filter tabs

Tab đề xuất:

- Tất cả.
- Chưa đọc.
- Cần xử lý.

Không dùng quá 3 tab trong dropdown.

Tab style:

```css
height: 32px;
padding-inline: 10px;
border-radius: 9px;
font-size: 12px;
font-weight: 600;
```

Active:

```css
background: #EAF1FF;
color: #0B57F0;
```

---

## 9. Notification item

Cấu trúc:

```text
[Icon] Tiêu đề
       Mô tả ngắn
       12 phút trước                [dot/menu]
```

Kích thước:

```css
min-height: 76px;
padding: 14px 16px;
border-bottom: 1px solid #EEF1F5;
```

Hover:

```css
background: #F8FAFC;
```

Unread:

```css
background: #F8FAFF;
```

Có thể thêm indicator trái:

```css
box-shadow: inset 3px 0 0 #0B57F0;
```

Chỉ dùng indicator nhẹ, không dùng nền xanh đậm.

---

## 10. Notification icon

Kích thước:

```css
width: 36px;
height: 36px;
border-radius: 12px;
display: grid;
place-items: center;
```

Mapping:

| Loại | Icon | Nền | Màu |
|---|---|---|---|
| Đơn hàng | `ShoppingCart` | `#EAF1FF` | `#0B57F0` |
| Tồn kho | `Warehouse` | `#FFF6ED` | `#EC4A0A` |
| Thanh toán | `CreditCard` | `#ECFDF3` | `#039855` |
| Cảnh báo | `TriangleAlert` | `#FEF3F2` | `#D92D20` |
| Hệ thống | `Settings` | `#F2F4F7` | `#475467` |

Không dùng emoji hoặc icon màu không nhất quán.

---

## 11. Nội dung thông báo

### Tiêu đề

```css
font-size: 13px;
font-weight: 600;
line-height: 18px;
color: #182230;
```

### Mô tả

```css
font-size: 12px;
font-weight: 400;
line-height: 18px;
color: #667085;
margin-top: 3px;
```

### Thời gian

```css
font-size: 11px;
color: #98A2B3;
margin-top: 6px;
```

Quy tắc nội dung:

- Tiêu đề ngắn, tối đa khoảng 60 ký tự.
- Mô tả nêu rõ đối tượng và vấn đề.
- Không dùng ngôn ngữ mơ hồ như `Có lỗi xảy ra`.
- Không đưa dữ liệu nhạy cảm không cần thiết.

Ví dụ tốt:

```text
Đơn #CYN-240806-0187 chờ xác nhận
Đơn hàng của Nguyễn Văn Minh đã chờ 32 phút.
```

Ví dụ không tốt:

```text
Bạn có một thông báo mới
```

---

## 12. Hành động trên item

Click item phải:

- Mở trang liên quan.
- Hoặc mở drawer xem nhanh.
- Đồng thời đánh dấu đã đọc nếu phù hợp.

Menu ba chấm có thể gồm:

- Đánh dấu đã đọc/chưa đọc.
- Ẩn thông báo.
- Tắt loại thông báo này.

Không đặt nhiều nút trực tiếp trong item.

---

## 13. Notification widget trên Dashboard

Widget dùng để hiển thị các thông báo cần xử lý nhất.

Card:

```css
min-height: 360px;
background: #FFFFFF;
border: 1px solid #E7ECF3;
border-radius: 20px;
```

Header:

```text
Cần chú ý                         Xem tất cả
```

Hiển thị tối đa:

```text
5–6 thông báo
```

Ưu tiên theo:

1. Severity.
2. Thời gian.
3. Trạng thái chưa xử lý.

Widget không nên lặp toàn bộ notification dropdown. Chỉ hiển thị item có giá trị hành động.

---

## 14. Notification widget item

Có thể dùng layout rộng hơn dropdown:

```text
[Icon] Tồn kho âm ở Kho Hà Nội
       Thép Việt Nhật D16 đang có tồn kho -4.
       10 phút trước                 [Kiểm tra kho]
```

Hành động trực tiếp chỉ hiển thị khi rõ ràng:

- Kiểm tra kho.
- Xem đơn hàng.
- Xem công nợ.
- Thử đồng bộ lại.

---

## 15. Toast notification

Toast dùng cho phản hồi tức thời sau thao tác hoặc sự kiện thời gian thực.

Các loại:

- Success.
- Info.
- Warning.
- Error.

Kích thước:

```css
width: min(380px, calc(100vw - 48px));
min-height: 64px;
padding: 14px 16px;
border-radius: 14px;
```

Vị trí:

```css
position: fixed;
top: 88px;
right: 24px;
z-index: 100;
```

---

## 16. Toast content

Cấu trúc:

```text
[Icon] Tiêu đề                              [×]
       Mô tả tùy chọn
       [Action tùy chọn]
```

Ví dụ:

```text
Đã cập nhật trạng thái đơn hàng
Đơn #CYN-240806-0187 đã chuyển sang Đang giao.
```

Không dùng toast để yêu cầu người dùng đọc nội dung dài.

---

## 17. Toast duration

- Success: 4 giây.
- Info: 5 giây.
- Warning: 7 giây.
- Error: không tự đóng quá nhanh; tối thiểu 8 giây hoặc yêu cầu đóng thủ công.

Toast có hành động phải tồn tại đủ lâu để người dùng tương tác.

Hỗ trợ pause timer khi hover hoặc focus.

---

## 18. Read state

Trạng thái:

- Unread.
- Read.
- Archived hoặc dismissed.
- Resolved, nếu có workflow xử lý.

`Read` không đồng nghĩa `Resolved`.

Ví dụ cảnh báo tồn kho:

- Đã đọc: người dùng đã xem.
- Đã xử lý: tồn kho đã được cập nhật hoặc phiếu nhập đã tạo.

Nếu nghiệp vụ cần, hệ thống phải lưu riêng hai trạng thái.

---

## 19. Mark all as read

Khi người dùng chọn `Đánh dấu tất cả đã đọc`:

- Chỉ áp dụng cho phạm vi filter hiện tại hoặc phải nói rõ áp dụng toàn bộ.
- Cập nhật badge ngay sau phản hồi thành công.
- Có thể dùng optimistic update nếu backend hỗ trợ idempotent.
- Nếu lỗi, khôi phục trạng thái và hiển thị toast.

Không cần modal xác nhận cho thao tác này.

---

## 20. Grouping

Dropdown có thể nhóm theo thời gian:

```text
Hôm nay
Hôm qua
Trước đó
```

Hoặc nhóm theo loại:

```text
Đơn hàng
Kho hàng
Hệ thống
```

Không dùng đồng thời cả hai kiểu nhóm trong cùng dropdown.

Khuyến nghị dùng grouping theo thời gian.

---

## 21. Notification preferences

Người dùng có thể cấu hình:

- Nhận thông báo trong Dashboard.
- Nhận qua email.
- Nhận qua Zalo OA hoặc kênh khác nếu hệ thống hỗ trợ.
- Mức cảnh báo tồn kho.
- Loại thông báo được phép tắt.

Không cho phép tắt các thông báo bảo mật hoặc lỗi hệ thống nghiêm trọng nếu nghiệp vụ yêu cầu.

---

## 22. Deduplication

Không tạo nhiều thông báo giống nhau cho cùng sự kiện.

Ví dụ sản phẩm tiếp tục dưới ngưỡng tồn kho:

- Cập nhật notification hiện có.
- Hoặc gom thành summary.

Ví dụ:

```text
5 sản phẩm tại Kho Hà Nội đang dưới mức tồn tối thiểu
```

Không gửi 20 toast liên tiếp cho 20 SKU trong cùng đợt đồng bộ.

---

## 23. Rate limiting

Đối với sự kiện số lượng lớn:

- Gom theo batch.
- Hạn chế toast.
- Chỉ cập nhật badge và danh sách.

Critical event vẫn có thể hiển thị toast riêng.

---

## 24. Real-time behavior

Có thể cập nhật bằng:

- WebSocket.
- Server-Sent Events.
- Polling theo chu kỳ.

Khi có notification mới:

- Tăng badge.
- Chèn item vào đầu danh sách.
- Chỉ hiển thị toast nếu sự kiện phù hợp.
- Không tự mở dropdown.

Không làm layout header nhảy khi badge thay đổi.

---

## 25. Loading state

Dropdown loading:

- 5 skeleton item.
- Giữ chiều rộng và chiều cao hợp lý.

Skeleton:

```css
.notification-skeleton {
  min-height: 76px;
  padding: 14px 16px;
}
```

Widget loading:

- Skeleton title.
- 5 item skeleton.
- Giữ min-height card.

---

## 26. Empty state

Dropdown:

```text
Bạn chưa có thông báo mới
Các cập nhật quan trọng sẽ xuất hiện tại đây.
```

Tab chưa đọc:

```text
Bạn đã đọc tất cả thông báo
```

Widget cần chú ý:

```text
Không có vấn đề cần xử lý
Hoạt động hệ thống đang ổn định.
```

---

## 27. Error state

```text
Không thể tải thông báo
[ Thử lại ]
```

Nếu mark-as-read thất bại:

```text
Chưa thể cập nhật trạng thái thông báo
```

Không xóa item khỏi giao diện trước khi biết kết quả nếu thao tác không an toàn để optimistic update.

---

## 28. Responsive behavior

### Viewport ≥ 1024px

- Dropdown rộng 380px.
- Widget hiển thị trong grid Dashboard.

### Viewport < 1024px

Notification dropdown có thể chuyển thành drawer bên phải hoặc bottom sheet.

Drawer:

```css
width: min(420px, calc(100vw - 24px));
```

Toast:

```css
left: 16px;
right: 16px;
width: auto;
```

---

## 29. Keyboard interaction

Yêu cầu:

- Enter hoặc Space mở dropdown.
- Escape đóng dropdown.
- Arrow keys có thể điều hướng item nếu triển khai menu semantics.
- Tab di chuyển qua filter, item và actions.
- Sau khi đóng, focus quay lại notification button.
- Toast có action phải focus được.

Không tự chuyển focus sang toast thông thường.

---

## 30. Accessibility

Notification button:

```html
<button aria-label="Thông báo, 4 thông báo chưa đọc" aria-expanded="false">
```

Dropdown:

```html
<section aria-labelledby="notification-heading">
```

Toast region:

```html
<div aria-live="polite" aria-relevant="additions">
```

Critical error toast:

```html
role="alert"
```

Không dùng `role="alert"` cho mọi toast vì gây quá tải screen reader.

Unread state phải có text hoặc accessible label, không chỉ background.

---

## 31. Data contract đề xuất

```ts
export type NotificationSeverity = "critical" | "high" | "medium" | "low";

export type NotificationCategory =
  | "order"
  | "inventory"
  | "payment"
  | "product"
  | "customer"
  | "system";

export type DashboardNotification = {
  id: string;
  category: NotificationCategory;
  severity: NotificationSeverity;
  title: string;
  description?: string;
  createdAt: string;
  readAt?: string;
  resolvedAt?: string;
  href?: string;
  actionLabel?: string;
  entityType?: string;
  entityId?: string;
};
```

---

## 32. API behavior đề xuất

```text
GET    /api/notifications
PATCH  /api/notifications/:id/read
PATCH  /api/notifications/read-all
PATCH  /api/notifications/:id/dismiss
GET    /api/notification-preferences
PATCH  /api/notification-preferences
```

Query params:

```text
?status=unread
?severity=high,critical
?category=inventory
?limit=20
```

---

## 33. Component tree đề xuất

```text
NotificationSystem
├── NotificationButton
│   └── UnreadBadge
├── NotificationDropdown
│   ├── DropdownHeader
│   ├── NotificationFilters
│   ├── NotificationList
│   │   └── NotificationItem
│   └── DropdownFooter
├── NotificationWidget
│   ├── WidgetHeader
│   └── ActionableNotificationList
└── ToastProvider
    └── ToastViewport
        └── ToastItem
```

---

## 34. CSS tham chiếu

```css
.notification-dropdown {
  width: 380px;
  max-height: 520px;
  overflow: hidden;
  background: #FFFFFF;
  border: 1px solid #E7ECF3;
  border-radius: 16px;
  box-shadow: 0 16px 40px rgba(16,24,40,0.16);
}

.notification-list {
  max-height: 420px;
  overflow-y: auto;
}

.notification-item {
  display: grid;
  grid-template-columns: 36px minmax(0, 1fr) auto;
  gap: 12px;
  min-height: 76px;
  padding: 14px 16px;
  border-bottom: 1px solid #EEF1F5;
}

.notification-item:hover {
  background: #F8FAFC;
}

.notification-item--unread {
  background: #F8FAFF;
}
```

---

## 35. Acceptance checklist

- [ ] Badge phản ánh đúng số thông báo chưa đọc.
- [ ] Dropdown không vượt khỏi viewport.
- [ ] Có filter Tất cả, Chưa đọc và Cần xử lý.
- [ ] Notification item nêu rõ đối tượng và vấn đề.
- [ ] Severity và category được tách biệt.
- [ ] Read và resolved là hai trạng thái riêng khi cần.
- [ ] Widget chỉ hiển thị thông báo có giá trị hành động.
- [ ] Toast không lạm dụng cho sự kiện số lượng lớn.
- [ ] Có loading, empty và error state.
- [ ] Mark all as read xử lý lỗi đúng.
- [ ] Có deduplication và grouping hợp lý.
- [ ] Real-time update không tự mở dropdown.
- [ ] Keyboard và focus management hoạt động.
- [ ] Toast dùng aria-live phù hợp.
- [ ] Critical event không thể bị ẩn hoàn toàn nếu nghiệp vụ yêu cầu.
- [ ] Notification preference được kiểm soát theo quyền.

---

## 36. Những điều không được làm

- Không gửi notification cho mọi thay đổi nhỏ.
- Không dùng tiêu đề mơ hồ.
- Không hiển thị nhiều toast liên tiếp cho sự kiện batch.
- Không dùng màu đỏ cho mọi thông báo.
- Không tự mở dropdown khi có thông báo mới.
- Không đồng nhất `đã đọc` với `đã xử lý`.
- Không xóa lịch sử quan trọng chỉ vì người dùng dismiss.
- Không để badge tăng vô hạn mà không có quy tắc.
- Không dùng `role="alert"` cho mọi toast.
- Không để thông báo dẫn đến trang không có quyền truy cập.

---

## 37. Kết luận

Hệ thống thông báo của Cynca VLXD phải giúp người quản trị nhận biết đúng sự kiện, đúng mức độ và đúng thời điểm. Thiết kế cần giảm nhiễu, ưu tiên hành động, hỗ trợ real-time và giữ trạng thái nhất quán giữa header, widget và toast.

File tiếp theo:

```text
10-Animation.md
```