# Cynca VLXD — Zalo Mini App UI Guideline

> Bộ hướng dẫn thiết kế giao diện Zalo Mini App bán vật liệu xây dựng cho thương hiệu **Cynca VLXD**.

## 1. Mục tiêu

Thiết kế một Mini App chuyên nghiệp, hiện đại, dễ mua hàng và phù hợp cả khách lẻ, chủ thầu, đội thi công và khách hàng doanh nghiệp. Giao diện lấy cảm hứng từ các ứng dụng thương mại cao cấp, nhưng giữ thao tác ngắn, chữ rõ và điều hướng phù hợp môi trường Zalo Mini App.

## 2. Định vị hình ảnh

- Thương hiệu: **Cynca VLXD**.
- Lĩnh vực: vật liệu xây dựng và thiết bị hoàn thiện công trình.
- Tính cách: đáng tin cậy, hiện đại, kỹ thuật, minh bạch, nhanh chóng.
- Cảm xúc cần truyền tải: công trình bền vững, nguồn hàng chính hãng, giao hàng đúng tiến độ.
- Phong cách: minimal commercial, premium industrial, nhiều khoảng trắng, ảnh sản phẩm lớn, ít chi tiết trang trí thừa.

## 3. Bảng màu

| Token | Mã màu | Mục đích |
|---|---:|---|
| Primary 700 | `#0646B8` | Header, nền hero, trạng thái quan trọng |
| Primary 600 | `#075CE5` | CTA chính, tab đang chọn |
| Primary 500 | `#1677FF` | Liên kết, icon chủ đạo |
| Primary 100 | `#EAF3FF` | Nền badge, card thông tin |
| Navy 900 | `#082554` | Tiêu đề lớn, gradient hero |
| Accent | `#FFB800` | Giá khuyến mại, điểm nhấn |
| Background | `#F5F7FB` | Nền ứng dụng |
| Surface | `#FFFFFF` | Card và khu vực nội dung |
| Text Primary | `#172033` | Văn bản chính |
| Text Secondary | `#667085` | Mô tả phụ |
| Border | `#E7ECF3` | Đường viền |
| Success | `#18A957` | Còn hàng, giao thành công |
| Error | `#E5484D` | Hết hàng, lỗi, cảnh báo |

Không dùng xanh dương trên toàn bộ màn hình. Màu xanh tập trung ở hero, header, CTA, tab active và icon quan trọng; phần nội dung dùng nền trắng để tạo cảm giác cao cấp.

## 4. Typography

Ưu tiên `Inter`, sau đó `SF Pro Display`, `Arial`, sans-serif.

| Cấp chữ | Kích thước | Độ đậm | Ứng dụng |
|---|---:|---:|---|
| Display | 32 px | 700 | Hero screen |
| H1 | 24 px | 700 | Tiêu đề trang |
| H2 | 20 px | 700 | Tiêu đề khu vực |
| H3 | 17 px | 600 | Tên sản phẩm/card |
| Body | 15–16 px | 400 | Nội dung chính |
| Caption | 12–13 px | 400 | Mô tả phụ |
| Price | 20–24 px | 700 | Giá bán |

Không dùng quá ba cấp độ đậm trên cùng một màn hình.

## 5. Grid và hình khối

- Grid cơ sở: 8 px.
- Padding màn hình: 16 px.
- Khoảng cách khu vực: 24–32 px.
- Khoảng cách card: 12–16 px.
- Bo góc card: 16–20 px.
- Bo góc hero/banner: 24 px.
- Bo góc nút: 14–16 px hoặc pill.
- Chiều cao CTA chính: 48–52 px.
- Shadow: rất nhẹ, ưu tiên đường viền mảnh thay vì bóng đậm.

## 6. Luồng trải nghiệm chính

```text
Zalo → Hero mở đầu → Trang chủ → Danh mục → Danh sách sản phẩm
→ Chi tiết sản phẩm → Giỏ hàng → Địa chỉ giao hàng → Xác nhận đơn
→ Theo dõi đơn → Đánh giá / Mua lại
```

Hero mở đầu chỉ hiển thị lần đầu hoặc khi chiến dịch cần quảng bá. Người dùng cũ có thể vào thẳng trang chủ.

## 7. Màn hình Hero mở đầu

### Mục tiêu

Tạo ấn tượng thương hiệu trước khi người dùng vào cửa hàng mà không làm chậm quá trình mua hàng.

### Bố cục

1. Ảnh công trình toàn màn hình, tỷ lệ dọc 9:16.
2. Overlay gradient từ `#05275D` 90% ở đáy tới trong suốt ở phần trên.
3. Nhãn nhỏ “Zalo Mini App” ở góc trên.
4. Logo Cynca VLXD ở khoảng 30–35% chiều cao.
5. Slogan: **Vật liệu chuẩn — Công trình bền vững**.
6. Ba cam kết dạng icon nhỏ:
   - Sản phẩm chính hãng.
   - Giao hàng toàn quốc.
   - Hỗ trợ kỹ thuật 24/7.
7. CTA: **Khám phá cửa hàng**.
8. Nút “Bỏ qua” nhỏ, dễ nhận biết nhưng không cạnh tranh với CTA.

### Chuyển động

- Ảnh nền zoom rất nhẹ 1.00 → 1.04 trong 4 giây.
- Logo và slogan fade-up 300 ms.
- CTA xuất hiện sau 150 ms.
- Chạm CTA: hero trượt lên, trang chủ xuất hiện từ dưới.

## 8. Trang chủ

Thứ tự khu vực:

1. App header: quay lại, tên Cynca VLXD, nút menu Zalo, giỏ hàng.
2. Thanh tìm kiếm cố định khi cuộn: “Tìm xi măng, thép, gạch, sơn…”.
3. Hero banner trong cửa hàng: ảnh công trình, một thông điệp, một CTA.
4. Danh mục nhanh 2 hàng × 4 cột.
5. Dải cam kết: chính hãng, báo giá nhanh, giao hàng, hỗ trợ.
6. Sản phẩm nổi bật dạng horizontal cards.
7. Khu vực “Giá tốt hôm nay”.
8. Banner báo giá dành cho nhà thầu/doanh nghiệp.
9. Thương hiệu nổi bật.
10. Sản phẩm đã xem.
11. Bottom navigation.

### Hero banner trang chủ

- Tỷ lệ 16:9 hoặc 1.55:1.
- Chữ tối đa 8–10 từ.
- Không đặt quá bốn icon trên banner.
- CTA ngắn: “Xem ngay”, “Nhận báo giá”, “Khám phá”.
- Slider tối đa 4 banner; tự chuyển sau 5 giây.

## 9. Danh mục

Danh mục cấp 1:

- Xi măng và phụ gia.
- Thép xây dựng.
- Gạch xây dựng.
- Cát, đá và vật liệu rời.
- Gạch ốp lát.
- Tôn và vật liệu mái.
- Sơn và chống thấm.
- Thiết bị điện nước.
- Thiết bị vệ sinh.
- Cửa và phụ kiện.

Danh sách dùng card ngang: ảnh bên trái 96–112 px, tên và số sản phẩm ở giữa, chevron bên phải. Bộ lọc dùng chip cuộn ngang.

## 10. Card sản phẩm

Mỗi card gồm:

- Ảnh chiếm 65–70% diện tích card.
- Badge: “Bán chạy”, “Giá tốt”, “Mới”, “Còn ít”.
- Tên sản phẩm tối đa hai dòng.
- Thông số ngắn: quy cách, khối lượng hoặc kích thước.
- Giá hiện tại; giá cũ và phần trăm giảm nếu có.
- Đơn vị tính rõ ràng: `/bao`, `/cây`, `/m²`, `/viên`, `/thùng`.
- Nút thêm nhanh hoặc biểu tượng giỏ hàng.

Không dùng ảnh có nền lộn xộn. Ảnh danh mục và ảnh sản phẩm phải cùng ánh sáng, cùng góc chụp và nền.

## 11. Chi tiết sản phẩm

1. Header trong suốt trên ảnh: quay lại, chia sẻ, yêu thích.
2. Gallery ảnh lớn; 4–6 ảnh phụ.
3. Tên, đánh giá, số lượng đã bán.
4. Giá và trạng thái kho.
5. Các chip dịch vụ: chính hãng, giao nhanh, hỗ trợ kỹ thuật.
6. Thuộc tính lựa chọn: quy cách, màu, kích thước, thương hiệu.
7. Thông tin kỹ thuật dạng accordion.
8. Mô tả và ứng dụng.
9. Chính sách giao hàng/đổi trả.
10. Sản phẩm liên quan.
11. Sticky CTA: **Thêm vào giỏ** và **Mua ngay**.

Đối với vật liệu nặng, hiển thị ghi chú: “Phí vận chuyển được xác nhận theo địa điểm và tải trọng”.

## 12. Giỏ hàng và thanh toán

### Giỏ hàng

- Nhóm sản phẩm theo nhà kho hoặc nhà cung cấp.
- Cho phép chọn/bỏ chọn từng sản phẩm.
- Chỉnh số lượng bằng stepper.
- Hiển thị khối lượng dự kiến và ghi chú giao hàng.
- Nút “Yêu cầu báo giá” cho đơn lớn.

### Checkout

- Thông tin người nhận.
- Địa chỉ và vị trí công trình.
- Loại phương tiện có thể tiếp cận.
- Thời gian mong muốn nhận hàng.
- Phương thức thanh toán.
- Mã khuyến mại.
- Ghi chú bốc xếp, nâng hạ.
- Tổng tạm tính, vận chuyển dự kiến, giảm giá, tổng thanh toán.

## 13. Đơn hàng

Trạng thái:

```text
Chờ xác nhận → Đã xác nhận → Đang chuẩn bị → Đang giao
→ Đã giao → Hoàn thành
```

Trạng thái ngoại lệ: chờ báo giá, cần bổ sung địa chỉ, giao không thành công, đã hủy.

Mỗi đơn hàng cần có nút gọi hỗ trợ, nhắn Zalo OA, mua lại và tải hóa đơn khi có.

## 14. Bottom navigation

Năm tab:

1. Trang chủ.
2. Danh mục.
3. Khuyến mại.
4. Đơn hàng.
5. Tài khoản.

Tab active dùng Primary 600; tab còn lại dùng màu xám. Nhãn luôn hiển thị, không chỉ dùng icon.

## 15. Thành phần dùng lại

- AppHeader.
- SearchBar.
- HeroBanner.
- CategoryGrid.
- ProductCard.
- ProductHorizontalCard.
- ServicePromise.
- PromotionBanner.
- FilterChip.
- QuantityStepper.
- PriceBlock.
- StockBadge.
- RatingSummary.
- BottomNavigation.
- StickyPurchaseBar.
- EmptyState.
- SkeletonLoader.
- ErrorState.

## 16. Quy chuẩn ảnh

### Ảnh hero

- 1440 × 2560 px.
- Công trình hiện đại, ánh sáng tự nhiên, tông xanh/xám.
- Chừa vùng tối hoặc khoảng trống để đặt logo và CTA.
- Không chứa logo thương hiệu giả hoặc chữ AI trong ảnh.

### Ảnh banner

- 1600 × 900 px.
- Chủ thể nằm bên phải, vùng đặt chữ bên trái hoặc ngược lại.
- Một banner chỉ truyền tải một thông điệp.

### Ảnh sản phẩm

- 1200 × 1200 px.
- Nền trắng ngà `#F7F8FA`.
- Góc chụp 3/4, camera ngang tầm sản phẩm.
- Ánh sáng studio mềm, bóng đổ tự nhiên.
- Không watermark.
- Sản phẩm chiếm 75–85% khung hình.
- Mỗi sản phẩm cần tối thiểu 4 ảnh: chính diện, góc 3/4, chi tiết bề mặt, ảnh ứng dụng.

## 17. Trạng thái hệ thống

- Loading: skeleton, không dùng spinner toàn màn hình lâu.
- Empty: icon đơn giản + hướng dẫn hành động.
- Error: thông báo rõ và nút thử lại.
- Offline: cho phép xem dữ liệu đã tải gần nhất, ghi rõ thời điểm cập nhật.
- Hết hàng: giữ trang sản phẩm, cho phép đăng ký nhận thông báo.

## 18. Accessibility

- Contrast chữ tối thiểu 4.5:1.
- Touch target tối thiểu 44 × 44 px.
- Không truyền đạt trạng thái chỉ bằng màu.
- Giá và đơn vị không tách rời khi xuống dòng.
- Alt text mô tả đúng loại vật liệu và quy cách.

## 19. Checklist bàn giao frontend

- Thiết kế từ chiều rộng 360–430 px.
- Safe area iOS/Android đầy đủ.
- Không để bottom CTA bị thanh điều hướng Zalo che.
- Ảnh dùng WebP/AVIF, có fallback.
- Lazy load danh sách ảnh.
- Banner có cấu hình từ backend.
- Danh mục, màu chủ đạo, thứ tự module và CTA được quản trị động.
- Theo dõi sự kiện: mở app, bấm hero, tìm kiếm, xem sản phẩm, thêm giỏ, checkout, đặt hàng, liên hệ báo giá.

## 20. Tệp liên quan

- [`PRODUCT-CATALOG-50.md`](./PRODUCT-CATALOG-50.md): danh sách 50 sản phẩm mẫu và quy chuẩn ảnh.
- [`IMAGE-PROMPTS.md`](./IMAGE-PROMPTS.md): prompt tạo hero, banner và ảnh sản phẩm đồng nhất.

---

Phiên bản: 1.0 — 06/08/2026
