# CYNCA VLXD — Zalo Mini App UI/UX Guideline

> Phiên bản 1.0 · Màu chủ đạo xanh dương · Thiết kế ưu tiên mobile 360–430 px

## 1. Mục tiêu sản phẩm

Cynca VLXD là Zalo Mini App bán vật liệu xây dựng cho khách lẻ, thợ thi công, chủ nhà, nhà thầu và doanh nghiệp. Giao diện phải tạo cảm giác uy tín, rõ giá, dễ tìm sản phẩm, thuận tiện xin báo giá và đặt hàng số lượng lớn.

### Nguyên tắc trải nghiệm

1. **Tin cậy trước, bán hàng sau:** luôn hiển thị nguồn gốc, quy cách, tiêu chuẩn, tình trạng hàng và chính sách giao.
2. **Tối giản thao tác:** người dùng tìm sản phẩm hoặc gửi yêu cầu báo giá trong tối đa 3 bước.
3. **Tối ưu cho vật liệu nặng:** ngoài “Mua ngay” phải có “Nhận báo giá”, “Tính khối lượng” và “Tư vấn kỹ thuật”.
4. **Ảnh lớn, chữ ngắn:** ưu tiên hình ảnh công trình và sản phẩm thật; không nhồi quá nhiều banner.
5. **Đồng bộ Zalo:** giữ hành vi điều hướng quen thuộc, vùng chạm tối thiểu 44 px và nút quay lại rõ ràng.

---

## 2. Định vị hình ảnh

### Tính cách thương hiệu

- Chuyên nghiệp
- Vững chắc
- Minh bạch
- Hiện đại
- Gần gũi với người xây nhà

### Thông điệp chính

**Cynca VLXD — Chất lượng tạo nền công trình bền vững.**

### Bảng màu

| Token | Màu | Mục đích |
|---|---:|---|
| `primary-600` | `#0B5ED7` | CTA, trạng thái active, giá |
| `primary-700` | `#084298` | Header, hover/pressed |
| `navy-900` | `#0A2E73` | Hero overlay, tiêu đề lớn |
| `blue-50` | `#EEF5FF` | Nền icon, badge nhẹ |
| `surface` | `#FFFFFF` | Card và vùng nội dung |
| `background` | `#F5F8FC` | Nền ứng dụng |
| `text-primary` | `#172033` | Nội dung chính |
| `text-secondary` | `#667085` | Nội dung phụ |
| `success` | `#16883E` | Còn hàng, hoàn tất |
| `warning` | `#F5A000` | Khuyến mại, cảnh báo |
| `danger` | `#D92D20` | Lỗi, hết hàng |

Tỷ lệ sử dụng đề xuất: 70% trắng/xám sáng, 20% xanh dương, 10% màu nhấn.

### Typography

- Font ưu tiên: **Inter**, fallback `-apple-system`, `BlinkMacSystemFont`, `Arial`.
- Display Hero: 28/34, 700.
- Heading 1: 24/30, 700.
- Heading 2: 20/26, 700.
- Heading 3: 17/24, 600.
- Body: 15/22, 400.
- Caption: 12/17, 400.
- Giá: 20/26, 700.

Không viết toàn bộ nội dung bằng chữ in hoa, ngoại trừ nhãn ngắn hoặc headline Hero.

### Grid và khoảng cách

Dùng hệ 8pt: `4, 8, 12, 16, 24, 32, 40`. Padding ngang màn hình 16 px. Khoảng cách giữa section 24 px. Card radius 16–24 px; button radius 12–16 px.

---

## 3. Luồng mở ứng dụng

### 3.1 Splash Screen

Thời lượng 600–900 ms.

- Nền gradient `#0A2E73 → #0B5ED7`.
- Logo Cynca VLXD màu trắng đặt giữa.
- Dòng phụ: “Vật liệu chuẩn · Giá minh bạch”.
- Không đặt nút bấm.
- Chuyển cảnh fade sang Hero Welcome.

### 3.2 Hero Welcome — trước khi vào cửa hàng

Đây là màn hình bắt buộc trong lần mở đầu tiên hoặc khi có chiến dịch lớn.

**Bố cục:**

- Ảnh công trình full-bleed.
- Overlay xanh navy từ 20% phía trên đến 85% phía dưới.
- Logo phía trên, tránh vùng điều khiển của Zalo.
- Headline tối đa 3 dòng.
- 3 lợi ích dạng icon: chính hãng, giao toàn quốc, hỗ trợ kỹ thuật.
- CTA chính: “Khám phá cửa hàng”.
- CTA phụ dạng text: “Nhận báo giá công trình”.
- Chỉ báo 3 slide ở phía trên CTA.

**Nội dung đề xuất:**

> Vật liệu chất lượng  
> Tạo nền công trình bền vững

Nút CTA cao 52 px, màu `primary-600`, chữ trắng, icon mũi tên.

**Hành vi:**

- Nhấn CTA → Home.
- Vuốt ngang → thay hero chiến dịch.
- Lần mở sau có thể bỏ qua màn hình nếu người dùng đã chọn “Không hiển thị lại”.
- Ảnh tải chậm phải có blur placeholder và nền gradient dự phòng.

---

## 4. Trang chủ

### 4.1 App Header

- Tên: **Cynca VLXD**.
- Subtitle: “Zalo Mini App”.
- Icon giỏ hàng có badge.
- Header trắng hoặc xanh tùy vị trí cuộn.
- Khi cuộn quá Hero, chuyển thành sticky compact header.

### 4.2 Search

Placeholder: “Tìm xi măng, thép, gạch, sơn...”

- Cao 48 px.
- Có tìm kiếm gần đây.
- Gợi ý theo từ khóa và danh mục.
- Hỗ trợ tìm theo thương hiệu, mã hàng, kích thước.
- Nút scan mã QR/barcode là tùy chọn.

### 4.3 Hero Banner trong Home

Tỷ lệ 16:9 hoặc 1.6:1, radius 20 px.

- Tối đa 3 slide.
- Mỗi slide chỉ có 1 headline, 1 mô tả ngắn và 1 CTA.
- Không để chữ đè lên vùng ảnh có chi tiết phức tạp.
- Auto-slide 5 giây; dừng khi người dùng tương tác.

### 4.4 Danh mục nổi bật

Hiển thị 8 danh mục:

1. Xi măng
2. Thép xây dựng
3. Gạch xây dựng
4. Cát, đá, sỏi
5. Tôn và mái lợp
6. Sơn và chống thấm
7. Điện nước
8. Thiết bị vệ sinh

Dùng lưới 4 cột. Icon hoặc ảnh nền `blue-50`, kích thước 56–64 px. Tên tối đa 2 dòng.

### 4.5 Dải cam kết

Bốn cam kết:

- Sản phẩm chính hãng
- Giá cạnh tranh
- Giao hàng đúng hẹn
- Hỗ trợ kỹ thuật 24/7

Dùng icon line màu xanh, không dùng quá nhiều hiệu ứng.

### 4.6 Sản phẩm nổi bật

Card rộng 156–172 px, scroll ngang.

Mỗi card gồm:

- Ảnh tỷ lệ 1:1
- Badge “Bán chạy” hoặc “Giá tốt”
- Tên tối đa 2 dòng
- Quy cách/đơn vị
- Giá hoặc “Liên hệ”
- Nút thêm nhanh vào giỏ

### 4.7 Khối báo giá công trình

Banner riêng cho khách hàng doanh nghiệp:

- “Nhận báo giá trong 15 phút”
- Nút “Gửi danh sách vật tư”
- Cho phép tải ảnh, nhập khối lượng hoặc gửi file.
- Không cam kết 15 phút nếu backend chưa có SLA thực tế.

### 4.8 Bottom Navigation

5 tab:

- Trang chủ
- Danh mục
- Khuyến mãi
- Đơn hàng
- Tài khoản

Active dùng `primary-600`, inactive dùng `#667085`. Không dùng nhãn dài hơn 12 ký tự nếu có thể.

---

## 5. Trang danh mục và danh sách sản phẩm

### Bộ lọc

- Danh mục
- Thương hiệu
- Khoảng giá
- Quy cách
- Đơn vị tính
- Khu vực giao
- Còn hàng
- Đánh giá

Bộ lọc đang áp dụng hiển thị thành chip, có “Xóa tất cả”.

### Kiểu hiển thị

- Danh mục cấp 1: list card ảnh lớn.
- Sản phẩm: grid 2 cột.
- Vật liệu có nhiều thông số: cho phép chuyển sang list để đọc nhanh.

### Sắp xếp

- Phù hợp nhất
- Bán chạy
- Giá thấp đến cao
- Giá cao đến thấp
- Mới nhất

---

## 6. Chi tiết sản phẩm

### Gallery

- Ảnh chính 1:1 hoặc 4:3.
- 4 thumbnail.
- Zoom khi chạm.
- Ảnh đầu là packshot nền sáng; ảnh sau là ứng dụng thực tế, quy cách, tem nhãn và vận chuyển.

### Thông tin phía trên

- Tên sản phẩm
- Mã SKU
- Đánh giá và số lượt bán
- Giá theo đơn vị
- Trạng thái tồn kho
- Khu vực giao dự kiến
- Chọn biến thể/quy cách

### Thông số bắt buộc

| Nhóm | Ví dụ |
|---|---|
| Thương hiệu | Cynca/đối tác |
| Xuất xứ | Việt Nam |
| Tiêu chuẩn | TCVN tương ứng |
| Quy cách | Bao 50 kg, cây 11.7 m... |
| Đơn vị bán | Bao, cây, viên, m², m³ |
| Bảo quản | Khô ráo, tránh ẩm |
| Giao hàng | Theo tải trọng và khu vực |

### CTA cố định

- Nút phụ: **Thêm vào giỏ**
- Nút chính: **Mua ngay**
- Với đơn số lượng lớn, thay nút phụ bằng **Nhận báo giá**.
- CTA nằm trong safe area và không che nội dung.

### Khối hỗ trợ

- Tư vấn kỹ thuật
- Chính sách đổi trả
- Điều kiện giao hàng
- Hóa đơn VAT
- Tài liệu kỹ thuật tải về

---

## 7. Giỏ hàng và thanh toán

### Giỏ hàng

- Nhóm theo nhà cung cấp hoặc kho.
- Chỉnh số lượng và đơn vị.
- Hiển thị trọng lượng ước tính.
- Cảnh báo phí giao chưa bao gồm bốc xếp/cẩu hàng.
- Cho phép yêu cầu xuất VAT.
- Cho phép ghi chú vị trí giao và thời gian nhận.

### Checkout

Các bước:

1. Địa chỉ giao hàng
2. Khung giờ
3. Phương thức giao
4. Thông tin xuất hóa đơn
5. Phương thức thanh toán
6. Xác nhận đơn

Không yêu cầu nhập lại dữ liệu đã có từ Zalo nếu người dùng đã đồng ý chia sẻ.

---

## 8. Đơn hàng và tài khoản

### Trạng thái đơn

- Chờ xác nhận
- Đã xác nhận
- Đang chuẩn bị
- Đang giao
- Đã hoàn thành
- Đã hủy

Timeline phải hiển thị thời gian, người liên hệ và nút gọi hỗ trợ.

### Tài khoản

- Hồ sơ
- Địa chỉ
- Thông tin doanh nghiệp
- Hóa đơn VAT
- Sản phẩm yêu thích
- Báo giá đã nhận
- Chính sách và hỗ trợ
- Đăng xuất

---

## 9. Trạng thái giao diện

Mỗi màn hình phải có:

- Loading skeleton
- Empty state
- Error state
- Offline state
- Permission denied
- Hết hàng
- Không giao tới khu vực
- Giá đang cập nhật

Thông báo lỗi phải mô tả cách xử lý, không chỉ ghi “Có lỗi xảy ra”.

---

## 10. Motion và micro-interaction

- Transition chuẩn: 180–240 ms.
- Card press: scale 0.98.
- Thêm giỏ: icon chuyển động nhẹ và badge tăng.
- Không dùng animation liên tục gây mất tập trung.
- Tôn trọng thiết lập giảm chuyển động của thiết bị.
- Hero Welcome: fade 300 ms, Home slide-up 260 ms.

---

## 11. Accessibility

- Tương phản chữ đạt tối thiểu WCAG AA.
- Vùng chạm ít nhất 44×44 px.
- Không truyền đạt trạng thái chỉ bằng màu.
- Ảnh có alt text.
- Giá và đơn vị đọc được bằng screen reader.
- Nội dung vẫn sử dụng được khi tăng font 200%.

---

## 12. Quy chuẩn ảnh

### Hero

- 1440×1920 cho Welcome.
- 1600×900 cho banner Home.
- Công trình hiện đại, ánh sáng tự nhiên.
- Chừa khoảng trống cho headline.
- Overlay xanh được xử lý trong UI, không đóng cứng vào ảnh gốc khi có thể.

### Ảnh sản phẩm

- Master: 1600×1600.
- Web export: 800×800, WebP 75–85%.
- Nền `#F7F9FC` hoặc trắng.
- Góc chụp 3/4, bóng đổ nhẹ.
- Không chèn giá hoặc CTA vào ảnh.
- Ảnh phải đúng sản phẩm, đúng màu, đúng bao bì.
- Với cát/đá: dùng khay hoặc pile sạch, có ảnh close-up kích thước hạt.
- Với thép: hiển thị tiết diện, đường kính và bó thép.
- Với sơn: tem nhãn phải đọc được ở ảnh zoom.

Các SVG trong `assets/cynca-vlxd/products/` là **bộ preview bố cục 50 sản phẩm**, dùng để kiểm tra giao diện trước khi thay bằng ảnh chụp chính thức.

---

## 13. Danh sách 50 sản phẩm mẫu

1. **Xi măng PCB40** — Xi măng
2. **Xi măng PCB30** — Xi măng
3. **Xi măng trắng** — Xi măng
4. **Xi măng xây tô** — Xi măng
5. **Vữa khô trộn sẵn** — Xi măng
6. **Thép cuộn D6** — Thép
7. **Thép cây D10** — Thép
8. **Thép cây D12** — Thép
9. **Thép cây D16** — Thép
10. **Lưới thép hàn** — Thép
11. **Gạch đỏ 2 lỗ** — Gạch
12. **Gạch đỏ 4 lỗ** — Gạch
13. **Gạch đặc** — Gạch
14. **Gạch block** — Gạch
15. **Gạch bê tông khí** — Gạch
16. **Cát vàng** — Cát đá
17. **Cát đen** — Cát đá
18. **Đá 1x2** — Cát đá
19. **Đá 2x4** — Cát đá
20. **Sỏi xây dựng** — Cát đá
21. **Tôn lạnh** — Mái lợp
22. **Tôn màu** — Mái lợp
23. **Ngói đất nung** — Mái lợp
24. **Ngói màu** — Mái lợp
25. **Tấm lợp fibro** — Mái lợp
26. **Sơn nội thất** — Sơn
27. **Sơn ngoại thất** — Sơn
28. **Sơn chống thấm** — Sơn
29. **Sơn lót kháng kiềm** — Sơn
30. **Bột bả tường** — Sơn
31. **Ống PVC phi 21** — Điện nước
32. **Ống PVC phi 60** — Điện nước
33. **Ống PPR nóng lạnh** — Điện nước
34. **Dây điện 2.5mm** — Điện nước
35. **Cáp điện 4mm** — Điện nước
36. **Bồn cầu một khối** — Thiết bị vệ sinh
37. **Chậu lavabo** — Thiết bị vệ sinh
38. **Vòi sen nóng lạnh** — Thiết bị vệ sinh
39. **Vòi rửa lavabo** — Thiết bị vệ sinh
40. **Chậu rửa inox** — Thiết bị vệ sinh
41. **Gạch lát 60x60** — Hoàn thiện
42. **Gạch lát 80x80** — Hoàn thiện
43. **Gạch ốp 30x60** — Hoàn thiện
44. **Đá granite** — Hoàn thiện
45. **Đá marble** — Hoàn thiện
46. **Tấm thạch cao** — Vật liệu phụ
47. **Khung xương trần** — Vật liệu phụ
48. **Keo dán gạch** — Vật liệu phụ
49. **Phụ gia chống thấm** — Vật liệu phụ
50. **Lưới thủy tinh** — Vật liệu phụ

---

## 14. Cấu trúc dữ liệu sản phẩm tối thiểu

```json
{
  "id": "string",
  "sku": "string",
  "name": "string",
  "categoryId": "string",
  "brand": "string",
  "price": 0,
  "priceType": "fixed | contact | tiered",
  "unit": "bao | cây | viên | m2 | m3 | bộ",
  "images": [],
  "specifications": {},
  "stockStatus": "in_stock | low_stock | out_of_stock",
  "deliveryAreas": [],
  "vatIncluded": false,
  "featured": false
}
```

---

## 15. Tiêu chí nghiệm thu UI

- Hiển thị tốt từ 320 đến 430 px.
- Không có horizontal overflow.
- Nội dung chính tải dưới 2.5 giây trên mạng 4G phổ thông.
- Hero có placeholder.
- Toàn bộ CTA có trạng thái default/pressed/disabled/loading.
- Bottom navigation không bị che bởi safe area.
- Tìm kiếm, giỏ hàng, báo giá và checkout có analytics event.
- Ảnh sản phẩm dùng lazy loading.
- Không hardcode sản phẩm, giá, banner và danh mục trong Mini App; tất cả lấy từ API/backend.

---

## 16. File đính kèm

- `assets/cynca-vlxd/mockups/hero-welcome.svg`
- `assets/cynca-vlxd/mockups/home-screen.svg`
- `assets/cynca-vlxd/products/product-catalog-50.svg`
- `docs/cynca-vlxd/products.csv`
