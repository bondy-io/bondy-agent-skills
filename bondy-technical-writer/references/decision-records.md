# Writing Decision Records (BDDRs)

A Bondy Design Decision Record captures one decision: what was decided, why, and what it changes.
Audience: a contributor or an agent who meets the consequence of the decision in the code and
needs to know whether it is deliberate. BDDRs are also given to agents as instructions: "check
every module against BDDR-000001". A record therefore has to be short enough to hold in mind and
exact enough to check against.

Read `controlled-language.md` first. Every rule there applies here at the strict tier.

## Legacy records

Most existing records predate this standard. Their structure, length and status vocabulary vary.
**Do not use an existing record as a model.** Write new records, and records that are still
Proposed, to this standard. Do not rewrite an Accepted record to conform unless Alejandro asks for
that migration.

## What a record is and is not

- **One decision.** If the title needs "and", there are two records.
- **A record, not a specification.** Syntax tables, operational semantics, representation rules
  and invariants belong in the book or the specification. The record states the decision and
  links to where the rules live. A record that grows a numbered "Semantics" section has become
  the wrong document.
- **A record, not a status report.** No "current state", no implementation progress, no list of
  what remains. Whether the decision is implemented is not a property of the decision.
- **A record, not an explanation.** An explanation in the doc tree can tell the story at length
  and cite the record.

## Where records live

In `bondy_lang`, records are Markdown files at `doc_extras/bddrs/BDDR-NNNNNN.md` with YAML
frontmatter, published to the doc site. Use the next free number. Use the skeleton in
`templates.md`, which carries the frontmatter keys the site reads.

Status and date live in the frontmatter only. Do not repeat them in the body, where the two copies
drift apart.

## Status

Exactly four values, spelled this way:

| Status | Meaning |
| --- | --- |
| `Proposed` | Written, not yet decided. The text can still change. |
| `Accepted` | Decided. The text is frozen. |
| `Rejected` | Considered and declined. Kept so that the question is not reopened blind. |
| `Superseded` | Replaced by a later record, which the Notes section names. |

"Draft", "Done" and "Implemented" are not statuses.

## Append-only

An Accepted record is not edited, apart from changing its status to `Superseded` and adding the
link to its successor. A change of mind, a narrowing or an extension is a **new record** that
names the one it supersedes or amends. A record edited in place cannot be cited: a reader cannot
know which version an older citation meant.

## Sections

Required, in this order:

1. **Decision.** One sentence. Simple present, active voice, a named actor. "Bondy indexes from 1."
   "The router never decodes a payload." No "will", no "we propose", no BCP 14 keyword. If the
   decision has parts, follow the sentence with a numbered list of at most five clauses, each one
   sentence. A reader who stops here has the decision.
2. **Rationale.** A list. Each item is one reason: a constraint, a trade-off, or a piece of
   evidence. A claim names its evidence, be it a test, a property, a measurement or a paper.
3. **Consequences.** A list. What changes for a user or an implementer, what the decision makes
   possible, what it forecloses, and what it costs. State the costs as plainly as the benefits.

Optional:

- **Context**, placed after Decision. At most one paragraph: the forces that made a decision
  necessary. Not the history of the discussion.
- **Alternatives rejected**, placed after Rationale. One item per alternative: its name and the
  reason it lost, in one or two sentences.
- **Notes**, placed last. The record this one supersedes, links, and a short example.

## Rules

- **The title is the decision**, as a declarative clause of at most 12 words. "Indexing is
  1-based", not "Indexing" and not a paragraph.
- **No BCP 14 keywords.** A record states a decision. A specification states requirements. Where
  the decision creates a requirement, the requirement goes in the specification and the record
  cites the section by file path and title.
- **A body of about one page.** Sixty lines is the review trigger. A longer record is usually
  carrying specification or explanation that has a better home.
- **No planning identifiers and no private paths.** Cite other records by id (`BDDR-000113`), the
  book by file path and title, papers by author and year.
- **Relate records in the frontmatter**, with the `"bondy:Related"` key, and say in one sentence
  in Notes what the relation is.
- **Present tense throughout.** Rationale explains why the decision holds, not how the meeting
  went.

## Citing a record from elsewhere

A BDDR id is a stable reference and can appear in code comments, doc attributes, specifications
and the book. Cite the id alone. Do not restate the decision at the citation site: the restated
copy goes stale and the record does not.
