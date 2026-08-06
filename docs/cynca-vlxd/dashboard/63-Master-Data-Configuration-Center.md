# Cynca VLXD Admin Dashboard — Master Data & Configuration Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý dữ liệu danh mục dùng chung và cấu hình chuẩn hóa cho toàn hệ thống

---

## 1. Mục tiêu

Master Data & Configuration Center giúp Cynca VLXD quản lý các dữ liệu nền dùng chung như danh mục vật liệu xây dựng, đơn vị tính, thuế, chi nhánh, kho, nhà cung cấp, hãng vận chuyển, phương thức thanh toán và các bộ mã trạng thái.

Hệ thống phải bảo đảm:

- Một nguồn dữ liệu chuẩn cho toàn hệ thống.
- Có owner, version và effective date.
- Không tạo mã trùng hoặc quan hệ không hợp lệ.
- Có impact preview trước thay đổi lớn.
- Có approval cho dữ liệu nhạy cảm.
- Có audit và rollback có kiểm soát.

---

## 2. Information architecture

```text
Master Data & Configuration Center
├── Tổng quan
├── Product Categories
├── Material Types
├── Units of Measure
├── Tax & Pricing References
├── Branches & Warehouses
├── Suppliers
├── Shipping Providers
├── Payment Methods
├── Status Dictionaries
├── Reference Tables
├── Change Requests
└── Audit History
```

---

## 3. Permission model

```text
master_data.center.view
master_data.catalog.view
master_data.catalog.manage
master_data.units.manage
master_data.tax.manage
master_data.branches.manage
master_data.warehouses.manage
master_data.suppliers.manage
master_data.shipping.manage
master_data.payments.manage
master_data.statuses.manage
master_data.publish
master_data.rollback
master_data.audit.view
```

Publish, tax, payment, branch và warehouse changes là quyền nhạy cảm.

---

## 4. Master record contract

```ts
export type MasterDataRecord = {
  id: string;
  domain: string;
  code: string;
  name: string;
  description?: string;
  status: "draft" | "active" | "inactive" | "deprecated" | "archived";
  parentId?: string;
  effectiveFrom?: string;
  effectiveTo?: string;
  version: number;
  ownerUserId: string;
  metadata?: Record<string, string | number | boolean | null>;
  createdAt: string;
  updatedAt: string;
};
```

Code phải unique trong domain và không nên thay đổi sau khi đã được sử dụng rộng rãi.

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Active master records.
2. Change requests chờ duyệt.
3. Duplicate/conflict warnings.
4. Deprecated records đang còn sử dụng.
5. Records sắp hết hiệu lực.
6. Integrity issues.

Widgets:

- Recent changes.
- Usage impact.
- Orphan records.
- Duplicate candidates.
- Effective-date calendar.
- Critical reference-data alerts.

---

## 6. Product categories

Cấu trúc tham chiếu:

```text
Vật liệu xây dựng
├── Xi măng
├── Gạch
├── Sắt thép
├── Cát đá
├── Sơn & chống thấm
├── Tấm trần & vách
├── Thiết bị điện nước
└── Dụng cụ thi công
```

Yêu cầu:

- Category code ổn định.
- Parent-child validation.
- Sort order.
- SEO/display name nếu dùng ngoài Mini App.
- Icon/image reference.
- Active/inactive state.

Không cho tạo vòng lặp category.

---

## 7. Material types và attributes

Mỗi material type có thể khai báo:

- Tên loại.
- Attribute schema.
- Required attributes.
- Units.
- Validation rules.
- Product-template mapping.

Ví dụ với sắt thép:

```text
Grade
Diameter
Length
Weight per unit
Standard
```

Không cho arbitrary code làm validation rule.

---

## 8. Units of measure

```ts
export type UnitOfMeasure = {
  id: string;
  code: string;
  name: string;
  category: "count" | "weight" | "length" | "area" | "volume" | "package";
  baseUnitCode?: string;
  conversionFactor?: number;
  precision: number;
  status: "active" | "deprecated";
};
```

Yêu cầu:

- Conversion chính xác.
- Precision rõ.
- Không dùng floating-point thiếu kiểm soát cho conversion quan trọng.
- Không đổi base unit khi dữ liệu đang hoạt động thiếu migration plan.

---

## 9. Tax and pricing references

Bao gồm:

- Tax code.
- Tax rate.
- Effective dates.
- Price type.
- Customer group.
- Branch applicability.
- Rounding policy.

Không hardcode thuế hoặc rounding trong frontend.

---

## 10. Branches and warehouses

Branch fields:

- Code.
- Name.
- Address.
- Status.
- Manager.
- Default warehouse.
- Timezone.

Warehouse fields:

- Code.
- Branch.
- Type.
- Address.
- Inventory policy.
- Negative-stock policy.
- Default unit policy.

Không delete branch/kho đã có dữ liệu nghiệp vụ; dùng inactive/archive.

---

## 11. Suppliers

Reference fields:

- Supplier code.
- Legal name.
- Display name.
- Tax code.
- Contact reference.
- Product categories.
- Status.
- Payment terms.
- Default lead time.

Dữ liệu nhạy cảm phải dùng permission riêng.

---

## 12. Shipping providers

Fields:

- Provider code.
- Name.
- Service types.
- Supported regions.
- SLA references.
- Tracking URL template registry.
- Integration ID.
- Status.

Không cho nhập tracking URL hoặc endpoint tùy ý thiếu validation.

---

## 13. Payment methods

Ví dụ:

```text
COD
Bank transfer
Payment gateway
Internal credit
```

Mỗi method gồm:

- Code.
- Display name.
- Allowed channels.
- Branch scope.
- Reconciliation rule.
- Refund support.
- Status.

Không lưu credential thanh toán trong master-data record.

---

## 14. Status dictionaries

Domains:

- Order status.
- Payment status.
- Shipment status.
- Ticket status.
- Inventory status.
- User status.

Mỗi status có:

- Code.
- Label.
- Semantic color token.
- Sort order.
- Terminal state.
- Allowed transitions.

Không đổi status code đã dùng trong integrations thiếu migration.

---

## 15. Reference table governance

Mỗi domain cần:

- Owner.
- Steward.
- Schema.
- Version.
- Effective date.
- Usage references.
- Change approval policy.

Không cho duplicate source of truth giữa nhiều module.

---

## 16. Change workflow

```text
Draft
→ Validate
→ Impact analysis
→ Review
→ Approve
→ Schedule
→ Publish
→ Verify
```

Low-risk text changes có thể dùng flow ngắn hơn; structural changes cần đầy đủ.

---

## 17. Impact analysis

Hiển thị:

- Records bị ảnh hưởng.
- Products/orders/inventory references.
- APIs/integrations.
- Reports.
- Imports/exports.
- Branches.
- Migration requirement.
- Rollback feasibility.

Không publish structural change thiếu impact preview.

---

## 18. Duplicate and conflict detection

Phát hiện:

- Duplicate code.
- Duplicate normalized name.
- Overlapping effective dates.
- Invalid parent.
- Invalid conversion.
- Status transition conflict.
- Branch/warehouse mismatch.

Không auto-merge master records thiếu review.

---

## 19. Deprecation workflow

```text
Mark deprecated
→ Select replacement
→ Identify references
→ Migrate references
→ Verify
→ Archive
```

Deprecated record vẫn phải đọc được cho dữ liệu lịch sử.

---

## 20. Bulk import/export

- Import dùng Data Import Center.
- Export dùng Reports & Export Center.
- Có template version.
- Có preview và validation.
- Không bypass approval bằng bulk file.

---

## 21. API endpoints đề xuất

```text
GET  /api/v1/master-data/summary
GET  /api/v1/master-data/domains
GET  /api/v1/master-data/:domain
POST /api/v1/master-data/:domain
PATCH /api/v1/master-data/:domain/:id
POST /api/v1/master-data/:domain/:id/validate
POST /api/v1/master-data/:domain/:id/submit-review
POST /api/v1/master-data/:domain/:id/publish
POST /api/v1/master-data/:domain/:id/deprecate
GET  /api/v1/master-data/:domain/:id/usage
GET  /api/v1/master-data/change-requests
```

---

## 22. Frontend component inventory

```text
MasterDataConfigurationCenterPage
├── MasterDataSummaryCards
├── DomainNavigation
├── MasterDataTable
├── RecordEditorDrawer
├── CategoryTree
├── UnitConversionEditor
├── StatusTransitionEditor
├── EffectiveDatePanel
├── ImpactAnalysisDialog
├── DuplicateConflictPanel
├── ChangeRequestQueue
└── UsageReferencePanel
```

---

## 23. Security and audit

- Backend enforce domain và field permissions.
- Không trả financial/supplier sensitive fields ngoài quyền.
- Structural changes cần version và approval.
- Audit create, update, publish, deprecate và rollback.

Audit events:

```text
master_data.record.created
master_data.record.updated
master_data.record.published
master_data.record.deprecated
master_data.record.archived
master_data.change.approved
master_data.rollback.completed
```

---

## 24. Testing

### Unit

- Code uniqueness.
- Effective-date overlap.
- Conversion precision.
- Category cycle detection.
- Status transition validation.

### Integration

- Deprecated record vẫn đọc được cho history.
- Structural change chặn khi có unresolved impact.
- Branch/kho mismatch bị chặn.
- Cross-domain permission bị chặn.

### E2E

- Tạo category mới.
- Tạo unit conversion.
- Tạo warehouse.
- Cập nhật payment method.
- Deprecate status có replacement.
- Publish change và verify usage.

---

## 25. Acceptance checklist

- [ ] Có domain registry.
- [ ] Có product categories và material types.
- [ ] Có units, tax và pricing references.
- [ ] Có branches, warehouses và suppliers.
- [ ] Có shipping providers và payment methods.
- [ ] Có status dictionaries.
- [ ] Có versioning, effective date và ownership.
- [ ] Có impact analysis và duplicate detection.
- [ ] Có deprecation workflow.
- [ ] Có permission, audit, responsive và accessibility.
- [ ] Có testing đầy đủ.

---

## 26. Những điều không được làm

- Không hardcode master data trong frontend.
- Không đổi code ổn định thiếu migration.
- Không delete record đang được sử dụng.
- Không tạo vòng lặp category.
- Không đổi conversion hoặc tax thiếu effective date.
- Không publish structural change thiếu impact analysis.
- Không auto-merge duplicate thiếu review.

---

## 27. Kết luận

Master Data & Configuration Center của Cynca VLXD phải cung cấp một nguồn dữ liệu chuẩn, version hóa và có thể kiểm chứng cho mọi module. Các thay đổi phải giữ ổn định mã, quan hệ, effective date và references để không làm sai lệch sản phẩm, đơn hàng, tồn kho, báo cáo hoặc integrations.
