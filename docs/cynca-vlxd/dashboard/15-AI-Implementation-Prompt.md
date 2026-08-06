# Cynca VLXD Admin Dashboard — AI Implementation Prompt

> Phiên bản: 1.0  
> Mục đích: Prompt chuẩn để giao cho Codex, Cursor hoặc AI coding agent triển khai Dashboard  
> Phạm vi: Frontend Dashboard quản trị Cynca VLXD  
> Tài liệu nguồn: toàn bộ thư mục `docs/cynca-vlxd/dashboard/`

---

## 1. Cách sử dụng

Sao chép prompt trong mục 2 và gửi cho AI coding agent trong repository chứa mã nguồn Dashboard.

Trước khi triển khai, AI phải đọc đầy đủ các file:

```text
docs/cynca-vlxd/dashboard/01-Overview.md
docs/cynca-vlxd/dashboard/02-Layout.md
docs/cynca-vlxd/dashboard/03-Sidebar.md
docs/cynca-vlxd/dashboard/04-Header.md
docs/cynca-vlxd/dashboard/05-KPI-Cards.md
docs/cynca-vlxd/dashboard/06-Charts.md
docs/cynca-vlxd/dashboard/07-Product-Widgets.md
docs/cynca-vlxd/dashboard/08-Orders-Table.md
docs/cynca-vlxd/dashboard/09-Notification.md
docs/cynca-vlxd/dashboard/10-Animation.md
docs/cynca-vlxd/dashboard/11-Responsive.md
docs/cynca-vlxd/dashboard/12-Accessibility.md
docs/cynca-vlxd/dashboard/13-Tailwind-Tokens.md
docs/cynca-vlxd/dashboard/14-Implementation-Checklist.md
```

AI không được chỉ đọc file này rồi tự suy đoán phần còn lại.

---

## 2. Prompt triển khai hoàn chỉnh

```text
Bạn đang làm việc trong repository của hệ thống Cynca VLXD.

Nhiệm vụ của bạn là triển khai giao diện Admin Dashboard theo đúng bộ Design Specification trong thư mục:

docs/cynca-vlxd/dashboard/

Hãy đọc toàn bộ các file từ 01-Overview.md đến 14-Implementation-Checklist.md trước khi sửa mã nguồn.

MỤC TIÊU

Dựng một Dashboard quản trị vật liệu xây dựng hiện đại, chuyên nghiệp, rõ ràng và có thể sử dụng thực tế. Giao diện phải bám sát guideline, không tự ý thay đổi màu thương hiệu, spacing, radius, breakpoint, trạng thái hoặc cấu trúc thông tin.

STACK ƯU TIÊN

- Next.js
- React
- TypeScript strict mode
- Tailwind CSS
- Lucide Icons
- Recharts hoặc thư viện chart hiện có trong repository
- class-variance-authority, clsx và tailwind-merge nếu repository đã sử dụng

Không cài thêm thư viện mới nếu chức năng hiện tại có thể đáp ứng.

NGUYÊN TẮC BẮT BUỘC

1. Đọc và tuân thủ toàn bộ Design Specification.
2. Dùng tên thương hiệu chính xác: Cynca VLXD.
3. Màu chủ đạo: #0B57F0.
4. Nền trang: #F6F8FC.
5. Card nền trắng, border nhẹ, radius 20px.
6. Dashboard dùng grid 12 cột.
7. Sidebar expanded 264px, collapsed 80px.
8. Header cao 72px và sticky.
9. Main content tối đa 1600px.
10. Dùng Inter hoặc font system fallback tương đương.
11. Không dùng emoji làm icon giao diện.
12. Không dùng biểu đồ 3D.
13. Không dùng gradient mạnh, glow hoặc shadow đậm.
14. Không hardcode màu và spacing lặp lại trong component.
15. Không dùng dữ liệu production hardcode trong component.
16. Không bỏ qua loading, empty, error, stale và permission state.
17. Không bỏ qua responsive và accessibility.

PHẠM VI GIAO DIỆN CẦN TRIỂN KHAI

1. Dashboard shell
   - Sidebar
   - Header
   - Main content
   - Page toolbar

2. Sidebar
   - Logo Cynca VLXD
   - Branch selector
   - Menu theo nhóm
   - Active, hover, focus và collapsed state
   - User profile area
   - Drawer dưới 1024px

3. Header
   - Global search
   - Date range selector
   - Branch filter
   - Export report
   - Notification button
   - User menu

4. KPI cards
   - Doanh thu thuần
   - Đơn hàng
   - Khách hàng
   - Lợi nhuận gộp
   - Trend và comparison label
   - Tooltip công thức

5. Charts
   - Revenue line hoặc area chart
   - Order status donut chart
   - Tooltip
   - Legend
   - Filter thời gian
   - Summary hỗ trợ accessibility

6. Product widgets
   - Sản phẩm bán chạy
   - Cảnh báo tồn kho
   - Danh mục nổi bật hoặc chất lượng dữ liệu sản phẩm

7. Orders table
   - Mã đơn
   - Khách hàng
   - Thanh toán
   - Trạng thái
   - Tổng tiền
   - Thời gian
   - Hành động
   - Quick view drawer nếu kiến trúc hiện tại phù hợp

8. Notification system
   - Unread badge
   - Dropdown
   - Actionable notification widget
   - Toast feedback

RESPONSIVE

- ≥1440px: sidebar expanded, KPI 4 cột, chart 8/4.
- 1280–1439px: giảm padding, KPI 4 hoặc 2 cột tùy chiều rộng.
- 1024–1279px: sidebar collapsed, KPI 2 cột, chart xếp dọc khi cần.
- <1024px: sidebar drawer, layout một hoặc hai cột.
- Không để horizontal scroll toàn trang.
- Table được phép scroll trong wrapper riêng.

ACCESSIBILITY

- Dùng semantic HTML.
- Có skip link.
- Chỉ một main landmark.
- Có h1 rõ ràng.
- Mọi chức năng chính dùng được bằng keyboard.
- Icon-only button có aria-label và tooltip.
- Modal, drawer và dropdown quản lý focus đúng.
- Status không chỉ dùng màu.
- Chart có summary text hoặc bảng dữ liệu thay thế.
- Table dùng caption, thead, tbody và th scope=col.
- Hỗ trợ prefers-reduced-motion.
- Mục tiêu WCAG 2.2 AA.

DỮ LIỆU VÀ STATE

Nếu backend API đã tồn tại:

- Tái sử dụng API hiện tại.
- Không thay đổi contract tùy tiện.
- Tạo type TypeScript cho response.
- Xử lý loading, empty, error và stale data.
- Dùng updatedAt cho thông tin cần cảnh báo dữ liệu cũ.

Nếu API chưa tồn tại:

- Tạo data adapter hoặc mock service tách riêng.
- Không đặt dữ liệu mẫu trực tiếp trong JSX.
- Ghi rõ vị trí cần thay bằng API thật.
- Không giả vờ đã kết nối backend.

PERMISSIONS

- Menu và action phải hiển thị theo permission.
- Không chỉ ẩn ở frontend; ghi rõ backend vẫn phải kiểm tra quyền.
- Không hiển thị KPI tài chính cho vai trò không được phép.
- Không để notification dẫn đến entity ngoài quyền truy cập.

MOTION

- Hover: 120–180ms.
- Dropdown: dưới 200ms.
- Sidebar collapse: khoảng 200ms.
- Drawer: 220–280ms.
- Chart initial animation: 500–700ms.
- Không dùng bounce, confetti hoặc parallax.
- Hỗ trợ reduced motion.

YÊU CẦU KỸ THUẬT

- TypeScript strict.
- Không có lỗi lint.
- Không có lỗi typecheck.
- Không có lỗi console trong luồng chính.
- Component có trách nhiệm rõ ràng.
- Không tạo file quá lớn nếu có thể tách hợp lý.
- Dùng mapping tập trung cho status và badge.
- Dùng semantic token từ 13-Tailwind-Tokens.md.
- Dùng error boundary hoặc widget-level error handling hợp lý.
- Giữ shell ổn định khi route loading.

TRÌNH TỰ THỰC HIỆN

1. Khảo sát repository và xác định stack hiện tại.
2. Đọc toàn bộ guideline.
3. Tóm tắt kế hoạch triển khai ngắn gọn.
4. Xác định file sẽ tạo hoặc sửa.
5. Tạo design tokens và UI primitives trước.
6. Dựng Dashboard shell.
7. Dựng Sidebar và Header.
8. Dựng KPI cards.
9. Dựng Charts.
10. Dựng Product widgets.
11. Dựng Orders table.
12. Dựng Notifications.
13. Bổ sung loading, empty, error, stale và permission states.
14. Hoàn thiện responsive.
15. Hoàn thiện accessibility.
16. Chạy lint, typecheck và test.
17. So sánh kết quả với 14-Implementation-Checklist.md.

KHÔNG ĐƯỢC LÀM

- Không thay đổi guideline vì sở thích cá nhân.
- Không dùng template admin chung không tùy chỉnh.
- Không sao chép UI của Stripe, Shopify hoặc Linear.
- Không dùng màu ngẫu nhiên cho từng card.
- Không dùng icon khác bộ.
- Không để giao diện chỉ đẹp với dữ liệu mẫu ngắn.
- Không giấu lỗi bằng try/catch rỗng.
- Không bỏ qua trạng thái API lỗi.
- Không tuyên bố đã chạy test nếu chưa chạy.
- Không tuyên bố hoàn thành nếu còn hạng mục chưa triển khai.

ĐẦU RA BẮT BUỘC

Sau khi hoàn thành, hãy trả về:

1. Tóm tắt những gì đã triển khai.
2. Danh sách file đã tạo hoặc sửa.
3. Kiến trúc component chính.
4. Cách kết nối dữ liệu.
5. Các lệnh test đã chạy và kết quả.
6. Hạng mục chưa hoàn thành hoặc cần backend hỗ trợ.
7. Các quyết định khác với guideline, nếu có, và lý do cụ thể.

Trước khi kết thúc, hãy tự kiểm tra toàn bộ checklist trong:

docs/cynca-vlxd/dashboard/14-Implementation-Checklist.md
```

---

## 3. Prompt triển khai theo từng phase

### Phase 1 — Foundation

```text
Đọc toàn bộ docs/cynca-vlxd/dashboard/.
Chỉ triển khai Phase 1 gồm:
- Design tokens
- Tailwind config
- UI primitives
- Dashboard shell
- Sidebar
- Header

Không triển khai KPI, chart hoặc bảng ở phase này.
Chạy lint và typecheck sau khi hoàn thành.
Báo rõ file đã tạo hoặc sửa.
```

### Phase 2 — Analytics

```text
Tiếp tục triển khai Dashboard Cynca VLXD.
Chỉ làm:
- KPI cards
- Revenue chart
- Order status chart
- Date range interaction
- Loading, empty, error và stale state

Tuân thủ 05-KPI-Cards.md và 06-Charts.md.
Không sửa lại foundation nếu không cần thiết.
```

### Phase 3 — Operations

```text
Tiếp tục triển khai Dashboard Cynca VLXD.
Chỉ làm:
- Product widgets
- Inventory alerts
- Orders table
- Quick view drawer
- Permission state liên quan

Tuân thủ 07-Product-Widgets.md và 08-Orders-Table.md.
```

### Phase 4 — Notification & polish

```text
Hoàn thiện Dashboard Cynca VLXD với:
- Notification dropdown
- Notification widget
- Toast feedback
- Responsive
- Accessibility
- Motion
- Visual QA

Dùng 09-Notification.md đến 14-Implementation-Checklist.md làm tiêu chuẩn nghiệm thu.
```

---

## 4. Prompt review mã nguồn

```text
Hãy review implementation Dashboard Cynca VLXD hiện tại dựa trên toàn bộ tài liệu trong docs/cynca-vlxd/dashboard/.

Không sửa code ngay.

Trả về báo cáo theo nhóm:
1. Sai lệch visual.
2. Sai lệch layout và responsive.
3. Thiếu state loading, empty, error, stale hoặc permission.
4. Lỗi accessibility.
5. Lỗi dữ liệu và business logic.
6. Lỗi performance.
7. Lỗi maintainability.
8. Mức độ ưu tiên: Blocker, Critical, Major, Minor.
9. Danh sách file cần sửa.
10. Kế hoạch sửa theo thứ tự an toàn.

Chỉ kết luận dựa trên code thực tế và guideline. Không đoán rằng chức năng đã tồn tại nếu chưa thấy trong mã nguồn.
```

---

## 5. Prompt Visual QA

```text
So sánh giao diện Dashboard hiện tại với Design Specification trong docs/cynca-vlxd/dashboard/.

Kiểm tra ở các viewport:
- 1920 × 1080
- 1440 × 900
- 1366 × 768
- 1280 × 800
- 1024 × 768

Kiểm tra:
- Sidebar
- Header
- KPI
- Chart
- Product widget
- Orders table
- Notification
- Spacing
- Typography
- Radius
- Shadow
- Status color
- Loading, empty và error states
- Focus state

Trả về checklist pass/fail và mô tả sai lệch cụ thể theo pixel hoặc token khi có thể.
```

---

## 6. Prompt accessibility audit

```text
Audit accessibility cho Dashboard Cynca VLXD theo docs/cynca-vlxd/dashboard/12-Accessibility.md.

Kiểm tra:
- Semantic landmarks
- Heading hierarchy
- Keyboard navigation
- Focus order
- Focus trap
- Focus return
- Accessible names
- ARIA state
- Color contrast
- Status không chỉ dùng màu
- Charts
- Tables
- Forms
- Toast/live region
- Reduced motion
- Zoom 200%

Chạy công cụ tự động nếu repository hỗ trợ, nhưng không chỉ dựa vào công cụ tự động.
Trả về lỗi theo mức độ và file/component liên quan.
```

---

## 7. Tiêu chí AI không được tự tuyên bố hoàn thành

AI chưa được phép kết luận hoàn thành khi:

- Chưa đọc đủ guideline.
- Chưa chạy typecheck.
- Chưa chạy lint.
- Chưa kiểm tra ít nhất các viewport chính.
- Chưa có loading, empty và error states.
- Chưa kiểm tra keyboard navigation.
- Còn dữ liệu hardcode nằm trực tiếp trong component production.
- Còn TODO ảnh hưởng luồng chính.
- API chưa kết nối nhưng báo là đã kết nối.
- Không có bằng chứng về test đã chạy.

---

## 8. Báo cáo hoàn thành mẫu

```text
## Đã triển khai

- Dashboard shell
- Sidebar responsive
- Header và global search
- 4 KPI cards
- Revenue chart
- Order status chart
- Product widgets
- Orders table
- Notification dropdown

## File đã thay đổi

- src/app/dashboard/page.tsx
- src/components/dashboard/...
- src/components/ui/...
- src/styles/tokens.css
- tailwind.config.ts

## Dữ liệu

- KPI lấy từ ...
- Orders lấy từ ...
- Inventory lấy từ ...

## Kiểm thử đã chạy

- npm run lint — Passed
- npm run typecheck — Passed
- npm test — Passed

## Chưa hoàn thành

- API export PDF chưa có endpoint backend.
- Real-time notification đang dùng polling thay cho WebSocket.
```

---

## 9. Kết luận

Prompt này giúp AI coding agent triển khai Cynca VLXD Dashboard theo một quy trình có kiểm soát, giảm tình trạng tự suy đoán giao diện hoặc báo hoàn thành khi chưa đủ bằng chứng. File này phải luôn được sử dụng cùng toàn bộ Design Specification, không thay thế các tài liệu chi tiết phía trước.

File tiếp theo đề xuất:

```text
16-Data-Contracts.md
```