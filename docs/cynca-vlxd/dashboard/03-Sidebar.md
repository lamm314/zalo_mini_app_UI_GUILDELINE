# Cynca VLXD Admin Dashboard — Sidebar Specification

> Phiên bản: 1.0  
> Phạm vi: Điều hướng bên trái của Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`, `02-Layout.md`

---

## 1. Vai trò của sidebar

Sidebar là khu vực điều hướng chính của hệ thống quản trị Cynca VLXD. Thành phần này phải giúp người dùng:

- Nhận biết nhanh vị trí hiện tại.
- Chuyển module trong một lần nhấp.
- Truy cập các tác vụ quản trị thường xuyên.
- Nhận biết khu vực có thông báo, đơn mới hoặc cảnh báo.
- Thu gọn để tăng diện tích làm việc khi cần.
- Hiển thị đúng theo vai trò và phân quyền.

Sidebar phải ổn định, dễ học và không thay đổi cấu trúc tùy tiện giữa các màn hình.

---

## 2. Định hướng thẩm mỹ

Phong cách sidebar:

- Xanh dương thương hiệu.
- Tối giản.
- Tương phản rõ.
- Icon nét mảnh, đồng nhất.
- Active state nổi bật nhưng không chói.
- Không dùng nhiều màu cho từng menu item.
- Không dùng emoji làm icon giao diện chính.

Màu nền chính:

```css
--sidebar-bg: #0B4FD8;
--sidebar-bg-deep: #083FAE;
```

Có thể dùng gradient rất nhẹ:

```css
background: linear-gradient(180deg, #0B57F0 0%, #0A48C8 100%);
```

Không sử dụng gradient nhiều màu hoặc hiệu ứng bóng phát sáng.

---

## 3. Kích thước

### 3.1 Trạng thái mở rộng

```css
--sidebar-width-expanded: 264px;
```

Áp dụng mặc định ở viewport từ 1280px trở lên.

### 3.2 Trạng thái thu gọn

```css
--sidebar-width-collapsed: 80px;
```

Áp dụng khi:

- Người dùng chủ động thu gọn.
- Viewport từ 1024px đến 1279px.

### 3.3 Chiều cao

```css
min-height: 100vh;
height: 100dvh;
```

Sidebar cố định toàn chiều cao màn hình và có vùng cuộn nội bộ cho menu khi cần.

---

## 4. Cấu trúc sidebar

```text
Sidebar
├── Brand area
│   ├── Logo
│   ├── Brand name
│   └── Collapse button
├── Workspace / branch selector
├── Navigation area
│   ├── Main navigation group
│   ├── Commerce group
│   ├── Operations group
│   └── System group
├── Spacer
├── Help / support
└── User profile area
```

Cấu trúc tổng quát:

```text
┌──────────────────────────────┐
│ Logo + Cynca VLXD        ◀   │
├──────────────────────────────┤
│ Chi nhánh Hà Nội         ⌄   │
├──────────────────────────────┤
│ Tổng quan                   │
│ Đơn hàng                    │
│ Sản phẩm                    │
│ Danh mục                    │
│ Khách hàng                  │
│ Nhà cung cấp                │
│ Kho hàng                    │
│ Báo cáo                     │
│ Marketing                   │
│ Cài đặt                     │
├──────────────────────────────┤
│ Trợ giúp                    │
│ Tài khoản quản trị          │
└──────────────────────────────┘
```

---

## 5. Brand area

### 5.1 Kích thước

```css
height: 72px;
padding-inline: 20px;
```

### 5.2 Logo

Logo icon:

```css
width: 36px;
height: 36px;
border-radius: 10px;
background: rgba(255,255,255,0.14);
```

Logo phải hoạt động tốt ở cả trạng thái mở rộng và thu gọn.

### 5.3 Tên thương hiệu

```text
Cynca VLXD
```

Typography:

```css
font-size: 18px;
font-weight: 700;
line-height: 24px;
color: #FFFFFF;
```

Có thể có dòng phụ:

```text
Quản trị hệ thống
```

Typography dòng phụ:

```css
font-size: 12px;
font-weight: 400;
color: rgba(255,255,255,0.68);
```

### 5.4 Collapse button

Kích thước:

```css
width: 32px;
height: 32px;
border-radius: 10px;
```

Trạng thái hover:

```css
background: rgba(255,255,255,0.12);
```

Icon:

- `PanelLeftClose` khi mở rộng.
- `PanelLeftOpen` khi thu gọn.

Button phải có:

```html
aria-label="Thu gọn thanh điều hướng"
```

hoặc

```html
aria-label="Mở rộng thanh điều hướng"
```

---

## 6. Workspace hoặc branch selector

Nếu hệ thống có nhiều chi nhánh, selector được đặt dưới brand area.

### 6.1 Nội dung

```text
Chi nhánh hiện tại
Hà Nội
```

### 6.2 Kích thước

```css
margin: 8px 16px 16px;
padding: 12px;
border-radius: 14px;
background: rgba(255,255,255,0.10);
```

### 6.3 Hover

```css
background: rgba(255,255,255,0.15);
```

### 6.4 Trạng thái thu gọn

Chỉ hiển thị icon chi nhánh. Tooltip hiển thị tên chi nhánh khi hover hoặc focus.

### 6.5 Menu dropdown

Danh sách có thể gồm:

- Tất cả hệ thống.
- Hà Nội.
- Hồ Chí Minh.
- Kho trung tâm.
- Chi nhánh khác.

Nếu người dùng không có quyền đổi chi nhánh, selector chuyển thành nhãn tĩnh.

---

## 7. Navigation groups

Menu được chia theo nhóm để giảm tải nhận thức.

### 7.1 Nhóm chính

```text
Tổng quan
```

### 7.2 Nhóm bán hàng

```text
Đơn hàng
Khách hàng
Marketing
```

### 7.3 Nhóm sản phẩm và vận hành

```text
Sản phẩm
Danh mục
Kho hàng
Nhà cung cấp
```

### 7.4 Nhóm phân tích

```text
Báo cáo
```

### 7.5 Nhóm hệ thống

```text
Nhân viên
Phân quyền
Cài đặt
```

Không nhất thiết hiển thị mọi nhóm cho mọi vai trò.

---

## 8. Group label

Nhãn nhóm chỉ hiển thị khi sidebar mở rộng.

Typography:

```css
font-size: 11px;
font-weight: 600;
letter-spacing: 0.08em;
text-transform: uppercase;
color: rgba(255,255,255,0.52);
```

Spacing:

```css
padding: 16px 20px 8px;
```

Không dùng group label quá dài.

---

## 9. Menu item chuẩn

### 9.1 Kích thước

```css
height: 44px;
margin-inline: 12px;
padding-inline: 12px;
border-radius: 12px;
```

### 9.2 Cấu trúc

```text
[Icon] Nhãn menu           [Badge/Chevron]
```

CSS tham chiếu:

```css
.sidebar-item {
  display: flex;
  align-items: center;
  gap: 12px;
  height: 44px;
  padding-inline: 12px;
  border-radius: 12px;
  color: rgba(255,255,255,0.78);
  transition: background-color 160ms ease,
              color 160ms ease,
              transform 160ms ease;
}
```

### 9.3 Icon

```css
width: 20px;
height: 20px;
flex: 0 0 auto;
stroke-width: 1.9;
```

### 9.4 Label

```css
font-size: 14px;
font-weight: 500;
line-height: 20px;
white-space: nowrap;
overflow: hidden;
text-overflow: ellipsis;
```

---

## 10. Trạng thái menu item

### 10.1 Default

```css
color: rgba(255,255,255,0.76);
background: transparent;
```

### 10.2 Hover

```css
color: #FFFFFF;
background: rgba(255,255,255,0.10);
```

Không dùng scale lớn khi hover.

Có thể dùng:

```css
transform: translateX(2px);
```

chỉ ở desktop và tối đa 2px.

### 10.3 Focus

```css
outline: 3px solid rgba(255,255,255,0.28);
outline-offset: 2px;
```

### 10.4 Active

```css
color: #FFFFFF;
background: rgba(255,255,255,0.16);
font-weight: 600;
```

Có thể bổ sung indicator trái:

```css
box-shadow: inset 3px 0 0 #FFFFFF;
```

Chỉ dùng một trong hai cách:

- Nền nổi rõ.
- Indicator trái.

Không cần dùng cả hai quá mạnh.

### 10.5 Disabled

```css
opacity: 0.45;
cursor: not-allowed;
```

Disabled item không được focus bằng bàn phím nếu thật sự không tương tác.

---

## 11. Menu badge

Badge dùng cho:

- Đơn hàng mới.
- Cảnh báo tồn kho.
- Thông báo cần xử lý.

### 11.1 Badge số

```css
min-width: 20px;
height: 20px;
padding-inline: 6px;
border-radius: 999px;
background: #FFFFFF;
color: #0B57F0;
font-size: 11px;
font-weight: 700;
```

### 11.2 Dot indicator

```css
width: 8px;
height: 8px;
border-radius: 999px;
background: #FEC84B;
```

### 11.3 Quy tắc

- Hiển thị tối đa `99+`.
- Không dùng badge cho dữ liệu không cần chú ý.
- Badge phải được cập nhật theo thời gian thực hoặc theo chu kỳ hợp lý.
- Không dùng màu đỏ nếu chỉ là số lượng thông thường.

---

## 12. Submenu

Submenu dùng cho module có nhiều trang con.

Ví dụ:

```text
Sản phẩm
├── Tất cả sản phẩm
├── Thêm sản phẩm
├── Danh mục
└── Thuộc tính
```

### 12.1 Trigger

Menu cha có chevron ở bên phải.

### 12.2 Animation

```css
duration: 180ms;
```

Không dùng animation chiều cao quá chậm.

### 12.3 Submenu item

```css
height: 40px;
padding-left: 44px;
font-size: 13px;
```

### 12.4 Active submenu

```css
color: #FFFFFF;
font-weight: 600;
```

Có thể dùng dot nhỏ hoặc thanh dọc mảnh để chỉ trạng thái.

### 12.5 Trạng thái thu gọn

Khi sidebar thu gọn, submenu mở dạng flyout bên phải.

Flyout:

```css
width: 220px;
background: #FFFFFF;
color: #182230;
border-radius: 14px;
box-shadow: 0 12px 32px rgba(16,24,40,0.14);
```

---

## 13. Trạng thái sidebar thu gọn

Khi thu gọn:

- Chỉ hiển thị icon.
- Ẩn logo text.
- Ẩn group label.
- Ẩn badge text, giữ dot hoặc badge nhỏ nếu cần.
- Căn giữa icon.
- Hiển thị tooltip khi hover hoặc focus.

CSS tham chiếu:

```css
.sidebar--collapsed .sidebar-item {
  justify-content: center;
  padding-inline: 0;
}
```

Tooltip phải hiển thị:

```text
Đơn hàng
```

và nếu có badge:

```text
Đơn hàng — 12 đơn mới
```

---

## 14. Tooltip

Tooltip dùng trong sidebar thu gọn.

```css
padding: 8px 10px;
border-radius: 8px;
background: #101828;
color: #FFFFFF;
font-size: 12px;
box-shadow: 0 6px 18px rgba(16,24,40,0.18);
```

Vị trí:

- Bên phải icon.
- Khoảng cách 10px.
- Không bị cắt bởi container sidebar.

Tooltip phải xuất hiện khi:

- Hover.
- Focus bằng bàn phím.

---

## 15. Sidebar scroll

Navigation area được phép cuộn dọc độc lập nếu danh sách dài.

```css
.sidebar-nav {
  min-height: 0;
  overflow-y: auto;
  overscroll-behavior: contain;
}
```

Scrollbar:

```css
scrollbar-width: thin;
scrollbar-color: rgba(255,255,255,0.28) transparent;
```

Không để user profile cuối sidebar bị đẩy ra ngoài màn hình. Profile area nên cố định ở dưới bằng flex layout.

---

## 16. User profile area

### 16.1 Vị trí

Nằm dưới cùng sidebar.

### 16.2 Cấu trúc

```text
[Avatar] Nguyễn Văn A
         Quản trị viên      [⋮]
```

### 16.3 Kích thước

```css
padding: 14px 16px 16px;
border-top: 1px solid rgba(255,255,255,0.12);
```

### 16.4 Avatar

```css
width: 36px;
height: 36px;
border-radius: 999px;
```

### 16.5 Name

```css
font-size: 13px;
font-weight: 600;
color: #FFFFFF;
```

### 16.6 Role

```css
font-size: 12px;
font-weight: 400;
color: rgba(255,255,255,0.64);
```

### 16.7 Menu tài khoản

Menu gồm:

- Hồ sơ cá nhân.
- Đổi mật khẩu.
- Cài đặt giao diện.
- Đăng xuất.

Đăng xuất dùng màu danger trong dropdown, không dùng đỏ trực tiếp trong sidebar.

---

## 17. Help và support item

Có thể hiển thị:

```text
Trợ giúp
Tài liệu hướng dẫn
Liên hệ hỗ trợ
```

Không cần hiển thị cả ba nếu sidebar dài. Có thể gom vào một mục `Trợ giúp`.

---

## 18. Phân quyền menu

Menu được sinh theo quyền truy cập.

Ví dụ:

### Admin

- Xem tất cả module.

### Kinh doanh

- Tổng quan.
- Đơn hàng.
- Khách hàng.
- Sản phẩm.
- Báo cáo cá nhân hoặc chi nhánh.

### Kho

- Tổng quan kho.
- Sản phẩm.
- Kho hàng.
- Đơn chờ xuất.
- Nhà cung cấp.

### Kế toán

- Tổng quan tài chính.
- Đơn hàng.
- Thanh toán.
- Công nợ.
- Báo cáo.

Không hiển thị menu mà người dùng không có quyền truy cập.

Backend vẫn phải kiểm tra quyền độc lập.

---

## 19. Route mapping đề xuất

```text
/dashboard
/orders
/products
/categories
/customers
/suppliers
/inventory
/reports
/marketing
/staff
/roles
/settings
```

Submenu có thể dùng route:

```text
/products/all
/products/create
/inventory/stock
/inventory/imports
/inventory/exports
```

Menu active dựa trên route hiện tại và route con.

Ví dụ `/products/123/edit` vẫn active mục `Sản phẩm`.

---

## 20. Icon mapping đề xuất

Dùng Lucide Icons:

| Menu | Icon |
|---|---|
| Tổng quan | `LayoutDashboard` |
| Đơn hàng | `ShoppingCart` |
| Sản phẩm | `Package` |
| Danh mục | `Boxes` |
| Khách hàng | `Users` |
| Nhà cung cấp | `Building2` |
| Kho hàng | `Warehouse` |
| Báo cáo | `BarChart3` |
| Marketing | `Megaphone` |
| Nhân viên | `UserCog` |
| Phân quyền | `ShieldCheck` |
| Cài đặt | `Settings` |
| Trợ giúp | `CircleHelp` |

Không thay icon ngẫu nhiên giữa các phiên bản.

---

## 21. Keyboard navigation

Yêu cầu:

- Tab di chuyển tuần tự qua các item tương tác.
- Enter hoặc Space kích hoạt menu.
- Arrow Down và Arrow Up có thể dùng trong submenu nếu triển khai dạng menu đầy đủ.
- Escape đóng submenu hoặc flyout.
- Focus phải luôn nhìn thấy.

Không dùng `tabindex` dương.

---

## 22. Accessibility

Sidebar nên dùng semantic HTML:

```html
<aside>
  <nav aria-label="Điều hướng quản trị chính">
    <ul>
      <li><a href="/dashboard">Tổng quan</a></li>
    </ul>
  </nav>
</aside>
```

Menu item active:

```html
aria-current="page"
```

Submenu trigger:

```html
aria-expanded="true"
aria-controls="products-submenu"
```

Collapse button:

```html
aria-pressed="true"
```

---

## 23. Responsive behavior

### 23.1 Viewport ≥ 1280px

- Sidebar mở rộng mặc định.
- Người dùng có thể thu gọn thủ công.

### 23.2 Viewport 1024–1279px

- Sidebar thu gọn mặc định.
- Có thể mở tạm thời dạng expanded overlay nếu cần.

### 23.3 Viewport < 1024px

- Sidebar chuyển thành drawer.
- Drawer mở từ bên trái.
- Có backdrop.
- Escape hoặc click backdrop để đóng.

Drawer width:

```css
width: min(288px, calc(100vw - 40px));
```

---

## 24. Persistence trạng thái

Có thể lưu trạng thái thu gọn/mở rộng theo người dùng.

Ưu tiên:

- Lưu server-side theo profile nếu có.
- Hoặc local preference phía client.

Không lưu trạng thái drawer mobile.

Khi hydration, phải tránh layout shift lớn.

---

## 25. Loading state sidebar

Sidebar không cần skeleton toàn bộ nếu menu tĩnh.

Nếu menu dựa trên phân quyền tải từ API:

- Hiển thị logo ngay.
- Hiển thị 6–8 skeleton item.
- Giữ nguyên chiều rộng sidebar.

Skeleton item:

```css
height: 44px;
margin: 4px 12px;
border-radius: 12px;
background: rgba(255,255,255,0.10);
```

---

## 26. Error state sidebar

Nếu không tải được quyền:

- Hiển thị các mục cơ bản an toàn như Tổng quan và Hồ sơ.
- Hiển thị thông báo nhỏ ở cuối:

```text
Không thể tải đầy đủ menu
[ Thử lại ]
```

Không hiển thị toàn bộ menu mặc định khi chưa xác thực quyền.

---

## 27. Animation

### 27.1 Collapse / expand

```css
duration: 200ms;
easing: cubic-bezier(0.2, 0, 0, 1);
```

Animate:

- Width sidebar.
- Opacity label.
- Gap giữa icon và label.

Không animate toàn bộ nội dung bằng scale.

### 27.2 Submenu

```css
duration: 180ms;
```

### 27.3 Drawer mobile

```css
duration: 220ms;
transform: translateX(-100%) → translateX(0);
```

---

## 28. CSS tham chiếu

```css
.sidebar {
  position: sticky;
  top: 0;
  height: 100dvh;
  display: flex;
  flex-direction: column;
  width: var(--sidebar-width-expanded);
  background: linear-gradient(180deg, #0B57F0 0%, #0A48C8 100%);
  color: #FFFFFF;
  transition: width 200ms cubic-bezier(0.2, 0, 0, 1);
}

.sidebar--collapsed {
  width: var(--sidebar-width-collapsed);
}

.sidebar-brand {
  height: 72px;
  display: flex;
  align-items: center;
  gap: 12px;
  padding-inline: 20px;
}

.sidebar-nav {
  flex: 1;
  min-height: 0;
  overflow-y: auto;
  padding-bottom: 16px;
}

.sidebar-item {
  display: flex;
  align-items: center;
  gap: 12px;
  height: 44px;
  margin: 2px 12px;
  padding-inline: 12px;
  border-radius: 12px;
  color: rgba(255,255,255,0.76);
}

.sidebar-item:hover {
  color: #FFFFFF;
  background: rgba(255,255,255,0.10);
}

.sidebar-item[aria-current="page"] {
  color: #FFFFFF;
  background: rgba(255,255,255,0.16);
  font-weight: 600;
}
```

---

## 29. Component tree đề xuất

```text
Sidebar
├── SidebarBrand
│   ├── BrandLogo
│   ├── BrandText
│   └── CollapseButton
├── BranchSelector
├── SidebarNavigation
│   ├── NavigationGroup
│   │   ├── GroupLabel
│   │   └── NavigationItem
│   │       ├── Icon
│   │       ├── Label
│   │       ├── Badge
│   │       └── Chevron
│   └── Submenu
├── SidebarSupport
└── SidebarUser
    ├── Avatar
    ├── UserMeta
    └── UserMenuButton
```

---

## 30. Props tham khảo

```ts
export type SidebarItem = {
  id: string;
  label: string;
  href?: string;
  icon: React.ComponentType;
  badge?: number | string;
  requiredPermissions?: string[];
  children?: SidebarItem[];
};

export type SidebarProps = {
  items: SidebarItem[];
  collapsed: boolean;
  activePath: string;
  onCollapsedChange: (collapsed: boolean) => void;
};
```

---

## 31. Acceptance checklist

- [ ] Sidebar rộng 264px khi mở rộng.
- [ ] Sidebar rộng 80px khi thu gọn.
- [ ] Active item dễ nhận biết.
- [ ] Hover không quá chói.
- [ ] Icon đồng nhất một bộ.
- [ ] Label không bị xuống dòng.
- [ ] Tooltip hoạt động khi thu gọn.
- [ ] Submenu mở và đóng bằng bàn phím.
- [ ] Menu hiển thị đúng theo phân quyền.
- [ ] User profile luôn truy cập được.
- [ ] Navigation area cuộn độc lập khi dài.
- [ ] Sidebar không che main content.
- [ ] Drawer hoạt động dưới 1024px.
- [ ] Focus ring rõ ràng.
- [ ] Badge không vượt quá `99+`.
- [ ] Route con vẫn active menu cha đúng.
- [ ] Collapse state không gây layout shift lớn.

---

## 32. Những điều không được làm

- Không dùng icon màu khác nhau cho từng menu.
- Không đặt quá nhiều menu cấp 1.
- Không dùng accordion lồng quá 2 cấp.
- Không để active state chỉ khác màu chữ rất nhẹ.
- Không giấu nhãn menu ở trạng thái mở rộng.
- Không dùng tooltip thay cho label ở sidebar mở rộng.
- Không cho phép menu item dài xuống 2 dòng.
- Không hiển thị menu người dùng không có quyền.
- Không tạo nhiều vùng cuộn dọc trong sidebar.

---

## 33. Kết luận

Sidebar của Cynca VLXD phải hoạt động như một hệ thống điều hướng ổn định, dễ học và có khả năng mở rộng. Mọi trạng thái từ mở rộng, thu gọn, submenu, phân quyền đến responsive đều phải được xử lý nhất quán để không làm gián đoạn công việc quản trị.

File tiếp theo:

```text
04-Header.md
```