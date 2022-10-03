# Filter functions

Filters can change the look and format of the source data, or even generate new data derived from the input values. What's important is that the original data is replaced by the result of transformations, and that's what ends up in rendered templates.

## Overview

The following functions are supported in addition to the built-in functions provided by [Nunjucks](https://mozilla.github.io/nunjucks/templating.html#builtin-filters).

### General functions

<div class="big-summary" markdown=1>

| Function | Input | Args | Output |
| --------------- | ------- | ---- |  ---- |
| [`every`](#every)<br />Checks whether all element in the list are `true` | [Bool] | - | Bool |
| [`filter`](#filter)<br />Filter items that match | [String]<br />[Object][Object] | `regex`, `term`, `list`, `attr` | [String]<br />[Object] |
| [`includes`](#match)<br />check if substring match | String | `regex`, `term`, `list` | Bool |
| [`map`](#map)<br />maps the objects | [Object] | `attr` | [Object] |
| [`match`](#match)<br />match every item | [String]<br />[Object] | `regex`, `term`, `list` `attr` | [Bool] |
| [`reject`](#reject)<br />reject items that match | [String]<br />[Object] | `regex`, `term`, `list`, `attr` | [String]<br />[Object] |
| [`some`](#some)<br />checks whether at least one element in the list is `true` | [Bool] | - | Bool |
| [`true`](#true)<br />returns `true` always | - | - | Bool |

</div>

### High level functions

<div class="big-summary" markdown=1>

| Function | Input | Args | Output |
| --------------- | ------- | ---- |  ---- |
| [`allDocs`](#alldocs)<br />checks the list includes only documents | [String] | - | Bool |
| [`allImages`](#allimages)<br />checks the list includes only images | [String] | - | Bool |
| [`allTests`](#alltests)<br />checks the list includes only tests | [String] | - | Bool |
| [`estimatedReviewTime`](#estimatedreviewtime)<br />esitmated review time in minutes | [branch-context](21_gitstream-context#branch-context)| - | Integer |
| [`extensions`](#extensions)<br />lists all the unique file extensions | [String] | - | [String] |
| [`ignoreFiles`](#ignorefiles)<br />removes selected files from the context copy | [branch-context](21_gitstream-context#branch-context)<br />[files-context](21_gitstream-context#files-context)<br />[source-context](21_gitstream-context#source-context) | `globs` | modofied context |
| [`isFormattingChange`](#isformattingchange)<br />checks that only formatting changed | [[`FileDiff` ](21_gitstream-context.md#filediff-structure)] | - | Bool |
| [`matchDiffLines`](#matchdifflines)<br />match every line in diff | [[`FileDiff` ](21_gitstream-context.md#filediff-structure)] | `regex`, `ignoreWhiteSpaces` | Bool |

</div>

### Named arguments

Some functions supports named arguments, many of these repeat in different functions.

`term` - a single string, used as substring to match with the matched item.

`list` - a list of strings, trying to match any of the listed substrings with the matched item.

`regex` - a single string, used as _regular expression_ to with the matched item.

`globs` - a key to an element in the `.cm` that holds a list of strings, used as _glob_ pattern test on the matched item.

For example, the following expressions provide an identical result:

```yaml
- {{ 'something' | includes(regex='^some.*') }}
- {{ 'something' | includes(term='some') }}
- {{ 'something' | includes(list=['some']) }}
```

## Reference

#### `every`

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input  | [Bool]    | List of booleans |
| -  | Output  | Bool   | Returns `true` when all list items are `true` |

For example, check that all changes are in either 'src' or 'dest' directories:

```yaml
{{ files | match(list=['src', 'dest']) | every }}
```

#### `filter`

Creates a shallow copy of a portion of a given list, filtered down to just the elements that match the given term. You can use either single term, regex or a list of terms to match with.

| Argument | Usage    | Type      | Description                              |
| ---------|-----------|-----------|------------------------------------ |
| - | Input | [String]<br />[Object]  | The list of strings to match, or list of objects if `attr` is used      |
| `term`<br />`regex`<br />`list`  | Input (either)  |  String<br />String<br />[String]  | Search term to match with the input items |
| `attr` |Input  (optional) | String  | match a named attribute in the input object |
| - | Output | [String]<br />[Object] | The list with only the matching items |

For example, check if all changes to JavaScript files are in tests directory:

```yaml
{{ files | filter(regex='\\.js$') | match(regex='tests\\/') | every }}
```

For example, check if all changes to JavaScript files are formatting:

```yaml
{{ source.diff.files | filter(attr='new_file', regex='\\.js$') | isFormattingChange }}
```

#### `includes`

Determines whether a string includes a certain substring. You can use either single term, regex or a list of terms to match with.

| Argument        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| -       | Input    | String  | Items                        |
| `term`<br />`regex`<br />`list`  | Input (either)  |  String<br />String<br />[String]  | Substring term to match
| -      | Output   | Bool | `true` if search terms matches   |

Check string matches either of the terms:

```yaml
{{ 'something' | includes(list=['any', 'thing']) }}
```

#### `map`

Creates a new list populated with the values of the selected attribute of every element in the input list. 

| Argument        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| -       | Input    | [Object]  | Items                        |
| `attr` | Input    | String    | Attribute to select      |
| -      | Output   | [Object] | List of the selected attributes  |

For example, the `source.diff.files` context holds a list of [`FileDiff` ](21_gitstream-context.md#filediff-structure), each has `new_file` attribute. You can create a list of all the new file names by mapping to the `new_file` attribute and then check if there are changes to any `handler.js` file:

```yaml
{{ source.diff.files | map(attr='new_file') | match('handler.js') | some }}
```

#### `match`

Return `true` for each element in the list that match the search term.

| Argument | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| - | Input  | [String]<br />[Object]  | The list of strings or if `attr` used the list of objects      |
| `term`<br />`regex`<br />`list`  | Input (either)  |  String<br />String<br />[String]  | Search term to match
| `attr` | Input   | String  | match a named attribute in the input object |
| - | Output | [Bool]      | `true` for every matching item |

For example, to check if all code changes are in the `tests` directory:

```yaml
{{ files | match(regex='tests\\/') | every }}
```

For example, to check if there are code changes with specific function call:

```yaml
{{ source.diff.files | match(attr='diff', 'myFunction') | some }}
```

#### `reject`

Creates a shallow copy of a portion of a given list, filtered down to just the elements that does **not** match the given term. You can use either single term, regex or a list of terms to match with.

| Argument | Usage    | Type      | Description                              |
| ---------|-----------|-----------|------------------------------------ |
| - | Input | [String]<br />[Object]  | The list of strings to match, or list of objects if `attr` is used      |
| `term`<br />`regex`<br />`list`  | Input (either)  |  String<br />String<br />[String]  | Search term to match with the input items |
| `attr` |Input  (optional) | String  | match a named attribute in the input object |
| - | Output | [String]<br />[Object] | The list with only the non-matching items |


For example, check if all changes but JavaScript files are in tests directory:

```yaml
{{ files | reject(regex='\\.js$') | match(regex='tests\\/') | every }}
```

For example, check if all changes except for `config.json` files are formatting:

```yaml
{{ source.diff.files | reject(attr='new_file', regex='config\\.json$') | isFormattingChange }}
```

#### `some`

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input  | [Bool]    | List of booleans |
| -  | Output  | Bool    | Returns `true` when all any of the items is `true` |

```yaml
{{ files | match(list=['src', 'dest']) | some }}
```

#### `true`

Returns `true`

```yaml
{{ true }}
```

#### `allDocs`

Return `true` if the input list includes only documents based on file extensions.

| Argument   | Usage    | Type      | Description                         |
| -------- | ---------|-----------|------------------------------------------------ |
| -  | Input    | [String]  | The list of changed files with their path       |
| - | Output   | Bool      | `true` if all file extensions are of docs       |

Doc files extensions are: `md`, `mkdown`, `txt`, `rst`.

```yaml
{{ files | allDocs }}
```

#### `allImages`

Return `true` if the input list includes only images based on file extensions.

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input    | [String]  | The list of changed files with their path       |
| - | Output   | Bool      | `true` if all file extensions are of images     |

Image file extensions are: `svg`, `png`, `gif`.

```yaml
{{ files | allImages }}
```

#### `allTests`

Return `true` if the input list includes only tests based on file's path and name.

| Argument | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| - | Input   | [String]  | The list of changed files with their path       |
| - | Output | Bool      | `true` if all file tests based on name and path |

Test files must include the substring `test` or `spec` in its name or path.

```yaml
{{ files | allTests }}
```

#### `estimatedReviewTime`

Returns the estimated review time in minutes based on statistical model. The model uses the PR size and type of file changed to produce the estimation.

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input  | [`branch-context`](20_reference#branch-context)    | Branch meta data |
| -  | Output  | String    | the estimated time for review in minutes |

```yaml
{{ branch | estimatedReviewTime }}
```

#### `extensions`

Expects `files` and provide a list of all unique file extensions.

| Argument | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| -  | Input    | [String]  | The list of changed files with their path       |
| - | Output   | [String]  | List of all unique file extensions              |

For example, check that only one file type was changed:

```yaml
{{ files | extensions | length == 1 }}
```

#### `ignoreFiles`

Takes a context and transform it by removing the matching files from it. 

| Argument | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| - | Input  | [Context]  | The context objects      |
| `globs` | Input  | [String]  | Key in the `.cm` that holds the globs list for ignore files, using _glob_ pattern matching
| - | Output | [Context] | Copy of the context object withtout the matching files |


```yaml
{{ files | ignoreFiles(globs='ignore') | allTests }}
```

```yaml
{{ branch | ignoreFiles(globs='ignore') | map(attr='files_meta') | map(attr='additions') | sum }}
```

For both examples, the `.cm` includes the `ignore` key:

```yaml
ignore:
  - yarn.lock
  - package-lock.json
  - openapi.json
  - ui/src/**/*Model.d.ts
```

#### `isFormattingChange`

Return `true` if all file diffs are validated as formatting changes.

Support source code languages: JavaScript, TypeScript, JSON, YAML and HTML.

If changes in other formats detected, the filter will return `false`.

| Argument       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| -     | Input    | [`source.diff.files`](21_gitstream-context.md#source-context)  | List of file diffs, expects  |
| -     | Output   | Bool   | `true` if the all code changes are non functional |

```yaml
{{ source.diff.files | isFormattingChange }}
```

#### `matchDiffLines`

Checks diff for matching lines.

| Argument | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| - | Input  | [Object]  | The list of objects      |
| `regex` | Input   | String  | Regex term to match with the input items, use `\\` for `\` |
| `ignoreWhiteSpaces` | Input   | Bool  | match a named attribute in the input object |
| - | Output | [Bool]      | `true` for every matching object |

For example, to check if all the changes are of adding prints and ignore white spaces:

```yaml
{{ source.diff.files | matchDiffLines(regex='^\\+.*console\\.log', ignoreEmptyLines=true) | every }}
```
