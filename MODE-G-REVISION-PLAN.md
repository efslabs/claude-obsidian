# Mode G Revision Plan

Status: **proposal — no plugin files changed yet.** Awaiting approval.
Author: drafted with Emily, 2026-05-24.

## Why

Mode G was added recently to support migrating `guidebook-vault/` into a wiki-style
vault. A dry-run ingest of one pilot series (DBT Skills for Life, 15 files) exposed
the problem: Mode G's **eight content folders** (`practices`, `mindsets`,
`frameworks`, `concepts`, `lessons`, `scenarios`, `sources`, `entities`) push every
source to shatter. One DBT session ("What Skills") wanted to become a lesson + a
framework + three practices — 5 pages for one coherent teaching. The 15-file pilot
projected to **56 wiki pages**.

Root cause: `practice / framework / mindset / concept / lesson` are not different
*kinds of page* — they are different *facets of one page*. A folder is a hard
partition (a page lives in exactly one); that partition is what creates the
atomization pressure. The fix is to make function a frontmatter property, not a
directory, and to keep as folders only the things that are genuinely different page
*shapes*.

Every other mode (A–F) has 4–5 folders. Mode G being the 8-folder outlier was the
smell.

## What changes (decisions, all settled)

### 1. Folder structure: 8 content folders → 1

New Mode G layout — **four folders**:

```
vault/
├── .raw/              # immutable source documents
├── wiki/
│   ├── notes/         # ALL content — practices, frameworks, mindsets, concepts.
│   │                  #   type: frontmatter delineates. One page per coherent unit.
│   ├── scenarios/     # felt-state routers — "When I'm Overwhelmed"
│   ├── sources/       # one page per ingested source + series-level source pages
│   ├── entities/      # teachers, authors, books, apps, courses
│   ├── index.md  log.md  hot.md  overview.md
│   └── meta/
├── _templates/
├── _attachments/
└── CLAUDE.md
```

The four folders are now genuinely distinct page **shapes**, not flavors of content:
`notes/` = content, `scenarios/` = routers, `sources/` = provenance records,
`entities/` = people/things. Nothing fuzzy left to partition.

- **Themes** are not a folder — they are `tags:`. A Dataview query renders any
  theme hub with zero maintenance.
- **Resources** are not a folder — an undigested book/app/course is an `entity`
  (`entity_type: book | app | course`). Mode D needs `resources/` because Mode D
  has no `entities/`; Mode G does.

### 2. `type:` carries function (Option B — overload, no new field)

`type:` was already `practice | lesson | …` in Mode G; it just also got mirrored
into folders. Remove the folder mirroring and `type:` simply *is* the function.
No new `form:` field. No `#function/*` tags. `tags:` stay purely subject.

Content `type:` enum (pages in `notes/`):

| `type:` | The page is… | Test |
|---|---|---|
| `practice` | a drill you enact, with steps | something you *do* |
| `framework` | a named method with named parts | has a name you'd look it up by (TIPP, DEAR MAN, PLEASE) |
| `mindset` | a short mantra / principle / re-orienting line | a line you hold, not steps you run |
| `concept` | explanatory theory | something you *understand* |

Other page shapes keep their `type:`: `scenario`, `source`, `entity`, `meta`,
`overview`.

**`lesson` is retired** as a `type:` value. A page's `type:` reflects its
*function* — a Waking Up session that teaches a drill becomes `type: practice`; one
that explains a model becomes `type: concept`. "Came from one session" is recorded
by the page's `source:` link, not its type. "Show all teachings from series X" is a
query on `source:`, which is more useful than a `lesson` bucket anyway.

practice-vs-framework tiebreaker: **does it have a name you'd look it up by?**
"Paced breathing" → `practice`. "TIPP" → `framework`.

### 3. Granularity rule rewritten

Current rule ("compiled lists become one page per item") optimizes for a wiki of
independent web articles, not a curriculum. New rule:

> A page is one coherent thing you'd reach for as a unit. A named method with named
> parts (TIPP, DEAR MAN) is **one page** — the parts are sections. Split a part into
> its own page only when you would genuinely retrieve it on its own, independent of
> the method it came from.

No "AND large" gate (if a part is truly independently useful, size is irrelevant).
No ambiguous illustrative examples.

### 4. Templates: 5 Mode-G templates → 1

`_templates/mode-g/{practice,mindset,framework,lesson,scenario}.md` collapse to:

- `_templates/note.md` — one content template; `type:` field is filled in per page
  (`practice | framework | mindset | concept`). Body sections are generic enough to
  fit all four (What it is / How to use it / Notes / Sources).
- `_templates/scenario.md` — kept (genuinely different shape).
- Shared `concept.md`, `entity.md`, `source.md`, `comparison.md`, `question.md`
  stay as-is at `_templates/` root. (`concept.md` still used; Mode-G `concept`
  pages can use it or `note.md` — note.md preferred for consistency.)

The `_templates/mode-g/` subfolder is removed.

### 5. CSS variant simplified

`css-snippets.md` Mode G variant currently colors 8 folders. New variant colors 4
(`notes`, `scenarios`, `sources`, `entities`) + the 4 custom callouts (unchanged).

## Files to change in the plugin

| File | Change |
|---|---|
| `skills/wiki/references/modes.md` | Rewrite the `## Mode G` section (lines ~251–402): new folder map, `type:` table, retired `lesson`, new granularity rule, updated frontmatter block, updated key-pages list. |
| `WIKI.md` | Rewrite the `**Mode G**` block in §4.1a (line ~404): folder map + the one-line summary. |
| `skills/wiki/references/css-snippets.md` | Replace the `## Mode G Variant` section (line ~126): 4-folder CSS. |
| `skills/wiki/SKILL.md` | Line 131 mentions Mode G — verify wording still accurate (it is; no folder names cited). Likely no change. |
| `_templates/` | Delete `_templates/mode-g/`. Add `_templates/note.md`. Keep `_templates/scenario.md` (move up from mode-g/). |
| `CHANGELOG.md` | Add an entry noting the Mode G restructure. |

**Not changed:** `wiki-ingest` and `wiki-lint` skills are mode-agnostic — they
reference `type:` and `status:` generically, never Mode G's folder names. The DBT
ingest dry-run confirmed `wiki-lint`'s tag/status logic needs no Mode G specifics.

## Re-scaffold guidebook-wiki/

`guidebook-wiki/` is currently scaffolded to the OLD 8-folder Mode G and is empty of
content — so re-scaffolding is cheap and lossless. After the plugin revision:

1. Delete the 8 content folders + their `_index.md` files.
2. Create `wiki/notes/` with `wiki/notes/_index.md`. Keep `scenarios/`, `sources/`,
   `entities/`, `meta/`.
3. Rewrite `wiki/index.md` sections to match (`Notes`, `Scenarios`, `Sources`,
   `Entities` — drop the 5 function-folder sections).
4. Rewrite `wiki/overview.md` "How It's Organized" to describe the 4 folders.
5. Replace `.obsidian/snippets/vault-colors.css` with the 4-folder variant.
6. Update vault `CLAUDE.md` structure block + folder table.
7. Swap templates: remove `mode-g/`, add `note.md`.
8. `wiki/log.md` gets a new entry noting the re-scaffold.

End state: `guidebook-wiki/` correctly scaffolded to revised Mode G, still empty of
content.

## Revised DBT pilot projection

Under the new model the 15-file DBT pilot produces roughly:

- **~20 `notes/` pages** (was 53 across 5 folders). Breakdown:
  - 9 `type: framework` — the named acronyms: TIPP, ACCEPTS, DEAR MAN, GIVE, FAST,
    ABC, PLEASE, plus the What-Skills and How-Skills sets (each ONE page, parts as
    sections — not 8 pages).
  - ~5 `type: concept` — Wise Mind, Crisis Survival, Radical Acceptance, Model of
    Emotions, Three Interpersonal Goals (canonical, linked from many sources).
  - ~3 `type: practice` — standalone drills that earn their own page (Paced
    Breathing, Paired Muscle Relaxation, Build a Self-Soothe Box).
  - ~3 `type: mindset` — the few mantras genuinely reached for on their own;
    most fold into their parent note as a "Mantras" section.
- **1 series-level `sources/` page** — `DBT Skills for Life (Waking Up)` — records
  the whole course as provenance, links every note it produced, notes original
  transcripts live in `guidebook-vault`. (Replaces 15 per-file source stubs.)
- **3 `entities/` pages** — Shireen Rizvi, Elisha Goldstein, Marsha Linehan.
  Waking Up = entity too (4 total). Thich Nhat Hanh = in-prose mention.
- **0 `scenarios/`** — deferred to a dedicated pass after more series are migrated
  (scenarios cross-cut series).

≈ 25 pages from 15 files, vs. 56 under the old model. Atomic where atomicity earns
its keep (the 9 named methods you look up by name); consolidated everywhere else.

## Sequencing

1. **Approve this plan.**
2. Revise the 4 plugin files + templates + CHANGELOG.
3. Re-scaffold `guidebook-wiki/` to revised Mode G (dry-run first, then commit).
4. Re-run the DBT pilot ingest dry-run under the new rules → ~25-page plan.
5. On approval, commit the DBT ingest. Then proceed series-by-series through the
   rest of `guidebook-vault/`.

## Open / deferred

- **Scenario-building pass** — after the bulk of `guidebook-vault/` is migrated,
  a dedicated pass builds `scenarios/` pages routing across all series.
- **`notes/` browse scalability** — with content unfoldered, `notes/` will hold
  100+ files post-migration. Navigation then depends on `index.md`, the
  `type:`-driven dashboard, and `scenarios/`. This is by design; flagged so it's a
  conscious choice, not a surprise.
