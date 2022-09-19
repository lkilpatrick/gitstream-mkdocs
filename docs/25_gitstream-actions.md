# Automation actions

gitStream executes actions in the order they are listed. If an action result fails, following actions will not be executed.

- [`approve`](#approve-action)
- [`merge`](#merge-action)
- [`set-required-approvals`](#set-required-approvals-action)
- [`add-reviewers`](#add-reviewers-action)
- [`add-labels`](#add-labels-action)
- [`add-comment`](#add-comment-action)
- [`update-check`](#update-check-action)

#### `approve` action

This action, once triggered, approves the PR for merge.

Syntax: 

```yaml
action: approve@v1
```

Example:

```yaml
automations:
  small_change:
    if:
      - {{ source.diff.files | isFormattingChange }}
    run:
      - action: approve@v1
```


#### `merge` action

This action, once triggered, approve & merge the PR.

It can set to wait for required checks to pass or ignore checks.

Syntax: 

```yaml
action: merge@v1
args:
    wait_for_all_checks: Boolean # default: false
    squash_on_merge: Boolean # default: false
```

Example:

```yaml
automations:
  small_change:
    if:
      - {{ files | allDocs }}
    run:
      - action: merge@v1
```


#### `set-required-approvals` action

This action, once triggered, blocks PR merge till the desired reviewers approved the PR. The actions fail the check to prevent the PR for merge.

Note: you should enable branch protection, so GitHub will prevent merging unless the gitStream action check pass successfully. 

Syntax: 

```yaml
action: set-required-approvals@v1
args: 
  approvals: Integer 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `approvals`| Integer   | Sets the number of required reviewer approvals for merge for that PR|

```yaml
automations:
  double_review:
    if:
      - {{ files | isSomeInListRegex('core\/') }}
    run:
      - action: set-required-approvals@v1
        args:
          approvals: 2
```

#### `add-reviewers` action

This action, once triggered, sets a specific reviewer.

Syntax: 

```yaml
action: add-reviewers@v1
args: 
  reviewers: [String] 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `reviewers` | [String]    | Sets reviewers user name |

```yaml
automations:
  senior_review:
    if:
      - {{ files | isSomeInListRegex('src\/ui\/') }}
    run:
      - action: add-reviewers@v1
        args:
          reviewers: [popeye, olive]
```

#### `add-labels` action

This action, once triggered, adds a label to the PR.

Syntax: 

```yaml
action: add-labels@v1
args: 
  labels: [String] 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `label`    | [String]  | List of labels, any string can work |

```yaml
automations:
  senior_review:
    if:
      - {{ files | isSomeInListRegex('api\/') }}
    run:
      - action: add-labels@v1
        args:
          labels: [api-change]
```

#### `add-comment` action

This action, once triggered, adds a comment to the PR.

Syntax: 

```yaml
action: add-comment@v1
args: 
  comment: String 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `comment`  | String    | Sets the comment, markdown is supported |

```yaml
automations:
  senior_review:
    if:
      - {{ files | isSomeInListRegex('core\/') }}
    run:
      - action: add-comment@v1
        args:
          comment: |
          Core service update
          (Updates API)
```

####  `update-check` action

Used to workaround unnecessary checks, this action, update the defined check with the defined status if all conditions pass.

Syntax: 

```yaml
action: update-check@v1
args: 
  checkName: cypress-e2e
  status: completed
  conclusion: success
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `comment`  | String    | Sets the comment, markdown is supported |


```yaml
automations:
  senior_review:
    if:
      - {{ files | isEveryInListRegex('.*.png$|.*.jpg$|.*.svg$|.*\.css$') }}
    run:
      - action : update-check@v1
        args:
          checkName: cypress-e2e
          status: completed
          conclusion: success
```