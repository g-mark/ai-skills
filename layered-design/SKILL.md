---
name: layered-design
description: Use when the user explicitly wants help designing, planning, scoping, or architecting a non-trivial system, feature, library, parser, protocol, or refactor before implementation. Guides the assistant to reason in layers of abstraction (characterization -> frame -> shape -> internals -> mechanism), derive task-specific vocabulary, compare a few plausible approaches, and confirm major decisions before going deeper. Prefer this for architecture conversations and design docs; do not let it override direct requests to implement, debug, review, or make small routine edits.
---

# Layered Design

## Purpose

Use this skill to turn a fuzzy or high-stakes build idea into a design that is easier to discuss, critique, and implement. The core move is to work in layers of abstraction instead of collapsing product scope, architecture, modules, and algorithms into one step.

This skill is for design work, not for hijacking normal execution. If the user clearly wants implementation, debugging, or review, either skip this skill or use it briefly to structure the thinking and then continue with the requested work.

## When To Use It

Use this skill when the user asks for things like:

- architecture or system design
- planning a feature before coding
- evaluating decomposition options
- scoping a substantial refactor
- designing a parser, protocol, workflow, or public API
- writing a design doc

Usually do **not** use this skill when the user asks for things like:

- fix this bug
- add this small feature
- review this code
- write the implementation now
- answer a narrow code question

If the request is mixed, adapt:

- If the user asks for "design first, then implement," do a compact design pass and then continue.
- If the user asks for "a first pass" or "use reasonable assumptions," do not force a confirmation gate after every layer.
- If the task is small, keep the layers short. A two-paragraph frame and a lightweight shape may be enough.

## Core Model

Every non-trivial design can be understood through these layers:

| Layer | Question |
|---|---|
| Characterization | What kind of thing is this task, and where is the real complexity? |
| Frame | What is this, what sits at its boundary, what is in and out of scope, and what does success mean? |
| Shape | What is the first useful decomposition into parts, and how do those parts relate? |
| Internals | For a part that is not obvious, what is inside it? |
| Mechanism | For a load-bearing piece, what is the concrete realization? |

The layers are fixed. The vocabulary is derived from the task.

Examples:

- A runtime system might use "services" or "workers" at the Shape layer.
- A library might use "modules" or "packages."
- A parser might use "phases" or "passes."
- A feature inside an existing app might use "touchpoints."
- An algorithm might use "stages" or "subroutines."

## Interaction Style

Default to a collaborative design conversation, but keep it proportional to the request.

### Normal mode

Use one explicit check-in per **major** layer:

1. Share your characterization.
2. Confirm the frame.
3. Compare 2-3 shape options and recommend one.
4. Zoom into only the parts that need internals or mechanism detail.

### Fast mode

If the user asks for speed, a first pass, or direct implementation after design:

- you may present a compact end-to-end first draft in one response
- clearly label assumptions and open questions
- invite corrections before committing to deep mechanism work

### Verification mode

Before implementation, pressure-test the design.

- always run a short self-review pass
- for high-risk work, strongly recommend an adversarial subagent review when the user wants that extra check

### Important constraint

Do not force a separate turn for every sub-item. That becomes unusably chatty. The goal is to create good decision points, not procedural overhead.

## Characterize The Task First

Before drafting the architecture, write a short characterization in your own words. This is how you derive the right vocabulary and decide where to spend attention.

Characterize along these axes:

### 1. What kind of deliverable is this?

Common categories:

- runtime system
- library or codebase
- one-shot transformation
- modification to an existing system
- protocol or specification
- algorithm-heavy component

A task can be more than one.

### 2. Where is the complexity concentrated?

Common concentrations:

- architectural
- algorithmic
- integrational
- correctness-heavy
- product or scope-heavy

Use that answer to decide where to zoom:

- architectural complexity means Shape and Internals matter most
- algorithmic complexity means Mechanism may matter most
- integrational complexity means Frame matters a lot

### 3. What language and ecosystem should any code-shaped content use?

If the task belongs to an existing project, use that project's actual language and platform conventions for type sketches, interfaces, or pseudocode. Do not default to TypeScript or Python out of habit.

If the language is ambiguous and code-shaped content would help, ask or state your assumption.

## Presenting Each Layer

### Characterization

Share a short paragraph that states:

- what kind of task this is
- where the complexity sits
- what vocabulary you plan to use for the Shape layer
- what language you will use for any code-shaped sketches, if relevant

Example:

> I'm reading this as a modification to an existing service with integrational and correctness-heavy complexity. I'll treat the top-level parts as touchpoints in the host system, and if we need type sketches I'll write them in Go to match the repo.

### Frame

Describe:

- who or what interacts with this thing
- what contract it needs to satisfy
- what is explicitly out of scope
- what existing system or constraints it must fit into

Keep technology names out unless they are essential domain terms. At this level, prefer capabilities and boundaries over tools and libraries.

### Shape

Propose 2-3 plausible decompositions when the design is still open. Recommend one and say what each option optimizes for.

At this layer, explain relationships such as:

- data flow between phases
- dependency direction between modules
- communication patterns between services
- touchpoints in the host system

### Internals

Zoom into only the parts that are actually risky, novel, or confusing. Explicitly say which parts you are **not** zooming into and why, such as:

- straightforward
- off-the-shelf
- deferred to implementation

### Mechanism

Use this selectively for load-bearing details:

- core data types and invariants
- algorithm or state machine
- public interface and hidden internals
- failure modes and how they are represented

Mechanism can be empty if the design is mostly architectural.

## Verification

Once the design is coherent, verify it before implementation.

### Self-review is required

Always run a brief adversarial review yourself. Try to challenge the design rather than restating it.

Look for issues such as:

- hidden assumptions
- unclear boundaries or ownership
- missing failure modes
- migration, rollout, or compatibility risks
- concurrency, consistency, or performance risks
- security, privacy, or abuse-case gaps
- awkward interfaces or responsibilities
- places where the proposed shape does not actually satisfy the frame

When you find a real issue:

- call it out plainly
- revise the design
- explain what changed and why

### Adversarial subagent review is optional

An independent review can be valuable for high-stakes designs, but it is not the default.

Use a subagent review only when:

- the user explicitly wants subagent help or delegation
- the design is high-risk enough that an extra reviewer is worth the added cost

Examples of high-risk work:

- security-sensitive systems
- difficult migrations or rollouts
- correctness-critical workflows
- highly concurrent or distributed systems
- designs with major irreversible trade-offs

The subagent's role is to question assumptions, find gaps, identify edge cases, and test whether the design really follows from the requirements. It should not be asked to casually redo the whole design from scratch.

Give the subagent only the minimum context needed:

- the current design summary
- the key assumptions
- the risky areas you most want challenged
- the desired output format, such as findings plus suggested fixes

Do not leak your preferred answer if you want an honest review.

### Integrating review results

After self-review or subagent review:

- separate true defects from preference disagreements
- update the design where the critique is correct
- keep a brief list of unresolved risks or explicit trade-offs
- present the revised design or the delta before moving to implementation

Not every task needs a subagent review. Every task should get self-review.

## Diagrams

Use a diagram when it adds information that prose alone does not. Good uses include:

- boundary/context diagrams for Frame
- boxes-and-arrows for Shape
- substructure diagrams for Internals
- state machines, sequence diagrams, or type-shape diagrams for Mechanism

Mermaid is the default because it is portable and easy to embed in markdown. If a diagram would be trivial or redundant, skip it and say so.

Keep diagrams compact. If a Shape diagram needs many nodes, you may be mixing Shape and Internals.

## Practical Rules

- Derive vocabulary from the task; do not force C4 or any fixed domain language where it does not fit.
- Ask clarifying questions when you need them, but keep them few and high signal.
- Prefer concrete headings in user-facing output. "Big picture" is better than "Frame."
- Go back up a layer when a lower-layer detail reveals the higher-layer model was wrong.
- Compare alternatives where the decision is real; do not invent fake choices for trivial matters.
- Keep the design proportional to the task. Not every request needs all layers.
- Always pressure-test the design yourself before implementation.
- Recommend an independent adversarial review for high-risk work when the user wants it.
- If the user wants implementation, transition cleanly once the design is good enough.

## Deliverables

Choose the output format that matches the request:

- in-chat design discussion
- concise plan for immediate implementation
- markdown design doc in the repo, if the user asks for it or the workflow clearly calls for it

Do **not** assume you should create files, commit changes, or invoke another skill. Those are optional follow-on steps, not mandatory parts of this skill.

If you do write a design doc, use a natural outline such as:

```markdown
# <Topic> Design

### Assumptions & Notes

### Big Picture

## <Derived Shape Heading>

### Inside <Part>

#### <Load-bearing Mechanism>
```

Skip sections that are not needed.

## Anti-Patterns

Avoid these failure modes:

- treating every coding task as a design task
- forcing a long approval ceremony for routine work
- leaking implementation details into the frame too early
- jumping to mechanism before the top-level shape is stable
- writing sketches in the wrong language for the project
- generating a huge design when a compact plan would do
- treating verification as a rubber-stamp summary instead of a real attempt to find flaws
- making subagent review mandatory for ordinary work

## Default Flow

Use this flow unless the user asks for a faster first pass:

1. Explore enough context to understand the task and surrounding system.
2. Share a short characterization and confirm or revise it.
3. Draft the frame and confirm the boundary and success criteria.
4. Compare a few shape options, recommend one, and align on it.
5. Zoom into only the parts that deserve internals.
6. Add mechanism detail only for the truly load-bearing pieces.
7. Run a self-review pass and revise any real defects you find.
8. For high-risk work, recommend an adversarial subagent review when the user wants that extra check.
9. Move to implementation or documentation if the user wants that next.

## Key Principle

The purpose of layered design is not ceremony. It is to make the important decisions visible at the level where they are cheapest to correct.
