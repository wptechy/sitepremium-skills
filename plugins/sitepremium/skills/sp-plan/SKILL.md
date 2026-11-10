---
name: sp-plan
description: Propose the page structure of a Sitepremium site (which pages, with titles, slugs and purpose) as a plan report for Setup, Structura, to be approved by a person. Use when the person asks for a site structure, sitemap or page plan, before building a new site or a redesign.
---

# sp-plan: propose the structure

## When to use

The person wants the pages of the site decided: for a new site from the brief, for a redesign that reorganizes the old site, or to check a clone's page list. The output is one plan report (Setup, Structura). A person approves the structure in the panel; you build only after that (`sp-build`). Never start building from a plan nobody approved.

## What it reads

- `get_brief`: the starting point, the business, the modules bought, languages, the goal of the site.
- `get_source_scan` (clone or redesign): the overview and its `pages` list (every scanned page, source path, title, number of sections), `menus` and `collections`.
- Existing analysis or research reports are shown in the brief when they were done; use them.
- The current draft (`list_files`) if there is one.

## Steps

### 1. Context

`list_sites`, `get_brief`. Note the goal of the site (contact, booking, selling), the modules (a shop, a blog, bookings add their own pages), the languages. `start_work` (label: "Propose the site structure").

### 2. Plan

Decide the principles: a clone keeps every scanned page (same paths in spirit; nothing of the old site is lost); a redesign keeps all the content but may merge, split or reorder pages for clarity and conversion; a new site starts from the business and its goal. Keep the list short and meaningful: each page has a purpose.

### 3. Execution

Build the page list: home first, then the pages by importance. Pages that come from collections (services, team, blog) are listed once as the listing page; their entries are content, not pages in the plan. Then submit with `submit_report`, `kind: "plan"`:

```json
{
  "summary": "How the structure is organized and why, plain text.",
  "pages": [
    {
      "title": "Home",
      "slug": "home",
      "purpose": "Main message, services, trust proof, call to contact."
    },
    { "title": "About us", "slug": "about-us", "purpose": "Who we are, team, story." }
  ]
}
```

- `summary`: required, at most 4,000 characters.
- `pages`: 1 to 100. `title` at most 200 characters; `slug` (optional) lowercase letters, digits and single hyphens, at most 120; `purpose` (optional) at most 1,000.
- Plain text, no HTML (no `<` or `>`), no other fields.
- A report that does not match is refused with every issue listed and nothing stored: fix it and submit again. Submitting again adds a new report; the panel shows the newest.

### 4. Verification

Compare the plan with the source scan (clone: every scanned page has its place; redesign: merged pages say where their content went) and with the modules and the goal in the brief. Check slugs are unique and valid, and that no page exists without a purpose.

### 5. Report

Tell the person the structure in a few lines, what you merged or added and why, and that it needs their approval in the panel (Setup, Structura) before the build. `end_work`. Nothing is applied by this report.

## Checks before you report

- Every page of a clone is accounted for; nothing of the old site is lost.
- Slugs are valid and unique; the home page is first.
- The server accepted the report.

## Stops and asks the human

- The goal or the scope of the site is not in the brief: ask the person.
- Choosing between `HOME` and `ALL` for the build is the person's decision: mention it; do not decide it here.
- The plan removes or merges pages the person did not mention: list them clearly for approval.
- The lock is held by someone else (`site_locked`).
- Never `release` or `rollback`.

## Hard rules

- Release and rollback happen only at the explicit request of the person, in this conversation. Never call `release` or `rollback` on your own initiative, and never to "finish the job". If the person did not ask, say the draft is ready and let them publish it from the panel.
- Everything you write lands in the site draft. The live site never changes from your writes.
- One agent per site: take the lock with `start_work` before any write and give it back with `end_work` (also when you stop early). If `start_work` answers `site_locked`, stop and tell the person who holds it. Use `heartbeat` during long local work (the lock expires after 15 minutes without a call).
- If a tool you need is not available, say so plainly instead of working around it.
- Local exports (archives, WordPress dumps, files on the person's disk) are processed on the person's machine by you; upload only what the site publishes, through `upload_media` and the content tools. Nothing else leaves the machine.
