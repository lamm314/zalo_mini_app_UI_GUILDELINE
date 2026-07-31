# Electrical & Home Appliances Zalo Mini App UI Guideline

## 1. Purpose

This document defines the detailed mobile UI and UX direction for a Zalo Mini App selling electrical products and home appliances, with a primary commercial focus on Midea air conditioners.

The guideline is designed for the Huayue Supply Chain business model and adapts the visual direction of the provided mobile reference: a clean turquoise interface, large product imagery, rounded white surfaces, compact cards, soft shadows, bottom navigation, and highly visible product actions.

The Mini App must not reproduce iOS device chrome or copy the reference literally. It should preserve the same design qualities while remaining native to the Zalo Mini App environment.

## 2. Business Context

The product experience supports both retail and quotation-oriented purchasing.

Primary product groups:

- Air conditioners
- Refrigerators
- Washing machines
- Space heaters
- Water heaters
- Electrical wires and cables
- Electrical conduits
- Cable trays
- Related installation materials and accessories

Primary brand focus:

- Midea

Secondary and future brands may include Toshiba Home Appliances and other verified manufacturers.

The Huayue website also supports buyer accounts, quotation requests, order tracking, saved products, address books, contracts, invoices, supplier information, and consultation. The Mini App should simplify these functions for mobile instead of attempting to reproduce the full desktop website.

## 3. Product Experience Goals

The Mini App must help users:

1. Find the correct air conditioner or appliance quickly.
2. Understand capacity, energy efficiency, installation requirements, and warranty terms.
3. Compare technically similar products without reading long specifications.
4. Request a quotation when pricing depends on quantity, installation location, or project requirements.
5. Purchase standard products directly when a fixed retail price is available.
6. Book a site survey or installation service.
7. Track quotation, payment, delivery, installation, and warranty status.
8. Contact a sales consultant without leaving the purchase context.

## 4. Design Direction

### 4.1 Visual Character

The interface should feel:

- Modern
- Clean
- Technical but approachable
- Reliable
- Energy-efficient
- Professional
- Suitable for both household buyers and small business buyers

Use a bright turquoise and white foundation inspired by cooling, clean air, efficiency, and modern home technology.

Avoid:

- Heavy industrial visuals on consumer-facing screens
- Too many dark panels
- Excessive gradients
- Dense specification tables on the first viewport
- Large blocks of uppercase text
- Decorative 3D effects that reduce product clarity
- Fake iPhone status bars or Dynamic Island elements

### 4.2 Core Visual Composition

The preferred composition is:

- Full-width or nearly full-width product image
- Rounded white information panel
- Large product title and primary specification
- One dominant turquoise CTA
- Secondary actions shown as outlined buttons or icon controls
- Floating or sticky bottom navigation
- Soft neutral background behind cards

## 5. Design Tokens

### 5.1 Color Palette

```json
{
  "color": {
    "primary": "#19B8B5",
    "primaryPressed": "#109896",
    "primarySoft": "#DFF7F6",
    "primaryDark": "#087D7C",
    "background": "#F5FAFA",
    "surface": "#FFFFFF",
    "surfaceMuted": "#EEF5F5",
    "surfaceTechnical": "#E8F1F2",
    "textPrimary": "#1C2729",
    "textSecondary": "#667376",
    "textMuted": "#8A9698",
    "border": "#DDE7E8",
    "success": "#218C5A",
    "warning": "#B77819",
    "danger": "#C43A3A",
    "info": "#2879B8",
    "energyGood": "#2E9B63"
  }
}
```

Rules:

- Use `primary` for the main purchase or quotation action.
- Use white product surfaces against a very light cool background.
- Use dark text for pricing, capacity, warranty, stock, and safety information.
- Never use light gray for essential specifications.
- Energy labels may use semantic colors, but the meaning must also be written in text.

### 5.2 Typography

Use the system sans-serif stack for performance and compatibility.

Recommended scale:

| Style | Size | Weight | Usage |
|---|---:|---:|---|
| Display | 30–34px | 600 | Onboarding headline |
| H1 | 26–28px | 600 | Main screen title |
| H2 | 21–23px | 600 | Section title |
| H3 | 17–19px | 600 | Card title or group title |
| Body | 15–16px | 400 | Standard content |
| Label | 14px | 500 | Buttons, tabs, chips |
| Caption | 12–13px | 400 | Supporting details |
| Price | 20–24px | 650 | Fixed product price |
| Technical value | 18–22px | 600 | BTU, HP, capacity, power |

Product model codes may use a monospaced style only when it improves readability.

### 5.3 Radius and Elevation

- Product cards: 16px
- Information panels: 20–24px
- Buttons: 14–16px
- Chips: pill radius
- Bottom sheets: 24px top corners
- Shadows must be subtle and used only for elevated cards, sticky CTAs, or bottom navigation.

## 6. Information Architecture

### 6.1 Recommended Bottom Navigation

Use five destinations:

1. Home
2. Categories
3. Quote
4. Orders
5. Account

The central `Quote` destination may use a stronger visual treatment because quotation is a core business action.

Alternative for a retail-first implementation:

1. Home
2. Products
3. Cart
4. Orders
5. Account

Quotation must still remain accessible from product detail and the Home screen.

### 6.2 Primary Screens

- Splash and onboarding
- Home
- Search
- Category browser
- Product listing
- Product detail
- Product comparison
- Air conditioner selector
- Room capacity calculator
- Quotation request
- Cart
- Checkout
- Site survey booking
- Installation booking
- Order tracking
- Warranty center
- Service request
- Saved products
- Account
- Contracts and invoices
- Notifications
- Contact and consultation

## 7. Splash and Onboarding

### 7.1 Splash Screen

Content:

- Huayue logo
- Brand statement such as `Smart Cooling. Reliable Supply.`
- Turquoise background or a soft cooling gradient
- Minimal animation lasting no more than 800ms

Do not delay entry unnecessarily.

### 7.2 Onboarding Screen

Use one to three slides only.

Suggested slides:

1. `Choose the Right Cooling Solution`
   - Large Midea indoor air conditioner image
   - Message about selecting the correct capacity for the room

2. `Transparent Quotation and Installation`
   - Visual showing product, survey, installation, and warranty

3. `Track Every Step in Zalo`
   - Quotation, order, delivery, installation, and service status

Primary CTA:

- `Get Started`

Secondary action:

- `Skip`

## 8. Home Screen

### 8.1 Header

The top area should contain:

- Huayue or Midea-focused logo lockup
- Current delivery or installation province
- Search icon or expandable search field
- Notification icon
- Cart icon when direct purchase is enabled

The location control should use a bottom sheet and explain that location affects delivery and installation availability.

### 8.2 Search

Placeholder examples:

- `Search Midea air conditioners`
- `Search by model, BTU, HP or appliance`

Search suggestions should support:

- Model number
- Brand
- Capacity
- Product category
- Feature
- Room size

### 8.3 Home Section Order

Recommended default order:

1. Hero campaign
2. Quick actions
3. Main categories
4. Air conditioner finder
5. Featured Midea products
6. Shop by room size
7. Best sellers
8. Current promotions
9. Installation and survey services
10. Why buy from Huayue
11. Recently viewed
12. Technical guides
13. Customer support

All sections must be backend-configurable.

### 8.4 Hero Campaign

The hero should use a clean, high-resolution air conditioner image with generous empty space.

Content hierarchy:

- Brand or campaign label
- Short headline
- One benefit statement
- One main CTA

Example:

- Label: `Midea Airstill Series`
- Headline: `Comfortable Cooling Without Harsh Airflow`
- Supporting text: `Energy-saving inverter models for modern homes.`
- CTA: `Explore Series`

Hero cards should use 4:5, 3:4, or 16:10 depending on the imagery.

### 8.5 Quick Actions

Show four compact actions:

- Find My Air Conditioner
- Request a Quote
- Book a Site Survey
- Track My Order

Each action must have an icon, short label, and 44px minimum touch target.

### 8.6 Category Grid

Priority categories:

- Air Conditioners
- Refrigerators
- Washing Machines
- Water Heaters
- Heaters
- Electrical Materials

Air conditioners should receive a larger or first-position card.

### 8.7 Shop by Room Size

Use simple cards:

- Under 15 m²
- 15–20 m²
- 20–30 m²
- 30–40 m²
- Over 40 m²

Each card maps to recommended BTU and HP ranges but must include a disclaimer that ceiling height, sunlight, room type, occupancy, and heat-generating equipment may change the recommendation.

## 9. Category Screen

### 9.1 Air Conditioner Subcategories

- Wall-mounted split AC
- Inverter AC
- Non-inverter AC
- One-way cooling
- Two-way cooling and heating
- Cassette AC
- Floor-standing AC
- Multi-split systems
- Commercial systems
- Accessories and installation materials

### 9.2 Other Appliance Subcategories

- Refrigerators by capacity
- Washing machines by load
- Water heaters by type and volume
- Space heaters by heating technology
- Electrical cables by cross-section
- Conduits by material
- Cable trays by material and finish

### 9.3 Category Card Design

Each card contains:

- Product or lifestyle image
- Category title
- One useful technical descriptor
- Optional product count

Do not overload category cards with promotional badges.

## 10. Product Listing Screen

### 10.1 Header

Include:

- Category title
- Product count
- Filter
- Sort
- Compare mode
- Grid or list switch when useful

### 10.2 Product Card

Mandatory content:

- Large product image
- Brand
- Product name
- Model code
- Main capacity or load
- Inverter status when relevant
- Energy rating when verified
- Fixed price or `Contact for Price`
- Rating only when real review data exists
- Save button
- Compare checkbox or icon

Recommended air conditioner card example:

```text
Midea
Airstill Wall-Mounted Air Conditioner
MSMTIII-10HRFN8
1.0 HP · Inverter · 9,000 BTU
Contact for Price
```

Card actions:

- `View Details`
- Compact `+` or `Request Quote` action

### 10.3 Filters for Air Conditioners

- Brand
- Product type
- Cooling capacity in BTU
- Horsepower
- Recommended room area
- Inverter or non-inverter
- Cooling only or cooling and heating
- Energy label
- Refrigerant type
- Noise level
- Smart control or Wi-Fi
- Price range when fixed pricing is available
- Availability
- Warranty period

### 10.4 Filters for Other Appliances

Refrigerators:

- Total capacity
- Door configuration
- Inverter
- Cooling technology
- Dimensions

Washing machines:

- Load capacity
- Front load or top load
- Inverter
- Drying function
- Dimensions

Water heaters:

- Direct or storage
- Capacity
- Rated power
- Safety features

Electrical materials:

- Conductor material
- Cross-section
- Rated voltage
- Length
- Certification
- Packaging unit

Filters should open in a bottom sheet with:

- Result count
- Clear all
- Apply filters

## 11. Product Detail Screen

### 11.1 Visual Layout

Use the visual structure shown in the reference image:

1. Full-width product gallery
2. Large rounded white product information panel overlapping or following the gallery
3. Product title and main value
4. Compact technical highlights
5. Expandable details
6. Sticky CTA at the bottom

For air conditioners, show both the indoor and outdoor units whenever image assets are available.

### 11.2 Gallery

Support:

- Product images
- Indoor unit
- Outdoor unit
- Remote control
- Installed-room lifestyle image
- Dimension drawing
- Energy label
- Short product video
- 360-degree view only when genuine assets exist

### 11.3 Product Header

Display:

- Brand
- Product name
- Model code
- Rating and verified review count
- Fixed price, starting price, or `Contact for Price`
- Stock or quotation availability
- Official or verified supplier badge where applicable

### 11.4 Technical Highlight Chips

For an air conditioner:

- `9,000 BTU`
- `1.0 HP`
- `Inverter`
- `Up to 15 m²`
- `R32 Refrigerant`
- `Wi-Fi` when supported

The chip row may scroll horizontally, but critical values must also appear in the details section.

### 11.5 Main Benefit Panel

Present three to five consumer benefits using icons:

- Fast cooling
- Energy saving
- Quiet operation
- Gentle airflow
- Smart control

Benefits must map to verified product specifications.

### 11.6 Capacity Fit Card

Show:

- Recommended room area
- Capacity
- Typical room type
- Button: `Check My Room`

Example:

```text
Recommended for rooms up to 15 m²
9,000 BTU · 1.0 HP
Best for bedrooms and compact offices
```

### 11.7 Price and Installation Breakdown

When pricing is fixed, separate:

- Product price
- Standard installation package
- Optional copper pipe cost
- Bracket or stand
- Electrical work
- Delivery
- Promotion
- Estimated total

When pricing is not fixed, show:

- `Contact for Price`
- `Final cost depends on quantity, location, installation conditions, and optional materials.`

Primary CTA:

- `Request Quote`

Secondary CTA:

- `Book Site Survey`

### 11.8 Specification Sections

Use accordions:

- Overview
- Cooling and performance
- Energy efficiency
- Air quality and filtration
- Noise
- Electrical requirements
- Dimensions and weight
- Installation requirements
- Warranty
- Included accessories
- Documentation

Do not place a dense desktop-style specification table in the first viewport.

### 11.9 Warranty and Authenticity

Display:

- Warranty period
- Warranty provider
- Serial number registration process
- Installation requirements that affect warranty
- Official documentation availability

### 11.10 Sticky Product Actions

For fixed-price products:

- Save
- Add to Cart
- Buy Now

For quotation products:

- Save
- Chat with Consultant
- Request Quote

For air conditioners, `Request Quote` should be the dominant action unless all installation costs can be calculated reliably.

## 12. Air Conditioner Finder

This guided selector is a core feature.

### 12.1 Flow

1. Select room type
2. Enter room length, width, and height or choose room area
3. Select sunlight level
4. Select typical occupancy
5. Select heat-generating equipment
6. Select preferred functions
7. Select budget range
8. Receive recommendations

### 12.2 Result Screen

Show:

- Recommended BTU range
- Recommended HP range
- Explanation
- Two to four matching products
- Comparison CTA
- Request survey CTA

Example explanation:

`Based on a 17 m² bedroom with moderate sunlight and two occupants, a 12,000 BTU inverter air conditioner is recommended.`

The result must be presented as guidance, not a guaranteed engineering calculation.

## 13. Product Comparison

Allow comparison of up to three products.

Comparison rows:

- Price or quotation status
- Capacity
- HP
- Recommended area
- Inverter
- Energy efficiency
- Refrigerant
- Noise
- Airflow features
- Wi-Fi
- Warranty
- Indoor unit dimensions
- Outdoor unit dimensions
- Installation notes

Highlight differences instead of repeating identical values visually.

Sticky footer actions:

- Request Quote for Selected Products
- Contact Consultant

## 14. Quotation Request Flow

### 14.1 Entry Points

- Home quick action
- Product card
- Product detail
- Cart
- Air conditioner finder
- Comparison screen

### 14.2 Quote Form

Required information:

- Customer name
- Zalo-linked phone number
- Customer type: household, dealer, contractor, project buyer
- Province and district
- Product or selected products
- Quantity
- Installation required or product only
- Preferred consultation time

Conditional fields:

- Room size
- Installation floor
- Existing air conditioner replacement
- Copper pipe length estimate
- Drainage condition
- Electrical source availability
- Project name
- Required delivery date
- Tax invoice requirement
- Contract requirement
- Additional notes
- Photo upload for installation location

### 14.3 Quote Confirmation

Show:

- Quote request number
- Product summary
- Expected response time
- Assigned consultant when available
- Next steps

Actions:

- Track Request
- Add More Details
- Chat with Consultant
- Return Home

## 15. Site Survey and Installation Booking

### 15.1 Survey Booking

Fields:

- Address
- Contact person
- Preferred date
- Preferred time slot
- Product or capacity of interest
- Property type
- Installation photos
- Notes

Status stages:

- Requested
- Confirmed
- Technician assigned
- Technician on the way
- Survey completed
- Quote updated

### 15.2 Installation Booking

The installation timeline should show:

- Order confirmed
- Product prepared
- Technician assigned
- Delivery scheduled
- Installation in progress
- Testing completed
- Handover completed

The handover screen may include:

- Installation photos
- Technician name
- Serial number
- Warranty activation
- Customer signature or confirmation
- Rating request

## 16. Cart and Checkout

### 16.1 Cart

Each item contains:

- Thumbnail
- Brand and product name
- Model
- Capacity
- Quantity
- Price or quotation status
- Installation option
- Save for later
- Remove

Allow mixed behavior:

- Fixed-price items proceed to checkout.
- Quote-only items proceed to quote request.

Do not combine both into one ambiguous payment action. Clearly separate:

- `Checkout Fixed-Price Items`
- `Request Quote for Selected Items`

### 16.2 Checkout

Blocks:

- Buyer information
- Delivery address
- Installation address if different
- Delivery method
- Installation service
- Invoice information
- Voucher
- Payment method
- Order summary
- Terms

For large appliances, show an explicit confirmation:

- Doorway and access requirements reviewed
- Delivery floor and elevator information provided
- Installation location is ready

## 17. Orders and Tracking

### 17.1 Order Types

- Retail order
- Quotation-based order
- Project order
- Sample order
- Installation service order
- Warranty service order

### 17.2 Order Timeline

Possible statuses:

- Quote requested
- Quote issued
- Awaiting confirmation
- Contract preparation
- Deposit required
- Deposit received
- Processing
- In production or sourcing
- In transit
- Arrived at warehouse
- Delivery scheduled
- Out for delivery
- Installation scheduled
- Installed
- Completed
- Cancelled
- Disputed

Each status must include a customer-friendly explanation and next action.

## 18. Warranty and Service Center

### 18.1 Warranty Registration

Fields:

- Product
- Serial number
- Purchase date
- Installation date
- Installer
- Installation photos
- Invoice or order reference

### 18.2 Service Request

Issue types:

- No cooling
- Weak cooling
- Water leakage
- Unusual noise
- Remote control issue
- Error code
- Electrical issue
- Cleaning and maintenance
- Other

The form should support:

- Description
- Photo upload
- Video upload when supported
- Error code
- Preferred service date
- Warranty status

### 18.3 Maintenance Reminder

The Mini App may show reminders for:

- Filter cleaning
- Periodic maintenance
- Warranty expiration
- Recommended professional cleaning

Reminders should be configurable and should not imply a mandatory service interval unless supported by the manufacturer.

## 19. Account Screen

Recommended modules:

- Profile
- My quotations
- My orders
- Site surveys
- Installations
- Saved products
- Comparison history
- Warranty products
- Service requests
- Addresses
- Contracts
- Invoices
- Notifications
- Language
- Help center
- Contact Huayue

For business buyers, add:

- Company information
- Tax code
- Billing details
- Project list
- Purchase contacts

## 20. Notifications

Notification categories:

- Quote updates
- Consultant messages
- Payment requests
- Delivery updates
- Installation schedule
- Technician arrival
- Warranty activation
- Maintenance reminders
- Promotion notifications
- Price or stock updates for saved products

Each notification should deep-link to the exact relevant screen.

## 21. Content and Education

Create a lightweight guide center with:

- How to choose air conditioner capacity
- Inverter vs non-inverter
- Understanding BTU and HP
- Energy-saving tips
- Installation preparation checklist
- Air conditioner maintenance guide
- Refrigerant basics
- Warranty conditions
- Common error codes

Articles should include relevant product links without becoming aggressive sales pages.

## 22. Empty, Loading, Error, and Offline States

Every data screen must include:

- Skeleton loading
- Empty state
- Partial-data state
- API error state
- Retry action
- Offline message

Examples:

- Empty saved products: `Save products to compare or request a quotation later.`
- Empty quotation list: `Your quotation requests will appear here.`
- No matching products: `Try changing room size, capacity, or feature filters.`

Do not show raw backend errors.

## 23. Accessibility

- Minimum touch target: 44 × 44px
- Minimum body text: 14px, preferably 15–16px
- Do not communicate energy class, stock, or status only through color
- Technical icons must include text labels or accessible names
- Product imagery needs descriptive alt text
- Inputs need visible labels
- Error messages must identify the field and explain how to correct it
- Support dynamic text sizing without hiding primary actions

## 24. Motion

Recommended motion:

- Card press: 0.98 scale, 120–160ms
- Bottom sheet: 240–300ms
- Product image change: short crossfade
- Add to cart: concise confirmation
- Quote submitted: one-time success animation
- Order timeline updates: subtle state transition

Avoid continuous animation around air conditioner fans, cold-air graphics, or promotional CTAs.

## 25. Backend-Driven Home Configuration

Example:

```json
{
  "screen": "home",
  "theme": "huayue_cooling",
  "sections": [
    {
      "id": "hero-midea-airstill",
      "type": "hero",
      "order": 1,
      "headline": "Comfortable Cooling Without Harsh Airflow",
      "cta": {
        "label": "Explore Series",
        "action": "open_collection",
        "target": "midea-airstill"
      }
    },
    {
      "id": "quick-actions",
      "type": "quick_action_grid",
      "order": 2,
      "items": [
        "ac-finder",
        "request-quote",
        "book-survey",
        "track-order"
      ]
    },
    {
      "id": "main-categories",
      "type": "category_grid",
      "order": 3
    },
    {
      "id": "featured-midea",
      "type": "product_carousel",
      "order": 4,
      "query": {
        "brand": "Midea",
        "category": "air-conditioner",
        "featured": true
      }
    }
  ]
}
```

## 26. Air Conditioner Product Data Model

```json
{
  "id": "midea-msmtiii-10hrfn8",
  "brand": "Midea",
  "name": "Airstill Wall-Mounted Air Conditioner",
  "model": "MSMTIII-10HRFN8",
  "category": "air-conditioner",
  "priceMode": "contact",
  "currency": "VND",
  "coolingCapacityBtu": 9000,
  "horsepower": 1.0,
  "recommendedAreaM2": {
    "min": 10,
    "max": 15
  },
  "inverter": true,
  "operationModes": ["cooling"],
  "refrigerant": "R32",
  "wifi": false,
  "energyLabel": null,
  "warrantyMonths": 24,
  "installationAvailable": true,
  "siteSurveyAvailable": true,
  "quoteRequired": true,
  "assets": {
    "indoorUnit": [],
    "outdoorUnit": [],
    "dimensions": [],
    "manuals": []
  }
}
```

## 27. Responsive and Zalo Mini App Constraints

- Design from 320px width upward.
- Use safe-area padding for sticky bottom actions.
- Keep the primary CTA visible above bottom navigation.
- Avoid fixed viewport heights.
- Avoid horizontal scrolling except compact technical chip rows or comparison tables.
- Optimize large appliance imagery for mobile bandwidth.
- Use progressive image loading.
- Do not assume browser access to unsupported device APIs.
- Zalo profile, phone, and address permissions must be requested only when required and explained clearly.

## 28. Developer Handoff Requirements

Every screen specification must define:

- Route
- User objective
- Entry points
- Required API data
- Components
- Component states
- Validation rules
- Empty and error states
- Permission requirements
- Analytics events
- Deep-link behavior
- Responsive behavior
- Sticky CTA behavior

Every component must define:

- Props
- Variants
- Tokens
- States
- Loading behavior
- Error behavior
- Data mapping
- Accessibility label

## 29. Recommended Analytics Events

- `home_viewed`
- `hero_clicked`
- `category_opened`
- `product_viewed`
- `product_saved`
- `product_compared`
- `ac_finder_started`
- `ac_finder_completed`
- `quote_started`
- `quote_submitted`
- `survey_booking_started`
- `survey_booking_completed`
- `consultant_chat_opened`
- `cart_item_added`
- `checkout_started`
- `order_completed`
- `installation_status_viewed`
- `warranty_registered`
- `service_request_submitted`

Do not collect unnecessary sensitive data in analytics payloads.

## 30. Definition of Done

A screen is complete only when:

- It follows shared design tokens and spacing.
- It works at 320px width.
- Primary actions remain accessible above safe areas.
- Loading, empty, error, offline, and retry states are implemented.
- Technical values use consistent units.
- Quotation and fixed-price products are clearly distinguished.
- Air conditioner capacity recommendations include appropriate context.
- Installation, warranty, and pricing conditions are not hidden.
- Product sections are backend-driven.
- Analytics events are defined.
- Accessibility labels and keyboard behavior are verified.
- No desktop-only interaction is required.

## 31. AI Coding Prompt

```text
Build a production-ready Zalo Mini App for Huayue Supply Chain selling electrical and home appliances, with Midea air conditioners as the primary product category.

Use a clean turquoise and white visual system inspired by modern furniture-commerce mobile UI: large product imagery, rounded white information panels, compact product cards, subtle elevation, pill filters, and a prominent sticky action. Do not copy iOS chrome or the reference literally.

Implement Home, Categories, Search, Product Listing, Product Detail, Product Comparison, Air Conditioner Finder, Room Capacity Calculator, Quotation Request, Cart, Checkout, Site Survey Booking, Installation Booking, Orders, Warranty, Service Requests, Saved Products, Notifications, and Account.

Support both fixed-price products and contact-for-price products. Clearly separate checkout from quotation. For air conditioners, prioritize BTU, HP, recommended room area, inverter status, refrigerant, energy efficiency, noise, warranty, and installation requirements.

All homepage sections, content, products, theme configuration, navigation, feature flags, and promotional blocks must be backend-driven. Implement loading, empty, error, offline, retry, and partial-data states for every data screen. Keep all primary CTAs safe-area aware and usable on screens as narrow as 320px.

Repeat implementation, validation, responsive testing, and interaction testing until every Definition of Done requirement in this guideline is satisfied.
```
