# Fashion Zalo Mini App UI Guideline

## 1. Scope

This guideline applies to Zalo Mini Apps selling:

- Womenswear
- Menswear
- Kidswear
- Shoes
- Bags
- Accessories
- Sportswear
- Uniforms
- Modest fashion
- Designer and multi-brand fashion

The intended experience should be visual, editorial, easy to browse, and efficient for selecting size, color, fit, and quantity.

## 2. Visual Direction

Recommended qualities:

- Editorial commerce
- Clean catalog presentation
- Strong product photography
- Clear hierarchy between campaign content and shopping content
- Restrained use of color outside product imagery
- Confident typography
- Large tap targets
- Consistent image ratios

Avoid:

- Dense desktop-style mega menus
- Too many promotional labels
- Mixing multiple image ratios inside the same grid
- Hiding size or stock information until checkout
- Using color swatches without text labels
- Autoplay video with sound
- Decorative animation that delays product browsing

## 3. Color System

Default fashion palette:

```json
{
  "primary": "#171717",
  "primaryPressed": "#000000",
  "primarySoft": "#F1F1F1",
  "background": "#FFFFFF",
  "surface": "#F6F6F4",
  "surfaceWarm": "#F3EFE9",
  "textPrimary": "#171717",
  "textSecondary": "#696969",
  "border": "#E2E2E2",
  "success": "#2E6B4A",
  "warning": "#A66A1F",
  "danger": "#B3261E"
}
```

Brand themes may extend the palette, but all component colors must map to semantic tokens.

## 4. Typography

- Use sans-serif for product names, price, size, stock, delivery, and actions.
- Editorial serif may be used for campaign headlines only.
- Product name: 15–17px, maximum two lines on cards.
- Price: 17–21px, weight 600.
- Size and color labels: minimum 14px.
- Promotional text must remain readable over imagery.

## 5. Information Architecture

Recommended bottom navigation:

1. Home
2. Shop
3. Discover or Lookbook
4. Orders
5. Profile

Typical category hierarchy:

- New In
- Women
- Men
- Kids
- Clothing
- Shoes
- Bags
- Accessories
- Sportswear
- Sale

Keep category depth shallow. Users should reach a product list within two or three taps.

## 6. Home Screen

Recommended sections:

1. Header with logo, search, wishlist, and cart
2. Campaign hero
3. Primary category shortcuts
4. New arrivals
5. Trending products
6. Shop by occasion
7. Lookbook
8. Brand or collection story
9. Voucher or sale strip
10. Recently viewed

### Campaign Hero

- Use full-width editorial imagery.
- Prefer 4:5 or 3:4 ratios.
- Use one main CTA and, at most, one secondary CTA.
- Keep text away from the subject and important garment detail.
- Provide alternate assets for light and dark photography when needed.

### Shop by Occasion

Examples:

- Workwear
- Weekend
- Party
- Travel
- Sports
- Wedding guest
- Back to school

## 7. Product Card

Required content:

- Product image
- Product name
- Price
- Previous price when discounted
- Wishlist action
- Available color count or swatches
- Sale or new badge when relevant

Optional content:

- Brand
- Fit label
- Material summary
- Quick add
- Low-stock state

Rules:

- Use a consistent image ratio, preferably 3:4 or 4:5.
- Use model photography or clean product photography consistently within a section.
- Show no more than two badges.
- Do not hide the price behind interaction.
- Keep color swatches compact but accessible.

## 8. Product Listing

Header should include:

- Category or collection name
- Product count
- Filter
- Sort
- Optional grid density control

Common filters:

- Category
- Size
- Color
- Brand
- Price
- Fit
- Material
- Pattern
- Occasion
- Availability
- Discount

Recommended sorting options:

- Recommended
- Newest
- Best Selling
- Price: Low to High
- Price: High to Low
- Highest Discount

Open filters in a bottom sheet. Preserve user selections when the sheet is closed and reopened.

## 9. Size Selection

- Use text buttons or chips with a minimum 44px touch target.
- Clearly distinguish selected, unavailable, and low-stock states.
- Include a `Size Guide` action near the selector.
- Remember a signed-in user's preferred size when permitted.
- Never select a size automatically without making the choice visible.

### Size Guide

Include:

- Measurement diagram
- Unit switch where relevant
- Product measurements
- Body measurements
- Fit notes
- Model height and worn size

## 10. Color and Variant Selection

- Every swatch must have a text name.
- Selected state requires a visible outline or checkmark.
- Update gallery images when the selected color changes.
- Show unavailable size and color combinations immediately.
- Preserve selected variants when returning from the cart.

## 11. Product Detail

Recommended structure:

1. Image or video gallery
2. Brand and product name
3. Price and promotion
4. Color selector
5. Size selector
6. Size guide and fit note
7. Quantity
8. Sticky Add to Cart or Buy Now
9. Product details
10. Material and care
11. Shipping and returns
12. Reviews
13. Complete the look
14. Related products

### Gallery

- Use high-resolution images with progressive loading.
- Include front, back, side, detail, and styled views when available.
- Support pinch or tap-to-zoom only when performance remains acceptable.
- Do not auto-play video with sound.

### Material and Care

Display:

- Composition
- Lining information
- Care instructions
- Country of origin when required
- Sustainability information only when verified

## 12. Fit and Size Recommendation

Recommended input:

- Height
- Weight
- Body measurements
- Preferred fit
- Known size in a familiar brand

Result should include:

- Recommended size
- Confidence level or explanation
- Alternative size for a looser or tighter fit
- Clear disclaimer when data is insufficient

Do not present a recommendation as guaranteed.

## 13. Outfit Builder

The outfit builder may allow users to combine:

- Top
- Bottom
- Outerwear
- Shoes
- Bag
- Accessories

Required behavior:

- Show running total
- Preserve selected variants
- Warn about unavailable combinations
- Allow add-all-to-cart
- Allow save-to-wishlist
- Keep styling recommendations separate from sponsored placement

## 14. Lookbook and Editorial Content

Lookbook pages should connect inspiration directly to products.

- Use large imagery.
- Add product hotspots only when they do not obstruct the garment.
- Provide a visible `Shop the Look` section below the image.
- Support mixed content: campaign, article, video, and product carousel.
- Keep editorial navigation separate from checkout navigation.

## 15. Wishlist

Wishlist item should show:

- Image
- Name
- Price
- Selected or preferred color
- Stock state
- Price-drop state when available
- Move-to-cart action

Do not remove an item automatically when it goes out of stock.

## 16. Cart

Each item should include:

- Thumbnail
- Product name
- Color
- Size
- Price
- Quantity
- Edit variant
- Remove or save for later

Summary should include:

- Voucher
- Subtotal
- Discount
- Shipping fee
- Total
- Sticky checkout CTA

Warn users immediately when stock changes.

## 17. Checkout

Required blocks:

- Recipient
- Address
- Delivery method
- Voucher
- Payment method
- Gift option when supported
- Order note
- Order summary
- Return-policy acknowledgement when necessary

Keep the checkout linear and minimize optional fields.

## 18. Order Tracking, Returns, and Exchanges

Order status timeline may include:

- Confirmed
- Preparing
- Shipped
- Out for delivery
- Delivered
- Return requested
- Returned
- Refunded

Return flow should support:

1. Select item
2. Select reason
3. Choose return or exchange
4. Upload evidence when required
5. Select pickup or drop-off
6. Review request
7. Receive confirmation

Explain non-returnable conditions before submission.

## 19. Reviews

Fashion reviews may include:

- Rating
- Fit: small, true to size, or large
- Height and approximate body profile when voluntarily provided
- Purchased size and color
- Text
- Images
- Verified purchase label

Do not expose sensitive body data by default.

## 20. Promotions

Supported patterns:

- Flash sale
- Collection discount
- Buy more, save more
- Member pricing
- Voucher
- Free shipping threshold

Always show eligibility, expiry, exclusions, and final price clearly.

## 21. Motion

- Product-card press: subtle scale to 0.98.
- Color change: crossfade gallery image.
- Add to cart: 200–250ms confirmation.
- Filter sheet: 240–300ms slide.
- Wishlist: brief state change without excessive animation.
- Avoid carousel auto-advance faster than five seconds.

## 22. Fashion Product Data Model

```json
{
  "id": "prd_fashion_001",
  "name": "Relaxed Linen Shirt",
  "brand": "Example Studio",
  "category": "shirts",
  "price": 790000,
  "compareAtPrice": 990000,
  "currency": "VND",
  "fit": "relaxed",
  "materials": ["linen", "cotton"],
  "colors": [
    { "id": "ivory", "label": "Ivory", "hex": "#E9E3D8" },
    { "id": "black", "label": "Black", "hex": "#171717" }
  ],
  "variants": [
    { "id": "ivory-s", "color": "ivory", "size": "S", "stock": 8 },
    { "id": "ivory-m", "color": "ivory", "size": "M", "stock": 3 }
  ]
}
```

## 23. Definition of Done

A fashion screen is complete when it:

- Uses shared tokens and approved fashion extensions.
- Works at 320px width.
- Handles long product names and missing imagery.
- Clearly represents size, color, fit, and stock.
- Supports unavailable variant combinations.
- Includes loading, empty, error, and retry states.
- Can render entirely from backend-provided data.
- Includes accessible names for swatches and image actions.
- Defines analytics for product view, variant selection, wishlist, cart, and purchase events.