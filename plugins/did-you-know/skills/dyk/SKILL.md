---
description: Replace Claude Code's loading-spinner text with fun "Did You Know" facts about a topic of your choice. Use when the user runs /dyk with a topic, or asks to change/refresh their spinner facts.
argument-hint: [topic]
allowed-tools: Read, Write, Edit, Bash, WebSearch
disable-model-invocation: false
---

# Did You Know — Spinner Fact Generator

Your job: turn the chosen topic into a batch of short, true, genuinely-fun
"Did You Know" facts, then install them as the user's Claude Code loading-spinner
text by writing to their `settings.json` (`spinnerVerbs`).

The topic the user chose is: **$ARGUMENTS**

## Step 0 — Validate the topic

If `$ARGUMENTS` is empty, do NOT proceed. Tell the user the usage:
`/dyk <topic>`  (e.g. `/dyk basketball`, `/dyk roman empire`, `/dyk space`)
and stop.

## Step 1 — Generate the facts

Produce **exactly 40** "Did You Know" facts about the topic. Rules — follow ALL of them,
because these strings render in a narrow one-line spinner:

1. **Length is sacred.** Each fact MUST be **≤ 50 characters** including spaces.
   The spinner truncates anything longer with "…", which ruins the effect.
   Count characters. If a fact is too long, tighten it or pick a different one.
2. **True, not generic.** Each fact must be accurate and specific — a real "huh, neat"
   moment, not filler like "Basketball is popular". If you are unsure whether a
   surprising stat/date is correct, either pick a fact you ARE sure of, or use the
   WebSearch tool to verify before including it. We do not ship guesses.
3. **No "Did you know" prefix inside the string.** It eats your 50 characters and the
   spinner context already implies it. Lead with the fact itself.
   - Good:  `Hakeem averaged a near quadruple-double 🏀`
   - Bad:   `Did you know that Hakeem Olajuwon once...` (too long, wasted prefix)
4. **Variety.** Mix angles: records, origins, weird trivia, numbers, people, "firsts".
   No two facts should feel like the same fact reworded.
5. **One tasteful emoji** related to the topic is allowed at the end (optional). Keep it to one.
6. **No duplicates.** 40 distinct facts.

If the user has run this before and asks to regenerate because it "feels repetitive",
deliberately choose a DIFFERENT, more obscure set than the obvious top-10 facts.

## Step 2 — Back up current settings (safety first)

Before touching anything, if `~/.claude/settings.json` exists, copy it to
`~/.claude/settings.json.bak` so the user can always roll back. Use Bash:
`cp ~/.claude/settings.json ~/.claude/settings.json.bak`

## Step 3 — Install the facts into settings.json

1. Read `~/.claude/settings.json`. If it does not exist, you will create a minimal one
   containing only the `spinnerVerbs` key.
2. Set the `spinnerVerbs` key to:
   ```json
   "spinnerVerbs": {
     "mode": "replace",
     "verbs": [ ...your 40 facts... ]
   }
   ```
3. **Preserve every other key in the file exactly as-is** (permissions, statusLine,
   enabledPlugins, theme, etc.). Only `spinnerVerbs` changes. Prefer editing just that
   key. The final file MUST be valid JSON — you are responsible for correct escaping.
4. Write the file back.

## Step 4 — Confirm

Tell the user:
- ✅ that their spinner now serves up `<topic>` facts,
- show **5 sample facts** from the batch so they get a taste,
- note: **new facts take effect in a fresh Claude Code session** — they may need to
  restart to see them,
- remind them they can run `/dyk <topic>` again any time to regenerate or switch topics,
  and that a backup of their previous settings is at `~/.claude/settings.json.bak`.

Keep the confirmation short and upbeat.
