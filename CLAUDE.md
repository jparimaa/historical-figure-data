# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

Data-only repository of historical figures, their descriptions, and relationships. Intended to be consumed as a git submodule. No application code.

## Data layout

All JSON lives in `data/`:

- `people.json` - array of person objects. Negative years are BCE. `hpi_score` is from the Pantheon dataset.
- `descriptions.json` - array of single-key objects `{ "<id>": { fields } }`. The file starts with `[  \n` (two spaces before the newline) and has no trailing newline; `add_description.py` preserves this exactly so diffs stay minimal.
- `relations.json` - array of edge objects. Edges are undirected in practice but direction encodes semantics (e.g. teacher -> student, patron -> artist). Only one edge per pair.
- `completed_relations.json` - flat array of person IDs whose relations have been checked (including people with zero qualifying connections).

## Scripts

All scripts in `scripts/` resolve paths relative to themselves (`parent.parent / "data"`), so run them from any directory. They all call `sys.stdout.reconfigure(encoding="utf-8")`.

| Script | Usage |
|---|---|
| `find_missing_description.py` | Prints the highest-HPI person still missing any required description field |
| `add_description.py <file>` | Merges one description entry into `descriptions.json`; validates required fields |
| `find_missing_relations.py` | Prints the highest-HPI person not yet in `completed_relations.json` |
| `find_overlaps.py <id>` | Lists all people with overlapping lifespans who have no existing relation to `<id>` |
| `check_relation.py <id1> <id2>` | Exits 0 and prints the relation if it exists, exits 1 and prints "Not found." otherwise |
| `add_relation.py <file>` | Appends one relation to `relations.json`; validates type and checks for duplicates |
| `append_completed.py <id>` | Appends one ID to `completed_relations.json`; validates against `people.json` |

## Rules

- `rules/descriptions.md` - style and length rules for each description field. `personality` is intentionally left empty (`""`) in the current dataset.
- `rules/display_name.md` - how to shorten names (prefer shortest unambiguous form).
- `rules/relations.md` - which connections qualify and the 12 allowed relation types. When in doubt, omit.

## Skills

Two Claude skills automate the main data-entry workflows:

- `/write-descriptions <count>` - fills in the next N missing descriptions, highest HPI first.
- `/write-relations <count>` - checks relations for the next N uncompleted people, highest HPI first.

Both work one person at a time, writing a temp file then immediately running the add script, to keep context small and diffs clean.
