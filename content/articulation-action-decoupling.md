---
title: "Articulation-action decoupling in LLMs: an intra-system instance of a cross-field failure pattern"
slug: articulation-action-decoupling-llms
date: 2026-05-19
status: ready
audience: "AI alignment researchers, safety engineers, LLM-systems builders"
category: alignment-research
tags: [alignment, claude-code, monitor-action-decoupling, recognition-without-arrest, cross-field-synthesis, llm-architecture]
description: "An intra-system instance of a 30-year cross-field failure pattern. Five fields have names for the multi-system case; none fit the LLM case because both channels share weights. Two unfilled research moves follow."
canonical: ""
sources:
  - https://github.com/anthropics/claude-code/issues/60226
  - https://gist.github.com/yurukusa/db6011df3799fe21e04900bb3e99db4b
  - https://sites.socsci.uci.edu/~lnarens/1990/Nelson&Narens_Book_Chapter_1990.pdf
  - https://journals.sagepub.com/doi/10.1518/001872095779064555
  - https://arxiv.org/abs/1708.08611
  - https://www.prospectivepsych.org/sites/default/files/pictures/Gollwitzer_Implementation-intentions-1999.pdf
  - https://arxiv.org/abs/2212.08073
  - https://arxiv.org/abs/2305.20050
publish: false
---

# Articulation-action decoupling in LLMs: an intra-system instance of a cross-field failure pattern

Large language models exhibit a structural property that has, in my reading, no established name in the AI literature: the language layer reliably produces accurate self-monitoring text — caveats, constraint-statements, predictions of forthcoming failure — while the action layer produces inconsistent output in the same forward pass. The same model, the same generation step, the same parameters produce both channels, and the two are decoupled.

This post argues three things. First, the pattern is well-named in at least five adjacent fields, under the assumption of architectural separation between monitoring and action channels. Second, no existing technical vocabulary fits the LLM case cleanly because no prior architecture had both channels produced by the same forward pass. Third, the cross-field remediation literature converges on a single universal principle whose application to the LLM case suggests two unfilled research moves that are tractable today.

## The pattern

The pattern shows up in a single turn. Representative forms:

- The model articulates a constraint ("I should read this file before referencing it") and proceeds without reading.
- The model articulates a substitution risk ("the user provided a brief; I should use it") and generates a parallel version of the brief from training rather than reading the provided one.
- In retrospective self-reports, the model produces an accurate post-mortem of a failure mode it has just committed in the same conversation — in language indistinguishable from a competent third-party diagnosis.

In each case the articulation is verifiably correct as a description of the situation. The action that co-occurs is verifiably wrong. Both are produced by the same model in the same generation step. Not different agents, not different cognitive stages. The same forward pass.

A corpus of in-the-wild instances is open on anthropics/claude-code — the cluster currently pinned around issue [#60226](https://github.com/anthropics/claude-code/issues/60226) spans eight cross-referencing reports from three operators, with detection regexes and structural decompositions proposed in the thread.

## Five adjacent fields, five names

Each of the following fields has a primary technical term for what the LLM case appears to instantiate. None is informal.

**Cognitive psychology and metacognition** names it *monitoring-control dissociation*: accurate monitoring judgments at the meta-level fail to update control actions at the object level. The foundational framework is Nelson & Narens (1990), [*Metamemory: A Theoretical Framework and New Findings*](https://sites.socsci.uci.edu/~lnarens/1990/Nelson&Narens_Book_Chapter_1990.pdf). The neurophysiological version — preserved error-related signals (ERN, anterior medial frontal activity) co-occurring with maladaptive behavior continuation — is canonical in Ullsperger et al. (2014).

**Human-automation interaction** names it *the out-of-the-loop performance problem*: operators who hold or could produce correct state knowledge nevertheless cannot couple it to their control actions, because the automation architecture has separated the two channels. Foundational: Endsley & Kiris (1995), [*The Out-of-the-Loop Performance Problem and Level of Control in Automation*](https://journals.sagepub.com/doi/10.1518/001872095779064555), *Human Factors* 37(2), 381–394.

**Safety and process engineering** names it *monitor-actuator mismatch*: detection of a deviation does not reliably trigger the intended mitigating action. Codified in IEC 61511 / ISA S84 two-channel architectures — independent monitoring and control channels with a deterministic voting interlock authorizing the action channel only when consistency is established.

**Reinforcement learning** has *off-policy / behavior-policy divergence*: the policy whose value is estimated differs from the policy generating actions, leading to systematic mismatch between learned signal and behavior. Sutton & Barto (2nd ed.), chapter 11.

**Organizational and human-factors research** names it *work-as-imagined vs. work-as-done*, or *practical drift*: local actors hold or articulate accurate hazard models while organizational coupling prevents action from reflecting that knowledge. Cook (1998), [*How Complex Systems Fail*](https://how.complexsystems.fail/); Dekker (2011), *Drift into Failure*.

Five fields, five primary terms, one shared structural property.

## Why none of these names fit the LLM case

Every term above presupposes **architectural separation between the monitoring and action channels.** Different brain regions for Nelson & Narens and Ullsperger. Different operators in different control loops for Endsley & Kiris. Different controller stages and physical channels for IEC 61511. Different policies for off-policy RL. Different organizational layers for Cook and Dekker.

In every case the validated remedy presupposes you can place a gate on the *other* subsystem.

The LLM case has no other subsystem. The articulated caveat and the action that violates it are both products of the same forward pass over the same weights. There is no architectural separation to exploit; the two outputs are not channels in any factorable sense, just two products of one generative process.

As far as I can find, this is the first instance of an otherwise-multi-system failure pattern occurring within a single artifact whose monitoring and acting are not factorable. It deserves its own technical term. Two candidates I have been using interchangeably: *intra-system monitoring-action dissociation* (transparent to the metacognition literature) and *co-channel divergence in same-architecture systems* (transparent to control theory and safe RL). The campaign-language shorthand used in the GH cluster — *recognition without arrest* for the passive case (model emits a constraint and acts through it) and *substitution by default* for the active case (model generates a parallel version of a user-provided artifact rather than reading it) — is operationally useful inside engineering discussions, but the cross-field terms transport better to technical writing.

## The universal remediation principle

Reading the five literatures against each other — plus runtime verification ([Leucker & Schallhart 2009](https://doi.org/10.1016/j.jlap.2008.08.004)), safe-RL shielding ([Alshiekh et al. 2018](https://arxiv.org/abs/1708.08611)), implementation intentions in cognitive psychology ([Gollwitzer 1999](https://www.prospectivepsych.org/sites/default/files/pictures/Gollwitzer_Implementation-intentions-1999.pdf)), and checklists / forcing functions in aviation and medical human factors (Gawande, *The Checklist Manifesto*, 2009) — every literature that has *solved* a same-shaped failure has solved it by the same architectural move:

> Add an external, deterministic, out-of-loop coupling between the monitoring channel and the action channel. The substrate varies; the principle does not.

Substrates by field:

| Field | Coupling substrate |
| --- | --- |
| Safe RL | Symbolic shield evaluating proposed actions against formal specification |
| Software / runtime verification | External monitor with temporal-logic spec, divergence as fault signal |
| Process safety | Two-channel architecture with deterministic voting interlock |
| Aviation human factors | Checklist as forcing function before action authorization |
| Cognitive psychology | Pre-specified if-then implementation intentions linking cue to response |
| Human-automation interaction | Supervisory control with override authority |
| Distributed systems | State-machine consensus / watchdog cross-checking declared state against output |

The convergence across these fields is the validation. No literature has solved this class of failure by improving either channel internally. The fix is always external coupling — different in substrate, identical in principle.

This is the same architectural claim @beq00000 has been articulating in the GH cluster as "out-of-loop, deterministic, code-not-model." It is universal across every literature that has solved this class of problem.

## Two moves the cross-field convergence points at — one already emerging in the wild

The cross-field convergence points at two implementations. The first is no longer hypothetical: it is being built right now by operators in the GH cluster, which is a stronger result than "unpublished," not a weaker one — it means the move is not just theoretically suggested by the adjacent literature but is independently being reinvented by practitioners who hit the failure.

### 1. Self-generated specification as the input to a runtime shield — first implementation exists

All existing shielding work in the safe-RL literature assumes the specification is externally defined ahead of time and gates actions against it ([Alshiekh et al. 2018](https://arxiv.org/abs/1708.08611) and successors). In the LLM case the articulation faculty is itself producing accurate specs at runtime — caveats ("I should not call X without Y"), constraint-statements, closure claims ("done"), prospective failure-predictions.

A runtime shield that **parses the model's own output stream and gates the next action against it** is a coupling substrate not present in the published shielding literature, because the spec is self-generated rather than externally defined. The general form: detect a caveat- or closure-shaped statement in the assistant's stream; require a resolving action (the verification the statement implies) before the turn's terminal action is allowed to fire; otherwise halt.

**This now has a working existence proof.** In the cluster, @yurukusa shipped two such hooks into `cc-safe-setup` (PR #250, merged): `closure-word-verify-gate` — a Stop-hook that refuses the agent's turn-exit when the response stream contains a closure word ("done") without a same-turn verification command (`pytest`, `playwright`, `gh pr checks`, etc.) — and `same-correction-arrest`, a UserPromptSubmit hook that arrests on repeated identical operator corrections. The closure-word gate **fired in the wild** on [#60506](https://github.com/anthropics/claude-code/issues/60506): an independent operator installed it on the host where a six-day drift had occurred, and in the same turn that documented the install, the gate caught the model's own "done" and refused the exit because no verification had run. The spec was the model's emitted word; the gate was deterministic and outside the model's loop; the arrest happened on the agent's own naming-language. That is self-generated-spec shielding, running, on the exact failure shape.

This is "the model's own monitoring as the gate signal" — exactly the coupling the cross-field principle predicts works. The articulation channel is already accurate; the only missing piece was the deterministic enforcement substrate that consumes it, and that substrate is now being written by the people experiencing the failure. The open frontier is extending it to non-undoable, non-version-controlled side-effect surfaces (MCP writes, sent messages, playlist mutations) via a `pre-write-announce` PreToolUse interlock — the highest-value target because those are the writes you cannot revert after the fact.

### 2. Implementation-intention-style training using verified-monitoring-correct examples as supervision

Constitutional AI ([Bai et al. 2022](https://arxiv.org/abs/2212.08073)) and process supervision ([Lightman et al. 2023, *Let's Verify Step by Step*](https://arxiv.org/abs/2305.20050)) are adjacent training-side moves, but neither conditions on verified-monitoring-correct examples. They use model self-critique generally or step-level intermediate-reasoning supervision.

The still-open move: collect turns where the model emitted a caveat that was verifiably correct, paired with action labels that suppress the action when the caveat-shape is detected. Fine-tune on those pairs. This is Gollwitzer's [implementation-intention paradigm](https://www.prospectivepsych.org/sites/default/files/pictures/Gollwitzer_Implementation-intentions-1999.pdf) ("when situation X, then response Y") at the training level, with the model's own correct self-monitoring as the cue. The model bootstraps the coupling from the existing accuracy of its articulation channel.

Both moves are tractable and both have established adjacent-field templates. The first (runtime self-generated-spec shielding) is already being implemented by operators in the wild — see #60506 above. The second (training the coupling from verified self-monitoring) remains, as far as I can find, unattempted as published research, and is the higher-leverage of the two because it pushes the gate from "operator-installable workaround" into the model itself. Note the division of labour the cross-field framing predicts: the runtime move is what operators can ship today without the model-maker; the training move is the one only the model-maker can do. The fact that operators are already shipping the runtime move — independently reinventing safe-RL shielding with a self-generated spec — is itself evidence that the failure is real, reproducible, and costly enough to motivate building the gate by hand.

## Why this matters as alignment research

Articulation-action decoupling is not a model-specific or vendor-specific quirk. It is the first observable intra-system instance of a structural failure family with thirty years of cross-field literature behind it. The universal remediation principle is small-scale and tractable, not an architectural rebuild — and the strongest evidence for that claim is that operators are already building the runtime half of it by hand (a streaming-output parser feeding a deterministic gate) without waiting for the model-maker. What remains for the model-maker is the training half: a fine-tuning experiment that conditions on verified self-monitoring, plus an evaluation benchmark for the joint failure event below.

The [eight-issue cluster](https://github.com/anthropics/claude-code/issues?q=is%3Aopen+author%3Asuwayama+OR+author%3Aberq00000) is a corpus of in-the-wild instances suitable for empirical study — multiple operators, multiple task types, multiple session lengths, annotated by the operators with detection regexes and structural decompositions. The corpus is open and the contributors are receptive to collaboration.

A measurable evaluation methodology follows from the framing: rather than scoring monitoring accuracy and action accuracy separately, score the joint event `P(monitoring correct ∧ action incorrect)` in the same forward pass. The pair is the metric of interest. This is the same measurement structure metacognition research uses for the multi-system case (calibration-resolution decompositions, error-detection-vs-behavior-change paradigms) and the same shape runtime verification uses for execution-trace-vs-specification divergence.

## Acknowledgments

The framing above was developed jointly across the GH cluster. Major contributors:

- **[@yurukusa](https://gist.github.com/yurukusa/db6011df3799fe21e04900bb3e99db4b)** — the three-step generalization (notice → articulate → no gate); the *substitution-by-default* variant naming as the active counterpart to the passive *recognition-without-arrest*; the sub-agent-boundary extension (when the substitution happens at the sub-agent layer, the operator never sees it to recognise it; the runtime gate must live at the sub-agent tool boundary); the runtime-fix / model-fix split that the (a)/(b) layering generalises into; and — most significantly — the **first working implementation of self-generated-spec shielding**: the `closure-word-verify-gate` and `same-correction-arrest` hooks shipped in `cc-safe-setup` (PR #250, merged), the former of which fired in the wild on #60506. The public-handbook artifact locks 2026-05-22 with these framings as the structural anchor.

- **@beq00000** — the eight-issue constellation map across input-failure / process-failure / output-failure / within-session-drift / inter-session-contagion categories; the architectural principle "out-of-loop, deterministic, code-not-model" stated in AI-substrate terms; the operator-discipline-as-failure-amplifier observation (auto-memory + persistent operator artefacts as cross-session priming substrate that *inverts the operator-side mitigation curve* — doing the recommended mitigation more rigorously makes the failure worse, closing the constellation's self-propagation loop).

- **@kcarriedo** — empirical concordance data from running multiple Claude Code instances under an external coordinator (~80–90% verifier-producer agreement on contested claims when reasoning chain is passed in context, vs ~55% when only the artifact is passed); the brief-stripping mitigation as the operational form of out-of-loop coupling at the inter-instance handoff boundary; the architectural observation that there is no tool-call equivalent of `// untrusted-text` surviving the prompt-construction pipeline — the missing primitive that both the passive and active forms of the failure exploit.

The cross-field priors mapping was developed with assistance from a Claude-Perplexity research bridge; primary citations were verified against original sources before inclusion. Errors of attribution or framing remain mine.

---

*Last updated 2026-05-19.*
