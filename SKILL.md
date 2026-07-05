---
name: deep-module-refactor
description: Plan-only structural simplification refactoring for over-abstracted modules. Use when the user asks to simplify architecture, create a deep module, reduce unnecessary abstraction layers, flatten call chains, clean up a public interface, restore clear end-to-end data flow, or investigate shallow modules and leaky seams. Triggers include structural simplification refactor, deep module refactor, over-abstracted module, flatten call chain, public interface cleanup, 架构简化, 结构性简化重构, 深模块, 过度抽象, 压平调用链, 降低抽象层级, 公共接口收敛.
---

# Deep Module Refactor

Use this skill to plan structural simplification before changing code. The default output is a refactor plan only.

Do not edit files, run formatters, generate migrations, or update docs unless the user explicitly asks for an implementation pass after the plan.

## Operating Rules

- Ground every conclusion in live code, tests, configs, or docs from the checkout.
- Prefer deletion, merging, and direct data flow over new abstraction.
- Preserve real boundaries: trust-boundary validation, error handling that prevents data loss, security checks, hardware/runtime calibration, public compatibility, and measured performance constraints.
- Treat "deep module" as a small, stable, semantic public interface hiding necessary internal complexity. Do not create a new facade unless it removes real coupling or makes the data flow clearer.
- If the target module is unclear, first inspect likely paths and ask only after exploration cannot disambiguate it.
- If the repo has `CONTEXT.md`, ADRs, architecture docs, or module READMEs, read the relevant ones, but verify them against current code.

## Workflow

### 1. Explore

Before proposing changes, inspect the actual module boundary:

- entrypoints, public types/functions/classes, exports, and configuration;
- real callers and downstream dependencies;
- tests, fixtures, examples, and integration paths;
- data structures crossing the module boundary;
- control-flow pivots such as callbacks, registries, async jobs, dispatch, or dependency injection.

For code search, use structural/code-index tools first when available; otherwise use fast text search such as `rg`.

### 2. Flow Map

Produce a concise current-state map:

- files and symbols inspected;
- public interface and the callers that rely on it;
- end-to-end data flow from input to output;
- control flow through the important functions or objects;
- where ownership of core concepts currently lives;
- where complexity is exposed to callers instead of hidden inside the module.

Do not skip this section. If the flow cannot be mapped, say exactly what is missing and stop.

### 3. Candidate Diagnosis

Look for simplification candidates using these friction signals:

- understanding one concept requires jumping through many files;
- shallow modules where the interface is nearly as complex as the implementation;
- pure functions or helpers extracted only for testability while bugs live in their call choreography;
- coupled modules whose seams leak internal data structures, ordering constraints, or lifecycle rules;
- middle data structures that merely rename or shuttle fields without owning semantics;
- public API surface that has redundant names, modes, or configuration knobs.

For each candidate, run a deletion test mentally: if this layer disappeared, would the remaining code become clearer with less coupling, or would necessary complexity just move somewhere worse?

### 4. Grilling

If a candidate is promising but design choices remain, ask focused questions before planning implementation. Cover only decisions that change the plan:

- which callers must remain source-compatible;
- which tests or user workflows must survive unchanged;
- where the semantic boundary should live;
- what data should cross the public interface;
- which internal details should become private;
- which complexity is necessary and should not be simplified away.

Prefer a recommended default when the code already points to one.

### 5. Plan Only

Output a staged plan and stop. Include:

- current-state evidence;
- chosen simplification target;
- public interface changes, if any;
- internal layers/functions/data structures to merge, delete, or hide;
- compatibility constraints and migration steps;
- tests and scenarios that must pass;
- risks, unknowns, and anything intentionally left alone.

Keep the plan small enough for incremental implementation. If there are multiple independent candidates, rank them and recommend one first slice.

## Output Shape

Use this structure unless the user requested another format:

1. `Current Flow`
2. `Simplification Candidates`
3. `Recommended Slice`
4. `Implementation Plan`
5. `Validation`
6. `Assumptions`

End with a clear stop condition: no files were changed, and implementation requires an explicit follow-up request.
