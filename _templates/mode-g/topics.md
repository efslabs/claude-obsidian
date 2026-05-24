---
type: meta
title: "Topics Vocabulary"
updated: 2026-05-24
tags:
  - meta
status: evergreen
---

# Topics Vocabulary

The controlled vocabulary for the `topics:` frontmatter field. Every content page
carries one or more of these slugs. This file is the **living source of truth** for
the vocabulary — it is meant to grow and be refined, not stay frozen.

## How this file is maintained

- **Slugs are lowercase, hyphen-separated.** `task-initiation`, not `Task Initiation`.
- **`topics:` is the subject axis.** The function axis is the `wiki/` folder a page
  lives in. The two are orthogonal.
- **Ingest may extend it.** When `wiki-ingest` processes a source whose content does
  not fit any existing slug, it proposes a new slug and appends it here (with a
  one-line gloss), then uses it on the new page.
- **Lint may consolidate it.** `wiki-lint` flags near-duplicate or barely-used slugs
  and proposes merges. Retired slugs are recorded under "Retired" below so old
  references can be understood.
- `tags:` is separate — that is workflow/lifecycle (`FAVORITE`, `daily-driver`), not
  subject. Do not put workflow markers here.

## Active slugs

Seeded from the guidebook starter set. Glosses are short on purpose.

- `acceptance` — allowing what is, rather than resisting it
- `adhd` — attention, executive function, neurology-tailored tactics
- `body-awareness` — somatic attention; noticing the body's signals
- `breathing` — breathwork and breath-based regulation
- `cbt` — cognitive behavioral therapy concepts and tools
- `cognitive-reframing` — changing the interpretation of a situation
- `communication` — expressing, listening, relating in words
- `dbt` — dialectical behavior therapy skills
- `discomfort` — tolerating and deliberately approaching discomfort
- `dopamine` — reward, motivation, stimulation management
- `emotional-regulation` — working with and steadying emotions
- `energy` — physical and mental energy management
- `exercise` — movement and physical activity
- `failure` — relating to setbacks and mistakes
- `focus` — sustaining attention on one thing
- `gratitude` — noticing and appreciating what is present
- `habits` — building and changing repeated behavior
- `identity` — who you are becoming; values-driven self-concept
- `journaling` — written reflection practice
- `meaning` — purpose, what matters, life direction
- `meditation` — formal, structured contemplative practice
- `mindfulness` — present-moment awareness as an attitude
- `momentum` — building and keeping forward motion
- `mortality` — awareness of finitude and death
- `organization` — ordering tasks, things, and information
- `prime-the-environment` — shaping surroundings to support behavior
- `procrastination` — avoidance of intended tasks
- `productivity` — broad output and work effectiveness
- `reflection-prompt` — a question used to prompt self-inquiry
- `relationships` — connection with other people
- `resilience` — recovering and adapting under stress
- `routines` — repeated daily structure and rituals
- `self-awareness` — knowing your own patterns and triggers
- `self-compassion` — treating yourself with kindness
- `sleep` — rest and sleep quality
- `stoicism` — stoic philosophy and practice
- `task-initiation` — getting started when starting is the hard part
- `task-management` — capturing, organizing, prioritizing what to do
- `time-management` — allocating time itself
- `values` — what you hold as important; principles to live by
- `visualization` — mental imagery as a practice
- `work` — the domain of one's job and working life

## Distinctions worth keeping clean

- `mindfulness` is the *attitude* of present-moment awareness; `meditation` is the
  *formal practice*. A page can carry both when a formal sit cultivates the attitude.
- `self-awareness` is knowing your patterns (introspective method); `identity` is
  who you are becoming (values-driven self-concept).
- `productivity` is the umbrella; `task-initiation` is starting, `task-management`
  is organizing the to-dos, `time-management` is allocating time itself.

## Retired

Slugs that were merged or dropped. Kept so old references stay interpretable.

- `somatic` — merged into `body-awareness` (2026-05-02, carried over from the
  guidebook-vault schema).
