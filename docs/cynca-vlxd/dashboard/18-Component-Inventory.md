# Cynca VLXD Admin Dashboard — Component Inventory

> Phiên bản: 1.0  
> Phạm vi: Danh mục component cần có cho Dashboard quản trị  
> Đối tượng sử dụng: Designer, Frontend Developer, QA, AI coding agent  
> Tài liệu liên quan: toàn bộ thư mục `docs/cynca-vlxd/dashboard/`

---

## 1. Mục tiêu

Tài liệu này liệt kê toàn bộ component cần thiết để triển khai Dashboard Cynca VLXD theo hướng tái sử dụng, có trạng thái rõ ràng và dễ kiểm thử.

Mỗi component phải xác định:

- Vai trò.
- Props chính.
- Variants.
- Trạng thái.
- Responsive behavior.
- Accessibility contract.
- Mức độ ưu tiên triển khai.

Không tạo component chỉ để bọc một thẻ HTML nếu không có giá trị tái sử dụng hoặc logic riêng.

---

## 2. Phân loại component

Hệ thống chia thành 6 nhóm:

1. Foundation.
2. UI primitives.
3. Navigation.
4. Dashboard data display.
5. Operational components.
6. Feedback và overlay.

---

## 3. Priority levels

| Mức | Ý nghĩa |
|---|---|
| P0 | Bắt buộc để Dashboard hoạt động |
| P1 | Bắt buộc trước production |
| P2 | Cải thiện trải nghiệm, có thể làm sau |
| P3 | Tùy chọn hoặc mở rộng tương lai |

---

## 4. Foundation components

### 4.1 `DashboardShell` — P0

**Vai trò**

Bao toàn bộ Sidebar, Header và Main Content.

**Props chính**

```ts
export type DashboardShellProps = {
  children: React.ReactNode;
  sidebar: React.ReactNode;
  header: React.ReactNode;
  sidebarCollapsed?: boolean;
};
```

**Trạng thái**

- Sidebar expanded.
- Sidebar collapsed.
- Sidebar drawer.

**Yêu cầu**

- Một `main` landmark.
- Không horizontal scroll toàn trang.
- Giữ shell ổn định khi route loading.

---

### 4.2 `DashboardContent` — P0

**Vai trò**

Container nội dung chính, giới hạn tối đa 1600px.

**Props**

```ts
export type DashboardContentProps = {
  children: React.ReactNode;
  className?: string;
};
```

**Yêu cầu**

- Responsive padding.
- `min-width: 0`.
- Hỗ trợ section spacing.

---

### 4.3 `DashboardGrid` — P0

**Vai trò**

Grid 12 cột cho card và widget.

**Props**

```ts
export type DashboardGridProps = {
  children: React.ReactNode;
  gap?: "default" | "compact";
  className?: string;
};
```

---

### 4.4 `PageToolbar` — P0

**Vai trò**

Hiển thị page title, description, filter và actions.

**Variants**

- Default.
- Compact.
- Wrapped.

**Trạng thái**

- Normal.
- Loading filter.
- Permission-limited action.

---

## 5. UI primitives

### 5.1 `Button` — P0

**Variants**

- Primary.
- Secondary.
- Ghost.
- Danger.
- Link.

**Sizes**

- Small: 36px.
- Medium: 40px.
- Large: 44px.

**States**

- Default.
- Hover.
- Focus.
- Active.
- Disabled.
- Loading.

**Props tham khảo**

```ts
export type ButtonProps = {
  variant?: "primary" | "secondary" | "ghost" | "danger" | "link";
  size?: "sm" | "md" | "lg";
  loading?: boolean;
  leadingIcon?: React.ReactNode;
  trailingIcon?: React.ReactNode;
};
```

---

### 5.2 `IconButton` — P0

**Yêu cầu**

- `aria-label` bắt buộc.
- Tooltip khi icon không tự giải thích rõ.
- Touch target 44px trên thiết bị touch.

**Variants**

- Default.
- Ghost.
- Danger.

---

### 5.3 `Badge` — P0

**Variants semantic**

- Neutral.
- Info.
- Success.
- Warning.
- Danger.
- Violet.

**Sizes**

- Small.
- Medium.

**Use cases**

- Order status.
- Payment status.
- Inventory status.
- Trend.
- Count.

---

### 5.4 `Card` — P0

**Variants**

- Default.
- Interactive.
- Warning.
- Error.
- Elevated.

**Subcomponents**

```text
Card
├── CardHeader
├── CardTitle
├── CardDescription
├── CardContent
└── CardFooter
```

---

### 5.5 `Input` — P0

**Variants**

- Text.
- Search.
- Number.
- Password.

**States**

- Default.
- Focus.
- Disabled.
- Readonly.
- Error.
- Success.

---

### 5.6 `Select` — P0

**Use cases**

- Branch.
- Warehouse.
- Date preset.
- Chart granularity.
- Status filter.

**Yêu cầu**

- Keyboard navigation.
- Searchable khi danh sách dài.
- Loading, empty và error state.

---

### 5.7 `Checkbox` — P1

Dùng cho settings hoặc bulk action ở trang đầy đủ.

Dashboard overview không bắt buộc bulk selection.

---

### 5.8 `RadioGroup` — P1

Dùng cho report format, compare mode hoặc lựa chọn loại dữ liệu.

---

### 5.9 `Switch` — P1

Dùng cho notification preference và settings dạng bật/tắt.

Không dùng switch cho hành động cần submit tức thời nhưng không rõ hậu quả.

---

### 5.10 `Tabs` — P0

**Use cases**

- Notification filters.
- Order status filters.
- Data views.

**Yêu cầu**

- Arrow key navigation.
- `aria-selected`.
- Không dùng quá nhiều tab trên card nhỏ.

---

### 5.11 `Tooltip` — P0

**Use cases**

- Sidebar collapsed.
- Icon-only action.
- KPI formula.
- Truncated value.

**Yêu cầu**

- Hover và focus.
- Không chứa action phức tạp.

---

### 5.12 `Avatar` — P0

**Variants**

- Image.
- Initials.
- Fallback icon.

**Sizes**

- 32px.
- 36px.
- 40px.

---

### 5.13 `Separator` — P1

Dùng trong sidebar, dropdown, menu và card.

Không lạm dụng separator khi spacing đã đủ để phân nhóm.

---

### 5.14 `Skeleton` — P0

**Variants**

- Text line.
- Circle.
- Rectangle.
- Table row.
- Chart placeholder.

**Accessibility**

- `aria-hidden="true"`.
- Container dùng `aria-busy`.

---

### 5.15 `Spinner` — P0

Dùng cho button loading hoặc tác vụ nhỏ.

Không dùng spinner thay skeleton cho cả Dashboard.

---

### 5.16 `Progress` — P1

**Use cases**

- Data quality.
- Inventory threshold.
- Export job.
- Background task.

**Accessibility**

- `aria-valuenow`.
- `aria-valuemin`.
- `aria-valuemax`.

---

## 6. Navigation components

### 6.1 `Sidebar` — P0

**Subcomponents**

```text
Sidebar
├── SidebarBrand
├── BranchSelector
├── SidebarGroup
├── SidebarItem
├── SidebarSubmenu
├── SidebarSupport
└── SidebarUser
```

**States**

- Expanded.
- Collapsed.
- Drawer.

---

### 6.2 `SidebarItem` — P0

**Props**

```ts
export type SidebarItemProps = {
  label: string;
  href?: string;
  icon: React.ComponentType;
  active?: boolean;
  badge?: number | string;
  disabled?: boolean;
  children?: React.ReactNode;
};
```

**States**

- Default.
- Hover.
- Focus.
- Active.
- Disabled.
- Submenu expanded.

---

### 6.3 `Breadcrumbs` — P1

**Yêu cầu**

- Tối đa 3 cấp.
- Trang hiện tại dùng `aria-current="page"`.
- Rút gọn ở viewport nhỏ.

---

### 6.4 `DashboardHeader` — P0

**Subcomponents**

```text
DashboardHeader
├── SidebarToggle
├── Breadcrumbs
├── GlobalSearch
├── DateRangeSelector
├── BranchFilter
├── ExportMenu
├── NotificationButton
└── UserMenu
```

---

### 6.5 `GlobalSearch` — P0

**States**

- Idle.
- Focused.
- Loading.
- Results.
- Empty.
- Error.

**Result types**

- Order.
- Product.
- Customer.
- Supplier.

---

### 6.6 `DateRangeSelector` — P0

**Presets**

- Today.
- 7 days.
- 30 days.
- This month.
- This quarter.
- This year.
- Custom.

---

### 6.7 `BranchFilter` — P0

Ẩn khi user chỉ có một branch hoặc không có quyền đổi branch.

---

### 6.8 `UserMenu` — P0

**Items**

- Profile.
- Change password.
- Appearance.
- Help.
- Logout.

---

## 7. Dashboard data display components

### 7.1 `KPIGrid` — P0

Quản lý layout 4, 2 hoặc 1 cột.

---

### 7.2 `KPICard` — P0

**Subcomponents**

```text
KPICard
├── KPILabel
├── KPIInfoTooltip
├── KPIIcon
├── KPIValue
├── TrendBadge
├── ComparisonLabel
└── Sparkline
```

**States**

- Success data.
- Loading.
- Empty.
- Error.
- Stale.
- Permission hidden.

---

### 7.3 `TrendBadge` — P0

**Variants**

- Positive.
- Negative.
- Neutral.

Semantic phải dựa trên business meaning, không chỉ dấu cộng/trừ.

---

### 7.4 `ChartCard` — P0

**Subcomponents**

```text
ChartCard
├── ChartCardHeader
├── ChartControls
├── ChartSummary
├── ChartViewport
├── ChartLegend
└── ChartFooter
```

---

### 7.5 `RevenueChart` — P0

**Variants**

- Single series.
- Compare series.

**States**

- Loading.
- Empty.
- Error.
- Stale.

---

### 7.6 `OrderStatusChart` — P0

Donut chart với center label và legend.

---

### 7.7 `ChartTooltip` — P0

Format:

- Date.
- Series label.
- Full value.
- Comparison.

---

### 7.8 `ChartLegend` — P0

Không hiển thị khi chỉ có một series và legend không tạo thêm giá trị.

---

### 7.9 `EmptyState` — P0

**Variants**

- No data.
- No filter result.
- Positive empty state.
- Permission denied.
- Setup required.

**Props**

```ts
export type EmptyStateProps = {
  icon?: React.ReactNode;
  title: string;
  description?: string;
  action?: React.ReactNode;
};
```

---

### 7.10 `ErrorState` — P0

**Variants**

- Widget error.
- Page error.
- Network error.
- Permission error.

**Yêu cầu**

- Có retry khi phù hợp.
- Không hiển thị stack trace.

---

### 7.11 `StaleDataNotice` — P1

Hiển thị `updatedAt`, lý do stale và refresh action khi phù hợp.

---

## 8. Product components

### 8.1 `ProductPerformanceWidget` — P0

**Subcomponents**

```text
ProductPerformanceWidget
├── WidgetHeader
├── MetricSelector
├── ProductPerformanceList
└── ViewAllLink
```

---

### 8.2 `ProductPerformanceRow` — P0

**Nội dung**

- Rank.
- Thumbnail.
- Product identity.
- Quantity sold.
- Revenue.
- Optional trend.

---

### 8.3 `ProductThumbnail` — P0

**States**

- Loaded.
- Loading.
- Broken image fallback.
- Missing image.

---

### 8.4 `InventoryAlertWidget` — P0

Hiển thị các SKU cần hành động.

---

### 8.5 `InventoryAlertRow` — P0

**Variants**

- Negative stock.
- Out of stock.
- Low stock.
- Slow moving.

---

### 8.6 `InventoryStatusBadge` — P0

Mapping tập trung, không viết lại màu trong row.

---

### 8.7 `CategoryPerformanceGrid` — P1

Grid danh mục 2 cột trên desktop, 1 cột khi hẹp.

---

### 8.8 `CategoryPerformanceCard` — P1

**Nội dung**

- Icon hoặc image.
- Category name.
- Product count.
- Revenue.

---

### 8.9 `ProductDataQualityWidget` — P1

Hiển thị completion percentage và issue list.

---

## 9. Orders components

### 9.1 `OrdersTableCard` — P0

**Subcomponents**

```text
OrdersTableCard
├── OrdersTableHeader
├── StatusFilter
├── OrdersTable
└── ViewAllLink
```

---

### 9.2 `OrdersTable` — P0

Dùng semantic `<table>`.

**States**

- Loading.
- Populated.
- Empty.
- Filter empty.
- Error.

---

### 9.3 `OrderRow` — P0

**Cells**

- Order code.
- Customer.
- Payment.
- Order status.
- Total.
- Time.
- Actions.

---

### 9.4 `OrderCodeCell` — P0

Dùng mono font và link semantic.

---

### 9.5 `CustomerCell` — P0

Tên khách hàng và metadata phụ.

---

### 9.6 `PaymentStatusBadge` — P0

**Variants**

- Unpaid.
- Partial.
- Paid.
- Partially refunded.
- Refunded.
- Failed.

---

### 9.7 `OrderStatusBadge` — P0

**Variants**

- Pending.
- Confirmed.
- Processing.
- Ready to ship.
- Shipping.
- Completed.
- Cancelled.
- Returned.

---

### 9.8 `OrderRowActions` — P0

Hiển thị action theo `availableActions` từ backend.

Không tự suy toàn bộ workflow ở frontend.

---

### 9.9 `OrderQuickViewDrawer` — P1

**Subcomponents**

```text
OrderQuickViewDrawer
├── OrderSummary
├── CustomerSummary
├── OrderItemList
├── PaymentSummary
├── OrderTimeline
└── OrderActionFooter
```

---

### 9.10 `OrderTimeline` — P1

Hiển thị status, timestamp, actor và note.

---

## 10. Notification components

### 10.1 `NotificationButton` — P0

Icon bell và unread badge.

---

### 10.2 `NotificationDropdown` — P0

**Subcomponents**

```text
NotificationDropdown
├── NotificationHeader
├── NotificationTabs
├── NotificationList
└── NotificationFooter
```

---

### 10.3 `NotificationItem` — P0

**States**

- Read.
- Unread.
- Resolved.
- Dismissed.

**Variants**

- Order.
- Inventory.
- Payment.
- Product.
- Customer.
- System.

---

### 10.4 `NotificationWidget` — P1

Chỉ hiển thị notification có giá trị hành động.

---

### 10.5 `UnreadBadge` — P0

Giới hạn `99+`.

---

## 11. Feedback và overlay components

### 11.1 `DropdownMenu` — P0

Dùng cho export, row action, user menu và item menu.

**Yêu cầu**

- Keyboard navigation.
- Focus return.
- Không vượt viewport.

---

### 11.2 `Popover` — P0

Dùng cho date picker, filter hoặc tooltip tương tác nhẹ.

---

### 11.3 `Modal` — P0

**Sizes**

- Small 480px.
- Medium 640px.
- Large 880px.

**Yêu cầu**

- Focus trap.
- Escape close khi phù hợp.
- Focus return.
- Scroll body hợp lý.

---

### 11.4 `Drawer` — P0

**Variants**

- Right detail drawer.
- Left navigation drawer.

---

### 11.5 `Toast` — P0

**Variants**

- Success.
- Info.
- Warning.
- Error.

**Yêu cầu**

- `aria-live` phù hợp.
- Pause timeout khi hover hoặc focus.

---

### 11.6 `ConfirmDialog` — P0

Dùng cho:

- Cancel order.
- Refund.
- Delete hoặc destructive mutation.

Không dùng cho action an toàn như mark notification read.

---

### 11.7 `CommandPalette` — P2

Có thể dùng cho global search nâng cao và quick navigation.

Shortcut:

```text
Ctrl + K / Cmd + K
```

---

## 12. Report và background task components

### 12.1 `ExportMenu` — P0

Formats:

- CSV.
- XLSX.
- PDF.

---

### 12.2 `ExportProgressToast` — P1

Hiển thị job queued, processing, completed hoặc failed.

---

### 12.3 `BackgroundJobIndicator` — P2

Dùng khi có nhiều tác vụ nền như import, export hoặc sync.

---

## 13. Permission components

### 13.1 `PermissionGate` — P0

```ts
export type PermissionGateProps = {
  permission: string | string[];
  mode?: "all" | "any";
  fallback?: React.ReactNode;
  children: React.ReactNode;
};
```

Dùng để kiểm soát hiển thị, không thay thế authorization backend.

---

### 13.2 `PermissionDeniedState` — P1

Giải thích rõ người dùng không có quyền và đường dẫn liên hệ quản trị nếu phù hợp.

---

## 14. Data state wrappers

### 14.1 `AsyncState` — P1

Component hoặc pattern thống nhất cho:

- Loading.
- Error.
- Empty.
- Success.

Không nên tạo wrapper quá trừu tượng làm khó kiểm soát layout từng widget.

---

### 14.2 `WidgetErrorBoundary` — P0

Mỗi widget quan trọng có khả năng lỗi độc lập.

Error boundary không thay thế API error handling.

---

### 14.3 `RefreshButton` — P1

Hiển thị last updated và trạng thái refreshing.

---

## 15. Formatting utilities không phải visual component

Các helper bắt buộc:

```text
formatCurrency
formatCompactCurrency
formatNumber
formatPercent
formatDate
formatDateTime
formatRelativeTime
formatQuantity
```

Các helper phải được kiểm thử unit test.

---

## 16. Status mapping modules

Nên có các module tập trung:

```text
order-status.ts
payment-status.ts
inventory-status.ts
notification-category.ts
```

Mỗi mapping gồm:

- Label.
- Badge variant.
- Icon.
- Semantic meaning.
- Allowed action nếu phù hợp.

---

## 17. Suggested file structure

```text
src/components/
├── ui/
│   ├── avatar.tsx
│   ├── badge.tsx
│   ├── button.tsx
│   ├── card.tsx
│   ├── dialog.tsx
│   ├── drawer.tsx
│   ├── dropdown-menu.tsx
│   ├── empty-state.tsx
│   ├── error-state.tsx
│   ├── input.tsx
│   ├── progress.tsx
│   ├── select.tsx
│   ├── skeleton.tsx
│   ├── tabs.tsx
│   ├── toast.tsx
│   └── tooltip.tsx
├── dashboard/
│   ├── dashboard-shell.tsx
│   ├── dashboard-header.tsx
│   ├── sidebar.tsx
│   ├── page-toolbar.tsx
│   ├── kpi-card.tsx
│   ├── revenue-chart.tsx
│   ├── order-status-chart.tsx
│   ├── product-performance-widget.tsx
│   ├── inventory-alert-widget.tsx
│   ├── orders-table.tsx
│   ├── notification-dropdown.tsx
│   └── notification-widget.tsx
└── permissions/
    └── permission-gate.tsx
```

---

## 18. Storybook inventory

Mỗi component P0 và P1 nên có story cho:

- Default.
- Hover hoặc interactive state khi mô phỏng được.
- Loading.
- Empty.
- Error.
- Disabled.
- Long content.
- Responsive narrow width.
- High contrast hoặc dark mode nếu hỗ trợ.

Ví dụ `KPICard` stories:

```text
DefaultCurrency
PositiveTrend
NegativeBusinessTrend
Loading
Empty
Error
Stale
LongValue
PermissionHidden
```

---

## 19. Test coverage priority

### Unit test

- Formatters.
- Status mappings.
- Permission logic.
- Variant helpers.

### Component test

- Button loading.
- Sidebar collapse.
- Select keyboard interaction.
- KPI states.
- Orders table sorting.
- Notification read state.
- Modal focus trap.

### Visual regression

- DashboardShell.
- Sidebar.
- Header.
- KPICard.
- Charts.
- OrdersTable.
- NotificationDropdown.
- Loading và error states.

---

## 20. Component API rules

- Props phải có tên semantic.
- Không truyền raw class names để điều khiển business state khi có thể dùng variant.
- Không tạo boolean props mâu thuẫn như `success`, `error`, `warning` cùng lúc.
- Dùng discriminated union cho variant phức tạp.
- Không expose internal DOM không cần thiết.
- Hỗ trợ `className` cho layout override có kiểm soát.
- Forward ref cho input, button và overlay trigger khi cần.

---

## 21. Accessibility contract bắt buộc

Mỗi component tương tác phải xác định:

- Semantic element.
- Accessible name.
- Keyboard interaction.
- Focus behavior.
- ARIA states.
- Reduced motion behavior.
- Touch target.

Component không được đánh dấu hoàn thành nếu chưa có accessibility contract.

---

## 22. Component status matrix

| Component | Priority | Design | Code | Test | Storybook |
|---|---|---|---|---|---|
| DashboardShell | P0 | Có | Chưa | Chưa | Chưa |
| Sidebar | P0 | Có | Chưa | Chưa | Chưa |
| DashboardHeader | P0 | Có | Chưa | Chưa | Chưa |
| Button | P0 | Có | Chưa | Chưa | Chưa |
| Badge | P0 | Có | Chưa | Chưa | Chưa |
| Card | P0 | Có | Chưa | Chưa | Chưa |
| Input | P0 | Có | Chưa | Chưa | Chưa |
| Select | P0 | Có | Chưa | Chưa | Chưa |
| KPICard | P0 | Có | Chưa | Chưa | Chưa |
| RevenueChart | P0 | Có | Chưa | Chưa | Chưa |
| OrderStatusChart | P0 | Có | Chưa | Chưa | Chưa |
| ProductPerformanceWidget | P0 | Có | Chưa | Chưa | Chưa |
| InventoryAlertWidget | P0 | Có | Chưa | Chưa | Chưa |
| OrdersTable | P0 | Có | Chưa | Chưa | Chưa |
| NotificationDropdown | P0 | Có | Chưa | Chưa | Chưa |
| Toast | P0 | Có | Chưa | Chưa | Chưa |
| OrderQuickViewDrawer | P1 | Có | Chưa | Chưa | Chưa |
| ProductDataQualityWidget | P1 | Có | Chưa | Chưa | Chưa |
| CommandPalette | P2 | Đề xuất | Chưa | Chưa | Chưa |

Bảng này cần được cập nhật khi bắt đầu triển khai mã nguồn.

---

## 23. Acceptance checklist

- [ ] Mỗi component có vai trò rõ ràng.
- [ ] Component P0 đủ để dựng Dashboard hoàn chỉnh.
- [ ] Variants dùng semantic names.
- [ ] Loading, empty, error và disabled state được xác định.
- [ ] Navigation component có keyboard contract.
- [ ] Overlay component có focus management.
- [ ] Status badge dùng mapping tập trung.
- [ ] Product thumbnail có fallback.
- [ ] Order actions dựa trên permission và available actions.
- [ ] Notification read và resolved state tách biệt.
- [ ] Component file structure không bị phân mảnh quá mức.
- [ ] Component P0 và P1 có test plan.
- [ ] Component quan trọng có visual regression case.
- [ ] Accessibility contract được ghi nhận.
- [ ] Không có component trùng chức năng.

---

## 24. Những điều không được làm

- Không tạo nhiều Button component cho từng module.
- Không tạo nhiều Badge mapping rời rạc.
- Không hardcode status color trong table row.
- Không dùng div click thay button hoặc link.
- Không tạo component quá tổng quát đến mức khó dùng.
- Không tạo component wrapper không có giá trị.
- Không đưa business data trực tiếp vào UI primitive.
- Không để component tự gọi API nếu kiến trúc yêu cầu data layer riêng.
- Không bỏ qua long-content story.
- Không coi component hoàn thành khi chưa có loading và error behavior.

---

## 25. Kết luận

Component Inventory là danh sách chuẩn để đội phát triển xây dựng Dashboard Cynca VLXD theo cùng một hệ thống. Việc hoàn thiện component P0 trước giúp giảm trùng lặp, bảo đảm trạng thái nhất quán và tạo nền tảng để mở rộng các module quản trị khác.

File tiếp theo đề xuất:

```text
README.md
19-Storybook-Cases.md
```