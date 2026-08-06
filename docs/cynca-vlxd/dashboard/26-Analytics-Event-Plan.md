# Cynca VLXD Admin Dashboard — Analytics Event Plan

> Phiên bản: 1.0  
> Phạm vi: Kế hoạch đo lường hành vi sử dụng Dashboard quản trị  
> Đối tượng sử dụng: Product, Frontend, Backend, Data, QA, Security và AI coding agent  
> Tài liệu liên quan: `20-Frontend-Architecture.md`, `23-Security-Privacy.md`, `24-Observability.md`

---

## 1. Mục tiêu

Analytics giúp đội sản phẩm hiểu người dùng đang sử dụng Dashboard như thế nào, tính năng nào có giá trị và luồng nào đang gây khó khăn.

Kế hoạch này phải trả lời được:

- Người dùng có mở Dashboard thường xuyên không?
- KPI, biểu đồ và widget nào được sử dụng nhiều?
- Người dùng có tìm được đơn hàng, sản phẩm và khách hàng không?
- Có bao nhiêu người hoàn thành các tác vụ chính?
- Luồng nào bị bỏ dở?
- Filter, branch, date range và export có được sử dụng đúng không?
- Thông báo có dẫn đến hành động hay chỉ tạo nhiễu?
- Vai trò nào cần giao diện hoặc dữ liệu khác nhau?

Analytics không được thay thế audit log, error monitoring hoặc business database.

---

## 2. Nguyên tắc đo lường

1. Chỉ thu thập sự kiện có câu hỏi sản phẩm rõ ràng.
2. Không thu thập PII hoặc dữ liệu tài chính chi tiết không cần thiết.
3. Tên event phải ổn định và có schema.
4. Không gửi event trùng lặp do re-render.
5. Phân biệt view, intent, action, success và failure.
6. Backend là nguồn chuẩn cho kết quả nghiệp vụ quan trọng.
7. Mọi event phải có owner và mục đích sử dụng.
8. Analytics failure không được làm hỏng luồng chính.

---

## 3. Phân biệt analytics và observability

### Analytics

Trả lời:

- Người dùng làm gì?
- Tính năng nào được sử dụng?
- Tỷ lệ hoàn thành ra sao?

### Observability

Trả lời:

- Hệ thống có lỗi không?
- API có chậm không?
- Release nào gây regression?

Một action có thể tạo cả hai loại event nhưng phải dùng schema và hệ thống xử lý riêng.

---

## 4. Naming convention

Dùng cấu trúc:

```text
domain.object.action
```

Ví dụ:

```text
dashboard.page.viewed
dashboard.filter.applied
orders.quick_view.opened
orders.status_update.submitted
orders.status_update.succeeded
reports.export.requested
search.result.selected
notifications.item.opened
```

Không dùng:

```text
click_button
page1
track_event
new_event
```

---

## 5. Event envelope

```ts
export type AnalyticsEvent = {
  eventName: string;
  eventId: string;
  occurredAt: string;
  sessionId: string;
  anonymousUserId?: string;
  userIdHash?: string;
  userRole?: string;
  branchScope?: string;
  route: string;
  release: string;
  environment: "development" | "staging" | "production";
  properties?: Record<string, string | number | boolean | null>;
};
```

Không dùng email, số điện thoại hoặc tên khách hàng làm identifier.

---

## 6. Common properties

Các thuộc tính chung:

```text
route
release
viewport_group
user_role
branch_scope_type
permission_profile
entry_source
```

`viewport_group`:

```text
mobile
tablet
laptop
desktop
wide
```

`branch_scope_type`:

```text
single_branch
multi_branch
all_branches
```

Không dùng branch name hoặc user ID có cardinality cao làm metric dimension nếu không cần.

---

## 7. Event lifecycle

Với action nghiệp vụ quan trọng, dùng chuỗi:

```text
opened
started
submitted
succeeded
failed
cancelled
```

Ví dụ:

```text
reports.export.opened
reports.export.requested
reports.export.succeeded
reports.export.failed
```

Không chỉ ghi `button_clicked` vì không biết tác vụ có hoàn thành hay không.

---

## 8. Page events

### `dashboard.page.viewed`

Khi Dashboard render thành công lần đầu.

Properties:

```text
entry_source
sidebar_state
initial_date_range
has_finance_permission
widget_count_visible
```

Không gửi lại khi component re-render.

### `dashboard.page.ready`

Khi các vùng P0 đã tải hoặc có trạng thái kết thúc rõ ràng.

Properties:

```text
time_to_ready_ms
success_widget_count
failed_widget_count
stale_widget_count
```

---

## 9. Sidebar events

### `navigation.sidebar.collapsed`
### `navigation.sidebar.expanded`
### `navigation.sidebar.item_selected`

Properties:

```text
item_key
menu_group
current_route
target_route
```

Không gửi label tiếng Việt làm key chuẩn; dùng key ổn định như `orders`, `inventory`, `reports`.

---

## 10. Header events

### `dashboard.date_range.opened`
### `dashboard.date_range.applied`
### `dashboard.branch.changed`
### `dashboard.refresh.requested`

Date range properties:

```text
preset
range_days
compare_mode
```

Không gửi ngày cụ thể nếu không cần cho phân tích sản phẩm.

Branch change properties:

```text
from_scope_type
to_scope_type
```

---

## 11. KPI events

### `dashboard.kpi.viewed`

Chỉ gửi nếu KPI thực sự xuất hiện trong viewport theo ngưỡng thống nhất.

Properties:

```text
kpi_key
value_type
state
```

`state`:

```text
loaded
empty
error
stale
permission_hidden
```

### `dashboard.kpi.opened`

Khi người dùng click KPI để xem báo cáo chi tiết.

Properties:

```text
kpi_key
target_route
current_date_range
```

Không gửi giá trị doanh thu hoặc lợi nhuận cụ thể.

---

## 12. Chart events

### `dashboard.chart.viewed`
### `dashboard.chart.filter_changed`
### `dashboard.chart.legend_toggled`
### `dashboard.chart.drilldown_opened`

Properties:

```text
chart_key
granularity
series_count
filter_key
drilldown_target
```

Không gửi từng hover tooltip vì tạo noise lớn.

---

## 13. Product widget events

### `products.performance.viewed`
### `products.performance.metric_changed`
### `products.performance.item_opened`
### `inventory.alert.opened`
### `inventory.alert.action_started`
### `inventory.alert.action_succeeded`
### `inventory.alert.action_failed`

Properties:

```text
metric
rank_bucket
alert_type
action_type
warehouse_scope_type
```

`rank_bucket`:

```text
1
2_3
4_5
6_10
```

Không gửi product name hoặc SKU vào analytics mặc định.

---

## 14. Orders table events

### `orders.table.viewed`
### `orders.filter.applied`
### `orders.sort.changed`
### `orders.row.opened`
### `orders.quick_view.opened`
### `orders.quick_view.closed`

Properties:

```text
status_filter
sort_field
sort_direction
row_position
order_status
payment_status
```

Không gửi mã đơn hoặc tên khách hàng.

---

## 15. Order mutation events

### `orders.status_update.started`
### `orders.status_update.submitted`
### `orders.status_update.succeeded`
### `orders.status_update.failed`
### `orders.status_update.cancelled`

Properties:

```text
from_status
to_status
action_type
confirmation_required
failure_code
```

Success nên được xác nhận từ response backend hoặc server event, không chỉ từ optimistic UI.

---

## 16. Search events

### `search.opened`
### `search.query_submitted`
### `search.results_received`
### `search.result_selected`
### `search.abandoned`
### `search.failed`

Properties:

```text
query_length
query_type_hint
result_count_bucket
result_type
selection_position
response_time_bucket
failure_code
```

Không gửi nguyên văn query vì có thể chứa PII.

Result count bucket:

```text
0
1_5
6_10
11_plus
```

---

## 17. Notification events

### `notifications.dropdown.opened`
### `notifications.filter.changed`
### `notifications.item.opened`
### `notifications.item.marked_read`
### `notifications.all_marked_read`
### `notifications.action_started`
### `notifications.action_succeeded`

Properties:

```text
category
severity
read_state
action_type
notification_age_bucket
```

Không gửi notification title hoặc description.

---

## 18. Export events

### `reports.export.opened`
### `reports.export.requested`
### `reports.export.queued`
### `reports.export.succeeded`
### `reports.export.failed`
### `reports.export.downloaded`

Properties:

```text
report_type
format
range_days
scope_type
column_count_bucket
job_mode
failure_code
file_size_bucket
```

Không gửi download URL hoặc file name chứa dữ liệu nhạy cảm.

---

## 19. Empty, error và retry events

### `dashboard.widget.empty_viewed`
### `dashboard.widget.error_viewed`
### `dashboard.widget.retry_clicked`
### `dashboard.widget.retry_succeeded`
### `dashboard.widget.retry_failed`

Properties:

```text
widget_key
state_reason
error_code
retry_count_bucket
```

Không gửi raw error message nếu có dữ liệu động hoặc nội bộ.

---

## 20. Permission events

### `permissions.action_hidden`

Không khuyến nghị gửi với mọi render vì tạo volume lớn.

Chỉ dùng trong testing hoặc sampling khi cần đánh giá thiết kế theo vai trò.

### `permissions.denied_viewed`
### `permissions.request_blocked`

Properties:

```text
permission_key
resource_type
entry_source
```

Không dùng analytics thay audit/security log cho truy cập trái phép.

---

## 21. Onboarding events

Áp dụng cho tài khoản mới hoặc Dashboard chưa có dữ liệu.

### `onboarding.dashboard.viewed`
### `onboarding.action_selected`
### `onboarding.step_completed`
### `onboarding.completed`

Properties:

```text
step_key
action_key
account_state
```

---

## 22. Funnel chính

### Funnel tìm và xử lý đơn hàng

```text
search.opened
→ search.query_submitted
→ search.result_selected
→ orders.quick_view.opened
→ orders.status_update.submitted
→ orders.status_update.succeeded
```

### Funnel cảnh báo tồn kho

```text
inventory.alert.opened
→ inventory.alert.action_started
→ inventory.alert.action_succeeded
```

### Funnel export báo cáo

```text
reports.export.opened
→ reports.export.requested
→ reports.export.succeeded
→ reports.export.downloaded
```

### Funnel notification đến hành động

```text
notifications.dropdown.opened
→ notifications.item.opened
→ notifications.action_started
→ notifications.action_succeeded
```

---

## 23. Product metrics đề xuất

### Dashboard adoption

```text
Weekly Active Dashboard Users
Dashboard sessions per user
Dashboard return rate
```

### Task completion

```text
Order update completion rate
Inventory alert action completion rate
Export completion rate
Search selection rate
```

### Efficiency

```text
Median time from Dashboard open to first useful action
Median time to find an order
Median time to complete order action
```

### Feature value

```text
KPI open rate
Chart drilldown rate
Notification action rate
Best-product widget open rate
```

---

## 24. Segmentation

Phân tích theo:

- User role.
- Permission profile.
- Viewport group.
- Branch scope type.
- Release.
- New vs returning user.
- Dashboard state: full, empty, partial error, stale.

Không phân đoạn theo thuộc tính có thể tái nhận diện cá nhân nếu không cần.

---

## 25. Event source of truth

### Client-side phù hợp cho

- Page viewed.
- Dropdown opened.
- Filter interaction.
- Chart drilldown.
- Search abandoned.

### Server-side phù hợp cho

- Order update succeeded.
- Export completed.
- Inventory action completed.
- Permission denied.
- Background job result.

Với action quan trọng, ưu tiên server event làm nguồn chuẩn.

---

## 26. Deduplication

Mỗi event có `eventId` duy nhất.

Quy tắc:

- Page view chỉ gửi một lần mỗi route entry.
- Success event không gửi cả từ client và server nếu không có cơ chế dedupe.
- Retry phải có attempt number hoặc retry bucket.
- Real-time reconnect không được gửi lại conversion cũ.

---

## 27. Event batching

Có thể batch event nhẹ để giảm request, nhưng:

- Không giữ quá lâu.
- Flush trước unload nếu nền tảng hỗ trợ.
- Không làm chậm action người dùng.
- Critical business completion nên gửi server-side.

Analytics request phải dùng endpoint riêng và không chặn UI.

---

## 28. Privacy rules

Không gửi:

- Tên khách hàng.
- Số điện thoại.
- Email.
- Địa chỉ.
- Mã đơn.
- SKU hoặc tên sản phẩm nếu không được phê duyệt.
- Giá trị doanh thu/lợi nhuận cụ thể.
- Search query nguyên văn.
- Notification content.
- Token, cookie hoặc URL ký số.

Có thể gửi enum, bucket và key nội bộ ổn định.

---

## 29. Consent và policy

Với Dashboard nội bộ, analytics vẫn phải:

- Có mục đích rõ ràng.
- Được mô tả trong policy nội bộ.
- Giới hạn quyền truy cập dữ liệu analytics.
- Có retention phù hợp.
- Không dùng cho đánh giá cá nhân ngoài mục đích đã công bố nếu chưa được phê duyệt.

Không mặc định dùng analytics để giám sát hiệu suất cá nhân nhân viên.

---

## 30. Retention

Gợi ý cần được phê duyệt:

- Raw events: thời gian ngắn đến trung bình.
- Aggregated metrics: dài hơn.
- Failed event logs: đủ để điều tra.
- User-level identifiers: tối thiểu cần thiết.

Retention thực tế phải phù hợp privacy, chi phí và mục tiêu phân tích.

---

## 31. Data quality rules

Mỗi event cần kiểm tra:

- Event name hợp lệ.
- Required properties đầy đủ.
- Enum đúng.
- Timestamp hợp lệ.
- Environment đúng.
- Không có PII.
- Không gửi trùng.
- Volume trong ngưỡng.

---

## 32. Event schema registry

Nên có file hoặc registry tập trung:

```text
analytics/
├── event-names.ts
├── event-schemas.ts
├── track.ts
├── privacy-redaction.ts
└── README.md
```

Ví dụ:

```ts
export const analyticsEvents = {
  dashboardViewed: "dashboard.page.viewed",
  orderQuickViewOpened: "orders.quick_view.opened",
  exportRequested: "reports.export.requested"
} as const;
```

Không viết tên event string rải rác trong component.

---

## 33. Typed tracking API

```ts
export type AnalyticsEventMap = {
  "dashboard.page.viewed": {
    entrySource: string;
    sidebarState: "expanded" | "collapsed" | "drawer";
  };
  "orders.quick_view.opened": {
    orderStatus: string;
    rowPosition: number;
  };
  "reports.export.requested": {
    reportType: string;
    format: "csv" | "xlsx" | "pdf";
    rangeDays: number;
  };
};

export function trackEvent<K extends keyof AnalyticsEventMap>(
  name: K,
  properties: AnalyticsEventMap[K]
): void;
```

---

## 34. Implementation rules

- Tracking không nằm trực tiếp trong UI primitive chung nếu không có semantic context.
- Feature component chịu trách nhiệm event nghiệp vụ.
- Event success không phát trước backend success.
- Analytics failure phải được nuốt an toàn và log ở mức phù hợp.
- Không dùng `console.log` thay analytics client.
- Không track hover thông thường.

---

## 35. Testing analytics

Unit test:

- Event name đúng.
- Required properties đúng.
- PII redaction.
- Dedupe.

Component/integration test:

- Click action gửi event một lần.
- Failed mutation gửi failed event, không gửi succeeded.
- Page re-render không tạo thêm page view.
- Keyboard và mouse tạo cùng semantic event.

E2E:

- Funnel chính có event theo đúng thứ tự.
- User logout clear analytics context.

---

## 36. Analytics QA checklist

- [ ] Event chỉ gửi một lần.
- [ ] Tên đúng registry.
- [ ] Properties đúng schema.
- [ ] Không có PII.
- [ ] Success/failure chính xác.
- [ ] Role và viewport được gắn đúng.
- [ ] Release metadata tồn tại.
- [ ] Staging và production tách biệt.
- [ ] Event không làm chậm UI.
- [ ] Ad blocker hoặc analytics failure không làm lỗi ứng dụng.

---

## 37. Dashboard phân tích đề xuất

### Adoption dashboard

- DAU/WAU/MAU Dashboard.
- Sessions per role.
- Returning users.

### Task dashboard

- Order action funnel.
- Inventory alert funnel.
- Export funnel.
- Search funnel.

### Feature dashboard

- KPI opens.
- Chart drilldowns.
- Notification actions.
- Filter usage.

### Quality dashboard

- Duplicate event rate.
- Missing property rate.
- Client/server success mismatch.
- Event volume by release.

---

## 38. Alert cho analytics quality

Có thể cảnh báo khi:

- Event volume giảm về 0 sau release.
- Event tăng gấp nhiều lần bất thường.
- Required property missing tăng.
- Duplicate rate tăng.
- Production event xuất hiện trong staging project hoặc ngược lại.
- PII scanner phát hiện dữ liệu nhạy cảm.

---

## 39. Governance

Mỗi event mới cần:

- Câu hỏi sản phẩm.
- Owner.
- Event name.
- Trigger.
- Properties.
- Privacy review.
- Retention.
- Dashboard hoặc báo cáo sử dụng.
- Kế hoạch deprecate nếu thay thế.

Không thêm event chỉ vì “có thể sau này cần”.

---

## 40. Event deprecation

Khi bỏ event:

1. Đánh dấu deprecated.
2. Xác định report phụ thuộc.
3. Duy trì song song nếu cần migration.
4. Xóa tracking code.
5. Xóa schema sau thời gian chuyển tiếp.
6. Cập nhật tài liệu.

Không đổi nghĩa event cũ mà giữ nguyên tên.

---

## 41. AI coding agent requirements

AI phải:

- Dùng event registry, không viết tên event tùy ý.
- Không gửi PII hoặc giá trị tài chính chi tiết.
- Không track mỗi render hoặc hover.
- Không gửi success trước backend confirmation.
- Báo rõ event mới hoặc event đã sửa.
- Thêm test cho event quan trọng.
- Không tự thêm analytics vendor hoặc SDK mới nếu chưa được yêu cầu.
- Không dùng analytics làm audit log.

---

## 42. Acceptance checklist

- [ ] Event naming thống nhất.
- [ ] Có event envelope và common properties.
- [ ] Page, navigation, filter, KPI và chart được đo lường.
- [ ] Product, inventory, orders, search, notification và export có event plan.
- [ ] Action quan trọng có started/succeeded/failed.
- [ ] Funnel chính được xác định.
- [ ] Product metrics và segmentation rõ ràng.
- [ ] Client/server source of truth được phân biệt.
- [ ] Có deduplication.
- [ ] Không thu PII hoặc dữ liệu tài chính chi tiết.
- [ ] Có typed tracking API.
- [ ] Có schema registry.
- [ ] Có test và QA checklist.
- [ ] Có analytics quality monitoring.
- [ ] Có governance và deprecation policy.

---

## 43. Những điều không được làm

- Không track mọi click.
- Không gửi search query nguyên văn.
- Không gửi mã đơn, số điện thoại hoặc địa chỉ.
- Không gửi giá trị doanh thu/lợi nhuận cụ thể.
- Không dùng event name tùy ý trong từng component.
- Không gửi page view mỗi lần re-render.
- Không coi optimistic UI là business success.
- Không dùng analytics thay audit hoặc observability.
- Không thêm vendor mới thiếu privacy review.
- Không dùng analytics để đánh giá cá nhân ngoài mục đích đã phê duyệt.

---

## 44. Kết luận

Analytics Event Plan của Cynca VLXD Dashboard phải đo đúng hành vi có giá trị, bảo vệ quyền riêng tư và giúp đội sản phẩm cải thiện luồng công việc thực tế. Mọi sự kiện cần có mục đích, schema, owner và quy tắc sử dụng rõ ràng; số lượng event nhiều không đồng nghĩa hệ thống đo lường tốt.

File tiếp theo đề xuất:

```text
README.md
27-Content-Guidelines.md
```