# Controlled Language

The sentence-level rules of the Bondy corpus. `house-style.md` says how the corpus sounds. This
file says how each sentence is built. Read it for every specification, decision record, reference
entry, doc attribute and code comment.

The rules are the writing rules of ASD-STE100 Simplified Technical English, cut down to the ones
that fit a language and protocol corpus. STE's dictionary of approved words is **not** adopted. It
was built for maintenance manuals and has no "unify", "entail" or "subsume". The Bondy glossary
does that job for Bondy terms, and ordinary English words are unrestricted apart from the banned
list in `SKILL.md`.

The purpose is one reading per sentence. A specification is implemented by people and agents who
were not in the room. A sentence with two readings produces two implementations.

## Where the rules apply

| Text | Tier |
| --- | --- |
| Normative specification text | Strict |
| Decision records (BDDRs), BEP proposal sections | Strict |
| Reference: doc attributes and hand-authored reference | Strict |
| Code comments | Strict |
| Glossary entries | Strict |
| How-to steps | Strict |
| Tutorials, explanations, rationale chapters | Relaxed |

**Strict** means every rule below is a defect when broken. **Relaxed** means that rules 1, 4, 6, 9
and 10 still bind. The length limits in rules 2 and 7 become review triggers. A longer sentence is
allowed when a split would break an argument, and it is the exception. Tense and person in the
relaxed tier follow the registers in `house-style.md`.

## The rules

1. **One term per concept. One meaning per term.** The full discipline is in
   `house-style.md#controlled-vocabulary`. It applies in both tiers and it is the rule that
   matters most.

2. **Keep sentences short.** A requirement, an instruction or a comment sentence has at most 20
   words. A descriptive sentence has at most 25. Count an inline code span as one word. Do not
   count a vertical list as part of the sentence that introduces it.

3. **One requirement or one instruction per sentence.** Two obligations joined by "and" are two
   sentences. A reader has to be able to cite, test and violate each one separately.

4. **Use the active voice and name the actor.** "The router stamps `from`." Not "`from` is
   stamped." Passive is correct only when the actor is unknown or irrelevant. That case is rare in
   a specification: an obligation with no actor binds nobody.

5. **Use the simple present.** "The solver propagates." Not "will propagate", "would propagate" or
   "is propagating". Reserve the future for a tutorial's promise to the reader.

6. **No noun cluster longer than three words.** "Per-flow credit window replenishment policy" is a
   cluster of six. Break it with a preposition: "the policy that replenishes the credit window of
   a flow". A glossary term or an identifier counts as one word.

7. **One topic per paragraph, six sentences at most.** The first sentence states the topic.

8. **Put three or more conditions, options or steps in a vertical list.** Inline, the scope of
   "and" and "or" is ambiguous. In a list it is not.

9. **Use verbs as verbs.** "The peer validates the claim." Not "performs validation of the claim".
   A nominalised verb hides the actor and adds words.

10. **Give every pronoun an antecedent in the same or the previous sentence.** Follow "this" and
    "that" with a noun: "this rule", "that frame". A bare "this" at the start of a sentence usually
    points at a whole paragraph, and the reader has to guess which part.

11. **Write full sentences.** Keep the articles and keep "that". Telegraphic style ("Returns tree
    if key absent") saves three words and costs the reader a parse. The summary line of a doc
    attribute is the one exception: it may open with a third-person verb ("Returns…").

12. **State a condition before the action it governs.** "When the window is empty, the sender
    stops." The reader meets the trigger first, as the implementation does.

13. **Do not use "must", "should", "may" or "shall" in lower case inside a document that declares
    BCP 14.** Write the keyword in capitals when the sentence is a requirement. Otherwise choose
    another word: "has to", "needs", "can", "might", "is able to". See `specifications.md`.

14. **Use British spelling**, as the corpus does: "behaviour", "normalise", "materialised".
    Identifiers, BCP 14 keywords and quoted external terms keep their own spelling.

## Worked examples

These show the form. They are not statements of Bondy semantics; verify any fact against the
source before reusing it.

**A requirement.**

- Before: "Payloads encrypted with an algorithm that cannot be processed should not be passed
  through and an error must be raised."
- After: "If a peer cannot process the `penc` value of a frame, then the peer MUST fail the frame.
  The peer MUST NOT forward the ciphertext."

The rewrite names the actor, splits two obligations, puts the condition first, and replaces a
lower-case "should not" that read as a requirement.

**A reference sentence.**

- Before: "Insertion order independence is guaranteed by this function's result."
- After: "The result does not depend on insertion order."

**A comment.**

- Before: `%% need sorted here, dedup below assumes it`
- After: `%% The deduplication pass below requires sorted input.`

## What the rules do not do

- They do not shorten a document by removing reasoning. Shorter sentences usually mean more of
  them. `house-style.md` already says to prefer one more sentence of meaning over one fewer.
- They do not flatten the register of a tutorial or an explanation. Those modes keep their voice.
- They do not apply to quoted text, to identifiers, or to formal notation. A grammar production
  or a typing rule keeps its own notation.

## Checking

Rules 2, 3, 6, 7 and 13 and the banned-word list can be checked by a tool. If the repository has
a Vale configuration (`.vale.ini`), run `vale` on every file you changed and treat each alert as a
defect. If it has none, check by reading: take each sentence that carries a requirement, count its
words, and count its keywords.
