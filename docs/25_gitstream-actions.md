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

:octicons-tag-24: Minimal version: 1.0

This action, once triggered, approves the PR for merge.

Syntax: 

```yaml
action: approve@v1
engine: gitstream
```

Example:

```yaml
checks:
  filetypes:
    is:
      docs: {{ files | allDocs }}

automations:
  small_change:
    if:
      - {{ checks.filetypes.is.docs }}
    run:
      - action: approve@v1
```


#### `merge` action

:octicons-beaker-24: Coming soon

This action, once triggered, approve & merge the PR.

It can set to wait for required checks to pass or ignore checks.

Syntax: 

```yaml
action: merge@v1
engine: gitstream
args:
    wait_for_all_checks: Boolean # default: false
    squash_on_merge: Boolean # default: false
```

Example:

```yaml
checks:
  filetypes:
    is:
      docs: {{ files | allDocs }}

automations:
  small_change:
    if:
      - {{ checks.filetypes.is.docs }}
    run:
      - action: merge@v1
        args:
          wait_for_all_checks: true
```


#### `set-required-approvals` action

:octicons-tag-24: Minimal version: 1.0

This action, once triggered, blocks PR merge till the desired reviewers approved the PR. The actions fails the check to prevent the PR for merge.

Note: you should enable branch protection so GitHub will prevent merging unless the gitStream action check pass successfully. 

Syntax: 

```yaml
action: set-required-approvals@v1
engine: gitstream
args: 
  approvals: Integer 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `approvals`| Integer   | Sets the number of required reviewer approvals for merge for that PR|

```yaml
checks:
  change:
    is:
      core_service: {{ files | filterRegex('core') | length > 0 }}

automations:
  double_review:
    if:
      - {{ checks.change.is.core_service }}
    run:
      - action: set-required-approvals@v1
        args:
          approvals: 2
```

#### `add-reviewers` action

:octicons-tag-24: Minimal version: 1.0

This action, once triggered, sets a specific reviewer.

Syntax: 

```yaml
action: add-reviewers@v1
engine: gitstream
args: 
  reviewers: [String] 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `reviewers` | [String]    | Sets reivewer user name |

```yaml
checks:
  change:
    is:
      core_service: {{ files | filterRegex('core') | length > 0 }}

automations:
  senior_review:
    if:
      - {{ checks.change.is.core_service }}
    run:
      - action: add-reviewers@v1
        args:
          reviewers: [@team-leader]
```

#### `add-labels` action

:octicons-tag-24: Minimal version: 1.0

This action, once triggered, adds a label to the PR.

Syntax: 

```yaml
action: add-labels@v1
engine: gitstream
args: 
  labels: [String] 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `label`    | [String]  | List of labels, any string can work |

```yaml
checks:
  change:
    is:
      core_service: {{ files | filterRegex('core') | length > 0 }}

automations:
  senior_review:
    if:
      - {{ checks.change.is.core_service }}
    run:
      - action: add-labels@v1
        args:
          labels: [core]
```

#### `add-comment` action

:octicons-tag-24: Minimal version: 1.0

This action, once triggered, adds a comment to the PR.

Syntax: 

```yaml
action: add-comment@v1
engine: gitstream
args: 
  comment: String 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `comment`  | String    | Sets the comment, markdown is supported |

```yaml
checks:
  change:
    is:
      core_service: {{ files | filterRegex(‘core’) | length > 0 }}

automations:
  senior_review:
    if:
      - {{ checks.change.is.core_service }}
    run:
      - action: add-comment@v1
        args:
          comment: |
          Core service update
          (Updates API)
```

####  `update-check` action

:octicons-tag-24: Minimal version: 1.0

Used to workaround unnecessary checks, this action, update the defined check with the defined 
status if all conditions pass.

Syntax: 

```yaml
action: update-check@v1
engine: gitstream
args: 
  checkName: cypress-e2e
  status: completed
  conclusion: success
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `comment`  | String    | Sets the comment, markdown is supported |


```yaml
checks:
  content:
    is:
      assets_only: {{ files | allPassRegex('.*.png$|.*.jpg$|.*.svg$|.*\.css$') }}
      
automations:
  senior_review:
    if:
      - {{ checks.content.is.assets_only }}
    run:
      - action : update-check@v1
        args:
          checkName: cypress-e2e
          status: completed
          conclusion: success
```