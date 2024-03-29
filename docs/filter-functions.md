# Filter functions

Filters can change the look and format of the source data, or even generate new data derived from the input values. What's important is that the original data is replaced by the result of transformations, and that's what ends up in rendered templates.

## Overview

The following functions are supported in addition to the built-in functions provided by [Nunjucks](https://mozilla.github.io/nunjucks/templating.html#builtin-filters).

### Low level functions

<div class="big-summary" markdown=1>

| Function | Input | Args | Output |
| --------------- | ------- | ---- |  ---- |
| [`every`](#every)<br />Checks whether all element in the list are `true` | [Bool] | - | Bool |
| [`filter`](#filter)<br />Reduce list of items into a list of same items that match the specified term | [String]<br />[Object] | `regex`, `term`, `list`, `attr` | [String]<br />[Object] |
| [`includes`](#match)<br />Check if substring match | String | `regex`, `term`, `list` | Bool |
| [`map`](#map)<br />Maps each object in a list into their specified attribute value | [Object] | `attr` | [Object] |
| [`match`](#match)<br />Maps list of items into a list of booleans that match the specified term | [String]<br />[Object] | `regex`, `term`, `list` `attr` | [Bool] |
| [`nope`](#nope)<br />Checks whether all element in the list are `false` | [Bool] | - | Bool |
| [`reject`](#reject)<br />Inverse of [`filter`](#filter), the result list contains non-matching items | [String]<br />[Object] | `regex`, `term`, `list`, `attr` | [String]<br />[Object] |
| [`some`](#some)<br />Checks whether at least one element in the list is `true` | [Bool] | - | Bool |

</div>

### High level functions

<div class="big-summary" markdown=1>

| Function | Input | Args | Output |
| --------------- | ------- | ---- |  ---- |
| [`allDocs`](#alldocs)<br />Checks the list includes only documents | [files](context-variables.md#files) | - | Bool |
| [`allImages`](#allimages)<br />Checks the list includes only images | [files](context-variables.md#files) | - | Bool |
| [`allTests`](#alltests)<br />Checks the list includes only tests | [files](context-variables.md#files) | - | Bool |
| [`estimatedReviewTime`](#estimatedreviewtime)<br />Estimated review time in minutes | [branch](context-variables.md#branch-context)| - | Integer |
| [`extensions`](#extensions)<br />Lists all the unique file extensions | [String] | - | [String] |
| [`isFormattingChange`](#isformattingchange)<br />Checks that only formatting changed | [[`FileDiff` ](context-variables.md#filediff-structure)] | - | Bool |
| [`matchDiffLines`](#matchdifflines)<br />Match every line in diff | [[`FileDiff` ](context-variables.md#filediff-structure)] | `regex`, `ignoreWhiteSpaces` | [Bool] |

</div>

### Named arguments

Some functions supports named arguments, many of these repeat in different functions.

`term` - a single string, used as substring to match with the matched item.

`list` - a list of strings, trying to match any of the listed substrings with the matched item.

`regex` - a single string, used as _regular expression_ to with the matched item. A regular expression can be created just like JavaScript, but needs to be prefixed with r, for example `r/^foo.*/g`, for more info see [Nunjucks](https://mozilla.github.io/nunjucks/templating.html#regular-expressions). 

`globs` - a key to an element in the `.cm` that holds a list of strings, used as _glob_ pattern test on the matched item. For more info see [Wikipedia](https://en.wikipedia.org/wiki/Glob_(programming)).

`attr` - a key in the element to use when doing the requested operation.

For example, the following expressions provide an identical result:

```yaml+jinja
- {{ 'something' | includes(regex=r/^some.*/) }}
- {{ 'something' | includes(term='some') }}
- {{ 'something' | includes(list=['some']) }}
```

## Reference

#### `every`

Checks whether all element in the list are `true`. In case the list of elements is empty it will return `false`.

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input  | [Bool]    | List of booleans |
| -  | Output  | Bool   | Returns `true` when all list items are `true` |

For example, check that all changes are in either 'src' or 'dest' directories:

```yaml+jinja
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

```yaml+jinja
{{ files | filter(regex=r/\.js$/) | match(regex=r/tests\/) | every }}
```

For example, check if all changes to JavaScript files are formatting:

```yaml+jinja
{{ source.diff.files | filter(attr='new_file', regex=r/\.js$/) | isFormattingChange }}
```

#### `includes`

Determines whether a string includes a certain substring. You can use either single term, regex or a list of terms to match with.

| Argument        | Usage    | Type      | Description                                     |
| ------------- | ---------|-----------|--------------------------------------|
| -       | Input    | String  |  The list of strings to match                        |
| `term`<br />`regex`<br />`list`  | Input (either)  |  String<br />String<br />[String]  | Substring term to match
| -      | Output   | Bool | `true` if search terms matches   |

Check string matches either of the terms:

```yaml+jinja
{{ 'something' | includes(list=['any', 'thing']) }}
```

#### `map`

Creates a new list populated with the values of the selected attribute of every element in the input list. 

| Argument      | Usage    | Type      | Description                        |
| ------------- | ---------|-----------|--------------------------------------|
| -      | Input    | [Object] | The list of objects to map, see [context](21_gitstream-context) for valid inputs               |
| `attr` | Input    | String   | Object attribute to select      |
| -      | Output   | [Object] | List of the selected object attributes  |

For example, the `source.diff.files` context holds a list of [`FileDiff` ](context-variables.md#filediff-structure), each has `new_file` attribute. You can create a list of all the new file names by mapping to the `new_file` attribute and then check if there are changes to any `handler.js` file:

```yaml+jinja
{{ source.diff.files | map(attr='new_file') | match(term='handler.js') | some }}
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

```yaml+jinja
{{ files | match(regex=r/tests\//) | every }}
```

For example, to check if there are code changes with specific function call:

```yaml+jinja
{{ source.diff.files | match(attr='diff', term='myFunction') | some }}
```

#### `nope`

The inverse of [`every`](#every), checks whether all element in the list are `false`. In case the list of elements is empty it will return `false`.

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input  | [Bool]    | List of booleans |
| -  | Output  | Bool   | Returns `true` when all list items are `false` |

For example, check that no changes in either 'src' or 'dest' directories:

```yaml+jinja
{{ files | match(list=['src', 'dest']) | nope }}
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

```yaml+jinja
{{ files | reject(regex=r/\.js$/) | match(regex=r/tests\//') | every }}
```

For example, check if all changes except for `config.json` files are formatting:

```yaml+jinja
{{ source.diff.files | reject(attr='new_file', regex=r/config\.json$/) | isFormattingChange }}
```

#### `some`

Checks whether any element in the list is `true`. In case the list of elements is empty it will return `false`.

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input  | [Bool]    | List of booleans |
| -  | Output  | Bool    | Returns `true` when any of the items is `true` |

```yaml+jinja
{{ files | match(list=['src', 'dest']) | some }}
```

#### `allDocs`

Return `true` if the input list includes only documents based on file extensions.

| Argument   | Usage    | Type      | Description                         |
| -------- | ---------|-----------|------------------------------------------------ |
| -  | Input    | [`files`](context-variables.md#files)  | The list of changed files with their path     |
| - | Output   | Bool      | `true` if all file extensions are of docs       |

Doc files extensions are: `md`, `mkdown`, `txt`, `rst`.

```yaml+jinja
{{ files | allDocs }}
```

#### `allImages`

Return `true` if the input list includes only images based on file extensions.

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input    | [`files`](context-variables.md#files)  | The list of changed files with their path       |
| - | Output   | Bool      | `true` if all file extensions are of images     |

Image file extensions are: `svg`, `png`, `gif`.

```yaml+jinja
{{ files | allImages }}
```

#### `allTests`

Return `true` if the input list includes only tests based on file's path and name.

| Argument | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| - | Input   | [`files`](context-variables.md#files)  |The list of changed files with their path        |
| - | Output | Bool      | `true` if all file tests based on name and path |

Test files must include the substring `test` or `spec` in its name or path.

```yaml+jinja
{{ files | allTests }}
```

#### `estimatedReviewTime`

Returns the estimated review time in minutes based on statistical model. The model uses the PR size and type of file changed to produce the estimation.

| Argument   | Usage    | Type      | Description                                     |
| -------- | ---------|-----------|------------------------------------------------ |
| - | Input  | [`branch`](context-variables.md#branch)    | Branch meta data |
| -  | Output  | Integer    | the estimated time for review in minutes |

```yaml+jinja
{{ branch | estimatedReviewTime }}
```

#### `extensions`

Expects `files` and provide a list of all unique file extensions.

| Argument | Usage    | Type      | Description                                     |
| ------ | ---------|-----------|------------------------------------------------ |
| -  | Input    | [`files`](context-variables.md#files)  | The list of changed files with their path       |
| - | Output   | [String]  | List of all unique file extensions              |

For example, check that only one file type was changed:

```yaml+jinja
{{ files | extensions | length == 1 }}
```

#### `isFormattingChange`

Return `true` if all file diffs are validated as formatting changes.

Support source code languages: JavaScript, TypeScript, JSON, YAML and HTML.

If changes in other formats detected, the filter will return `false`.

| Argument       | Usage    | Type   | Description                                     |
| ------------ | ---------|--------|------------------------------------------------ |
| -     | Input    | [`source.diff.files`](context-variables.md#source)  | List of file diffs  |
| -     | Output   | Bool   | `true` if the all code changes are non functional |

```yaml+jinja
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

```yaml+jinja	
{{ source.diff.files | matchDiffLines(regex=r/^\+.*console\.log/, ignoreWhiteSpaces=true) | every }}
```
