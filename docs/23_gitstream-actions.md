# gitStream built-in actions

- [`approve`](#approve-action)
- [`set-required-reviewers`](#set-required-reviewers-action)
- [`add-label`](#add-label-action)
- [`add-comment`](#add-comment-action)

#### approve action

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

#### set required reviewers action

#### add label action

#### add comment action
