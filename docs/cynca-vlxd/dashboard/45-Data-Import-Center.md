# Cynca VLXD Admin Dashboard — Data Import Center Specification

> Phiên bản: 1.0  
> Phạm vi: Trung tâm nhập dữ liệu hàng loạt cho sản phẩm, khách hàng, tồn kho, giá, đơn hàng và dữ liệu quản trị  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Data Operations, Sales Operations, Warehouse, Finance, QA, Security và AI coding agent  
> Tài liệu liên quan: `16-Data-Contracts.md`, `23-Security-Privacy.md`, `24-Observability.md`, `38-Reports-Export-Center.md`, `40-User-Role-Management.md`

---

## 1. Mục tiêu

Data Import Center giúp Cynca VLXD nhập dữ liệu có cấu trúc vào hệ thống một cách an toàn, có kiểm tra, có thể xem trước và truy vết đầy đủ.

Hệ thống phải hỗ trợ:

- Tải template nhập liệu theo từng loại dữ liệu.
- Upload CSV hoặc XLSX.
- Kiểm tra cấu trúc file trước khi xử lý.
- Mapping cột từ file sang field hệ thống.
- Validate dữ liệu theo từng dòng.
- Preview dữ liệu hợp lệ và không hợp lệ.
- Chạy import theo background job với file lớn.
- Theo dõi tiến độ, lỗi và kết quả.
- Cho phép tải file lỗi để sửa và nhập lại.
- Hỗ trợ dry run trước khi ghi dữ liệu thật.
- Không cho import vượt permission hoặc data scope.
- Audit các import ảnh hưởng tới dữ liệu quan trọng.

Data Import Center không phải công cụ chạy SQL, sửa database trực tiếp hoặc bỏ qua quy trình nghiệp vụ.

---

## 2. Nhóm dữ liệu hỗ trợ

```ts
export type ImportEntityType =
  | "products"
  | "product_prices"
  | "product_categories"
  | "inventory_balances"
  | "inventory_adjustments"
  | "customers"
  | "suppliers"
  | "orders"
  | "order_status_updates"
  | "users"
  | "branch_assignments"
  | "support_tickets";
```

Không nhất thiết bật toàn bộ entity ngay từ đầu. Mỗi entity phải có schema, permission và workflow riêng.

---

## 3. Use cases tham chiếu

### Sản phẩm

- Tạo mới nhiều SKU.
- Cập nhật tên, mô tả, category và thuộc tính.
- Bổ sung ảnh URL đã được kiểm soát.

### Giá

- Cập nhật giá bán theo chi nhánh.
- Thiết lập ngày hiệu lực.
- Import bảng giá theo nhóm khách hàng nếu được hỗ trợ.

### Tồn kho

- Import số dư đầu kỳ.
- Điều chỉnh tồn có reason và approval.
- Đồng bộ số lượng từ hệ thống ngoài.

### Khách hàng

- Import hồ sơ doanh nghiệp.
- Gán nhân viên phụ trách.
- Phân nhóm khách hàng.

### Người dùng

- Tạo lời mời hàng loạt.
- Gán role và branch scope.

---

## 4. Permission model

```text
imports.center.view
imports.products.create
imports.products.update
imports.prices.update
imports.inventory.view
imports.inventory.adjust
imports.customers.create
imports.customers.update
imports.orders.create
imports.orders.update_status
imports.users.create
imports.branch_assignments.update
imports.jobs.view
imports.jobs.cancel
imports.jobs.retry
imports.error_files.download
imports.audit.view
```

Mỗi import template phải kiểm tra permission riêng.

Không dựa duy nhất vào role name.

---

## 5. Information architecture

```text
Data Import Center
├── Tổng quan
├── Chọn loại dữ liệu
├── Tải template
├── Upload file
├── Mapping cột
├── Validation
├── Preview
├── Đang xử lý
├── Lịch sử import
├── File lỗi
└── Saved mappings
```

---

## 6. Trang tổng quan

Khu vực chính:

- Import gần đây.
- Job đang xử lý.
- Job thất bại.
- Template phổ biến.
- Saved mappings.
- Cảnh báo import nhạy cảm.

KPI nhẹ:

- Số import trong 7 ngày.
- Tổng dòng đã xử lý.
- Tỷ lệ dòng lỗi.
- Job đang chờ.

Không cần chart phức tạp nếu không giúp vận hành.

---

## 7. Import template registry

```ts
export type ImportTemplateDefinition = {
  key: string;
  name: string;
  description?: string;
  entityType: ImportEntityType;
  version: number;
  requiredPermissions: string[];
  supportedFormats: Array<"csv" | "xlsx">;
  columns: ImportColumnDefinition[];
  maxFileSizeBytes: number;
  maxRows: number;
  supportsCreate: boolean;
  supportsUpdate: boolean;
  supportsUpsert: boolean;
  supportsDryRun: boolean;
  requiresApproval: boolean;
};
```

Template key phải map tới registry backend, không phải raw table name.

---

## 8. Import column definition

```ts
export type ImportColumnDefinition = {
  key: string;
  label: string;
  dataType: "text" | "number" | "currency" | "boolean" | "date" | "datetime" | "enum" | "identifier";
  required: boolean;
  unique?: boolean;
  allowedValues?: string[];
  referenceType?: string;
  sensitive?: boolean;
  example?: string;
  description?: string;
};
```

Mỗi cột phải có label, loại dữ liệu, ví dụ và quy tắc rõ.

---

## 9. Template download

Template cần có:

- Header chuẩn.
- Dòng ví dụ tùy chọn.
- Sheet `Hướng dẫn` đối với XLSX.
- Danh sách giá trị hợp lệ nếu có.
- Phiên bản template.
- Ngày tạo.
- Ghi chú timezone và định dạng ngày.

Không chèn macro.

Không chứa dữ liệu production hoặc PII thật.

---

## 10. Template versioning

Mỗi file template nên có metadata:

```text
Template key
Template version
Generated at
Locale
```

Khi upload template cũ:

- Hệ thống nhận diện version.
- Cảnh báo field mới hoặc deprecated.
- Hỗ trợ migration nếu an toàn.
- Không tự đoán field đã thay đổi thiếu xác nhận.

---

## 11. File formats

### CSV

- Encoding UTF-8.
- Có hỗ trợ BOM nếu cần Excel tiếng Việt.
- Delimiter được nhận diện hoặc quy định rõ.
- Chống CSV formula injection ở file kết quả và error file.

### XLSX

- Chỉ đọc sheet được chỉ định.
- Giới hạn số sheet.
- Không chạy macro.
- Kiểm tra merged cells và hidden rows nếu policy yêu cầu.

Không hỗ trợ XLS/XLSM mặc định nếu không có lý do rõ.

---

## 12. Upload component

Hiển thị:

- Drag and drop zone.
- Nút chọn file.
- Định dạng hỗ trợ.
- Kích thước tối đa.
- Số dòng tối đa.
- Link tải template.
- Link hướng dẫn.

Trạng thái:

```text
Idle
Uploading
Uploaded
Scanning
Rejected
Ready for Mapping
```

---

## 13. Upload security

Yêu cầu:

- MIME/type whitelist.
- Kiểm tra extension và signature file.
- Giới hạn dung lượng.
- Malware scanning.
- Lưu file trong private storage.
- Signed upload/download URL.
- File tự hết hạn.
- Không render file HTML hoặc script.
- Không tin filename do người dùng cung cấp.

---

## 14. File parsing

Backend phải:

- Parse trong môi trường cô lập phù hợp.
- Giới hạn memory và execution time.
- Chặn zip bomb hoặc file nén bất thường trong XLSX.
- Giới hạn số row, column và cell.
- Chuẩn hóa Unicode.
- Không thực thi formula.

Frontend không parse toàn bộ file lớn nếu có thể gây treo trình duyệt.

---

## 15. Import modes

```ts
export type ImportMode = "create" | "update" | "upsert";
```

### Create

Chỉ tạo bản ghi mới. Trùng khóa sẽ lỗi.

### Update

Chỉ cập nhật bản ghi đã tồn tại. Không tìm thấy sẽ lỗi.

### Upsert

Tạo mới nếu chưa tồn tại, cập nhật nếu đã có.

Upsert phải được bật có chọn lọc vì có rủi ro ghi đè dữ liệu.

---

## 16. Match key

Các khóa có thể dùng:

- SKU.
- Product ID.
- Customer code.
- Email nội bộ.
- Order code.
- External reference.

Quy tắc:

- Match key phải unique và ổn định.
- Không dùng tên hiển thị làm khóa duy nhất.
- Không cho người dùng tự chọn field nhạy cảm không được registry hỗ trợ.

---

## 17. Column mapping

Màn hình mapping gồm:

- Cột file bên trái.
- Field hệ thống bên phải.
- Kiểu dữ liệu.
- Sample values.
- Mapping status.
- Warning/error.

Hỗ trợ:

- Auto-map theo header.
- Manual map.
- Ignore column.
- Save mapping preset.

Không cho một cột file map vào nhiều field nếu schema không hỗ trợ.

---

## 18. Mapping confidence

Auto-map có thể trả:

```text
Exact
High confidence
Needs review
Unmapped
```

Người dùng phải review mapping `Needs review`.

Không tự chạy import chỉ vì auto-map đạt confidence cao.

---

## 19. Saved mapping

```ts
export type SavedImportMapping = {
  id: string;
  name: string;
  templateKey: string;
  templateVersion: number;
  sourceHeaders: string[];
  mappings: Record<string, string | null>;
  ownerUserId: string;
  visibility: "private" | "team" | "branch";
  createdAt: string;
  updatedAt: string;
};
```

Saved mapping không được mở rộng permission hoặc field nhạy cảm.

---

## 20. Data transformations

Chỉ hỗ trợ transformations đã đăng ký:

- Trim whitespace.
- Normalize casing.
- Parse number.
- Parse date theo format.
- Map enum value.
- Add fixed branch ID trong scope được phép.
- Split hoặc combine field đã định nghĩa.

Không cho nhập JavaScript, SQL hoặc expression tùy ý.

---

## 21. Validation layers

### File-level validation

- Định dạng.
- Dung lượng.
- Sheet.
- Số dòng/cột.
- Template version.

### Schema validation

- Required columns.
- Data type.
- Enum.
- Date format.
- Length.

### Business validation

- SKU unique.
- Branch tồn tại.
- Warehouse thuộc branch.
- Price hợp lệ.
- Transition đơn hàng hợp lệ.
- User không được gán role ngoài quyền.

### Referential validation

- Category tồn tại.
- Supplier tồn tại.
- Order tồn tại.
- Customer code hợp lệ.

---

## 22. Validation result contract

```ts
export type ImportValidationResult = {
  importId: string;
  totalRows: number;
  validRows: number;
  warningRows: number;
  invalidRows: number;
  duplicateRows: number;
  errorsByCode: Record<string, number>;
  warningsByCode: Record<string, number>;
  sampleErrors: ImportRowIssue[];
  canProceed: boolean;
};
```

---

## 23. Row issue contract

```ts
export type ImportRowIssue = {
  rowNumber: number;
  fieldKey?: string;
  code: string;
  severity: "error" | "warning";
  message: string;
  rawValue?: string;
  suggestedValue?: string;
};
```

Raw value nhạy cảm phải được mask theo permission.

---

## 24. Error code conventions

Ví dụ:

```text
REQUIRED_VALUE_MISSING
INVALID_DATE_FORMAT
INVALID_NUMBER
UNKNOWN_CATEGORY
DUPLICATE_SKU_IN_FILE
DUPLICATE_SKU_IN_SYSTEM
BRANCH_OUT_OF_SCOPE
INVALID_STATUS_TRANSITION
PERMISSION_DENIED_FIELD
```

Error message phải dễ hiểu và có hướng dẫn sửa.

Không chỉ hiển thị `Validation failed` chung chung.

---

## 25. Warning behavior

Warning không nhất thiết chặn import.

Ví dụ:

- Mô tả sản phẩm trống nhưng không bắt buộc.
- Số điện thoại không chuẩn hóa đầy đủ.
- Category deprecated.
- Giá mới giảm mạnh so với hiện tại.

Import chỉ được tiếp tục nếu policy cho phép và người dùng xác nhận warning.

---

## 26. Duplicate handling

Phân biệt:

- Duplicate trong file.
- Duplicate trong hệ thống.
- Duplicate theo external reference.

Options có thể gồm:

```text
Reject duplicates
Keep first
Keep last
Merge theo rule được định nghĩa
```

Không dùng merge tự do thiếu business rule.

---

## 27. Preview screen

Preview hiển thị:

- Tổng số dòng.
- Số valid/warning/error.
- Data diff đối với update/upsert.
- Scope bị ảnh hưởng.
- Số bản ghi tạo mới.
- Số bản ghi cập nhật.
- Số bản ghi bỏ qua.
- Cảnh báo nhạy cảm.

Không cần hiển thị toàn bộ file nếu rất lớn; dùng mẫu và filter theo trạng thái.

---

## 28. Before/after diff

Đối với update/upsert, hiển thị:

| Field | Hiện tại | Sau import |
|---|---|---|
| Giá bán | 120.000 ₫ | 128.000 ₫ |
| Trạng thái | Active | Inactive |

Sensitive fields phải mask.

Không cho import cập nhật field không được phép chỉ vì cột có trong file.

---

## 29. Dry run

Dry run thực hiện toàn bộ:

- Upload.
- Parse.
- Mapping.
- Validation.
- Business rules.
- Diff.

Nhưng không ghi dữ liệu thật.

Kết quả dry run phải ghi rõ:

```text
Đây là bản chạy thử. Chưa có dữ liệu nào được thay đổi.
```

---

## 30. Approval workflow

Import nhạy cảm cần approval:

- Inventory adjustment.
- Price change diện rộng.
- User/role assignment.
- Order status update hàng loạt.
- Finance-related import.

Workflow:

```text
Draft
→ Validated
→ Submitted for Approval
→ Approved / Rejected
→ Executed
```

Người tạo không được tự duyệt nếu policy phân tách nhiệm vụ.

---

## 31. Import job contract

```ts
export type DataImportJob = {
  id: string;
  templateKey: string;
  templateVersion: number;
  entityType: ImportEntityType;
  mode: ImportMode;
  status: "uploaded" | "mapping" | "validating" | "awaiting_approval" | "queued" | "processing" | "completed" | "partially_completed" | "failed" | "cancelled" | "expired";
  requestedBy: string;
  approvedBy?: string;
  totalRows?: number;
  processedRows?: number;
  successRows?: number;
  warningRows?: number;
  failedRows?: number;
  progressPercent?: number;
  createdAt: string;
  startedAt?: string;
  completedAt?: string;
  expiresAt?: string;
  availableActions: string[];
};
```

---

## 32. Execution strategy

### Small files

Có thể xử lý đồng bộ nếu nhanh và an toàn.

### Large files

Bắt buộc background job.

Yêu cầu:

- Chunk processing.
- Progress tracking.
- Idempotency.
- Retry có giới hạn.
- Transaction strategy rõ.
- Partial failure handling.

Không giữ HTTP request mở lâu cho file lớn.

---

## 33. Atomic và partial import

### Atomic

Toàn bộ thành công hoặc rollback toàn bộ.

Phù hợp:

- Import nhỏ.
- Dữ liệu cần nhất quán cao.

### Partial

Dòng hợp lệ được ghi, dòng lỗi bị bỏ qua.

Phù hợp:

- Dataset lớn.
- Entity độc lập.

UI phải cho biết rõ strategy trước khi chạy.

Không âm thầm chuyển từ atomic sang partial.

---

## 34. Idempotency

Mỗi import cần:

- Import ID.
- File hash.
- Template version.
- Idempotency key.

Khi upload lại cùng file:

- Cảnh báo đã nhập trước đó.
- Cho xem job cũ.
- Chỉ cho chạy lại khi người dùng xác nhận và policy cho phép.

---

## 35. Cancel behavior

Cho phép cancel khi:

- Job queued.
- Job processing nhưng backend hỗ trợ dừng an toàn.

Yêu cầu:

- Chờ backend confirmation.
- Hiển thị dữ liệu đã ghi nếu partial strategy.
- Cleanup file tạm.
- Audit action.

Không báo cancelled nếu worker vẫn đang ghi dữ liệu.

---

## 36. Retry behavior

Retry phù hợp khi:

- Worker timeout.
- Storage tạm lỗi.
- Upstream tạm lỗi.

Không retry tự động vô hạn khi:

- Validation lỗi.
- Permission denied.
- Business rule conflict.
- Template version không hỗ trợ.

Retry phải dùng idempotency để tránh tạo trùng.

---

## 37. Import result screen

Hiển thị:

- Status.
- Tổng dòng.
- Thành công.
- Warning.
- Thất bại.
- Thời gian xử lý.
- Người thực hiện.
- Scope.
- Link file lỗi.
- Link audit.
- Các action tiếp theo.

Actions:

- Tải file lỗi.
- Tải kết quả.
- Duplicate configuration.
- Retry.
- Mở bản ghi đã tạo.

---

## 38. Error file

Error file nên gồm:

- Dữ liệu gốc đã được phép hiển thị.
- Row number.
- Error code.
- Error message.
- Suggested value nếu có.

Yêu cầu:

- CSV injection protection.
- Signed URL.
- Expiry.
- PII masking theo quyền.
- Không chứa stack trace hoặc internal IDs không cần thiết.

---

## 39. Import history

Cột đề xuất:

- Import name.
- Entity type.
- Mode.
- Template version.
- Người tạo.
- Scope.
- Status.
- Tổng dòng.
- Success/error.
- Thời gian.
- Actions.

Filters:

- Entity type.
- Status.
- Creator.
- Branch.
- Date.

---

## 40. Retention

Có thể giữ:

- File nguồn: 24–72 giờ hoặc theo policy.
- File lỗi: 7–30 ngày tùy dữ liệu.
- Job metadata: lâu hơn.
- Audit event: theo policy audit.

Không giữ file chứa PII vô thời hạn.

Khi file hết hạn, history vẫn có thể được giữ.

---

## 41. Import-specific rules — Products

Required fields tham khảo:

```text
sku
name
category_code
unit
status
```

Optional:

```text
description
brand
weight
length
width
height
image_url
supplier_code
```

Rules:

- SKU unique.
- Category tồn tại.
- Unit hợp lệ.
- Image URL phải thuộc domain hoặc pipeline được phê duyệt.
- Không tự tải ảnh từ URL nội bộ/private.

---

## 42. Import-specific rules — Prices

Fields:

```text
sku
branch_code
price
currency
effective_from
effective_to
```

Rules:

- Price không âm.
- Currency hợp lệ.
- Branch trong scope.
- Khoảng hiệu lực không chồng lấn nếu policy cấm.
- Thay đổi lớn vượt threshold cần approval.

Không dùng float thiếu kiểm soát cho tiền tệ.

---

## 43. Import-specific rules — Inventory

Fields:

```text
sku
warehouse_code
quantity
unit
reason_code
reference
```

Rules:

- Warehouse trong scope.
- Unit match.
- Adjustment cần reason.
- Không cho negative quantity nếu workflow không hỗ trợ.
- Có approval threshold.
- Có before/after preview.

Không nhập trực tiếp số tồn mới thiếu audit nếu nghiệp vụ yêu cầu adjustment transaction.

---

## 44. Import-specific rules — Customers

Fields:

```text
customer_code
customer_type
name
company_name
email
phone
branch_code
assigned_user_code
```

Rules:

- PII permission.
- Email/phone normalization.
- Duplicate detection.
- Assigned user thuộc scope.
- Không cho import consent status tùy ý thiếu nguồn.

---

## 45. Import-specific rules — Users

Fields:

```text
display_name
employee_code
email
role_key
branch_code
warehouse_code
starts_at
expires_at
```

Rules:

- Không có password trong file.
- Role phải nằm trong phạm vi admin được gán.
- Scope hợp lệ.
- Sensitive role cần approval.
- Temporary access phải có expiry.
- Import tạo invitation, không tự kích hoạt thiếu quy trình.

---

## 46. Search và notification integration

Search trong Import Center hỗ trợ:

- Import ID.
- Filename đã sanitize.
- Template key.
- Creator.
- Error code.

Notification events:

- Validation completed.
- Approval requested.
- Import completed.
- Import partially completed.
- Import failed.
- Error file sắp hết hạn.

Không toast từng row lỗi.

---

## 47. Audit requirements

Bắt buộc audit:

```text
imports.file.uploaded
imports.mapping.saved
imports.validation.completed
imports.approval.requested
imports.approved
imports.executed
imports.completed
imports.partially_completed
imports.failed
imports.cancelled
imports.error_file.downloaded
```

Audit gồm actor, template, version, mode, scope, row counts, result, job ID, request ID và timestamp.

Không ghi toàn bộ file hoặc dữ liệu từng dòng vào audit payload.

---

## 48. API endpoints đề xuất

```text
GET    /api/v1/imports/templates
GET    /api/v1/imports/templates/:key
GET    /api/v1/imports/templates/:key/download
POST   /api/v1/imports/uploads
POST   /api/v1/imports/:id/mapping
POST   /api/v1/imports/:id/validate
GET    /api/v1/imports/:id/validation
POST   /api/v1/imports/:id/submit-approval
POST   /api/v1/imports/:id/approve
POST   /api/v1/imports/:id/execute
GET    /api/v1/imports/jobs
GET    /api/v1/imports/jobs/:id
POST   /api/v1/imports/jobs/:id/cancel
POST   /api/v1/imports/jobs/:id/retry
POST   /api/v1/imports/jobs/:id/error-file-url
GET    /api/v1/imports/mappings
POST   /api/v1/imports/mappings
```

---

## 49. Frontend component inventory

```text
DataImportCenterPage
├── ImportSummaryCards
├── ImportTemplateCatalog
├── ImportUploadStep
├── ColumnMappingEditor
├── MappingConfidenceBadge
├── ImportValidationSummary
├── RowIssueTable
├── ImportPreviewTable
├── ImportDiffViewer
├── ImportApprovalPanel
├── ImportJobTable
├── ImportProgress
├── ImportResultPanel
├── SavedMappingList
└── ImportHistoryTable
```

---

## 50. Loading states

- Template catalog skeleton.
- Upload progress.
- Parsing state.
- Mapping suggestion loading.
- Validation progress.
- Preview skeleton.
- Job progress.

Không hiển thị `0 lỗi` trước khi validation hoàn tất.

---

## 51. Empty states

### Chưa có lịch sử

```text
Bạn chưa thực hiện lần nhập dữ liệu nào.
```

### Không có lỗi

```text
Không phát hiện lỗi trong dữ liệu đã tải lên.
```

### Không có mapping đã lưu

```text
Bạn chưa lưu cấu hình mapping nào.
```

### Filter không có kết quả

```text
Không tìm thấy import phù hợp với bộ lọc.
```

---

## 52. Error states

- Upload lỗi giữ file để người dùng retry nếu an toàn.
- Parse lỗi chỉ rõ sheet/row nếu có thể.
- Mapping lỗi giữ lựa chọn hiện tại.
- Validation service lỗi không được coi là pass.
- Execute lỗi không tự báo rollback nếu backend chưa xác nhận.
- Có job/request ID.
- Không hiển thị stack trace.

---

## 53. Responsive behavior

### Desktop

- Stepper ngang hoặc dọc.
- Mapping 2–3 cột.
- Preview table đầy đủ.

### Tablet

- Mapping chuyển thành từng row card.
- Preview có horizontal scroll.
- Upload và validation vẫn đầy đủ.

### Mobile

- Chỉ hỗ trợ xem history, job status và error summary.
- Upload file nhỏ có thể hỗ trợ nhưng mapping phức tạp nên khuyến nghị desktop.
- Không cố hiển thị diff table lớn trên mobile.

---

## 54. Accessibility

- Upload zone có button alternative.
- Stepper có trạng thái rõ.
- Mapping select có label.
- Validation error liên kết row/field.
- Table có caption và headers.
- Progress có text.
- Không chỉ dùng màu cho valid/warning/error.
- Dialog quản lý focus.
- Keyboard có thể hoàn thành import trên desktop.

---

## 55. Security và privacy

- Backend enforce permission và scope ở mọi bước.
- Không trả dữ liệu toàn hệ thống rồi lọc trong frontend.
- File lưu private và có expiry.
- Không thực thi formula, macro hoặc script.
- Chống zip bomb, malware và file spoofing.
- Sensitive columns phải mask.
- Không lưu file content trong analytics hoặc error monitoring.
- Signed URL có thời hạn.
- Import user/finance/inventory nhạy cảm cần approval và audit.

---

## 56. Analytics events

```text
imports.center.viewed
imports.template.selected
imports.file.upload_started
imports.file.upload_completed
imports.mapping.completed
imports.validation.completed
imports.execute_requested
imports.completed
imports.failed
imports.error_file.downloaded
```

Properties an toàn:

- Template key.
- Entity type.
- Mode.
- Row-count bucket.
- Error-count bucket.
- Duration bucket.

Không gửi filename, row content, SKU, customer data hoặc field values vào analytics.

---

## 57. Observability

Theo dõi:

- Upload failure rate.
- Parsing duration.
- Validation duration.
- Job queue depth.
- Rows processed per second.
- Partial failure rate.
- Retry rate.
- Duplicate detection rate.
- Approval delay.
- Storage cleanup failure.
- Permission denied anomalies.

Trace spans:

```text
import.upload
import.parse
import.validate
import.execute
import.generate_error_file
```

---

## 58. Testing

### Unit

- Header normalization.
- Mapping suggestions.
- Data type parser.
- Validation rules.
- Duplicate detection.
- CSV formula protection.
- Import mode behavior.

### Component

- Upload zone.
- Mapping editor.
- Validation summary.
- Row issue table.
- Diff viewer.
- Progress states.
- Approval panel.

### Integration

- Upload → map → validate → dry run.
- Sensitive import cần approval.
- Branch scope validation.
- Retry dùng idempotency.
- Partial result tạo error file.
- User import không tự kích hoạt.

### E2E

- Import sản phẩm mới.
- Import cập nhật giá.
- Import tồn kho có lỗi.
- Tải error file, sửa và nhập lại.
- Tạo saved mapping.
- Import users theo branch scope.
- Cancel job đang queued.
- Viewer thiếu quyền không thấy inventory adjustment template.

---

## 59. Visual QA

Baseline:

```text
Import Center — 1440 × 900
Template Catalog
Upload Empty
Upload Progress
File Rejected
Column Mapping
Unmapped Columns
Validation Passed
Validation Errors
Preview Create
Preview Update Diff
Awaiting Approval
Processing Job
Partial Completion
Error File Ready
Mobile Job History
```

Kiểm tra:

- Step hierarchy.
- Mapping readability.
- Long Vietnamese headers.
- Error density.
- Table overflow.
- Progress alignment.
- Focus states.

---

## 60. Performance budgets

Mục tiêu UI tham khảo:

```text
Import Center LCP p75 < 2,5s
Template API p95 < 1s
Upload initialization < 1s
Validation summary render < 500ms sau response
Job list refresh < 1s
```

Processing SLA phụ thuộc entity, file size và backend worker.

Không parse file lớn hoàn toàn trên main thread.

---

## 61. AI-assisted mapping requirements

Nếu dùng AI gợi ý mapping:

- Chỉ gửi header và sample đã redact nếu cần.
- Không gửi toàn bộ file chứa PII tới provider chưa phê duyệt.
- Chỉ trả suggestion, không tự xác nhận.
- Hiển thị confidence.
- Người dùng phải review.
- Có fallback rule-based.
- Không dùng AI để tự sửa dữ liệu tài chính, tồn kho hoặc quyền.

---

## 62. AI coding agent requirements

AI coding agent phải:

- Không tạo import từ raw table name hoặc SQL.
- Không bỏ file scanning và parsing limits.
- Không cho mapping arbitrary script.
- Không coi validation service lỗi là pass.
- Không import user/finance/inventory nhạy cảm thiếu approval.
- Không báo completed trước backend confirmation.
- Không retry thiếu idempotency.
- Không lưu file content hoặc PII trong analytics/logs.
- Bổ sung test cho duplicate, cross-scope, CSV injection, zip bomb và partial failure.
- Báo rõ template registry, transaction strategy và backend assumptions.

---

## 63. Acceptance checklist

- [ ] Có Import Template Catalog.
- [ ] Template có version, schema và permission.
- [ ] Hỗ trợ CSV/XLSX an toàn.
- [ ] Upload có scanning và limits.
- [ ] Có Column Mapping và saved mappings.
- [ ] Có transformations theo whitelist.
- [ ] Có file, schema, business và referential validation.
- [ ] Có error codes và row issues.
- [ ] Có duplicate handling.
- [ ] Có Preview và before/after diff.
- [ ] Có Dry Run.
- [ ] Có approval cho import nhạy cảm.
- [ ] Có background job, progress, cancel và retry.
- [ ] Có atomic/partial strategy rõ.
- [ ] Có idempotency.
- [ ] Có result screen và error file.
- [ ] Có import history và retention.
- [ ] Permission và scope được backend enforce.
- [ ] Loading, empty, error và responsive states đầy đủ.
- [ ] Accessibility, security, analytics, observability và testing hoàn chỉnh.

---

## 64. Những điều không được làm

- Không cho import bằng SQL hoặc raw table name.
- Không thực thi macro, formula hoặc script trong file.
- Không tin extension hoặc MIME do client gửi.
- Không cho import field ngoài permission.
- Không dùng tên hiển thị làm khóa match duy nhất.
- Không tự chạy upsert thiếu review.
- Không coi warning là lỗi hoặc bỏ qua warning thiếu policy.
- Không nhập tồn kho, giá hoặc quyền nhạy cảm thiếu approval.
- Không retry thiếu idempotency.
- Không giữ file chứa PII vô thời hạn.
- Không gửi row content hoặc filename vào analytics.
- Không báo dữ liệu đã ghi khi backend chưa xác nhận.

---

## 65. Kết luận

Data Import Center của Cynca VLXD phải biến việc nhập dữ liệu hàng loạt thành một quy trình có kiểm soát: chọn đúng template, upload an toàn, mapping rõ ràng, validation nhiều lớp, preview tác động, approval khi cần và execution có khả năng truy vết. Mọi import phải tuân permission, scope, idempotency, audit và chính sách bảo vệ dữ liệu.

File tiếp theo đề xuất:

```text
README.md
46-Integration-Monitoring-Center.md
```