# Small Thing — zdog

![Small Thing — zdog](../images/small-thing-2.png)

*Not every small thing works.*

---

**The problem.** At Zillow, testing almost anything started with assembling a user. You needed one in the right state, and then you needed the things that hung off them — their dashboards, their logs — or you needed to create a fresh one from scratch. Each of those lived somewhere different, and each had its own way of being reached.

None of it was hard. All of it was scattered, and scattered work has a way of costing more attention than the sum of its parts.

**What I built.** A Chrome extension. Test user data in one panel, direct links to that user's dashboards and logs, a button to create a new one. A little test helper. Nothing clever — just every scattered thing gathered into one place that was already open, because the browser was where the work was happening anyway.

It also had a dog. An animated one, and every time you opened the extension it was visibly excited to see you. I called it zdog.

That wasn't only a joke. Internal tooling is relentlessly joyless — grey panels built by people who had a ticket to close. A tool you enjoy opening gets opened, and for a tool whose entire value depends on being opened, that isn't decoration. It's the feature.

**What happened.** Not long after I finished it, I was affected by a company-wide reduction in force.

So I was the only person who ever used it. A small, enthusiastic dog, waiting at the door of an extension nobody else installed. It became Hachikō.

---

**What I'd take from it.**

[Chapter 2](Chapter%202.md) argues that a tool doesn't need to be opened to be worth having. I still believe that. But zdog marks where the argument ends. There's a difference between a tool nobody opens because it's quietly doing its job, and a tool nobody opens because nobody else ever knew it was there. The first one is monitoring working as designed. The second one is just a thing I made.

The uncomfortable part is that the build was the easy half. Getting other people to install it, telling them it existed, finding out whether it actually fit how they worked — that's the half that turns a tool into a force multiplier, and it's the half I never started. One person using one tool is a preference. The multiplication is other people.

**When this doesn't apply.** Building something only you will use is fine — plenty of good tools start as one person's private convenience and spread later because someone saw it over a shoulder. The mistake isn't building for yourself. It's assuming that spreading happens on its own, and treating the last ten percent as somebody else's problem.

Sometimes you don't get the last ten percent. Sometimes the timing decides for you.

---

> **A tool with one user is a preference. It isn't a force multiplier until someone else opens it.**
