# OctoQuestDatabase

Shared Octo quest data for Questie-Octo and Questline. This repository contains
data only. Each addon owns its build adapter and ships generated Lua; players
need no separate database addon. See [NOTICE.md](NOTICE.md) for attribution.

## Data format

English/Octo source tables are in `source/*.json`:

| Files | Contents |
| --- | --- |
| quests, units, objects, items | Effective entities, relationships, flags, loot, vendors and coordinates |
| refloot, quests-itemreq | Reference loot and item-use targets (negative IDs identify objects) |
| zones, areatrigger, minimap, meta | Zone geometry, exploration triggers, map scales and tracking metadata |
| locales | English quest/entity/item/zone/profession text, including reward-only item names |
| scriptedEncounters, supplemental | Encounter guidance, rewards, objective counts, events, calendar rules and progression |

Entity and sparse maps use numeric string keys. Contiguous sequences use JSON
arrays and represent **one-based Lua tables**: `[x,y,zone,respawn]` becomes
`{[1]=x,[2]=y,[3]=zone,[4]=respawn}`. Recursively restore that indexing when
adapting data. Do not treat these arrays as zero-based game data. Empty objects
are empty Lua tables. JSON null is unsupported. Preserve unknown fields,
negative/zero values, sparse keys, coordinate precision and upstream IDs.

## Importing

Correct data here; keep addon-specific geometry, indexes and presentation in
consumer adapters. No manifest-generation step or Node packages are needed in
this repository. Both current adapters compute an exact SHA-256 content revision
from source files plus LICENSE/NOTICE.md, then pin it in `database-source.json`.
Ordinary imports reject a changed revision. From the parent addons workspace:

```powershell
node Questie-Octo/Tools/build_database.js ./OctoQuestDatabase --update-lock
node Questie-Octo/Tools/validate_database.js
node Questline/tools/import-database.js ./OctoQuestDatabase --update-lock
node Questline/tools/build.js
node Questline/tests/run.js
```

Omit `--update-lock` to reproduce a pinned revision. The adapters accept a
checkout path or `OCTO_QUEST_DATABASE`, defaulting to the sibling repository.
Other addons should read these source tables directly, preserve the sequence
contract, record the source revision, and include LICENSE/NOTICE.md with their
generated data. Commit the data changes and each consumer's updated lock and
outputs in their respective repositories. Historical migration evidence and
validation tooling live in Questie-Octo, outside this data repository.
