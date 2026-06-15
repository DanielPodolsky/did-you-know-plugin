# Did You Know — Spinner Facts 🧠

Tired of staring at "Flibbertigibbeting…" while Claude Code thinks?

This plugin replaces Claude Code's loading-spinner text with **fun, true "Did You
Know" facts** about a topic *you* pick. Choose `basketball`, `roman empire`,
`space`, anything — and learn something every time you wait.

## What problem it solves

Developers stare at the loading spinner dozens of times a day and get nothing back
but a random gerund. This turns that dead time into a tiny, voluntary learning loop:
pick a topic you love, and every wait becomes a micro-fact.

## How it works (the architecture)

Claude Code lets you override the spinner text via the `spinnerVerbs` key in
`~/.claude/settings.json`. That array is **static** — the spinner just picks a
random entry per request, so it's free at display time.

The plugin ships a single slash command (`/did-you-know:dyk`). The command is a
**prompt**, not a script: when you run it, *your* Claude generates a batch of true,
spinner-sized facts about your topic and writes them into `spinnerVerbs`. No API
keys, no servers — it rides on the Claude you already have.

```
/did-you-know:dyk basketball
        │
        ├─ Claude generates 40 short, true facts (≤ 50 chars each)
        ├─ backs up ~/.claude/settings.json → settings.json.bak
        └─ rewrites only spinnerVerbs.verbs  (everything else preserved)
```

## Install

```bash
# Add this repo as a marketplace
claude plugin marketplace add DanielPodolsky/did-you-know-plugin   # GitHub shorthand
# or, for a local copy:
claude plugin marketplace add file:///path/to/did-you-know-plugin

# Install the plugin
claude plugin install did-you-know
```

## Use

```bash
/did-you-know:dyk basketball      # generate & install basketball facts
/did-you-know:dyk roman empire    # switch topics any time
/did-you-know:dyk basketball      # run again to regenerate if it feels repetitive
```

New facts take effect in a **fresh Claude Code session**, so restart to see them.
Your previous settings are always backed up to `~/.claude/settings.json.bak`.

## Design decisions

- **Facts capped at ~50 characters.** The spinner is one short line; longer strings
  get truncated with "…". The length cap is the single most important rule and is
  enforced in the command prompt.
- **`mode: "replace"`**, not `"append"` — so the spinner shows *only* your facts,
  not a mix with the built-in verbs.
- **Backup before write.** The command never overwrites `settings.json` without first
  copying it to `.bak`.
- **Truth over filler.** The command is instructed to verify uncertain facts with
  web search rather than guess.

## License

MIT
