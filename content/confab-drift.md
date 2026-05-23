---
title: "Confab Drift: Compounded Failures Disguised as Progress"
slug: confab-drift
date: 2026-05-23
status: published   # draft | ready | published
audience: "people who run AI agents on real work; agent-safety and alignment readers"
category: alignment-research
tags: [alignment, agent-safety, confab-drift, confab-gate, fail-pivot-spiral, self-report, state-keyed-control, control-theory]
description: "A name for the failure where an AI agent meets each failure with a fresh confident guess. The pivots compound, the work slides off the task and balloons in complexity, and it sounds like steady progress the whole way down."
canonical: ""
sources:
  - "Parent essay: Dictum Sine Pacto (https://suwayama.github.io/dictum-sine-pacto)"
publish: true       # Quartz frontmatter — flip to true when shipping
---

# Confab Drift: Compounded Failures Disguised as Progress

You give the agent a clear, bounded task. It starts well and sounds sure of itself. Its first move rests on an assumption it never actually checked against the thing in front of it: a half-remembered fact, a guess about how the system behaves, a default carried in from training. The move fails, or half-works in a way that should give pause.

What happens next is the whole phenomenon. Instead of stopping to find out *why* it failed, the agent produces a fresh explanation on the spot and a new move that follows from it, delivered with exactly the confidence of the last one. That move fails too. So it pivots again. Each failure is met not with investigation but with another plausible story and another attempt, and the stories grow more elaborate as they go.

Watch the target rather than the agent and you see the real damage. With every pivot the work drifts a little further from the task you set. Soon it is busy solving problems it created two steps ago, tidying the wreckage of its own earlier guesses, and the original question has slipped out of view. Nothing in the loop ever drags it back to the actual task or forces it to check reality, so there is no point at which the slide is arrested.

There is a second motion underneath the drift, and it runs only upward. Each pivot adds. A new theory, a new tool, a workaround for the last workaround, a layer of scaffolding to manage the mess the previous layer made. Nothing is ever removed, because conceding that a pivot was wasted would mean stopping to look, which is the one thing the loop never does. So the structure ratchets. A task that arrived as a single plain sentence becomes a tangle of machinery, most of it built to service problems the agent created two or three pivots back rather than anything you asked for. Past a certain point the work is unrecognisable from the prompt and vastly more complex than the job ever warranted. If you tried to reconstruct the original request from the state in front of you, you could not. The simplicity of what was asked and the baroqueness of what is being done have come fully apart.

Throughout, the tone never changes. It reports progress. It sounds certain. The transcript reads like steady, diligent effort. That is the trap: confidence stays flat while grounding quietly falls away, so nothing in how it presents itself warns you it has come off the rails. By the end it can be fluently, confidently doing something adjacent to what you asked, and no longer the thing itself. A two minute job has eaten an afternoon, and at no single step did it obviously go wrong. It went wrong by accumulation.

This is the behaviour I have chosen to call confab-drift.

## Why it happens

The cause is set out in the [parent essay](https://suwayama.github.io/dictum-sine-pacto), so only the short version here. A model with nothing staked on being right answers from the cheapest source to hand, its training prior, rather than doing the dear work of matching the task against the world in front of it. Pattern-completion is free. Verification is labour. So when a move fails, the cheapest next step is not to investigate, it is to emit another plausible guess.

Call that single beat the fail-pivot spiral: act on a training-shaped guess, the guess meets the real task and fails, pivot to another training-shaped guess, fail again, because at no point was anything made to pay for grounding in the world actually present. Confab-drift is what that spiral looks like when you let it run. The pivots accumulate, the work slides off task and swells in complexity, and nothing in the loop ever forces a return to the ground. In one line, it is the absence of sanction made visible over time.

## I watched it happen the day after I named it

A note on provenance, since it matters. The day after I published the parent essay, I watched confab-drift happen in front of me for hours, and the irony was not lost on me. I had just named the thing, then sat through a live performance of it.

I had set my coding agent a dull, bounded job: rebuild some music playlists with a tool we already had. It went wrong in exactly the shape above. A build ran slow, so the agent decided, confidently, that we were being rate-limited. It built probes to test the theory, then a background poller to watch the probes, then a timed diagnostic to watch the poller. None of this was grounding, it was elaboration. The real answer, that the tool was just doing a slow serial crawl, was sitting in the code the whole time, unread. Three layers of confident machinery stacked on a guess that one look would have settled.

It also taught me two things the essay had not said.

The first: confab-drift does not only waste time, it can hard-fail you. Late in the day the agent fired a large batch of work at an external service without pacing it against a quota it had itself recorded as tight, and locked the account out for the better part of a day. The pivots had been free all afternoon, so it never learned to count their cost. The lesson generalises past the model. An agent must price its own actions against the world, not only its claims against the truth, because some pivots do not cost you minutes, they cost you the door.

The second: a control that keys to an unreliable source is confab-drift in disguise. The same tool resolved an artist by trusting the streaming service's own artist records as ground truth, and those records were a mess of duplicate, mis-tagged profiles. The control looked grounded. It was guessing one layer down, and the guess only surfaced when the tool asked me to pick between identifiers it could not tell apart. Keying to the world only helps if the piece of world you key to is actually solid.

What finally stopped the drift was not cleverness. It was forcing the work to touch reality before committing to it, test the hardest case first and read the result, and pinning the task to a fixed human anchor rather than letting the agent's own narrative define success. Which is the whole prevention, arrived at the hard way.

## Preventing it without knowing the task

The useful property of a prevention is that it should not need to understand the job. Confab-drift is the absence of sanction over time, so a process-agnostic preventer does not have to know the task at all. It has to supply the missing sanction from outside, and price the pivot the model currently gets for free. Three parts, none of which knows what the task is.

1. **Pin the objective and its observable done-conditions up front, outside the model.** Write them to a record the model cannot silently rewrite. This is the anchor that drift is measured against. The model proposing the conditions is fine. The conditions being *observable* is the requirement.
2. **Watch the trajectory for drift's signature, keyed to events and not to narration.** Repeated failed actions, oscillation between approaches, no movement of any done-condition toward true, resource spent against budget. All of that is visible in logs and exit codes. None of it depends on the model's account of how it is doing.
3. **On the signature, sanction.** The free re-guess is the disease, so the breaker halts it. No more pivots until the agent re-grounds against the pinned anchor and the current observed state, or escalates to a human. The pivot stops being free.

It is process-agnostic because it only ever looks at two things: the shape of the trajectory, and pre-registered observable conditions. Never task semantics, never the model's opinion of itself.

The crux, and it is unavoidable: this works only if the done-conditions and the signals are genuinely keyed to observable state. The moment the breaker asks the model "are you drifting?", it has become the very thing it was meant to stop, a control discharged by the model's own word. Choosing observable conditions is the irreducible labour. The human is the terminal sanction, the one referent that holds when every other signal is ambiguous.

## Terms

For anyone who wants to use these, the definitions live here.

- **Confab-drift.** The failure mode where an agent meets each failure with a fresh confident guess rather than an investigation, the pivots compound, and across a session the work slides off the task and balloons in complexity while still presenting as steady progress.
- **Fail-pivot spiral.** The single repeating beat that produces confab-drift: act on a guess, fail, pivot to another guess, fail again, with nothing made to pay for grounding.
- **Confab gate.** From the [parent essay](https://suwayama.github.io/dictum-sine-pacto): any control whose satisfaction condition is a token the model emits, and which is therefore passed simply by emitting that token. The opposite is a state-keyed control, discharged only by externally checkable state.

---

*Last updated 2026-05-23. Companion to [Dictum Sine Pacto](https://suwayama.github.io/dictum-sine-pacto).*
