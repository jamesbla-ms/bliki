# What does good code look like

It's hard to imagine a more subjective topic, but the question comes up a lot when trying to talk about quality. Good codes should do a lot more than do something without blowing up. This is what I think, trying to keep things language-agnostic.

## The basics

**Have a single responsibility**: A function or method should only do one thing. If it is doing more than one thing, it should be multiple methods composed together.

**Utilize dependency inversion**: If a function or a class needs something in order to work, it should be injected in some way the vast majority of the time.

**Perform input validation**: Do the inputs match expectations? This should be checked before doing anything.

**Handle exceptions**: If something exceptional occurs, it should be logged and handled if possible. If it can't be handled, it should be allowed to propogate. Never swallow an exception unless that is actually the correct thing to do under the circumstances.

**Be DRY**: Don't repeat yourself.

**Be Resilient**: Anticipate reasons why a line of code might fail and be prepared to do something about it. In the cloud, it's normal for transient failures to occur. By definition, these aren't exceptions because we should be expecting them to happen and be ready to do something about them.

**Emit Telemetry**: Logging exceptions is the minimum. Trace statements should be included to aid in troubleshooting in development and test enviroinments. Metrics should be collected. Performance should be evaluated. This should be built in early in the code's lifecycle as opposed to trying to retrofit it later.

## Unit test coverage

If you've done the first four things in the list above, then you are well situated to write tests to cover your code. Now I personally like doing test-first development if I can. I find that the code I wind up writing is clearer, more concise, and less coupled. It doesn't always work out that way, and that's fine, as long as you don't forget that you have to have some mechanism to prove that everything works. I read somewhere, and I'll come back and cite it when I find it again, that a brownfield codebase is a a codebase that doesn't have tests. If you don't have tests, you can't prove your code works the way it is supposed to. You can't prove that the little change you made didn't break something.

If there isn't an automated way to prove that the code works the way it's suppposed to and prevents a future maintainer from accidentally breaking it, it isn't good code.
