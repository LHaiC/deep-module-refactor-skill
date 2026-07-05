---
name: deep-module-refactor
description: Plan-only deep-module refactoring for shallow chains of over-layered modules. Use when the user wants to turn over-abstracted, scattered data-flow code into a deep module with a clear primary flow, concentrated internal implementation, minimal external API, and explicit internal interfaces. Triggers include deep module refactor, shallow modules, over-abstracted module chain, flatten call chain, simplify public API, structural simplification refactor, 架构简化, 结构性简化重构, 深模块, 浅模块串联, 过度分层, 过度抽象, 数据流分散, 主流程清晰, 压平调用链, 公共接口收敛.
---

# Deep Module Refactor

Use this skill to produce one complete deep-refactor plan. The target is not generic cleanup: convert many shallow modules chained into one large tangled whole into a deep module with a tiny semantic interface and concentrated internals.

Default behavior is plan-only. Do not edit files, run formatters, generate migrations, or update docs unless the user explicitly asks for implementation after the plan.

## Rules

- Ground every conclusion in live code, tests, configs, or docs from the checkout.
- Identify the primary flow first. If there are parallel heads or modes, map each head and the point where flows converge or diverge.
- Move cross-file or cross-module choreography back inside the module: ordering rules, lifecycle steps, transient state, helper coordination, and intermediate data ownership should not leak to callers.
- Prefer deletion, merging, and direct data flow over new abstraction.
- Preserve real boundaries: trust-boundary validation, error handling that prevents data loss, security checks, hardware/runtime calibration, public compatibility, and measured performance constraints.
- Treat a deep module as: minimal external API, explicit internal interfaces only where needed, hidden necessary complexity, no leaked intermediate data structures, and a primary path a maintainer can trace end to end.
- If the target module is unclear, inspect likely paths first and ask only after exploration cannot disambiguate it.
- If the repo has `CONTEXT.md`, ADRs, architecture docs, or module READMEs, read the relevant ones, but verify them against current code.

## Required Analysis

Before recommending a design, inspect:

- module entrypoints, exports, public types/functions/classes, and configuration;
- real callers and downstream dependencies;
- tests, fixtures, examples, and integration paths;
- data structures crossing the module boundary;
- current control flow, including callbacks, registries, async jobs, dispatch, dependency injection, or backend selection;
- lifecycle and ordering constraints now enforced by caller-side choreography.

For code search, use structural/code-index tools first when available; otherwise use fast text search such as `rg`.

## Required Diagnosis

Produce a diagnosis of why the current shape is shallow instead of deep:

- the primary flow is split across too many files or abstractions;
- the public interface is nearly as complex as the implementation;
- callers must know internal order, lifecycle, staging, modes, or intermediate structures;
- helpers or pure functions were extracted so far that the bug-prone behavior lives in their call choreography;
- middle data structures rename, shuttle, or mirror fields without owning semantics;
- several shallow modules form one large tangled whole instead of one module hiding its internal complexity.

For each major layer, run a deletion test: if this layer disappeared, would the design become clearer with less coupling, or would necessary complexity just move somewhere worse?

## Required Design Output

Recommend one complete deep-refactor design, not a list of unrelated options. Include:

- the primary flow after refactor, including parallel heads if they remain;
- the minimal external API: every public function/type/config value must have a reason to stay public;
- the internal interfaces: private types/functions/stages that structure the implementation without leaking to callers;
- what caller-side choreography, lifecycle management, and intermediate state moves inside the module;
- which shallow modules, data wrappers, helper layers, or redundant APIs should be merged, hidden, or deleted;
- compatibility constraints and migration steps;
- tests and scenarios that must prove the new deep module preserves behavior.

If key design choices remain unknown, ask focused questions, then still recommend a default when the inspected code points to one.

## Output Shape

Use this structure unless the user requested another format:

1. `Current Primary Flow`
2. `Why This Is Shallow`
3. `Deep Module Target`
4. `Minimal External API`
5. `Internal Interfaces`
6. `Complete Refactor Plan`
7. `Validation`
8. `Assumptions`

End with a clear stop condition: no files were changed, and implementation requires an explicit follow-up request.
