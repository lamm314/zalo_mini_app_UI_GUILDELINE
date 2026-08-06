# Cynca VLXD Admin Dashboard — Role-Based Dashboard Specification

> Phiên bản: 1.0  
> Phạm vi: Cá nhân hóa Dashboard theo vai trò, quyền và phạm vi dữ liệu  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, QA, Security và AI coding agent  
> Tài liệu liên quan: `03-Sidebar.md`, `05-KPI-Cards.md`, `07-Product-Widgets.md`, `08-Orders-Table.md`, `16-Data-Contracts.md`, `23-Security-Privacy.md`

---

## 1. Mục tiêu

Dashboard theo vai trò giúp mỗi nhóm người dùng nhìn thấy đúng thông tin, đúng mức độ chi tiết và đúng hành động cần thiết cho công việc của họ.

Mục tiêu:

- Giảm nhiễu thông tin.
- Không hiển thị dữ liệu ngoài quyền.
- Ưu tiên widget phục vụ tác vụ thường xuyên.
- Giữ một Design System thống nhất giữa các vai trò.
- Cho phép mở rộng role và permission mà không phải tạo nhiều Dashboard tách biệt hoàn toàn.
- Bảo đảm backend vẫn là nguồn quyết định cuối cùng về quyền.

Role-based Dashboard không có nghĩa là hardcode giao diện theo tên vai trò. Giao diện phải được dựng từ permission, scope và cấu hình widget.

---

## 2. Vai trò mặc định

Các vai trò tham chiếu:

1. Super Admin.
2. Admin hệ thống.
3. Quản lý kinh doanh.
4. Nhân viên kinh doanh.
5. Quản lý kho.
6. Nhân viên kho.
7. Kế toán.
8. Quản lý chi nhánh.
9. Chăm sóc khách hàng.
10. Người chỉ xem báo cáo.

Tên vai trò thực tế có thể thay đổi. Permission key phải ổn định hơn role name.

---

## 3. Permission groups

### Dashboard

```text
dashboard.view
dashboard.configure
```

### Finance

```text
finance.view
finance.gross_profit.view
finance.debt.view
```

### Orders

```text
orders.view
orders.view_all
orders.update
orders.cancel
orders.refund
orders.assign
```

### Inventory

```text
inventory.view
inventory.view_all_warehouses
inventory.update
inventory.adjust
inventory.import
```

### Products

```text
products.view
products.update
products.pricing.view
products.pricing.update
```

### Customers

```text
customers.view
customers.view_pii
customers.update
```

### Reports

```text
reports.view
reports.export
reports.finance.export
```

### Administration

```text
users.manage
roles.manage
settings.manage
branches.manage
```

---

## 4. Scope model

Quyền phải đi cùng phạm vi.

```ts
export type DataScope = {
  level: "system" | "branch" | "warehouse" | "assigned" | "self";
  branchIds?: string[];
  warehouseIds?: string[];
  assignedUserId?: string;
};
```

Ví dụ:

- Admin hệ thống: toàn hệ thống.
- Quản lý chi nhánh: một chi nhánh.
- Nhân viên kinh doanh: đơn được phân công.
- Nhân viên kho: một hoặc nhiều kho.
- Kế toán: toàn bộ dữ liệu tài chính được cho phép.

Frontend không được tự mở rộng scope do người dùng chọn filter.

---

## 5. Dashboard composition model

Dashboard nên được tạo từ cấu hình widget:

```ts
export type DashboardWidgetConfig = {
  key: string;
  visible: boolean;
  order: number;
  span: {
    desktop: 4 | 6 | 8 | 12;
    tablet: 6 | 12;
    mobile: 12;
  };
  requiredPermissions?: string[];
  scopeMode?: "inherit" | "system" | "branch" | "warehouse" | "assigned";
  priority?: "critical" | "high" | "normal" | "low";
};
```

Backend hoặc configuration layer có thể trả widget list đã được lọc theo quyền.

---

## 6. Quy tắc render theo quyền

Trình tự:

1. Xác thực user.
2. Tải permission và data scope.
3. Tải Dashboard configuration.
4. Loại widget không đủ quyền.
5. Áp scope lên mọi query.
6. Sắp xếp widget theo role preset hoặc user preference được phép.
7. Render loading state an toàn.

Không render toàn bộ widget rồi ẩn sau khi API đã trả dữ liệu nhạy cảm.

---

## 7. Super Admin Dashboard

### Mục tiêu

Theo dõi toàn hệ thống, cấu hình và phát hiện sự cố.

### KPI đề xuất

- Doanh thu toàn hệ thống.
- Tổng đơn hàng.
- Lợi nhuận gộp.
- Số chi nhánh hoạt động.
- Tổng tồn kho theo giá trị nếu được phép.
- Cảnh báo hệ thống.

### Widgets

- Doanh thu toàn hệ thống.
- So sánh chi nhánh.
- Order status tổng hợp.
- Inventory alerts nhiều kho.
- System notifications.
- Data quality.
- Recent admin activities.

### Actions

- Chuyển chi nhánh.
- Quản lý user/role.
- Xem audit log.
- Export báo cáo.
- Cấu hình hệ thống.

### Không nên ưu tiên

- Danh sách task nhỏ của một nhân viên cụ thể.

---

## 8. Admin hệ thống

Admin hệ thống tương tự Super Admin nhưng có thể bị giới hạn:

- Không xem dữ liệu tài chính nhạy cảm.
- Không thay đổi owner hoặc cấu hình bảo mật cấp cao.
- Không xem secret hoặc infrastructure setting.

Dashboard tập trung:

- Sức khỏe hệ thống.
- User và permission issues.
- Data synchronization.
- Notification errors.
- Product data quality.

---

## 9. Quản lý kinh doanh

### Mục tiêu

Theo dõi doanh số, hiệu suất đội ngũ và tình trạng đơn hàng.

### KPI

- Doanh thu.
- Số đơn.
- Tỷ lệ chuyển đổi nếu có dữ liệu.
- Giá trị đơn trung bình.
- Khách hàng mới.
- Đơn chờ xử lý.

### Widgets ưu tiên

1. KPI kinh doanh.
2. Revenue chart.
3. Orders by status.
4. Best-selling products.
5. Salesperson performance.
6. Recent orders.
7. Customer follow-up alerts.

### Actions

- Xem và phân công đơn.
- Xem khách hàng.
- Export báo cáo kinh doanh.
- Drill-down theo nhân viên hoặc chi nhánh.

### Ẩn nếu không có quyền

- Giá vốn.
- Lợi nhuận chi tiết.
- Cấu hình hệ thống.
- Điều chỉnh tồn kho.

---

## 10. Nhân viên kinh doanh

### Mục tiêu

Xử lý nhanh đơn và khách hàng được phân công.

### KPI

- Đơn được giao.
- Đơn chờ xử lý.
- Doanh số cá nhân.
- Khách cần chăm sóc.

### Widgets ưu tiên

- My orders.
- Đơn mới chưa xác nhận.
- Khách cần liên hệ.
- Sản phẩm bán chạy.
- Thông báo liên quan trực tiếp.

### Scope

```text
assigned
```

### Không hiển thị

- Doanh thu toàn công ty nếu không có quyền.
- Dữ liệu nhân viên khác.
- Tồn kho chi tiết ngoài nhu cầu bán hàng.
- User management.

---

## 11. Quản lý kho

### Mục tiêu

Bảo đảm tồn kho, nhập xuất và xử lý đơn chờ xuất.

### KPI

- SKU sắp hết.
- SKU hết hàng.
- Tồn kho âm.
- Đơn chờ xuất.
- Phiếu nhập chờ xử lý.
- Tỷ lệ đồng bộ kho.

### Widgets ưu tiên

1. Inventory alerts.
2. Orders ready to ship.
3. Warehouse stock summary.
4. Slow-moving inventory.
5. Sync status.
6. Recent stock movements.

### Actions

- Tạo phiếu nhập.
- Xử lý xuất kho.
- Kiểm tra SKU.
- Đồng bộ lại dữ liệu.
- Điều chỉnh tồn nếu có quyền đặc biệt.

### Không hiển thị

- Lợi nhuận.
- Công nợ.
- Báo cáo tài chính.
- Dữ liệu khách hàng không cần thiết.

---

## 12. Nhân viên kho

### KPI tối giản

- Đơn chờ lấy hàng.
- Đơn chờ đóng gói.
- SKU thiếu hàng.
- Tác vụ được giao hôm nay.

### Widgets

- Pick/pack queue.
- Inventory alerts trong kho được gán.
- Task list.
- Recent completed actions.

### Scope

```text
warehouse
```

### Giao diện

- Ít chart hơn.
- Nhiều danh sách hành động hơn.
- Primary actions rõ ràng.
- Không hiển thị KPI tài chính.

---

## 13. Kế toán

### Mục tiêu

Theo dõi thanh toán, công nợ và báo cáo tài chính vận hành.

### KPI

- Doanh thu ghi nhận.
- Đã thu.
- Chưa thu.
- Công nợ đến hạn.
- Công nợ quá hạn.
- Hoàn tiền.

### Widgets ưu tiên

1. Payment status summary.
2. Debt aging.
3. Revenue chart.
4. Failed payments.
5. Refund requests.
6. Recent financial transactions.

### Actions

- Xem chi tiết thanh toán.
- Đối soát.
- Export báo cáo.
- Xác nhận thanh toán nếu workflow cho phép.

### Không hiển thị

- Điều chỉnh kho.
- Quản trị user trừ khi có quyền riêng.
- Dữ liệu marketing không liên quan.

---

## 14. Quản lý chi nhánh

### Mục tiêu

Theo dõi toàn bộ hoạt động trong một chi nhánh.

### KPI

- Doanh thu chi nhánh.
- Đơn hàng.
- Tồn kho.
- Nhân viên hoạt động.
- Khách hàng mới.

### Scope

```text
branch
```

### Widgets

- KPI chi nhánh.
- Revenue chart.
- Orders by status.
- Inventory alerts.
- Staff task summary.
- Recent orders.

Không cho phép đổi sang chi nhánh khác nếu scope chỉ có một branch.

---

## 15. Chăm sóc khách hàng

### Mục tiêu

Theo dõi yêu cầu, khiếu nại, đơn giao thất bại và khách cần chăm sóc.

### KPI

- Ticket mở.
- Ticket quá hạn.
- Đơn giao thất bại.
- Khiếu nại mới.
- Khách cần gọi lại.

### Widgets

- Customer requests.
- Failed deliveries.
- Complaint queue.
- Recent customer interactions.
- Notifications liên quan khách hàng.

### Privacy

Chỉ hiển thị PII cần thiết để xử lý công việc.

---

## 16. Người chỉ xem báo cáo

### Mục tiêu

Xem số liệu tổng hợp mà không thay đổi dữ liệu.

### Đặc điểm

- Không có mutation actions.
- Không có destructive controls.
- Có thể export nếu được phép.
- Quick view ở chế độ read-only.

### Widgets

- KPI.
- Charts.
- Summary tables.
- Scheduled reports.

UI phải thể hiện rõ read-only nhưng không làm mọi thành phần trông disabled.

---

## 17. Role-to-widget matrix

| Widget | Admin | Kinh doanh | Kho | Kế toán | Chi nhánh | CSKH | Viewer |
|---|---|---|---|---|---|---|---|
| Revenue KPI | Có | Có | Không | Có | Có | Không | Có |
| Gross Profit | Theo quyền | Theo quyền | Không | Có | Theo quyền | Không | Theo quyền |
| Orders KPI | Có | Có | Có liên quan | Có | Có | Có liên quan | Có |
| Revenue Chart | Có | Có | Không | Có | Có | Không | Có |
| Order Status | Có | Có | Đơn chờ xuất | Có giới hạn | Có | Giao thất bại | Có |
| Best Products | Có | Có | Không bắt buộc | Không | Có | Không | Có |
| Inventory Alerts | Có | Xem hạn chế | Có | Không | Có | Không | Theo quyền |
| Recent Orders | Có | Assigned/all | Ready to ship | Payment view | Branch | Support cases | Read-only |
| Notifications | Có | Có | Có | Có | Có | Có | Có |
| User Management | Có | Không | Không | Không | Theo quyền | Không | Không |

Matrix là mặc định. Permission thực tế mới là nguồn quyết định.

---

## 18. Role-based page toolbar

Toolbar thay đổi theo role:

### Admin

- Branch filter.
- Date range.
- Export.
- Configure Dashboard.

### Sales

- Assigned user filter nếu quản lý.
- Date range.
- Create order nếu có quyền.

### Warehouse

- Warehouse filter.
- Stock action.
- Sync status.

### Accountant

- Date range.
- Payment status.
- Export financial report.

Không hiển thị action không liên quan chỉ để toolbar trông đầy.

---

## 19. Role-based sidebar

Sidebar phải sinh từ navigation config:

```ts
export type NavigationItem = {
  key: string;
  label: string;
  href: string;
  icon: string;
  permissions?: string[];
  scopeRequirements?: string[];
  children?: NavigationItem[];
};
```

Quy tắc:

- Không để empty group.
- Route hiện tại vẫn active chính xác.
- Permission loading không được flash toàn bộ menu.
- Không dùng role name trực tiếp trong từng item.

---

## 20. Widget priority by role

### Priority Critical

Tác vụ cần xử lý ngay:

- Đơn chờ xác nhận.
- Tồn kho âm.
- Thanh toán lỗi.
- Ticket quá hạn.

### Priority High

- KPI công việc chính.
- Đơn chờ xuất.
- Công nợ đến hạn.

### Priority Normal

- Chart xu hướng.
- Best sellers.
- Category performance.

### Priority Low

- Tips.
- Secondary reports.
- System announcements không khẩn cấp.

Widget critical phải nằm trong vùng nhìn thấy sớm của role liên quan.

---

## 21. Layout presets

### Executive preset

```text
KPI → Charts → Risks → Summary tables
```

### Operations preset

```text
Urgent actions → Queues → Alerts → KPIs → History
```

### Finance preset

```text
Financial KPIs → Payment/debt charts → Exceptions → Reports
```

### Read-only preset

```text
KPIs → Charts → Summary tables → Scheduled reports
```

Không tạo layout riêng hoàn toàn cho từng role nếu có thể dùng preset.

---

## 22. Dashboard personalization

Có thể cho phép người dùng:

- Thu gọn widget.
- Sắp xếp widget trong phạm vi cho phép.
- Lưu date preset mặc định.
- Chọn hiển thị chart phụ.

Không cho phép:

- Bật widget ngoài quyền.
- Mở rộng scope dữ liệu.
- Ẩn cảnh báo bắt buộc.
- Thay đổi semantic KPI.

---

## 23. Configuration precedence

Thứ tự ưu tiên:

```text
Security/Permission rules
→ Organization policy
→ Role preset
→ Branch configuration
→ User preference
```

User preference không được ghi đè security hoặc policy bắt buộc.

---

## 24. Permission loading state

Trong lúc tải permission:

- Render shell tối thiểu.
- Hiển thị skeleton menu/widget.
- Không render dữ liệu nhạy cảm tạm thời.
- Không flash action rồi biến mất.

Nếu permission load lỗi:

- Fail closed.
- Hiển thị error state.
- Cho phép retry.
- Không giả định quyền mặc định rộng.

---

## 25. No-access states

### Route denied

```text
Bạn không có quyền truy cập trang này.
```

### Widget denied

Ưu tiên ẩn hoàn toàn nếu widget không liên quan.

Có thể hiển thị restricted state khi người dùng cần biết tính năng tồn tại:

```text
Bạn cần quyền finance.view để xem chỉ số này.
```

Không để error 500 cho trường hợp 403 hợp lệ.

---

## 26. Data masking by role

Ví dụ:

| Dữ liệu | Sales | Warehouse | Accountant | Viewer |
|---|---|---|---|---|
| Số điện thoại khách | Full nếu xử lý đơn | Mask | Theo nhu cầu | Mask |
| Địa chỉ giao hàng | Full nếu xử lý | Full khi xuất kho | Hạn chế | Ẩn |
| Giá vốn | Ẩn | Ẩn | Có | Theo quyền |
| Lợi nhuận | Theo quyền | Ẩn | Có | Theo quyền |
| Payment detail | Hạn chế | Ẩn | Có | Tổng hợp |

Masking nên được backend áp dụng trước khi trả dữ liệu.

---

## 27. Role-specific empty states

### Sales

```text
Bạn chưa có đơn hàng được phân công.
```

### Warehouse

```text
Không có đơn chờ xuất tại kho này.
```

### Accountant

```text
Không có công nợ đến hạn trong khoảng thời gian đã chọn.
```

### Admin

```text
Chưa có dữ liệu cho chi nhánh này.
```

Không dùng một empty state chung cho mọi role.

---

## 28. Role-specific notifications

### Sales

- Đơn mới.
- Khách phản hồi.
- Đơn quá hạn xác nhận.

### Warehouse

- Tồn âm.
- Đơn chờ xuất.
- Đồng bộ kho lỗi.

### Accountant

- Thanh toán thất bại.
- Công nợ đến hạn.
- Hoàn tiền chờ xử lý.

### Admin

- Lỗi hệ thống.
- Permission changes.
- Data sync failures.

Notification backend phải lọc theo quyền và scope.

---

## 29. API contract đề xuất

```ts
export type RoleBasedDashboardResponse = {
  user: {
    id: string;
    roleId: string;
    roleName: string;
    permissions: string[];
    scope: DataScope;
  };
  preset: "executive" | "operations" | "finance" | "readonly" | "custom";
  widgets: DashboardWidgetConfig[];
  allowedFilters: string[];
  availableActions: string[];
  updatedAt: string;
};
```

Endpoint đề xuất:

```text
GET /api/v1/dashboard/configuration
```

---

## 30. Frontend component pattern

```tsx
<PermissionGate permission="finance.view">
  <DashboardWidgetBoundary widgetKey="gross-profit">
    <GrossProfitKPI />
  </DashboardWidgetBoundary>
</PermissionGate>
```

Data query vẫn phải truyền scope đã được xác thực.

---

## 31. Backend enforcement

Backend bắt buộc:

- Validate permission.
- Validate object scope.
- Filter response fields.
- Filter available actions.
- Apply masking.
- Log sensitive actions.

Frontend không được gửi `role=admin` để yêu cầu dữ liệu rộng hơn.

---

## 32. Testing matrix

Bắt buộc test:

- Admin thấy đầy đủ widget được phép.
- Warehouse không thấy finance KPI.
- Sales chỉ thấy assigned orders.
- Branch manager không đổi sang branch ngoài scope.
- Accountant xem payment nhưng không chỉnh kho.
- Viewer không có mutation controls.
- Permission loading không flash dữ liệu.
- Direct URL access trả 403 đúng.
- Search và export tuân thủ scope.
- Notification không dẫn tới entity ngoài quyền.

---

## 33. Visual QA theo role

Chụp baseline cho:

```text
Admin Dashboard
Sales Dashboard
Warehouse Dashboard
Accountant Dashboard
Branch Manager Dashboard
Read-only Dashboard
```

Kiểm tra:

- Grid không có khoảng trống bất thường.
- Widget ưu tiên đúng thứ tự.
- Toolbar đúng action.
- Sidebar không có empty group.
- Không xuất hiện dữ liệu nhạy cảm.

---

## 34. Analytics theo role

Có thể phân tích:

- Feature adoption theo role.
- Search success theo role.
- Order action success theo role.
- Alert response time theo role.

Không dùng role analytics để suy ra dữ liệu cá nhân nhạy cảm.

---

## 35. Migration từ Dashboard chung

Trình tự:

1. Audit widget hiện tại.
2. Gắn permission cho từng widget.
3. Xác định preset theo role.
4. Tách scope query.
5. Bổ sung masking.
6. Test role matrix.
7. Rollout theo một nhóm người dùng.
8. Theo dõi lỗi và feedback.
9. Mở rộng rollout.

Không nhân bản toàn bộ code Dashboard thành nhiều route role-specific nếu không cần.

---

## 36. Governance

Mỗi thay đổi role/permission phải có:

- Product owner.
- Security review nếu nhạy cảm.
- Backend enforcement.
- UI behavior.
- Test cases.
- Migration notes.

Không cấp thêm quyền chỉ để sửa một lỗi UI.

---

## 37. AI coding agent requirements

AI phải:

- Không hardcode theo `roleName` nếu permission đã có.
- Không chỉ ẩn ở frontend mà bỏ backend enforcement.
- Không render dữ liệu nhạy cảm trước khi permission load xong.
- Không mở rộng scope khi filter thay đổi.
- Không tạo duplicate Dashboard code cho từng role nếu preset đủ dùng.
- Báo rõ permission, scope và widget mapping đã triển khai.
- Bổ sung test cho direct URL, export, search và notification scope.
- Không tuyên bố role-based security hoàn chỉnh nếu backend chưa hỗ trợ.

---

## 38. Acceptance checklist

- [ ] Vai trò và permission được tách biệt.
- [ ] Có scope model rõ ràng.
- [ ] Widget render theo permission.
- [ ] Query áp đúng scope.
- [ ] Backend lọc dữ liệu và actions.
- [ ] Có preset executive, operations, finance và read-only.
- [ ] Super Admin, Sales, Warehouse, Accountant và Branch Manager có cấu hình rõ.
- [ ] Viewer không có mutation control.
- [ ] Permission loading fail closed.
- [ ] No-access state đúng semantic.
- [ ] PII và financial data được masking theo quyền.
- [ ] Notification, search và export tuân thủ scope.
- [ ] Role-specific empty state được định nghĩa.
- [ ] Có role-to-widget matrix.
- [ ] Có test matrix và visual baseline theo role.
- [ ] User preference không ghi đè security policy.
- [ ] Migration plan không nhân bản code không cần thiết.

---

## 39. Những điều không được làm

- Không dùng role name làm authorization duy nhất.
- Không trả dữ liệu toàn hệ thống rồi lọc ở client.
- Không flash widget nhạy cảm khi permission đang tải.
- Không cho user preference bật widget ngoài quyền.
- Không cho branch filter vượt scope.
- Không tạo nhiều Dashboard codebase tách biệt thiếu cần thiết.
- Không để viewer thấy action có thể mutation.
- Không dùng empty state chung cho mọi vai trò.
- Không để notification hoặc search lộ entity ngoài quyền.
- Không cấp quyền rộng tạm thời để giải quyết lỗi.

---

## 40. Kết luận

Role-Based Dashboard của Cynca VLXD phải kết hợp permission, data scope, widget configuration và layout preset để mỗi người dùng nhận được giao diện phù hợp với công việc. Tính cá nhân hóa chỉ được thực hiện sau các lớp bảo mật và chính sách tổ chức. Backend luôn là nguồn quyết định cuối cùng về dữ liệu và hành động được phép.

File tiếp theo đề xuất:

```text
README.md
28-Dashboard-Configuration-Schema.md
```