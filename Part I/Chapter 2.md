# Chapter 2 — Silent Until It Matters

> I built a tool nobody used. For three months I thought that meant it had failed. It turned out I was measuring the wrong thing—including my own work.

I built a frontend performance dashboard almost as a side experiment.

Nobody asked for it. Nobody depended on it. Every day, GitHub Actions launched Puppeteer, ran Lighthouse against our application, collected Core Web Vitals, and pushed the results into ELK. Every morning another report appeared.

Nobody opened it.

Three months later, it found a regression before anyone else did.

## What Lighthouse Couldn't Tell Me

Lighthouse is a good tool. But it has one limitation that shapes everything about how you should build around it: it's a point-in-time measurement.

It tells you how fast your application is today. It doesn't tell you that you've been getting two percent slower every week.

That distinction matters more than it first appears. Nobody ships a change that makes the application twice as slow—that gets caught. What actually happens is a dependency upgrade here, a new third-party script there, an image that wasn't compressed. Each one is invisible on its own. Run Lighthouse and you get a number that looks fine, because it *is* fine compared to nothing.

```text
Run Lighthouse
      │
      ▼
  One report

   (tomorrow)

Run Lighthouse
      │
      ▼
Another report

No history · No trend · No alert
```

Each report was accurate. Together they told me nothing, because nothing connected them. The information I needed didn't live in any single run—it lived in the difference between them, and I was throwing that away every day.

## The Engineering Design

It was easy to think the project was about visualizing Lighthouse scores. It wasn't.

The real goal was to answer one question:

> **"Are we getting slower over time?"**

Lighthouse could already tell us whether the application was fast today. What it couldn't do was remember. So the design problem wasn't measurement—it was persistence and cadence. I needed the measurement to happen without anyone deciding to take it, and I needed the results to accumulate somewhere.

The pipeline was deliberately boring:

```text
GitHub Actions (daily schedule)
            │
            ▼
        Puppeteer
  (open the application)
            │
            ▼
       Lighthouse
 (collect Core Web Vitals)
            │
            ▼
           ELK
    (store every run)
            │
            ▼
      Trend over time
```

Every morning the system ran on its own. No engineer had to remember to run Lighthouse. No one had to compare reports by hand. Every run became another data point.

The dashboard wasn't meant to replace Lighthouse. It extended it by adding the one thing Lighthouse structurally couldn't have: history.

## Nobody Opened It

I expected engineers to check it. Maybe not daily, but occasionally—when they shipped something significant, or when the frontend felt sluggish.

They didn't.

Weeks went by. The reports kept arriving and kept going unread. I'd built something that ran perfectly and that no one had any reason to look at, and I started to file it mentally under *interesting experiment, limited value*. When you build internal tooling, adoption is the metric you reach for first, and by that metric the project was a failure.

Three months in, it was still a failure by that metric.

---

## The Unexpected Lesson

Then an npm dependency upgrade regressed the application's performance.

The dashboard showed it immediately. Not because anyone was watching—because the shape of the data made it obvious. Without history, the slowdown would have looked like today's performance, and today's performance always looks like today's performance. With history, it was clearly a step change on a specific date, traceable to a specific set of merges.

The tool worked exactly as intended on the one day it mattered. It just happened that the previous ninety days had also been the tool working as intended—I'd been reading them wrong.

That's when the reframe landed. The dashboard was valuable *precisely because* nobody had to use it.

> **You don't build monitoring for people to stare at dashboards. You build monitoring so they don't have to.**

The system quietly waited until something became abnormal. That's not a consolation prize for low adoption. That's the design goal, and I'd been evaluating my own work against a standard that contradicted it.

---

## The Principle

One of the more persistent mistakes we make as engineers is measuring a tool's value by how often someone opens it.

Some tools do create value through frequent interaction. IDEs. GitHub. Slack. For those, daily usage is a reasonable proxy for whether the thing is working.

Others create value by quietly reducing risk:

- Monitoring
- Backups
- Smoke tests
- Feature flags
- Performance tracking

For this second category, usage is close to meaningless. Their success isn't measured by how many people looked. It's measured by whether they were ready when something went wrong—and, ideally, by how quickly they made the problem visible once it did.

Applying the first category's metric to the second category is how good tools get deprecated. A backup system that nobody thinks about is not underused; it's working.

Looking back, the mistake wasn't that nobody used the dashboard. The mistake was mine:

> **I assumed the dashboard was the product. The monitoring was the product. The dashboard was only the interface.**

---

## Today I'd Build It Differently

The lesson isn't that the design was right and I misjudged it. Part of the design was genuinely wrong—it just wasn't the part I thought.

What I built ended with a place to look:

```text
Collect metrics
      │
      ▼
  Dashboard
      │
      ▼
Hope someone looks
```

That last step is the flaw. It's a system that does everything correctly and then hands the final, most important job—noticing—to a human who has no prompt to do it. The three months of silence weren't a failure of the pipeline. They were a missing final step.

Today I'd close the loop:

```text
Collect metrics
      │
      ▼
Detect regression
      │
      ▼
  Slack alert
      │
      ▼
Link to the trend
      │
      ▼
Engineer investigates
```

The dashboard still exists. But it becomes somewhere you go *after* receiving a signal, not somewhere you periodically check hoping to notice one. The interface stops being responsible for detection.

That's a small structural change with a large consequence: the time between a regression happening and someone knowing about it drops from "whenever somebody happens to look" to minutes. In my case, that gap was three months of luck.

---

## What This Means for DevEx

There's a pattern here that runs well beyond performance monitoring.

> **The best DevEx tools don't ask engineers to change their behavior—they quietly improve it.**

The same shape shows up across most internal tooling worth building:

- **Frictionless test automation** — developers shouldn't have to think about running tests.
- **Environment management bots** — developers shouldn't coordinate environments in chat.
- **Release sign-off automation** — nobody should manually chase approvals.
- **Lead time analysis** — leaders shouldn't stitch together data from five systems by hand.

None of these require engineers to adopt a new habit. Each removes an existing burden.

That's the useful test when you're deciding what to build: *does this add a step to someone's day, or remove one?* Tools that add a step need continuous advocacy to survive. Tools that remove one survive on their own—and often go unnoticed, which is a compliment, even when it doesn't feel like one.

---

## Questions to Ask Your Team

- What tools are running silently today, and does anyone know they exist?
- Which of our dashboards require a human to notice a problem?
- What signals should be alerts instead of charts?
- Which metrics are we viewing as snapshots when we should be tracking them over time?
- If this tool disappeared tomorrow, would anyone notice before the next incident?

That last question is the one worth sitting with. If the honest answer is "no, not until something broke," that isn't an argument for deleting the tool. It might be the strongest argument for keeping it.

---

## Further Reading

- **Site Reliability Engineering** (Google) — particularly the argument that monitoring should be actionable rather than merely informative.
- **The Four Golden Signals** (Google SRE) — latency, traffic, errors, saturation as a starting vocabulary for what's worth alerting on.
- **Accelerate** (Forsgren, Humble, Kim) — on continuous measurement as an organizational capability.
- **Observability Engineering** (Majors, Fong-Jones, Miranda) — on designing systems that let you ask questions you didn't anticipate.
- **Charity Majors' writing on high-cardinality observability** — on why aggregate dashboards hide the problems you most need to find.

I didn't build the dashboard from any of these. I'm listing them because I arrived at a narrow version of a conclusion these authors had already reached more completely, and reading them afterward is what turned an anecdote into a principle I could apply somewhere else.

---

## The Takeaway

Don't measure a tool by how often it's opened. Measure it by whether it's ready on the day something goes wrong—and then do the work to make sure the right person finds out on that day, without having to go looking.

A tool that quietly waits isn't a tool that failed to catch on. But a tool that quietly waits *and never tells anyone* is only half-built. The silence is the point. The notification is the part I forgot.
