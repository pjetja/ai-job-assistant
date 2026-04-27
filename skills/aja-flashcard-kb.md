# AJA Flashcard KB Skill

Read and write the local flashcard knowledge base.

## Operations

This skill supports three operations passed via `operation` parameter:

### Operation: READ_INDEX

Read `{data_dir}/knowledge-base/index.md`.
Return the full index table so the caller can decide which category files to load.
If index does not exist: return `EMPTY_KB`.

### Operation: READ_CATEGORY

Input: `category_file` — filename (e.g. `react.md`)
Read `{data_dir}/knowledge-base/{category_file}`.
Return all flashcards in that file as structured list.
If file not found: return `CATEGORY_NOT_FOUND`.

### Operation: SEARCH

Input: `query` — keywords
Read index to find relevant category files.
Read those files. Return cards where question, short answer, or tags contain any query keyword.
Return as list: `[{card_id, question, short_answer, category}]`.

### Operation: ADD_CARDS

Input: `cards` — list of card objects with fields:
- `category`: e.g. "React"
- `category_file`: e.g. `react.md`
- `question`: short question text
- `short_answer`: 1-2 sentence answer
- `detail`: full detail text
- `tags`: comma-separated tags

For each card:
1. Read `{data_dir}/knowledge-base/{category_file}` (or create if missing).
2. Determine next card ID: `{CATEGORY_ABBREV}-{NNN}` (pad to 3 digits, increment from max existing).
3. Append card using the standard flashcard format.
4. Write updated file.

After adding all cards:
- Read `index.md` (or create if missing).
- Update card count and last_updated for affected categories.
- Write updated index.

Print: `Added {N} cards to {category_file}. KB index updated.`

### Operation: UPDATE_CARD

Input: `card_id`, `field` (short_answer / detail / tags), `new_value`
Find card by ID across all category files. Update specified field. Write file.
