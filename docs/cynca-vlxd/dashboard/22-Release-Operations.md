# Cynca VLXD Admin Dashboard — Release Operations

> Phiên bản: 1.0  
> Phạm vi: Quy trình build, phát hành, giám sát và rollback Dashboard quản trị  
> Stack mục tiêu: Next.js, React, TypeScript, Tailwind CSS  
> Đối tượng sử dụng: Frontend, Backend, QA, DevOps, Product Owner, AI coding agent  
> Tài liệu liên quan: toàn bộ thư mục `docs/cynca-vlxd/dashboard/`

---

## 1. Mục tiêu

Tài liệu này định nghĩa cách đưa Dashboard Cynca VLXD từ development lên production một cách an toàn, có kiểm soát và có khả năng khôi phục nhanh khi xảy ra sự cố.

Quy trình phát hành phải bảo đảm:

- Mã nguồn đã được kiểm thử đầy đủ.
- Dữ liệu và phân quyền không bị sai lệch.
- Có khả năng phát hành theo từng phần.
- Có giám sát sau deploy.
- Có rollback rõ ràng.
- Không phụ thuộc vào một cá nhân duy nhất.
- Mọi thay đổi production có dấu vết kiểm tra.

---

## 2. Nguyên tắc phát hành

### 2.1 Không deploy trực tiếp từ máy cá nhân

Production deployment phải chạy qua pipeline hoặc hệ thống phát hành có log.

### 2.2 Mỗi release phải có commit hoặc tag xác định

Không phát hành từ working tree chưa commit hoặc branch không rõ trạng thái.

### 2.3 Rollback phải được chuẩn bị trước deploy

Không chờ xảy ra lỗi mới nghĩ cách quay lại phiên bản cũ.

### 2.4 Thay đổi rủi ro cao phải chia nhỏ

Ví dụ:

- Thay toàn bộ Dashboard.
- Đổi permission model.
- Đổi API contract.
- Thay chart library.
- Thay cơ chế authentication.

### 2.5 Production là môi trường bất biến

Không sửa file trực tiếp trên server production ngoài tình huống khẩn cấp có ghi nhận đầy đủ.

---

## 3. Môi trường triển khai

Tối thiểu gồm:

```text
Local
Development
Staging
Production
```

### Local

- Dành cho developer.
- Dùng mock hoặc development API.

### Development

- Tích hợp liên tục.
- Có thể không ổn định.
- Dùng cho review kỹ thuật sớm.

### Staging

- Cấu hình gần production.
- Dữ liệu thử nghiệm hoặc đã ẩn danh.
- Dùng cho QA, UAT và performance check.

### Production

- Dữ liệu thật.
- Quyền truy cập hạn chế.
- Giám sát đầy đủ.

Không dùng production database cho automated tests thông thường.

---

## 4. Branch strategy

Có thể áp dụng trunk-based development hoặc flow hiện có trong repository.

Khuyến nghị:

```text
main                → production-ready
feature/*           → phát triển tính năng
fix/*               → sửa lỗi thường
hotfix/*            → sửa lỗi production khẩn cấp
release/*           → tùy chọn nếu cần giai đoạn ổn định riêng
```

Mọi thay đổi vào `main` phải qua pull request, trừ hotfix đặc biệt có phê duyệt.

---

## 5. Versioning

Khuyến nghị Semantic Versioning:

```text
MAJOR.MINOR.PATCH
```

Ví dụ:

```text
1.0.0
1.1.0
1.1.1
```

### MAJOR

- Breaking change lớn.
- Thay workflow chính.
- API hoặc permission model không tương thích.

### MINOR

- Thêm tính năng tương thích ngược.
- Thêm widget hoặc module mới.

### PATCH

- Sửa lỗi.
- Điều chỉnh UI nhỏ.
- Performance improvement không đổi contract.

---

## 6. Release tag

Mỗi release production nên có Git tag:

```text
v1.0.0
v1.1.0
v1.1.1
```

Tag phải trỏ đúng commit đã deploy.

Có thể dùng annotated tag với nội dung:

- Release name.
- Ngày phát hành.
- Người duyệt.
- Link release notes.

---

## 7. Environment variables

Biến môi trường cần được quản lý qua secret manager hoặc cấu hình deployment platform.

Ví dụ:

```text
NEXT_PUBLIC_APP_URL
NEXT_PUBLIC_API_BASE_URL
API_INTERNAL_BASE_URL
AUTH_SECRET
SESSION_SECRET
SENTRY_DSN
NEXT_PUBLIC_ANALYTICS_ID
FEATURE_FLAG_PROVIDER_KEY
```

Quy tắc:

- Không commit secret vào repository.
- Chỉ biến thật sự public mới dùng prefix `NEXT_PUBLIC_`.
- Production secret phải khác staging.
- Rotation secret phải có kế hoạch.
- Log không được in giá trị secret.

---

## 8. Environment validation

Ứng dụng phải kiểm tra biến môi trường khi build hoặc startup.

Ví dụ với Zod:

```ts
const envSchema = z.object({
  NEXT_PUBLIC_APP_URL: z.string().url(),
  NEXT_PUBLIC_API_BASE_URL: z.string().url(),
  AUTH_SECRET: z.string().min(32)
});
```

Nếu thiếu biến bắt buộc, build hoặc startup phải fail rõ ràng.

Không để ứng dụng chạy với giá trị fallback nguy hiểm trong production.

---

## 9. Build requirements

Trước deploy production:

- [ ] Cài dependency theo lockfile.
- [ ] Lint pass.
- [ ] Typecheck pass.
- [ ] Unit tests pass.
- [ ] Component tests pass.
- [ ] Build production thành công.
- [ ] Critical E2E pass.
- [ ] Accessibility scan pass.
- [ ] Visual diff được duyệt.
- [ ] Không có dependency vulnerability nghiêm trọng chưa xử lý.

Lệnh tham khảo:

```bash
npm ci
npm run lint
npm run typecheck
npm run test
npm run build
npm run test:e2e
```

Chỉ dùng lệnh thực tế của repository khi triển khai.

---

## 10. Build artifact

Build artifact phải:

- Gắn với commit SHA.
- Không thay đổi giữa staging approval và production deploy nếu có thể.
- Có checksum hoặc metadata.
- Được lưu đủ thời gian để rollback.

Nguyên tắc tốt:

```text
Build once, deploy many
```

Không rebuild production từ mã nguồn khác với bản đã QA trên staging.

---

## 11. Dependency management

- Dùng lockfile.
- Không tự động update major dependency trong release bình thường.
- Dependency update phải có PR riêng khi rủi ro cao.
- Kiểm tra license và security advisory.
- Không cài package chỉ để giải quyết một thao tác nhỏ.
- Xóa package không còn sử dụng.

Chart, authentication và state management libraries cần review kỹ trước khi nâng major version.

---

## 12. Database và API compatibility

Dashboard frontend phải tương thích với API đang chạy trong thời gian deploy.

Khi có thay đổi breaking:

1. Backend hỗ trợ contract cũ và mới trong giai đoạn chuyển tiếp.
2. Deploy backend tương thích ngược trước.
3. Deploy frontend dùng contract mới.
4. Theo dõi.
5. Loại bỏ contract cũ ở release sau.

Không deploy frontend yêu cầu field mới trước khi backend production hỗ trợ.

---

## 13. Feature flags

Feature flag nên dùng cho:

- Dashboard mới thay Dashboard cũ.
- Widget chưa hoàn thiện.
- Real-time notifications.
- Export PDF mới.
- Workflow đơn hàng mới.

Flag phải có:

- Tên rõ ràng.
- Owner.
- Ngày tạo.
- Điều kiện bật.
- Kế hoạch xóa.

Ví dụ:

```text
new_dashboard_enabled
realtime_notification_enabled
order_quick_view_enabled
```

Không để feature flag tồn tại vĩnh viễn sau khi rollout hoàn tất.

---

## 14. Rollout strategy

Có thể chọn:

### All-at-once

Chỉ dùng cho thay đổi nhỏ, rủi ro thấp.

### Percentage rollout

Ví dụ:

```text
5% → 25% → 50% → 100%
```

### Role-based rollout

- Admin nội bộ trước.
- Nhóm quản lý sau.
- Toàn bộ người dùng cuối cùng.

### Branch-based rollout

- Một chi nhánh thử nghiệm trước.
- Sau đó mở rộng toàn hệ thống.

Dashboard mới nên ưu tiên role-based hoặc branch-based rollout.

---

## 15. Canary deployment

Canary phù hợp khi:

- Thay đổi API lớn.
- Thay authentication.
- Thay Dashboard shell.
- Có lượng người dùng đủ lớn để quan sát.

Theo dõi canary:

- Error rate.
- Page load.
- API failure.
- Permission denial bất thường.
- User feedback.

Nếu chỉ số xấu vượt ngưỡng, dừng rollout và rollback.

---

## 16. Blue-green deployment

Nếu hạ tầng hỗ trợ:

```text
Blue  → phiên bản đang chạy
Green → phiên bản mới
```

Quy trình:

1. Deploy Green.
2. Smoke test.
3. Chuyển traffic.
4. Theo dõi.
5. Giữ Blue trong thời gian an toàn.
6. Xóa Blue sau khi release ổn định.

Blue-green giúp rollback nhanh nhưng cần kiểm soát session và API compatibility.

---

## 17. Pre-release checklist

### Product

- [ ] Scope release đã chốt.
- [ ] Acceptance criteria đạt.
- [ ] Known issues được ghi nhận.

### Design

- [ ] Visual QA đã sign-off.
- [ ] Responsive chính xác.
- [ ] Không còn sai token nghiêm trọng.

### Engineering

- [ ] PR đã review.
- [ ] API contract ổn định.
- [ ] Migration strategy rõ.
- [ ] Logging và monitoring sẵn sàng.

### QA

- [ ] Regression pass.
- [ ] Critical E2E pass.
- [ ] Permission test pass.
- [ ] UAT pass.

### DevOps

- [ ] Pipeline sẵn sàng.
- [ ] Backup hoặc rollback artifact sẵn có.
- [ ] Secret đúng môi trường.
- [ ] Health checks hoạt động.

---

## 18. Release notes

Release notes phải nêu:

- Phiên bản.
- Ngày phát hành.
- Tính năng mới.
- Sửa lỗi.
- Breaking changes.
- Thay đổi quyền.
- Thay đổi dữ liệu hoặc API.
- Known issues.
- Hướng dẫn rollback nếu đặc biệt.

Mẫu:

```md
# Cynca VLXD Dashboard v1.1.0

## Tính năng mới
- Thêm cảnh báo tồn kho theo kho.
- Thêm quick-view đơn hàng.

## Sửa lỗi
- Sửa sai định dạng tổng tiền trên Safari.

## Thay đổi quyền
- Quyền `finance.view` được yêu cầu để xem lợi nhuận gộp.

## Known issues
- Export PDF có thể mất nhiều thời gian với báo cáo trên 50.000 dòng.
```

---

## 19. Change log

Repository nên có:

```text
CHANGELOG.md
```

Có thể dùng Keep a Changelog hoặc chuẩn nội bộ.

Nhóm thay đổi:

- Added.
- Changed.
- Deprecated.
- Removed.
- Fixed.
- Security.

---

## 20. Deployment window

Release lớn nên chọn thời điểm:

- Lưu lượng thấp.
- Có đủ Frontend, Backend, QA và DevOps trực hỗ trợ.
- Tránh thời điểm chốt đơn, đối soát hoặc báo cáo quan trọng.
- Không deploy lớn sát ngày nghỉ nếu không có đội trực.

Hotfix bảo mật hoặc lỗi nghiêm trọng có thể phát hành ngoài cửa sổ bình thường.

---

## 21. Deployment steps

Quy trình tham khảo:

1. Chốt commit hoặc tag.
2. Xác nhận CI xanh.
3. Deploy staging.
4. Chạy staging smoke test.
5. UAT hoặc final approval.
6. Tạo release tag.
7. Deploy production.
8. Chạy production smoke test.
9. Theo dõi metrics và logs.
10. Xác nhận release ổn định.
11. Công bố release notes.

---

## 22. Health checks

Health checks cần kiểm tra:

- Frontend trả HTTP thành công.
- API backend truy cập được.
- Authentication service hoạt động.
- Dashboard overview endpoint hoạt động.
- Search endpoint hoạt động.
- Notification endpoint hoạt động.

Có thể tách:

```text
/liveness
/readiness
```

Frontend không nên báo ready nếu dependency bắt buộc hoàn toàn không khả dụng.

---

## 23. Production smoke test

Sau deploy, kiểm tra:

- [ ] Trang đăng nhập tải được.
- [ ] Đăng nhập test account thành công.
- [ ] Dashboard shell hiển thị.
- [ ] KPI tải được.
- [ ] Revenue chart tải được.
- [ ] Recent orders tải được.
- [ ] Search mở và trả kết quả.
- [ ] Notification count tải được.
- [ ] User menu hoạt động.
- [ ] Không có console error nghiêm trọng.
- [ ] Không có request 401/403 bất thường.

Không chạy mutation nguy hiểm trên dữ liệu thật nếu không có test tenant.

---

## 24. Monitoring sau deploy

Theo dõi ít nhất:

- Frontend error rate.
- API error rate.
- Authentication failure.
- Page load time.
- Core Web Vitals.
- Export job failure.
- Search error rate.
- Notification connection status.
- Permission denied anomalies.

Thời gian theo dõi tăng cường:

```text
30–60 phút với patch nhỏ
2–4 giờ với release lớn
24 giờ với thay đổi kiến trúc quan trọng
```

---

## 25. Error monitoring

Error report nên gồm:

- Release version.
- Commit SHA.
- Route.
- Browser.
- User role hoặc scope không nhạy cảm.
- Request ID.
- Error code.
- Stack trace đã source-map.

Không gửi:

- Password.
- Token.
- Địa chỉ khách hàng đầy đủ.
- Số điện thoại đầy đủ nếu không cần.
- Dữ liệu thanh toán nhạy cảm.

---

## 26. Performance monitoring

Theo dõi:

```text
LCP
CLS
INP
TTFB
API latency
JavaScript error rate
```

Mục tiêu tham khảo:

```text
LCP < 2,5 giây
CLS < 0,1
INP < 200ms
```

Cần phân tách theo:

- Browser.
- Thiết bị.
- Route.
- Release version.

---

## 27. Alert thresholds

Ngưỡng phải dựa trên baseline thực tế, nhưng có thể bắt đầu với:

- Frontend fatal error tăng gấp 2 baseline.
- API 5xx trên 2–5% trong 5 phút.
- Login failure tăng bất thường.
- LCP p75 vượt 4 giây.
- Export failure trên 10%.
- Dashboard overview failure trên 5%.

Alert phải có owner và kênh nhận rõ ràng.

---

## 28. Rollback triggers

Rollback khi xảy ra một trong các tình huống:

- Người dùng không đăng nhập được.
- Dashboard không tải trên diện rộng.
- Lộ dữ liệu ngoài quyền.
- KPI tài chính sai nghiêm trọng.
- Order workflow ghi sai trạng thái.
- API error rate vượt ngưỡng và không thể khắc phục nhanh.
- Performance suy giảm nghiêm trọng.
- Critical browser không dùng được.

Không cố giữ release mới chỉ vì đã deploy xong.

---

## 29. Rollback procedure

### Frontend-only rollback

1. Xác định release ổn định gần nhất.
2. Chuyển traffic hoặc redeploy artifact cũ.
3. Xác nhận health check.
4. Chạy smoke test.
5. Theo dõi error rate.
6. Thông báo incident status.

### Rollback có API change

- Xác nhận backend còn hỗ trợ frontend cũ.
- Nếu không, rollback theo thứ tự đã chuẩn bị.
- Tránh rollback frontend về contract không còn được backend hỗ trợ.

### Feature flag rollback

Tắt flag là cách nhanh nhất nếu lỗi nằm trong feature mới và shell vẫn ổn định.

---

## 30. Roll-forward

Không phải lỗi nào cũng cần rollback toàn bộ.

Có thể roll-forward khi:

- Lỗi nhỏ, phạm vi hẹp.
- Fix rõ ràng và kiểm thử nhanh được.
- Rollback có rủi ro lớn hơn.
- Không có vấn đề dữ liệu hoặc bảo mật.

Roll-forward vẫn phải qua pipeline và có review tối thiểu.

---

## 31. Hotfix process

Hotfix dùng cho Blocker hoặc Critical production issue.

Quy trình:

1. Tạo branch `hotfix/*` từ production commit.
2. Sửa tối thiểu phạm vi cần thiết.
3. Chạy test liên quan.
4. Review nhanh bởi ít nhất một người phù hợp.
5. Deploy staging hoặc preview nếu thời gian cho phép.
6. Deploy production.
7. Smoke test.
8. Merge lại main.
9. Ghi post-release note.

Không gộp refactor không liên quan vào hotfix.

---

## 32. Incident classification

### SEV-1

- Hệ thống không dùng được diện rộng.
- Lộ dữ liệu.
- Sai dữ liệu tài chính nghiêm trọng.

### SEV-2

- Chức năng chính không hoạt động cho nhóm lớn.
- Order workflow lỗi.
- Export hoặc inventory operations gián đoạn nghiêm trọng.

### SEV-3

- Một widget hoặc browser bị lỗi.
- Có workaround.

### SEV-4

- Lỗi giao diện nhỏ.
- Không ảnh hưởng tác vụ chính.

---

## 33. Incident response

Khi xảy ra incident:

1. Xác nhận phạm vi.
2. Chỉ định incident commander.
3. Dừng rollout nếu đang diễn ra.
4. Quyết định rollback, flag off hoặc roll-forward.
5. Cập nhật trạng thái cho bên liên quan.
6. Khôi phục dịch vụ.
7. Xác minh dữ liệu.
8. Ghi timeline.
9. Thực hiện postmortem nếu cần.

Không để nhiều người thay đổi production cùng lúc mà thiếu điều phối.

---

## 34. Communication template

### Thông báo bắt đầu incident

```text
Cynca VLXD Dashboard đang gặp sự cố ảnh hưởng đến [phạm vi].
Đội kỹ thuật đã bắt đầu xử lý từ [thời gian].
Tạm thời người dùng nên [workaround nếu có].
```

### Thông báo khôi phục

```text
Sự cố Cynca VLXD Dashboard đã được khắc phục lúc [thời gian].
Các chức năng bị ảnh hưởng đã hoạt động trở lại.
Đội kỹ thuật tiếp tục theo dõi và kiểm tra tính toàn vẹn dữ liệu.
```

---

## 35. Postmortem

Postmortem cần cho SEV-1, SEV-2 hoặc sự cố lặp lại.

Nội dung:

- Tóm tắt.
- Tác động.
- Timeline.
- Root cause.
- Yếu tố góp phần.
- Điều gì hoạt động tốt.
- Điều gì chưa tốt.
- Action items.
- Owner và deadline.

Postmortem tập trung cải thiện hệ thống, không đổ lỗi cá nhân.

---

## 36. Backup và retention

Đối với frontend artifact:

- Giữ tối thiểu vài release gần nhất.
- Lưu release metadata và checksum.

Đối với cấu hình:

- Có bản sao environment configuration.
- Có lịch sử feature flag.
- Có audit log thay đổi secret hoặc deployment setting.

Frontend không tự chịu trách nhiệm backup database, nhưng release plan phải xác nhận backend đã xử lý nếu có migration.

---

## 37. Source maps

Source map giúp điều tra lỗi nhưng cần bảo mật phù hợp.

Khuyến nghị:

- Upload private source map lên error monitoring.
- Không public source map nếu có rủi ro lộ mã hoặc cấu trúc nội bộ.
- Gắn source map với release version.
- Xóa hoặc giới hạn truy cập artifact không cần public.

---

## 38. Cache invalidation

Sau deploy cần kiểm soát:

- CDN cache.
- Browser cache.
- Next.js data cache.
- API cache.
- Service worker nếu có.

Static asset nên dùng content hash.

Không cache HTML hoặc API response chứa dữ liệu người dùng sai scope.

---

## 39. Session compatibility

Release không nên làm toàn bộ người dùng bị đăng xuất nếu không cần thiết.

Nếu thay session format:

- Hỗ trợ migration hoặc graceful re-login.
- Thông báo trước nếu ảnh hưởng lớn.
- Không làm mất form chưa lưu mà thiếu cảnh báo.

---

## 40. Browser support policy

Phải xác định browser support chính thức.

Khuyến nghị:

- Chrome hiện hành và một phiên bản trước.
- Edge hiện hành và một phiên bản trước.
- Safari hiện hành và một phiên bản trước.
- Firefox hiện hành và một phiên bản trước.

Nếu bỏ hỗ trợ browser cũ, cần release note rõ ràng.

---

## 41. Maintenance mode

Nếu cần bảo trì:

- Hiển thị thông báo rõ ràng.
- Không để người dùng thao tác rồi mới báo lỗi.
- Có thời gian dự kiến.
- Giữ trang trạng thái hoặc liên hệ hỗ trợ.

Maintenance page phải dùng được trên mobile và đạt accessibility cơ bản.

---

## 42. Operational ownership

| Khu vực | Owner chính | Owner phối hợp |
|---|---|---|
| Frontend release | Frontend Lead | DevOps |
| Backend compatibility | Backend Lead | Frontend |
| CI/CD | DevOps | Engineering |
| QA sign-off | QA Lead | Product |
| Visual approval | Design | Frontend |
| UAT | Product Owner | QA |
| Incident response | Engineering Lead | DevOps/Product |
| Security incident | Security/Backend | Engineering |

Owner thực tế cần được ghi trong runbook nội bộ.

---

## 43. Release evidence

Mỗi release production nên lưu:

- Commit SHA.
- Tag.
- CI run.
- Artifact ID.
- Test report.
- Visual diff approval.
- Accessibility report.
- UAT sign-off.
- Deployment timestamp.
- Người duyệt.
- Smoke test result.
- Known issues.

---

## 44. AI coding agent requirements

Khi AI chuẩn bị release-related changes, AI phải:

- Không tự deploy nếu chưa được yêu cầu rõ.
- Không chỉnh secret.
- Không tuyên bố production-ready nếu chưa có test evidence.
- Báo rõ migration hoặc compatibility risk.
- Không tạo workflow có quyền quá rộng.
- Không hardcode token hoặc credential.
- Ghi rõ file CI/CD đã sửa.
- Ghi rõ lệnh kiểm thử đã chạy.

---

## 45. Example CI release flow

```text
Pull Request
→ Lint
→ Typecheck
→ Unit/Component Test
→ Build
→ Preview Deploy
→ E2E/A11y/Visual Test
→ Review Approval
→ Merge Main
→ Staging Deploy
→ UAT
→ Tag Release
→ Production Deploy
→ Smoke Test
→ Monitoring
```

---

## 46. Release checklist

- [ ] Scope release đã khóa.
- [ ] CI xanh.
- [ ] Build artifact gắn commit SHA.
- [ ] Staging deploy thành công.
- [ ] Regression và UAT pass.
- [ ] Visual và accessibility sign-off.
- [ ] API compatibility được xác nhận.
- [ ] Feature flags có trạng thái đúng.
- [ ] Rollback artifact sẵn sàng.
- [ ] Release notes hoàn tất.
- [ ] Deployment window được duyệt.
- [ ] On-call hoặc owner sẵn sàng.
- [ ] Production deploy thành công.
- [ ] Smoke test pass.
- [ ] Monitoring không có bất thường.
- [ ] Release evidence được lưu.

---

## 47. Những điều không được làm

- Không deploy trực tiếp từ máy cá nhân.
- Không deploy commit chưa được review.
- Không phát hành khi CI đỏ.
- Không rebuild artifact khác sau khi staging đã duyệt nếu tránh được.
- Không đổi secret trong cùng release mà thiếu kế hoạch.
- Không deploy breaking frontend trước backend tương thích.
- Không rollout 100% ngay với thay đổi rủi ro cao.
- Không sửa production thủ công mà không ghi nhận.
- Không giữ feature flag cũ vô thời hạn.
- Không tiếp tục rollout khi metrics xấu rõ ràng.
- Không tuyên bố sự cố đã hết trước khi smoke test và dữ liệu được xác minh.

---

## 48. Kết luận

Release Operations của Cynca VLXD Dashboard phải giúp đội phát triển phát hành nhanh nhưng không đánh đổi tính an toàn. Mỗi release cần có test evidence, API compatibility, rollback plan, monitoring và ownership rõ ràng. Quy trình tốt không chỉ giúp tránh sự cố mà còn giúp giảm thời gian khôi phục khi sự cố xảy ra.

File tiếp theo đề xuất:

```text
README.md
23-Security-Privacy.md
```