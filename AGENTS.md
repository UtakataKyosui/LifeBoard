# LifeBoard Implementation Rules

## 1. Purpose

LifeBoard is a personal life-management application for managing routines, recurring tasks, execution records, and measurements.

The project prioritizes correctness, maintainability, and simplicity.

Do not introduce abstractions, dependencies, or architectural complexity without a concrete requirement.

## 2. Architecture

- Use pnpm workspace for monorepo management.
- NestJS is responsible for API endpoints and application use cases.
- Keep domain logic independent of NestJS, Notion, Mastra, and Cloudflare.
- Controllers must not contain business logic.
- Application Services orchestrate use cases.
- External API access must be encapsulated in adapters or services.
- Avoid circular dependencies between modules and packages.
- Prefer simple modules over premature package extraction.
- Do not introduce an ORM or database without an explicit requirement.

## 3. TypeScript and Validation

- Enable TypeScript strict mode.
- Do not use `any` unless its necessity is explicitly documented.
- Treat external data as untrusted.
- Validate API inputs and external data using Zod.
- Infer types from Zod schemas where appropriate.
- Avoid unnecessary duplication between DTOs and schemas.
- Use explicit types for domain identifiers, dates, and measurements when ambiguity could cause defects.
- Never silently replace missing measurements with zero.

## 4. Domain Rules

- One ExecutionRecord represents one execution.
- Multiple ExecutionRecords may belong to the same routine and day.
- Measurements are optional for an execution.
- Daily and weekly summaries are derived from execution records.
- Execution occurrence and measurement achievement must be evaluated separately.
- Schedule calculations must be deterministic.
- Use the configured time zone when evaluating routine schedules.
- Do not use LLMs for deterministic schedule calculations.
- Changes to routine rules must not unintentionally modify historical records.

## 5. Notion Integration

- Notion is the primary persistence provider for the initial implementation.
- Keep Notion-specific data structures outside domain logic.
- Validate data mapped from Notion before using it in application logic.
- Explicitly handle API errors, rate limits, and missing properties.
- Do not report successful persistence before Notion confirms the operation.
- Do not assume database transactions or unique constraints are available.
- Keep credentials in server-side secrets.
- Never include access tokens or private user content in logs.

## 6. AI Integration

- Use Mastra for AI-related workflows.
- Prefer Cloudflare Workers AI as the initial inference provider.
- AI-generated content is a proposal, not confirmed application state.
- Require explicit user approval before persisting AI-generated changes.
- Validate structured AI output before use.
- Do not let AI bypass domain validation.
- Keep basic routine management available when AI services fail.
- Show sources and uncertainty when proposing actions based on external knowledge.
- Avoid unnecessary AI calls for deterministic operations.

## 7. Code Quality

- Follow existing naming and directory conventions.
- Use descriptive names reflecting domain concepts.
- Keep functions focused on one responsibility.
- Prefer readable code over clever abstractions.
- Avoid unnecessary classes, generic utilities, and wrapper layers.
- Do not introduce dependencies for functionality already covered by the standard library.
- Handle expected errors explicitly.
- Do not silently swallow exceptions.
- Keep comments focused on intent and non-obvious constraints.

### Static Analysis: Fallow

- Use Fallow for project-wide structural analysis.
- Detect circular dependencies, dead code, duplicated logic, and architecture boundary violations.
- Run Fallow analysis before major refactoring.
- Use `fallow audit` to review newly introduced structural problems.
- Treat automated fixes as proposals requiring review.
- Do not modify code solely to maximize health scores.

### Effect Usage

- NestJS remains responsible for dependency injection and HTTP application lifecycle.
- Use Effect selectively for fallible external operations when typed errors, retry, timeout, or resource management provide clear value.
- Do not introduce a second application-wide dependency injection system without an explicit architectural decision.
- Keep deterministic domain logic as ordinary pure TypeScript functions.
- Convert Effect failures into application errors at explicit integration boundaries.
- Avoid mixing Effect Schema and Zod for the same validation responsibility without a documented reason.
- Do not introduce Effect solely to wrap synchronous functions or simple Promise operations.

## 8. Testing

- Test domain invariants and schedule calculations.
- Test measurement aggregation and achievement evaluation.
- Test date boundaries and time-zone behavior.
- Test Zod validation failures.
- Test Notion mapper behavior with fixtures.
- Mock external services in ordinary unit tests.
- Keep tests requiring real Notion or AI credentials separate.
- Add regression tests when fixing confirmed defects.
- Do not weaken or remove failing tests solely to pass CI.

## 9. AI-Assisted Development

- Read relevant requirements and existing implementation before making changes.
- Identify the scope and acceptance criteria of the task.
- Do not implement unrelated features.
- Do not add speculative abstractions for future requirements.
- Make changes in small, reviewable units.
- Run relevant tests, type checks, and lint checks after changes.
- Clearly report tests that were not executed.
- Do not claim success without verifying the result.
- Ask for approval before destructive operations, deployments, or changes involving credentials.
- Do not modify accepted requirements silently.

## 10. Git and Pull Requests

- Prefer one logical concern per pull request.
- Reference the corresponding GitHub Issue.
- Describe what changed and how it was verified.
- Separate refactoring from behavior changes when practical.
- Do not commit secrets or generated local credentials.
- Require explicit approval before merging or pushing when operating autonomously.

## 11. Definition of Done

A task is complete only when:

- Its acceptance criteria are satisfied.
- Relevant tests pass.
- Type checking passes.
- Lint checks pass.
- No unintended changes remain.
- Documentation is updated when behavior or contracts change.
- Known limitations are explicitly reported.

Do not mark incomplete work as complete.
