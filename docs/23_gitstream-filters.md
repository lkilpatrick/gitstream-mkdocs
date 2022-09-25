# Filter functions

Filters can change the look and format of the source data, or even generate new data derived 
from the input values. What's important is that the original data is replaced by the result 
of transformations and that's what ends up in rendered templates.

Filters for lists of strings:

Item checks:

- [`isStringIncludes`](#isstringincludes-filter) -  Return `true` if any of items in a list match either of the search terms.
- [`isStringIncludesRegex`](#isstringincludesregex-filter) -  Return `true` if any of items in a list match the regex term.

List checks:

- [`isEveryInList`](#iseveryinlist-filter) - Return `true` if the all the items in the input list matches either of the search terms.
- [`isEveryInListRegex`](#iseveryinlistregex-filter) - Return `true` if the all the items in the input list matches the regex term.
- [`isSomeInList`](#issomeinlist-filter) -  Return `true` if any of items in a list match either of the search terms.
- [`isSomeInListRegex`](#issomeinlistregex-filter) -  Return `true` if any of items in a list match the regex term.

List operations:

- [`filterList`](#filterlist-filter) - List of items that match either of the search terms from the input list.
- [`filterListRegex`](#filterlistregex-filter) - List of items that match the regex from the input list.

File names checks:

- [`allDocs`](#alldocs-filter) - Return `true` if the input list includes only documents based on file extensions.
- [`allImages`](#allimages-filter) - Return `true` if the input list includes only images based on file extensions.
- [`allTests`](#alltests-filter) - Return `true` if the input list includes only tests based on file's path and name.
- [`isEveryExtension`](#iseveryextension-filter) - Return `true` if the input list includes only any of the specified extensions.
- [`isEveryExtensionRegex`](#iseveryextensionregex-filter)- Return `true` if the input list includes only the matching regex.

File names operators:

- [`extractExtensions`](#extractextensions-filter) - List of all unique file extensions from a list of file names.

[`FileDiff` ](21_gitstream-context.md#filediff-structure) checks:

- [`isFormattingChange`](#isformattingchange-filter) - Return `true` if all file diffs are validated as formatting changes. 
- [`isEveryLineInFileDiffRegex`](#iseverylineinfilediffregex-filter) - Return `true` if every changed line matches the regex.
- [`isSomeLineInFileDiffRegex`](#issomelineinfilediffregex-filter) - Return `true` if any changed line matches the regex.

[`FileDiff` ](21_gitstream-context.md#filediff-structure) operators:

- [`filterFileDiffRegex`](#filterfilediffregex-filter) - List of file diffs that match the regex from the input file diff list.

PR checks: 

- [`estimatedReviewTime`](#estimatedreviewtime-filter)

Other:

- [`true`](#true-filter) - Returns `true` always



#### `allDocs` filter

Return `true` if the input list includes only documents based on file extensions.

| Values   | Usage    | Type      | Description                         |
| -------- | ---------|-----------|------------------------------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path       |
| `result` | Output   | Bool      | `true` if all file extensions are of docs       |

Doc files extensions are: `md`, `mkdown`, `txt`, `rst`.

```yaml
{{ files | allDocs }}
```

#### `allImages` filter

Return `true` if the input list includes only images based on file extensions.

| Values   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path       |
| `result` | Output   | Bool      | `true` if all file extensions are of images     |

Image file extensions are: `svg`, `png`, `gif`.

```yaml
{{ files | allImages }}
```

#### `allTests` filter

Return `true` if the input list includes only tests based on file's path and name.

| Values | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| `files` | Input   | [String]  | The list of changed files with their path       |
| `result` | Output | Bool      | `true` if all file tests based on name and path |

Test files must include the substring `test` or `spec` in its name or path.

```yaml
{{ files | allTests }}
```

#### `estimatedReviewTime` filter

| Values   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| `branch-context` | Input  | Object    | gitStream generated [branch context variable](20_reference#branch-context) |
| `result`  | Output  | String    | the estimated time for review in minutes |

```yaml
{{ branch | estimatedReviewTime }}
```

#### `extractExtensions` filter

Expects `files` and provide a list of all unique file extensions.

| Values | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path       |
| `result` | Output   | [String]  | List of all unique file extensions              |

```yaml
{{ files | extractExtensions | length == 1 }}
```

#### `filterFileDiffRegex` filter

List of file diffs that match the search term from the input file diff list.

| Values        | Usage    | Type      | Description                                |
| --------------| ---------|--------|-----------------------------------------------|
| `files`       | Input    | [FileDiff]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `filterRegex` | Input    | String | Regex filter applied to the `new_file` field of files diffs |
| `result`  | Output | [FileDiff]  | List of matching file diffs           |

Reduce the FileDiff list and keep only those that added or edited a line with call to `console.log`:

```yaml
{{ source.diff.files | filterFileDiffRegex('^\+.*console\.log') }}
```

#### `filterList` filter

Expects a list and provide a new list with the items that match the search term.

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | [String]  | List of items                        |
| `searchTerms` | Input    | [String]    | Search terms list to look for       |
| `result`      | Output   | [String] | All items that match any of the search terms  |

```yaml
{{ files | filterList(['py']) | length == 0 }}
```

#### `filterListRegex` filter

Expects a list and provide a new list with the items that match the regex term.

| Values        | Usage    | Type    | Description                                |
| ------------- | ---------|---------|------------------------------------------- |
| `items`       | Input  | [String]  | List of items                              |
| `regexExpression` | Input | String | Regex expression, `\.py$`              |
| `result`      | Output | [String]  | All items that match the regular expression   |

```yaml
{{ files | filterListRegex('\.py$') | length == 0 }}
```


#### `isEveryExtension` filter

Return `true` if the input list includes only any of the specified extensions.

| Values   | Usage   | Type      | Description                                     |
| -------- | --------|-----------|------------------------------------------------ |
| `files`  | Input   | [String]  | The list of changed files with their path       |
| `qualifingExtensions` | Input | [String] | the list of desired extensions, like `py`, `js` |
| `result` | Output  | Bool | `true` if all file extensions are of one the qualifying extensions |

```yaml
{{ files | isEveryExtension(['json', 'toml']) }}
```


#### `isEveryExtensionRegex` filter

Return `true` if the input list includes only any of the specified extensions.

| Values   | Usage   | Type      | Description                                     |
| -------- | --------|-----------|------------------------------------------------ |
| `files`  | Input   | [String]  | The list of changed files with their path       |
| `qualifingExtensions` | Input | String | Regex extensions, like `\.py$` |
| `result` | Output  | Bool | `true` if all file extensions are of one the qualifying extensions |

```yaml
{{ files | isEveryExtensionRegex('json|toml') }}
```


#### `isEveryInList` filter

Return `true` if the all the items in the input list matches the regex term.

| Values   | Usage    | Type      | Description   |
| -------- | ---------|-----------|------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path      |
| `searchTerms`  | Input    | [String]  | List of search terms to match      |
| `result` | Output   | Bool      | `true` if all files in list match any of the search terms   |


```yaml
{{ files | isEveryInList(['png', 'jpg', 'svg']) }}
```


#### `isEveryInListRegex` filter

Return `true` if the all the items in the input list matches the regex term.

| Values   | Usage    | Type      | Description   |
| -------- | ---------|-----------|------------------------ |
| `files`  | Input    | [String]  | The list of changed files with their path      |
| `regexTerm`  | Input    | [String]  | Regex search term to match      |
| `result` | Output   | Bool      | `true` if all files in list match regex terms   |

```yaml
{{ files | isEveryInListRegex('.*\.png$|.*\.jpg$|.*\.svg$') }}
```

#### `isEveryLineInFileDiffRegex` filter

| Values       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| `filediffs`      | Input    | [Map]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `matchRegex` | Input    | String | Regex filter applied to the `diff` field of files diffs  |
| `result`     | Output   | Bool   | `true` if the all lines in diffs match the regex |

```yaml
{{ source.diff.files | isEveryLineInFileDiffRegex('logger\.(debug|info|warn|error)') }}
```

#### `isFormattingChange` filter

Return `true` if all file diffs are validated as formatting changes.

Support source code languages: JavaScript, TypeScript, JSON, YAML and HTML.

If changes in other formats detected, the filter will return `false`.

| Values       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| `filediffs`      | Input    | [Map]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `result`     | Output   | Bool   | `true` if the all code changes are non functional |

```yaml
{{ source.diff.files | isFormattingChange }}
```

#### `isSomeInList` filter

Expects a list and return `true` if any of items match search term.

| Values       | Usage    | Type      | Description                             |
| -------------| ---------|-----------|---------------------------------------- |
| `items`      | Input    | [String]  | Text string      |
| `searchTerms` | Input   | [String]    | Search terms list to look for`                |
| `result`     | Output   | Bool      | `true` if the any of the search terms are found   |

```yaml
{{ files | isSomeInList(['py']) }}
```

#### `isSomeInListRegex` filter

Expects a list and return `true` if any of items match the regex term.

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|-------------------------------------------|
| `items`       | Input    | [String]  | List of items                             |
| `regexExpression` | Input    | String    | Regex expression to search for, `\.py$` |
| `result`        | Output   | Bool      | `true` if a matching element is found   |

```yaml
{{ files | isSomeInListRegex('\.py$') }}
```

#### `isSomeLineInFileDiffRegex` filter

| Values       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| `filediffs`      | Input    | [Map]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `matchRegex` | Input    | String | Regex filter applied to the `diff` field of files diffs  |
| `result`     | Output   | Bool   | `true` if any of the lines in diffs match the regex |

```yaml
{{ source.diff.files | isSomeLineInFileDiffRegex('oldAPIcall') }}
```


#### `isStringIncludes` filter

Expects a list and provide a new list with the items that match the search term.

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | String  | Items                        |
| `searchTerms` | Input    | [String]    | Search terms to look for       |
| `result`      | Output   | Bool | `true` if any of the search terms matches   |

```yaml
{{ "something" | isStringIncludes(["thing"]) }}
```

#### `isStringIncludesRegex` filter

Expects a list and provide a new list with the items that match the search term.

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | String  | Items                        |
| `searchTerms` | Input    | [String]    | Regex term to look for       |
| `result`      | Output   | Bool | `true` if the regex term match   |

```yaml
{{ "something" | isStringIncludesRegex(".*ing") }}
```


#### `true` filter

Returns `true`

```yaml
{{ true }}
```

