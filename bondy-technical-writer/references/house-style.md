# House Style

The voice and discipline of the Bondy corpus. Read this on essentially every documentation task.
It is derived from the NeXTSTEP/OpenStep/WebObjects/Cocoa tradition, whose defining trait was
that the whole corpus read as though written by one careful mind. That effect is produced by
rules, not talent. Obey the rules and the effect follows.

## Voice

Write in **declarative present tense**, addressed to an intelligent reader who does not need to be
sold anything.

- **Declarative, not promotional.** State what something is and does. The reference tier contains
  no adjectives of praise ("powerful", "elegant", "blazingly fast", "simple"). If a thing is
  elegant the reader will notice; saying so insults them.
- **Present tense, active where possible.** "`new/1` returns a fresh router." Not "will return".
  Not "A fresh router is returned by `new/1`" unless the grammatical subject genuinely is the
  return value.
- **Name the actor.** Cocoa's "receiver" convention worked because there was always a clear
  grammatical subject. In Bondy prose, name the construct doing the work: "the rule fires", "the
  solver propagates", "the session peer sends". Avoid agentless passive that hides who acts.
- **Respect the reader's time.** No throat-clearing, no "In this section we will explore". Start
  with the substance. The first sentence of any document states what it is and what the reader
  will be able to do or understand after it.
- **Unhurried, not terse-to-the-point-of-cryptic.** Density is a virtue when every word does
  work; it is a vice when it forces the reader to reconstruct omitted reasoning. Prefer
  one more sentence of *meaning* over one fewer sentence of *padding*. The limits in
  `controlled-language.md` cap the length of a sentence, never the number of sentences.

## Sentence construction

Voice says how the corpus sounds. The controlled language says how each sentence is built: its
length, its voice, its tense, and how many obligations it carries. Those rules are in
`controlled-language.md`, with the tier that applies to each kind of text. They bind strictly in
specifications, decision records, reference and comments, and loosely in tutorials and
explanations.

## Prose is foundation

Code does not document itself. Every construct, every member, every example earns prose that says
what it is *for*, not what its syntax *is*.

- Bad: "`merge/2` — merges two states." (Restates the name.)
- Good: "`merge/2` joins two replica states into their least upper bound. It is the lattice join
  for this CRDT and is therefore commutative, associative, and idempotent — replaying it cannot
  move state backwards." (States meaning and contract.)

## Controlled vocabulary

This is the discipline that most distinguishes great docs from adequate ones, and the one most
often skipped. **One term, one meaning. One meaning, one term.**

Rules:

1. A word that names a Bondy concept means *exactly* that concept everywhere in the corpus.
2. You never introduce a second word for a concept that already has one. If the codebase says
   "peer", you do not write "node", "member", or "participant" for the same thing in prose.
3. You never reuse an established term for a different concept.
4. When you must coin a term, coin it once, define it at first use in bold, and add it to the
   glossary. When you change a term, change it everywhere and update the glossary.
5. Prefer the term the *source* already uses over a prettier synonym. The source is the contract;
   prose that drifts from it creates two vocabularies and the reader pays for it.

Maintain a **project glossary** as a living reference document in the doc tree. In `bondy_lang`
it has two renderings: `doc_extras/concepts/glossary/` (one file per term) and
`book/src/C-glossary.typ`. A term changed in one is changed in the other. Each entry: the
term in bold, a one-sentence definition, and — where helpful — a "not to be confused with" note
distinguishing it from a neighbour. Treat the glossary as authoritative: if prose and glossary
disagree, one of them is a bug.

A glossary entry looks like:

> **Fluent** — a time-varying value whose history is materialised as a bitemporal relation. A
> fluent is *not* a signal: a signal is a push-based event stream, whereas a fluent is the
> queryable state those events accumulate into.

### Terminology you must get right (verify against current source)

The Bondy ecosystem has fast-moving, precise vocabulary. Do not guess at these — confirm the
current spelling and meaning against the source or spec before using them, because several have
changed (e.g. CHR rule keywords, the storage backend). Categories to pin down per document:

- **Language kernel**: the names of the type-system layers (HM(X), CHR, TFS, refinement, graded
  effects, session types, modes-as-constraint-domains), and which is which.
- **CHR**: the current rule keywords and arrow (these have changed — verify), rule-naming
  requirements, and the simpagation/propagation/simplification distinctions.
- **Classifiers**: the current `@law` set and what each asserts (monotonic, idempotent,
  deterministic, pure, …) — see `source-documentation.md`.
- **Reactive layer**: fluent vs. signal vs. event, and how each lowers (e.g. to DBSP / bitemporal
  relations).
- **Platform**: router, realm, peer, session, subscription, registration, and the WAMP roles —
  use them as Bondy/WAMP define them, not approximately.
- **Protocol**: BAMP envelope vs. body, framing, and the agent/commitment vocabulary.

If the current source uses a term this list spells differently, **the source wins** — and fix this
list.

## Concept before API

Sequence every document so the reader has the mental model before the surface. Within a single
page, this means a short "what this is and why" before the first construct. Across the corpus, it
means a construct's reference page links *up* to the explanation that frames it, and the
explanation links *down* to the reference.

The litmus test: if a reader who has read only the preceding prose would meet the next construct
and not know *why it exists*, the concept is missing. Add it or link it.

## Cross-references

"See also" is a curated editorial act, not an automated dump.

- Place links **at the point of need**, inline, where the reader's question naturally arises — not
  only in a trailing list.
- Link across modes deliberately: a how-to links to the reference for the constructs it uses and
  to the explanation for the model it assumes. A reference page links to the explanation that
  frames it.
- Every link earns its place. A reference that links to ten loosely related pages is noise; one
  that links to the two pages the reader will actually need next is navigation.

## Examples

Examples are minimal but complete — the Cocoa standard. Not a toy that doesn't compile, not an
enterprise scaffold that buries the point.

- The example shows exactly the construct under discussion and nothing extraneous.
- It is runnable / type-checks as written (verify against the real language surface).
- It is annotated with prose that says what the reader should notice, not a line-by-line
  paraphrase of the code.

## Register by mode

The voice flexes across Diátaxis modes:

- **Tutorial**: warm, second person, confident. "You'll build…", "Now run it — you should see…".
  Encouraging without being cute. Never hedges; the tutorial is a guarantee that this path works.
- **How-to**: imperative, terse, competent-reader. "To rotate the TLS material, call…". No theory.
- **Reference**: declarative, impersonal, exhaustive within scope. "Returns…", "Raises…",
  "Requires that…". No narrative beyond the task-grouped skeleton.
- **Explanation**: discursive, allowed to use first person plural and to argue. "We chose
  Multi-Paxos here because…". This is the only mode that may editorialise, and only about design.

Two kinds of document sit outside the four modes and have their own register:

- **Specification**: impersonal, declarative, exact. Definitions in the simple present;
  requirements with one BCP 14 keyword and a conformance target as subject. No rationale, no
  second person, no hedges. See `specifications.md`.
- **Decision record**: declarative, present tense, brief. It states the decision and justifies it.
  It does not instruct, specify or narrate. See `decision-records.md`.
