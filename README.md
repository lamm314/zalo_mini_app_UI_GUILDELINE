# Zalo Mini App UI Guideline

Bộ quy chuẩn thiết kế giao diện cho hệ thống Zalo Mini App đa ngành.

## Phạm vi hiện tại

- [Beauty / Mỹ phẩm](docs/beauty/README.md)
- [Fashion / Quần áo](docs/fashion/README.md)
- [Nền tảng thiết kế dùng chung](docs/shared/FOUNDATIONS.md)

## Mục tiêu

Repository này là nguồn chuẩn để Designer, Product Owner, Frontend Developer và AI coding agent cùng triển khai giao diện nhất quán. Mỗi ngành có thể thay đổi màu sắc, nội dung, mô hình danh mục và các luồng nghiệp vụ đặc thù, nhưng vẫn dùng chung nền tảng về khoảng cách, khả năng chạm, trạng thái giao diện, accessibility và cấu trúc dữ liệu động.

## Nguyên tắc sử dụng

1. Không sao chép nguyên giao diện iOS hoặc Android; giao diện phải phù hợp môi trường Zalo Mini App.
2. Không hardcode nội dung trang chủ, banner, danh mục, màu chủ đạo hoặc thứ tự section.
3. Mỗi màn hình phải có trạng thái loading, empty, error và retry.
4. CTA chính phải dễ chạm bằng một tay và không bị che bởi thanh điều hướng hoặc safe area.
5. Mọi thành phần mới phải ưu tiên tái sử dụng component và design token hiện có.

## Cấu trúc đề xuất

```text
docs/
├── shared/
│   └── FOUNDATIONS.md
├── beauty/
│   └── README.md
└── fashion/
    └── README.md
```

## Trạng thái

Phiên bản hiện tại là **v1.0**, tập trung vào hai ngành đầu tiên: Mỹ phẩm và Quần áo.