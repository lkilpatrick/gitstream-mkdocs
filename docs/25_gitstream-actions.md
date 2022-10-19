# Automation actions

## Overview

gitStream executes actions in the order they are listed. If an action result fails, following actions will not be executed.

- [`add-comment`](#add-comment)
- [`add-label`](#add-label)
- [`add-reviewers`](#add-reviewers)
- [`approve`](#approve)
- [`merge`](#merge)
- [`set-required-approvals`](#set-required-approvals)
- [`require-reviewers`](#require-reviewers)
- [`request-changes`](#request-changes)
- [`update-check`](#update-check)

!!! note

    Multiple actions can be listed in a single automation. The actions are invoked one by one.

#### Dynamic actions arguments

Arguments values a dynamic value is supported using expressions based on Jinja2 syntax, and includes gitStream context variables, for example:

```yaml+jinja
automations:
  pr_complexity:
    if:
      - true
    run:
      - action: add-comment@v1
        args:
          comment: "Estimated {{ branch | estimatedReviewTime }} minutes to review"
```


## Reference 

#### `add-comment`

This action, once triggered, adds a comment to the PR.

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `comment`  | String    | Sets the comment, markdown is supported |

```yaml+jinja title="example"
automations:
  senior_review:
    if:
      - {{ files | match(term='core/') | some }}
    run:
      - action: add-comment@v1
        args:
          comment: |
            Core service update
            (Updates API)
```


#### `add-label`

This action, once triggered, adds a label to the PR.

This is a manged action, when a PR updates existing labels that was added by gitStream are re-evaluated and those that are not applicable are removed.

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `label`    | String  | The label text any string can work |
| `color`    | String  | (optional) The color in hexa, for exmaple: `'FEFEFE'` (you can also add `#` prefix `#FEFEFE`) |

```yaml+jinja title="example"
automations:
  senior_review:
    if:
      - {{ files | match(term='api/') | some }}
    run:
      - action: add-label@v1
        args:
          label: api-change
```


#### `add-reviewers`

This action, once triggered, sets a specific reviewer.

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `reviewers` | [String]    | Sets reviewers user name |

```yaml+jinja title="example"
automations:
  senior_review:
    if:
      - {{ files | match(term='src/ui/') }}
    run:
      - action: add-reviewers@v1
        args:
          reviewers: [popeye, olive]
```


#### `approve`

This action, once triggered, approves the PR for merge.

```yaml+jinja title="example"
automations:
  small_change:
    if:
      - {{ source.diff.files | isFormattingChange }}
    run:
      - action: approve@v1
```


#### `merge`

This action, once triggered, approve & merge the PR. It can set to wait for required checks to pass or ignore checks.

Syntax: 

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `wait_for_all_checks`| Boolean | By default `false`, so only Required checks can block merge, when `true` the action won't merrge even if non-Required check fail  |
| `squash_on_merge`| Boolean   | When merging use squash mode |


```yaml+jinja title="example"
automations:
  small_change:
    if:
      - {{ files | allDocs }}
    run:
      - action: merge@v1
```


#### `set-required-approvals`

This action, once triggered, blocks PR merge till the desired reviewers approved the PR. The actions fail the check to prevent the PR for merge.

!!! note

    You should enable branch protection, so GitHub will prevent merging unless the gitStream action check pass successfully. 

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `approvals`| Integer   | Sets the number of required reviewer approvals for merge for that PR|

```yaml+jinja title="example"
automations:
  double_review:
    if:
      - {{ files | match(regex=r/agent\//) | some }}
    run:
      - action: set-required-approvals@v1
        args:
          approvals: 2
```


#### `request-changes`

This action, once triggered, request changes on the PR. As long as request change is set, gitStream will block the PR merge.

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `comment` | [String]    | The desired request changes comment |

```yaml+jinja title="example"
automations:
  catch_deprecated:
    if:
      - {{ source.diff.files | matchDiffLines(regex=r/^[+].*oldFetch\(/') | some }}
    run:
      - action: request-changes@v1
        args:
          comment: |
            You have used deprected API `oldFetch`, use `newFetch` instead.
```

#### `require-reviewers`

This action, once triggered, sets a specific reviewer.

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `reviewers` | [String]    | Sets reviewers user name, merge is blocked till approved by any of the listed users |

```yaml+jinja title="example"
automations:
  senior_review:
    if:
      - {{ files | match(regex=r/src\/ui\//) | some }}
    run:
      - action: require-reviewers@v1
        args:
          reviewers: ['popeye', 'olive']
```

#### `update-check`

Used to workaround unnecessary checks, this action, update the defined check with the defined status if all conditions pass.

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `check_name`  | String    | The name of the check |
| `status`  | String    | The status of the check |
| `conclusion`  | String    | The conclusion of the check |


```yaml+jinja title="example"
automations:
  senior_review:
    if:
      - {{ files | match(regex=r/.*\.png$|.*\.jpg$|.*\.svg$|.*\.css$/) | every }}
    run:
      - action : update-check@v1
        args:
          check_name: cypress-e2e
          status: completed
          conclusion: success
```