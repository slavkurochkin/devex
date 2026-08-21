# Chapter 8 — Completeness Isn't Clarity

![Chapter 8 — Completeness Isn't Clarity](../images/chapter-8-hero.png)

> I built a dashboard with everything on it. Then I watched a room full of intelligent people read all of it and understand none of it.

At LTK I built a dashboard showing the state of quality across our application. Everything we collected went on it. If we measured something, it was there.

I was proud of it, which should have been the first warning.

---

## The Meeting

I presented it to a leadership group, and it didn't land.

Not in the way where people disagree with you. In the quieter, worse way: polite attention, a couple of questions about individual numbers, and no discussion of what any of it meant. Nobody argued because nobody had gotten far enough to have a position.

They could read everything and understand nothing.

That's not a comment about them. Every number on that screen was legible on its own, and each one meant something to me because I'd built the thing that produced it. What I hadn't given anyone was a way in — a place to start, an order to read in, or any signal about which of the forty things mattered more than the others.

---

## What I Thought the Problem Was

My first instinct was that I'd explained it badly. I could walk them through it. I could add a legend, annotate the panels, write a short guide to reading it.

Every one of those ideas is the same idea: keep the dashboard and add explanation on top. Which is what you do when you believe the artifact is fine and the audience is the gap.

The dashboard wasn't fine. A thing that requires a guided tour is not finished, it's a draft with a person attached — and that person is me, in every meeting, forever. Chapter 6 is about what that costs.

---

## Four Numbers

What actually worked was cutting it down to four:

- **Total tests**
- **Failed tests**
- **Flaky tests**
- **Execution time**

That's the leadership view. Four numbers, no drilling required to get the gist.

They're worth walking through, because the set is doing more work than it looks like:

**Total tests** answers *is this growing?* On its own it's a vanity metric, and I'd never show it alone. Next to the other three it's the denominator that stops them being misleading — forty failures out of four hundred is a different situation from forty out of forty thousand.

**Failed tests** is the only one that looks like quality, and it's the one people reach for first.

**Flaky tests** is the most important of the four and the least obvious. It measures whether the other numbers can be believed. A suite with a lot of flakiness doesn't just waste time; it produces failures that people learn to ignore, and once that habit forms the failing tests stop meaning anything at all. Putting it on the leadership view was a deliberate choice: it makes trust a visible, tracked thing rather than a private worry.

**Execution time** is the feedback loop, expressed as a number somebody owns. It's the difference between finding out in fifteen minutes and finding out tomorrow, and it's the metric most likely to quietly degrade because no single change makes it much worse.

---

## The Detail Didn't Go Away

This is the part I'd emphasise most, because "simplify the dashboard" is usually heard as "delete things."

Nothing was deleted. The four numbers sat on top, and each linked through to the detailed views that had made up the original dashboard. When somebody asked *why are failures up this week*, we went from the summary into the detail during the meeting and answered it.

That structure is what made the simplification survivable. If cutting to four numbers had meant losing the ability to answer the follow-up question, it would have been a worse dashboard that happened to be prettier. What made it better was that the depth was still there, one click away, and nobody had to look at it to get the headline.

```text
Four numbers            ← what you see
      │
      ├── failures by suite
      ├── flake history by test
      ├── execution time by stage
      └── runs by branch       ← where the question gets answered
```

The general version of this: **a summary is not a smaller version of the detail. It's a different artifact with a different job.** The detail's job is to answer a specific question once you have one. The summary's job is to tell you whether you should have a question at all.

---

## What Four Numbers Can't Tell You

I want to be honest about what that view actually measured, because it's easy to miss and I missed it for a while.

All four are metrics about the *test suite*. None of them are metrics about the product. You can have a green, fast, non-flaky suite and a bad application — the suite only knows about the things somebody wrote a test for.

So calling it a quality dashboard was generous. It was a dashboard about our ability to detect problems, which is related to quality and is not the same thing. The genuinely product-facing measures — escaped defects, incidents, what customers actually hit — weren't on it.

If I built it again, at least one of the four would be about things we missed rather than things we caught. Not because the four were wrong, but because a leadership view that only reports on the health of your detection system quietly teaches everyone that the detection system is the thing that matters.

---

## When You Shouldn't Simplify

The four-number view was right for that room and wrong for most others.

**Engineers need the full surface.** Somebody debugging a failure at four in the afternoon wants every panel, unaggregated, with filters. Giving them the executive summary is the same mistake in the other direction — and it's the more common one once a team gets excited about simplification.

**Summaries hide variance.** Four numbers rolled up across an org can look completely healthy while one team is drowning. Whoever owns the summary needs to be looking at the distribution behind it, or the aggregate becomes a way of not knowing.

**A number on a leadership dashboard becomes a target.** The moment "flaky tests" is a tracked figure, deleting flaky tests improves it just as well as fixing them. That's not an argument against measuring it — it's an argument for the person presenting to know what the cheap way to move the number is, and to watch for it.

---

## AI Changes the Equation

The fix in this chapter was to build a second artifact for a second audience. That used to be the only option, and it's why most organizations have one dashboard everybody complains about.

That constraint is loosening. A summary layer can now be generated rather than designed — the same underlying data described at whatever depth the reader asks for, in prose, on demand. *Why are failures up this week?* becomes a question you ask the data rather than a click path someone anticipated when they built the page.

Which mostly relocates the judgment rather than removing it. Somebody still has to decide what a leadership view should contain, because the generated version will happily summarise the wrong four things with total fluency. The dashboard I built first would have produced a beautiful, confident summary of forty numbers that didn't matter.

There's a subtler risk too. A generated summary reads as authoritative in a way a chart doesn't. A chart shows you its own shape and lets you notice something odd about it. A paragraph tells you what to think, and if the underlying data is skewed by one team, the paragraph will smooth right over that and sound completely reasonable.

> **Generating a summary is easy now. Knowing which four numbers belong in it is the part that was always hard.**

---

## Questions to Ask Before You Build a Dashboard

- Who is going to look at this, and what decision are they making?
- What are the three or four things that person needs, and what's the honest reason everything else is on the screen?
- Can someone read it without me in the room?
- When they have a follow-up question, where do they go?
- Does this measure the product, or does it measure our ability to detect problems with the product?
- What's the cheapest way to make each number look better, and would that be an improvement?
- If one team were in serious trouble, would this view show it, or would the average absorb it?

The first one decides everything else. A dashboard without a named reader and a named decision is a collection of things you happen to be able to measure.

---

## Further Reading

- **Information Dashboard Design** (Stephen Few) — the book on this, and specifically on why the instinct to add another panel is almost always wrong.
- **The Visual Display of Quantitative Information** (Edward Tufte) — for the data-ink idea, which is really an argument about what you remove.
- **"Progressive Disclosure"** (Nielsen Norman Group) — the interaction pattern behind summary-plus-drill-down, and the research on why it works.
- **The Pyramid Principle** (Barbara Minto) — answer first, structure the support underneath. The same shape as this dashboard, applied to how you write and present.
- **Measuring and Managing Performance in Organizations** (Robert Austin) — on what happens to a metric once people know it's being watched. Read it before you put a number in front of leadership.

---

## The Takeaway

I built something complete and mistook it for something clear. Those aren't the same property, and the first one actively works against the second — every additional thing on a screen makes the important things harder to find.

The fix wasn't better explanation. It was accepting that a summary is a separate artifact with its own job, building that, and keeping the detail one click behind it.

> **Everything on one screen isn't a dashboard. It's a data dump with a legend.**
