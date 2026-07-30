# Shared Zalo Mini App Administration Features

## 1. Purpose

This document defines the common administration capabilities required by commerce-oriented Zalo Mini Apps. It is based on the operational pattern of a seasonal retail Mini App administration portal and converts that pattern into a reusable foundation for multiple product categories.

The administration portal should allow business operators to manage content, products, orders, customers, promotions, fulfillment, reports, permissions, and Mini App configuration without editing source code.

## 2. Administration Roles

Recommended default roles:

- **Super Administrator:** Full platform access, role assignment, security settings, and Mini App configuration.
- **Business Administrator:** Manages catalog, content, promotions, customers, and reports.
- **Order Operator:** Manages orders, payment status, delivery, cancellations, and returns.
- **Content Editor:** Manages banners, landing pages, articles, media, and navigation.
- **Marketing Operator:** Manages vouchers, campaigns, customer segments, broadcasts, and loyalty programs.
- **Warehouse Operator:** Manages inventory, stock adjustments, warehouse transfers, and stock alerts.
- **Customer Support Agent:** Views customers, orders, complaints, review history, and support requests.
- **Analyst:** Read-only access to dashboards, reports, and exports.

Every sensitive operation should be logged with actor, timestamp, previous value, new value, and affected object.

## 3. Dashboard

The dashboard provides an operational overview for the selected Mini App and date range.

### 3.1 Core Metrics

- Gross sales
- Net sales after discounts and refunds
- Number of orders
- Average order value
- Paid, unpaid, cancelled, refunded, and returned orders
- New customers
- Returning customers
- Product views and add-to-cart rate
- Checkout conversion rate
- Voucher usage
- Inventory alerts
- Customer service requests

### 3.2 Operational Widgets

- Orders requiring confirmation
- Orders waiting for payment
- Orders ready for packing
- Delayed deliveries
- Low-stock products
- Out-of-stock variants
- Campaigns ending soon
- Pending reviews or reported reviews
- Failed notification deliveries

### 3.3 Dashboard Filters

- Today, yesterday, last 7 days, last 30 days, custom range
- Mini App
- Store, branch, distributor, or warehouse
- Sales channel
- Product category
- Campaign

## 4. Product Catalog Management

### 4.1 Product List

Administrators can:

- Create, edit, duplicate, archive, restore, or delete products
- Publish immediately or schedule publication
- Set product status: draft, active, hidden, out of stock, discontinued
- Bulk update price, inventory, category, tags, or publication status
- Import and export catalog data
- Search by product name, SKU, barcode, brand, category, or tag

### 4.2 Product Information

Common product fields:

- Product name
- Short description
- Full description
- Category and subcategory
- Brand
- SKU and internal code
- Images and videos
- Base price and comparison price
- Cost price, if permitted by role
- Tax configuration
- Weight and dimensions
- Inventory policy
- Shipping restrictions
- Product tags
- SEO or share metadata
- Related products
- Cross-sell and upsell groups

### 4.3 Variants

The portal should support category-specific variants such as size, color, shade, volume, fragrance, pack size, material, flavor, or gift-box configuration.

Each variant may include:

- Variant SKU
- Price override
- Stock quantity
- Image override
- Barcode
- Weight and dimensions
- Availability status
- Pre-order status

### 4.4 Categories and Collections

Administrators can manage:

- Hierarchical categories
- Manual collections
- Rule-based collections
- Best sellers
- New arrivals
- Seasonal collections
- Brand collections
- Homepage featured groups

Collection rules may use category, price, inventory, tag, brand, publication date, sales volume, or campaign membership.

## 5. Inventory and Warehouse

### 5.1 Inventory Control

- Current stock by product variant
- Available, reserved, damaged, and incoming stock
- Manual stock adjustment with reason
- Stock movement history
- Low-stock threshold
- Out-of-stock behavior
- Pre-order quantity and release date

### 5.2 Warehouse Management

Where multiple warehouses exist:

- Create and manage warehouses
- Assign service areas
- Transfer stock between warehouses
- Select fulfillment priority
- View stock by warehouse
- Restrict products to specific branches or distributors

### 5.3 Inventory Alerts

- Low stock
- Out of stock
- Negative stock attempt
- Unusual stock adjustment
- Variant stock mismatch

## 6. Order Management

### 6.1 Order List

Order filters should include:

- Order code
- Customer name or phone number
- Date range
- Payment status
- Fulfillment status
- Delivery method
- Store, warehouse, or distributor
- Voucher or campaign
- Product
- Order source

### 6.2 Order Status Workflow

Recommended states:

1. New
2. Awaiting confirmation
3. Awaiting payment
4. Confirmed
5. Preparing
6. Ready for shipment or pickup
7. In delivery
8. Delivered
9. Completed
10. Cancelled
11. Return requested
12. Returned
13. Refund pending
14. Refunded

Status transitions should be configurable and permission-controlled.

### 6.3 Order Detail

The order detail page includes:

- Customer information
- Delivery address
- Ordered items and variants
- Pricing breakdown
- Voucher and promotion details
- Payment method and transaction reference
- Delivery provider and tracking code
- Internal notes
- Customer notes
- Timeline of status changes
- Attached evidence or documents
- Refund and return history

### 6.4 Order Actions

- Confirm order
- Edit recipient information before fulfillment
- Change warehouse or delivery method
- Add internal note
- Print invoice, packing slip, or shipping label
- Cancel order with reason
- Create partial or full refund request
- Approve or reject return request
- Resend confirmation message
- Export selected orders

## 7. Customer Management

### 7.1 Customer Profile

The customer profile includes:

- Zalo user identifier where permitted
- Name, phone number, email, and addresses
- Consent and communication preferences
- Order history
- Lifetime value
- Average order value
- Loyalty points and tier
- Vouchers issued and used
- Product reviews
- Support requests
- Customer tags
- Internal notes

### 7.2 Customer Segmentation

Segments may be based on:

- New or returning customer
- Purchase frequency
- Lifetime value
- Product category interest
- Last purchase date
- Abandoned cart status
- Loyalty tier
- Location
- Campaign response
- Birthday month

### 7.3 Data Privacy

Administrators should be able to:

- View consent history
- Export customer data when legally required
- Process deletion or anonymization requests
- Restrict access to personal information by role
- Review data access logs

## 8. Promotions and Voucher Management

### 8.1 Voucher Types

- Fixed amount discount
- Percentage discount
- Free shipping
- Product-specific discount
- Category-specific discount
- Buy X get Y
- Bundle price
- New customer voucher
- Birthday voucher
- Loyalty reward voucher
- Distributor or branch-specific voucher

### 8.2 Voucher Rules

- Start and end time
- Usage limit
- Limit per customer
- Minimum order value
- Maximum discount value
- Eligible products or categories
- Excluded products
- Eligible customer segments
- Payment method restriction
- Delivery method restriction
- Combinability rules

### 8.3 Campaign Management

Campaigns combine products, banners, vouchers, landing pages, customer segments, and notification schedules.

Recommended campaign states:

- Draft
- Scheduled
- Active
- Paused
- Completed
- Archived

## 9. Content and Interface Management

### 9.1 Homepage Builder

The homepage should be backend-driven. Administrators can:

- Add, remove, reorder, hide, or schedule sections
- Select section type
- Configure title, subtitle, CTA, image, theme, and destination
- Preview before publishing
- Set visibility by date, customer segment, branch, or Mini App

Common section types:

- Hero banner
- Category grid
- Product carousel
- Flash sale
- Voucher strip
- Brand story
- Article list
- Review carousel
- Live event banner
- Membership summary

### 9.2 Banner Management

- Desktop preview is optional; mobile preview is mandatory
- Start and end time
- Destination type and destination value
- Audience targeting
- Display position
- Priority
- Impression and click tracking

### 9.3 Navigation Management

Administrators can configure:

- Bottom navigation items
- Category menu
- Shortcut menu
- Header actions
- Deep links
- External links, where allowed

### 9.4 Articles and Pages

- Create and edit articles
- Draft, schedule, publish, unpublish, or archive
- Manage cover image, content blocks, author, tags, and related products
- Create policy pages, FAQ, buying guides, and campaign landing pages

## 10. Notifications and Customer Communication

### 10.1 Transactional Notifications

Recommended triggers:

- Order created
- Payment successful or failed
- Order confirmed
- Order shipped
- Delivery completed
- Order cancelled
- Refund processed
- Voucher issued
- Loyalty points changed
- Review request after successful delivery

### 10.2 Marketing Messages

Administrators can:

- Create message templates
- Select customer segment
- Schedule delivery
- Apply frequency limits
- Preview messages
- Track sent, delivered, failed, opened, and clicked status where supported

Marketing messages must respect user consent and Zalo platform requirements.

## 11. Reviews and User-Generated Content

- View product reviews
- Filter by rating, product, verification status, or report status
- Approve, hide, restore, or report content
- Reply to reviews
- Mark verified purchases
- Manage customer-uploaded images
- Export review data

Moderation actions must be logged.

## 12. Returns, Refunds, and Complaints

### 12.1 Return Requests

Each request should include:

- Order and product
- Quantity
- Reason
- Description
- Images or evidence
- Requested resolution
- Return shipping information

### 12.2 Resolution Actions

- Approve or reject
- Request additional evidence
- Offer exchange
- Offer partial refund
- Issue full refund
- Issue store credit or voucher
- Close complaint

### 12.3 Complaint Management

Support tickets should include category, priority, owner, SLA deadline, status, conversation history, attachments, and resolution notes.

## 13. Reports and Analytics

### 13.1 Sales Reports

- Sales by date
- Sales by product, category, brand, branch, or distributor
- Gross and net sales
- Discount value
- Refund value
- Average order value
- Payment method breakdown

### 13.2 Product Reports

- Best-selling products
- Low-performing products
- Product views
- Add-to-cart rate
- Conversion rate
- Return rate
- Stock turnover

### 13.3 Customer Reports

- New and returning customers
- Customer lifetime value
- Retention
- Repeat purchase rate
- Segment performance
- Loyalty participation

### 13.4 Campaign Reports

- Voucher redemptions
- Campaign revenue
- Banner impressions and clicks
- Notification delivery and click metrics
- Revenue by campaign

All reports should support date filtering and export to CSV or spreadsheet format.

## 14. Mini App Configuration

Administrators can manage:

- Mini App name and branding
- Logo and theme tokens
- Contact information
- Business hours
- Supported delivery methods
- Supported payment methods
- Default warehouse
- Order numbering rules
- Terms of use and privacy policy links
- Customer support information
- Maintenance mode
- Feature flags

## 15. Distributor and Multi-Tenant Management

For systems with multiple distributors or Mini Apps:

- Create and manage distributor accounts
- Assign one or more Mini Apps
- Restrict data access by tenant
- Configure distributor-specific catalog, pricing, inventory, vouchers, and branding
- Consolidate reports at platform level
- Allow tenant-level reports without exposing other tenants

## 16. Permissions and Audit Logs

The system must provide:

- Role-based access control
- Permission by module and action
- Optional scope by Mini App, branch, distributor, or warehouse
- Login history
- Data export history
- Configuration change history
- Order and refund action history
- Failed access attempts

## 17. Import and Export

Supported data operations should include:

- Product import and export
- Inventory import and export
- Order export
- Customer export subject to permission
- Voucher import
- Report export
- Validation report for failed import rows

Bulk imports should support preview, validation, error download, and rollback when possible.

## 18. Non-Functional Requirements

- Responsive desktop administration interface
- Clear empty, loading, success, and error states
- Search and filtering for every large data table
- Pagination or virtualized lists
- Confirmation for destructive actions
- Optimistic locking or conflict warning for concurrent edits
- Automatic session timeout
- Auditability of sensitive actions
- Accessibility for keyboard navigation and readable contrast
- Data isolation between Mini Apps and distributors

## 19. Definition of Done

An administration module is complete only when it includes:

- Role and permission rules
- List, detail, create, edit, and archive flows where applicable
- Search, filter, sort, and pagination
- Validation and user-friendly error handling
- Loading and empty states
- Audit log coverage for sensitive actions
- Import or export capability where operationally necessary
- API mapping and backend data ownership
- Mobile Mini App impact clearly documented
