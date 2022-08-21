# gitStream built-in Context variables

gitStream includes a collection of variables called contexts. 

- [`branch`](#branch-context)
    - [`name`](#name-context)
    - [`diff`](#branch-context)
        - [`size`](#branch-context)
        - [`files`](#branch-context)
- [`source`](#source-context)
    - [`diff`](#source-context)
        - [`files`](#source-context)
- [`pr`](#pr-context)
    - [`author`](#pr-context)
    - [`wip`](#pr-context)
    - [`name`](#pr-context)

#### branch context

:octicons-tag-24: Minimal version: 1.0

| Values               | Type      | Description                                              |
|----------------------|-----------|--------------------------------------------------------- |
| `branch`             | Map       | Includes the info related to the current branch          |
| `branch.name`        | String    | The current branch, `feature-123-branch`                 |
| `branch.diff`        | Map       | Includes the info compared to the default branch, `main` |
| `branch.diff.size`   | Integer   | The sum of line changed: addtions, edits and deletions   |
| `branch.diff.files`  | [String]  | List of changed files including their relative path      |

The branch context dosnt include any source code, but only related metadata.

#### source context

:octicons-beaker-24: Coming soon

| Values               | Type      | Description                                                     |
|----------------------|-----------|---------------------------------------------------------------- |
| `source`             | Map       | Includes the info related to source code                        |
| `source.diff`        | Map       | Includes the info compared to the default branch, `main`        |
| `source.diff.files`  | [Map]     | List of changed files with their respective code changes        |

The source context include all code changes, it is not safe to share it with unknown services.

The files mapping includes a list of the following structure:

| Values          | Type      | Description                                                     |
| ----------------|-----------|---------------------------------------------------------------- |
| `original_file` | String    | The name of the file before the changes, including its path     |
| `new_file`      | String    | The name of the file after the changes, including its path      |
| `diff`          | String    | The content in diff format `+` for addtions, `-` for deletions  |

#### pr context

:octicons-beaker-24: Coming soon

| Values      | Type      | Description                                                     |
| ------------|-----------|---------------------------------------------------------------- |
| `pr`        | Map       | Includes the PR info
| `pr.author` | String    | The git user name of the PR author
| `pr.wip`    | Bool      | `true` when the PR is marked as draft 
| `pr.name`   | String    | The PR name
