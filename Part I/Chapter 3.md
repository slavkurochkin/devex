# Chapter 3 — Understand the Constraints

![Chapter 3 — Understand the Constraints](../images/chapter-3-hero.png)

> I used to think constraints were the things getting in the way of good automation. Eventually I realized they were the design brief.

One of the easiest mistakes to make in test automation is to start with the test.

You look at a feature and think:

> "We need an end-to-end test for this."

Then you start designing the framework, finding selectors, creating helpers, building fixtures, and figuring out how to make the test reliable.

But there's a question that should come first:

> **"What constraints does the system have?"**

I learned this at LTK.

---

## The Test Isn't the System

At LTK, I spent a lot of time building test automation around end-to-end user workflows. The goal was straightforward: take the workflows a real user would perform and make sure they kept working as the application evolved.

On paper it sounds simple.

```text
User
  │
  ▼
Open application
  │
  ▼
Perform action
  │
  ▼
Verify result
```

So the natural instinct is to reproduce those actions with automation.

But an application isn't a collection of screens. There's architecture behind those screens — services talking to each other, databases, queues, feature flags, asynchronous operations, external dependencies, environments, business rules.

Every one of those creates a constraint. And the automation strategy has to work **with** them, because it isn't going to work around them.

---

## The Approach I Wanted Isn't Always the Approach That Works

When I started thinking about the automation strategy, it was easy to focus on what the test should do. But the more I understood the application architecture, the more I realized the architecture itself had to shape how we tested it. The most obvious end-to-end approach often wasn't the most reliable or the most maintainable one.

A workflow that looks like a single user action might actually look like this:

```text
Browser
   │
   ▼
Frontend
   │
   ▼
Service A
   │
   ▼
Service B
   │
   ▼
Database
   │
   ▼
External Service
```

A test that exercises the entire chain is genuinely valuable. It's also a test that now depends on every component in that chain behaving correctly.

If Service B is slow, your test is slow. If an external service is unavailable, your test fails. If test data isn't there, your test fails. If asynchronous processing takes longer than expected, your test is flaky. If the environment is shared with other teams, your test might fail because somebody else changed the state underneath it.

The test reports a failure. But the failure doesn't necessarily mean the user's workflow is broken.

That gap — between what the test says and what's actually true — is the difference between **testing a workflow** and **understanding the system you're testing**.

---

## Test Automation Has Trade-offs

There's no perfect automation strategy. You can optimize for:

- Realistic user behavior
- Execution speed
- Reliability
- Isolation
- Maintainability
- Debuggability
- Coverage
- Cost

But optimizing one usually costs you another.

```text
More realistic
      │
      ▼
More dependencies
      │
      ▼
More failure points
      │
      ▼
Less reliable
```

And in the other direction:

```text
More isolated
      │
      ▼
More mocks / stubs
      │
      ▼
Faster + more reliable
      │
      ▼
Less representative of production
```

Neither is automatically right. Which one you want depends entirely on a question most test plans never write down:

> **What are you trying to learn from this test?**

---

## Start With the Question

This became one of the most important changes in how I thought about automation.

Instead of asking *"how do I automate this workflow?"* I started asking:

> **"What do I need to know?"**

Those are very different questions, and they produce very different test suites.

If I need to know whether an entire user journey works across multiple services, an end-to-end test makes sense. If I need to know whether a service handles a particular input correctly, I probably don't need a browser. If I need to know whether two services agree on a contract, contract testing is a better fit. If I need to know how a component behaves under varying conditions, a lower-level test will give me faster and clearer feedback.

The workflow determines the question. The question determines the test.

Not the other way around.

---

## Don't Fight the Architecture

The biggest lesson I took from this is that test automation should respect the architecture of the application rather than paper over it.

If the application is asynchronous, the tests need to understand asynchronous behavior. If it's distributed, the tests need to account for distributed dependencies. If it leans on external services, you need to decide deliberately which dependencies are real and which are controlled. If test data is hard to create, you need to solve test-data management rather than pretending it isn't a problem. If environments aren't isolated, that's a constraint to design around, not one to hope your way past.

Trying to hide these constraints inside a test framework doesn't make them disappear. It relocates them — usually into framework complexity that one person understands and everyone else is afraid of.

---

## The Automation Strategy Should Follow the System

This changed how I approached framework design. Instead of one strategy applied everywhere, I started thinking in layers, each answering a different kind of question.

```text
                    User confidence
                          ▲
                          │
                    ┌───────────┐
                    │    E2E    │
                ┌───┴───────────┴───┐
                │    Integration    │
            ┌───┴───────────────────┴───┐
            │         Contract          │
        ┌───┴───────────────────────────┴───┐
        │               Unit                │
        └───────────────────────────────────┘
                          │
                          ▼
                     Fast feedback
```

The point isn't that every organization needs exactly this shape. Plenty of good teams run something that looks nothing like it.

The point is that **different tests answer different questions**. A good automation strategy isn't "more end-to-end tests." It's having the right test at the layer where it can actually give you a trustworthy answer.

---

## Constraints Are Not Obstacles

This was the real mindset shift for me.

Earlier in my career, I treated constraints as things standing between me and the automation I wanted to build. Eventually I started treating them as input.

> **Constraints aren't obstacles. They're information.**

The architecture tells you something. The deployment model tells you something. The environment, the data model, the team's development practices — all of it tells you something about what kind of feedback is achievable and what kind isn't.

Ignore that, and you'll build a framework that spends most of its energy fighting the system it's meant to verify. When that happens, engineers stop seeing automation as a tool and start seeing it as a tax.

That's a developer experience failure, even if the coverage numbers look fine.

---

## The Developer Experience Test

There's a simple way to evaluate an automation strategy:

> **Does this test make it easier or harder for an engineer to get confident about their change?**

A test that takes 45 minutes, fails randomly, and requires someone to investigate infrastructure every time it goes red is technically providing coverage. But once developers stop trusting it, its value approaches zero — and it keeps costing time regardless. An untrusted test is worse than no test, because you're still paying for it.

A small, focused test that runs quickly and fails for one clear reason can be worth more than a dozen comprehensive ones.

The goal was never maximum automation. The goal is **useful feedback**.

---

## AI Changes the Equation

Writing tests used to be expensive, and that expense did something quietly useful: it forced you to choose. When a test costs a day to write and a week a year to maintain, you think carefully about which ones you actually want.

That cost is mostly gone. AI will produce a plausible end-to-end suite for a workflow in minutes.

Which means the constraint that used to impose discipline has been removed, and nothing has replaced it. If you don't understand the system, you can now generate two hundred tests that all depend on the same shared environment, the same async timing assumption, and the same external service — and discover it two months later when the suite goes permanently red and everyone learns to ignore it. Volume was never the bottleneck. Deciding what's worth knowing was, and that part hasn't gotten any cheaper.

But there's a genuinely useful side, and it lands squarely on this chapter's argument. The constraint-discovery work I've been describing — which services participate in a workflow, where the synchronous boundaries end, where state lives, what a service's contract actually is versus what the docs claim — is largely reading. It used to take weeks of tracing code and interviewing people who'd been there longer than you. A model that can read the whole repository does a competent first pass in an afternoon.

That's the part worth leaning on. Not "write my tests," but *"walk this workflow through the codebase and tell me every service, queue, and external call it touches."* The output needs verifying, but it turns a multi-week archaeology project into something you can do before writing the first test — which is exactly when this chapter says you should be doing it.

There's a second corpus worth reading, and most teams are sitting on it without touching it: a year of bugs and incidents in Jira or Linear.

This chapter argues you should choose a test layer based on what you need to know. Your defect history is a record of what you needed to know and found out too late. With an MCP server connected to the tracker, a model can pull every escaped defect from the past year and work through them one at a time, asking the only question that matters for strategy: **which layer of test could have caught this before a customer did?**

```text
A year of bugs and incidents
            │
            ▼
For each escaped defect:
which layer could have caught it?
            │
            ▼
Contract 38% · Integration 24%
E2E 9% · Not preventable by tests 29%
            │
            ▼
Where the next test belongs
```

Those numbers are made up, but the shape of the answer is the point. If most of what reaches production is two services disagreeing about a payload, the fix isn't a broader end-to-end suite — it's contract tests, and now you can say so with your own data instead of a blog post. If a quarter of your incidents are configuration and environment drift, no test layer is going to help and the effort belongs somewhere else entirely. This analysis was always possible. It was also a week of reading tickets, which is why nobody did it.

Two cautions. Tracker data is a record of what was found and written down, not what happened — an area nobody tests thoroughly produces few bug reports, and that absence reads as health when it means you aren't looking. And the categorization is only as good as your ticket hygiene, which is usually worse than anyone wants to admit. Treat the output as a hypothesis to check against what you know, the same as any other generated answer.

The same applies to failure triage. *"Is this a product defect or an environment problem?"* is a question teams answer by hand, badly, every day. It's pattern matching across failure history, and it's a reasonable thing to hand to a machine.

> **AI makes tests cheap to write. It doesn't make them worth running.**

---

## Questions to Ask Before You Automate

Before building an automation strategy for a new system, I'd work through these.

**About the application**

- What does the architecture look like?
- Which services participate in this workflow?
- Which operations are synchronous, and which are asynchronous?
- What external dependencies exist?
- Where does state live?

**About the environment**

- How isolated is the environment?
- How is test data created, and can it be reset?
- Can tests run in parallel?
- What happens when another team changes the environment mid-run?

**About the tests**

- What question is this test answering?
- Does it need to be end-to-end to answer it?
- Could a lower-level test give faster feedback on the same question?
- Which dependencies actually need to be real?
- How will we tell a product defect from an environment problem?
- What have our escaped defects actually been for the past year, and which layer could have caught them?

**About the team**

- Who maintains these tests?
- Can a developer understand the failure without asking someone?
- How long is the feedback loop?
- Will people trust the results?

That last one decides whether any of the others matter. A suite nobody trusts doesn't get fixed when it breaks — it gets ignored, then muted, then deleted.

These questions aren't bureaucracy. They're the design work.

---

## Further Reading

- **"Just Say No to More End-to-End Tests"** (Google Testing Blog) — the case that end-to-end suites tend to grow past the point where their signal is worth their cost.
- **"The Practical Test Pyramid"** (Ham Vocke, martinfowler.com) — a careful treatment of what each layer is actually for, including where the pyramid metaphor breaks down.
- **"Testing Strategies in a Microservice Architecture"** (Toby Clemson, martinfowler.com) — on choosing test types when a workflow crosses service boundaries.
- **Software Engineering at Google** (Winters, Manshreck, Wright) — the testing chapters, particularly on test size and the economics of flakiness at scale.
- **Consumer-driven contract testing** (the Pact documentation is a good entry point) — the concrete answer to "do these two services still agree?" without standing up both.

Most of these arrive at the same place from different directions: the layer you test at should be chosen by what you need to learn, not by how realistic it feels.

---

## The Takeaway

Test automation doesn't exist separately from the application. The architecture, infrastructure, data, dependencies, and development process all shape what good automation looks like — and they'll shape it whether or not you take them into account.

The mistake is forcing a favorite testing strategy onto every system you meet.

> **Understand the system first.** Then decide how to test it.

The best automation strategy isn't the one with the most sophisticated framework. It's the one that understands the constraints of the system and gives engineers feedback they'll actually believe.

> **Don't automate the workflow you see. Understand the system underneath it first.**
