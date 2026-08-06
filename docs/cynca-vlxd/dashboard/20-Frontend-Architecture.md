# Cynca VLXD Admin Dashboard — Frontend Architecture

> Phiên bản: 1.0  
> Phạm vi: Kiến trúc frontend cho Dashboard quản trị Cynca VLXD  
> Stack mục tiêu: Next.js, React, TypeScript, Tailwind CSS  
> Tài liệu liên quan: toàn bộ thư mục `docs/cynca-vlxd/dashboard/`

---

## 1. Mục tiêu kiến trúc

Kiến trúc frontend phải giúp Dashboard:

- Dễ mở rộng thêm module.
- Không phụ thuộc chặt vào một API cụ thể.
- Tách rõ UI, business logic và data access.
- Có khả năng kiểm thử độc lập.
- Hỗ trợ loading, empty, error, stale và permission state.
- Giữ Dashboard shell ổn định khi điều hướng.
- Không tạo component quá lớn hoặc khó bảo trì.
- Cho phép Codex hoặc developer mới hiểu nhanh cấu trúc dự án.

---

## 2. Nguyên tắc cốt lõi

### 2.1 Server state và UI state phải tách biệt

Server state gồm:

- KPI.
- Charts.
- Orders.
- Products.
- Inventory.
- Notifications.

UI state gồm:

- Sidebar collapsed.
- Dropdown open.
- Drawer open.
- Selected tab.
- Temporary form state.

Không lưu dữ liệu API vào global UI store nếu query layer đã quản lý được.

### 2.2 Component không tự gọi API tùy tiện

Data fetching nên nằm ở:

- Route/page layer.
- Feature hook.
- Service layer.
- Query hook.

UI primitive không được biết endpoint backend.

### 2.3 Backend là nguồn quyết định quyền cuối cùng

Frontend chỉ kiểm soát hiển thị và UX. Authorization thật phải được backend kiểm tra lại.

### 2.4 Dùng type contract thống nhất

Mọi dữ liệu Dashboard dùng type từ một thư mục contract chung, không định nghĩa lại ở từng component.

---

## 3. Kiến trúc lớp

```text
Presentation Layer
        ↓
Feature Layer
        ↓
Data Access Layer
        ↓
API / BFF Layer
        ↓
Backend Services
```

### Presentation Layer

- UI primitives.
- Dashboard widgets.
- Layout.
- Visual states.

### Feature Layer

- Dashboard overview.
- Orders.
- Inventory.
- Notifications.
- Search.
- Export.

### Data Access Layer

- API client.
- Query hooks.
- Runtime validation.
- Cache.
- Error normalization.

### API / BFF Layer

- Next.js route handlers hoặc backend API hiện có.
- Gom dữ liệu khi cần.
- Áp dụng session và permission scope.

---

## 4. Cấu trúc thư mục đề xuất

```text
src/
├── app/
│   ├── (auth)/
│   │   └── login/
│   ├── (dashboard)/
│   │   ├── layout.tsx
│   │   ├── dashboard/
│   │   │   ├── page.tsx
│   │   │   ├── loading.tsx
│   │   │   └── error.tsx
│   │   ├── orders/
│   │   ├── products/
│   │   ├── inventory/
│   │   ├── customers/
│   │   ├── reports/
│   │   └── settings/
│   └── api/
├── components/
│   ├── ui/
│   ├── navigation/
│   ├── dashboard/
│   ├── orders/
│   ├── inventory/
│   ├── products/
│   └── permissions/
├── features/
│   ├── dashboard/
│   ├── orders/
│   ├── inventory/
│   ├── notifications/
│   ├── search/
│   └── reports/
├── services/
│   ├── api-client.ts
│   ├── dashboard.service.ts
│   ├── orders.service.ts
│   ├── inventory.service.ts
│   ├── notification.service.ts
│   └── report.service.ts
├── hooks/
├── lib/
│   ├── auth/
│   ├── permissions/
│   ├── formatting/
│   ├── validation/
│   ├── query/
│   └── utils/
├── types/
│   ├── api.ts
│   ├── dashboard.ts
│   ├── orders.ts
│   ├── inventory.ts
│   └── notifications.ts
├── mocks/
│   └── dashboard/
└── styles/
    ├── globals.css
    ├── tokens.css
    └── components.css
```

---

## 5. Route architecture

Đề xuất dùng route group để Dashboard shell không reload giữa các module.

```text
app/(dashboard)/layout.tsx
```

Layout chứa:

- Session guard.
- Permission provider.
- Sidebar state provider.
- DashboardShell.
- Sidebar.
- Header.
- Toast provider.

Page chỉ chứa nội dung riêng của route.

---

## 6. Dashboard layout responsibility

`app/(dashboard)/layout.tsx` chịu trách nhiệm:

- Kiểm tra người dùng đã đăng nhập.
- Tải profile và permission cơ bản.
- Dựng sidebar theo quyền.
- Duy trì header.
- Cung cấp branch context.
- Cung cấp notification count.
- Render main content.

Không nên tải toàn bộ KPI, chart và orders trong layout vì sẽ làm mọi route phụ thuộc Dashboard overview.

---

## 7. Server Components và Client Components

### Server Components phù hợp cho

- Route shell.
- Page initial data.
- Permission guard cấp route.
- Metadata.
- Nội dung không tương tác.

### Client Components phù hợp cho

- Sidebar collapse.
- Dropdown.
- Date range selector.
- Chart.
- Table sorting client-side.
- Drawer.
- Toast.
- Real-time notification.

Quy tắc:

- Không đánh dấu cả page là `use client` nếu chỉ một phần nhỏ cần tương tác.
- Đẩy client boundary xuống thấp nhất hợp lý.

---

## 8. Data fetching strategy

Có thể chọn một trong hai hướng:

### Hướng A — Server-first

- Page lấy initial data ở server.
- Truyền dữ liệu xuống client widgets.
- Client refresh khi filter thay đổi.

### Hướng B — Query client

- Page render shell nhanh.
- Widgets dùng TanStack Query hoặc query layer tương đương.
- Cache và refetch độc lập.

Khuyến nghị kết hợp:

- Initial overview tải server-side nếu ổn định.
- Widget cần real-time hoặc refetch thường xuyên dùng query client.

---

## 9. Query key strategy

Query key phải bao gồm đầy đủ scope.

```ts
export const dashboardKeys = {
  all: ["dashboard"] as const,
  overview: (scope: DashboardScope) => ["dashboard", "overview", scope] as const,
  kpis: (scope: DashboardScope) => ["dashboard", "kpis", scope] as const,
  revenue: (scope: DashboardScope) => ["dashboard", "revenue", scope] as const,
  recentOrders: (scope: DashboardScope) => ["dashboard", "orders", scope] as const,
  inventoryAlerts: (scope: DashboardScope) => ["dashboard", "inventory-alerts", scope] as const
};
```

Không dùng một key chung cho tất cả branch hoặc date range.

---

## 10. API client

API client phải xử lý tập trung:

- Base URL.
- Session cookie hoặc token.
- JSON parsing.
- Timeout.
- AbortSignal.
- Error normalization.
- Request ID.
- Unauthorized redirect hoặc refresh session.

Ví dụ interface:

```ts
export type ApiClientOptions = {
  signal?: AbortSignal;
  headers?: Record<string, string>;
  cache?: RequestCache;
  next?: NextFetchRequestConfig;
};

export interface ApiClient {
  get<T>(url: string, options?: ApiClientOptions): Promise<T>;
  post<TBody, TResponse>(url: string, body: TBody, options?: ApiClientOptions): Promise<TResponse>;
  patch<TBody, TResponse>(url: string, body: TBody, options?: ApiClientOptions): Promise<TResponse>;
}
```

---

## 11. Service layer

Service chỉ chịu trách nhiệm giao tiếp data source.

Ví dụ:

```ts
export const dashboardService = {
  getOverview(scope: DashboardScope, signal?: AbortSignal) {
    return apiClient.get<ApiResponse<DashboardOverviewData>>(
      buildDashboardOverviewUrl(scope),
      { signal }
    );
  }
};
```

Service không chứa JSX và không quyết định màu hoặc layout.

---

## 12. Feature hooks

Ví dụ:

```ts
export function useDashboardOverview(scope: DashboardScope) {
  return useQuery({
    queryKey: dashboardKeys.overview(scope),
    queryFn: ({ signal }) => dashboardService.getOverview(scope, signal),
    staleTime: 60_000,
    refetchOnWindowFocus: false
  });
}
```

Hook feature có thể:

- Chuẩn hóa data.
- Xác định stale.
- Map error.
- Expose refresh.

Không format visual string quá sớm nếu nhiều component cần giá trị raw.

---

## 13. State management

### Local state

Dùng cho:

- Dropdown open.
- Selected row.
- Temporary filter draft.
- Modal state cục bộ.

### URL state

Dùng cho:

- Date range.
- Branch.
- Warehouse.
- Main filter.
- Sort.
- Pagination.

### Context

Dùng cho:

- Current user.
- Permissions.
- Current branch scope.
- Sidebar state.
- Toast provider.

### Global store

Chỉ dùng nếu thực sự cần cho state chia sẻ phức tạp. Không thêm Zustand/Redux chỉ để lưu sidebar collapsed.

---

## 14. URL filter contract

Ví dụ:

```text
/dashboard?from=2026-07-01&to=2026-07-31&branchId=branch_hn&compare=previous_period
```

Lợi ích:

- Refresh không mất filter.
- Có thể chia sẻ URL.
- Back/forward hoạt động đúng.
- Query cache ổn định.

Filter draft trong date picker chỉ cập nhật URL sau khi người dùng nhấn `Áp dụng`.

---

## 15. Permission architecture

### Route permission

Kiểm tra trước khi render page.

### Component permission

Dùng `PermissionGate` để điều chỉnh hiển thị action hoặc widget.

### Backend permission

Luôn kiểm tra lại khi request.

Ví dụ:

```tsx
<PermissionGate permission="finance.view">
  <GrossProfitKPI />
</PermissionGate>
```

Không dùng role name để kiểm tra trực tiếp trong nhiều component.

---

## 16. Error architecture

Ba cấp error:

### Page-level

- Route không tải được.
- Session lỗi.
- Overview hoàn toàn không khả dụng.

### Widget-level

- Chart lỗi.
- Orders lỗi.
- Inventory alert lỗi.

### Mutation-level

- Cập nhật đơn thất bại.
- Mark notification read thất bại.
- Export thất bại.

Mỗi cấp có UI và retry khác nhau.

---

## 17. Error boundary strategy

```text
DashboardPage
├── KPIErrorBoundary
├── RevenueChartErrorBoundary
├── OrdersWidgetErrorBoundary
├── InventoryWidgetErrorBoundary
└── NotificationWidgetErrorBoundary
```

Không bọc từng text nhỏ bằng error boundary.

Error boundary chỉ xử lý render error, không thay API error state.

---

## 18. Loading architecture

### Route loading

Hiển thị shell và page skeleton.

### Widget loading

Mỗi widget có skeleton riêng.

### Mutation loading

Button hoặc row action hiển thị pending state.

### Background loading

Export/import hiển thị toast hoặc background job indicator.

Không dùng một spinner toàn màn hình cho mọi tình huống.

---

## 19. Stale data handling

Mỗi loại dữ liệu có stale threshold khác nhau.

Frontend cần:

- Đọc `updatedAt`.
- So với threshold.
- Hiển thị `StaleDataNotice` khi cần.
- Cho phép refresh.
- Không xóa dữ liệu cũ ngay nếu vẫn an toàn để hiển thị.

Tồn kho cần threshold ngắn hơn KPI tổng hợp.

---

## 20. Real-time architecture

Có thể dùng:

- WebSocket.
- Server-Sent Events.
- Polling.

Event handler phải:

- Idempotent.
- Không tạo toast hàng loạt.
- Update query cache có chọn lọc.
- Không tự scroll.
- Không tự mở overlay.

Ví dụ:

```ts
onEvent("order.created", event => {
  queryClient.invalidateQueries({ queryKey: dashboardKeys.recentOrders(currentScope) });
  queryClient.invalidateQueries({ queryKey: dashboardKeys.kpis(currentScope) });
});
```

---

## 21. Mutation architecture

Mutation quan trọng cần:

- Permission check.
- Confirmation khi destructive.
- Pending state.
- Conflict handling.
- Error rollback.
- Audit metadata nếu backend hỗ trợ.

Không dùng optimistic update cho:

- Thanh toán.
- Hoàn tiền.
- Hủy đơn.
- Hoàn thành đơn.

---

## 22. Component composition

Page nên compose feature components:

```tsx
export default async function DashboardPage() {
  return (
    <DashboardContent>
      <DashboardToolbar />
      <DashboardKPISection />
      <DashboardAnalyticsSection />
      <DashboardOperationsSection />
    </DashboardContent>
  );
}
```

Không viết toàn bộ Dashboard trong một file `page.tsx` hàng nghìn dòng.

---

## 23. Feature folder example

```text
features/dashboard/
├── components/
│   ├── dashboard-kpi-section.tsx
│   ├── dashboard-analytics-section.tsx
│   └── dashboard-operations-section.tsx
├── hooks/
│   ├── use-dashboard-overview.ts
│   └── use-dashboard-scope.ts
├── queries/
│   └── dashboard-keys.ts
├── adapters/
│   └── dashboard.adapter.ts
├── schemas/
│   └── dashboard.schema.ts
└── index.ts
```

---

## 24. Adapter pattern

Adapter giúp frontend không phụ thuộc trực tiếp response backend.

```ts
export function adaptDashboardKPI(dto: DashboardKPIDto): DashboardKPI {
  return {
    id: dto.id,
    label: dto.label,
    value: dto.value,
    valueType: dto.valueType,
    trend: dto.semanticTrend,
    trendValue: dto.trendValue,
    comparisonLabel: dto.comparisonLabel,
    updatedAt: dto.updatedAt
  };
}
```

Dùng adapter khi API legacy khác contract frontend mong muốn.

---

## 25. Runtime validation

Dùng schema ở boundary data:

```ts
const response = dashboardOverviewSchema.parse(rawResponse);
```

Có thể:

- Strict ở development/test.
- Safe parse và log ở production.

Không tin dữ liệu network chỉ vì TypeScript compile.

---

## 26. Formatting architecture

Tập trung helper trong:

```text
lib/formatting/
```

Ví dụ:

```ts
formatCurrency
formatCompactCurrency
formatPercent
formatDateTime
formatRelativeTime
formatQuantity
```

Component không tự viết `Intl.NumberFormat` lặp lại nhiều nơi.

---

## 27. Status mapping architecture

```text
lib/status/
├── order-status.ts
├── payment-status.ts
├── inventory-status.ts
└── notification-status.ts
```

Mỗi mapping trả:

- Label.
- Badge variant.
- Icon key.
- Semantic category.

---

## 28. Design token architecture

Nguồn token:

```text
styles/tokens.css
tailwind.config.ts
```

Component chỉ dùng semantic class:

```text
bg-background-surface
text-text-primary
border-border-subtle
bg-brand
```

Không hardcode lại token trong từng feature.

---

## 29. Chart architecture

Chart wrapper chịu trách nhiệm:

- Responsive container.
- Tooltip format.
- Empty/error state.
- Accessibility summary.
- Reduced motion.

Chart feature chỉ truyền data và configuration.

Không để từng chart tự tạo bộ màu riêng.

---

## 30. Table architecture

Tách:

- Table primitive.
- Orders-specific columns.
- Status cell.
- Row actions.
- Responsive wrapper.

Nếu dùng TanStack Table, business action vẫn nằm trong feature layer.

Không đưa toàn bộ domain logic vào generic DataTable.

---

## 31. Notification architecture

```text
NotificationProvider
├── unread count query
├── real-time subscription
├── notification dropdown state
└── toast bridge
```

Phân biệt:

- Persistent notification.
- Toast feedback.
- Actionable Dashboard widget.

Không đồng nhất ba loại này thành một cấu trúc UI duy nhất.

---

## 32. Search architecture

Global search cần:

- Debounce khoảng 200–300ms.
- Abort request cũ.
- Minimum query length.
- Group result theo type.
- Permission scope từ backend.
- Keyboard selection.

Search query không nên lưu vào global store lâu dài.

---

## 33. Export architecture

Luồng đề xuất:

```text
User chọn format
→ POST export job
→ Nhận file ngay hoặc jobId
→ Theo dõi background job
→ Toast hoàn thành
→ Download link có thời hạn
```

Không block toàn Dashboard khi tạo báo cáo.

---

## 34. Testing architecture

### Unit

- Adapters.
- Formatters.
- Status mapping.
- Permission helpers.
- Query serialization.

### Component

- UI states.
- Keyboard behavior.
- Overlay focus.
- Table actions.

### Integration

- Filter → API → widget update.
- Permission scope.
- Mutation rollback.
- Real-time cache update.

### E2E

- Login.
- Dashboard overview.
- Search.
- Orders quick view.
- Inventory action.
- Export.
- Notification.

---

## 35. Performance architecture

- Lazy-load chart library nếu cần.
- Không tải widget dưới fold ngay nếu không cần thiết.
- Dùng image optimization cho thumbnail.
- Giới hạn item Dashboard.
- Memoize calculation nặng, không memoize mọi thứ.
- Tránh context provider thay đổi liên tục ở root.
- Batch real-time updates.
- Hủy request cũ khi filter đổi nhanh.

---

## 36. Security architecture

- Session dùng HTTP-only cookie nếu phù hợp.
- Không lưu secret trong client.
- Không log PII vào console.
- API client không tự gửi dữ liệu ngoài scope.
- Route handler kiểm tra session và permission.
- Export endpoint kiểm tra quyền lại.
- HTML từ API phải sanitize trước khi render.

---

## 37. Observability

Frontend nên ghi nhận:

- Page load failure.
- Widget API failure.
- Mutation failure.
- Export job failure.
- Unexpected contract parse error.
- Performance metric.

Log nên gồm:

- Request ID.
- Route.
- Widget.
- User role hoặc scope không nhạy cảm.
- Error code.

Không log token, mật khẩu hoặc dữ liệu khách hàng đầy đủ.

---

## 38. Naming conventions

### Component

```text
PascalCase
```

### Hook

```text
useDashboardOverview
```

### Service

```text
dashboardService
```

### Query key

```text
dashboardKeys
```

### File

```text
kebab-case.tsx
```

### Permission

```text
resource.action
```

Ví dụ:

```text
orders.view
orders.update
reports.export
```

---

## 39. Import boundaries

Khuyến nghị:

- `ui/` không import từ `features/`.
- Feature có thể import `ui/`, `lib/`, `types/`.
- Service không import React component.
- Type không import runtime module nếu không cần.
- Dashboard feature không import implementation nội bộ của feature khác; dùng public barrel có kiểm soát.

---

## 40. Public API của feature

```text
features/orders/index.ts
```

Chỉ export:

- Component public.
- Hook public.
- Type cần thiết.

Không export mọi file nội bộ.

---

## 41. Code quality rules

- TypeScript strict.
- Không dùng `any` nếu không có lý do rõ.
- Không để catch rỗng.
- Không bỏ qua promise rejection.
- Không disable lint rule rộng toàn file nếu có thể sửa đúng.
- Không tạo component vượt 300–400 dòng nếu có thể tách logic hợp lý.
- Không trừu tượng hóa sớm khi chỉ có một use case.

---

## 42. Migration strategy

Nếu repository đã có Dashboard cũ:

1. Khảo sát component dùng lại được.
2. Tạo token trước.
3. Dựng shell mới.
4. Migrate từng widget.
5. Dùng feature flag nếu cần.
6. Không thay toàn bộ trong một commit quá lớn nếu rủi ro cao.
7. Giữ API adapter để tương thích backend cũ.

---

## 43. Suggested implementation phases

### Phase 1 — Foundation

- Tokens.
- UI primitives.
- Shell.
- Sidebar.
- Header.

### Phase 2 — Analytics

- KPI.
- Charts.
- Date range.

### Phase 3 — Operations

- Product widgets.
- Inventory alerts.
- Orders table.
- Quick view.

### Phase 4 — Feedback

- Notifications.
- Toast.
- Export jobs.

### Phase 5 — Hardening

- Responsive.
- Accessibility.
- Performance.
- Tests.
- Observability.

---

## 44. Architecture decision records

Các quyết định lớn nên ghi ADR:

```text
docs/adr/
├── 001-dashboard-data-fetching.md
├── 002-query-library.md
├── 003-chart-library.md
├── 004-permission-model.md
└── 005-realtime-notifications.md
```

ADR gồm:

- Context.
- Decision.
- Alternatives.
- Consequences.

---

## 45. Acceptance checklist

- [ ] Shell tách khỏi page content.
- [ ] Server state và UI state tách biệt.
- [ ] Data access nằm ngoài UI primitive.
- [ ] Query key chứa đầy đủ scope.
- [ ] Filter quan trọng được serialize vào URL.
- [ ] Permission kiểm tra ở route, component và backend.
- [ ] Widget có error boundary hợp lý.
- [ ] Loading chia theo route, widget và mutation.
- [ ] Stale data dùng `updatedAt`.
- [ ] Real-time event idempotent.
- [ ] Mutation quan trọng không optimistic mù quáng.
- [ ] Type contract dùng tập trung.
- [ ] Có adapter khi API legacy khác contract.
- [ ] Runtime validation ở data boundary quan trọng.
- [ ] Format và status mapping tập trung.
- [ ] Chart và table có wrapper kiến trúc rõ.
- [ ] Test strategy đủ unit, component, integration và E2E.
- [ ] Không có import vòng hoặc vi phạm layer.
- [ ] Có kế hoạch migration nếu thay Dashboard cũ.

---

## 46. Những điều không được làm

- Không đặt toàn bộ Dashboard trong một component lớn.
- Không để UI primitive gọi API.
- Không dùng global store cho mọi server state.
- Không kiểm tra quyền chỉ bằng role name.
- Không dùng một cache key cho mọi scope.
- Không hardcode status mapping trong từng component.
- Không để chart tự định nghĩa token riêng.
- Không dùng error boundary thay cho API error handling.
- Không gọi API production trong Storybook.
- Không thêm thư viện chỉ để giải quyết một state đơn giản.
- Không tuyên bố hoàn thành nếu chưa chạy lint, typecheck và test.

---

## 47. Kết luận

Kiến trúc frontend Cynca VLXD Dashboard phải giữ ranh giới rõ giữa UI, feature logic, data access và backend contract. Cấu trúc này giúp Dashboard dễ mở rộng, dễ kiểm thử, hỗ trợ phân quyền và giữ trải nghiệm ổn định khi dữ liệu, module hoặc đội phát triển tăng lên.

File tiếp theo đề xuất:

```text
README.md
21-Test-Strategy.md
```