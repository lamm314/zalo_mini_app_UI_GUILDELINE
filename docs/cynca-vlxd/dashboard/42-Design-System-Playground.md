# Cynca VLXD Admin Dashboard — Design System Playground Specification

> Phiên bản: 1.0  
> Phạm vi: Khu vực thử nghiệm, kiểm tra và tài liệu hóa Design System của Dashboard quản trị  
> Đối tượng sử dụng: Product, Design, Frontend, QA và AI coding agent  
> Tài liệu liên quan: `02-Layout.md`, `12-Accessibility.md`, `13-Tailwind-Tokens.md`, `20-Frontend-Architecture.md`, `21-Testing-QA.md`

---

## 1. Mục tiêu

Design System Playground là môi trường nội bộ giúp đội ngũ Cynca VLXD kiểm tra component, token, trạng thái, responsive behavior và accessibility trước khi đưa vào các màn hình nghiệp vụ.

Playground phải hỗ trợ:

- Xem toàn bộ component trong một nơi tập trung.
- Kiểm tra biến thể, kích thước và trạng thái.
- Kiểm tra responsive theo nhiều viewport.
- Kiểm tra nội dung tiếng Việt dài, dữ liệu lớn và dữ liệu lỗi.
- Kiểm tra keyboard, focus và screen-reader semantics.
- So sánh light/dark mode nếu hệ thống hỗ trợ.
- Kiểm tra token màu, typography, spacing, radius và shadow.
- Tạo visual baseline cho QA.
- Cho phép copy ví dụ sử dụng component an toàn.
- Không kết nối trực tiếp tới dữ liệu production nhạy cảm.

Playground không phải page builder và không thay thế tài liệu component chính thức.

---

## 2. Đối tượng sử dụng

### Designer

- Kiểm tra tính nhất quán của token.
- So sánh biến thể component.
- Kiểm tra hierarchy, spacing và trạng thái.

### Frontend Developer

- Xem API của component.
- Kiểm tra props và behavior.
- Kiểm tra edge cases trước khi tích hợp.

### QA

- Chạy visual, accessibility và responsive checks.
- Xem baseline cho từng trạng thái.

### Product

- Review nhanh component và pattern trước khi áp dụng.

### AI coding agent

- Dùng làm nguồn tham chiếu để không tự tạo pattern mới ngoài Design System.

---

## 3. Information architecture

```text
Design System Playground
├── Foundations
│   ├── Colors
│   ├── Typography
│   ├── Spacing
│   ├── Radius
│   ├── Shadows
│   └── Icons
├── Components
│   ├── Buttons
│   ├── Inputs
│   ├── Selects
│   ├── Checkboxes & Radios
│   ├── Badges
│   ├── Cards
│   ├── Tables
│   ├── Tabs
│   ├── Dialogs
│   ├── Drawers
│   ├── Toasts
│   ├── Tooltips
│   └── Navigation
├── Dashboard Patterns
│   ├── KPI Cards
│   ├── Charts
│   ├── Filters
│   ├── Empty States
│   ├── Error States
│   └── Loading States
├── Responsive Lab
├── Accessibility Lab
├── Content Stress Lab
└── Visual Regression
```

---

## 4. Route và quyền truy cập

Route đề xuất:

```text
/internal/design-system
```

Quyền:

```text
design_system.playground.view
design_system.playground.manage
```

Playground có thể chỉ được bật ở development và staging.

Nếu bật ở production:

- Phải yêu cầu permission nội bộ.
- Không index public.
- Không hiển thị secret, token hoặc dữ liệu thật.
- Không cho mutation nghiệp vụ.

---

## 5. Bố cục trang

Desktop:

```text
Top Bar
├── Search
├── Theme Toggle
├── Viewport Selector
├── Density Selector
└── Accessibility Controls

Sidebar Navigation
Main Preview Canvas
Properties / Controls Panel
Documentation Panel
```

Kích thước gợi ý:

- Sidebar: 240–280px.
- Controls panel: 300–360px.
- Main canvas: flexible.
- Documentation panel: collapsible.

---

## 6. Top bar controls

Các control chính:

- Search component.
- Light/dark/system theme nếu hỗ trợ.
- Viewport preset.
- Zoom.
- Density.
- Locale.
- Direction LTR/RTL nếu tương lai cần.
- Toggle grid overlay.
- Toggle focus outline.
- Reset controls.

Không cho control trong Playground thay đổi settings production.

---

## 7. Component registry

```ts
export type PlaygroundComponentDefinition = {
  key: string;
  name: string;
  category: string;
  description: string;
  component: React.ComponentType<any>;
  controls: PlaygroundControlDefinition[];
  examples: PlaygroundExample[];
  accessibilityNotes?: string[];
  usageGuidelines?: string[];
  deprecated?: boolean;
};
```

Frontend dùng registry tĩnh.

Không load arbitrary component name từ server.

---

## 8. Control definition

```ts
export type PlaygroundControlDefinition = {
  key: string;
  label: string;
  type: "text" | "number" | "boolean" | "select" | "multi_select" | "color_token";
  defaultValue?: unknown;
  options?: Array<{ label: string; value: string }>;
  description?: string;
};
```

Controls chỉ được thay props đã whitelist.

Không cho nhập raw JavaScript, HTML hoặc CSS.

---

## 9. Foundations — Colors

Hiển thị theo nhóm:

```text
Brand
Neutral
Success
Warning
Danger
Info
Background
Surface
Border
Text
Focus
```

Mỗi token gồm:

- Token name.
- Giá trị hiện tại.
- Preview.
- Usage description.
- Contrast ratio với text liên quan nếu có.
- Light/dark mapping.

Ví dụ:

```text
--color-brand-600
--color-surface-default
--color-text-primary
--color-border-subtle
--color-focus-ring
```

Không khuyến khích copy raw hex thay vì token.

---

## 10. Color usage rules

- Brand dùng cho primary action và selected state.
- Success dùng cho trạng thái tích cực đã xác nhận.
- Warning dùng cho cảnh báo cần chú ý.
- Danger dùng cho lỗi, rủi ro và destructive action.
- Neutral dùng cho phần lớn surface, border và text phụ.
- Không dùng màu làm tín hiệu duy nhất.
- Không tạo biến thể màu mới trực tiếp trong page nghiệp vụ.

---

## 11. Typography

Hiển thị:

- Font family.
- Font size.
- Line height.
- Font weight.
- Letter spacing.
- Ví dụ tiếng Việt.

Style tham chiếu:

```text
Display
Heading 1–4
Body Large
Body Default
Body Small
Label
Caption
Code
```

Mỗi style phải có token và usage.

---

## 12. Vietnamese typography stress test

Chuỗi mẫu:

```text
Vật liệu xây dựng chính hãng, giao nhanh toàn quốc
Cập nhật số lượng tồn kho và công nợ quá hạn
Đơn hàng đang chờ xác nhận tại Chi nhánh Hà Nội
```

Kiểm tra:

- Dấu tiếng Việt.
- Line wrapping.
- Truncation.
- Uppercase.
- Font weight.
- Số tiền và mã đơn.

---

## 13. Spacing system

Hiển thị token:

```text
space-0
space-1
space-2
space-3
space-4
space-6
space-8
space-10
space-12
space-16
```

Mỗi token có preview block và usage gợi ý.

Quy tắc:

- Dùng spacing token thay raw pixel.
- Giữ rhythm nhất quán giữa section, card và field.
- Không giảm spacing chỉ để nhồi quá nhiều nội dung.

---

## 14. Radius và shadow

Radius:

```text
radius-sm
radius-md
radius-lg
radius-xl
radius-full
```

Shadow:

```text
shadow-xs
shadow-sm
shadow-md
shadow-lg
```

Usage:

- Card dùng shadow nhẹ hoặc border, không dùng cả hai quá nặng.
- Dialog/drawer dùng elevation rõ hơn.
- Input không cần shadow mặc định.
- Không tạo hiệu ứng glassmorphism hoặc shadow mạnh thiếu lý do.

---

## 15. Icon library

Hiển thị:

- Tên icon.
- Kích thước 16/20/24px.
- Stroke width.
- Usage.
- Accessible label requirement.

Ưu tiên một bộ icon thống nhất, ví dụ Lucide.

Không trộn nhiều phong cách icon trong cùng Dashboard.

---

## 16. Button playground

Biến thể:

```text
Primary
Secondary
Outline
Ghost
Danger
Link
```

Kích thước:

```text
sm
md
lg
icon
```

Trạng thái:

```text
Default
Hover
Focus
Active
Disabled
Loading
```

Kiểm tra:

- Text dài.
- Icon trước/sau.
- Loading không làm thay đổi width quá mạnh.
- Disabled vẫn đọc được nhưng không gây nhầm với loading.

---

## 17. Input playground

Component:

- Text input.
- Search input.
- Number input.
- Currency input.
- Textarea.
- Password/secret input nếu cần nội bộ.

Trạng thái:

- Default.
- Focus.
- Filled.
- Error.
- Warning.
- Disabled.
- Read-only.
- Loading.

Mỗi input phải có label, description và error association.

---

## 18. Select, combobox và date controls

Kiểm tra:

- Single select.
- Multi-select.
- Searchable combobox.
- Date picker.
- Date range picker.
- Time picker.

Edge cases:

- Danh sách dài.
- Option tiếng Việt dài.
- Empty options.
- Loading options.
- Permission-limited options.
- Keyboard navigation.

---

## 19. Checkbox, radio và switch

Yêu cầu:

- Checked, unchecked và indeterminate.
- Disabled state.
- Error state.
- Label nhiều dòng.
- Group heading.
- Keyboard support.

Switch chỉ dùng cho setting bật/tắt tức thời hoặc rõ nghĩa.

Không dùng switch cho action cần review phức tạp.

---

## 20. Badge và status system

Badge variants:

```text
Neutral
Brand
Success
Warning
Danger
Info
```

Status patterns:

- Dot + text.
- Icon + text.
- Soft background.
- Outline.

Playground phải có mapping cho:

- Order status.
- Payment status.
- Inventory status.
- Ticket status.
- User status.
- Report job status.

Không tạo màu status riêng ở từng module.

---

## 21. Card patterns

Patterns:

- Standard card.
- KPI card.
- Chart card.
- Table card.
- Alert card.
- Empty-state card.
- Interactive card.

Controls:

- Header on/off.
- Description.
- Actions.
- Padding density.
- Loading/error/empty.
- Long content.

Không dùng card lồng quá nhiều cấp.

---

## 22. Table playground

Kiểm tra:

- Header.
- Sorting.
- Selection.
- Pagination.
- Sticky columns.
- Row actions.
- Compact/comfortable density.
- Empty/loading/error.
- Horizontal scroll.
- Long values.

Dữ liệu mẫu:

- Mã đơn dài.
- Tên khách hàng dài.
- Giá trị tiền lớn.
- Badge nhiều trạng thái.
- Thiếu dữ liệu.

---

## 23. Dialog và drawer

Biến thể:

- Confirmation dialog.
- Form dialog.
- Destructive confirmation.
- Detail drawer.
- Full-screen mobile sheet.

Kiểm tra:

- Focus trap.
- Focus return.
- Escape behavior.
- Scroll lock.
- Long content.
- Nested overlay hạn chế.
- Loading/submission state.

Không mở nhiều dialog lồng nhau nếu có thể tránh.

---

## 24. Toast và notification feedback

Toast types:

```text
Success
Error
Warning
Info
Loading/Progress
```

Kiểm tra:

- Tiêu đề dài.
- Description.
- Action button.
- Dismiss.
- Stack nhiều toast.
- Screen-reader live region.

Không dùng toast thay thế inline validation hoặc persistent notification.

---

## 25. Tooltip và popover

Tooltip:

- Chỉ nội dung ngắn.
- Không chứa interactive controls.
- Có delay hợp lý.
- Hoạt động với keyboard.

Popover:

- Có interactive content.
- Có focus management.
- Có collision handling.

Playground phải kiểm tra vị trí ở bốn cạnh viewport.

---

## 26. Navigation patterns

Component:

- Sidebar.
- Header navigation.
- Breadcrumb.
- Tabs.
- Pagination.
- Command palette.

Kiểm tra:

- Active state.
- Collapsed state.
- Long labels.
- Permission-hidden items.
- Mobile drawer.
- Keyboard navigation.

---

## 27. KPI Card Lab

Fixtures:

```text
Normal value
Large currency
Negative value
Percentage
No comparison
No target
Stale data
Permission denied
API error
Loading
```

Kiểm tra:

- Compact value.
- Full value tooltip.
- Semantic trend.
- Target progress.
- Accessibility summary.

---

## 28. Chart Lab

Chart types:

- Line.
- Area.
- Bar.
- Stacked bar.
- Donut.
- Heatmap nếu được dùng.

Fixtures:

- Normal data.
- One point.
- Empty.
- Very large values.
- Negative values.
- Long labels.
- Many series.
- Stale data.

Yêu cầu:

- Có text summary hoặc table alternative.
- Tooltip keyboard-accessible nếu thư viện cho phép.
- Không dùng chart 3D.
- Không dùng màu quá gần nhau.

---

## 29. Loading State Lab

Patterns:

- Skeleton card.
- Skeleton table.
- Skeleton chart.
- Inline spinner.
- Button loading.
- Full-page initial loading.

Quy tắc:

- Skeleton giữ gần đúng layout cuối.
- Không hiển thị số 0 giả.
- Không dùng spinner toàn trang cho mọi trường hợp.
- Respect reduced motion.

---

## 30. Empty State Lab

Các loại:

- First-use empty.
- Filtered empty.
- Positive empty.
- Permission-limited empty.
- Setup-required empty.

Mỗi empty state gồm:

- Icon tùy chọn.
- Title.
- Description.
- Primary action khi phù hợp.
- Secondary help link tùy chọn.

Không dùng cùng một nội dung empty cho mọi module.

---

## 31. Error State Lab

Patterns:

- Inline field error.
- Widget error.
- Page error.
- Permission denied.
- Offline.
- Stale data.
- Partial data.

Kiểm tra:

- Message dễ hiểu.
- Retry action.
- Request ID khi phù hợp.
- Không lộ stack trace.
- Không thay lỗi bằng empty giả.

---

## 32. Responsive Lab

Viewport presets:

```text
1920 × 1080
1440 × 900
1366 × 768
1280 × 800
1024 × 768
768 × 1024
390 × 844
360 × 800
```

Chức năng:

- Chuyển viewport nhanh.
- Rotate portrait/landscape.
- Hiển thị breakpoint hiện tại.
- Overlay grid.
- Zoom canvas.
- Test sticky/overflow.

---

## 33. Content Stress Lab

Fixtures:

- Tên sản phẩm 80–120 ký tự.
- Tên khách hàng doanh nghiệp dài.
- Số tiền hàng nghìn tỷ.
- Mã đơn dài.
- Badge nhiều từ.
- Không có ảnh.
- Nội dung tiếng Việt nhiều dấu.
- Nội dung trộn số và ký hiệu.

Ví dụ:

```text
Tấm thạch cao chống ẩm tiêu chuẩn cao cấp dùng cho khu vực có độ ẩm lớn
```

Mục tiêu là phát hiện overflow, truncation và layout shift.

---

## 34. Accessibility Lab

Kiểm tra:

- Tab order.
- Focus visible.
- Keyboard-only operation.
- Screen-reader labels.
- Contrast.
- Zoom 200%.
- Reduced motion.
- High contrast mode nếu hỗ trợ.
- Touch target.
- Error association.

Có checklist tự động và manual.

---

## 35. Accessibility controls

Playground có thể hỗ trợ:

- Bật outline focus luôn hiển thị.
- Tắt animation.
- Tăng font scale.
- Mô phỏng color vision deficiency nếu công cụ cho phép.
- Hiển thị accessible name.
- Hiển thị heading structure.

Các control chỉ phục vụ kiểm thử, không phải setting của ứng dụng thật.

---

## 36. Theme testing

Nếu hỗ trợ dark mode:

- Mỗi token phải có mapping light/dark.
- Component không hardcode màu.
- Chart series đủ contrast ở cả hai theme.
- Image/logo có biến thể phù hợp.
- Không đảo màu ảnh sản phẩm.

Nếu chưa hỗ trợ dark mode, Playground vẫn phải giữ kiến trúc token để mở rộng sau.

---

## 37. Density testing

Density modes:

```text
Comfortable
Compact
```

Compact phù hợp bảng dữ liệu lớn.

Không giảm touch target hoặc focus target dưới chuẩn khi dùng compact mode.

---

## 38. Component documentation panel

Mỗi component hiển thị:

- Mục đích.
- Khi nào nên dùng.
- Khi nào không nên dùng.
- Props chính.
- States.
- Accessibility notes.
- Responsive behavior.
- Ví dụ code.
- Related patterns.
- Deprecated alternatives.

---

## 39. Code examples

Ví dụ phải:

- Dùng component thật.
- Dùng token thật.
- Không hardcode màu/spacing.
- Có type an toàn.
- Có accessibility props cần thiết.
- Không chứa dữ liệu production.

Ví dụ:

```tsx
<Button variant="primary" size="md">
  Tạo đơn hàng
</Button>
```

---

## 40. Copy code behavior

- Copy code có toast xác nhận.
- Không copy secret hoặc dữ liệu fixture nhạy cảm.
- Code phải khớp phiên bản component hiện tại.
- Có nút mở source/documentation nếu được phép.

---

## 41. Visual regression

Mỗi component cần baseline cho:

- Default.
- Hover/focus nếu công cụ hỗ trợ.
- Disabled.
- Loading.
- Error.
- Long content.
- Mobile.
- Dark mode nếu có.

Snapshot nên chạy ở viewport và font ổn định.

---

## 42. Visual regression naming

Quy ước:

```text
component__variant__state__viewport__theme
```

Ví dụ:

```text
button__primary__loading__desktop__light
kpi-card__currency__stale__tablet__light
```

Tên baseline phải ổn định để dễ review diff.

---

## 43. Component maturity

```ts
export type ComponentMaturity =
  | "experimental"
  | "beta"
  | "stable"
  | "deprecated";
```

Playground hiển thị badge maturity.

Quy tắc:

- Experimental không dùng trong luồng critical nếu chưa review.
- Beta cần test bổ sung.
- Stable được phép dùng rộng.
- Deprecated không dùng cho code mới.

---

## 44. Deprecation workflow

Khi component bị deprecated:

1. Gắn badge.
2. Ghi lý do.
3. Chỉ định component thay thế.
4. Ghi thời hạn migration.
5. Không xóa ngay khỏi Playground.
6. Theo dõi usage trong codebase nếu có công cụ.

---

## 45. Design token contract

```ts
export type DesignToken = {
  key: string;
  category: "color" | "spacing" | "typography" | "radius" | "shadow" | "motion";
  value: string | number;
  description?: string;
  status: "active" | "deprecated";
  aliases?: string[];
};
```

Không cho Playground sửa token production trực tiếp nếu chưa có quy trình review riêng.

---

## 46. Motion guidelines

Motion chỉ dùng khi giúp hiểu thay đổi trạng thái.

Nguyên tắc:

- Ngắn và nhẹ.
- Không cản trở thao tác.
- Respect `prefers-reduced-motion`.
- Không animate số liệu liên tục gây mất tập trung.
- Không dùng parallax hoặc hiệu ứng trang trí trong Dashboard quản trị.

---

## 47. Fixture registry

```ts
export type PlaygroundFixture = {
  key: string;
  name: string;
  description?: string;
  data: unknown;
  tags: string[];
};
```

Fixture groups:

- Normal.
- Empty.
- Loading.
- Error.
- Stale.
- Permission denied.
- Long content.
- Large values.
- Partial data.

Không dùng dữ liệu production làm fixture.

---

## 48. State persistence

Có thể lưu local:

- Theme.
- Viewport.
- Selected component.
- Control values không nhạy cảm.

Phải reset được.

Không lưu fixture có PII hoặc secret trong localStorage.

---

## 49. URL sharing

Có thể encode an toàn:

```text
/component/button?variant=primary&size=md&state=loading
```

Không đưa free-form content, secret hoặc PII vào URL.

URL giúp Design và QA chia sẻ đúng state cần review.

---

## 50. Search

Search hỗ trợ:

- Component name.
- Token name.
- Pattern.
- Status.
- Category.

Kết quả hiển thị maturity và category.

Không log raw search nếu người dùng nhập nội dung fixture tùy chỉnh.

---

## 51. Performance

Playground cần:

- Lazy-load nhóm component.
- Không render mọi fixture cùng lúc.
- Virtualize danh sách icon/token lớn.
- Tách chart library nặng.
- Không ảnh hưởng bundle production nếu route nội bộ không được dùng.

Có thể tách build hoặc dynamic import.

---

## 52. Security

- Không tải dữ liệu production.
- Không cho arbitrary code execution.
- Không render raw HTML thiếu sanitize.
- Không hiển thị environment secret.
- Không public route nếu không cần.
- Không cho Playground gọi mutation API nghiệp vụ thật.
- Fixture upload nếu có phải validate nghiêm ngặt.

---

## 53. Analytics

Có thể ghi tổng hợp:

```text
design_system.playground.viewed
design_system.component.opened
design_system.viewport.changed
design_system.example.copied
design_system.accessibility_check.run
```

Không gửi control content tùy chỉnh hoặc fixture data vào analytics.

---

## 54. Observability

Theo dõi:

- Component render failures.
- Registry mapping failures.
- Visual test failures.
- Accessibility test failures.
- Broken documentation links.
- Deprecated component usage nếu pipeline hỗ trợ.

---

## 55. Testing

### Unit

- Registry validation.
- Control schema.
- Fixture selection.
- Token mapping.
- URL state serialization.

### Component

- Preview rendering.
- Control updates.
- Theme switching.
- Viewport switching.
- Copy code.
- Error boundary.

### Integration

- Search → component → fixture.
- Accessibility controls.
- Visual baseline generation.
- Deprecated component banner.
- Registry key missing fallback.

### E2E

- Mở Playground.
- Tìm Button.
- Chuyển loading state.
- Chuyển mobile viewport.
- Kiểm tra keyboard focus.
- Copy example.
- Mở KPI stale fixture.
- Chạy accessibility check.

---

## 56. Visual QA baseline

```text
Foundations — Colors
Foundations — Typography
Buttons — All Variants
Inputs — All States
Badges — Status Matrix
Cards — Loading/Empty/Error
Table — Dense Data
Dialog — Destructive Confirmation
Drawer — Long Content
KPI Cards — Stress Fixtures
Charts — Empty/Large/Negative
Responsive Lab — Mobile
Accessibility Lab — Focus Visible
Dark Mode — Core Components
```

---

## 57. CI integration

Pipeline đề xuất:

- Typecheck registry.
- Lint token usage.
- Component tests.
- Accessibility tests.
- Visual regression.
- Broken-link check.
- Deprecated component warning.

Có thể block merge khi:

- Component stable bị visual regression chưa duyệt.
- Accessibility critical issue.
- Token không tồn tại.
- Registry key trùng.

---

## 58. Governance

Mỗi component cần:

- Owner.
- Maturity.
- Documentation.
- Test coverage.
- Accessibility review.
- Change log.
- Migration note khi breaking change.

Không thêm component mới chỉ vì một trang cần style khác nếu có thể mở rộng component hiện có.

---

## 59. AI coding agent requirements

AI coding agent phải:

- Kiểm tra Playground và registry trước khi tạo component mới.
- Không hardcode màu, spacing hoặc typography ngoài token.
- Không tạo variant mới thiếu tài liệu và test.
- Không dùng fixture production.
- Không bỏ loading, empty, error, stale và long-content states.
- Không bỏ keyboard hoặc accessibility testing.
- Báo rõ component nào được tái sử dụng và component nào được bổ sung.
- Không tuyên bố Design System hoàn chỉnh nếu visual regression và accessibility chưa được kiểm tra.

---

## 60. Acceptance checklist

- [ ] Có Foundations cho color, typography, spacing, radius, shadow và icons.
- [ ] Có component registry tĩnh.
- [ ] Có control panel theo whitelist.
- [ ] Có playground cho buttons, inputs, selects, badges, cards và tables.
- [ ] Có dialog, drawer, toast, tooltip và navigation patterns.
- [ ] Có KPI, chart, loading, empty và error labs.
- [ ] Có Responsive Lab.
- [ ] Có Content Stress Lab.
- [ ] Có Accessibility Lab.
- [ ] Có light/dark testing nếu hỗ trợ.
- [ ] Có density testing.
- [ ] Có documentation và code examples.
- [ ] Có visual regression baseline.
- [ ] Có component maturity và deprecation workflow.
- [ ] Có fixture registry an toàn.
- [ ] Không dùng dữ liệu production.
- [ ] Có security, analytics, observability và testing.
- [ ] Có CI integration và governance.

---

## 61. Những điều không được làm

- Không dùng Playground làm page builder.
- Không cho chạy JavaScript, HTML hoặc CSS tùy ý.
- Không kết nối dữ liệu production nhạy cảm.
- Không cho mutation nghiệp vụ thật.
- Không hardcode raw hex hoặc pixel trong ví dụ.
- Không tạo component mới khi component hiện tại có thể mở rộng hợp lý.
- Không dùng màu làm tín hiệu duy nhất.
- Không bỏ edge cases tiếng Việt dài và giá trị lớn.
- Không bỏ keyboard, focus hoặc screen-reader checks.
- Không xóa component deprecated trước khi có migration.
- Không để Playground làm tăng bundle production không cần thiết.

---

## 62. Kết luận

Design System Playground của Cynca VLXD phải là nguồn tham chiếu trực quan và kỹ thuật cho toàn bộ component, token và UI pattern của Dashboard. Mỗi component cần được kiểm tra với trạng thái thực tế, nội dung tiếng Việt, responsive, accessibility và visual regression trước khi sử dụng rộng rãi trong các module nghiệp vụ.

File tiếp theo đề xuất:

```text
README.md
43-Onboarding-Help-Center.md
```