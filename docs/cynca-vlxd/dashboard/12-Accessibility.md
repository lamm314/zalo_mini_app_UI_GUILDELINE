# Cynca VLXD Admin Dashboard — Accessibility Specification

> Phiên bản: 1.0  
> Phạm vi: Tiêu chuẩn khả năng tiếp cận cho toàn bộ Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin, laptop và tablet  
> Mục tiêu tuân thủ: WCAG 2.2 mức AA  
> Tài liệu liên quan: toàn bộ thư mục `dashboard/`

---

## 1. Mục tiêu accessibility

Dashboard Cynca VLXD phải cho phép người dùng hoàn thành các tác vụ chính bằng:

- Chuột.
- Bàn phím.
- Màn hình cảm ứng.
- Screen reader.
- Chế độ zoom lớn.
- Chế độ tương phản cao.
- Chế độ giảm chuyển động.

Khả năng tiếp cận không phải phần bổ sung sau khi code xong. Mọi component phải được thiết kế và kiểm thử accessibility ngay từ đầu.

---

## 2. Nguyên tắc cốt lõi

### 2.1 Perceivable

Thông tin phải có thể nhận biết qua nhiều tín hiệu, không chỉ dựa vào màu sắc.

### 2.2 Operable

Mọi chức năng chính phải sử dụng được bằng bàn phím và thiết bị hỗ trợ.

### 2.3 Understandable

Nhãn, trạng thái, lỗi và hướng dẫn phải rõ ràng, nhất quán.

### 2.4 Robust

HTML, ARIA và component phải hoạt động ổn định với trình duyệt và công nghệ hỗ trợ phổ biến.

---

## 3. Semantic HTML

Ưu tiên HTML semantic trước khi dùng ARIA.

Cấu trúc trang tham chiếu:

```html
<body>
  <a href="#main-content" class="skip-link">Bỏ qua điều hướng</a>
  <aside>
    <nav aria-label="Điều hướng quản trị chính">...</nav>
  </aside>
  <div>
    <header>...</header>
    <main id="main-content" tabindex="-1">...</main>
  </div>
</body>
```

Quy tắc:

- Chỉ có một `<main>` trong trang.
- Dùng `<button>` cho thao tác.
- Dùng `<a>` cho điều hướng.
- Không dùng `<div onClick>` thay cho button hoặc link.
- Dùng heading theo thứ tự logic.

---

## 4. Skip link

Skip link phải là phần tử focus đầu tiên.

```css
.skip-link {
  position: fixed;
  top: 8px;
  left: 8px;
  z-index: 999;
  padding: 10px 14px;
  background: #101828;
  color: #FFFFFF;
  border-radius: 8px;
  transform: translateY(-150%);
}

.skip-link:focus {
  transform: translateY(0);
}
```

Nhãn:

```text
Bỏ qua điều hướng, đến nội dung chính
```

---

## 5. Heading hierarchy

Cấu trúc khuyến nghị:

```text
H1: Tổng quan
H2: Chỉ số kinh doanh
H2: Phân tích doanh thu
H2: Đơn hàng gần đây
H2: Cảnh báo tồn kho
H3: Tiêu đề widget con nếu cần
```

Không chọn heading chỉ vì kích thước font.

Mỗi trang có một `h1` rõ ràng.

---

## 6. Color contrast

Mục tiêu tối thiểu:

- Text thông thường: 4.5:1.
- Text lớn: 3:1.
- UI component và focus indicator: 3:1 so với nền liền kề.

Không dùng:

- Xám quá nhạt cho text quan trọng.
- Xanh dương nhạt trên nền trắng nếu không đủ tương phản.
- Placeholder thay cho label.

Màu đề xuất:

```css
--text-primary: #182230;
--text-secondary: #526071;
--text-muted-accessible: #667085;
--link: #0B57F0;
--danger-text: #B42318;
--success-text: #027A48;
```

`#98A2B3` chỉ dùng cho nội dung phụ không bắt buộc hoặc decorative metadata, không dùng cho thông tin nghiệp vụ chính.

---

## 7. Không chỉ dùng màu

Trạng thái phải kết hợp:

- Nhãn text.
- Icon khi cần.
- Màu.

Ví dụ đúng:

```text
✓ Hoàn thành
! Chờ xác nhận
× Đã hủy
```

Ví dụ sai:

```text
Chỉ có dot xanh, vàng hoặc đỏ không có nhãn.
```

Biểu đồ nhiều series cần:

- Màu khác nhau.
- Pattern, dash hoặc marker khác nhau khi cần.
- Legend rõ ràng.
- Summary text thay thế.

---

## 8. Focus indicator

Mọi phần tử tương tác phải có focus visible.

```css
:focus-visible {
  outline: 3px solid rgba(11,87,240,0.28);
  outline-offset: 2px;
}
```

Focus indicator phải:

- Không bị `overflow: hidden` cắt mất.
- Có tương phản rõ.
- Không bị thay thế chỉ bằng đổi màu nền nhẹ.

Không dùng:

```css
outline: none;
```

trừ khi đã có focus style thay thế tương đương hoặc tốt hơn.

---

## 9. Keyboard navigation

Mọi chức năng chính phải dùng được bằng bàn phím.

### Tab

Di chuyển theo thứ tự thị giác và logic.

### Enter / Space

Kích hoạt button, menu trigger, checkbox hoặc switch.

### Escape

Đóng:

- Modal.
- Drawer.
- Dropdown.
- Popover.
- Command palette.

### Arrow keys

Dùng cho:

- Menu.
- Tab list.
- Radio group.
- Calendar.
- Combobox.

Không tạo custom keyboard interaction nếu native element đã đáp ứng.

---

## 10. Focus order

Thứ tự focus tổng thể đề xuất:

1. Skip link.
2. Sidebar navigation.
3. Header actions.
4. Page toolbar.
5. KPI cards tương tác.
6. Charts controls.
7. Tables và row actions.
8. Widgets phía dưới.

DOM order phải phản ánh visual order.

Không dùng `tabindex` dương.

---

## 11. Focus management

### Modal / Drawer

- Focus chuyển vào heading hoặc action đầu tiên.
- Focus bị giữ trong vùng modal.
- Escape đóng nếu nghiệp vụ cho phép.
- Khi đóng, focus trả về trigger.

### Dropdown

- Focus vào item đầu hoặc giữ tại trigger tùy pattern.
- Khi đóng, focus trở về trigger.

### Route change

Sau điều hướng trang:

- Focus chuyển đến `main` hoặc `h1`.
- Screen reader được thông báo tiêu đề trang mới.

### Error form

Khi submit lỗi:

- Focus đến error summary hoặc field lỗi đầu tiên.

---

## 12. Sidebar accessibility

Sidebar dùng:

```html
<aside>
  <nav aria-label="Điều hướng quản trị chính">
```

Active page:

```html
aria-current="page"
```

Submenu trigger:

```html
aria-expanded="true"
aria-controls="products-submenu"
```

Collapsed sidebar:

- Mỗi icon có accessible name.
- Tooltip hiển thị khi hover và focus.
- Không ẩn label khỏi accessibility tree nếu không có tên thay thế.

---

## 13. Header accessibility

Icon-only button bắt buộc có `aria-label`.

Ví dụ:

```html
<button aria-label="Mở thông báo, 4 thông báo chưa đọc">
```

Search:

```html
<label for="global-search" class="sr-only">Tìm kiếm toàn hệ thống</label>
<input id="global-search" type="search" />
```

Date picker và branch selector phải có label rõ ràng, không chỉ placeholder.

---

## 14. KPI card accessibility

Card chỉ dùng link hoặc button nếu thực sự tương tác.

Accessible name mẫu:

```text
Doanh thu thuần 1,28 tỷ đồng, tăng 12,5 phần trăm so với tháng trước. Xem báo cáo doanh thu.
```

Icon decorative:

```html
aria-hidden="true"
```

Trend không chỉ dựa vào mũi tên và màu.

Sparkline cần mô tả text hoặc ẩn khỏi screen reader nếu thông tin đã được diễn đạt bằng text.

---

## 15. Chart accessibility

Mỗi chart phải có:

- Title.
- Description.
- Summary text.
- Data table thay thế khi số liệu quan trọng.

SVG mẫu:

```html
<svg role="img" aria-labelledby="revenue-title revenue-desc">
```

Summary mẫu:

```text
Doanh thu tăng từ 820 triệu đồng trong tháng 1 lên 1,28 tỷ đồng trong tháng 7. Tháng 5 là tháng duy nhất giảm so với tháng trước.
```

Nếu chart interactive:

- Điểm dữ liệu có thể focus hoặc có bảng dữ liệu tương đương.
- Tooltip không chỉ xuất hiện khi hover.

---

## 16. Table accessibility

Dùng cấu trúc semantic:

```html
<table>
  <caption>Đơn hàng gần đây</caption>
  <thead>
    <tr>
      <th scope="col">Mã đơn</th>
    </tr>
  </thead>
</table>
```

Sortable header:

```html
aria-sort="ascending"
```

Row action phải có nhãn cụ thể:

```text
Xem chi tiết đơn #CYN-240806-0187
```

Không dùng nhãn chung `Xem` cho nhiều row.

Table scroll ngang phải có container focus được hoặc có hướng dẫn khi cần.

---

## 17. Forms

Mọi input có label hiển thị hoặc label ẩn có ý nghĩa.

```html
<label for="branch-select">Chi nhánh</label>
```

Helper text liên kết bằng:

```html
aria-describedby="branch-help"
```

Field lỗi:

```html
aria-invalid="true"
aria-describedby="email-error"
```

Không dùng màu đỏ và border làm tín hiệu lỗi duy nhất.

---

## 18. Error summary

Form dài cần error summary ở đầu:

```text
Không thể lưu sản phẩm
Có 3 trường cần kiểm tra:
- Giá bán
- Đơn vị tính
- Ảnh sản phẩm
```

Error summary:

- Có heading.
- Có link đến từng field lỗi.
- Được focus sau submit thất bại.

---

## 19. Buttons và links

Nhãn phải mô tả hành động:

Tốt:

```text
Tạo đơn hàng
Xuất báo cáo Excel
Xem tồn kho sản phẩm
```

Không tốt:

```text
Bấm vào đây
Xem thêm
OK
```

Nếu có nhiều `Xem thêm`, accessible name phải khác nhau bằng `aria-label` hoặc text bổ sung.

---

## 20. Icon-only actions

Icon-only button chỉ dùng khi icon quen thuộc và có tooltip.

Bắt buộc có:

```html
aria-label="Mở menu thao tác của đơn #CYN-240806-0187"
```

Kích thước target:

- Desktop: tối thiểu 36 × 36px.
- Touch: tối thiểu 44 × 44px.

---

## 21. Tooltip

Tooltip:

- Hiển thị khi hover và focus.
- Không chứa nội dung bắt buộc duy nhất.
- Không chứa action phức tạp.
- Có thể đóng bằng Escape nếu giữ lâu.

Trigger phải có accessible name độc lập, không dựa vào tooltip để screen reader hiểu chức năng.

---

## 22. Toast và live region

Thông báo thông thường:

```html
<div aria-live="polite" aria-atomic="true">
```

Lỗi nghiêm trọng:

```html
<div role="alert">
```

Không dùng `role="alert"` cho mọi toast.

Toast có action phải:

- Tồn tại đủ lâu.
- Có thể focus.
- Pause timeout khi hover hoặc focus.

---

## 23. Loading state

Khi vùng dữ liệu tải:

```html
aria-busy="true"
```

Loading text có thể dùng visually hidden:

```text
Đang tải dữ liệu doanh thu
```

Không để screen reader đọc từng skeleton element.

Skeleton nên dùng:

```html
aria-hidden="true"
```

---

## 24. Empty và error state

Empty state phải nêu rõ:

- Không có dữ liệu thật.
- Hay filter không có kết quả.

Error state phải gồm:

- Điều gì xảy ra.
- Hành động tiếp theo.
- Nút thử lại nếu phù hợp.

Không dùng chỉ icon và câu `Có lỗi`.

---

## 25. Status announcements

Khi filter thay đổi và dữ liệu cập nhật, có thể dùng live region:

```text
Đã tải 28 đơn hàng trong 30 ngày qua.
```

Khi sort bảng:

```text
Đã sắp xếp theo tổng tiền giảm dần.
```

Không thông báo quá nhiều thay đổi nhỏ gây nhiễu.

---

## 26. Zoom và reflow

Dashboard phải duy trì chức năng chính ở:

- 200% zoom.
- Viewport tương đương 320 CSS px nếu có thể.

Yêu cầu:

- Không chồng text.
- Không mất action.
- Không có horizontal scroll toàn trang, trừ vùng table hợp lệ.
- Modal và drawer vẫn dùng được.

---

## 27. Text resize

Khi text tăng đến 200%:

- Button cho phép tăng chiều cao.
- Label không bị cắt hoàn toàn.
- Card có thể tăng chiều cao.
- Không dùng fixed height cho vùng text động.

Chỉ dùng `min-height`, tránh `height` cố định cho component có nội dung chữ.

---

## 28. Motion accessibility

Bắt buộc hỗ trợ:

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Không có:

- Nhấp nháy trên 3 lần/giây.
- Parallax bắt buộc.
- Animation là nguồn thông tin duy nhất.

---

## 29. High contrast mode

Component phải kiểm tra trong forced colors mode.

```css
@media (forced-colors: active) {
  .status-badge {
    border: 1px solid CanvasText;
  }
}
```

Không dựa vào background color duy nhất để hiển thị trạng thái.

Focus indicator phải còn nhìn thấy trong high contrast mode.

---

## 30. Images và thumbnails

Ảnh sản phẩm có nội dung thông tin phải có alt phù hợp:

```text
Bao xi măng Hoàng Thạch PCB40
```

Ảnh decorative dùng:

```html
alt=""
```

Không lặp alt với text ngay bên cạnh nếu không cần thiết.

Fallback ảnh phải vẫn giữ tên sản phẩm trong text.

---

## 31. Language và định dạng

Trang dùng:

```html
<html lang="vi">
```

Đoạn tiếng Anh dài có thể dùng `lang="en"`.

Định dạng số và ngày phải dễ đọc:

```text
1.280.000.000 đồng
06 tháng 08 năm 2026
```

Accessible label có thể viết đầy đủ thay vì chỉ ký hiệu `₫` nếu screen reader đọc không rõ.

---

## 32. Time limits

Không đặt timeout ngắn cho tác vụ quản trị.

Session timeout phải:

- Cảnh báo trước.
- Cho phép gia hạn.
- Không làm mất dữ liệu form chưa lưu nếu có thể.

Toast không phải nội dung duy nhất cho cảnh báo hết phiên.

---

## 33. Authentication accessibility

Trang đăng nhập phải có:

- Label rõ ràng.
- Toggle hiển thị mật khẩu có accessible name.
- Error summary.
- Không chặn paste mật khẩu.
- Hỗ trợ password manager.
- CAPTCHA có phương án thay thế nếu dùng.

---

## 34. Permissions và disabled state

Nếu người dùng không có quyền:

Ưu tiên ẩn action không liên quan.

Nếu cần hiển thị disabled để giải thích:

- Có text hoặc tooltip nói rõ lý do.
- Không chỉ giảm opacity.

Ví dụ:

```text
Bạn không có quyền xuất báo cáo tài chính.
```

---

## 35. Screen reader announcements

Các sự kiện nên thông báo:

- Route change.
- Filter hoàn tất.
- Tạo hoặc cập nhật thành công.
- Lỗi submit.
- Xóa item thành công.
- Mở modal hoặc drawer.

Các sự kiện không nên thông báo liên tục:

- Hover.
- Mỗi điểm chart khi rê chuột.
- Mỗi lần badge real-time tăng nhẹ.

---

## 36. ARIA usage rules

- Không thêm ARIA nếu native HTML đã đủ.
- Không dùng role sai để làm component trông giống widget.
- `aria-hidden="true"` không được đặt lên parent đang chứa focus.
- ID trong `aria-describedby` và `aria-labelledby` phải tồn tại.
- `aria-expanded` phải đồng bộ với trạng thái thật.
- `aria-disabled` không tự chặn click; code phải xử lý hành vi.

Nguyên tắc:

```text
No ARIA is better than bad ARIA.
```

---

## 37. Component accessibility contract

Mỗi component mới phải xác định:

- Semantic element.
- Accessible name.
- Keyboard interaction.
- Focus behavior.
- Screen reader announcement.
- Contrast.
- Loading state.
- Error state.
- Reduced motion behavior.

Không merge component tương tác nếu chưa có accessibility contract.

---

## 38. Automated testing

Công cụ đề xuất:

- axe-core.
- eslint-plugin-jsx-a11y.
- Lighthouse Accessibility.
- Storybook accessibility addon.
- Playwright + axe.

Automated test không thay thế manual test.

---

## 39. Manual testing

Bắt buộc kiểm tra:

- Chỉ dùng bàn phím.
- Screen reader: VoiceOver trên macOS hoặc NVDA trên Windows.
- Zoom 200%.
- Reduced motion.
- High contrast hoặc forced colors.
- Nội dung dài.
- Error và validation.
- Modal, drawer, dropdown và toast.

---

## 40. Test case bàn phím tối thiểu

1. Tab từ đầu trang đến cuối mà không mắc kẹt.
2. Mở và đóng sidebar drawer.
3. Mở search và chọn kết quả.
4. Thay đổi date range.
5. Mở notification dropdown.
6. Mở quick view order drawer.
7. Sort bảng đơn hàng.
8. Mở modal và đóng bằng Escape.
9. Submit form lỗi và đến field lỗi.
10. Quay lại trigger sau khi đóng overlay.

---

## 41. Test case screen reader tối thiểu

- Trang có title và heading rõ ràng.
- Sidebar active item được đọc đúng.
- KPI được đọc đủ value và trend.
- Chart có summary.
- Table có caption và header association.
- Status badge có text.
- Notification unread state được đọc.
- Form error được liên kết với field.
- Toast được thông báo đúng mức độ.

---

## 42. Acceptance checklist

- [ ] Mục tiêu WCAG 2.2 AA được xác định.
- [ ] Có skip link.
- [ ] Chỉ có một main landmark.
- [ ] Heading hierarchy logic.
- [ ] Text contrast đạt yêu cầu.
- [ ] Trạng thái không chỉ dùng màu.
- [ ] Mọi action sử dụng được bằng bàn phím.
- [ ] Focus indicator rõ ràng.
- [ ] Modal và drawer có focus trap và focus return.
- [ ] Icon-only button có accessible name.
- [ ] Chart có summary text hoặc bảng dữ liệu thay thế.
- [ ] Table dùng semantic đúng.
- [ ] Form label và error association đầy đủ.
- [ ] Loading dùng `aria-busy` hợp lý.
- [ ] Toast dùng live region đúng mức.
- [ ] Zoom 200% vẫn dùng được.
- [ ] Reduced motion và forced colors hoạt động.
- [ ] Automated test không có lỗi nghiêm trọng.
- [ ] Đã manual test bằng keyboard và screen reader.

---

## 43. Những điều không được làm

- Không dùng div click thay button.
- Không xóa outline mà không có thay thế.
- Không dùng placeholder làm label duy nhất.
- Không chỉ dùng màu để biểu thị trạng thái.
- Không tạo keyboard trap ngoài modal hợp lệ.
- Không dùng `tabindex` dương.
- Không thêm ARIA sai vai trò.
- Không ẩn nội dung đang focus bằng `aria-hidden`.
- Không để tooltip là nguồn thông tin bắt buộc duy nhất.
- Không coi Lighthouse 100 điểm là đã hoàn tất accessibility.

---

## 44. Kết luận

Accessibility của Cynca VLXD Dashboard phải được tích hợp vào semantic HTML, keyboard interaction, focus management, màu sắc, biểu đồ, bảng, form và trạng thái hệ thống. Một component chỉ được xem là hoàn chỉnh khi người dùng có thể hiểu và vận hành nó bằng nhiều phương thức tương tác khác nhau.

File tiếp theo:

```text
13-Tailwind-Tokens.md
```