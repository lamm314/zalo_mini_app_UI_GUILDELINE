# Cynca VLXD Admin Dashboard — Mobile & Tablet Operations Specification

> Phiên bản: 1.0  
> Phạm vi: Trải nghiệm vận hành Dashboard trên điện thoại và máy tính bảng  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Warehouse Operations, Sales, Customer Service, QA và AI coding agent  
> Tài liệu liên quan: `02-Layout.md`, `12-Accessibility.md`, `20-Frontend-Architecture.md`, `27-Role-Based-Dashboard.md`, `34-Warehouse-Dashboard.md`, `36-Customer-Service-Dashboard.md`

---

## 1. Mục tiêu

Mobile & Tablet Operations giúp người dùng Cynca VLXD xử lý nhanh các tác vụ vận hành tại kho, cửa hàng, chi nhánh hoặc khi di chuyển mà không cần sử dụng đầy đủ giao diện desktop.

Hệ thống phải hỗ trợ:

- Xem KPI và cảnh báo quan trọng.
- Xử lý tác vụ được phân công.
- Tìm đơn hàng, sản phẩm, khách hàng và ticket.
- Quét mã vạch hoặc QR khi nghiệp vụ yêu cầu.
- Xác nhận các bước pick, pack, handover, callback và follow-up.
- Nhận thông báo và cập nhật trạng thái gần real-time.
- Hoạt động ổn định với mạng yếu hoặc gián đoạn ngắn.
- Không ép toàn bộ Dashboard desktop vào màn hình nhỏ.
- Không cho mobile trở thành đường tắt bỏ qua permission, xác nhận hoặc audit.

---

## 2. Phạm vi thiết bị

### Mobile

```text
360 × 800
375 × 812
390 × 844
430 × 932
```

### Tablet portrait

```text
768 × 1024
820 × 1180
```

### Tablet landscape

```text
1024 × 768
1180 × 820
```

### Rugged device

Thiết bị kho có thể có:

- Màn hình 5–8 inch.
- Camera hoặc scanner tích hợp.
- Độ phân giải thấp hơn.
- Găng tay hoặc thao tác một tay.
- Kết nối Wi-Fi không ổn định.

Thiết kế cần kiểm tra thực tế trên các nhóm thiết bị này nếu được triển khai.

---

## 3. Nguyên tắc thiết kế

1. Ưu tiên tác vụ, không ưu tiên mật độ dữ liệu.
2. Một màn hình chỉ nên có một primary action rõ ràng.
3. Touch target tối thiểu 44 × 44px.
4. Không dùng hover như cách duy nhất để hiển thị thông tin.
5. Không dùng bảng desktop quá rộng trên mobile.
6. Không ẩn cảnh báo quan trọng chỉ vì thiếu không gian.
7. Không tự động hoàn tất nghiệp vụ sau một thao tác quét đơn lẻ.
8. Không báo thành công trước khi backend xác nhận.
9. Không mở rộng permission hoặc scope trên thiết bị di động.
10. Mọi trạng thái offline, pending và sync phải hiển thị rõ.

---

## 4. Chiến lược responsive

Không thu nhỏ toàn bộ giao diện desktop. Sử dụng ba chiến lược:

### Reflow

Các widget chuyển từ nhiều cột về một cột.

### Reduce

Giảm metadata phụ, giữ nội dung chính và action.

### Replace

Thay table/chart phức tạp bằng card list, summary hoặc drill-down.

Ví dụ:

```text
Desktop Order Table
→ Tablet compact table
→ Mobile Order Card List
```

---

## 5. Information hierarchy trên mobile

Thứ tự ưu tiên:

```text
1. Tác vụ quá hạn hoặc critical
2. Việc được phân công hôm nay
3. Primary action
4. Trạng thái cần theo dõi
5. Search / scan
6. KPI tóm tắt
7. Lịch sử gần đây
8. Báo cáo thứ cấp
```

Chart tổng hợp hoặc dữ liệu lịch sử dài nên đứng sau tác vụ.

---

## 6. Mobile navigation

Cấu trúc đề xuất:

```text
Top App Bar
Main Content
Bottom Navigation hoặc Primary Action Bar
Contextual Sheet/Drawer
```

Bottom navigation tối đa 4–5 mục:

- Tổng quan.
- Tác vụ.
- Tìm kiếm/Quét.
- Thông báo.
- Tài khoản.

Không đưa toàn bộ Sidebar desktop vào bottom navigation.

---

## 7. Tablet navigation

### Portrait

- Sidebar dạng drawer.
- Top bar giữ search và notification.
- Có thể dùng bottom task bar trong workflow kho.

### Landscape

- Sidebar thu gọn 64–72px hoặc drawer.
- Nội dung có thể dùng grid 2 cột.
- Table compact được phép nếu cột quan trọng vừa đủ.

---

## 8. Top App Bar

Thành phần:

- Back hoặc Menu.
- Page title.
- Scope label ngắn.
- Search hoặc scan action.
- Notification.
- More menu.

Title dài phải truncate có kiểm soát.

Không nhồi date filter, branch filter, export và nhiều action cùng một hàng trên mobile.

---

## 9. Mobile filter pattern

Filters mở trong bottom sheet hoặc full-screen sheet.

Cấu trúc:

```text
Filter Header
Filter Groups
Applied Filter Summary
Reset
Apply
```

Yêu cầu:

- Nút Apply sticky dưới cùng.
- Hiển thị số filter đang áp dụng.
- Không đóng sheet khi chọn từng option nếu còn nhiều bước.
- Scope filter chỉ hiển thị giá trị backend cho phép.

---

## 10. KPI trên mobile

KPI nên dùng:

- 1 cột hoặc 2 cột.
- Value lớn.
- Label ngắn.
- Trend và trạng thái ngắn gọn.
- Không chứa sparkline phức tạp nếu không cần.

KPI critical có thể chiếm toàn chiều rộng.

Không hiển thị quá 4 KPI trước fold trên mobile.

---

## 11. Chart trên mobile

Chỉ giữ chart có giá trị hành động cao.

Yêu cầu:

- Chiều cao 220–300px.
- Tối đa 1–2 series mặc định.
- Có summary text.
- Tooltip hỗ trợ tap.
- Có bảng dữ liệu thay thế nếu cần.
- Label trục tối giản.

Không dùng heatmap, multi-axis hoặc nhiều series trên mobile nếu không có chế độ chi tiết riêng.

---

## 12. Mobile card list

Thay table bằng card khi:

- Có nhiều cột.
- Cần quick actions.
- Nội dung có trạng thái, SLA hoặc priority.

Card anatomy:

- Primary identifier.
- Status/priority.
- 2–4 metadata chính.
- Deadline hoặc age.
- Primary action.
- More actions.

Không đưa tất cả cột desktop vào card.

---

## 13. Swipe actions

Có thể dùng swipe cho action an toàn, ví dụ:

- Mark read.
- Snooze.
- Open quick actions.

Không dùng swipe trực tiếp cho:

- Hủy đơn.
- Hoàn tiền.
- Điều chỉnh tồn.
- Xóa dữ liệu.
- Hoàn tất task quan trọng.

Mọi swipe action phải có alternative bằng button/menu.

---

## 14. Sticky primary action

Trong workflow, action chính có thể sticky dưới cùng:

```text
Bắt đầu lấy hàng
Hoàn tất đóng gói
Gọi lại khách hàng
Xác nhận đơn
Gửi phản hồi
```

Yêu cầu:

- Không che nội dung.
- Respect safe-area inset.
- Disabled state giải thích lý do.
- Loading state không thay đổi vị trí.
- Không có nhiều primary buttons cùng lúc.

---

## 15. Warehouse mobile workflow

Luồng tham chiếu:

```text
Mở My Tasks
→ Chọn Pick Task
→ Xem danh sách dòng hàng
→ Quét SKU
→ Xác nhận số lượng
→ Báo thiếu hàng nếu có
→ Hoàn tất lấy hàng
→ Chuyển sang đóng gói
```

Mỗi bước phải có trạng thái rõ và hỗ trợ quay lại an toàn.

---

## 16. Pick task detail

Nội dung:

- Task code.
- Order code.
- Priority.
- SLA countdown.
- Bin/zone.
- Dòng hàng.
- Số lượng cần lấy/đã lấy.
- Scan action.
- Exception action.

Không hiển thị giá bán hoặc thông tin khách hàng nếu không cần cho picking.

---

## 17. Scan experience

Yêu cầu:

- Chỉ xin quyền camera khi bắt đầu scan.
- Có hướng dẫn đặt mã trong khung.
- Phản hồi âm thanh/rung nhẹ nếu được phép.
- Hiển thị mã vừa quét.
- Xử lý mã không hợp lệ.
- Xử lý duplicate scan.
- Có nhập mã thủ công.
- Không tự hoàn tất toàn task sau một mã quét.

---

## 18. Scan states

```text
Ready
Scanning
Matched
Not Found
Wrong SKU
Duplicate
Quantity Completed
Permission Denied
Camera Unavailable
```

Mỗi trạng thái cần text rõ và action tiếp theo.

Không chỉ dùng âm thanh hoặc màu.

---

## 19. Quantity input

Phù hợp thiết bị cảm ứng:

- Nút tăng/giảm lớn.
- Numeric keyboard.
- Unit rõ.
- Min/max validation.
- Không cho giá trị âm nếu nghiệp vụ không cho phép.
- Cảnh báo khi vượt số lượng yêu cầu.

Không phụ thuộc vào spinner input mặc định của trình duyệt.

---

## 20. Exception handling tại kho

Exception types:

```text
Không tìm thấy hàng
Thiếu số lượng
Sai SKU
Hàng hư hỏng
Sai vị trí
Không quét được mã
Khác
```

Luồng:

```text
Chọn lý do
→ Nhập ghi chú hoặc ảnh nếu cần
→ Xác nhận
→ Backend tạo exception
→ Task cập nhật trạng thái
```

Không cho nhân viên tự điều chỉnh tồn để xử lý exception thiếu quyền.

---

## 21. Photo attachment

Nếu workflow cho phép chụp ảnh:

- Chỉ mở camera khi người dùng chọn.
- Preview trước khi upload.
- Cho chụp lại.
- Nén hợp lý.
- Hiển thị progress.
- Có retry.
- Không lưu ảnh vào gallery nếu không cần.
- Xóa metadata nhạy cảm nếu policy yêu cầu.

---

## 22. Sales mobile workflow

Ưu tiên:

- My Orders.
- Customer Follow-up.
- Create Order.
- Product Search.
- Product availability.
- Call/Message actions.

Không đưa Team Performance phức tạp vào màn hình đầu của Sales Staff mobile.

---

## 23. Customer follow-up mobile card

Thông tin:

- Customer/company name.
- Reason.
- Due time.
- Last interaction.
- Related order/quote.
- Call/message/view actions theo permission.

PII chỉ hiển thị khi cần xử lý và backend cho phép.

---

## 24. Customer Service mobile workflow

Ưu tiên:

- My Priority Queue.
- Ticket near/breached SLA.
- Customer reply.
- Callback queue.
- Failed delivery cases.

Ticket detail cần:

- Summary.
- Conversation timeline.
- Reply box.
- Internal note.
- Status/action bar.

Không tự đóng ticket sau khi gửi phản hồi.

---

## 25. Finance mobile fallback

Finance mobile chỉ nên hỗ trợ:

- Data status.
- KPI chính.
- Công nợ đến hạn/quá hạn.
- Reconciliation exceptions.
- Refund requests cần review.

Các thao tác phê duyệt giá trị lớn có thể yêu cầu desktop hoặc re-authentication tùy policy.

Không cố hiển thị toàn bộ bảng tài chính nhiều cột trên mobile.

---

## 26. Branch Manager mobile fallback

Ưu tiên:

1. Branch Health.
2. Critical risks.
3. KPI.
4. Đơn quá SLA.
5. Inventory risk.
6. Tasks cần giao.

Các báo cáo chi tiết mở sang trang responsive hoặc yêu cầu desktop nếu quá phức tạp.

---

## 27. Command Center trên mobile

Mở full-screen.

Hỗ trợ:

- Search entity.
- Search command.
- Scan shortcut.
- Recent entities an toàn.
- Role-based suggestions.

Keyboard mobile không được che active result.

Không lưu phone/email query nhạy cảm trong recent searches.

---

## 28. Notification Center trên mobile

- Full-screen sheet/page.
- Tabs tối giản.
- Swipe mark read nếu có alternative.
- Detail mở full-screen.
- Primary action sticky khi phù hợp.

Không tự mark notification resolved khi người dùng mở.

---

## 29. Offline và mạng yếu

Phân biệt:

```text
Online
Slow connection
Offline
Sync pending
Sync failed
```

UI phải hiển thị trạng thái kết nối nhẹ nhưng rõ.

Không báo dữ liệu mới khi chỉ đang hiển thị cache cũ.

---

## 30. Offline-capable actions

Chỉ queue offline khi:

- Action idempotent hoặc có idempotency key.
- Có conflict strategy.
- Không phải action tài chính/nhạy cảm cao.
- Người dùng hiểu action đang pending.

Ví dụ có thể cân nhắc:

- Ghi nhận scan nội bộ.
- Draft note.
- Mark read.

Không queue offline mặc định cho:

- Refund approval.
- Stock adjustment.
- Order cancellation.
- Permission update.

---

## 31. Pending sync model

```ts
export type PendingMobileAction = {
  id: string;
  actionType: string;
  entityType: string;
  entityId: string;
  createdAt: string;
  status: "queued" | "syncing" | "synced" | "failed" | "conflict";
  retryCount: number;
  idempotencyKey: string;
};
```

Không lưu payload nhạy cảm dư thừa trong local storage.

---

## 32. Conflict handling

Ví dụ conflict:

- Task đã được người khác hoàn tất.
- Order status đã thay đổi.
- Inventory quantity khác lúc offline.
- Ticket đã được reassigned.

UI phải:

- Dừng retry tự động.
- Hiển thị thay đổi mới nhất.
- Cho reload hoặc mở detail.
- Không ghi đè âm thầm.

---

## 33. Connection recovery

Khi online lại:

- Refetch dữ liệu quan trọng.
- Sync queue theo thứ tự.
- Deduplicate.
- Hiển thị kết quả.
- Không phát nhiều toast cho từng item.
- Có trang `Đang chờ đồng bộ` nếu cần.

---

## 34. Local storage policy

Có thể lưu:

- Theme/density.
- Viewport preference.
- Draft không nhạy cảm.
- Pending action metadata tối thiểu.

Không lưu:

- Token trong localStorage nếu architecture không cho phép.
- PII đầy đủ.
- Payment details.
- Secret.
- Toàn bộ inventory/customer dataset.

---

## 35. Authentication trên mobile

Yêu cầu:

- Session timeout rõ.
- Re-authentication cho action nhạy cảm.
- Biometrics chỉ khi platform và policy hỗ trợ.
- Không hiển thị secret trong app switcher preview nếu có thể kiểm soát.
- Logout xóa cache nhạy cảm.

---

## 36. Camera và device permissions

Permission có thể gồm:

- Camera.
- Notification.
- Location nếu nghiệp vụ thật sự cần.
- File access.

Quy tắc:

- Xin đúng lúc.
- Giải thích lý do.
- Có fallback.
- Không xin permission hàng loạt khi mở app.
- Không chặn toàn bộ app nếu một permission bị từ chối.

---

## 37. Haptics và âm thanh

Có thể dùng cho:

- Scan thành công.
- Scan lỗi.
- Critical warning.

Yêu cầu:

- Có visual/text feedback song song.
- Respect system settings.
- Không phát âm thanh liên tục.
- Không dùng cho mọi button tap.

---

## 38. Orientation

### Mobile

Ưu tiên portrait.

### Tablet

Hỗ trợ portrait và landscape.

Workflow scan hoặc table compact có thể tối ưu landscape, nhưng không bắt buộc người dùng xoay thiết bị nếu không cần.

Không mất form data khi orientation thay đổi.

---

## 39. Safe areas

Phải xử lý:

- Notch.
- Home indicator.
- Browser toolbar.
- Virtual keyboard.

Sticky footer dùng `env(safe-area-inset-bottom)` hoặc giải pháp tương đương.

---

## 40. Virtual keyboard

Yêu cầu:

- Input đang focus luôn nhìn thấy.
- Sticky action không bị che.
- Numeric field dùng numeric keyboard.
- Enter/Next chuyển field hợp lý.
- Không đóng form khi keyboard xuất hiện.

---

## 41. Accessibility

- Touch target tối thiểu 44px.
- Text hỗ trợ Dynamic Type/zoom.
- Focus rõ khi dùng keyboard trên tablet.
- Screen reader label đầy đủ.
- Không phụ thuộc màu, rung hoặc âm thanh.
- Swipe có button alternative.
- Countdown có text dễ hiểu.
- Reduced motion được tôn trọng.
- Landscape và zoom không làm mất action.

---

## 42. Performance budgets

Mục tiêu tham khảo:

```text
Initial shell p75 < 2,5s trên 4G tốt
Tap response < 100ms
List scroll ổn định 50–60fps khi có thể
Search p95 < 1s
Scan match p95 < 1s
Task mutation feedback < 1,5s
```

Tối ưu:

- Lazy-load chart và module hiếm dùng.
- Resize/compress ảnh.
- Virtualize list dài.
- Prefetch task detail hợp lý.
- Không tải toàn bộ dữ liệu desktop.

---

## 43. Data usage

- Không tải ảnh lớn nếu thumbnail đủ dùng.
- Không refetch liên tục khi app background.
- Batch realtime updates.
- Tạm dừng polling khi tab/app không active.
- Hiển thị data freshness thay vì polling quá nhanh.

---

## 44. Error states

### Scan error

```text
Không nhận diện được mã. Hãy thử lại hoặc nhập thủ công.
```

### Mutation error

```text
Không thể cập nhật tác vụ. Dữ liệu của bạn chưa được ghi nhận.
```

### Offline action

```text
Thao tác đang chờ đồng bộ khi có mạng.
```

Không hiển thị generic `Có lỗi xảy ra` cho mọi trường hợp.

---

## 45. Loading states

- Card skeleton.
- Task list skeleton.
- Inline button loading.
- Scan initializing.
- Upload progress.
- Sync queue progress.

Không dùng full-screen spinner cho mọi chuyển trang.

---

## 46. Empty states

### Không có task

```text
Không có tác vụ nào đang chờ xử lý.
```

### Không có kết quả scan

```text
Không tìm thấy sản phẩm phù hợp với mã này.
```

### Không có notification

```text
Bạn không có thông báo mới.
```

### Offline chưa có cache

```text
Không có dữ liệu đã lưu để hiển thị khi ngoại tuyến.
```

---

## 47. Security và privacy

- Backend enforce permission, scope và available actions.
- Không trả dữ liệu ngoài branch/warehouse/assigned scope.
- Không lưu PII đầy đủ lâu dài trên thiết bị.
- Upload ảnh dùng signed URL và kiểm tra file.
- Không cho scan endpoint truy cập entity ngoài scope.
- Không queue offline action nhạy cảm cao.
- Clear cache khi logout.
- Error logs phải redact payload.

---

## 48. Analytics events

```text
mobile.dashboard.viewed
mobile.task.opened
mobile.task.action_started
mobile.task.action_completed
mobile.scan.started
mobile.scan.matched
mobile.scan.failed
mobile.offline.entered
mobile.sync.completed
mobile.sync.failed
```

Không gửi barcode, SKU, customer name, phone, order code hoặc payload task vào analytics.

---

## 49. Observability

Theo dõi:

- Device category.
- Network type bucket nếu platform cho phép và privacy policy phù hợp.
- Mobile API latency.
- Scan success/failure.
- Camera permission denial.
- Offline queue size.
- Sync conflicts.
- Mutation failure.
- Crash/render error.
- Upload failure.

---

## 50. Testing

### Unit

- Responsive mapping.
- Pending action state.
- Sync retry.
- Conflict resolution mapping.
- Scan result mapping.
- Safe-area helpers.

### Component

- Mobile card.
- Bottom sheet.
- Sticky action bar.
- Scan states.
- Quantity input.
- Offline banner.
- Pending sync list.

### Integration

- Warehouse task scan flow.
- Offline → online sync.
- Duplicate scan.
- Conflict after reconnect.
- Permission-limited result.
- Camera denied fallback.

### E2E

- Mobile Sales Staff xử lý follow-up.
- Warehouse Staff bắt đầu pick task.
- Quét SKU đúng/sai.
- Báo thiếu hàng.
- Mất mạng giữa workflow.
- Kết nối lại và sync.
- Agent xử lý callback.
- Tablet landscape kiểm tra queue.

---

## 51. Visual QA

Baseline:

```text
Mobile Dashboard — 390 × 844
Tablet Portrait — 768 × 1024
Tablet Landscape — 1024 × 768
Warehouse Task List
Pick Task Detail
Scan Ready
Scan Matched
Scan Wrong SKU
Quantity Input
Offline Banner
Pending Sync
Conflict State
Customer Follow-up Card
Ticket Mobile Detail
Bottom Filter Sheet
```

Kiểm tra:

- Safe areas.
- Keyboard overlap.
- Long Vietnamese content.
- Touch targets.
- Sticky action.
- Orientation change.
- Dark/bright warehouse environment nếu có device test.

---

## 52. AI coding agent requirements

AI coding agent phải:

- Không chỉ thu nhỏ giao diện desktop.
- Không dùng table rộng làm trải nghiệm chính trên mobile.
- Không phụ thuộc hover hoặc swipe duy nhất.
- Không tự hoàn tất task sau một scan.
- Không queue offline action nhạy cảm thiếu policy.
- Không báo thành công trước backend confirmation.
- Không lưu PII hoặc secret trong local storage.
- Bổ sung test cho camera denied, offline sync, conflict và safe area.
- Báo rõ hành vi mobile, tablet, offline và phần backend còn thiếu.

---

## 53. Acceptance checklist

- [ ] Có chiến lược mobile/tablet riêng, không chỉ shrink desktop.
- [ ] Có mobile và tablet navigation rõ ràng.
- [ ] Có filter sheet và card list.
- [ ] KPI/chart được tối giản phù hợp.
- [ ] Có sticky primary action an toàn.
- [ ] Có Warehouse pick/pack workflow.
- [ ] Có scan states và manual fallback.
- [ ] Có quantity input và exception flow.
- [ ] Có Sales, CSKH, Finance và Branch mobile fallback.
- [ ] Có Command và Notification Center trên mobile.
- [ ] Có offline, pending sync và conflict handling.
- [ ] Có device permission strategy.
- [ ] Có safe-area, keyboard và orientation handling.
- [ ] Accessibility và touch target đạt yêu cầu.
- [ ] Security và privacy được backend enforce.
- [ ] Analytics, observability, testing và Visual QA đầy đủ.

---

## 54. Những điều không được làm

- Không ép toàn bộ Dashboard desktop vào mobile.
- Không dùng hover làm interaction bắt buộc.
- Không dùng swipe làm action duy nhất.
- Không cho destructive action chạy bằng gesture đơn giản.
- Không tự hoàn tất task chỉ sau một lần quét.
- Không báo sync thành công khi đang offline.
- Không queue refund, permission hoặc stock adjustment nhạy cảm mặc định.
- Không lưu PII, payment data hoặc secret lâu dài trên thiết bị.
- Không xin toàn bộ device permissions khi mở app.
- Không để keyboard hoặc safe area che primary action.

---

## 55. Kết luận

Mobile & Tablet Operations của Cynca VLXD phải tập trung vào các tác vụ ngắn, rõ và có giá trị vận hành cao. Thiết kế cần ưu tiên thao tác cảm ứng, quét mã, mạng yếu, trạng thái đồng bộ và phạm vi quyền; đồng thời phải tránh biến mobile thành bản desktop thu nhỏ hoặc thành đường tắt bỏ qua workflow an toàn.

File tiếp theo đề xuất:

```text
README.md
45-Data-Import-Center.md
```