# Job search log

Append-only record of operations on this vault. One line per write.

Format: `## [YYYY-MM-DD] action | <stage or file> — <what changed>`

Verbs: `setup`, `search`, `ingest`, `rank`, `apply`, `move`, `interview`, `update`, `decision`, `close`.

The first entry is written by setup. If this file has no entries, setup did not finish.

---
