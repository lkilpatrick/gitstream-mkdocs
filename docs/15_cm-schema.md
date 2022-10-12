# Overview gitstream.cm

This document is a reference for the gitStream 1.x, used in .cm/gitstream.cm file.

!!! note 

    You can edit the `.cm/gitstream.cm` and add your own checks and automations. Check out the [examples](18_examples.md) 

## Context variables

gitStream includes a collection of variables called contexts.

gitStream built-in context variables are described [here](21_gitstream-context.md).

## Filter functions

Filters are essentially functions that can be applied to variables. They are called with a pipe operator `|` and can take arguments. The logic expressions are based on Jinja2 syntax, supported by Nunjucks lib.

All Nunjucks filters are supported, see [documentation](https://mozilla.github.io/nunjucks/templating.html#builtin-filters).

gitStream built-in filter functions are described [here](23_gitstream-filters.md).

??? tip "Coming soon"

	You can also add custom filters, see [instructions](28_custom-filters.md).

## Automation actions

Specify the desired automations that are triggered when all conditions are met, read more [here](25_gitstream-actions.md).

The conditions are evaluated on new Pull Requests or changes to the Pull Request. 

PRs that are marked as Draft are ignored by default, you can change that, see [`config`](#config) .

## Schema

The following sections are used in `.cm` file to describe the desired automations:

- [`manifest`](#manifest)
- [`config`](#config)
- [`automations`](#automations)

### `manifest`

The first section in a `gitstream.cm` file is the `manifest`.

```yaml+jinja
manifest: 
  version: 1.0
```

The only field required is `version`.

| Key         | Required | Type    | Description                              |
| ----------- | ---------|---------|----------------------------------------- |
| `manifest`         | Y        | Map     | The manifest section root                |
| `manifest.version` | Y        | String  | Specify the `.cm` spec version: 0.1, 1.0 |

The manifest version field is used to parse the `.cm` file, in the future if breaking changes are introduced to the parser then older automation will be still supported.

### `config`

??? tip "Coming soon"

    The `config` section is optional in `gitstream.cm` file and is used to specify configuration for the way gitStream works.


    | Key         | Required | Type    |  Default | Description                              |
    | ----------- | ---------|---------|----------|------------------------------ |
    | `config`              | N | Map   | - | The config section root                |
    | `config.ignore_files` | N | [String]   | - | Specifies files that will not be included in the context variables |

    `config.ignore_files` supports glob pattern matching that contains list of files to ignore, for example:

    ```yaml+jinja
    config:
      ignore_files:
        - yarn.lock
        - package-lock.json
        - openapi.json
        - ui/src/**/*Model.d.ts
    ```

### `automations` 

The `automations` section defines the automations and their conditions. 

```yaml+jinja
automations:
  mark_small_pr:
    if:
      - {{ checks.size.is.xsmall }}
    run:
      - action: add-label@v1
        args:
          label: xsmall
```

Each automation includes its name, and few fields: `if` and `run`.

| Key        | Required  | Type    | Description                                     |
|------------|-----------|---------|------------------------------------------------ |
| `automations`  | Y        | Map     | The automations section root     |
| `automations.NAME`     | Y | Map | User defined name of the automation, can be any string       |
| `automations.NAME.if`  | Y | Map | List of conditions                               |
| `automations.NAME.run` | Y | Map | The automation to run if all conditions are met |

The `if` field includes the list of conditions. The conditions are checked when a pull request 
is opened or changed, if all the conditions pass, the automation is executed.

The `run` field includes the automation to execute. It includes the following fields:

| Key         | Required | Type    | Description                                     |
| ----------- | ---------|---------|------------------------------------------------ |
| `action`    | Y        | String  | The action pointer                              |
| `engine`    | N        | String  | The action engine, default is `gitstream`       |
| `args`      | N        | List    | The action inputs list                          |

For `gitstream` engine, the action is specified by: `name@version`

gitStream supported actions, see [actions](25_gitstream-actions.md).

### Reusing checks

You can define an accessory section, e.g. `checks`, that defines common conditions, and resue.  

```yaml+jinja
size:
  is:
    small: {{ branch.diff.size < 20 }}
    medium: {{ branch.diff.size >= 20 and branch.diff.size < 100 }}
    large: {{ branch.diff.size >= 100 }}
automations:
  approve_small:
    if:
      - {{ size.is.small }}
    run:
      - action: approve@v1
  mark_small_medium:
    if:
      - {{ size.is.small or size.is.medium }}
    run:
      - action: add-label@v1
        args:
          label: 'good-size'

```