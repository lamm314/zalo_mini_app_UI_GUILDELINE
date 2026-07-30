# Beauty Zalo Mini App UI Guideline

## 1. Phạm vi

Guideline này dành cho Zalo Mini App bán:

- Skincare
- Makeup
- Nước hoa
- Chăm sóc tóc
- Chăm sóc cơ thể
- Beauty devices
- Spa hoặc dịch vụ làm đẹp có bán sản phẩm

Mục tiêu là tạo giao diện cao cấp, mềm mại, hiện đại, giàu hình ảnh nhưng vẫn tối ưu chuyển đổi mua hàng.

## 2. Visual direction

Phong cách đề xuất:

- Premium minimal
- Editorial beauty
- Soft luxury
- Clean clinical khi cần thể hiện thành phần hoặc hiệu quả
- Khoảng trắng rộng
- Hình ảnh sản phẩm lớn
- Card bo góc mềm
- Shadow rất nhẹ

Không nên:

- Dùng quá nhiều màu nổi trong một màn hình
- Dùng gradient mạnh ở mọi section
- Đặt quá nhiều badge sale
- Làm giao diện giống website desktop thu nhỏ
- Sao chép Dynamic Island hoặc chrome iOS

## 3. Beauty color system

### Palette mặc định

```json
{
  "primary": "#D33D5C",
  "primaryPressed": "#B92E4A",
  "primarySoft": "#FBE9EE",
  "background": "#FFFDFC",
  "surface": "#F8F5F4",
  "surfacePink": "#FFF1F4",
  "textPrimary": "#1D1A1B",
  "textSecondary": "#746C70",
  "border": "#EAE3E5",
  "success": "#2E7D5B",
  "warning": "#A86A1B",
  "danger": "#C62828"
}
```

Có thể thay palette theo thương hiệu, nhưng vẫn phải map về token chung.

## 4. Typography

- Sans-serif cho toàn bộ UI và dữ liệu thương mại.
- Có thể dùng serif hoặc script font cho một phần hero title, nhưng không dùng cho giá, thành phần, hướng dẫn hoặc CTA.
- Tên sản phẩm: 16–18px, tối đa 2 dòng trên card.
- Giá hiện tại: 18–22px, weight 600.
- Giá cũ: 13–15px, gạch ngang.

## 5. Information architecture

### Bottom navigation đề xuất

1. Trang chủ
2. Danh mục
3. Soi da / Thử makeup
4. Đơn hàng
5. Cá nhân

Tab AI chỉ hiển thị khi Mini App thật sự có tính năng này.

### Nhóm danh mục phổ biến

- Best Sellers
- Cleanser
- Toner
- Serum
- Moisturizer
- Sunscreen
- Makeup
- Hair Care
- Body Care
- Fragrance
- Beauty Tools

## 6. Home screen

Thứ tự section khuyến nghị:

1. Header với logo, search, cart, profile
2. Hero banner
3. Quick category
4. Beauty concern
5. Best sellers
6. New arrivals
7. Routine builder
8. Voucher
9. Brand story
10. Reviews hoặc social proof

### Hero banner

- Tỷ lệ gợi ý 4:5 hoặc 3:4 trên mobile.
- Ảnh lifestyle hoặc product composition.
- Overlay gradient để chữ dễ đọc.
- Chỉ một CTA chính.
- Nội dung tối đa: eyebrow, headline, subheadline, CTA.

### Beauty concern section

Cho phép duyệt sản phẩm theo nhu cầu:

- Mụn
- Khô
- Nhạy cảm
- Thâm nám
- Lão hóa
- Lỗ chân lông
- Da xỉn màu

Mỗi concern dùng icon hoặc ảnh đơn giản, không dùng hình minh họa gây khó chịu.

## 7. Product card

### Nội dung bắt buộc

- Ảnh sản phẩm
- Tên
- Giá
- Giá cũ nếu có
- Rating và số đánh giá khi dữ liệu đủ tin cậy
- Badge sale hoặc new
- Nút yêu thích

### Nội dung tùy chọn

- Loại da
- Công dụng chính
- Dung tích
- Shade count
- Quick add

### Quy tắc

- Ảnh chiếm khoảng 60–70% diện tích card.
- Không hiển thị quá 2 badge.
- Tên tối đa 2 dòng.
- Không làm card quá cao vì mô tả dài.
- Dùng background trung tính để sản phẩm nổi bật.

## 8. Product listing

Header gồm:

- Tên danh mục
- Số lượng sản phẩm
- Filter
- Sort
- Chuyển grid 2 cột hoặc list nếu cần

Filter beauty thường gồm:

- Loại sản phẩm
- Loại da
- Vấn đề da
- Thành phần
- Thương hiệu
- Khoảng giá
- Rating
- Dung tích
- Không cồn / không hương liệu / vegan nếu có dữ liệu xác minh

Filter mở bằng bottom sheet, có nút `Áp dụng` và `Xóa bộ lọc`.

## 9. Product detail

### Cấu trúc

1. Gallery
2. Tên sản phẩm
3. Rating
4. Giá và khuyến mại
5. Variant: shade, size, volume
6. Tóm tắt công dụng
7. Chọn số lượng
8. Sticky CTA
9. Tabs hoặc accordion
10. Sản phẩm liên quan

### Tabs đề xuất

- Tổng quan
- Thành phần
- Cách dùng
- Đánh giá
- Chính sách

### Thành phần

- Hiển thị key ingredients trước.
- Full INCI list nằm trong accordion.
- Không dùng các claim y tế nếu dữ liệu không được xác minh.
- Có cảnh báo dị ứng hoặc patch test khi phù hợp.

### Shade selector

- Swatch tối thiểu 32px.
- Swatch được chọn có border rõ.
- Hiển thị tên shade bằng text.
- Không chỉ dùng màu để phân biệt.

## 10. Routine builder

Luồng đề xuất:

1. Chọn loại da
2. Chọn vấn đề chính
3. Chọn mức độ nhạy cảm
4. Chọn ngân sách
5. Nhận routine sáng/tối

Kết quả phải gồm:

- Thứ tự sử dụng
- Tần suất
- Hướng dẫn ngắn
- Tổng giá
- Thêm toàn bộ vào giỏ

## 11. AI skin scan

Chỉ thiết kế khi backend và quyền sử dụng hình ảnh đã rõ.

Màn hình cần:

- Giải thích dữ liệu được dùng thế nào
- Consent rõ ràng
- Hướng dẫn ánh sáng, khoảng cách, góc mặt
- Trạng thái scanning
- Kết quả theo nhóm vấn đề
- Mức độ tin cậy hoặc lưu ý kết quả tham khảo
- CTA xem routine hoặc sản phẩm phù hợp

Không nên hiển thị kết luận như chẩn đoán y khoa.

## 12. Virtual try-on

Dùng cho son, má hồng, eye makeup hoặc hair color.

UI gồm:

- Camera hoặc ảnh tải lên
- Danh sách shade
- Tên shade
- Mức độ filter
- Ảnh trước/sau
- Add to cart
- Disclaimer kết quả có thể khác tùy ánh sáng và thiết bị

## 13. Cart

Mỗi item gồm:

- Thumbnail
- Tên
- Variant
- Giá
- Quantity stepper
- Xóa hoặc lưu sau

Cuối màn hình:

- Voucher
- Tạm tính
- Giảm giá
- Phí vận chuyển
- Tổng cộng
- Sticky Checkout

Có cảnh báo khi shade hoặc size hết hàng.

## 14. Checkout

Các block:

- Người nhận
- Địa chỉ
- Phương thức giao hàng
- Voucher
- Phương thức thanh toán
- Ghi chú
- Tóm tắt đơn hàng
- Điều khoản

Không để nút `Đặt hàng` active khi chưa chọn đủ dữ liệu bắt buộc.

## 15. Order success

Hiển thị:

- Icon thành công
- Mã đơn
- Số tiền
- Phương thức thanh toán
- Thời gian dự kiến
- Theo dõi đơn
- Tiếp tục mua sắm

Sau 7 ngày kể từ khi giao thành công có thể kích hoạt luồng yêu cầu đánh giá.

## 16. Review

- Rating tổng
- Phân bổ sao
- Filter theo loại da hoặc shade
- Ảnh từ người mua
- Tag `Đã mua hàng`
- Report review

Form đánh giá gồm rating, nội dung, ảnh, loại da và shade đã mua nếu phù hợp.

## 17. Loyalty

Màn hình thành viên gồm:

- Hạng thành viên
- Điểm hiện tại
- Tiến độ lên hạng
- Voucher
- Lịch sử điểm
- Quyền lợi

Không làm gamification quá phức tạp.

## 18. Motion

- Product card press: scale 0.98.
- Add to cart: feedback ngắn 200–250ms.
- Shade change: crossfade ảnh.
- Bottom sheet: slide up 240–300ms.
- Success: animation một lần, không lặp vô hạn.

## 19. Beauty API mapping

Ví dụ product:

```json
{
  "id": "prd_001",
  "name": "Hydrating Barrier Serum",
  "category": "serum",
  "price": 389000,
  "compareAtPrice": 429000,
  "currency": "VND",
  "rating": 4.8,
  "reviewCount": 1850,
  "skinTypes": ["dry", "sensitive"],
  "concerns": ["dehydration", "barrier"],
  "keyIngredients": ["Ceramide", "Panthenol"],
  "variants": [
    { "id": "50ml", "label": "50 ml", "stock": 12 }
  ]
}
```

## 20. Beauty design checklist

- [ ] Hero rõ CTA và không che mặt người mẫu.
- [ ] Product card ưu tiên ảnh và giá.
- [ ] Shade có tên text.
- [ ] Thành phần hiển thị rõ, không gây hiểu nhầm.
- [ ] Có trạng thái hết hàng.
- [ ] Sticky CTA không bị che bởi Zalo safe area.
- [ ] AI feature có consent và disclaimer.
- [ ] Luồng checkout không quá dài.
- [ ] Nội dung có thể cấu hình từ backend.
- [ ] Màn hình hoạt động tốt từ 320px.

## 21. Prompt cho AI coding agent

```text
Build a premium beauty ecommerce Zalo Mini App using the shared design foundations and this beauty guideline. Use backend-driven sections, reusable components, soft luxury styling, large product photography, clear pricing, accessible shade selectors, sticky commerce actions, complete loading and error states, and Zalo-safe mobile spacing. Do not copy native iOS chrome. Do not hardcode banners, categories, products, theme colors, or section order. Implement and test the complete customer flow from home to product detail, cart, checkout, order success, order tracking, review, and repurchase.
```