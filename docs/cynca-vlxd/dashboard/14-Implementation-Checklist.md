# Cynca VLXD Admin Dashboard — Implementation Checklist

> Phiên bản: 1.0  
> Phạm vi: Checklist triển khai, kiểm thử và nghiệm thu toàn bộ Dashboard quản trị  
> Nền tảng mục tiêu: Next.js / React / TypeScript / Tailwind CSS  
> Tài liệu liên quan: toàn bộ thư mục `dashboard/`

---

## 1. Mục đích

Tài liệu này là danh sách kiểm tra cuối cùng dành cho designer, frontend developer, backend developer, QA và AI coding agent khi triển khai Cynca VLXD Dashboard.

Một hạng mục chỉ được đánh dấu hoàn thành khi:

- Giao diện đúng guideline.
- Dữ liệu và trạng thái nghiệp vụ đúng.
- Responsive hoạt động.
- Accessibility đạt yêu cầu.
- Loading, empty, error và permission state đầy đủ.
- Không phát sinh lỗi nghiêm trọng trong kiểm thử.

---

## 2. Definition of Done

Dashboard được xem là hoàn thành khi đáp ứng toàn bộ điều kiện sau:

- [ ] Người dùng hiểu tình trạng kinh doanh trong 10 giây đầu.
- [ ] Các KPI chính hiển thị đúng dữ liệu và phạm vi thời gian.
- [ ] Các biểu đồ có tooltip, summary và trạng thái đầy đủ.
- [ ] Đơn hàng mới và cảnh báo tồn kho có thể xử lý nhanh.
- [ ] Phân quyền được kiểm tra ở cả frontend và backend.
- [ ] Giao diện hoạt động tốt từ 1024px trở lên.
- [ ] Không có horizontal scroll toàn trang.
- [ ] Có keyboard navigation và focus management đúng.
- [ ] Không có lỗi accessibility mức serious hoặc critical.
- [ ] Không có lỗi console trong luồng sử dụng chính.
- [ ] Không dùng dữ liệu mẫu hardcode trong production.
- [ ] API failure không làm sập toàn Dashboard.

---

## 3. Kiến trúc frontend

- [ ] Dùng TypeScript strict mode.
- [ ] Tách Dashboard shell khỏi page content.
- [ ] Sidebar, header, card, badge, button và table là component tái sử dụng.
- [ ] Design token được khai báo tập trung.
- [ ] Không hardcode màu lặp lại trong JSX.
- [ ] Không nối class Tailwind động không có mapping tĩnh.
- [ ] Không trộn nhiều thư viện UI hoặc chart không cần thiết.
- [ ] Component chỉ nhận props cần thiết.
- [ ] Server state và UI state được tách rõ.
- [ ] Error boundary được bố trí theo vùng hợp lý.
- [ ] Route loading không làm mất shell sidebar và header.

Cấu trúc tham chiếu:

```text
src/
├── app/
│   └── dashboard/
├── components/
│   ├── dashboard/
│   └── ui/
├── hooks/
├── lib/
├── services/
├── styles/
└── types/
```

---

## 4. Dashboard shell

- [ ] Sidebar nằm bên trái và không phủ nội dung desktop.
- [ ] Header cao 72px và sticky đúng.
- [ ] Main content có `min-width: 0`.
- [ ] Content giới hạn tối đa 1600px.
- [ ] Page background dùng token `background.page`.
- [ ] Main content padding đúng breakpoint.
- [ ] Scroll dọc nằm ở trang, không tạo nhiều scroll container lồng nhau.
- [ ] Z-index tuân theo hệ thống token.
- [ ] Skip link hoạt động.
- [ ] Có một `main` landmark duy nhất.

---

## 5. Sidebar

- [ ] Expanded width đúng 264px hoặc 240px ở laptop.
- [ ] Collapsed width đúng 80px.
- [ ] Dưới 1024px chuyển thành drawer.
- [ ] Active route hiển thị chính xác.
- [ ] Route con vẫn active menu cha.
- [ ] Menu hiển thị theo quyền.
- [ ] Icon dùng cùng một bộ.
- [ ] Label không xuống hai dòng.
- [ ] Tooltip hoạt động khi collapsed.
- [ ] Submenu hỗ trợ keyboard.
- [ ] Profile area luôn truy cập được.
- [ ] Collapse preference được lưu nhưng không làm vỡ breakpoint.
- [ ] Drawer có backdrop, focus trap và Escape để đóng.

---

## 6. Header

- [ ] Global search tìm được đơn hàng, sản phẩm và khách hàng.
- [ ] Search có loading, empty và error state.
- [ ] Search hỗ trợ `Ctrl/Cmd + K` nếu đã thiết kế.
- [ ] Date range cập nhật đúng toàn Dashboard.
- [ ] Branch filter chỉ hiển thị khi có quyền.
- [ ] Export có CSV, Excel hoặc PDF theo yêu cầu nghiệp vụ.
- [ ] Export button có loading state và không đổi chiều rộng.
- [ ] Notification badge phản ánh số chưa đọc.
- [ ] User menu có hồ sơ, đổi mật khẩu và đăng xuất.
- [ ] Chỉ một dropdown mở tại một thời điểm.
- [ ] Header không tràn ở 1024px.
- [ ] Icon-only button có tooltip và `aria-label`.

---

## 7. Page toolbar

- [ ] Có `h1` rõ ràng.
- [ ] Có mô tả hoặc thời điểm cập nhật nếu cần.
- [ ] Chỉ một primary action nổi bật.
- [ ] Filter cấp trang được nhóm hợp lý.
- [ ] Toolbar wrap đúng trên laptop nhỏ.
- [ ] Action bị giới hạn quyền có trạng thái phù hợp.
- [ ] Không đặt quá nhiều nút cạnh nhau.

---

## 8. KPI cards

- [ ] Desktop lớn hiển thị 4 KPI trên một hàng.
- [ ] Laptop hẹp chuyển 2 cột khi cần.
- [ ] Value nổi bật nhất trong card.
- [ ] Trend có semantic đúng với nghiệp vụ.
- [ ] Có kỳ so sánh rõ ràng.
- [ ] Tooltip mô tả công thức KPI.
- [ ] Số tiền đúng định dạng Việt Nam.
- [ ] Loading không hiển thị giá trị 0 giả.
- [ ] Empty, error và stale state tách biệt.
- [ ] KPI tài chính bị ẩn theo phân quyền.
- [ ] KPI link có focus và accessible name đầy đủ.
- [ ] Không quá 6 KPI ở vùng đầu trang.

---

## 9. Charts

- [ ] Revenue chart dùng line hoặc area chart.
- [ ] Order status dùng donut chart.
- [ ] Màu trạng thái đồng nhất với badge đơn hàng.
- [ ] Axis, gridline và legend dễ đọc.
- [ ] Tooltip hiển thị giá trị đầy đủ.
- [ ] Granularity thay đổi đúng theo date range.
- [ ] Không quá 2 series trong chart tổng quan mặc định.
- [ ] Có loading, empty, error và stale state.
- [ ] Có summary text cho screen reader.
- [ ] Tooltip dùng được bằng keyboard hoặc có data table thay thế.
- [ ] Chart không thấp dưới 280–320px tùy breakpoint.
- [ ] Animation tắt hoặc giảm khi `prefers-reduced-motion`.
- [ ] Drill-down giữ nguyên filter qua URL hoặc state.
- [ ] Không dùng chart 3D.

---

## 10. Product widgets

- [ ] Best sellers có tiêu chí xếp hạng rõ.
- [ ] Hiển thị tối đa 5–10 sản phẩm.
- [ ] Thumbnail đúng tỷ lệ và có fallback.
- [ ] SKU và danh mục dễ nhận biết.
- [ ] Số lượng và doanh thu đúng format.
- [ ] Inventory warning ưu tiên đúng severity.
- [ ] Tồn kho âm, hết hàng và sắp hết có nhãn riêng.
- [ ] Dữ liệu tồn kho cũ có cảnh báo.
- [ ] Quick action kiểm tra quyền trước khi hiển thị.
- [ ] Data quality widget phát hiện thiếu ảnh, giá và thông số.
- [ ] Empty state riêng cho từng widget.
- [ ] Row click được có semantic link hoặc button.
- [ ] Không dùng ảnh sản phẩm ngẫu nhiên hoặc sai sản phẩm.

---

## 11. Orders table

- [ ] Dashboard hiển thị tối đa 8–10 đơn gần nhất.
- [ ] Mã đơn nghiệp vụ dễ đọc.
- [ ] Khách hàng có tên và thông tin phụ hợp lý.
- [ ] Payment status và order status tách biệt.
- [ ] Badge trạng thái dùng mapping tập trung.
- [ ] Tổng tiền căn phải và đúng định dạng.
- [ ] Thời gian có giá trị tương đối và tuyệt đối.
- [ ] Đơn quá hạn có cảnh báo nhẹ.
- [ ] Row action có nhãn cụ thể theo mã đơn.
- [ ] Bảng dùng semantic HTML.
- [ ] Sortable column có `aria-sort`.
- [ ] Loading dùng skeleton rows.
- [ ] Empty filter state khác empty dataset state.
- [ ] Bảng có overflow riêng ở 1024px.
- [ ] Quick view drawer trả focus về row trigger.
- [ ] Hủy đơn, hoàn tiền và xác nhận thanh toán không dùng optimistic update mù quáng.

---

## 12. Notifications

- [ ] Badge chỉ đếm thông báo chưa đọc phù hợp.
- [ ] Notification item nêu rõ đối tượng và sự kiện.
- [ ] Severity và category tách biệt.
- [ ] Read và resolved là hai trạng thái riêng nếu nghiệp vụ cần.
- [ ] Dropdown có Tất cả, Chưa đọc và Cần xử lý.
- [ ] Widget Dashboard chỉ hiển thị thông báo actionable.
- [ ] Mark all as read xử lý rollback khi lỗi.
- [ ] Có deduplication cho sự kiện lặp.
- [ ] Sự kiện batch không tạo hàng loạt toast.
- [ ] Real-time update không tự mở dropdown.
- [ ] Toast timeout phù hợp severity.
- [ ] Toast action pause timeout khi hover hoặc focus.
- [ ] Live region dùng đúng `polite` hoặc `alert`.
- [ ] Notification link kiểm tra quyền truy cập.

---

## 13. Loading states

- [ ] Không để màn hình trắng khi tải Dashboard.
- [ ] KPI dùng skeleton đúng kích thước.
- [ ] Chart giữ chiều cao cố định.
- [ ] Table dùng 6–8 skeleton rows.
- [ ] Skeleton không được screen reader đọc từng phần.
- [ ] Chỉ vùng đang tải bị thay đổi.
- [ ] Filter refresh không làm layout nhảy.
- [ ] Tác vụ trên 300ms có feedback.
- [ ] Tác vụ dài có mô tả tiến độ.
- [ ] Không dùng spinner toàn màn hình cho tải cục bộ.

---

## 14. Empty states

- [ ] Mỗi widget có empty state theo ngữ cảnh.
- [ ] Phân biệt không có dữ liệu và không có kết quả lọc.
- [ ] Có action tiếp theo khi phù hợp.
- [ ] Empty state giữ min-height card.
- [ ] Không dùng dấu `—` thay cho giải thích.
- [ ] Không hiển thị thông báo tiêu cực khi trạng thái thực tế là tốt, ví dụ không có cảnh báo tồn kho.

---

## 15. Error handling

- [ ] API lỗi một widget không làm sập toàn trang.
- [ ] Error message nói rõ vấn đề và hành động.
- [ ] Có nút `Thử lại` khi phù hợp.
- [ ] Không hiển thị stack trace cho người dùng.
- [ ] Toast không phải nơi duy nhất chứa lỗi form quan trọng.
- [ ] Error state giữ kích thước component.
- [ ] Dữ liệu cũ được đánh dấu stale thay vì xóa ngay nếu có thể dùng an toàn.
- [ ] Mutation thất bại khôi phục UI đúng.
- [ ] Lỗi được log kèm request ID hoặc correlation ID.

---

## 16. Permissions

- [ ] API kiểm tra quyền độc lập frontend.
- [ ] Menu sinh theo permission.
- [ ] KPI tài chính chỉ hiển thị cho vai trò phù hợp.
- [ ] Dữ liệu được giới hạn theo chi nhánh hoặc phạm vi người dùng.
- [ ] Action bị cấm được ẩn hoặc disabled có giải thích.
- [ ] Người dùng không thể truy cập route trực tiếp bằng URL nếu không có quyền.
- [ ] Export tuân thủ phạm vi dữ liệu.
- [ ] Notification không dẫn đến entity ngoài quyền truy cập.
- [ ] Permission loading không hiển thị tạm toàn bộ menu.

---

## 17. Responsive

Kiểm tra các viewport:

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

Checklist:

- [ ] Không có horizontal scroll toàn trang từ 1024px.
- [ ] Sidebar đổi trạng thái đúng breakpoint.
- [ ] Header không bị tràn.
- [ ] KPI chuyển cột đúng.
- [ ] Chart không bị cắt legend hoặc tooltip.
- [ ] Table scroll trong wrapper riêng.
- [ ] Product widget ẩn metadata theo ưu tiên.
- [ ] Notification chuyển drawer hoặc sheet khi cần.
- [ ] Modal không vượt viewport.
- [ ] Touch target tối thiểu 44px trên thiết bị touch.
- [ ] Không có action chỉ xuất hiện bằng hover.

---

## 18. Accessibility

- [ ] Có skip link.
- [ ] Semantic landmark đúng.
- [ ] Heading hierarchy logic.
- [ ] Text contrast đạt WCAG AA.
- [ ] Không chỉ dùng màu cho trạng thái.
- [ ] Mọi chức năng chính dùng được bằng keyboard.
- [ ] Focus indicator rõ ràng.
- [ ] Modal và drawer có focus trap.
- [ ] Focus trả về trigger sau khi đóng overlay.
- [ ] Icon-only button có accessible name.
- [ ] Chart có summary hoặc data table thay thế.
- [ ] Table có caption và header association.
- [ ] Form error liên kết đúng field.
- [ ] Toast dùng live region phù hợp.
- [ ] Zoom 200% vẫn dùng được.
- [ ] Reduced motion hoạt động.
- [ ] Forced colors hoặc high contrast vẫn nhận biết trạng thái.
- [ ] Không có lỗi axe mức serious hoặc critical.

---

## 19. Motion

- [ ] Hover button dưới 160ms.
- [ ] Dropdown mở dưới 200ms.
- [ ] Sidebar collapse không làm text wrap.
- [ ] Modal và drawer không bounce.
- [ ] Chart không animate lại vô lý.
- [ ] Real-time update chỉ highlight vùng liên quan.
- [ ] Không count-up mọi KPI sau mỗi refresh.
- [ ] Skeleton shimmer nhẹ.
- [ ] Reduced motion tắt animation không cần thiết.
- [ ] Motion dùng chủ yếu transform và opacity.
- [ ] Không có nhấp nháy nhanh hoặc animation vô hạn gây nhiễu.

---

## 20. Data formatting

- [ ] Tiền tệ dùng VND nhất quán.
- [ ] Số hàng nghìn dùng dấu chấm.
- [ ] Phần thập phân dùng dấu phẩy.
- [ ] Ngày dùng `dd/mm/yyyy`.
- [ ] Thời gian dùng 24 giờ.
- [ ] Timezone rõ ràng nếu có nhiều khu vực.
- [ ] Compact number có tooltip giá trị đầy đủ.
- [ ] Không làm tròn dữ liệu kế toán trong báo cáo chi tiết.
- [ ] Giá trị null, zero và unavailable được phân biệt.

---

## 21. API integration

- [ ] API contract có type rõ ràng.
- [ ] Request hỗ trợ date range và branch scope.
- [ ] Cache key bao gồm đầy đủ filter.
- [ ] Mutation có trạng thái pending, success và error.
- [ ] Request được hủy khi component unmount hoặc filter đổi nhanh.
- [ ] Không tạo request trùng không cần thiết.
- [ ] Pagination hoặc limit được dùng cho danh sách.
- [ ] Server trả semantic trend nếu có thể.
- [ ] `updatedAt` có trong dữ liệu cần cảnh báo stale.
- [ ] API error được normalize.

---

## 22. Performance

- [ ] Dashboard shell render nhanh.
- [ ] Widget dưới fold được lazy-load khi phù hợp.
- [ ] Chart library không bị bundle nhiều lần.
- [ ] Ảnh thumbnail dùng kích thước phù hợp.
- [ ] Không tải ảnh full-size cho bảng.
- [ ] Không render hàng trăm row trong Dashboard widget.
- [ ] Expensive calculation được memoize hợp lý.
- [ ] Filter typing có debounce.
- [ ] Search result giới hạn số lượng.
- [ ] Real-time event có batching hoặc rate limiting.
- [ ] Không có layout shift đáng kể khi dữ liệu tải xong.
- [ ] Motion đạt cảm giác mượt trên laptop phổ thông.

Mục tiêu tham khảo:

```text
LCP < 2,5 giây
CLS < 0,1
INP < 200ms
```

---

## 23. Security và privacy

- [ ] Không đưa secret hoặc token vào frontend bundle.
- [ ] Session hết hạn được xử lý rõ ràng.
- [ ] Không log dữ liệu nhạy cảm vào console.
- [ ] Export chỉ chứa dữ liệu được phép.
- [ ] Search không trả dữ liệu ngoài phạm vi quyền.
- [ ] HTML từ API được sanitize nếu phải render.
- [ ] File upload kiểm tra loại và dung lượng.
- [ ] Các mutation quan trọng có CSRF hoặc cơ chế bảo vệ phù hợp kiến trúc.
- [ ] Audit log có cho thao tác nhạy cảm nếu nghiệp vụ yêu cầu.

---

## 24. Testing

### Unit tests

- [ ] Format tiền, số và ngày.
- [ ] Status mapping.
- [ ] Permission helper.
- [ ] KPI trend semantic.
- [ ] Filter serialization.

### Component tests

- [ ] KPI loading, success, empty và error.
- [ ] Table sorting và row actions.
- [ ] Sidebar collapse.
- [ ] Notification read state.
- [ ] Modal focus management.

### Integration tests

- [ ] Filter cập nhật KPI và chart.
- [ ] Branch scope giới hạn dữ liệu.
- [ ] Quick view order tải đúng entity.
- [ ] Mutation lỗi rollback UI.
- [ ] Session timeout flow.

### End-to-end tests

- [ ] Đăng nhập và mở Dashboard.
- [ ] Tìm đơn hàng.
- [ ] Thay đổi date range.
- [ ] Xem đơn hàng chi tiết.
- [ ] Xử lý cảnh báo tồn kho.
- [ ] Xuất báo cáo.
- [ ] Đọc và đánh dấu thông báo.
- [ ] Đăng xuất.

---

## 25. Browser testing

Kiểm tra tối thiểu:

- [ ] Chrome phiên bản hiện hành.
- [ ] Edge phiên bản hiện hành.
- [ ] Safari phiên bản hiện hành.
- [ ] Firefox phiên bản hiện hành.

Kiểm tra thêm:

- [ ] macOS trackpad.
- [ ] Windows mouse và keyboard.
- [ ] Touch device nếu hỗ trợ tablet.

---

## 26. Visual QA

- [ ] Màu thương hiệu đúng token.
- [ ] Card radius nhất quán.
- [ ] Shadow không quá đậm.
- [ ] Spacing theo 8pt Grid.
- [ ] Typography đúng scale.
- [ ] Icon cùng kích thước và stroke.
- [ ] Badge không thay đổi chiều cao giữa trạng thái.
- [ ] Card cùng hàng có chiều cao hợp lý.
- [ ] Không có text lệch baseline.
- [ ] Không có ảnh méo hoặc bị crop sai.
- [ ] Loading state gần với layout thật.
- [ ] Focus ring không bị cắt.

---

## 27. Content QA

- [ ] Không còn lorem ipsum.
- [ ] Không còn tên thương hiệu cũ.
- [ ] Dùng thống nhất `Cynca VLXD`.
- [ ] Thuật ngữ trạng thái đơn hàng nhất quán.
- [ ] Chính tả tiếng Việt đúng.
- [ ] Không trộn tiếng Anh không cần thiết.
- [ ] Error message dễ hiểu.
- [ ] Button dùng động từ rõ ràng.
- [ ] Tooltip ngắn và có giá trị.
- [ ] Empty state không gây hiểu nhầm.

---

## 28. Production readiness

- [ ] Environment variables được cấu hình.
- [ ] Production API endpoint đúng.
- [ ] Error monitoring hoạt động.
- [ ] Performance monitoring hoạt động nếu có.
- [ ] Source map được xử lý theo chính sách bảo mật.
- [ ] Analytics không thu thập dữ liệu nhạy cảm.
- [ ] Cache và revalidation phù hợp.
- [ ] Fallback khi dịch vụ chart hoặc ảnh lỗi.
- [ ] Feature flag cho chức năng chưa hoàn thiện.
- [ ] Rollback plan có sẵn.

---

## 29. UAT theo vai trò

### Admin

- [ ] Xem được toàn hệ thống.
- [ ] Chuyển được chi nhánh.
- [ ] Xem KPI tài chính.
- [ ] Truy cập cài đặt và phân quyền.

### Kinh doanh

- [ ] Chỉ thấy dữ liệu được phân công.
- [ ] Xem và xử lý đơn hàng.
- [ ] Xem khách hàng phù hợp.
- [ ] Không thấy cấu hình hệ thống ngoài quyền.

### Kho

- [ ] Xem cảnh báo tồn kho.
- [ ] Truy cập đơn chờ xuất.
- [ ] Tạo phiếu nhập nếu có quyền.
- [ ] Không thấy lợi nhuận nếu bị giới hạn.

### Kế toán

- [ ] Xem thanh toán và công nợ.
- [ ] Xuất báo cáo đúng phạm vi.
- [ ] Không thay đổi tồn kho nếu không có quyền.

---

## 30. Release checklist

Trước khi phát hành:

- [ ] Product owner duyệt nghiệp vụ.
- [ ] Designer duyệt visual QA.
- [ ] Frontend review code.
- [ ] Backend xác nhận API contract.
- [ ] QA hoàn thành regression test.
- [ ] Accessibility test hoàn thành.
- [ ] Performance test đạt ngưỡng.
- [ ] Security review các luồng nhạy cảm.
- [ ] UAT hoàn thành theo vai trò.
- [ ] Không còn blocker hoặc critical bug.
- [ ] Release notes đã chuẩn bị.
- [ ] Có kế hoạch rollback.

---

## 31. Bug severity

### Blocker

- Không đăng nhập được.
- Dashboard không tải.
- Sai dữ liệu tài chính nghiêm trọng.
- Người dùng xem được dữ liệu ngoài quyền.

### Critical

- Không xử lý được đơn hàng.
- Export sai phạm vi dữ liệu.
- Mutation quan trọng ghi sai trạng thái.
- Mất dữ liệu form hoặc nghiệp vụ.

### Major

- Một widget không tải.
- Responsive vỡ ở viewport chính.
- Keyboard flow chính không dùng được.

### Minor

- Lệch spacing nhỏ.
- Tooltip sai vị trí.
- Animation chưa mượt nhưng không cản tác vụ.

Không phát hành khi còn bug Blocker hoặc Critical.

---

## 32. Handoff cho Codex hoặc AI coding agent

Prompt triển khai phải nêu rõ:

- Đọc toàn bộ file trong `docs/cynca-vlxd/dashboard/`.
- Không tự thay đổi màu, spacing và breakpoint.
- Dùng token trong `13-Tailwind-Tokens.md`.
- Triển khai đầy đủ loading, empty, error và permission state.
- Không dùng dữ liệu hardcode trong production code.
- Giữ semantic HTML và keyboard behavior.
- Không thêm thư viện ngoài nếu không cần.
- Chạy lint, typecheck và test trước khi hoàn thành.

AI phải báo rõ:

- File đã tạo hoặc sửa.
- Quyết định kiến trúc chính.
- Test đã chạy.
- Hạng mục chưa hoàn thành.

---

## 33. Final sign-off

| Nhóm | Người duyệt | Trạng thái | Ngày |
|---|---|---|---|
| Product |  | Chưa duyệt |  |
| Design |  | Chưa duyệt |  |
| Frontend |  | Chưa duyệt |  |
| Backend |  | Chưa duyệt |  |
| QA |  | Chưa duyệt |  |
| Accessibility |  | Chưa duyệt |  |
| Security |  | Chưa duyệt |  |

---

## 34. Kết luận

Dashboard chỉ được coi là hoàn chỉnh khi giao diện, dữ liệu, trạng thái, phân quyền, accessibility, responsive và hiệu năng cùng đạt yêu cầu. Checklist này phải được dùng trong code review, QA, UAT và trước mỗi lần phát hành có thay đổi lớn tới Dashboard.

Phần Design Specification chính của Dashboard kết thúc tại đây.

Tài liệu bổ sung đề xuất tiếp theo:

```text
README.md
15-AI-Implementation-Prompt.md
16-Data-Contracts.md
17-Visual-QA-Cases.md
```