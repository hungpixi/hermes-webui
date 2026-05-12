# Functional MVP page batching

Use when improving an existing SaaS dashboard/MVP from reference screenshots where exact visual cloning is not required.

## User preference signal

- For this user, prioritize coded behavior over matching screenshot structure pixel-for-pixel.
- Reference screenshots are feature inventory: buttons, filters, drawers, pagination, export/share, AI actions, tables, statuses.
- Work in batches of 3 custom pages per lượt so progress is easy to review.
- Keep chat updates terse Vietnamese: what pages changed, what behavior added, lint/build, amended commit hash.

## Workflow

1. Pick 3 pages with most UI-only controls or highest demo value.
2. Inspect page/data files and identify unresponsive controls.
3. Wire broad MVP behavior before visual polish:
   - search text filters
   - select/dropdown filters
   - pagination using filtered slice
   - drawer/modal open/close
   - row actions selecting record and showing toast
   - export/share buttons producing download/copy/toast
   - AI buttons simulating async progress + completion toast
4. Prefer central templates for template-driven pages; patch custom pages locally.
5. Run `npm run lint && npm run build` from app directory.
6. Amend active MVP commit when user asked for amend workflow.
7. Report remaining untracked refs separately; do not add reference zips/assets unless asked.

## Template-driven pages

When many routes share one `PageTemplate`, upgrade the template once instead of coding every route separately:

- Add search/status/owner filters in the template.
- Add pagination over the filtered slice.
- Make table rows clickable and open a generic detail drawer.
- Put row-level actions inside the drawer (`Duyệt`, `Chạy AI`, etc.) and route them through the existing backend/toast action helper.
- Keep custom pages local when they already diverged from the template.

## Verification checklist

- Every visible primary/secondary action gives some response: state change, drawer, download, copied link, toast, selected row, or async status.
- Filters reset page to 1.
- Pagination counts reflect filtered rows, not hardcoded copy.
- For custom page audits, grep for button controls lacking `onClick` after wiring: `src/domains/**/*.tsx` should not have obvious `<button>` without response unless native form behavior is intended.
- After domain pages look done, run a broader UI-only sweep across `src/**/*.tsx` for dead anchors/buttons:
  - `<button(?![^>]*(onClick|type=))`
  - `href="#"`
  - placeholder click handlers like `onClick={() => {}}`
- Shared shell components (header, notification/user dropdowns, sidebar) count for “click anything responds”; replace `href="#"` with real routes or button-style dropdown items, and add toast/state feedback for notification rows.
- No secrets in logs or memory.
- Build passes before amend.
