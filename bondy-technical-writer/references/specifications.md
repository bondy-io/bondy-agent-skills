# Writing Specifications

How to write normative text. Normative text is text that an implementation is held to:

- the BAMP wire protocol and its profiles;
- the gateway specifications;
- Bondy Connect conformance;
- the language specification and the typing rules;
- the proposal section of a BEP.

Audience: an implementer who was not present when the document was designed. That reader treats
every sentence as either binding or not.

Read `controlled-language.md` first. Every rule there applies here at the strict tier.

## Ground truth

- Find where the specification lives before writing. In `bondy_lang` the specifications are Typst
  chapters under `book/src/`, and the repository's `CLAUDE.md` maps each file to what it governs.
  Follow that map. Do not write a specification from a design note that the map marks as
  superseded.
- A specification is not Diátaxis reference. Reference describes what the implementation does. A
  specification states what every conforming implementation has to do. Do not merge the two.
- Match the document's own conventions section. If it declares a requirements language, a
  notation, or citation forms such as `[Dn]` and `[Qn]`, use those forms exactly.

## Three kinds of normative statement

A reader has to be able to tell, for each sentence, whether an implementation can violate it.
There are exactly three normative forms.

1. **Definition.** A declarative sentence in the simple present that states what a construct is.
   It carries no keyword. "A frame is an array whose position 0 is the opcode." An implementation
   that contradicts a definition is non-conformant.
2. **Requirement.** A sentence that places an obligation on a conformance target and carries
   exactly one BCP 14 keyword.
3. **Formal artefact.** A grammar, a CDDL rule, a Shape, a typing rule, a state table. Where a
   formal artefact and prose disagree, the formal artefact governs. Say so once in the
   conventions section.

Everything else is **informative**: rationale, examples, notes, diagrams. Mark informative
material so that it cannot be mistaken for the other three. An example is never the only statement
of a rule.

Use a definition for structure and a requirement for behaviour. "A frame MUST have a DETAILS
map at position 1" is a definition wearing a keyword: a frame does not act. Write the structure as
a definition or a grammar rule, and write the behaviour as a requirement on the peer that receives
a malformed frame.

## Requirement language (BCP 14)

- Declare BCP 14 (RFC 2119 and RFC 8174) once, in the conventions section of the document.
- Use five keywords only: **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, **MAY**. Do not use
  SHALL, REQUIRED, RECOMMENDED or OPTIONAL. They are synonyms, and one term per concept applies to
  keywords too.
- Typeset keywords as the document already does. The BAMP chapters use plain capitals.
- One keyword per sentence. A sentence with two keywords is two requirements.
- The subject of a requirement is a **conformance target**: a router, a client, a peer, a
  provider, a gateway, a compiler, an implementation. Never "the user", never "the system", never
  a data structure.
- **MUST** and **MUST NOT** state what a test can check. If no vector, transcript or property
  could detect a violation, the sentence is a definition or it is informative.
- **SHOULD** and **SHOULD NOT** state the reason, or the condition under which an implementation
  can do otherwise, in the next sentence. A SHOULD with no stated exception is a MUST that nobody
  decided.
- **MAY** grants an option. State what the other party has to tolerate as a result.
- Do not use the lower-case words inside a normative document. See rule 13 of
  `controlled-language.md`.

## Requirement patterns (EARS)

Protocol requirements follow the Easy Approach to Requirements Syntax, with the BCP 14 keyword in
the place where EARS puts "shall". The pattern fixes word order, so the trigger, the actor and the
obligation are always in the same place.

| Kind | Pattern |
| --- | --- |
| Ubiquitous | The *actor* MUST *response*. |
| Event-driven | When *trigger*, the *actor* MUST *response*. |
| State-driven | While *state*, the *actor* MUST *response*. |
| Unwanted behaviour | If *condition*, then the *actor* MUST *response*. |
| Optional feature | Where *feature is supported*, the *actor* MUST *response*. |

Combine at most two clauses, in the order While, When: "While the session is established, when the
router receives `GOODBYE`, the router MUST …". Use "If … then" for every error path, so that a
reader can find the failure behaviour by scanning for the word "If".

A remote party can violate some requirements. Each of those has a matching unwanted-behaviour
requirement. It states which error the receiver reports and whether the session survives. A MUST
with no stated consequence leaves every implementation to invent one.

## Addressing and citation

- Each normative section carries a label. Cross-reference by label (`#ref(<label>)` in Typst).
  Never cite a chapter, an appendix or a section by number: numbers shift when a chapter is added.
- Outside the document, cite a specification by file path and title.
- Keep one requirement per sentence and one topic per section, so that a section label plus a
  sentence identifies a requirement. Do not invent a requirement-identifier scheme. If the
  document has one, use it; if a task needs one, raise it with Alejandro.
- A section that carries an unresolved question is marked **Provisional**, in the form the
  document already uses. State the current leaning and cite the question. A conformance vector
  does not pin a provisional point.

## What stays out of normative text

- **Rationale.** The reason for a decision lives in the rationale chapter or in a BDDR. Normative
  text cites it and does not repeat it.
- **History.** No "previously", "no longer", "now". A specification states the current contract.
- **Planning identifiers.** No roadmap, phase, task or ledger ids.
- **Real names in examples.** Use fictional identifiers in every example.
- **Hedges.** "Typically", "generally", "in most cases" and "as appropriate" have no place in a
  requirement. Either the condition can be stated, or the sentence is not a requirement.

## Protocol specifications

- Order each section as: definition, requirements for the normal path, requirements for the error
  paths, then informative notes.
- Tie requirements to the conformance corpus. When you add or change a MUST, say which vector,
  transcript or property checks it, or flag that none does.
- Tables state structure. A table cell does not carry a keyword; put the obligation in a sentence
  below the table.

## Language specifications

- The grammar, the typing rules and the semantics are the normative content. Prose beside them is
  an informative gloss, and the formal rule governs.
- Use BCP 14 keywords only for implementation conformance: "A conforming compiler MUST reject a
  program that …". Do not use EARS patterns. A typing judgment is not an event-driven requirement.
- Write judgments in the notation of the typing-rules chapter. Do not restate a rule in prose as a
  second normative source; describe what it means and point to it.
- State each static error as a definition of what is rejected, with the rule that rejects it.

## BEPs

A BEP follows the template in the repository (`beps/BEP-0001_template.md` in `bondy_lang`). Its
Proposal section is normative text in waiting: write it by the rules above, so that it moves into
the specification without a rewrite. Motivation, alternatives and open questions are informative
and follow the relaxed tier.

## Before delivering

- Verify a Typst edit by compiling the book. An unresolved reference fails the build.
- Search the file for lower-case "must", "should", "may" and "shall".
- Read every sentence that contains a keyword. Check that it has:
  - exactly one keyword;
  - a conformance target as its subject;
  - at most 20 words;
  - an obligation that a test can check;
  - a stated failure behaviour, where a remote party can violate it.
