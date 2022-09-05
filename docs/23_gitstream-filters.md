# gitStream built-in Filter functions

Filters can change the look and format of the source data, or even generate new data derived 
from the input values. What's important is that the original data is replaced by the result 
of transformations and that's what ends up in rendered templates.

Filters for lists of strings:

- [`allExtensions`](#allExtensions-filter)
- [`allDocs`](#allDocs-filter)
- [`allImages`](#allImages-filter)
- [`allTests`](#allTests-filter)
- [`allPassRegex`](#allPassRegex-filter)
- [`extensions`](#extensions-filter)
- [`filter`](#filter-filter)
- [`filterRegex`](#filterRegex-filter)
- [`includes`](#includes-filter)
- [`includesRegex`](#includesRegex-filter)

Specialized filters:

- [`true`](#true-filter)
- [`estimatedReviewTime`](#estimatedReviewTime-filter)

Filters for file diffs:

- [`filterFiles`](#filterFiles-filter)
- [`allLines`](#allLines-filter)
- [`allFormattingChange`](#allFormattingChange-filter)

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
| `result`               | Output  | Bool      | `true` if all file extensions are of one the qualifying extensions |

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

#### `allPassRegex` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
allPassRegex(files)
```

| Values                 | Usage    | Type      | Description                                     |
| ---------------------- | ---------|-----------|------------------------------------------------ |
| `files`                | Input    | [String]  | The list of changed files with their path       |
| `result`               | Output   | Bool      | `true` if all file extensions are of images     |

Image file extensions are: `svg`, `png`, `gif`.

```yaml
checks:
  content:
    is:
      assets_only: {{ files | allPassRegex('.*\.png$|.*\.jpg$|.*\.svg$|.*\.css$') }}
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

#### `filter` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
filter(items, searchTerm)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | [String]  | List of items                        |
| `searchTerm` | Input    | String    | Search term to look for       |
| `result`      | Output   | [String] | All items that match the regex expression   |

Simple text filter:

```yaml
checks:
  filetypes:
    is:
     no_python: {{ files | filter('py') | length == 0 }}
```

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
| `result`      | Output   | [String]      | All items that match the regex expression   |

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
includes(items, searchTerm)
```

| Values          | Usage    | Type      | Description                                     |
| ----------------| ---------|-----------|------------------------------------------------ |
| `items`         | Input    | [String]    | Text string                                     |
| `searchTerm` | Input    | String    | Search term to look for`                      |
| `result`        | Output   | Bool      | `true` if the search element is found           |

```yaml
checks:
  filetypes:
    is:
     has_python: {{ files | includes('py') }}
```

#### `includesRegex` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
includesRegex(items, regexExpression)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|-------------------------------------------|
| `items`       | Input    | [String]  | List of items                             |
| `regexExpression` | Input    | String    | Regex expression to search for, `\.py$` |
| `result`        | Output   | Bool      | `true` if a matching element is found   |

```yaml
checks:
  filetypes:
    is:
     has_python: {{ files | includesRegex('\.py$') }}
```

#### `true` filter

:octicons-tag-24: Minimal version: 1.0

Syntax: 
```
true()
```

Returns `true`

```yaml
automations:
  add_ready_comment:
    if:
      - {{ true() }}
    run:
      - action: add_comment@v1
        comment: ready
```

#### `filterFiles` filter

:octicons-beaker-24: Coming soon

Syntax: 
```
filterFiles(files, filterRegex)
```

| Values        | Usage    | Type      | Description                                |
| --------------| ---------|--------|-----------------------------------------------|
| `files`       | Input    | [Map]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `filterRegex` | Input    | String | Regex filter applied to the `new_file` field of files diffs |
| `result`  | Output | [Map]  | List of matching file diffs           |

#### `allLines` filter

:octicons-beaker-24: Coming soon

Syntax: 
```
allLines(diffs, matchRegex)
```

| Values       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| `files`      | Input    | [Map]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `matchRegex` | Input    | String | Regex filter applied to the `diff` field of files diffs  |
| `result`     | Output   | Bool   | `true` if the all lines in diffs match the regex |

```yaml
checks:
  filetype:
    all:
     python: {{ source.diff.files | allExtensions(['.py']) }}
     javascript: {{ source.diff.files | allExtensions(['js'])}}

  only_logs:
    in:
     python: {{ source.diff.files | filterFiles('\.py$') | allLines('logger\.(debug|info|warn|error)') }}
     javascript: {{ source.diff.files | filterFiles('\.js$') | allLines('console\.log') }}

automations:
  allow_py_logging_changes:
    if:
      - {{ checks.filetype.all.python }}
      - {{ checks.only_logs.in.python }}
    run:
      - action: approve@v1
  allow_js_logging_changes:
    if:
      - {{ checks.filetype.all.javascript }}
      - {{ checks.only_logs.in.javascript }}
    run:
      - action: approve@v1
```


#### `allFormattingChange` filter

:octicons-beaker-24: Coming soon

Support source code languages: 
- JavaScript
- TypeScript
- Python 

Syntax: 
```
allFormattingChange(diffs)
```

| Values       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| `files`      | Input    | [Map]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `result`     | Output   | Bool   | `true` if the all code changes are non functional |

```yaml
checks:
  change:
    is:
     reformatting: {{ source.diff.files | allFormattingChange() }}

automations:
  allow_reformatting:
    if:
      - {{ checks.change.in.reformatting }}
    run:
      - action: approve@v1
```