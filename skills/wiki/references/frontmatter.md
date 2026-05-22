# Frontmatter Schema

Every wiki page starts with flat YAML frontmatter. No nested objects. Obsidian's Properties UI requires flat structure.

---

## Universal Fields

Every page, no exceptions:

```yaml
---
type: <source|entity|concept|domain|comparison|question|overview|meta>
title: "Human-Readable Title"
created: 2026-04-07
updated: 2026-04-07
tags:
  - <domain-tag>
  - <type-tag>
status: <seed|developing|mature|evergreen>
related:
  - "[[Other Page]]"
sources:
  - "[[.raw/articles/source-file.md]]"
---
```

**status values:**
- `seed`: exists, barely populated
- `developing`: has real content, not yet complete
- `mature`: comprehensive, well-linked
- `evergreen`: unlikely to need updates

---

## Type-Specific Additions

### source

Add these fields after the universal fields:

```yaml
source_type: article    # article | video | podcast | paper | book | transcript | data
author: ""
date_published: YYYY-MM-DD
url: ""
confidence: high        # high | medium | low
key_claims:
  - "First key claim from this source"
  - "Second key claim"
```

### entity

```yaml
entity_type: person     # person | organization | product | repository | place
role: ""
first_mentioned: "[[Source Title]]"
```

### concept

```yaml
complexity: intermediate  # basic | intermediate | advanced
domain: ""
aliases:
  - "alternative name"
  - "abbreviation"
```

### comparison

```yaml
subjects:
  - "[[Thing A]]"
  - "[[Thing B]]"
dimensions:
  - "performance"
  - "cost"
  - "ease of use"
verdict: "One-line conclusion."
```

### question

```yaml
question: "The original query as asked."
answer_quality: solid   # draft | solid | definitive
```

### domain

```yaml
subdomain_of: ""        # leave empty for top-level domains
page_count: 0
```

---

## Vault-Specific Extensions

The universal and type-specific fields above are the plugin baseline — they apply to
every vault. A vault may also declare **its own additional frontmatter fields**. These
are deliberately *not* hardcoded here: page-creating skills read them, at write time,
from the vault's own `CLAUDE.md`.

### How a vault declares custom fields

In the vault-root `CLAUDE.md`, add a `## Custom Frontmatter` section containing a
table. Each row declares one field:

```markdown
## Custom Frontmatter

| Field      | Type   | Applies to        | Required | Allowed values                              | Open list | Description                          |
|------------|--------|-------------------|----------|----------------------------------------------|-----------|--------------------------------------|
| subtype    | string | concept           | optional | quote, lesson, practice, mindset, framework | no        | Sub-categorizes a concept page       |
| difficulty | string | concept, question | optional | easy, medium, hard                           | yes       | Rough difficulty; new levels allowed |
| reviewed   | date   | all               | optional | —                                            | —         | Date the page was last fact-checked  |
```

Column meaning:

- **Field** — the YAML key. Lowercase, no spaces (use underscores).
- **Type** — the value shape: `string`, `date`, `number`, `boolean`, `list`,
  `wikilink`, or `wikilink-list`. This tells the skill how to format the YAML —
  dates as `YYYY-MM-DD`, lists as `- item`, wikilinks quoted (per the Rules below).
- **Applies to** — `all`, or a comma-separated list of `type` values (`source`,
  `entity`, `concept`, `comparison`, `question`, `domain`). The field is added only
  to pages whose `type` matches.
- **Required** — `required` or `optional`. Required fields are populated on every
  applicable page; optional fields are added only when a sensible value exists.
- **Allowed values** — a comma-separated controlled vocabulary, or `—` when the
  field has no fixed value set.
- **Open list** — `yes` or `no`, meaningful only when **Allowed values** is a list.
  `no` = closed set: only the listed values may be used. `yes` = open set: the listed
  values are suggestions, and a new value may be created when none fit. Use `—` when
  there is no value list.
- **Description** — one line on what the field captures.

### How the skills honor it

Any skill that creates or updates a wiki page (`wiki-ingest`, `save`, `wiki` scaffold)
must:

1. Read the vault-root `CLAUDE.md` and look for a `## Custom Frontmatter` section.
   If absent, behave exactly as the baseline — custom fields are purely opt-in.
2. For each declared field whose **Applies to** matches the page's `type`, add the
   YAML key to the page's frontmatter, after the type-specific fields, formatted
   according to its **Type**.
3. Respect **Required**: always populate required fields; add optional fields only
   when a value is known.
4. Respect **Allowed values** + **Open list**: when **Open list** is `no`, use only a
   listed value; when `yes`, prefer a listed value but create a new one if none fit;
   when there is no list, any value of the right **Type** is fine.
5. On update, never strip an existing custom field even if it is no longer declared —
   surface it for the user instead.

This mechanism is fully vault-agnostic. No field name, type, or value list is baked
into the plugin; a vault changes its schema by editing its own `CLAUDE.md` alone.

---

## Rules

1. Use flat YAML only. Never nest objects.
2. Dates as `YYYY-MM-DD` strings, not ISO datetime.
3. Lists always use the `- item` format, not inline `[a, b, c]`.
4. Wikilinks in YAML fields must be quoted: `"[[Page Name]]"`.
5. Keep `related` and `sources` as wikilinks, not plain URLs.
6. Update `updated` every time you edit the page content.
7. Honor vault-specific custom fields declared in the vault's `CLAUDE.md` — see Vault-Specific Extensions above.
