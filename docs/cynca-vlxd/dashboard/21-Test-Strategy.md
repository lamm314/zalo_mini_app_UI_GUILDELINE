# Cynca VLXD Admin Dashboard — Test Strategy

> Phiên bản: 1.0  
> Phạm vi: Chiến lược kiểm thử toàn diện cho Dashboard quản trị Cynca VLXD  
> Stack mục tiêu: Next.js, React, TypeScript, Tailwind CSS  
> Đối tượng sử dụng: Frontend, Backend, QA, Product Owner, AI coding agent  
> Tài liệu liên quan: toàn bộ thư mục `docs/cynca-vlxd/dashboard/`

---

## 1. Mục tiêu

Chiến lược kiểm thử phải bảo đảm Dashboard:

- Hiển thị đúng dữ liệu.
- Hoạt động đúng theo phân quyền.
- Không làm mất hoặc ghi sai trạng thái nghiệp vụ.
- Ổn định khi API chậm, lỗi hoặc trả dữ liệu thiếu.
- Responsive đúng từ desktop đến mobile fallback.
- Dùng được bằng bàn phím và công nghệ hỗ trợ.
- Không phát sinh regression khi mở rộng module.
- Có đủ bằng chứng trước khi phát hành production.

Testing không chỉ nhằm tăng coverage. Mỗi test phải bảo vệ một rủi ro thực tế.

---

## 2. Nguyên tắc kiểm thử

### 2.1 Test behavior, không test implementation detail

Ưu tiên kiểm tra:

- Người dùng nhìn thấy gì.
- Người dùng có thể làm gì.
- Hệ thống gửi request nào.
- Kết quả nghiệp vụ thay đổi ra sao.

Hạn chế kiểm tra:

- Tên state nội bộ.
- Cấu trúc hook không ảnh hưởng hành vi.
- Class name chi tiết nếu visual regression đã bao phủ.

### 2.2 Test theo mức độ rủi ro

Luồng tài chính, phân quyền và cập nhật trạng thái đơn hàng cần độ bao phủ cao hơn hover animation.

### 2.3 Không phụ thuộc API production

Test phải dùng:

- Mock service.
- Test API.
- Fixture ổn định.
- MSW hoặc công cụ tương đương.

### 2.4 Kết quả phải tái lập

Không dùng:

- Dữ liệu random không seed.
- Thời gian thực không mock.
- Network phụ thuộc bên ngoài.

---

## 3. Test pyramid

```text
            E2E
         Integration
      Component Tests
        Unit Tests
```

Tỷ lệ tham khảo:

| Loại test | Tỷ lệ tương đối |
|---|---:|
| Unit | 35% |
| Component | 30% |
| Integration | 20% |
| E2E | 10% |
| Visual / Accessibility / Performance | 5% bổ sung |

Không cần ép đúng tỷ lệ tuyệt đối. Mục tiêu là tránh phụ thuộc quá nhiều vào E2E chậm và dễ lỗi.

---

## 4. Công cụ đề xuất

### Unit và component

- Vitest hoặc Jest.
- React Testing Library.
- Testing Library User Event.

### API mocking

- Mock Service Worker.

### E2E

- Playwright.

### Visual regression

- Playwright screenshots.
- Chromatic hoặc công cụ tương đương nếu dùng Storybook.

### Accessibility

- axe-core.
- Storybook addon a11y.
- Playwright + axe.

### Performance

- Lighthouse CI.
- Web Vitals monitoring.

Không cài đồng thời nhiều công cụ trùng chức năng nếu repository đã có stack kiểm thử ổn định.

---

## 5. Test environments

### Local

Dùng cho:

- Unit.
- Component.
- Storybook.
- Debug E2E.

### CI

Dùng cho:

- Lint.
- Typecheck.
- Unit.
- Component.
- Integration.
- E2E chính.
- Accessibility automation.
- Visual regression.

### Staging

Dùng cho:

- UAT.
- API contract thật.
- Permission thật.
- Performance gần production.
- Cross-browser.

### Production smoke test

Chỉ kiểm tra read-only hoặc thao tác an toàn sau deploy.

---

## 6. Test data strategy

Fixture phải bao phủ:

- Admin.
- Sales manager.
- Warehouse user.
- Accountant.
- User không đủ quyền.
- Dashboard có dữ liệu đầy đủ.
- Dashboard mới chưa có dữ liệu.
- Dữ liệu stale.
- Dữ liệu rất lớn.
- Nội dung tiếng Việt dài.
- API lỗi từng widget.

Cấu trúc đề xuất:

```text
tests/fixtures/
├── users.ts
├── permissions.ts
├── dashboard.ts
├── orders.ts
├── inventory.ts
├── notifications.ts
└── errors.ts
```

---

## 7. Unit tests

### 7.1 Formatting

Bắt buộc kiểm tra:

- `formatCurrency`.
- `formatCompactCurrency`.
- `formatNumber`.
- `formatPercent`.
- `formatDate`.
- `formatDateTime`.
- `formatRelativeTime`.
- `formatQuantity`.

Cases:

- Zero.
- Negative.
- Very large value.
- Decimal quantity.
- Invalid date.
- Different timezone.

### 7.2 Status mapping

Kiểm tra:

- Order status label.
- Payment status label.
- Inventory semantic.
- Badge variant.
- Icon mapping.
- Unknown status fallback.

### 7.3 Permission helpers

Kiểm tra:

- One permission.
- Any permission.
- All permissions.
- Empty permission list.
- Permission denied.
- Scope by branch.

### 7.4 Query serialization

Kiểm tra:

- Date range.
- Branch.
- Warehouse.
- Compare mode.
- Sort.
- Pagination.
- Query parameter encoding.

### 7.5 Adapters

Kiểm tra:

- API DTO chuyển đúng frontend model.
- Missing optional field.
- Unknown enum.
- Null và zero.
- UpdatedAt và stale calculation.

---

## 8. Component tests — UI primitives

### Button

- Render đúng label.
- Click đúng một lần.
- Disabled không click.
- Loading giữ chiều rộng và chặn click lặp.
- Icon-only có accessible name.

### Input

- Label liên kết đúng.
- Helper text.
- Error text.
- `aria-invalid`.
- Keyboard input.

### Select

- Mở bằng keyboard.
- Chọn option.
- Escape đóng.
- Loading, empty và disabled.

### Tabs

- Arrow key navigation.
- Active state.
- `aria-selected`.

### Modal / Drawer

- Focus trap.
- Escape close.
- Focus return.
- Scroll behavior.

---

## 9. Component tests — Sidebar

Cases bắt buộc:

- Expanded render đúng labels.
- Collapsed chỉ hiển thị icon.
- Tooltip xuất hiện khi focus.
- Active route đúng.
- Route con active menu cha.
- Submenu mở và đóng.
- Badge `99+`.
- Permission-limited menu.
- Drawer mở dưới 1024px.
- Escape đóng drawer.
- Focus return về trigger.

---

## 10. Component tests — Header

Kiểm tra:

- Search focus bằng shortcut.
- Search debounce.
- Request cũ bị hủy khi query đổi.
- Grouped results.
- No results.
- Search error.
- Date range selection.
- Branch filter theo quyền.
- Export loading.
- Notification unread count.
- Chỉ một dropdown mở cùng lúc.

---

## 11. Component tests — KPI cards

Cases:

- Currency value.
- Number value.
- Percentage value.
- Positive semantic trend.
- Negative business trend.
- Neutral trend.
- Loading không hiện số 0.
- Empty.
- Error.
- Stale.
- Long value.
- Interactive link.
- Permission hidden.

Accessibility:

- Accessible name đọc đủ label, value và trend.
- Icon decorative không bị screen reader đọc.

---

## 12. Component tests — Charts

Kiểm tra:

- Render series đúng.
- Tooltip format đúng.
- Empty không render axis giả.
- Error có retry.
- Stale notice.
- Legend ẩn khi không cần.
- Comparison series.
- Reduced motion.
- Summary text tồn tại.

Không test chi tiết SVG path pixel bằng unit test; dùng visual regression cho phần đó.

---

## 13. Component tests — Product widgets

### Product performance

- Ranking đúng.
- Metric selector đổi dữ liệu.
- Long product name.
- Missing image fallback.
- Large revenue.
- Loading, empty, error, stale.

### Inventory alerts

- Severity ordering.
- Negative stock.
- Out of stock.
- Low stock.
- Stale inventory.
- Permission-limited action.
- Quick action callback.

---

## 14. Component tests — Orders table

Cases:

- Render 8–10 rows.
- Sort createdAt.
- Status filter.
- Payment và order status tách biệt.
- Total amount format.
- Overdue indicator.
- Unread row.
- Long customer name.
- Loading rows.
- Empty dataset.
- Empty filter result.
- Error state.
- Permission-limited actions.
- Horizontal overflow wrapper.

Accessibility:

- Table caption.
- Header scope.
- `aria-sort`.
- Row actions có tên cụ thể.

---

## 15. Component tests — Notifications

Kiểm tra:

- Unread badge.
- Open/close dropdown.
- Tabs filter.
- Mark one read.
- Mark all read.
- Rollback khi API lỗi.
- Read và resolved khác nhau.
- Grouping theo thời gian.
- Deduplication behavior.
- Empty và error.
- Toast timeout.
- Toast pause khi hover/focus.
- Live region phù hợp.

---

## 16. Integration tests

### 16.1 Dashboard scope

Luồng:

```text
Chọn date range
→ URL cập nhật
→ KPI refetch
→ chart refetch
→ orders refetch
→ loading chỉ vùng cần thiết
```

### 16.2 Branch scope

- Admin đổi branch.
- Dữ liệu mọi widget đổi đúng.
- Search chỉ trả dữ liệu branch được phép.
- Export dùng đúng branch.

### 16.3 Permission flow

- Warehouse user không thấy profit KPI.
- Không thấy finance menu.
- Request finance endpoint trả forbidden nếu gọi trực tiếp.

### 16.4 Order quick view

- Click row mở drawer.
- Tải order detail.
- Loading trong drawer.
- Error retry.
- Close trả focus.

### 16.5 Notification real-time

- Event mới tăng unread count.
- Item được thêm.
- Không tự mở dropdown.
- Không tạo toast hàng loạt.

---

## 17. API contract tests

Kiểm tra:

- Envelope success/error.
- ISO 8601 dates.
- Enum hợp lệ.
- UpdatedAt tồn tại.
- Total order status khớp tổng item.
- Percentage hợp lý.
- Available actions phù hợp permission.
- Branch scope không bị vượt.
- Export contract đúng.
- Conflict error đúng format.

Có thể dùng schema validation hoặc consumer-driven contract testing nếu backend và frontend phát triển độc lập.

---

## 18. E2E critical journeys

### Journey 1 — Admin xem Dashboard

1. Đăng nhập.
2. Mở Dashboard.
3. Thấy 4 KPI.
4. Thấy chart.
5. Thấy recent orders.
6. Không có console error.

### Journey 2 — Đổi khoảng thời gian

1. Chọn 30 ngày.
2. URL cập nhật.
3. KPI và chart cập nhật.
4. Refresh trang vẫn giữ filter.

### Journey 3 — Tìm đơn hàng

1. Mở global search.
2. Nhập mã đơn.
3. Chọn kết quả bằng keyboard.
4. Mở đúng order detail.

### Journey 4 — Xử lý đơn

1. Mở recent order.
2. Xem quick view.
3. Chọn action hợp lệ.
4. Xác nhận nếu destructive.
5. Trạng thái cập nhật sau backend success.

### Journey 5 — Cảnh báo tồn kho

1. Warehouse user đăng nhập.
2. Thấy low-stock widget.
3. Mở SKU.
4. Thực hiện action được phép.

### Journey 6 — Export

1. Chọn format XLSX.
2. Tạo export job.
3. Thấy progress.
4. Nhận notification hoàn thành.

### Journey 7 — Notifications

1. Mở dropdown.
2. Lọc chưa đọc.
3. Đánh dấu đã đọc.
4. Badge giảm.

---

## 19. E2E negative journeys

Bắt buộc kiểm tra:

- Session hết hạn.
- API 500 ở một widget.
- API timeout.
- User truy cập route không có quyền.
- Mutation conflict.
- Export failed.
- Search no result.
- Image broken.
- Network offline rồi online lại.

---

## 20. Visual regression strategy

Baseline bắt buộc:

- Dashboard 1440 × 900.
- Dashboard 1024 × 768.
- Sidebar expanded.
- Sidebar collapsed.
- Header search open.
- KPI states.
- Revenue chart.
- Orders table.
- Notification dropdown.
- Order drawer.
- Loading Dashboard.
- Empty Dashboard.
- Partial error Dashboard.

Quy tắc:

- Không dùng random data.
- Freeze current time.
- Mask dynamic IDs nếu cần.
- Font phải load xong.
- Baseline update cần reviewer duyệt.

---

## 21. Accessibility test strategy

### Automated

- axe trong component tests.
- axe trong E2E trang chính.
- Storybook addon a11y.

### Manual

- Keyboard-only.
- VoiceOver hoặc NVDA.
- Zoom 200%.
- Reduced motion.
- High contrast.

Các lỗi tự động không được waiving rộng nếu chưa có lý do rõ.

---

## 22. Responsive test strategy

Kiểm tra viewport:

```text
1920 × 1080
1440 × 900
1366 × 768
1280 × 800
1024 × 768
820 × 1180
768 × 1024
390 × 844
```

Kiểm tra:

- Sidebar state.
- Header overflow.
- KPI columns.
- Chart layout.
- Table wrapper.
- Drawer/modal size.
- Touch target.
- Long content.

---

## 23. Performance test strategy

### Mục tiêu tham khảo

```text
LCP < 2,5s
CLS < 0,1
INP < 200ms
```

### Kiểm tra

- Initial Dashboard load.
- Filter update.
- Search interaction.
- Drawer open.
- Chart render.
- Long table render.

### Network profiles

- Fast 4G.
- Slow 4G.
- High latency API.

Performance regression phải được theo dõi theo xu hướng, không chỉ một lần đo.

---

## 24. Security test cases

- User không quyền gọi trực tiếp finance endpoint.
- Branch ID giả không trả dữ liệu ngoài scope.
- Export không vượt permission.
- Search không lộ customer ngoài phạm vi.
- HTML payload không tạo XSS.
- Token không xuất hiện trong client logs.
- Session timeout xử lý đúng.
- CSRF protection cho mutation nếu kiến trúc yêu cầu.

---

## 25. Concurrency và conflict tests

Kịch bản:

1. Hai user mở cùng một đơn.
2. User A cập nhật trạng thái.
3. User B gửi thay đổi dựa trên version cũ.
4. Backend trả conflict.
5. Frontend thông báo và yêu cầu reload.

Không được ghi đè âm thầm.

---

## 26. Timezone tests

Kiểm tra:

- Date range đầu/ngày cuối.
- Đơn tạo gần 00:00.
- So sánh kỳ trước.
- Browser timezone khác server timezone.
- DST nếu tương lai hỗ trợ thị trường khác.

Mặc định Việt Nam:

```text
Asia/Ho_Chi_Minh
```

---

## 27. Data integrity tests

- KPI revenue khớp report endpoint trong cùng scope.
- Order status total khớp tổng trạng thái.
- Best products ranking đúng metric.
- Inventory alert dựa trên available quantity và minimum.
- Notification count khớp unread items.
- Export dữ liệu khớp filter hiện tại.

---

## 28. Mutation test matrix

| Mutation | Optimistic | Confirmation | Rollback | Conflict handling |
|---|---|---|---|---|
| Mark read | Có thể | Không | Có | Không cần |
| Assign user | Có thể | Không | Có | Có thể |
| Confirm order | Không | Tùy nghiệp vụ | Bắt buộc | Bắt buộc |
| Cancel order | Không | Bắt buộc | Bắt buộc | Bắt buộc |
| Refund | Không | Bắt buộc | Bắt buộc | Bắt buộc |
| Update stock | Không | Tùy nghiệp vụ | Bắt buộc | Bắt buộc |

---

## 29. CI test pipeline

Thứ tự đề xuất:

```text
Install
→ Lint
→ Typecheck
→ Unit tests
→ Component tests
→ Build
→ E2E critical journeys
→ Accessibility scan
→ Visual regression
→ Performance budget
```

Có thể chạy song song sau bước install để giảm thời gian.

---

## 30. Pull request gates

PR không được merge khi:

- Lint fail.
- Typecheck fail.
- Unit hoặc component critical tests fail.
- E2E critical journey fail.
- Có accessibility serious/critical issue mới.
- Visual diff chưa được duyệt.
- Coverage giảm mạnh không có giải thích.
- Có console error trong test chính.

---

## 31. Coverage policy

Coverage là tín hiệu, không phải mục tiêu duy nhất.

Ngưỡng tham khảo:

```text
Statements: 80%
Branches: 75%
Functions: 80%
Lines: 80%
```

Module rủi ro cao nên cao hơn:

- Permissions.
- Order workflow.
- Money formatting.
- Export scope.
- Conflict handling.

Không viết test vô nghĩa chỉ để tăng coverage.

---

## 32. Flaky test policy

Khi test flaky:

1. Không rerun vô hạn để làm xanh.
2. Ghi issue.
3. Xác định nguyên nhân.
4. Freeze time hoặc ổn định fixture.
5. Sửa selector và wait condition.
6. Chỉ quarantine tạm thời với owner và deadline.

Không bỏ qua flaky test lâu dài.

---

## 33. Selector policy cho E2E

Ưu tiên:

1. Role.
2. Accessible name.
3. Label.
4. Text ổn định.
5. `data-testid` khi không có semantic selector phù hợp.

Không chọn theo class Tailwind hoặc DOM nesting dễ thay đổi.

Ví dụ tốt:

```ts
page.getByRole("button", { name: "Xuất báo cáo" })
```

---

## 34. Network mocking policy

MSW handlers phải bao phủ:

- Success.
- Empty.
- Error.
- Slow response.
- Stale data.
- Permission denied.
- Conflict.

Handler nên tái sử dụng giữa Storybook, component test và E2E mock mode nếu phù hợp.

---

## 35. Test clock policy

Freeze time cho:

- Relative time.
- Date range.
- Stale detection.
- Notification grouping.
- Screenshot baseline.

Ví dụ:

```text
2026-08-06T17:00:00+07:00
```

Không để test phụ thuộc thời gian máy chạy CI.

---

## 36. Browser matrix

### Mỗi PR

- Chromium.

### Nightly hoặc pre-release

- Chromium.
- Firefox.
- WebKit.

### Manual release QA

- Chrome Windows.
- Edge Windows.
- Safari macOS.
- Chrome macOS.

---

## 37. Smoke tests sau deploy

Kiểm tra an toàn:

- Login page tải được.
- Dashboard tải được.
- KPI endpoint trả dữ liệu.
- Search mở được.
- Notification count tải được.
- Không có lỗi JS nghiêm trọng.

Không tự động tạo hoặc hủy đơn thật trong production smoke test trừ khi có test tenant riêng.

---

## 38. UAT strategy

UAT theo vai trò:

### Admin

- Xem toàn hệ thống.
- Đổi branch.
- Xem finance KPI.
- Export.

### Sales

- Xem assigned orders.
- Search customer/order.
- Không thấy admin settings.

### Warehouse

- Xem inventory alerts.
- Xử lý stock action.
- Không thấy gross profit.

### Accountant

- Xem payment và debt.
- Export đúng scope.

UAT phải dùng kịch bản nghiệp vụ, không chỉ click kiểm tra giao diện.

---

## 39. Bug reporting requirements

Bug phải gồm:

- Tiêu đề rõ.
- Môi trường.
- Role.
- Branch scope.
- Date range.
- Bước tái hiện.
- Kết quả thực tế.
- Kết quả mong đợi.
- Screenshot hoặc video.
- Request ID nếu có.
- Severity.

---

## 40. Bug severity

### Blocker

- Không đăng nhập.
- Dashboard không tải.
- Lộ dữ liệu ngoài quyền.
- Sai số tài chính nghiêm trọng.

### Critical

- Không xử lý được order workflow chính.
- Export sai scope.
- Mutation ghi sai trạng thái.
- Mất dữ liệu.

### Major

- Widget lỗi.
- Responsive vỡ breakpoint chính.
- Keyboard flow chính không dùng được.

### Minor

- Sai spacing nhỏ.
- Tooltip lệch.
- Animation chưa mượt.

---

## 41. Release criteria

Không phát hành khi:

- Còn Blocker hoặc Critical.
- Critical journey E2E fail.
- Permission tests fail.
- Contract tests fail.
- Có accessibility serious/critical issue chưa chấp thuận.
- Visual regression chưa review.
- Performance vượt budget đáng kể.

Có thể phát hành với Minor hoặc một số Major đã được Product Owner chấp thuận rõ ràng.

---

## 42. Test ownership

| Khu vực | Owner chính | Owner phối hợp |
|---|---|---|
| Unit | Developer | Reviewer |
| Component | Frontend | QA |
| API contract | Backend + Frontend | QA |
| E2E | QA | Frontend/Backend |
| Accessibility | Frontend + QA | Design |
| Visual regression | Frontend + Design | QA |
| Performance | Frontend | DevOps |
| Security | Backend/Security | Frontend |
| UAT | Product Owner | QA |

---

## 43. Test evidence

Mỗi release nên lưu:

- CI run link.
- Test report.
- E2E result.
- Accessibility report.
- Visual diff approval.
- Performance report.
- UAT sign-off.
- Known issues.

Không chỉ ghi `đã test` mà thiếu bằng chứng.

---

## 44. AI coding agent testing requirements

Khi AI triển khai code, bắt buộc báo:

- Test nào đã thêm.
- Lệnh nào đã chạy.
- Kết quả thật.
- Test nào chưa chạy được.
- Lý do.
- Hạng mục còn thiếu.

AI không được tuyên bố `all tests passed` nếu chưa thực sự chạy.

Prompt tối thiểu:

```text
Sau khi sửa code, hãy chạy lint, typecheck, unit tests và các E2E liên quan. Báo chính xác lệnh và kết quả. Không suy đoán kết quả test.
```

---

## 45. Test file structure đề xuất

```text
src/
├── components/
│   └── dashboard/
│       ├── kpi-card.tsx
│       └── kpi-card.test.tsx
├── lib/
│   └── formatting/
│       ├── currency.ts
│       └── currency.test.ts

tests/
├── e2e/
│   ├── dashboard.spec.ts
│   ├── orders.spec.ts
│   ├── notifications.spec.ts
│   └── permissions.spec.ts
├── integration/
│   ├── dashboard-scope.test.tsx
│   └── order-mutation.test.tsx
├── fixtures/
└── helpers/
```

---

## 46. Acceptance checklist

- [ ] Có test pyramid rõ ràng.
- [ ] Unit test bao phủ formatter, mapping, permission và adapter.
- [ ] Component test bao phủ states và keyboard behavior.
- [ ] Integration test bao phủ scope, permission và mutation.
- [ ] E2E có critical journeys và negative journeys.
- [ ] API contract được xác thực.
- [ ] Visual regression dùng fixture ổn định.
- [ ] Accessibility có automated và manual test.
- [ ] Responsive test đủ viewport.
- [ ] Performance có budget.
- [ ] Security test bao phủ scope và XSS.
- [ ] Conflict và concurrency được kiểm tra.
- [ ] Timezone được kiểm tra.
- [ ] CI có merge gates.
- [ ] Flaky test có policy.
- [ ] Production smoke test an toàn.
- [ ] UAT theo vai trò hoàn tất trước release.
- [ ] Test evidence được lưu.

---

## 47. Những điều không được làm

- Không chỉ dựa vào E2E.
- Không viết test theo implementation detail không cần thiết.
- Không gọi API production trong test thông thường.
- Không dùng random data không seed.
- Không phụ thuộc giờ máy CI.
- Không chọn DOM bằng class Tailwind.
- Không bỏ qua permission tests.
- Không tự cập nhật visual baseline khi diff fail.
- Không dùng coverage để thay thế chất lượng test.
- Không báo test passed nếu chưa chạy.
- Không phát hành khi còn Blocker hoặc Critical.

---

## 48. Kết luận

Test Strategy của Cynca VLXD Dashboard phải bảo vệ đồng thời dữ liệu, phân quyền, workflow, giao diện, accessibility và hiệu năng. Một Dashboard được xem là sẵn sàng production khi có bằng chứng kiểm thử đủ sâu ở nhiều lớp, không chỉ khi giao diện hiển thị đúng trong một kịch bản mặc định.

File tiếp theo đề xuất:

```text
README.md
22-Release-Operations.md
```