# Fashion Zalo Mini App Administration Features

## 1. Scope

This document defines administration capabilities for Fashion Zalo Mini Apps, including womenswear, menswear, childrenswear, footwear, bags, accessories, sportswear, uniforms, designer collections, and multi-brand fashion retail.

It extends the shared administration foundation with category-specific functions for size and fit, color variants, collections, lookbooks, outfit recommendations, pre-orders, drops, store inventory, exchanges, and return-heavy operations.

## 2. Fashion Dashboard

In addition to shared commerce metrics, the Fashion dashboard should display:

- Sales by department, category, brand, collection, and season
- Sales by size and color
- Best-selling and slow-moving variants
- Size-level and color-level stock alerts
- Sell-through rate by collection
- Return and exchange rate
- Return reasons by product and size
- Fit recommendation usage and conversion
- Lookbook and outfit-builder conversion
- Pre-order and product-drop performance
- Store pickup volume
- Inventory aging
- Markdown and clearance performance
- Gross margin by product or collection, subject to permission

## 3. Fashion Taxonomy Management

Administrators can manage structured taxonomies for:

### 3.1 Departments and Categories

- Women
- Men
- Kids
- Unisex
- Clothing
- Footwear
- Bags
- Accessories
- Sportswear
- Formalwear
- Workwear
- Sleepwear
- Swimwear

### 3.2 Product Attributes

- Garment type
- Fit
- Silhouette
- Length
- Sleeve type
- Neckline
- Rise
- Material
- Pattern
- Occasion
- Style
- Season
- Care method
- Sustainability attribute

### 3.3 Merchandising Taxonomies

- Collection
- Season
- Drop
- Capsule
- Trend
- Edit
- Lookbook
- Campaign

Taxonomy values should be reusable across product filters, collections, recommendations, reports, and content pages.

## 4. Product and Variant Management

### 4.1 Fashion Product Fields

Additional fashion-specific fields include:

- Department
- Garment or accessory type
- Gender or unisex designation
- Collection and season
- Material composition
- Fit description
- Model information
- Product measurements
- Care instructions
- Country of origin
- Pattern and style
- Occasion
- Sustainability claims
- Warranty or repair information where applicable

### 4.2 Variant Matrix

The portal should support matrix-style management across:

- Size
- Color
- Width
- Length
- Fit
- Cup size
- Shoe size
- Pack configuration

Administrators should be able to:

- View all size-color combinations in one matrix
- Assign SKU, barcode, price, image, stock, and status per combination
- Bulk update stock or status
- Disable unavailable combinations
- Set color-specific media
- Set market-specific size labels

### 4.3 Color Management

Each color record may include:

- Color name
- Internal color code
- Swatch value or swatch image
- Color family
- Search synonyms
- Product media
- Display order
- Availability by region or Mini App

### 4.4 Product Media

Fashion media management should support:

- Front, back, side, and detail images
- Model images
- Flat-lay images
- Fabric close-ups
- Product videos
- Color-specific media groups
- Lookbook associations
- Image order by variant

## 5. Size and Fit Management

### 5.1 Size Charts

Administrators can create size charts by:

- Brand
- Department
- Product category
- Market
- Measurement system

Each chart may include body measurements, garment measurements, conversion tables, and measurement instructions.

### 5.2 Product Measurements

Administrators can enter measurements per size, such as:

- Chest or bust
- Waist
- Hip
- Shoulder
- Sleeve length
- Garment length
- Inseam
- Rise
- Foot length
- Width

### 5.3 Fit Profiles

Supported fit values may include:

- Slim
- Regular
- Relaxed
- Oversized
- Cropped
- Tall
- Petite
- Wide
- Narrow

### 5.4 Fit Recommendation Configuration

Administrators can configure:

- Customer measurement fields
- Brand-specific size conversion rules
- Category-specific recommendation rules
- Confidence threshold
- Recommendation disclaimer
- Alternative size suggestion
- Learning inputs from exchanges and returns

### 5.5 Fit Analytics

- Recommendation usage
- Recommended-size purchase rate
- Return rate with and without recommendation
- Most exchanged sizes
- Product-level fit issue rate
- Customer feedback such as runs small, true to size, or runs large

## 6. Collection, Season, and Drop Management

### 6.1 Collection Records

Each collection can include:

- Name
- Description
- Season and year
- Launch date
- End date
- Cover media
- Product membership
- Target audience
- Publication status
- Lookbook association

### 6.2 Product Drops

Administrators can configure:

- Drop name
- Release date and time
- Early-access segment
- Product allocation
- Purchase limit
- Countdown visibility
- Waiting-room or queue behavior if supported
- Notification schedule

### 6.3 Seasonal Merchandising

- Schedule collection publication
- Automatically move expired collections to sale or archive
- Configure markdown phases
- Track sell-through by season
- Manage carry-over products

## 7. Lookbook Management

### 7.1 Lookbook Content

A lookbook may include:

- Title and campaign story
- Hero image or video
- Editorial image sequence
- Tagged products
- Outfit groups
- Model information
- Publication schedule
- Target customer segment

### 7.2 Shoppable Media

Administrators can place product hotspots on images and connect each hotspot to a product or exact variant.

### 7.3 Lookbook Analytics

- Views
- Product hotspot clicks
- Add-to-cart actions
- Outfit purchase conversion
- Best-performing image
- Customer segment performance

## 8. Outfit Builder and Styling Management

### 8.1 Outfit Records

Each outfit includes:

- Outfit name
- Style or occasion
- Hero image
- Required items
- Optional alternatives
- Color compatibility notes
- Total price
- Stylist notes
- Publication status

### 8.2 Outfit Rules

Administrators can define compatible categories and combinations, such as:

- Top plus bottom
- Dress plus outerwear
- Footwear plus bag
- Formalwear combinations
- Sportswear sets

### 8.3 Outfit Analytics

- Outfit views
- Add-all-to-cart rate
- Partial outfit purchase rate
- Most substituted items
- Conversion by occasion or style

## 9. Inventory and Allocation

### 9.1 Variant-Level Inventory

Inventory must be tracked by exact size-color combination.

Recommended states:

- Available
- Reserved
- In transfer
- Damaged
- Display stock
- Return inspection
- Incoming

### 9.2 Store and Warehouse Allocation

Administrators can:

- Allocate stock to stores, warehouses, distributors, or Mini Apps
- Reserve stock for product launches
- Set online-only or store-only variants
- Configure store pickup availability
- Transfer stock
- View oversold or undersupplied locations

### 9.3 Inventory Aging

Reports should classify inventory by age and identify products requiring markdown, transfer, bundling, or clearance.

## 10. Pre-Order and Back-in-Stock Management

### 10.1 Pre-Order

Administrators can configure:

- Pre-order start and end date
- Expected delivery window
- Deposit or full-payment requirement
- Quantity limit
- Cancellation terms
- Customer notification schedule

### 10.2 Back-in-Stock Requests

- View demand by product variant
- Export waiting-list data
- Notify customers when stock returns
- Track notification conversion
- Prioritize replenishment using demand data

## 11. Order, Exchange, and Return Management

Fashion operations require strong exchange support because size and fit issues are common.

### 11.1 Exchange Workflow

1. Customer requests exchange
2. Customer selects reason and replacement variant
3. System checks replacement stock
4. Operator approves, rejects, or requests evidence
5. Return shipment is arranged
6. Returned item is inspected
7. Replacement item is shipped
8. Inventory and order history are updated

### 11.2 Return Reasons

Recommended structured reasons:

- Too small
- Too large
- Fit not as expected
- Color differs from expectation
- Material differs from expectation
- Damaged item
- Wrong item
- Missing item
- Changed mind
- Delivery delay

### 11.3 Return Inspection

Inspection records may include:

- Item condition
- Tags attached
- Packaging condition
- Signs of wear
- Damage evidence
- Restock decision
- Refund decision

### 11.4 Return Analytics

- Return rate by product
- Return rate by size
- Exchange rate by size pair
- Return reason distribution
- Return rate by supplier or brand
- Net revenue after returns

## 12. Pricing and Markdown Management

### 12.1 Price Lists

Administrators can manage:

- Standard price
- Compare-at price
- Member price
- Distributor price
- Store-specific price
- Market-specific price

### 12.2 Markdown Scheduling

- Schedule percentage or fixed markdown
- Apply by product, variant, collection, season, or age of stock
- Configure multiple markdown phases
- Set minimum allowed price
- Preview affected products
- Roll back before publication

### 12.3 Clearance Management

- Clearance collection
- Final-sale flag
- Return restriction disclosure
- Remaining stock report
- Clearance performance report

## 13. Fashion Content Management

### 13.1 Content Types

- Style guide
- Size guide
- Fabric guide
- Care guide
- Trend article
- Occasion edit
- Brand story
- Collection story
- Lookbook
- Styling video

### 13.2 Content-to-Commerce Linking

Administrators can link content to:

- Products
- Exact variants
- Collections
- Outfits
- Brands
- Campaigns
- Store appointments

## 14. Store and Appointment Management

For fashion businesses with physical stores:

### 14.1 Store Records

- Store name
- Address
- Contact details
- Business hours
- Services
- Pickup support
- Inventory visibility
- Map coordinates where supported

### 14.2 Appointment Types

- Personal styling
- Fitting appointment
- Bridal or formalwear consultation
- Product pickup
- Repair consultation

Administrators can manage availability, staff assignment, capacity, confirmation, rescheduling, cancellation, and follow-up.

## 15. Loyalty and Membership

Fashion-specific membership features may include:

- Early access to drops
- Member pricing
- Birthday voucher
- Free alterations
- Free shipping
- Personal styling session
- Exclusive collection access
- Repair or care benefits

Administrators can configure tier thresholds, benefit rules, validity, and eligible collections.

## 16. Fashion Campaign Management

Common Fashion campaigns:

- New collection launch
- Product drop
- Seasonal sale
- Clearance
- Outfit campaign
- Back-to-school
- Workwear edit
- Holiday gift guide
- Member-only preview
- Store opening campaign

Campaign performance should be measurable by collection, product, variant, content entry point, store, and customer segment.

## 17. Fashion Reports

Recommended reports:

- Sales by department and category
- Sales by brand and collection
- Size and color performance
- Variant sell-through
- Inventory aging
- Markdown effectiveness
- Return and exchange rate
- Return reasons
- Fit recommendation performance
- Lookbook conversion
- Outfit-builder conversion
- Pre-order performance
- Store pickup performance

## 18. Fashion Administration Definition of Done

A Fashion administration module is complete when:

- Inventory is managed at exact variant level
- Size charts and product measurements are structured data
- Color-specific media and stock are supported
- Collections, seasons, and drops are independently manageable
- Lookbooks and outfits are shoppable
- Exchanges are supported as a dedicated workflow
- Return reasons and inspection results are reportable
- Markdown scheduling and inventory aging are available
- Fit recommendation rules can be managed without source-code changes
- Store and warehouse stock remain isolated and auditable
