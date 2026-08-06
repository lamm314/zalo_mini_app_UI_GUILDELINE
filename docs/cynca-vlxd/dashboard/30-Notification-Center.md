# Cynca VLXD Admin Dashboard — Notification Center Specification

> Phiên bản: 1.0  
> Phạm vi: Trung tâm thông báo quản trị, cảnh báo nghiệp vụ và tác vụ cần xử lý  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, QA, Operations và AI coding agent  
> Tài liệu liên quan: `09-Notification.md`, `16-Data-Contracts.md`, `23-Security-Privacy.md`, `24-Observability.md`, `27-Role-Based-Dashboard.md`

---

## 1. Mục tiêu

Notification Center là nơi tập trung các thông báo có giá trị vận hành cho người dùng Dashboard Cynca VLXD.

Mục tiêu:

- Giúp người dùng nhận biết nhanh vấn đề cần xử lý.
- Phân biệt thông báo đơn thuần với cảnh báo cần hành động.
- Không tạo spam hoặc làm gián đoạn công việc.
- Tuân thủ permission, role và data scope.
- Đồng bộ trạng thái đọc, xử lý và bỏ qua.
- Hỗ trợ real-time nhưng có fallback khi kết nối gián đoạn.
- Cung cấp lịch sử đủ dùng cho vận hành và điều tra.
- Không đưa PII hoặc dữ liệu tài chính nhạy cảm dư thừa vào thông báo.

---

## 2. Phân biệt ba loại thông báo

### Persistent Notification

Được lưu trong Notification Center.

Ví dụ:

- Đơn mới chờ xác nhận.
- Tồn kho âm.
- Thanh toán thất bại.
- Đồng bộ kho lỗi.

### Toast Feedback

Phản hồi ngắn sau hành động của người dùng.

Ví dụ:

- Đã lưu thay đổi.
- Xuất báo cáo đang được xử lý.
- Cập nhật trạng thái thất bại.

### Dashboard Alert Widget

Chỉ hiển thị các thông báo có tính hành động cao trên trang Dashboard.

Không đồng nhất ba loại này thành một luồng UI duy nhất.

---

## 3. Notification categories

```ts
export type NotificationCategory =
  | "order"
  | "inventory"
  | "payment"
  | "customer"
  | "product"
  | "report"
  | "security"
  | "system";
```

### Order

- Đơn mới.
- Đơn chờ xác nhận quá lâu.
- Đơn giao thất bại.
- Đơn bị hủy hoặc hoàn trả.

### Inventory

- Tồn kho âm.
- Hết hàng.
- Sắp hết hàng.
- Đồng bộ kho thất bại.

### Payment

- Thanh toán thất bại.
- Công nợ đến hạn.
- Hoàn tiền chờ xử lý.

### Customer

- Khiếu nại mới.
- Khách cần gọi lại.
- Yêu cầu hỗ trợ quá hạn.

### Product

- Thiếu ảnh.
- Thiếu giá.
- SKU trùng.
- Dữ liệu sản phẩm không hoàn chỉnh.

### Report

- Báo cáo đã tạo xong.
- Export thất bại.

### Security

- Đăng nhập bất thường.
- Thay đổi quyền.
- Tài khoản bị khóa.

### System

- Bảo trì.
- API hoặc tích hợp gặp sự cố.
- Cấu hình Dashboard mới được publish.

---

## 4. Severity model

```ts
export type NotificationSeverity =
  | "critical"
  | "high"
  | "medium"
  | "low";
```

| Severity | Ý nghĩa | Ví dụ |
|---|---|---|
| Critical | Cần xử lý ngay, rủi ro cao | Lộ quyền, tồn kho âm diện rộng |
| High | Cần xử lý sớm | Thanh toán thất bại, đơn quá hạn |
| Medium | Cần chú ý | Sắp hết hàng, dữ liệu sản phẩm thiếu |
| Low | Thông tin | Báo cáo đã sẵn sàng, cập nhật hệ thống |

Severity không đồng nghĩa với màu duy nhất. Luôn có text hoặc icon đi kèm.

---

## 5. Notification state model

```ts
export type NotificationState = {
  readAt?: string;
  resolvedAt?: string;
  dismissedAt?: string;
  snoozedUntil?: string;
};
```

### Unread

Người dùng chưa mở hoặc chưa đánh dấu đã đọc.

### Read

Người dùng đã xem nhưng vấn đề có thể chưa được xử lý.

### Resolved

Vấn đề nghiệp vụ đã hoàn tất.

### Dismissed

Người dùng bỏ khỏi danh sách cá nhân nhưng không đồng nghĩa vấn đề đã được giải quyết.

### Snoozed

Tạm ẩn đến thời điểm đã chọn.

Không dùng `read=true` để biểu thị vấn đề đã xử lý.

---

## 6. Notification data contract

```ts
export type DashboardNotification = {
  id: string;
  category: NotificationCategory;
  severity: NotificationSeverity;
  title: string;
  description?: string;
  createdAt: string;
  updatedAt?: string;
  readAt?: string;
  resolvedAt?: string;
  dismissedAt?: string;
  snoozedUntil?: string;
  href?: string;
  entityType?: string;
  entityId?: string;
  actionLabel?: string;
  availableActions?: NotificationAction[];
  source?: string;
  deduplicationKey?: string;
  expiresAt?: string;
};
```

---

## 7. Notification actions

```ts
export type NotificationAction =
  | "view"
  | "mark_read"
  | "mark_unread"
  | "resolve"
  | "dismiss"
  | "snooze"
  | "retry"
  | "download";
```

Backend nên trả `availableActions` dựa trên trạng thái và quyền.

Frontend không tự giả định mọi notification đều có thể resolve hoặc dismiss.

---

## 8. Header Notification Button

Nút chuông gồm:

- Icon Bell.
- Unread badge.
- Tooltip.
- Accessible name.

Accessible label mẫu:

```text
Mở thông báo, 7 thông báo chưa đọc
```

Badge:

- 0: ẩn.
- 1–99: hiển thị số.
- Trên 99: `99+`.

Badge update không được làm header nhảy layout.

---

## 9. Notification dropdown desktop

Kích thước đề xuất:

```text
Width: 380–420px
Max-height: 640px
```

Cấu trúc:

```text
NotificationDropdown
├── Header
├── Tabs
├── Filter/Sort Controls
├── NotificationList
├── Load More
└── Footer
```

Header gồm:

- Tiêu đề `Thông báo`.
- Số chưa đọc.
- `Đánh dấu tất cả đã đọc`.
- Link `Xem tất cả`.

---

## 10. Notification Center full page

Route đề xuất:

```text
/notifications
```

Mục tiêu:

- Xem toàn bộ lịch sử phù hợp quyền.
- Filter nâng cao.
- Bulk actions có kiểm soát.
- Tìm thông báo theo loại hoặc trạng thái.
- Xử lý notification actionable.

Layout desktop:

```text
Page Toolbar
Filter Bar
Summary Cards
Notification List / Table
Detail Drawer
```

---

## 11. Tabs

Tabs mặc định:

```text
Tất cả
Chưa đọc
Cần xử lý
Đã giải quyết
```

Tùy role có thể thêm:

```text
Đơn hàng
Tồn kho
Thanh toán
Hệ thống
```

Không hiển thị quá nhiều tabs trên dropdown nhỏ. Category filter nâng cao nên chuyển sang full page.

---

## 12. Filter model

```ts
export type NotificationFilter = {
  state?: "all" | "unread" | "actionable" | "resolved" | "dismissed";
  categories?: NotificationCategory[];
  severities?: NotificationSeverity[];
  from?: string;
  to?: string;
  branchId?: string;
  warehouseId?: string;
};
```

Full page filters:

- Trạng thái.
- Category.
- Severity.
- Date range.
- Branch.
- Warehouse.
- Source.

Filter phải tuân thủ scope người dùng.

---

## 13. Sorting

Mặc định:

1. Critical chưa giải quyết.
2. High chưa giải quyết.
3. Unread mới nhất.
4. Các notification còn lại theo thời gian giảm dần.

Tùy chọn:

- Mới nhất.
- Cũ nhất.
- Severity cao nhất.
- Cần xử lý trước.

Không để low-priority notifications đẩy cảnh báo critical xuống dưới.

---

## 14. Notification item anatomy

Mỗi item gồm:

- Category icon.
- Severity indicator.
- Title.
- Description tối đa 2–3 dòng.
- Timestamp.
- Read/unread state.
- Action chính.
- More menu.

Không đưa quá nhiều metadata vào dropdown.

Full page có thể thêm:

- Branch.
- Warehouse.
- Entity code.
- Source.
- Resolution state.

---

## 15. Typography và spacing

Dropdown item:

```text
Padding: 14–16px
Gap icon-content: 12px
Title: 14px / 20px / Semibold
Description: 13px / 20px
Metadata: 12px / 18px
```

Unread item có nền brand-subtle rất nhẹ.

Resolved item không giảm opacity quá thấp gây khó đọc.

---

## 16. Category icon mapping

| Category | Icon đề xuất |
|---|---|
| Order | ShoppingCart / Package |
| Inventory | Boxes / Warehouse |
| Payment | CreditCard / Wallet |
| Customer | UserRound / MessageCircle |
| Product | PackageSearch |
| Report | FileSpreadsheet |
| Security | ShieldAlert |
| System | Settings / ServerCog |

Dùng cùng một bộ Lucide hoặc bộ icon đã chọn.

---

## 17. Color mapping

| Severity | Background | Text/Icon |
|---|---|---|
| Critical | danger soft | danger text |
| High | warning soft hoặc danger soft nhẹ | warning/danger text |
| Medium | info soft | info text |
| Low | neutral soft | secondary text |

Không dùng toàn bộ item nền đỏ hoặc vàng đậm.

---

## 18. Unread behavior

Notification được đánh dấu read khi:

- Người dùng mở detail có chủ đích.
- Click action liên quan.
- Chọn `Đánh dấu đã đọc`.

Không tự động mark read chỉ vì dropdown mở.

Có thể mark read khi item nằm trong viewport đủ lâu, nhưng cần product decision rõ và test chính xác.

---

## 19. Mark all as read

Yêu cầu:

- Chỉ áp dụng notification trong scope hiện tại hoặc toàn bộ theo thiết kế đã nêu rõ.
- Có pending state.
- Rollback UI khi API lỗi.
- Không đồng nghĩa resolve.

Confirmation thường không cần vì đây là action có thể đảo ngược.

---

## 20. Resolve workflow

Resolve chỉ dùng cho notification gắn với vấn đề có lifecycle.

Ví dụ:

- Tồn kho âm đã được điều chỉnh.
- Khiếu nại đã xử lý.
- Đồng bộ kho đã chạy lại thành công.

Backend có thể tự resolve khi entity thay đổi trạng thái.

Frontend không nên cho resolve thủ công nếu nghiệp vụ không cho phép.

---

## 21. Dismiss workflow

Dismiss chỉ ẩn notification khỏi danh sách cá nhân.

Yêu cầu:

- Không ảnh hưởng entity.
- Có thể undo trong toast ngắn.
- Không cho dismiss security-critical notification nếu policy cấm.
- Có audit khi dismiss notification quan trọng nếu cần.

---

## 22. Snooze workflow

Preset đề xuất:

```text
1 giờ
Đến chiều nay
Đến ngày mai
1 tuần
Tùy chọn
```

Snooze không dùng cho:

- Critical security alerts.
- Vấn đề bắt buộc xử lý ngay.

Sau thời gian snooze, notification quay lại đúng vị trí ưu tiên.

---

## 23. Notification detail drawer

Khi notification cần thêm ngữ cảnh, mở drawer bên phải.

Nội dung:

- Title.
- Severity.
- Category.
- Full description.
- Timestamp.
- Entity summary.
- Timeline.
- Available actions.
- Related notifications.

Drawer không tải PII ngoài quyền.

Focus trả về notification item khi đóng.

---

## 24. Notification grouping

Có thể nhóm theo:

- Thời gian: Hôm nay, Hôm qua, Cũ hơn.
- Entity.
- Event batch.
- Category.

Ví dụ gom batch:

```text
12 sản phẩm tại Kho Hà Nội sắp hết hàng.
```

Thay vì tạo 12 toast và 12 item liên tiếp nếu người dùng chỉ cần một summary.

Full detail có thể mở danh sách SKU.

---

## 25. Deduplication

Backend nên dùng:

```text
deduplicationKey
entityId
event type
time window
```

Ví dụ:

```text
inventory.low_stock:warehouse_hn:sku_123
```

Nếu cùng sự kiện lặp:

- Update notification hiện tại.
- Tăng counter.
- Cập nhật timestamp.
- Không tạo item mới vô hạn.

---

## 26. Notification expiry

Một số notification có thể hết hiệu lực.

Ví dụ:

- Báo cáo download link hết hạn.
- Thông báo bảo trì đã qua.
- Chương trình tạm thời kết thúc.

Khi hết hạn:

- Ẩn action không còn dùng được.
- Giữ lịch sử nếu cần.
- Hiển thị trạng thái `Đã hết hạn`.

---

## 27. Role-based notification rules

### Sales

- Đơn mới được phân công.
- Khách cần liên hệ.
- Đơn quá hạn xác nhận.

### Warehouse

- Tồn kho âm.
- Hết hàng.
- Đơn chờ xuất.
- Đồng bộ kho lỗi.

### Accountant

- Thanh toán thất bại.
- Công nợ đến hạn.
- Hoàn tiền chờ xử lý.

### Admin

- Lỗi hệ thống.
- Thay đổi quyền.
- Cấu hình Dashboard publish.
- Security alerts.

Backend phải lọc theo permission và scope.

---

## 28. Notification privacy

Không đưa vào notification list:

- Địa chỉ khách hàng đầy đủ.
- Số điện thoại đầy đủ nếu không cần.
- Chi tiết tài chính nhạy cảm.
- Token, URL nội bộ hoặc stack trace.

Ví dụ tốt:

```text
Đơn #CYN-240806-0187 đang chờ xác nhận quá 30 phút.
```

Không tốt:

```text
Nguyễn Văn A, 0987..., địa chỉ đầy đủ..., đơn 12.500.000 đồng...
```

---

## 29. Real-time architecture

Có thể dùng:

- WebSocket.
- Server-Sent Events.
- Polling fallback.

Luồng:

```text
Connect
→ Authenticate
→ Subscribe scope
→ Receive event
→ Validate schema
→ Deduplicate
→ Update query cache
→ Update unread count
```

Không trực tiếp render payload real-time chưa validate.

---

## 30. Reconnect behavior

Khi mất kết nối:

- Hiển thị trạng thái nhẹ nếu kéo dài.
- Exponential backoff.
- Không reconnect vô hạn với tần suất cao.
- Sau reconnect, lấy delta hoặc refetch list.
- Deduplicate backlog.
- Không phát lại toast cho toàn bộ backlog.

---

## 31. Polling fallback

Nếu real-time không khả dụng:

- Poll unread count mỗi 1–5 phút tùy nghiệp vụ.
- Khi dropdown mở có thể refetch list.
- Tạm dừng polling khi tab hidden nếu phù hợp.
- Resume và refetch khi tab active.

Không polling mỗi vài giây gây tải không cần thiết.

---

## 32. API endpoints đề xuất

```text
GET    /api/v1/notifications
GET    /api/v1/notifications/unread-count
GET    /api/v1/notifications/:id
PATCH  /api/v1/notifications/:id/read
PATCH  /api/v1/notifications/:id/unread
PATCH  /api/v1/notifications/:id/resolve
PATCH  /api/v1/notifications/:id/dismiss
PATCH  /api/v1/notifications/:id/snooze
PATCH  /api/v1/notifications/read-all
POST   /api/v1/notifications/:id/retry
```

---

## 33. List response contract

```ts
export type NotificationListResponse = {
  items: DashboardNotification[];
  unreadCount: number;
  actionableCount: number;
  nextCursor?: string | null;
  updatedAt: string;
};
```

Notification feed nên ưu tiên cursor pagination.

---

## 34. Cursor pagination

Request:

```text
?cursor=...&limit=20
```

Quy tắc:

- Dropdown tải 10–20 item đầu.
- Full page tải 20–50 item.
- Infinite scroll hoặc `Tải thêm`.
- Không tự tải vô hạn khi user không cuộn.

---

## 35. Loading states

### Header count loading

- Không hiển thị `0` giả.
- Giữ button ổn định.

### Dropdown loading

- Skeleton 5–6 items.
- Giữ width và max-height.

### Detail loading

- Skeleton riêng trong drawer.

Không dùng spinner toàn trang.

---

## 36. Empty states

### Tất cả

```text
Bạn chưa có thông báo nào.
```

### Chưa đọc

```text
Bạn đã đọc tất cả thông báo.
```

### Cần xử lý

```text
Không có vấn đề nào đang chờ xử lý.
```

### Filter không có kết quả

```text
Không tìm thấy thông báo phù hợp với bộ lọc.
```

---

## 37. Error states

### Dropdown error

```text
Không thể tải thông báo.
```

Có nút `Thử lại`.

### Mutation error

Toast hoặc inline feedback:

```text
Không thể đánh dấu thông báo đã đọc. Vui lòng thử lại.
```

Nếu optimistic update đã áp dụng, phải rollback.

---

## 38. Offline state

Khi offline:

- Hiển thị danh sách cache gần nhất nếu an toàn.
- Có stale label.
- Disable mutation hoặc queue có kiểm soát.
- Không báo thành công trước khi server xác nhận.

Không lưu payload nhạy cảm lâu dài trong browser storage.

---

## 39. Toast policy

Chỉ dùng toast cho:

- Mutation result.
- Background job result.
- Critical event tức thời có giá trị rõ.

Không toast mọi notification mới.

Giới hạn:

- Không quá 3 toast hiển thị cùng lúc.
- Gom batch khi có nhiều event.
- Error toast không tự biến mất quá nhanh.

---

## 40. Accessibility

- Notification button có accessible name.
- Dropdown dùng dialog/popover pattern phù hợp.
- Tabs có keyboard navigation.
- Item action dùng button/link semantic.
- Unread không chỉ biểu thị bằng nền màu.
- Live region dùng `polite` cho count update.
- Critical alert có thể dùng `role=alert` nếu thật sự cần.
- Focus return khi đóng dropdown/drawer.
- Infinite list vẫn có cách tải thêm bằng keyboard.

---

## 41. Responsive behavior

### Desktop

- Dropdown 380–420px.

### Tablet

- Dropdown hoặc right sheet rộng 420–480px.

### Mobile

- Full-screen sheet.
- Header sticky.
- Tabs scroll ngang hoặc chuyển select.
- Touch target tối thiểu 44px.
- Detail có thể mở full-screen.

Không để dropdown nhỏ vượt cạnh viewport.

---

## 42. Performance

- Virtualize full-page list nếu rất dài.
- Không render hàng trăm item trong dropdown.
- Lazy-load detail.
- Debounce filter search nếu có.
- Batch unread count updates.
- Dùng cursor pagination.
- Không subscribe event ngoài scope.

---

## 43. Security

- Backend kiểm tra permission và object scope.
- Notification href phải là route whitelist.
- Không tin entity ID từ client.
- Không hiển thị notification của branch khác.
- Không trả payload chi tiết trước khi user có quyền.
- Mutation phải chống CSRF nếu dùng cookie session.
- Security notification không được dismiss nếu policy cấm.

---

## 44. Audit requirements

Nên audit:

- Resolve notification quan trọng.
- Dismiss security notification nếu được phép.
- Retry integration job.
- Mark all read không cần audit chi tiết trong hầu hết trường hợp.

Audit event ví dụ:

```text
notification.resolved
notification.dismissed
notification.retry_requested
```

---

## 45. Analytics events

```text
notifications.dropdown.opened
notifications.filter.changed
notifications.item.opened
notifications.item.read
notifications.item.resolved
notifications.item.dismissed
notifications.item.snoozed
notifications.all_marked_read
notifications.action.failed
```

Không gửi title hoặc description vào analytics.

---

## 46. Observability

Theo dõi:

- Unread count API success.
- List load latency.
- Mutation failure rate.
- Real-time connection state.
- Event delivery delay.
- Duplicate rate.
- Toast volume.
- Permission denied anomalies.

---

## 47. Test cases

### Unit

- Severity mapping.
- Category mapping.
- Deduplication.
- Sorting.
- State transitions.

### Component

- Badge count.
- Dropdown open/close.
- Tabs.
- Mark read/unread.
- Resolve.
- Dismiss/undo.
- Snooze.
- Empty/error/loading.
- Keyboard navigation.

### Integration

- Real-time event cập nhật list và count.
- Reconnect không duplicate.
- Permission filter.
- Mutation rollback.
- Role-based notifications.

### E2E

- Mở dropdown.
- Xem unread.
- Mở detail.
- Thực hiện action.
- Mark all read.
- Mở full Notification Center.
- Filter và pagination.

---

## 48. Visual QA

Baseline:

```text
Notification Button — no unread
Notification Button — 99+
Dropdown Default
Dropdown Loading
Dropdown Empty
Dropdown Error
Critical Notification
Resolved Notification
Full Notification Center
Detail Drawer
Mobile Sheet
Toast Stack
```

Kiểm tra:

- Badge alignment.
- Dropdown edge collision.
- Long title/description.
- Severity consistency.
- Focus ring.
- Scroll behavior.

---

## 49. AI coding agent requirements

AI phải:

- Phân biệt read, resolved, dismissed và snoozed.
- Không mark all read như resolve.
- Không toast mọi real-time event.
- Không render payload chưa validate.
- Không bỏ permission và scope filter.
- Không gửi PII vào notification content hoặc analytics.
- Không dùng polling quá nhanh.
- Bổ sung deduplication và reconnect test.
- Báo rõ API, state model, real-time strategy và fallback đã triển khai.

---

## 50. Acceptance checklist

- [ ] Notification categories và severity rõ ràng.
- [ ] Read, resolved, dismissed và snoozed tách biệt.
- [ ] Header button và unread badge đúng.
- [ ] Dropdown desktop hoàn chỉnh.
- [ ] Có full Notification Center page.
- [ ] Có filter, sorting và cursor pagination.
- [ ] Notification item anatomy nhất quán.
- [ ] Có detail drawer.
- [ ] Có grouping và deduplication.
- [ ] Có role-based và scope filtering.
- [ ] Có real-time và polling fallback.
- [ ] Loading, empty, error, stale và offline states đầy đủ.
- [ ] Toast policy tránh spam.
- [ ] Accessibility và responsive hoàn chỉnh.
- [ ] Security, audit, analytics và observability được xác định.
- [ ] Test unit, component, integration và E2E đầy đủ.

---

## 51. Những điều không được làm

- Không đồng nhất read với resolved.
- Không tự mark read chỉ vì mở dropdown.
- Không toast mọi notification mới.
- Không hiển thị PII dư thừa.
- Không trả notification ngoài role hoặc scope.
- Không dùng raw href không whitelist.
- Không polling vài giây một lần thiếu cần thiết.
- Không reconnect vô hạn không backoff.
- Không tạo item trùng vô hạn.
- Không cho dismiss critical security notification nếu policy cấm.
- Không báo mutation thành công trước backend confirmation.

---

## 52. Kết luận

Notification Center của Cynca VLXD phải giúp người dùng nhận biết đúng vấn đề, ưu tiên đúng việc và hành động nhanh mà không bị spam. Thiết kế phải phân biệt rõ trạng thái đọc và trạng thái xử lý, áp dụng permission và scope từ backend, hỗ trợ real-time có fallback, đồng thời giữ an toàn dữ liệu và trải nghiệm nhất quán trên mọi thiết bị.

File tiếp theo đề xuất:

```text
README.md
31-Search-Command-Center.md
```