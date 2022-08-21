# Configuring gitStream automation

This document is a reference for the gitStream 1.x, used in .cm/gitstream.cm file.

## Schema

The following sections are used in `.cm` file to describe the desired automations:

- [`manifest`](#manifest-section)
    - [`version`](#manifest-section)
- [`checks`](#checks-section)
- [`automations`](#automations-section)
    - [`if`](#automations-section)
    - [`run`](#automations-section)

### `manifest` section

:octicons-tag-24: Minimal version: 1.0

The first section in a gitstream.cm is `manifest`.

```yaml
manifest: 
  version: 1.0
```

The only field required is `version`.

| Key         | Required | Type    | Description                              |
| ----------- | ---------|---------|----------------------------------------- |
| `manifest`         | Y        | Map     | The manifest section root                |
| `manifest.version` | Y        | String  | Specify the `.cm` spec version: 0.1, 1.0 |

The manifest fields is used a version to parse the `.cm` file, so if breaking chnages are 
introdcued to the parser - older automation are still supported.

### `checks` section

:octicons-tag-24: Minimal version: 1.0

The `checks` section defines conditions that are nested under the `checks` section.  

```yaml
checks:
  size:
    is:
      xsmall: {{ branch.diff.size < 20 }}
```

Each condition includes a logic expresison that eventually results with a boolean value: `true` or `false`. 

The logic expression are based on Jinja2 syntax, and includes gitStream context varaibales.


### `automations` section

:octicons-tag-24: Minimal version: 1.0

The `automations` section defines the automations and their conditions. 

```yaml
automations:
  mark_small_pr:
    if:
      - {{ checks.size.is.xsmall }}
    run:
      - action: add-label@v1
        args:
          - label: xsmall
```

| Key            | Required | Type    | Description                                     |
|----------------|----------|---------|------------------------------------------------ |
| `automations`  | Y        | Map     | The automations section root     |

Each automation includes its name, and few fields: `if` and `run`.

| Key        | Required  | Type    | Description                                     |
|------------|-----------|---------|------------------------------------------------ |
| `NAME`     | Y | Map | User defined name of the automation, can be any string       |
| `NAME.if`  | Y | Map | List of condtions                               |
| `NAME.run` | Y | Map | The automation to run if all conditions are met |

The `if` field includes the list of conditions. The condtions are checked when a pull request 
is opened or changed, if all the conditions pass, the automation is executed.

The `run` field includes the automation to execute. It includes the following fields:

| Key         | Required | Type    | Description                                     |
| ----------- | ---------|---------|------------------------------------------------ |
| `action`    | Y        | String  | The action pointer                              |
| `engine`    | N        | String  | The action engine, default is `gitstream`       |
| `args`      | N        | List    | The action inputs list                          |

For `gitstream` engine the action is specfied by: `name@version`

gitStream supported actions, see [documentation](25_gitstream-actions.md).

## Context variables

gitStream includes a collection of variables called contexts, these variables are intended 
for use with the [`checks`](#checks-section) condtions.

gitStream built-in context varaibales are described [here](21_gitstream-context.md).

## Filter functions

Filters are essentially functions that can be applied to variables. They are called with a pipe 
operator `|` and can take arguments. 

All Nunjucks filters are supported, see [documentation](https://mozilla.github.io/nunjucks/templating.html#builtin-filters).

gitStream built-in filter functions are described [here](23_gitstream-filters.md).

You can also add custom filters, see [instructions](24_custom-filters.md).
