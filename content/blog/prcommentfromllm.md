---
title:  Why writing a good PR description is more important than ever
description: A great PR description is no longer documentation—it’s the master prompt that tells humans and AI why your code exists and whether it should be merged.
date: 2025-12-26
tags: LLM, Pull Request, Engineering, Developer Experience, Code Review
---

What differentiates a good engineer from a great one? One of the most impactful improvements you can make in your career is learning how to write well. This is an invaluable trait that will help you with everything from writing design docs and getting buy-in from your team, to composing better prompts and—why not—charming pull requests. This is especially important in the AI age; it can be a game changer.

Some years ago, I came across this blog post by Gergely Orosz: [On Writing Well](https://blog.pragmaticengineer.com/on-writing-well/). It was an eye-opening revelation that we can put into practice in our day-to-day work.

As you probably already know, AI is everywhere, especially in the software development lifecycle. Your work doesn’t end after "vibe coding" a 200k LOC solution to a bug; you need to get this change merged, and for that, you need to be able to convince people. This is where we go back to the basics. It is even more important when there are already AI agents reviewing these diffs; as usual with AI, context is king.

## How to write a pull request

**First things first: we judge a book by its cover.** Your PR title is the most important part—try to make it as catchy and factual as possible. It should describe the intent, not necessarily the "how."

Don't forget the **Semantic Conventions**. A great PR starts with a searchable title. Adopting [Conventional Commits](https://www.conventionalcommits.org/) (e.g., `feat:`, `fix:`, `refactor:`) isn't just for automation or generating changelogs; it gives the reviewer—human or AI—an immediate mental map of the scope of your changes. It’s the "Subject Line" that determines the priority of the review.

GitHub is arguably one of the masters of writing pull requests. Ten years ago, they wrote an excerpt that still stands: [How to write the perfect pull request](https://github.blog/developer-skills/github/how-to-write-the-perfect-pull-request/). In short, your PR description should answer the following questions:

* **Why:** Why this change is needed.
* **What:** What problem it solves.
* **How:** Explain clearly what the code does. This is probably one of the most important parts these days. If you don’t understand the code your favorite LLM agent has produced, you won’t get this code merged.
* **How it has been tested:** Was it by hand, a unit test, or an integration test?
* **Extra context:** Once this is provided, it is up to the reviewer (or LLM) to skim over your detailed implementation.

## Workflow: Using AI as Your Technical Writer

If you are a seasoned "vibe coder," you can just tell your favorite tool to do it for you! Tools like **Cursor** allow you to do that simply by using `@Git (Diff of Working Copy)` and asking for a description directly.

However, I propose a different workflow—one that is more ubiquitous and works with any tool:

### 1. Generate Real Context
Once you’ve finished your changes and tests, extract the exact diff of your work into a patch file. This prevents the AI from hallucinating based on files you might have open that aren't part of the actual submission:

```bash
git diff origin/main..HEAD > changes.patch
```

### 2. The Master Prompt
Take that file to your trusted LLM (Cursor, Claude, ChatGPT) and use this prompt to get a professional description:

```
Generate a clear and concise Pull Request description summarizing the changes introduced in this branch. Use the diff provided in @changes.patch as the source of truth. Your description should include:

- A high-level overview of what was changed and why.

- Any important implementation details or noteworthy decisions.

- Impact or risks (breaking changes, performance, etc.).

- Testing or validation steps.
```

As always, review the output. Correct what is not accurate and try to give it your own tone. Don’t be a stochastic parrot.

## Conclusion
In a world where LLMs perform the code reviews, your PR description is now the master prompt guiding that review. A mediocre description will result in a mediocre review.

If you want technology to work for you, and not the other way around, mastering context communication is your new superpower. A machine can generate the code, but only you can provide the direction.