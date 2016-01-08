# Tips for E2E testing

On a [previous post](https://github.com/ubiqua/engblog/blob/refactoring_legacy_code/refactoring_legacy_code.md), I explained our team's plan to reduce legacy code by increasing test coverage. On this post I'll go a bit more into detail about how we approached that task and the specific tools we used.

## E2E vs Unit Tests
One of the first issues we discussed was which types of tests to write. There's a famous concept called the [Test Pyramid](http://martinfowler.com/bliki/TestPyramid.html), which argues you should have a lot more low level unit tests than high level end to end tests.

We think the Test Pyramid makes a lot of sense when writing new code, but it wasn't the best fit for our specific situation. Since a lot of our legacy code was poorly designed, we needed enough flexibility to redesign entire modules (or even groups of modules) without continuously breaking our tests.

E2E tests work a lot better for this purpose, since they test the app from a user's perspective, regardless of internal implementation. However, choosing to write more E2E tests means you need to address some issues associated with them:
- Slower to run
- Harder to maintain
- Unreliable (false positives)

## Speed
E2E tests are slow because actions run through a user interface (a browser if it's a web application). However, not **all** of your actions need to run through the UI. For example, if you're writing a test for user checkout, you can setup the context (create a user, create products, etc) using your ORM, and only run the steps related to checkout on the browser.

Also, make sure to choose an appropiate cleanup strategy for your database. Unit tests tend to use the transaction strategy, ie: every test runs in a transaction that is rolled back at the end of the test. Transactions are very fast, but they don't work for E2E tests. 

On E2E tests, the test and the browser run in two different processes, each with separate database connections. That's why E2E tests tend to use the much slower truncation strategy, ie: writing to the db, then removing all data at the end of each test. However, depending on your database, there might be [other options](http://dev.alexishevia.com/2014/01/resetting-your-postgres-database-before.html) to consider.

Finally, make sure to manually handle the system clock. For example, instead of waiting 10 seconds for a callback to run, simulate the passage of time using a tool like [Timecop](https://github.com/travisjeffery/timecop) or [SinonJS](http://sinonjs.org/docs/#clock).

## Maintainability
Always keep in mind that tests are code too, and as such the same principles apply ([KISS](https://people.apache.org/~fhanik/kiss.html), [DRY](http://c2.com/cgi/wiki?DontRepeatYourself), etc)

If you're using a tool like [Cucumber](https://cucumber.io/), these tips might help you organize your tests:

- Keep Cucumber features expressed at a business level.  
Avoid steps like "Visit the product page", "Click on the link that says Foo", "Drag the item to the bottom". Those are all implementation details, which belong inside step definitions. Instead, prefer steps like "Register as a store owner", "Add product foo to the cart", "Leave positive feedback". Features should define *what* the user is doing, not *how* he is doing it.

- Organize step definitions in a consistent folder structure.  
Developers should be able to read a step and immediately know where to look for the step definition. The sorting criteria isn't as important as being consistent.

  We use a very simple rule: look for the first noun in the step; the step definition should be in a file called `[noun]_steps.rb`. Eg: The step definition for "Add product to the cart" would be on `product_steps.rb`.

- DON'T call step definitions from other step definitions.  
Instead, move reusable code into support modules.

- Move UI handling to Page Objects  
Martin Fowler has a [great post](http://martinfowler.com/bliki/PageObject.html) on how to use Page Objects to keep your tests easier to read and maintain.

## Reliability
One of the most common problems I've seen with E2E tests is false-positives and false-negatives. E2E tests run on top of browsers, which are usually running some pretty complex JS apps while constantly talking to an app/db server. All this complexity means there are more chances of external factors influencing your test results.

However, you can't let false-positives/negatives flood your test codebase. Once you become used to brittle tests, your whole test suite becomes pointless.

Some of the root causes I've seen for brittle E2E tests are:
- Time-related features  
If you have code that depends on time (callbacks that run after a timeout, for example), getting them to work correctly on your tests can be difficult. Tools like [Timecop](https://github.com/travisjeffery/timecop) and [SinonJS](http://sinonjs.org/docs/#clock) help a lot when dealing with these features.

- DOM altering features  
Keep in mind your JS code might need to remove or add DOM elements before reaching the desired state. Make sure to use retries and give components the time they need to finish before running assertions.
