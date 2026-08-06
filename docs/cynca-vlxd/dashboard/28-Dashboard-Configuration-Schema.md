# Cynca VLXD Admin Dashboard — Dashboard Configuration Schema

> Phiên bản: 1.0  
> Phạm vi: Schema cấu hình động cho layout, widget, filter, quyền và hành vi Dashboard  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, QA và AI coding agent  
> Tài liệu liên quan: `02-Layout.md`, `13-Tailwind-Tokens.md`, `16-Data-Contracts.md`, `27-Role-Based-Dashboard.md`

---

## 1. Mục tiêu

Tài liệu này định nghĩa schema để Dashboard Cynca VLXD có thể cấu hình động mà không hardcode toàn bộ bố cục trong frontend.

Schema phải hỗ trợ:

- Layout theo vai trò.
- Ẩn hoặc hiện widget theo permission.
- Sắp xếp widget theo preset.
- Responsive span.
- Filter theo trang và widget.
- Cấu hình dữ liệu, trạng thái và hành động.
- Versioning và migration.
- User preference nhưng không vượt security policy.

Schema chỉ điều khiển cách hiển thị và cấu hình được phép. Backend vẫn phải kiểm tra quyền và phạm vi dữ liệu.

---

## 2. Nguyên tắc

1. Schema có version rõ ràng.
2. Widget dùng `key` ổn định.
3. Không nhúng dữ liệu nghiệp vụ thật vào configuration.
4. Không cho client tự khai báo endpoint tùy ý.
5. Permission và scope được kiểm soát bởi backend.
6. Giá trị visual chỉ được chọn từ token whitelist.
7. Cấu hình lỗi phải fallback an toàn.
8. User preference không được ghi đè widget bắt buộc.

---

## 3. Root schema

```ts
export type DashboardConfiguration = {
  schemaVersion: number;
  configurationId: string;
  name: string;
  status: "draft" | "published" | "archived";
  preset: DashboardPreset;
  audience: DashboardAudience;
  layout: DashboardLayoutConfig;
  toolbar: DashboardToolbarConfig;
  widgets: DashboardWidgetConfig[];
  featureFlags?: Record<string, boolean>;
  metadata: DashboardConfigurationMetadata;
};
```

---

## 4. Preset

```ts
export type DashboardPreset =
  | "executive"
  | "sales"
  | "operations"
  | "warehouse"
  | "finance"
  | "customer_support"
  | "readonly"
  | "custom";
```

Preset là điểm khởi đầu, không phải authorization rule.

---

## 5. Audience

```ts
export type DashboardAudience = {
  roleIds?: string[];
  requiredPermissions?: string[];
  permissionMode?: "all" | "any";
  branchIds?: string[];
  warehouseIds?: string[];
  userIds?: string[];
};
```

`userIds` chỉ dùng cho rollout thử nghiệm hoặc ngoại lệ có kiểm soát.

Không dùng audience config thay backend authorization.

---

## 6. Layout config

```ts
export type DashboardLayoutConfig = {
  maxContentWidth: 1280 | 1440 | 1600;
  gridColumns: 12;
  pagePaddingToken: "page-x" | "page-x-laptop" | "page-x-mobile";
  sectionGapToken: "section-gap";
  widgetGapToken: "widget-gap";
  allowUserReorder: boolean;
  allowWidgetCollapse: boolean;
  persistPreferences: boolean;
};
```

Không cho nhập giá trị pixel tùy ý từ admin UI.

---

## 7. Toolbar config

```ts
export type DashboardToolbarConfig = {
  showTitle: boolean;
  title: string;
  description?: string;
  filters: DashboardFilterConfig[];
  actions: DashboardActionConfig[];
};
```

---

## 8. Filter config

```ts
export type DashboardFilterConfig = {
  key: "date_range" | "branch" | "warehouse" | "assigned_user" | "compare_mode";
  visible: boolean;
  requiredPermissions?: string[];
  defaultValue?: string;
  allowedValues?: string[];
  persistInUrl: boolean;
  affectsWidgets: "all" | string[];
  order: number;
};
```

Filter không được mở rộng scope ngoài quyền backend trả về.

---

## 9. Action config

```ts
export type DashboardActionConfig = {
  key: string;
  label: string;
  type: "link" | "menu" | "dialog" | "export" | "refresh";
  iconKey?: string;
  requiredPermissions?: string[];
  variant: "primary" | "secondary" | "ghost" | "danger";
  href?: string;
  order: number;
};
```

`href` phải là route nội bộ hoặc URL đã được whitelist.

---

## 10. Widget config

```ts
export type DashboardWidgetConfig = {
  id: string;
  key: DashboardWidgetKey;
  title?: string;
  description?: string;
  visible: boolean;
  requiredPermissions?: string[];
  permissionMode?: "all" | "any";
  requiredFeatureFlags?: string[];
  scopeMode: "inherit" | "system" | "branch" | "warehouse" | "assigned";
  priority: "critical" | "high" | "normal" | "low";
  order: number;
  layout: WidgetLayoutConfig;
  data: WidgetDataConfig;
  display: WidgetDisplayConfig;
  states?: WidgetStateConfig;
  actions?: DashboardActionConfig[];
  locked?: boolean;
};
```

---

## 11. Widget keys

```ts
export type DashboardWidgetKey =
  | "net_revenue_kpi"
  | "orders_kpi"
  | "customers_kpi"
  | "gross_profit_kpi"
  | "revenue_chart"
  | "order_status_chart"
  | "best_products"
  | "inventory_alerts"
  | "category_performance"
  | "product_data_quality"
  | "recent_orders"
  | "payment_summary"
  | "debt_aging"
  | "notifications"
  | "system_health"
  | "custom_report";
```

Mỗi key phải map đến component đã đăng ký trong frontend.

---

## 12. Widget registry

Frontend dùng registry tĩnh:

```ts
export const dashboardWidgetRegistry = {
  net_revenue_kpi: NetRevenueKPI,
  orders_kpi: OrdersKPI,
  revenue_chart: RevenueChart,
  order_status_chart: OrderStatusChart,
  best_products: ProductPerformanceWidget,
  inventory_alerts: InventoryAlertWidget,
  recent_orders: OrdersTableCard,
  notifications: NotificationWidget
} satisfies Partial<Record<DashboardWidgetKey, React.ComponentType<any>>>;
```

Không render component hoặc JavaScript tùy ý từ server config.

---

## 13. Widget layout

```ts
export type WidgetLayoutConfig = {
  span: {
    desktop: 3 | 4 | 6 | 8 | 12;
    laptop: 4 | 6 | 8 | 12;
    tablet: 6 | 12;
    mobile: 12;
  };
  minHeight?: "sm" | "md" | "lg" | "xl";
  rowStart?: number;
  sticky?: boolean;
};
```

`sticky` chỉ cho widget được whitelist và không dùng mặc định.

---

## 14. Data config

```ts
export type WidgetDataConfig = {
  sourceKey: string;
  refreshPolicy: "manual" | "on_focus" | "interval" | "realtime";
  refreshIntervalSeconds?: number;
  staleAfterSeconds?: number;
  queryParams?: Record<string, string | number | boolean>;
  limit?: number;
};
```

`sourceKey` map đến data source đã đăng ký, không phải raw URL.

Ví dụ:

```ts
export const widgetDataSources = {
  dashboard_kpis: dashboardService.getKpis,
  dashboard_revenue: dashboardService.getRevenue,
  recent_orders: dashboardService.getRecentOrders,
  inventory_alerts: dashboardService.getInventoryAlerts
};
```

---

## 15. Refresh rules

- `manual`: chỉ refresh khi người dùng yêu cầu.
- `on_focus`: refetch khi quay lại tab nếu dữ liệu đã stale.
- `interval`: polling có giới hạn.
- `realtime`: dùng event và fallback polling nếu cần.

Giới hạn đề xuất:

```text
Minimum interval: 60 giây
Inventory: 1–5 phút
Orders: 1–5 phút
Finance KPI: 15–60 phút
```

Không cho admin cấu hình polling dưới ngưỡng kỹ thuật cho phép.

---

## 16. Display config

```ts
export type WidgetDisplayConfig = {
  variant?: string;
  density?: "comfortable" | "compact";
  showHeader?: boolean;
  showDescription?: boolean;
  showLastUpdated?: boolean;
  showViewAll?: boolean;
  emptyStateKey?: string;
  iconKey?: string;
  accentToken?: "brand" | "success" | "warning" | "danger" | "info" | "neutral";
};
```

Không cho chọn raw hex color.

---

## 17. Widget states

```ts
export type WidgetStateConfig = {
  loadingVariant?: "skeleton" | "spinner";
  emptyVariant?: "default" | "positive" | "setup_required";
  errorVariant?: "inline" | "card";
  showRetry?: boolean;
  showStaleWarning?: boolean;
};
```

Widget dữ liệu lớn nên dùng skeleton, không dùng spinner đơn độc.

---

## 18. KPI widget extension

```ts
export type KPIWidgetConfig = DashboardWidgetConfig & {
  key: "net_revenue_kpi" | "orders_kpi" | "customers_kpi" | "gross_profit_kpi";
  display: WidgetDisplayConfig & {
    valueFormat: "currency" | "number" | "percentage";
    showTrend: boolean;
    showSparkline: boolean;
    compactValue: boolean;
  };
};
```

Không cho configuration thay đổi công thức KPI.

---

## 19. Chart widget extension

```ts
export type ChartWidgetConfig = DashboardWidgetConfig & {
  key: "revenue_chart" | "order_status_chart";
  display: WidgetDisplayConfig & {
    chartType: "line" | "area" | "bar" | "donut";
    showLegend: boolean;
    showTooltip: boolean;
    showSummary: boolean;
    allowedGranularities?: Array<"day" | "week" | "month" | "quarter">;
  };
};
```

Chart type phải nằm trong loại được widget hỗ trợ.

---

## 20. Table widget extension

```ts
export type TableWidgetConfig = DashboardWidgetConfig & {
  key: "recent_orders";
  display: WidgetDisplayConfig & {
    columns: string[];
    defaultSort?: string;
    rowLimit: number;
    showRowActions: boolean;
  };
};
```

Column list phải được backend/frontend whitelist theo permission.

---

## 21. Example configuration

```json
{
  "schemaVersion": 1,
  "configurationId": "dashboard_sales_manager_v1",
  "name": "Dashboard Quản lý kinh doanh",
  "status": "published",
  "preset": "sales",
  "audience": {
    "requiredPermissions": ["dashboard.view", "orders.view"]
  },
  "layout": {
    "maxContentWidth": 1600,
    "gridColumns": 12,
    "pagePaddingToken": "page-x",
    "sectionGapToken": "section-gap",
    "widgetGapToken": "widget-gap",
    "allowUserReorder": true,
    "allowWidgetCollapse": true,
    "persistPreferences": true
  },
  "toolbar": {
    "showTitle": true,
    "title": "Tổng quan kinh doanh",
    "filters": [
      {
        "key": "date_range",
        "visible": true,
        "defaultValue": "30_days",
        "persistInUrl": true,
        "affectsWidgets": "all",
        "order": 1
      },
      {
        "key": "branch",
        "visible": true,
        "requiredPermissions": ["orders.view_all"],
        "persistInUrl": true,
        "affectsWidgets": "all",
        "order": 2
      }
    ],
    "actions": [
      {
        "key": "export_sales_report",
        "label": "Xuất báo cáo",
        "type": "export",
        "requiredPermissions": ["reports.export"],
        "variant": "secondary",
        "order": 1
      }
    ]
  },
  "widgets": [
    {
      "id": "w_revenue",
      "key": "net_revenue_kpi",
      "visible": true,
      "requiredPermissions": ["finance.view"],
      "scopeMode": "inherit",
      "priority": "high",
      "order": 1,
      "layout": {
        "span": { "desktop": 3, "laptop": 6, "tablet": 6, "mobile": 12 },
        "minHeight": "sm"
      },
      "data": {
        "sourceKey": "dashboard_kpis",
        "refreshPolicy": "on_focus",
        "staleAfterSeconds": 1800
      },
      "display": {
        "showHeader": true,
        "showLastUpdated": false,
        "accentToken": "brand"
      },
      "locked": true
    },
    {
      "id": "w_recent_orders",
      "key": "recent_orders",
      "visible": true,
      "requiredPermissions": ["orders.view"],
      "scopeMode": "inherit",
      "priority": "high",
      "order": 8,
      "layout": {
        "span": { "desktop": 8, "laptop": 12, "tablet": 12, "mobile": 12 },
        "minHeight": "lg"
      },
      "data": {
        "sourceKey": "recent_orders",
        "refreshPolicy": "realtime",
        "staleAfterSeconds": 300,
        "limit": 10
      },
      "display": {
        "density": "comfortable",
        "showHeader": true,
        "showViewAll": true,
        "showLastUpdated": true
      }
    }
  ],
  "metadata": {
    "createdAt": "2026-08-06T18:00:00+07:00",
    "updatedAt": "2026-08-06T18:00:00+07:00",
    "createdBy": "system",
    "publishedAt": "2026-08-06T18:00:00+07:00"
  }
}
```

---

## 22. Metadata

```ts
export type DashboardConfigurationMetadata = {
  createdAt: string;
  updatedAt: string;
  createdBy: string;
  updatedBy?: string;
  publishedAt?: string;
  publishedBy?: string;
  changeNote?: string;
};
```

Mọi publish phải có audit log.

---

## 23. User preferences

```ts
export type DashboardUserPreference = {
  userId: string;
  configurationId: string;
  hiddenWidgetIds?: string[];
  collapsedWidgetIds?: string[];
  widgetOrder?: string[];
  defaultFilters?: Record<string, string>;
  updatedAt: string;
};
```

Quy tắc:

- Không được ẩn widget `locked=true`.
- Không thêm widget ngoài config.
- Không thay scope.
- Không thay permission.
- Không thay sourceKey.

---

## 24. Merge precedence

```text
System security rules
→ Published configuration
→ Role/branch override
→ Feature flags
→ User preferences
```

Mỗi lớp chỉ được sửa các field đã cho phép.

---

## 25. Validation

Schema cần runtime validation.

Ví dụ Zod:

```ts
const widgetSpanSchema = z.object({
  desktop: z.union([z.literal(3), z.literal(4), z.literal(6), z.literal(8), z.literal(12)]),
  laptop: z.union([z.literal(4), z.literal(6), z.literal(8), z.literal(12)]),
  tablet: z.union([z.literal(6), z.literal(12)]),
  mobile: z.literal(12)
});
```

Validation phải kiểm tra:

- Key có trong registry.
- Source key có trong registry.
- Span hợp lệ.
- Permission key hợp lệ.
- Action type hợp lệ.
- Không trùng widget ID.
- Không trùng order bất hợp lý.
- Refresh interval đạt minimum.
- Route và icon được whitelist.

---

## 26. Validation errors

Không publish config khi có lỗi.

Ví dụ:

```text
Widget w_revenue sử dụng sourceKey không tồn tại.
Widget w_orders có mobile span khác 12.
Action export_report thiếu permission reports.export.
Widget inventory_alerts yêu cầu interval dưới mức tối thiểu 60 giây.
```

Error phải chỉ rõ path field.

---

## 27. Safe fallback

Nếu config không tải hoặc không hợp lệ:

1. Không render dữ liệu nhạy cảm mặc định rộng.
2. Dùng preset tối thiểu theo permission.
3. Hiển thị Dashboard shell và error notice.
4. Log configuration error.
5. Cho phép retry.
6. Không dùng config draft thay published config.

Fallback không được tự gán quyền.

---

## 28. Draft, preview và publish

Lifecycle:

```text
Draft → Validated → Previewed → Approved → Published → Archived
```

Preview cần hỗ trợ:

- Role giả lập.
- Viewport.
- Permission set.
- Empty/loading/error states.
- Long content.

Không publish trực tiếp mà bỏ validation.

---

## 29. Versioning và migration

```ts
export type DashboardConfigurationMigration = {
  fromVersion: number;
  toVersion: number;
  migrate: (config: unknown) => unknown;
};
```

Breaking change cần:

- Tăng `schemaVersion`.
- Có migration.
- Có rollback plan.
- Không sửa config published tại chỗ thiếu lịch sử.

---

## 30. API đề xuất

```text
GET    /api/v1/dashboard/configuration
GET    /api/v1/dashboard/configurations/:id
POST   /api/v1/dashboard/configurations
PATCH  /api/v1/dashboard/configurations/:id
POST   /api/v1/dashboard/configurations/:id/validate
POST   /api/v1/dashboard/configurations/:id/preview
POST   /api/v1/dashboard/configurations/:id/publish
POST   /api/v1/dashboard/configurations/:id/archive
GET    /api/v1/dashboard/preferences
PATCH  /api/v1/dashboard/preferences
```

Admin APIs phải yêu cầu quyền cấu hình riêng.

---

## 31. Security requirements

- Backend lọc config theo audience.
- Không trả widget ngoài quyền.
- Không cho raw endpoint URL.
- Không cho arbitrary HTML/JS/CSS.
- Route và icon phải whitelist.
- Publish cần permission và audit log.
- User preference chỉ sửa field cho phép.
- Configuration cache phải tách theo role/scope.

---

## 32. Cache strategy

Cache key đề xuất:

```ts
[
  "dashboard-configuration",
  user.roleId,
  permissionHash,
  scope.level,
  scope.branchIds,
  configurationVersion
]
```

Invalidate khi:

- Publish config mới.
- Permission thay đổi.
- Feature flag thay đổi.
- Role assignment thay đổi.

Không dùng một config cache chung cho mọi user nếu audience khác nhau.

---

## 33. Frontend rendering sequence

```text
Load session
→ Load permissions/scope
→ Load published config
→ Validate config
→ Merge safe preferences
→ Resolve feature flags
→ Build toolbar/widgets
→ Fetch data per widget
→ Render states
```

Không fetch dữ liệu widget trước khi xác định quyền và scope.

---

## 34. Admin configuration UI

Trang cấu hình nên có:

- Danh sách widget registry.
- Canvas 12 cột.
- Drag-and-drop có keyboard alternative.
- Span theo breakpoint.
- Permission selector.
- Scope mode.
- Data refresh policy.
- Preview theo role và viewport.
- Validation panel.
- Change note.
- Publish history.

Không cho chỉnh raw JSON là cách quản trị duy nhất.

---

## 35. Audit events

Bắt buộc ghi:

```text
dashboard.configuration.created
dashboard.configuration.updated
dashboard.configuration.validated
dashboard.configuration.published
dashboard.configuration.archived
dashboard.preference.updated
```

Audit gồm actor, configuration ID, version, diff summary và timestamp.

---

## 36. Analytics events

Có thể ghi tổng hợp:

```text
dashboard.configuration.previewed
dashboard.widget.reordered
dashboard.widget.collapsed
dashboard.preference.reset
```

Không gửi toàn bộ config payload vào analytics.

---

## 37. Test cases

- Config hợp lệ render đúng widget.
- Widget key lạ bị từ chối.
- Source key lạ bị từ chối.
- Permission thiếu thì widget không render.
- User preference không ẩn widget locked.
- Branch scope không bị mở rộng.
- Draft không được dùng ở production.
- Config lỗi dùng safe fallback.
- Migration giữ đúng dữ liệu.
- Cache invalidates sau publish.
- Direct API publish thiếu quyền trả 403.
- Preview đúng 1440, 1024 và 390px.

---

## 38. Visual QA

Kiểm tra:

- Không tạo khoảng trống grid do widget bị ẩn.
- Order và span đúng breakpoint.
- Widget locked có trạng thái rõ trong admin UI.
- Preview khớp Dashboard thật.
- Long title không làm vỡ card.
- User reorder không phá nhóm critical widget.
- Empty/error state vẫn giữ layout.

---

## 39. AI coding agent requirements

AI phải:

- Không render component từ tên tùy ý ngoài registry.
- Không cho config chứa raw URL endpoint.
- Không bỏ runtime validation.
- Không dùng user preference để thay permission.
- Không fetch widget trước khi scope được xác định.
- Báo rõ schema version, migration và fallback.
- Bổ sung test cho config lỗi và quyền publish.
- Không tuyên bố config-driven an toàn nếu backend chưa enforce audience/scope.

---

## 40. Acceptance checklist

- [ ] Root schema có version.
- [ ] Preset và audience được định nghĩa.
- [ ] Layout chỉ dùng token whitelist.
- [ ] Toolbar, filter và action có schema.
- [ ] Widget key map qua registry tĩnh.
- [ ] Data source map qua registry tĩnh.
- [ ] Responsive span hợp lệ.
- [ ] Refresh policy có giới hạn.
- [ ] Permission và scope được backend enforce.
- [ ] User preference không vượt policy.
- [ ] Có runtime validation.
- [ ] Có safe fallback.
- [ ] Có draft, preview, publish và archive.
- [ ] Có version migration.
- [ ] Có audit log.
- [ ] Cache tách theo role/scope.
- [ ] Admin UI có preview và validation.
- [ ] Test và Visual QA đầy đủ.

---

## 41. Những điều không được làm

- Không dùng raw component name từ server để import động tùy ý.
- Không cho cấu hình arbitrary JavaScript, HTML hoặc CSS.
- Không cho admin nhập raw endpoint URL.
- Không để user preference mở rộng quyền hoặc scope.
- Không dùng config draft trong production.
- Không publish config chưa validate.
- Không sửa config published mà thiếu version/history.
- Không dùng raw hex và pixel tùy ý ngoài token system.
- Không fetch dữ liệu nhạy cảm rồi mới ẩn widget.
- Không dùng cache chung làm lẫn audience.

---

## 42. Kết luận

Dashboard Configuration Schema giúp Cynca VLXD quản lý bố cục và widget linh hoạt mà vẫn giữ kiến trúc an toàn, có kiểm soát và dễ mở rộng. Cấu hình chỉ được phép chọn từ component, data source, token, permission và action đã đăng ký trước. Mọi thay đổi phải được validate, preview, version hóa và audit trước khi publish.

File tiếp theo đề xuất:

```text
README.md
29-Dashboard-Admin-Configurator.md
```