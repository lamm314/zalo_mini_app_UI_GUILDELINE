# Shared Design Foundations

These foundations apply to every commerce-oriented Zalo Mini App in this repository. Industry guidelines may extend them, but must not contradict the core interaction, accessibility, performance, and backend-driven architecture rules defined here.

## 1. Design Principles

- **Content first:** prioritize product imagery, product name, price, availability, and the primary action.
- **One-hand friendly:** place essential actions inside comfortable thumb-reach zones.
- **Fast to understand:** users should understand the purpose of a screen within three seconds.
- **Backend driven:** banners, sections, navigation, themes, feature flags, and merchandising data must come from APIs.
- **State complete:** every interactive component must define default, pressed, focus, disabled, loading, error, and success states where relevant.
- **Trustworthy commerce:** pricing, discounts, stock, shipping, returns, and order status must be explicit.
- **Progressive disclosure:** show essential information first and move secondary detail into accordions, sheets, or expandable areas.
- **Mobile native, not mobile copied:** use patterns suitable for Zalo Mini Apps instead of imitating iOS or Android chrome.

## 2. Layout System

Use a 4-point base grid, with 8-point increments preferred for major spacing.

| Token | Value |
|---|---:|
| `space-1` | 4px |
| `space-2` | 8px |
| `space-3` | 12px |
| `space-4` | 16px |
| `space-5` | 20px |
| `space-6` | 24px |
| `space-8` | 32px |
| `space-10` | 40px |

Default horizontal screen padding is **16px**. Wider devices may use 20–24px. Important content should not sit closer than 12px to a device edge.

### Responsive Rules

- Must work from 320px width upward.
- Product grids default to two columns on common mobile widths.
- Avoid fixed card heights when content can wrap.
- Keep text, icons, and controls inside safe areas.
- Do not introduce horizontal scrolling unless content would otherwise become unreadable.

## 3. Radius

| Token | Value | Typical Use |
|---|---:|---|
| `radius-sm` | 8px | Small chips and badges |
| `radius-md` | 12px | Inputs and secondary buttons |
| `radius-lg` | 16px | Product cards and compact modals |
| `radius-xl` | 24px | Hero cards and bottom sheets |
| `radius-pill` | 999px | Filter chips and avatars |

## 4. Typography

Use the system sans-serif stack for speed, readability, and rendering stability.

| Style | Size | Weight | Use |
|---|---:|---:|---|
| Display | 32–36px | 600 | Hero title |
| H1 | 28px | 600 | Screen title |
| H2 | 22–24px | 600 | Section title |
| H3 | 18–20px | 600 | Group title |
| Body | 15–16px | 400 | Primary content |
| Label | 14px | 500 | Buttons, tabs, controls |
| Caption | 12–13px | 400 | Secondary details |

Do not use more than three typography levels inside one card. Never use decorative typefaces for prices, legal information, product ingredients, size details, or primary actions.

## 5. Color Tokens

Industry themes must map brand colors to semantic tokens rather than using raw values throughout components.

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

Primary text, prices, stock states, and critical CTAs must meet readable contrast requirements. Never communicate an important state through color alone.

## 6. Navigation

### Top Bar

May include back, title, search, share, cart, or profile. Limit the right side to three actions.

### Bottom Navigation

Use a maximum of five items. Every item requires both an icon and a text label. Active state must use more than an icon change alone.

Recommended default:

- Home
- Categories
- Explore, Scan, or Style
- Orders
- Profile

Only include a feature-specific center tab when the feature is genuinely available.

## 7. Touch Targets

- Minimum interactive area: **44 × 44px**.
- Minimum spacing between adjacent icon actions: **8px**.
- Primary button height: **48–52px**.
- Never place destructive controls too close to quantity controls or primary CTAs.

## 8. Component States

Every interactive component must define:

- Default
- Pressed
- Focus
- Disabled
- Loading
- Error
- Success where relevant
- Hover for web preview only

## 9. Loading, Empty, Error, and Offline States

### Loading

Use skeletons that resemble the final content structure. Avoid full-screen spinners when partial content can render progressively.

### Empty

Include a clear title, concise explanation, optional illustration, and one relevant CTA.

### Error

Describe the problem in plain language and provide a retry action. Never show raw API errors.

### Offline

Preserve previously loaded data where possible. Explain which actions require a connection.

## 10. Sticky Actions

Sticky actions are appropriate for Add to Cart, Buy Now, Checkout, and Place Order.

- Use a visually separate surface.
- Respect the device safe area.
- Recalculate position when the keyboard opens.
- Never cover product options, totals, or required fields.

## 11. Backend-Driven UI

Home pages should render from an ordered section configuration.

```json
{
  "sections": [
    { "type": "hero", "id": "hero-1", "order": 1, "visible": true },
    { "type": "category_grid", "id": "category-1", "order": 2, "visible": true },
    { "type": "product_carousel", "id": "best-sellers", "order": 3, "visible": true },
    { "type": "voucher_strip", "id": "voucher-1", "order": 4, "visible": true }
  ]
}
```

Frontend rendering must respect `type`, `order`, `visible`, `theme`, `audience`, and API data. Section order must not be fixed in source code.

## 12. Accessibility

- Do not communicate state through color alone.
- Give meaningful icons accessible names.
- Support reasonable text enlargement.
- Do not use text smaller than 12px.
- Provide concise product image alt text.
- Keep focus order aligned with visual order.
- Make validation messages specific and actionable.

## 13. Motion

- Typical duration: 180–300ms.
- Prefer fade, slide, and subtle scale.
- Avoid strong bounce effects.
- Do not delay commerce actions with decorative animation.
- Respect reduced-motion preferences where the platform allows it.

## 14. Performance

- Use responsive images and modern formats.
- Lazy-load below-the-fold imagery.
- Avoid autoplay video on initial load.
- Prevent layout shift by reserving image dimensions.
- Cache configuration and catalog data where appropriate.
- Keep first-screen content lightweight.

## 15. Developer Handoff

Each component specification should define:

- Purpose
- Props
- Variants
- States
- Design tokens
- Dimensions
- Spacing
- Loading and error behavior
- API mapping
- Responsive rules
- Accessibility notes
- Analytics events

## 16. Definition of Done

A screen is complete only when it:

- Uses approved tokens and spacing.
- Works at 320px width without overflow.
- Includes loading, empty, error, and retry states.
- Keeps primary actions visible and reachable.
- Can render with API-provided data.
- Does not hardcode industry-specific content inside shared components.
- Handles long names, missing imagery, and unavailable variants.
- Includes analytics event definitions for critical actions.