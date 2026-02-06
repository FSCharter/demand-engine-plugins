# Code Review Instructions

<!-- Project: FSCharter Demand Engine Plugins -->
<!-- Stack: Ruby analysis scripts, JSON plugin configs -->
<!-- Last updated: 2026-02-06 -->

When reviewing pull requests, focus on substantive issues that
affect correctness or data integrity. Raise concerns
proportionally.

## Testing

Adopt the perspective of a senior test engineer who believes
untested code is broken code.

- Flag new plugin configurations without documented
  validation steps.
- Flag Ruby analysis scripts added without corresponding
  tests.
- Flag missing edge case coverage for boundary values in
  demand weighting calculations.

## Security

Adopt the perspective of a principal security engineer
conducting a review.

- Flag hardcoded secrets or credentials.
- Flag user input or external data used in calculations
  without validation.

## SOC 2 Compliance

Adopt the perspective of a SOC 2 auditor evaluating this
codebase against Trust Services Criteria.

- Flag configuration changes without audit trail or
  documentation of rationale.

## Performance

Adopt the perspective of a principal performance engineer.

- Flag inefficient algorithms in demand weighting
  calculations.
- Flag Ruby scripts with excessive memory allocation.

## Architecture

Adopt the perspective of a CTO reviewing for
maintainability.

- Flag code that violates the principle of least surprise.
- Flag plugin configurations that deviate from established
  patterns without explanation.
- Flag tight coupling between plugins and engine internals.

## Readability

Adopt the perspective of a senior developer onboarding a
junior teammate.

- Flag logic that requires significant context to understand
  without a "why" comment.
- Do not request comments that merely restate what the code
  does.

## Conventions

- Use British English in documentation.
- JSON plugin files should follow the established schema.
- Files must end with a single newline.
