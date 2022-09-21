# Filter functions

Filters can change the look and format of the source data, or even generate new data derived 
from the input values. What's important is that the original data is replaced by the result 
of transformations and that's what ends up in rendered templates.

Filters for lists of strings:

Item checks:

- [`isStringIncludes`](#isStringIncludes-filter) -  Return `true` if any of items in a list match either of the search terms.
- [`isStringIncludesRegex`](#isStringIncludesRegex-filter) -  Return `true` if any of items in a list match the regex term.

List checks:

- [`isEveryInList`](#isEveryInList-filter) - Return `true` if the all the items in the input list matches either of the search terms.
- [`isEveryInListRegex`](#isEveryInListRegex-filter) - Return `true` if the all the items in the input list matches the regex term.
- [`isSomeInList`](#isSomeInList-filter) -  Return `true` if any of items in a list match either of the search terms.
- [`isSomeInListRegex`](#isSomeInListRegex-filter) -  Return `true` if any of items in a list match the regex term.

List operations:

- [`filterList`](#filterList-filter) - List of items that match either of the search terms from the input list.
- [`filterListRegex`](#filterListRegex-filter) - List of items that match the regex from the input list.

File names checks:

- [`allDocs`](#allDocs-filter) - Return `true` if the input list includes only documents based on file extensions.
- [`allImages`](#allImages-filter) - Return `true` if the input list includes only images based on file extensions.
- [`allTests`](#allTests-filter) - Return `true` if the input list includes only tests based on file's path and name.
- [`isEveryExtension`](#isEveryExtension-filter) - Return `true` if the input list includes only any of the specified extensions.
- [`isEveryExtensionRegex`](#isEveryExtensionRegex-filter)- Return `true` if the input list includes only the matching regex.

File names operators:

- [`extractExtensions`](#extractExtensions-filter) - List of all unique file extensions from a list of file names.

[`FileDiff` ](21_gitstream-context.md#file-diff-structure) checks:

- [`isFormattingChange`](#isFormattingChange-filter) - Return `true` if all file diffs are validated as formatting changes. 

PR checks: 

- [`estimatedReviewTime`](#estimatedReviewTime-filter)

Other:

- [`true`](#true-filter) - Returns `true` always



#### `allDocs` filter

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

#### `estimatedReviewTime` filter

Syntax: 
```
estimatedReviewTime(branch)
```

| Values   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| `branch-context` | Input  | Object    | gitStream generated [branch context variable](20_reference#branch-context) |
| `result`  | Output  | String    | the estimated time for review in minutes |

#### `extractExtensions` filter

Expects `files` and provide a list of all unique file extensions.

Syntax: 
```
extractExtensions(files)
```

| Values | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path       |
| `result` | Output   | [String]  | List of all unique file extensions              |

```yaml
checks:
  filetypes:
    is:
      single_type: {{ files | extractExtensions | length == 1 }}
```


#### `filterList` filter

Expects a list and provide a new list with the items that match the search term.

Syntax: 
```
filterList(items, searchTerms)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | [String]  | List of items                        |
| `searchTerms` | Input    | [String]    | Search terms list to look for       |
| `result`      | Output   | [String] | All items that match any of the search terms  |

Simple text filter:

```yaml
checks:
  filetypes:
    is:
     no_python: {{ files | filterList(['py']) | length == 0 }}
```

#### `filterListRegex` filter

Expects a list and provide a new list with the items that match the regex term.

Syntax: 
```
filterListRegex(items, regexExpression)
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
     no_python: {{ files | filterListRegex('\.py$') | length == 0 }}
```


#### `isEveryExtension` filter

Return `true` if the input list includes only any of the specified extensions.

Syntax: 
```
isEveryExtension(files, qualifingExtensions)
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
      configuration: {{ files | isEveryExtension(['json', 'toml']) }}
```



#### `isEveryExtensionRegex` filter

Return `true` if the input list includes only any of the specified extensions.

Syntax: 
```
isEveryExtensionRegex(files, qualifingExtensions)
```

| Values   | Usage   | Type      | Description                                     |
| -------- | --------|-----------|------------------------------------------------ |
| `files`  | Input   | [String]  | The list of changed files with their path       |
| `qualifingExtensions` | Input | String | Regex extensions, like `\.py$` |
| `result` | Output  | Bool | `true` if all file extensions are of one the qualifying extensions |

```yaml
checks:
  filetypes:
    is:
      configuration: {{ files | isEveryExtensionRegex('json|toml') }}
```


#### `isEveryInList` filter

Return `true` if the all the items in the input list matches the regex term.

Syntax: 
```
isEveryInList(files, searchTerms)
```

| Values   | Usage    | Type      | Description   |
| -------- | ---------|-----------|------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path      |
| `searchTerms`  | Input    | [String]  | List of search terms to match      |
| `result` | Output   | Bool      | `true` if all files in list match any of the search terms   |


```yaml
checks:
  content:
    is:
      assets_only: {{ files | isEveryInList(['png', 'jpg', 'svg']) }}
```


#### `isEveryInListRegex` filter

Return `true` if the all the items in the input list matches the regex term.

Syntax: 
```
isEveryInListRegex(files, regexTerms)
```

| Values   | Usage    | Type      | Description   |
| -------- | ---------|-----------|------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path      |
| `regexTerm`  | Input    | [String]  | Regex search term to match      |
| `result` | Output   | Bool      | `true` if all files in list match regex terms   |

```yaml
checks:
  content:
    is:
      assets_only: {{ files | isEveryInListRegex('.*\.png$|.*\.jpg$|.*\.svg$') }}
```


#### `isFormattingChange` filter

:octicons-tag-24: Minimal version: 1.0

Return `true` if all file diffs are validated as formatting changes.

Support source code languages: 

- JavaScript
- TypeScript
- JSON
- YAML
- HTML

If changes in other formats detected, the filter will return `false`.

Syntax: 
```
isFormattingChange(filediffs)
```

| Values       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| `filediffs`      | Input    | [Map]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `result`     | Output   | Bool   | `true` if the all code changes are non functional |

```yaml
checks:
  change:
    is:
     reformatting: {{ source.diff.files | isFormattingChange }}

automations:
  allow_reformatting:
    if:
      - {{ checks.change.in.reformatting }}
    run:
      - action: approve@v1
```

#### `isSomeInList` filter

Expects a list and return `true` if any of items match search term.

Syntax: 
```
isSomeInList(items, searchTerms)
```

| Values       | Usage    | Type      | Description                             |
| -------------| ---------|-----------|---------------------------------------- |
| `items`      | Input    | [String]  | Text string      |
| `searchTerms` | Input   | [String]    | Search terms list to look for`                |
| `result`     | Output   | Bool      | `true` if the any of the search terms are found   |

```yaml
checks:
  filetypes:
    is:
     has_python: {{ files | isSomeInList(['py']) }}
```

#### `isSomeInListRegex` filter

Expects a list and return `true` if any of items match the regex term.

Syntax: 
```
isSomeInListRegex(items, regexExpression)
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
     has_python: {{ files | isSomeInListRegex('\.py$') }}
```


#### `isStringIncludes` filter

Expects a list and provide a new list with the items that match the search term.

Syntax: 
```
isStringIncludes(item, searchTerms)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | String  | Items                        |
| `searchTerms` | Input    | [String]    | Search terms to look for       |
| `result`      | Output   | Bool | `true` if any of the search terms matches   |

Simple text filter:

```yaml
checks:
  thing: {{ "something" | isStringIncludes(["thing"]) }}
```

#### `isStringIncludesRegex` filter

Expects a list and provide a new list with the items that match the search term.

Syntax: 
```
isStringIncludesRegex(item, searchTerms)
```

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | String  | Items                        |
| `searchTerms` | Input    | [String]    | Regex term to look for       |
| `result`      | Output   | Bool | `true` if the regex term match   |

Simple text filter:

```yaml
checks:
  thing: {{ "something" | isStringIncludesRegex(".*ing") }}
```


#### `true` filter

Syntax: 
```
true()
```

Returns `true`

```yaml
automations:
  add_ready_comment:
    if:
      - {{ true }}
    run:
      - action: add-comment@v1
        comment: ready
```

