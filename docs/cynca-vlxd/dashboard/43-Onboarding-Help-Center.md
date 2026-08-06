# Cynca VLXD Admin Dashboard — Onboarding & Help Center Specification

> Phiên bản: 1.0  
> Phạm vi: Hướng dẫn làm quen, trợ giúp theo ngữ cảnh, tài liệu sử dụng và hỗ trợ người dùng Dashboard quản trị  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Customer Success, Training, QA và AI coding agent  
> Tài liệu liên quan: `12-Accessibility.md`, `20-Frontend-Architecture.md`, `26-Analytics-Event-Plan.md`, `27-Role-Based-Dashboard.md`, `42-Design-System-Playground.md`

---

## 1. Mục tiêu

Onboarding & Help Center giúp người dùng Cynca VLXD hiểu nhanh hệ thống, hoàn thành các thao tác quan trọng và tìm được hướng dẫn đúng lúc mà không phải rời khỏi Dashboard.

Hệ thống phải hỗ trợ:

- Onboarding theo vai trò và phạm vi công việc.
- Checklist làm quen cho người dùng mới.
- Product tour có thể bỏ qua và mở lại.
- Trợ giúp theo ngữ cảnh tại từng trang, widget và workflow.
- Trung tâm tài liệu có tìm kiếm.
- Hướng dẫn xử lý lỗi phổ biến.
- Video, hình minh họa và bài viết ngắn nếu cần.
- Theo dõi mức độ hoàn thành onboarding.
- Không ép người dùng xem tour dài hoặc cản trở công việc.
- Không hiển thị tài liệu về chức năng ngoài permission.

---

## 2. Phân biệt các lớp trợ giúp

### First-time Onboarding

Dành cho người dùng mới đăng nhập lần đầu.

### Role-based Checklist

Danh sách nhiệm vụ làm quen theo vai trò.

### Contextual Help

Trợ giúp ngay tại trang, field hoặc widget đang sử dụng.

### Help Center

Kho tài liệu tập trung, có search và category.

### Support Escalation

Kênh liên hệ khi tài liệu không giải quyết được vấn đề.

Không dùng một tour duy nhất cho tất cả vai trò.

---

## 3. Vai trò tham chiếu

Onboarding cần có biến thể cho:

- Super Admin / System Admin.
- Executive Viewer.
- Sales Manager.
- Sales Staff.
- Warehouse Manager.
- Warehouse Staff.
- Finance Manager.
- Accountant.
- Branch Manager.
- Customer Service Manager.
- Customer Service Agent.
- Report Viewer.
- Auditor.

Role chỉ dùng để chọn nội dung gợi ý; permission và scope vẫn quyết định nội dung thực tế được hiển thị.

---

## 4. Information architecture

```text
Onboarding & Help
├── Bắt đầu sử dụng
├── Checklist của tôi
├── Hướng dẫn theo vai trò
├── Hướng dẫn theo chức năng
├── Câu hỏi thường gặp
├── Khắc phục sự cố
├── Video & minh họa
├── Có gì mới
└── Liên hệ hỗ trợ
```

---

## 5. Entry points

Người dùng có thể mở trợ giúp từ:

- Biểu tượng Help trong Header.
- Link `Tìm hiểu thêm` trong empty/error state.
- Tooltip hoặc help icon cạnh field.
- Command Center.
- Onboarding card trên Dashboard.
- Trang `/help`.

Không dùng icon `?` thiếu accessible label.

---

## 6. First-login flow

Luồng đề xuất:

```text
Chào mừng
→ Xác nhận vai trò và phạm vi
→ Giới thiệu 3–5 chức năng chính
→ Checklist làm quen
→ Mở Dashboard
```

Yêu cầu:

- Có nút bỏ qua.
- Có thể quay lại.
- Không dài quá 3–5 bước chính.
- Không chặn đăng nhập nếu dịch vụ onboarding lỗi.
- Không yêu cầu nhập lại dữ liệu đã có trong hồ sơ.

---

## 7. Welcome screen

Nội dung:

- Tên người dùng.
- Vai trò hiện tại.
- Chi nhánh/kho/phạm vi.
- Mô tả ngắn về Dashboard.
- Thời gian hoàn thành ước tính.
- Nút `Bắt đầu` và `Bỏ qua`.

Ví dụ:

```text
Chào mừng bạn đến với Cynca VLXD.
Bạn đang truy cập với vai trò Quản lý kho tại Chi nhánh Hà Nội.
```

Không hiển thị permission kỹ thuật dài trên màn hình chào mừng.

---

## 8. Onboarding checklist

```ts
export type OnboardingChecklist = {
  id: string;
  roleKey: string;
  title: string;
  description?: string;
  items: OnboardingChecklistItem[];
  completionPercent: number;
  dismissedAt?: string;
  completedAt?: string;
};
```

```ts
export type OnboardingChecklistItem = {
  id: string;
  title: string;
  description?: string;
  actionLabel: string;
  actionType: "navigate" | "open_tour" | "open_article" | "complete_action";
  href?: string;
  requiredPermissions?: string[];
  status: "pending" | "in_progress" | "completed" | "skipped";
};
```

---

## 9. Checklist theo vai trò

### Sales Staff

- Xem đơn được phân công.
- Tìm sản phẩm.
- Mở hồ sơ khách hàng.
- Tạo hoặc xử lý đơn thử nghiệm nếu môi trường cho phép.
- Xem Notification Center.

### Warehouse Staff

- Xem Pick & Pack Queue.
- Tìm SKU.
- Mở tác vụ kho.
- Thử quét mã trong môi trường an toàn.
- Xem cảnh báo tồn.

### Accountant

- Xem công nợ đến hạn.
- Mở Reconciliation Queue.
- Xem trạng thái dữ liệu tài chính.
- Tạo báo cáo mẫu.

### Branch Manager

- Xem Branch Health.
- Mở Priority Attention.
- Drill-down sang Sales, Warehouse và Support.
- Export báo cáo chi nhánh.

---

## 10. Product tour

Tour chỉ nên tập trung vào chức năng quan trọng.

Mỗi bước gồm:

- Target element.
- Title.
- Description ngắn.
- Step number.
- Next/Back/Skip.
- Optional action.

Không tạo tour dài hàng chục bước.

Không mở tour tự động lại sau khi người dùng đã hoàn thành hoặc bỏ qua.

---

## 11. Tour contract

```ts
export type ProductTour = {
  key: string;
  name: string;
  requiredPermissions?: string[];
  routes: string[];
  version: number;
  steps: ProductTourStep[];
};
```

```ts
export type ProductTourStep = {
  id: string;
  targetKey: string;
  title: string;
  description: string;
  placement: "top" | "right" | "bottom" | "left" | "auto";
  optional?: boolean;
};
```

Dùng `targetKey` ổn định, không phụ thuộc selector CSS dễ thay đổi.

---

## 12. Tour behavior

- Tự scroll tới target nếu an toàn.
- Target không tồn tại thì bỏ qua bước optional hoặc dừng với thông báo rõ.
- Không khóa toàn bộ trang nếu người dùng cần thoát.
- Không che action quan trọng.
- Hỗ trợ keyboard.
- Focus phải đi theo tour hợp lý.
- Respect reduced motion.

---

## 13. Contextual Help

Các hình thức:

- Help icon cạnh label.
- Tooltip ngắn.
- Popover có ví dụ.
- Link tới bài viết chi tiết.
- Inline explanation trong empty/error state.
- Guided action trong workflow phức tạp.

Tooltip chỉ dành cho nội dung ngắn. Nội dung dài phải dùng popover, drawer hoặc article.

---

## 14. Help topic contract

```ts
export type HelpTopic = {
  id: string;
  slug: string;
  title: string;
  summary: string;
  category: string;
  contentType: "article" | "faq" | "video" | "troubleshooting" | "release_note";
  requiredPermissions?: string[];
  relatedRoutes?: string[];
  relatedFeatureKeys?: string[];
  tags: string[];
  locale: string;
  version: number;
  status: "draft" | "published" | "archived";
  updatedAt: string;
};
```

---

## 15. Help Center page

Route đề xuất:

```text
/help
```

Layout desktop:

```text
Help Header + Search
Popular Topics
Role-based Guides
Categories
Troubleshooting
What's New
Contact Support
```

Article detail:

```text
Breadcrumb
Title + Updated Time
Article Content
Related Topics
Was This Helpful
Contact Support
```

---

## 16. Categories

```text
Bắt đầu sử dụng
Dashboard
Đơn hàng
Sản phẩm
Kho
Tài chính
Khách hàng
Chăm sóc khách hàng
Báo cáo
Tài khoản & quyền
Cài đặt
Khắc phục sự cố
```

Không hiển thị category nếu người dùng không có quyền truy cập bất kỳ chức năng liên quan nào.

---

## 17. Search

Search hỗ trợ:

- Title.
- Summary.
- Tags.
- Feature name.
- Error code chuẩn hóa.
- Từ đồng nghĩa tiếng Việt.

Ví dụ:

```text
"không tải được báo cáo"
"đơn quá hạn"
"tồn âm"
"quên quyền truy cập"
```

Không tìm trong nội dung nội bộ dành cho role khác nếu user không có permission.

---

## 18. Search result anatomy

Mỗi kết quả gồm:

- Title.
- Summary.
- Category.
- Content type.
- Updated time.
- Matching snippet đã sanitize.

Không hiển thị raw HTML hoặc internal metadata.

---

## 19. FAQ

FAQ nên:

- Trả lời ngắn gọn.
- Có link tới hướng dẫn chi tiết.
- Nhóm theo module.
- Có owner nội dung.
- Có ngày cập nhật.

Ví dụ:

```text
Tại sao tôi không thấy dữ liệu của chi nhánh khác?
Làm thế nào để tải lại báo cáo đã hết hạn?
Tại sao số tồn kho hiển thị là dữ liệu cũ?
```

---

## 20. Troubleshooting guides

Cấu trúc chuẩn:

```text
Triệu chứng
Nguyên nhân có thể
Các bước kiểm tra
Cách xử lý
Khi nào cần liên hệ hỗ trợ
Thông tin cần cung cấp
```

Ví dụ chủ đề:

- Dashboard không tải dữ liệu.
- Không tìm thấy đơn hàng.
- Không thể xuất báo cáo.
- Dữ liệu tồn kho stale.
- Không có quyền truy cập.
- Zalo OA không gửi thông báo.

---

## 21. Error-code integration

Error state có thể hiển thị:

```text
Mã hỗ trợ: REPORT_EXPORT_TIMEOUT
```

Link `Xem cách xử lý` mở đúng troubleshooting article.

Không hiển thị stack trace hoặc internal error message cho người dùng thông thường.

---

## 22. Guided troubleshooting

Có thể dùng decision tree cho lỗi phổ biến.

Ví dụ:

```text
Bạn có thấy nút Export không?
→ Không: kiểm tra quyền
→ Có: báo cáo có chuyển sang Processing không?
→ Không: kiểm tra lỗi validation
→ Có: mở Job Detail
```

Không cho guided flow tự thay đổi settings hoặc permission.

---

## 23. Video và hình minh họa

Yêu cầu:

- Video ngắn 1–5 phút.
- Có phụ đề tiếng Việt.
- Có transcript.
- Có poster image.
- Không autoplay có âm thanh.
- Hình minh họa phải cập nhật theo UI hiện tại.
- Không dùng dữ liệu khách hàng thật.

---

## 24. Interactive demo

Nếu có demo:

- Dùng dữ liệu giả.
- Không gọi mutation production.
- Có reset state.
- Hiển thị rõ `Môi trường hướng dẫn`.
- Không lưu PII.

Interactive demo phù hợp cho:

- Tạo đơn.
- Xử lý Pick Task.
- Xuất báo cáo.
- Gán quyền mẫu.

---

## 25. What's New

Trang `Có gì mới` gồm:

- Tính năng mới.
- Cải tiến UX.
- Thay đổi workflow.
- Deprecated feature.
- Ngày phát hành.
- Role bị ảnh hưởng.
- Link hướng dẫn.

Không hiển thị release note kỹ thuật nội bộ hoặc lỗ hổng bảo mật chưa công bố.

---

## 26. In-app announcement

Thông báo tính năng mới có thể xuất hiện dạng:

- Badge `Mới`.
- Popover một lần.
- Banner nhỏ.
- Notification Center item.

Yêu cầu:

- Có dismiss.
- Không lặp lại vô hạn.
- Không che action quan trọng.
- Có link `Tìm hiểu thêm`.

---

## 27. Content versioning

Mỗi bài viết cần:

- Version.
- Updated date.
- Product version áp dụng.
- Owner.
- Review date.
- Status.

Khi workflow thay đổi, bài viết liên quan phải được đánh dấu cần review.

---

## 28. Content governance

Mỗi topic có:

- Content owner.
- Reviewer nghiệp vụ.
- Reviewer UX nếu cần.
- Ngày review tiếp theo.
- Source of truth.

Không để tài liệu không có owner tồn tại lâu dài.

---

## 29. Role-based visibility

Ví dụ:

- Warehouse Staff không thấy hướng dẫn duyệt refund.
- Sales Staff không thấy cấu hình permission.
- Accountant không thấy stock adjustment approval nếu thiếu quyền.
- Admin thấy hướng dẫn hệ thống và bảo mật phù hợp.

Backend hoặc content service phải lọc nội dung; không trả toàn bộ rồi ẩn ở client nếu tài liệu nhạy cảm.

---

## 30. Progress persistence

Lưu:

- Tour đã hoàn thành.
- Checklist progress.
- Topic gần đây.
- Announcement đã dismiss.

Không lưu:

- Nội dung người dùng nhập trong workflow.
- PII.
- Search query nhạy cảm.

Cho phép reset onboarding từ User Menu.

---

## 31. Onboarding state contract

```ts
export type UserOnboardingState = {
  userId: string;
  roleKey: string;
  checklistId?: string;
  completedItemIds: string[];
  skippedItemIds: string[];
  completedTourVersions: Record<string, number>;
  dismissedAnnouncementIds: string[];
  onboardingCompletedAt?: string;
  updatedAt: string;
};
```

---

## 32. Support contact

Kênh có thể gồm:

- Tạo ticket nội bộ.
- Email hỗ trợ.
- Hotline nội bộ.
- Zalo OA hỗ trợ nếu được phê duyệt.
- Liên hệ quản trị viên chi nhánh.

Form hỗ trợ gồm:

- Chủ đề.
- Mô tả.
- Module.
- Mức độ ảnh hưởng.
- Screenshot tùy chọn.
- Request ID/error code.

Không tự đính kèm dữ liệu màn hình nhạy cảm nếu chưa có consent và masking.

---

## 33. Screenshot attachment safety

- Cảnh báo người dùng che PII nếu cần.
- File type whitelist.
- Giới hạn dung lượng.
- Malware scanning.
- Signed upload/download URL.
- Không tự chụp toàn màn hình âm thầm.
- Không ghi secret hoặc token vào attachment metadata.

---

## 34. Feedback

Cuối bài viết:

```text
Bài viết này có hữu ích không?
Có / Không
```

Nếu `Không`, có thể chọn lý do:

- Không đúng vấn đề.
- Khó hiểu.
- Nội dung đã cũ.
- Thiếu bước hướng dẫn.
- Khác.

Không bắt buộc người dùng nhập bình luận dài.

---

## 35. Analytics events

```text
onboarding.welcome.viewed
onboarding.tour.started
onboarding.tour.completed
onboarding.tour.skipped
onboarding.checklist.item_completed
help.center.viewed
help.search.submitted
help.article.opened
help.article.feedback_submitted
help.support_request.started
```

Properties an toàn:

- Role category.
- Topic ID.
- Search result count bucket.
- Tour key/version.
- Completion state.

Không gửi raw search query, support description hoặc screenshot metadata vào analytics.

---

## 36. Success metrics

Có thể đo:

- Onboarding completion rate.
- Time to first key action.
- Checklist completion rate.
- Tour skip rate.
- Help search success rate.
- Article helpfulness rate.
- Support deflection rate.
- Repeated help searches.

Không dùng completion rate làm KPI cá nhân bắt buộc cho nhân viên nếu không có mục đích đào tạo rõ ràng.

---

## 37. Accessibility

- Tour hỗ trợ keyboard.
- Tooltip/popover có semantics đúng.
- Video có caption và transcript.
- Search có label.
- Article dùng heading hierarchy đúng.
- Focus trả về trigger sau khi đóng Help Drawer.
- Không khóa focus sai trong tour.
- Respect reduced motion.
- Zoom 200% vẫn đọc và thao tác được.
- Touch target tối thiểu 44px.

---

## 38. Responsive behavior

### Desktop

- Help drawer 420–520px.
- Help Center layout nhiều cột.

### Tablet

- Drawer rộng 420–480px.
- Category grid 2 cột.

### Mobile

- Help mở full-screen.
- Search sticky.
- Article một cột.
- Product tour dùng bottom sheet hoặc callout gọn.
- Video responsive.

Không để tooltip nhỏ là cách trợ giúp duy nhất trên thiết bị cảm ứng.

---

## 39. Loading states

- Help search skeleton.
- Article skeleton.
- Checklist skeleton.
- Video poster loading.

Không hiển thị `Không có kết quả` trước khi request hoàn tất.

---

## 40. Empty states

### Không có checklist

```text
Bạn đã hoàn tất các bước làm quen hiện có.
```

### Search không có kết quả

```text
Không tìm thấy hướng dẫn phù hợp.
```

Gợi ý:

- Thử từ khóa khác.
- Xem category liên quan.
- Liên hệ hỗ trợ.

### Không có release note mới

```text
Bạn đã xem tất cả cập nhật mới.
```

---

## 41. Error states

- Help service lỗi không chặn Dashboard.
- Giữ query hiện tại khi retry.
- Article lỗi có link về Help Center.
- Tour target lỗi không làm trang bị khóa.
- Có request ID cho lỗi hỗ trợ nếu phù hợp.

Không hiển thị stack trace.

---

## 42. Offline behavior

Có thể cache:

- Các bài viết phổ biến.
- Checklist hiện tại.
- FAQ cơ bản.

Khi offline:

- Hiển thị nhãn `Nội dung đã lưu`.
- Không tạo support ticket nếu không thể gửi.
- Giữ draft cục bộ an toàn hoặc yêu cầu copy nội dung.
- Không báo gửi thành công trước server confirmation.

---

## 43. Security và privacy

- Nội dung lọc theo permission.
- Không hiển thị tài liệu security nhạy cảm cho user thường.
- Không dùng dữ liệu production trong video/demo.
- Search logs phải redact dữ liệu nhạy cảm.
- Support attachments có permission và expiry.
- Không tự thu thập màn hình hoặc dữ liệu người dùng.
- Sanitize article HTML/Markdown.
- External links phải được whitelist hoặc cảnh báo.

---

## 44. API endpoints đề xuất

```text
GET    /api/v1/help/topics
GET    /api/v1/help/topics/:slug
GET    /api/v1/help/search
GET    /api/v1/help/categories
GET    /api/v1/help/tours
GET    /api/v1/help/checklist
PATCH  /api/v1/help/checklist/items/:id
POST   /api/v1/help/articles/:id/feedback
GET    /api/v1/help/onboarding-state
PATCH  /api/v1/help/onboarding-state
POST   /api/v1/help/support-requests
```

---

## 45. Frontend component inventory

```text
HelpCenterPage
├── HelpSearch
├── HelpCategoryGrid
├── PopularTopicList
├── HelpArticlePage
├── HelpArticleFeedback
├── HelpDrawer
├── ContextualHelpPopover
├── ProductTourOverlay
├── OnboardingChecklistCard
├── WelcomeDialog
├── WhatsNewPanel
├── TroubleshootingFlow
└── SupportRequestForm
```

---

## 46. Content authoring UI

Nếu có CMS nội bộ, cần:

- Markdown hoặc rich-text editor an toàn.
- Preview desktop/mobile.
- Permission visibility.
- Related routes/features.
- Version và status.
- Review workflow.
- Publish schedule.
- Link validation.

Không cho chèn script, iframe tùy ý hoặc HTML chưa sanitize.

---

## 47. Testing

### Unit

- Permission-based topic filtering.
- Checklist progress.
- Tour target resolution.
- Search result ranking.
- URL sanitization.
- Article version selection.

### Component

- Welcome dialog.
- Checklist.
- Product tour.
- Contextual help.
- Search.
- Article page.
- Feedback.
- Support form.

### Integration

- Role-based checklist.
- Tour bỏ qua target optional.
- Article permission filtering.
- Error-code deep link.
- Feedback submission.
- Reset onboarding.

### E2E

- User mới đăng nhập.
- Hoàn thành welcome flow.
- Hoàn thành checklist item.
- Mở help từ widget.
- Search lỗi export.
- Đọc bài troubleshooting.
- Gửi feedback.
- Tạo yêu cầu hỗ trợ.

---

## 48. Visual QA

Baseline:

```text
Welcome Dialog
Onboarding Checklist
Product Tour Step
Tour Missing Target
Help Drawer
Help Center Home
Search Results
No Search Results
Article Detail
Troubleshooting Flow
What's New
Support Request Form
Mobile Help Center
```

Kiểm tra:

- Tour placement.
- Long Vietnamese content.
- Search result readability.
- Video responsiveness.
- Focus states.
- Mobile keyboard behavior.

---

## 49. Performance budgets

Mục tiêu tham khảo:

```text
Help drawer open < 200ms
Help search API p95 < 1s
Article load p95 < 1s
Tour step transition < 100ms
Checklist update feedback < 1s
```

Lazy-load video, hình ảnh và article dài.

---

## 50. AI-assisted help requirements

Nếu dùng AI trả lời câu hỏi trợ giúp:

- Chỉ dùng tài liệu đã publish và phù hợp permission.
- Không tự bịa workflow hoặc permission.
- Trích dẫn bài viết nguồn trong UI.
- Không nhận hoặc xử lý secret, token, password.
- Không tự thực hiện mutation.
- Hiển thị rõ câu trả lời được tạo tự động.
- Có fallback sang search tài liệu thông thường.
- Có logging an toàn và quality review.

---

## 51. AI coding agent requirements

AI coding agent phải:

- Không tạo một tour chung cho mọi vai trò.
- Không chặn Dashboard khi onboarding service lỗi.
- Không hardcode CSS selector dễ vỡ làm tour target.
- Không hiển thị tài liệu ngoài permission.
- Không dùng dữ liệu production trong demo/fixture.
- Không autoplay video có âm thanh.
- Không gửi raw search query hoặc support content vào analytics.
- Bổ sung test cho role filtering, missing target, keyboard và mobile.
- Báo rõ content source, permissions và backend assumptions.

---

## 52. Acceptance checklist

- [ ] Có welcome flow cho lần đăng nhập đầu.
- [ ] Có onboarding checklist theo vai trò.
- [ ] Có Product Tour ngắn, bỏ qua và mở lại được.
- [ ] Có contextual help.
- [ ] Có Help Center và search.
- [ ] Có category, FAQ và troubleshooting.
- [ ] Có error-code deep link.
- [ ] Có video/transcript nếu dùng media.
- [ ] Có What's New và in-app announcement.
- [ ] Có content versioning và governance.
- [ ] Nội dung lọc theo permission.
- [ ] Có support request và attachment safety.
- [ ] Có feedback và success metrics.
- [ ] Loading, empty, error, offline và responsive states đầy đủ.
- [ ] Accessibility, security, analytics và testing hoàn chỉnh.
- [ ] AI-assisted help có guardrail nếu được triển khai.

---

## 53. Những điều không được làm

- Không ép người dùng xem tour dài.
- Không dùng một onboarding cho mọi vai trò.
- Không mở lại tour liên tục sau khi đã bỏ qua.
- Không chặn Dashboard khi Help service lỗi.
- Không dùng CSS selector dễ vỡ làm target duy nhất.
- Không hiển thị tài liệu ngoài permission.
- Không dùng dữ liệu khách hàng thật trong video hoặc demo.
- Không autoplay video có âm thanh.
- Không dùng tooltip cho nội dung hướng dẫn dài.
- Không gửi raw search query, support description hoặc attachment metadata vào analytics.
- Không để AI tự thực hiện thay đổi hệ thống.

---

## 54. Kết luận

Onboarding & Help Center của Cynca VLXD phải giúp từng vai trò học đúng chức năng, tìm đúng hướng dẫn và giải quyết lỗi với ít gián đoạn nhất. Nội dung cần ngắn gọn, theo ngữ cảnh, có version và owner rõ ràng; trải nghiệm phải hỗ trợ keyboard, mobile, permission filtering và không được làm cản trở công việc chính.

File tiếp theo đề xuất:

```text
README.md
44-Mobile-Tablet-Operations.md
```