# Changelog

*Log all rule changes, schema updates, and taxonomy revisions here. Most recent first.*

---

## Template v3 · September 25, 2026
- SKILL.md updated to v4: reads entries from a Notion database (one row per entry) instead of a single page, runs on chat trigger only, reads configuration from project instructions, compares entry numbers as numbers, decodes the corpus explicitly as UTF-8
- methodology.md updated to v1.1: raw data layer section, two new declared limitations
- taxonomy.md updated to v3: eight new tags, two definitions broadened
- schema.md and extraction-rules.md updated to v4: `taxonomy_version` field, one-tag-by-default rule, versioned re-tag procedure
- SKILL.md updated to v4.1: new beats carry `schema_version` v4 and `taxonomy_version` v3
- SKILL.md updated to v4.2: extraction commits to an integrity branch after code-level checks and opens a pull request; never commits to main

## Template v2 — June 26, 2026

- Schema updated to v3: added `emotion_valence` and `emotion_arousal` fields
- Beat definition updated from emotion-shift based to breach-based (Bruner/Burke)
- methodology.md added as new document
- extraction-rules.md updated to v3: anti-inference rules A through E added
- taxonomy.md updated to v2: full definitions, linguistic anchors, Silverstone theoretical anchor
- SKILL.md added: fully cloud-based Cowork extraction skill
- Sample corpus updated to v3 schema
- README updated: extraction skill section added, setup instructions added

## Template v1 — June 25, 2026

- Initial methodology release. Schema v2. Theme taxonomy v1.

---

*Add your own entries below as you iterate on the rules.*
