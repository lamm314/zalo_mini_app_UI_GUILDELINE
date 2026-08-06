# Cynca VLXD Admin Dashboard — Operational Runbooks

> Phiên bản: 1.0  
> Phạm vi: Quy trình xử lý sự cố vận hành thường gặp của Dashboard quản trị  
> Đối tượng sử dụng: Frontend, Backend, DevOps, QA, Product Owner, Security và đội trực vận hành  
> Tài liệu liên quan: `22-Release-Operations.md`, `23-Security-Privacy.md`, `24-Observability.md`

---

## 1. Mục tiêu

Runbook là hướng dẫn thao tác theo từng bước khi một sự cố đã được phát hiện. Mục tiêu là giúp người trực:

- Xác minh sự cố nhanh.
- Khoanh vùng ảnh hưởng.
- Giảm tác động đến người dùng.
- Chọn đúng phương án rollback, feature flag hoặc sửa nóng.
- Tránh nhiều người xử lý chồng chéo.
- Lưu lại bằng chứng và timeline.
- Khôi phục hệ thống an toàn.

Runbook không thay thế điều tra nguyên nhân gốc. Runbook ưu tiên khôi phục dịch vụ trước, phân tích sâu sau.

---

## 2. Nguyên tắc sử dụng runbook

1. Xác minh alert trước khi thay đổi production.
2. Ghi lại thời gian bắt đầu và người phụ trách.
3. Chỉ định một Incident Commander khi sự cố từ SEV-2 trở lên.
4. Không sửa nhiều lớp cùng lúc nếu chưa khoanh vùng.
5. Ưu tiên thao tác có thể đảo ngược.
6. Không xóa log hoặc evidence.
7. Không chạy lệnh nguy hiểm nếu chưa hiểu phạm vi.
8. Sau mỗi thay đổi phải xác minh lại bằng metric và smoke test.
9. Nếu nghi ngờ lộ dữ liệu, chuyển ngay sang quy trình security incident.
10. Mọi hotfix phải được merge lại nhánh chính sau khi ổn định.

---

## 3. Mẫu header cho mỗi runbook

Mỗi runbook cần có:

```text
Tên sự cố
Severity mặc định
Dấu hiệu
Ảnh hưởng
Owner chính
Dashboard/Alert liên quan
Điều kiện rollback
Điều kiện escalation
```

---

## 4. Mẫu timeline incident

```md
## Timeline

- 17:20 — Alert phát hiện error rate tăng.
- 17:23 — Xác minh Dashboard không tải ở production.
- 17:25 — Chỉ định Incident Commander.
- 17:28 — Dừng rollout phiên bản mới.
- 17:35 — Rollback về phiên bản trước.
- 17:42 — Smoke test thành công.
- 18:10 — Xác nhận metrics trở lại bình thường.
```

---

# Runbook 01 — Dashboard không tải

## 5. Mô tả

Trang Dashboard hiển thị màn hình trắng, lỗi toàn trang, loading vô hạn hoặc không thể truy cập sau đăng nhập.

### Severity mặc định

```text
SEV-1 nếu ảnh hưởng diện rộng
SEV-2 nếu chỉ một nhóm người dùng hoặc browser
```

### Dấu hiệu

- `dashboard.page.load` failure tăng.
- Frontend fatal error tăng.
- Dashboard overview API error cao.
- Synthetic check thất bại.
- Người dùng báo màn hình trắng.

### Owner

- Frontend Lead.
- Backend Lead nếu API liên quan.
- DevOps nếu hạ tầng hoặc deploy liên quan.

---

## 6. Xác minh ban đầu

- [ ] Kiểm tra production URL từ mạng khác.
- [ ] Kiểm tra anonymous/login route có tải không.
- [ ] Kiểm tra browser console.
- [ ] Kiểm tra network requests.
- [ ] Kiểm tra release vừa deploy.
- [ ] Kiểm tra health endpoint.
- [ ] Kiểm tra error rate theo release.
- [ ] Kiểm tra API overview.

Phân loại nhanh:

```text
Frontend bundle/chunk lỗi
API overview lỗi
Authentication/session lỗi
DNS/CDN/proxy lỗi
Permission lỗi diện rộng
Browser-specific regression
```

---

## 7. Mitigation

### Nếu lỗi sau release mới

1. Dừng rollout.
2. Tắt feature flag Dashboard mới nếu có.
3. Rollback artifact frontend gần nhất.
4. Chạy smoke test.
5. Theo dõi error rate tối thiểu 15–30 phút.

### Nếu API overview lỗi

1. Kiểm tra endpoint và dependency.
2. Nếu các endpoint widget riêng còn hoạt động, cân nhắc cho Dashboard tải từng phần.
3. Tắt widget gây lỗi bằng feature flag nếu có.
4. Không để một widget làm sập toàn trang.

### Nếu chunk load error

1. Kiểm tra cache/CDN.
2. Xác nhận HTML và static asset cùng release.
3. Purge cache có kiểm soát.
4. Kiểm tra service worker nếu có.

---

## 8. Xác minh khôi phục

- [ ] Dashboard tải trên Chrome và Safari/Edge chính.
- [ ] KPI tải.
- [ ] Orders tải.
- [ ] Search mở.
- [ ] Không còn fatal error tăng.
- [ ] Synthetic check pass.
- [ ] Không có 401/403 bất thường.

---

# Runbook 02 — Dashboard Overview API lỗi

## 9. Mô tả

Endpoint tổng quan trả 5xx, timeout, dữ liệu không hợp lệ hoặc contract parse failure.

### Severity

```text
SEV-2 mặc định
SEV-1 nếu toàn Dashboard phụ thuộc endpoint này và không có fallback
```

### Dấu hiệu

- `dashboard_overview_api_success_rate` giảm.
- p95 latency tăng.
- Contract validation failure tăng.
- Widget đồng loạt lỗi.

---

## 10. Điều tra

- [ ] Kiểm tra status code.
- [ ] Kiểm tra request ID và trace.
- [ ] Kiểm tra database latency.
- [ ] Kiểm tra dependency service.
- [ ] Kiểm tra payload size.
- [ ] Kiểm tra release backend gần nhất.
- [ ] So sánh contract hiện tại với frontend.
- [ ] Kiểm tra branch/date filter cụ thể.

### Phân biệt lỗi

- 500: lỗi server.
- 502/503: upstream hoặc gateway.
- Timeout: query hoặc dependency chậm.
- 200 nhưng parse fail: contract regression.
- 403: permission hoặc scope regression.

---

## 11. Mitigation

- Rollback backend nếu contract breaking vừa deploy.
- Bật lại contract cũ nếu có versioning.
- Tách widget request nếu endpoint tổng hợp quá nặng.
- Trả dữ liệu cached/stale có cảnh báo nếu an toàn.
- Giảm date range mặc định tạm thời nếu query quá nặng.
- Disable compare-period nếu đây là nguyên nhân.

Không tự trả số 0 thay dữ liệu lỗi.

---

# Runbook 03 — Đơn hàng không cập nhật được

## 12. Mô tả

Người dùng không thể xác nhận, chuyển trạng thái, hủy hoặc hoàn tất đơn hàng.

### Severity

```text
SEV-2
SEV-1 nếu workflow đơn hàng ngừng toàn hệ thống
```

### Dấu hiệu

- `order_status_update_success_rate` giảm.
- Mutation 409/500 tăng.
- Toast lỗi xuất hiện nhiều.
- Đơn bị treo ở trạng thái cũ.

---

## 13. Xác minh

- [ ] Kiểm tra một đơn test hợp lệ.
- [ ] Kiểm tra permission và availableActions.
- [ ] Kiểm tra current order version.
- [ ] Kiểm tra transition có hợp lệ không.
- [ ] Kiểm tra API response và request ID.
- [ ] Kiểm tra audit log.
- [ ] Kiểm tra queue hoặc integration giao vận nếu liên quan.

### Trường hợp conflict 409

- Xác minh đơn đã được người khác cập nhật.
- Yêu cầu người dùng reload.
- Không retry mutation cũ tự động.

### Trường hợp 403

- Kiểm tra permission model và branch scope.
- Không cấp quyền rộng tạm thời nếu chưa rõ nguyên nhân.

---

## 14. Mitigation

- Tắt action lỗi riêng bằng feature flag nếu action khác vẫn hoạt động.
- Cung cấp workflow thủ công có audit nếu Product phê duyệt.
- Rollback backend workflow nếu release mới gây lỗi.
- Không chỉnh trực tiếp database trừ quy trình khẩn cấp được phê duyệt.

### Xác minh sau khôi phục

- Mutation thành công.
- UI phản ánh trạng thái từ backend.
- Audit log có event.
- KPI/order table cập nhật.
- Không tạo trạng thái chuyển tiếp sai.

---

# Runbook 04 — Dữ liệu tồn kho bị stale

## 15. Mô tả

Tồn kho hiển thị cũ hơn ngưỡng, đồng bộ ngừng hoặc cảnh báo không phản ánh thực tế.

### Severity

```text
SEV-2 nếu ảnh hưởng bán hàng/xuất kho
SEV-3 nếu chỉ một kho và có workaround
```

### Dấu hiệu

- `data_age_seconds{dataset="inventory"}` vượt ngưỡng.
- Inventory sync job failed.
- Nhiều SKU hiển thị cảnh báo stale.
- Số tồn Dashboard khác hệ thống kho nguồn.

---

## 16. Điều tra

- [ ] Kiểm tra `lastSyncedAt`.
- [ ] Kiểm tra sync job status.
- [ ] Kiểm tra queue depth.
- [ ] Kiểm tra source inventory system.
- [ ] Kiểm tra mapping SKU/warehouse.
- [ ] Kiểm tra timezone.
- [ ] Kiểm tra cache invalidation.
- [ ] So sánh một số SKU mẫu với nguồn.

---

## 17. Mitigation

- Hiển thị stale warning rõ ràng.
- Tạm dừng action phụ thuộc tồn chính xác nếu rủi ro cao.
- Trigger sync lại nếu idempotent.
- Chuyển sang polling hoặc nguồn dự phòng nếu có.
- Không thay stale value bằng 0.
- Không cho phép oversell dựa trên dữ liệu đã biết là cũ nếu nghiệp vụ không cho phép.

### Escalation

Chuyển SEV-1 nếu:

- Toàn bộ kho không đồng bộ.
- Có nguy cơ bán vượt tồn diện rộng.
- Có chênh lệch dữ liệu lớn và chưa rõ nguồn đúng.

---

# Runbook 05 — KPI tài chính sai hoặc không khớp

## 18. Mô tả

Doanh thu, lợi nhuận, công nợ hoặc số liệu tổng hợp không khớp báo cáo nguồn.

### Severity

```text
SEV-1 nếu số liệu sai nghiêm trọng hoặc dẫn đến quyết định sai
SEV-2 nếu chỉ một phạm vi nhỏ
```

### Nguyên tắc

Không sửa bằng cách hardcode hoặc làm tròn để che sai lệch.

---

## 19. Điều tra

- [ ] Khóa scope: date range, branch, timezone.
- [ ] Xác định KPI definition đang dùng.
- [ ] Kiểm tra đơn hủy, hoàn tiền, chiết khấu, trả hàng.
- [ ] Kiểm tra data aggregation timestamp.
- [ ] So sánh raw query/report nguồn.
- [ ] Kiểm tra cache.
- [ ] Kiểm tra release backend và frontend formatter.
- [ ] Kiểm tra currency unit và compact formatting.

### Phân loại

- Sai dữ liệu nguồn.
- Sai công thức backend.
- Sai phạm vi filter.
- Sai timezone.
- Sai cache key.
- Sai formatting frontend.

---

## 20. Mitigation

- Ẩn KPI hoặc gắn cảnh báo dữ liệu đang kiểm tra nếu số liệu không đáng tin.
- Rollback aggregation/query thay đổi mới.
- Purge cache theo scope.
- Không hiển thị số cũ như số hiện tại nếu không có stale label.
- Thông báo Product/Finance owner.

### Xác minh

- Đối chiếu ít nhất hai nguồn độc lập nếu có.
- Kiểm tra nhiều date range.
- Kiểm tra nhiều branch.
- Kiểm tra số tiền đầy đủ, không chỉ compact value.

---

# Runbook 06 — Global Search lỗi hoặc chậm

## 21. Dấu hiệu

- Search response p95 vượt ngưỡng.
- Empty result rate tăng bất thường.
- Request timeout.
- Người dùng không tìm được đơn có thật.

### Severity

```text
SEV-3 mặc định
SEV-2 nếu Search là luồng vận hành chính và lỗi diện rộng
```

---

## 22. Điều tra và mitigation

- [ ] Kiểm tra query length và debounce.
- [ ] Kiểm tra request cũ có được abort không.
- [ ] Kiểm tra search index.
- [ ] Kiểm tra permission scope.
- [ ] Kiểm tra branch filter.
- [ ] Kiểm tra rate limiting.
- [ ] Kiểm tra result ranking.

Mitigation:

- Giới hạn loại kết quả tạm thời.
- Tăng minimum query length.
- Fallback exact code lookup cho order/SKU.
- Disable fuzzy search nếu gây tải cao.
- Không bỏ permission filter để tăng kết quả.

---

# Runbook 07 — Export báo cáo thất bại

## 23. Dấu hiệu

- Export job failure tăng.
- Job queue backlog.
- File không tải được.
- Signed URL hết hạn ngay.
- CSV/XLSX/PDF lỗi định dạng.

### Severity

```text
SEV-3
SEV-2 vào thời điểm đối soát hoặc báo cáo quan trọng
```

---

## 24. Điều tra

- [ ] Kiểm tra job ID.
- [ ] Kiểm tra queue wait time.
- [ ] Kiểm tra worker status.
- [ ] Kiểm tra data volume.
- [ ] Kiểm tra storage.
- [ ] Kiểm tra signed URL expiry.
- [ ] Kiểm tra permission và scope.
- [ ] Kiểm tra format cụ thể.

### Mitigation

- Cho phép export CSV nhẹ hơn nếu PDF/XLSX lỗi.
- Giảm phạm vi date/filter nếu dữ liệu quá lớn.
- Retry job nếu idempotent.
- Scale worker nếu backlog cao.
- Không tạo file base64 qua JSON.
- Không mở rộng permission để bỏ qua lỗi export.

---

# Runbook 08 — Notification real-time bị ngắt

## 25. Dấu hiệu

- `realtime_connection_state=disconnected` kéo dài.
- Reconnect count tăng.
- Notification chỉ xuất hiện sau refresh.
- Event delay tăng.

### Severity

```text
SEV-3 nếu polling fallback hoạt động
SEV-2 nếu không có fallback và cảnh báo quan trọng bị trễ
```

---

## 26. Điều tra và mitigation

- Kiểm tra WebSocket/SSE endpoint.
- Kiểm tra proxy timeout.
- Kiểm tra auth token/session.
- Kiểm tra browser-specific issue.
- Kiểm tra event schema.

Mitigation:

- Chuyển sang polling có kiểm soát.
- Tăng reconnect backoff hợp lý.
- Không reconnect liên tục không giới hạn.
- Không tạo toast cho toàn bộ event backlog sau reconnect.
- Deduplicate bằng event ID.

---

# Runbook 09 — Notification spam hoặc toast hàng loạt

## 27. Dấu hiệu

- Toast rate tăng đột biến.
- Badge tăng nhanh bất thường.
- Notification trùng nội dung.
- Người dùng không thao tác được vì toast che UI.

### Mitigation

1. Tắt toast cho event batch.
2. Giữ notification persistent trong dropdown.
3. Bật deduplication theo entity/event type.
4. Gom summary theo kho, branch hoặc batch.
5. Rate limit event consumer.
6. Xác minh event source không publish lặp.

Không xóa notification audit quan trọng chỉ để giảm UI noise.

---

# Runbook 10 — Permission anomaly hoặc nghi ngờ lộ dữ liệu

## 28. Mô tả

Người dùng thấy menu, KPI, đơn hàng, khách hàng hoặc export ngoài quyền được cấp.

### Severity

```text
SEV-1
```

### Hành động ngay

1. Dừng rollout liên quan.
2. Tắt feature/endpoint gây lộ bằng flag hoặc gateway rule.
3. Thu hồi session bị ảnh hưởng nếu cần.
4. Không chỉ sửa frontend hide.
5. Bảo toàn log, request ID và audit evidence.
6. Thông báo Security và Engineering Lead.

---

## 29. Điều tra

- [ ] Xác định user, role, permission và scope.
- [ ] Xác định entity bị lộ.
- [ ] Kiểm tra object-level authorization.
- [ ] Kiểm tra cache key.
- [ ] Kiểm tra branch/user context khi logout/login.
- [ ] Kiểm tra search và export.
- [ ] Kiểm tra notification links.
- [ ] Kiểm tra thời gian bắt đầu.
- [ ] Xác định số người dùng bị ảnh hưởng.

### Không được làm

- Không xóa log.
- Không thay đổi dữ liệu bằng tay để che dấu.
- Không thông báo sự cố đã hết trước khi xác minh mọi đường truy cập.

---

# Runbook 11 — Session hết hạn hoặc đăng nhập lỗi diện rộng

## 30. Dấu hiệu

- Login failure tăng.
- Người dùng bị logout liên tục.
- 401 tăng ở tất cả endpoint.
- Cookie/session không được gửi.

### Điều tra

- Kiểm tra auth service.
- Kiểm tra cookie domain, Secure, SameSite.
- Kiểm tra secret rotation.
- Kiểm tra clock skew.
- Kiểm tra release auth gần nhất.
- Kiểm tra CORS/CSRF.

### Mitigation

- Rollback auth/session config mới.
- Khôi phục secret tương thích nếu an toàn.
- Thông báo người dùng đăng nhập lại nếu bắt buộc.
- Không tắt authentication để khôi phục tạm.

---

# Runbook 12 — Frontend performance regression

## 31. Dấu hiệu

- LCP/INP tăng sau release.
- Route transition chậm.
- Chart render lag.
- Bundle size tăng.
- Người dùng báo Dashboard giật.

### Severity

```text
SEV-3
SEV-2 nếu tác vụ chính gần như không dùng được
```

---

## 32. Điều tra

- So sánh release mới và cũ.
- Kiểm tra bundle diff.
- Kiểm tra chart library duplication.
- Kiểm tra API latency riêng.
- Kiểm tra ảnh thumbnail quá lớn.
- Kiểm tra context re-render.
- Kiểm tra long task và INP trace.
- Kiểm tra layout shift.

### Mitigation

- Tắt widget mới bằng feature flag.
- Lazy-load chart/widget.
- Giảm số điểm chart hiển thị.
- Giới hạn row Dashboard.
- Rollback release nếu regression lớn.

Không giảm dữ liệu chính xác hoặc accessibility để đạt điểm performance giả tạo.

---

# Runbook 13 — Browser-specific failure

## 33. Dấu hiệu

- Chỉ Safari, Firefox hoặc Edge lỗi.
- Dropdown lệch, chart trắng, date picker lỗi hoặc modal không scroll.

### Điều tra

- Xác định browser version và OS.
- Reproduce bằng viewport thật.
- Kiểm tra unsupported API.
- Kiểm tra CSS feature.
- Kiểm tra polyfill.
- Kiểm tra timezone/date parsing.

### Mitigation

- Feature flag theo capability nếu có.
- Fallback component đơn giản hơn.
- Hotfix nhỏ, tránh refactor rộng.
- Cập nhật browser support note nếu browser ngoài policy.

---

# Runbook 14 — Chunk load hoặc cache mismatch

## 34. Dấu hiệu

```text
ChunkLoadError
Failed to fetch dynamically imported module
404 static asset
```

### Điều tra

- HTML có trỏ asset release cũ không.
- CDN cache có stale HTML không.
- Artifact deploy có đầy đủ file không.
- Service worker có giữ asset cũ không.

### Mitigation

- Purge HTML cache, không purge mù toàn bộ nếu không cần.
- Rollback artifact đồng bộ.
- Tắt service worker nếu cấu hình sai.
- Hiển thị recovery UI yêu cầu reload một lần.

Không tạo vòng lặp auto reload vô hạn.

---

# Runbook 15 — Contract validation failure

## 35. Dấu hiệu

- API trả 200 nhưng widget lỗi.
- Schema validation error tăng.
- Field hoặc enum mới không được nhận diện.

### Điều tra

- So response thật với schema.
- Kiểm tra backend release.
- Kiểm tra API version.
- Kiểm tra optional/nullable field.
- Kiểm tra enum breaking change.

### Mitigation

- Rollback backend breaking change.
- Bật adapter tương thích.
- Cho unknown enum fallback an toàn nếu nghiệp vụ cho phép.
- Không bỏ runtime validation toàn bộ chỉ để hết lỗi.

---

# Runbook 16 — Data cache lẫn giữa user hoặc branch

## 36. Mô tả

Dữ liệu của branch hoặc user trước xuất hiện ở session/scope khác.

### Severity

```text
SEV-1
```

### Hành động ngay

- Tắt cache layer nghi ngờ.
- Clear cache server/client liên quan.
- Thu hồi session nếu cần.
- Dừng export/search nếu cùng cache path.
- Kích hoạt security incident process.

### Điều tra

- Query key có user/scope không.
- CDN có cache response private không.
- Server cache có vary đúng không.
- Logout có clear cache không.
- Branch switch có invalidate query không.

---

# Runbook 17 — API latency tăng cao

## 37. Dấu hiệu

- API p95 > ngưỡng.
- Widget loading kéo dài.
- Timeout tăng.

### Điều tra

- Endpoint nào chậm.
- Database query.
- Cache miss.
- Dependency latency.
- Payload size.
- Date range.
- Concurrency spike.

### Mitigation

- Bật cache an toàn.
- Giới hạn date range hoặc result size tạm thời.
- Scale service/worker.
- Disable compare query nặng.
- Tách endpoint tổng hợp.
- Giữ dữ liệu cũ có stale warning nếu an toàn.

---

# Runbook 18 — API rate limit bất thường

## 38. Dấu hiệu

- 429 tăng.
- Search hoặc notification polling thất bại.
- Một client gửi request lặp.

### Điều tra

- Kiểm tra request pattern.
- Kiểm tra retry loop.
- Kiểm tra polling interval.
- Kiểm tra debounce.
- Kiểm tra bot/abuse.

### Mitigation

- Sửa exponential backoff.
- Dừng retry vô hạn.
- Tăng giới hạn chỉ khi tải thật hợp lệ và hạ tầng cho phép.
- Block abusive source khi cần.
- Không tắt rate limit toàn hệ thống.

---

# Runbook 19 — Ảnh sản phẩm/CDN lỗi diện rộng

## 39. Dấu hiệu

- Thumbnail broken tăng.
- CDN 4xx/5xx.
- Ảnh tải chậm.

### Mitigation

- Dùng fallback icon.
- Kiểm tra signed URL expiry.
- Kiểm tra image domain config.
- Rollback CDN rule mới.
- Không để ảnh lỗi làm hỏng widget hoặc layout.

---

# Runbook 20 — Background job queue backlog

## 40. Dấu hiệu

- Queue depth tăng.
- Export/import delay.
- Job wait time cao.

### Điều tra

- Worker count.
- Job failure/retry loop.
- Job quá lớn.
- Dependency/storage chậm.
- Poison job.

### Mitigation

- Scale worker.
- Tạm dừng job không ưu tiên.
- Tách job lớn.
- Đưa poison job vào dead-letter queue.
- Không retry vô hạn job luôn thất bại.

---

## 41. Quy trình escalation chung

Escalate khi:

- Không xác định nguyên nhân sau 15–30 phút với SEV-1/2.
- Có nguy cơ lộ dữ liệu.
- Có sai số tài chính.
- Có mất dữ liệu.
- Rollback thất bại.
- Nhiều service cùng lỗi.
- Cần thay đổi database hoặc infrastructure lớn.

Kênh escalation:

```text
Incident Commander
→ Engineering Lead
→ DevOps/Backend/Frontend owner
→ Security hoặc Product theo phạm vi
→ Lãnh đạo nếu ảnh hưởng kinh doanh lớn
```

---

## 42. Quy trình communication

### Cập nhật nội bộ

Tần suất tham khảo:

- SEV-1: mỗi 15–30 phút.
- SEV-2: mỗi 30–60 phút.
- SEV-3: khi có thay đổi quan trọng.

Mỗi update gồm:

- Tình trạng hiện tại.
- Phạm vi ảnh hưởng.
- Hành động đang thực hiện.
- Rủi ro.
- Thời điểm cập nhật tiếp theo.

Không hứa thời gian khôi phục khi chưa có đủ dữ liệu.

---

## 43. Điều kiện kết thúc incident

Incident chỉ được đóng khi:

- Dịch vụ đã khôi phục.
- Smoke test pass.
- Metrics ổn định trong khoảng theo dõi.
- Dữ liệu được xác minh.
- Không còn security concern chưa xử lý.
- Timeline đã lưu.
- Owner follow-up đã xác định.
- Người liên quan đã được thông báo.

---

## 44. Post-incident checklist

- [ ] Tạo incident summary.
- [ ] Lưu timeline.
- [ ] Gắn release/commit liên quan.
- [ ] Lưu request ID/trace mẫu.
- [ ] Xác định root cause hoặc kế hoạch điều tra tiếp.
- [ ] Tạo action items.
- [ ] Gán owner và deadline.
- [ ] Cập nhật alert nếu phát hiện quá muộn.
- [ ] Cập nhật runbook nếu thiếu bước.
- [ ] Bổ sung automated test chống tái diễn.
- [ ] Review feature flag hoặc rollback process.

---

## 45. Mẫu incident summary

```md
# Incident Summary — Dashboard Overview Failure

## Thời gian
- Bắt đầu: 17:20
- Khôi phục: 17:42

## Tác động
- 72% người dùng không tải được Dashboard.
- Orders API riêng vẫn hoạt động.

## Nguyên nhân
- Backend trả enum mới không tương thích schema frontend.

## Mitigation
- Rollback backend release.

## Action items
- Thêm contract test enum.
- Bắt buộc backward-compatible API deployment.
- Thêm alert contract validation failure.
```

---

## 46. Runbook ownership matrix

| Runbook | Owner chính | Owner phối hợp |
|---|---|---|
| Dashboard không tải | Frontend | DevOps/Backend |
| Overview API lỗi | Backend | Frontend/DevOps |
| Order mutation lỗi | Backend | Frontend/Product |
| Inventory stale | Backend/Operations | Warehouse/Frontend |
| KPI tài chính sai | Backend/Data | Finance/Product |
| Search lỗi | Backend/Frontend | DevOps |
| Export lỗi | Backend/Worker | Frontend/DevOps |
| Notification realtime | Backend/Frontend | DevOps |
| Permission anomaly | Security/Backend | Frontend/Product |
| Session/login lỗi | Backend/Security | DevOps/Frontend |
| Performance regression | Frontend | Backend/DevOps |
| Cache scope leak | Security/Backend | Frontend/DevOps |

Owner thực tế phải được ghi trong hệ thống on-call nội bộ.

---

## 47. Runbook review cadence

Runbook phải được review:

- Sau mỗi incident liên quan.
- Sau thay đổi kiến trúc lớn.
- Sau đổi monitoring platform.
- Ít nhất mỗi quý với runbook SEV-1.
- Khi owner hoặc kênh escalation thay đổi.

Runbook cũ hoặc sai còn nguy hiểm hơn không có runbook.

---

## 48. Game day và diễn tập

Nên diễn tập định kỳ:

- Dashboard overview API down.
- Permission scope leak giả lập.
- Export queue backlog.
- Inventory stale.
- Frontend rollback.
- Auth service outage.

Mục tiêu:

- Kiểm tra runbook có dùng được không.
- Kiểm tra quyền truy cập công cụ.
- Đo thời gian phát hiện và khôi phục.
- Xác định bước còn thiếu.

Không chạy diễn tập phá hoại trên production nếu chưa có cơ chế an toàn.

---

## 49. AI coding agent requirements

AI khi cập nhật runbook hoặc code liên quan phải:

- Không tự chạy thao tác production.
- Không bịa dashboard, metric hoặc endpoint chưa tồn tại.
- Phân biệt rõ bước xác minh và bước thay đổi.
- Không đề xuất xóa log hoặc bỏ security check.
- Không đề xuất sửa trực tiếp database như giải pháp mặc định.
- Báo rõ thao tác nào cần human approval.
- Ghi rõ rollback và cách xác minh.
- Không tuyên bố sự cố đã khắc phục nếu chưa có metric/smoke-test evidence.

---

## 50. Acceptance checklist

- [ ] Có runbook cho Dashboard không tải.
- [ ] Có runbook cho API overview lỗi.
- [ ] Có runbook cho order mutation.
- [ ] Có runbook cho inventory stale.
- [ ] Có runbook cho KPI tài chính sai.
- [ ] Có runbook cho search và export.
- [ ] Có runbook cho realtime notification.
- [ ] Có runbook cho permission anomaly.
- [ ] Có runbook cho session/auth failure.
- [ ] Có runbook cho performance regression.
- [ ] Có runbook cho cache scope leak.
- [ ] Mỗi runbook có severity, owner và escalation.
- [ ] Có bước xác minh trước thay đổi.
- [ ] Có rollback hoặc mitigation rõ.
- [ ] Có bước xác minh khôi phục.
- [ ] Có incident timeline và summary template.
- [ ] Có ownership matrix.
- [ ] Có review cadence và game day.

---

## 51. Những điều không được làm

- Không thay đổi production trước khi xác minh alert.
- Không để nhiều người cùng chỉ huy incident.
- Không xóa log hoặc audit evidence.
- Không cấp quyền rộng để né lỗi permission.
- Không hardcode số liệu để che lỗi KPI.
- Không dùng số 0 thay cho dữ liệu không tải được.
- Không retry mutation nguy hiểm tự động.
- Không reload trang vô hạn khi chunk lỗi.
- Không tắt rate limit hoặc authentication toàn hệ thống.
- Không đóng incident khi metrics chưa ổn định.
- Không để runbook thiếu owner hoặc kênh escalation.

---

## 52. Kết luận

Bộ runbook của Cynca VLXD Dashboard giúp đội kỹ thuật phản ứng nhất quán trước các sự cố về giao diện, API, dữ liệu, workflow, permission, hiệu năng và hạ tầng. Mỗi runbook phải ưu tiên giảm tác động, bảo toàn dữ liệu, giữ bằng chứng và xác minh rõ ràng trước khi kết luận hệ thống đã khôi phục.

File tiếp theo đề xuất:

```text
README.md
26-Analytics-Event-Plan.md
```