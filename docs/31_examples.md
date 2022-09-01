# Examples

### Add label for x-small PRs

:octicons-tag-24: Minimal version: 1.0

Automatically add a label to PRs that are very small to get faster reviewer response.

Edit your .cm/gitstream.cm to include the following:

```yaml
checks:
  ...
  size:
    is:
      xsmall: {{ branch.diff.size < 20 }}

automations:
  ...
  mark_small_pr:
    if:
      - {{ checks.size.is.xsmall }}
    run:
      - action: add-labels@v1
        args:
          labels: [xsmall]
```

### Add Estimated Time for Review in PRs comment 

:octicons-beaker-24: Coming soon

Automatically add a comment to all PRs with the estimated time for review to get faster reviewer response.

Edit your .cm/gitstream.cm to include the following:

```yaml
checks:
  ...
  etr: {{ branch | estimatedReviewTime }}

automations:
  ...
  etr_on_all:
    if:
      - true
    run:
      - action: add-comment@v1
        args:
          comment: "Estimated {{ checks.etr }} to review"
```

### Auto approve low risk PRs 

:octicons-tag-24: Minimal version: 1.0

Automatically add a comment to all PRs with the estimated time for review to get faster reviewer response.

Edit your .cm/gitstream.cm to include the following:

```yaml
checks:
  ...
  filetypes:
    is:
      docs: {{ files | allDocs }}

automations:
  ...
  approve_docs:
    if:
      - {{ checks.filetypes.is.docs }}
    run:
      - action: approve@v1
```

### Set 2 reviwers for high complexity PRs 

:octicons-tag-24: Minimal version: 1.0

Automatically require 2 reviewers for PRs that changes core functionlity.

Edit your .cm/gitstream.cm to include the following:

```yaml
checks:
  ...
  change:
    is:
      core: {{ files | filterRegex('core\/') | length > 0 }}

automations:
  ...
  double_review:
    if:
      - {{ checks.change.is.core }}
    run:
      - action: set-required-approvals@v1
        args:
          reviewers: 2
```

