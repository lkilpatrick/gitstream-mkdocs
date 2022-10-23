# /:\ gitStream

**gitStream automates your reviews, so you can focus human effort on what matters most.** Not all code changes are equal. Some pull requests can be automated, like changes using a specific API (deprecated, sensitive), changes that are non-functional, like docs or tests, or even reformatting code. Automate these reviews to reduce context switch by assigning specific people to review, approving, or even merging simple changes that passed all checks and more.

#### Quick Start

Take these 3 steps to see how gitStream works, later on you can learn about the `.cm` file, see some automation examples and learn how to create and edit your automations.

1. Get gitStream for free from [GitHub marketplace](https://github.com/marketplace/gitstream-by-linearb)
2. Add these 2 files [`.cm/gitstream.cm`](assets/gitstream.cm) and [`.github/workflows/gitstream.yml`](assets/gitstream.yml) to your repo
3. Open a new PR to see gitStream in action (You don’t have to merge it)

## Features

**Multiple conditions**

- [Files names and path](context-variables/#files-context)
- [Code changes](context-variables/#source-context)
- [Detect specific changes using regex expressions](filter-functions/#matchdifflines)
- [Check for formatting changes](filter-functions/#isformattingchange)
- [Calculate PR complexity](filter-functions/#estimatedreviewtime)  

**Review automations**

- Add labels and comments to PR
- Approve or Merge PRs
- Request Changes with contextual content
- [and more...](automation-actions)

**Easy to configure and powerful**

- Review automation as code `.cm` files
- Based on Jinja2 template engine
- Rich expressions support 
- [Powerful built-in filter functions](filter-functions)

## Continuous Merge

**Continuous Merge (CM)** is the practice of automating the merge path by classifying pull requests based on change type, size, and complexity to allow work to flow more efficiently.

[learn more about the .cm file](editing-cm-file)

[automation examples](examples)

![Continuous Merge](assets/ContinuousMerge3l.png#only-light)
![Continuous Merge](assets/ContinuousMerge3d.png#only-dark)

