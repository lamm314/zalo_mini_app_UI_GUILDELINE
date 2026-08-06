# Cynca VLXD Admin Dashboard — Header Specification

> Phiên bản: 1.0  
> Phạm vi: Thanh điều hướng phía trên của Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`, `02-Layout.md`, `03-Sidebar.md`

---

## 1. Vai trò của header

Header là vùng điều hướng và thao tác nhanh nằm phía trên khu vực nội dung chính. Thành phần này giúp người dùng:

- Tìm kiếm nhanh dữ liệu trong hệ thống.
- Chọn khoảng thời gian thống kê.
- Chọn chi nhánh hoặc phạm vi dữ liệu.
- Theo dõi thông báo mới.
- Truy cập hồ sơ tài khoản.
- Thực hiện hành động nhanh như xuất báo cáo.
- Nhận biết trạng thái kết nối hoặc đồng bộ khi cần.

Header phải gọn, ổn định và không cạnh tranh thị giác với nội dung Dashboard.

---

## 2. Cấu trúc tổng thể

```text
Header
├── Left area
│   ├── Mobile/sidebar toggle
│   ├── Breadcrumb hoặc page context
│   └── Global search
├── Flexible spacer
└── Right area
    ├── Date range selector
    ├── Branch filter
    ├── Export action
    ├── Notification button
    └── User menu
```

Bố cục tham chiếu:

```text
┌──────────────────────────────────────────────────────────────────────┐
│ ☰  Tổng quan / Dashboard   [ Tìm kiếm... ]   [30 ngày] [HN] [Xuất] 🔔 👤 │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 3. Kích thước và vị trí

```css
--header-height: 72px;
```

Thuộc tính:

```css
position: sticky;
top: 0;
z-index: 30;
height: 72px;
background: rgba(255, 255, 255, 0.96);
backdrop-filter: blur(12px);
border-bottom: 1px solid #E7ECF3;
```

Padding ngang:

```css
padding-inline: 32px;
```

Ở viewport từ 1024px đến 1279px:

```css
padding-inline: 24px;
```

Header không phủ lên sidebar.

---

## 4. Layout bên trong

```css
.dashboard-header-inner {
  display: flex;
  align-items: center;
  gap: 16px;
  height: 100%;
  min-width: 0;
}
```

Left area:

```css
.header-left {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 0;
}
```

Right area:

```css
.header-right {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-left: auto;
  flex: 0 0 auto;
}
```

Search có thể co giãn nhưng không đẩy các hành động bên phải ra ngoài viewport.

---

## 5. Breadcrumb và page context

### 5.1 Mục đích

Breadcrumb cho biết vị trí hiện tại trong hệ thống.

Ví dụ:

```text
Tổng quan
```

Hoặc:

```text
Sản phẩm / Chỉnh sửa sản phẩm
```

### 5.2 Quy tắc

- Tối đa 3 cấp.
- Cấp cuối là trang hiện tại.
- Không lặp lại tiêu đề quá dài.
- Dùng dấu `/` hoặc chevron nhỏ.
- Chỉ cấp trước đó mới có thể click.

### 5.3 Typography

```css
font-size: 14px;
font-weight: 500;
color: #667085;
```

Trang hiện tại:

```css
color: #182230;
font-weight: 600;
```

### 5.4 Responsive

Ở viewport hẹp, chỉ hiển thị trang hiện tại hoặc ẩn breadcrumb nếu đã có page title rõ ràng trong nội dung.

---

## 6. Sidebar toggle

### 6.1 Khi hiển thị

- Luôn có thể hiển thị khi sidebar thu gọn.
- Bắt buộc hiển thị dưới 1024px để mở drawer.
- Có thể ẩn ở desktop lớn nếu đã có collapse button trong sidebar.

### 6.2 Kích thước

```css
width: 40px;
height: 40px;
border-radius: 12px;
```

### 6.3 Trạng thái

Default:

```css
color: #526071;
background: transparent;
```

Hover:

```css
background: #F1F4F9;
color: #182230;
```

Focus:

```css
outline: 3px solid rgba(11,87,240,0.20);
outline-offset: 2px;
```

Icon đề xuất: `Menu`, `PanelLeftOpen`.

---

## 7. Global search

### 7.1 Mục tiêu

Search cho phép tìm nhanh:

- Mã đơn hàng.
- Tên khách hàng.
- Số điện thoại.
- Tên sản phẩm.
- SKU.
- Nhà cung cấp.

### 7.2 Kích thước

```css
width: clamp(260px, 28vw, 420px);
height: 40px;
```

### 7.3 Cấu trúc

```text
[Search icon] Tìm đơn hàng, sản phẩm, khách hàng... [⌘K]
```

### 7.4 Styling

```css
.global-search {
  display: flex;
  align-items: center;
  gap: 10px;
  height: 40px;
  padding-inline: 12px;
  border: 1px solid #DDE3EC;
  border-radius: 12px;
  background: #F8FAFC;
}
```

Focus:

```css
border-color: #0B57F0;
box-shadow: 0 0 0 3px rgba(11,87,240,0.12);
background: #FFFFFF;
```

### 7.5 Placeholder

```text
Tìm đơn hàng, sản phẩm, khách hàng...
```

Không dùng placeholder quá dài trên màn hình laptop.

### 7.6 Keyboard shortcut

Đề xuất:

```text
Ctrl + K hoặc Cmd + K
```

Khi kích hoạt có thể mở command palette hoặc focus search.

### 7.7 Search result dropdown

Chiều rộng:

```css
width: min(560px, calc(100vw - 48px));
max-height: 520px;
```

Nhóm kết quả:

- Đơn hàng.
- Sản phẩm.
- Khách hàng.
- Nhà cung cấp.

Mỗi kết quả gồm:

- Icon hoặc thumbnail.
- Tiêu đề.
- Mô tả phụ.
- Loại dữ liệu.
- Trạng thái nếu có.

### 7.8 Empty state

```text
Không tìm thấy kết quả
Hãy thử từ khóa khác hoặc kiểm tra lại mã đơn hàng.
```

---

## 8. Date range selector

### 8.1 Mục tiêu

Dùng để thay đổi khoảng thời gian thống kê cho KPI và biểu đồ.

### 8.2 Preset

- Hôm nay.
- 7 ngày qua.
- 30 ngày qua.
- Tháng này.
- Quý này.
- Năm nay.
- Tùy chỉnh.

### 8.3 Kích thước

```css
height: 40px;
min-width: 132px;
```

### 8.4 Nội dung

```text
[Calendar icon] 30 ngày qua [Chevron]
```

### 8.5 Styling

```css
border: 1px solid #DDE3EC;
border-radius: 12px;
background: #FFFFFF;
color: #344054;
```

### 8.6 Custom range

Khi chọn tùy chỉnh, hiển thị date range picker.

Yêu cầu:

- Định dạng ngày `dd/mm/yyyy`.
- Không cho phép ngày kết thúc trước ngày bắt đầu.
- Có nút `Áp dụng` và `Đặt lại`.
- Hiển thị timezone nếu hệ thống có nhiều quốc gia.

---

## 9. Branch filter

### 9.1 Mục tiêu

Chọn phạm vi dữ liệu theo:

- Toàn hệ thống.
- Chi nhánh.
- Kho.
- Khu vực.

### 9.2 Kích thước

```css
height: 40px;
min-width: 112px;
max-width: 180px;
```

### 9.3 Nội dung

```text
[Building icon] Hà Nội [Chevron]
```

### 9.4 Quy tắc

- Chỉ hiển thị nếu người dùng có quyền xem nhiều đơn vị.
- Với một chi nhánh duy nhất, có thể ẩn filter.
- Giá trị đã chọn phải đồng bộ với Dashboard content.
- Thay đổi filter phải cập nhật URL query hoặc state rõ ràng.

---

## 10. Export button

### 10.1 Mục tiêu

Cho phép xuất báo cáo hiện tại.

### 10.2 Nhãn

```text
Xuất báo cáo
```

Ở màn hình hẹp có thể chỉ hiển thị icon và tooltip.

### 10.3 Kiểu nút

Secondary button:

```css
height: 40px;
padding-inline: 14px;
border: 1px solid #DDE3EC;
border-radius: 12px;
background: #FFFFFF;
color: #344054;
font-size: 14px;
font-weight: 600;
```

Hover:

```css
background: #F8FAFC;
border-color: #C9D1DD;
```

### 10.4 Menu export

- CSV.
- Excel.
- PDF.

Tùy nghiệp vụ có thể thêm:

- Gửi qua email.
- Lưu mẫu báo cáo.

### 10.5 Loading

Khi đang xuất:

```text
Đang tạo báo cáo...
```

Giữ nguyên chiều rộng nút để tránh layout shift.

---

## 11. Notification button

### 11.1 Kích thước

```css
width: 40px;
height: 40px;
border-radius: 12px;
```

Icon: `Bell`.

### 11.2 Badge

```css
position: absolute;
top: 7px;
right: 7px;
min-width: 16px;
height: 16px;
border-radius: 999px;
background: #F04438;
color: #FFFFFF;
font-size: 10px;
font-weight: 700;
```

Nếu chỉ cần báo có thông báo mới, dùng dot 8px.

### 11.3 Dropdown

```css
width: 380px;
max-height: 520px;
```

Header dropdown:

```text
Thông báo                       Đánh dấu đã đọc
```

Nhóm thông báo:

- Đơn hàng mới.
- Tồn kho thấp.
- Thanh toán.
- Hệ thống.
- Marketing.

### 11.4 Notification item

Gồm:

- Icon loại thông báo.
- Tiêu đề.
- Mô tả ngắn.
- Thời gian.
- Dấu chưa đọc.

### 11.5 Empty state

```text
Bạn chưa có thông báo mới
```

### 11.6 Accessibility

Button phải có:

```html
aria-label="Thông báo, 3 thông báo chưa đọc"
```

---

## 12. User menu

### 12.1 Trigger

```text
[Avatar] Nguyễn Văn A [Chevron]
```

Ở màn hình nhỏ có thể chỉ hiển thị avatar.

### 12.2 Avatar

```css
width: 36px;
height: 36px;
border-radius: 999px;
```

### 12.3 User info

Tên:

```css
font-size: 13px;
font-weight: 600;
color: #182230;
```

Vai trò:

```css
font-size: 12px;
color: #7B8797;
```

### 12.4 Dropdown menu

Nội dung:

- Hồ sơ cá nhân.
- Đổi mật khẩu.
- Tùy chọn giao diện.
- Trung tâm trợ giúp.
- Đăng xuất.

Dropdown:

```css
width: 240px;
border-radius: 14px;
background: #FFFFFF;
box-shadow: 0 12px 32px rgba(16,24,40,0.14);
```

`Đăng xuất` dùng màu danger.

---

## 13. Sync hoặc connection status — tùy chọn

Nếu hệ thống có đồng bộ dữ liệu, có thể hiển thị trạng thái nhỏ:

```text
● Đã đồng bộ 2 phút trước
```

Trạng thái:

- Đã đồng bộ.
- Đang đồng bộ.
- Mất kết nối.
- Đồng bộ lỗi.

Không hiển thị nếu hệ thống không có nhu cầu thực tế.

---

## 14. Header states

### 14.1 Default

- Nền trắng gần như đặc.
- Border dưới nhẹ.
- Không dùng shadow đậm.

### 14.2 Scrolled

Khi nội dung cuộn:

```css
box-shadow: 0 4px 12px rgba(16,24,40,0.04);
```

### 14.3 Loading

Giữ header hoạt động bình thường. Chỉ disable các filter liên quan khi dữ liệu đang cập nhật.

### 14.4 Offline

Có thể hiển thị banner mỏng dưới header:

```text
Mất kết nối mạng. Một số dữ liệu có thể chưa được cập nhật.
```

---

## 15. Responsive behavior

### 15.1 Viewport ≥ 1440px

Hiển thị đầy đủ:

- Breadcrumb.
- Search 360–420px.
- Date range.
- Branch filter.
- Export.
- Notification.
- User name và avatar.

### 15.2 Viewport 1280–1439px

- Search giảm còn 280–340px.
- User menu có thể ẩn role.
- Export giữ nhãn nếu đủ chỗ.

### 15.3 Viewport 1024–1279px

- Search còn 240–280px.
- Export chỉ hiển thị icon.
- User chỉ hiển thị avatar.
- Branch filter có thể chuyển vào page toolbar.
- Breadcrumb rút gọn.

### 15.4 Viewport < 1024px

- Hiển thị menu button.
- Search có thể chuyển thành icon mở command palette.
- Date range và branch filter chuyển xuống page toolbar hoặc filter drawer.
- Chỉ giữ notification và avatar.

---

## 16. Overflow strategy

Thứ tự ưu tiên khi thiếu chiều rộng:

1. Ẩn user role.
2. Ẩn user name.
3. Chuyển export thành icon-only.
4. Rút ngắn date range label.
5. Chuyển branch filter xuống page toolbar.
6. Chuyển search thành icon hoặc command palette.

Không cho header cuộn ngang.

---

## 17. Keyboard navigation

Thứ tự focus đề xuất:

1. Sidebar toggle.
2. Breadcrumb links.
3. Search.
4. Date range.
5. Branch filter.
6. Export.
7. Notification.
8. User menu.

Yêu cầu:

- Enter hoặc Space mở dropdown.
- Escape đóng dropdown.
- Arrow keys điều hướng trong menu nếu triển khai menu widget.
- Focus trả về trigger sau khi đóng.

---

## 18. Accessibility

Semantic HTML:

```html
<header>
  <nav aria-label="Điều hướng phụ của trang quản trị">
    ...
  </nav>
</header>
```

Search:

```html
<label for="global-search">Tìm kiếm toàn hệ thống</label>
<input id="global-search" type="search" />
```

Icon-only button phải có `aria-label`.

Dropdown trigger phải có:

```html
aria-expanded="false"
aria-haspopup="menu"
```

Badge không được là cách duy nhất thể hiện số lượng thông báo.

---

## 19. Animation

### Dropdown

```css
duration: 160ms;
opacity: 0 → 1;
transform: translateY(-4px) → translateY(0);
```

### Search result

```css
duration: 180ms;
```

### Header shadow

```css
duration: 140ms;
```

Không animate toàn bộ header khi cuộn.

---

## 20. Z-index

```css
--z-header: 30;
--z-search-dropdown: 50;
--z-filter-dropdown: 50;
--z-notification-dropdown: 50;
--z-user-dropdown: 50;
```

Chỉ một dropdown nên mở tại một thời điểm.

---

## 21. Loading, empty và error state

### 21.1 Search loading

- Hiển thị spinner nhỏ.
- Giữ kết quả cũ nếu phù hợp.

### 21.2 Search error

```text
Không thể tìm kiếm lúc này
[ Thử lại ]
```

### 21.3 Notification loading

Dùng 4–5 skeleton item.

### 21.4 Notification error

```text
Không thể tải thông báo
[ Thử lại ]
```

### 21.5 Filter error

Nếu filter không tải được, hiển thị giá trị hiện tại và disable thay đổi, không làm sập header.

---

## 22. CSS tham chiếu

```css
.dashboard-header {
  position: sticky;
  top: 0;
  z-index: 30;
  height: 72px;
  background: rgba(255,255,255,0.96);
  backdrop-filter: blur(12px);
  border-bottom: 1px solid #E7ECF3;
}

.dashboard-header-inner {
  height: 100%;
  display: flex;
  align-items: center;
  gap: 16px;
  padding-inline: 32px;
}

.header-action {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  height: 40px;
  border: 1px solid #DDE3EC;
  border-radius: 12px;
  background: #FFFFFF;
  color: #344054;
}

.header-action:hover {
  background: #F8FAFC;
  border-color: #C9D1DD;
}

.header-icon-button {
  width: 40px;
  padding: 0;
}
```

---

## 23. Component tree đề xuất

```text
DashboardHeader
├── HeaderLeft
│   ├── SidebarToggle
│   ├── Breadcrumbs
│   └── GlobalSearch
├── HeaderSpacer
└── HeaderRight
    ├── DateRangeSelector
    ├── BranchSelector
    ├── ExportMenu
    ├── NotificationMenu
    └── UserMenu
```

---

## 24. Props TypeScript tham khảo

```ts
export type DashboardHeaderProps = {
  currentPath: string;
  searchQuery: string;
  onSearchQueryChange: (value: string) => void;
  dateRange: {
    from: Date;
    to: Date;
  };
  onDateRangeChange: (range: { from: Date; to: Date }) => void;
  branchId?: string;
  onBranchChange?: (branchId: string) => void;
  unreadNotifications: number;
  user: {
    name: string;
    role: string;
    avatarUrl?: string;
  };
};
```

---

## 25. Acceptance checklist

- [ ] Header cao đúng 72px.
- [ ] Header sticky không che nội dung.
- [ ] Search dễ nhận biết và focus rõ ràng.
- [ ] Date range dùng định dạng ngày Việt Nam.
- [ ] Branch filter chỉ hiển thị theo quyền.
- [ ] Export button có loading state.
- [ ] Notification badge hiển thị đúng số lượng.
- [ ] User menu có đầy đủ hồ sơ và đăng xuất.
- [ ] Dropdown không vượt khỏi viewport.
- [ ] Chỉ một dropdown mở tại một thời điểm.
- [ ] Header không cuộn ngang ở 1024px.
- [ ] Icon-only button có tooltip và aria-label.
- [ ] Keyboard navigation hoạt động.
- [ ] Focus trả về trigger sau khi đóng menu.
- [ ] Search empty và error state đầy đủ.
- [ ] Responsive đúng thứ tự ưu tiên.

---

## 26. Những điều không được làm

- Không đặt quá nhiều nút primary trong header.
- Không dùng header cao hơn 80px.
- Không để search chiếm toàn bộ chiều rộng.
- Không dùng avatar quá lớn.
- Không đặt breadcrumb nhiều hơn 3 cấp.
- Không dùng badge đỏ cho mọi loại thông báo.
- Không để nhiều dropdown mở cùng lúc.
- Không dùng shadow đậm hoặc glow.
- Không cho filter thay đổi mà không phản ánh vào dữ liệu trang.
- Không ẩn hoàn toàn khả năng tìm kiếm ở laptop.

---

## 27. Kết luận

Header của Cynca VLXD phải cung cấp các công cụ thao tác nhanh cần thiết mà vẫn giữ giao diện gọn gàng. Search, filter, notification và tài khoản phải dễ tiếp cận, có đầy đủ trạng thái tương tác và thích ứng tốt với nhiều kích thước màn hình.

File tiếp theo:

```text
05-KPI-Cards.md
```