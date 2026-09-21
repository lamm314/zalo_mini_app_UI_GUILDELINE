# Bộ đặc tả Mini App + CRM phòng khám

Phiên bản 1.0 — 2026-09-21. Bộ tài liệu dành cho AI coding agent triển khai ba hệ thống độc lập, theo thứ tự dưới đây.

| Thứ tự | File triển khai độc lập | Trọng tâm chuyên khoa |
|---|---|---|
| 1 | [Nha khoa](01-NHA-KHOA-SPEC.md) | Sơ đồ răng, nha chu, kế hoạch nhiều buổi, thủ thuật, labo, chỉnh nha và tiệt khuẩn |
| 2 | [Da liễu](02-DA-LIEU-SPEC.md) | Body map, ảnh và consent, liệu trình/gói buổi, home care và phản ánh sau điều trị |
| 3 | [Đa khoa](03-DA-KHOA-SPEC.md) | Visit nhiều phòng khám, sinh hiệu, chuyển khoa, mẫu xét nghiệm, kết quả, đối chiếu thuốc và theo dõi |

## Cách sử dụng

Đưa **một file chuyên khoa** cho AI và yêu cầu triển khai theo hướng dẫn sau. Mỗi file tự chứa toàn bộ core, màn hình, dữ liệu, workflow, API, module chuyên khoa, seed và acceptance criteria; không cần ghép ba file hoặc đọc tài liệu Cynca trong repo này.

> Hãy triển khai hệ thống Mini App + CRM theo toàn bộ file đặc tả được đính kèm. Làm P0 trước, dùng backend và database lưu bền vững, tách adapter mock/live, không dùng localStorage làm database nghiệp vụ. Triển khai module chuyên khoa riêng của file, thực hiện checklist screen IDs và acceptance criteria, rồi bàn giao source, migration, seed/reset, OpenAPI, DEMO.md, hướng dẫn chạy và kết quả kiểm thử. Không thay chức năng bằng nút giả thành công. Áp dụng các mặc định đã chốt trong đặc tả; ghi khác biệt cần thiết vào DECISIONS.md. Chưa có credentials thì hoàn tất demo bằng mock adapter, không chặn các luồng còn lại.

Hoàn thành và nghiệm thu Nha khoa trước, tiếp tục Da liễu, rồi Đa khoa. Có thể tái sử dụng package core; mỗi hệ thống giữ cấu hình, DB, danh tính và dữ liệu demo độc lập. Bộ file này là **đặc tả**, chưa phải mã nguồn hệ thống đã triển khai.

## Nội dung trong mỗi file

1. Mục tiêu, phạm vi P0/P1 và mặc định triển khai.
2. Persona, sitemap và yêu cầu UI responsive.
3. Luồng Mini App → tiếp nhận → khám → thực hiện/cấp phát → thu tiền → chia sẻ → tái khám.
4. Màn hình Mini App M01–M17 và CRM C01–C29; màn hình riêng D/S/G.
5. Trường dữ liệu, validation, trạng thái, quyền chuyển và transaction.
6. Module chuyên khoa với mô hình, API, báo cáo và kịch bản riêng.
7. Lịch, hồ sơ, đơn thuốc, kho/lô/vật tư và tính tiền/công nợ/hoàn tiền.
8. Phân quyền theo vai trò/phạm vi, audit, consent và quyền truy cập tệp.
9. Dashboard/report, API core, quan hệ dữ liệu, sự kiện và tích hợp.
10. Cấu trúc thư mục, lộ trình triển khai, seed deterministic, 24 tiêu chí core và 10 tiêu chí chuyên khoa.

## Các quyết định đã chốt

- Mini App ưu tiên Zalo, có web mobile demo; CRM là web cho nhân viên.
- Demo hoạt động đủ end-to-end với mock login/notification/payment, không cần gửi tin hoặc thu tiền thật.
- Chuyên môn do bác sĩ nhập/xác nhận; không triển khai tự chẩn đoán, tự kê thuốc hoặc tự tính liều.
- Hồ sơ signed dùng addendum; patient chỉ thấy phiên bản đã release; role admin không mặc nhiên có quyền đọc bệnh án.
- Trạng thái lead/lịch/lượt khám/đơn/thu tiền độc lập, không dùng một pipeline chung.
- Kho dùng ledger theo lô, thu tiền dùng payment/allocation/credit/refund, có chống lặp giao dịch.
- Tất cả người, ảnh, thuốc, số điện thoại, giá và nội dung seed là dữ liệu kiểm thử. Tích hợp thật, bảo hiểm, hóa đơn điện tử và chữ ký số thuộc P1.

## Nơi lưu và nguồn tham khảo

Đặt trong `docs/clinic-crm-specs` của repo `lamm314/zalo_mini_app_UI_GUILDELINE` vì repo hiện dùng để lưu hướng dẫn Mini App theo ngành. Giữ nguyên tài liệu và tài nguyên ngành khác. Mỗi file có nguồn chính thức cho Zalo, OWASP và HL7; Nha khoa có thêm nguồn định danh răng. Các lựa chọn sản phẩm và giá demo không được trình bày như quy định y khoa/pháp lý.
