# Configuring gitStream automation

This document is a reference for the gitStream 1.x, used in .cm/gitstream.cm file.

## Schema

- [`manifest`](#manifest-section)
    - [`version`](#manifest-section)
- [`checks`](#checks-section)
- [`automations`](#automations-section)
    - [`if`](#automations-section)
    - [`run`](#automations-section)

### Manifest section

The first section in a gitstream.cm is `manifest`.

```yaml
manifest: 
  version: 1.0
```

The only field required is `version`.

| Key         | Required | Type    | Description                              |
| ----------- | ---------|---------|----------------------------------------- |
| `manifest`  | Y        | Map     |                                          |
| `version`   | Y        | String  | Specify the `.cm` spec version: 0.1, 1.0 |

The manifest fields is used a version to parse the `.cm` file, so if breaking chnages are 
introdcued to the parser - older automation are still supported.

### Checks section

The `checks` section defines conditions that are nested under the `checks` section.  

```yaml
checks:
  size:
    is:
      xsmall: {{ branch.diff.size < 20 }}
```

Each condition includes a logic expresison that eventually results with a boolean value: `true` or `false`. 

The logic expression are based on Jinja2 syntax, and includes gitStream context varaibales.


### Automations section

The `automations` section defines the automations and their conditions. 

```yaml
automations:
  mark_small_pr:
    if:
      - {{ checks.size.is.xsmall }}
    run:
      - action: add-label@v1
        engine: gitstream
        args:
          - text: xsmall
```

Each automation includes its name, and few fields: `if` and `run`.

| Key          | Required | Type    | Description                                     |
|--------------|----------|---------|------------------------------------------------ |
| _automation_ | Y        | Map     | Name of the automation, can be any string       |
| `if`         | Y        | Map     | List of condtions                               |
| `run`        | Y        | Map     | The automation to run if all conditions are met |

The `if` field includes the list of conditions. The condtions are checked when a pull request is opened or changed, if all the conditions pass, the automation is executed.

The `run` field includes the automation to execute. 

| Key         | Required | Type    | Description                                     |
| ----------- | ---------|---------|------------------------------------------------ |
| `action`    | Y        | String  | The action pointer                              |
| `engine`    | N        | String  | The action engine, default is `gitstream`       |
| `args`      | N        | List    | The action inputs list                          |

For `gitstream` engine the action is specfied by: `name@version`

gitStream supported actions, see [documentation](23_gitstream-actions.md).

## Context variables

gitStream includes a collection of variables called contexts. These variables are intended 
for use with the [`checks`](#checks-section) condtions in the 
[gitStream automation file](#gitstream-automation-file).

- [`branch`](#branch-context)
    - [`name`](#name-context)
    - [`diff`](#branch-context)
        - [`size`](#branch-context)
        - [`files`](#branch-context)
- [`source`](#source-context)
    - [`diff`](#source-context)
        - [`files`](#source-context)
- [`pr`](#pr-context)
    - [`author`](#pr-context)
    - [`wip`](#pr-context)
    - [`name`](#pr-context)

#### branch context

| Values      | Type      | Description                                              |
| ------------|-----------|--------------------------------------------------------- |
| `branch`    | Map       | Includes the info related to the current branch          |
| `name`      | String    | The current branch, `feature-123-branch`                 |
| `diff`      | Map       | Includes the info compared to the default branch, `main` |
| `size`      | Integer   | The sum of line changed: addtions, edits and deletions   |
| `files`     | [String]  | List of changed files including their relative path      |

The branch context dosnt include any source code, but only related metadata.

#### source context

| Values      | Type      | Description                                                     |
| ------------|-----------|---------------------------------------------------------------- |
| `source`    | Map       | Includes the info related to source code                        |
| `diff`      | Map       | Includes the info compared to the default branch, `main`        |
| `files`     | [Map]     | List of changed files with their respective code changes        |

The source context include all code changes, it is not safe to share it with unknown services.

The files mapping includes a list of the following structure:

| Values          | Type      | Description                                                     |
| ----------------|-----------|---------------------------------------------------------------- |
| `original_file` | String    | The name of the file before the changes, including its path     |
| `new_file`      | String    | The name of the file after the changes, including its path      |
| `diff`          | String    | The content in diff format `+` for addtions, `-` for deletions  |

#### pr context

| Values      | Type      | Description                                                     |
| ------------|-----------|---------------------------------------------------------------- |
| `pr`        | Map       | Includes the PR info
| `author`    | String    | The git user name of the PR author
| `wip`       | Bool      | `true` when the PR is marked as draft 
| `name`      | String    | The PR name

## Filter functions

Filters are essentially functions that can be applied to variables. They are called with a pipe 
operator `|` and can take arguments. 

All Nunjucks filters are supported, see [documentation](https://mozilla.github.io/nunjucks/templating.html#builtin-filters).

gitStream adds more filters, see [documentation](21_gitstream-filters.md).

You can also add custom filters, see [documentation](22_custom-filters.md).
