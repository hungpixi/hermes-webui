---
name: entity-topical-seo-vi
description: "Build Vietnamese topical authority with entity research, pillar clusters, internal links, and E-E-A-T signals."
---

# Entity & Topical SEO VI

Use when a site must become known for a topic, not only rank one keyword.

## Workflow
1. Pick main entity and macro topic.
2. Collect related entities from SERP, PAA, Google AI Overview, Wikipedia/Wikidata when relevant, official docs, competitors.
3. Classify entities: concept, law, organization, person, tool, process, metric, product, audience.
4. Create topical map: hub → pillar → cluster → support → glossary.
5. Map one primary intent per URL.
6. Add definitions for important entities in-page or glossary.
7. Link entities internally with descriptive anchors.
8. Add `Person`, `Organization`, `Article`, `BreadcrumbList`, and `sameAs` only when real.
9. Add visible author, sources, date modified, editorial/legal caveat for YMYL-adjacent content.

## Content architecture
- Hub page: overview, roadmap, pillar links, latest updates, FAQ.
- Pillar page: broad answer, human opening, quick classification/table, framework, concrete case story, cluster links.
- Support page: one specific long-tail question, one clear decision rule, one real example.
- Glossary page: only for entities with enough depth, not thin one-paragraph pages.

## Trust and voice layer
- Build topical authority through recognizable experience, not only entity coverage.
- Add case stories where they clarify decisions: timeline, persona, channel, turning point, next action.
- Use source-backed claims for facts, but use lived examples to make page feel authored.
- Do not add fake reviewer, fake sameAs, fake credentials, or placeholder expert names.
- If expert review is missing, use an editorial note: `Bài viết chưa thay thế tư vấn chuyên môn. Khi có người kiểm tra chuyên môn độc lập, thông tin reviewer sẽ được bổ sung.`

## Checks
- Main entity defined in first section.
- Synonyms included naturally, e.g. English/Vietnamese variants.
- Every important entity has source or explanation.
- Pillar links down; support links back up; related pages link sideways.
- Breadcrumbs reflect structure.
- Orphan pages = 0.
- Click depth for key pages <= 3.
- Entity names consistent across title, headings, schema, internal anchors.

## Pitfalls
- Treating keywords as entities.
- Creating fake entities or fake sameAs.
- Linking every page to every page.
- Glossary spam.
- No author/reviewer/source trust.

## Verification
- Crawl internal link graph.
- Check GSC query spread across cluster.
- Inspect SERP for brand/topic association.
- Validate schema graph.
- Refresh map after Google shows new PAA or AI Overview angles.