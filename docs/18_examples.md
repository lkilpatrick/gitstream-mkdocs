# Examples

### Add label to PR by PR size

Automatically add a label to PRs that are very small to get faster reviewer response.

```yaml title=".cm/gitstream.cm"
automations:
  mark_good_pr:
    if:
      - {{ branch.diff.size <= 100 }}
      - {{ files | length <= 100 }}
    run:
      - action: add-labels@v1
        args:
          labels: [good_size]
  mark_big_pr:
    if:
      - {{ branch.diff.size > 100 }}
    run:
      - action: add-labels@v1
        args:
          labels: [big_size]
```

### Add Estimated Time for Review in PRs comment 

Automatically add a comment to all PRs with the estimated time for review to get faster reviewer response.

```yaml title=".cm/gitstream.cm"
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

```yaml title=".cm/gitstream.cm"
automations:
  approve_docs:
    if:
      - {{ files | allDocs }}
    run:
      - action: approve@v1
```


### Set 2 reviewers for large PRs 

Automatically require 2 reviewers for PRs that has more than 100 lines of code changed under the `src` directory.

```yaml title=".cm/gitstream.cm"
automations:
  double_review:
    if:
      - {{ branch.diff.size > 100 }}
      - {{ files | match(regex='src\\/') | some }}
    run:
      - action: set-required-approvals@v1
        args:
          reviewers: 2
```

### Approve indentation changes in JavaScript files 

For PRs that include only code format change, approve merge automatically.

```yaml title=".cm/gitstream.cm"
automations:
  allow_formatting:
    if:
      - {{ files | extensions | match(list=['js', 'ts']) | every }}
      - {{ source.diff.files | isFormattingChange }}
    run:
      - action: approve@v1
      - action: add-labels@v1
        args:
          labels: [code-formatting]

```

!!! note

    Multiple actions can be listed in a single automation. The actions are invoked one by one.
    
    Multiple conditons can be listed for a single automation. All listed conditions must pass to triger the actions.
    
### Automatcialy review and request code change when using deprecated APIs

For example assume we have an old API `oldCall` we want to switch from to a new API `newCall`, gitStream can review and trigger a change request automatically when the PR includes use of the deprected API.

```yaml title=".cm/gitstream.cm"
automations:
  catch_deprecated:
    if:
      - {{ source.diff.files | matchDiffLines('oldCall\\(', ignoreWhiteSpaces=true) | every }}
    run:
      - action: request-changes@v1
        args:
        comment: |
          Deprecated API used `oldCall`, use `newCall` instead
```

### Approve additonal tests automatically

You can use map to check that a PR was about adding more tests.

```yaml
automations:
  tests_safe_changes:
    if:
      # Given the PR files changes, check that only tests were changed. The allTests filter checks for 
      # the substring `test` or `spec` in the file path or file name.
      - {{ files | allTests }}
      - {{ changes.ratio > 0.8 }}
    run: 
      - action: add-labels@v1
        args:
          labels: ['tests-changes']
      - action: add-comment@v1
        args:
          comment: |
            PR added tests (ratio: {{ changes.ratio }})
      # - action: approve@v1

changes:
  additions: {{ branch.diff.files_metadata | map(attr='additions') | sum }}
  deletions: {{ branch.diff.files_metadata | map(attr='deletions') | sum }}
  ratio: {{ changes.additions / (changes.additions + changes.deletions) }}
```
