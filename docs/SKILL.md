---
name: ai-journey-map-extraction
description: Extraction skill for the AI Journey Map project. Run this skill whenever new voice note entries need processing. Reads new rows from your Voice notes database in Notion, compares against the live corpus on GitHub, extracts structured emotional beats from unprocessed entries following the rules in the GitHub docs folder, and commits new beats to GitHub via the API. Runs on chat trigger only, for example "process new entries" or "run extraction".
---

# AI Journey Map: Extraction Skill v4

Fully cloud-based. Reads rules and corpus from GitHub. Reads entries from a Notion database. Writes beats back to GitHub via API. No local files required.

---

## Credentials and configuration

Read from project instructions:
- `GITHUB_TOKEN`: your personal access token with repo scope
- `GITHUB_REPO`: your repo name, format `username/repo-name`
- `NOTION_VOICE_NOTES`: the data source ID of your Voice notes database
- `NOTION_PROJECT_LOG`: the page ID of your project log

These are never written to any file or output. If any is missing, stop and ask for it.

---

## Source setup

**Rules and docs (raw GitHub):**
- Methodology: `https://raw.githubusercontent.com/{GITHUB_REPO}/main/docs/methodology.md`
- Schema: `https://raw.githubusercontent.com/{GITHUB_REPO}/main/docs/schema.md`
- Extraction rules: `https://raw.githubusercontent.com/{GITHUB_REPO}/main/docs/extraction-rules.md`
- Taxonomy: `https://raw.githubusercontent.com/{GITHUB_REPO}/main/docs/taxonomy.md`

**Corpus (GitHub API):**
- `https://api.github.com/repos/{GITHUB_REPO}/contents/data/corpus.json`

**Voice notes database (Notion):**
One row per voice note. The row's page content is the raw transcript. Rows are never edited after creation. Required properties:
- `Entry` (title): "Entry 01", "Entry 02"... Numbers only ever increase.
- `Date` (date): recording date
- `Type` (select): Entry or Addendum
- Optional: `Headline`, `Location`, `Parent entry` (relation, for addendums)

**Project log (Notion):**
- The page at `NOTION_PROJECT_LOG`

---

## Step 1: Read the rules

Fetch and read these four documents in order:

1. `methodology.md`: the intellectual foundation. Understand the beat definition, the hybrid emotion protocol, and the declared limitations.
2. `schema.md`: the field reference. Confirm field names, types, and constraints before extracting any record.
3. `extraction-rules.md`: the mechanical rules. Every field definition, every anti-inference rule, every edge case.
4. `taxonomy.md`: the theme taxonomy. Read the full definition and linguistic anchor for every tag before applying any.

Do not proceed until all four are read and confirmed. If any fetch fails, stop and log the failure.

---

## Step 2: Fetch the current corpus

Fetch the corpus via GitHub API:

```
GET https://api.github.com/repos/{GITHUB_REPO}/contents/data/corpus.json
Authorization: token {GITHUB_TOKEN}
Accept: application/vnd.github.v3+json
```

From the response:
- Decode the base64 `content` field as UTF-8 to get the JSON array
- Store the `sha` value, required for the write step
- Identify the last processed entry: parse the number from every `entry_ref` and take the highest, as a number, not as text
- Identify the next beat_id: parse the number from every `beat_id`, take the highest, add 1
- Note total beats in corpus

If the corpus is empty, start from Entry 01 and beat_001.

---

## Step 3: Read new entries from the Voice notes database

Use the Notion connector to query the database at `NOTION_VOICE_NOTES`.

List all rows. Parse the number from each row's `Entry` property as a number. Select every row with a number higher than the last processed entry from Step 2. Process them in ascending order.

For each selected row, fetch its page content. That content is the transcript. Addendum rows are processed as their own entry; `entry_ref` is the row's `Entry` value.

Read only. Never create, edit or delete rows.

If no new rows exist, skip to Step 8. Log: *No new entries to process. Corpus is current.*

---

## Step 4: Self-assessment declaration

Before extracting anything, output the mandatory declaration as defined in `extraction-rules.md` Step 3.

The declaration must include:
- Which entries will be processed
- Last processed entry
- Next beat_id
- One concrete example of each Rule A through E applied to the specific entries about to be processed

Do not begin extraction until this declaration is complete.

---

## Step 5: Extract beats

For each unprocessed entry, apply the full extraction rules from `extraction-rules.md`.

Key reminders:
- A beat opens on a breach of expectation, not an emotion shift
- The prior expectation must be stated or strongly implied within the entry itself
- Select the verbatim before writing the emotion label
- Derive `emotion_valence` and `emotion_arousal` from the label using the Russell grid, and verify consistency before finalising
- Apply the scope test to every field
- Apply Rules A through E throughout
- Set `schema_version` to the version of `extraction-rules.md` and `taxonomy_version` to the version of `taxonomy.md`

If an entry produces no beats, log: *Entry [ref]: no qualifying beats extracted.* Continue to next entry.

---

## Step 6: Commit updated corpus to GitHub

Take the existing corpus array from Step 2. Append the new beats. Do not reorder or modify existing records.

Encode the updated array as base64. Push via GitHub API:

```
PUT https://api.github.com/repos/{GITHUB_REPO}/contents/data/corpus.json
Authorization: token {GITHUB_TOKEN}
Accept: application/vnd.github.v3+json

{
  "message": "corpus: append [N] beat(s) -- [YYYY-MM-DD]",
  "content": "[base64-encoded updated corpus]",
  "sha": "[sha from Step 2]",
  "branch": "main"
}
```

Wait for confirmation. Store the commit URL from the response.

If the commit fails, stop. Log the failure. Do not retry automatically.

---

## Step 7: Output extraction note

- Entries processed: [list]
- Beats extracted: [count]
- Total corpus after commit: [new total] beats
- Commit URL: [url]
- Ambiguous labels: [if any, exact text only]
- Theme candidate text: [if any, exact text and beat reference only, no proposed label]

---

## Step 8: Log to the project log

Append a run entry to the changelog section of the page at `NOTION_PROJECT_LOG`.

**If new beats were extracted and committed:**
`[YYYY-MM-DD]` Extraction run complete. Entries processed: [list]. Beats extracted: [count]. Total corpus: [new total] beats. Commit: [URL].

**If no new entries were found:**
`[YYYY-MM-DD]` Extraction run. No new entries. Corpus unchanged at [total] beats.

**If extraction failed at any step:**
`[YYYY-MM-DD]` Extraction run failed at Step [N]. Reason: [error]. No changes made to corpus.

---

## Failure handling

**Missing configuration:** stop. Ask for the missing value.
**Any doc fetch fails (Step 1):** stop. Do not extract without the rules.
**Corpus fetch fails (Step 2):** stop. Cannot determine last processed entry safely.
**Database read fails (Step 3):** stop. Cannot extract without source data.
**GitHub commit fails (Step 6):** stop. Do not retry. Corpus is unchanged.
**Partial extraction:** commit what was extracted. Log both successes and empty entries.

---

## Output format reference

```json
[
  {
    "beat_id": "beat_001",
    "schema_version": "v4",
    "taxonomy_version": "v3",
    "entry_ref": "Entry 01",
    "date": "YYYY-MM-DD",
    "emotion": "cautious curiosity",
    "emotion_valence": 0.3,
    "emotion_arousal": 0.2,
    "pain_gain": "gain",
    "activity_type": "prompting",
    "action": "Ran a first structured prompt and received unexpectedly useful pushback.",
    "verbatim": "I wasn't sure what to expect. But it actually... pushed back. In a useful way.",
    "job_to_be_done": "Get honest external feedback on work without a human reviewer.",
    "complexity": 1,
    "subject": "self",
    "open_question": null,
    "phase": null,
    "themes": ["#tool-as-mirror", "#delegation"]
  }
]
```

---

*Based on the AI Journey Map extraction skill by Charline Vergoz. Original project: github.com/chagoz/AI-Journey-Map. September 2026, v4.1*
