# Configuring gitStream automation

This document is a reference for the gitStream 1.x, used in .cm/gitstream.cm file.

## Schema

- [`manifest`](#manifest-section)
    - [`version`](#version-field)
- [`checks`](#checks-section)
- [`automations`](#automations-section)
    - [`if`](#if-field)
    - [`run`](#run-field)

#### manifest section

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

#### checks section

The `checks` section defines conditions that are nested under the `checks` section.  

```yaml
checks:
  size:
    is:
      xsmall: {{ branch.diff.size < 20 }}
```

Each condition includes a logic expresison that eventually results with a boolean value: `true` or `false`. 

The logic expression are based on Jinja2 syntax, and includes gitStream context varaibales.

#### automations section

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

#### if field

The `if` field includes the list of conditions. The condtions are checked when a pull request is opened or changed, if all the conditions pass, the automation is executed.

#### run field

The `run` field includes the automation to execute. 

| Key         | Required | Type    | Description                                     |
| ----------- | ---------|---------|------------------------------------------------ |
| `action`    | Y        | String  | The action pointer                              |
| `engine`    | N        | String  | The action engine, default is `gitstream`       |
| `args`      | N        | Map     | The action inputs                               |

For `gitstream` engine the action is specfied by: `name@version`

## Filter functions

Filters are essentially functions that can be applied to variables. They are called with a pipe 
operator `|` and can take arguments. All Nunjucks filters are supported, as well as gitStream 
built-in filters. You can also add custom filters byt editing the .cm/filters.js file.

For example, the following filter checks that all changed files are docs:
```yaml
{{ files | allDocs }}
```

- [`allExtensions`](#allExtensions-filter)
- [`allDocs`](#allDocs-filter)
- [`allImages`](#allImages-filter)
- [`allTests`](#allTests-filter)
- [`extensions`](#extensions-filter)
- [`estimatedReviewTime`](#estimatedReviewTime-filter)
- [`regex`](#regex-filter)
- [`grep`](#grep-filter)

#### allExtensions filter

Syntax: 
```
allExtensions(files, qualifingExtensions)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `qualifingExtensions`  | Input    | [String]  | the list of desired extensions, like `py`, `js` |
| `result`               | Output   | Bool      | `true` if all file extensions are of one the qualifing extensions |

#### allDocs filter

Syntax: 
```
allDocs(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | Bool      | `true` if all file extensions are of docs       |

Doc files extensions are: `md`, `mkdown`, `txt`, `rst`.

#### allImages filter

Syntax: 
```
allImages(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | Bool      | `true` if all file extensions are of images     |

Image file extensions are: `svg`, `png`, `gif`.

#### allTests filter

Syntax: 
```
allTests(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | Bool      | `true` if all file tests based on name and path |

Test files must include the substring `test` or `spec` in its name or path.

#### extensions filter

Syntax: 
```
extensions(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | [String]  | list of all file extensions                     |

#### estimatedReviewTime filter

Syntax: 
```
estimatedReviewTime(branch)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `branch-context`       | Input    | Object    | The gitstream generated [branch context varaible](#branch-context) |
| `result`               | Output   | String    | the estimated time for review in minutes |

Test files must include the substring `test` or `spec` in its name or path.

#### regex filter

Syntax: 
```
regex(items, regexFilter)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|------------------------------------------------ |
| `items`       | Input    | [String]  | List of items                                   |
| `regexFilter` | Input    | String    | Regex filter, `\.py$`                           |
| `result`      | Output   | Bool      | `true` if all items match the regex filter      |

#### grep filter

Syntax: 
```
grep(string, searchTerm)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|------------------------------------------------ |
| `items`       | Input    | String    | Text string                                     |
| `searchTerm`  | Input    | String    | Filter string                                   |
| `result`      | Output   | Bool      | `true` if the search term is found              |

## Context variables

gitStream includes a collection of variables called contexts. These variables are intended 
for use with the [`checks`](#checks-section) condtions in the 
[gitStream automation file](#gitstream-automation-file).

- [`branch`](#branch-context)
    - [`diff`](#branch-diff-context)
        - [`size`](#branch-diff-size-context)
- [`files`](#files-context)
- [`source`](#source-context)
    - [`diff`](#source-diff-context)
        - [`content`](#source-diff-content-context)
