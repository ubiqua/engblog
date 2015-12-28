# Refactoring Legacy Code
Even though there's some debate on the programming community about [what is considered legacy code](http://programmers.stackexchange.com/questions/94007/when-is-code-legacy), at Ubiqua we define it as:

Code that gets the job done, but
- no one on the team fully understands how it works
- adding new features or trying to refactor it is error prone

In other words, if you're trying to fix a bug or add a new feature, but you're afraid you'll break something, then you're dealing with legacy code.

As our project grew in complexity, we noticed more and more code was achieving "legacy" status, so we decided we had to take some action before it got any worse. We knew we couldn't stop adding features or fixing bugs, and we didn't have enough resources to rewrite everything from scratch. We had to come up with a plan that was reasonable within our constraints, something we wouldn't be forced to abandon a few weeks later.

## Plan of Action
Our solution was to gradually increase our test coverage, until we reached a point where we could safely refactor the old code. So we came up with some simple rules for the team:
1. All new features must include tests (bonus points for using TDD).
2. Bug fixes must include a corresponding regression test.
3. One test must be added to the existing codebase every week.

As we approach the end of this quarter, we're very close to achieving our goal, and very soon we'll be able to start refactoring our legacy code. It's been a slow process, but it's had minimal impact on our team's performance, which was really important for getting team buy in.

On our next posts I'll go into more detail on which type of tests we wrote, and some tips on how to avoid writing tests from becoming a burden.
