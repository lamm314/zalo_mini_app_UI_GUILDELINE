# Cynca VLXD Admin Dashboard — Storybook Cases

> Phiên bản: 1.0  
> Phạm vi: Danh sách Storybook stories cho component Dashboard  
> Đối tượng sử dụng: Frontend Developer, Designer, QA, AI coding agent  
> Tài liệu liên quan: `18-Component-Inventory.md`, `17-Visual-QA-Cases.md`

---

## 1. Mục tiêu

Storybook được dùng để:

- Phát triển component độc lập với backend.
- Kiểm tra toàn bộ trạng thái giao diện.
- Làm tài liệu sống cho Design System.
- Chạy visual regression.
- Kiểm tra accessibility ở cấp component.
- Giảm tình trạng component chỉ hoạt động với dữ liệu mặc định.

Mỗi component P0 và P1 phải có story đầy đủ trước khi đánh dấu hoàn thành.

---

## 2. Cấu hình Storybook đề xuất

```text
Storybook 8+
React
TypeScript
Vite hoặc Next.js framework adapter
```

Addons đề xuất:

- `@storybook/addon-essentials`
- `@storybook/addon-a11y`
- `@storybook/addon-interactions`
- `@storybook/addon-viewport`
- Visual regression service hoặc Playwright screenshot

Không thêm addon trùng chức năng nếu hệ thống hiện tại đã có công cụ tương đương.

---

## 3. Cấu trúc file stories

```text
src/components/
├── ui/
│   ├── button.tsx
│   ├── button.stories.tsx
│   ├── badge.tsx
│   └── badge.stories.tsx
└── dashboard/
    ├── kpi-card.tsx
    ├── kpi-card.stories.tsx
    ├── orders-table.tsx
    └── orders-table.stories.tsx
```

Tên title đề xuất:

```text
UI/Button
UI/Badge
Dashboard/KPI Card
Dashboard/Orders Table
Navigation/Sidebar
Feedback/Notification Dropdown
```

---

## 4. Global decorators

Storybook phải cung cấp:

- Font Inter hoặc system fallback.
- Tailwind global styles.
- Design tokens.
- Toast provider.
- Router mock.
- Permission context mock.
- Query/data provider nếu component cần.

Decorator nền Dashboard:

```tsx
export const DashboardCanvas = ({ children }) => (
  <div className="min-h-screen bg-background-page p-8 text-text-primary">
    {children}
  </div>
);
```

Không để story phụ thuộc trực tiếp vào API production.

---

## 5. Viewport presets

```ts
export const dashboardViewports = {
  mobile: {
    name: "Mobile 390",
    styles: { width: "390px", height: "844px" }
  },
  tablet: {
    name: "Tablet 768",
    styles: { width: "768px", height: "1024px" }
  },
  laptop: {
    name: "Laptop 1366",
    styles: { width: "1366px", height: "768px" }
  },
  desktop: {
    name: "Desktop 1440",
    styles: { width: "1440px", height: "900px" }
  },
  wide: {
    name: "Wide 1920",
    styles: { width: "1920px", height: "1080px" }
  }
};
```

---

## 6. Story naming rules

Tên story dùng tiếng Anh kỹ thuật thống nhất:

```text
Default
Hover
Focus
Disabled
Loading
Empty
Error
Stale
LongContent
Compact
Mobile
PermissionDenied
```

Không dùng tên mơ hồ như:

```text
Test1
New
Demo
Example2
```

---

## 7. Button stories

File:

```text
button.stories.tsx
```

Stories bắt buộc:

- `Primary`
- `Secondary`
- `Ghost`
- `Danger`
- `WithLeadingIcon`
- `WithTrailingIcon`
- `IconOnly`
- `Small`
- `Large`
- `Loading`
- `Disabled`
- `LongLabel`
- `KeyboardFocus`

Interaction test:

- Click gọi callback một lần.
- Loading không gọi callback.
- Disabled không tương tác.
- Icon-only có accessible name.

---

## 8. Badge stories

Stories:

- `Neutral`
- `Info`
- `Success`
- `Warning`
- `Danger`
- `Violet`
- `OrderStatuses`
- `PaymentStatuses`
- `InventoryStatuses`
- `TrendPositive`
- `TrendNegative`
- `TrendNeutral`
- `LongLabel`
- `Count99Plus`

Kiểm tra badge không thay đổi chiều cao giữa các variant.

---

## 9. Card stories

Stories:

- `Default`
- `Interactive`
- `Warning`
- `Error`
- `Elevated`
- `WithHeaderAndFooter`
- `LongContent`
- `NarrowContainer`
- `KeyboardFocus`

---

## 10. Input stories

Stories:

- `Default`
- `WithLabel`
- `WithHelperText`
- `Search`
- `Password`
- `Disabled`
- `Readonly`
- `Error`
- `Success`
- `LongValue`
- `KeyboardFocus`

Interaction:

- Gõ dữ liệu.
- Clear search.
- Toggle password visibility.
- Error message liên kết đúng input.

---

## 11. Select stories

Stories:

- `Default`
- `WithSelectedValue`
- `Searchable`
- `LongOptionList`
- `Loading`
- `Empty`
- `Error`
- `Disabled`
- `BranchSelector`
- `WarehouseSelector`
- `KeyboardNavigation`

---

## 12. Tabs stories

Stories:

- `Default`
- `NotificationTabs`
- `OrderStatusTabs`
- `LongLabels`
- `NarrowWidth`
- `KeyboardNavigation`

Interaction:

- Arrow Left/Right đổi tab.
- Tab active có `aria-selected=true`.

---

## 13. Tooltip stories

Stories:

- `Default`
- `SidebarCollapsedLabel`
- `IconButtonTooltip`
- `LongContent`
- `ViewportEdge`
- `KeyboardFocus`

Kiểm tra tooltip không bị cắt khi trigger gần cạnh viewport.

---

## 14. Skeleton stories

Stories:

- `Text`
- `Avatar`
- `KPICard`
- `Chart`
- `ProductRows`
- `OrdersTableRows`
- `NotificationItems`
- `ReducedMotion`

---

## 15. Sidebar stories

Stories bắt buộc:

- `Expanded`
- `Collapsed`
- `WithActiveDashboard`
- `WithActiveSubmenu`
- `WithBadges`
- `LongLabels`
- `PermissionLimited`
- `LoadingPermissions`
- `PermissionError`
- `DrawerOpen`
- `DrawerMobile`
- `KeyboardNavigation`

Interaction tests:

- Collapse và expand.
- Mở submenu.
- Escape đóng drawer.
- Active route đúng.
- Tooltip hiển thị khi collapsed.

---

## 16. Dashboard header stories

Stories:

- `Default`
- `WithLongUserName`
- `WithUnreadNotifications`
- `SearchFocused`
- `SearchLoading`
- `SearchResults`
- `SearchEmpty`
- `SearchError`
- `ExportLoading`
- `LaptopWidth`
- `TabletWidth`
- `MobileFallback`

---

## 17. Global search stories

Dữ liệu story phải có:

- Order result.
- Product result.
- Customer result.
- Supplier result.
- Long title.
- Missing image.

Stories:

- `Idle`
- `Typing`
- `Loading`
- `GroupedResults`
- `NoResults`
- `NetworkError`
- `KeyboardSelection`

---

## 18. KPI card stories

Stories bắt buộc:

- `RevenueDefault`
- `OrdersDefault`
- `CustomersDefault`
- `GrossProfitDefault`
- `PositiveTrend`
- `NegativeBusinessTrend`
- `NeutralTrend`
- `WithSparkline`
- `Loading`
- `Empty`
- `Error`
- `Stale`
- `LongCurrencyValue`
- `PercentageValue`
- `PermissionHidden`
- `Interactive`
- `NarrowContainer`

Dữ liệu số lớn:

```text
128.450.780.000 ₫
```

---

## 19. KPI grid stories

- `FourColumns`
- `TwoColumns`
- `SingleColumn`
- `ThreeItems`
- `SixItems`
- `MixedStates`
- `FinancePermissionLimited`

Story `MixedStates` gồm:

- 1 success.
- 1 loading.
- 1 stale.
- 1 error.

---

## 20. Revenue chart stories

- `SingleSeries`
- `ComparisonSeries`
- `DailyGranularity`
- `MonthlyGranularity`
- `LargeValues`
- `NegativeOrRefundAdjustedData`
- `Loading`
- `Empty`
- `Error`
- `Stale`
- `NarrowWidth`
- `ReducedMotion`
- `KeyboardAccessibleSummary`

Không dùng random data mỗi lần render vì làm visual regression không ổn định.

---

## 21. Order status chart stories

- `Default`
- `AllStatuses`
- `OnlyCompleted`
- `ManySmallSegments`
- `ZeroOrders`
- `Loading`
- `Error`
- `NarrowWidth`
- `LongLegendLabels`

---

## 22. Product thumbnail stories

- `Loaded`
- `Loading`
- `MissingImage`
- `BrokenImage`
- `WideSourceImage`
- `TallSourceImage`
- `LongAltText`

---

## 23. Product performance widget stories

- `Default`
- `RankedByRevenue`
- `RankedByQuantity`
- `RankedByGrossProfit`
- `FiveItems`
- `TenItems`
- `LongProductNames`
- `MissingImages`
- `LargeRevenueValues`
- `Loading`
- `Empty`
- `Error`
- `Stale`
- `NarrowWidth`

---

## 24. Inventory alert widget stories

- `MixedAlerts`
- `NegativeStock`
- `OutOfStock`
- `LowStock`
- `SlowMoving`
- `NoAlertsPositiveEmpty`
- `StaleInventory`
- `Loading`
- `Error`
- `PermissionLimitedActions`
- `NarrowWidth`

Interaction:

- Click `Nhập hàng`.
- Mở row actions.
- Truy cập chi tiết SKU.

---

## 25. Category performance stories

- `DefaultGrid`
- `EightCategories`
- `LongCategoryNames`
- `MissingImages`
- `LargeRevenue`
- `SingleColumn`
- `Loading`
- `Empty`
- `Error`

---

## 26. Product data quality stories

- `CompleteData`
- `PartialCompletion`
- `ManyIssues`
- `MissingImagesOnly`
- `DuplicateSKU`
- `Loading`
- `Error`

---

## 27. Orders table stories

Stories bắt buộc:

- `Default`
- `TenRows`
- `MixedStatuses`
- `LongCustomerNames`
- `BusinessCustomers`
- `LargeOrderValues`
- `OverdueOrders`
- `UnreadOrders`
- `Loading`
- `EmptyDataset`
- `EmptyFilterResult`
- `Error`
- `HorizontalOverflow`
- `PermissionLimitedActions`
- `KeyboardNavigation`

---

## 28. Order row stories

- `Pending`
- `Confirmed`
- `Processing`
- `Shipping`
- `Completed`
- `Cancelled`
- `Returned`
- `PaymentFailed`
- `Overdue`
- `LongContent`
- `NoPhone`
- `CompanyCustomer`

---

## 29. Order quick-view drawer stories

- `DefaultOpen`
- `ManyOrderItems`
- `LongCustomerAddress`
- `LongTimeline`
- `PermissionLimitedActions`
- `Loading`
- `Error`
- `MobileFullWidth`
- `KeyboardFocusTrap`

Interaction:

- Open.
- Close bằng Escape.
- Focus return.
- Scroll body nhưng footer action vẫn truy cập được.

---

## 30. Notification button stories

- `NoUnread`
- `OneUnread`
- `ManyUnread`
- `Count99Plus`
- `KeyboardFocus`

---

## 31. Notification dropdown stories

- `Default`
- `UnreadItems`
- `ActionableItems`
- `GroupedByTime`
- `LongDescriptions`
- `Loading`
- `EmptyAll`
- `EmptyUnread`
- `Error`
- `MarkAllRead`
- `ViewportEdge`
- `MobileDrawer`
- `KeyboardNavigation`

---

## 32. Notification widget stories

- `CriticalAndHighPriority`
- `InventoryOnly`
- `OrdersOnly`
- `NoActionRequired`
- `Loading`
- `EmptyPositive`
- `Error`
- `LongContent`

---

## 33. Toast stories

- `Success`
- `Info`
- `Warning`
- `Error`
- `WithAction`
- `LongContent`
- `ToastStackThree`
- `ToastStackFive`
- `MobileWidth`
- `ReducedMotion`

Interaction:

- Close.
- Click action.
- Pause timeout khi hover/focus.

---

## 34. Modal stories

- `Small`
- `Medium`
- `Large`
- `LongContent`
- `FormError`
- `LoadingAction`
- `DestructiveConfirm`
- `MobileFullScreen`
- `KeyboardFocusTrap`

---

## 35. Drawer stories

- `RightMedium`
- `RightLarge`
- `LeftNavigation`
- `LongContent`
- `StickyFooter`
- `Tablet`
- `Mobile`
- `KeyboardFocusTrap`

---

## 36. Empty state stories

- `NoData`
- `NoFilterResults`
- `PositiveInventoryState`
- `SetupRequired`
- `PermissionDenied`
- `WithPrimaryAction`
- `WithoutAction`
- `NarrowContainer`

---

## 37. Error state stories

- `WidgetError`
- `PageError`
- `NetworkError`
- `PermissionError`
- `Retryable`
- `NotRetryable`
- `LongMessage`

---

## 38. Stale data notice stories

- `RecentlyUpdated`
- `InventoryStale`
- `RevenueStale`
- `WithRefreshAction`
- `RefreshLoading`

---

## 39. Permission gate stories

- `Allowed`
- `DeniedHidden`
- `DeniedFallback`
- `RequiresAllPermissions`
- `RequiresAnyPermission`
- `PermissionsLoading`
- `PermissionsError`

---

## 40. Full dashboard composition stories

File:

```text
dashboard-page.stories.tsx
```

Stories:

- `AdminDefault`
- `SalesManagerDefault`
- `WarehouseUserDefault`
- `AccountantDefault`
- `LoadingDashboard`
- `PartialWidgetErrors`
- `EmptyNewAccount`
- `StaleDashboard`
- `DenseData`
- `LongVietnameseContent`
- `SidebarCollapsed`
- `Laptop1366`
- `Tablet1024`
- `MobileFallback390`
- `ReducedMotion`

---

## 41. Mock data requirements

Mock data phải cố định và nằm trong:

```text
src/mocks/dashboard/
```

Bộ dữ liệu đề xuất:

```text
admin-dashboard.ts
warehouse-dashboard.ts
empty-dashboard.ts
error-dashboard.ts
stale-dashboard.ts
dense-dashboard.ts
long-content-dashboard.ts
```

Không dùng `Math.random()` trong story baseline.

---

## 42. Interaction tests bắt buộc

Tối thiểu cần test:

- Sidebar collapse/expand.
- Sidebar submenu.
- Global search result selection.
- Date range preset selection.
- Notification dropdown open/close.
- Mark notification read.
- Orders filter.
- Open order drawer.
- Modal focus trap.
- Toast action.
- Retry error state.

---

## 43. Accessibility tests trong Storybook

Mỗi story P0 phải chạy addon a11y.

Không được còn lỗi nghiêm trọng như:

- Button không có tên.
- Contrast thấp.
- ARIA state không hợp lệ.
- Form thiếu label.
- Heading sai cấu trúc nghiêm trọng.
- Focusable element nằm trong `aria-hidden`.

Story có lỗi intentional phải ghi chú rõ và không dùng làm production reference.

---

## 44. Visual regression baseline

Baseline bắt buộc:

- Button variants.
- Badge status matrix.
- Sidebar expanded/collapsed.
- Header default/search open.
- KPI states.
- Revenue chart.
- Order status chart.
- Product widget.
- Inventory alerts.
- Orders table.
- Notification dropdown.
- Modal/drawer.
- Full Dashboard 1440px và 1024px.

Không tự động cập nhật baseline khi test thất bại.

---

## 45. Story parameters đề xuất

```ts
export default {
  parameters: {
    layout: "centered",
    controls: { expanded: true },
    a11y: { test: "error" }
  }
};
```

Full Dashboard stories dùng:

```ts
parameters: {
  layout: "fullscreen"
}
```

---

## 46. Story documentation

Mỗi component chính phải có phần docs gồm:

- Mục đích.
- Khi nào sử dụng.
- Khi nào không sử dụng.
- Props.
- Variants.
- Accessibility notes.
- Responsive notes.
- Link đến file guideline liên quan.

---

## 47. Acceptance checklist

- [ ] Component P0 có stories đầy đủ.
- [ ] Component P1 có stories trước production.
- [ ] Có story loading, empty và error cho component dữ liệu.
- [ ] Có story long content.
- [ ] Có story narrow width.
- [ ] Có interaction tests cho luồng chính.
- [ ] Có a11y test.
- [ ] Mock data không dùng random.
- [ ] Full Dashboard có story theo vai trò.
- [ ] Có story permission-limited.
- [ ] Có story reduced motion.
- [ ] Visual regression baseline được thiết lập.
- [ ] Story title và naming nhất quán.
- [ ] Không gọi API production trong Storybook.
- [ ] Story docs liên kết guideline tương ứng.

---

## 48. Những điều không được làm

- Không chỉ tạo một story `Default` cho component phức tạp.
- Không dùng dữ liệu ngắn đẹp cho mọi story.
- Không gọi API production.
- Không dùng random data cho screenshot test.
- Không bỏ qua mobile hoặc narrow-width story.
- Không cập nhật baseline tự động để làm test xanh.
- Không ẩn lỗi a11y mà không có lý do.
- Không tạo story chỉ để tăng số lượng nhưng không kiểm tra trạng thái thực tế.

---

## 49. Kết luận

Storybook của Cynca VLXD Dashboard phải trở thành tài liệu sống cho component, trạng thái, responsive và accessibility. Bộ stories đầy đủ giúp designer, developer và QA kiểm tra giao diện độc lập, giảm lỗi khi tích hợp và tạo nền tảng chắc chắn cho visual regression.

File tiếp theo đề xuất:

```text
README.md
20-Frontend-Architecture.md
```