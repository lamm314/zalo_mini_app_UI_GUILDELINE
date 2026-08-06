# Cynca VLXD Admin Dashboard — Reports & Export Center Specification

> Phiên bản: 1.0  
> Phạm vi: Trung tâm báo cáo, xuất dữ liệu, lịch chạy và quản lý file báo cáo  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Data, Finance, Operations, QA, Security và AI coding agent  
> Tài liệu liên quan: `16-Data-Contracts.md`, `22-Release-Operations.md`, `23-Security-Privacy.md`, `24-Observability.md`, `27-Role-Based-Dashboard.md`, `35-Finance-Dashboard.md`

---

## 1. Mục tiêu

Reports & Export Center là nơi tập trung để người dùng Cynca VLXD tạo, theo dõi, tải xuống và quản lý các báo cáo theo đúng quyền và phạm vi dữ liệu.

Hệ thống phải hỗ trợ:

- Chọn báo cáo theo từng nhóm nghiệp vụ.
- Cấu hình bộ lọc trước khi xuất.
- Xuất dữ liệu đồng bộ hoặc theo background job.
- Theo dõi tiến độ, trạng thái và lỗi.
- Tải file qua signed URL có thời hạn.
- Lập lịch báo cáo định kỳ.
- Gửi thông báo khi báo cáo hoàn tất.
- Quản lý lịch sử và thời hạn lưu file.
- Không làm lộ dữ liệu ngoài role, permission hoặc branch scope.
- Có audit log cho báo cáo nhạy cảm.

Reports & Export Center không được dùng như cách vượt qua permission của các module nguồn.

---

## 2. Các nhóm báo cáo

```ts
export type ReportCategory =
  | "executive"
  | "sales"
  | "orders"
  | "inventory"
  | "warehouse"
  | "finance"
  | "customers"
  | "customer_service"
  | "products"
  | "system";
```

Ví dụ:

### Executive

- Báo cáo điều hành tháng.
- So sánh chi nhánh.
- Tổng hợp rủi ro.

### Sales

- Doanh số theo nhân viên.
- Doanh số theo sản phẩm.
- Tiến độ mục tiêu.
- Khách hàng cần chăm sóc.

### Orders

- Danh sách đơn hàng.
- Đơn quá SLA.
- Đơn hủy và trả hàng.

### Inventory / Warehouse

- Tồn kho hiện tại.
- SKU sắp hết.
- Biến động kho.
- Pick/pack SLA.
- Chênh lệch kiểm kê.

### Finance

- Doanh thu và thu tiền.
- Công nợ.
- Đối soát.
- Hoàn tiền.

### Customer Service

- Ticket theo trạng thái.
- SLA.
- Khiếu nại.
- CSAT.

---

## 3. Vai trò và quyền

Permission đề xuất:

```text
reports.center.view
reports.create
reports.download
reports.schedule
reports.history.view
reports.delete_own
reports.delete_all
reports.executive.export
reports.sales.export
reports.orders.export
reports.inventory.export
reports.finance.export
reports.customers.export
reports.support.export
reports.system.export
```

Mỗi report template phải có permission riêng hoặc nhóm permission rõ ràng.

Không dựa duy nhất vào role name.

---

## 4. Information architecture

```text
Reports & Export Center
├── Tổng quan
├── Danh mục báo cáo
├── Tạo báo cáo
├── Đang xử lý
├── Báo cáo đã hoàn tất
├── Lịch báo cáo
├── Mẫu đã lưu
└── Lịch sử / Audit
```

---

## 5. Trang tổng quan

Khu vực chính:

- Quick report cards.
- Báo cáo gần đây.
- Job đang chạy.
- Báo cáo lỗi.
- Lịch chạy tiếp theo.
- Dung lượng file sắp hết hạn.

KPI nhẹ:

- Báo cáo đã tạo trong 7 ngày.
- Báo cáo đang xử lý.
- Báo cáo thất bại.
- Lịch báo cáo đang hoạt động.

Không cần chart phức tạp nếu không giúp vận hành.

---

## 6. Report Catalog

Mỗi report card gồm:

- Tên báo cáo.
- Mô tả.
- Category.
- Định dạng hỗ trợ.
- Permission required.
- Estimated size/time.
- Data freshness.
- Last generated time.
- Primary action `Tạo báo cáo`.

Có search và filter theo category, role và format.

---

## 7. Report template contract

```ts
export type ReportTemplate = {
  key: string;
  name: string;
  description?: string;
  category: ReportCategory;
  requiredPermissions: string[];
  supportedFormats: ReportFormat[];
  filters: ReportFilterDefinition[];
  columns?: ReportColumnDefinition[];
  maxRangeDays?: number;
  estimatedRowLimit?: number;
  supportsScheduling: boolean;
  supportsSavedPreset: boolean;
  dataStatus?: "realtime" | "near_realtime" | "batch" | "closed_period";
};
```

Template key phải map tới backend registry, không phải raw SQL hoặc raw endpoint.

---

## 8. Định dạng xuất

```ts
export type ReportFormat = "csv" | "xlsx" | "pdf";
```

### CSV

- Phù hợp dữ liệu bảng lớn.
- Nhẹ và dễ xử lý.
- Phải chống formula injection.

### XLSX

- Phù hợp đối soát và làm việc nội bộ.
- Có sheet, header, format số và ngày.

### PDF

- Phù hợp trình bày, họp và lưu bản tổng hợp.
- Không phù hợp dữ liệu hàng trăm nghìn dòng.

Không dùng PDF cho export dữ liệu chi tiết cực lớn.

---

## 9. Create Report flow

```text
Chọn template
→ Chọn scope và bộ lọc
→ Chọn cột/định dạng
→ Xem ước tính
→ Xác nhận
→ Tạo job
→ Theo dõi tiến độ
→ Nhận thông báo
→ Tải file
```

Không báo `Hoàn tất` trước khi backend tạo file thành công.

---

## 10. Report filters

Filter có thể gồm:

- Date range.
- Branch.
- Warehouse.
- Team/employee.
- Order status.
- Payment status.
- Product/category.
- Customer segment.
- Ticket status.
- Data status: provisional/closed.

Filter phải tuân thủ backend scope.

Không hiển thị giá trị branch/warehouse ngoài quyền.

---

## 11. Filter definition

```ts
export type ReportFilterDefinition = {
  key: string;
  label: string;
  type: "date_range" | "single_select" | "multi_select" | "boolean" | "number_range";
  required: boolean;
  defaultValue?: unknown;
  allowedValuesSource?: string;
  permission?: string;
  affectsEstimate?: boolean;
};
```

`allowedValuesSource` map đến registry an toàn, không dùng raw URL.

---

## 12. Date range rules

- Mỗi template có `maxRangeDays`.
- Nếu phạm vi quá lớn, yêu cầu chia nhỏ hoặc chạy batch.
- Closed-period reports phải hiển thị trạng thái kỳ.
- Timezone phải rõ, mặc định `Asia/Ho_Chi_Minh`.

Không tự cắt date range mà không báo người dùng.

---

## 13. Column selection

Cho phép người dùng chọn cột khi template hỗ trợ.

Quy tắc:

- Cột mặc định được định nghĩa trước.
- Cột nhạy cảm cần permission riêng.
- Không cho chọn field ngoài registry.
- Không cho export PII đầy đủ khi user chỉ có quyền masked.
- Có `Chọn mặc định`, `Chọn tất cả được phép` và `Đặt lại`.

---

## 14. Column contract

```ts
export type ReportColumnDefinition = {
  key: string;
  label: string;
  dataType: "text" | "number" | "currency" | "percentage" | "date" | "datetime" | "status";
  defaultSelected: boolean;
  requiredPermission?: string;
  sensitive?: boolean;
  exportFormats?: ReportFormat[];
};
```

---

## 15. Data estimate

Trước khi tạo report, backend có thể trả:

```ts
export type ReportEstimate = {
  estimatedRows?: number;
  estimatedFileSizeBytes?: number;
  estimatedDurationSeconds?: number;
  executionMode: "sync" | "async";
  warnings: string[];
};
```

Ví dụ cảnh báo:

```text
Báo cáo có khoảng 125.000 dòng và có thể mất 3–5 phút.
```

Ước tính không được trình bày như cam kết tuyệt đối.

---

## 16. Sync và async export

### Sync

Dùng cho báo cáo nhỏ, hoàn tất nhanh.

### Async

Bắt buộc khi:

- Dữ liệu lớn.
- PDF phức tạp.
- Nhiều nguồn dữ liệu.
- Có aggregation nặng.

Frontend không giữ request HTTP mở quá lâu cho báo cáo lớn.

---

## 17. Export job contract

```ts
export type ReportExportJob = {
  id: string;
  templateKey: string;
  reportName: string;
  format: ReportFormat;
  status: "queued" | "processing" | "completed" | "failed" | "cancelled" | "expired";
  progressPercent?: number;
  requestedBy: string;
  requestedAt: string;
  startedAt?: string;
  completedAt?: string;
  expiresAt?: string;
  rowCount?: number;
  fileSizeBytes?: number;
  downloadUrl?: string;
  errorCode?: string;
  errorMessage?: string;
  filtersSummary: string[];
  availableActions: string[];
};
```

---

## 18. Job statuses

### Queued

Đang chờ worker.

### Processing

Đang tạo dữ liệu hoặc file.

### Completed

File sẵn sàng tải.

### Failed

Có lỗi; hiển thị reason và retry khi phù hợp.

### Cancelled

Người dùng hoặc hệ thống hủy.

### Expired

File đã hết thời hạn lưu.

Không hiển thị download action khi job chưa completed hoặc đã expired.

---

## 19. Progress behavior

- Chỉ hiển thị percent khi backend có progress đáng tin cậy.
- Nếu không, dùng trạng thái theo bước:

```text
Đang lấy dữ liệu
Đang tạo file
Đang tải lên kho lưu trữ
Đang hoàn tất
```

Không giả progress tăng dần rồi đứng ở 99% trong thời gian dài.

---

## 20. Export queue UI

Bảng gồm:

- Report name.
- Format.
- Scope summary.
- Requested by.
- Requested time.
- Status/progress.
- File size.
- Expiry.
- Actions.

Actions:

- View details.
- Cancel nếu còn hợp lệ.
- Retry.
- Download.
- Duplicate configuration.
- Delete history theo quyền.

---

## 21. Download security

Yêu cầu:

- Signed URL có thời hạn ngắn.
- Kiểm tra permission lại trước khi cấp URL.
- Không public bucket/object.
- Không dùng filename chứa PII không cần thiết.
- File có content type đúng.
- Có `Content-Disposition` an toàn.
- Download event được audit với báo cáo nhạy cảm.

Không lưu signed URL lâu dài trong client storage.

---

## 22. File naming convention

Ví dụ:

```text
cynca-vlxd_sales-report_2026-08-01_2026-08-31.xlsx
cynca-vlxd_inventory-hanoi_2026-08-06.csv
```

Quy tắc:

- Dùng ký tự an toàn.
- Không chứa tên khách hàng hoặc dữ liệu nhạy cảm.
- Có report key và kỳ dữ liệu.
- Có thể thêm branch code khi phù hợp.

---

## 23. CSV injection protection

Giá trị bắt đầu bằng:

```text
=
+
-
@
```

phải được neutralize theo policy nếu đến từ dữ liệu người dùng.

Cần test các payload như:

```text
=HYPERLINK(...)
+SUM(...)
@IMPORTXML(...)
```

Không dựa vào frontend để xử lý; backend export service phải bảo vệ.

---

## 24. XLSX requirements

- Header rõ ràng.
- Freeze header row nếu phù hợp.
- Currency/number/date format đúng.
- Không merge cell quá mức.
- Sheet name an toàn và ngắn.
- Có metadata sheet tùy chọn: filter, scope, generatedAt.
- Không nhúng macro.

---

## 25. PDF requirements

PDF nên gồm:

- Tên báo cáo.
- Scope.
- Kỳ dữ liệu.
- Trạng thái dữ liệu.
- GeneratedAt.
- KPI/charts/tables chính.
- Page number.
- Footer bảo mật nếu cần.

PDF phải xử lý:

- Font tiếng Việt.
- Page break.
- Table lặp header.
- Chart resolution.
- Long text.

---

## 26. Saved report presets

Người dùng có thể lưu:

- Template.
- Format.
- Filters.
- Columns.
- Sort.
- Report name.

Không lưu:

- Signed URL.
- Dữ liệu kết quả.
- Permission snapshot cố định.

Khi chạy lại preset, backend phải kiểm tra permission và scope hiện tại.

---

## 27. Saved preset contract

```ts
export type SavedReportPreset = {
  id: string;
  name: string;
  templateKey: string;
  format: ReportFormat;
  filters: Record<string, unknown>;
  selectedColumns?: string[];
  ownerUserId: string;
  visibility: "private" | "team" | "branch";
  createdAt: string;
  updatedAt: string;
};
```

Shared preset không được mở rộng dữ liệu cho người nhận thiếu quyền.

---

## 28. Scheduled reports

Hỗ trợ:

- Daily.
- Weekly.
- Monthly.
- Custom business schedule nếu backend hỗ trợ.

Mỗi schedule gồm:

- Preset/template.
- Timezone.
- Recurrence.
- Recipients hoặc notification target.
- Delivery method.
- Enabled state.
- Next run.

Không dùng schedule để gửi file chứa PII qua kênh không được phê duyệt.

---

## 29. Schedule contract

```ts
export type ReportSchedule = {
  id: string;
  name: string;
  presetId?: string;
  templateKey: string;
  format: ReportFormat;
  recurrenceRule: string;
  timezone: string;
  delivery: "notification" | "email" | "storage_only";
  recipientIds?: string[];
  enabled: boolean;
  nextRunAt?: string;
  lastRunAt?: string;
  lastRunStatus?: "completed" | "failed" | "skipped";
};
```

---

## 30. Scheduled delivery security

- Recipient phải được resolve từ tài khoản hoặc contact được phép.
- Kiểm tra permission tại thời điểm chạy.
- Nếu owner mất quyền, schedule phải fail closed hoặc bị disable.
- Không đính kèm file nhạy cảm qua email nếu policy chỉ cho signed link.
- Link phải hết hạn.
- Có audit log cho thay đổi recipient và schedule.

---

## 31. Report history

Lịch sử gồm:

- Report name.
- Template.
- Người tạo.
- Scope.
- Status.
- Generated time.
- Expiry.
- Download count nếu policy cho phép.
- Error code.

Filter:

- Status.
- Category.
- Date.
- Creator.
- Format.

---

## 32. Retention và expiry

Mỗi loại report có retention khác nhau.

Ví dụ định hướng:

- File thông thường: 24–72 giờ.
- Báo cáo lớn: ngắn hơn nếu tốn storage.
- Báo cáo audit hoặc pháp lý: theo policy riêng.

Khi file hết hạn:

- History có thể vẫn giữ.
- Download action biến mất.
- Cho phép regenerate nếu còn quyền.

Không giữ export chứa PII vô thời hạn.

---

## 33. Notification integration

Notification events:

- Report queued.
- Report completed.
- Report failed.
- Scheduled report completed.
- Scheduled report failed.
- File sắp hết hạn nếu cần.

Không toast mọi bước progress.

Notification completed có action `Tải xuống` nếu URL còn hiệu lực.

---

## 34. Error handling

### Validation error

Hiển thị field và lý do.

### Permission error

```text
Bạn không có quyền xuất báo cáo này.
```

### Job failure

- Hiển thị error code chuẩn hóa.
- Cho retry nếu lỗi tạm thời và job idempotent.
- Có request/job ID.

### Download expired

```text
File đã hết hạn. Hãy tạo lại báo cáo.
```

Không hiển thị stack trace hoặc storage path nội bộ.

---

## 35. Retry rules

Cho retry khi:

- Worker timeout.
- Storage tạm lỗi.
- Upstream tạm thời không khả dụng.

Không tự retry vô hạn khi:

- Permission denied.
- Invalid filters.
- Unsupported format.
- Data contract lỗi lặp lại.

Có retry count và backoff.

---

## 36. Cancel behavior

- Chỉ cancel queued/processing nếu backend hỗ trợ.
- Cancel không đảm bảo xóa ngay file tạm; backend phải cleanup.
- UI chờ backend confirmation.
- Không optimistic chuyển completed job thành cancelled.

---

## 37. Bulk actions

Full page có thể hỗ trợ:

- Download nhiều file qua từng link.
- Delete own expired history.
- Disable schedules.

Không zip nhiều file chứa dữ liệu nhạy cảm ở frontend.

Bulk delete cần confirmation và permission.

---

## 38. Data privacy

- Backend chỉ export field user có quyền xem.
- Masking phải nhất quán với UI.
- Không ghi raw filter PII vào analytics.
- Không gửi full report content vào error monitoring.
- File tạm phải được mã hóa khi lưu trữ nếu hạ tầng yêu cầu.
- Không dùng public CDN cho file private.

---

## 39. Audit requirements

Bắt buộc hoặc khuyến nghị mạnh:

```text
report.requested
report.completed
report.downloaded
report.failed
report.schedule.created
report.schedule.updated
report.schedule.disabled
report.history.deleted
```

Audit gồm actor, template key, scope summary, format, result, job ID, request ID và timestamp.

Không ghi toàn bộ dữ liệu report vào audit payload.

---

## 40. Analytics events

```text
reports.center.viewed
reports.template.opened
reports.export.requested
reports.export.completed
reports.export.failed
reports.export.downloaded
reports.preset.saved
reports.schedule.created
```

Properties an toàn:

- Template key.
- Category.
- Format.
- Row-count bucket.
- Duration bucket.
- File-size bucket.
- Error code.

Không gửi giá trị tài chính chi tiết hoặc PII.

---

## 41. Observability

Theo dõi:

- Job queue depth.
- Queue wait time.
- Processing duration.
- Success/failure rate.
- Retry rate.
- Worker utilization.
- File upload failure.
- Download failure.
- Expired-file access rate.
- Permission denied anomalies.

Trace spans:

```text
report.estimate
report.generate
report.upload
report.download_authorize
```

---

## 42. API endpoints đề xuất

```text
GET    /api/v1/reports/templates
GET    /api/v1/reports/templates/:key
POST   /api/v1/reports/estimate
POST   /api/v1/reports/jobs
GET    /api/v1/reports/jobs
GET    /api/v1/reports/jobs/:id
POST   /api/v1/reports/jobs/:id/cancel
POST   /api/v1/reports/jobs/:id/retry
POST   /api/v1/reports/jobs/:id/download-url
GET    /api/v1/reports/presets
POST   /api/v1/reports/presets
PATCH  /api/v1/reports/presets/:id
DELETE /api/v1/reports/presets/:id
GET    /api/v1/reports/schedules
POST   /api/v1/reports/schedules
PATCH  /api/v1/reports/schedules/:id
DELETE /api/v1/reports/schedules/:id
```

---

## 43. Frontend component inventory

```text
ReportsCenterPage
├── ReportsSummaryCards
├── ReportCatalog
├── ReportTemplateCard
├── ReportBuilderDrawer
├── ReportFilterForm
├── ReportColumnSelector
├── ReportEstimatePanel
├── ExportJobTable
├── ExportJobStatus
├── ReportHistoryTable
├── SavedPresetList
├── ScheduleList
├── ScheduleEditor
└── ReportDetailsDrawer
```

---

## 44. Loading states

- Catalog skeleton.
- Job row skeleton.
- Estimate loading state.
- Schedule list skeleton.

Không hiển thị `0 báo cáo` trước khi request hoàn tất.

---

## 45. Empty states

### Chưa có lịch sử

```text
Bạn chưa tạo báo cáo nào.
```

### Không có job đang chạy

```text
Không có báo cáo nào đang được xử lý.
```

### Chưa có lịch

```text
Bạn chưa thiết lập báo cáo định kỳ.
```

### Filter không có kết quả

```text
Không tìm thấy báo cáo phù hợp với bộ lọc.
```

---

## 46. Responsive behavior

### Desktop

- Catalog 3–4 cột.
- History dùng table.
- Builder mở drawer rộng 520–640px.

### Tablet

- Catalog 2 cột.
- Table chuyển horizontal scroll hoặc card list.

### Mobile

- Catalog một cột.
- Builder full-screen sheet.
- Job/history hiển thị card list.
- Touch target tối thiểu 44px.

Không cố hiển thị column selector phức tạp trong modal hẹp.

---

## 47. Accessibility

- Filter form có label và error association.
- Progress có text, không chỉ thanh màu.
- Status badge có semantic text.
- Tables có caption và headers.
- Drawer/dialog quản lý focus.
- File size và expiry đọc được rõ.
- Keyboard có thể tạo, theo dõi và tải báo cáo.
- Live update progress dùng `aria-live=polite` có kiểm soát.

---

## 48. Testing

### Unit

- File naming.
- CSV neutralization.
- Estimate formatting.
- Status mapping.
- Expiry calculation.
- Permission-based columns.

### Component

- Template catalog.
- Filter validation.
- Column selection.
- Estimate panel.
- Job progress.
- Error/retry states.
- Schedule form.

### Integration

- Tạo async job.
- Job completed cập nhật Notification Center.
- Download URL kiểm tra lại permission.
- Preset không mở rộng scope.
- Schedule bị disable khi owner mất quyền.
- Retry và cancel đúng trạng thái.

### E2E

- Tạo sales XLSX.
- Tạo inventory CSV lớn.
- Theo dõi progress.
- Tải file hoàn tất.
- Tạo lịch báo cáo hàng tuần.
- Finance user export đúng branch.
- Viewer thiếu quyền không thấy finance template.

---

## 49. Visual QA

Baseline:

```text
Reports Center — 1440 × 900
Report Catalog
Builder Default
Builder Validation Error
Large Export Estimate
Queued Job
Processing Job
Completed Job
Failed Job
Expired File
Schedule Editor
Mobile Reports Center
```

Kiểm tra:

- Status readability.
- Progress alignment.
- Long report names.
- Filter wrapping.
- Table overflow.
- Signed download action states.
- Focus rings.

---

## 50. Performance budgets

Mục tiêu UI tham khảo:

```text
Reports Center LCP p75 < 2,5s
Catalog API p95 < 1s
Estimate API p95 < 1,5s
Job list refresh < 1s
Download authorization < 1s
```

Job tạo file có SLA riêng theo kích thước và loại báo cáo.

---

## 51. AI coding agent requirements

AI coding agent phải:

- Không tạo export bằng raw SQL từ client.
- Không cho preset hoặc schedule mở rộng permission/scope.
- Không lưu signed URL lâu dài.
- Không bỏ CSV injection protection.
- Không báo completed trước backend confirmation.
- Không gửi file content hoặc PII vào analytics/logs.
- Bổ sung test cho expiry, permission recheck, retry, cancel và scheduled delivery.
- Báo rõ API, worker, storage và security assumptions.

---

## 52. Acceptance checklist

- [ ] Có Report Catalog theo category.
- [ ] Template dùng registry và permission rõ.
- [ ] Hỗ trợ CSV, XLSX và PDF đúng mục đích.
- [ ] Có filter, column selector và estimate.
- [ ] Có sync/async execution mode.
- [ ] Có Export Job queue và progress.
- [ ] Download dùng signed URL có expiry.
- [ ] Có CSV injection protection.
- [ ] Có saved presets.
- [ ] Có scheduled reports và delivery controls.
- [ ] Có history, retention và expired state.
- [ ] Notification integration đầy đủ.
- [ ] Permission và scope được backend enforce lại khi tạo và tải.
- [ ] Audit, analytics và observability được xác định.
- [ ] Loading, empty, error và responsive states đầy đủ.
- [ ] Accessibility hoàn chỉnh.
- [ ] Unit, component, integration, E2E và Visual QA đầy đủ.

---

## 53. Những điều không được làm

- Không trả dữ liệu toàn hệ thống rồi lọc trong file ở client.
- Không cho user chọn field ngoài permission.
- Không lưu signed URL lâu dài hoặc dùng public file URL.
- Không dùng CSV thiếu chống formula injection.
- Không giữ file chứa PII vô thời hạn.
- Không gửi report qua recipient chưa được kiểm tra.
- Không optimistic update completed status.
- Không retry vô hạn lỗi không thể phục hồi.
- Không cho schedule tiếp tục chạy khi owner mất quyền.
- Không dùng PDF cho dữ liệu cực lớn.
- Không ghi report content vào analytics hoặc logs.

---

## 54. Kết luận

Reports & Export Center của Cynca VLXD phải cung cấp quy trình xuất dữ liệu rõ ràng, có kiểm soát, có khả năng theo dõi và an toàn theo permission. Mọi báo cáo phải dựa trên template đã đăng ký, giữ đúng scope, tạo qua job phù hợp, lưu file private có thời hạn và để lại audit trail cho các thao tác nhạy cảm.

File tiếp theo đề xuất:

```text
README.md
39-Audit-Activity-Center.md
```