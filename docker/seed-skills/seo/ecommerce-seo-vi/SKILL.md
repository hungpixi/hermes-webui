---
name: ecommerce-seo-vi
description: "Vietnamese ecommerce/resource SEO workflow for categories, products, templates, tools, course pages, schema, and faceted navigation."
---

# Ecommerce SEO VI

Use for ecommerce, template libraries, tools, courses, resource pages, or product-led content.

## Category/resource hub workflow
1. Identify commercial or resource intent.
2. Create category page with useful intro, filters, resource cards, FAQ, and guide links.
3. Control faceted URLs: index only filters with real search demand; noindex/canonical/robots carefully for low-value filters.
4. Add breadcrumbs and internal links from guides.
5. Add `CollectionPage`, `ItemList`, `BreadcrumbList` if useful.
6. Monitor index bloat.

## Product/template/course page workflow
1. Define what user gets.
2. Show preview/demo/screenshot if possible.
3. Explain who it is for, when to use, how to use.
4. Add comparison/FAQ and related resources.
5. Use schema only when the page truly represents that type:
   - `Product` for actual product.
   - `Course` for actual course.
   - `SoftwareApplication` for actual app/tool.
   - `CreativeWork` for templates/checklists.
6. Keep visible content and schema identical.

## Checks
- Category has indexable HTML content, not only JS cards.
- Product/resource title contains type + benefit.
- Price/availability/schema match visible content if product/course.
- No fake reviews or fake aggregateRating.
- Variant/canonical strategy clear.
- Pagination crawlable.
- Out-of-stock/old resources handled.

## Pitfalls
- Faceted navigation crawl traps.
- Thin category pages.
- Product schema on blog posts.
- Review/rating markup without real reviews.
- Duplicate descriptions copied from suppliers.
- Hiding key content behind forms.

## Verification
- Crawl category/filter URLs.
- Rich Results Test.
- GSC coverage stable, no index bloat.
- Commercial long-tail impressions grow.
- Resource/category pages receive contextual internal links.