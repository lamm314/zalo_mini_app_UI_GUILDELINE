# Cynca VLXD Admin Dashboard — Warehouse Dashboard Specification

> Phiên bản: 1.0  
> Phạm vi: Dashboard kho dành cho quản lý kho và nhân viên kho  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Warehouse Operations, QA và AI coding agent  
> Tài liệu liên quan: `07-Product-Widgets.md`, `08-Orders-Table.md`, `16-Data-Contracts.md`, `27-Role-Based-Dashboard.md`, `30-Notification-Center.md`, `31-Search-Command-Center.md`

---

## 1. Mục tiêu

Warehouse Dashboard giúp đội kho Cynca VLXD nắm nhanh tồn kho, tiến độ nhập–xuất, đơn chờ xử lý và các rủi ro cần hành động ngay.

Dashboard phải trả lời được:

- Kho nào đang có dữ liệu bất thường?
- Có bao nhiêu đơn chờ lấy hàng, đóng gói hoặc bàn giao vận chuyển?
- SKU nào hết hàng, sắp hết, tồn âm hoặc chậm luân chuyển?
- Phiếu nhập, phiếu xuất và điều chuyển nào đang quá hạn?
- Dữ liệu kho có đồng bộ và đủ mới không?
- Nhân viên kho đang có những tác vụ nào cần ưu tiên?
- Có chênh lệch tồn kho, lỗi quét mã hoặc lỗi kiểm đếm nào cần xử lý?

Warehouse Dashboard không thay thế hệ thống WMS hoặc trang quản lý phiếu chi tiết. Đây là màn hình tổng hợp để điều phối công việc trong ngày.

---

## 2. Hai biến thể chính

### Warehouse Manager Dashboard

Dành cho quản lý kho.

Trọng tâm:

- Tổng quan nhiều kho.
- Tồn kho bất thường.
- SLA nhập–xuất.
- Năng suất đội kho.
- Điều chuyển và đồng bộ.
- Cảnh báo tồn, tồn âm và chậm luân chuyển.

### Warehouse Staff Dashboard

Dành cho nhân viên kho.

Trọng tâm:

- Tác vụ hôm nay.
- Đơn chờ lấy hàng.
- Đơn chờ đóng gói.
- Phiếu nhập được giao.
- Cảnh báo SKU trong kho phụ trách.
- Lịch sử thao tác gần nhất.

Hai biến thể dùng cùng component registry nhưng khác permission, scope và thứ tự widget.

---

## 3. Nguyên tắc thiết kế

1. Ưu tiên tác vụ và cảnh báo trước biểu đồ.
2. Không hiển thị KPI tài chính nếu role không có quyền.
3. Tồn kho âm và đơn quá SLA phải nổi bật.
4. Mỗi số tồn phải có đơn vị tính và thời điểm cập nhật.
5. Không dùng số `0` thay cho dữ liệu chưa tải.
6. Không cho phép điều chỉnh tồn trực tiếp từ Dashboard nếu thiếu workflow xác nhận.
7. Mọi action phải dựa trên `availableActions` từ backend.
8. Không để một widget lỗi làm sập toàn Dashboard.
9. Không hiển thị dữ liệu kho ngoài scope.
10. Dashboard phải sử dụng tốt trên màn hình laptop và thiết bị cảm ứng tại kho.

---

## 4. Layout — Warehouse Manager

Desktop 1440px:

```text
Page Toolbar
Warehouse Health Summary
KPI Grid 4–6 cards
Inbound / Outbound Trend         7 columns
Inventory Risk Summary           5 columns
Operational Queues               8 columns
Priority Alerts                  4 columns
Warehouse Performance            7 columns
Slow-moving / Dead Stock         5 columns
Recent Stock Movements          12 columns
```

---

## 5. Layout — Warehouse Staff

Desktop 1440px:

```text
Page Toolbar
My Shift Summary
KPI Grid 4 cards
My Priority Tasks                5 columns
Pick & Pack Queue                7 columns
Inbound Tasks                    6 columns
Inventory Alerts                 6 columns
Recent Completed Actions        12 columns
```

Warehouse Staff cần ít chart hơn, nhiều queue và quick action hơn.

---

## 6. Page toolbar

### Warehouse Manager

- Warehouse filter.
- Branch filter nếu có quyền.
- Date range.
- Shift filter.
- Refresh.
- Export warehouse report.
- Create inbound hoặc transfer action nếu có quyền.

### Warehouse Staff

- Warehouse hiện tại.
- Shift hiện tại.
- Search SKU/đơn.
- Scan code action nếu hỗ trợ.
- Refresh.

Không hiển thị warehouse selector nếu user chỉ được gán một kho.

---

## 7. KPI — Warehouse Manager

KPI đề xuất:

1. Đơn chờ xuất.
2. Phiếu nhập chờ xử lý.
3. SKU sắp hết.
4. SKU hết hàng.
5. SKU tồn âm.
6. Tác vụ quá SLA.

KPI tùy chọn:

- Tổng giá trị tồn kho nếu có quyền.
- Tỷ lệ sử dụng sức chứa.
- Tỷ lệ đồng bộ thành công.
- Tỷ lệ kiểm đếm chênh lệch.

---

## 8. KPI — Warehouse Staff

KPI đề xuất:

1. Tác vụ được giao hôm nay.
2. Đơn chờ lấy hàng.
3. Đơn chờ đóng gói.
4. Tác vụ quá hạn.

Tùy chọn:

- Phiếu nhập được giao.
- Số dòng hàng đã xử lý.
- Tỷ lệ hoàn thành ca.

Không hiển thị năng suất đồng nghiệp khác nếu không có permission.

---

## 9. Warehouse Health Summary

Tóm tắt tình trạng kho theo các nhóm:

```text
Tồn kho
Nhập hàng
Xuất hàng
Đồng bộ dữ liệu
Sức chứa
An toàn tác vụ
```

Mỗi nhóm dùng trạng thái:

```text
Tốt
Cần chú ý
Rủi ro
Chưa đủ dữ liệu
```

Phải có label và lý do, không chỉ dùng màu.

---

## 10. Inventory status model

```ts
export type InventoryStatus =
  | "available"
  | "low_stock"
  | "out_of_stock"
  | "negative_stock"
  | "overstock"
  | "slow_moving"
  | "stale_data"
  | "sync_error";
```

Mỗi trạng thái phải có mapping label, icon và semantic token tập trung.

---

## 11. Inventory availability model

Phân biệt:

```text
On-hand quantity
Reserved quantity
Available quantity
Incoming quantity
Damaged quantity
Blocked quantity
```

Công thức tham khảo:

```text
Available = On-hand - Reserved - Blocked - Damaged
```

Công thức thực tế phải do backend quản lý và thống nhất với nghiệp vụ.

Frontend không tự tính lại nếu backend đã trả giá trị chuẩn.

---

## 12. Inventory Risk Summary

Widget tổng hợp:

- Tồn âm.
- Hết hàng.
- Sắp hết.
- Tồn cao.
- Chậm luân chuyển.
- Dữ liệu stale.

Mỗi nhóm hiển thị:

- Số SKU.
- Số kho bị ảnh hưởng.
- Mức độ nghiêm trọng.
- Xu hướng.
- Drill-down.

---

## 13. Low-stock rules

Low stock phải dựa trên:

- Minimum stock.
- Reorder point.
- Lead time.
- Sales velocity nếu có.
- Incoming stock.

Không gắn `sắp hết` chỉ dựa trên số lượng tuyệt đối giống nhau cho mọi SKU.

Nếu rule chỉ là threshold tĩnh, phải ghi rõ trong tooltip.

---

## 14. Negative stock handling

Tồn âm là cảnh báo critical hoặc high tùy phạm vi.

Mỗi item gồm:

- SKU.
- Warehouse.
- On-hand.
- Reserved.
- Available.
- Last movement.
- Last sync.
- Suggested investigation action.

Không cho phép sửa tồn âm chỉ bằng cách nhập số mới thiếu audit và reason.

---

## 15. Overstock và slow-moving stock

Overstock cần dựa trên:

- Days of inventory.
- Sales velocity.
- Reorder policy.
- Seasonality nếu có.

Slow-moving có thể dựa trên:

```text
Không phát sinh xuất trong 30/60/90 ngày
```

Widget hiển thị:

- SKU.
- Số lượng.
- Giá trị tồn nếu có quyền.
- Days since last movement.
- Suggested action.

---

## 16. Operational Queues

Các queue chính:

```text
Inbound Pending
Putaway Pending
Picking Pending
Packing Pending
Ready to Handover
Transfer Pending
Cycle Count Pending
```

Mỗi queue có:

- Số lượng task.
- Số task quá SLA.
- Oldest task age.
- Owner hoặc team.
- Primary action.

---

## 17. Pick & Pack Queue

Bảng dành cho nhân viên kho:

- Task code.
- Order code.
- Priority.
- Number of lines.
- Number of units.
- Zone/bin.
- Due time.
- Status.
- Action.

Quick actions:

- Bắt đầu lấy hàng.
- Tạm dừng.
- Hoàn tất lấy hàng.
- Bắt đầu đóng gói.
- Báo thiếu hàng.

Mọi action phải được backend kiểm tra lại trạng thái task.

---

## 18. Inbound Queue

Hiển thị:

- Purchase receipt hoặc inbound code.
- Supplier.
- Expected arrival.
- Received status.
- Number of lines.
- Discrepancy count.
- Assigned staff.
- SLA.

Actions:

- Mở chi tiết.
- Bắt đầu nhận.
- Ghi nhận chênh lệch.
- Hoàn tất putaway.

Không hiển thị giá mua nếu role không có permission.

---

## 19. Transfer Queue

Dành cho điều chuyển giữa kho.

Statuses:

```text
Requested
Approved
Picking
In Transit
Received
Cancelled
```

Mỗi item:

- Transfer code.
- Source warehouse.
- Destination warehouse.
- Item count.
- Due time.
- Current owner.
- Status.

Không cho warehouse staff tự approve transfer nếu thiếu quyền.

---

## 20. SLA model

Ví dụ tham khảo:

```text
Pick task bắt đầu trong 30 phút
Packing hoàn tất trong 60 phút
Inbound receiving bắt đầu trong 2 giờ
Putaway hoàn tất trong 4 giờ
Transfer processing trong ngày
```

SLA phải cấu hình theo warehouse, priority hoặc task type nếu cần.

Frontend chỉ hiển thị trạng thái SLA do backend trả hoặc tính từ rule version rõ ràng.

---

## 21. Priority Alerts

Cảnh báo cần ưu tiên:

- Tồn âm.
- Thiếu hàng ảnh hưởng đơn đang xử lý.
- Đơn xuất quá SLA.
- Inbound discrepancy lớn.
- Đồng bộ kho lỗi.
- Sức chứa vượt ngưỡng.
- Nhiều lần quét mã thất bại.
- Phiếu điều chuyển quá hạn.

Mỗi alert có severity, scope, impact, timestamp và action.

---

## 22. Warehouse Performance

Dành cho Warehouse Manager.

Metrics:

- Tasks completed.
- Pick accuracy.
- Packing accuracy.
- Average processing time.
- SLA compliance.
- Inbound discrepancy rate.
- Stock adjustment count.
- Rework count.

Không dùng productivity metric để đánh giá cá nhân thiếu ngữ cảnh về task complexity.

---

## 23. Staff Performance

Nếu organization policy cho phép, bảng gồm:

- Staff name.
- Assigned tasks.
- Completed tasks.
- SLA compliance.
- Accuracy.
- Rework.
- Active task.

Quy tắc:

- Chỉ manager có quyền.
- Không hiển thị lương hoặc thông tin cá nhân.
- Không dùng một metric duy nhất để xếp hạng.
- Có thể hiển thị theo team thay vì cá nhân để giảm áp lực không cần thiết.

---

## 24. Recent Stock Movements

Bảng gồm:

- Movement code.
- SKU.
- Warehouse/bin.
- Type.
- Quantity.
- Before/after balance nếu có quyền.
- Related entity.
- Actor.
- Timestamp.
- Status.

Movement types:

```text
inbound
outbound
transfer_in
transfer_out
adjustment
return
reservation
release
```

---

## 25. Stock adjustment workflow

Điều chỉnh tồn là action nhạy cảm.

Yêu cầu:

- Permission riêng.
- Reason code bắt buộc.
- Note tùy chọn hoặc bắt buộc theo mức chênh lệch.
- Confirmation.
- Version/concurrency check.
- Audit log.
- Có thể yêu cầu approval với giá trị lớn.

Không cho nhập adjustment từ Dashboard nếu hệ thống chưa có workflow đầy đủ.

---

## 26. Cycle count

Dashboard có thể hiển thị:

- Cycle count due.
- Count in progress.
- Discrepancy awaiting review.
- Overdue counts.

Mỗi task:

- Warehouse/zone.
- SKU count.
- Due date.
- Assigned staff.
- Status.
- Discrepancy summary.

---

## 27. Capacity overview

Nếu có dữ liệu vị trí kho:

- Used capacity.
- Available capacity.
- Blocked locations.
- Full zones.
- Receiving congestion.

Hiển thị bằng progress bar hoặc bar chart.

Không dùng gauge trang trí khó đọc.

---

## 28. Bin and zone status

Có thể hiển thị summary:

```text
Available
Occupied
Blocked
Maintenance
Unknown
```

Không cần hiển thị bản đồ kho phức tạp trong Dashboard tổng hợp; link sang module chi tiết.

---

## 29. Sync status

Widget theo dõi tích hợp:

- Last successful sync.
- Failed sync count.
- Pending events.
- Source system status.
- Stale data age.

Actions:

- Retry sync nếu idempotent.
- Mở integration log.
- Xem affected SKUs.

Không cho retry hàng loạt vô hạn.

---

## 30. Barcode/QR integration

Nếu hỗ trợ quét mã:

- Nút scan rõ ràng trên tablet/mobile.
- Camera permission chỉ yêu cầu khi người dùng bắt đầu scan.
- Có fallback nhập mã thủ công.
- Hiển thị mã vừa quét và entity match.
- Xử lý duplicate scan.
- Không tự hoàn tất task chỉ vì quét một mã nếu còn bước xác nhận.

---

## 31. Product search integration

Warehouse Search ưu tiên:

1. SKU exact match.
2. Barcode.
3. Product name.
4. Bin/location.
5. Inbound/outbound task.

Result cần hiển thị:

- SKU.
- Product name.
- Available quantity.
- Warehouse/bin.
- Data freshness.

Search phải tuân warehouse scope.

---

## 32. Notification integration

Warehouse Notification categories:

- Negative stock.
- Out of stock.
- Low stock.
- Order ready to pick.
- Task overdue.
- Sync failed.
- Transfer delayed.
- Count discrepancy.

Không toast mọi task mới; ưu tiên Notification Center và Priority Alerts.

---

## 33. Warehouse task contract

```ts
export type WarehouseTask = {
  id: string;
  type: "inbound" | "putaway" | "pick" | "pack" | "handover" | "transfer" | "cycle_count";
  code: string;
  warehouseId: string;
  priority: "critical" | "high" | "medium" | "low";
  status: "pending" | "assigned" | "in_progress" | "blocked" | "completed" | "cancelled";
  assignedUserId?: string;
  dueAt?: string;
  startedAt?: string;
  completedAt?: string;
  itemLines: number;
  unitCount: number;
  overdue: boolean;
  availableActions: string[];
};
```

---

## 34. Inventory alert contract

```ts
export type WarehouseInventoryAlert = {
  id: string;
  type: "negative_stock" | "out_of_stock" | "low_stock" | "overstock" | "slow_moving" | "stale_data" | "sync_error";
  severity: "critical" | "high" | "medium" | "low";
  warehouseId: string;
  productId: string;
  sku: string;
  productName: string;
  onHandQuantity: number | null;
  reservedQuantity: number | null;
  availableQuantity: number | null;
  incomingQuantity?: number | null;
  unit: string;
  threshold?: number | null;
  lastMovementAt?: string;
  updatedAt: string;
  availableActions: string[];
};
```

---

## 35. Warehouse dashboard contract

```ts
export type WarehouseDashboardData = {
  scope: DashboardScope;
  health: WarehouseHealthSummary;
  kpis: DashboardKPI[];
  queues: WarehouseQueueSummary[];
  alerts: WarehouseInventoryAlert[];
  tasks: WarehouseTask[];
  performance?: WarehousePerformanceSummary;
  recentMovements: StockMovementRow[];
  syncStatus: WarehouseSyncStatus;
  updatedAt: string;
};
```

---

## 36. Permissions

Đề xuất:

```text
warehouse.dashboard.view
inventory.view
inventory.view_all_warehouses
inventory.adjust
inventory.count
inventory.transfer
warehouse.tasks.view
warehouse.tasks.assign
warehouse.tasks.update
warehouse.performance.view
warehouse.sync.view
warehouse.sync.retry
reports.warehouse.export
```

Không dựa duy nhất vào role name.

---

## 37. Scope behavior

### Warehouse Manager

Có thể có:

```text
single warehouse
multiple warehouses
branch warehouses
```

### Warehouse Staff

Mặc định:

```text
assigned warehouse
assigned tasks
```

Filter không được mở rộng ngoài backend scope.

---

## 38. Loading states

- KPI skeleton.
- Queue skeleton.
- Alert list skeleton.
- Task rows skeleton.
- Sync status skeleton.

Không hiển thị tồn `0` khi dữ liệu chưa tải.

---

## 39. Empty states

### Không có task

```text
Không có tác vụ kho nào đang chờ xử lý.
```

### Không có alert

```text
Không phát hiện cảnh báo tồn kho trong phạm vi đã chọn.
```

### Không có movement

```text
Chưa có biến động kho trong khoảng thời gian này.
```

### Không có cycle count

```text
Không có đợt kiểm kê nào đến hạn.
```

---

## 40. Error states

- Widget lỗi độc lập.
- Sync widget lỗi không làm mất Pick Queue.
- Inventory API lỗi phải hiển thị `Chưa thể xác định tồn kho`.
- Có retry và request ID.
- Không thay dữ liệu lỗi bằng `0` hoặc trạng thái `Còn hàng`.

---

## 41. Stale data

Ngưỡng tham khảo:

| Dataset | Stale threshold |
|---|---:|
| Warehouse tasks | 1–3 phút |
| Orders ready to ship | 2–5 phút |
| Inventory availability | 5–15 phút |
| Warehouse KPI | 10–30 phút |
| Performance summary | 30–60 phút |

Dữ liệu tồn stale phải có cảnh báo rõ và có thể hạn chế action phụ thuộc tồn chính xác.

---

## 42. Real-time updates

Events đề xuất:

```text
warehouse.task.created
warehouse.task.updated
warehouse.inventory.changed
warehouse.alert.created
warehouse.sync.failed
warehouse.transfer.updated
```

Frontend phải:

- Deduplicate event.
- Update cache có chọn lọc.
- Không tự mở drawer.
- Không tự cuộn.
- Không phát toast hàng loạt.

---

## 43. Responsive behavior

### ≥1440px

- KPI 4–6 cột.
- Trend/Risk 7/5.
- Queue/Alerts 8/4.

### 1024–1439px

- KPI 2–3 cột.
- Widgets xếp dọc khi cần.
- Tables scroll trong wrapper.

### 768–1023px

- Sidebar drawer.
- KPI 2 cột.
- Queue một cột.
- Touch targets 44px.

### <768px

Warehouse Staff mobile fallback ưu tiên:

1. My tasks.
2. Pick & pack queue.
3. Inventory alerts.
4. Scan action.
5. Recent actions.

---

## 44. Accessibility

- KPI có accessible summary.
- Queue dùng semantic table/list.
- Priority không chỉ dùng màu.
- Progress có text value.
- Scan action có label rõ.
- Drawer và dialog quản lý focus.
- Touch target tối thiểu 44px.
- Zoom 200% không mất task action.
- Live update dùng `aria-live=polite` phù hợp.

---

## 45. Security và privacy

- Backend enforce warehouse scope.
- Không trả dữ liệu nhiều kho rồi lọc client.
- Không hiển thị giá vốn hoặc giá mua thiếu quyền.
- Stock adjustment có audit log.
- Signed export URL có thời hạn.
- Cache key gồm user, warehouse, permissions và filters.
- Không lưu dữ liệu inventory chi tiết lâu dài trong browser storage.

---

## 46. Audit requirements

Bắt buộc audit:

```text
warehouse.task.started
warehouse.task.completed
inventory.adjusted
inventory.count_submitted
inventory.transfer_approved
warehouse.sync_retried
```

Audit gồm actor, warehouse, entity, before/after nếu phù hợp, reason, result, request ID và timestamp.

---

## 47. Analytics events

```text
warehouse.dashboard.viewed
warehouse.filter.applied
warehouse.task.opened
warehouse.task.started
warehouse.task.completed
warehouse.alert.opened
warehouse.scan.started
warehouse.scan.completed
warehouse.report.export_requested
```

Không gửi SKU name, stock quantity chi tiết hoặc thông tin nhà cung cấp vào analytics nếu không cần.

---

## 48. Observability

Theo dõi:

- Warehouse Dashboard load success.
- Task API latency.
- Inventory freshness.
- Queue backlog.
- Task mutation failure.
- Sync failure.
- Duplicate realtime events.
- Stock adjustment failure.
- Permission denied anomalies.

---

## 49. Testing

### Unit

- Available quantity formatting.
- SLA overdue calculation.
- Inventory severity mapping.
- Queue sorting.
- Data freshness.
- Scope serialization.

### Component

- KPI states.
- Pick & Pack Queue.
- Inventory Alerts.
- Warehouse Health Summary.
- Sync Status.
- Adjustment confirmation.

### Integration

- Staff chỉ thấy assigned warehouse/tasks.
- Manager đổi warehouse đúng scope.
- Realtime update queue.
- Stale inventory state.
- Adjustment audit flow.
- Export đúng warehouse.

### E2E

- Warehouse Manager mở Dashboard.
- Chọn kho.
- Mở tồn âm.
- Retry sync.
- Assign task.
- Warehouse Staff bắt đầu và hoàn tất pick task.
- Scan SKU.
- Báo thiếu hàng.

---

## 50. Visual QA

Baseline:

```text
Warehouse Manager — 1440 × 900
Warehouse Staff — 1440 × 900
Warehouse Manager — 1024 × 768
Warehouse Staff Mobile Fallback
Negative Stock Alert
No Tasks
Sync Error
Stale Inventory
Large Queue
Long Product Names
Scan Sheet
```

Kiểm tra:

- Queue hierarchy.
- Alert severity.
- Table overflow.
- Touch targets.
- Long SKU/product names.
- Stale warning.
- Focus states.

---

## 51. Performance budgets

Mục tiêu tham khảo:

```text
Dashboard LCP p75 < 2,5s
Warehouse overview API p95 < 1,5s
Task mutation feedback < 1s trong điều kiện bình thường
Search/scan match < 1s
Filter response UI < 200ms
```

Virtualize danh sách dài và giới hạn queue trên Dashboard.

---

## 52. AI coding agent requirements

AI coding agent phải:

- Phân biệt Warehouse Manager và Warehouse Staff scope.
- Không tự tính inventory availability nếu backend đã có giá trị chuẩn.
- Không cho stock adjustment thiếu reason, confirmation và audit.
- Không hiển thị dữ liệu kho ngoài scope.
- Không thay dữ liệu lỗi bằng số 0 hoặc `Còn hàng`.
- Không tạo SLA hoặc slow-moving rule giả.
- Bổ sung test cho stale inventory, negative stock, realtime deduplication và task conflict.
- Báo rõ API, permissions, scope và phần backend còn thiếu.

---

## 53. Acceptance checklist

- [ ] Có biến thể Warehouse Manager và Warehouse Staff.
- [ ] Toolbar đúng theo quyền và scope.
- [ ] KPI kho rõ ràng.
- [ ] Có Warehouse Health Summary.
- [ ] Có Inventory Risk Summary.
- [ ] Có Operational Queues.
- [ ] Có Pick & Pack, Inbound và Transfer Queue.
- [ ] Có Priority Alerts.
- [ ] Có Warehouse/Staff Performance theo quyền.
- [ ] Có Recent Stock Movements.
- [ ] Stock adjustment có workflow an toàn.
- [ ] Có Cycle Count và Sync Status.
- [ ] Search/scan integration đầy đủ.
- [ ] Permissions và warehouse/task scope được backend enforce.
- [ ] Loading, empty, error và stale states đầy đủ.
- [ ] Responsive và accessibility hoàn chỉnh.
- [ ] Analytics, observability, security và audit được xác định.
- [ ] Unit, component, integration, E2E và Visual QA đầy đủ.

---

## 54. Những điều không được làm

- Không hiển thị nhiều kho cho user thiếu quyền.
- Không dùng số 0 khi inventory API lỗi.
- Không cho điều chỉnh tồn thiếu audit và reason.
- Không retry sync vô hạn.
- Không toast mọi task mới.
- Không tạo SLA, reorder point hoặc slow-moving rule không có nguồn.
- Không hiển thị giá vốn hoặc giá mua thiếu quyền.
- Không tự hoàn tất task chỉ vì quét một mã.
- Không dùng real-time event chưa validate.
- Không thay WMS bằng Dashboard tổng hợp.

---

## 55. Kết luận

Warehouse Dashboard của Cynca VLXD phải giúp quản lý nhìn thấy tình trạng kho và giúp nhân viên biết ngay tác vụ cần làm tiếp theo. Giao diện cần ưu tiên queue, cảnh báo, độ mới dữ liệu và hành động an toàn, đồng thời giữ chặt permission, warehouse scope, audit và tính chính xác của số tồn.

File tiếp theo đề xuất:

```text
README.md
35-Finance-Dashboard.md
```