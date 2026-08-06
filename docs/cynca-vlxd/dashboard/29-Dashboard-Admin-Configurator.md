# Cynca VLXD Admin Dashboard — Admin Configurator Specification

> Phiên bản: 1.0  
> Phạm vi: Giao diện quản trị cấu hình Dashboard động  
> Đối tượng sử dụng: Product Admin, System Admin, Design, Frontend, Backend, QA và AI coding agent  
> Tài liệu liên quan: `27-Role-Based-Dashboard.md`, `28-Dashboard-Configuration-Schema.md`, `12-Accessibility.md`, `23-Security-Privacy.md`

---

## 1. Mục tiêu

Admin Configurator là công cụ giúp người quản trị cấu hình Dashboard theo vai trò, chi nhánh và phạm vi sử dụng mà không cần sửa trực tiếp mã nguồn.

Công cụ phải hỗ trợ:

- Tạo Dashboard configuration mới.
- Chọn preset theo vai trò.
- Thêm, xóa, sắp xếp và thay đổi kích thước widget.
- Cấu hình toolbar, filter và action.
- Gắn permission, scope và feature flag.
- Preview theo role, viewport và trạng thái dữ liệu.
- Validate trước khi publish.
- Quản lý phiên bản, lịch sử và rollback.
- Giữ an toàn, không cho chạy JavaScript, HTML hoặc endpoint tùy ý.

Admin Configurator không phải page builder tự do. Đây là công cụ cấu hình có whitelist và schema nghiêm ngặt.

---

## 2. Người dùng mục tiêu

### Product Admin

- Chọn widget.
- Sắp xếp bố cục.
- Đặt title và mô tả.
- Cấu hình preset và filter.

### System Admin

- Gắn permission.
- Gắn audience.
- Quản lý scope.
- Publish và rollback.

### Designer

- Kiểm tra layout, spacing, breakpoint và states.

### QA

- Preview các role, viewport và trường hợp dữ liệu.

Không cấp quyền configurator cho người dùng vận hành thông thường.

---

## 3. Permission đề xuất

```text
dashboard.configuration.view
dashboard.configuration.create
dashboard.configuration.update
dashboard.configuration.validate
dashboard.configuration.preview
dashboard.configuration.publish
dashboard.configuration.archive
dashboard.configuration.rollback
dashboard.configuration.audit.view
```

`publish`, `rollback` và `archive` phải là quyền riêng, không mặc định đi cùng quyền chỉnh sửa.

---

## 4. Information architecture

```text
Dashboard Configurator
├── Danh sách cấu hình
├── Tạo cấu hình
├── Editor
│   ├── Canvas
│   ├── Widget Library
│   ├── Properties Panel
│   ├── Toolbar Configuration
│   └── Validation Panel
├── Preview
├── Publish Review
├── Version History
└── Audit Log
```

---

## 5. Trang danh sách cấu hình

### Cột đề xuất

- Tên cấu hình.
- Preset.
- Audience.
- Trạng thái.
- Version.
- Người cập nhật.
- Thời gian cập nhật.
- Thời gian publish.
- Actions.

### Filter

- Status.
- Preset.
- Role.
- Branch.
- Người cập nhật.

### Actions

- Mở editor.
- Duplicate.
- Preview.
- Publish.
- View history.
- Archive.

Không hiển thị action nếu user không có quyền tương ứng.

---

## 6. Configuration status

```text
Draft
Validation Failed
Ready for Review
Approved
Published
Archived
```

Mapping:

| Status | Ý nghĩa |
|---|---|
| Draft | Đang chỉnh sửa |
| Validation Failed | Có lỗi schema hoặc rule |
| Ready for Review | Đã validate, chờ duyệt |
| Approved | Đã duyệt, chưa publish |
| Published | Đang dùng production |
| Archived | Không còn dùng |

Không cho publish trực tiếp từ trạng thái validation failed.

---

## 7. Editor layout desktop

Bố cục đề xuất ở 1440px trở lên:

```text
┌──────────────────────────────────────────────────────────────┐
│ Top Bar: Back · Name · Status · Preview · Validate · Publish │
├───────────────┬──────────────────────────────┬───────────────┤
│ Widget Library│ Canvas 12-column             │ Properties    │
│ 280px         │ flexible                     │ 360px         │
└───────────────┴──────────────────────────────┴───────────────┘
```

### Kích thước

- Widget Library: 272–288px.
- Properties Panel: 340–380px.
- Canvas: phần còn lại, `min-width: 0`.
- Top bar: 64–72px.

---

## 8. Editor layout laptop

Ở 1024–1439px:

- Widget Library có thể collapse thành drawer.
- Properties Panel mở dạng drawer bên phải.
- Canvas chiếm tối đa diện tích.
- Preview breakpoint selector nằm trong top bar.

Không ép 3 panel cùng hiển thị nếu canvas quá hẹp.

---

## 9. Mobile và tablet fallback

Configurator không ưu tiên mobile.

Dưới 1024px:

- Cho phép xem, preview và chỉnh thuộc tính đơn giản.
- Drag-and-drop có thể bị tắt.
- Dùng danh sách widget và nút Move Up/Move Down.
- Publish vẫn yêu cầu review đầy đủ.

Có thể hiển thị notice:

```text
Để chỉnh bố cục chính xác, nên sử dụng màn hình từ 1280px trở lên.
```

---

## 10. Top bar

### Bên trái

- Back.
- Configuration name.
- Version.
- Unsaved changes indicator.

### Bên phải

- Undo.
- Redo.
- Viewport selector.
- Role preview selector.
- Save draft.
- Validate.
- Preview.
- Publish.

### States

- Saving.
- Saved.
- Save failed.
- Validation failed.
- Publishing.

Không đổi vị trí nút publish giữa các trạng thái.

---

## 11. Autosave

Có thể autosave draft sau 1–3 giây khi ngừng chỉnh sửa.

Yêu cầu:

- Hiển thị `Đang lưu`, `Đã lưu`, `Lưu thất bại`.
- Không autosave khi schema đang ở trạng thái tạm không hợp lệ nếu backend không hỗ trợ partial draft.
- Không publish tự động.
- Không ghi đè thay đổi của user khác mà thiếu conflict handling.

---

## 12. Conflict handling

Khi hai admin cùng chỉnh sửa:

- Config có `version` hoặc `updatedAt`.
- Save gửi `expectedVersion`.
- Backend trả conflict nếu version cũ.

UI hiển thị:

```text
Cấu hình đã được người khác cập nhật. Hãy tải bản mới hoặc lưu bản sao.
```

Actions:

- Tải lại bản mới.
- So sánh thay đổi.
- Lưu thành bản sao.

Không tự ghi đè.

---

## 13. Widget Library

Widget được nhóm theo domain:

```text
KPI
Charts
Orders
Products
Inventory
Finance
Customers
Notifications
System
```

Mỗi item gồm:

- Icon.
- Tên.
- Mô tả ngắn.
- Permission requirement.
- Supported presets.
- Supported spans.

Search theo widget name hoặc key.

---

## 14. Widget availability states

### Available

Có thể thêm.

### Already Used

Đã tồn tại nếu widget chỉ cho một instance.

### Restricted

Admin không có quyền hoặc preset không hỗ trợ.

### Feature Flag Off

Widget chưa được bật.

### Deprecated

Không cho thêm mới, nhưng vẫn hiển thị ở config cũ để migration.

---

## 15. Add widget

Có hai cách:

1. Drag widget vào canvas.
2. Chọn `Thêm vào Dashboard` rồi chọn vị trí.

Sau khi thêm:

- Tạo widget ID duy nhất.
- Áp default span.
- Áp default data source.
- Mở Properties Panel.
- Chạy validation cục bộ.

Không tạo widget nếu registry hoặc source key không hợp lệ.

---

## 16. Canvas

Canvas sử dụng grid 12 cột.

Hiển thị:

- Grid guides khi kéo.
- Section boundaries.
- Widget title.
- Span.
- Priority.
- Locked indicator.
- Validation badge.

Canvas phải gần với Dashboard thật nhưng không cần tải dữ liệu production.

---

## 17. Widget canvas card

Mỗi widget trong editor có:

- Drag handle.
- Widget icon và title.
- Span label, ví dụ `8/12`.
- Priority badge.
- Duplicate.
- Hide/show.
- More menu.
- Error indicator.

Selection state dùng border brand và focus ring rõ.

Không nhầm selection state với published active state.

---

## 18. Drag-and-drop

Yêu cầu:

- Có placeholder vị trí.
- Không gây layout nhảy mạnh.
- Auto-scroll khi kéo gần cạnh.
- Hỗ trợ undo.
- Không cho thả vào vị trí vi phạm rule.
- Có keyboard alternative.

Keyboard alternative:

- Chọn widget.
- `Alt + Arrow` hoặc action menu để di chuyển.
- Move to position dialog.

Accessibility không được phụ thuộc duy nhất vào drag-and-drop.

---

## 19. Resize widget

Resize theo span được whitelist:

```text
3, 4, 6, 8, 12 cột
```

Không resize tự do theo pixel.

Properties có selector:

- Desktop span.
- Laptop span.
- Tablet span.
- Mobile luôn 12.

Canvas preview cập nhật theo viewport đang chọn.

---

## 20. Widget ordering

Mỗi widget có `order`.

UI không yêu cầu admin nhập số order thủ công.

Khi reorder:

- Tự chuẩn hóa order.
- Không tạo số trùng.
- Giữ critical widgets trước nếu policy khóa.
- User preference chỉ reorder widget được phép.

---

## 21. Properties Panel

Các nhóm thuộc tính:

1. General.
2. Layout.
3. Data.
4. Display.
5. Permissions.
6. Scope.
7. States.
8. Actions.
9. Advanced.

Chỉ hiển thị field được widget hỗ trợ.

---

## 22. General properties

- Widget title override.
- Description.
- Visible.
- Priority.
- Locked.
- Show header.
- Show last updated.

Title override phải có giới hạn ký tự và preview long-content.

Không cho thay đổi widget key sau khi tạo; muốn đổi loại phải replace widget.

---

## 23. Layout properties

- Span theo breakpoint.
- Min height token.
- Section assignment.
- Order.
- Collapsible.
- Sticky nếu widget hỗ trợ.

Mọi giá trị dùng token hoặc enum.

Không nhập raw CSS.

---

## 24. Data properties

- Source key.
- Refresh policy.
- Stale threshold.
- Item limit.
- Allowed query params.

Source key dùng select từ registry.

Không hiển thị raw endpoint URL.

Có warning khi:

- Polling quá nhanh.
- Limit quá cao.
- Realtime không có fallback.
- Stale threshold không phù hợp loại dữ liệu.

---

## 25. Display properties

Tùy widget:

### KPI

- Value format.
- Trend.
- Sparkline.
- Compact value.

### Chart

- Chart type hợp lệ.
- Legend.
- Tooltip.
- Summary.
- Granularity.

### Table

- Columns.
- Default sort.
- Row limit.
- Row actions.

Không cho config thay đổi business formula.

---

## 26. Permission properties

- Required permissions.
- Mode `all` hoặc `any`.
- Restricted behavior: hidden hoặc denied state.

UI hiển thị cảnh báo nếu:

- Widget tài chính không yêu cầu finance permission.
- Export action thiếu reports permission.
- User management widget thiếu admin permission.

Permission selector lấy từ registry backend, không nhập text tự do.

---

## 27. Scope properties

```text
Inherit
System
Branch
Warehouse
Assigned
```

Configurator chỉ cho chọn scope mà widget hỗ trợ.

Nếu chọn `system`, phải có permission tương ứng.

Không cho config tự khai báo branch ID ngoài audience hoặc organization policy.

---

## 28. State properties

- Loading variant.
- Empty variant.
- Error variant.
- Retry button.
- Stale warning.

Preview nhanh:

```text
Data
Loading
Empty
Error
Stale
Permission denied
```

Mỗi state có thể preview ngay trong canvas.

---

## 29. Actions properties

- Action key.
- Label.
- Type.
- Variant.
- Permission.
- Route whitelist.
- Order.

Destructive action phải dùng danger variant và confirm flow.

Không cho cấu hình JavaScript handler.

---

## 30. Toolbar configurator

Admin có thể cấu hình:

- Page title.
- Description.
- Date filter.
- Branch filter.
- Warehouse filter.
- Compare mode.
- Export.
- Refresh.
- Primary action.

Toolbar preview phải kiểm tra overflow tại 1440, 1280 và 1024px.

---

## 31. Filter dependency editor

Mỗi filter có thể ảnh hưởng:

- Tất cả widget.
- Một nhóm widget.
- Danh sách widget cụ thể.

UI dùng checkbox tree hoặc multi-select.

Warning nếu:

- Filter branch không ảnh hưởng widget branch-scoped.
- Date filter không ảnh hưởng KPI/chart.
- Filter tham chiếu widget đã xóa.

---

## 32. Audience editor

Cho phép chọn:

- Role.
- Required permissions.
- Branch.
- Warehouse.
- User thử nghiệm.
- Feature flag.

Preview phải hiển thị audience summary:

```text
Áp dụng cho: Quản lý kinh doanh có orders.view tại tất cả chi nhánh
```

Không publish nếu audience rỗng hoặc mâu thuẫn.

---

## 33. Role preview

Role preview selector gồm:

- Super Admin.
- Sales Manager.
- Sales Staff.
- Warehouse Manager.
- Warehouse Staff.
- Accountant.
- Branch Manager.
- Viewer.

Preview phải dùng permission fixture thật hoặc mô phỏng theo contract.

Không coi role preview là bằng chứng backend authorization đã đúng.

---

## 34. Viewport preview

Preset:

```text
1920 × 1080
1440 × 900
1366 × 768
1280 × 800
1024 × 768
768 × 1024
390 × 844
```

Có thể zoom canvas nhưng phải hiển thị kích thước thật đang mô phỏng.

---

## 35. Data state preview

Fixtures:

- Normal.
- Dense data.
- Long Vietnamese content.
- Empty account.
- Partial API error.
- Stale data.
- Missing images.
- Large currency values.

Không preview chỉ với dữ liệu ngắn đẹp.

---

## 36. Validation Panel

Phân loại:

### Errors

Chặn publish.

### Warnings

Cho publish khi có xác nhận hoặc policy cho phép.

### Suggestions

Khuyến nghị UX, không chặn.

Ví dụ:

```text
Error: Widget recent_orders dùng sourceKey không tồn tại.
Warning: 7 KPI đang hiển thị ở vùng đầu trang.
Suggestion: Chuyển inventory alerts lên trước best products cho preset warehouse.
```

---

## 37. Validation categories

- Schema.
- Registry.
- Permissions.
- Scope.
- Layout.
- Responsive.
- Accessibility.
- Performance.
- Data freshness.
- Content.
- Security.

Validation result phải chỉ rõ widget và field liên quan.

---

## 38. Accessibility validation

Kiểm tra tự động:

- Title và label không rỗng.
- Icon-only action có accessible label.
- Không có duplicate landmark config.
- Mobile span đúng.
- Restricted state có text.
- Color chỉ dùng token hợp lệ.
- Drag-and-drop có keyboard alternative.

Preview vẫn cần manual keyboard và screen-reader QA.

---

## 39. Performance validation

Warning khi:

- Quá nhiều chart.
- Quá nhiều realtime widget.
- Polling interval quá ngắn.
- Tổng item limit quá cao.
- Nhiều widget tải ngay dưới fold.

Ngưỡng tham khảo:

```text
Tối đa 2 chart chính trên vùng đầu.
Tối đa 6 KPI.
Tối đa 10 rows cho widget table.
Không quá 3 realtime widgets mặc định.
```

---

## 40. Preview mode

Preview mở ở route riêng hoặc modal full-screen.

Có banner:

```text
Chế độ xem trước — dữ liệu mô phỏng — chưa được publish
```

Preview không được nhầm với production.

Không cho mutation thật trong preview.

---

## 41. Save draft

Save draft lưu:

- Schema hiện tại.
- Editor metadata.
- Change note tùy chọn.
- Expected version.

Draft chỉ hiển thị cho người có permission.

Production luôn tải published version.

---

## 42. Review workflow

Quy trình đề xuất:

```text
Editor tạo Draft
→ Validate
→ Gửi Review
→ Reviewer Preview
→ Approve hoặc Request Changes
→ Publisher Publish
```

Có thể dùng four-eyes principle cho cấu hình nhạy cảm.

Người tạo không nên tự publish nếu organization policy yêu cầu tách quyền.

---

## 43. Publish review screen

Hiển thị:

- Configuration name.
- Version mới.
- Audience.
- Widgets added/removed.
- Permission changes.
- Scope changes.
- Toolbar changes.
- Warnings còn lại.
- Change note.
- Rollback target.

Publish cần confirm rõ:

```text
Bạn sắp phát hành Dashboard này cho 4 vai trò tại 6 chi nhánh.
```

---

## 44. Publish strategy

Hỗ trợ:

- Publish ngay.
- Schedule publish.
- Percentage rollout.
- Role-based rollout.
- Branch-based rollout.

Schedule phải dùng timezone rõ ràng.

Rollout lớn nên có feature flag hoặc version targeting.

---

## 45. Version History

Mỗi version hiển thị:

- Version number.
- Status.
- Người tạo.
- Người publish.
- Timestamp.
- Change note.
- Diff summary.
- Audience.
- Metrics/incident link nếu có.

Actions:

- Preview version.
- Compare.
- Rollback.
- Duplicate as draft.

---

## 46. Configuration diff

Diff phải nhóm theo:

- Added widgets.
- Removed widgets.
- Reordered widgets.
- Layout changes.
- Permission changes.
- Scope changes.
- Data refresh changes.
- Toolbar changes.

Không chỉ hiển thị raw JSON diff cho người dùng không kỹ thuật.

Raw JSON có thể nằm trong tab Advanced.

---

## 47. Rollback

Rollback tạo một published version mới dựa trên version cũ, không xóa lịch sử.

Trước rollback:

- Preview version cũ.
- Validate với schema hiện tại.
- Kiểm tra widget deprecated.
- Xác nhận API/data source còn hỗ trợ.

Không rollback mù nếu backend contract đã thay đổi.

---

## 48. Audit Log

Ghi nhận:

```text
configuration.created
configuration.updated
configuration.validated
configuration.review_requested
configuration.approved
configuration.published
configuration.rollback_started
configuration.rolled_back
configuration.archived
```

Audit gồm:

- Actor.
- Action.
- Configuration ID.
- Version.
- Diff summary.
- Result.
- Request ID.
- Timestamp.

---

## 49. Error handling

### Load error

- Hiển thị retry.
- Không tạo draft rỗng thay config thật.

### Save error

- Giữ thay đổi local tạm thời.
- Cho retry hoặc copy JSON an toàn nếu policy cho phép.

### Validation service error

- Không coi là validation passed.

### Publish error

- Không thay status local thành Published trước backend success.
- Hiển thị request ID.

---

## 50. Unsaved changes

Khi rời trang có thay đổi chưa lưu:

```text
Bạn có thay đổi chưa được lưu. Rời trang sẽ làm mất các thay đổi này.
```

Có actions:

- Lưu và rời.
- Rời không lưu.
- Tiếp tục chỉnh sửa.

Không dùng browser confirm làm trải nghiệm duy nhất nếu app có modal chuẩn.

---

## 51. Keyboard shortcuts

Đề xuất:

```text
Cmd/Ctrl + S — Save draft
Cmd/Ctrl + Z — Undo
Cmd/Ctrl + Shift + Z — Redo
Cmd/Ctrl + P — Preview
Escape — Close panel/dialog
```

Không ghi đè shortcut trình duyệt quan trọng nếu gây xung đột.

Có bảng trợ giúp shortcut.

---

## 52. Accessibility

- Mọi control có label.
- Canvas có outline structure cho screen reader.
- Widget selection dùng button hoặc listbox pattern phù hợp.
- Drag-and-drop có alternative.
- Properties errors liên kết field.
- Validation Panel có heading và anchor tới lỗi.
- Focus trả về widget sau khi đóng panel/dialog.
- Color không là tín hiệu duy nhất.
- Zoom 200% vẫn chỉnh được chức năng chính.

---

## 53. Security

- Không render arbitrary code.
- Không cho raw API URL.
- Route, icon, widget và source key dùng whitelist.
- Publish kiểm tra permission server-side.
- Preview không truy cập dữ liệu ngoài scope.
- Draft không public.
- Audit mọi publish/rollback.
- Sanitize title và description.
- Không lưu secret trong config.

---

## 54. API contracts đề xuất

```text
GET    /api/v1/dashboard/configurations
GET    /api/v1/dashboard/configurations/:id
POST   /api/v1/dashboard/configurations
PATCH  /api/v1/dashboard/configurations/:id
POST   /api/v1/dashboard/configurations/:id/validate
POST   /api/v1/dashboard/configurations/:id/review
POST   /api/v1/dashboard/configurations/:id/approve
POST   /api/v1/dashboard/configurations/:id/preview
POST   /api/v1/dashboard/configurations/:id/publish
POST   /api/v1/dashboard/configurations/:id/rollback
POST   /api/v1/dashboard/configurations/:id/archive
GET    /api/v1/dashboard/configurations/:id/versions
GET    /api/v1/dashboard/configurations/:id/audit
```

---

## 55. Frontend component inventory

```text
DashboardConfiguratorPage
├── ConfiguratorTopBar
├── WidgetLibrary
├── WidgetLibraryItem
├── DashboardCanvas
├── CanvasWidget
├── GridGuide
├── PropertiesPanel
├── ToolbarEditor
├── AudienceEditor
├── ValidationPanel
├── PreviewFrame
├── PublishReviewDialog
├── VersionHistoryDrawer
├── ConfigurationDiff
└── AuditLogTable
```

---

## 56. State architecture

### Server state

- Configuration.
- Registry.
- Permission catalog.
- Version history.
- Audit log.

### Editor state

- Selected widget.
- Draft layout.
- Undo stack.
- Redo stack.
- Active viewport.
- Active data fixture.
- Open panels.

Không lưu toàn bộ editor state vào global app store nếu chỉ dùng trong configurator route.

---

## 57. Undo và redo

Undo stack bao phủ:

- Add/remove widget.
- Reorder.
- Resize.
- Properties change.
- Toolbar change.

Không undo:

- Publish.
- Rollback.
- Server permission changes bên ngoài.

Giới hạn stack để tránh memory tăng vô hạn.

---

## 58. Testing

### Unit

- Schema mapping.
- Validation rules.
- Diff generation.
- Merge precedence.
- Undo/redo reducer.

### Component

- Widget add/remove.
- Properties update.
- Permission selector.
- Validation navigation.
- Save states.
- Conflict dialog.

### Integration

- Draft → validate → review → publish.
- Publish permission denied.
- Version conflict.
- Rollback validation.
- Preview by role and viewport.

### E2E

- Tạo config mới.
- Thêm KPI và chart.
- Resize/reorder.
- Gắn permissions.
- Preview role warehouse.
- Validate.
- Publish.
- Mở version history.
- Rollback.

---

## 59. Visual QA

Baseline:

```text
Configuration List — 1440px
Editor Default — 1440px
Widget Selected
Validation Errors
Preview Desktop
Preview Mobile
Publish Review
Version History
Conflict State
Save Error
```

Kiểm tra panel width, canvas grid, drag state, focus ring và responsive editor.

---

## 60. Analytics events

Có thể ghi:

```text
configurator.opened
configurator.widget.added
configurator.widget.removed
configurator.widget.reordered
configurator.validation.completed
configurator.preview.opened
configurator.review.requested
configurator.publish.completed
configurator.publish.failed
configurator.rollback.completed
```

Không gửi toàn bộ config payload vào analytics.

---

## 61. Observability

Theo dõi:

- Load failure.
- Save failure.
- Validation service failure.
- Publish failure.
- Conflict rate.
- Preview render failure.
- Invalid configuration rate.

Mọi lỗi publish phải có request ID và release metadata.

---

## 62. AI coding agent requirements

AI phải:

- Đọc `28-Dashboard-Configuration-Schema.md` trước khi code.
- Không biến configurator thành arbitrary page builder.
- Không dùng raw HTML/JS/CSS config.
- Không bỏ keyboard alternative cho drag-and-drop.
- Không publish trước backend confirmation.
- Không ghi đè conflict.
- Không dùng role name làm authorization duy nhất.
- Báo rõ component, state model, validation và tests đã thêm.
- Không tuyên bố hoàn thành nếu chưa triển khai version history và safe publish flow.

---

## 63. Acceptance checklist

- [ ] Có configuration list và status filters.
- [ ] Editor có Widget Library, Canvas và Properties Panel.
- [ ] Canvas dùng grid 12 cột.
- [ ] Widget add/remove/reorder/resize hoạt động.
- [ ] Có keyboard alternative cho drag-and-drop.
- [ ] Properties dùng whitelist và token.
- [ ] Permission, scope và audience editor đầy đủ.
- [ ] Toolbar và filter dependency editor có validation.
- [ ] Preview hỗ trợ role, viewport và data states.
- [ ] Validation Panel phân biệt error, warning và suggestion.
- [ ] Draft không tự publish.
- [ ] Có review và approval workflow.
- [ ] Publish review hiển thị diff và audience.
- [ ] Có version history, compare và rollback.
- [ ] Có conflict handling.
- [ ] Có audit log.
- [ ] Có accessibility, security và observability.
- [ ] Test đủ unit, component, integration và E2E.

---

## 64. Những điều không được làm

- Không cho admin nhập JavaScript, HTML hoặc CSS tùy ý.
- Không cho raw endpoint URL.
- Không publish cấu hình chưa validate.
- Không cho editor tự publish nếu policy yêu cầu reviewer khác.
- Không dùng drag-and-drop làm phương thức duy nhất.
- Không ghi đè thay đổi của người khác.
- Không xóa lịch sử version khi rollback.
- Không dùng draft trong production.
- Không để preview chạy mutation thật.
- Không cho user preference hoặc config mở rộng permission.
- Không coi raw JSON editor là giao diện quản trị duy nhất.

---

## 65. Kết luận

Dashboard Admin Configurator của Cynca VLXD phải cung cấp trải nghiệm cấu hình trực quan nhưng vẫn giữ giới hạn kỹ thuật và bảo mật nghiêm ngặt. Người quản trị chỉ được chọn từ widget, source, token, action và permission đã đăng ký. Mọi thay đổi phải được lưu nháp, validate, preview, review, version hóa và audit trước khi áp dụng cho người dùng thật.

File tiếp theo đề xuất:

```text
README.md
30-Notification-Center.md
```