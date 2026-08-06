# Cynca VLXD Admin Dashboard — Visual QA Cases

> Phiên bản: 1.0  
> Phạm vi: Bộ test case kiểm tra hình ảnh, bố cục và trạng thái giao diện Dashboard  
> Đối tượng sử dụng: Designer, Frontend Developer, QA, Product Owner, AI coding agent  
> Tài liệu liên quan: toàn bộ thư mục `docs/cynca-vlxd/dashboard/`

---

## 1. Mục tiêu

Tài liệu này định nghĩa các trường hợp kiểm thử trực quan bắt buộc trước khi nghiệm thu Dashboard Cynca VLXD.

Visual QA phải phát hiện được:

- Sai màu thương hiệu.
- Sai spacing, radius, shadow hoặc typography.
- Grid không nhất quán.
- Component bị tràn, méo hoặc lệch.
- Responsive sai breakpoint.
- Nội dung dài làm vỡ giao diện.
- Trạng thái loading, empty, error hoặc permission chưa hoàn thiện.
- Focus, hover và active state không rõ ràng.

Visual QA không chỉ kiểm tra màn hình mặc định với dữ liệu đẹp.

---

## 2. Viewport bắt buộc

Chụp và kiểm tra tối thiểu tại:

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

Viewport chính để so sánh thiết kế:

```text
1440 × 900
```

---

## 3. Môi trường kiểm thử

Kiểm tra tối thiểu trên:

- Chrome hiện hành.
- Safari hiện hành trên macOS.
- Edge hoặc Chrome trên Windows.
- Zoom 100%, 125%, 150% và 200%.
- Light mode mặc định.
- Reduced motion.
- High contrast hoặc forced colors nếu có thể.

Dữ liệu, font và icon phải được tải hoàn chỉnh trước khi chụp ảnh so sánh.

---

## 4. Quy tắc đánh giá

Mỗi test case được đánh dấu:

```text
PASS
FAIL
BLOCKED
NOT APPLICABLE
```

Mức sai lệch:

- **Blocker:** giao diện không dùng được hoặc lộ dữ liệu sai quyền.
- **Critical:** sai dữ liệu, mất action chính hoặc responsive vỡ nghiêm trọng.
- **Major:** sai layout, spacing, component state hoặc accessibility quan trọng.
- **Minor:** sai lệch nhỏ về pixel, icon hoặc motion nhưng không cản tác vụ.

---

## 5. Sai số cho phép

| Hạng mục | Sai số đề xuất |
|---|---:|
| Spacing nhỏ | ±2px |
| Kích thước card | ±4px |
| Sidebar width | 0–2px |
| Header height | 0–2px |
| Radius | ±1px |
| Font size | 0–1px |
| Màu | Phải đúng token |
| Grid span | Không cho phép sai |
| Breakpoint behavior | Không cho phép sai |

Không sử dụng sai số để hợp thức hóa việc dùng token khác guideline.

---

## 6. Case VQA-001 — Dashboard mặc định 1440px

### Điều kiện

- Viewport: 1440 × 900.
- Sidebar mở rộng.
- Dữ liệu đầy đủ.
- Không có modal hoặc dropdown mở.

### Kiểm tra

- [ ] Sidebar rộng 264px.
- [ ] Header cao 72px.
- [ ] Nền trang `#F6F8FC`.
- [ ] Content padding 32px.
- [ ] KPI hiển thị 4 cột.
- [ ] Revenue chart 8 cột.
- [ ] Order status 4 cột.
- [ ] Grid gap 24px.
- [ ] Card radius 20px.
- [ ] Không có horizontal scroll toàn trang.
- [ ] Nội dung phía trên đọc được mà không bị chật.

---

## 7. Case VQA-002 — Màn hình rộng 1920px

### Kiểm tra

- [ ] Main content không vượt quá 1600px.
- [ ] Nội dung được căn giữa trong vùng sau sidebar.
- [ ] Card không bị kéo quá rộng.
- [ ] Typography không tăng kích thước vô lý.
- [ ] Khoảng trắng hai bên cân đối.
- [ ] Chart không bị giãn làm đường dữ liệu quá thưa.

---

## 8. Case VQA-003 — Laptop 1366px

### Kiểm tra

- [ ] Header không tràn.
- [ ] Search vẫn truy cập được.
- [ ] User name hoặc role được rút gọn đúng thứ tự ưu tiên.
- [ ] KPI không nhỏ hơn chiều rộng tối thiểu.
- [ ] Chart chuyển layout khi không đủ không gian.
- [ ] Table không ép font quá nhỏ.
- [ ] Không có nút hoặc badge chồng lên nhau.

---

## 9. Case VQA-004 — Viewport 1024px

### Kiểm tra

- [ ] Sidebar collapsed còn 80px.
- [ ] Tooltip sidebar hoạt động.
- [ ] KPI chuyển 2 cột.
- [ ] Revenue chart và order status xếp dọc khi cần.
- [ ] Orders table có wrapper scroll ngang riêng.
- [ ] Header không có horizontal scroll.
- [ ] Date range hoặc branch filter được chuyển vị trí hợp lý.
- [ ] Main content padding khoảng 24px.

---

## 10. Case VQA-005 — Tablet portrait

### Viewport

```text
768 × 1024 hoặc 820 × 1180
```

### Kiểm tra

- [ ] Sidebar chuyển drawer.
- [ ] Menu button dễ nhận biết.
- [ ] Drawer không vượt viewport.
- [ ] KPI chuyển 1–2 cột phù hợp.
- [ ] Chart xếp một cột.
- [ ] Toolbar wrap đúng.
- [ ] Action quan trọng không bị ẩn hoàn toàn.
- [ ] Touch target tối thiểu 44px.

---

## 11. Case VQA-006 — Mobile fallback

### Viewport

```text
390 × 844
```

### Kiểm tra

- [ ] Không có horizontal scroll toàn trang.
- [ ] KPI một cột.
- [ ] Table chuyển card list hoặc scroll hợp lý.
- [ ] Header tối giản nhưng vẫn có menu, notification và tài khoản.
- [ ] Modal hoặc drawer không bị cắt.
- [ ] Toast có margin 16px hai bên.
- [ ] Text và số liệu không bị cắt mất hoàn toàn.

---

## 12. Case VQA-007 — Sidebar expanded

### Kiểm tra

- [ ] Logo rõ nét.
- [ ] Tên `Cynca VLXD` đúng chính tả.
- [ ] Menu item cao 44px.
- [ ] Icon 20px và cùng stroke.
- [ ] Active state rõ nhưng không quá chói.
- [ ] Group label có độ tương phản phù hợp.
- [ ] Badge không làm thay đổi chiều cao row.
- [ ] User profile nằm ổn định dưới cùng.

---

## 13. Case VQA-008 — Sidebar collapsed

### Kiểm tra

- [ ] Icon được căn giữa.
- [ ] Label ẩn hoàn toàn về thị giác.
- [ ] Không có text wrap trong animation.
- [ ] Active indicator vẫn rõ.
- [ ] Tooltip đúng vị trí và không bị cắt.
- [ ] Badge chuyển dạng phù hợp.
- [ ] Main content mở rộng mượt, không nhảy giật mạnh.

---

## 14. Case VQA-009 — Sidebar drawer

### Kiểm tra

- [ ] Drawer mở từ bên trái.
- [ ] Backdrop đủ rõ nhưng không quá đậm.
- [ ] Nội dung phía sau không cuộn.
- [ ] Nút đóng dễ thấy.
- [ ] Focus ring không bị cắt.
- [ ] Click backdrop và Escape đóng drawer.
- [ ] Drawer không che mất vùng safe area.

---

## 15. Case VQA-010 — Header đầy đủ

### Kiểm tra

- [ ] Header cao 72px.
- [ ] Search cao 40px.
- [ ] Date range, branch, export, notification và user menu cùng baseline.
- [ ] Khoảng cách action 8–12px nhất quán.
- [ ] Border dưới nhẹ.
- [ ] Sticky shadow chỉ xuất hiện khi cuộn.
- [ ] Không có control cao thấp khác nhau bất thường.

---

## 16. Case VQA-011 — Search dropdown

### Kiểm tra

- [ ] Dropdown căn đúng với search field.
- [ ] Không vượt viewport.
- [ ] Nhóm kết quả dễ phân biệt.
- [ ] Thumbnail không méo.
- [ ] Hover và focus state rõ.
- [ ] Empty state căn chỉnh hợp lý.
- [ ] Loading skeleton gần với layout kết quả thật.

---

## 17. Case VQA-012 — KPI mặc định

### Kiểm tra

- [ ] Label dùng text muted.
- [ ] Value là yếu tố nổi bật nhất.
- [ ] Icon container 44px.
- [ ] Trend badge đúng semantic.
- [ ] Comparison label không bị xuống dòng khó đọc.
- [ ] Các card cùng hàng có chiều cao nhất quán.
- [ ] Không dùng nền màu khác nhau cho toàn card.

---

## 18. Case VQA-013 — KPI số rất lớn

Dữ liệu mẫu:

```text
128.450.780.000 ₫
```

### Kiểm tra

- [ ] Hiển thị compact hợp lý.
- [ ] Không tràn card.
- [ ] Tooltip có số đầy đủ.
- [ ] Font không bị giảm dưới mức guideline.
- [ ] Currency unit không tách dòng bất thường.

---

## 19. Case VQA-014 — KPI loading, empty, error

### Loading

- [ ] Skeleton giữ đúng chiều cao card.
- [ ] Không hiển thị số 0 giả.

### Empty

- [ ] Nêu rõ chưa có dữ liệu.
- [ ] Không giống error state.

### Error

- [ ] Border hoặc nền cảnh báo nhẹ.
- [ ] Có nút thử lại.
- [ ] Card không co chiều cao.

---

## 20. Case VQA-015 — Revenue chart

### Kiểm tra

- [ ] Đường chính màu brand.
- [ ] Area fill nhẹ.
- [ ] Axis label không chồng nhau.
- [ ] Gridline không quá đậm.
- [ ] Tooltip đúng format VND.
- [ ] Chart giữ chiều cao tối thiểu.
- [ ] Filter thời gian cùng style với hệ thống.
- [ ] Legend không xuất hiện khi chỉ một series nếu không cần.

---

## 21. Case VQA-016 — Order status donut

### Kiểm tra

- [ ] Donut không dùng 3D.
- [ ] Tổng đơn ở giữa rõ ràng.
- [ ] Màu status khớp orders table.
- [ ] Legend có số lượng và phần trăm.
- [ ] Nhóm nhỏ vẫn có thể đọc được.
- [ ] Không có quá nhiều màu tương tự khó phân biệt.

---

## 22. Case VQA-017 — Chart loading, empty, error

- [ ] Loading placeholder giữ chiều cao chart.
- [ ] Empty state không render axis rỗng gây hiểu nhầm.
- [ ] Error state chỉ ảnh hưởng card bị lỗi.
- [ ] Nút thử lại đúng style.
- [ ] Card khác trên Dashboard vẫn ổn định.

---

## 23. Case VQA-018 — Product best sellers

### Kiểm tra

- [ ] Thumbnail 52 × 52px.
- [ ] Ảnh không méo.
- [ ] Tên sản phẩm truncate đúng.
- [ ] SKU và danh mục dùng metadata style.
- [ ] Doanh thu căn thẳng hàng.
- [ ] Row hover nhẹ.
- [ ] Ranking không quá nổi hơn sản phẩm.

---

## 24. Case VQA-019 — Inventory alerts

### Kiểm tra

- [ ] Hết hàng, sắp hết và tồn âm có style khác nhau.
- [ ] Màu không là tín hiệu duy nhất.
- [ ] Số tồn và ngưỡng tối thiểu rõ ràng.
- [ ] Quick action không làm row quá chật.
- [ ] Sản phẩm không ảnh có fallback đúng.
- [ ] Cảnh báo stale data nhìn thấy được.

---

## 25. Case VQA-020 — Orders table đầy đủ

### Kiểm tra

- [ ] Header row nền `#F8FAFC`.
- [ ] Row cao tối thiểu khoảng 64px.
- [ ] Mã đơn dùng mono font.
- [ ] Tổng tiền căn phải.
- [ ] Status badge không xuống dòng.
- [ ] Row hover nhất quán.
- [ ] Row quá hạn có indicator nhẹ.
- [ ] Action icon cùng kích thước.

---

## 26. Case VQA-021 — Table nội dung dài

Dữ liệu mẫu:

- Tên doanh nghiệp dài 60–80 ký tự.
- Mã đơn dài.
- Số tiền 12 chữ số.
- Status label dài.

### Kiểm tra

- [ ] Không làm thay đổi chiều rộng toàn trang.
- [ ] Truncate đúng cột.
- [ ] Tooltip hoặc accessible name vẫn có đầy đủ nội dung.
- [ ] Action column không bị đẩy ra ngoài vô lý.
- [ ] Row height không tăng quá mức.

---

## 27. Case VQA-022 — Order quick view drawer

- [ ] Drawer mở từ phải.
- [ ] Width đúng guideline.
- [ ] Header và footer action rõ.
- [ ] Danh sách sản phẩm không bị chật.
- [ ] Timeline dễ đọc.
- [ ] Drawer có scroll riêng hợp lý.
- [ ] Nút đóng và primary action luôn truy cập được.

---

## 28. Case VQA-023 — Notification dropdown

- [ ] Width 380px trên desktop.
- [ ] Header, tabs và list đúng spacing.
- [ ] Unread item khác biệt nhẹ.
- [ ] Icon category đúng màu semantic.
- [ ] Badge số không lệch vị trí.
- [ ] Dropdown không vượt cạnh phải viewport.
- [ ] Empty và error state đầy đủ.

---

## 29. Case VQA-024 — Toast stack

Kiểm tra với 1, 3 và 5 toast.

- [ ] Toast nằm dưới header.
- [ ] Không che action quan trọng.
- [ ] Các toast có gap nhất quán.
- [ ] Text dài không làm toast vượt viewport.
- [ ] Nút đóng và action cùng baseline.
- [ ] Error toast nổi bật hơn success nhưng không quá chói.

---

## 30. Case VQA-025 — Permission state

### Kịch bản

Người dùng kho không có quyền xem lợi nhuận.

### Kiểm tra

- [ ] KPI lợi nhuận không xuất hiện hoặc có replacement phù hợp.
- [ ] Grid không để khoảng trống bất thường.
- [ ] Menu tài chính không hiển thị.
- [ ] Action bị cấm không xuất hiện.
- [ ] Nếu disabled, có giải thích rõ và không chỉ giảm opacity.

---

## 31. Case VQA-026 — Long Vietnamese text

Kiểm tra với:

- Tên chi nhánh dài.
- Tên sản phẩm dài.
- Notification description dài.
- Button label dài.

### Kỳ vọng

- [ ] Không tràn card.
- [ ] Không làm header tăng chiều cao.
- [ ] Truncate đúng nơi.
- [ ] Nội dung quan trọng vẫn đọc được.
- [ ] Tooltip hoặc detail view giữ nội dung đầy đủ.

---

## 32. Case VQA-027 — Zoom 125% và 150%

- [ ] Header không tràn.
- [ ] Sidebar label không chồng icon.
- [ ] KPI chuyển cột đúng khi không đủ chỗ.
- [ ] Table wrapper xử lý overflow.
- [ ] Dropdown và modal vẫn nằm trong viewport.
- [ ] Không mất action chính.

---

## 33. Case VQA-028 — Zoom 200%

- [ ] Chức năng chính vẫn truy cập được.
- [ ] Không có text chồng nhau.
- [ ] Focus indicator còn nhìn thấy.
- [ ] Sidebar chuyển trạng thái phù hợp.
- [ ] Modal có thể scroll.
- [ ] Horizontal scroll chỉ nằm ở vùng hợp lệ.

---

## 34. Case VQA-029 — Keyboard focus visual

- [ ] Focus ring rõ hơn hover.
- [ ] Focus ring không bị cắt bởi card hoặc table wrapper.
- [ ] Thứ tự focus phù hợp visual order.
- [ ] Dropdown item focus dễ nhận biết.
- [ ] Modal và drawer giữ focus bên trong.
- [ ] Sau khi đóng, focus trở về trigger.

---

## 35. Case VQA-030 — Reduced motion

- [ ] Sidebar đổi trạng thái gần như tức thời hoặc chuyển nhẹ.
- [ ] Chart không draw animation dài.
- [ ] Skeleton không shimmer.
- [ ] Modal và drawer không bounce.
- [ ] Không mất thông tin khi animation bị tắt.

---

## 36. Case VQA-031 — High contrast mode

- [ ] Border card và control vẫn nhận biết được.
- [ ] Status badge có border hoặc text rõ.
- [ ] Focus ring còn hiển thị.
- [ ] Icon không biến mất hoàn toàn.
- [ ] Button primary và secondary vẫn phân biệt được.

---

## 37. Case VQA-032 — API chậm

Giả lập latency 2–5 giây.

- [ ] Skeleton xuất hiện đúng vùng.
- [ ] Shell vẫn hiển thị ngay.
- [ ] Không có layout shift lớn.
- [ ] Filter bị disable hợp lý khi cần.
- [ ] Người dùng hiểu hệ thống vẫn đang xử lý.

---

## 38. Case VQA-033 — API lỗi từng widget

Giả lập riêng:

- KPI lỗi.
- Revenue chart lỗi.
- Orders table lỗi.
- Inventory alerts lỗi.

### Kiểm tra

- [ ] Chỉ widget liên quan hiển thị error state.
- [ ] Các vùng khác vẫn hiển thị bình thường.
- [ ] Card giữ nguyên kích thước.
- [ ] Có action thử lại.

---

## 39. Case VQA-034 — Dữ liệu stale

- [ ] Có timestamp cập nhật cuối.
- [ ] Tồn kho stale nổi bật hơn KPI doanh thu stale.
- [ ] Cảnh báo không che số liệu chính.
- [ ] Người dùng có thể refresh hoặc hiểu nguyên nhân.

---

## 40. Case VQA-035 — Dữ liệu real-time

Khi có đơn mới hoặc notification mới:

- [ ] Badge cập nhật không làm header nhảy.
- [ ] Row mới được highlight nhẹ.
- [ ] Không tự scroll trang.
- [ ] Không tự mở dropdown.
- [ ] Toast không xuất hiện hàng loạt.

---

## 41. Case VQA-036 — Ảnh sản phẩm lỗi

- [ ] Fallback icon xuất hiện.
- [ ] Container vẫn giữ đúng kích thước.
- [ ] Không hiển thị icon broken image của trình duyệt.
- [ ] Tên sản phẩm vẫn đầy đủ.
- [ ] Một ảnh lỗi không làm lỗi toàn widget.

---

## 42. Case VQA-037 — Empty Dashboard mới tạo

### Kịch bản

Tài khoản mới chưa có sản phẩm và đơn hàng.

### Kiểm tra

- [ ] Không hiển thị hàng loạt số 0 gây khô cứng.
- [ ] Có onboarding action phù hợp.
- [ ] Empty state từng khu vực rõ ràng.
- [ ] Không có biểu đồ giả.
- [ ] Primary action hướng đến tạo sản phẩm hoặc cấu hình ban đầu.

---

## 43. Case VQA-038 — Dense data mode

### Kịch bản

- 10 đơn.
- 10 sản phẩm.
- Nhiều notification.
- Badge `99+`.

### Kiểm tra

- [ ] Card không vượt chiều cao thiếu kiểm soát.
- [ ] Có `Xem tất cả` thay vì scroll dài.
- [ ] Badge không làm vỡ layout.
- [ ] Danh sách giữ nhịp hàng đều.
- [ ] Không có text chồng nhau.

---

## 44. Case VQA-039 — Visual consistency audit

So sánh toàn bộ Dashboard để bảo đảm:

- [ ] Tất cả card cùng radius.
- [ ] Tất cả input và button cùng chiều cao chuẩn.
- [ ] Icon cùng bộ Lucide hoặc bộ đã chọn.
- [ ] Status color dùng cùng mapping.
- [ ] Text muted dùng cùng token.
- [ ] Link dùng cùng màu brand.
- [ ] Shadow cùng cấp độ theo context.
- [ ] Spacing không xuất hiện giá trị ngẫu nhiên.

---

## 45. Case VQA-040 — Screenshot regression

Thiết lập ảnh chuẩn cho:

```text
Dashboard default — 1440 × 900
Dashboard default — 1024 × 768
Sidebar collapsed
Notification open
Order drawer open
Loading state
Empty state
Error state
```

Quy tắc:

- Không tự cập nhật baseline khi test fail.
- Mọi thay đổi baseline phải có designer hoặc reviewer duyệt.
- Mask dữ liệu động như thời gian thực nếu cần.
- Font phải tải xong trước khi chụp.

---

## 46. Quy trình báo lỗi Visual QA

Mỗi bug cần có:

- Mã test case.
- Viewport.
- Browser và OS.
- Ảnh hiện tại.
- Ảnh kỳ vọng hoặc token tham chiếu.
- Mức độ lỗi.
- Component hoặc route liên quan.
- Các bước tái hiện.

Mẫu:

```text
VQA-020 — Major
Viewport: 1366 × 768
Browser: Chrome / Windows
Lỗi: Cột tổng tiền bị đẩy ra ngoài table wrapper.
Kỳ vọng: Table scroll trong wrapper, không tạo horizontal scroll toàn trang.
```

---

## 47. Checklist sign-off Visual QA

- [ ] Đã kiểm tra đủ viewport bắt buộc.
- [ ] Đã kiểm tra loading, empty và error.
- [ ] Đã kiểm tra permission state.
- [ ] Đã kiểm tra nội dung dài và số lớn.
- [ ] Đã kiểm tra zoom 125%, 150% và 200%.
- [ ] Đã kiểm tra keyboard focus.
- [ ] Đã kiểm tra reduced motion.
- [ ] Đã kiểm tra ảnh lỗi.
- [ ] Đã chạy screenshot regression.
- [ ] Không còn bug Blocker hoặc Critical.
- [ ] Bug Major còn lại đã được chấp thuận rõ ràng.
- [ ] Designer hoặc Product Owner đã sign-off.

---

## 48. Bảng sign-off

| Nhóm | Người duyệt | Trạng thái | Ngày |
|---|---|---|---|
| Design |  | Chưa duyệt |  |
| Frontend |  | Chưa duyệt |  |
| QA |  | Chưa duyệt |  |
| Product |  | Chưa duyệt |  |

---

## 49. Kết luận

Visual QA của Cynca VLXD Dashboard phải bao phủ nhiều viewport, nhiều trạng thái dữ liệu và nhiều điều kiện nội dung thực tế. Dashboard chỉ được nghiệm thu khi giao diện giữ đúng token, không vỡ responsive, không mất action và duy trì tính nhất quán ở mọi component chính.

File tiếp theo đề xuất:

```text
README.md
18-Component-Inventory.md
```