# Integrate frontend-specialist into Critical Workflow

## Objective

Update the Critical Workflow so that `frontend-specialist` contributes front-end technical analysis and verification for front-end tasks, while `architector` remains the primary planner and verifier.

## Approach

Add dedicated specialist sub-steps when a task is front-end related:
- **4.1a**: `frontend-specialist` analyzes front-end requirements and produces a front-end technical specification.
- **4.1b**: `architector` consumes the spec and produces the complete implementation plan.
- **4.5a**: `frontend-specialist` verifies front-end implementation quality.
- **4.5b**: `architector` verifies overall plan adherence.

## Files to modify

1. `.kilo/agents/frontend-specialist.md`
   - Add a `Process` section defining:
     - How to analyze front-end tasks (frameworks, component structure, state management, CSS architecture, responsive behavior, API integration).
     - How to produce a front-end technical specification (save as `.kilo/plans/<YYYYMMDD>-<plan-name>-frontend-spec.md`).
     - How to verify front-end implementation in step 4.5 (check component patterns, CSS/TS correctness, accessibility, performance).
   - Ensure permissions allow writing `*.md` files (already present).

2. `.kilo/commands/critical-workflow.md`
   - In step `4.1. Analysis and Planning`, add a conditional sub-step `4.1a` for front-end tasks:
     - Plan Agent assigns `frontend-specialist` to analyze and produce a front-end spec.
     - Then Plan Agent assigns `architector` to produce the full plan, referencing the spec.
   - In step `4.5. Verification`, add a conditional sub-step `4.5a` for front-end tasks:
     - Plan Agent assigns `frontend-specialist` to verify front-end quality.
     - Then Plan Agent assigns `architector` for overall verification.
   - Update the Global Plan Example to show the front-end sub-steps.

## Out of scope

- No changes to other agents or workflows.
- No changes to `kilo.jsonc` or permissions beyond the agent/process definition.
