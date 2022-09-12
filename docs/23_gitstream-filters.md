# Filter functions

Filters can change the look and format of the source data, or even generate new data derived 
from the input values. What's important is that the original data is replaced by the result 
of transformations and that's what ends up in rendered templates.

Filters for lists of strings:

List filters:

- [`extractExtensions`](#extensions-filter) - List of all unique file extensions from a list of file names.
- [`filterList`](#filter-filter) - List of items that match the search term from the input list.
- [`filterListRegex`](#filterRegex-filter) - List of items that match the regex from the input list.
- [`listIncludes`](#includes-filter) -  Return `true` if any of items in a list match the search term.
- [`listIncludesRegex`](#includesRegex-filter) -  Return `true` if any of items in a list match the regex term.
- [`stringIncludes`](#includesRegex-filter) -  Return `true` if any of items in a list match the regex term.
- [`stringIncludesRegex`](#includesRegex-filter) -  Return `true` if any of items in a list match the regex term.

File names checks:

- [`allExtensions`](#allExtensions-filter)- Return `true` if the input list includes only any of the specified extensions.
- [`allDocs`](#allDocs-filter) - Return `true` if the input list includes only documents based on file extensions.
- [`allImages`](#allImages-filter) - Return `true` if the input list includes only images based on file extensions.
- [`allTests`](#allTests-filter) - Return `true` if the input list includes only tests based on file's path and name.
- [`allFilesMatchRegex`](#allPassRegex-filter) - Return `true` if the all the items in the input list matches the regex term.

File diff filters, expects [`source.diff.files`](21_gitstream-context.md#source-context):

- [`filterFiles`](#filterFiles-filter) - List of file diffs that match the search term from the input file diff list.
- [`allLines`](#allLines-filter)
- [`allFormattingChange`](#allFormattingChange-filter) - Return `true` if all file diffs are validated as formatting changes.

Other:

- [`true`](#true-filter) - Return `true`

PR evaluation: 

- [`estimatedReviewTime`](#estimatedReviewTime-filter)



#### `allExtensions` filter

:octicons-tag-24: Minimal version: 1.0

Return `true` if the input list includes only any of the specified extensions.

Syntax: 
```
allExtensions(files, qualifingExtensions)
```

| Values   | Usage   | Type      | Description                                     |
| -------- | --------|-----------|------------------------------------------------ |
| `files`  | Input   | [String]  | The list of changed files with their path       |
| `qualifingExtensions` | Input | [String] | the list of desired extensions, like `py`, `js` |
| `result` | Output  | Bool | `true` if all file extensions are of one the qualifying extensions |

```yaml
checks:
  filetypes:
    is:
      configuration: {{ files | allExtensions(['json', 'toml']) }}
```

#### `allDocs` filter

:octicons-tag-24: Minimal version: 1.0

Return `true` if the input list includes only documents based on file extensions.

Syntax: 
```
allDocs(files)
```

| Values   | Usage    | Type      | Description                         |
| -------- | ---------|-----------|------------------------------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path       |
| `result` | Output   | Bool      | `true` if all file extensions are of docs       |

Doc files extensions are: `md`, `mkdown`, `txt`, `rst`.

```yaml
checks:
  filetypes:
    is:
      docs: {{ files | allDocs }}
```

#### `allImages` filter

:octicons-tag-24: Minimal version: 1.0

Return `true` if the input list includes only images based on file extensions.

Syntax: 
```
allImages(files)
```

| Values   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path       |
| `result` | Output   | Bool      | `true` if all file extensions are of images     |

Image file extensions are: `svg`, `png`, `gif`.

```yaml
checks:
  filetypes:
    is:
      images: {{ files | allImages }}
```

#### `allTests` filter

:octicons-tag-24: Minimal version: 1.0

Return `true` if the input list includes only tests based on file's path and name.

Syntax: 
```
allTests(files)
```

| Values | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| `files` | Input   | [String]  | The list of changed files with their path       |
| `result` | Output | Bool      | `true` if all file tests based on name and path |

Test files must include the substring `test` or `spec` in its name or path.

```yaml
checks:
  filetypes:
    is:
      tests: {{ files | allTests }}
```

#### `allPassRegex` filter

:octicons-tag-24: Minimal version: 1.0

Return `true` if the all the items in the input list matches the regex term.

Syntax: 
```
allPassRegex(files)
```

| Values   | Usage    | Type      | Description   |
| -------- | ---------|-----------|------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path      |
| `result` | Output   | Bool      | `true` if all file extensions are of images    |

Image file extensions are: `svg`, `png`, `gif`.

```yaml
checks:
  content:
    is:
      assets_only: {{ files | allPassRegex('.*\.png$|.*\.jpg$|.*\.svg$|.*\.css$') }}
```


#### `extensions` filter

:octicons-tag-24: Minimal version: 1.0

Expects `files` and provide a list of all unique file extensions.

Syntax: 
```
extensions(files)
```

| Values | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path       |
| `result` | Output   | [String]  | List of all unique file extensions              |

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

| Values   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| `branch-context` | Input  | Object    | gitStream generated [branch context variable](20_reference#branch-context) |
| `result`  | Output  | String    | the estimated time for review in minutes |

#### `filter` filter

:octicons-tag-24: Minimal version: 1.0

Expects a list and provide a new list with the items that match the search term.

Syntax: 
```
filter(items, searchTerm)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | [String]  | List of items                        |
| `searchTerm` | Input    | String    | Search term to look for       |
| `result`      | Output   | [String] | All items that match   |

Simple text filter:

```yaml
checks:
  filetypes:
    is:
     no_python: {{ files | filter('py') | length == 0 }}
```

#### `filterRegex` filter

:octicons-tag-24: Minimal version: 1.0

Expects a list and provide a new list with the items that match the regex term.

Syntax: 
```
filterRegex(items, regexExpression)
```

| Values        | Usage    | Type    | Description                                |
| ------------- | ---------|---------|------------------------------------------- |
| `items`       | Input  | [String]  | List of items                              |
| `regexExpression` | Input | String | Regex expression, `\.py$`              |
| `result`      | Output | [String]  | All items that match the regular expression   |

```yaml
checks:
  filetypes:
    is:
     no_python: {{ files | filterRegex('\.py$') | length == 0 }}
```

#### `includes` filter

:octicons-tag-24: Minimal version: 1.0

Expects a list and return `true` if any of items match search term.

Syntax: 
```
includes(items, searchTerm)
```

| Values       | Usage    | Type      | Description                             |
| -------------| ---------|-----------|---------------------------------------- |
| `items`      | Input    | [String]  | Text string      |
| `searchTerm` | Input    | String    | Search term to look for`                |
| `result`     | Output   | Bool      | `true` if the search element is found   |

```yaml
checks:
  filetypes:
    is:
     has_python: {{ files | includes('py') }}
```

#### `includesRegex` filter

:octicons-tag-24: Minimal version: 1.0

Expects a list and return `true` if any of items match the regex term.

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

:octicons-tag-24: Minimal version: 1.0

List of file diffs that match the search term from the input file diff list.

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

:octicons-tag-24: Minimal version: 1.0

Return `true` if all file diffs are validated as formatting changes.

Support source code languages: 
- JavaScript
- TypeScript
- Python 
- JSON
- YAML

If changes in other formats detected the filter will return `false`.

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
     reformatting: {{ source.diff.files | allFormattingChange }}

automations:
  allow_reformatting:
    if:
      - {{ checks.change.in.reformatting }}
    run:
      - action: approve@v1
```