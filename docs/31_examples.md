# Examples

### Add label to PR by PR size

Automatically add a label to PRs that are very small to get faster reviewer response.

Edit your `.cm/gitstream.cm` to include the following:

```yaml
checks:
  size:
    is:
      xsmall: {{ branch.diff.size <= 5 }}
      small: {{ branch.diff.size > 5 and branch.diff.size <= 20 }}
      medium: {{ branch.diff.size > 20 and branch.diff.size <= 100 }}
      large: {{ branch.diff.size > 100 and branch.diff.size <= 200 }}
      xlarge: {{ branch.diff.size > 200 }}

automations:
  ...
  mark_good_pr:
    if:
      - {{ checks.size.is.xsmall or checks.size.is.small or checks.size.is.medium }}
    run:
      - action: add-labels@v1
        args:
          labels: [good_size]
  mark_big_pr:
    if:
      - {{ checks.size.is.large or checks.size.is.xlarge }}
    run:
      - action: add-labels@v1
        args:
          labels: [big_size]
```

### Add Estimated Time for Review in PRs comment 

Automatically add a comment to all PRs with the estimated time for review to get faster reviewer response.

Edit your `.cm/gitstream.cm` to include the following:

```yaml
automations:
  etr_on_all:
    if:
      - true
    run:
      - action: add-comment@v1
        args:
          comment: "Estimated {{ branch | estimatedReviewTime }} minutes to review"
```

### Automated check and approve low risk PRs 

Automatically add a comment to all PRs with the estimated time for review to get faster reviewer response.

Edit your `.cm/gitstream.cm` to include the following:

```yaml
automations:
  ...
  approve_docs:
    if:
      - {{ files | allDocs }}
    run:
      - action: approve@v1
```


### Set 2 reviewers for large PRs 

Automatically require 2 reviewers for PRs that has more than 100 lines of code changed.

Edit your `.cm/gitstream.cm` to include the following:

```yaml
automations:
  double_review:
    if:
      - {{ branch.diff.size > 100 }}
    run:
      - action: set-required-approvals@v1
        args:
          reviewers: 2
```

### Approve indentation changes in JavaScript files 

For PRs that include only code format change, approve merge automatically.

Edit your `.cm/gitstream.cm` to include the following:

```yaml
automations:
  allow_formatting:
    if:
      - {{ files | isEveryExtension(['js', 'ts']) }}
      - {{ source.diff.files | isFormattingChange }}
    run:
      - action: approve@v1
      - action: add-labels
        args:
          labels: [code-formatting]

```
