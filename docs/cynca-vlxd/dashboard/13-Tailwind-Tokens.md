# Cynca VLXD Admin Dashboard — Tailwind Tokens Specification

> Phiên bản: 1.0  
> Phạm vi: Design tokens và cấu hình Tailwind cho Dashboard quản trị  
> Nền tảng mục tiêu: Next.js / React / Tailwind CSS  
> Tài liệu liên quan: toàn bộ thư mục `dashboard/`

---

## 1. Mục tiêu

Tài liệu này chuyển các quy chuẩn thiết kế của Cynca VLXD Dashboard thành hệ thống token có thể dùng trực tiếp trong frontend.

Mục tiêu:

- Không hardcode màu, spacing, radius và shadow tùy ý.
- Giữ giao diện đồng nhất giữa các module.
- Giúp Codex, Cursor và developer dựng UI đúng guideline.
- Dễ thay đổi nhận diện thương hiệu về sau.
- Hỗ trợ light mode mặc định và mở rộng dark mode nếu cần.

---

## 2. Nguyên tắc token

Token được chia thành 3 lớp:

1. **Primitive tokens** — giá trị nền tảng như blue-500, gray-100.
2. **Semantic tokens** — ý nghĩa như primary, surface, text-muted.
3. **Component tokens** — áp dụng cho button, card, sidebar, chart.

Frontend nên ưu tiên semantic token thay vì gọi trực tiếp primitive token.

Ví dụ tốt:

```tsx
className="bg-surface text-text-primary border-border-subtle"
```

Ví dụ không nên dùng:

```tsx
className="bg-white text-slate-900 border-slate-200"
```

---

## 3. Color primitives

```ts
const colors = {
  blue: {
    50: "#F4F7FF",
    100: "#EAF1FF",
    200: "#C9D7F8",
    300: "#9BB9F4",
    400: "#5F8EF1",
    500: "#0B57F0",
    600: "#0847C7",
    700: "#063CA8",
    800: "#08388F",
    900: "#0A2E73"
  },
  gray: {
    25: "#FCFCFD",
    50: "#F8FAFC",
    100: "#F2F4F7",
    200: "#E7ECF3",
    300: "#D0D5DD",
    400: "#98A2B3",
    500: "#667085",
    600: "#526071",
    700: "#344054",
    800: "#182230",
    900: "#101828"
  },
  green: {
    50: "#ECFDF3",
    500: "#12B76A",
    700: "#027A48"
  },
  amber: {
    50: "#FFFAEB",
    500: "#F79009",
    700: "#B54708"
  },
  red: {
    50: "#FEF3F2",
    500: "#F04438",
    700: "#B42318"
  },
  violet: {
    50: "#F4F3FF",
    500: "#7A5AF8",
    700: "#5925DC"
  }
};
```

---

## 4. Semantic color tokens

```ts
const semanticColors = {
  brand: {
    DEFAULT: "#0B57F0",
    hover: "#0847C7",
    active: "#063CA8",
    soft: "#EAF1FF",
    subtle: "#F4F7FF"
  },
  background: {
    page: "#F6F8FC",
    surface: "#FFFFFF",
    muted: "#F1F4F9",
    elevated: "#FFFFFF"
  },
  text: {
    primary: "#182230",
    secondary: "#526071",
    muted: "#667085",
    inverse: "#FFFFFF",
    link: "#0B57F0"
  },
  border: {
    subtle: "#E7ECF3",
    DEFAULT: "#DDE3EC",
    strong: "#C9D1DD",
    focus: "#0B57F0"
  },
  success: {
    DEFAULT: "#12B76A",
    soft: "#ECFDF3",
    text: "#027A48"
  },
  warning: {
    DEFAULT: "#F79009",
    soft: "#FFFAEB",
    text: "#B54708"
  },
  danger: {
    DEFAULT: "#F04438",
    soft: "#FEF3F2",
    text: "#B42318"
  },
  info: {
    DEFAULT: "#2E90FA",
    soft: "#EFF8FF",
    text: "#175CD3"
  }
};
```

---

## 5. Typography tokens

Font stack:

```ts
fontFamily: {
  sans: ["Inter", "system-ui", "-apple-system", "BlinkMacSystemFont", "Segoe UI", "sans-serif"],
  mono: ["ui-monospace", "SFMono-Regular", "Menlo", "monospace"]
}
```

Font sizes:

```ts
fontSize: {
  xs: ["12px", { lineHeight: "18px" }],
  sm: ["13px", { lineHeight: "20px" }],
  base: ["14px", { lineHeight: "22px" }],
  md: ["16px", { lineHeight: "24px" }],
  lg: ["18px", { lineHeight: "26px" }],
  xl: ["20px", { lineHeight: "28px" }],
  "2xl": ["24px", { lineHeight: "32px" }],
  "3xl": ["30px", { lineHeight: "38px", letterSpacing: "-0.02em" }],
  "4xl": ["36px", { lineHeight: "42px", letterSpacing: "-0.02em" }]
}
```

Font weight:

```ts
fontWeight: {
  normal: "400",
  medium: "500",
  semibold: "600",
  bold: "700"
}
```

---

## 6. Spacing tokens

Tailwind mặc định dùng hệ 4px, phù hợp 8pt Grid. Bổ sung các token có tên semantic:

```ts
spacing: {
  "page-x": "32px",
  "page-x-laptop": "24px",
  "page-x-mobile": "16px",
  "section-gap": "32px",
  "widget-gap": "24px",
  "card-padding": "24px",
  "card-padding-compact": "20px",
  "header-height": "72px",
  "sidebar-expanded": "264px",
  "sidebar-collapsed": "80px"
}
```

Quy tắc:

- Gap card: `gap-widget-gap`.
- Padding card: `p-card-padding`.
- Khoảng cách section: `space-y-section-gap`.
- Không tự thêm giá trị như `17px`, `23px`, `29px`.

---

## 7. Radius tokens

```ts
borderRadius: {
  sm: "8px",
  md: "12px",
  lg: "16px",
  xl: "20px",
  "2xl": "24px",
  full: "9999px"
}
```

Mapping:

| Component | Utility |
|---|---|
| Input | `rounded-md` |
| Button | `rounded-md` |
| Icon container | `rounded-md` hoặc `rounded-lg` |
| Card | `rounded-xl` |
| Modal | `rounded-2xl` |
| Badge | `rounded-full` |

---

## 8. Shadow tokens

```ts
boxShadow: {
  xs: "0 1px 2px rgba(16,24,40,0.04)",
  sm: "0 4px 12px rgba(16,24,40,0.05)",
  md: "0 8px 24px rgba(16,24,40,0.07)",
  popover: "0 12px 32px rgba(16,24,40,0.14)",
  dropdown: "0 16px 40px rgba(16,24,40,0.16)"
}
```

Không dùng shadow mặc định quá đậm của trình duyệt hoặc plugin ngoài.

---

## 9. Breakpoint tokens

```ts
screens: {
  sm: "480px",
  md: "768px",
  lg: "1024px",
  xl: "1280px",
  "2xl": "1440px",
  wide: "1920px"
}
```

Mapping:

- `< lg`: drawer mode.
- `lg–xl`: sidebar collapsed.
- `xl+`: sidebar expanded.
- `2xl+`: layout desktop chuẩn.

---

## 10. Z-index tokens

```ts
zIndex: {
  base: "0",
  card: "5",
  sticky: "30",
  dropdown: "50",
  backdrop: "80",
  modal: "90",
  toast: "100"
}
```

Không dùng `z-[9999]` trừ trường hợp tích hợp bên thứ ba bất khả kháng.

---

## 11. Motion tokens

```ts
transitionDuration: {
  instant: "80ms",
  fast: "120ms",
  normal: "180ms",
  slow: "260ms",
  emphasis: "420ms"
},
transitionTimingFunction: {
  standard: "cubic-bezier(0.2, 0, 0, 1)",
  enter: "cubic-bezier(0, 0, 0, 1)",
  exit: "cubic-bezier(0.4, 0, 1, 1)",
  emphasis: "cubic-bezier(0.2, 0.8, 0.2, 1)"
}
```

Ví dụ:

```tsx
className="transition-all duration-fast ease-standard"
```

Không dùng `transition-all` cho component phức tạp nếu có thể chỉ định chính xác thuộc tính.

---

## 12. Tailwind config tham chiếu

```ts
import type { Config } from "tailwindcss";

const config: Config = {
  content: [
    "./app/**/*.{ts,tsx}",
    "./pages/**/*.{ts,tsx}",
    "./components/**/*.{ts,tsx}",
    "./src/**/*.{ts,tsx}"
  ],
  theme: {
    extend: {
      colors: {
        brand: {
          DEFAULT: "#0B57F0",
          hover: "#0847C7",
          active: "#063CA8",
          soft: "#EAF1FF",
          subtle: "#F4F7FF"
        },
        background: {
          page: "#F6F8FC",
          surface: "#FFFFFF",
          muted: "#F1F4F9"
        },
        text: {
          primary: "#182230",
          secondary: "#526071",
          muted: "#667085",
          inverse: "#FFFFFF"
        },
        border: {
          subtle: "#E7ECF3",
          DEFAULT: "#DDE3EC",
          strong: "#C9D1DD"
        },
        success: {
          DEFAULT: "#12B76A",
          soft: "#ECFDF3",
          text: "#027A48"
        },
        warning: {
          DEFAULT: "#F79009",
          soft: "#FFFAEB",
          text: "#B54708"
        },
        danger: {
          DEFAULT: "#F04438",
          soft: "#FEF3F2",
          text: "#B42318"
        }
      },
      fontFamily: {
        sans: ["Inter", "system-ui", "sans-serif"],
        mono: ["ui-monospace", "SFMono-Regular", "Menlo", "monospace"]
      },
      spacing: {
        "page-x": "32px",
        "section-gap": "32px",
        "widget-gap": "24px",
        "card-padding": "24px",
        "header-height": "72px",
        "sidebar-expanded": "264px",
        "sidebar-collapsed": "80px"
      },
      borderRadius: {
        md: "12px",
        lg: "16px",
        xl: "20px",
        "2xl": "24px"
      },
      boxShadow: {
        xs: "0 1px 2px rgba(16,24,40,0.04)",
        sm: "0 4px 12px rgba(16,24,40,0.05)",
        md: "0 8px 24px rgba(16,24,40,0.07)",
        popover: "0 12px 32px rgba(16,24,40,0.14)"
      },
      screens: {
        wide: "1920px"
      },
      zIndex: {
        sticky: "30",
        dropdown: "50",
        backdrop: "80",
        modal: "90",
        toast: "100"
      }
    }
  },
  plugins: []
};

export default config;
```

---

## 13. CSS variables khuyến nghị

Để dễ đổi theme, semantic token nên đặt qua CSS variables.

```css
:root {
  --color-brand: 11 87 240;
  --color-brand-hover: 8 71 199;
  --color-page: 246 248 252;
  --color-surface: 255 255 255;
  --color-text-primary: 24 34 48;
  --color-text-secondary: 82 96 113;
  --color-border: 221 227 236;
}
```

Tailwind mapping:

```ts
colors: {
  brand: "rgb(var(--color-brand) / <alpha-value>)",
  surface: "rgb(var(--color-surface) / <alpha-value>)",
  "text-primary": "rgb(var(--color-text-primary) / <alpha-value>)"
}
```

---

## 14. Base layer

```css
@layer base {
  html {
    font-family: theme("fontFamily.sans");
    color: theme("colors.text.primary");
    background: theme("colors.background.page");
  }

  body {
    min-height: 100vh;
    background: theme("colors.background.page");
  }

  :focus-visible {
    outline: 3px solid rgba(11,87,240,0.28);
    outline-offset: 2px;
  }
}
```

---

## 15. Component classes tham chiếu

### Dashboard card

```css
@layer components {
  .dashboard-card {
    @apply min-w-0 rounded-xl border border-border-subtle bg-background-surface shadow-xs;
  }

  .dashboard-card-body {
    @apply p-card-padding;
  }
}
```

### Primary button

```css
.btn-primary {
  @apply inline-flex h-10 items-center justify-center gap-2 rounded-md bg-brand px-4 text-sm font-semibold text-white transition-colors duration-fast;
}

.btn-primary:hover {
  @apply bg-brand-hover;
}
```

### Secondary button

```css
.btn-secondary {
  @apply inline-flex h-10 items-center justify-center gap-2 rounded-md border border-border bg-white px-4 text-sm font-semibold text-text-secondary transition-colors duration-fast;
}
```

---

## 16. Badge variants

```ts
const badgeVariants = {
  success: "bg-success-soft text-success-text",
  warning: "bg-warning-soft text-warning-text",
  danger: "bg-danger-soft text-danger-text",
  info: "bg-blue-50 text-blue-700",
  neutral: "bg-gray-100 text-gray-700"
};
```

Base class:

```text
inline-flex h-6 items-center rounded-full px-2.5 text-xs font-semibold
```

---

## 17. Status mapping

```ts
export const orderStatusClasses = {
  pending: "bg-warning-soft text-warning-text",
  confirmed: "bg-blue-50 text-blue-700",
  processing: "bg-violet-50 text-violet-700",
  shipping: "bg-indigo-50 text-indigo-700",
  completed: "bg-success-soft text-success-text",
  cancelled: "bg-danger-soft text-danger-text",
  returned: "bg-orange-50 text-orange-700"
};
```

Status mapping phải tập trung trong một file, không lặp ở từng component.

---

## 18. Layout utilities

### Dashboard shell

```tsx
<div className="grid min-h-screen grid-cols-[264px_minmax(0,1fr)] bg-background-page xl:grid-cols-[240px_minmax(0,1fr)] lg:grid-cols-[80px_minmax(0,1fr)]">
```

Nên cân nhắc class helper hoặc CSS variable thay vì chuỗi class quá dài.

### Content container

```tsx
<main className="mx-auto w-full max-w-[1600px] px-page-x pb-12 pt-7 xl:px-6 lg:px-6 md:px-5 sm:px-4">
```

### Grid 12 cột

```tsx
<div className="grid grid-cols-12 gap-widget-gap xl:gap-5">
```

---

## 19. KPI card recipe

```tsx
<div className="dashboard-card min-h-[156px] p-6">
  <div className="flex items-start justify-between gap-4">
    <span className="text-sm font-medium text-text-muted">Doanh thu thuần</span>
    <div className="grid size-11 place-items-center rounded-lg bg-brand-soft text-brand">
      {/* icon */}
    </div>
  </div>
  <div className="mt-[18px] text-[34px] font-bold leading-[1.15] tracking-[-0.02em] text-text-primary">
    1,28 tỷ ₫
  </div>
</div>
```

---

## 20. Chart card recipe

```tsx
<section className="dashboard-card min-h-[380px] p-6">
  <header className="flex items-start justify-between gap-4">
    <div>
      <h2 className="text-lg font-semibold text-text-primary">Doanh thu</h2>
      <p className="mt-1 text-sm text-text-muted">Theo 12 tháng gần nhất</p>
    </div>
  </header>
  <div className="mt-6 h-[300px]">...</div>
</section>
```

---

## 21. Table recipe

```tsx
<div className="dashboard-card overflow-hidden">
  <div className="overflow-x-auto">
    <table className="w-full min-w-[920px] border-separate border-spacing-0">
      <thead className="bg-gray-50 text-xs font-semibold text-text-muted">
        ...
      </thead>
      <tbody className="divide-y divide-border-subtle">
        ...
      </tbody>
    </table>
  </div>
</div>
```

---

## 22. Dark mode — tùy chọn

Dark mode chưa phải yêu cầu bắt buộc. Nếu triển khai, dùng CSS variables thay vì viết lại toàn bộ class.

```css
.dark {
  --color-page: 16 24 40;
  --color-surface: 24 34 48;
  --color-text-primary: 248 250 252;
  --color-text-secondary: 208 213 221;
  --color-border: 71 84 103;
}
```

Không triển khai dark mode nửa vời chỉ cho một số card.

---

## 23. Class composition

Khuyến nghị dùng:

- `clsx`.
- `tailwind-merge`.
- `class-variance-authority`.

Ví dụ:

```ts
import { cva } from "class-variance-authority";

export const buttonVariants = cva(
  "inline-flex h-10 items-center justify-center rounded-md px-4 text-sm font-semibold transition-colors duration-fast focus-visible:outline-none focus-visible:ring-4 focus-visible:ring-brand/20",
  {
    variants: {
      variant: {
        primary: "bg-brand text-white hover:bg-brand-hover",
        secondary: "border border-border bg-white text-text-secondary hover:bg-gray-50",
        danger: "bg-danger text-white hover:bg-danger-text"
      }
    },
    defaultVariants: {
      variant: "primary"
    }
  }
);
```

---

## 24. Arbitrary values

Arbitrary values chỉ dùng khi token chưa có và giá trị thật sự đặc thù.

Được phép:

```text
min-h-[380px]
max-w-[1600px]
grid-cols-[264px_minmax(0,1fr)]
```

Không nên:

```text
mt-[17px]
rounded-[19px]
text-[#172132]
```

Nếu một arbitrary value lặp lại từ 3 lần trở lên, phải chuyển thành token.

---

## 25. Safelist

Không tạo class động bằng nối chuỗi như:

```ts
`bg-${status}-50`
```

Dùng mapping tĩnh để Tailwind có thể tree-shake đúng.

Nếu bắt buộc dùng class động, khai báo safelist rõ ràng.

---

## 26. Plugin policy

Chỉ thêm plugin khi có giá trị rõ ràng.

Có thể dùng:

- `@tailwindcss/forms`.
- `@tailwindcss/typography` cho tài liệu dài, không dùng cho Dashboard core.
- Plugin custom cho scrollbar nếu cần.

Không thêm nhiều UI plugin trùng chức năng.

---

## 27. Token governance

Mọi token mới cần trả lời:

- Có lặp lại không?
- Có semantic rõ ràng không?
- Có thuộc primitive, semantic hay component token?
- Có ảnh hưởng accessibility không?
- Có thay thế token hiện có được không?

Không thêm token chỉ để sửa một component riêng lẻ nếu có thể dùng token hiện tại.

---

## 28. File structure đề xuất

```text
src/
├── styles/
│   ├── globals.css
│   ├── tokens.css
│   └── components.css
├── lib/
│   ├── cn.ts
│   └── status-classes.ts
├── components/
│   └── ui/
│       ├── button.tsx
│       ├── badge.tsx
│       ├── card.tsx
│       └── input.tsx
└── tailwind.config.ts
```

---

## 29. Acceptance checklist

- [ ] Màu dùng semantic token.
- [ ] Không hardcode màu lặp trong component.
- [ ] Typography dùng đúng scale.
- [ ] Spacing tuân thủ hệ 4px/8pt.
- [ ] Card dùng radius và shadow chuẩn.
- [ ] Breakpoint khớp tài liệu responsive.
- [ ] Z-index dùng token chung.
- [ ] Motion duration và easing nhất quán.
- [ ] Status class được map tập trung.
- [ ] Arbitrary value lặp lại được chuyển thành token.
- [ ] Dark mode, nếu có, dùng CSS variables.
- [ ] Component variants dùng cva hoặc mapping tương đương.
- [ ] Tailwind purge không làm mất class động.
- [ ] Focus ring đạt accessibility.
- [ ] Không có class chain dài khó bảo trì nếu có thể tách component.

---

## 30. Những điều không được làm

- Không dùng trực tiếp màu hex trong JSX lặp lại.
- Không dùng nhiều hệ spacing khác nhau.
- Không tạo radius mới tùy ý.
- Không dùng `z-[9999]` tùy tiện.
- Không nối chuỗi class động không safelist.
- Không lạm dụng arbitrary values.
- Không trộn Tailwind và inline style thiếu quy tắc.
- Không dùng primitive token khi semantic token phù hợp hơn.
- Không thêm plugin chỉ vì tiện cho một component.

---

## 31. Kết luận

Hệ thống Tailwind token của Cynca VLXD phải là cầu nối trực tiếp giữa guideline thiết kế và mã nguồn frontend. Việc sử dụng token thống nhất giúp giao diện dễ mở rộng, dễ bảo trì và giữ đúng nhận diện khi nhiều developer hoặc AI cùng tham gia triển khai.

File tiếp theo:

```text
14-Implementation-Checklist.md
```