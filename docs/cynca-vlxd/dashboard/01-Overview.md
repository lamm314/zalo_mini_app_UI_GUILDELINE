# Cynca VLXD Admin Dashboard — Tổng quan thiết kế

> Phiên bản: 1.0  
> Phạm vi: Dashboard quản trị dành cho hệ thống Cynca VLXD  
> Nền tảng mục tiêu: Desktop Web Admin  
> Trạng thái: Design Specification

---

## 1. Mục đích tài liệu

Tài liệu này định nghĩa định hướng thiết kế tổng thể cho trang quản trị Cynca VLXD. Đây là nền tảng chung để các file tiếp theo mô tả chi tiết layout, sidebar, header, KPI, biểu đồ, bảng dữ liệu, trạng thái tương tác, responsive và component.

Mục tiêu là giúp designer, frontend developer, Codex, Cursor hoặc các công cụ AI có thể triển khai giao diện đồng nhất mà không phải tự suy đoán phong cách.

---

## 2. Vai trò của Dashboard

Dashboard là màn hình đầu tiên sau khi người quản trị đăng nhập. Màn hình phải trả lời nhanh các câu hỏi sau:

1. Hôm nay doanh thu bao nhiêu?
2. Có bao nhiêu đơn hàng mới?
3. Đơn nào đang chờ xử lý?
4. Sản phẩm nào bán tốt?
5. Sản phẩm nào sắp hết hàng?
6. Tỷ lệ đơn hoàn thành và hủy là bao nhiêu?
7. Có thông báo quan trọng nào cần xử lý ngay?
8. Người quản trị nên thực hiện hành động tiếp theo nào?

Dashboard không chỉ là màn hình hiển thị số liệu. Nó phải là trung tâm điều hành hoạt động bán hàng, tồn kho, khách hàng và vận hành của Cynca VLXD.

---

## 3. Đối tượng sử dụng

### 3.1 Quản trị viên hệ thống

- Xem toàn bộ số liệu.
- Quản lý tài khoản và phân quyền.
- Theo dõi trạng thái hệ thống.
- Truy cập toàn bộ module.

### 3.2 Nhân viên kinh doanh

- Theo dõi đơn hàng.
- Xem doanh thu cá nhân hoặc chi nhánh.
- Quản lý khách hàng.
- Kiểm tra sản phẩm và tồn kho.

### 3.3 Nhân viên kho

- Theo dõi cảnh báo tồn kho.
- Kiểm tra hàng chờ xuất.
- Xử lý trạng thái giao hàng.
- Xem biến động nhập, xuất và tồn.

### 3.4 Kế toán hoặc quản lý tài chính

- Xem doanh thu.
- Theo dõi công nợ.
- Kiểm tra trạng thái thanh toán.
- Xuất báo cáo.

### 3.5 Ban lãnh đạo

- Xem KPI tổng hợp.
- Theo dõi xu hướng tăng trưởng.
- So sánh kỳ hiện tại với kỳ trước.
- Phát hiện vấn đề cần xử lý.

---

## 4. Định hướng thẩm mỹ

Dashboard phải mang cảm giác:

- Hiện đại.
- Chuyên nghiệp.
- Tin cậy.
- Cao cấp nhưng không phô trương.
- Dễ đọc số liệu.
- Tối ưu cho hoạt động quản trị.
- Phù hợp lĩnh vực vật liệu xây dựng.

Phong cách tham chiếu:

- Stripe Dashboard.
- Shopify Admin.
- Linear.
- Vercel Dashboard.
- Notion Analytics.

Không sao chép trực tiếp bất kỳ sản phẩm nào. Chỉ áp dụng tinh thần về bố cục, tính rõ ràng, khoảng trắng, hệ thống phân cấp và sự tinh gọn.

---

## 5. Nhận diện thương hiệu

Tên hiển thị chính thức:

```text
Cynca VLXD
```

Tên mở rộng:

```text
Cynca Vật Liệu Xây Dựng
```

Màu chủ đạo là xanh dương, đồng bộ với giao diện Zalo Mini App phía khách hàng.

### 5.1 Màu thương hiệu chính

```css
--brand-primary: #0B57F0;
--brand-primary-hover: #0847C7;
--brand-primary-active: #063CA8;
--brand-primary-soft: #EAF1FF;
--brand-primary-subtle: #F4F7FF;
```

### 5.2 Màu nền

```css
--background-page: #F6F8FC;
--background-surface: #FFFFFF;
--background-muted: #F1F4F9;
```

### 5.3 Màu văn bản

```css
--text-primary: #182230;
--text-secondary: #526071;
--text-muted: #7B8797;
--text-inverse: #FFFFFF;
```

### 5.4 Màu trạng thái

```css
--success: #12B76A;
--success-soft: #ECFDF3;
--warning: #F79009;
--warning-soft: #FFFAEB;
--danger: #F04438;
--danger-soft: #FEF3F2;
--info: #2E90FA;
--info-soft: #EFF8FF;
```

---

## 6. Nguyên tắc UX cốt lõi

### 6.1 Ưu tiên thông tin quan trọng

Thông tin cần xử lý ngay phải xuất hiện trước:

- Đơn hàng mới.
- Đơn chờ xác nhận.
- Cảnh báo sắp hết hàng.
- Công nợ quá hạn.
- Lỗi đồng bộ.

Các số liệu mang tính tham khảo được đặt ở vị trí thứ cấp.

### 6.2 Không làm người dùng bị ngợp

Không hiển thị quá nhiều biểu đồ trên cùng một màn hình. Mỗi khu vực phải có một mục tiêu rõ ràng.

Khuyến nghị:

- 4 KPI chính ở hàng đầu.
- 2 biểu đồ chính ở hàng thứ hai.
- 1 bảng đơn hàng lớn.
- 1 hoặc 2 widget hỗ trợ.

### 6.3 Mọi số liệu phải có ngữ cảnh

Không hiển thị một con số đơn độc nếu người dùng không hiểu ý nghĩa.

Ví dụ tốt:

```text
1,28 tỷ đồng
+12,5% so với tháng trước
```

Ví dụ không tốt:

```text
1,28 tỷ
```

### 6.4 Hành động phải gần dữ liệu

Mỗi khu vực nên có hành động liên quan:

- KPI đơn hàng → Xem tất cả đơn hàng.
- Tồn kho thấp → Cập nhật tồn kho.
- Công nợ → Xem chi tiết công nợ.
- Báo cáo doanh thu → Xuất báo cáo.

### 6.5 Trạng thái phải rõ ràng

Không chỉ dùng màu để biểu thị trạng thái. Luôn kết hợp:

- Màu.
- Nhãn văn bản.
- Icon khi cần.

Ví dụ:

```text
● Đang giao
✓ Hoàn thành
! Chờ xác nhận
× Đã hủy
```

### 6.6 Giảm số lần nhấp

Các thao tác thường xuyên phải hoàn thành trong tối đa 2 đến 3 lần nhấp:

- Tạo sản phẩm.
- Xác nhận đơn.
- Cập nhật tồn kho.
- Xuất báo cáo.
- Xem chi tiết khách hàng.

---

## 7. Mục tiêu kinh doanh được phản ánh trên giao diện

Dashboard phải hỗ trợ các mục tiêu sau:

1. Tăng tốc độ xử lý đơn hàng.
2. Giảm tình trạng thiếu hàng.
3. Tăng khả năng theo dõi doanh thu.
4. Phát hiện sản phẩm bán chậm.
5. Giảm đơn hủy.
6. Theo dõi hiệu suất nhân viên hoặc chi nhánh.
7. Tăng tính minh bạch trong quản trị.
8. Hỗ trợ ra quyết định dựa trên dữ liệu.

---

## 8. Cấu trúc nội dung tổng thể

Dashboard được chia thành 6 vùng chính.

```text
┌──────────────────────────────────────────────────────────────┐
│ Sidebar │ Header                                             │
│         ├────────────────────────────────────────────────────┤
│         │ Page title + filter + quick actions                │
│         ├────────────────────────────────────────────────────┤
│         │ KPI cards                                          │
│         ├───────────────────────────┬────────────────────────┤
│         │ Revenue chart             │ Order status           │
│         ├───────────────────────────┼────────────────────────┤
│         │ Latest orders             │ Inventory / alerts     │
│         ├───────────────────────────┴────────────────────────┤
│         │ Best products / customers / reports                │
└──────────────────────────────────────────────────────────────┘
```

### 8.1 Sidebar

Dùng để điều hướng giữa các module quản trị.

### 8.2 Header

Chứa tìm kiếm, bộ lọc ngày, thông báo và tài khoản.

### 8.3 Page toolbar

Chứa tiêu đề trang, mô tả ngắn và hành động chính.

### 8.4 KPI area

Chứa 4 chỉ số quan trọng nhất.

### 8.5 Analytics area

Chứa biểu đồ doanh thu và trạng thái đơn hàng.

### 8.6 Operational area

Chứa đơn mới, cảnh báo tồn kho, sản phẩm bán chạy và thông báo.

---

## 9. KPI mặc định

Bốn KPI ưu tiên:

1. Doanh thu.
2. Đơn hàng.
3. Khách hàng.
4. Lợi nhuận gộp.

Tùy phân quyền, KPI thứ tư có thể thay bằng:

- Giá trị tồn kho.
- Công nợ phải thu.
- Sản phẩm sắp hết hàng.
- Tỷ lệ hoàn thành đơn.

Mỗi KPI phải gồm:

- Nhãn.
- Giá trị hiện tại.
- So sánh với kỳ trước.
- Icon.
- Sparkline hoặc tín hiệu xu hướng khi cần.
- Tooltip giải thích cách tính.

---

## 10. Quy tắc phân cấp thị giác

Mức độ ưu tiên từ cao đến thấp:

### Cấp 1 — Hành động khẩn cấp

- Cảnh báo lỗi.
- Tồn kho bằng 0.
- Công nợ quá hạn.
- Đơn chờ xử lý lâu.

### Cấp 2 — Số liệu chính

- Doanh thu.
- Đơn hàng.
- Khách hàng.
- Lợi nhuận.

### Cấp 3 — Xu hướng

- Biểu đồ theo thời gian.
- So sánh kỳ.
- Tỷ lệ chuyển đổi.

### Cấp 4 — Thông tin hỗ trợ

- Danh mục sản phẩm.
- Sản phẩm bán chạy.
- Thông báo hệ thống.

---

## 11. Hệ thống khoảng cách

Dashboard áp dụng hệ thống 8pt Grid.

```css
--space-1: 4px;
--space-2: 8px;
--space-3: 12px;
--space-4: 16px;
--space-5: 20px;
--space-6: 24px;
--space-8: 32px;
--space-10: 40px;
--space-12: 48px;
```

Quy tắc:

- Khoảng cách giữa các card: 24px.
- Padding nội dung chính: 32px.
- Padding trong card: 24px.
- Khoảng cách nhãn và giá trị: 8px.
- Khoảng cách tiêu đề và nội dung: 16px.

---

## 12. Bo góc

```css
--radius-sm: 8px;
--radius-md: 12px;
--radius-lg: 16px;
--radius-xl: 20px;
--radius-2xl: 24px;
```

Quy tắc sử dụng:

- Input: 12px.
- Button: 12px.
- Badge: 999px.
- Card nhỏ: 16px.
- Card dashboard chính: 20px hoặc 24px.
- Modal: 24px.

Không sử dụng quá nhiều mức bo góc trong cùng một màn hình.

---

## 13. Bóng đổ

Bóng đổ phải nhẹ, không tạo cảm giác nổi quá mức.

```css
--shadow-xs: 0 1px 2px rgba(16, 24, 40, 0.04);
--shadow-sm: 0 4px 12px rgba(16, 24, 40, 0.05);
--shadow-md: 0 8px 24px rgba(16, 24, 40, 0.07);
```

Quy tắc:

- Card mặc định: dùng border mảnh hoặc shadow-xs.
- Card hover: shadow-sm.
- Dropdown và popover: shadow-md.
- Không dùng glow.
- Không dùng bóng đen đậm.

---

## 14. Typography tổng thể

Font ưu tiên:

```text
Inter
```

Fallback:

```css
font-family: Inter, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
```

Kích thước khuyến nghị:

| Loại | Kích thước | Độ đậm | Mục đích |
|---|---:|---:|---|
| Page title | 30px | 700 | Tiêu đề trang |
| Section title | 20px | 600 | Tiêu đề khu vực |
| Card title | 16px | 600 | Tiêu đề card |
| KPI value | 30–36px | 700 | Giá trị chính |
| Body | 14–16px | 400–500 | Nội dung |
| Caption | 12–13px | 400 | Chú thích |
| Button | 14px | 600 | Nhãn nút |

Không sử dụng quá nhiều chữ in đậm. Chỉ dùng trọng lượng 600 hoặc 700 cho thông tin quan trọng.

---

## 15. Iconography

Bộ icon đề xuất:

- Lucide Icons.
- Heroicons Outline.

Quy tắc:

- Chỉ dùng một bộ icon trong cùng sản phẩm.
- Kích thước thông dụng: 16px, 18px, 20px, 24px.
- Stroke mặc định: 1.75 hoặc 2.
- Icon trong sidebar: 20px.
- Icon trong KPI: 22px hoặc 24px.
- Icon không được thay thế nhãn văn bản ở thao tác quan trọng.

---

## 16. Chuyển động tổng thể

Motion phải tinh tế, nhanh và hỗ trợ cảm nhận trạng thái.

```css
--duration-fast: 120ms;
--duration-normal: 180ms;
--duration-slow: 280ms;
--ease-standard: cubic-bezier(0.2, 0, 0, 1);
```

Khuyến nghị:

- Hover button: 120ms.
- Hover card: 180ms.
- Dropdown: 180ms.
- Modal: 220–280ms.
- Chart draw animation: 500–700ms.

Không dùng animation liên tục gây mất tập trung.

---

## 17. Trạng thái giao diện bắt buộc

Mỗi component phải có các trạng thái:

- Default.
- Hover.
- Focus.
- Active.
- Disabled.
- Loading.
- Empty.
- Error.

Ví dụ button:

```text
Default  → nền xanh
Hover    → xanh đậm hơn
Active   → giảm sáng nhẹ
Focus    → có focus ring
Disabled → giảm opacity, không click
Loading  → spinner + giữ nguyên chiều rộng
```

---

## 18. Empty State

Mỗi khu vực dữ liệu phải có empty state riêng.

Ví dụ danh sách đơn hàng trống:

```text
Chưa có đơn hàng nào
Các đơn hàng mới sẽ xuất hiện tại đây.
[ Tạo đơn hàng ]
```

Empty state phải gồm:

- Icon hoặc illustration nhẹ.
- Tiêu đề ngắn.
- Mô tả.
- Hành động tiếp theo khi phù hợp.

---

## 19. Loading State

Không để màn hình trắng trong quá trình tải dữ liệu.

Sử dụng:

- Skeleton cho KPI.
- Skeleton dòng cho bảng.
- Placeholder cho biểu đồ.
- Spinner cho thao tác nhỏ.

Không dùng spinner toàn màn hình nếu chỉ một khu vực đang tải.

---

## 20. Error State

Thông báo lỗi phải nói rõ:

1. Điều gì đã xảy ra.
2. Người dùng có thể làm gì.
3. Có thể thử lại hay không.

Ví dụ:

```text
Không thể tải dữ liệu doanh thu
Kết nối đến máy chủ đang gián đoạn.
[ Thử lại ]
```

Không hiển thị mã lỗi kỹ thuật trực tiếp cho người dùng thông thường.

---

## 21. Accessibility

Yêu cầu tối thiểu:

- Tỷ lệ tương phản văn bản đạt WCAG AA.
- Có focus ring rõ ràng.
- Điều hướng bằng bàn phím.
- Không chỉ dùng màu để biểu thị trạng thái.
- Icon button phải có aria-label.
- Tooltip không chứa thông tin bắt buộc duy nhất.
- Bảng dữ liệu có header rõ ràng.
- Biểu đồ có phần tóm tắt bằng văn bản.

Focus ring đề xuất:

```css
outline: 3px solid rgba(11, 87, 240, 0.24);
outline-offset: 2px;
```

---

## 22. Nguyên tắc dữ liệu

- Số tiền dùng định dạng Việt Nam.
- Ngày dùng định dạng `dd/mm/yyyy`.
- Thời gian dùng định dạng 24 giờ.
- Số lớn phải có dấu phân tách hàng nghìn.
- Đơn vị phải nhất quán.
- Không tự làm tròn nếu ảnh hưởng đến nghiệp vụ kế toán.

Ví dụ:

```text
1.280.000.000 ₫
06/08/2026
16:30
```

---

## 23. Phân quyền hiển thị

Dashboard phải thay đổi theo vai trò.

Ví dụ:

- Nhân viên kho không thấy lợi nhuận.
- Nhân viên kinh doanh chỉ thấy dữ liệu được phân công.
- Quản lý chi nhánh chỉ thấy chi nhánh của mình.
- Admin thấy toàn hệ thống.

Không chỉ ẩn nút ở frontend. Quyền truy cập phải được kiểm soát cả ở backend.

---

## 24. Phạm vi responsive

Dashboard ưu tiên desktop.

Breakpoints đề xuất:

```css
--breakpoint-xl: 1440px;
--breakpoint-lg: 1280px;
--breakpoint-md: 1024px;
```

Nguyên tắc:

- Từ 1440px trở lên: hiển thị đầy đủ.
- Từ 1280px: giảm khoảng cách, giữ sidebar.
- Từ 1024px: sidebar thu gọn dạng icon.
- Dưới 1024px: không ưu tiên; có thể dùng tablet mode rút gọn.
- Mobile không phải nền tảng chính của trang quản trị.

---

## 25. Những điều không được làm

- Không dùng gradient quá mạnh trên toàn bộ giao diện.
- Không dùng card với quá nhiều màu khác nhau.
- Không dùng icon 3D hoặc emoji làm icon chính.
- Không dùng shadow đậm.
- Không dùng font trang trí.
- Không đưa quá nhiều KPI vào hàng đầu.
- Không hiển thị biểu đồ không có nhãn và tooltip.
- Không dùng bảng quá dày, thiếu khoảng thở.
- Không dùng modal cho mọi thao tác.
- Không dùng màu đỏ cho thông tin không khẩn cấp.

---

## 26. Tiêu chí hoàn thành

Dashboard được xem là đạt yêu cầu khi:

- Người dùng hiểu tình trạng kinh doanh trong 10 giây đầu.
- Các KPI chính đọc được ngay.
- Hành động khẩn cấp nổi bật nhưng không gây căng thẳng.
- Bố cục nhất quán ở 1280px và 1440px.
- Không có khu vực dữ liệu nào thiếu loading, empty hoặc error state.
- Mọi thành phần có trạng thái hover và focus.
- Màu sắc, typography và spacing dùng đúng token.
- Người dùng hoàn thành tác vụ chính trong tối đa 3 lần nhấp.

---

## 27. Danh sách tài liệu tiếp theo

Các phần tiếp theo của bộ Dashboard Specification:

1. `02-Layout.md`
2. `03-Sidebar.md`
3. `04-Header.md`
4. `05-KPI-Cards.md`
5. `06-Charts.md`
6. `07-Product-Widgets.md`
7. `08-Orders-Table.md`
8. `09-Notification.md`
9. `10-Animation.md`
10. `11-Responsive.md`
11. `12-Accessibility.md`
12. `13-Tailwind-Tokens.md`

---

## 28. Kết luận

Cynca VLXD Admin Dashboard phải tạo cảm giác của một hệ thống quản trị hiện đại, đáng tin cậy và dễ vận hành. Thiết kế phải ưu tiên tính rõ ràng, tốc độ xử lý và khả năng ra quyết định thay vì chỉ tập trung vào yếu tố trang trí.

Mọi file chi tiết phía sau phải tuân thủ các nguyên tắc được xác lập trong tài liệu này.