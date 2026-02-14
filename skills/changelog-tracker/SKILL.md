---
name: changelog-tracker
description: >
  Track Claude Code changelog updates with smart digest summaries.
  Use when the user asks about "changelog", "what's new in Claude Code",
  "обновления Claude Code", "новые версии", "что нового", "changelog Claude",
  "последние изменения", "release notes", or wants to review recent
  Claude Code releases. Fetches the official changelog, compares with
  last reviewed version, and provides concise summaries — either for the
  latest release or as an accumulated digest since last check.
---

# Changelog Tracker

Track Claude Code releases and get smart digest summaries of what changed since your last check.

## Source & State

- **Changelog URL:** `https://raw.githubusercontent.com/anthropics/claude-code/refs/heads/main/CHANGELOG.md`
- **State file:** `~/.claude/changelog-tracker-state.json`

State file format:
```json
{
  "last_reviewed_version": "1.0.40",
  "last_reviewed_date": "2026-02-14"
}
```

## Workflow

Follow these steps sequentially. Do NOT skip any step.

### Step 1: Fetch the changelog

Use **WebFetch** to retrieve the changelog:

- **URL:** `https://raw.githubusercontent.com/anthropics/claude-code/refs/heads/main/CHANGELOG.md`
- **Prompt:** `Extract ALL version entries from this changelog. For each version, return: version number, release date, and full content of all subsections (Features, Fixes, Breaking Changes, etc). Return them in order from newest to oldest. Be thorough — include every version entry present in the file.`

Parse the response to identify all versions with their dates and content.

### Step 2: Load state

Use **Read** to load `~/.claude/changelog-tracker-state.json`.

- If the file does **not** exist — this is a **first run**. Note this for Step 4.
- If the file exists — parse `last_reviewed_version` and `last_reviewed_date`.

### Step 3: Determine versions

- **Current version** = the first (topmost) version in the changelog
- **Last reviewed version** = from the state file, or "none" on first run

Compare the two to determine if there are new versions.

### Step 4: Ask the user

Use **AskUserQuestion** to present options based on context:

**Case A — State exists AND there are new versions:**

```
question: "Доступны обновления Claude Code. Какой режим просмотра?"
header: "Changelog"
options:
  - label: "Последняя (vX.Y.Z)"
    description: "Только изменения в текущей версии vX.Y.Z"
  - label: "Дайджест (vA.B.C → vX.Y.Z)"
    description: "Сводка всех изменений с момента последнего просмотра (N версий)"
```

Replace vX.Y.Z with the current version number, vA.B.C with last reviewed version, and N with the count of new versions.

**Case B — First run (no state file):**

```
question: "Это первый запуск changelog-tracker. Какой режим просмотра?"
header: "Changelog"
options:
  - label: "Последняя (vX.Y.Z)"
    description: "Только изменения в текущей версии vX.Y.Z"
  - label: "Последние 5 версий"
    description: "Дайджест за последние 5 релизов"
```

**Case C — No new versions (current == last reviewed):**

Do NOT use AskUserQuestion. Instead, directly inform the user:

> Новых изменений нет. Последняя проверенная версия — **vX.Y.Z** от DATE.

Then STOP. Do not proceed to Steps 5-6.

### Step 5: Analyze and present

Based on the user's choice:

**Mode 1 — Latest version only:**

Extract the section for the current version from the changelog data. Present a structured summary in Russian using this format:

```
## Claude Code vX.Y.Z (DATE)

### Новые возможности
- Feature 1 description
- Feature 2 description

### Исправления
- Fix 1 description
- Fix 2 description

### Breaking Changes
- (if any, otherwise omit this section)
```

Translate feature/fix descriptions into Russian for readability. Keep technical terms (API names, flags, CLI commands) in English.

**Mode 2 — Digest (multiple versions):**

Collect all versions from `last_reviewed_version` (exclusive) to `current_version` (inclusive). If first run with "last 5 versions" chosen, take the 5 most recent versions.

Do NOT dump a raw per-version list. Instead, synthesize a **consolidated digest**:

```
## Дайджест Claude Code: vA.B.C → vX.Y.Z (N версий)

### Ключевые новые возможности
- Most important new features across all versions (grouped thematically)

### Важные исправления
- Notable fixes that users should know about

### Breaking Changes
- (if any across the period, otherwise omit)

### Статистика
- Версий за период: N
- Период: DATE_FROM — DATE_TO
```

Prioritize significance over completeness. The user wants to quickly understand what matters, not read every minor tweak.

### Step 6: Update state

After presenting the summary, use **Write** to save the new state:

```json
{
  "last_reviewed_version": "CURRENT_VERSION",
  "last_reviewed_date": "YYYY-MM-DD"
}
```

Write to `~/.claude/changelog-tracker-state.json`.

Inform the user:

> Версия **vX.Y.Z** зафиксирована. При следующем запуске дайджест начнётся с этой точки.

## Edge Cases

1. **WebFetch fails:** Inform the user that the changelog could not be fetched. Suggest checking internet connection or trying again later. Do not update the state file.

2. **Changelog format changed:** If the changelog cannot be parsed into version entries, inform the user and show the raw URL so they can check manually.

3. **State file corrupted:** If the state file exists but cannot be parsed, treat it as a first run. Inform the user that the state was reset.

4. **Last reviewed version not found in changelog:** If the version from state no longer appears in the changelog (e.g., very old), fall back to showing the last 10 versions in digest mode and inform the user.
