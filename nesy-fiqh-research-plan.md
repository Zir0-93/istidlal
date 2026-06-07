# A Neuro-Symbolic System for Derivation-Aware Legal Reasoning in Shia Fiqh

**Working title for the program.** A phased research plan toward a NeSy system that (a) reconstructs *how* historical rulings were derived from a given scholar's *mabānī* (foundational positions in *usul al-fiqh*), and (b) derives rulings on novel cases under those same *mabānī*, with a fully inspectable reasoning trace.

**Who this is for.** An internal program document for the project team and close collaborators — the load-bearing map we orient by, not a grant proposal or a publication. It is written to be revised.

**What "this worked" means (program north star).** The system reproduces a *documented historical derivation* for a chosen scholar, and *swapping that scholar's mabānī changes the ruling in the direction a scholar would predict*. Everything below serves that one sentence; if a phase stops serving it, the phase is wrong, not the goal.

**The one risk that ends the program.** If a documented *derivation* layer does not exist in usable form for the chosen scholars — i.e. the scholars stated *rulings* but not *how they reached them* — then retrodiction has no ground truth and the core thesis cannot be tested. This is not a Phase 3 discovery; it must be confirmed in Phase 0 before any building begins. (Per-phase risks are listed at the end; this is the existential one.)

**How fixed any of this is.** Only the Phase 0 scoping decisions (scholars, chapter, formal substrate, correctness oracle) are meant to be load-bearing commitments. Phases 3–5 are *expected* to reshape substantially as earlier phases teach us what's feasible — treat their detail as provisional and the structure, not the specifics, as the durable part.

---

## Guiding design principles

A few commitments that run through every phase and should be stated up front in every paper:

1. **The *mabānī* are the unit of novelty.** Generic legal-NeSy work formalizes statutes and precedents. This program's distinctive claim is that it parameterizes the *reasoning engine itself* by a scholar's methodological commitments — their position on *ḥujjiyyat khabar al-wāḥid*, on *istiṣḥāb*, on *barāʾa* vs. *iḥtiyāṭ*, on the authority of *ʿaql*, etc. Two scholars given the same evidence and the same case should produce different rulings *because the engine is configured differently*, and the system should show exactly where the divergence originates.

2. **Two capabilities, two validation bars.** *Retrodiction* (explaining a known historical ruling) is checkable against ground truth — the ruling exists, the derivation is documented in the scholar's own works. *Prediction* (ruling on a novel case) has no ground truth and must be validated differently (expert evaluation, internal consistency, defeasibility). Conflating them is the most common way projects like this overclaim. Keep them separate.

3. **The trace is the product.** A correct ruling with an opaque derivation is a failure for this program. Every output is a structured argument: evidences invoked, rules applied, conflicts (*taʿāruḍ*) detected, preferences (*tarjīḥ*) exercised, and the *mabānī* that licensed each step.

4. **Scope down hard, at least at first.** Pick one *bāb* (chapter) — *ṭahāra* is the conventional choice in the existing formalization literature because its rules are comparatively self-contained — and one or two scholars with well-documented *mabānī*. Resist breadth until the pipeline works end to end on a narrow slice.

---

## Phase 0 — Foundations and scoping (short, ~1–2 months)

**Goal.** Lock the scope, the scholars, the chapter, and the formal target before building anything. This phase prevents the most expensive mistakes.

**Activities.**
- **Confirm the derivation layer exists first (gate condition).** Before committing to any scholar, verify that their *derivations* — not just their rulings — are documented in usable form (in their *taqrīrāt*, *usuli* works, or commentaries). If they aren't, change scholars or change scope. This is the go/no-go check for the whole program.
- Select 1–2 *marājiʿ* whose *mabānī* are explicitly documented (their *risāla ʿamaliyya* plus their *usuli* works, e.g. *taqrīrāt* of their lectures). The pairing matters: choose two who *diverge* on at least one well-known *mabnā*, so the contrast is demonstrable.
- Fix the target *bāb* (recommend *ṭahāra* or a subset, e.g. *aḥkām al-miyāh* and *najāsāt*).
- Choose the formal substrate (see Phase 2) at least provisionally, so data collection is shaped by what the model will need.
- Assemble an advisory relationship with at least one hawza scholar who can adjudicate correctness — this is your ground-truth oracle for later phases and should be lined up now.

**Deliverable / paper.** A *position / problem-framing paper*: "Derivation-aware legal AI for Shia fiqh: why pattern-matching fails and what a *mabānī*-parameterized system must do." This is publishable on its own (venues like *Artificial Intelligence and Law*, or Islamic-studies-and-technology venues), establishes priority on the framing, and forces you to articulate the whole vision before committing engineering effort.

---

## Phase 1 — Corpus construction (runs in PARALLEL with Phase 2)

**Goal.** Build a structured, machine-usable corpus for the chosen scope: the source evidences (Qurʾanic *āyāt*, relevant *aḥādīth* with their *sanad/matn* and grading), the scholars' rulings, and — critically — the *documented derivations* where they exist.

This is the bottleneck the literature repeatedly flags: there's a severe Arabic-vs-English data imbalance, incomplete and unevenly curated digital collections, and unstructured Arabic that's hard to process. Treat corpus-building as a first-class research contribution, not pre-work.

**Three data layers to build:**
1. **Evidence layer.** The *adilla*: relevant verses, hadith (with transmission and authentication metadata, since a scholar's *mabnā* on *ḥujjiyya* operates on exactly this metadata), and points of *ijmāʿ*.
2. **Ruling layer.** The scholars' actual *fatāwā* on cases in scope, normalized into a consistent case representation.
3. **Derivation layer (the hard, valuable one).** Where a scholar or their commentators document *how* a ruling was reached — which evidence, which usuli principle, which conflict-resolution — capture it as a structured derivation. This is your retrodiction ground truth and the scarcest resource. Even a few dozen fully-annotated derivations are worth more than thousands of bare rulings.

**Method.** LLMs are genuinely useful *here* as an extraction/annotation engine (the "observation engine" role) — proposing structured facts from unstructured Arabic text — with scholar-in-the-loop verification. Build an annotation schema and inter-annotator agreement process; this rigor is what makes the dataset publishable and citable.

**Deliverable / paper.** A *resource/dataset paper* describing the corpus, the annotation schema for derivations, and agreement metrics. Dataset papers are high-citation and establish the foundation everyone (including you, in later phases) builds on. Release a version publicly if the sources permit.

---

## Phase 2 — Modelling the reasoning core (runs in PARALLEL with Phase 1)

**Goal.** Build the symbolic reasoning engine and, separately, formalize *mabānī* as configurable parameters of that engine. No LLM in the loop yet — this phase is about getting the logic right in isolation.

**Two sub-tracks:**

**2a. The defeasible-argumentation engine.** Implement the conflict-and-preference machinery (ASPIC+-style or an abstract dialectical framework) that mirrors *taʿāruḍ al-adilla* and *tarjīḥ*: defeasible rules with explicit attack relations and a priority ordering, where a general rule (*ʿumūm*) can be defeated by a specifier (*takhṣīṣ*), an apparent meaning yields to an explicit one, and presumptions like *istiṣḥāb* hold until defeated. This is the well-matched formalism for fiqh because legal claims are inherently revisable, not monotone.

**2b. *Mabānī* as parameterization.** The novel contribution. Formalize a scholar's methodological commitments as the configuration that determines: which evidences are admissible (e.g. their stance on *khabar al-wāḥid*), the priority ordering over conflicting rules (their *tarjīḥ* preferences), and the default principle in cases of doubt (*barāʾa* vs. *iḥtiyāṭ*, role of *istiṣḥāb*). Building on existing FOL formalizations of *usul al-fiqh* — which already show you can ask structural questions like whether a school's treatment of a chapter is *consistent* and *complete* — extend them so the engine is *swappable* between scholars.

**Validation in this phase** is formal, not empirical: does the engine reproduce the *known* divergence between your two scholars on the *known* contested case, purely from their differing parameterization? If yes, you've demonstrated the core thesis in miniature.

**Deliverable / paper.** A *methods paper*: "Formalizing scholarly *mabānī* as parameters of a defeasible-argumentation engine for fiqh." Even with hand-encoded inputs (no automated extraction yet), demonstrating *mabānī*-driven divergence on a real contested case is a strong, self-contained result.

---

## Phase 3 — Integration: the neuro-symbolic bridge (retrodiction)

**Goal.** Connect Phase 1's corpus and Phase 2's engine into an end-to-end NeSy system, and validate it on the *retrodictive* task: given a historical case and a scholar's *mabānī*, reconstruct the derivation and match the known ruling.

**Architecture.** The standard, well-supported division of labor: the LLM is the *observation engine* that maps unstructured case descriptions and source texts into the structured facts the symbolic layer consumes; the symbolic layer does the *determinative* reasoning deductively and emits the trace. Reasoning-tuned LLMs are markedly better at the text→formal-structure translation than base models, so this is where model choice matters.

**Why retrodiction first.** It has ground truth. You can measure: (1) ruling accuracy (did it reach the documented *ḥukm*?), and — more importantly — (2) *derivation fidelity* (did it reach it *the way the scholar did*, invoking the same evidences and principles?). A system that gets the right answer by the wrong path is diagnosable here, which it never would be on novel cases.

**Deliverable / paper.** A *system paper* with quantitative retrodiction results on the corpus: ruling accuracy and derivation-fidelity metrics, broken down by scholar, with error analysis. This is the flagship empirical paper of the program.

---

## Phase 4 — Prediction on novel cases, and evaluation methodology

**Goal.** Turn the validated retrodictive engine toward *new* cases (*masāʾil mustaḥdatha* — contemporary questions), and — equally important — establish a defensible way to evaluate output that has no ground truth.

**The hard part is evaluation, not generation.** Once Phase 3 works, generating a ruling-with-trace for a novel case is mechanically the same operation. The research contribution is the *validation framework*:
- **Expert evaluation** — blinded scholar panel rates derivations for soundness and for fidelity to the named scholar's *mabānī*.
- **Internal consistency** — does a novel ruling contradict the scholar's established rulings in scope? (Your Phase 2 consistency machinery does this automatically.)
- **Counterfactual / defeasibility probes** — flip a *mabnā* and confirm the ruling changes in the predicted direction; add a defeating evidence and confirm the conclusion is correctly withdrawn.
- **Calibrated non-answers** — the system should *decline* when the case is under-determined by the available evidence and the scholar's *mabānī*, rather than fabricate. Appropriate abstention is a feature, and measuring it is part of the framework.

**Deliverable / paper.** An *evaluation-framework + results paper*: "Evaluating derivation-aware legal AI on novel cases without ground truth." The methodology is arguably more citable than the results, because everyone working on generative legal/normative AI faces this same no-ground-truth problem.

---

## Phase 5 — Synthesis, system release, and the limits paper

**Goal.** Integrate the components into a usable tool, release what's releasable, and write the honest account of what the system can and cannot do.

**Activities.** A usable interface (the trace, rendered for a scholar or student); a packaged corpus + engine release if sources permit; and a candid treatment of the boundaries — where *ijtihād* involves judgment the formalism doesn't capture, where the system must defer to a human *mujtahid*, and the theological/authority questions about machine-derived rulings (a machine does not *do ijtihād*; it reconstructs and extends a documented methodology, and that distinction must be explicit).

**Deliverable / paper.** A capstone *synthesis paper* plus a tool/demo release. Optionally a separate shorter piece on the ethical and authority dimension, which may reach an Islamic-studies audience that the technical papers won't.

---

## Parallelism and dependency map

```
Phase 0  ──┐
           ├──> Phase 1 (corpus)   ──┐
           └──> Phase 2 (modelling) ──┤
                                       ├──> Phase 3 (integration / retrodiction)
                                       │         └──> Phase 4 (prediction + eval)
                                       │                   └──> Phase 5 (synthesis)
```

Phases 1 and 2 are deliberately concurrent — the corpus schema (Phase 1) should be *shaped by* what the engine consumes (Phase 2), so run them with a shared interface contract: agree early on the structured representation of a "case," an "evidence," and a "derivation step," and let both tracks build against it. That contract is itself worth documenting.

## Papers at a glance

| Phase | Paper type | Core claim |
|---|---|---|
| 0 | Position / framing | Why derivation-aware, *mabānī*-parameterized reasoning is the right target |
| 1 | Resource / dataset | A structured, derivation-annotated fiqh corpus + annotation methodology |
| 2 | Methods | *Mabānī* formalized as parameters of a defeasible engine; reproduces known divergence |
| 3 | System | End-to-end NeSy retrodiction with ruling-accuracy and derivation-fidelity results |
| 4 | Evaluation + results | Validating novel-case rulings without ground truth |
| 5 | Synthesis (+ ethics) | Integrated system, release, and honest limits |

## Risks worth naming early

- **Derivation data scarcity (highest risk).** The derivation layer is the scarcest and most valuable resource. Mitigate by scoping narrowly and accepting that a small, deeply-annotated set beats a large shallow one.
- **Formalization burden.** Encoding rules from source texts that use general language and sometimes conflict is labor-intensive and is itself a scholarly act. Budget for it and treat the encoding decisions as documented research, not plumbing.
- **The novelty must stay visible.** If reviewers can't tell this apart from generic legal NeSy, the *mabānī*-parameterization claim hasn't been made sharply enough. Every paper should be able to answer "what changes when you swap the scholar?" in one sentence.
- **Authority and scope claims.** Be precise and modest about what the system *is* (a reconstruction-and-extension tool over a documented methodology) versus what it is *not* (a *mujtahid*). Overclaiming here is both a scholarly and a credibility risk.
