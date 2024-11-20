# Thoughts on DevOps

I'm working with some combination of Git and Azure DevOps the vast majority of the time. If you ask someone a question about best practices in how to use this stuff, you'll get at least 3 opinions. These are mine.

## Source control generally and Git specifically

This is simultaneously a really important topic to get right and one that people, IMHO, spend entirely too much time thinking about. 

It's critical that everything, and I mean everything, goes into source control.

- Code
- Configuration files
- Developer documentation
- DevOps pipelines
- Probably 50 other things

We want robust change control governing all of this and keeping it all together simply makes sense.

Where things get a little too hectic is when the conversation turns to branching. The thing is, we're just talking about keeping track of text files here. It's a lot of text files and they are really important, but they are just text files. Don't go sprinting to grab something like Git Flow, or GitHub Flow, or whatever off the shelf unless the rules those flows layer on make sense. Otherwise you are just blindly importing ceremony where you might not really need it. Keep it simple and remember that Git is your friend here. Branching is cheap and easy. We shouldn't be attaching too much importance to the how, when, and why of a branch being created.

On the topic of Git, being really good at using Git is just not negotiable anymore. Is Git kind of a pain in the neck to understand? Can rebasing a branch feel like you are juggling chainsaws? Yes and yes. Yes to about 50 other complaints too. Doesn't matter. This means more than knowing a bunch of commands, it means understanding what those commands are actually doing; how does it really work under the covers. 

It has to be on the command line too. The visual tooling are all crutches that hide half the features and cripple you. Everyone puts up with Git's many idiosyncracies because it lets you solve about a million tricky problems.

### There does need to be a target branch

Whatever the team decides to do, there does need to be some branch somewhere that serves as the integration point for changes. Once you start talking about CI/CD, you are going to start talking about a lot of automation. The integration branch, be it main, a feature branch, or something else, needs to exist for your CI/CD automation to watch for changes and trigger builds and deployments.

### Pull requests are not agile

We usually use the PR workflow for integrating code into the target branch, but I do like to remind people that they are a huge piece of ceremony that exists to inhibit change. The whole concept originated in OSS to solve the problem of "I don't implicitly trust strangers to write good code for my project, but they might have a good idea and maybe it'll be great! I need slow this down so I get a chance to look it over and see what I think.".

It's worth asking if PRs are actually necessary for what you are doing. Maybe it is, maybe it isn't. Regardless, it's not a particularly agile way of doing things.

### Do not foget about secret scanning

I have lost more hours than I would like to think about to not having a solution in place that prevents secrets from being committed to the repository. Solve this problem on the first day of the project.

## On to DevOps

We're talking about CI/CD specifically here. Being a developer with a bad DevOps pipeline setup is a special kind of agony. There is so much to do and if the tool that you absolutly rely on is some combination of slow, confusing, and unreliable it will slowly drive you insane.

There are a lot of ways to set this stuff up well, if the team likes how it works then it's good; otherwise it's not good enough. My interest here is what are the characteristics that I like and think are important. I'm a developer, so I see everything through a developer lens, but I try to incorporate thinking about IaC here too.

The most important point to take away from this section: CI/CD is a critical delivery tool that is actually custom software. It needs to be thought out, carefully engineered, and thoroughly tested to be bomb-proof in terms of reliability. If it's just thrown together, it's going to be a permanent drag on productivity.

### Requiring manual configuration of anything

I'm starting with this because it is the source of more pain and suffering than perhaps anything else associated with CI/CD. This should simply never happen unless it is impossible to automate, which is incredibly rare. Every single time a manual configuration step is required, it creates time consuming problems 100% of the time that recur over and over and over when someone forgets to do something or does it wrong. It doesn't matter how long it takes to automate whatever it is, it is worth the time, every single time.

I should be able to run the pipelines and get something that works every time without needing to tweak something. If I can't do that, then we have more work to do.

### Azure DevOps Library variable groups are bad

Hot take: these are a very convenient place to store configuration for pipelines but I believe that they are bad and think they should never be used. It's possible for someone to come in and change the values without any sort of tracking on when something changed, who changed it and why. This feature makes it easy to lose control of the configuration management of your environments.

#### All configuration settings belong in source control

It's fine for the CI/CD system to push these out to a key vault or a database or whatever, but the source of truth for configuration needs to be under source control so changes can be tracked.

#### All secrets belong in a key vault

It is trivial, or at least well-known how, to automate the generation and storage of certificates and secrets in a keyvault as a part of a CI/CD process. The keyvault keeps track of change history and old secret values can be recovered if necessary. Those secrets can then be used however you need to use a secret; passwords, function keys, whatever. This automation can then be used to handle things like secret rotation. Run your pipelines and ta-da, all your secrets can be rotated without any drama. There isn't really a good reason not to invest in this.

### Overall Structure

I'm a fan of the structure where changes to the target branch automatically trigger a build and deployment to a development environment. That release can be subsequently released further to a test environment, an integration test environment, etc, etc, until it winds up in production. Each subsquent change includes every change that came before it. I strongly prefer this for reasons having to do with configuration management. I've been on too many projects where understanding what version of the code is deployed to which environments is hard to rationalize.

This sounds like a minor thing but it is actually a huge time sink. If each change yields a build and release to DEV, it's easy to see when a given feature was added to the target branch and which environment it has been deployed to. If this is opaque, you will spend hours in meetings discussing what is ready for testing and where. Use the tools and save yourself the pain.

To be clear, I am advocating for a world where anything going further than the development environment goes through each subsequent environment one at a time and in order. It should not be possible to deploy directly to production from a code branch. If a team is in that situation, something has gone very wrong. The DevOps system shouldn't enable doing something crazy.

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

It's natural to divide the IaC pipelines that deploy the infrastructure from the pipelines that deploy configuration and code. The infrastructure pipelines will be run much less frequently and are usually really time consuming in ways that cannot be helped. However they are no less important. Infrastructure issues are a frequent cause of problems and having good infrastructure pipelines having the ability to pave over an environment with a known infrastructure configuration is an incredibly valuable tool. The dev environment is probably going to be irreparably messed up at some point. Probably several times. Don't waste time fixing it. Nuke it and redeploy. Problem fixed. 

This also opens the door to ephemeral environments too. Having the ability to spin up a big, expensive, load testing environment, run tests for a few hours or days, and then wipe it out again is just an incredible capability to have.

##### What belongs in infrastructure pipelines vs application pipelines

There are a couple of places where it can be a little tricky to know which pipeline to put something in. The most common of these are app settings for things like websites. Are those settings part of the infrastructure, because that's where they appear in the portal, or are they software?

For me, these are software. The reason the value for a setting would change or a new setting would be added is because the software needs it. The software pipelines should be handlinge setting them up. Something like networking configuration is different. The software may need it to work, but it is just part of the infrastructure air that the application breathes.

##### Special purpose pipelines

Having special purpose pipelines that do things like allow a development team to deploy an arbitrary branch to the development environment are usually good to have. I do not like adding switches to the main pipelines to allow unexpected outcomes. Again, configuration management. If I look at the main CI/CD pipeline, I should know what it is telling me without having to open the logs to see which switches were set to what values.