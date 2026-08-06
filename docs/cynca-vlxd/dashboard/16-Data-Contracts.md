# Cynca VLXD Admin Dashboard — Data Contracts Specification

> Phiên bản: 1.0  
> Phạm vi: Data model, API response, filter, error và permission contracts cho Dashboard  
> Nền tảng mục tiêu: Next.js / React / TypeScript / REST hoặc BFF  
> Tài liệu liên quan: toàn bộ thư mục `docs/cynca-vlxd/dashboard/`

---

## 1. Mục tiêu

Tài liệu này định nghĩa hợp đồng dữ liệu giữa frontend Dashboard và backend Cynca VLXD.

Mục tiêu:

- Frontend không tự đoán cấu trúc dữ liệu.
- Backend trả dữ liệu có semantic rõ ràng.
- KPI, biểu đồ, bảng đơn hàng và cảnh báo dùng cùng chuẩn.
- Phân biệt chính xác loading, empty, error và stale state.
- Hỗ trợ phân quyền, chi nhánh, khoảng thời gian và timezone.
- Giảm lỗi do định dạng tiền, ngày và trạng thái không nhất quán.

---

## 2. Nguyên tắc chung

### 2.1 Không trả dữ liệu đã format làm nguồn duy nhất

Backend nên trả giá trị thô và metadata cần thiết.

Ví dụ tốt:

```json
{
  "value": 1280450000,
  "currency": "VND"
}
```

Frontend có thể format thành:

```text
1,28 tỷ ₫
```

Có thể trả thêm `formattedValue` để đồng bộ cách hiển thị, nhưng không thay thế giá trị thô.

### 2.2 Dùng ISO 8601 cho ngày giờ

```text
2026-08-06T16:30:00+07:00
```

Không trả ngày kiểu:

```text
06/08/2026 16:30
```

làm dữ liệu nguồn.

### 2.3 ID và code là hai trường khác nhau

- `id`: ID hệ thống, ổn định cho API.
- `code`: mã nghiệp vụ hiển thị cho người dùng.

### 2.4 Trạng thái phải dùng enum ổn định

Không trả label tiếng Việt làm enum.

Tốt:

```json
{ "status": "shipping", "statusLabel": "Đang giao" }
```

Không tốt:

```json
{ "status": "Đang giao" }
```

---

## 3. Envelope API chuẩn

Response thành công:

```ts
export type ApiSuccess<T> = {
  success: true;
  data: T;
  meta?: ApiMeta;
  requestId?: string;
};
```

Response lỗi:

```ts
export type ApiFailure = {
  success: false;
  error: ApiError;
  requestId?: string;
};
```

Union:

```ts
export type ApiResponse<T> = ApiSuccess<T> | ApiFailure;
```

---

## 4. API metadata

```ts
export type ApiMeta = {
  generatedAt?: string;
  updatedAt?: string;
  timezone?: string;
  currency?: string;
  page?: number;
  pageSize?: number;
  totalItems?: number;
  totalPages?: number;
  nextCursor?: string | null;
};
```

`updatedAt` dùng cho stale state.

`generatedAt` dùng khi backend tạo dữ liệu tổng hợp tại thời điểm request.

---

## 5. Error contract

```ts
export type ApiErrorCode =
  | "UNAUTHENTICATED"
  | "FORBIDDEN"
  | "VALIDATION_ERROR"
  | "NOT_FOUND"
  | "CONFLICT"
  | "RATE_LIMITED"
  | "UPSTREAM_UNAVAILABLE"
  | "INTERNAL_ERROR"
  | "UNKNOWN_ERROR";

export type ApiError = {
  code: ApiErrorCode;
  message: string;
  userMessage?: string;
  fieldErrors?: Record<string, string[]>;
  retryable?: boolean;
  details?: Record<string, unknown>;
};
```

Frontend hiển thị `userMessage` khi có.

`message` có thể phục vụ log kỹ thuật, không nhất thiết hiển thị trực tiếp.

---

## 6. Query scope chung

```ts
export type DashboardScope = {
  from: string;
  to: string;
  timezone: string;
  branchId?: string;
  warehouseId?: string;
  compareMode?: "previous_period" | "previous_year" | "none";
};
```

Ví dụ query:

```text
?from=2026-07-01T00:00:00+07:00
&to=2026-07-31T23:59:59+07:00
&timezone=Asia/Ho_Chi_Minh
&branchId=branch_hn
&compareMode=previous_period
```

---

## 7. User và permission contract

```ts
export type DashboardUser = {
  id: string;
  name: string;
  email?: string;
  phone?: string;
  avatarUrl?: string;
  roleId: string;
  roleName: string;
  permissions: string[];
  branchIds: string[];
  warehouseIds: string[];
};
```

Permission key đề xuất:

```text
dashboard.view
finance.view
orders.view
orders.update
inventory.view
inventory.update
products.view
products.update
reports.export
notifications.manage
settings.view
```

Frontend không tự suy quyền từ `roleName`.

---

## 8. Branch contract

```ts
export type Branch = {
  id: string;
  code: string;
  name: string;
  address?: string;
  active: boolean;
  timezone: string;
};
```

Response:

```ts
export type BranchListData = {
  items: Branch[];
  selectedBranchId?: string;
};
```

---

## 9. Dashboard overview contract

Endpoint đề xuất:

```text
GET /api/dashboard/overview
```

Response:

```ts
export type DashboardOverviewData = {
  scope: DashboardScope;
  kpis: DashboardKPI[];
  revenueChart: RevenueChartData;
  orderStatus: OrderStatusSummary;
  bestProducts: ProductPerformanceItem[];
  inventoryAlerts: InventoryAlertItem[];
  recentOrders: DashboardOrderRow[];
  notifications: DashboardNotification[];
  updatedAt: string;
};
```

Có thể tách endpoint theo widget nếu cần cache hoặc tải độc lập.

---

## 10. KPI contract

```ts
export type KPISemanticTrend = "positive" | "negative" | "neutral";

export type KPIValueType =
  | "currency"
  | "number"
  | "percentage"
  | "duration";

export type DashboardKPI = {
  id: string;
  label: string;
  value: number;
  valueType: KPIValueType;
  currency?: "VND";
  unit?: string;
  trend?: KPISemanticTrend;
  trendValue?: number;
  comparisonLabel?: string;
  tooltip?: string;
  href?: string;
  sparkline?: Array<{
    timestamp: string;
    value: number;
  }>;
  updatedAt: string;
};
```

Ví dụ:

```json
{
  "id": "net_revenue",
  "label": "Doanh thu thuần",
  "value": 1280450000,
  "valueType": "currency",
  "currency": "VND",
  "trend": "positive",
  "trendValue": 12.5,
  "comparisonLabel": "so với tháng trước",
  "tooltip": "Doanh thu sau chiết khấu, hoàn tiền và trả hàng.",
  "href": "/reports/revenue",
  "updatedAt": "2026-08-06T16:30:00+07:00"
}
```

---

## 11. Revenue chart contract

```ts
export type ChartGranularity =
  | "hour"
  | "day"
  | "week"
  | "month"
  | "quarter"
  | "year";

export type TimeSeriesPoint = {
  timestamp: string;
  value: number;
};

export type ChartSeries = {
  id: string;
  label: string;
  points: TimeSeriesPoint[];
  semanticColor?: "primary" | "compare" | "success" | "warning" | "danger";
};

export type RevenueChartData = {
  currency: "VND";
  granularity: ChartGranularity;
  series: ChartSeries[];
  summary?: string;
  updatedAt: string;
};
```

---

## 12. Order status summary contract

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

export type OrderStatusItem = {
  status: OrderStatus;
  label: string;
  count: number;
  percentage: number;
};

export type OrderStatusSummary = {
  total: number;
  items: OrderStatusItem[];
  updatedAt: string;
};
```

Tổng phần trăm có thể lệch nhẹ 100% do làm tròn.

---

## 13. Product performance contract

```ts
export type ProductPerformanceMetric =
  | "revenue"
  | "quantity"
  | "gross_profit";

export type ProductPerformanceItem = {
  productId: string;
  name: string;
  sku: string;
  categoryId?: string;
  categoryName?: string;
  imageUrl?: string;
  quantitySold: number;
  revenue: number;
  grossProfit?: number;
  trendPercent?: number;
  rank: number;
};

export type ProductPerformanceData = {
  metric: ProductPerformanceMetric;
  currency: "VND";
  items: ProductPerformanceItem[];
  updatedAt: string;
};
```

---

## 14. Inventory alert contract

```ts
export type InventoryAlertStatus =
  | "negative"
  | "out_of_stock"
  | "low_stock"
  | "slow_moving";

export type InventoryAlertItem = {
  productId: string;
  warehouseId: string;
  warehouseName: string;
  name: string;
  sku: string;
  imageUrl?: string;
  availableQuantity: number;
  reservedQuantity?: number;
  minimumQuantity: number;
  unitName: string;
  status: InventoryAlertStatus;
  lastSyncedAt: string;
  actionHref?: string;
};
```

Không dùng `stock` mơ hồ. Phải phân biệt:

- `availableQuantity`.
- `reservedQuantity`.
- `onHandQuantity` nếu backend có.

---

## 15. Category performance contract

```ts
export type CategoryPerformanceItem = {
  categoryId: string;
  name: string;
  imageUrl?: string;
  iconKey?: string;
  productCount: number;
  revenue: number;
  quantitySold: number;
  href?: string;
};
```

---

## 16. Product data quality contract

```ts
export type ProductDataIssueType =
  | "missing_image"
  | "missing_price"
  | "missing_description"
  | "missing_specification"
  | "missing_category"
  | "missing_unit"
  | "duplicate_sku"
  | "missing_inventory";

export type ProductDataIssueSummary = {
  issueType: ProductDataIssueType;
  label: string;
  count: number;
  href: string;
};

export type ProductDataQualityData = {
  totalProducts: number;
  completedProducts: number;
  completionPercentage: number;
  issues: ProductDataIssueSummary[];
  updatedAt: string;
};
```

---

## 17. Order row contract

```ts
export type PaymentStatus =
  | "unpaid"
  | "partial"
  | "paid"
  | "partially_refunded"
  | "refunded"
  | "failed";

export type PaymentMethod =
  | "cod"
  | "bank_transfer"
  | "e_wallet"
  | "credit"
  | "at_store"
  | "other";

export type DashboardOrderRow = {
  id: string;
  code: string;
  customer: {
    id?: string;
    name: string;
    phone?: string;
    companyName?: string;
  };
  paymentMethod: PaymentMethod;
  paymentStatus: PaymentStatus;
  orderStatus: OrderStatus;
  totalAmount: number;
  currency: "VND";
  branchId?: string;
  branchName?: string;
  assignedUserId?: string;
  assignedUserName?: string;
  createdAt: string;
  updatedAt: string;
  overdueMinutes?: number;
  unread?: boolean;
  availableActions: OrderAction[];
};
```

---

## 18. Order action contract

```ts
export type OrderAction =
  | "view"
  | "confirm"
  | "prepare"
  | "ship"
  | "complete"
  | "cancel"
  | "refund"
  | "print";
```

Backend nên trả `availableActions` dựa trên trạng thái và quyền hiện tại.

Frontend không tự suy toàn bộ workflow chỉ từ `orderStatus`.

---

## 19. Order detail quick view contract

```ts
export type OrderQuickView = {
  id: string;
  code: string;
  customer: {
    id?: string;
    name: string;
    phone?: string;
    email?: string;
    address?: string;
  };
  items: Array<{
    productId: string;
    sku: string;
    name: string;
    imageUrl?: string;
    quantity: number;
    unitName: string;
    unitPrice: number;
    lineTotal: number;
  }>;
  subtotal: number;
  discountAmount: number;
  shippingAmount: number;
  refundAmount: number;
  totalAmount: number;
  paymentMethod: PaymentMethod;
  paymentStatus: PaymentStatus;
  orderStatus: OrderStatus;
  timeline: Array<{
    status: string;
    label: string;
    occurredAt: string;
    actorName?: string;
    note?: string;
  }>;
  availableActions: OrderAction[];
};
```

---

## 20. Notification contract

```ts
export type NotificationSeverity =
  | "critical"
  | "high"
  | "medium"
  | "low";

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
  dismissedAt?: string;
  href?: string;
  actionLabel?: string;
  entityType?: string;
  entityId?: string;
};
```

---

## 21. Notification list contract

```ts
export type NotificationListData = {
  items: DashboardNotification[];
  unreadCount: number;
  actionableCount: number;
  nextCursor?: string | null;
};
```

---

## 22. Export report contract

Request:

```ts
export type ExportReportRequest = {
  reportType: "dashboard" | "revenue" | "orders" | "inventory";
  format: "csv" | "xlsx" | "pdf";
  scope: DashboardScope;
  columns?: string[];
};
```

Response đồng bộ:

```ts
export type ExportReportResult = {
  mode: "ready";
  fileName: string;
  downloadUrl: string;
  expiresAt: string;
};
```

Response bất đồng bộ:

```ts
export type ExportReportJob = {
  mode: "queued";
  jobId: string;
  status: "queued" | "processing";
};
```

Không trả file base64 lớn trong JSON.

---

## 23. Background job contract

```ts
export type BackgroundJobStatus =
  | "queued"
  | "processing"
  | "completed"
  | "failed";

export type BackgroundJob = {
  id: string;
  type: string;
  status: BackgroundJobStatus;
  progress?: number;
  resultUrl?: string;
  errorMessage?: string;
  createdAt: string;
  updatedAt: string;
};
```

---

## 24. Search contract

Request:

```ts
export type GlobalSearchQuery = {
  q: string;
  types?: Array<"order" | "product" | "customer" | "supplier">;
  limit?: number;
  branchId?: string;
};
```

Result:

```ts
export type GlobalSearchResult = {
  id: string;
  type: "order" | "product" | "customer" | "supplier";
  title: string;
  subtitle?: string;
  imageUrl?: string;
  status?: string;
  href: string;
};

export type GlobalSearchData = {
  query: string;
  items: GlobalSearchResult[];
};
```

Backend phải áp dụng permission scope trước khi trả kết quả.

---

## 25. Pagination contract

Offset pagination:

```ts
export type PaginationParams = {
  page: number;
  pageSize: number;
};
```

Cursor pagination:

```ts
export type CursorParams = {
  cursor?: string;
  limit: number;
};
```

Notification và activity feed nên ưu tiên cursor pagination.

Order table đầy đủ có thể dùng offset pagination nếu nghiệp vụ cần nhảy trang.

---

## 26. Sorting contract

```ts
export type SortDirection = "asc" | "desc";

export type SortParam = {
  field: string;
  direction: SortDirection;
};
```

Ví dụ:

```text
?sort=createdAt:desc
```

Backend phải whitelist field được sort.

---

## 27. Filter serialization

Query filter cần ổn định và có thể chia sẻ URL.

Ví dụ:

```text
/dashboard?from=2026-07-01&to=2026-07-31&branchId=branch_hn
```

Không lưu toàn bộ filter chỉ trong component local state nếu người dùng cần refresh hoặc chia sẻ URL.

---

## 28. Stale data contract

Frontend có thể xác định stale từ `updatedAt` và ngưỡng theo loại dữ liệu.

Ngưỡng tham khảo:

| Dữ liệu | Ngưỡng cảnh báo stale |
|---|---:|
| Tồn kho | 5–15 phút |
| Đơn hàng | 2–5 phút |
| Notification | 1–5 phút |
| KPI doanh thu | 15–60 phút |
| Báo cáo tổng hợp | Theo lịch ETL |

Backend có thể trả thêm:

```ts
export type FreshnessMeta = {
  updatedAt: string;
  stale: boolean;
  staleReason?: string;
};
```

---

## 29. Mutation result contract

```ts
export type MutationResult<T> = {
  item: T;
  message?: string;
  updatedAt: string;
};
```

Ví dụ cập nhật trạng thái đơn:

```text
PATCH /api/orders/:id/status
```

Request:

```ts
export type UpdateOrderStatusRequest = {
  targetStatus: OrderStatus;
  note?: string;
  expectedVersion?: number;
};
```

`expectedVersion` hỗ trợ optimistic concurrency control.

---

## 30. Conflict handling

Khi dữ liệu đã bị người khác sửa, backend trả:

```json
{
  "success": false,
  "error": {
    "code": "CONFLICT",
    "message": "Order version conflict",
    "userMessage": "Đơn hàng đã được người khác cập nhật. Hãy tải lại dữ liệu.",
    "retryable": true
  }
}
```

Frontend không ghi đè mù dữ liệu mới hơn.

---

## 31. Real-time event contract

```ts
export type RealtimeEvent<T = unknown> = {
  id: string;
  type: string;
  occurredAt: string;
  entityType?: string;
  entityId?: string;
  payload: T;
};
```

Event đề xuất:

```text
order.created
order.updated
inventory.updated
inventory.low_stock
notification.created
report.completed
```

Frontend phải xử lý event idempotent theo `id`.

---

## 32. Cache key contract

Cache key phải bao gồm:

- Endpoint.
- User scope.
- Branch hoặc warehouse.
- Date range.
- Compare mode.
- Filter và sort.

Ví dụ:

```ts
[
  "dashboard-kpis",
  user.id,
  branchId,
  from,
  to,
  compareMode
]
```

Không dùng một cache key chung cho mọi chi nhánh.

---

## 33. Null, zero và unavailable

Quy tắc:

- `0`: giá trị hợp lệ bằng không.
- `null`: chưa có hoặc không áp dụng.
- Thiếu field: contract không đầy đủ, cần xử lý lỗi.

Ví dụ KPI chưa tính được:

```json
{
  "value": null,
  "availability": "not_calculated"
}
```

Có thể dùng:

```ts
export type DataAvailability =
  | "available"
  | "not_calculated"
  | "not_applicable"
  | "permission_denied";
```

---

## 34. Currency và precision

- Currency mặc định: `VND`.
- Không dùng floating-point cho giá trị tiền ở backend nếu có thể.
- Giá trị JSON có thể là integer đồng.
- Nếu hỗ trợ nhiều tiền tệ, cần `currency` trên mỗi monetary object.

Ví dụ mở rộng:

```ts
export type Money = {
  amount: number;
  currency: "VND";
};
```

---

## 35. Units of measure

Vật liệu xây dựng có nhiều đơn vị:

```text
bao
kg
tấn
cây
mét
m²
m³
viên
thùng
cuộn
```

Contract:

```ts
export type UnitOfMeasure = {
  id: string;
  code: string;
  name: string;
  symbol?: string;
  decimalScale: number;
};
```

Không giả định mọi số lượng là integer.

---

## 36. Validation rules

Frontend validation cải thiện UX, nhưng backend là nguồn quyết định cuối cùng.

Backend trả field error:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request",
    "userMessage": "Vui lòng kiểm tra lại thông tin.",
    "fieldErrors": {
      "targetStatus": ["Không thể chuyển trực tiếp từ Chờ xác nhận sang Hoàn thành."]
    }
  }
}
```

---

## 37. Runtime validation

TypeScript không xác thực dữ liệu runtime.

Khuyến nghị dùng:

- Zod.
- Valibot.
- Schema validation có sẵn trong repository.

Ví dụ:

```ts
import { z } from "zod";

export const dashboardKpiSchema = z.object({
  id: z.string(),
  label: z.string(),
  value: z.number(),
  valueType: z.enum(["currency", "number", "percentage", "duration"]),
  updatedAt: z.string().datetime()
});
```

Không bắt buộc validate mọi response lớn ở production nếu ảnh hưởng hiệu năng, nhưng contract quan trọng phải có kiểm soát.

---

## 38. Versioning

API có thể version bằng:

```text
/api/v1/dashboard/overview
```

Hoặc header versioning nếu kiến trúc yêu cầu.

Breaking change phải:

- Tăng version.
- Có migration plan.
- Không đổi enum âm thầm.
- Không xóa field ngay khi frontend cũ còn sử dụng.

---

## 39. Endpoint đề xuất

```text
GET   /api/v1/dashboard/overview
GET   /api/v1/dashboard/kpis
GET   /api/v1/dashboard/revenue
GET   /api/v1/dashboard/order-status
GET   /api/v1/dashboard/products/best-selling
GET   /api/v1/dashboard/inventory/alerts
GET   /api/v1/dashboard/orders/recent
GET   /api/v1/notifications
PATCH /api/v1/notifications/:id/read
PATCH /api/v1/notifications/read-all
GET   /api/v1/search
POST  /api/v1/reports/export
GET   /api/v1/jobs/:id
```

Không bắt buộc dùng đúng URL nếu backend đã có chuẩn khác. Điều quan trọng là contract thống nhất.

---

## 40. Mock data policy

Mock data phải nằm riêng:

```text
src/mocks/dashboard.ts
```

Không đặt mock data trực tiếp trong component.

Mock phải:

- Tuân thủ type thật.
- Có dữ liệu dài, số lớn và trạng thái lỗi.
- Có biến thể empty, loading và stale.
- Được thay thế rõ ràng khi tích hợp API.

---

## 41. Contract test

Khuyến nghị kiểm thử:

- Response đúng schema.
- Enum không có giá trị lạ.
- Tổng KPI khớp phạm vi filter.
- Tổng order status bằng total.
- Percentage hợp lệ.
- `availableActions` phù hợp permission.
- Dữ liệu ngoài branch scope không xuất hiện.
- Export dùng đúng scope.
- Date range và timezone không lệch ngày.

---

## 42. Security requirements

- Backend không tin `branchId` từ client nếu người dùng không có quyền.
- Search phải áp dụng permission filter.
- Export phải kiểm tra quyền lại ở server.
- `availableActions` không thay thế authorization backend.
- Không trả dữ liệu tài chính nhạy cảm cho vai trò bị giới hạn.
- Không trả PII không cần thiết trong Dashboard list.

---

## 43. Acceptance checklist

- [ ] Có envelope success và error thống nhất.
- [ ] Date/time dùng ISO 8601.
- [ ] Mọi response quan trọng có `updatedAt`.
- [ ] ID và code được tách biệt.
- [ ] Enum dùng key ổn định, label dùng field riêng.
- [ ] KPI trả value thô, semantic trend và comparison label.
- [ ] Chart trả granularity và series rõ ràng.
- [ ] Order row tách payment status và order status.
- [ ] Backend trả available actions hoặc workflow metadata.
- [ ] Inventory phân biệt available, reserved và minimum quantity.
- [ ] Notification tách read, resolved và dismissed.
- [ ] Error có code, userMessage và retryable.
- [ ] Permission scope áp dụng ở backend.
- [ ] Filter có thể serialize vào URL.
- [ ] Null, zero và unavailable được phân biệt.
- [ ] Mock data tuân thủ contract thật.
- [ ] Có runtime validation hoặc contract tests cho dữ liệu quan trọng.
- [ ] Breaking changes được version hóa.

---

## 44. Những điều không được làm

- Không trả label tiếng Việt làm enum duy nhất.
- Không trả ngày giờ theo format hiển thị làm dữ liệu nguồn.
- Không dùng `stock` chung chung nếu nghiệp vụ cần nhiều loại tồn.
- Không để frontend tự suy quyền từ tên vai trò.
- Không để frontend tự suy mọi action chỉ từ status.
- Không trả file export lớn dưới dạng base64 JSON.
- Không coi null và zero là một.
- Không đổi enum âm thầm.
- Không trả dữ liệu ngoài branch scope rồi chỉ ẩn ở frontend.
- Không đặt mock data trực tiếp trong JSX production.

---

## 45. Kết luận

Data contracts của Cynca VLXD Dashboard phải làm rõ dữ liệu nào được trả, ý nghĩa của từng field, quyền truy cập, độ mới của dữ liệu và cách xử lý lỗi. Frontend và backend phải dùng cùng một contract để giao diện hiển thị chính xác, an toàn và có thể mở rộng.

File tiếp theo đề xuất:

```text
17-Visual-QA-Cases.md
```