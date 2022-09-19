# Getting Started

### Automate your reviews, focus human effort on what matters most

Not all code changes are equal.  
Some pull requests can be automated, like changes using a specific API (deprecated, sensitive), changes that are non-functional, like docs or tests, or even reformatting code.  
  
Automate these reviews to reduce context switch by assigning specific people to review, approving, or even merging simple changes that passed all checks and more


### Quick Start

Take these 3 steps to see how gitStream works, later on you can learn about the .cm file, see some automation examples and learn how to create and edit your automations.

1. Get gitStream for free from [GitHub marketplace](https://github.com/marketplace/gitstream-by-linearb)
2. Add these 2 files `.cm/gitstream.cm` and `.github/workflows/gitstream.yml` to your repo
3. Open a new PR to see gitStream in action (You don’t have to merge it)

[learn more about the .cm file](20_cm-schema.md)
[automation examples](31_examples.md)

!!! info inline

    Continuous Merge (CM) is the practice of automating the merge path by classifying pull requests based on change type, size, and complexity to allow work to flow more efficiently.