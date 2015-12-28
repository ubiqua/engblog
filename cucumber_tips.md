On a [previous post](https://github.com/ubiqua/engblog/blob/refactoring_legacy_code/refactoring_legacy_code.md), I explained our team's plan to reduce legacy code by increasing our test coverage. On this post I'll go a bit more into detail into how we approached that task and the specific tools we used.

## E2E vs Unit Tests
One of the first issues we discussed was which types of tests to write. There's a famous concept called the [Test Pyramid](http://martinfowler.com/bliki/TestPyramid.html), which argues that you should have a lot more low level unit tests than high level end to end tests.

While we agree that the Test Pyramid makes a lot of sense when writing new code, we felt it wasn't the best fit for our specific situation. Since a lot of our legacy code was poorly designed, we needed enough flexibility to redesign entire modules (or even groups of modules) without continuously breaking our tests.

E2E tests work a lot better for this purpose, since they test the app from a user's perspective, regardless of the internal implementation. However, choosing to write more E2E tests meant we needed to address some issues associated with them:
- Slower to run
- Harder to maintain
- Unreliable (false positives)

## Speed
E2E tests are slower to run because actions run through a user interface (a browser if it's a web application). However, not **all** of your actions need to run through the UI. For example, if you're writing a test for user registration, then your test should definitely have a browser visit the registration page, input all data, and submit the form. If instead you're writing a test for user checkout, you can run all user registration actions through your ORM, and only run the checkout actions on the browser.

Also, make sure to choose an appropiate cleanup strategy for your database. Unit tests tend to use the transaction strategy, ie: every test runs in a transaction that is rolled back at the end of the test. Transactions are very fast, but they don't work for E2E tests. On E2E tests, the test and the browser run in two different processes, each with separate database connections. That's why E2E tests tend to use the much slower truncation strategy, ie: writing to the db, then removing all data at the end of each test. However, depending on your database, there might be [other options](http://dev.alexishevia.com/2014/01/resetting-your-postgres-database-before.html) to consider.

Another tip to improve your speed is to manually handle the system clock. For example, instead of waiting 10 seconds for a callback to run, simulate the passage of time using a tool like [Timecop](https://github.com/travisjeffery/timecop) or [SinonJS](http://sinonjs.org/docs/#clock).

Finally, don't test all edge cases with E2E tests. As long as your main paths are covered through high level tests, then you're free to use unit tests for testing edge cases.

## Maintainability
- Keep Cucumber features expressed at a business level.
- Organize step definitions in a consistent folder structure.
- DON'T call step definitions from other step definitions. Instead, keep step definitions small and DRY. Move logic into reusable support modules.
- Move UI handling to PageElements

## Reliability
- Keep in mind UI might need to remove or add DOM elements. Make sure to use retries and give components the time they need to finish before running assertions.
