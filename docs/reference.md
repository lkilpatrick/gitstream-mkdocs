# Configuring gitStream automation

## gitStream automation file

This document is a reference for the gitStream 1.x, used in .cm/gitstream.cm file.

The file fomrat is based on Yaml with support for Jinja2 rendering using Nunjucks engine.

### Schema

- [`manifest`](#the-manifest-section)
    - [`version`](#the-version-field)
- [`checks`](#the-checks-section)
- [`automations`](#the-automations-section)
    - [`if`](#the-if-field)
    - [`run`](#the-run-field)

#### The `manifest` section

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

#### The `checks` section

The `checks` section defines conditions that are nested under the `checks` section.  

```yaml
checks:
  size:
    is:
      xsmall: {{ branch.diff.size < 20 }}
```

Each condition includes a logic expresison that eventually results with a boolean value: `true` or `false`. 

The logic expression are based on Jinja2 syntax, and includes gitStream context varaibales.

#### The `automations` section

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

| Key         | Required | Type    | Description                                     |
| ----------- | ---------|---------|------------------------------------------------ |
| `if`        | Y        | Map     | List of condtions                               |
| `run`       | Y        | Map     | The automation to run if all conditions are met |

#### The `if` field

The `if` field includes the list of conditions. The condtions are checked when a pull request is opened or changed, if all the conditions pass, the automation is executed.

#### The `run` field

The `run` field includes the automation to execute. 

| Key         | Required | Type    | Description                                     |
| ----------- | ---------|---------|------------------------------------------------ |
| `action`    | Y        | String  | The action pointer                              |
| `engine`    | N        | String  | The action engine, default is `gitstream`       |
| `args`      | N        | Map     | The action inputs                               |

For `gitstream` engine the action is specfied by: `name@version`

### Filter functions

Filters are essentially functions that can be applied to variables. They are called with a pipe 
operator `|` and can take arguments. All Nunjucks filters are supported, as well as gitStream 
built-in filters. You can also add custom filters byt editing the .cm/filters.js file.

- [`allExtensions`](#the-allExtensions-filter)
- [`allDocs`](#the-allDocs-filter)
- [`allImages`](#the-allImages-filter)
- [`extensions`](#the-extensions-filter)
- [`allTests`](#the-allTests-filter)
- [`estimatedReviewTime`](#the-estimatedReviewTime-filter)
- [`regex`](#the-regex-filter)
- [`grep`](#the-grep-filter)

### Context varaibales

gitStream includes a collection of variables called contexts. These variables are intended 
for use with the [`checks`](#the-checks-section) condtions in the 
[gitStream automation file](#gitstream-automation-file).

- [`branch`](#the-branch-context)
    - [`diff`](#the-branch-diff-context)
        - [`size`](#the-branch-diff-size-context)
- [`files`](#the-files-context)
- [`source`](#the-source-context)
    - [`diff`](#the-source-diff-context)
        - [`content`](#the-source-diff-content-context)
