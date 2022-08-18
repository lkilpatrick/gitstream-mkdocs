# gitStream built-in actions

- [`approve`](#approve-action)
- [`set-required-reviewers`](#set-required-reviewers-action)
- [`set-reviewer`](#set-reviewer-action)
- [`add-label`](#add-label-action)
- [`add-comment`](#add-comment-action)

#### approve action

This action, once triggered, approves the PR for merge.

Syntax: 

```yaml
action: approve@v1
engine: gitstream
```

Example:

```yaml
checks:
  size:
    is:
      xsmall: {{ branch.diff.size < 20 }}

automations:
  small_change:
    if:
      - {{ checks.size.is.xsmall }}
    run:
      - action: approve@v1
        engine: gitstream
```

#### set required reviewers action

This action, once triggered, blocks PR merge till the desired reviewers approved the PR. The actions fails the check to prevent the PR for merge.

Note: you should enable branch protection so GitHub will prevent merging unless the gitStream action check pass successfully. 

Syntax: 

```yaml
action: set-required-reviewers@v1
engine: gitstream
args: 
  - reviewers: Integer 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `reviewers`| Integer   | Sets the number of required reviewer approvals for merge for that PR|

```yaml
checks:
  change:
    is:
      critical: {{ source | grep(‘panic’) | length > 0 }}

automations:
  double_review:
    if:
      - {{ checks.change.is.critical }}
    run:
      - action: set-required-reviewers@v1
        engine: gitstream
        args:
          - reviewer: 2
```

#### set reviewer action

This action, once triggered, sets a specific reviewer.

Syntax: 

```yaml
action: set-reviewer@v1
engine: gitstream
args: 
  - reviewer: String 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `reviewer` | String    | Sets reivewer user name |

```yaml
checks:
  change:
    is:
      core_service: {{ files | regexFilter(‘core’) | length > 0 }}

automations:
  senior_review:
    if:
      - {{ checks.change.is.core_service }}
    run:
      - action: set-reviewer@v1
        engine: gitstream
        args:
          - reviewer: @team-leader
```

#### add label action

#### add comment action
