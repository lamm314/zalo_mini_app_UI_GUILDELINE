# Cynca VLXD Admin Dashboard — Animation & Motion Specification

> Phiên bản: 1.0  
> Phạm vi: Chuyển động, phản hồi tương tác và trạng thái động trên Dashboard quản trị  
> Nền tảng mục tiêu: Desktop Web Admin  
> Tài liệu liên quan: `01-Overview.md`, `02-Layout.md`, `03-Sidebar.md`, `04-Header.md`, `06-Charts.md`, `09-Notification.md`

---

## 1. Mục tiêu của motion system

Motion trong Cynca VLXD Dashboard phải hỗ trợ trải nghiệm sử dụng, không phải để trang trí.

Mỗi animation cần phục vụ ít nhất một mục tiêu:

- Giúp người dùng hiểu thành phần vừa thay đổi.
- Làm rõ quan hệ giữa trigger và nội dung được mở.
- Tạo phản hồi tức thời sau thao tác.
- Giảm cảm giác giật khi layout thay đổi.
- Dẫn sự chú ý đến trạng thái quan trọng.
- Giúp giao diện cảm giác nhanh, ổn định và chuyên nghiệp.

Animation không được làm chậm thao tác quản trị hoặc khiến người dùng mất tập trung khi đọc dữ liệu.

---

## 2. Nguyên tắc motion cốt lõi

### 2.1 Nhanh hơn đẹp

Dashboard là công cụ vận hành. Thời lượng chuyển động phải ngắn và dứt khoát.

### 2.2 Có nguyên nhân và kết quả

Nội dung xuất hiện gần vị trí trigger để người dùng hiểu mối liên hệ.

Ví dụ:

- Dropdown xuất hiện từ button.
- Drawer trượt vào từ bên phải.
- Submenu mở từ menu cha.

### 2.3 Không chuyển động đồng loạt quá nhiều

Không animate tất cả card, bảng, icon và số liệu cùng lúc sau mỗi filter change.

### 2.4 Giữ tính liên tục

Khi dữ liệu cập nhật, ưu tiên chuyển đổi giá trị tại chỗ thay vì xóa toàn bộ nội dung rồi render lại.

### 2.5 Tôn trọng reduced motion

Người dùng bật `prefers-reduced-motion` phải nhận được trải nghiệm ít chuyển động hơn.

---

## 3. Motion tokens

### 3.1 Duration tokens

```css
:root {
  --motion-duration-instant: 80ms;
  --motion-duration-fast: 120ms;
  --motion-duration-normal: 180ms;
  --motion-duration-slow: 260ms;
  --motion-duration-emphasis: 420ms;
}
```

### 3.2 Easing tokens

```css
:root {
  --motion-ease-standard: cubic-bezier(0.2, 0, 0, 1);
  --motion-ease-enter: cubic-bezier(0, 0, 0, 1);
  --motion-ease-exit: cubic-bezier(0.4, 0, 1, 1);
  --motion-ease-emphasis: cubic-bezier(0.2, 0.8, 0.2, 1);
}
```

### 3.3 Distance tokens

```css
:root {
  --motion-distance-xs: 2px;
  --motion-distance-sm: 4px;
  --motion-distance-md: 8px;
  --motion-distance-lg: 16px;
  --motion-distance-xl: 24px;
}
```

Không tạo duration và easing riêng ngẫu nhiên cho từng component.

---

## 4. Bảng thời lượng chuẩn

| Tương tác | Duration | Easing |
|---|---:|---|
| Hover button | 120ms | Standard |
| Hover card | 160–180ms | Standard |
| Focus ring | 80–120ms | Standard |
| Dropdown mở | 160–180ms | Enter |
| Dropdown đóng | 120–150ms | Exit |
| Tooltip | 120ms | Enter |
| Sidebar collapse | 200–220ms | Standard |
| Submenu | 160–180ms | Standard |
| Modal mở | 220–260ms | Enter |
| Modal đóng | 180–220ms | Exit |
| Drawer mở | 220–280ms | Standard |
| Toast xuất hiện | 180–220ms | Enter |
| Chart initial draw | 500–700ms | Emphasis |
| Chart update | 250–400ms | Standard |
| Skeleton shimmer | 1.4–1.8s loop | Linear |

---

## 5. Button interaction

### 5.1 Hover

```css
.button {
  transition:
    background-color var(--motion-duration-fast) var(--motion-ease-standard),
    border-color var(--motion-duration-fast) var(--motion-ease-standard),
    color var(--motion-duration-fast) var(--motion-ease-standard),
    box-shadow var(--motion-duration-fast) var(--motion-ease-standard),
    transform var(--motion-duration-fast) var(--motion-ease-standard);
}
```

Có thể dùng:

```css
transform: translateY(-1px);
```

chỉ với primary button hoặc card action quan trọng.

### 5.2 Active

```css
transform: translateY(0) scale(0.99);
```

Không scale nhỏ hơn `0.98`.

### 5.3 Loading

Khi loading:

- Giữ nguyên chiều rộng nút.
- Hiển thị spinner nhỏ.
- Giữ label hoặc thay label rõ ràng.
- Disable click lặp.

Ví dụ:

```text
Đang lưu...
```

### 5.4 Success feedback

Sau thao tác nhanh, icon có thể chuyển từ spinner sang check trong 180ms trước khi đóng trạng thái.

Không dùng hiệu ứng confetti trong Dashboard quản trị.

---

## 6. Card interaction

Card chỉ animate khi thực sự click được.

Hover:

```css
.interactive-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(16,24,40,0.07);
}
```

Duration:

```css
160ms
```

Không dùng:

- Scale lớn.
- Xoay.
- Parallax.
- Glow mạnh.

Card dữ liệu tĩnh không cần hover lift.

---

## 7. Sidebar collapse và expand

### 7.1 Thành phần được animate

- Width sidebar.
- Opacity label.
- Gap icon và label.
- Main content grid column.
- Chevron hoặc collapse icon.

### 7.2 Thời lượng

```css
200ms var(--motion-ease-standard)
```

### 7.3 Trình tự

Khi collapse:

1. Label giảm opacity nhanh.
2. Sidebar giảm width.
3. Icon căn giữa.

Khi expand:

1. Sidebar tăng width.
2. Label xuất hiện sau khoảng 40–60ms.

### 7.4 CSS tham chiếu

```css
.sidebar {
  transition: width 200ms var(--motion-ease-standard);
}

.sidebar-label {
  transition:
    opacity 120ms var(--motion-ease-standard),
    transform 180ms var(--motion-ease-standard);
}

.sidebar--collapsed .sidebar-label {
  opacity: 0;
  transform: translateX(-4px);
  pointer-events: none;
}
```

Không để text wrap trong quá trình chuyển đổi.

---

## 8. Submenu animation

Submenu mở bằng:

- Opacity.
- Height hoặc grid rows.
- TranslateY rất nhỏ.

Khuyến nghị:

```css
.submenu {
  display: grid;
  grid-template-rows: 0fr;
  opacity: 0;
  transition:
    grid-template-rows 180ms var(--motion-ease-standard),
    opacity 140ms var(--motion-ease-standard);
}

.submenu[data-open="true"] {
  grid-template-rows: 1fr;
  opacity: 1;
}
```

Không animate `height: auto` bằng JavaScript nếu có thể tránh.

---

## 9. Header và sticky state

Header không cần animate vị trí khi scroll.

Chỉ thay đổi nhẹ:

- Border.
- Shadow.
- Background opacity.

```css
.dashboard-header {
  transition:
    box-shadow 140ms var(--motion-ease-standard),
    background-color 140ms var(--motion-ease-standard);
}
```

Không làm header thu nhỏ hoặc ẩn khi cuộn trong Dashboard desktop.

---

## 10. Dropdown và popover

### 10.1 Enter

```css
opacity: 0 → 1;
transform: translateY(-4px) scale(0.98) → translateY(0) scale(1);
duration: 160–180ms;
```

### 10.2 Exit

```css
opacity: 1 → 0;
transform: translateY(0) scale(1) → translateY(-2px) scale(0.99);
duration: 120–150ms;
```

### 10.3 Transform origin

Transform origin phải gần trigger:

- Dropdown phải: `top right`.
- Dropdown trái: `top left`.
- Context menu: vị trí click.

### 10.4 Focus

Focus được chuyển vào nội dung sau khi animation bắt đầu nhưng không cần chờ animation kết thúc hoàn toàn.

---

## 11. Tooltip

Tooltip delay:

```text
Mouse hover: 350–500ms
Keyboard focus: 0–100ms
```

Animation:

```css
opacity: 0 → 1;
transform: translateY(2px) → translateY(0);
duration: 120ms;
```

Không animate tooltip lâu vì đây là nội dung hỗ trợ nhanh.

---

## 12. Modal animation

### 12.1 Backdrop

```css
opacity: 0 → 1;
duration: 180–220ms;
```

### 12.2 Modal panel

```css
opacity: 0 → 1;
transform: translateY(12px) scale(0.98) → translateY(0) scale(1);
duration: 220–260ms;
```

### 12.3 Exit

Exit nhanh hơn enter khoảng 20–40ms.

### 12.4 Focus management

- Focus chuyển vào modal ngay khi mở.
- Focus trap hoạt động trong modal.
- Khi đóng, focus trả về trigger.

Không để animation trì hoãn focus quá lâu.

---

## 13. Drawer animation

Drawer bên phải:

```css
transform: translateX(100%) → translateX(0);
duration: 240–280ms;
```

Backdrop:

```css
opacity: 0 → 1;
duration: 180–220ms;
```

Khi đóng:

```css
duration: 200–240ms;
```

Không dùng bounce hoặc overshoot cho drawer nghiệp vụ.

---

## 14. Toast animation

Toast xuất hiện:

```css
opacity: 0 → 1;
transform: translateX(16px) → translateX(0);
duration: 180–220ms;
```

Toast biến mất:

```css
opacity: 1 → 0;
transform: translateX(0) → translateX(12px);
duration: 140–180ms;
```

Khi nhiều toast:

- Item cũ dịch chuyển mượt bằng layout animation.
- Không làm toàn stack nhảy đột ngột.

Không animate toast từ dưới lên ở desktop nếu vị trí là góc trên bên phải.

---

## 15. Notification item animation

Khi notification mới xuất hiện:

- Chèn đầu danh sách.
- Fade in.
- TranslateY tối đa 8px.

```css
duration: 200ms;
```

Không nhấp nháy badge liên tục.

Badge có thể dùng scale nhẹ một lần:

```css
scale: 0.9 → 1;
duration: 160ms;
```

---

## 16. Table row update

Khi row thay đổi trạng thái:

- Badge đổi màu trong 160ms.
- Có thể dùng background highlight rất nhẹ trong 600–900ms.

Ví dụ:

```css
@keyframes row-updated {
  0% { background: #EAF1FF; }
  100% { background: transparent; }
}
```

Không flash màu đỏ hoặc xanh quá mạnh.

Khi row bị xóa:

- Fade out.
- Collapse height trong 180–240ms.

Chỉ dùng nếu danh sách đang ở trạng thái tương tác trực tiếp.

---

## 17. Filter và data refresh

Khi filter thay đổi:

- Không xóa toàn bộ layout ngay lập tức.
- Giữ dữ liệu cũ mờ nhẹ hoặc hiển thị skeleton trong vùng thay đổi.
- Có progress indicator nhỏ nếu tải lâu hơn 300ms.

Khuyến nghị:

```css
opacity: 1 → 0.55;
duration: 120ms;
```

sau đó cập nhật dữ liệu và trở về opacity 1.

Không dùng page-level spinner cho thay đổi filter cục bộ.

---

## 18. KPI value transition

Khi giá trị KPI thay đổi:

- Không bắt buộc count-up.
- Ưu tiên crossfade hoặc cập nhật tức thời.

Nếu dùng count-up:

- Chỉ áp dụng lần tải đầu.
- Duration tối đa 500ms.
- Không count từ 0 cho số rất lớn nếu gây chậm cảm nhận.
- Tắt khi reduced motion.

Trend badge có thể fade và slide 2px.

---

## 19. Chart animation

### 19.1 Initial render

Line chart:

- Draw path 500–700ms.
- Area fade 300–450ms.

Bar chart:

- Bar grow từ baseline.
- Duration 400–600ms.

Donut chart:

- Arc reveal 450–650ms.

### 19.2 Data update

- Interpolate path hoặc bar trong 250–400ms.
- Không reset animation toàn chart nếu chỉ thay một điểm.

### 19.3 Hover

- Point radius tăng nhẹ.
- Tooltip xuất hiện nhanh.
- Không làm line thay đổi độ dày toàn bộ.

### 19.4 Reduced motion

- Render chart ngay.
- Chỉ giữ tooltip transition rất ngắn.

---

## 20. Skeleton loading

Skeleton shimmer:

```css
@keyframes skeleton-shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

```css
.skeleton {
  background: linear-gradient(
    90deg,
    #F2F4F7 25%,
    #EAECF0 37%,
    #F2F4F7 63%
  );
  background-size: 400% 100%;
  animation: skeleton-shimmer 1.6s linear infinite;
}
```

Reduced motion:

```css
animation: none;
background: #EEF1F5;
```

Không dùng shimmer quá tương phản.

---

## 21. Spinner

Spinner dùng cho:

- Button loading.
- Tác vụ nhỏ.
- Khu vực không phù hợp skeleton.

Kích thước:

```css
--spinner-sm: 16px;
--spinner-md: 20px;
--spinner-lg: 28px;
```

Duration vòng quay:

```css
700–900ms linear infinite
```

Không dùng spinner toàn màn hình cho tải Dashboard thông thường.

---

## 22. Progress indicator

Tác vụ dài như xuất báo cáo hoặc import dữ liệu cần progress rõ ràng.

Có thể dùng:

- Indeterminate bar khi chưa biết phần trăm.
- Determinate bar khi có tiến độ.

Transition value:

```css
width 220ms var(--motion-ease-standard)
```

Không để progress nhảy lùi nếu backend không yêu cầu.

---

## 23. Success và error feedback

### Success

- Check icon fade/scale nhẹ.
- Toast xuất hiện.
- Row hoặc card cập nhật tại chỗ.

### Error

- Không shake toàn modal hoặc page.
- Field lỗi có thể dùng một chuyển động rất nhỏ 2–3px nếu cần, nhưng không bắt buộc.
- Ưu tiên border, message và focus.

Không dùng rung mạnh hoặc flash liên tục.

---

## 24. Drag and drop — nếu có

Nếu Dashboard hỗ trợ sắp xếp widget:

- Item được nâng bằng shadow nhẹ.
- Placeholder giữ nguyên kích thước.
- Các item khác di chuyển mượt 160–220ms.
- Có keyboard alternative.

Không bắt buộc drag-and-drop cho phiên bản đầu.

---

## 25. Page transition

Dashboard không cần page transition phức tạp.

Khuyến nghị:

- Route change giữ shell sidebar và header.
- Main content fade nhẹ 100–160ms nếu cần.
- Không slide toàn trang sang trái/phải.

Nếu framework hỗ trợ streaming/loading route, ưu tiên skeleton theo vùng.

---

## 26. Layout animation

Khi component thay đổi kích thước:

- Chỉ animate nếu người dùng vừa thao tác.
- Không animate layout lớn trong lần tải ban đầu.

Ví dụ hợp lệ:

- Sidebar collapse.
- Expand submenu.
- Mở filter panel.
- Thu gọn card detail.

Ví dụ không hợp lệ:

- Toàn bộ Dashboard rearrange liên tục khi API trả dữ liệu theo từng phần.

---

## 27. Reduced motion

CSS bắt buộc:

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    scroll-behavior: auto !important;
    transition-duration: 0.01ms !important;
  }
}
```

Có thể tinh chỉnh để giữ opacity transition ngắn nếu không gây khó chịu.

Không phụ thuộc vào animation để truyền tải trạng thái bắt buộc.

---

## 28. Performance requirements

Motion phải ưu tiên các thuộc tính GPU-friendly:

- `transform`.
- `opacity`.

Hạn chế animate:

- `width` và `height` ở nhiều thành phần cùng lúc.
- `box-shadow` quá phức tạp.
- `filter: blur()` lớn.
- `top`, `left` cho chuyển động liên tục.

Sidebar có thể animate width vì đây là tương tác hiếm và cần thiết, nhưng phải kiểm tra hiệu năng.

Mục tiêu:

```text
60 FPS trên laptop phổ thông
```

---

## 29. Animation trong trạng thái mạng chậm

Nếu thao tác kéo dài hơn 300ms:

- Hiển thị loading feedback.

Nếu dài hơn 1 giây:

- Hiển thị mô tả tác vụ.

Nếu dài hơn 5 giây:

- Cho biết vẫn đang xử lý.
- Cân nhắc cho phép chạy nền.

Ví dụ:

```text
Đang tạo báo cáo, bạn có thể tiếp tục làm việc.
```

Animation không được che giấu việc hệ thống đang chờ backend.

---

## 30. Motion cho real-time update

Khi dữ liệu real-time thay đổi:

- Không animate toàn card.
- Chỉ highlight giá trị hoặc row liên quan.
- Badge tăng nhẹ một lần.
- Không tự scroll người dùng đến item mới.

Thông báo critical có thể dùng toast, nhưng không rung hoặc nhấp nháy liên tục.

---

## 31. Motion accessibility checklist

- [ ] Không có animation nhấp nháy nhanh.
- [ ] Không có chuyển động lặp vô hạn ngoài spinner và skeleton.
- [ ] Có hỗ trợ `prefers-reduced-motion`.
- [ ] Animation không là nguồn thông tin duy nhất.
- [ ] Focus không bị trì hoãn bởi animation.
- [ ] Modal và drawer trả focus đúng.
- [ ] Toast action đủ thời gian tương tác.
- [ ] Hover animation không áp dụng bắt buộc cho keyboard.
- [ ] Chart có summary text thay thế.

---

## 32. CSS motion utilities tham chiếu

```css
.motion-fade-in {
  animation: fade-in 180ms var(--motion-ease-enter) both;
}

.motion-popover-in {
  transform-origin: top right;
  animation: popover-in 170ms var(--motion-ease-enter) both;
}

.motion-drawer-in {
  animation: drawer-in 260ms var(--motion-ease-standard) both;
}

@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes popover-in {
  from {
    opacity: 0;
    transform: translateY(-4px) scale(0.98);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

@keyframes drawer-in {
  from { transform: translateX(100%); }
  to { transform: translateX(0); }
}
```

---

## 33. Framer Motion mapping — tùy chọn

Nếu dùng Framer Motion:

```ts
export const motionTokens = {
  fast: 0.12,
  normal: 0.18,
  slow: 0.26,
  easeStandard: [0.2, 0, 0, 1],
  easeEnter: [0, 0, 0, 1],
  easeExit: [0.4, 0, 1, 1],
};
```

Popover:

```ts
export const popoverVariants = {
  hidden: { opacity: 0, y: -4, scale: 0.98 },
  visible: { opacity: 1, y: 0, scale: 1 },
  exit: { opacity: 0, y: -2, scale: 0.99 },
};
```

Không bắt buộc dùng animation library nếu CSS đáp ứng đủ.

---

## 34. Component motion mapping

| Component | Motion |
|---|---|
| Sidebar | Width + label opacity |
| Header | Shadow transition |
| Button | Color + 1px translate |
| Card | Shadow + 2px translate |
| Dropdown | Fade + translate + scale |
| Tooltip | Fade + 2px translate |
| Modal | Fade + translateY + scale |
| Drawer | TranslateX |
| Toast | Fade + translateX |
| Table row | Background highlight |
| KPI | Crossfade value |
| Chart | Path/bar interpolation |
| Skeleton | Subtle shimmer |

---

## 35. Acceptance checklist

- [ ] Motion tokens được dùng thống nhất.
- [ ] Hover button không dài hơn 120–160ms.
- [ ] Dropdown mở dưới 200ms.
- [ ] Modal và drawer không có bounce.
- [ ] Sidebar collapse không làm text wrap.
- [ ] Filter update không xóa toàn bộ layout.
- [ ] Chart animation không chạy lại không cần thiết.
- [ ] Table update có highlight nhẹ.
- [ ] Loading feedback xuất hiện khi thao tác vượt 300ms.
- [ ] Skeleton hỗ trợ reduced motion.
- [ ] Không có animation lặp gây mất tập trung.
- [ ] Focus management không bị ảnh hưởng.
- [ ] Motion đạt hiệu năng tốt trên laptop phổ thông.
- [ ] Real-time update không tự scroll hoặc flash mạnh.
- [ ] Page transition không làm mất cảm giác ổn định của shell.

---

## 36. Những điều không được làm

- Không dùng bounce, elastic hoặc spring quá mạnh.
- Không dùng confetti.
- Không animate toàn bộ Dashboard sau mỗi filter.
- Không count-up mọi số liệu mỗi lần refresh.
- Không dùng parallax.
- Không dùng animation dài hơn 700ms cho thao tác thường xuyên.
- Không để tooltip delay quá lâu.
- Không dùng shake mạnh cho lỗi form.
- Không nhấp nháy badge liên tục.
- Không phụ thuộc vào chuyển động để truyền tải trạng thái.
- Không bỏ qua `prefers-reduced-motion`.

---

## 37. Kết luận

Motion của Cynca VLXD Dashboard phải nhanh, có mục đích và nhất quán. Chuyển động cần hỗ trợ mối quan hệ giữa thao tác và kết quả, giữ layout ổn định, cung cấp phản hồi rõ ràng và không làm gián đoạn quá trình đọc dữ liệu hoặc xử lý công việc.

File tiếp theo:

```text
11-Responsive.md
```