# Cynca VLXD Admin Dashboard — UAT, Acceptance & Launch Checklist

> Phiên bản: 1.0  
> Phạm vi: Nghiệm thu nghiệp vụ, kiểm thử phát hành và chuẩn bị đưa Dashboard vào vận hành chính thức

---

## 1. Mục tiêu

Tài liệu này là cổng kiểm soát cuối cùng trước khi Cynca VLXD đưa Dashboard lên production. Không được Go-live chỉ dựa trên việc giao diện đã hoàn thành; hệ thống phải đạt yêu cầu nghiệp vụ, dữ liệu, bảo mật, hiệu năng, khả năng phục hồi và vận hành.

---

## 2. Trạng thái nghiệm thu

```text
Not Started
In Progress
Passed
Passed with Conditions
Failed
Blocked
Waived with Approval
```

Mọi mục `Failed`, `Blocked` hoặc `Unknown` thuộc nhóm critical phải chặn Go-live.

---

## 3. Vai trò tham gia

- Product Owner.
- Business Owner.
- Project Manager/PMO.
- UI/UX.
- Frontend.
- Backend.
- QA.
- Security.
- DevOps/SRE.
- Data/Finance/Warehouse Operations.
- Customer Service.
- Approver cuối cùng.

Không để một người tự xây dựng, tự test và tự phê duyệt toàn bộ hệ thống critical.

---

## 4. UAT entry criteria

- Scope và acceptance criteria đã được chốt.
- Test environment ổn định.
- Build/version rõ ràng.
- Test data hợp lệ và không chứa PII thật ngoài policy.
- Required APIs đã sẵn sàng.
- Migration và seed data đã chạy.
- Known issues đã được ghi nhận.
- UAT users có đúng role/scope.
- Monitoring cơ bản hoạt động.

---

## 5. UAT exit criteria

- 100% critical test cases Passed.
- Không còn blocker hoặc critical defect mở.
- High defects có mitigation và approval rõ.
- Business Owner ký xác nhận.
- Security checks đạt yêu cầu.
- Performance budget đạt.
- Backup/rollback đã xác minh.
- Runbook và support handover hoàn tất.

---

## 6. Checklist giao diện

- [ ] Layout đúng thiết kế tại 1440×900.
- [ ] Sidebar, Header và navigation nhất quán.
- [ ] Màu xanh thương hiệu Cynca VLXD đúng token.
- [ ] Typography tiếng Việt hiển thị chuẩn.
- [ ] Không có overflow hoặc text bị cắt sai.
- [ ] Loading, empty, error và stale states đầy đủ.
- [ ] Disabled/loading states không gây nhầm lẫn.
- [ ] Toast không thay thế inline validation.
- [ ] Dialog/drawer quản lý focus đúng.
- [ ] Không còn placeholder hoặc dữ liệu demo trên production.

---

## 7. Checklist responsive

- [ ] Desktop 1920×1080.
- [ ] Desktop 1440×900.
- [ ] Laptop 1366×768.
- [ ] Tablet 1024×768.
- [ ] Tablet portrait 768×1024.
- [ ] Mobile 390×844.
- [ ] Mobile 360×800.
- [ ] Safe-area và sticky action hoạt động.
- [ ] Virtual keyboard không che input/action.
- [ ] Table rộng có phương án card/scroll phù hợp.

---

## 8. Checklist accessibility

- [ ] Keyboard-only hoàn thành được luồng chính.
- [ ] Focus visible và đúng thứ tự.
- [ ] Form có label và error association.
- [ ] Status không chỉ dùng màu.
- [ ] Contrast đạt tiêu chuẩn áp dụng.
- [ ] Zoom 200% không mất chức năng.
- [ ] Screen-reader labels hợp lý.
- [ ] Dialog có focus trap và trả focus.
- [ ] Touch target tối thiểu 44px.
- [ ] Reduced motion được tôn trọng.
- [ ] Charts có text/table alternative.

---

## 9. Checklist chức năng cốt lõi

- [ ] Đăng nhập, logout và session timeout.
- [ ] Role/permission/scope hoạt động đúng.
- [ ] Dashboard theo từng vai trò.
- [ ] Product, Order, Inventory, Warehouse, Finance và Customer Service.
- [ ] Search và Command Center.
- [ ] Notification Center.
- [ ] Reports/Export và Data Import.
- [ ] User/Role Management.
- [ ] System Settings.
- [ ] Audit Activity.
- [ ] Mobile operations nếu nằm trong scope release.

---

## 10. Checklist API

- [ ] API versioning rõ ràng.
- [ ] Request/response schema đúng contract.
- [ ] Validation server-side.
- [ ] Permission và scope kiểm tra server-side.
- [ ] Pagination, filtering và sorting đúng.
- [ ] Idempotency cho mutation quan trọng.
- [ ] 409 conflict được xử lý.
- [ ] Rate limit phù hợp.
- [ ] Timeout và retry policy rõ.
- [ ] Error code chuẩn hóa.
- [ ] Request ID/trace ID được cung cấp.
- [ ] Không trả secret hoặc field ngoài quyền.

---

## 11. Checklist dữ liệu

- [ ] Master data đã được xác minh.
- [ ] Migration hoàn tất và có log.
- [ ] Record counts/reconciliation đạt.
- [ ] Timezone, currency và rounding đúng.
- [ ] Dữ liệu stale có nhãn rõ.
- [ ] Không hiển thị số 0 khi dữ liệu lỗi/chưa tải.
- [ ] PII masking đúng.
- [ ] Import/export giữ đúng encoding tiếng Việt.
- [ ] CSV injection protection.
- [ ] Retention và deletion policies đã cấu hình.

---

## 12. Checklist bảo mật

- [ ] Authentication và authorization được kiểm thử.
- [ ] Cross-branch/cross-scope access bị chặn.
- [ ] Privilege escalation tests đạt.
- [ ] Session revoke hoạt động server-side.
- [ ] MFA/re-authentication cho action nhạy cảm.
- [ ] CSRF/XSS/SQL injection protections.
- [ ] SSRF protection cho webhook/integration.
- [ ] Upload scanning và file-type validation.
- [ ] Secret không xuất hiện trong UI/log/analytics.
- [ ] Signed URL có expiry.
- [ ] Security headers và TLS đúng.
- [ ] Dependency/container scan đạt policy.
- [ ] Audit events cho hành động critical.

---

## 13. Checklist hiệu năng

Mục tiêu tham khảo:

```text
LCP p75 < 2,5s
INP p75 < 200ms
CLS p75 < 0,1
API quan trọng p95 theo budget từng module
Search p95 < 1s
```

- [ ] Bundle theo route trong budget.
- [ ] Không tải toàn bộ dataset về client.
- [ ] List lớn được pagination/virtualization.
- [ ] Chart/module nặng được lazy-load.
- [ ] Database query chậm đã xử lý.
- [ ] Queue/worker capacity đủ tải.
- [ ] Load test scenario chính đạt.
- [ ] Không có regression đáng kể so với baseline.

---

## 14. Checklist integration

- [ ] Zalo OA test thành công.
- [ ] Provider credentials hợp lệ và có owner.
- [ ] Webhook signature verification.
- [ ] Payment/shipping/WMS integration theo scope.
- [ ] Retry và DLQ hoạt động.
- [ ] Idempotency ngăn duplicate.
- [ ] Rate limits được xử lý.
- [ ] Credential expiry alert hoạt động.
- [ ] Integration monitoring có health/freshness.

---

## 15. Checklist backup và rollback

- [ ] Backup gần nhất valid.
- [ ] Recovery point trước migration/release.
- [ ] Restore test đã hoàn tất.
- [ ] Rollback application đã diễn tập.
- [ ] Database rollback/forward-fix plan rõ.
- [ ] Feature-flag kill switch hoạt động.
- [ ] RPO/RTO được chấp nhận.
- [ ] Rollback owner và approval xác định.

---

## 16. Checklist monitoring

- [ ] Metrics, logs và traces hoạt động.
- [ ] Dashboard health có dữ liệu.
- [ ] Alert critical đã test.
- [ ] Notification routing đúng owner.
- [ ] Audit ingestion health được theo dõi.
- [ ] Error monitoring đã redact PII.
- [ ] Synthetic checks cho luồng chính.
- [ ] Business transaction checks: login, create order, inventory lookup, report.

---

## 17. Checklist vận hành

- [ ] Runbook cho sự cố phổ biến.
- [ ] Incident severity và escalation rõ.
- [ ] Support contacts cập nhật.
- [ ] On-call hoặc người trực Go-live xác định.
- [ ] Training cho admin/branch/warehouse/support hoàn tất.
- [ ] Help Center và tài liệu người dùng sẵn sàng.
- [ ] Known issues và workaround đã truyền thông.
- [ ] SLA hỗ trợ sau Go-live rõ.

---

## 18. Smoke test production

Sau deploy kiểm tra tối thiểu:

```text
Login
Load role-based dashboard
Search product/order
Open order detail
Create/update permitted test entity
Inventory availability
Notification delivery
Export small report
Audit event written
Logout/session revoke
```

Test mutation phải dùng tài khoản và dữ liệu test được kiểm soát.

---

## 19. Go-live decision

```ts
export type GoLiveDecision = {
  releaseVersion: string;
  decision: "go" | "conditional_go" | "no_go";
  openCriticalIssues: number;
  openHighIssues: number;
  approvedExceptions: string[];
  rollbackOwnerUserId: string;
  businessApproverUserId: string;
  technicalApproverUserId: string;
  securityApproverUserId?: string;
  decidedAt: string;
};
```

---

## 20. No-Go conditions

- Critical security issue.
- Data corruption hoặc reconciliation fail.
- Permission/scope leakage.
- Không có backup/rollback khả dụng.
- Critical workflow failed.
- Monitoring/alerting unavailable.
- Required approval thiếu.
- Migration chưa xác minh.
- Performance không đáp ứng mức vận hành tối thiểu.

---

## 21. Conditional Go

Chỉ dùng khi:

- Không có critical defect.
- High issue có workaround rõ.
- Có owner và deadline.
- Có monitoring riêng.
- Business và Technical Approver chấp nhận.
- Có khả năng rollback nhanh.

Không dùng waiver để hợp thức hóa lỗi bảo mật hoặc dữ liệu critical.

---

## 22. Post-launch monitoring

Trong 24–72 giờ đầu:

- Error rate.
- API latency.
- Login/session failures.
- Order success.
- Inventory freshness.
- Queue backlog.
- Notification delivery.
- Support-ticket volume.
- User feedback.
- Data reconciliation.

Có cadence cập nhật và tiêu chí rollback rõ.

---

## 23. Defect severity

```text
Critical — mất dữ liệu, vượt quyền, hệ thống không dùng được
High — chức năng chính lỗi, chưa có workaround tốt
Medium — ảnh hưởng hạn chế, có workaround
Low — lỗi trình bày hoặc cải tiến nhỏ
```

Mỗi defect cần owner, environment, evidence, reproduction steps và target fix.

---

## 24. Sign-off table

| Vai trò | Trách nhiệm | Trạng thái |
|---|---|---|
| Product Owner | Phạm vi và nghiệp vụ | Pending |
| Business Owner | UAT và vận hành | Pending |
| QA Lead | Test evidence | Pending |
| Engineering Lead | Technical readiness | Pending |
| Security | Security acceptance | Pending |
| DevOps/SRE | Deploy, monitoring, rollback | Pending |
| Final Approver | Go/No-Go | Pending |

---

## 25. Acceptance checklist cuối

- [ ] UAT Passed.
- [ ] Security Passed.
- [ ] Performance Passed.
- [ ] Accessibility Passed.
- [ ] Responsive Passed.
- [ ] Data reconciliation Passed.
- [ ] Integration Passed.
- [ ] Backup/restore Passed.
- [ ] Rollback rehearsal Passed.
- [ ] Monitoring and alerts Passed.
- [ ] Training and documentation Passed.
- [ ] Business sign-off completed.
- [ ] Technical sign-off completed.
- [ ] Go-live decision recorded.

---

## 26. Những điều không được làm

- Không Go-live khi critical test còn Unknown/Failed.
- Không dùng waiver cho lỗi vượt quyền hoặc mất dữ liệu.
- Không deploy thiếu backup và rollback plan.
- Không coi giao diện đẹp là đủ điều kiện nghiệm thu.
- Không dùng dữ liệu khách hàng thật thiếu kiểm soát trong UAT.
- Không đóng defect thiếu evidence.
- Không báo Go-live thành công trước smoke test và monitoring verification.

---

## 27. Kết luận

Cynca VLXD chỉ được đưa vào vận hành khi trải nghiệm, nghiệp vụ, dữ liệu, bảo mật, hiệu năng và vận hành đều có bằng chứng nghiệm thu. Quyết định Go-live phải minh bạch, có người chịu trách nhiệm và có khả năng rollback ngay khi các tiêu chí an toàn không còn được đáp ứng.
