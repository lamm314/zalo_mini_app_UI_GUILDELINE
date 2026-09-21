# Bộ đặc tả Mini App + CRM phòng khám

Phiên bản 1.1 — 2026-09-21. Bộ tài liệu dành cho AI coding agent triển khai ba hệ thống độc lập, theo thứ tự dưới đây.

| Thứ tự | File triển khai độc lập | Trọng tâm chuyên khoa |
|---|---|---|
| 1 | [Nha khoa](01-NHA-KHOA-SPEC.md) | Sơ đồ răng, nha chu, kế hoạch nhiều buổi, thủ thuật, labo, chỉnh nha và tiệt khuẩn |
| 2 | [Da liễu](02-DA-LIEU-SPEC.md) | Body map, ảnh và consent, liệu trình/gói buổi, home care và phản ánh sau điều trị |
| 3 | [Đa khoa](03-DA-KHOA-SPEC.md) | Visit nhiều phòng khám, sinh hiệu, chuyển khoa, mẫu xét nghiệm, kết quả, đối chiếu thuốc và theo dõi |

## Cách sử dụng

Đưa **một file chuyên khoa** cho AI và yêu cầu triển khai theo hướng dẫn sau. Mỗi file tự chứa toàn bộ core, màn hình, dữ liệu, workflow, API, module chuyên khoa, seed và acceptance criteria; không cần ghép ba file hoặc đọc tài liệu Cynca trong repo này.

> Hãy triển khai hệ thống Mini App + CRM theo toàn bộ file đặc tả được đính kèm. Làm P0 trước, dùng backend và database lưu bền vững, tách adapter mock/live, không dùng localStorage làm database nghiệp vụ. Triển khai module chuyên khoa riêng của file, thực hiện checklist screen IDs và acceptance criteria, rồi bàn giao source, migration, seed/reset, OpenAPI, DEMO.md, hướng dẫn chạy và kết quả kiểm thử. Không thay chức năng bằng nút giả thành công. Áp dụng các mặc định đã chốt trong đặc tả; ghi khác biệt cần thiết vào DECISIONS.md. Chưa có credentials thì hoàn tất demo bằng mock adapter, không chặn các luồng còn lại. Thiết kế đẹp và chuyên nghiệp là P0: tuân thủ hướng thiết kế riêng tại mục 3.4–3.8; dùng ảnh tham khảo để học bố cục, xây nhận diện và layout khác biệt cho từng chuyên khoa và từng clinic, không chỉ đổi màu/logo. Bàn giao DESIGN-BRIEF.md, themes/tokens, component gallery, asset manifest và ảnh QA; nghiệm thu đủ DESIGN-AC01–10.

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
10. Cấu trúc thư mục, lộ trình triển khai, seed deterministic, 24 tiêu chí core, 10 tiêu chí chuyên khoa và 10 tiêu chí thiết kế.

## Hướng thiết kế bắt buộc

Thiết kế là P0 cho cả Mini App và CRM. Mục 3.4–3.8 trong mỗi file mô tả đầy đủ palette, typography, bố cục các màn chính, biến thể từng phòng khám và tiêu chí nghiệm thu; không cần đọc thêm một file thiết kế chung.

| Chuyên khoa | Phong cách / màu chủ đạo | Bố cục nhận diện |
|---|---|---|
| Nha khoa | Sáng, sạch, thân thiện; teal/navy `#053147`, cyan, trắng | Hero bác sĩ, dịch vụ răng, kế hoạch và chart răng, CRM lịch ghế/labo |
| Da liễu | Tinh tế, nhẹ nhàng; green `#31564D`, nền kem, sage và màu đất nhẹ | Trang chủ editorial, ảnh có khoảng thở, liệu trình/home care, workspace ảnh và consent |
| Đa khoa | Rõ ràng, tin cậy; blue `#1D4ED8`, navy, trắng | Tìm khoa và đặt lịch ưu tiên, profile gia đình, timeline visit, CRM điều phối nhiều phòng |

![Ảnh tham khảo do người dùng cung cấp](references/mini-app-design-reference.png)

Giữ tinh thần ảnh bác sĩ nổi bật, card gọn, khoảng trắng và CTA rõ từ ảnh; tạo composition phù hợp riêng cho mỗi hệ thống. Ảnh này chỉ làm reference thiết kế, không là tài sản production; không sao chép logo, tên, rating, giá hoặc watermark. Mỗi clinic có brand profile riêng và một fixture B để kiểm tra khác biệt ít nhất ba chiều, gồm ít nhất một chiều bố cục/thứ tự nội dung. Chi nhánh cùng thương hiệu thừa kế nhận diện clinic.

Bằng chứng nghiệm thu: screenshot từ app chạy thật, theme tokens, component states, kiểm tra tương phản/responsive và 10 DESIGN-AC. Chỉ đổi màu trên template chung hoặc có giao diện đúng chức năng nhưng sơ sài chưa được tính là hoàn tất. Bộ tài liệu này quy định đầu ra thiết kế cho lần triển khai; chưa phải bộ mockup hoặc ứng dụng đã được xây dựng.

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
