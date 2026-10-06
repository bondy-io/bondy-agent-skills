# Documenting Bondy Source

Conventions for documentation that lives *inside* the source tree — Erlang module and function
docs, and the Bondy-specific contract annotations. Audience: a contributor reading the code. The
reference discipline from `diataxis-for-bondy.md` and the voice from `house-style.md` both apply;
this file adds the Erlang/Bondy specifics.

## The module reference is generated — never hand-write it

The rendered reference page for a module is produced by **`bondy docs`** from the module's
in-source documentation: its `@moduledoc` (Bondy Lang) / `-moduledoc` (Erlang), plus the `@doc` /
`-doc` attached to each **public signature**. The doc attributes are the single source of truth;
the page is a generated artifact.

Consequences for how you work:

- When asked to "write the reference for module X", your deliverable is the edited in-source doc
  attributes, then a run (or instruction to run) `bondy docs`. **Do not create a Markdown reference
  file for a module by hand** — it would be stale the moment the source changes, would duplicate
  the generator, and would drift from what `bondy docs` actually renders.
- Therefore the quality of a module's reference is entirely the quality of its `@moduledoc`/`@doc`.
  Everything below — the contract discipline, the module-doc skeleton, the law annotations — is
  about writing those attributes well, because they *are* the reference.
- Only document a signature in `@doc` if it is **public**; private functions stay out of the
  generated reference (mark them `-doc false.` in Erlang where they'd otherwise leak).
- Hand-authored Markdown reference is reserved for things that are *not* one module's generated
  output: a subsystem overview spanning several modules, the type-system forms, a wire format. Those
  live in the doc tree as files; module reference does not.

## Comment and export style (strict)

Alejandro's house conventions for Bondy Erlang source. These are not negotiable defaults:

- **`%%` for all comments, never `%`.** Even a trailing comment uses `%%`.
- **One `-export` attribute per function**, not a combined list:

  ```erlang
  %% Yes
  -export([new/1]).
  -export([validate/1]).

  %% No
  -export([new/1, validate/1]).
  ```

- Module-level section banners use `%%` rules consistent with the existing tree — match the
  surrounding file, don't import a new banner style.

## Code comments

A comment is not documentation. Doc attributes describe the public contract and follow the rest of
this file. A comment sits beside code that a contributor is already reading, so it has to say
something the code cannot. If deleting a comment loses no information, delete it.

Where the repository's `CLAUDE.md` has a comments section, that section governs. `bondy_lang` has
one, with budgets and a check to run before finishing. The rules below are the portable core and
agree with it.

Write a comment for:

- **A reason** — why this approach, when an obvious alternative was rejected.
- **An invariant or a precondition** that the types do not express.
- **A hazard** — ordering, concurrency, partial failure, resource ownership.
- **A citation** — the BDDR, specification, RFC section, paper or test that explains the shape.
- **`TODO` / `FIXME`**, with an issue reference.

Do not write:

- a restatement of the next line, the function name or the type signature;
- narration: "Step 1", "First we", "Now we";
- history or process notes: "previously", "no longer", "refactored to", "the fix", dates;
- planning identifiers: roadmap, phase, task or ledger ids, and paths to private design notes;
- commented-out code.

Form:

- **Default to no comment in a function body.** Three lines is the normal ceiling. A longer
  argument goes in the module doc as one sentence and in a BDDR as the argument.
- **Write full sentences in the controlled language**, strict tier: at most 20 words, simple
  present, a named actor. See `controlled-language.md`.
- **Cite, do not restate.** Give the BDDR id, or the specification or book chapter by file path
  and title. Never cite a chapter by number. A paraphrase of a decision goes stale; the id does
  not.
- **A claim names its evidence.** "Equivalent to", "cannot disagree" and "by construction" each
  need the test or property that checks them, named in the same sentence. Without it, write the
  sentence as intent or leave it out.
- **One home per explanation.** If the same rationale would appear at two sites, it belongs in
  the owning module's doc, and the other site gets nothing.

```erlang
%% Intersection membership is checked per conjunct (BDDR-000113).
```

The rules on history, planning identifiers and evidence apply to doc attributes as well. A
`-doc` states the contract, never how the code came to be.

## Use `-doc` / `-moduledoc`, not edoc

Bondy targets modern OTP, so prefer the **EEP-48 documentation attributes** (`-moduledoc` and
`-doc`) over edoc `%% @doc` comments. They are first-class, tool-agnostic, and consumed by ExDoc
to produce the doc site. Reserve edoc-style `@`-tags only where a module already uses them
consistently and converting it is out of scope.

```erlang
-module(bondy_mst).
-moduledoc """
A Merkle Search Tree: an ordered key-value map whose structure is a deterministic function of its
contents, so two replicas holding the same entries hold byte-identical trees regardless of
insertion order.

This determinism is what makes the MST a CRDT substrate: structural equality implies state
equality, so a hash comparison decides whether two peers have converged.
""".

-export([new/0]).
-export([insert/3]).

-doc """
Inserts `Key` with `Value`, returning the updated tree.

The result is independent of insertion order: inserting the same set of pairs in any sequence
yields the same tree. Re-inserting an existing `Key` replaces its value.
""".
-spec insert(Key :: term(), Value :: term(), Tree :: t()) -> t().
insert(Key, Value, Tree) ->
    %% ...
    Tree.
```

Notes:

- Use triple-quoted sigil strings (`"""..."""`) for multi-line docs; they read cleanly and avoid
  escaping.
- The first line/paragraph of a `-doc` is the summary that appears in listings. Make it a complete,
  standalone sentence stating what the function *is for* — not a restatement of its name.
- Keep `-spec` and `-doc` adjacent and in agreement. The spec is the type contract; the doc is the
  *behavioural* contract. They are complementary, never redundant — the doc says what the types
  cannot.
- Mark non-public surface with `-doc false.` so it stays out of generated docs without losing the
  in-source comment.

## Document the contract, not the signature

A `-spec` tells the reader the types. The doc's job is everything the types cannot say. For each
public function, state whichever of these apply:

- **Preconditions** — what the caller must guarantee (ordering, ownership, that a realm exists).
- **Postconditions / guarantees** — what is true of the result.
- **Failure modes** — what it raises or returns as error, and the condition that triggers each.
  Distinguish `error`-returning from exception-raising explicitly.
- **Effects** — does it perform I/O, mutate ETS/the store, send messages, start processes? An
  effect the type doesn't show is exactly what the prose must show.
- **Determinism** — same inputs, same output? Or environment-/time-/node-dependent?
- **Concurrency** — is it safe to call concurrently? Does it block? Is it a `gen_*` call with
  timeout semantics?
- **Causality / convergence** (for distributed and CRDT code) — what ordering or delivery
  guarantee it assumes or provides; whether it is a lattice operation (and therefore commutative /
  associative / idempotent).

This is the NeXT "designated initializer" discipline: document the *law* the construct lives
under, with its rationale, not merely its shape.

## Bondy contract annotations

Bondy's source carries first-class annotations that *are* contract documentation. Treat them as
the most authoritative statements on the page: your prose must agree with them, and where they
exist you surface and explain them rather than paraphrasing around them. **Verify the current set
and exact semantics against the source — these evolve.**

- **`@law` classifiers** — assert algebraic/operational laws a definition obeys. The set includes
  classifiers such as `monotonic`, `idempotent`, `deterministic`, and `pure`. Where a function or
  rule carries an `@law`, the doc must (a) state which law(s) hold and (b) say what the reader may
  rely on as a consequence — e.g. "idempotent ⇒ safe to retry", "monotonic ⇒ never retracts a
  derived fact", "pure ⇒ referentially transparent, safe to memoise". Do not assert a law the
  source does not; do not silently drop one the source does.
- **`@bookdoc` / `@bddr`** — Bondy's literate/book-documentation annotations. These tie source to
  the narrative documentation layer. When present, they tell you which explanation or reference a
  construct belongs to; keep the two in sync — if the source's `@bookdoc` reference and the prose
  drift apart, that is a defect. Confirm their exact role from existing usage before relying on
  them; do not invent a semantics for them.

If you encounter an annotation you don't recognise, read its existing uses in the tree to learn its
contract before documenting around it. Never guess.

## Module-doc skeleton

Every public module's `-moduledoc` follows the same shape, so a contributor learns it once:

1. **One-sentence identity** — what this module *is*, in a single standalone sentence.
2. **The model** — the two-to-four sentences of concept the reader needs to make sense of the API
   (the "concept before API" rule, in miniature). State the key invariant the module maintains.
3. **Entry points** — name the few functions a reader should start from, so the module isn't a flat
   wall of equally-weighted exports.
4. **Rationale, if non-obvious** — one line on why the module exists or why it's shaped this way,
   where that isn't self-evident.

Keep it tight. The module doc orients; the function docs carry the detail.

## Spec/type docs

- Document exported types (`-type`, `-opaque`) with `-doc`. An opaque type especially needs prose:
  the whole point is that the reader cannot see its structure, so the doc is their only window into
  what it represents and what operations are legal on it.
- For a behaviour module, document each callback's contract from the *implementer's* point of view:
  what they must return, what the framework guarantees about when and with what it is called.
