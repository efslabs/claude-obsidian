# Wiki Modes

Seven modes cover the most common use cases. Pick the one that fits, or combine them.

---

## Mode A: Website / Sitemap

Use when: "build a sitemap wiki for my website", "map content gaps", "SEO audit wiki"

```
vault/
├── .raw/              # crawl exports, analytics, scraped pages, GSC data
├── wiki/
│   ├── pages/         # one note per URL: status, meta, content summary
│   ├── structure/     # site architecture, nav hierarchy, internal link map
│   ├── audits/        # content gaps, redirect needs, thin content flags
│   ├── keywords/      # keyword clusters, target page assignments
│   └── entities/      # brand, authors, topic hubs
├── _meta/
│   ├── index.md
│   └── log.md
└── CLAUDE.md
```

Frontmatter for `wiki/pages/` notes:
```yaml
---
type: page
url: "https://example.com/page-slug"
status: live          # live | redirect | 404 | stub | no-index
title: ""
h1: ""
meta_description: ""
word_count: 0
has_schema: false
indexed: true
canonical: ""
internal_links_in: 0
internal_links_out: 0
last_crawled: YYYY-MM-DD
tags: [page]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

Key wiki pages to create: `[[Site Overview]]`, `[[Navigation Structure]]`, `[[Content Gaps]]`, `[[Redirect Map]]`, `[[Keyword Clusters]]`

---

## Mode B: GitHub / Repository

Use when: "map my codebase", "architecture wiki for my repo", "understand this project"

```
vault/
├── .raw/              # README, git log exports, code dumps, issue exports
├── wiki/
│   ├── modules/       # one note per major module / package / service
│   ├── components/    # reusable UI or functional components
│   ├── decisions/     # Architecture Decision Records (ADRs)
│   ├── dependencies/  # external deps, versions, risk assessment
│   └── flows/         # data flows, request paths, auth flows
├── _meta/
│   ├── index.md
│   └── log.md
└── CLAUDE.md
```

Frontmatter for `wiki/modules/` notes:
```yaml
---
type: module           # module | component | decision | dependency | flow
path: "src/auth/"
status: active         # active | deprecated | experimental | planned
language: typescript
purpose: ""
maintainer: ""
last_updated: YYYY-MM-DD
linked_issues: []
depends_on: []
used_by: []
tags: [module]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

Key wiki pages to create: `[[Architecture Overview]]`, `[[Data Flow]]`, `[[Tech Stack]]`, `[[Dependency Graph]]`, `[[Key Decisions]]`

---

## Mode C: Business / Project

Use when: "project wiki", "competitive intelligence", "team knowledge base", "meeting notes"

```
vault/
├── .raw/              # meeting transcripts, Slack exports, docs, emails
├── wiki/
│   ├── stakeholders/  # people, companies, decision-makers
│   ├── decisions/     # key decisions with rationale and date
│   ├── deliverables/  # milestones, outputs, status tracking
│   ├── intel/         # competitor analysis, market research
│   └── comms/         # synthesized meeting notes, key threads
├── _meta/
│   ├── index.md
│   └── log.md
└── CLAUDE.md
```

Frontmatter for `wiki/decisions/` notes:
```yaml
---
type: decision         # stakeholder | decision | deliverable | intel | meeting | competitor
status: active         # active | pending | done | blocked | superseded
priority: 3            # 1 (highest) to 5 (lowest)
date: YYYY-MM-DD
owner: ""
due_date: ""
context: ""
tags: [decision]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

Key wiki pages to create: `[[Project Overview]]`, `[[Stakeholder Map]]`, `[[Decision Log]]`, `[[Competitor Landscape]]`

---

## Mode D: Personal / Second Brain

Use when: "personal second brain", "track my goals", "journal synthesis", "life wiki"

```
vault/
├── .raw/              # journal entries, articles, podcast notes, voice transcripts
├── wiki/
│   ├── goals/         # personal and professional goals with progress tracking
│   ├── learning/      # concepts being mastered, skill development
│   ├── people/        # relationships, shared context, follow-ups
│   ├── areas/         # life areas: health, finances, career, creative
│   └── resources/     # books, courses, tools worth referencing
├── _meta/
│   ├── index.md
│   ├── log.md
│   └── hot-cache.md   # ~500-word summary of most active context
└── CLAUDE.md
```

Frontmatter for `wiki/goals/` notes:
```yaml
---
type: goal             # goal | concept | person | area | resource | reflection
status: active         # active | paused | completed | abandoned
area: career           # health | career | finance | creative | relationships | growth
priority: 1
target_date: YYYY-MM-DD
progress: 0            # 0-100 percent
tags: [goal]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

Hot cache note: `_meta/hot-cache.md` is a ~500-word file Claude updates at the end of each session. It captures current focus areas, recent wins, and open threads. This prevents Claude from having to crawl the whole wiki to answer "where were we?".

Key wiki pages to create: `[[North Star]]`, `[[Weekly Review Template]]`, `[[Annual Goals]]`

---

## Mode E: Research

Use when: "research wiki on [topic]", "track papers I'm reading", "build a thesis"

```
vault/
├── .raw/              # PDFs, web clips, data files, raw notes
├── wiki/
│   ├── papers/        # paper summaries with key claims and methodology
│   ├── concepts/      # extracted concepts, models, frameworks
│   ├── entities/      # people, organizations, methods, datasets
│   ├── thesis/        # evolving synthesis: the "state of the field" pages
│   └── gaps/          # open questions, contradictions, research needed
├── _meta/
│   ├── index.md
│   └── log.md
└── CLAUDE.md
```

Frontmatter for `wiki/papers/` notes:
```yaml
---
type: paper            # paper | concept | entity | thesis | gap
status: summarized     # raw | summarized | synthesized | superseded
year: 2024
authors: []
venue: ""
key_claim: ""
methodology: ""
contradicts: []
supports: []
tags: [paper]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

Key wiki pages to create: `[[Research Overview]]`, `[[Key Claims Map]]`, `[[Open Questions]]`, `[[Methodology Comparison]]`

---

## Mode F: Book / Course

Use when: "companion wiki for a book", "course notes wiki", "as I read [title]"

```
vault/
├── .raw/              # chapter notes, highlights, exercises
├── wiki/
│   ├── characters/    # characters, personas, agents, experts (adapt to content)
│   ├── themes/        # major themes with supporting evidence
│   ├── concepts/      # domain-specific terms and frameworks
│   ├── timeline/      # plot structure, curriculum sequence, chapter map
│   └── synthesis/     # your own takeaways, questions, applications
├── _meta/
│   ├── index.md
│   └── log.md
└── CLAUDE.md
```

Frontmatter for `wiki/concepts/` notes:
```yaml
---
type: concept          # concept | character | theme | chapter | synthesis
status: developing     # stub | developing | mature
source_chapters: []
first_appearance: ""
tags: [concept]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

Key wiki pages to create: `[[Book Overview]]`, `[[Theme Map]]`, `[[Character / Expert Index]]`, `[[My Takeaways]]`

---

## Mode G: Guidebook / Personal Practice Library

Use when: "personal practice library", "guidebook of techniques", "my reference
library for productivity / mindfulness / mental health", "collect practices and
mantras I can reach for", "second brain for self-improvement content"

Mode G is for a curated library of *techniques, principles, frameworks, and
distilled teachings* that the user reaches for to handle a moment or a mental
state. It differs from Mode D (Second Brain) in two ways: it organizes pages by
**function** — what you *do* with the page — rather than by life area, and it
treats **scenario-based retrieval as a first-class folder**. The defining question
a Mode G vault answers is *"I feel X / I'm stuck on Y — what helps?"*

```
vault/
├── .raw/              # source transcripts, articles, clippings, PDFs, book notes
├── wiki/
│   ├── practices/     # things you DO — meditations, exercises, drills
│   ├── mindsets/      # principles & mantras — short, re-orienting lines
│   ├── frameworks/    # multi-step systems — dopamine menu, planning templates
│   ├── concepts/      # theory to understand — automatic thoughts, flow state
│   ├── lessons/       # distilled teachings from one source — a lecture, a chapter
│   ├── scenarios/     # FIRST-CLASS retrieval layer — one page per felt state
│   ├── sources/       # one summary page per ingested .raw source
│   ├── entities/      # teachers, authors, coaches, orgs, apps
│   ├── index.md       # master catalog
│   ├── hot.md         # ~500-word recent-context cache
│   ├── overview.md    # executive summary of the library
│   ├── log.md         # append-only operations log
│   └── meta/
│       └── topics.md  # living topics-slug vocabulary (see "Living topics" below)
├── _templates/        # one Templater file per type
├── _attachments/      # images / PDFs referenced by pages
└── CLAUDE.md
```

### Function folders, not type folders

The folder a page lands in is decided by its **primary function**:

| Folder | The page is... | Examples |
|---|---|---|
| `practices/` | something you *do* | body scan, control audit, loss rehearsal |
| `mindsets/` | a principle, mantra, or re-orienting line | "progress over perfection"; quotes; personal rules |
| `frameworks/` | a multi-step system or template | dopamine menu, daily-planning template |
| `concepts/` | theory to *understand* | automatic thoughts, flow state |
| `lessons/` | a distilled teaching from one source | a Waking Up session, a book chapter |
| `scenarios/` | an entry point for a felt state | "When I'm overwhelmed", "When I can't start" |
| `entities/` | a teacher, author, coach, org, or app | William B. Irvine, an ADHD coach |
| `sources/` | a summary of one raw source | the transcript or article a lesson came from |

A page that genuinely has two functions (e.g. both a mindset and a practice) picks
its **primary** function for the folder; the secondary is noted in prose or `tags:`.
`type:` stays a scalar — do not use arrays.

### Frontmatter for Mode G pages

```yaml
---
type: practice          # practice | mindset | framework | concept | lesson | scenario | source | entity
title: "Human-Readable Title"
topics: [slug, slug]    # lowercase-hyphen content slugs — the subject axis
status: seed            # seed | developing | mature | evergreen
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
state: "overwhelmed"                       # the felt state this page answers
triggers: ["too much to do", "spinning"]   # phrases / situations that map here

# lesson
author: "William B. Irvine"
series: "[[Stoic Path]]"

# entity
entity_type: person                        # person | organization | app
role: "ADHD coach"
```

`topics:` is the **subject** axis; the function folder is the **function** axis.
They are orthogonal — a `practice` and a `lesson` can share `topics: [stoicism]`,
and one slug query finds both.

### Living topics vocabulary

The `topics:` slug list is **not frozen**. It lives in `wiki/meta/topics.md`, a
plain list of slugs with one-line glosses. Scaffold seeds it with a starter set.
During ingest, when content does not fit an existing slug, propose a new slug and
append it to `topics.md`. During lint, flag near-duplicate slugs for merging. The
taxonomy improves as the library grows instead of going stale.

### The scenarios/ folder

`scenarios/` is the headline feature — the user's real entry point. Each page names
a felt state and routes to the practices/frameworks that help, ordered as a
first/then/if-still sequence. Example — `wiki/scenarios/When I'm Overwhelmed.md`:

```markdown
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
```

`wiki-query` should read `scenarios/` first when a question describes a state or
situation ("I'm scattered, what helps?"), the same way it reads `hot.md` first.

### Granularity / atomization

Atomize a source page **when it bundles units you would retrieve separately**. Test:
*would you ever want one item from this page without the others?* A single-idea page
stays whole regardless of length; a compiled list of unrelated tips becomes one page
per tip, because the `scenarios/` layer links to *specific* practices. Atomic pages
are retrieval targets; compiled lists are not. Propose splits for approval rather
than auto-splitting.

Key wiki pages to create: `[[Library Overview]]`, the `scenarios/` set, a curated
`[[Daily Drivers]]` shortlist, and `wiki/meta/topics.md`.

---

## Combining Modes

You can combine modes. Examples:

- "GitHub repo + research on the AI approach used" -> Mode B folders + Mode E papers/ folder
- "My SaaS business + second brain" -> Mode C intel/ + Mode D goals/
- "YouTube channel" -> Mode F (content as "book") + Mode E (research on topics covered)

When combining, keep folder names distinct. Don't merge `decisions/` from Mode B and Mode C into one folder.
