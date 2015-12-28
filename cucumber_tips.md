E2E tests are a great tool for increasing the code quality of your project.
- Prevent critical bugs from creeping in
- Gives your team the confidence to refactor smelly code

However, they also have drawbacks you need to address:
- Slow to run
- Hard to maintain
- Unreliable (false positives)

On this post we'll see tips for fixing (or at least minimizing) these issues when using Cucumber.

Speed
-----
- Execute your `Given` clauses at the data level, not through the user interface.
- Postgres only: use db templates instead of slow :truncate strategy (use Ave's features/support/env.rb for guidance)
- Manually handle system clock (instead of waiting 10 seconds for a callback to run, simulate the pass of time on your tests)
- Don't test all edge cases with E2E tests.

Maintainability
---------------
- Keep Cucumber features expressed at a business level.
- Organize step definitions in a consistent folder structure.
- DON'T call step definitions from other step definitions. Instead, keep step definitions small and DRY. Move logic into reusable support modules.
- Move UI handling to PageElements
