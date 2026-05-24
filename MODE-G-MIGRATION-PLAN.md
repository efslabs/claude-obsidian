# Mode G — Guidebook Migration Plan

Draft plan. Planning artifact only — nothing here is implemented yet.

Owner: Emily · Date: 2026-05-24 · Status: reviewed — open questions resolved

---

## 1. The problem this solves

The `guidebook-vault` is a mature, hand-curated personal reference library (345 md
files, 516 wikilinks, a 4-axis MOC layer). It is well-built — and largely unused.
The reason is not content quality; it is **activation cost**. Finding the right
practice means opening Obsidian and navigating MOCs by hand. That friction is enough
to stop it being used.

The goal is therefore not to *preserve* the existing vault. It is to migrate the
content into a wiki an agent can retrieve from on the user's behalf, organized by
**what a page is for** rather than by abstract type. The end state should look
*somewhat* like the current vault — same content, recognizably "guidebook-shaped" —
but restructured for retrieval.

The vehicle is a new plugin scaffold mode: **Mode G — Guidebook / Personal Practice
Library**, added to `skills/wiki/references/modes.md` alongside the existing six
modes (A–F). Re-ingesting the vault under Mode G produces the target structure.

### Why not the paths considered earlier

- **New `guidebook-wiki` dir, stock scaffold + re-ingest** — rejected. Stock modes
  organize by generic type (`concepts/`, `entities/`). Re-ingesting under them
  discards the guidebook's function-based vocabulary (`practice`, `mindset`,
  `framework`, `lesson`) for a *less* expressive ontology.
- **Scaffold `wiki/` inside guidebook-vault, gradually move files to `.raw/`** —
  rejected. Same schema loss, plus a months-long half-migrated state where `pages/`
  and `wiki/` coexist and MOCs point at a moving target.
- **Retrieval overlay, no migration** — rejected by the user. It is a bolt-on; it
  leaves the plugin not natively understanding guidebook content.

Mode G is the chosen path: bake the guidebook ontology into the plugin so ingest
*natively* classifies content as practices, mindsets, frameworks, lessons.

---

## 2. What Mode G is

A 7th wiki mode. Where Modes A–F target websites, repos, businesses, second-brains,
research, and books, **Mode G targets a personal practice/reference library** — the
case where the user collects techniques, mantras, frameworks, and distilled
teachings, and needs to retrieve the right one *for a given moment or state*.

Defining trait: Mode G organizes pages by **function** (what you do with the page),
and it makes **scenario-based retrieval a first-class folder**, not just a MOC.

### Proposed folder structure

```
vault/
├── .raw/                 # immutable sources: transcripts, articles, clippings, PDFs
├── wiki/
│   ├── practices/        # things you DO — meditations, exercises, drills
│   ├── mindsets/         # principles & mantras — short, re-orienting lines
│   ├── frameworks/       # multi-step systems — dopamine menu, planning templates
│   ├── concepts/         # theory to understand — automatic thoughts, flow state
│   ├── lessons/          # distilled teachings from a source — Waking Up sessions
│   ├── scenarios/        # FIRST-CLASS retrieval layer — "when I'm overwhelmed"
│   ├── sources/          # one summary page per ingested .raw source
│   ├── entities/         # teachers, authors, coaches, orgs, apps
│   ├── index.md          # master catalog
│   ├── hot.md            # ~500-word recent-context cache
│   ├── overview.md       # executive summary of the library
│   └── log.md            # append-only operations log
├── _templates/           # one Templater file per content type
├── _attachments/         # images / PDFs referenced by pages
└── CLAUDE.md             # vault instructions (Mode G variant)
```

Mapping from the existing guidebook-vault `type:` values:

| guidebook-vault `type:` | Mode G folder | Notes |
|---|---|---|
| `practice` | `practices/` | direct |
| `mindset`, `quote`, `rule` | `mindsets/` | quotes/rules are short re-orienting lines — same shelf |
| `framework`, `list` | `frameworks/` | a compiled `list` usually *is* a loose framework; atomize where it bundles unrelated items (see §4) |
| `concept` | `concepts/` | direct |
| `lesson` | `lessons/` | direct |
| `person` | `entities/` | `entities/` also absorbs orgs/apps (Waking Up, Headway); keeps the plugin's stock vocabulary |
| `topic-moc`, `nav-moc`, `series-index` | (regenerated) | not migrated as content — see §5 |
| `journal`, `reflection`, `coaching-notes` | **out of scope** | stays private; see §6 |
| `reference`, `inbox` | `.raw/` or `sources/` | references become source pages; inbox is intake |

### Frontmatter for Mode G pages

Mode G keeps the guidebook's better ideas and the plugin's required universals.
Proposed schema, to be documented in the Mode G section of `modes.md`:

```yaml
---
type: practice          # practice | mindset | framework | concept | lesson | scenario | source | entity
title: "Human-Readable Title"
topics: [slug, slug]    # KEPT from guidebook — lowercase-hyphen content slugs
status: seed            # seed | developing | mature | evergreen  (plugin's scale)
source: book            # waking-up | masterclass | book | app | podcast | coach | article | personal | unknown
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: []                # workflow/lifecycle only — FAVORITE, daily-driver
related:
  - "[[Other Page]]"
sources:
  - "[[.raw/articles/source.md]]"
---
```

Type-specific additions:

```yaml
# scenario
state: "overwhelmed"          # the felt state this page answers
triggers: ["can't start", "too much to do"]
# lesson
author: "William B. Irvine"
series: "[[Stoic Path]]"
# entity
entity_type: person          # person | organization | app
role: "ADHD coach"
```

Design decisions baked in:

- **`topics:` survives — and stays flexible.** It is the guidebook's strongest
  feature: atomic, dataview-friendly, drives the Obsidian graph. Folders give
  *function*; `topics:` gives *subject*. Two orthogonal axes, both useful. The
  vocabulary is **not frozen in `modes.md`** — it lives in a single living file,
  `wiki/meta/topics.md` (a starter slug list seeded from the guidebook's existing
  set). `wiki-ingest` reads it, and when ingested content does not fit an existing
  slug, ingest *proposes a new one* and appends it. `wiki-lint` flags near-duplicate
  slugs for merging. The taxonomy improves as the vault grows instead of going
  stale — the same "living schema" posture the guidebook's `yaml-schema.md` already
  intends, but enforced by the skills rather than by hand.
- **`type:` becomes a scalar**, not the guidebook's array. The plugin's index, lint,
  and ingest all assume scalar `type:`. A page that is genuinely both a mindset and a
  practice picks its *primary* function for the folder and notes the secondary in
  `tags:` or prose. (Open question O-1 below.)
- **`status:` adopts the plugin's `seed|developing|mature|evergreen`.** The
  guidebook's `draft|complete|needs-review|stub` was a *migration* lifecycle; once
  migrated, the plugin's content-maturity scale is the right one. The 97 current
  `needs-review` files become `developing` or `seed`.
- **`written-by:` is dropped.** Post-migration, most pages are agent-touched; the
  field stops being meaningful. `source:` (origin) is kept.

---

## 3. Mode G scaffold behavior

When `/wiki` runs and the user describes a practice/reference library, the wiki
skill picks Mode G and scaffolds:

1. The 8 `wiki/` content folders above, each with an `_index.md`.
2. `index.md`, `hot.md`, `overview.md`, `log.md`, and `meta/topics.md` (the living
   topics-slug vocabulary, seeded from the guidebook's starter set).
3. `_templates/` — one Templater file per type (practice, mindset, framework,
   concept, lesson, scenario, source, entity).
4. `.obsidian/snippets/vault-colors.css` — Mode G colour scheme. Suggested: one
   colour per function folder so the graph view reads at a glance.
5. A Mode-G variant of the vault `CLAUDE.md`.

Scaffold honors the existing `--dry-run` mode (stdout-only, zero filesystem
residue) exactly as Modes A–F do. No new scaffold mechanics — Mode G is *data*
(a 7th entry in `modes.md`), not new code.

---

## 4. Re-ingest strategy

The current vault becomes the source corpus. High-level flow:

1. **Copy, don't move.** `guidebook-vault` stays intact and untouched as a safety
   net. The migration produces a *new* vault directory. (Path TBD — open question
   O-2; not inside the plugin repo, and not inside the live guidebook-vault.)
2. **Stage `.raw/`.** Current `pages/` and `inbox/` content is copied into the new
   vault's `.raw/` as the immutable source layer. `_transcripts/` also moves into
   `.raw/`.
3. **Batch-ingest under Mode G.** `wiki-ingest` reads each `.raw` page and writes the
   classified `wiki/` page. Because the source already has clean frontmatter,
   classification is mostly mechanical: `type: [practice]` → `wiki/practices/`.
4. **Atomization pass (see below).**
5. **Generate the navigation layer last** — `index.md`, `_index.md` sub-indexes,
   scenario pages, and any regenerated MOCs are built *from* the migrated pages, so
   they never point at a moving target.

This is a large batch ingest (≈230 content pages). The plugin's batch-ingest flow
already prescribes check-ins every 10 sources and a cross-reference pass at the end;
that applies here.

### Atomization rule

Atomize a source page **when it bundles units you would retrieve separately**. Test:
*would you ever want one item from this page without the others?*

- `loss-rehearsal.md` — one practice → **keep whole**.
- `ADHD habits and screenshots.md` (539 lines, bundles BROOKE'S ADVICE /
  @ElizaBeaHD / 14-day-minimalism / How-To-Stop) — four unrelated units →
  **split into four pages**.
- Pages already flagged `review-note: "Phase 7 atomization candidate"` → split.

Rationale: the `scenarios/` layer links to *specific* practices. A practice buried
mid-list in a long compiled page is not a clean retrieval target. Atomic pages are
retrieval targets; compiled lists are not. Genuinely single-idea pages are left
alone regardless of length.

`wiki-ingest` should surface proposed splits for approval rather than auto-splitting
(consistent with the guidebook's "surface, don't auto-resolve" posture).

### The scenarios/ layer

`scenarios/` is the headline feature — the user's actual entry point. It is built
from the existing `By Scenario` and `Daily Drivers` MOCs, but promoted from MOC
*navigation* to first-class *content*: one page per state.

Example — `wiki/scenarios/Overwhelmed.md`:

```yaml
---
type: scenario
title: "When I'm Overwhelmed"
state: overwhelmed
triggers: ["too much to do", "can't think straight", "spinning"]
topics: [emotional-regulation, productivity]
status: developing
created: 2026-05-24
updated: 2026-05-24
---

# When I'm Overwhelmed

First reach: [[Control Audit]] — separate what's yours from what isn't.
Then: [[Brain Dump]] — get it out of your head and onto paper.
If still spinning: [[TIPP]] — physiological reset.

> [!key-insight]
> The move is to *narrow*, not to *solve*. Pick the smallest next action.
```

Scenario pages are what an agent (or a future scheduled task) queries to answer
"I'm scattered, what helps?" — `wiki-query` reads `scenarios/` first, exactly as it
reads `hot.md` first today.

---

## 5. The navigation layer (MOCs)

The existing vault has 33 MOC files. Under Mode G these are **not migrated as
content** — most are regenerated:

- **Topic MOCs** (`Stoic Philosophy & Resilience`, etc.) → regenerated by
  `wiki-lint` as dataview-driven pages, or by an `_index.md` per `topics:` slug.
  They become *derived artifacts*, not hand-maintained files.
- **`By Scenario`, `Daily Drivers`** → become the `scenarios/` folder (§4).
- **Series indexes** (`WU - Stoic Path`, etc.) → `lessons/` sub-grouping +
  `series:` frontmatter; index regenerated.
- **`dv_*` dataview MOCs** → fold into `wiki/meta/dashboard.md` (plugin standard).
- **`Glossary`** → regenerated from `concept` pages.

Net effect: the navigation layer stops being hand-maintained drift-prone files and
becomes generated output. This is the single biggest *improvement* over the current
vault — the Phase-5 MOC work the user did by hand becomes automatic.

---

## 6. Scope boundaries

- **`journal/` is out of scope.** 61 files, privacy-walled. Not ingested, not
  copied, not read. The migration is content-library only.
- **`coaching-notes/` is out of scope** for now — dated personal session notes,
  closer to `journal/` than to reference content. Can be revisited.
- **Scheduled surfacing / proactive delivery is explicitly out of scope.** It was
  raised only to motivate *why* agent-retrieval matters. Mode G makes the vault
  retrievable; what gets built on top later is a separate effort.
- The existing `guidebook-vault` is **not modified**. It remains as-is until the
  user is satisfied with the migrated result and chooses to retire it.

---

## 7. Deliverables

For this round, the concrete build is small:

1. **Mode G section in `skills/wiki/references/modes.md`** — folder tree,
   frontmatter schema, type mappings, key pages. Pure documentation/data; the
   scaffold skill already knows how to consume a mode.
2. **Mode G `vault CLAUDE.md` template** — a guidebook-flavored variant of the
   template in `skills/wiki/SKILL.md`.
3. **Mode G `_templates/`** — Templater files for the 8 types.
4. **Mode G CSS snippet** — colour scheme for the function folders.
5. **`wiki/meta/topics.md` seed** — the living topics-slug vocabulary, seeded from
   the guidebook's existing starter set; read and appended-to by ingest/lint.

Possible follow-on (flagged, not committed):

5. **`wiki-ingest` Mode-G awareness** — teach ingest to classify into guidebook
   types and propose atomization splits. May be needed for the re-ingest to be
   smooth rather than manual.
6. **A `wiki-adopt` / "ingest-vault" skill** — a dedicated skill that takes an
   *existing* vault, inspects its frontmatter and folder layout, and runs the whole
   §4 migration as one guided operation (stage `.raw/`, batch-ingest, atomize,
   generate navigation). The user flagged this as "we may want an ingest-vault skill
   too." It is the natural home for the re-ingest logic; without it, the migration
   is a long manual batch-ingest. **Recommendation:** design Mode G first, do a
   pilot migration of ~20 pages manually, and only then decide whether to build the
   skill — the pilot will show how much of the migration is mechanical enough to
   automate.

---

## 8. Resolved decisions

All resolved with Emily on 2026-05-24:

- **O-1 — Multi-type pages → resolved (a).** A page with more than one function
  picks its *primary* function for the folder; secondary function noted in prose or
  `tags:`. The plugin keeps scalar `type:`; no array-awareness work needed.
- **O-2 — Location → resolved.** The migrated vault is a **new sibling directory,
  `guidebook-wiki/`**, next to `guidebook-vault`. `guidebook-vault` stays untouched
  as the safety net.
- **O-3 — Pilot → resolved.** The **stoicism cluster** (every page whose `topics:`
  includes `stoicism`, ~15 pages spanning practices, mindsets, lessons, and a
  scenario) is the pilot, migrated by hand under Mode G before the full run.
- **O-4 — `concepts/` vs `mindsets/` boundary → resolved.** Folder is decided by
  *primary function*; borderline pages are resolved case-by-case during ingest.
- **O-5 — Follow-on skills → resolved (wait).** No commitment now to the
  `wiki-adopt` ingest-vault skill. Decide after the pilot, per §7.

---

## 9. Suggested sequence

1. Resolve O-1 through O-5.
2. Write the Mode G section of `modes.md` + the CLAUDE.md template + templates + CSS.
3. Scaffold a Mode G vault at the chosen location (dry-run first).
4. Pilot-migrate the stoicism cluster (~15 pages) by hand under Mode G.
5. Review the pilot together — does the structure feel right, does retrieval work?
6. Decide on the `wiki-adopt` skill based on what the pilot showed.
7. Full re-ingest; regenerate the navigation layer; validate; retire the old vault.
