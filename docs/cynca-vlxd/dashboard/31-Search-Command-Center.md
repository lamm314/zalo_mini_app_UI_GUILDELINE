# Cynca VLXD Admin Dashboard — Search & Command Center Specification

> Phiên bản: 1.0  
> Phạm vi: Global Search, Command Palette và điều hướng tác vụ nhanh trong Dashboard quản trị  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, QA, Security và AI coding agent  
> Tài liệu liên quan: `04-Header.md`, `16-Data-Contracts.md`, `20-Frontend-Architecture.md`, `23-Security-Privacy.md`, `26-Analytics-Event-Plan.md`, `27-Role-Based-Dashboard.md`

---

## 1. Mục tiêu

Search & Command Center giúp người dùng tìm nhanh dữ liệu và thực hiện các tác vụ thường xuyên mà không phải điều hướng qua nhiều màn hình.

Mục tiêu:

- Tìm đơn hàng, sản phẩm, khách hàng, nhà cung cấp và trang chức năng.
- Điều hướng nhanh tới module phù hợp.
- Hỗ trợ command palette bằng bàn phím.
- Hiển thị đúng dữ liệu theo role, permission và scope.
- Không làm lộ PII hoặc dữ liệu ngoài quyền.
- Hỗ trợ exact match, prefix match và fuzzy match có kiểm soát.
- Có loading, empty, error, offline và stale states.
- Đảm bảo accessibility và hoạt động tốt trên desktop, tablet và mobile fallback.

---

## 2. Phân biệt Global Search và Command Palette

### Global Search

Tập trung tìm entity nghiệp vụ:

- Đơn hàng.
- Sản phẩm.
- Khách hàng.
- Nhà cung cấp.
- Kho.
- Báo cáo.

### Command Palette

Tập trung điều hướng và action:

- Mở trang đơn hàng.
- Tạo đơn mới.
- Xuất báo cáo.
- Mở cảnh báo tồn kho.
- Chuyển chi nhánh.
- Mở cài đặt.

Hai chức năng có thể dùng chung overlay nhưng phải phân loại rõ kết quả.

---

## 3. Entry points

Search & Command Center có thể mở từ:

- Search field trong Header.
- Shortcut `Ctrl + K` hoặc `Cmd + K`.
- Nút Search trên mobile.
- Shortcut `/` nếu không xung đột với input đang focus.

Không kích hoạt shortcut khi người dùng đang nhập trong input, textarea hoặc editor.

---

## 4. Desktop search field

Kích thước đề xuất:

```text
Height: 40px
Width mặc định: 280–360px
Max width: 480px
```

Nội dung:

- Search icon.
- Placeholder.
- Shortcut hint.
- Clear button khi có query.

Placeholder:

```text
Tìm đơn hàng, sản phẩm, khách hàng...
```

Không dùng placeholder làm label duy nhất; cần accessible name.

---

## 5. Command Center overlay

Kích thước desktop:

```text
Width: 680–760px
Max-height: 72–80vh
```

Cấu trúc:

```text
CommandCenter
├── SearchInput
├── Context/Scope Bar
├── Suggested Commands
├── Search Result Groups
├── Preview Panel tùy chọn
└── Footer Shortcut Hints
```

Overlay nên căn giữa phía trên viewport, cách mép trên khoảng 8–12vh.

---

## 6. Mobile behavior

Trên mobile:

- Mở full-screen sheet.
- Search input sticky trên cùng.
- Kết quả scroll toàn màn hình.
- Không hiển thị preview panel hai cột.
- Touch target tối thiểu 44px.
- Nút đóng rõ ràng.
- Keyboard mobile không che kết quả đang chọn.

---

## 7. Search domains

```ts
export type SearchEntityType =
  | "order"
  | "product"
  | "customer"
  | "supplier"
  | "warehouse"
  | "report"
  | "navigation"
  | "command";
```

Mỗi domain phải có permission và scope riêng.

---

## 8. Search query contract

```ts
export type GlobalSearchQuery = {
  q: string;
  types?: SearchEntityType[];
  branchId?: string;
  warehouseId?: string;
  limit?: number;
  cursor?: string;
};
```

Quy tắc:

- Trim khoảng trắng đầu cuối.
- Normalize Unicode phù hợp.
- Không gửi request khi query dưới minimum length, trừ command/navigation mode.
- Exact order code hoặc SKU có thể tìm ngay từ 2–3 ký tự nếu nghiệp vụ cho phép.

---

## 9. Result contract

```ts
export type GlobalSearchResult = {
  id: string;
  type: SearchEntityType;
  title: string;
  subtitle?: string;
  metadata?: Array<{
    label: string;
    value: string;
  }>;
  imageUrl?: string;
  iconKey?: string;
  status?: string;
  href?: string;
  commandKey?: string;
  score?: number;
  highlightedFields?: string[];
  availableActions?: string[];
};
```

Frontend không được dùng `score` làm dữ liệu hiển thị cho người dùng.

---

## 10. Search response

```ts
export type GlobalSearchResponse = {
  query: string;
  groups: Array<{
    type: SearchEntityType;
    label: string;
    items: GlobalSearchResult[];
    total?: number;
  }>;
  totalResults: number;
  nextCursor?: string | null;
  searchId?: string;
  updatedAt: string;
};
```

`searchId` có thể dùng cho analytics và tracing, không hiển thị công khai.

---

## 11. Minimum query length

Gợi ý:

```text
Order code exact/prefix: 2–3 ký tự
SKU: 2–3 ký tự
Product name: 2 ký tự
Customer name: 2–3 ký tự
Phone/email: chỉ khi có quyền và minimum dài hơn
General fuzzy search: 3 ký tự
```

Không cho phép tìm kiếm số điện thoại quá ngắn gây enumeration.

---

## 12. Debounce và cancellation

- Debounce 200–300ms.
- Hủy request cũ khi query đổi.
- Không để kết quả request cũ ghi đè query mới.
- Hiển thị loading cục bộ sau khoảng 150–250ms để tránh nhấp nháy.

Dùng `AbortController` hoặc query cancellation tương đương.

---

## 13. Search modes

Có thể hỗ trợ:

```text
All
Orders
Products
Customers
Commands
```

Mode có thể chọn bằng tabs hoặc filter chips.

Không hiển thị quá nhiều mode trong Header nhỏ; full Command Center mới cần mode selector đầy đủ.

---

## 14. Exact match behavior

Khi query khớp chính xác:

- Đơn hàng: ưu tiên mã đơn.
- Sản phẩm: ưu tiên SKU.
- Khách hàng: ưu tiên mã khách hàng nếu có.
- Nhà cung cấp: ưu tiên mã nhà cung cấp.

Exact match phải xuất hiện đầu nhóm và có indicator nhẹ:

```text
Khớp chính xác
```

Không tự mở entity mà thiếu hành động xác nhận từ người dùng.

---

## 15. Fuzzy search behavior

Fuzzy search dùng cho:

- Tên sản phẩm.
- Tên khách hàng.
- Tên nhà cung cấp.
- Navigation labels.

Quy tắc:

- Ưu tiên prefix và token match trước typo-tolerant match.
- Không fuzzy trên dữ liệu quá nhạy cảm nếu làm tăng rủi ro enumeration.
- Kết quả phải ổn định đủ để người dùng học được thứ tự.

---

## 16. Vietnamese normalization

Hỗ trợ:

- Có dấu và không dấu.
- Chữ hoa/chữ thường.
- Khoảng trắng thừa.
- Một số biến thể viết tắt nội bộ.

Ví dụ:

```text
"xi mang" → "Xi măng"
"thep hoa phat" → sản phẩm Thép Hòa Phát
```

Không normalize làm thay đổi mã SKU hoặc mã đơn.

---

## 17. Result grouping

Thứ tự nhóm mặc định:

1. Khớp chính xác.
2. Đơn hàng.
3. Sản phẩm.
4. Khách hàng.
5. Nhà cung cấp.
6. Navigation.
7. Commands.

Thứ tự có thể thay đổi theo role.

Ví dụ Warehouse user ưu tiên:

1. Sản phẩm.
2. Kho.
3. Đơn chờ xuất.
4. Commands kho.

---

## 18. Role-based ranking

### Sales

Ưu tiên:

- Orders.
- Customers.
- Products.

### Warehouse

Ưu tiên:

- Products/SKU.
- Warehouses.
- Orders ready to ship.

### Accountant

Ưu tiên:

- Orders.
- Payment-related reports.
- Customers theo công nợ.

### Admin

Ưu tiên:

- Navigation.
- System commands.
- Users/configuration nếu có quyền.

Backend phải áp permission trước khi ranking.

---

## 19. Result item anatomy

Mỗi result gồm:

- Thumbnail hoặc icon.
- Title.
- Subtitle.
- Status badge tùy chọn.
- Metadata ngắn.
- Keyboard selection state.
- Trailing action hoặc shortcut hint.

Không hiển thị quá nhiều PII trong subtitle.

---

## 20. Result examples

### Order

```text
#CYN-240806-0187
Nguyễn Văn A · Đang giao · 06/08/2026
```

### Product

```text
Xi măng PCB40
SKU: XM-PCB40-001 · Vật liệu thô
```

### Customer

```text
Công ty TNHH Minh Long
Khách hàng doanh nghiệp · Hà Nội
```

### Command

```text
Tạo đơn hàng mới
Đi tới biểu mẫu tạo đơn
```

---

## 21. Highlighting

Có thể highlight phần khớp trong title/subtitle.

Quy tắc:

- Không làm giảm contrast.
- Không chèn HTML chưa sanitize.
- Không highlight mã bằng cách làm mất khả năng đọc.
- Screen reader vẫn đọc chuỗi đầy đủ, không bị chia nhỏ khó hiểu.

---

## 22. Recent searches

Có thể hiển thị khi query rỗng:

- Recent entities.
- Recent navigation.
- Saved commands.

Quy tắc privacy:

- Chỉ lưu local nếu dữ liệu không nhạy cảm.
- Không lưu raw phone/email query.
- Clear khi logout.
- Cho phép xóa lịch sử.

Ưu tiên lưu entity reference an toàn thay vì raw query.

---

## 23. Suggested commands

Khi query rỗng, hiển thị:

- Tạo đơn hàng.
- Xem đơn mới.
- Mở cảnh báo tồn kho.
- Xuất báo cáo tháng.
- Mở Notification Center.
- Chuyển chi nhánh.

Danh sách dựa trên role, permission và usage pattern được phép.

---

## 24. Command registry

```ts
export type DashboardCommand = {
  key: string;
  label: string;
  description?: string;
  iconKey?: string;
  keywords?: string[];
  requiredPermissions?: string[];
  scopeRequirements?: string[];
  actionType: "navigate" | "dialog" | "drawer" | "mutation";
  href?: string;
  confirmationRequired?: boolean;
};
```

Frontend dùng registry tĩnh hoặc config được whitelist.

Không thực thi JavaScript tùy ý từ backend.

---

## 25. Command categories

```text
Navigation
Create
Orders
Inventory
Reports
Settings
Help
```

Command mutation phải được hạn chế mạnh.

Ví dụ an toàn:

- Mở dialog tạo đơn.
- Mở export menu.

Không nên chạy ngay:

- Hủy đơn.
- Hoàn tiền.
- Điều chỉnh tồn kho.

---

## 26. Destructive commands

Nếu có destructive command:

- Phải có permission.
- Phải yêu cầu context/entity rõ ràng.
- Phải có confirm dialog.
- Không xuất hiện ở suggested commands chung.
- Không thực thi chỉ bằng Enter nếu query chưa đủ cụ thể.
- Backend kiểm tra lại quyền và version.

---

## 27. Keyboard interaction

- `Arrow Down/Up`: di chuyển kết quả.
- `Enter`: mở/chọn.
- `Esc`: đóng overlay.
- `Tab`: di chuyển qua interactive controls có chủ đích.
- `Cmd/Ctrl + K`: mở/đóng.
- `Cmd/Ctrl + Enter`: có thể mở trong tab mới nếu phù hợp và được mô tả rõ.

Active descendant hoặc roving tabindex phải được triển khai đúng.

---

## 28. Focus management

Khi mở:

- Focus vào search input.

Khi đóng:

- Trả focus về trigger.

Khi chọn result:

- Điều hướng route hoặc mở overlay tương ứng.
- Không để focus rơi về body.

Khi lỗi:

- Giữ focus trong search để người dùng sửa query.

---

## 29. Accessibility semantics

Có thể dùng combobox pattern:

- Input `role=combobox`.
- Popup `role=listbox`.
- Item `role=option`.
- `aria-expanded`.
- `aria-controls`.
- `aria-activedescendant`.

Group labels cần semantic rõ.

Không gán ARIA nếu behavior không tuân theo pattern.

---

## 30. Loading state

- Giữ kết quả cũ mờ nhẹ trong lúc refetch nếu an toàn.
- Hiển thị skeleton 4–6 items.
- Không chuyển sang empty trước khi request hoàn tất.
- Có text screen-reader:

```text
Đang tìm kiếm
```

---

## 31. Empty states

### Query quá ngắn

```text
Nhập thêm để tìm kiếm.
```

### Không có kết quả

```text
Không tìm thấy kết quả phù hợp.
```

Gợi ý:

- Kiểm tra mã đơn hoặc SKU.
- Thử từ khóa ngắn hơn.
- Thay đổi loại kết quả.

Không gợi ý bỏ filter quyền hoặc branch scope.

---

## 32. Error state

```text
Không thể tải kết quả tìm kiếm.
```

Có:

- Retry.
- Request ID nếu phù hợp.
- Giữ query hiện tại.

Không đóng overlay khi lỗi.

---

## 33. Offline state

Khi offline:

- Hiển thị recent navigation và commands local an toàn.
- Không hiển thị entity cache nếu có nguy cơ stale hoặc lộ dữ liệu.
- Disable search server-side.
- Không báo entity không tồn tại chỉ vì offline.

---

## 34. Search preview panel

Desktop rộng có thể có preview panel bên phải cho result đang chọn.

Nội dung giới hạn:

- Entity summary.
- Status.
- Metadata ngắn.
- Primary action.

Không tải full detail trước khi cần nếu gây nhiều request hoặc lộ dữ liệu.

Preview panel phải ẩn ở viewport nhỏ.

---

## 35. Search scope bar

Có thể hiển thị scope hiện tại:

```text
Chi nhánh: Hà Nội
Kho: Tất cả kho được phép
```

Cho phép đổi scope chỉ khi user có quyền.

Scope change phải cập nhật query và cache key.

---

## 36. Permission behavior

- Không trả kết quả ngoài quyền.
- Không hiển thị command ngoài quyền.
- Direct href vẫn phải được route/backend guard.
- Permission loading phải fail closed.
- Search result không được flash rồi biến mất sau khi kiểm tra quyền.

---

## 37. PII handling

Khách hàng search result:

- Chỉ hiển thị tên và metadata cần thiết.
- Phone/email được mask theo role.
- Không hiển thị full address.
- Không log raw query.
- Không gửi raw query vào analytics.

Backend phải áp masking trước khi trả.

---

## 38. Enumeration protection

Đặc biệt cho phone, email và customer data:

- Minimum query length cao hơn.
- Rate limiting.
- Result limit thấp.
- Permission bắt buộc.
- Monitoring search volume bất thường.
- Không trả thông báo khác biệt làm lộ entity có tồn tại hay không khi không đủ quyền.

---

## 39. Search analytics

Events:

```text
search.command_center.opened
search.query.submitted
search.result.selected
search.filter.changed
search.abandoned
search.command.executed
search.error.retry_clicked
```

Properties an toàn:

- Query length bucket.
- Result count bucket.
- Result type.
- Selected position bucket.
- Input method.
- Response duration bucket.

Không gửi raw query hoặc entity title.

---

## 40. Observability

Theo dõi:

- Search API success rate.
- p50/p95 latency.
- Zero-result rate.
- Result selected rate.
- Abort count.
- Permission denied anomalies.
- Contract validation failures.
- Command execution failure rate.

Trace span đề xuất:

```text
search.global
search.command.execute
```

---

## 41. Search indexing strategy

Backend có thể dùng:

- Database search cho exact code.
- Full-text search.
- Search index chuyên dụng khi dữ liệu lớn.

Ưu tiên hybrid:

1. Exact code lookup.
2. Prefix search.
3. Full-text/fuzzy search.

Không dùng query chậm trên database production cho mỗi keystroke nếu không có index phù hợp.

---

## 42. Search index freshness

Mỗi domain cần SLA khác nhau:

- Orders: gần real-time.
- Products: vài phút.
- Customers: vài phút.
- Navigation/commands: theo release/config.

Khi index stale:

- Exact lookup có thể fallback database nếu an toàn.
- Hiển thị warning chỉ khi ảnh hưởng rõ.
- Theo dõi data age metric.

---

## 43. Result limits

Dropdown/overlay:

```text
Tối đa 5 kết quả mỗi nhóm
Tổng tối đa 20–30 kết quả ban đầu
```

Có `Xem tất cả` theo nhóm để mở trang tìm kiếm đầy đủ.

Không tải hàng trăm kết quả vào command palette.

---

## 44. Full search results page

Route đề xuất:

```text
/search?q=...&type=...
```

Chức năng:

- Filter nâng cao.
- Pagination.
- Sort.
- Table/list theo domain.
- Search scope rõ ràng.
- Shareable URL nếu không chứa dữ liệu nhạy cảm.

Không đặt raw phone/email vào URL nếu policy không cho phép.

---

## 45. Caching

Có thể cache:

- Navigation commands.
- Recent entity metadata an toàn.
- Query results ngắn hạn theo user/scope.

Cache key phải gồm:

- User/session boundary.
- Permission hash.
- Branch/warehouse scope.
- Query.
- Types.

Clear cache khi logout hoặc đổi tài khoản.

---

## 46. Command execution model

```ts
export type CommandExecutionResult = {
  status: "completed" | "opened" | "queued" | "failed";
  message?: string;
  href?: string;
  jobId?: string;
};
```

Navigation command có thể xử lý client-side.

Mutation command phải gọi backend và chờ confirmation.

---

## 47. Security requirements

- Backend áp authorization và object scope.
- Route/href dùng whitelist.
- Không cho raw command script.
- Không log raw query.
- Không lưu PII vào recent searches.
- Rate limit customer search.
- Chống open redirect.
- Sanitize highlighted content.
- Clear user context khi logout.

---

## 48. API endpoints đề xuất

```text
GET  /api/v1/search
GET  /api/v1/search/suggestions
GET  /api/v1/search/recent
POST /api/v1/search/recent/clear
GET  /api/v1/commands
POST /api/v1/commands/:key/execute
```

Không bắt buộc endpoint execute chung nếu command mutation cần API domain riêng.

---

## 49. Frontend component inventory

```text
SearchCommandCenter
├── SearchTrigger
├── SearchInput
├── SearchModeTabs
├── SearchScopeBar
├── RecentSearchList
├── SuggestedCommandList
├── SearchResultGroup
├── SearchResultItem
├── SearchPreviewPanel
├── SearchLoadingState
├── SearchEmptyState
├── SearchErrorState
└── CommandShortcutFooter
```

---

## 50. State model

```ts
export type SearchCommandCenterState = {
  open: boolean;
  query: string;
  mode: SearchEntityType | "all";
  activeIndex: number;
  loading: boolean;
  error?: string;
  results: GlobalSearchResponse | null;
  selectedResult?: GlobalSearchResult;
};
```

Server state nên dùng query layer; overlay interaction state dùng local state.

---

## 51. Testing

### Unit

- Query normalization.
- Result grouping.
- Ranking fallback.
- Permission filtering helper.
- Highlight sanitization.
- Recent search privacy rules.

### Component

- Open/close shortcut.
- Debounce.
- Keyboard navigation.
- Loading/empty/error.
- Mode switch.
- Result selection.
- Focus return.

### Integration

- Abort request cũ.
- Scope change.
- Role-based ranking.
- Permission-limited results.
- Command execution.
- Analytics không chứa raw query.

### E2E

- Tìm mã đơn.
- Tìm SKU không dấu.
- Chọn bằng keyboard.
- Mở command tạo đơn.
- Warehouse user không thấy finance command.
- Mobile full-screen search.

---

## 52. Visual QA

Baseline:

```text
Header Search Default
Command Center Empty
Recent Searches
Grouped Results
Exact Match
Loading
No Results
Error
Long Vietnamese Content
Permission Limited
Desktop Preview Panel
Tablet Sheet
Mobile Full Screen
Keyboard Active Result
```

Kiểm tra:

- Overlay size.
- Group spacing.
- Active option.
- Highlight contrast.
- Long titles.
- Edge collision.
- Mobile keyboard behavior.

---

## 53. Performance budgets

Mục tiêu tham khảo:

```text
Input response: dưới 100ms
Search request p95: dưới 1 giây
Command Center open: dưới 200ms
First result paint sau response: dưới 100ms
```

Command registry và navigation suggestions nên tải sẵn hoặc cache nhẹ.

---

## 54. AI coding agent requirements

AI phải:

- Không gửi raw query vào analytics hoặc logs.
- Không tìm và trả dữ liệu ngoài scope.
- Không dùng role name làm authorization duy nhất.
- Không lưu phone/email query trong recent searches.
- Không thực thi raw command từ backend.
- Không chạy destructive command trực tiếp bằng Enter.
- Bổ sung debounce, cancellation, focus management và keyboard tests.
- Báo rõ search index, fallback, permission và privacy assumptions.
- Không tuyên bố search an toàn nếu backend chưa áp object-level authorization.

---

## 55. Acceptance checklist

- [ ] Header search field hoàn chỉnh.
- [ ] Command Center hỗ trợ shortcut.
- [ ] Có search entity và command modes.
- [ ] Query contract và result contract rõ ràng.
- [ ] Có debounce và request cancellation.
- [ ] Hỗ trợ exact, prefix và fuzzy search.
- [ ] Hỗ trợ tiếng Việt có dấu/không dấu.
- [ ] Result grouping và role-based ranking hợp lý.
- [ ] Có recent searches và suggested commands an toàn.
- [ ] Keyboard navigation và focus management đúng.
- [ ] Loading, empty, error và offline states đầy đủ.
- [ ] Permission, scope và PII masking được backend enforce.
- [ ] Có enumeration protection.
- [ ] Analytics không ghi raw query.
- [ ] Observability và performance metrics đầy đủ.
- [ ] Search index freshness và limits được xác định.
- [ ] Có full search results page.
- [ ] Test unit, component, integration, E2E và Visual QA đầy đủ.

---

## 56. Những điều không được làm

- Không gửi raw search query vào analytics hoặc logs.
- Không trả entity ngoài quyền rồi ẩn ở frontend.
- Không lưu phone/email query trong localStorage.
- Không dùng một request mới cho mỗi keystroke thiếu debounce.
- Không để request cũ ghi đè kết quả mới.
- Không thực thi arbitrary command code.
- Không chạy destructive command thiếu confirmation.
- Không tải hàng trăm kết quả trong overlay.
- Không tự mở exact-match result thiếu hành động người dùng.
- Không dùng fuzzy search để lộ dữ liệu nhạy cảm.
- Không bỏ focus management hoặc keyboard alternative.

---

## 57. Kết luận

Search & Command Center của Cynca VLXD phải giúp người dùng tìm đúng dữ liệu và mở đúng tác vụ trong vài giây, đồng thời giữ chặt permission, scope và quyền riêng tư. Hệ thống nên kết hợp exact lookup, search index, role-based ranking và command registry an toàn. Mọi kết quả và command đều phải được backend xác thực trước khi hiển thị hoặc thực thi.

File tiếp theo đề xuất:

```text
README.md
32-Executive-Dashboard.md
```