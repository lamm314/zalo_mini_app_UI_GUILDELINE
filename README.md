# Zalo Mini App UI Guideline

A reusable, industry-specific UI, UX, and administration guideline for commerce-oriented Zalo Mini Apps.

## Current Scope

- [Shared Design Foundations](docs/shared/FOUNDATIONS.md)
- [Shared Administration Features](docs/admin/SHARED-ADMIN-FEATURES.md)
- [Beauty UI Guideline](docs/beauty/README.md)
- [Beauty Administration Features](docs/beauty/ADMIN-FEATURES.md)
- [Fashion UI Guideline](docs/fashion/README.md)
- [Fashion Administration Features](docs/fashion/ADMIN-FEATURES.md)
- [Electrical & Home Appliances UI Guideline](docs/electrical-appliances/README.md)

## Purpose

This repository is the single source of truth for product designers, product owners, frontend developers, backend developers, QA teams, administration operators, and AI coding agents building Zalo Mini Apps.

Each industry may define its own visual language, catalog model, content hierarchy, filters, product variants, administration modules, and business flows. However, all implementations must share the same foundations for spacing, touch targets, accessibility, interaction states, responsive behavior, safe areas, backend-driven rendering, permissions, auditability, and operational data management.

## Core Rules

1. Do not copy iOS, Android, or desktop e-commerce interfaces literally. The experience must be designed for the Zalo Mini App environment.
2. Do not hardcode home-page sections, banners, categories, theme colors, promotional blocks, or section order.
3. Every data-driven screen must support loading, empty, error, retry, offline, and partial-data states.
4. Primary actions must remain reachable with one hand and must never be hidden by the bottom navigation, keyboard, or device safe area.
5. Reuse shared components and design tokens before creating industry-specific variants.
6. Prices, discounts, inventory, delivery expectations, return policies, and order statuses must be explicit and trustworthy.
7. Product imagery must be optimized for mobile bandwidth and progressive loading.
8. Administration features must be permission-controlled, auditable, searchable, filterable, and exportable where operationally necessary.
9. Industry-specific administration features must extend the shared administration foundation instead of duplicating common modules.
10. All user-facing documentation, component specifications, design tokens, administration specifications, and AI prompts in this repository must be written in English.

## Repository Structure

```text
docs/
├── admin/
│   └── SHARED-ADMIN-FEATURES.md
├── shared/
│   └── FOUNDATIONS.md
├── beauty/
│   ├── README.md
│   └── ADMIN-FEATURES.md
├── fashion/
│   ├── README.md
│   └── ADMIN-FEATURES.md
└── electrical-appliances/
    └── README.md
```

## Recommended Implementation Model

The Mini App should behave as a presentation shell. Content, catalog data, banners, navigation, feature flags, layout configuration, and theme settings should be delivered by backend APIs.

The administration portal owns operational configuration and business data management.

```text
Administration Portal
        ↓
Backend API + Database
        ↓
Catalog + Orders + Customers + Content + Layout Configuration
        ↓
Zalo Mini App Renderer
```

## Administration Documentation Model

Administration requirements are divided into two levels:

1. **Shared administration foundation:** dashboard, products, orders, customers, promotions, content, notifications, reports, permissions, audit logs, imports, exports, Mini App configuration, and multi-tenant management.
2. **Industry-specific extensions:** product attributes, operational workflows, analytics, and tools unique to Beauty, Fashion, Electrical & Home Appliances, or future categories.

## Status

Version **1.2** currently covers three commerce categories:

- Beauty and cosmetics
- Fashion and apparel
- Electrical and home appliances, with a primary focus on Midea air conditioners

Beauty and Fashion include UI guidelines and administration feature specifications. Electrical and Home Appliances currently includes a detailed Mini App UI and UX guideline adapted to Huayue Supply Chain's quotation, delivery, installation, warranty, and after-sales workflows.

Additional industry guidelines should extend both the shared design foundations and the shared administration foundation without redefining common rules.
