# Azure DevOps and Project Management

Everyone loves to hate, or at least be irritated by, whatever project management software they are using. I think this is unhelpful at best. Worse, this irritation leads some to feel justified in not understanding how to use their project management tools effectively. There is a project, it needs to be tracked and managed, we're going to use software to do it. Not being proficient with the tools is self-defeating and is actually going to harm the team.

I invariably wind up using Azure DevOps and have opinions about how the team, not just the poor project manager, should us it to manage projects.

## What's the point of all this?

This is an important question that needs an answer everyone can rally around. What are we using the project management tooling to do in concrete terms? We probably already accept this:

- The project team will have bunches of work items in a backlog that will, sooner or later, be assigned to project increments
- The work items will be assigned to people
- The people will mark them complete when they are done

This is a fine starting point but will be totally inadequate. Arguably the easiest part of managing the project is assgining people work to do. What about:

- Making sure we are working on the right things? Is that work item even part of our scope? How do you know?
- Communicating progress to stakeholders? Are we going to finish on time?
- Tracking non-work item things like decisions and risks?
- 50 other things that fall under the topic of managing the project...

I've written [elsewhere](../agile/myth_of_agile.md) here about the myth of agile on my projects, so I won't rehash it. Suffice it to say that by the time I'm involved, a lot of constraints have been agreed to and it's not truly an agile project. That means that we don't get to pretend that scope and due dates are fully negotiable.

Beyond that, there are ADO-specific questions to be answered:

- Are bugs associated with the work item they relate to or do they live independently
- Are we using Test Plans? Do test plans map to the work items they test?

The point is, we're going to be using ADO for more than just a task list. There are going to be lots of activities that leverage ADO because that's where so much information collects. Using this information and the features of the tool effectively can make a big difference in the outcomes.

## Capture your starting point

The first thing that should happen is that all the commitments in the Statement of Work or whatever are reflected in ADO. This stuff is all likely to change dramatically, but it hasn't changed yet, so write the reality down. These include:

- The high level requirements should exist, probably as epics and features for starters
- The timeline should be represented
  - Set up the sprints and put dates on them. Start accounting for major holiday periods, particularly Thanksgiving and the December holidays.
  - Represent milestones, especially if they have contractual relevance for things like when invoicing needs to happen or funding expires.
  - Put start and end dates on the epics and, if possible the features.
  - Avoid creating user stories, unless their are named deliverables (documentation, test plans, etc.) that are tied to dates or phases. Those should be put in to the relevant sprints.
- Lay all this out in a delivery plan

This is actually not that hard, or at least it shouldn't be. Nothing is carved in stone just because we created a work item. We're just building a sketch of the commitments that have been made. It should take someone a day to just splat things down. It's not unusual for this to look terrible, where the commitments and the timeline are impossible. That's actually good, we can start figuring out what to do about that problem in the first or second week, not the second month.

The idea is to get everyone aligned with reality as it's currently understood.

