# Thoughts on DevOps

I'm working with some combination of Git and Azure DevOps the vast majority of the time. If you ask someone a question about best practices in how to use this stuff, you'll get at least 3 opinions. These are mine.

## Source control and branching

This is simultaneously a really important topic to get right and one that people, IMHO, spend entirely too much time thinking about.

It's critical that everything, and I mean everything, goes into source control.

- Code
- Configuration files
- Developer documentation
- DevOps pipeline definitions
- Probably 50 other things

We want robust change control governing all of this and keeping it all together simply makes sense. I think it's confusing to have source code in one place, configuration information in another, and documentation in a third.

The topic that is frequently overthought is branching. Don't adopt something like Git Flow, GitHub Flow, or some other branching methodology unless those added rules make sense. Keep it simple; we shouldn't be attaching unnecessary importance to the how, when, and why of a branch being created.

### Do not foget about secret scanning

It is the easiest thing in the world to commit a local config file that has a secret in it. Solve this problem on the first day of the project.

## On to DevOps

We're talking about CI/CD specifically. There are a lot of ways to set this stuff up well, if the team likes how it works then it's good. My interest here is what are the characteristics that I like and think are important. I'm a developer, so I see everything through a developer lens, but I try to incorporate thinking about IaC here too.

The most important point to take away from this section: CI/CD is a critical delivery tool that is actually custom software. It needs to be thought out, carefully engineered, and thoroughly tested to be bomb-proof in terms of reliability. If it's just thrown together, it's going to be a permanent drag on productivity.

### Manual configuration of anything is bad

This should simply never happen unless it is impossible to automate, which is rare. Every single time a manual configuration step is required, it creates time consuming problems 100% of the time. Inevitiably someone will forget to do something or do it incorrectly and the team will lose a lot of time trying to figure out why things are broken. It doesn't matter how long it takes to automate everything, it's worth doing.

The team should be able to run the pipelines and get something that works every time without needing to tweak something.

### All configuration settings belong in source control

It's fine for the CI/CD system to push these out to a key vault or a database or whatever, but the source of truth for configuration needs to be under source control so changes can be tracked.

#### Azure DevOps Library variable groups are bad

These are a very convenient place to store configuration for pipelines but I believe that they are bad and think they should never be used. It's possible for someone to come in and change values without any sort of tracking on when something changed, who changed it and why.

### All secrets belong in a key vault

It is trivial, or at least well-known how, to automate the generation and storage of certificates and secrets in a keyvault as a part of a CI/CD process. The keyvault keeps track of change history and old secret values can be recovered if necessary. Those secrets can then be used however you need to use a secret; passwords, function keys, whatever. This automation can then be used to handle things like secret rotation. Run your pipelines and ta-da, all your secrets can be rotated without any drama. There isn't really a good reason not to invest in this.

### Overall Structure

I prefer a model where changes to the target branch automatically trigger a build and deployment to a development environment. That release can be subsequently released further to a test environment, an integration test environment, etc, etc, until it winds up in production. This approach makes it much easier to understand what is deployed to which environment. It should not be possible to deploy directly to something other the development environment from a code branch. The DevOps system shouldn't enable doing something crazy.

If each change yields a build and release to DEV, it's easy to see when a given feature or fix was added to the target branch and which environment it has been deployed to. This in turn makes it easy for someone to understand if something is deployed and ready for validation and testing.

### Characteristics of a good DevOps system

#### Continuous integration

- Every change committed to the target branch gets built automatically
- The build automation should be carefully engineered and wrung out to be as fast as humanly possible
- A core set of unit tests should be run after every build. Again, these tests should be engineered and need to run really fast.
- Each build should contain actionable feedback that the team can use. The test results and any output from static analysis should be valuable. It's pretty common to use something like SonarQube but not tune the outputs so everyone just ignores all the warnings. Don't do this. Configure the tooling so it emits feedback that the team will use. Otherwise it's just a waste of time.

That's actually all there is to it. It's not that long a list, but the engineering to make it fast is real work and shouldn't be underestimated or undervalued.

#### Continuous delivery

- Deployment to the development environment should be automatic without an approval gate.
- Deploying to test shouldn't happen automatically, but the bar should be pretty low. Lots of people should be allowed to approve a release to the test environment.
- If an expansive and time-consuming automated test suite exists, it should run in the test environment post-deployment. There is going to be a lot of churn in the development environment and it's going to be kind of normal for something to not fully work there. It makes more sense to incur the time hit in test, as it will be less frequent.
- Above this YMMV. I don't have strong opinions about how strict this should be, other than production being quite restricted. Just depends on how the team works.

Again, not a long list. The effort is in making it fast and reliable. This is, make no mistake, work and will require ongoing maintenance througout the project. It should have it's own set of features and lifecycle in the backlog. The effort spent on it will save countless hours down the road, especially at the end when there is a lot of pressure to do things quickly and reliably. If you have built good tooling, it will pay you back.

##### Infrastructure pipelines

It's natural to divide the IaC pipelines that deploy the infrastructure from the pipelines that deploy configuration and code. The infrastructure pipelines will be run much less frequently and are time consuming in ways that cannot be helped. They are no less important. Infrastructure issues are a frequent cause of problems and having good infrastructure pipelines that can pave over an environment with a known infrastructure configuration is an incredibly valuable tool. The dev environment is probably going to be irreparably messed up at some point. Probably several times. Don't waste time fixing it; just pave it over and keep moving.

This also opens the door to ephemeral environments too. Having the ability to spin up a big, expensive, load testing environment, run tests for a few hours or days, and then wipe it out again is just an incredible capability to have.

##### What belongs in infrastructure pipelines vs application pipelines

There are a couple of places where it can be a little tricky to know which pipeline to put something in. The most common of these are app settings for things like websites. Are those settings part of the infrastructure, because that's where they appear in the portal, or are they a part of the application?

For me, these are application concerns and belong in the application pipelines. The reason the value for a setting would change or a new setting would be added is because the application needs it. The application pipelines should manage this. Something like networking configuration is different. The software may need it to work, but it is just part of the infrastructure air that the application breathes.

##### Special purpose pipelines

Special purpose pipelines that do things like allow a development team to deploy an arbitrary branch to the development environment are good to have. I do not like adding switches to the main pipelines to allow unexpected outcomes. Again, this comes back to configuration management. If I look at the main CI/CD pipelines, I should know what it is telling me without having to open the logs to see which switches were set to what values.
