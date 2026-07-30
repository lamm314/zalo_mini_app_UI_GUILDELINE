# Zalo Mini App UI Guideline

A reusable, industry-specific UI and UX guideline for commerce-oriented Zalo Mini Apps.

## Current Scope

- [Shared Design Foundations](docs/shared/FOUNDATIONS.md)
- [Beauty](docs/beauty/README.md)
- [Fashion](docs/fashion/README.md)

## Purpose

This repository is the single source of truth for product designers, product owners, frontend developers, backend developers, QA teams, and AI coding agents building Zalo Mini Apps.

Each industry may define its own visual language, catalog model, content hierarchy, filters, product variants, and business flows. However, all implementations must share the same foundations for spacing, touch targets, accessibility, interaction states, responsive behavior, safe areas, and backend-driven rendering.

## Core Rules

1. Do not copy iOS, Android, or desktop e-commerce interfaces literally. The experience must be designed for the Zalo Mini App environment.
2. Do not hardcode home-page sections, banners, categories, theme colors, promotional blocks, or section order.
3. Every data-driven screen must support loading, empty, error, retry, offline, and partial-data states.
4. Primary actions must remain reachable with one hand and must never be hidden by the bottom navigation, keyboard, or device safe area.
5. Reuse shared components and design tokens before creating industry-specific variants.
6. Prices, discounts, inventory, delivery expectations, return policies, and order statuses must be explicit and trustworthy.
7. Product imagery must be optimized for mobile bandwidth and progressive loading.
8. All user-facing documentation, component specifications, design tokens, and AI prompts in this repository must be written in English.

## Repository Structure

```text
docs/
├── shared/
│   └── FOUNDATIONS.md
├── beauty/
│   └── README.md
└── fashion/
    └── README.md
```

## Recommended Implementation Model

The Mini App should behave as a presentation shell. Content, catalog data, banners, navigation, feature flags, layout configuration, and theme settings should be delivered by backend APIs.

```text
Admin Portal
    ↓
Backend API + Database
    ↓
Layout Configuration + Catalog + User Data
    ↓
Zalo Mini App Renderer
```

## Status

Version **1.0** currently covers two initial commerce categories:

- Beauty and cosmetics
- Fashion and apparel

Additional industry guidelines should extend the shared foundations without redefining common interaction rules.