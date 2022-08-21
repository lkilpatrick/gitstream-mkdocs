# gitStream built-in Filter functions

Filters can change the look and format of the source data, or even generate new data derived 
from the input values. What's important is that the original data is replaced by the result 
of transformations and that's what ends up in rendered templates.

- [`allExtensions`](#allExtensions-filter)
- [`allDocs`](#allDocs-filter)
- [`allImages`](#allImages-filter)
- [`allTests`](#allTests-filter)
- [`extensions`](#extensions-filter)
- [`estimatedReviewTime`](#estimatedReviewTime-filter)
- [`filterRegex`](#filterRegex-filter)
- [`includes`](#includes-filter)
- [`grep`](#grep-filter)

#### `allExtensions` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
allExtensions(files, qualifingExtensions)
```

| Values                 | Usage   | Type      | Description                                     |
| ---------------------- | --------|-----------|------------------------------------------------ |
| `files`                | Input   | [String]  | The list of changed files with their path       |
| `qualifingExtensions`  | Input   | [String]  | the list of desired extensions, like `py`, `js` |
| `result`               | Output  | Bool      | `true` if all file extensions are of one the qualifing extensions |

```yaml
checks:
  filetypes:
    is:
      configuration: {{ files | allExtensions(['json', 'toml']) }}
```

#### `allDocs` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
allDocs(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | Bool      | `true` if all file extensions are of docs       |

Doc files extensions are: `md`, `mkdown`, `txt`, `rst`.

```yaml
checks:
  filetypes:
    is:
      docs: {{ files | allDocs }}
```

#### `allImages` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
allImages(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | Bool      | `true` if all file extensions are of images     |

Image file extensions are: `svg`, `png`, `gif`.

```yaml
checks:
  filetypes:
    is:
      images: {{ files | allImages }}
```

#### `allTests` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
allTests(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | Bool      | `true` if all file tests based on name and path |

Test files must include the substring `test` or `spec` in its name or path.

```yaml
checks:
  filetypes:
    is:
      tests: {{ files | allTests }}
```

#### `extensions` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
extensions(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | [String]  | list of all unique file extensions              |

```yaml
checks:
  filetypes:
    is:
      single_type: {{ files | extensions | length == 1 }}
```

#### `estimatedReviewTime` filter

:octicons-beaker-24: Coming soon

Syntax: 
```
estimatedReviewTime(branch)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `branch-context`       | Input    | Object    | gitStream generated [branch context varaible](20_reference#branch-context) |
| `result`               | Output   | String    | the estimated time for review in minutes |

#### `filterRegex` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
filterRegex(items, regexExpression)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|------------------------------------------------ |
| `items`       | Input    | [String]  | List of items                                   |
| `regexExpression` | Input    | String    | Regex expression, `\.py$`                           |
| `result`      | Output   | [String]      | All items match that match the regex expression      |

```yaml
checks:
  filetypes:
    is:
     no_python: {{ files | filterRegex('\.py$') | length == 0 }}
```

#### `includes` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
includes(string, searchElement)
```

| Values          | Usage    | Type      | Description                                     |
| ----------------| ---------|-----------|------------------------------------------------ |
| `items`         | Input    | String    | Text string                                     |
| `searchElement` | Input    | String    | The value to search for                         |
| `result`        | Output   | Bool      | `true` if the search element is found           |