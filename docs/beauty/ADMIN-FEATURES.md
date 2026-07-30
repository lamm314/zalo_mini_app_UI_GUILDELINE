# Beauty Zalo Mini App Administration Features

## 1. Scope

This document defines the administration capabilities for Beauty Zalo Mini Apps, including skincare, makeup, fragrance, hair care, body care, beauty devices, salon services, and beauty membership programs.

It extends the shared administration foundation with category-specific functions required to manage ingredients, skin concerns, shades, routines, beauty consultations, product claims, sample programs, subscriptions, and AI-assisted beauty features.

## 2. Beauty Dashboard

In addition to shared commerce metrics, the Beauty dashboard should display:

- Sales by skincare, makeup, fragrance, hair care, and body care
- Sales by brand and product concern
- Best-selling shades and volumes
- Shade-level out-of-stock alerts
- Routine bundle performance
- Skin concern conversion performance
- AI skin scan completion rate
- Virtual try-on usage and conversion rate
- Sample-to-full-size conversion
- Repeat purchase interval by product type
- Subscription or replenishment revenue
- Review distribution by skin type and shade
- Products with high allergy or irritation complaint rates

## 3. Beauty Taxonomy Management

Administrators can manage structured taxonomies for:

### 3.1 Product Types

- Cleanser
- Toner
- Essence
- Serum
- Moisturizer
- Sunscreen
- Mask
- Exfoliant
- Lip product
- Foundation
- Concealer
- Blush
- Eye makeup
- Fragrance
- Shampoo
- Conditioner
- Hair treatment
- Body care
- Beauty device

### 3.2 Skin and Hair Profiles

- Skin type
- Skin concern
- Skin sensitivity level
- Hair type
- Scalp concern
- Age range
- Climate suitability
- Routine step
- Day or night use

### 3.3 Product Attributes

- Finish
- Coverage
- Texture
- Fragrance family
- SPF level
- Water resistance
- Vegan status
- Cruelty-free status
- Alcohol-free status
- Fragrance-free status
- Dermatologically tested status

Taxonomy values should be reusable across product filters, recommendations, routine builders, reports, and customer profiles.

## 4. Ingredient Library

### 4.1 Ingredient Records

Each ingredient record may include:

- Common name
- INCI name
- Ingredient group
- Primary function
- Benefits
- Recommended skin types
- Concerns addressed
- Usage notes
- Compatibility notes
- Potential sensitivities
- Pregnancy or medical disclaimer where applicable
- Evidence or reference source
- Icon or image

### 4.2 Ingredient Assignment

Administrators can:

- Assign key ingredients to products
- Maintain the full ingredient list
- Set ingredient display order
- Mark hero ingredients
- Add explanatory content
- Flag incomplete or unverified ingredient data

### 4.3 Ingredient Conflict Rules

Optional rules may support routine recommendations, such as:

- Ingredients that should not be layered in the same routine
- Ingredients requiring gradual introduction
- Ingredients recommended only for evening use
- Ingredients requiring sunscreen guidance

These rules are informational and must not be presented as medical diagnosis.

## 5. Product and Variant Management

### 5.1 Beauty Product Fields

Additional beauty-specific fields include:

- Product type
- Skin type compatibility
- Skin concerns
- Hair or scalp type
- Routine step
- Texture
- Finish
- Coverage level
- Key ingredients
- Full INCI list
- Directions for use
- Patch-test guidance
- Warnings
- Clinical or consumer study summary
- Claim verification status
- Country of origin
- Shelf life
- Period after opening
- Regulatory or safety documents

### 5.2 Beauty Variants

Supported variant types:

- Shade
- Volume
- Size
- Fragrance
- Formula
- Finish
- Pack configuration

Each shade can include:

- Shade name
- Shade code
- Color swatch
- Undertone
- Depth level
- Product image
- Try-on asset reference
- Stock and price
- Search keywords

### 5.3 Product Claims

Administrators can manage claims such as hydrating, brightening, smoothing, long-wearing, waterproof, or non-comedogenic.

Each claim should have:

- Claim text
- Verification status
- Supporting reference
- Expiration or review date
- Approved usage context

Unverified claims should not be published.

## 6. Routine Builder Management

### 6.1 Routine Rules

Administrators can configure recommendations using:

- Skin type
- Main concern
- Sensitivity level
- Age range
- Budget
- Preferred number of steps
- Morning or evening routine
- Ingredient restrictions
- Product availability

### 6.2 Routine Templates

A routine template includes:

- Routine name
- Target profile
- Ordered steps
- Required and optional products
- Usage frequency
- Instructions
- Total estimated price
- Alternative products
- Disclaimer

### 6.3 Routine Analytics

- Routine starts and completions
- Add-all-to-cart rate
- Routine purchase rate
- Most selected concerns
- Product substitution frequency
- Drop-off by questionnaire step

## 7. AI Skin Scan Administration

### 7.1 Configuration

Administrators can configure:

- Feature availability
- Consent text
- Data retention policy
- Supported analysis categories
- Result disclaimer
- Confidence display rules
- Recommended product mapping
- Escalation to human consultant

### 7.2 Analysis Categories

Possible categories include:

- Hydration appearance
- Oiliness appearance
- Visible pores
- Uneven tone
- Fine-line appearance
- Redness appearance
- Blemish appearance

The system must avoid presenting results as a medical diagnosis.

### 7.3 Result Mapping

Administrators can map each result category and severity range to:

- Educational content
- Routine template
- Product collection
- Consultation CTA
- Follow-up recommendation

### 7.4 AI Feature Monitoring

- Consent rate
- Scan completion rate
- Scan failures
- Result distribution
- Product click-through
- Purchase conversion
- User deletion requests

## 8. Virtual Try-On Administration

### 8.1 Supported Products

- Lipstick
- Lip tint
- Blush
- Foundation
- Eye shadow
- Eyeliner
- Hair color

### 8.2 Try-On Asset Management

For each supported variant:

- Shade asset
- Rendering parameters
- Opacity range
- Skin tone compatibility notes
- Preview image
- Device support status
- Quality review status

### 8.3 Try-On Analytics

- Try-on sessions
- Shades tried
- Average session duration
- Add-to-cart after try-on
- Purchase after try-on
- Most compared shades
- Device or rendering failure rate

## 9. Consultation and Appointment Management

For Beauty Mini Apps offering consultations or services:

### 9.1 Service Management

- Service name
- Duration
- Price
- Location
- Consultant or specialist
- Capacity
- Preparation instructions
- Cancellation policy

### 9.2 Appointment Operations

- Calendar view
- Appointment confirmation
- Reschedule
- Cancellation
- No-show status
- Consultant notes
- Recommended products
- Follow-up message

### 9.3 Consultation Records

Access must be role-restricted. Records may include customer concern, preferences, recommendation summary, purchased items, and follow-up date.

## 10. Samples, Gifts, and Gift Sets

### 10.1 Sample Rules

Administrators can configure:

- Free sample eligibility
- Sample quantity per order
- Minimum order value
- Product or campaign restrictions
- Sample stock
- Customer selection or automatic assignment

### 10.2 Gift-With-Purchase

- Gift tiers by order value
- Gift stock
- Eligible products
- Campaign dates
- Substitution rules

### 10.3 Gift Sets

- Fixed gift set
- Build-your-own set
- Gift wrapping
- Gift message
- Occasion tags
- Set-specific inventory or component inventory

## 11. Subscription and Replenishment

Where supported, administrators can configure:

- Eligible products
- Delivery interval
- Subscriber discount
- Minimum commitment
- Skip, pause, or cancel rules
- Payment retry rules
- Reminder schedule
- Replacement product behavior when unavailable

Reports should include active subscriptions, churn, failed renewals, and subscription revenue.

## 12. Beauty Content Management

### 12.1 Content Types

- Ingredient guide
- Skin concern guide
- Routine guide
- Makeup tutorial
- Shade guide
- Fragrance guide
- Hair care guide
- Expert article
- Before-and-after story with consent
- Product usage video

### 12.2 Content-to-Commerce Linking

Administrators can attach:

- Related products
- Related routines
- Related ingredients
- Related concerns
- Consultant booking CTA
- Campaign CTA

## 13. Review and Safety Monitoring

Beauty review moderation should support filters for:

- Skin type
- Skin concern
- Shade
- Product variant
- Verified purchase
- Reaction or irritation mention
- Packaging issue
- Scent feedback

### 13.1 Safety Escalation

Reviews or support tickets containing potential adverse reactions may be flagged for specialist review.

The workflow may include:

1. Automatic keyword flag
2. Human review
3. Customer follow-up
4. Product batch reference collection
5. Internal safety note
6. Resolution and closure

## 14. Loyalty and Membership

Beauty-specific loyalty features may include:

- Points by purchase
- Tier progression
- Birthday benefits
- Early access to launches
- Free samples
- Consultation benefits
- Member-only bundles
- Review rewards
- Replenishment rewards

Administrators can configure earning rules, redemption rules, tier thresholds, and benefit validity.

## 15. Campaign Management

Common Beauty campaigns:

- Product launch
- Shade launch
- Skincare routine campaign
- Ingredient education campaign
- Gift-with-purchase
- Sample campaign
- Seasonal beauty collection
- Member-only preview
- Consultation campaign

Campaign performance should be measurable by product, shade, customer segment, and content entry point.

## 16. Beauty Reports

Recommended reports:

- Sales by product type
- Sales by concern
- Sales by skin type compatibility
- Shade performance
- Ingredient interest
- Routine conversion
- AI scan conversion
- Virtual try-on conversion
- Sample conversion
- Repeat purchase interval
- Complaint and sensitivity rate
- Review sentiment by product attribute

## 17. Beauty Administration Definition of Done

A Beauty administration module is complete when:

- Product types, skin concerns, ingredients, and variants are structured data
- Claims have a verification workflow
- Shade-level inventory is supported
- Routine recommendations are editable without code changes
- AI features have consent, disclaimer, retention, and monitoring controls
- Reviews can be filtered by relevant Beauty attributes
- Safety-related reports can be escalated
- Content can link directly to products, routines, and campaigns
- All sensitive customer and consultation data is permission-controlled
