# Convention — Size of an analysis

An analysis is **proportionate to the request**. A narrow request gets a short file, a complex one a
deep file. Hard rule, not an aspiration: it outranks the section list of the analyst's Step 4.

Length is never a quality signal → an analysis is finished when every section left carries
information, not when every heading has a paragraph under it.

## The three classes

The class is chosen **before writing**, from the request itself, and declared in the file's
frontmatter as `size:`.

| Class | The request is | Whole file |
|-------|----------------|------------|
| `narrow` | one screen, one behaviour, a rewording, a bugfix | under 80 lines |
| `ordinary` | one feature | under 200 lines |
| `broad` | a new product, a whole area | no ceiling, but earn every section |

## Sections

- **Always present** → Request, Summary, Requirements, Scope.
- **Typically dropped** on a `narrow` request → Business goal, Market trends, Risks.
- **Never padded** → a section with nothing to say is dropped, not filled with restatement,
  generalities, or a rationale for its own emptiness.
- A section is written only when it tells the later phases something they could not deduce.

## What is not a model

- **The analyses already in `.sdd/analysis/` are not a length reference.** They may predate this
  convention, and in an established project most of them do.
- Classify against the **request**, never against what the folder happens to contain: a neighbouring
  file of 400 lines is not permission to write 400.
- The previous analysis of a fix/evolution is read for its **content**, not for its size.

## Who checks

- The analyst classifies, writes, and cuts to the budget before returning.
- The **orchestrator verifies** before committing: `wc -l` against the class in the frontmatter.
- Over budget → the orchestrator sends the file back **once**, naming the class, the budget and the
  actual count. What comes back is committed as it is: the budget is a corrective, not a gate.
