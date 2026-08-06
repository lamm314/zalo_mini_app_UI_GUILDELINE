# Cynca VLXD Admin Dashboard — Analytics Event Plan

> Phiên bản: 1.0  
> Phạm vi: Kế hoạch sự kiện phân tích hành vi và hiệu quả sử dụng Dashboard quản trị  
> Đối tượng sử dụng: Product, Frontend, Backend, Data, QA, Security và AI coding agent  
> Tài liệu liên quan: `20-Frontend-Architecture.md`, `23-Security-Privacy.md`, `24-Observability.md`

---

## 1. Mục tiêu

Analytics Event Plan giúp đội sản phẩm hiểu người dùng đang sử dụng Dashboard như thế nào, tính năng nào tạo giá trị và điểm nào gây cản trở.

Kế hoạch này phải trả lời được:

- Người dùng có tải Dashboard thành công không?
- KPI, chart và widget nào được sử dụng nhiều?
- Người dùng có hoàn thành luồng tìm kiếm, xử lý đơn và xuất báo cáo không?
- Filter nào thường được dùng?
- Vai trò nào gặp nhiều lỗi nhất?
- Tính năng mới có cải thiện hiệu quả công việc không?
- Có điểm rơi nào khiến người dùng bỏ dở thao tác?

Analytics không được thay thế audit log hoặc observability. Analytics tập trung vào hành vi sản phẩm ở mức tổng hợp.

---

## 2. Nguyên tắc cốt lõi

### 2.1 Chỉ thu sự kiện có giá trị quyết định

Không ghi mọi click nếu dữ liệu đó không giúp cải thiện sản phẩm.

### 2.2 Không thu PII không cần thiết

Không gửi tên khách hàng, số điện thoại, email, địa chỉ hoặc nội dung tìm kiếm nhạy cảm.

### 2.3 Event name phải ổn định

Không đổi tên event tùy tiện sau khi dashboard phân tích đã được xây dựng.

### 2.4 Phân biệt intent và outcome

Ví dụ:

```text
reports.export.requested
reports.export.completed
reports.export.failed
```

Không chỉ ghi sự kiện click nút export.

### 2.5 Có version schema

Event thay đổi cấu trúc phải có version hoặc migration rõ ràng.

---

## 3. Phân biệt Analytics, Observability và Audit

| Loại | Mục tiêu | Ví dụ |
|---|---|---|
| Analytics | Hiểu hành vi sử dụng | Người dùng mở quick-view đơn |
| Observability | Vận hành kỹ thuật | Quick-view API mất 2,5 giây |
| Audit | Kiểm soát hành động nhạy cảm | Ai đã hủy đơn nào |

Một workflow có thể tạo cả ba loại sự kiện nhưng payload và retention khác nhau.

---

## 4. Event naming convention

Dùng cấu trúc:

```text
domain.object.action
```

Ví dụ:

```text
dashboard.page.viewed
dashboard.filter.applied
kpi.card.opened
orders.quick_view.opened
reports.export.completed
notifications.item.read
```

Quy tắc:

- Chữ thường.
- Dùng dấu chấm.
- Động từ ở quá khứ cho sự kiện đã xảy ra.
- Không dùng tên chung như `button_clicked`, `event1`, `action_done`.

---

## 5. Event envelope chuẩn

```ts
export type AnalyticsEvent = {
  eventName: string;
  eventVersion: number;
  occurredAt: string;
  anonymousUserId?: string;
  sessionId?: string;
  userRole?: string;
  branchScope?: "all" | "single" | "multiple";
  route: string;
  release: string;
  environment: "development" | "staging" | "production";
  properties?: Record<string, string | number | boolean | null>;
};
```

Không dùng email hoặc phone làm user identifier.

---

## 6. Common properties

Các thuộc tính dùng chung:

```text
route
release
viewport_group
user_role
branch_scope
entry_source
feature_flag_variant
```

`viewport_group`:

```text
mobile
tablet
laptop
desktop
wide
```

Không gửi kích thước viewport chính xác nếu không cần.

---

## 7. Privacy-safe properties

Được phép:

- Số lượng kết quả.
- Loại widget.
- Trạng thái workflow.
- Loại filter.
- Khoảng thời gian dạng preset.
- Loại báo cáo.
- Format export.
- Error code chuẩn hóa.

Không được gửi:

- Tên khách hàng.
- Mã đơn đầy đủ nếu không thật sự cần.
- Nội dung query tìm kiếm nguyên văn.
- Số điện thoại.
- Địa chỉ.
- Giá trị tài chính gắn với entity cụ thể.

---

## 8. Page lifecycle events

### `dashboard.page.viewed`

Gửi khi Dashboard đã render shell và nội dung chính sẵn sàng.

Properties:

```ts
{
  load_state: "success" | "partial" | "empty" | "error";
  widget_count: number;
  visible_widget_count: number;
  sidebar_state: "expanded" | "collapsed" | "drawer";
}
```

### `dashboard.page.exited`

Chỉ dùng nếu nền tảng analytics hỗ trợ đáng tin cậy.

Properties:

```text
time_on_page_bucket
interaction_count
```

Không phụ thuộc tuyệt đối vào exit event vì browser có thể không gửi kịp.

---

## 9. Dashboard filter events

### `dashboard.filter.opened`

Properties:

```text
filter_type: date_range | branch | warehouse | compare_mode
```

### `dashboard.filter.applied`

Properties:

```ts
{
  filter_type: string;
  preset?: "today" | "7_days" | "30_days" | "this_month" | "this_quarter" | "this_year" | "custom";
  compare_mode?: "previous_period" | "previous_year" | "none";
  result_state: "success" | "empty" | "error";
  refresh_duration_bucket?: string;
}
```

Không gửi ngày cụ thể nếu không cần cho phân tích sản phẩm.

### `dashboard.filter.cleared`

Gửi khi người dùng quay về mặc định.

---

## 10. Sidebar events

### `navigation.sidebar.collapsed`
### `navigation.sidebar.expanded`
### `navigation.sidebar.item_selected`

Properties:

```text
item_key
item_group
source_state
```

Không gửi label tùy biến nếu có `item_key` ổn định.

### `navigation.sidebar.drawer_opened`

Dùng cho viewport dưới 1024px.

---

## 11. Header events

### `header.search.opened`
### `header.notifications.opened`
### `header.user_menu.opened`
### `header.export_menu.opened`

Chỉ ghi open event cho control quan trọng, không cần ghi mọi hover.

---

## 12. KPI events

### `kpi.card.viewed`

Không bắt buộc gửi cho mọi lần render nếu gây volume lớn. Có thể gửi một event tổng hợp.

Properties:

```text
kpi_key
state: success | stale | empty | error
```

### `kpi.card.opened`

Gửi khi người dùng click để drill-down.

Properties:

```text
kpi_key
comparison_mode
source_scope
```

### `kpi.tooltip.opened`

Chỉ theo dõi khi Product cần đánh giá khả năng hiểu KPI.

---

## 13. Chart events

### `chart.view_changed`

Properties:

```text
chart_key
control_type
selected_value
```

### `chart.legend_toggled`

Properties:

```text
chart_key
series_key
visible
```

### `chart.drilldown.opened`

Properties:

```text
chart_key
point_granularity
source_filter
```

Không gửi giá trị doanh thu cụ thể hoặc timestamp chi tiết nếu không cần.

---

## 14. Product widget events

### `products.widget.metric_changed`

Properties:

```text
metric: revenue | quantity | gross_profit
```

### `products.item.opened`

Properties:

```text
source_widget
rank_bucket: top_1 | top_3 | top_5 | other
product_category_key
```

Không gửi product name nếu category key đủ phục vụ phân tích.

### `inventory.alert.action_started`

Properties:

```text
alert_type
source_widget
selected_action
```

### `inventory.alert.action_completed`
### `inventory.alert.action_failed`

Properties thêm:

```text
result
error_code
```

---

## 15. Orders events

### `orders.table.filtered`

Properties:

```text
status_filter
result_count_bucket
```

### `orders.table.sorted`

Properties:

```text
sort_field
sort_direction
```

### `orders.quick_view.opened`

Properties:

```text
source: dashboard_table | search | notification
order_status
payment_status
```

Không gửi order ID đầy đủ vào analytics.

### `orders.action.started`
### `orders.action.completed`
### `orders.action.failed`

Properties:

```text
action_key
from_status
to_status
source
error_code
```

Các action nhạy cảm vẫn cần audit log riêng.

---

## 16. Search events

### `search.query.submitted`

Properties:

```ts
{
  query_length_bucket: "1_2" | "3_5" | "6_10" | "11_plus";
  requested_types: string;
  result_count_bucket: "0" | "1_5" | "6_10" | "11_plus";
  response_time_bucket?: string;
}
```

Không gửi query nguyên văn.

### `search.result.selected`

Properties:

```text
result_type
result_position_bucket
input_method: mouse | keyboard
```

### `search.abandoned`

Gửi khi người dùng nhập nhưng đóng mà không chọn kết quả, nếu đo được ổn định.

---

## 17. Notification events

### `notifications.dropdown.opened`

Properties:

```text
unread_count_bucket
actionable_count_bucket
```

### `notifications.filter.changed`

```text
filter: all | unread | actionable
```

### `notifications.item.opened`

Properties:

```text
category
severity
read_state
```

### `notifications.item.read`
### `notifications.all_marked_read`

Không gửi notification description.

---

## 18. Export events

### `reports.export.requested`

Properties:

```text
report_type
format
scope_type
row_count_bucket_if_known
```

### `reports.export.queued`
### `reports.export.completed`
### `reports.export.failed`
### `reports.export.downloaded`

Properties:

```text
report_type
format
duration_bucket
file_size_bucket
error_code
```

Không gửi signed URL hoặc file name chứa dữ liệu nhạy cảm.

---

## 19. Error-state interactions

### `ui.error.retry_clicked`

Properties:

```text
component_key
error_code
retry_number_bucket
```

### `ui.empty_state.action_clicked`

Properties:

```text
component_key
action_key
empty_reason
```

Các event này giúp đánh giá lỗi nào gây nhiều hành động retry và onboarding nào hiệu quả.

---

## 20. Permission events

### `permission.ui_restricted_viewed`

Chỉ dùng khi Product cần đánh giá nhu cầu quyền.

Properties:

```text
feature_key
restriction_mode: hidden | disabled | denied_page
user_role
```

Không gửi event mỗi lần component bị ẩn trong background. Chỉ gửi khi người dùng chủ động truy cập hoặc thấy trạng thái bị giới hạn.

### `permission.access_denied`

Analytics chỉ ghi tổng hợp. Security/observability phải ghi chi tiết riêng.

---

## 21. Onboarding events

Dùng cho tài khoản mới chưa có dữ liệu.

### `onboarding.dashboard.viewed`
### `onboarding.primary_action.clicked`
### `onboarding.step.completed`

Properties:

```text
step_key
entry_source
account_state
```

---

## 22. Feature flag events

Mỗi event liên quan tính năng thử nghiệm nên có:

```text
feature_flag_key
feature_flag_variant
```

Không tạo event riêng chỉ để ghi exposure nếu hệ thống experimentation đã tự xử lý.

Nếu cần:

### `experiment.exposure.recorded`

Properties:

```text
experiment_key
variant
```

---

## 23. Session-level derived metrics

Từ event có thể tính:

- Dashboard engagement rate.
- Filter usage rate.
- KPI drill-down rate.
- Search success rate.
- Quick-view completion rate.
- Order action success rate.
- Export completion rate.
- Notification action rate.
- Retry recovery rate.

Không cần gửi các metric dẫn xuất trực tiếp từ frontend nếu warehouse có thể tính ổn định.

---

## 24. Funnel đề xuất

### Funnel 1 — Tìm và xử lý đơn

```text
search.query.submitted
→ search.result.selected
→ orders.quick_view.opened
→ orders.action.started
→ orders.action.completed
```

### Funnel 2 — Xử lý cảnh báo tồn kho

```text
inventory.alert.action_started
→ inventory.alert.action.completed
```

### Funnel 3 — Xuất báo cáo

```text
reports.export.requested
→ reports.export.queued
→ reports.export.completed
→ reports.export.downloaded
```

### Funnel 4 — Drill-down KPI

```text
dashboard.page.viewed
→ kpi.card.opened
→ report.page.viewed
```

---

## 25. Success metrics đề xuất

### Dashboard adoption

```text
Weekly active dashboard users
Dashboard sessions per role
Return usage rate
```

### Efficiency

```text
Median time to find an order
Median time from alert view to action
Export completion time
Search result selection rate
```

### Reliability from product perspective

```text
Partial-load session rate
Retry rate
Action failure rate
Search zero-result rate
```

### Discoverability

```text
KPI drill-down rate
Notification action rate
Feature usage by role
```

---

## 26. Event ownership

| Domain | Owner chính | Owner phối hợp |
|---|---|---|
| Dashboard page | Product + Frontend | Data |
| KPI/Charts | Product Analytics | Frontend |
| Orders | Operations Product | Backend/Frontend |
| Inventory | Warehouse Product | Backend |
| Search | Product + Backend | Data |
| Export | Product/Operations | Backend |
| Notifications | Product | Frontend/Backend |
| Privacy review | Security/Legal | Product/Data |

---

## 27. Event schema registry

Nên duy trì registry:

```text
docs/analytics/
├── events.yaml
├── properties.yaml
└── changelog.md
```

Mỗi event gồm:

- Tên.
- Version.
- Mô tả.
- Trigger.
- Properties.
- Owner.
- Privacy classification.
- Retention.
- Status: proposed, active, deprecated.

---

## 28. Event versioning

Khi thêm optional property:

- Có thể giữ cùng version.

Khi đổi meaning hoặc xóa/đổi tên required property:

- Tăng version.
- Duy trì migration trong dashboard phân tích.

Không âm thầm đổi ý nghĩa event cũ.

---

## 29. Event deduplication

Các event outcome phải có cơ chế tránh gửi trùng.

Có thể dùng:

```text
event_id
workflow_id
job_id
```

Ví dụ export completed chỉ gửi một lần dù UI reconnect nhiều lần.

---

## 30. Offline và retry

Nếu analytics SDK retry:

- Giới hạn queue.
- Không block UI.
- Không gửi trùng outcome.
- Xóa event quá cũ nếu không còn ý nghĩa.
- Không persist payload nhạy cảm trong browser storage.

---

## 31. Consent và legal review

Tùy mô hình sử dụng và khu vực pháp lý, cần xác định:

- Analytics nào là cần thiết cho vận hành.
- Analytics nào cần thông báo hoặc consent.
- Retention bao lâu.
- Third-party provider nào được dùng.
- Dữ liệu có ra ngoài khu vực hay không.

Không tự tuyên bố tuân thủ nếu chưa được bộ phận pháp lý xác nhận.

---

## 32. Data retention

Retention tham khảo:

- Raw behavioral events: ngắn đến trung bình.
- Aggregated product metrics: dài hơn.
- Experiment exposure: theo vòng đời thí nghiệm.

Retention thực tế phải theo privacy policy và nhu cầu phân tích.

---

## 33. Analytics data quality checks

Kiểm tra định kỳ:

- Event volume đột biến hoặc giảm về 0.
- Required property bị thiếu.
- Enum lạ.
- Duplicate rate.
- Timestamp lệch.
- Event chỉ xuất hiện ở một browser.
- Event production lẫn staging.
- Role phân bố bất thường.

---

## 34. Event validation trong code

Có thể dùng type hoặc schema:

```ts
export type DashboardFilterAppliedEvent = {
  eventName: "dashboard.filter.applied";
  eventVersion: 1;
  properties: {
    filter_type: "date_range" | "branch" | "warehouse" | "compare_mode";
    preset?: string;
    result_state: "success" | "empty" | "error";
  };
};
```

Nên có helper tập trung:

```ts
trackAnalyticsEvent(event)
```

Không gọi SDK analytics trực tiếp rải rác khắp component.

---

## 35. Analytics adapter

```ts
export interface AnalyticsClient {
  track(event: AnalyticsEvent): void;
  identify(user: AnalyticsIdentity): void;
  reset(): void;
}
```

Adapter giúp:

- Đổi provider.
- Redact dữ liệu.
- Gắn common properties.
- Tắt analytics ở test/local.
- Kiểm tra schema.

---

## 36. User identity lifecycle

Sau login:

- Dùng internal identifier hoặc hash.
- Gắn role và scope tổng quát.

Sau logout:

- Gọi `reset()`.
- Xóa session analytics context.
- Không gắn event tài khoản mới vào user cũ.

Không dùng email hoặc phone làm analytics ID mặc định.

---

## 37. Sampling

Các event volume cao có thể sampling:

- Widget viewed.
- Tooltip opened.
- Sidebar interaction.

Không sampling outcome quan trọng như:

- Export completed/failed.
- Order action completed/failed.
- Permission denied quan trọng.

Sampling phải nhất quán và được ghi trong tài liệu metric.

---

## 38. Analytics dashboards đề xuất

### Product Adoption

- Active users.
- Sessions by role.
- Widget usage.
- Feature adoption.

### Order Operations

- Search-to-action funnel.
- Quick-view usage.
- Action success/failure.

### Inventory Operations

- Alert interaction.
- Time to action.
- Most frequent alert types.

### Reporting

- Export requests by format.
- Completion rate.
- Download rate.

### UX Friction

- Zero-result search.
- Retry clicks.
- Partial-load sessions.
- Permission denied journeys.

---

## 39. Event test cases

Bắt buộc kiểm tra:

- Event gửi đúng một lần.
- Event không gửi trước khi outcome thành công.
- Common properties đầy đủ.
- Không có PII.
- Logout reset identity.
- Staging không vào production dataset.
- Retry không tạo duplicate.
- Keyboard và mouse action không gửi hai event cùng lúc.
- Feature flag variant đúng.

---

## 40. QA checklist cho analytics

- [ ] Trigger đúng thời điểm.
- [ ] Event name đúng registry.
- [ ] Version đúng.
- [ ] Required properties đủ.
- [ ] Enum hợp lệ.
- [ ] Không có dữ liệu nhạy cảm.
- [ ] Outcome event phản ánh backend result.
- [ ] Không duplicate.
- [ ] Hoạt động trên desktop và tablet/mobile fallback.
- [ ] Không làm chậm thao tác người dùng.

---

## 41. CI analytics checks

Có thể bổ sung:

- Typecheck event payload.
- Schema lint.
- Event registry diff review.
- Unit test redaction.
- Test không gửi raw search query.
- Test logout reset analytics.

Phải block nếu thay đổi có nguy cơ gửi PII hoặc làm sai core business metric.

---

## 42. Deprecation policy

Khi bỏ event:

1. Đánh dấu deprecated trong registry.
2. Xác định dashboard hoặc report đang dùng.
3. Cập nhật downstream.
4. Ngừng gửi sau thời gian chuyển tiếp.
5. Ghi changelog.

Không xóa event production đột ngột làm hỏng báo cáo.

---

## 43. AI coding agent requirements

AI phải:

- Không tự thêm event cho mọi click.
- Không gửi raw search query.
- Không gửi PII hoặc secret.
- Dùng helper analytics tập trung.
- Báo rõ event mới, trigger và properties.
- Bổ sung test chống duplicate.
- Không gọi outcome success trước khi backend xác nhận.
- Không thay đổi event name/schema cũ thiếu migration.
- Không tuyên bố analytics hoàn chỉnh nếu chưa có registry và QA.

---

## 44. Event inventory tóm tắt

| Domain | Events chính |
|---|---|
| Dashboard | page viewed, filter applied |
| Navigation | sidebar item selected |
| KPI | card opened |
| Charts | view changed, drilldown opened |
| Products | metric changed, item opened |
| Inventory | alert action started/completed/failed |
| Orders | quick view opened, action started/completed/failed |
| Search | query submitted, result selected, abandoned |
| Notifications | dropdown opened, item read/opened |
| Reports | export requested/queued/completed/failed/downloaded |
| UX states | retry clicked, empty-state action clicked |
| Permissions | access denied |
| Onboarding | viewed, action clicked, step completed |

---

## 45. Acceptance checklist

- [ ] Event naming convention được thống nhất.
- [ ] Có event envelope và common properties.
- [ ] Analytics tách khỏi observability và audit.
- [ ] Không thu PII không cần thiết.
- [ ] Có events cho page, filter, KPI, chart và widgets.
- [ ] Có events cho search, orders, inventory, notifications và export.
- [ ] Intent và outcome được tách biệt.
- [ ] Core funnels được xác định.
- [ ] Event ownership rõ ràng.
- [ ] Có schema registry và versioning.
- [ ] Có deduplication.
- [ ] Logout reset identity.
- [ ] Có data quality checks.
- [ ] Có automated test và QA checklist.
- [ ] Sampling policy rõ.
- [ ] Retention và privacy review được xác định.
- [ ] Dashboard analytics đề xuất đủ cho Product.

---

## 46. Những điều không được làm

- Không ghi mọi click không có mục đích.
- Không gửi tên, phone, email hoặc địa chỉ khách hàng.
- Không gửi raw search query.
- Không dùng order ID hoặc user ID làm metric dimension cardinality cao.
- Không ghi outcome success trước backend confirmation.
- Không gửi duplicate event sau retry/reconnect.
- Không đổi tên event âm thầm.
- Không gọi analytics SDK trực tiếp rải rác trong component.
- Không trộn production và staging event.
- Không dùng analytics thay audit log cho action nhạy cảm.
- Không tuyên bố tuân thủ privacy khi chưa được review.

---

## 47. Kết luận

Analytics Event Plan của Cynca VLXD Dashboard phải giúp đội sản phẩm đo adoption, hiệu quả công việc và điểm ma sát mà không làm tăng rủi ro quyền riêng tư. Mỗi event cần có mục tiêu, schema, owner, trigger và quy tắc dữ liệu rõ ràng. Kế hoạch tốt ưu tiên outcome thực tế thay vì số lượng click.

File tiếp theo đề xuất:

```text
README.md
27-Role-Based-Dashboard.md
```