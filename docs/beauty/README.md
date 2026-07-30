# Beauty Zalo Mini App UI Guideline

## 1. Scope

This guideline applies to Zalo Mini Apps selling or promoting:

- Skincare
- Makeup
- Fragrance
- Hair care
- Body care
- Beauty devices
- Spa and beauty services with product sales

The intended experience is premium, modern, visually rich, and conversion-focused without becoming visually crowded.

## 2. Visual Direction

Recommended qualities:

- Premium minimalism
- Editorial beauty styling
- Soft luxury
- Clean clinical presentation for ingredients and efficacy information
- Generous whitespace
- Large product imagery
- Soft card radii
- Very light shadows
- Calm, confident motion

Avoid:

- Too many saturated colors on one screen
- Strong gradients in every section
- Excessive sale badges
- Desktop layouts compressed into mobile
- Imitating iOS chrome or Dynamic Island patterns
- Medical-style claims without verified source data

## 3. Color System

Default beauty palette:

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

Brand palettes may vary, but every raw value must map to a semantic token.

## 4. Typography

- Use sans-serif for all commerce, product, price, ingredient, and instructional content.
- Serif or script may be used only for a limited hero headline.
- Product name: 16–18px, maximum two lines on cards.
- Current price: 18–22px, weight 600.
- Previous price: 13–15px with strikethrough.
- Ingredient and usage text: minimum 14px.

## 5. Information Architecture

Recommended bottom navigation:

1. Home
2. Categories
3. Skin Scan or Try-On
4. Orders
5. Profile

Only show an AI or camera tab when the feature is fully available.

Common category groups:

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

## 6. Home Screen

Recommended section order:

1. Header with logo, search, cart, and profile
2. Hero banner
3. Quick categories
4. Shop by concern
5. Best sellers
6. New arrivals
7. Routine builder
8. Voucher strip
9. Brand story
10. Reviews or social proof

### Hero Banner

- Prefer a 4:5 or 3:4 mobile ratio.
- Use lifestyle or product-composition imagery.
- Add a subtle overlay only when necessary for text readability.
- Use one primary CTA.
- Limit content to eyebrow, headline, subheadline, and CTA.

### Shop by Concern

Typical concerns:

- Acne
- Dryness
- Sensitivity
- Dark spots
- Aging
- Enlarged pores
- Dullness

Use calm icons or imagery. Avoid uncomfortable medical imagery.

## 7. Product Card

Required content:

- Product image
- Product name
- Current price
- Previous price when applicable
- Rating and review count when reliable
- Sale or new badge
- Wishlist action

Optional content:

- Skin type
- Main benefit
- Volume
- Shade count
- Quick add

Rules:

- Image should occupy approximately 60–70% of the card.
- Show no more than two badges.
- Product name must not exceed two lines.
- Keep backgrounds neutral so the product remains visually dominant.
- Use a consistent image aspect ratio within each grid.

## 8. Product Listing

Header should include:

- Category name
- Product count
- Filter
- Sort
- Optional grid/list toggle

Common beauty filters:

- Product type
- Skin type
- Skin concern
- Ingredient
- Brand
- Price range
- Rating
- Volume
- Alcohol-free, fragrance-free, vegan, or cruelty-free only when verified

Open filters in a bottom sheet with clear `Apply` and `Clear all` actions.

## 9. Product Detail

Recommended structure:

1. Gallery
2. Product name
3. Rating
4. Price and promotion
5. Variant selection: shade, size, or volume
6. Benefit summary
7. Quantity
8. Sticky action area
9. Accordions or tabs
10. Related products

Recommended detail sections:

- Overview
- Ingredients
- How to Use
- Reviews
- Shipping and Returns

### Ingredients

- Show key ingredients first.
- Place the full INCI list in an accordion.
- Do not present unverified medical claims.
- Include allergy or patch-test guidance where relevant.

### Shade Selector

- Minimum swatch size: 32px.
- Selected state requires a clear border or indicator.
- Always show the shade name as text.
- Never rely on color alone.

## 10. Routine Builder

Recommended flow:

1. Select skin type
2. Select primary concern
3. Select sensitivity level
4. Select budget
5. Receive morning and evening routines

Result should include:

- Product order
- Frequency
- Short instructions
- Total price
- Add-all-to-cart action
- Warnings for ingredient conflicts when verified rules exist

## 11. AI Skin Scan

Only design this feature when image-processing capabilities, privacy terms, and consent rules are defined.

Required screens and states:

- Clear explanation of data use
- Explicit consent
- Lighting, distance, and face-angle guidance
- Camera permission state
- Scanning progress
- Result grouped by concern
- Confidence or reference-only notice
- CTA to routine or recommended products
- Delete-image and retake controls

Do not present the result as a medical diagnosis.

## 12. Virtual Try-On

Suitable for lipstick, blush, eye makeup, or hair color.

UI should include:

- Camera or uploaded image
- Shade list
- Shade name
- Effect intensity where supported
- Before and after view
- Add to cart
- Disclaimer that results vary by lighting, screen, and device

## 13. Cart

Each item should show:

- Thumbnail
- Name
- Selected variant
- Price
- Quantity stepper
- Remove or save-for-later action

Order summary:

- Voucher
- Subtotal
- Discount
- Shipping fee
- Total
- Sticky checkout CTA

Warn users when a shade, size, or gift item becomes unavailable.

## 14. Checkout

Required blocks:

- Recipient
- Address
- Delivery method
- Voucher
- Payment method
- Note
- Order summary
- Terms acceptance

Do not enable `Place Order` until all required information is valid.

## 15. Order Success and Post-Purchase

Show:

- Success state
- Order number
- Amount paid
- Payment method
- Estimated delivery
- Track order
- Continue shopping

A review request may be triggered seven days after successful delivery. Keep the message optional and easy to dismiss.

## 16. Reviews

Recommended content:

- Average rating
- Rating distribution
- Filters by skin type or shade
- Buyer images
- Verified purchase label
- Report action

Review form may include rating, text, images, skin type, and purchased shade.

## 17. Loyalty

Member screen may include:

- Membership tier
- Current points
- Tier progress
- Available vouchers
- Point history
- Benefits

Keep gamification simple and clearly connected to real rewards.

## 18. Motion

- Product card press: subtle scale to 0.98.
- Add to cart feedback: 200–250ms.
- Shade change: crossfade product image.
- Bottom sheet: slide up in 240–300ms.
- Success animation: play once, never loop indefinitely.

## 19. Beauty Product Data Model

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

## 20. Definition of Done

A beauty screen is complete when it:

- Uses shared tokens and approved beauty extensions.
- Works at 320px width.
- Supports missing images, long names, and unavailable shades.
- Includes loading, empty, error, and retry states.
- Avoids unverified medical claims.
- Respects consent and privacy for camera or image features.
- Can render entirely from backend-provided data.