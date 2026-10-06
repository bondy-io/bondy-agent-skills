# Diátaxis for Bondy

Diátaxis sorts documentation by two axes: **acquisition vs. application** (is the reader studying,
or working?) and **theory vs. practice** (is the content about cognition, or action?). The four
quadrants:

|              | Practice (action)        | Theory (cognition)          |
| ------------ | ------------------------ | --------------------------- |
| **Study**    | Tutorial                 | Explanation                 |
| **Work**     | How-to guide             | Reference                   |

The discipline that makes this work — and that the NeXT corpus enforced rigorously — is **mode
purity**: each document does exactly one of these jobs. The most common documentation defect is a
page that tries to be two things at once (a reference that teaches, a tutorial that documents
exhaustively). When you catch that, split the page.

Read this file when writing standalone documentation for Bondy Lang or the platform. For
in-source docs, read `source-documentation.md` instead — though the reference discipline below
still governs the *reference* portions of source docs.

## Tutorial — learning-oriented

A lesson. The reader is a beginner who learns by doing, under your guidance. Success is measured by
*the reader's experience of competence*, not by coverage.

**For Bondy Lang**, tutorials look like: "Write your first CHR program", "Model a small domain
with fluents and signals", "Stand up a Bondy realm and call a procedure".

Rules specific to tutorials:

- **You are responsible for success.** Every step works. You have run it. There are no "if this
  fails, try…" branches — a branch means you don't control the environment, so fix the
  environment, not the prose.
- **Concrete over abstract.** The reader builds one specific thing, not "a program in general".
- **Defer explanation.** When the reader must do something whose reasons aren't yet clear, say "for
  now, do X — we explain why in [link]". Do not stop to teach theory; that breaks the lesson.
- **No choices.** The tutorial picks one path. Options and alternatives belong in how-to and
  reference.
- **Minimal but real.** The thing built is small but not fake — it actually runs and does
  something the reader recognises as worthwhile.

Anti-pattern: a "tutorial" that is really a feature tour, listing everything the language can do.
That is reference wearing a tutorial's clothes.

## How-to guide — task-oriented

A recipe for a competent user with a goal. The reader already knows the basics; they have a
specific problem and want the steps.

**For Bondy**, how-tos look like: "How to define a refinement type with a CHR side-condition",
"How to replicate state across peers with the MST CRDT", "How to rotate Ranch TLS material without
dropping sessions", "How to expose a Z-object through the SAP connector".

Rules specific to how-tos:

- **Assume competence.** No explanation of what a CHR rule *is*; the reader knows. Link to the
  explanation for readers who don't, but don't inline it.
- **Address a real-world goal, stated in the title.** "How to <accomplish goal>". If you can't name
  the goal, it isn't a how-to.
- **Sequence of actions, not a tour of features.** Each step is an action the reader takes.
- **Practical over complete.** Cover the path to the goal, including the realistic forks ("if your
  realm uses X, do Y instead"); omit everything irrelevant to *this* goal.
- **Name the result.** End at a recognisable done-state: "You now have…".

Anti-pattern: a how-to that explains the theory of the subsystem before the steps. The theory is an
explanation; link it, don't inline it.

## Reference — information-oriented

A description of the machinery, structured to be *consulted*, not read. The reader is working and
needs to look something up. Reference is austere: accurate, complete within its scope, and devoid
of teaching.

**For Bondy Lang**, reference covers: the type-system constructs (each HM(X)/CHR/TFS/effect/mode
form), the `@law` classifier set, the standard library, the reactive primitives, the BAMP wire
format, and every public module/function in the source.

Note the production split. **Module/API reference is generated**: `bondy docs` renders it from the
in-source `@moduledoc` and the `@doc` on each public signature, so you produce it by writing those
attributes well (see `source-documentation.md`), not by authoring a Markdown page. **Hand-authored
reference** is for the cross-cutting material that is not one module's output — the type-system
forms, a subsystem spanning several modules, the wire format — and uses the template below. The
reference *discipline* (task-grouping, contract-not-signature, consistent skeleton) governs both.

Rules specific to reference — this is where the NeXT discipline matters most:

- **Task-grouped, never alphabetical.** Group members by what the reader is trying to do
  ("Creating", "Querying", "Combining", "Lowering"). The section headings should let a reader read
  the *shape of the thing's responsibilities* off the page. Alphabetical ordering throws this
  information away.
- **Describe, don't instruct or explain.** State what each thing is, its signature/contract, its
  parameters, its result, its failure modes, its invariants. No "you should", no design rationale
  (link to the explanation), no tutorial walkthrough.
- **Document the contract.** Types are the floor. Add the law: preconditions, postconditions,
  effects, determinism, `@law` classifiers, causality/ownership constraints, what raises and when.
  (See `source-documentation.md` for the contract vocabulary.)
- **Consistent structure per entry.** Every entry of the same kind has the same skeleton, so the
  reader learns the shape once. (Templates in `templates.md`.)
- **Exhaustive within scope, ruthless about scope.** A reference page covers its subject
  completely and nothing else.

Anti-pattern: a reference entry that, instead of stating the contract, narrates how you'd typically
use the function. That's how-to material leaking in.

## Explanation — understanding-oriented

A discussion that illuminates. The reader is studying — away from the keyboard, building a mental
model. This is the mode the NeXT/Cocoa "Concepts" documents lived in, and it's where Bondy needs
the most investment because its ideas are deep.

**For Bondy Lang**, explanation covers: "Why Bondy Lang unifies HM(X) and TFS" (the bridge that is
the language's central contribution), "How the type system's layers compose", "The
provenance-semiring / CRDT-join-semilattice correspondence", "Why CHR for constraint solving",
"Why Multi-Paxos maps onto Bondy Lang primitives", "What a fluent is and how it differs from a
signal".

Rules specific to explanation:

- **Make connections.** Explanation's job is to relate this idea to others — to history, to
  alternatives, to the theory it rests on. It is the only mode allowed to discuss what *could* have
  been and why it wasn't.
- **Admit rationale and trade-offs.** "We chose X over Y because…" belongs here and only here. A
  sophisticated reader is owed the reasoning.
- **No instruction.** An explanation contains no step the reader is meant to perform. The moment it
  says "now run this", it has become a tutorial or how-to.
- **Bounded by topic, not by feature.** An explanation is organised around an idea ("graded
  effects") not a code unit ("the `effects` module").
- **It's allowed to argue.** Explanation may take a position about design and defend it. This is
  where Bondy's intellectual identity lives.

Anti-pattern: an "overview" that is a shallow list of features with one sentence each. That is a
table of contents, not an explanation. Explanation has a thesis.

## Routing a request

Most real requests are *several* documents. "Document the CHR subsystem" decomposes into:

- an **explanation** — what constraint handling rules are, why Bondy uses them, how they relate to
  the type kernel;
- a **reference** — every rule form, keyword, and the solver's public API, task-grouped;
- one or more **how-tos** — "How to write a propagation rule", "How to debug a non-terminating
  rule set";
- optionally a **tutorial** — "Write your first CHR program".

Identify the set, name each piece by its mode, and write them as separate documents that
cross-reference. Resist the urge to fuse them; the fusion is the defect.
