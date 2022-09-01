# gitStream built-in Context variables

gitStream includes a collection of variables called contexts. 

- [`branch`](#branch-context)
    - [`name`](#name-context)
    - [`base`](#base-context)
    - [`diff`](#branch-context)
        - [`size`](#branch-context)
        - [`files_metadata`](#branch-context)
- [`files`](#files-context)
- [`source`](#source-context)
    - [`diff`](#source-context)
        - [`files`](#source-context)
- `repo`
    - [`codeowners`](#repo-context)
    - [`contributors`](#repo-context)

#### `branch` context

:octicons-tag-24: Minimal version: 1.0

| Values               | Type      | Description                                              |
|----------------------|-----------|--------------------------------------------------------- |
| `branch`             | Map       | Includes the info related to the current branch          |
| `branch.name`        | String    | The current branch, `feature-123-branch`                 |
| `branch.diff`        | Map       | Includes the info compared to the default branch, `main` |
| `branch.diff.size`   | Integer   | The sum of line changed: addtions, edits and deletions   |
| `branch.diff.files_metadata`  | [Map]  | List of changed files including their relative path      |

The branch context dosnt include any source code, but only related metadata.

The `files_metadata` mapping includes a list of the following structure:

| Values          | Type      | Description                                                     |
| ----------------|-----------|---------------------------------------------------------------- |
| `file` | String    | The name of the file before the changes, including its path     |
| `additions` | Integer   | The number of lines edited or added to the file  |
| `deletions` | Integer   | The number of lines removed from the file      |

#### `files` context

:octicons-tag-24: Minimal version: 1.0

| Values               | Type      | Description                                                     |
|----------------------|-----------|---------------------------------------------------------------- |
| `files`             | [String]       | List of all chnanged files with their full path                        |


#### `source` context

:octicons-tag-24: Minimal version: 1.0

| Values               | Type      | Description                                                     |
|----------------------|-----------|---------------------------------------------------------------- |
| `source`             | Map       | Includes the info related to source code                        |
| `source.diff`        | Map       | Includes the info compared to the default branch, `main`        |
| `source.diff.files`  | [Map]     | List of changed files with their respective code changes        |

The source context include all code changes, it is not safe to share it with unknown services.

The `files` mapping includes a list of the following structure:

| Values          | Type      | Description                                                     |
| ----------------|-----------|---------------------------------------------------------------- |
| `original_file` | String    | The name of the file before the changes, including its path     |
| `new_file`      | String    | The name of the file after the changes, including its path      |
| `diff`          | String    | The content in diff format `+` for addtions, `-` for deletions  |


#### `repo` context

:octicons-tag-24: Minimal version: 1.0

| Values             | Type      | Description                                              |
|--------------------|-----------|--------------------------------------------------------- |
| `repo`             | Map       | Includes the info related to the current repo            |
| `repo.contributors`  | [Map]  | List of changed files including their relative path      |

The `contributors` mapping includes a list of the following structure:

| Values          | Type      | Description                                                     |
| ----------------|-----------|---------------------------------------------------------------- |
| `name` | String    | Name of contributor     |
| `commitsInRepo` | Number   | The number of commit by contributor to repo  |
