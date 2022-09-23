# Filter functions

Filters can change the look and format of the source data, or even generate new data derived 
from the input values. What's important is that the original data is replaced by the result 
of transformations and that's what ends up in rendered templates.

<div class="big-summary" markdown=1>

| Function | Input | Args | Output |
| --------------- | ------- | ---- |  ---- |
| [`allDocs`](#alldocs-filter) returns `true` if the input list includes only documents based on file extensions | [String] | [String] | Bool |
| [`allImages`](#allimages-filter) returns `true` if the input list includes only images based on file extensions | [String] | [String] | Bool |
| [`allTests`](#alltests-filter) returns `true` if the input list includes only tests based on file's path and name | [String] | [String] | Bool |
| [`checkByAttr`](#checkbyattr-filter) from a list of object, return `true` for every attribute that match the regex | [Object] | String, Regex | [Bool] |
| [`every`](#every-filter) returns `true` when all list items are `true` | [Bool] | - | Bool |
| [`extractExtensions`](#extractextensions-filter) lists all the unique file extensions from a list of file names | [String] | - | [String] |
| [`estimatedReviewTime`](#estimatedreviewtime-filter) provides statstical model based estimation for review time in minutes | [branch-context](21_gitstream-context#branch-context) | - | Integer |
| [`filterByAttr`](#filterbyattr-filter) lists the objects that their attribute match the regex | [Object] | String, Regex | [Object] |
| [`filterFileDiffRegex`](#filterfilediffregex-filter) lists all the file diffs that match the regex from the input file diff list. | [[`FileDiff` ](21_gitstream-context.md#filediff-structure)] | Regex | [[`FileDiff` ](21_gitstream-context.md#filediff-structure)] |
| [`filterList`](#filterlist-filter) lists the items that match either of the search terms | [String] | [String] | [String] |
| [`filterListRegex`](#filterlistregex-filter) lists the items that match the regex | [String] | Regex | [String] |
| [`isEveryExtension`](#iseveryextension-filter) returns `true` if the input list includes only any of the specified extensions | [String] | [String] | Bool |
| [`isEveryExtensionRegex`](#iseveryextensionregex-filter) returns `true` if the input list includes only the matching regex | [String] | Regex | Bool |
| [`isEveryInList`](#iseveryinlist-filter) returns `true` if the all the items in the input list matches either of the search terms | [String] | [String] | Bool |
| [`isEveryInListRegex`](#iseveryinlistregex-filter) returns `true` if the all the items in the input list matches the regex term | [String] | Regex | Bool |
| [`isEveryLineInFileDiffRegex`](#iseverylineinfilediffregex-filter) returns `true` if every changed line matches the regex | [[`FileDiff` ](21_gitstream-context.md#filediff-structure)] | Regex | Bool |
| [`isSomeLineInFileDiffRegex`](#issomelineinfilediffregex-filter) returns `true` if any changed line matches the regex | [[`FileDiff` ](21_gitstream-context.md#filediff-structure)] | Regex | Bool |
| [`isFormattingChange`](#isformattingchange-filter) returns `true` if all file diffs are validated as formatting changes | [[`FileDiff` ](21_gitstream-context.md#filediff-structure)] | - | Bool |
| [`isSomeInList`](#issomeinlist-filter) returns `true` if any of items in a list match either of the search terms | [String] | Regex | Bool |
| [`isSomeInListRegex`](#issomeinlistregex-filter) returns `true` if any of items in a list match the regex term | [String] | Regex | Bool |
| [`isStringIncludes`](#isstringincludes-filter) returns `true` if any of items in a list match either of the search terms | String | String | Bool|
| [`isStringIncludesRegex`](#isstringincludesregex-filter) returns `true` if any of items in a list match the regex term | String | Regex | Bool |
| [`mapByAttr`](#mapbyattr-filter) maps the objects list into a list of the selected attribute | [Object] | String | [Object] |
| [`rejectByAttr`](#rejectbyattr-filter) lists the objects that their attribute does not match the regex | [Object] | String, Regex | [Object] |
| [`some`](#some-filter) returns `true` when any of the list iterms are `true` | [Bool] | - | Bool |
| [`true`](#true-filter) returns `true` always | - | - | Bool |

</div>

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

#### `checkByAttr` filter

Return `true` for each element in the list that match the regex.

| Values | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| `items` | Input   | [Object]  | The list of objects      |
| `attribute` | Input   | String  | An object attribute name |
| `term` | Input   | Regex  | Regex to match |
| `result` | Output | [Bool]      | `true` for every matching object |

For example to check if there are code chnages with specific function call:

```yaml
{{ sourcd.diff.files | checkByAttr('diff', 'myFunction') | some }}
```

#### `estimatedReviewTime` filter

| Values   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| `branch-context` | Input  | Object    | gitStream generated [branch context variable](20_reference#branch-context) |
| `result`  | Output  | String    | the estimated time for review in minutes |

```yaml
{{ branch | estimatedReviewTime }}
```

#### `every` filter

| Values   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| `list` | Input  | [Bool]    | List of booleans |
| `result`  | Output  | Bool   | Returns `true` when all list items are `true` |

```yaml
{{ files | isInList(['src', 'dest']) | every }}
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


#### `filterByAttr` filter

Lists the matching objects that their attributes match the regex

| Values | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| `items` | Input   | [Object]  | The list of objects      |
| `attribute` | Input   | String  | An object attribute name |
| `term` | Input   | Regex  | Regex to match |
| `result` | Output | [Object]      | Lists the matching objects |

For example keep only JavaScript changes:

```yaml
{{ sourcd.diff.files | filterByAttr('new_file', '\\.js$') }}
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
{{ source.diff.files | filterFileDiffRegex('^\\+.*console\\.log') }}
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
{{ files | filterListRegex('\\.py$') | length == 0 }}
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
{{ files | isEveryInListRegex('.*\\.png$|.*\\.jpg$|.*\\.svg$') }}
```

#### `isEveryLineInFileDiffRegex` filter

| Values       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| `filediffs`      | Input    | [Map]  | List of file diffs, expects [`source.diff.files`](21_gitstream-context.md#source-context) |
| `matchRegex` | Input    | String | Regex filter applied to the `diff` field of files diffs  |
| `result`     | Output   | Bool   | `true` if the all lines in diffs match the regex |

```yaml
{{ source.diff.files | isEveryLineInFileDiffRegex('logger\\.(debug|info|warn|error)') }}
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
{{ files | isSomeInListRegex('\\.py$') }}
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
{{ "something" | isStringIncludesRegex('.*ing') }}
```

#### `mapByAttr` filter

Maps the object list into a list of the selected attribute.

| Values        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| `items`       | Input    | [Object]  | Items                        |
| `attribute` | Input    | String    | Attribute to select      |
| `result`      | Output   | [Object] | List of the selected attributes  |

For example listing all the new file names changed:

```yaml
{{ "something" | mapByAttr("new_file") }}
```

#### `rejectByAttr` filter

Lists the non-matching objects that their attributes match the regex

| Values | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| `items` | Input   | [Object]  | The list of objects      |
| `attribute` | Input   | String  | An object attribute name |
| `term` | Input   | Regex  | Regex to match |
| `result` | Output | [Object]      | Lists the non-matching objects |

For example filter out JavaScript changes:

```yaml
{{ sourcd.diff.files | rejectByAttr('new_file', '\\.js$') }}
```


#### `some` filter

| Values   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| `list` | Input  | [Bool]    | List of booleans |
| `result`  | Output  | Bool    | Returns `true` when all any of the items is `true` |

```yaml
{{ files | isInList(['src', 'dest']) | some }}
```


#### `true` filter

Returns `true`

```yaml
{{ true }}
```

