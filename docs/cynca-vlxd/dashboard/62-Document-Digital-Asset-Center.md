# Cynca VLXD Admin Dashboard — Document & Digital Asset Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý tài liệu, hình ảnh sản phẩm, banner, file thương hiệu và tài sản số

---

## 1. Mục tiêu

Document & Digital Asset Center giúp Cynca VLXD lưu trữ, phân loại, phê duyệt, phân phối và theo dõi toàn bộ tài liệu và tài sản số dùng cho Mini App, Dashboard, sản phẩm và hoạt động vận hành.

Hệ thống phải hỗ trợ:

- Media Library tập trung.
- Ảnh sản phẩm, banner, logo, tài liệu PDF và file nghiệp vụ.
- Folder, taxonomy, tags và search.
- Versioning, approval và publishing.
- Image optimization, thumbnail và CDN delivery.
- Permission theo folder, asset, branch và mục đích.
- Expiry, archive và retention.
- Không để file private bị public ngoài ý muốn.

---

## 2. Information architecture

```text
Document & Digital Asset Center
├── Tổng quan
├── Media Library
├── Product Images
├── Banners & Campaign Assets
├── Brand Assets
├── Business Documents
├── Upload Queue
├── Review & Approval
├── Usage & References
├── Archive & Retention
└── Audit History
```

---

## 3. Permission model

```text
assets.center.view
assets.library.view
assets.upload
assets.update
assets.delete
assets.publish
assets.approve
assets.folders.manage
assets.tags.manage
assets.usage.view
assets.private.download
assets.audit.view
```

Publish, delete, private download và folder permission là quyền nhạy cảm.

---

## 4. Asset contract

```ts
export type DigitalAsset = {
  id: string;
  name: string;
  assetType: "image" | "video" | "document" | "spreadsheet" | "archive" | "brand_file";
  mimeType: string;
  status: "uploading" | "processing" | "review" | "approved" | "published" | "rejected" | "archived" | "expired";
  classification: "public" | "internal" | "confidential" | "restricted";
  folderId?: string;
  tags: string[];
  ownerUserId: string;
  version: number;
  sizeBytes: number;
  checksum?: string;
  width?: number;
  height?: number;
  durationSeconds?: number;
  expiresAt?: string;
  createdAt: string;
  updatedAt: string;
};
```

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Assets active.
2. Assets chờ review.
3. Upload/processing failures.
4. Assets sắp hết hạn.
5. Unused assets.
6. Storage usage.

Widgets:

- Recent uploads.
- Pending approvals.
- Storage growth.
- Broken references.
- Low-resolution product images.
- Expiring campaign assets.

---

## 6. Media Library

View modes:

- Grid.
- List.
- Compact table.

Filters:

- Asset type.
- Status.
- Folder.
- Tag.
- Owner.
- Product/category.
- Classification.
- Date.
- Resolution/aspect ratio.

Search theo filename đã chuẩn hóa, title, tag và linked entity.

---

## 7. Upload workflow

```text
Select files
→ Client validation
→ Signed upload
→ Malware scan
→ Metadata extraction
→ Processing/optimization
→ Classification
→ Review
→ Publish
```

Không báo upload hoàn tất trước khi storage và processing backend xác nhận.

---

## 8. Upload safeguards

- Whitelist MIME và extension.
- Kiểm tra file signature.
- Giới hạn dung lượng.
- Malware scanning.
- Chống archive bomb.
- Private storage mặc định.
- Signed URL có thời hạn.
- Không tin filename từ client.
- Không render SVG/HTML không sanitize.

---

## 9. Product images

Yêu cầu:

- Ảnh chính và ảnh phụ.
- Thứ tự hiển thị.
- Alt text tiếng Việt.
- Background phù hợp.
- Minimum resolution.
- Aspect ratio policy.
- Thumbnail và responsive variants.
- Product/SKU references.

Không publish ảnh sai SKU, mờ hoặc thiếu quyền sử dụng.

---

## 10. Banner assets

Metadata:

- Placement.
- Desktop/mobile dimensions.
- Campaign.
- Start/end time.
- CTA target.
- Locale.
- Branch scope.
- Approval status.

Preview phải mô phỏng Mini App và Dashboard đúng kích thước mục tiêu.

---

## 11. Brand assets

Bao gồm:

- Logo.
- Logo variants.
- Icon.
- Color usage guides.
- Typography references.
- Templates.

Brand assets chính thức phải có badge `Approved` và version rõ.

---

## 12. Business documents

Ví dụ:

- Hợp đồng.
- Chứng từ.
- Quy trình.
- Hướng dẫn.
- Báo cáo PDF.
- Chứng nhận sản phẩm.

Tài liệu nhạy cảm phải có field-level metadata, permission và retention riêng.

---

## 13. Folder và taxonomy

Folder dùng cho điều hướng; tags dùng cho phân loại chéo.

Ví dụ taxonomy:

```text
Products
Campaigns
Brand
Branches
Warehouses
Legal
Finance
Operations
Training
```

Không tạo folder quá sâu hoặc tag tự do không kiểm soát.

---

## 14. Versioning

Khi thay file:

- Tạo version mới.
- Giữ lịch sử.
- Có change note.
- Preview diff metadata.
- Không phá reference hiện tại trước khi publish.
- Có rollback nếu format còn tương thích.

Không ghi đè file published âm thầm.

---

## 15. Review và approval

Review criteria:

- Chất lượng hình ảnh.
- Đúng sản phẩm/thương hiệu.
- Quyền sử dụng.
- Nội dung an toàn.
- Alt text.
- Kích thước.
- Expiry.
- Placement.

Reviewer có thể approve, reject hoặc request changes.

---

## 16. Asset usage and references

Hiển thị asset đang được dùng tại:

- Product page.
- Category page.
- Hero banner.
- Campaign.
- Knowledge article.
- Report/document.
- Zalo Mini App configuration.

Không cho delete asset đang được dùng mà thiếu impact preview.

---

## 17. CDN và delivery

- Public asset dùng CDN URL có version/hash.
- Private asset dùng signed URL.
- Cache invalidation có kiểm soát.
- Responsive image variants.
- WebP/AVIF nếu platform hỗ trợ.
- Fallback format.
- Không expose origin bucket.

---

## 18. Image optimization

Pipeline có thể tạo:

```text
thumbnail
small
medium
large
original
```

Yêu cầu:

- Giữ aspect ratio.
- Không upscale quá mức.
- Preserve transparency khi cần.
- Remove metadata nhạy cảm.
- Quality threshold rõ.

---

## 19. Watermark

Chỉ áp dụng khi nghiệp vụ cần.

- Có template.
- Vị trí và opacity.
- Preview.
- Không phá ảnh gốc.
- Tạo derived asset.

Không watermark logo hoặc tài liệu nội bộ mặc định.

---

## 20. Archive, expiry và retention

- Campaign asset tự hết hiệu lực theo lịch.
- Asset expired không được dùng cho publication mới.
- Archive giữ lịch sử và references.
- Delete vĩnh viễn theo policy và approval.
- Legal hold chặn deletion.

---

## 21. API endpoints đề xuất

```text
GET  /api/v1/assets/summary
GET  /api/v1/assets
POST /api/v1/assets/uploads
GET  /api/v1/assets/:id
PATCH /api/v1/assets/:id
POST /api/v1/assets/:id/approve
POST /api/v1/assets/:id/publish
POST /api/v1/assets/:id/archive
GET  /api/v1/assets/:id/usage
GET  /api/v1/asset-folders
POST /api/v1/asset-folders
GET  /api/v1/asset-tags
POST /api/v1/asset-tags
```

---

## 22. Frontend component inventory

```text
DocumentDigitalAssetCenterPage
├── AssetSummaryCards
├── MediaLibraryGrid
├── AssetTable
├── UploadQueue
├── AssetDetailDrawer
├── ImageEditorPreview
├── ProductImageManager
├── BannerPreview
├── VersionTimeline
├── ApprovalQueue
├── UsageReferencePanel
├── FolderTree
└── RetentionPanel
```

---

## 23. Security, privacy và audit

- Backend enforce folder, asset và classification permission.
- Private URLs có expiry.
- Không public hóa asset bằng cách đổi URL client-side.
- Audit upload, classification, approval, publish, download và delete.
- Không ghi file content hoặc signed URL vào analytics.

Audit events:

```text
asset.uploaded
asset.processed
asset.classification.updated
asset.approved
asset.published
asset.version.created
asset.downloaded_private
asset.archived
asset.deleted
```

---

## 24. Testing

### Unit

- MIME validation.
- Filename normalization.
- Expiry calculation.
- Usage blocking.
- Responsive variant mapping.

### Integration

- Malware scan fail chặn publish.
- Private asset dùng signed URL.
- Asset đang dùng không delete trực tiếp.
- Version mới không phá reference cũ trước publish.
- Cross-folder permission bị chặn.

### E2E

- Upload ảnh sản phẩm.
- Xử lý thumbnail.
- Gắn vào SKU.
- Approve và publish.
- Upload banner mobile/desktop.
- Xem usage.
- Archive campaign asset.

---

## 25. Acceptance checklist

- [ ] Có Media Library.
- [ ] Có upload, scanning và processing.
- [ ] Có Product Images, Banners và Brand Assets.
- [ ] Có business documents.
- [ ] Có folder, taxonomy và search.
- [ ] Có versioning và approval.
- [ ] Có CDN, variants và image optimization.
- [ ] Có usage references và delete safeguards.
- [ ] Có retention, archive, permission và audit.
- [ ] Có responsive, accessibility và testing đầy đủ.

---

## 26. Những điều không được làm

- Không public file private.
- Không bỏ malware scanning.
- Không ghi đè asset published thiếu version.
- Không delete asset đang được dùng thiếu impact preview.
- Không render SVG/HTML nguy hiểm.
- Không expose bucket path hoặc signed URL lâu dài.
- Không publish ảnh sai SKU, mờ hoặc thiếu quyền sử dụng.

---

## 27. Kết luận

Document & Digital Asset Center của Cynca VLXD phải quản lý tài sản số từ upload đến publication bằng quy trình an toàn, có version, approval, usage tracking và retention. Ảnh sản phẩm, banner và tài liệu phải được phân phối đúng kích thước, đúng quyền và đúng ngữ cảnh sử dụng.
