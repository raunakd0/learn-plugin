# learn

A Claude Code plugin for study sessions on your own material. It probes what you already know,
builds a prerequisite map of the topics, teaches one topic at a time from your sources with a
diagram and reading pointers, and schedules spaced practice. An exam profile adds a syllabus with
marks weights, spacing that fits the exam date, timed past-paper checks, flashcards and a formula
sheet.

## Install

In a Claude Code session:

```
/plugin marketplace add raunakd0/learn-plugin
/plugin install learn@learn-plugin
```

## Use

Open Claude Code in the folder that holds your material and run `/learn:learn`. The first run asks
a few questions (profile, subject, goal, deadline) and creates a `Learning/` folder. Later runs read
that record and propose a plan for the sitting.

Modes: `/learn:learn` with no argument proposes a plan; or give one of `probe`, `map`,
`ingest [file]`, `teach [topic]`, `practice`, `check`, `cards`, `status`.

## Files it writes

All in `Learning/` in your folder, as plain Markdown you can edit:

- `CONFIG.md`: settings, sources, seed topics and known traps
- `MAP.md`: the topic map with a status per topic
- `claims.md`: what was taught, with sources and practice dates
- `ingested.md`: material files already read
- `flashcards.md` and `formulas.md`: written by `cards`
- `sessions/`: one file per mode run

In the exam profile, `SYLLABUS.md` in your folder holds the exam date, units and marks weights.

## Update

```
/plugin marketplace update learn-plugin
```
