# Examples


## Label PRs by size

Automatically add a label to PRs that are very small to get faster reviewer response.

```yaml+jinja title=".cm/gitstream.cm"
automations:
  mark_good_pr:
    if:
      - {{ branch.diff.size <= 100 }}
      - {{ files | length <= 100 }}
    run:
      - action: add-labels@v1
        args:
          labels: ['good-size']
  mark_big_pr:
    if:
      - {{ branch.diff.size > 100 }}
    run:
      - action: add-labels@v1
        args:
          labels: ['big-size']
```


## Estimated Time for Review 

Automatically add a comment to all PRs with the estimated time for review to get faster reviewer response.

```yaml+jinja title=".cm/gitstream.cm"
automations:
  etr_on_all:
    if:
      - true
    run:
      - action: add-comment@v1
        args:
          comment: "Estimated {{ branch | estimatedReviewTime }} minutes to review"
```


## Approve documents changes  

PRs that include only documentations changes are verified and approved by gitStream. In the example below, marked in yellow, the `files` context is checked by `allDocs` filter that verifies there are only document files. PRs that pass the check are approved by gitStream.

```yaml+jinja title=".cm/gitstream.cm" hl_lines="4"
automations:
  approve_docs:
    if:
      - {{ files | allDocs }}
    run:
      - action: approve@v1
```


## More approvals for complex changes 

Automatically require 2 reviewers for PRs that has more than 100 lines of code changed under the `src` directory.

```yaml+jinja title=".cm/gitstream.cm"
automations:
  double_review:
    if:
      - {{ branch | estimatedReviewTime >= 30 }}
      - {{ files | length >= 10 }}
      - {{ files | match(regex='src\\/') | some }}
    run:
      - action: set-required-approvals@v1
        args:
          reviewers: 2
```


## Validate formatting changes

For PRs that include only code format change, approve merge automatically. The automation includes 2 actions that both are executed, one after another, when all conditions pass.

```yaml+jinja title=".cm/gitstream.cm" hl_lines="7-8"
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


## Request changes on deprecated APIs

For example, assume we have an old API `callingElvis` we want to switch from to a new API `callingGaga`, gitStream can review and trigger a change request automatically when the PR includes use of the deprecated API.

```yaml+jinja title=".cm/gitstream.cm" 
automations:
  catch_deprecated:
    if:
      # Given the PR code changes, check if a call to an unwanted API was introduced
      - {{ source.diff.files | matchDiffLines(regex='^[+].*callingElvis\\(') | some }}
    run:
      - action: request-changes@v1
        args:
        comment: |
          Deprecated API used `callingElvis`, use `callingGaga` instead
```

![Request changes automatically](screenshots/change_use_deprectaed_api.png)


## Approve additional tests

You can use map to check that a PR was about adding more tests.

```yaml+jinja title=".cm/gitstream.cm" hl_lines="23 25"
automations:
  tests_safe_changes:
    if:
      # Given the PR files changes, check that only tests were changed. The allTests filter checks for 
      # the substring `test` or `spec` in the file path or file name.
      - {{ files | allTests }}
      # Checking `changes.ratio` which is calculated below
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

# You can add more sections to the .cm file to place more logic, and use it in your automations
# The following logic `ratio` result is used in the automation above by checking `changes.ratio`
changes:
  # Sum all the line added/edited in the PR
  additions: {{ branch.diff.files_metadata | map(attr='additions') | sum }}
  # Sum all the line removed in the PR
  deletions: {{ branch.diff.files_metadata | map(attr='deletions') | sum }}
  # Calculate the ratio to check if it is about new code
  ratio: {{ changes.additions / (changes.additions + changes.deletions) }}
```

As a result, if you add test cases to your repo, gitStream can automatically check that and approve the PR automatically.

![Adding tests example](screenshots/adding_tests_to_repo.png)
