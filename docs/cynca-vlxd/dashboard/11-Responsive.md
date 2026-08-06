# Cynca VLXD Admin Dashboard — Responsive Specification

> Phiên bản: 1.0  
> Phạm vi: Quy tắc responsive cho toàn bộ Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin, laptop và tablet landscape  
> Tài liệu liên quan: `01-Overview.md`, `02-Layout.md`, `03-Sidebar.md`, `04-Header.md`, `05-KPI-Cards.md`, `06-Charts.md`, `07-Product-Widgets.md`, `08-Orders-Table.md`, `09-Notification.md`, `10-Animation.md`

---

## 1. Mục tiêu responsive

Responsive của Cynca VLXD Dashboard phải bảo đảm:

- Không mất chức năng quan trọng khi thu hẹp màn hình.
- Không làm vỡ grid, bảng, biểu đồ hoặc nội dung số liệu.
- Giữ thứ tự ưu tiên thông tin nhất quán.
- Tối ưu tốt cho desktop và laptop là chính.
- Hỗ trợ tablet landscape ở mức vận hành cơ bản.
- Không cố ép toàn bộ Dashboard desktop thành trải nghiệm mobile đầy đủ.

Dashboard phải thích ứng theo nội dung và mức độ ưu tiên, không chỉ giảm kích thước mọi thành phần.

---

## 2. Phạm vi thiết bị

### Ưu tiên cao

- Desktop 1440px trở lên.
- Laptop 1280px–1439px.
- Laptop nhỏ hoặc tablet landscape 1024px–1279px.

### Ưu tiên thấp

- Tablet portrait dưới 1024px.
- Mobile dưới 768px.

Trên mobile, chỉ cần bảo đảm truy cập được các chức năng cơ bản. Mini App phía khách hàng vẫn là trải nghiệm mobile chính.

---

## 3. Breakpoint tokens

```css
:root {
  --bp-mobile: 480px;
  --bp-tablet: 768px;
  --bp-dashboard-min: 1024px;
  --bp-laptop: 1280px;
  --bp-desktop: 1440px;
  --bp-wide: 1920px;
}
```

Media query theo hướng desktop-first hoặc mobile-first đều được, nhưng phải thống nhất trong codebase.

Khuyến nghị desktop-first cho Dashboard quản trị.

---

## 4. Ma trận bố cục tổng thể

| Viewport | Sidebar | Content padding | KPI | Charts | Tables |
|---|---|---:|---|---|---|
| ≥1920px | 264px | 32px | 4 cột | 8/4 hoặc 7/5 | Full |
| 1440–1919px | 264px | 32px | 4 cột | 8/4 | Full |
| 1280–1439px | 240–264px | 24px | 4 hoặc 2 cột | 8/4 hoặc 12/12 | Rút gọn |
| 1024–1279px | 80px | 24px | 2 cột | 12/12 | Scroll ngang |
| 768–1023px | Drawer | 20px | 1–2 cột | 12/12 | Scroll hoặc card list |
| <768px | Drawer | 16px | 1 cột | 1 cột | Card list |

---

## 5. Desktop rộng từ 1920px

### 5.1 Layout shell

- Sidebar mở rộng 264px.
- Main content giới hạn tối đa 1600px.
- Nội dung căn giữa trong vùng còn lại.
- Không kéo card quá rộng làm giảm khả năng đọc.

```css
.dashboard-content {
  max-width: 1600px;
  margin-inline: auto;
  padding-inline: 32px;
}
```

### 5.2 Grid

- KPI: 4 cột.
- Revenue chart: 8 cột.
- Order status: 4 cột.
- Product widgets: 6/6.
- Orders table: 8 hoặc 12 cột tùy bố cục.

### 5.3 Typography

Không tăng font quá lớn chỉ vì màn hình rộng. Giữ:

- Page title: 30–32px.
- KPI value: 34–36px.
- Card title: 18px.

---

## 6. Desktop chuẩn 1440–1919px

Đây là breakpoint thiết kế tham chiếu chính.

### 6.1 Sidebar

```css
width: 264px;
```

### 6.2 Header

- Hiển thị đầy đủ search.
- Hiển thị date range.
- Hiển thị branch filter.
- Hiển thị export button có nhãn.
- Hiển thị user name và avatar.

### 6.3 KPI

```css
grid-template-columns: repeat(4, minmax(0, 1fr));
```

### 6.4 Analytics

```text
Revenue chart: 8 columns
Order status: 4 columns
```

### 6.5 Orders table

Hiển thị đầy đủ các cột mặc định.

---

## 7. Laptop 1280–1439px

### 7.1 Mục tiêu

Giữ trải nghiệm gần desktop nhưng giảm khoảng trống và nội dung phụ.

### 7.2 Sidebar

Có thể giữ 264px hoặc giảm xuống 240px.

Khuyến nghị:

```css
--sidebar-width-expanded: 240px;
```

### 7.3 Header

Ưu tiên thu gọn theo thứ tự:

1. Ẩn role người dùng.
2. Ẩn user name, giữ avatar.
3. Export chuyển icon-only.
4. Search giảm còn 280–320px.
5. Branch filter chuyển xuống page toolbar nếu cần.

### 7.4 KPI

Nếu card vẫn đạt tối thiểu 240px, giữ 4 cột.

Nếu không đủ:

```css
grid-template-columns: repeat(2, minmax(0, 1fr));
```

Không ép 4 card quá hẹp.

### 7.5 Charts

Nếu revenue chart còn ít nhất 620px và order status còn ít nhất 300px, giữ 8/4.

Nếu không:

```text
Revenue chart: 12 columns
Order status: 12 columns
```

### 7.6 Product widgets

Có thể giữ 6/6 hoặc chuyển 12/12.

### 7.7 Orders table

Có thể ẩn:

- Dòng phụ thời gian.
- Phương thức thanh toán.
- Số điện thoại khách hàng nếu thiếu chỗ.

Giữ bắt buộc:

- Mã đơn.
- Khách hàng.
- Trạng thái.
- Tổng tiền.
- Thời gian.
- Hành động.

---

## 8. Laptop nhỏ và tablet landscape 1024–1279px

### 8.1 Sidebar

Sidebar thu gọn mặc định:

```css
width: 80px;
```

Chỉ hiển thị icon và tooltip.

Có thể mở expanded overlay tạm thời.

### 8.2 Header

Hiển thị:

- Sidebar toggle.
- Search rút gọn hoặc icon search.
- Notification.
- Avatar.

Chuyển xuống page toolbar hoặc filter panel:

- Date range.
- Branch filter.
- Export.

### 8.3 Main content

```css
padding: 24px;
```

### 8.4 KPI

```css
grid-template-columns: repeat(2, minmax(0, 1fr));
```

### 8.5 Charts

Mỗi chart chiếm 12 cột.

```text
Revenue chart
Order status
```

xếp theo chiều dọc.

### 8.6 Widgets

- Product widget: 12 cột.
- Inventory alert: 12 cột.
- Orders table: 12 cột.
- Notification widget: 12 cột hoặc 6 cột nếu đủ rộng.

### 8.7 Tables

Cho phép cuộn ngang.

```css
.table-wrapper {
  overflow-x: auto;
}
```

Không giảm font bảng dưới 12px.

---

## 9. Tablet portrait 768–1023px

### 9.1 Sidebar

Chuyển thành drawer.

```css
width: min(288px, calc(100vw - 40px));
```

Có backdrop và focus trap.

### 9.2 Header

Giữ:

- Menu button.
- Page title rút gọn.
- Search icon.
- Notification.
- Avatar.

Ẩn khỏi header:

- Breadcrumb dài.
- Date range.
- Branch filter.
- Export label.

### 9.3 Page toolbar

Cho phép wrap thành nhiều dòng.

```css
.page-toolbar {
  flex-direction: column;
  align-items: stretch;
}
```

### 9.4 KPI

Có thể dùng 2 cột nếu viewport đủ rộng.

```css
grid-template-columns: repeat(2, minmax(0, 1fr));
```

Nếu card nhỏ hơn 220px, chuyển 1 cột.

### 9.5 Charts

- Xếp 1 cột.
- Min-height 280px.
- Legend chuyển xuống dưới.
- Giảm số label trục.

### 9.6 Tables

Ưu tiên một trong hai cách:

1. Scroll ngang.
2. Chuyển sang list card nếu bảng quá phức tạp.

Không tạo bảng font cực nhỏ.

---

## 10. Mobile dưới 768px

Mobile không phải nền tảng chính nhưng giao diện không được hỏng.

### 10.1 Shell

- Sidebar drawer.
- Header tối giản.
- Content padding 16px.

### 10.2 KPI

```css
grid-template-columns: 1fr;
```

### 10.3 Charts

- Một chart mỗi hàng.
- Min-height 260–280px.
- Giảm label.
- Tooltip phải không vượt viewport.

### 10.4 Orders

Chuyển table row thành card:

```text
#CYN-240806-0187
Nguyễn Văn Minh
12.450.000 ₫
[Đang giao]
06/08/2026 · 16:18
```

### 10.5 Widgets

- Một cột.
- Ẩn metadata phụ.
- Action chuyển vào menu.

### 10.6 Modal

Modal lớn chuyển thành full-screen sheet hoặc drawer.

---

## 11. Container width rules

```css
.dashboard-content {
  width: 100%;
  max-width: 1600px;
  margin-inline: auto;
}
```

Không dùng fixed width cho main content.

Các card dùng:

```css
min-width: 0;
```

để tránh overflow trong CSS Grid.

---

## 12. Grid adaptation

### Desktop

```css
.dashboard-grid {
  display: grid;
  grid-template-columns: repeat(12, minmax(0, 1fr));
  gap: 24px;
}
```

### Tablet

Vẫn giữ 12 cột hoặc chuyển component-specific grid. Không bắt buộc đổi số cột toàn hệ thống.

### Mobile

Có thể dùng:

```css
grid-template-columns: 1fr;
```

Không dùng absolute positioning để giữ layout desktop trên màn hình nhỏ.

---

## 13. Spacing adaptation

| Viewport | Page padding | Grid gap | Card padding |
|---|---:|---:|---:|
| ≥1440px | 32px | 24px | 24px |
| 1280–1439px | 24px | 20–24px | 20–24px |
| 1024–1279px | 24px | 20px | 20px |
| 768–1023px | 20px | 16–20px | 18–20px |
| <768px | 16px | 16px | 16–18px |

Không giảm card padding dưới 16px.

---

## 14. Typography adaptation

### Page title

```css
font-size: 30px;
```

Tablet:

```css
font-size: 26px;
```

Mobile:

```css
font-size: 22px;
```

### KPI value

Desktop:

```css
font-size: 34px;
```

Laptop:

```css
font-size: 30px;
```

Mobile:

```css
font-size: 28px;
```

Body text không giảm dưới 13px trong Dashboard.

---

## 15. Header responsive rules

Thứ tự ưu tiên nội dung trong header:

1. Sidebar trigger.
2. Search hoặc search trigger.
3. Notification.
4. User menu.
5. Date range.
6. Branch filter.
7. Export.
8. Breadcrumb đầy đủ.

Khi thiếu chiều rộng, ẩn hoặc chuyển vị trí từ dưới lên theo danh sách ưu tiên thấp.

Không cho header cuộn ngang.

---

## 16. Sidebar responsive rules

### Expanded

```text
≥1280px
```

### Collapsed

```text
1024–1279px
```

### Drawer

```text
<1024px
```

State người dùng đã chọn có thể được lưu, nhưng viewport phải có quyền ghi đè để tránh layout vỡ.

Ví dụ: người dùng chọn expanded ở desktop nhưng khi xuống 1100px vẫn phải collapsed.

---

## 17. KPI responsive rules

KPI card không được nhỏ hơn:

```css
min-width: 220–240px;
```

Nếu không đủ:

- Chuyển số cột.
- Không giảm font quá mức.
- Không ẩn trend label quan trọng.

Sparkline có thể ẩn ở màn hình nhỏ nếu cần.

---

## 18. Chart responsive rules

- Không để chart thấp hơn 260px.
- Giảm số tick trục X.
- Legend chuyển xuống dưới.
- Tooltip dùng max-width phù hợp.
- Donut chart có thể giảm đường kính nhưng vẫn giữ label trung tâm đọc được.
- Không chuyển line chart thành loại chart khác chỉ vì màn hình nhỏ.

---

## 19. Table responsive rules

### Ưu tiên 1: Ẩn nội dung phụ

Ẩn:

- Metadata dòng hai.
- Cột phụ ít quan trọng.
- Label hành động dài.

### Ưu tiên 2: Scroll ngang

Giữ table semantic.

### Ưu tiên 3: Chuyển thành card list

Chỉ áp dụng dưới 768px hoặc khi bảng không còn đọc được.

Không dùng scale transform để thu nhỏ cả bảng.

---

## 20. Product widget responsive rules

Desktop row:

```text
Rank | Image | Product | Metrics | Action
```

Tablet:

```text
Image | Product | Main metric | Action
```

Mobile:

```text
Image | Product
        Main metric + status
```

Ẩn lần lượt:

1. Progress bar.
2. Metadata phụ.
3. Secondary metric.
4. Rank nếu không cần thiết.

---

## 21. Notification responsive rules

Desktop:

```css
width: 380px;
```

Tablet/mobile:

- Chuyển thành drawer hoặc sheet.
- Width tối đa viewport trừ 16–24px.
- Toast full-width có margin 16px.

Notification item vẫn giữ min-height tối thiểu 68px.

---

## 22. Modal và drawer responsive rules

### Modal desktop

- 480px, 640px hoặc 880px.

### Tablet

```css
max-width: calc(100vw - 40px);
```

### Mobile

- Full-screen dialog.
- Bottom sheet.
- Right drawer full-width.

Action footer có thể sticky ở dưới.

---

## 23. Touch target

Trên tablet và mobile:

```css
min-width: 44px;
min-height: 44px;
```

Áp dụng cho:

- Icon button.
- Menu trigger.
- Checkbox.
- Pagination.
- Row action.

Desktop có thể dùng 36–40px, nhưng không nhỏ hơn 32px.

---

## 24. Hover và touch

Không phụ thuộc vào hover để lộ hành động quan trọng.

Trên thiết bị touch:

- Hành động phải hiển thị hoặc truy cập qua menu rõ ràng.
- Tooltip không phải nguồn thông tin duy nhất.
- Card không yêu cầu hover để click được.

CSS:

```css
@media (hover: none) {
  .hover-only-action {
    opacity: 1;
  }
}
```

---

## 25. Safe area

Trên thiết bị có notch hoặc vùng an toàn:

```css
padding-bottom: env(safe-area-inset-bottom);
padding-left: env(safe-area-inset-left);
padding-right: env(safe-area-inset-right);
```

Chủ yếu áp dụng cho mobile drawer, sheet và sticky footer.

---

## 26. Orientation change

Khi tablet đổi hướng:

- Sidebar drawer có thể chuyển thành collapsed sidebar.
- Grid tái bố cục theo breakpoint.
- Modal không vượt viewport.
- Không giữ width tính từ orientation cũ.

Không cần reload trang.

---

## 27. Zoom và browser scaling

Dashboard phải hoạt động ở:

- Zoom 100%.
- Zoom 125%.
- Zoom 150%.
- Zoom 200% ở mức truy cập cơ bản.

Không dùng pixel positioning gây chồng nội dung khi zoom.

---

## 28. Content overflow

### Text

- Tên sản phẩm: truncate 1–2 dòng.
- Tên khách hàng: truncate 1 dòng.
- Mã đơn: no wrap.
- Badge: no wrap.

### Numbers

Dùng format rút gọn nếu cần:

```text
1,28 tỷ ₫
```

Tooltip hiển thị giá trị đầy đủ.

### Long labels

Không dùng label dài trong button ở viewport nhỏ. Có thể chuyển sang icon với tooltip.

---

## 29. Container queries

Có thể dùng container queries cho widget độc lập:

```css
.widget {
  container-type: inline-size;
}

@container (max-width: 460px) {
  .widget-header {
    flex-direction: column;
    align-items: stretch;
  }
}
```

Container query hỗ trợ component tái sử dụng tốt hơn nhưng không thay thế breakpoint trang.

---

## 30. CSS tham chiếu

```css
:root {
  --sidebar-expanded: 264px;
  --sidebar-collapsed: 80px;
  --page-padding: 32px;
  --grid-gap: 24px;
}

@media (max-width: 1439px) {
  :root {
    --sidebar-expanded: 240px;
    --page-padding: 24px;
    --grid-gap: 20px;
  }
}

@media (max-width: 1279px) {
  .dashboard-shell {
    grid-template-columns: var(--sidebar-collapsed) minmax(0, 1fr);
  }

  .kpi-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 1023px) {
  .dashboard-shell {
    display: block;
  }

  .desktop-sidebar {
    display: none;
  }

  .dashboard-content {
    padding: 20px;
  }
}

@media (max-width: 767px) {
  .dashboard-content {
    padding: 16px;
  }

  .kpi-grid {
    grid-template-columns: 1fr;
  }
}
```

---

## 31. Test viewport bắt buộc

Frontend phải kiểm tra tối thiểu các viewport:

```text
1920 × 1080
1440 × 900
1366 × 768
1280 × 800
1024 × 768
820 × 1180
768 × 1024
390 × 844
375 × 667
```

Ngoài viewport cố định, phải kéo resize liên tục để phát hiện breakpoint gãy.

---

## 32. Test content bắt buộc

Phải kiểm tra với:

- Tên sản phẩm dài.
- Tên khách hàng dài.
- Số tiền rất lớn.
- Badge `99+`.
- 6 KPI.
- Bảng nhiều cột.
- Chart legend dài.
- Không có dữ liệu.
- Loading.
- Error.
- Sidebar expanded và collapsed.

Responsive không được chỉ test với dữ liệu mẫu ngắn.

---

## 33. Accessibility responsive

- Reflow không làm mất nội dung ở 320px.
- Focus order vẫn logic sau khi component đổi vị trí.
- Drawer có focus trap.
- Không có nội dung chỉ hiển thị khi hover.
- Touch target đạt tối thiểu 44px trên touch device.
- Zoom 200% vẫn truy cập được chức năng chính.
- Horizontal scroll chỉ xuất hiện trong table wrapper, không trên toàn page.

---

## 34. Performance responsive

Trên màn hình nhỏ:

- Không render chart quá chi tiết không cần thiết.
- Có thể giảm số điểm hoặc label hiển thị, nhưng không thay đổi dữ liệu nguồn.
- Lazy-load widget dưới fold.
- Không tải ảnh sản phẩm kích thước desktop cho thumbnail nhỏ.
- Dùng responsive image hoặc CDN transformation.

---

## 35. Acceptance checklist

- [ ] Dashboard không có horizontal scroll toàn trang ở 1024px trở lên.
- [ ] Sidebar chuyển đúng expanded, collapsed và drawer.
- [ ] Header không tràn ngang.
- [ ] KPI không nhỏ hơn chiều rộng tối thiểu.
- [ ] Charts không thấp dưới ngưỡng đọc được.
- [ ] Tables có overflow riêng.
- [ ] Product widgets ẩn nội dung theo đúng thứ tự ưu tiên.
- [ ] Notification dropdown chuyển drawer khi cần.
- [ ] Modal không vượt viewport.
- [ ] Touch target đủ lớn trên tablet/mobile.
- [ ] Không có chức năng quan trọng chỉ xuất hiện khi hover.
- [ ] Layout hoạt động khi zoom 125%, 150% và 200%.
- [ ] Test đủ viewport bắt buộc.
- [ ] Test với nội dung dài và số lớn.
- [ ] Reduced motion và responsive hoạt động đồng thời.
- [ ] Không có breakpoint tùy tiện ngoài hệ thống nếu không có lý do.

---

## 36. Những điều không được làm

- Không ép 4 KPI vào màn hình quá hẹp.
- Không thu nhỏ font dưới mức đọc được.
- Không scale toàn bộ Dashboard bằng CSS transform.
- Không giữ sidebar 264px dưới 1024px.
- Không cho toàn trang cuộn ngang vì một bảng.
- Không ẩn hành động quan trọng mà không có cách truy cập thay thế.
- Không dùng mobile layout giống hệt desktop thu nhỏ.
- Không thay đổi loại biểu đồ tùy breakpoint nếu không cần thiết.
- Không bỏ qua touch interaction.
- Không chỉ test một kích thước màn hình.

---

## 37. Kết luận

Responsive của Cynca VLXD Dashboard phải ưu tiên sự ổn định, khả năng đọc và tính liên tục của tác vụ. Thiết kế cần thích ứng bằng cách thay đổi bố cục, mức độ chi tiết và phương thức tương tác thay vì chỉ thu nhỏ toàn bộ giao diện.

File tiếp theo:

```text
12-Accessibility.md
```