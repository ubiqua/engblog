# Refactoring Legacy Code

## Our Problem
A lot of code achieved "legacy" status, ie:
- code that gets the job done, but
- no one fully understands how it works
- adding new features or trying to refactor it is error prone

## How did it happen? / Why didn't you fix it earlier?
This is a problem that creeps up on software projects. When you're getting started, you can get away with almost anything, including poorly designed code. At this stage, developers tend to focus on adding features (which provide immediate business value) and forget about code quality tasks (testing, refactoring, documenting, etc). Once you realize it's a problem, you're already knee deep into it.

## Proposed Solution
- Add E2E tests for our main features
These tests treat the app as a huge black box, like a user would.
They will allow us to re-design and refactor big chunks of our app without the fear of making the app unusable.

- Add Unit Tests for modules we want to keep
If a module has a good interface, but a bad internal implementation, we can add unit tests for it and then refactor with confidence.

## Approach
Gradually increase coverage.
- New features must include tests (bonus points for using TDD)
- Bug fixes must include a regression test.
- Identify main app features and start adding E2E tests for each one (one per week?).

After a while, you'll have enough confidence on your test suite to start refactoring your code.

## Tools Chosen
Cucumber for E2E tests
RSpec and Jasmine for unit tests
