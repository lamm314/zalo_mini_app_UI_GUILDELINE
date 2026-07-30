# Shared Foundations

## 1. Mục tiêu nền tảng

Phần này áp dụng cho mọi Zalo Mini App thương mại điện tử trong repository. Guideline ngành chỉ được phép mở rộng hoặc điều chỉnh có kiểm soát, không phá vỡ các nguyên tắc cốt lõi.

## 2. Design principles

- **Content first:** ưu tiên hình ảnh, tên sản phẩm, giá và hành động chính.
- **One-hand friendly:** CTA chính nằm trong vùng dễ chạm.
- **Fast to understand:** người dùng phải hiểu mục đích màn hình trong 3 giây.
- **Backend-driven:** banner, section, menu, theme và nội dung đến từ API.
- **Consistent states:** mọi component có default, pressed, disabled, loading và error.
- **Trustworthy commerce:** giá, khuyến mại, tồn kho, đổi trả và trạng thái đơn phải rõ ràng.

## 3. Layout system

Dùng lưới 4pt, ưu tiên bội số 8.

| Token | Giá trị |
|---|---:|
| `space-1` | 4px |
| `space-2` | 8px |
| `space-3` | 12px |
| `space-4` | 16px |
| `space-5` | 20px |
| `space-6` | 24px |
| `space-8` | 32px |
| `space-10` | 40px |

Padding ngang mặc định: **16px**. Màn hình rộng có thể tăng lên 20–24px. Không để nội dung quan trọng sát mép dưới 12px.

## 4. Radius

| Token | Giá trị | Dùng cho |
|---|---:|---|
| `radius-sm` | 8px | chip, badge nhỏ |
| `radius-md` | 12px | input, button phụ |
| `radius-lg` | 16px | product card, modal nhỏ |
| `radius-xl` | 24px | hero card, bottom sheet |
| `radius-pill` | 999px | filter chip, avatar |

## 5. Typography

Font mặc định dùng system sans-serif để tải nhanh và hiển thị ổn định.

| Style | Size | Weight | Dùng cho |
|---|---:|---:|---|
| Display | 32–36 | 600 | hero title |
| H1 | 28 | 600 | tiêu đề màn hình |
| H2 | 22–24 | 600 | tiêu đề section |
| H3 | 18–20 | 600 | tên nhóm |
| Body | 15–16 | 400 | nội dung chính |
| Label | 14 | 500 | button, tab |
| Caption | 12–13 | 400 | mô tả phụ |

Không dùng quá ba cấp độ chữ trên cùng một card.

## 6. Color tokens

Mỗi ngành phải map về token thay vì dùng màu trực tiếp.

```json
{
  "color": {
    "background": "#FFFFFF",
    "surface": "#F7F7F8",
    "surfaceElevated": "#FFFFFF",
    "textPrimary": "#171717",
    "textSecondary": "#6B6B70",
    "border": "#E6E6E8",
    "primary": "#D33D5C",
    "primaryPressed": "#B92E4A",
    "primarySoft": "#FBE9EE",
    "success": "#198754",
    "warning": "#B7791F",
    "danger": "#C62828"
  }
}
```

Màu chữ chính phải đủ tương phản. Không dùng màu nhạt cho thông tin giá, tồn kho hoặc CTA quan trọng.

## 7. Navigation

### Top bar

Có thể gồm back, title, search, share, cart hoặc profile. Không đặt quá ba hành động bên phải.

### Bottom navigation

Tối đa 5 mục. Mỗi mục có icon và label. Mục active phải rõ bằng màu, không chỉ dựa vào thay đổi icon.

Đề xuất mặc định:

- Home
- Category
- Explore/Scan
- Orders
- Profile

## 8. Touch targets

- Tối thiểu 44 × 44px.
- Khoảng cách giữa hai icon hành động tối thiểu 8px.
- Button chính cao 48–52px.
- Không đặt nút xóa nhỏ sát nút tăng/giảm số lượng.

## 9. Component states

Mọi component tương tác phải có:

- Default
- Hover nếu chạy web preview
- Pressed
- Focus
- Disabled
- Loading
- Error
- Success khi phù hợp

## 10. Loading và lỗi

### Skeleton

Dùng skeleton theo đúng cấu trúc nội dung thật. Không dùng spinner toàn màn hình nếu có thể hiển thị skeleton.

### Empty state

Gồm icon hoặc minh họa, tiêu đề, mô tả ngắn và một CTA rõ ràng.

### Error state

Nêu vấn đề bằng ngôn ngữ dễ hiểu và có nút `Thử lại`. Không hiển thị raw API error cho người dùng.

## 11. Sticky CTA

- Dùng cho Add to cart, Buy now, Checkout, Place order.
- Có nền riêng để tách khỏi nội dung cuộn.
- Tính safe area phía dưới.
- Khi bàn phím mở, tránh che input hoặc tổng tiền.

## 12. Backend-driven UI

Trang chủ nên được dựng từ danh sách section:

```json
{
  "sections": [
    { "type": "hero", "id": "hero-1", "order": 1 },
    { "type": "category_grid", "id": "cat-1", "order": 2 },
    { "type": "product_carousel", "id": "best-seller", "order": 3 },
    { "type": "voucher_strip", "id": "voucher-1", "order": 4 }
  ]
}
```

Frontend phải render theo `type`, `order`, `visibility`, `theme` và dữ liệu API. Không cố định thứ tự section trong code.

## 13. Accessibility

- Không truyền đạt trạng thái chỉ bằng màu.
- Icon quan trọng có label hoặc accessible name.
- Hỗ trợ phóng to chữ hợp lý.
- Không dùng text dưới 12px.
- Hình ảnh sản phẩm cần alt text mô tả ngắn.

## 14. Motion

- Duration: 180–300ms.
- Easing tự nhiên, không bounce quá mạnh.
- Dùng fade, slide và scale nhẹ.
- Không làm animation cản thao tác mua hàng.

## 15. Developer handoff

Mỗi component cần ghi rõ:

- Props
- Variant
- State
- Token sử dụng
- Kích thước
- Khoảng cách
- Hành vi khi loading/error
- Mapping API
- Quy tắc responsive

## 16. Definition of done

Một màn hình chỉ được xem là hoàn thành khi:

- Đúng token và spacing.
- Không tràn ở chiều rộng 320px.
- Có loading, empty, error.
- CTA không bị che.
- Dữ liệu có thể thay bằng API.
- Không hardcode nội dung ngành trong component dùng chung.