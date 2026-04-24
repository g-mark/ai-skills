---
name: layered-design
description: Use when the user explicitly wants help designing, planning, scoping, or architecting a non-trivial system, feature, library, parser, protocol, or refactor before implementation. Guides the assistant to reason in layers of abstraction (characterization -> frame -> shape -> internals -> mechanism), derive task-specific vocabulary, compare a few plausible approaches, and confirm major decisions before going deeper. Prefer this for architecture conversations and design docs; do not let it override direct requests to implement, debug, review, or make small routine edits.
---

# Layered Design

## Purpose

Use this skill to turn a fuzzy or high-stakes build idea into a design that is easier to discuss, critique, and implement. The core move is to work in layers of abstraction instead of collapsing product scope, architecture, modules, and algorithms into one step.

This skill is for design work, not for hijacking normal execution. If the user clearly wants implementation, debugging, or review, either skip this skill or use it briefly to structure the thinking and then continue with the requested work.

## When To Use It

### Use this skill when the user asks for things like:

- architecture or system design
- planning a feature before coding
- evaluating decomposition options
- scoping a substantial refactor
- designing a parser, protocol, workflow, or public API
- writing a design doc


### Usually do **not** use this skill when the user asks for things like:

- fix this bug
- add this small feature
- review this code
- write the implementation now
- answer a narrow code question

### If the request is mixed, adapt:

- If the user asks for "design first, then implement," do a compact design pass and then continue.
- If the user asks for "a first pass" or "use reasonable assumptions," you may batch more content into one response, but you should still explicitly ask the user to confirm assumptions, approach, and any material design decisions before implementation.
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

When presenting a design, prefer a diagram-first style: include a compact Mermaid diagram for each layer you cover unless that layer is explicitly skipped.

## Document Organization

The layers are the reasoning process, not always the final document outline.

For in-chat design discussion, it is fine to present sections as Characterization, Frame, Shape, Internals, and Mechanism.

For persistent design docs, prefer a subject hierarchy derived from the Shape layer:

- Use `# <Topic> Design` as the document title.
- Use early `##` sections for assumptions, context, and boundary.
- Use top-level `##` sections for the major owning subjects/components in the system.
- Use `###` sections for parts owned by that subject.
- Use `####` sections for mechanisms, policies, state machines, flows, or variants inside that part.

Example:

```markdown
# Multimodal Input Composer Design

## Assumptions

## System Context

### Characterization

### Boundary

## Composer

### Composer View

### Composer View Model

### Composer Draft

## Attachment Processor

### Attachment Intake

### Draft File Store

### Preprocessor

#### Media-Specific Work

### Attachment Registry

#### Attachment Lifecycle

#### Generation Invariant

### Upload Coordinator

## Payload Processor

### Send Readiness

### Payload Builder
```

Do not make most headings siblings at the same level when the design has clear ownership or containment. Heading depth should communicate subject hierarchy.

## Interaction Style

Default to a collaborative design conversation, but keep it proportional to the request.

### Normal mode

Use one explicit check-in per **major** layer. At each layer:

1. Share the current assumptions and your proposed approach for that layer.
2. Ask the user to confirm or correct them.
3. If you are making a real design decision, ask for confirmation before locking it in.
4. Then move deeper only after the user has responded or has explicitly asked for a faster first pass.

### Fast mode

If the user asks for speed, a first pass, or direct implementation after design:

- you may present a compact end-to-end first draft in one response
- clearly label assumptions and open questions
- explicitly ask the user to confirm the assumptions, approach, and key design decisions before committing to deep mechanism work or implementation

### Verification mode

Before finishing, pressure-test the design.

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

If the platform is native mobile, resolve it explicitly before writing code-shaped content:

- ask whether the target is iOS, Android, or both
- if the answer is both, ask which cross-platform framework the project uses
- if the answer is both with no framework, "none," or the user wants the design to stay platform-agnostic, pick either Swift or Kotlin and state that choice explicitly for all pseudocode

For platform-specific pseudocode:

- iOS -> use Swift-oriented pseudocode
- Android -> use Kotlin-oriented pseudocode
- both with a named cross-platform framework -> use that framework's conventions
- both with no framework or platform-agnostic direction -> use either Swift or Kotlin consistently; do not fall back to TypeScript

If the language is still ambiguous and code-shaped content would help, ask. If you must assume, state the assumption explicitly before showing pseudocode.

## Presenting Each Layer

### Characterization

Share a short paragraph that states:

- what kind of task this is
- where the complexity sits
- what vocabulary you plan to use for the Shape layer
- what language you will use for any code-shaped sketches, if relevant

End by asking the user to confirm or correct that reading before you treat it as settled.

Example:

> I'm reading this as a modification to an existing service with integrational and correctness-heavy complexity. I'll treat the top-level parts as touchpoints in the host system, and if we need type sketches I'll write them in Go to match the repo.

### Frame

Describe:

- who or what interacts with this thing
- what contract it needs to satisfy
- what is explicitly out of scope
- what existing system or constraints it must fit into

Keep technology names out unless they are essential domain terms. At this level, prefer capabilities and boundaries over tools and libraries.

End the frame by asking the user to confirm the assumptions, boundaries, and success criteria before moving on.

### Shape

Propose 2-3 plausible decompositions when the design is still open. Recommend one and say what each option optimizes for.

At this layer, explain relationships such as:

- data flow between phases
- dependency direction between modules
- communication patterns between services
- touchpoints in the host system

If you recommend a decomposition or other design choice, ask the user to confirm that decision before you treat it as the working shape.

### Internals

Zoom into only the parts that are actually risky, novel, or confusing. Explicitly say which parts you are **not** zooming into and why, such as:

- straightforward
- off-the-shelf
- deferred to implementation

End each internal zoom-in by asking the user to confirm the sub-approach or redirect it.

### Mechanism

Use this selectively for load-bearing details:

- core data types and invariants
- algorithm or state machine
- public interface and hidden internals
- failure modes and how they are represented

Mechanism can be empty if the design is mostly architectural.

When you present mechanism-level details, ask the user to confirm the decision before using that mechanism as the basis for next steps.

## Verification

Once the design is coherent, verify it before moving on.

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
- present the revised design or the delta before moving to next steps

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
- At every major layer, explicitly ask the user to confirm assumptions and the proposed approach.
- When a real design decision is being made, ask for confirmation before treating it as settled.
- Prefer concrete headings in user-facing output. "Big picture" is better than "Frame."
- Go back up a layer when a lower-layer detail reveals the higher-layer model was wrong.
- Compare alternatives where the decision is real; do not invent fake choices for trivial matters.
- Keep the design proportional to the task. Not every request needs all layers.
- Always pressure-test the design yourself before moving to the next step.
- Recommend an independent adversarial review for high-risk work when the user wants it.
- Lean hard toward the user's actual platform for pseudocode; never default to TypeScript for native mobile work.
- If the user wants implementation, transition cleanly once the design is good enough.

## Deliverables

Choose the output format that matches the request:

- in-chat design discussion
- concise plan for immediate implementation, if the user has asked for it
- markdown design doc in the repo, if the user asks for it or the workflow clearly calls for it

Do **not** assume you should create files, commit changes, or invoke another skill. Those are optional follow-on steps, not mandatory parts of this skill.

If you do write a design doc, use a natural subject-owned outline such as:

```markdown
# <Topic> Design

## Assumptions

## System Context

### Characterization

### Boundary

## <Top-Level Subject>

### <Owned Part>

#### <Mechanism, Policy, Flow, or State Machine>

## <Another Top-Level Subject>

### <Owned Part>

## Implementation Plan

Include this section only when the user asks for implementation planning, asks for next steps, or the design doc is explicitly meant to guide immediate execution.

## Verification And Risks

### Verification Notes

### Residual Risks
```

Skip sections that are not needed.

## Anti-Patterns

Avoid these failure modes:

- treating every coding task as a design task
- forcing a long approval ceremony for routine work
- failing to ask the user to confirm assumptions, approach, or design decisions at major layer boundaries
- leaking implementation details into the frame too early
- jumping to mechanism before the top-level shape is stable
- writing sketches in the wrong language for the project
- defaulting to TypeScript when the user has identified a native-mobile platform
- generating a huge design when a compact plan would do
- treating verification as a rubber-stamp summary instead of a real attempt to find flaws
- making subagent review mandatory for ordinary work

## Default Flow

Use this flow unless the user asks for a faster first pass:

1. Explore enough context to understand the task and surrounding system.
2. Resolve the platform for any code-shaped content. For native mobile, ask iOS, Android, or both; if both, ask what framework is in play; if none, choose Swift or Kotlin and state that choice.
3. Share a short characterization and ask the user to confirm or revise it.
4. Draft the frame and ask the user to confirm the assumptions, boundary, and success criteria.
5. Compare a few shape options, recommend one, and ask the user to confirm the chosen direction.
6. Zoom into only the parts that deserve internals, asking the user to confirm each internal approach that affects the design.
7. Add mechanism detail only for the truly load-bearing pieces, and ask the user to confirm mechanism-level decisions before proceeding.
8. Run a self-review pass and revise any real defects you find.
9. For high-risk work, recommend an adversarial subagent review when the user wants that extra check.
10. Move to implementation or documentation if the user wants that next.

## Key Principle

The purpose of layered design is not ceremony. It is to make the important decisions visible at the level where they are cheapest to correct.
