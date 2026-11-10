---
name: sp-seo
description: Write SEO titles and meta descriptions for the pages and entries of a Sitepremium site and hand them in as suggestions for the SEO queue, where a person accepts or rejects each one. Use when the person asks for SEO texts, meta titles or descriptions for a site that has the seo module.
---

# sp-seo: SEO titles and descriptions

## When to use

The person wants better titles and meta descriptions for the pages and entries of a site. Suggestions go to the SEO queue; people review each one in the panel and decide. The site needs the `seo` module. For changing the page content itself use `sp-edit`.

## What it reads

- `get_brief`: the business, its audience, the languages (one suggestion per page per language), the modules.
- The pages and entries to cover, with their ids and current titles and descriptions. A suggestion points to its target with a `ref`, `page:<id>:<locale>` or `entry:<id>:<locale>`, and those ids come from the site content (the brief and the content tools of the server, where available).
- `render_preview` for the visible text of a page (the `title` and the HTML of the rendered page).
- `get_source_scan` on a clone: the old titles and descriptions are a starting point.

## Steps

### 1. Context

`list_sites`, `get_brief`. Note the languages and the business. `start_work` (label: "SEO titles and descriptions"). Collect the list of pages and entries with their `ref` values. If you cannot get the ids, stop (see the stops).

### 2. Plan

Decide the scope (all pages, or the ones the person named) and the pattern: a title that says what the page is and for whom, the business name at the end if it fits; a description that promises a benefit and invites the click, in the language of the page.

### 3. Execution

For each page and language write a title (at most 120 characters, one line) and a description (at most 300 characters, one line, plain text; aim for about 155 so it is not cut in results). Submit with `submit_report`, `kind: "seo"`:

```json
{
  "suggestions": [
    {
      "ref": "page:11111111-1111-1111-1111-111111111111:ro",
      "title": "Dental clinic in Cluj | Clinica Zâmbet",
      "description": "Implants, braces and check-ups for the whole family. Book online in two minutes."
    }
  ]
}
```

- `ref` must match `page:<id>:<locale>` or `entry:<id>:<locale>` with a real id of this site.
- `title` and `description`: one line each, no line breaks, no HTML (no `<` or `>`), no other fields.
- A report that does not match is refused with every issue listed and nothing stored: fix it and submit again.

### 4. Verification

Each text is true for the page (read the page: do not promise what it does not say); each page has a unique title and a unique description; the language matches the `ref`; no keyword stuffing; no invented claims (prices, awards, numbers) that are not on the site; lengths are within limits.

### 5. Report

Tell the person how many suggestions you submitted, for which pages and languages, and that they accept or reject them in the SEO queue. `end_work`. Nothing is applied until a person accepts it.

## Checks before you report

- Every `ref` is a real target; titles and descriptions are unique and truthful.
- The server accepted the report.

## Stops and asks the human

- The site does not have the `seo` module: say so; the module is the person's decision.
- You cannot get the ids of the pages and entries: do not guess refs; say what is missing.
- A page has no content to base a description on: ask the person what it should promise.
- The lock is held by someone else (`site_locked`).
- Never `release` or `rollback`.

## Hard rules

- Release and rollback happen only at the explicit request of the person, in this conversation. Never call `release` or `rollback` on your own initiative, and never to "finish the job". If the person did not ask, say the draft is ready and let them publish it from the panel.
- Everything you write lands in the site draft. The live site never changes from your writes.
- One agent per site: take the lock with `start_work` before any write and give it back with `end_work` (also when you stop early). If `start_work` answers `site_locked`, stop and tell the person who holds it. Use `heartbeat` during long local work (the lock expires after 15 minutes without a call).
- If a tool you need is not available, say so plainly instead of working around it.
- Local exports (archives, WordPress dumps, files on the person's disk) are processed on the person's machine by you; upload only what the site publishes, through `upload_media` and the content tools. Nothing else leaves the machine.
