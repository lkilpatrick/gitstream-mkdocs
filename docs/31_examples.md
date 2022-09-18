# Examples

### Add label to PR by PR size

:octicons-tag-24: Minimal version: 1.0

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

:octicons-beaker-24: Coming soon

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

:octicons-tag-24: Minimal version: 1.0

Automatically add a comment to all PRs with the estimated time for review to get faster reviewer response.

Edit your `.cm/gitstream.cm` to include the following:

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

### Set 2 reviewers for high complexity PRs 

:octicons-tag-24: Minimal version: 1.0

Automatically require 2 reviewers for PRs that changes core functionality.

Edit your `.cm/gitstream.cm` to include the following:

```yaml
checks:
  change:
    is:
      core: {{ files | isSomeInListRegex('core\/') }}

automations:
  double_review:
    if:
      - {{ checks.change.is.core }}
    run:
      - action: set-required-approvals@v1
        args:
          reviewers: 2
```

### Set 2 reviewers for large PRs 

:octicons-tag-24: Minimal version: 1.0

Automatically require 2 reviewers for PRs that has more than 100 lines of code changed.

Edit your `.cm/gitstream.cm` to include the following:

```yaml
checks:
  ...
  change:
    is:
      core: {{ branch.diff.size > 100 }}

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

### Approve indentation changes in JavaScript files 

:octicons-beaker-24: Coming soon

For PRs that include only code format change, approve merge automatically.

Edit your `.cm/gitstream.cm` to include the following:

```yaml
checks:
  ...
  content:
    is:
      only_js: {{ files | isEveryExtension(['js', 'ts']) }}
      only_formatting: {{ source.diff.files | isFormattingChange }}

automations:
  ...
  allow_formatting:
    if:
      - {{ checks.content.is.only_js }}
      - {{ checks.content.is.only_formatting }}
    run:
      - action: approve@v1
      - action: add-labels
        args:
          labels: [code-formatting]

```

### Approve small changes with 100% coverage on changes 

:octicons-beaker-24: Coming soon

Approve small PRs that has 100% code coverage.

Edit your .cm/gitstream.cm to include the following:

```yaml
checks:
  ...
  is:
    simple: {{ files | length == 1 }}
    small: {{ branch.diff.size < 10 }}

automations:
  ...
  small_and_verified:
    if:
      - {{ checks.is.simple }}
      - {{ checks.is.small }}
    run:
      - action: approve@v1
      - action: add-labels
        args:
          labels: [verified]

```

For the above to work you need to load the inputs context with the coverage results, see [here](22_custom-context.md).
