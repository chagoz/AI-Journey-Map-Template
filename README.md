# AI Journey Map — Template

A methodology for documenting your own AI adoption as a structured field study.

**Fork this. Make it yours.**

---

## What this is

A research framework for tracking how your relationship with AI changes over time. Not a blog. Not a highlight reel. A longitudinal record, structured like user research, honest like a journal.

The methodology treats you as both researcher and subject. Every voice note, every frustration, every reframe gets processed into a structured beat record. Over time, patterns emerge that you could not have planned for.

The subject is the relationship between you and the technology. Not your productivity. Not your output. The relationship itself.

---

## How it works

1. Record voice notes whenever you have an AI interaction worth capturing, on commute, at your desk, wherever
2. The extraction skill runs daily against your voice note archive and produces structured beat records
3. Records accumulate in `data/corpus.json`, one record per emotional beat, never rewritten
4. At volume, patterns emerge: phase transitions, theme clusters, emotional arcs

The loop: AI documents the process of using AI.

---

## What you get

**A journey map:** chronological emotional curve across your adoption. Phases emerge from the data, not from narrative smoothing.

**A galaxy view:** theme-based clustering. No fixed axis. Navigate by affinity.

Both views read from the same corpus. One data layer, two renderings.

---

## Getting started

### Step 1 — Set up your voice note archive
Any tool works: Notion, Apple Notes, voice memo apps with transcription. The extraction skill needs plain text transcripts with dates. Keep entries raw, do not edit after recording.

### Step 2 — Read the docs
Read `docs/methodology.md` first. Then `docs/extraction-rules.md`. The methodology explains why the rules exist. The rules explain how to apply them. Both matter before you run a first extraction.

### Step 3 — Run your first extraction
Use Claude (or any capable LLM) with `docs/extraction-rules.md` as the system prompt. Feed it one voice note transcript. It outputs structured JSON beat records. Append them to `data/corpus.json`.

For best results, run inside Claude Code or Claude Cowork so the skill can read your local docs folder and write directly to your data folder.

### Step 4 — Set up a daily trigger
A scheduled task at 07:00 works well. The lag between recording and processing is intentional: it creates distance between raw voice and structured interpretation without requiring your involvement.

### Step 5 — Build the frontend
Lovable plus this corpus JSON produces a working journey map. Frontend templates coming soon.

---

## Sample data

`data/sample-corpus.json` contains 5 anonymised beats showing the schema in use. Real emotional registers, correctly tagged, no personal information. Use them to understand the field definitions before running your own extraction.

---

## File structure

```
data/
  corpus.json          — your live corpus (starts empty)
  sample-corpus.json   — 5 anonymised example beats

docs/
  methodology.md       — intellectual foundations and theoretical anchors
  extraction-rules.md  — mechanical rules governing extraction
  schema.md            — field-by-field reference
  taxonomy.md          — theme definitions, application rules, linguistic anchors
  changelog.md         — your rule changes, versioned
```

---

## The rules that matter most

**One beat = one breach.** A beat opens when the narrator's prior model of the AI tool is violated. Not when the emotion shifts. The breach is the unit.

**Phases are outputs, never inputs.** The `phase` field stays null until corpus density earns it. Do not declare a phase because it feels like one.

**Corpus is append-only.** Never rewrite existing records. What was extracted under the rules of a given day stays as extracted. Use `schema_version` to track rule changes over time.

**The scope test.** Before tagging any emotion: was AI a necessary condition for this feeling? If no, exclude it.

---

## Credits

Methodology designed by [Charline Vergoz](https://www.linkedin.com/in/charlinevergoz/) x Claude (Anthropic).
Original project: [github.com/chagoz/AI-Journey-Map](https://github.com/chagoz/AI-Journey-Map)
Initiated: June 2026.

*If you fork this and build something with it, share it. That is the point.*
