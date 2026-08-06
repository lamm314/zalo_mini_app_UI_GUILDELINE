# Cynca VLXD Admin Dashboard — Layout Specification

> Phiên bản: 1.0  
> Phạm vi: Bố cục tổng thể Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`

---

## 1. Mục tiêu của layout

Layout phải giúp người dùng:

- Nhìn thấy KPI quan trọng ngay khi mở trang.
- Di chuyển nhanh giữa các module quản trị.
- Đọc bảng và biểu đồ mà không bị chật.
- Thao tác thuận tiện trên màn hình laptop và desktop.
- Giữ cấu trúc ổn định khi dữ liệu thay đổi.
- Không bị nhảy layout khi đang tải dữ liệu.

Dashboard ưu tiên khả năng vận hành, đọc số liệu và xử lý công việc hơn yếu tố trang trí.

---

## 2. Cấu trúc tổng thể

Dashboard gồm 3 lớp chính:

1. Sidebar cố định bên trái.
2. Header cố định phía trên khu vực nội dung.
3. Main content có thể cuộn theo chiều dọc.

```text
┌──────────────────────────────────────────────────────────────────┐
│ Sidebar │ Header                                                 │
│         ├────────────────────────────────────────────────────────┤
│         │ Main content                                           │
│         │                                                        │
│         │ Page toolbar                                           │
│         │ KPI cards                                              │
│         │ Charts                                                 │
│         │ Tables and operational widgets                         │
│         │                                                        │
└──────────────────────────────────────────────────────────────────┘
```

---

## 3. Kích thước khung chuẩn

Thiết kế tham chiếu chính:

```text
Canvas: 1440 × 1024 px
```

Thiết kế mở rộng:

```text
Canvas: 1920 × 1080 px
```

Giới hạn nội dung:

```css
--content-max-width: 1600px;
--content-min-width: 960px;
```

Tại màn hình rộng hơn 1920px, nội dung chính phải được giới hạn chiều rộng và căn giữa trong vùng còn lại sau sidebar.

---

## 4. Sidebar và content shell

### 4.1 Sidebar mở rộng

```css
--sidebar-width-expanded: 264px;
```

Áp dụng ở:

```text
Viewport ≥ 1280px
```

### 4.2 Sidebar thu gọn

```css
--sidebar-width-collapsed: 80px;
```

Áp dụng ở:

```text
Viewport từ 1024px đến 1279px
```

### 4.3 Main shell

```css
.dashboard-shell {
  display: grid;
  grid-template-columns: var(--sidebar-width) minmax(0, 1fr);
  min-height: 100vh;
  background: #F6F8FC;
}
```

Khi sidebar thay đổi trạng thái, main content chuyển động mượt trong khoảng 180–220ms.

---

## 5. Header

Header nằm trong vùng nội dung, không phủ lên sidebar.

```css
--header-height: 72px;
```

Thuộc tính:

```css
position: sticky;
top: 0;
z-index: 30;
background: rgba(255, 255, 255, 0.96);
backdrop-filter: blur(12px);
border-bottom: 1px solid #E7ECF3;
```

Padding ngang:

```css
padding-inline: 32px;
```

Trên màn hình từ 1024px đến 1279px:

```css
padding-inline: 24px;
```

Header không được quá cao vì làm giảm diện tích hiển thị dữ liệu.

---

## 6. Main content container

```css
.dashboard-content {
  width: 100%;
  max-width: 1600px;
  margin-inline: auto;
  padding: 28px 32px 48px;
}
```

Responsive:

```css
@media (max-width: 1279px) {
  .dashboard-content {
    padding: 24px;
  }
}
```

Không dùng padding nhỏ hơn 20px trên desktop.

---

## 7. Grid system

Dashboard sử dụng grid 12 cột.

```css
.dashboard-grid {
  display: grid;
  grid-template-columns: repeat(12, minmax(0, 1fr));
  gap: 24px;
}
```

### 7.1 Quy tắc span

| Thành phần | Desktop lớn | Laptop |
|---|---:|---:|
| KPI card | 3 cột | 6 cột |
| Revenue chart | 8 cột | 8 cột |
| Order status | 4 cột | 4 cột |
| Latest orders | 8 cột | 12 cột |
| Inventory alerts | 4 cột | 12 cột |
| Best products | 6 cột | 12 cột |
| Customer summary | 6 cột | 12 cột |

### 7.2 Quy tắc không phá grid

- Card không được dùng width cố định trong grid.
- Dùng `minmax(0, 1fr)` để tránh tràn nội dung.
- Bảng dữ liệu phải nằm trong container có `overflow-x: auto` khi cần.
- Không dùng margin âm.
- Không dùng absolute positioning để căn layout chính.

---

## 8. Vertical rhythm

Khoảng cách dọc chuẩn:

```css
--section-gap: 32px;
--widget-gap: 24px;
--content-gap: 16px;
--compact-gap: 12px;
```

Thứ tự khoảng cách:

- Page toolbar → KPI: 24px.
- KPI → Analytics: 32px.
- Analytics → Operational widgets: 32px.
- Tiêu đề section → nội dung: 16px.
- Các hàng dữ liệu trong card: 12–16px.

Không dùng khoảng cách ngẫu nhiên như 18px, 22px hoặc 30px nếu không có lý do đặc biệt.

---

## 9. Page toolbar

Page toolbar nằm đầu main content.

Cấu trúc:

```text
┌──────────────────────────────────────────────────────────────┐
│ Title + description                  Filter + Export + Action │
└──────────────────────────────────────────────────────────────┘
```

CSS tham chiếu:

```css
.page-toolbar {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 24px;
  margin-bottom: 24px;
}
```

Nội dung trái:

- Tiêu đề trang.
- Mô tả hoặc thời điểm cập nhật gần nhất.

Nội dung phải:

- Date range.
- Bộ lọc chi nhánh.
- Nút xuất báo cáo.
- Nút hành động chính khi cần.

Tại màn hình hẹp, toolbar cho phép xuống dòng.

---

## 10. KPI section layout

Mặc định hiển thị 4 card ngang nhau.

```css
.kpi-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 24px;
}
```

Tại viewport dưới 1280px:

```css
.kpi-grid {
  grid-template-columns: repeat(2, minmax(0, 1fr));
}
```

Không hiển thị 3 card ở một hàng rồi 1 card ở hàng dưới. Nếu số lượng KPI là số lẻ, ưu tiên:

- 3 card trên một hàng khi tổng số là 3.
- 2 + 2 khi tổng số là 4.
- 3 + 3 khi tổng số là 6.

---

## 11. Analytics section layout

Bố cục khuyến nghị:

```text
┌──────────────────────────────────────┬───────────────────────┐
│ Revenue chart — 8 columns            │ Order status — 4 cols │
└──────────────────────────────────────┴───────────────────────┘
```

Chiều cao card:

```css
--analytics-card-min-height: 380px;
```

Hai card trong cùng một hàng phải bằng chiều cao.

Không đặt biểu đồ nhỏ hơn 320px chiều cao vì làm giảm khả năng đọc dữ liệu.

---

## 12. Operational section layout

Bố cục chính:

```text
┌──────────────────────────────────────┬───────────────────────┐
│ Latest orders — 8 columns            │ Inventory — 4 columns │
└──────────────────────────────────────┴───────────────────────┘
```

Hoặc khi bảng cần nhiều không gian:

```text
┌──────────────────────────────────────────────────────────────┐
│ Latest orders — 12 columns                                   │
├──────────────────────────────┬───────────────────────────────┤
│ Inventory — 6 columns        │ Notifications — 6 columns     │
└──────────────────────────────┴───────────────────────────────┘
```

Ưu tiên bố cục thứ hai ở màn hình 1280px.

---

## 13. Card container

Card chuẩn:

```css
.dashboard-card {
  min-width: 0;
  background: #FFFFFF;
  border: 1px solid #E7ECF3;
  border-radius: 20px;
  box-shadow: 0 1px 2px rgba(16, 24, 40, 0.04);
}
```

Padding:

```css
padding: 24px;
```

Card có bảng lớn có thể dùng:

```css
padding: 0;
```

Sau đó tách:

- Card header: 24px.
- Table body: 0px ngang.
- Card footer: 16–24px.

---

## 14. Card header layout

```css
.card-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 16px;
}
```

Bên trái:

- Tiêu đề.
- Mô tả hoặc số liệu phụ.

Bên phải:

- Select filter.
- Menu ba chấm.
- Link xem tất cả.

Không đặt quá 2 hành động bên phải card header.

---

## 15. Sticky và scroll behavior

### 15.1 Thành phần sticky

- Sidebar.
- Header.
- Table header trong bảng dài, nếu cần.

### 15.2 Thành phần không sticky

- KPI card.
- Biểu đồ.
- Notification widget.
- Page toolbar mặc định.

### 15.3 Scroll

Main content cuộn theo trang.

Không tạo nhiều vùng cuộn dọc độc lập bên trong dashboard, trừ:

- Notification dropdown.
- Select dropdown.
- Modal.
- Danh sách rất dài có giới hạn chiều cao rõ ràng.

---

## 16. Z-index system

```css
--z-base: 0;
--z-card-hover: 5;
--z-sticky-header: 30;
--z-dropdown: 50;
--z-modal-backdrop: 80;
--z-modal: 90;
--z-toast: 100;
```

Không sử dụng giá trị tùy ý như `9999` nếu không cần thiết.

---

## 17. Responsive behavior

### 17.1 Màn hình từ 1440px trở lên

- Sidebar mở rộng 264px.
- Main content padding 32px.
- KPI 4 cột.
- Revenue chart 8 cột.
- Order status 4 cột.
- Latest orders 8 cột.
- Inventory 4 cột.

### 17.2 Màn hình 1280–1439px

- Sidebar mở rộng 240–264px.
- Main content padding 24px.
- KPI 4 cột nếu còn đủ không gian, nếu không chuyển 2 cột.
- Latest orders ưu tiên 12 cột.
- Widget phụ chuyển xuống hàng dưới.

### 17.3 Màn hình 1024–1279px

- Sidebar thu gọn 80px.
- KPI 2 cột.
- Analytics vẫn có thể giữ 8/4 nếu đọc tốt.
- Bảng chiếm 12 cột.
- Widget phụ xếp 1 cột hoặc 2 cột tùy chiều rộng.

### 17.4 Dưới 1024px

Dashboard không phải trải nghiệm chính.

Có thể áp dụng:

- Sidebar dạng drawer.
- Grid 1 cột.
- Bảng cuộn ngang.
- Ẩn bớt widget phụ.

Không cần tối ưu như mobile app khách hàng.

---

## 18. Breakpoint token

```css
--bp-tablet: 1024px;
--bp-laptop: 1280px;
--bp-desktop: 1440px;
--bp-wide: 1920px;
```

Không tạo breakpoint riêng cho từng component nếu chưa thực sự cần.

---

## 19. Minimum dimensions

| Component | Chiều rộng tối thiểu | Chiều cao tối thiểu |
|---|---:|---:|
| KPI card | 240px | 156px |
| Revenue chart | 620px | 380px |
| Order status | 300px | 380px |
| Latest orders | 760px | 420px |
| Inventory alerts | 300px | 420px |
| Product widget | 420px | 360px |

Nếu component không đạt chiều rộng tối thiểu, phải chuyển xuống hàng mới thay vì ép nhỏ.

---

## 20. Width behavior của bảng

Bảng đơn hàng thường có nhiều cột. Container phải xử lý:

```css
.table-wrapper {
  width: 100%;
  overflow-x: auto;
}
```

Bảng:

```css
.data-table {
  width: 100%;
  min-width: 920px;
  border-collapse: separate;
  border-spacing: 0;
}
```

Không ép cột quan trọng xuống quá hẹp.

Ưu tiên cố định chiều rộng tương đối cho:

- Mã đơn.
- Trạng thái.
- Giá trị.
- Hành động.

Cho phép tên khách hàng và sản phẩm linh hoạt hơn.

---

## 21. Container query — tùy chọn

Nếu frontend sử dụng trình duyệt hiện đại, có thể dùng container query cho widget:

```css
.widget-container {
  container-type: inline-size;
}

@container (max-width: 420px) {
  .widget-header {
    flex-direction: column;
    align-items: stretch;
  }
}
```

Container query chỉ dùng để tối ưu component nội bộ, không thay thế breakpoint tổng thể.

---

## 22. Layout loading state

Khi tải dữ liệu:

- Giữ nguyên kích thước card.
- Giữ nguyên số hàng KPI.
- Biểu đồ dùng placeholder có chiều cao cố định.
- Bảng dùng skeleton 5–8 dòng.
- Không làm các section nhảy vị trí sau khi tải xong.

Ví dụ:

```css
.chart-skeleton {
  min-height: 320px;
}
```

---

## 23. Layout empty state

Empty state không được làm card co lại quá nhỏ.

Ví dụ card đơn hàng:

```css
min-height: 360px;
```

Nội dung empty state căn giữa theo cả hai chiều trong phần body card.

---

## 24. Layout error state

Khi một widget lỗi:

- Chỉ thay nội dung widget đó bằng error state.
- Không làm sập cả trang.
- Giữ nguyên vị trí và kích thước của widget.
- Có nút thử lại.

Nếu toàn bộ dashboard lỗi, dùng page-level error state trong main content, vẫn giữ sidebar và header.

---

## 25. Layout cho modal và drawer

### 25.1 Modal

Kích thước:

```css
--modal-sm: 480px;
--modal-md: 640px;
--modal-lg: 880px;
```

Modal không vượt quá:

```css
max-height: calc(100vh - 64px);
```

### 25.2 Drawer

Dùng cho:

- Xem nhanh đơn hàng.
- Xem chi tiết khách hàng.
- Chỉnh sửa nhanh sản phẩm.

Chiều rộng:

```css
--drawer-md: 520px;
--drawer-lg: 720px;
```

Drawer mở từ bên phải.

---

## 26. Layout cho toast

Toast đặt ở góc trên bên phải, dưới header.

```css
.toast-region {
  position: fixed;
  top: 88px;
  right: 24px;
  width: min(380px, calc(100vw - 48px));
  z-index: 100;
}
```

Không đặt toast che lên điều hướng hoặc nút hành động chính.

---

## 27. Layout cho notification dropdown

Dropdown thông báo:

```css
width: 380px;
max-height: 520px;
```

Vị trí:

- Căn phải với icon notification.
- Cách header 8px.
- Có scroll nội bộ nếu danh sách dài.

Trên viewport hẹp, dropdown có thể chuyển thành drawer.

---

## 28. Quy tắc bố cục cho hành động chính

Mỗi màn hình chỉ nên có một primary action nổi bật.

Ví dụ:

```text
[ Xuất báo cáo ] [ + Tạo đơn hàng ]
```

Trong đó:

- `Tạo đơn hàng` là primary.
- `Xuất báo cáo` là secondary.

Không dùng nhiều hơn 2 nút ở page toolbar nếu không cần thiết.

---

## 29. Layout CSS tham chiếu

```css
:root {
  --sidebar-width-expanded: 264px;
  --sidebar-width-collapsed: 80px;
  --header-height: 72px;
  --content-max-width: 1600px;
  --page-padding: 32px;
  --grid-gap: 24px;
}

.dashboard-shell {
  min-height: 100vh;
  display: grid;
  grid-template-columns: var(--sidebar-width-expanded) minmax(0, 1fr);
  background: #F6F8FC;
}

.dashboard-main {
  min-width: 0;
}

.dashboard-header {
  height: var(--header-height);
  position: sticky;
  top: 0;
  z-index: 30;
}

.dashboard-content {
  width: 100%;
  max-width: var(--content-max-width);
  margin-inline: auto;
  padding: 28px var(--page-padding) 48px;
}

.dashboard-grid {
  display: grid;
  grid-template-columns: repeat(12, minmax(0, 1fr));
  gap: var(--grid-gap);
}
```

---

## 30. Component tree đề xuất

```text
DashboardShell
├── Sidebar
├── DashboardMain
│   ├── Header
│   └── DashboardContent
│       ├── PageToolbar
│       ├── KPIGrid
│       │   └── KPI cards
│       ├── AnalyticsGrid
│       │   ├── RevenueChartCard
│       │   └── OrderStatusCard
│       ├── OperationalGrid
│       │   ├── LatestOrdersCard
│       │   └── InventoryAlertCard
│       └── SecondaryGrid
│           ├── BestProductsCard
│           └── CustomerSummaryCard
```

---

## 31. Acceptance checklist

- [ ] Sidebar và header giữ đúng kích thước token.
- [ ] Main content không tràn ngang ngoài viewport.
- [ ] Grid dùng 12 cột và gap 24px.
- [ ] KPI hiển thị 4 cột ở desktop lớn.
- [ ] Bảng không làm vỡ layout.
- [ ] Sidebar thu gọn đúng tại viewport nhỏ.
- [ ] Card cùng hàng có chiều cao hợp lý.
- [ ] Loading state không gây layout shift.
- [ ] Empty state giữ kích thước card.
- [ ] Header sticky không che nội dung.
- [ ] Modal, drawer và toast có z-index đúng.
- [ ] Nội dung rộng không vượt quá 1600px.
- [ ] Không có vùng scroll dọc lồng nhau không cần thiết.

---

## 32. Kết luận

Layout của Cynca VLXD Dashboard phải tạo cảm giác ổn định, thoáng và có hệ thống. Mọi widget phải tuân theo grid, spacing và responsive rule chung để giao diện không bị rời rạc khi mở rộng thêm module hoặc dữ liệu.

File tiếp theo:

```text
03-Sidebar.md
```