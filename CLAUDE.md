# CLAUDE.md — Project guidelines

Binding rules for anyone (human or agent) working in this repository.

## 1. Language

- Markdown files → **English**
- Chat conversation → **English**
- No calques from other languages (word-for-word translations)
- Established technical terms (e.g. `spec`, `plugin`, `commit`, `hook`) → keep them as they are

## 2. Markdown style

- Hierarchical, clear structure (headings and sub-headings)
- Schematic format → lists, tables, checklists
- Prose kept to the strict minimum
- Rule of thumb: one line = one piece of information
- Every line self-explanatory: a complete sentence, understandable without context — schematic does
  not mean cryptic

## 3. Conventions

**What a convention is** → a rule that **several agents** need to know.

- Rule shared by 2+ agents → **centralized convention** (written once only)
- Rule used by a single agent → stays in its prompt, it is not a convention
- Agents **reference** it, they do not copy it → single source, zero duplication

**How they are organized**

- No single catch-all file
- **One convention = one dedicated file**
- Small files, single responsibility
- Goal → every agent receives only the context it needs

**Duplication → centralize**

- If a rule already described elsewhere is repeated/rewritten → it must be extracted and centralized
- Trigger → **second occurrence**: the 2nd time a rule is needed, centralize it
- First occurrence → may stay local; from the second on → a single source referenced by all
- No shared rule lives duplicated in two places

## 4. Editing files

- **Minimal diff** → apply the smallest possible change
- Touch only what is needed, do not rewrite parts that are already fine
- Prefer targeted edits over full rewrites
- Benefits → less risk, simpler reviews

## 5. Commits

- Messages in **English**
- Do **not** add the assistant's name (no `Co-Authored-By`)
- Schematic message → concise subject + any details as a list
