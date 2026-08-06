# Cynca VLXD Admin Dashboard — Complete Documentation Index

> Bộ tài liệu số 65  
> Trạng thái: Hoàn thành bộ đặc tả nền tảng từ `01` đến `64`  
> Thương hiệu: **Cynca VLXD**  
> Phạm vi: UI/UX, kiến trúc frontend, data contracts, vận hành, bảo mật, kiểm thử và quản trị hệ thống

---

## 1. Mục đích

Thư mục này là nguồn tham chiếu chính để Product, Design, Frontend, Backend, QA, DevOps, Security, Data và AI coding agent triển khai Dashboard quản trị cho hệ sinh thái Cynca VLXD.

Bộ tài liệu không chỉ mô tả giao diện. Mỗi module xác định thêm:

- Mục tiêu nghiệp vụ.
- Information architecture.
- Permission và data scope.
- Data contracts.
- API endpoints đề xuất.
- Component inventory.
- Loading, empty, error và stale states.
- Responsive và accessibility.
- Security, privacy và audit.
- Analytics và observability.
- Unit, integration, E2E và Visual QA.
- Acceptance checklist.

---

## 2. Mục lục đầy đủ — 64 tài liệu chuyên môn

### Nền tảng giao diện và Design System

1. [01-Overview.md](./01-Overview.md) — Tổng quan Dashboard và nguyên tắc thiết kế.
2. [02-Layout.md](./02-Layout.md) — Hệ thống layout, grid và cấu trúc trang.
3. [03-Sidebar.md](./03-Sidebar.md) — Sidebar, navigation và trạng thái thu gọn.
4. [04-Header.md](./04-Header.md) — Header, scope, search và user actions.
5. [05-KPI-Cards.md](./05-KPI-Cards.md) — KPI cards, trend và data states.
6. [06-Charts.md](./06-Charts.md) — Chart patterns và quy tắc trực quan hóa.
7. [07-Product-Widgets.md](./07-Product-Widgets.md) — Widgets sản phẩm và tồn kho.
8. [08-Orders-Table.md](./08-Orders-Table.md) — Bảng đơn hàng và hành động hàng loạt.
9. [09-Notification.md](./09-Notification.md) — Notification patterns nền tảng.
10. [10-Animation.md](./10-Animation.md) — Motion, transition và reduced motion.
11. [11-Responsive.md](./11-Responsive.md) — Responsive behavior.
12. [12-Accessibility.md](./12-Accessibility.md) — Accessibility và keyboard support.
13. [13-Tailwind-Tokens.md](./13-Tailwind-Tokens.md) — Design tokens và Tailwind conventions.
14. [14-Implementation-Checklist.md](./14-Implementation-Checklist.md) — Checklist triển khai ban đầu.
15. [15-AI-Implementation-Prompt.md](./15-AI-Implementation-Prompt.md) — Prompt chuẩn cho AI coding agent.
16. [16-Data-Contracts.md](./16-Data-Contracts.md) — Data contracts và schema dùng chung.
17. [17-Visual-QA-Cases.md](./17-Visual-QA-Cases.md) — Visual QA cases.
18. [18-Component-Inventory.md](./18-Component-Inventory.md) — Danh mục component.
19. [19-Storybook-Cases.md](./19-Storybook-Cases.md) — Storybook stories và fixtures.

### Kiến trúc, kiểm thử và vận hành nền

20. [20-Frontend-Architecture.md](./20-Frontend-Architecture.md) — Kiến trúc frontend.
21. [21-Test-Strategy.md](./21-Test-Strategy.md) — Chiến lược kiểm thử.
22. [22-Release-Operations.md](./22-Release-Operations.md) — Release và vận hành.
23. [23-Security-Privacy.md](./23-Security-Privacy.md) — Bảo mật và quyền riêng tư.
24. [24-Observability.md](./24-Observability.md) — Metrics, logs và traces.
25. [25-Runbooks.md](./25-Runbooks.md) — Runbooks vận hành.
26. [26-Analytics-Event-Plan.md](./26-Analytics-Event-Plan.md) — Analytics events.
27. [27-Role-Based-Dashboard.md](./27-Role-Based-Dashboard.md) — Dashboard theo vai trò.
28. [28-Dashboard-Configuration-Schema.md](./28-Dashboard-Configuration-Schema.md) — Schema cấu hình động.
29. [29-Dashboard-Admin-Configurator.md](./29-Dashboard-Admin-Configurator.md) — Công cụ cấu hình Dashboard.
30. [30-Notification-Center.md](./30-Notification-Center.md) — Trung tâm thông báo vận hành.
31. [31-Search-Command-Center.md](./31-Search-Command-Center.md) — Search và Command Center.

### Dashboard nghiệp vụ theo vai trò

32. [32-Executive-Dashboard.md](./32-Executive-Dashboard.md) — Dashboard điều hành.
33. [33-Sales-Dashboard.md](./33-Sales-Dashboard.md) — Dashboard bán hàng.
34. [34-Warehouse-Dashboard.md](./34-Warehouse-Dashboard.md) — Dashboard kho.
35. [35-Finance-Dashboard.md](./35-Finance-Dashboard.md) — Dashboard tài chính.
36. [36-Customer-Service-Dashboard.md](./36-Customer-Service-Dashboard.md) — Dashboard CSKH.
37. [37-Branch-Manager-Dashboard.md](./37-Branch-Manager-Dashboard.md) — Dashboard quản lý chi nhánh.

### Quản trị dữ liệu, người dùng và hệ thống

38. [38-Reports-Export-Center.md](./38-Reports-Export-Center.md) — Báo cáo và xuất dữ liệu.
39. [39-Audit-Activity-Center.md](./39-Audit-Activity-Center.md) — Audit và activity history.
40. [40-User-Role-Management.md](./40-User-Role-Management.md) — User, role, permission và scope.
41. [41-System-Settings.md](./41-System-Settings.md) — System Settings.
42. [42-Design-System-Playground.md](./42-Design-System-Playground.md) — Design System Playground.
43. [43-Onboarding-Help-Center.md](./43-Onboarding-Help-Center.md) — Onboarding và Help Center.
44. [44-Mobile-Tablet-Operations.md](./44-Mobile-Tablet-Operations.md) — Mobile và tablet operations.
45. [45-Data-Import-Center.md](./45-Data-Import-Center.md) — Nhập dữ liệu hàng loạt.

### Reliability, governance và security operations

46. [46-Integration-Monitoring-Center.md](./46-Integration-Monitoring-Center.md) — Giám sát integrations.
47. [47-System-Health-Incident-Center.md](./47-System-Health-Incident-Center.md) — System health và incidents.
48. [48-Backup-Recovery-Center.md](./48-Backup-Recovery-Center.md) — Backup và recovery.
49. [49-Data-Governance-Quality-Center.md](./49-Data-Governance-Quality-Center.md) — Data governance và quality.
50. [50-Performance-Capacity-Center.md](./50-Performance-Capacity-Center.md) — Performance và capacity.
51. [51-Compliance-Privacy-Center.md](./51-Compliance-Privacy-Center.md) — Compliance và privacy.
52. [52-Security-Operations-Center.md](./52-Security-Operations-Center.md) — Security Operations Center.
53. [53-Release-Change-Management-Center.md](./53-Release-Change-Management-Center.md) — Release và change management.
54. [54-Business-Continuity-Center.md](./54-Business-Continuity-Center.md) — Business continuity.
55. [55-Vendor-Third-Party-Management-Center.md](./55-Vendor-Third-Party-Management-Center.md) — Vendor và third-party management.

### Tự động hóa, AI, dữ liệu khách hàng và điều hành

56. [56-Automation-Workflow-Center.md](./56-Automation-Workflow-Center.md) — Workflow automation.
57. [57-AI-Assistant-Governance-Center.md](./57-AI-Assistant-Governance-Center.md) — AI Assistant governance.
58. [58-Search-Knowledge-Management-Center.md](./58-Search-Knowledge-Management-Center.md) — Search và knowledge management.
59. [59-Customer-Identity-Profile-Center.md](./59-Customer-Identity-Profile-Center.md) — Customer identity và profile.
60. [60-Executive-Planning-OKR-Center.md](./60-Executive-Planning-OKR-Center.md) — Planning và OKR.
61. [61-Notification-Campaign-Center.md](./61-Notification-Campaign-Center.md) — Campaign và Zalo OA messaging.
62. [62-Document-Digital-Asset-Center.md](./62-Document-Digital-Asset-Center.md) — Documents và digital assets.
63. [63-Master-Data-Configuration-Center.md](./63-Master-Data-Configuration-Center.md) — Master data và reference configuration.
64. [64-UAT-Acceptance-Launch-Checklist.md](./64-UAT-Acceptance-Launch-Checklist.md) — UAT, acceptance và Go-live checklist.

---

## 3. Tài liệu số 65

File `README.md` này là tài liệu số **65**, đóng vai trò:

- Mục lục toàn bộ bộ đặc tả.
- Hướng dẫn thứ tự đọc và triển khai.
- Quy tắc dùng tài liệu cho người và AI.
- Cổng kiểm soát phạm vi để không tiếp tục mở rộng module thiếu nhu cầu thực tế.

Bộ tài liệu được chốt tại **65 file trong bộ Dashboard**, gồm 64 tài liệu chuyên môn và README này.

---

## 4. Kiến trúc đề xuất

```text
Zalo Mini App / Admin Web
        ↓
Frontend Application Layer
        ↓
API Gateway / BFF
        ↓
Domain Services
├── Products
├── Orders
├── Inventory
├── Warehouse
├── Finance
├── Customers
├── Support
├── Reports
├── Notifications
└── Administration
        ↓
Data, Queue, Search, Storage, Integrations
```

Nguyên tắc:

- Frontend không phải lớp authorization cuối cùng.
- Backend enforce permission và scope.
- Các mutation quan trọng dùng idempotency.
- File private dùng signed URL.
- Event/audit phải có request ID và trace ID.
- Dữ liệu lỗi hoặc thiếu phải hiển thị `Unknown`, không tự thay bằng `0` hoặc `Healthy`.

---

## 5. Thứ tự triển khai khuyến nghị

### Giai đoạn 1 — Foundation

Đọc và triển khai `01–26`:

- Layout và Design System.
- Data contracts.
- Architecture.
- Testing.
- Security.
- Observability.

### Giai đoạn 2 — Core Dashboard

Triển khai `27–37`:

- Role-based shell.
- Configuration schema.
- Notifications và Search.
- Executive, Sales, Warehouse, Finance, CSKH và Branch Dashboard.

### Giai đoạn 3 — Administration

Triển khai `38–45`:

- Reports/Export.
- Audit.
- User/Role.
- Settings.
- Help.
- Mobile operations.
- Import Center.

### Giai đoạn 4 — Reliability và Governance

Triển khai `46–55` theo mức độ cần thiết:

- Integration monitoring.
- System health.
- Backup/recovery.
- Data quality.
- Performance.
- Compliance và security.
- Change, continuity và vendor management.

### Giai đoạn 5 — Advanced Capabilities

Triển khai `56–63` khi core system đã ổn định:

- Workflow automation.
- AI governance.
- Knowledge management.
- Customer identity.
- OKR.
- Campaigns.
- Digital assets.
- Master data.

### Giai đoạn 6 — Launch

Dùng `64-UAT-Acceptance-Launch-Checklist.md` làm cổng Go-live.

---

## 6. Coding conventions

### Component naming

```text
PascalCase cho React components
camelCase cho hooks và functions
UPPER_SNAKE_CASE cho constants đặc biệt
kebab-case cho route segments
```

Ví dụ:

```text
OrderStatusBadge
useOrderFilters
formatCurrencyVND
REPORT_EXPORT_MAX_ROWS
/reports/export-center
```

### File organization

```text
src/
├── app/
├── features/
├── components/
├── design-system/
├── services/
├── hooks/
├── stores/
├── schemas/
├── permissions/
├── analytics/
└── tests/
```

### Component rules

- Ưu tiên component có trách nhiệm rõ.
- Không hardcode token màu và spacing.
- Không gọi API trực tiếp từ component trình bày.
- Có loading, empty, error và permission state.
- Có test cho logic quan trọng.

---

## 7. API conventions

```text
/api/v1/<domain>/<resource>
```

Yêu cầu:

- Response schema versioned.
- Errors có `code`, `message`, `requestId`.
- Pagination ưu tiên cursor cho dữ liệu lớn.
- Mutation hỗ trợ idempotency khi cần.
- 409 cho version conflict.
- Không trả field ngoài permission.
- Timestamp lưu UTC; UI hiển thị theo timezone.

Ví dụ error:

```ts
export type ApiError = {
  code: string;
  message: string;
  requestId?: string;
  fieldErrors?: Record<string, string[]>;
};
```

---

## 8. Permission và scope

Permission có dạng:

```text
domain.resource.action
```

Ví dụ:

```text
orders.view
orders.update
reports.finance.export
inventory.adjust
users.roles.assign
```

Scope có thể là:

```text
system
branch
warehouse
team
assigned
self
```

Frontend dùng permission để hiển thị UX phù hợp; backend phải xác thực lại mọi request.

---

## 9. Design System rules

- Màu chủ đạo: xanh dương Cynca VLXD.
- Dùng semantic tokens cho success, warning, danger và info.
- Không dùng màu làm tín hiệu duy nhất.
- Touch target tối thiểu 44px.
- Desktop ưu tiên dữ liệu; mobile ưu tiên tác vụ.
- Không thu nhỏ nguyên bảng desktop thành mobile.
- Dark mode chỉ triển khai khi toàn bộ token và charts đã hỗ trợ.

---

## 10. Testing workflow

```text
Unit
→ Component
→ Integration
→ E2E
→ Accessibility
→ Visual Regression
→ Performance
→ Security
→ UAT
```

Merge phải bị chặn khi:

- Typecheck/lint fail.
- Critical test fail.
- Accessibility critical issue.
- Visual regression chưa được review.
- Permission/scope test fail.
- Secret hoặc PII bị phát hiện trong source/log fixture.

---

## 11. Development workflow

```text
Read specification
→ Confirm scope and assumptions
→ Define contracts
→ Implement fixtures/states
→ Build components
→ Integrate API
→ Add tests
→ Visual/accessibility review
→ Security review
→ Merge
```

Không bắt đầu bằng việc tạo giao diện đẹp nhưng thiếu contracts và permission model.

---

## 12. Release workflow

```text
Change Request
→ Review
→ Required Checks
→ Approval
→ Staging
→ UAT
→ Scheduled Release
→ Canary/Rollout
→ Health Gates
→ Full Release
→ Monitoring
→ Post-release Review
```

Production release phải có:

- Owner.
- Rollback plan.
- Monitoring.
- Backup/recovery point khi cần.
- Communication plan.
- Go/No-Go record.

---

## 13. AI coding rules

AI coding agent phải:

- Đọc tài liệu liên quan trước khi code.
- Tái sử dụng Design System và component inventory.
- Không tự tạo permission, status hoặc API contract không có căn cứ.
- Không hardcode data/role/branch.
- Không bỏ loading, empty, error, stale và permission states.
- Không dùng mock data như production data.
- Không tuyên bố hoàn thành khi test chưa chạy.
- Báo rõ assumptions và phần backend còn thiếu.
- Không tự chạy mutation critical hoặc publish/deploy.

Prompt khởi đầu nên tham chiếu:

```text
15-AI-Implementation-Prompt.md
16-Data-Contracts.md
20-Frontend-Architecture.md
23-Security-Privacy.md
27-Role-Based-Dashboard.md
```

---

## 14. Definition of Done

Một module chỉ được coi là hoàn thành khi:

- [ ] UI đúng specification.
- [ ] Data contract được thống nhất.
- [ ] Permission/scope backend enforce.
- [ ] Loading/empty/error/stale đầy đủ.
- [ ] Responsive đạt.
- [ ] Accessibility đạt.
- [ ] Unit/component/integration/E2E test đạt.
- [ ] Visual QA đạt.
- [ ] Analytics và audit được xác định.
- [ ] Observability có metrics/errors.
- [ ] Security review đạt.
- [ ] Documentation cập nhật.
- [ ] Product/Business acceptance hoàn tất.

---

## 15. Phạm vi được chốt

Bộ tài liệu kết thúc tại file số 65. Không tạo thêm module mới chỉ để tăng số lượng tài liệu.

Tài liệu mới chỉ nên được bổ sung khi có ít nhất một trong các điều kiện:

- Nghiệp vụ thực tế chưa được module hiện tại bao phủ.
- Có thay đổi kiến trúc lớn.
- Có yêu cầu pháp lý hoặc bảo mật mới.
- Có feature đã được phê duyệt và có owner.
- Tài liệu hiện tại không thể mở rộng hợp lý.

Các cải tiến nhỏ nên cập nhật file hiện có thay vì tạo file số 66 trở đi.

---

## 16. Kết luận

Bộ tài liệu Cynca VLXD Admin Dashboard đã hoàn thành ở mức đặc tả toàn diện cho UI/UX, nghiệp vụ, kỹ thuật, dữ liệu, bảo mật và vận hành. Nhóm triển khai nên bắt đầu từ foundation, ưu tiên core business modules, kiểm soát scope và dùng UAT checklist làm điều kiện bắt buộc trước Go-live.
