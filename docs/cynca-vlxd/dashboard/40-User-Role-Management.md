# Cynca VLXD Admin Dashboard — User & Role Management

> Phiên bản: 1.0  
> Phạm vi: Giao diện quản lý người dùng, vai trò, quyền và phạm vi dữ liệu  
> Đối tượng: Product, Design, Frontend, Backend, QA và quản trị hệ thống

---

## 1. Mục tiêu

Trang User & Role Management giúp quản trị viên kiểm soát người dùng nào được truy cập Dashboard, được sử dụng chức năng nào và được xem dữ liệu trong phạm vi nào.

Mục tiêu chính:

- Quản lý danh sách tài khoản.
- Tạo và gửi lời mời cho người dùng mới.
- Gán vai trò theo công việc.
- Gán phạm vi theo chi nhánh, kho hoặc đội nhóm.
- Xem trước quyền hiệu lực trước khi lưu.
- Quản lý trạng thái hoạt động của tài khoản.
- Theo dõi lịch sử thay đổi.
- Hỗ trợ rà soát quyền định kỳ.

---

## 2. Cấu trúc trang

```text
User & Role Management
├── Người dùng
├── Lời mời
├── Vai trò
├── Danh mục quyền
├── Phạm vi dữ liệu
├── Rà soát quyền
└── Lịch sử thay đổi
```

---

## 3. Nhóm quyền quản trị

```text
users.view
users.create
users.invite
users.update
users.disable
users.sessions.revoke
roles.view
roles.create
roles.update
roles.assign
permissions.view
scope.assign
access_reviews.view
access_reviews.manage
```

Giao diện chỉ hiển thị hành động mà người quản trị hiện tại được phép sử dụng. Backend vẫn phải kiểm tra lại khi nhận yêu cầu.

---

## 4. Trạng thái tài khoản

```ts
export type UserStatus =
  | "invited"
  | "active"
  | "suspended"
  | "disabled"
  | "locked"
  | "expired";
```

| Trạng thái | Ý nghĩa |
|---|---|
| Invited | Đã gửi lời mời, chưa kích hoạt |
| Active | Đang hoạt động |
| Suspended | Tạm dừng truy cập |
| Disabled | Đã vô hiệu hóa |
| Locked | Bị khóa theo chính sách hệ thống |
| Expired | Quyền truy cập đã hết thời hạn |

Không dùng một trạng thái chung để thay thế tất cả trường hợp trên.

---

## 5. Danh sách người dùng

### Cột hiển thị

- Họ tên.
- Mã nhân viên.
- Tài khoản liên hệ.
- Vai trò chính.
- Chi nhánh hoặc kho.
- Trạng thái.
- Xác thực bổ sung.
- Lần hoạt động gần nhất.
- Thời hạn truy cập.
- Actions.

### Bộ lọc

- Trạng thái.
- Vai trò.
- Chi nhánh.
- Kho.
- Đội nhóm.
- Tài khoản sắp hết hạn.
- Tài khoản ít hoạt động.

### Hành động

- Xem chi tiết.
- Chỉnh sửa.
- Gán vai trò.
- Cập nhật phạm vi.
- Tạm dừng.
- Vô hiệu hóa.
- Khôi phục.
- Thu hồi phiên đăng nhập.
- Xem lịch sử.

---

## 6. User contract

```ts
export type DashboardUser = {
  id: string;
  displayName: string;
  employeeCode?: string;
  contact?: string;
  avatarUrl?: string;
  status: UserStatus;
  roleAssignments: RoleAssignment[];
  scopeAssignments: ScopeAssignment[];
  verificationStatus: "not_configured" | "configured" | "required" | "locked";
  lastActiveAt?: string;
  invitedAt?: string;
  activatedAt?: string;
  expiresAt?: string;
  createdAt: string;
  updatedAt: string;
};
```

---

## 7. Luồng mời người dùng

```text
Nhập thông tin
→ Chọn vai trò
→ Chọn phạm vi
→ Chọn thời hạn nếu cần
→ Xem trước quyền
→ Xác nhận
→ Gửi lời mời
```

Form gồm:

- Họ tên.
- Mã nhân viên.
- Email hoặc thông tin liên hệ nội bộ.
- Vai trò.
- Chi nhánh.
- Kho hoặc đội nhóm.
- Ngày bắt đầu.
- Ngày hết hạn.
- Ghi chú quản trị.

Các trạng thái lời mời:

```text
Pending
Accepted
Expired
Revoked
```

---

## 8. Role model

```ts
export type DashboardRole = {
  id: string;
  key: string;
  name: string;
  description?: string;
  status: "active" | "deprecated" | "archived";
  permissions: string[];
  defaultScopeMode?: "system" | "branch" | "warehouse" | "team" | "assigned" | "self";
  sensitive: boolean;
  systemRole: boolean;
  userCount?: number;
  createdAt: string;
  updatedAt: string;
};
```

Vai trò tham chiếu:

- Super Admin.
- System Admin.
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

---

## 9. Permission catalog

```ts
export type PermissionDefinition = {
  key: string;
  name: string;
  description: string;
  domain: string;
  action: "view" | "create" | "update" | "delete" | "approve" | "export" | "manage";
  sensitivity: "normal" | "sensitive" | "critical";
  dependencies?: string[];
  conflicts?: string[];
  supportsScope: boolean;
};
```

Permission Matrix cần nhóm theo domain:

```text
Dashboard
Sales
Orders
Products
Inventory
Warehouse
Finance
Customers
Customer Service
Reports
Administration
Audit
```

---

## 10. Permission Matrix UI

Bố cục desktop:

```text
Permission domain       View  Create  Update  Delete  Approve  Export  Manage
Orders                    ✓      ✓       ✓       -        -        ✓       -
Inventory                 ✓      -       ✓       -        ✓        ✓       -
Finance                   ✓      -       -       -        ✓        ✓       -
```

Yêu cầu:

- Có tìm kiếm permission.
- Có mô tả khi hover hoặc focus.
- Hiển thị mức độ nhạy cảm.
- Phân biệt quyền kế thừa và quyền gán trực tiếp.
- Hiển thị dependency và conflict.
- Không để danh sách hàng trăm quyền thành một nhóm phẳng khó đọc.

---

## 11. Dependency và conflict

Ví dụ dependency:

```text
orders.update → orders.view
finance.refunds.approve → finance.refunds.view
roles.assign → roles.view + users.view
```

Ví dụ conflict theo chính sách tổ chức:

```text
refund.request + refund.approve
role.request + role.approve
period.prepare + period.final_approve
```

Khi có conflict:

- Hiển thị rõ quyền xung đột.
- Không cho lưu nếu policy không cho phép.
- Nêu cách xử lý hoặc quyền cần bỏ.

---

## 12. Scope model

```ts
export type ScopeAssignment = {
  id: string;
  scopeType: "system" | "branch" | "warehouse" | "team" | "assigned" | "self";
  resourceIds?: string[];
  startsAt?: string;
  expiresAt?: string;
};
```

Ví dụ:

- Toàn hệ thống.
- Chi nhánh Hà Nội.
- Kho HN-01 và HN-02.
- Đội Sales miền Bắc.
- Chỉ đơn hàng được phân công.

Filter ở Dashboard không được mở rộng phạm vi vượt scope đã cấp.

---

## 13. Role assignment

```ts
export type RoleAssignment = {
  id: string;
  roleId: string;
  scopeAssignmentIds: string[];
  source: "direct" | "group" | "temporary";
  startsAt?: string;
  expiresAt?: string;
  approvedBy?: string;
};
```

Một người dùng có thể có nhiều vai trò. Trang chi tiết phải hiển thị quyền hợp nhất và phạm vi đi kèm từng vai trò.

---

## 14. Effective Access Preview

Trước khi lưu, hiển thị bản tóm tắt:

- Vai trò sẽ được gán.
- Permission hiệu lực.
- Chi nhánh, kho và đội nhóm được phép.
- Quyền nhạy cảm.
- Dependency tự động bổ sung.
- Conflict cần xử lý.
- Thời gian bắt đầu và kết thúc.

Ví dụ:

```text
Người dùng có thể xem và cập nhật đơn hàng tại Chi nhánh Hà Nội, xem khả năng cung ứng sản phẩm nhưng không được xem lợi nhuận, công nợ hoặc quản lý người dùng.
```

---

## 15. Quyền tạm thời

Dùng cho:

- Thay ca.
- Hỗ trợ chi nhánh khác.
- Dự án có thời hạn.
- Điều tra sự cố.

Yêu cầu:

- Có thời điểm bắt đầu.
- Có thời điểm hết hạn.
- Có lý do.
- Có người phê duyệt nếu quyền nhạy cảm.
- Tự thu hồi khi hết hạn.

Không tạo quyền tạm thời không có ngày hết hạn.

---

## 16. Trang chi tiết người dùng

Tabs:

```text
Tổng quan
Vai trò & quyền
Phạm vi dữ liệu
Phiên hoạt động
Lịch sử thao tác
Rà soát quyền
```

Header gồm:

- Avatar.
- Tên.
- Mã nhân viên.
- Trạng thái.
- Vai trò chính.
- Phạm vi chính.
- Lần hoạt động gần nhất.
- Actions được phép.

---

## 17. Quản lý phiên hoạt động

Danh sách gồm:

- Thiết bị hoặc trình duyệt.
- Thời gian bắt đầu.
- Lần hoạt động cuối.
- Phiên hiện tại.
- Thời điểm hết hạn.

Actions:

- Thu hồi một phiên.
- Thu hồi các phiên khác.
- Thu hồi toàn bộ phiên.

Thao tác phải chờ xác nhận từ backend trước khi báo thành công.

---

## 18. Tạm dừng và vô hiệu hóa

### Tạm dừng

- Dùng khi ngừng truy cập trong thời gian ngắn.
- Có thể đặt thời điểm tự khôi phục.
- Phải có lý do.

### Vô hiệu hóa

- Dùng khi người dùng không còn cần truy cập.
- Phải thu hồi các phiên đang hoạt động.
- Giữ lịch sử thao tác.
- Chuyển giao task hoặc trách nhiệm còn tồn tại.

---

## 19. Offboarding workflow

```text
Xác nhận ngày kết thúc
→ Chọn thời điểm vô hiệu hóa
→ Thu hồi phiên hoạt động
→ Thu hồi vai trò và phạm vi
→ Chuyển task/ownership
→ Kiểm tra tài khoản dịch vụ liên quan
→ Lưu lịch sử hoàn tất
```

Có thể đặt lịch vô hiệu hóa theo ngày làm việc cuối cùng.

---

## 20. Access Review

Rà soát quyền theo:

- Vai trò.
- Chi nhánh.
- Phòng ban.
- Quyền nhạy cảm.
- Tài khoản ít hoạt động.
- Quyền tạm thời sắp hết hạn.

Kết quả:

```text
Giữ nguyên
Điều chỉnh
Thu hồi
Cần xác minh thêm
```

```ts
export type AccessReviewItem = {
  id: string;
  userId: string;
  roleAssignmentId: string;
  reviewerUserId: string;
  dueAt: string;
  status: "pending" | "approved" | "modified" | "revoked" | "overdue";
  decisionReason?: string;
  reviewedAt?: string;
};
```

---

## 21. Role Editor

Các khu vực:

1. Thông tin chung.
2. Permission Matrix.
3. Scope mặc định.
4. Dependency và conflict.
5. Người dùng bị ảnh hưởng.
6. Effective access preview.
7. Change note.

Không sửa vai trò đang được nhiều người sử dụng mà thiếu màn hình xem trước tác động.

---

## 22. Role versioning và diff

Mỗi thay đổi role cần lưu:

- Phiên bản.
- Người sửa.
- Thời điểm.
- Permission thêm.
- Permission bỏ.
- Scope mặc định thay đổi.
- Số người dùng bị ảnh hưởng.
- Change note.

Diff cần trình bày dễ đọc, không chỉ là raw JSON.

---

## 23. Bulk operations

Có thể hỗ trợ:

- Gán vai trò cho nhiều người.
- Gán chi nhánh hoặc kho.
- Tạm dừng nhiều tài khoản.
- Gửi lại lời mời.
- Yêu cầu cấu hình xác thực bổ sung.

Luồng:

```text
Chọn người dùng
→ Chọn hành động
→ Xem trước tác động
→ Validate
→ Xác nhận
→ Theo dõi kết quả
```

Nếu một phần thất bại, phải hiển thị kết quả theo từng người dùng.

---

## 24. Audit events

Bắt buộc ghi nhận:

```text
users.invitation.created
users.invitation.revoked
users.account.activated
users.account.suspended
users.account.disabled
users.session.revoked
users.role.assigned
users.role.removed
users.scope.updated
roles.created
roles.permissions.updated
access_reviews.completed
```

Audit gồm actor, target, thay đổi trước/sau, lý do, kết quả và thời gian.

---

## 25. API endpoints đề xuất

```text
GET    /api/v1/admin/users
POST   /api/v1/admin/users
GET    /api/v1/admin/users/:id
PATCH  /api/v1/admin/users/:id
POST   /api/v1/admin/users/:id/suspend
POST   /api/v1/admin/users/:id/disable
POST   /api/v1/admin/users/:id/reactivate
POST   /api/v1/admin/users/:id/revoke-sessions
POST   /api/v1/admin/invitations
POST   /api/v1/admin/invitations/:id/resend
POST   /api/v1/admin/invitations/:id/revoke
GET    /api/v1/admin/roles
POST   /api/v1/admin/roles
PATCH  /api/v1/admin/roles/:id
GET    /api/v1/admin/permissions
GET    /api/v1/admin/access-reviews
POST   /api/v1/admin/access-reviews
```

---

## 26. Frontend component inventory

```text
UserRoleManagementPage
├── UserList
├── UserFilterBar
├── UserStatusBadge
├── UserDetailPage
├── InvitationDrawer
├── RoleAssignmentEditor
├── ScopeAssignmentEditor
├── PermissionMatrix
├── EffectiveAccessPreview
├── SessionList
├── RoleList
├── RoleEditor
├── RoleDiffDialog
├── AccessReviewTable
└── BulkActionReviewDialog
```

---

## 27. Loading, empty và error states

### Loading

- User table skeleton.
- User detail skeleton.
- Permission Matrix skeleton.
- Role editor skeleton.

### Empty

```text
Chưa có người dùng nào trong phạm vi này.
Không có lời mời nào đang chờ xử lý.
Không có đợt rà soát quyền nào đang mở.
```

### Error

- Giữ nguyên filter hiện tại.
- Không hiển thị dữ liệu cache của người dùng khác.
- Permission service lỗi phải fail closed.
- Conflict khi nhiều admin cùng sửa phải yêu cầu tải lại hoặc so sánh.
- Có mã hỗ trợ cho lỗi quan trọng.

---

## 28. Responsive behavior

### Desktop

- User table đầy đủ.
- Role editor 2–3 panel.
- Permission Matrix theo nhóm.

### Tablet

- Giảm cột.
- Detail mở drawer hoặc full page.
- Matrix có header sticky.

### Mobile

- User card list.
- Form full-screen sheet.
- Chỉ cho chỉnh thông tin đơn giản.
- Khuyến nghị desktop khi thay đổi permission phức tạp.

---

## 29. Accessibility

- Table có caption và header.
- Status không chỉ dùng màu.
- Permission checkbox có label rõ.
- Domain có heading.
- Dependency và conflict liên kết tới control liên quan.
- Drawer/dialog quản lý focus.
- Keyboard có thể hoàn thành luồng gán role và scope.
- Touch target tối thiểu 44px.
- Zoom 200% không mất action chính.

---

## 30. Security requirements

- Backend kiểm tra mọi thay đổi quyền.
- Người quản trị không được cấp quyền vượt phạm vi họ có thể quản lý.
- Không hiển thị dữ liệu xác thực bí mật trong giao diện.
- Thu hồi phiên phải có hiệu lực server-side.
- Quyền nhạy cảm cần reason và approval theo policy.
- Cache phải tách theo người quản trị, permission và scope.
- Không trả dữ liệu toàn hệ thống rồi lọc ở frontend.

---

## 31. Analytics và observability

Analytics có thể ghi:

```text
users.management.viewed
users.invitation.completed
users.role_assignment.completed
users.scope_assignment.completed
roles.permissions.updated
access_review.completed
```

Không gửi tên, email, số điện thoại, mã nhân viên hoặc chi tiết permission vào analytics.

Observability theo dõi:

- User list/detail API latency.
- Invitation failure.
- Role assignment failure.
- Permission validation failure.
- Session revoke failure.
- Approval workflow delay.
- Cross-scope denial anomalies.

---

## 32. Testing

### Unit

- Permission merge.
- Scope intersection.
- Dependency validation.
- Conflict detection.
- Temporary access expiry.
- Role diff.

### Component

- User list/filter.
- Invitation form.
- Role assignment editor.
- Scope editor.
- Permission Matrix.
- Session revoke dialog.
- Access review table.

### Integration

- Branch admin chỉ quản lý user trong branch.
- Admin không tự cấp quyền vượt quyền quản lý.
- Disable user thu hồi phiên.
- Temporary role hết hạn đúng.
- Quyền nhạy cảm cần approval.
- Conflict update không ghi đè.

### E2E

- Mời user mới.
- Gán Sales Staff và branch scope.
- Cập nhật role.
- Thu hồi phiên.
- Tạm dừng và khôi phục.
- Tạo role tùy chỉnh.
- Chạy access review.
- Vô hiệu hóa user nghỉ việc.

---

## 33. Visual QA

Baseline:

```text
User List — 1440 × 900
User Detail
Invitation Drawer
Role Editor
Permission Matrix
Sensitive Permission Warning
Scope Assignment
Effective Access Preview
Session List
Access Review
Conflict State
Mobile User List
```

---

## 34. AI coding agent requirements

AI coding agent phải:

- Không dùng role name làm authorization duy nhất.
- Không bỏ backend validation cho permission và scope.
- Không cho admin cấp quyền vượt phạm vi quản lý.
- Không tạo quyền tạm thời thiếu thời hạn.
- Không vô hiệu hóa user chỉ ở UI.
- Không sửa role dùng rộng rãi thiếu impact preview.
- Bổ sung test cho privilege escalation, cross-branch access, conflict và offboarding.
- Báo rõ permission model, scope rules và phần backend còn thiếu.

---

## 35. Acceptance checklist

- [ ] Có User List và bộ lọc.
- [ ] Có invitation flow.
- [ ] Có trạng thái tài khoản rõ ràng.
- [ ] Có Role Catalog và Permission Catalog.
- [ ] Có Permission Matrix theo domain.
- [ ] Có dependency và conflict validation.
- [ ] Có scope assignment.
- [ ] Có Effective Access Preview.
- [ ] Có quyền tạm thời và thời hạn.
- [ ] Có User Detail và Session Management.
- [ ] Có Access Review.
- [ ] Có offboarding workflow.
- [ ] Có bulk operations an toàn.
- [ ] Có role versioning và diff.
- [ ] Có audit đầy đủ.
- [ ] Loading, empty, error và responsive states hoàn chỉnh.
- [ ] Accessibility, security, analytics, observability và testing đầy đủ.

---

## 36. Những điều không được làm

- Không dùng tài khoản chung cho nhiều người.
- Không dùng role name làm authorization duy nhất.
- Không cho user tự mở rộng quyền hoặc scope.
- Không tạo quyền tạm thời không có ngày hết hạn.
- Không vô hiệu hóa tài khoản mà bỏ qua thu hồi phiên.
- Không xóa lịch sử thao tác khi tài khoản bị vô hiệu hóa.
- Không sửa role nhiều người đang dùng thiếu impact review.
- Không fallback sang quyền rộng khi permission service lỗi.
- Không hiển thị dữ liệu xác thực bí mật trong giao diện.

---

## 37. Kết luận

User & Role Management của Cynca VLXD phải quản lý đầy đủ vòng đời tài khoản, vai trò, permission và scope theo nguyên tắc quyền tối thiểu. Mọi thay đổi phải được backend kiểm tra, có xem trước tác động, xử lý xung đột, thu hồi phiên khi cần và lưu lịch sử rõ ràng.

File tiếp theo đề xuất:

```text
README.md
41-System-Settings.md
```