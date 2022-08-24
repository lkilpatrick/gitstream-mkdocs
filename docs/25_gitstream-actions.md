# gitStream built-in automation actions

- [`approve`](#approve-action)
- [`set-required-approvals`](#set-required-approvals-action)
- [`set-reviewer`](#set-reviewer-action)
- [`add-labels`](#add-labels-action)
- [`add-comment`](#add-comment-action)

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

#### `set-required-approvals` action

:octicons-tag-24: Minimal version: 1.0

This action, once triggered, blocks PR merge till the desired reviewers approved the PR. The actions fails the check to prevent the PR for merge.

Note: you should enable branch protection so GitHub will prevent merging unless the gitStream action check pass successfully. 

Syntax: 

```yaml
action: set-required-approvals@v1
engine: gitstream
args: 
  required_approvals: Integer 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `required_approvals`| Integer   | Sets the number of required reviewer approvals for merge for that PR|

```yaml
checks:
  change:
    is:
      critical: {{ source | grep('panic') | length > 0 }}

automations:
  double_review:
    if:
      - {{ checks.change.is.critical }}
    run:
      - action: set-required-approvals@v1
        args:
          required_approvals: 2
```

#### `set-reviewer` action

:octicons-beaker-24: Coming soon

This action, once triggered, sets a specific reviewer.

Syntax: 

```yaml
action: set-reviewer@v1
engine: gitstream
args: 
  reviewer: String 
```

| Args       | Type      | Description                                     |
| -----------|-----------|------------------------------------------------ |
| `reviewer` | String    | Sets reivewer user name |

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
      - action: set-reviewer@v1
        args:
          reviewer: @team-leader
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

#### add comment action

:octicons-tag-24: Minimal version: 1.0

This action, once triggered, adds a comment to the PR.

Syntax: 

```yaml
action: add-comment@v1
engine: gitstream
args: 
  - comment: String 
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