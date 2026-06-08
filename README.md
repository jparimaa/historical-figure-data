# historical-figure-data

A data-only repository of historical figures, their descriptions, and the relationships between them. Intended to be used as a git submodule in other projects.

## Data files

All files live in `data/`.

| File | Contents |
|---|---|
| `people.json` | One record per person: `id`, `name`, `display_name`, `birth_year`, `death_year`, `occupation`, `birth_country`, `hpi_score` |
| `descriptions.json` | Per-person text: `short_description`, `long_description`, `why_they_matter`, `personality` |
| `relations.json` | Directed edges: `source_id`, `target_id`, `type`, `strength`, `confidence`, `reason` |
| `completed_relations.json` | Tracks which person IDs have had their relations checked |

Negative years are BCE. `hpi_score` is the Historic Popularity Index from [pantheon.world](https://pantheon.world).

## Relation types

`teacher` `mentor` `collaborator` `rival` `ally` `enemy` `patron` `family` `spouse` `romantic` `friend` `predecessor`

See `rules/relations.md` for the full rules governing which connections qualify.

## Scripts

Helper scripts in `scripts/` are used by the Claude skills to add data incrementally without loading large files into context.

| Script | Purpose |
|---|---|
| `find_missing_description.py` | Prints the highest-HPI person still missing a description |
| `add_description.py <file>` | Merges a new description entry into `descriptions.json` |
| `find_missing_relations.py` | Prints the next person whose relations have not been checked |
| `find_overlaps.py <id>` | Lists people whose lifespan overlaps the given person and who have no existing relation |
| `check_relation.py <id1> <id2>` | Checks whether a relation between two people already exists |
| `add_relation.py <file>` | Appends a new relation entry to `relations.json` |
| `append_completed.py <id>` | Records a person ID as completed in `completed_relations.json` |

## Using as a submodule

```bash
git submodule add <repo-url> historical-figure-data
git submodule update --init
```

## Data and attribution

People, occupation, birth and death years, and HPI score come from the [Pantheon dataset](https://pantheon.world/data/datasets) (with small modifications).

> Yu, A. Z., et al. (2016). Pantheon 1.0, a manually verified dataset of globally famous biographies. *Scientific Data* 2:150075. doi: 10.1038/sdata.2015.75

Pantheon by Datawheel is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).

**AI-generated content.** Display names, descriptions and the relationships between people are mostly AI-generated (Claude Code by Anthropic). They likely contain historical errors, many of the claims are debatable, and the data is incomplete: many descriptions and relations are still missing.